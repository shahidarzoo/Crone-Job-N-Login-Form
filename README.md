
# Crone Job In Laravel 5
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
        'App\Console\Commands\ReservationUser'
    ];
```
## set schdule in same App\Console\Kernel.php
```php
protected function schedule(Schedule $schedule)
{
    $schedule->command('user:email')->everyMinute();
}
```
Check your conrone job has registered or not: php artisan 
Know run php artisan user:email
