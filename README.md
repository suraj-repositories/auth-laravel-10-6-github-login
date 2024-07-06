
<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo"></a></p>

# auth-laravel-10-6-github-login

- Github Auth system
- Role based authentication system where we can give the user permission according to his role.
- Also see : [Laravel Socialite Documentation](https://laravel.com/docs/10.x/socialite)

## Step 0
 - first you need to create a simple working login system 
 - you can take reference of : [auth-laravel-10-1](https://github.com/suraj-repositories/auth-laravel-10-1)


## Steps
1. require the 'socialite' package
```sh
composer require laravel/socialite
```

2. Now you need to create credentials on github app
    - go to your github account settings
    - on the left sidebar `Developer settings` (bottom of list)
    - O-auth-apps -> new Oauth app
    - Fill the form details
      - homepage url = in my case `http://localhost:8000`. In deployed website it should be the website url
      - Authorization callback URL=  `http://localhost:8000/auth/github/callback`
      - after filling all details click on finish
    - after that you can find the `Client ID` on your app page
    - you can generate the `Client secrets` by clicking on the button `Generate new client secret`
    - and the last thing is `Redirect URL` which is `http://localhost:8000/auth/github/callback`

```sh
GITHUB_CLIENT_ID=********************
GITHUB_CLIENT_SECRET=********************************
GITHUB_REDIRECT_URL=http://localhost:8000/auth/github/callback
```
3. After the credential setup you need to open config\services.php in which you need to setup the following
```sh
  'github' => [
        'client_id' => env('GITHUB_CLIENT_ID'),
        'client_secret' => env('GITHUB_CLIENT_SECRET'),
        'redirect' => env('GITHUB_REDIRECT_URL'),
    ],
```
4. on your login file create a link for login 
```php
<a href="{{ URL::to('githubLogin') }}">Github Login</a>
```
5. setup the route for the URL and success-redirect-url
```php
 Route::get('/githubLogin', [AuthController::class, 'githubLogin'])->name('githubLogin');
 Route::get('/auth/github/callback', [AuthController::class, 'githubHandler'])->name('githubHandler');
```
6. setup a controller method to handle github login
```php
    public function githubLogin(){
        return Socialite::driver('github')->redirect();
    }
```
```php
    public function githubHandler(Request $request){
        try{

            $user = Socialite::driver('github')->user();
            $findUser = User::where('email', $user->email)->first();

            if(!$findUser){
                $findUser = new User();
                $findUser->name = $user->name;
                $findUser->email = $user->email;
                $findUser->password = Hash::make("123");
                $findUser->role = 'USER';
                $findUser->save();
            }

            Auth::login($findUser);
            return redirect('/');
        }catch(Exception $e){
            dd($e->getMessage() . "something went wrong!!");
            dd($e->getMessage());
        }
    }
```
Now your app is ready to login with github.

### Further steps
- Also when you make changes on env - do this command before test your application
```sh
php artisan cache:clear
php artisan optimize
php artisan serve
```

<br />
<p align="center">⭐️ Star my repositories if you find it helpful.</p>
