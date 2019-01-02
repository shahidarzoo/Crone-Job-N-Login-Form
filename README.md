
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

