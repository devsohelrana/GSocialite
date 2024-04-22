# Getting Started with Socialite Package in Laravel 11 | Visual Dev - Elegant Coding

## Create Laravel New Project

```sh
composer create laravel/laravel ProjectName
```

#### or

```shell
composer global require laravel/installer

laravel new ProjectName
```

Database create and configure `.env` database name.

```sh
php artisan migrate
npm install
npm run dev
```

### Laravel Socialite

```sh
composer require laravel/socialite
```

#### Configuration `config/services.php`

```php
'google' => [
    'client_id' => env('GOOGLE_CLIENT_ID'),
    'client_secret' => env('GOOGLE_CLIENT_SECRET'),
    'redirect' => env('GOOGLE_REDIRECT_URL'),
],
```

`.env`

```.env
GOOGLE_CLIENT_ID= // Google Client ID
GOOGLE_CLIENT_SECRET= // Google Client Secret
GOOGLE_REDIRECT_URL= // Google Callback Url // http://localhost:8000/auth/google/callback
```

#### Create Controller `GSocialiteController`

```sh
php artisan make:controller GSocialiteController
```

#### Create Route `web.php`

```php
Route::post('/auth/google/redirect', [GSocialiteController::class, 'redirect'])->name('google.auth');
Route::get('/auth/google/callback', [GSocialiteController::class, 'callback']);
```

`GoogleAuthController`

```php
public function redirect() {
	return Socialite::driver('google')->redirect();
}

public function callback() {
	$googleUser = Socialite::driver('google')->user();

	$user = User::firstOrCreate([
		'email' => $googleUser->email,
	], [
		'name' => $googleUser->name,
		'email' => $googleUser->email,
		'password' => 'password',
		'avatar' => $googleUser->avatar,
		'email_verified_at' => $googleUser->email_verified_at,
	]);

	Auth::login($user);
	return redirect('/dashboard');
}
```

Edit `user table` && add filed

```php
$table->string('avatar')->nullable();
```

Edit `user model`

```php
protected $fillable = [
	'name',
	'email',
	'password',
	'avatar',
];
```

Edit `login.blade.php` file

```php
<div>
    <x-primary-button class="mt-5">
        <a href="{{ route('google.auth') }}">
            {{ __('Log in with google') }}
        </a>
    </x-primary-button>
</div>
```
