# 26 Rules Overview

## AddArgumentDefaultValueRector

Adds default value for arguments in defined methods.

:wrench: **configure it!**

- class: [`RectorLaravel\Rector\ClassMethod\AddArgumentDefaultValueRector`](../src/Rector/ClassMethod/AddArgumentDefaultValueRector.php)

```php
use Rector\Config\RectorConfig;
use RectorLaravel\Rector\ClassMethod\AddArgumentDefaultValueRector;
use RectorLaravel\ValueObject\AddArgumentDefaultValue;

return static function (RectorConfig $rectorConfig): void {
    $rectorConfig->ruleWithConfiguration(AddArgumentDefaultValueRector::class, [
        AddArgumentDefaultValueRector::ADDED_ARGUMENTS => [
            new AddArgumentDefaultValue('SomeClass', 'someMethod', 0, false),
        ],
    ]);
};
```

↓

```diff
 class SomeClass
 {
-    public function someMethod($value)
+    public function someMethod($value = false)
     {
     }
 }
```

<br>

## AddGenericReturnTypeToRelationsRector

Add generic return type to relations in child of `Illuminate\Database\Eloquent\Model`

- class: [`RectorLaravel\Rector\ClassMethod\AddGenericReturnTypeToRelationsRector`](../src/Rector/ClassMethod/AddGenericReturnTypeToRelationsRector.php)

```diff
 use App\Account;
 use Illuminate\Database\Eloquent\Model;
 use Illuminate\Database\Eloquent\Relations\HasMany;

 class User extends Model
 {
+    /** @return HasMany<Account> */
     public function accounts(): HasMany
     {
         return $this->hasMany(Account::class);
     }
 }
```

<br>

## AddGuardToLoginEventRector

Add new `$guard` argument to Illuminate\Auth\Events\Login

- class: [`RectorLaravel\Rector\New_\AddGuardToLoginEventRector`](../src/Rector/New_/AddGuardToLoginEventRector.php)

```diff
 use Illuminate\Auth\Events\Login;

 final class SomeClass
 {
     public function run(): void
     {
-        $loginEvent = new Login('user', false);
+        $guard = config('auth.defaults.guard');
+        $loginEvent = new Login($guard, 'user', false);
     }
 }
```

<br>

## AddMockConsoleOutputFalseToConsoleTestsRector

Add "$this->mockConsoleOutput = false"; to console tests that work with output content

- class: [`RectorLaravel\Rector\Class_\AddMockConsoleOutputFalseToConsoleTestsRector`](../src/Rector/Class_/AddMockConsoleOutputFalseToConsoleTestsRector.php)

```diff
 use Illuminate\Support\Facades\Artisan;
 use Illuminate\Foundation\Testing\TestCase;

 final class SomeTest extends TestCase
 {
+    public function setUp(): void
+    {
+        parent::setUp();
+
+        $this->mockConsoleOutput = false;
+    }
+
     public function test(): void
     {
         $this->assertEquals('content', \trim((new Artisan())::output()));
     }
 }
```

<br>

## AddParentBootToModelClassMethodRector

Add `parent::boot();` call to `boot()` class method in child of `Illuminate\Database\Eloquent\Model`

- class: [`RectorLaravel\Rector\ClassMethod\AddParentBootToModelClassMethodRector`](../src/Rector/ClassMethod/AddParentBootToModelClassMethodRector.php)

```diff
 use Illuminate\Database\Eloquent\Model;

 class Product extends Model
 {
     public function boot()
     {
+        parent::boot();
     }
 }
```

<br>

## AddParentRegisterToEventServiceProviderRector

Add `parent::register();` call to `register()` class method in child of `Illuminate\Foundation\Support\Providers\EventServiceProvider`

- class: [`RectorLaravel\Rector\ClassMethod\AddParentRegisterToEventServiceProviderRector`](../src/Rector/ClassMethod/AddParentRegisterToEventServiceProviderRector.php)

```diff
 use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;

 class EventServiceProvider extends ServiceProvider
 {
     public function register()
     {
+        parent::register();
     }
 }
```

<br>

## AnonymousMigrationsRector

Convert migrations to anonymous classes.

- class: [`RectorLaravel\Rector\Class_\AnonymousMigrationsRector`](../src/Rector/Class_/AnonymousMigrationsRector.php)

```diff
 use Illuminate\Database\Migrations\Migration;

-class CreateUsersTable extends Migration
+return new class extends Migration
 {
     // ...
-}
+};
```

<br>

## CallOnAppArrayAccessToStandaloneAssignRector

Replace magical call on `$this->app["something"]` to standalone type assign variable

- class: [`RectorLaravel\Rector\Assign\CallOnAppArrayAccessToStandaloneAssignRector`](../src/Rector/Assign/CallOnAppArrayAccessToStandaloneAssignRector.php)

```diff
 class SomeClass
 {
     /**
      * @var \Illuminate\Contracts\Foundation\Application
      */
     private $app;

     public function run()
     {
-        $validator = $this->app['validator']->make('...');
+        /** @var \Illuminate\Validation\Factory $validationFactory */
+        $validationFactory = $this->app['validator'];
+        $validator = $validationFactory->make('...');
     }
 }
```

<br>

## ChangeQueryWhereDateValueWithCarbonRector

Add `parent::boot();` call to `boot()` class method in child of `Illuminate\Database\Eloquent\Model`

- class: [`RectorLaravel\Rector\MethodCall\ChangeQueryWhereDateValueWithCarbonRector`](../src/Rector/MethodCall/ChangeQueryWhereDateValueWithCarbonRector.php)

```diff
 use Illuminate\Database\Query\Builder;

 final class SomeClass
 {
     public function run(Builder $query)
     {
-        $query->whereDate('created_at', '<', Carbon::now());
+        $dateTime = Carbon::now();
+        $query->whereDate('created_at', '<=', $dateTime);
+        $query->whereTime('created_at', '<=', $dateTime);
     }
 }
```

<br>

## FactoryApplyingStatesRector

Call the state methods directly instead of specify the name of state.

- class: [`RectorLaravel\Rector\MethodCall\FactoryApplyingStatesRector`](../src/Rector/MethodCall/FactoryApplyingStatesRector.php)

```diff
-$factory->state('delinquent');
-$factory->states('premium', 'delinquent');
+$factory->delinquent();
+$factory->premium()->delinquent();
```

<br>

## FactoryDefinitionRector

Upgrade legacy factories to support classes.

- class: [`RectorLaravel\Rector\Namespace_\FactoryDefinitionRector`](../src/Rector/Namespace_/FactoryDefinitionRector.php)

```diff
 use Faker\Generator as Faker;

-$factory->define(App\User::class, function (Faker $faker) {
-    return [
-        'name' => $faker->name,
-        'email' => $faker->unique()->safeEmail,
-    ];
-});
+class UserFactory extends \Illuminate\Database\Eloquent\Factories\Factory
+{
+    protected $model = App\User::class;
+    public function definition()
+    {
+        return [
+            'name' => $this->faker->name,
+            'email' => $this->faker->unique()->safeEmail,
+        ];
+    }
+}
```

<br>

## FactoryFuncCallToStaticCallRector

Use the static factory method instead of global factory function.

- class: [`RectorLaravel\Rector\FuncCall\FactoryFuncCallToStaticCallRector`](../src/Rector/FuncCall/FactoryFuncCallToStaticCallRector.php)

```diff
-factory(User::class);
+User::factory();
```

<br>

## HelperFuncCallToFacadeClassRector

Change `app()` func calls to facade calls

- class: [`RectorLaravel\Rector\FuncCall\HelperFuncCallToFacadeClassRector`](../src/Rector/FuncCall/HelperFuncCallToFacadeClassRector.php)

```diff
 class SomeClass
 {
     public function run()
     {
-        return app('translator')->trans('value');
+        return \Illuminate\Support\Facades\App::get('translator')->trans('value');
     }
 }
```

<br>

## LumenRoutesStringActionToUsesArrayRector

Changes action in rule definitions from string to array notation.

- class: [`RectorLaravel\Rector\MethodCall\LumenRoutesStringActionToUsesArrayRector`](../src/Rector/MethodCall/LumenRoutesStringActionToUsesArrayRector.php)

```diff
-$router->get('/user', 'UserController@get');
+$router->get('/user', ['uses => 'UserController@get']);
```

<br>

## LumenRoutesStringMiddlewareToArrayRector

Changes middlewares from rule definitions from string to array notation.

- class: [`RectorLaravel\Rector\MethodCall\LumenRoutesStringMiddlewareToArrayRector`](../src/Rector/MethodCall/LumenRoutesStringMiddlewareToArrayRector.php)

```diff
-$router->get('/user', ['middleware => 'test']);
-$router->post('/user', ['middleware => 'test|authentication']);
+$router->get('/user', ['middleware => ['test']]);
+$router->post('/user', ['middleware => ['test', 'authentication']]);
```

<br>

## MigrateToSimplifiedAttributeRector

Migrate to the new Model attributes syntax

- class: [`RectorLaravel\Rector\ClassMethod\MigrateToSimplifiedAttributeRector`](../src/Rector/ClassMethod/MigrateToSimplifiedAttributeRector.php)

```diff
 use Illuminate\Database\Eloquent\Model;

 class User extends Model
 {
-    public function getFirstNameAttribute($value)
+    protected function firstName(): \Illuminate\Database\Eloquent\Casts\Attribute
     {
-        return ucfirst($value);
-    }
-
-    public function setFirstNameAttribute($value)
-    {
-        $this->attributes['first_name'] = strtolower($value);
-        $this->attributes['first_name_upper'] = strtoupper($value);
+        return \Illuminate\Database\Eloquent\Casts\Attribute::make(get: function ($value) {
+            return ucfirst($value);
+        }, set: function ($value) {
+            return ['first_name' => strtolower($value), 'first_name_upper' => strtoupper($value)];
+        });
     }
 }
```

<br>

## MinutesToSecondsInCacheRector

Change minutes argument to seconds in `Illuminate\Contracts\Cache\Store` and Illuminate\Support\Facades\Cache

- class: [`RectorLaravel\Rector\StaticCall\MinutesToSecondsInCacheRector`](../src/Rector/StaticCall/MinutesToSecondsInCacheRector.php)

```diff
 class SomeClass
 {
     public function run()
     {
-        Illuminate\Support\Facades\Cache::put('key', 'value', 60);
+        Illuminate\Support\Facades\Cache::put('key', 'value', 60 * 60);
     }
 }
```

<br>

## OptionalToNullsafeOperatorRector

Convert simple calls to optional helper to use the nullsafe operator

:wrench: **configure it!**

- class: [`RectorLaravel\Rector\PropertyFetch\OptionalToNullsafeOperatorRector`](../src/Rector/PropertyFetch/OptionalToNullsafeOperatorRector.php)

```php
use Rector\Config\RectorConfig;
use RectorLaravel\Rector\PropertyFetch\OptionalToNullsafeOperatorRector;

return static function (RectorConfig $rectorConfig): void {
    $rectorConfig->ruleWithConfiguration(OptionalToNullsafeOperatorRector::class, [
        OptionalToNullsafeOperatorRector::EXCLUDE_METHODS => ['present'],
    ]);
};
```

↓

```diff
-optional($user)->getKey();
-optional($user)->id;
+$user?->getKey();
+$user?->id;
 // macro methods
 optional($user)->present()->getKey();
```

<br>

## PropertyDeferToDeferrableProviderToRector

Change deprecated `$defer` = true; to `Illuminate\Contracts\Support\DeferrableProvider` interface

- class: [`RectorLaravel\Rector\Class_\PropertyDeferToDeferrableProviderToRector`](../src/Rector/Class_/PropertyDeferToDeferrableProviderToRector.php)

```diff
 use Illuminate\Support\ServiceProvider;
+use Illuminate\Contracts\Support\DeferrableProvider;

-final class SomeServiceProvider extends ServiceProvider
+final class SomeServiceProvider extends ServiceProvider implements DeferrableProvider
 {
-    /**
-     * @var bool
-     */
-    protected $defer = true;
 }
```

<br>

## Redirect301ToPermanentRedirectRector

Change "redirect" call with 301 to "permanentRedirect"

- class: [`RectorLaravel\Rector\StaticCall\Redirect301ToPermanentRedirectRector`](../src/Rector/StaticCall/Redirect301ToPermanentRedirectRector.php)

```diff
 class SomeClass
 {
     public function run()
     {
-        Illuminate\Routing\Route::redirect('/foo', '/bar', 301);
+        Illuminate\Routing\Route::permanentRedirect('/foo', '/bar');
     }
 }
```

<br>

## RedirectBackToBackHelperRector

Replace `redirect()->back()` and `Redirect::back()` with `back()`

- class: [`RectorLaravel\Rector\MethodCall\RedirectBackToBackHelperRector`](../src/Rector/MethodCall/RedirectBackToBackHelperRector.php)

```diff
 use Illuminate\Support\Facades\Redirect;

 class MyController
 {
     public function store()
     {
-        return redirect()->back()->with('error', 'Incorrect Details.')
+        return back()->with('error', 'Incorrect Details.')
     }

     public function update()
     {
-        return Redirect::back()->with('error', 'Incorrect Details.')
+        return back()->with('error', 'Incorrect Details.')
     }
 }
```

<br>

## RedirectRouteToToRouteHelperRector

Replace `redirect()->route("home")` and `Redirect::route("home")` with `to_route("home")`

- class: [`RectorLaravel\Rector\MethodCall\RedirectRouteToToRouteHelperRector`](../src/Rector/MethodCall/RedirectRouteToToRouteHelperRector.php)

```diff
 use Illuminate\Support\Facades\Redirect;

 class MyController
 {
     public function store()
     {
-        return redirect()->route('home')->with('error', 'Incorrect Details.')
+        return to_route('home')->with('error', 'Incorrect Details.')
     }

     public function update()
     {
-        return Redirect::route('home')->with('error', 'Incorrect Details.')
+        return to_route('home')->with('error', 'Incorrect Details.')
     }
 }
```

<br>

## RemoveDumpDataDeadCodeRector

It will removes the dump data just like dd or dump functions from the code.`

- class: [`RectorLaravel\Rector\FuncCall\RemoveDumpDataDeadCodeRector`](../src/Rector/FuncCall/RemoveDumpDataDeadCodeRector.php)

```diff
 class MyController
 {
     public function store()
     {
-        dd('test');
         return true;
     }

     public function update()
     {
-        dump('test');
         return true;
     }
 }
```

<br>

## RequestStaticValidateToInjectRector

Change static `validate()` method to `$request->validate()`

- class: [`RectorLaravel\Rector\StaticCall\RequestStaticValidateToInjectRector`](../src/Rector/StaticCall/RequestStaticValidateToInjectRector.php)

```diff
 use Illuminate\Http\Request;

 class SomeClass
 {
-    public function store()
+    public function store(\Illuminate\Http\Request $request)
     {
-        $validatedData = Request::validate(['some_attribute' => 'required']);
+        $validatedData = $request->validate(['some_attribute' => 'required']);
     }
 }
```

<br>

## RouteActionCallableRector

Use PHP callable syntax instead of string syntax for controller route declarations.

:wrench: **configure it!**

- class: [`RectorLaravel\Rector\StaticCall\RouteActionCallableRector`](../src/Rector/StaticCall/RouteActionCallableRector.php)

```php
use Rector\Config\RectorConfig;
use RectorLaravel\Rector\StaticCall\RouteActionCallableRector;

return static function (RectorConfig $rectorConfig): void {
    $rectorConfig->ruleWithConfiguration(RouteActionCallableRector::class, [
        RouteActionCallableRector::NAMESPACE => 'App\Http\Controllers',
    ]);
};
```

↓

```diff
-Route::get('/users', 'UserController@index');
+Route::get('/users', [\App\Http\Controllers\UserController::class, 'index']);
```

<br>

## UnifyModelDatesWithCastsRector

Unify Model `$dates` property with `$casts`

- class: [`RectorLaravel\Rector\Class_\UnifyModelDatesWithCastsRector`](../src/Rector/Class_/UnifyModelDatesWithCastsRector.php)

```diff
 use Illuminate\Database\Eloquent\Model;

 class Person extends Model
 {
     protected $casts = [
-        'age' => 'integer',
+        'age' => 'integer', 'birthday' => 'datetime',
     ];
-
-    protected $dates = ['birthday'];
 }
```

<br>
