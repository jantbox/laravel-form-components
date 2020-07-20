# Laravel Form Components

[![Latest Version on Packagist](https://img.shields.io/packagist/v/protonemedia/laravel-form-components.svg?style=flat-square)](https://packagist.org/packages/protonemedia/laravel-form-components)
[![Build Status](https://img.shields.io/travis/pascalbaljetmedia/laravel-form-components/master.svg?style=flat-square)](https://travis-ci.org/pascalbaljetmedia/laravel-form-components)
[![Quality Score](https://img.shields.io/scrutinizer/g/pascalbaljetmedia/laravel-form-components.svg?style=flat-square)](https://scrutinizer-ci.com/g/pascalbaljetmedia/laravel-form-components)
[![Total Downloads](https://img.shields.io/packagist/dt/protonemedia/laravel-form-components.svg?style=flat-square)](https://packagist.org/packages/protonemedia/laravel-form-components)
[![Buy us a tree](https://img.shields.io/badge/Treeware-%F0%9F%8C%B3-lightgreen)](https://plant.treeware.earth/pascalbaljetmedia/laravel-form-components)

A set of Blade components to rapidly build forms with [Tailwind CSS Custom Forms](https://tailwindcss-custom-forms.netlify.app) and [Bootstrap 4](https://getbootstrap.com/docs/4.0/components/forms/). Supports validation, model binding, default values, translations, includes default vendor styling and fully customizable!

## Features

* Components for input, textarea, select, multi-select, checkbox and radio elements.
* Support for [Tailwind CSS Custom Forms](https://tailwindcss-custom-forms.netlify.app) and [Bootstrap 4 Forms](https://getbootstrap.com/docs/4.0/components/forms/).
* Component logic independent from Blade views, the Tailwind and Bootstrap views use the same logic.
* Bind a target to a form (or a set of elements) to provide default values.
* Support for Spatie's [laravel-translatable](https://github.com/spatie/laravel-translatable).
* Re-populate forms with [old input](https://laravel.com/docs/master/requests#old-input).
* Validation errors.
* Components classes and Blade views fully customizable.
* Support for prefixing the components.

## Requirements

* PHP 7.4 + Laravel 7.0 only

## Installation

You can install the package via composer:

```bash
composer require protonemedia/laravel-form-components
```

Make sure the [Tailwind plugin](https://github.com/tailwindcss/custom-forms#install) is installed and added to your `tailwind.config.js` file. If you're starting a new project, you can use the [Frontend preset for Tailwind CSS](https://github.com/laravel-frontend-presets/tailwindcss) which includes the Custom Forms plugin as well.

## Configuration

There is no configuration needed unless you want to [customize the Blade views and components](#customize-the-blade-views).

## Quick example

```blade
<x-form>
    @bind($user)
        <x-form-input name="last_name" label="Last Name" />
        <x-form-select name="country_code" :options="$options" />
        <x-form-select name="interests" :options="$multiOptions" label="Select your interests" multiple />

        <!-- \Spatie\Translatable\HasTranslations -->
        <x-form-textarea name="biography" language="nl" placeholder="Dutch Biography" />
        <x-form-textarea name="biography" language="en" placeholder="English Biography" />

        <!-- Inline radio inputs -->
        <x-form-group name="newsletter_frequency" label="Newsletter frequency" inline>
            <x-form-radio name="newsletter_frequency" value="daily" label="Daily" />
            <x-form-radio name="newsletter_frequency" value="weekly" label="Weekly" />
        </x-form-group>

        <x-form-group>
            <x-form-checkbox name="subscribe_to_newsletter" label="Subscribe to newsletter" />
            <x-form-checkbox name="agree_terms" label="Agree with terms" />
        </x-form-group>

        <x-form-submit />
    @endbind
</x-form>
```

<img src="https://github.com/pascalbaljetmedia/laravel-form-components/blob/master/quick-example-form.png?raw=true" width="450" />

## Usage

### Input and textarea elements

The minimum requirement for an `input` or `textarea` is the `name` attribute.

```blade
<x-form-input name="company_name" />
```

Optionally you can add a `label` attribute, which can be computed as well.

```blade
<x-form-input name="company_name" label="Company name" />
<x-form-input name="company_name" :label="trans('forms.company_name')" />
```

You can also choose to use a `placeholder` instead of a label, and of course you can change the `type` of the element.

```blade
<x-form-input type="email" name="current_email" placeholder="Current email address" />
```

By default every element shows validation errors but you can hide them if you want.

```blade
<x-form-textarea name="description" :show-errors="false" />
```

### Default value and binds

You can use the `default` attribute to specify the default value of the element.

```blade
<x-form-textarea name="motivation" default="I want to use this package because..." />
```

#### Binding a target

Instead of setting a default value, you can also pass in a target, like an Eloquent model. Now the component will get the value from the target by the `name`.

```blade
<x-form-textarea name="description" :bind="$video" />
```

In the example above, where `$video` is an Eloquent model, the default value will be `$video->description`.

#### Binding a target to multiple elements

You can also bind a target by using the `@bind` directive. This will bind the target to all elements until the `@endbind` directive.

```blade
<x-form>
    @bind($video)
        <x-form-input name="title" label="Title" />
        <x-form-textarea name="description" label="Description" />
    @endbind
</x-form>
```

You can even mix targets!

```blade
<x-form>
    @bind($user)
        <x-form-input name="full_name" label="Full name" />

        @bind($userProfile)
            <x-form-textarea name="biography" label="Biography" />
        @endbind

        <x-form-input name="email" label="Email address" />
    @endbind
</x-form>
```

#### Override or remove a binding

You can override the `@bind` directive by passing a target directly to the element using the `:bind` attribute. If you want to remove a binding for a specific element, pass in `false`.

```blade
<x-form>
    @bind($video)
        <x-form-input name="title" label="Title" />
        <x-form-input :bind="$videoDetails" name="subtitle" label="Subtitle" />
        <x-form-textarea :bind="false" name="description" label="Description" />
    @endbind
</x-form>
```

### Select elements

Besides the `name` attribute, the `select` element has a required `options` attribute, which should be a simple *key-value* array.

```php
$countries = [
    'be' => 'Belgium',
    'nl' => 'The Netherlands',
];
```

```blade
<x-form-select name="country_code" :options="$countries" />
```

If you want a select element where multiple options can be selected, add the `multiple` attribute to the element. If you specify a default, make sure it is an array. This applies to bound targets as well.

```blade
<x-form-select name="country_code" :options="$countries" multiple :default="['be', 'nl']" />
```

### Checkbox elements

Checkboxes have a default value of `1`, but you can customize it as well.

```blade
<x-form-checkbox name="subscribe_to_newsletter" label="Subscribe to newsletter" />
```

If you have a fieldset of multiple checkboxes, you can group them together with the `form-group` component. This component has an optional `label` attribute and you can set the `name` as well. This is a great way to handle the validation of arrays. If you disable the errors on the individual checkboxes, it will one show the validation errors once. The `form-group` component has a `show-errors` attribute that defaults to `true`.

```blade
<x-form-group name="interests" label="Pick one or more interests">
    <x-form-checkbox name="interests[]" :show-errors="false" value="laravel" label="Laravel" />
    <x-form-checkbox name="interests[]" :show-errors="false" value="tailwindcss" label="Tailwind CSS" />
</x-form-group>
```

### Radio elements

Radio elements behave exactly the same as checkboxes, except the `show-errors` attribute defaults to `false` as you almost always want to wrap multiple radio elements in a `form-group`.

You can group checkbox and radio elements on the same horizontal row by adding an `inline` attribute to the `form-group` element.

```blade
<x-form-group name="notification_channel" label="How do you want to receive your notifications?" inline>
    <x-form-checkbox name="notification_channel" value="mail" label="Mail" />
    <x-form-checkbox name="notification_channel" value="slack" label="Slack" />
</x-form-group>
```

### Old input data

When a validation errors occurs and Laravel redirects you back, the form will be re-populated with the old input data. This old data will override any binding or default value.

### Handling translations

This package supports `spatie/laravel-translatable` out of the box. You can add a `language` attribute to your element.

```blade
<x-form-input name="title" language="en" :bind="$book" />
```

This will result in the following HTML:

```html
<input name="title[en]" value="Laravel: Up & Running" />
```

To get the validation errors from the session, the name of the input will be mapped to a *dot* notation like `title.en`. This is how old input data is handled as well.

### Customize the blade views

Publish the configuration file and Blade views with the following command:

```bash
php artisan vendor:publish --provider="ProtoneMedia\LaravelFormComponents\Support\ServiceProvider"
```

You can find the Blade views in the `resources/views/vendor/form-components` folder. Optionally, in the `form-components.php` configuration file, you can change the location of the Blade view *per* component.

#### Component logic

You can bind your own component classes to any of the elements. In the `form-components.php` configuration file, you can change the class *per* component. As the logic for the components is quite complex, it is strongly recommended to duplicate the default component as a starting point and start editing. You'll find the default component classes in the `vendor/protonemedia/laravel-form-components/src/Components` folder.

### Prefix the components

You can define a prefix in the `form-components.php` configuration file.

```php
return [
    'prefix' => 'tailwind',
];
```

Now all components can be referenced like so:

```blade
<x-tailwind-form>
    <x-tailwind-form-input name="company_name" />
</x-tailwind-form>
```

### Error messages

By the default, the errors messages are positioned under the element. To show these messages, we created a `FormErrors` component. You can manually use this component as well.

```blade
<x-form>
    <x-form-input name="company_name" :show-errors="false" />

    <!-- other elements -->

    <x-form-errors name="company_name" />
</x-form>
```

### Submit button

The label defaults to *Submit* but you can use the slot to provide your own content.

```blade
<x-form-submit>
    <span class="text-green-500">Send</span>
</x-form-submit>
```

### Bootstrap 4

You can switch to [Bootstrap 4](https://getbootstrap.com/docs/4.0/components/forms/) by updating the `framework` setting in the `form-components.php` configuration file.

```php
return [
    'framework' => 'bootstrap-4',
];
```

There is a little bit of styling added to the `form.blade.php` view to add support for inline form groups. If you want to change it or remove it, [publish the assets](#customize-the-blade-views) and update the view file.

#### Input prepend and append

In addition to the Tailwind features, there is also support for [input groups](https://getbootstrap.com/docs/4.1/components/forms/#auto-sizing). Use the `prepend` and `append` slots to provide the contents.

```blade
<x-form-input name="username" label="Username">
    @slot('prepend')
        <span>@</span>
    @endslot
</x-form-input>

<x-form-input name="subdomain" label="Subdomain">
    @slot('append')
        <span>.protone.media</span>
    @endslot
</x-form-input>
```

#### Help text

You can add [block-level help text](https://getbootstrap.com/docs/4.1/components/forms/#help-text) to any element by using the `help` slot.

```blade
<x-form-input name="username" label="Username">
    @slot('help')
        <small class="form-text text-muted">
            Your username must be 8-20 characters long.
        </small>
    @endslot
</x-form-input>
```

### Testing

``` bash
composer test
```

### Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information about what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Other Laravel packages

* [`Laravel Analytics Event Tracking`](https://github.com/pascalbaljetmedia/laravel-analytics-event-tracking): Laravel package to easily send events to Google Analytics.
* [`Laravel Blade On Demand`](https://github.com/pascalbaljetmedia/laravel-blade-on-demand): Laravel package to compile Blade templates in memory.
* [`Laravel Cross Eloquent Search`](https://github.com/pascalbaljetmedia/laravel-cross-eloquent-search): Laravel package to search through multiple Eloquent models.
* [`Laravel FFMpeg`](https://github.com/pascalbaljetmedia/laravel-ffmpeg): This package provides an integration with FFmpeg for Laravel. The storage of the files is handled by Laravel's Filesystem.
* [`Laravel Paddle`](https://github.com/pascalbaljetmedia/laravel-paddle): Paddle.com API integration for Laravel with support for webhooks/events.
* [`Laravel Verify New Email`](https://github.com/pascalbaljetmedia/laravel-verify-new-email): This package adds support for verifying new email addresses: when a user updates its email address, it won't replace the old one until the new one is verified.
* [`Laravel WebDAV`](https://github.com/pascalbaljetmedia/laravel-webdav): WebDAV driver for Laravel's Filesystem.

### Security

If you discover any security related issues, please email pascal@protone.media instead of using the issue tracker.

## Credits

- [Pascal Baljet](https://github.com/protonemedia)
- [All Contributors](../../contributors)

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.

## Treeware

This package is [Treeware](https://treeware.earth). If you use it in production, then we ask that you [**buy the world a tree**](https://plant.treeware.earth/pascalbaljetmedia/laravel-form-components) to thank us for our work. By contributing to the Treeware forest you’ll be creating employment for local families and restoring wildlife habitats.
