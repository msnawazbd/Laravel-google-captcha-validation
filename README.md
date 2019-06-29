## Laravel google captcha validation

### Step 01. Go to https://www.google.com/recaptcha/admin/create
- Register a new site
  - Give a **Label** name
  - Select **reCAPTCHA v2**
  - Add a domain **localhost**
  - **Accept the reCAPTCHA Terms of Service**
  - And **Submit**
- After submit we get **SITE KEY** and **SECRET KEY**

### Step 02. Go to ".env" file
- Added **KEY** and **SECRET**

```dotenv
CAPTCHA_KEY=6LfDQKsUAAAAAIALIJ0E5hoGebTclmYdVArAmfhK
CAPTCHA_SECRET=6LfDQKsUAAAAABf_F_HU40qTNCRu4PR_2HjH3J4j
```
- In google top of the captcha key **"See client side intigration"** link
- After click the link we get an api.js
- Put it in head of **resources/views/layouts/app.blade.php**

```js
<script src="https://www.google.com/recaptcha/api.js" async defer></script>
```
- Then got to **resources/views/auth/register.blade.php** and added

```html
<div class="form-group{{ $errors->has('g-recaptcha-response') ? ' has-error' : '' }} row">
    <div class="col-md-6 offset-md-4">
        <div class="g-recaptcha" data-sitekey="{{ env('CAPTCHA_KEY') }}"></div>
        @if ($errors->has('g-recaptcha-response'))
            <span class="help-block">
                <strong>{{ $errors->first('g-recaptcha-response') }}</strong>
            </span>
        @endif
    </div>
</div>
```
### Step 03. Go to terminal and write

>composer require google/recaptcha '~1.2.2'

- And then create a rule

>php artisan make:rule Captcha

### Step 04. Go to "app/Rules/Captcha.php" 

- Update the code that is present with the one below :

```php
use ReCaptcha\ReCaptcha;
```

```php
public function passes($attribute, $value)
    {
        $recaptcha = new Recaptcha(env('CAPTCHA_SECRET'));
        $response = $recaptcha->verify($value, $_SERVER['REMOTE_ADDR']);
        return $response->isSuccess();
    }
```

### Step 05. Go to "app/HTTP/Controllers/Auth/RegisterController.php"

 - Update the code that is present with the one below :
 
 ```php
 use App\Rules\Captcha;
 ```
 
 ```php
 protected function validator(array $data)
     {
         return Validator::make($data, [
             'name' => ['required', 'string', 'max:255'],
             'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
             'password' => ['required', 'string', 'min:8', 'confirmed'],
             'g-recaptcha-response' => new Captcha(),
         ]);
     }
 ```

### That's It :)
