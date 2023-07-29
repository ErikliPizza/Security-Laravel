
## Laravel Security

## Deploying Securely
**Things You'll Learn**


- APP_ENV
-  APP_DEBUG
-  Debugging Output



*We do not want to skip the basics, if you start skipping the basics of security, you'll get lazy and you'll forget.*

**while production:**

    APP_ENV: Production or etc. without "local"  

    APP_DEBUG: False  



> **Attention ps:**   telescope usage, **hijack injection via Cookies/Session**


## Missing Authorisation

**Things You'll Learn**


- Laravel Policies
- Route Authorization


**IDOR:** *Insecure Direct Object References (IDOR) occur when an application provides direct access to objects based on user-supplied input. As a result of this vulnerability attackers can bypass authorization and access resources in the system directly, for example database records or files.*

**Creating A Policy:**
`php artisan make:policy --model=Post PostPolicy`  
**Adding Your Logic Into**
*/App/Policies/PostPolicy*
**e.g:** `return $post->user()->is($user)`

**Additional:** *you may want to return a response **as 404 instead of not 403 to hide your routes or etc**. Here is the example:*

    return $post->user()->is($user) ? Response::allow() : Response::denyAsNotFound();  


**Usage the Policy inside the Controller**:

> *App/Http/Controllers/PostController ---> function update(Post $post)* { }

**e.g:** `$this->authorize('update', $post);`

**Additional 2:** *you may forget to use the policy rule inside all the CRUD functions, totally normal. In this situation you can use Laravel is default can middleware.*
**e.g:**  
`Route::put()->middleware('can:update,book')`
*one step further, you can group your routes based on the middleware if you want.*

**Additional 3:** *if you are using resources, you can use* `__construct` method *in your controller as* `$this->authorizeResource('post');`


## Validation
**Things You'll Learn**

-   More Validation, Not Less
-   Mass Assignment Concerns
- Password Validation

**Never trust request->all()**
**Always check it out the fillable sections**
- You may want to use custom password rule
    - You can use **uncompromised**
      feature, but do not forget it may cause the user fell exhausted..

what is uncompromised?
```
    // Ensures the password has not been compromised in data leaks.
```
## SQL Injection, Sqlmap, and the Apostrophe Trick

**Things You'll Learn**

-  What is SQL Injection?
-  The Apostraphe Trick
-  Parameterized Queries


## Escaping user Input

## Markdown Security Concerns

**Things You'll Learn**

- Markdown
- XSS Attacks
-  Safety Options


https://commonmark.thephpleague.com/

## Sensitive Attributes

**Things You'll Learn**


- Exposing Sensitive Data
-    Attribute Hiding
-   API Resources

***especially important for the inertiajs; api resources highly recommended.***

      php artisan make resource PostResource
[*an example of a resource*](https://github.com/ErikliPizza/blackrainbow/blob/master/app/Http/Resources/ArtResource.php)

[*an example of a resource usage*](https://github.com/ErikliPizza/blackrainbow/blob/master/app/Http/Controllers/HomeController.php)
## Rate Limiting
**Things You'll Learn**

-   Why Rate Limit?
-   Sensitive Rate Limits
- IP Address Cautioning

**General Options:**

- Limiting by **ip** address
-  Limiting by **email** address


**e.g For Breeze:**

> App\Http\Request\Auth\LoginRequest

**Limiting by ip address:** can may be bypassed by using botnet.
**e.g:** `return Str::transliterate(Str::lower($this->input('email')).'|'.$this->ip());`  
**Limiting by email address:** owner of that email address will be blocked for that specified time tho.
**e.g:** `return Str::transliterate(Str::lower($this->input('email'));`

***tips fm:*** *for exaggerated security, you may restrict/force to usage only with unique username and pw.*

***also Laravel has ratelimitter for api requests. By the default, it limits based on the user id and ip address.***

    return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());  


Limiting with user id the api requests is handy for the multiple logged-in situations.  
if you don't have any other indicator of where the request is coming from, ip address coming into play.
