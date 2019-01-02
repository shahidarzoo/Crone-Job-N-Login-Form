
# Crone Job In Laravel 5 and Translation Process
### First of all run the artisan command: 
php artisan make:command ReservationUser
### Then it will create in side App\console\command\ReservationUser.php
```php
namespace App\Console\Commands;

use Illuminate\Console\Command;

class ReservationUser extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'user:email';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'This is crone job';

    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
    }

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        $user  = \App\User::select('id')->orderBy('id', 'DESC')->first();
        $id = $user->id+1;
        $user = \DB::table('users')->insert([
            "name" => "User",
            "email" => "user$id@gmail.com",
            "password" => bcrypt("123456"),
        ]);
        if($user)
        {
            echo 'Crone Job is Running';
        }
    }
}

```
## Inside Kernel register App\Console\Kernel.php
```php
protected $commands = [
       Commands\ReservationUser::class
    ];
```
## set schdule in same App\Console\Kernel.php
```php
protected function schedule(Schedule $schedule)
{
    $schedule->command('user:email')->everyMinute();
}
```
### Run crone job 
Know run php artisan user:email

# Translate
### Make middleware LanguageSwitcher
```php

<?php

namespace App\Http\Middleware;

use Closure;
use Session;
use Config;
use App;
class LanguageSwitcher
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if(!Session::has('locale'))
        {
            Session::put('local', Config::get('app.locale'));
        }
        App::setLocale(session('locale'));
        return $next($request);
    }
}

```
### Route
```php
Route::group(['middleware' => 'langSwitcher'], function(){
	/*Route */
	Route::get('/', function(){
		return view('welcome');
	});

	/* Language Switcher */

	Route::get('language/{lang}', function($lang){
		\Session::put('locale', $lang);
		return redirect()->back();
	});
  });

```
### View
inside layout/app.php
```html
<li>
    <div class="btn-group" role="group" aria-label="...">
       <div class="btn-group" role="group">
	    <button type="button" class="btn btn-default dropdown-toggle" data-toggle="dropdown">
	    @if(Session::has('locale'))
		{{session('locale')}}
	    @else
		{{Config::get('app.locale')}}
	    @endif
	    <span class="creat"></span>
	</button>
	<ul class="dropdown-menu">
	    <li>
		<a href="{{url('language/en')}}">English</a>
		<a href="{{url('language/ur')}}">Urdu</a>
	    </li>
	</ul>
       </div>
    </div>
</li>
```
### recources/view/auth/login.php
```html
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row">
        <div class="col-md-8 col-md-offset-2">
            <div class="panel panel-default">
                <div class="panel-heading <?php if(App::isLocale('ur')) echo 'text-right' ?>">{{__('auth.login')}}</div>

                <div class="panel-body">
                    <form class="form-horizontal" method="POST" action="{{ route('login') }}">
                        {{ csrf_field() }}

                        <div class="form-group{{ $errors->has('email') ? ' has-error' : '' }}">
                            <label for="email" class="col-md-4 control-label <?php if(App::isLocale('ur')) echo 'col-md-push-7' ?>">{{__('forms.email')}}</label>

                            <div class="col-md-6">
                                <input id="email" type="email" class="form-control" name="email" value="{{ old('email') }}" required autofocus>

                                @if ($errors->has('email'))
                                    <span class="help-block">
                                        <strong>{{ $errors->first('email') }}</strong>
                                    </span>
                                @endif
                            </div>
                        </div>

                        <div class="form-group{{ $errors->has('password') ? ' has-error' : '' }}">
                            <label for="password" class="col-md-4 control-label <?php if(App::isLocale('ur')) echo 'col-md-push-7' ?>">{{__('forms.password')}}</label>

                            <div class="col-md-6">
                                <input id="password" type="password" class="form-control" name="password" required>

                                @if ($errors->has('password'))
                                    <span class="help-block">
                                        <strong>{{ $errors->first('password') }}</strong>
                                    </span>
                                @endif
                            </div>
                        </div>

                        <div class="form-group">
                            <div class="col-md-6 col-md-offset-4">
                                <div class="checkbox">
                                    <label>
                                        <input type="checkbox" name="remember" {{ old('remember') ? 'checked' : '' }}>{{__('forms.rememberme')}}
                                    </label>
                                </div>
                            </div>
                        </div>

                        <div class="form-group">
                            <div class="col-md-8 col-md-offset-4">
                                <button type="submit" class="btn btn-primary">
                                    Login
                                </button>

                                <a class="btn btn-link" href="{{ route('password.request') }}">
                                    {{__('forms.fpassword')}}
                                </a>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
@endsection

```
### inside resource/view/auth/register.php

```html
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row">
        <div class="col-md-8 col-md-offset-2">
            <div class="panel panel-default">
                <div class="panel-heading <?php if(App::isLocale('ur')) echo 'text-right' ?>">{{__('auth.register')}}</div>

                <div class="panel-body">
                    <form class="form-horizontal" method="POST" action="{{ route('register') }}">
                        {{ csrf_field() }}

                        <div class="form-group{{ $errors->has('name') ? ' has-error' : '' }}">
                            <label for="name" class="col-md-4 control-label <?php if(App::isLocale('ur')) echo 'col-md-push-7' ?>">{{__('forms.name')}}</label>

                            <div class="col-md-6">
                                <input id="name" type="text" class="form-control" name="name" value="{{ old('name') }}" required autofocus>

                                @if ($errors->has('name'))
                                    <span class="help-block">
                                        <strong>{{ $errors->first('name') }}</strong>
                                    </span>
                                @endif
                            </div>
                        </div>

                        <div class="form-group{{ $errors->has('email') ? ' has-error' : '' }}">
                            <label for="email" class="col-md-4 control-label <?php if(App::isLocale('ur')) echo 'col-md-push-7' ?>">{{__('forms.email')}}</label>

                            <div class="col-md-6">
                                <input id="email" type="email" class="form-control" name="email" value="{{ old('email') }}" required>

                                @if ($errors->has('email'))
                                    <span class="help-block">
                                        <strong>{{ $errors->first('email') }}</strong>
                                    </span>
                                @endif
                            </div>
                        </div>

                        <div class="form-group{{ $errors->has('password') ? ' has-error' : '' }}">
                            <label for="password" class="col-md-4 control-label <?php if(App::isLocale('ur')) echo 'col-md-push-7' ?>">{{__('forms.password')}}</label>

                            <div class="col-md-6">
                                <input id="password" type="password" class="form-control" name="password" required>

                                @if ($errors->has('password'))
                                    <span class="help-block">
                                        <strong>{{ $errors->first('password') }}</strong>
                                    </span>
                                @endif
                            </div>
                        </div>

                        <div class="form-group">
                            <label for="password-confirm" class="col-md-4 control-label <?php if(App::isLocale('ur')) echo 'col-md-push-7' ?>">{{__('forms.cpassword')}}</label>

                            <div class="col-md-6">
                                <input id="password-confirm" type="password" class="form-control" name="password_confirmation" required>
                            </div>
                        </div>

                        <div class="form-group">
                            <div class="col-md-6 col-md-offset-4">
                                <button type="submit" class="btn btn-primary">
                                    Register
                                </button>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
@endsection


```

inside resouces/lang/en/auth.php add these two lines
```
 'login' => 'Login',
  'register' => 'Register',
```
inside resouces/lang/ur/auth.php add these two lines
```
 'login' => 'لوگن',
    'register' => 'رجسٹر',
```

inside resouces/lang/en/forms.php add these lines
```
<?php
return [
	'name' => 'Name',
	'email' => 'E-Mail Address',
	'password' => 'Password',
	'cpassword' => 'Confirm Password',
	'rememberme' => 'Remember Me',
	'fpassword' => 'Forget Password'

?>
```

inside resouces/lang/ur/forms.php add these lines
```
<?php
return [
	'name' => 'نام',
	'email' => ' ای میل ',
	'password' => 'پاس ورڑ  ',
	'cpassword' => 'کنفرم پاس ورڑ ',
	'rememberme' => 'یاد رکھے  ',
	'fpassword' => 'پاسورڑبھول گیا  '
];

?>
```
