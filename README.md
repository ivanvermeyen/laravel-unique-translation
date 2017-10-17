# Laravel Unique Translation

#### Check if a translated value in a JSON column is unique in the database.

Imagine you want store a `slug` for a `Post` model in different languages.

The amazing [`spatie/laravel-translatable`](https://github.com/spatie/laravel-translatable) package makes this a cinch!

But then you want to make sure each translation is unique for its language.

That's where this package comes in to play.

## Requirements

-   PHP >= 7.0
-   MySQL >= 5.7
-   [Laravel](https://laravel.com/) >= 5.5
-   [spatie/laravel-translatable](https://github.com/spatie/laravel-translatable)

## Installation

Require the package via Composer:

```
composer require codezero/laravel-unique-translation
```
Laravel will automatically register the [ServiceProvider](https://github.com/codezero-be/laravel-unique-translation/blob/master/src/UniqueTranslationServiceProvider.php).

## Usage

For the following examples, I will use a `slug` in a `posts` table as the subject of our validation.

### Validate a Single Translation

Your form can submit a single slug:

```html
<input name="slug">
```

We can then check if it is unique **in the current locale**:

```php
$attributes = request()->validate([
    'slug' => ['unique_translation:posts'],
]);
```

You could also use the Rule instance:

```php
use CodeZero\UniqueTranslation\UniqueTranslationRule;

$attributes = request()->validate([
    'slug' => [new UniqueTranslationRule('posts')],
]);
```

### Validate an Array of Translations

Your form can also submit an array of slugs.

```html
<input name="slug[en]">
<input name="slug[nl]">
```

We need to validate the entire array in this case. Mind the `slug.*` key.

```php
$attributes = request()->validate([
    'slug.*' => ['unique_translation:posts'],
    // or...
    'slug.*' => [new UniqueTranslationRule('posts')],
]);
```

### Specify a Column

Maybe your form field has a name of `post_slug` and your database field `slug`:

```php
$attributes = request()->validate([
    'post_slug.*' => ['unique_translation:posts,slug'],
    // or...
    'post_slug.*' => [new UniqueTranslationRule('posts', 'slug')],
]);
```

### Ignore a Record with ID

If you're updating a record, you may want to ignore the post itself from the unique check.

```php
$attributes = request()->validate([
    'slug.*' => ["unique_translation:posts,slug,{$post->id}"],
    // or...
    'slug.*' => [(new UniqueTranslationRule('posts'))->ignore($post->id)],
]);
```

### Ignore Records with a Specific Column and Value

If your ID column has a different name, or you just want to use another column:

```php
$attributes = request()->validate([
    'slug.*' => ['unique_translation:posts,slug,ignore_value,ignore_column'],
    // or...
    'slug.*' => [(new UniqueTranslationRule('posts'))->ignore('ignore_value', 'ignore_column')],
]);
```

## Example

Your existing `slug`  column (JSON) in a `posts` table:

```json
{
  "en":"not-abc",
  "nl":"abc"
}
```

Your form input to create a new record:


```html
<input name="slug[en]" value="abc">
<input name="slug[nl]" value="abc">
```

Your validation logic:

```php
$attributes = request()->validate([
    'slug.*' => ['unique_translation:posts'],
]);
```

The result is that `slug[en]` is valid, since the only `en` value in the database is `not-abc`.

And `slug[nl]` would fail, because there already is a `nl` value of `abc`.


## Testing

```
vendor/bin/phpunit
```

## Security

If you discover any security related issues, please [e-mail me](mailto:ivan@codezero.be) instead of using the issue tracker.

## License

The MIT License (MIT). Please see [License File](https://github.com/codezero-be/laravel-unique-translation/blob/master/LICENSE.md) for more information.
