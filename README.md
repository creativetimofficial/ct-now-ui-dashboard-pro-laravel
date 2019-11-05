# [Now-ui Dashboard Pro Laravel](https://https://www.creative-tim.com/live/now-ui-dashboard-laravel/?ref=ndl-readme) [![Tweet](https://img.shields.io/twitter/url/http/shields.io.svg?style=social&logo=twitter)](https://twitter.com/home?status=now-ui%20Dashboard%20Pro%20Laravel%E2%9D%A4%EF%B8%8F%0Ahttps%3A//now-ui-dashboard-pro-laravel.creative-tim.com/%20%23%now-ui%20%23design%20%23dashboard%20%23laravel%20%23pro%20via%20%40CreativeTim)

![version](https://img.shields.io/badge/version-1.0.1-blue.svg) ![license](https://img.shields.io/badge/license-MIT-blue.svg) [![GitHub issues open](https://img.shields.io/github/issues/creativetimofficial/ct-now-ui-dashboard-pro-laravel.svg?maxAge=2592000)](https://github.com/creativetimofficial/ct-now-ui-dashboard-pro-laravel/issues?q=is%3Aopen+is%3Aissue) [![GitHub issues closed](https://img.shields.io/github/issues-closed-raw/creativetimofficial/ct-now-ui-dashboard-pro-laravel/ct-now-ui-dashboard-pro-laravel.svg?maxAge=2592000)](https://github.com/creativetimofficial/ct-now-ui-dashboard-pro-laravel/issues?q=is%3Aissue+is%3Aclosed)

*Frontend version*: Now UI Dashboard v1.4.1. More info at https://www.creative-tim.com/product/now-ui-dashboard-pro/?ref=ndl-readme

![Product Image](https://github.com/creativetimofficial/public-assets/raw/master/now-ui-dashboard-pro-laravel/intro.gif?raw=true)

Speed up your web development with the Bootstrap 4 Admin Dashboard built for Laravel Framework 5.5 and up.

## Prerequisites

If you don't already have an Apache local environment with PHP and MySQL, use one of the following links:

 - Windows: https://updivision.com/blog/post/beginner-s-guide-to-setting-up-your-local-development-environment-on-windows
 - Linux: https://howtoubuntu.org/how-to-install-lamp-on-ubuntu
 - Mac: https://wpshout.com/quick-guides/how-to-install-mamp-on-your-mac/

Also, you will need to install Composer: https://getcomposer.org/doc/00-intro.md

## Installation

1. Unzip the downloaded archive
2. Copy and paste **now-ui-dashbaord-master** folder in your **projects** folder. Rename the folder to your project's name
3. In your terminal run `composer install`
4. Copy `.env.example` to `.env` and updated the configurations (mainly the database configuration)
5. In your terminal run `php artisan key:generate`
6. Run `php artisan migrate --seed` to create the database tables and seed the roles and users tables
7. Run `php artisan storage:link` to create the storage symlink (if you are using **Vagrant** with **Homestead** for development, remember to ssh into your virtual machine and run the command from there).

## Usage

To start testing the Pro theme, register as a user or log in using one of the default users: 

- admin type - **admin@nowui.com** with the password **secret**
- creator type - **creator@nowui.com** with the password **secret**
- member type - **member@nowui.com** with the password **secret**

Make sure to run the migrations and seeders for the above credentials to be available.

In addition to the features included in the free preset, the Pro theme also has a role management example with an updated user management, as well as tag management, category management and item management examples. All the necessary files (controllers, requests, views) are installed out of the box and all the needed routes are added to `routes/web.php`. Keep in mind that all the features can be viewed once you log in using the credentials provided above or by registering your own user.

Each role has a different privilege level and can perform a certain number of actions according to this level.  

A **member type** user can log in, update his profile and view a list of added items.  
A **creator type** user can log in, update his profile and perform actions on categories, tags and items.  
A **admin type** user can log in, update his profile and perform actions on categories, tags, items, roles and users.  

### Dashboard

You can access the dashboard either by using the "**Dashboards/Dashboard**" link in the left sidebar or by adding **/home** in the URL.

### Profile edit

You have the option to edit the current logged in user's profile information (name, email, profile picture) and password. To access this page, just click the "**Examples/Profile**" link in the left sidebar or add **/profile** in the URL.

The `App\Http\Controllers\ProfileController` handles the update of the user information and password.

```
public function update(ProfileRequest $request)
    {
        auth()->user()->update(
            $request->merge(['picture' => $request->photo ? $request->photo->store('profile', 'public') : null])
                ->except([$request->hasFile('photo') ? '' : 'picture'])
        );

        return back()->withStatus(__('Profile successfully updated.'));
    }

/**
* Change the password
*
* @param  \App\Http\Requests\PasswordRequest  $request
* @return \Illuminate\Http\RedirectResponse
*/
public function password(PasswordRequest $request)
{
    auth()->user()->update(['password' => Hash::make($request->get('password'))]);

    return back()->withStatus(__('Password successfully updated.'));
}
```

If you input the wrong data when editing the profile, don`t worry. Validation rules have been added to prevent this (see `App\Http\Requests\ProfileRequest`). If you try to change the password, you will see that additional validation rules have been added in `App\Http\Requests\PasswordRequest`. You also have  a custom validation rule that can be found in `App\Rules\CurrentPasswordCheckRule`.

```
public function rules()
{
    return [
        'old_password' => ['required', 'min:6', new CurrentPasswordCheckRule],
        'password' => ['required', 'min:6', 'confirmed', 'different:old_password'],
        'password_confirmation' => ['required', 'min:6'],
    ];
}
```

### Role management

The Pro theme allows you to add user roles. By default, the theme comes with **Admin**, **Creator** and **Member** roles. To access the role management example click the "**Examples/Role Management**" link in the left sidebar or add **/role** to the URL. Here you can add/edit new roles. Deletion is not allowed in this section.
To add a new role, click the "**Add role**" button. To edit an existing role, click the dotted menu (available on every table row) and then click "**Edit**". In both cases, you will be directed to a form which allows you to modify the name and description of a role.

The policy which authorizes the user to access the role management option is implemented in `App\Policies\RolePolicy.php`.``

You can find this functionality in `App\Http\RoleController.php`.

Also, validation rules were added so you will know exactly what to input in the form fields (see `App\Http\Requests\RoleRequest`). Note that these validation rules also apply for the role edit option.
```
public function rules()
{
    return [
        'name' => [
            'required', 'min:3', Rule::unique((new Role)->getTable())->ignore($this->route()->role->id ?? null)
        ],
        'description' => [
            'nullable', 'min:5'
        ]
    ];
}
```

### User management

The theme comes with an out of the box user management option. To access this option ,click the "**Examples/User Management**" link in the left sidebar or add **/user** to the URL.
The first thing you will see is a list of existing users. You can add new ones by clicking the "**Add user**" button (above the table on the right). On the Add user page, you will find a form which allows you to fill out the user`s name, email, role and password. All pages are generated using blade templates:

```
<div class="form-group{{ $errors->has('name') ? ' has-danger' : '' }}">
    <label class="form-control-label" for="input-name">{{ __('Name') }}</label>
    <input type="text" name="name" id="input-name" class="form-control{{ $errors->has('name') ? ' is-invalid' : '' }}" placeholder="{{ __('Name') }}" value="{{ old('name') }}" required autofocus>
    @include('alerts.feedback', ['field' => 'name'])
</div>
```

Validation rules were added to prevent errors in the form fields (see `App\Http\Requests\UserRequest`). Note that these validation rules also apply for the user edit option.

```
public function rules()
{
    return [
        'name' => [
            'required', 'min:3'
        ],
        'email' => [
            'required', 'email', Rule::unique((new User)->getTable())->ignore($this->route()->user->id ?? null)
        ],
        'role_id' => [
            'required', 'exists:'.(new Role)->getTable().',id'
        ],
        'password' => [
            $this->route()->user ? 'nullable' : 'required', 'confirmed', 'min:6'
        ]
    ];
}

/**
* Get the validation attributes that apply to the request.
*
* @return array
*/
public function attributes()
{
    return [
        'role_id' => 'role',
    ];
}
```

The policy which authorizes the user to access the user management pages is implemented in `App\Policies\UserPolicy.php`.``

Once you add more users, the list will grow and for every user you will have edit and delete options (access these options by clicking the three dotted menu that appears at the end of every row).

All the sample code for the user management can be found in `App\Http\Controllers\UserController`. See store method example bellow:

```
public function store(UserRequest $request, User $model)
{
    $model->create($request->merge([
        'picture' => $request->photo ? $request->photo->store('profile', 'public') : null,
        'password' => Hash::make($request->get('password'))
    ])->all());

    return redirect()->route('user.index')->withStatus(__('User successfully created.'));
}
```

### Tag management

The Pro theme also comes with a tag management option. To access this example, click the "**Examples/Tag Management**" link in the left sidebar or add **/tag** to the URL.
In this section you can add and edit tags, but you can only delete them if they are not attached to any items. You can add new ones by clicking the "**Add tag**" button (above the table on the right). You will then be directed to a form which allows you to add new tags.
Although you can add in the form only the name and color of a tag, you can write your own migrations to extend this functionality.

```
public function destroy(Tag $tag)
{
    if (!$tag->items->isEmpty()) {
        return redirect()->route('tag.index')->withErrors(__('This tag has items attached and can\'t be deleted.'));
    }

    $tag->delete();

    return redirect()->route('tag.index')->withStatus(__('Tag successfully deleted.'));
}
```

The policy which authorizes the user to access tags management pages is implemented in `App\Policies\TagPolicy.php`.

### Category management

Out of the box you will have an example of category management (for the cases in which you are developing a blog or a shop). To access this example, click the "**Examples/Category Management**" link in the left sidebar or add **/category** to the URL.
You can add and edit categories here, but you can only delete them if they are not attached to any items.

```
@if ($tag->items->isEmpty() && auth()->user()->can('delete', $tag))
    <form action="{{ route('tag.destroy', $tag) }}" method="post" style="display:inline-block;" class ="delete-form">
        @csrf
        @method('delete')
        <button type="button" rel="tooltip" class="btn btn-danger btn-icon btn-sm delete-button" data-original-title="" title="" onclick="demo.showSwal('warning-message-and-confirmation')">
        <i class="now-ui-icons ui-1_simple-remove"></i>
        </button>
    </form>
@endif
```

The policy which authorizes the user on categories management pages is implemented in `App\Policies\CategoryPolicy.php`.

### Item management

Item management is the most advanced example included in the Pro theme, because every item has a picture, belongs to a category and has multiple tags. To access this example click the "**Examples/Item Management**" link in the left sidebar or add **/item** to the URL.
Here you can manage the items. A list of items will appear once you start adding them (to access the add page click "**Add item**").
On the add page, besides the Name and Description fields (which are present in most of the CRUD examples) you can see a category dropdown, which contains the categories you added, a file input and a tag multi select. If you did not add any categories or tags, please go to the corresponding sections (category management, tag management) and add some.  


The code for saving a new item is a bit different than before (see snippet bellow):

```
public function store(ItemRequest $request, Item $model)
{
    $item = $model->create($request->merge([
        'picture' => $request->photo->store('pictures', 'public'),
        'show_on_homepage' => $request->show_on_homepage ? 1 : 0,
        'options' => $request->options ? $request->options : null,
        'date' => $request->date ? Carbon::parse($request->date)->format('Y-m-d') : null
    ])->all());

    $item->tags()->sync($request->get('tags'));

    return redirect()->route('item.index')->withStatus(__('Item successfully created.'));
}
```

Notice how the picture and tags are easily saved in the database and on the disk.

Similar to all the examples included in the theme, this one also has validation rules in place. Note that the picture is mandatory only on the item creation. On the edit page, if no new picture is added, the old picture will not be modified.

```
public function rules()
{
    return [
        'name' => [
            'required', 'min:3', Rule::unique((new Item)->getTable())->ignore($this->route()->item->id ?? null)
        ],
        'category_id' => [
            'required', 'exists:'.(new Category)->getTable().',id'
        ],
        'description' => [
            'required'
        ],
        'photo' => [
            $this->route()->item ? 'nullable' : 'required', 'image'
        ],
        'tags' => [
            'required'
        ],
        'tags.*' => [
            'exists:'.(new Tag)->getTable().',id'
        ],
        'status' => [
            'required',
            'in:published,draft,archive'
        ],
        'date' => [
            'required',
            'date_format:d-m-Y'
        ]
    ];
}
```

In the item management we have an **observer** (`app\Observers\ItemObserver`) example. This observer handles the deletion of the picture from the disk when the item is deleted or when the picture is changed via the edit form. The **observer** also removes the association between the item and the tags.

```
public function deleting(Item  $item)
{
    File::delete(storage_path("/app/public/{$item->picture}"));
    
    $item->tags()->detach();
}
```

The policy which authorizes the user on item management pages is implemented in `App\Policies\ItemPolicy.php`.

## Table of Contents

* [Versions](#versions)
* [Demo](#demo)
* [Documentation](#documentation)
* [File Structure](#file-structure)
* [Browser Support](#browser-support)
* [Resources](#resources)
* [Reporting Issues](#reporting-issues)
* [Licensing](#licensing)
* [Useful Links](#useful-links)

## Versions

[<img src="https://github.com/creativetimofficial/public-assets/blob/master/logos/html-logo.jpg?raw=true" width="60" height="60" />](https://demos.creative-tim.com/argon-dashboard-pro/pages/dashboards/dashboard.html?ref=ndl-readme)
[<img src="https://github.com/creativetimofficial/public-assets/blob/master/logos/laravel_logo.png?raw=true" width="60" height="60" />](https://argon-dashboard-pro-laravel.creative-tim.com/?ref=ndl-readme)

| HTML | LARAVEL |
| --- | --- |
| [![now-ui Dashboard Pro HTML](https://s3.amazonaws.com/creativetim_bucket/products/72/original/opt_nudp_thumbnail.jpg?1517588807)](https://demos.creative-tim.com/now-ui-dashboard-pro/examples/dashboard.html?ref=ndl-readme) | [![now-ui Dashboard Pro Laravel](https://s3.amazonaws.com/creativetim_bucket/products/210/original/opt_nudp_laravel_thumbnail.jpg?1567087239)](https://www.creative-tim.com/live/now-ui-dashboard-pro-laravel/?ref=ndl-readme)

## Demo

| Register | Login | Dashboard |
| --- | --- | ---  |
| [![Register](https://github.com/creativetimofficial/public-assets/raw/master/now-ui-dashboard-pro-laravel/register.png)](https://now-ui-dashboard-pro-laravel.creative-tim.com/register?ref=ndl-readme)  | [![Login](https://github.com/creativetimofficial/public-assets/raw/master/now-ui-dashboard-pro-laravel/login.png)](https://now-ui-dashboard-pro-laravel.creative-tim.com/login?ref=ndl-readme)  | [![Dashboard](https://github.com/creativetimofficial/public-assets/raw/master/now-ui-dashboard-pro-laravel/dashboard.png)](https://now-ui-dashboard-pro-laravel.creative-tim.com/?ref=ndl-readme)

| Profile Page | Users Page | Tables Page  |
| --- | --- | ---  |
| [![Profile Page](https://github.com/creativetimofficial/public-assets/raw/master/now-ui-dashboard-pro-laravel/profile.png)](https://now-ui-dashboard-pro-laravel.creative-tim.com/profile?ref=ndl-readme)  | [![Users Page](https://github.com/creativetimofficial/public-assets/raw/master/now-ui-dashboard-pro-laravel/users.png)](https://now-ui-dashboard-pro-laravel.creative-tim.com/user?ref=ndl-readme) | [![Tables Page](https://github.com/creativetimofficial/public-assets/raw/master/now-ui-dashboard-pro-laravel/tables.png)](https://now-ui-dashboard-pro-laravel.creative-tim.com/extendedt)
[View More](https://www.creative-tim.com/live/now-ui-dashboard-pro-laravel/?ref=ndl-readme)

## Documentation
The documentation for the now-ui Dashboard Laravel is hosted at our [website](https://www.creative-tim.com/live/now-ui-dashboard-pro-laravel/?start-page=/docs/getting-started/laravel-setup.html&ref=ndl-readme).

## File Structure
```
.
├── app
│   ├── Category.php
│   ├── Console
│   │   └── Kernel.php
│   ├── Exceptions
│   │   └── Handler.php
│   ├── Http
│   │   ├── Controllers
│   │   │   ├── Auth
│   │   │   │   ├── ForgotPasswordController.php
│   │   │   │   ├── LoginController.php
│   │   │   │   ├── RegisterController.php
│   │   │   │   ├── ResetPasswordController.php
│   │   │   │   └── VerificationController.php
│   │   │   ├── CategoryController.php
│   │   │   ├── Controller.php
│   │   │   ├── HomeController.php
│   │   │   ├── ItemController.php
│   │   │   ├── PageController.php
│   │   │   ├── ProfileController.php
│   │   │   ├── RoleController.php
│   │   │   ├── TagController.php
│   │   │   └── UserController.php
│   │   ├── Kernel.php
│   │   ├── Middleware
│   │   │   ├── Authenticate.php
│   │   │   ├── CheckForMaintenanceMode.php
│   │   │   ├── EncryptCookies.php
│   │   │   ├── RedirectIfAuthenticated.php
│   │   │   ├── TrimStrings.php
│   │   │   ├── TrustProxies.php
│   │   │   └── VerifyCsrfToken.php
│   │   └── Requests
│   │       ├── CategoryRequest.php
│   │       ├── ItemRequest.php
│   │       ├── PasswordRequest.php
│   │       ├── ProfileRequest.php
│   │       ├── RoleRequest.php
│   │       ├── TagRequest.php
│   │       └── UserRequest.php
│   ├── Item.php
│   ├── Observers
│   │   ├── ItemObserver.php
│   │   └── UserObserver.php
│   ├── Policies
│   │   ├── CategoryPolicy.php
│   │   ├── ItemPolicy.php
│   │   ├── RolePolicy.php
│   │   ├── TagPolicy.php
│   │   └── UserPolicy.php
│   ├── Providers
│   │   ├── AppServiceProvider.php
│   │   ├── AuthServiceProvider.php
│   │   ├── BroadcastServiceProvider.php
│   │   ├── EventServiceProvider.php
│   │   └── RouteServiceProvider.php
│   ├── Role.php
│   ├── Rules
│   │   └── CurrentPasswordCheckRule.php
│   ├── Tag.php
│   └── User.php
├── artisan
├── bootstrap
│   ├── app.php
│   └── cache
│       ├── packages.php
│       └── services.php
├── CHANGELOG.md
├── composer.json
├── composer.lock
├── config
│   ├── app.php
│   ├── auth.php
│   ├── broadcasting.php
│   ├── cache.php
│   ├── database.php
│   ├── filesystems.php
│   ├── hashing.php
│   ├── items.php
│   ├── logging.php
│   ├── mail.php
│   ├── queue.php
│   ├── services.php
│   ├── session.php
│   └── view.php
├── database
│   ├── factories
│   │   └── UserFactory.php
│   ├── migrations
│   │   ├── 2014_10_12_100000_create_password_resets_table.php
│   │   ├── 2019_01_15_100000_create_roles_table.php
│   │   ├── 2019_01_15_110000_create_users_table.php
│   │   ├── 2019_01_17_121504_create_categories_table.php
│   │   ├── 2019_01_21_130422_create_tags_table.php
│   │   ├── 2019_01_21_163402_create_items_table.php
│   │   ├── 2019_01_21_163414_create_item_tag_table.php
│   │   ├── 2019_03_06_132557_add_photo_column_to_users_table.php
│   │   ├── 2019_03_06_143255_add_fields_to_items_table.php
│   │   └── 2019_03_20_090438_add_color_tags_table.php
│   └── seeds
│       ├── CategoriesTableSeeder.php
│       ├── DatabaseSeeder.php
│       ├── ItemsTableSeeder.php
│       ├── RolesTableSeeder.php
│       ├── TagsTableSeeder.php
│       └── UsersTableSeeder.php
├── ISSUE_TEMPLATE.md
├── out.txt
├── package.json
├── phpunit.xml
├── public
│   ├── assets
│   │   ├── css
│   │   │   ├── bootstrap.min.css
│   │   │   ├── bootstrap.min.css.map
│   │   │   ├── now-ui-dashboard.css
│   │   │   ├── now-ui-dashboard.css.map
│   │   │   └── now-ui-dashboard.min.css
│   │   ├── demo
│   │   │   ├── demo.css
│   │   │   └── demo.js
│   │   ├── fonts
│   │   │   ├── nucleo-license.md
│   │   │   ├── nucleo-outline.eot
│   │   │   ├── nucleo-outline.ttf
│   │   │   ├── nucleo-outline.woff
│   │   │   └── nucleo-outline.woff2
│   │   ├── img
│   │   │   ├── apple-icon.png
│   │   │   ├── AU.png
│   │   │   ├── balmain.jpg
│   │   │   ├── bg13.jpg
│   │   │   ├── bg14.jpg
│   │   │   ├── bg15.jpg
│   │   │   ├── bg16.jpg
│   │   │   ├── bg1.jpg
│   │   │   ├── bg3.jpg
│   │   │   ├── bg5.jpg
│   │   │   ├── BR.png
│   │   │   ├── default-avatar.png
│   │   │   ├── DE.png
│   │   │   ├── emilyz.jpg
│   │   │   ├── favicon.png
│   │   │   ├── GB.png
│   │   │   ├── header.jpg
│   │   │   ├── image_placeholder.jpg
│   │   │   ├── james.jpg
│   │   │   ├── mike.jpg
│   │   │   ├── now-logo.png
│   │   │   ├── placeholder.jpg
│   │   │   ├── prada.jpg
│   │   │   ├── RO.png
│   │   │   ├── saint-laurent.jpg
│   │   │   └── US.png
│   │   ├── js
│   │   │   ├── core
│   │   │   │   ├── bootstrap.min.js
│   │   │   │   ├── jquery.min.js
│   │   │   │   └── popper.min.js
│   │   │   ├── now-ui-dashboard.js
│   │   │   ├── now-ui-dashboard.js.map
│   │   │   ├── now-ui-dashboard.min.js
│   │   │   └── plugins
│   │   │       ├── bootstrap-datetimepicker.js
│   │   │       ├── bootstrap-notify.js
│   │   │       ├── bootstrap-selectpicker.js
│   │   │       ├── bootstrap-switch.js
│   │   │       ├── bootstrap-tagsinput.js
│   │   │       ├── chartjs.min.js
│   │   │       ├── fullcalendar.min.js
│   │   │       ├── jasny-bootstrap.min.js
│   │   │       ├── jquery.bootstrap-wizard.js
│   │   │       ├── jquery.dataTables.min.js
│   │   │       ├── jquery-jvectormap.js
│   │   │       ├── jquery.validate.min.js
│   │   │       ├── moment.min.js
│   │   │       ├── nouislider.min.js
│   │   │       ├── perfect-scrollbar.jquery.min.js
│   │   │       └── sweetalert2.min.js
│   │   └── scss
│   │       ├── now-ui-dashboard
│   │       │   ├── _alerts.scss
│   │       │   ├── _badges.scss
│   │       │   ├── _buttons.scss
│   │       │   ├── cards
│   │       │   │   ├── _card-background.scss
│   │       │   │   ├── _card-chart.scss
│   │       │   │   ├── _card-collapse.scss
│   │       │   │   ├── _card-contributions.scss
│   │       │   │   ├── _card-info-area.scss
│   │       │   │   ├── _card-lock.scss
│   │       │   │   ├── _card-map.scss
│   │       │   │   ├── _card-plain.scss
│   │       │   │   ├── _card-pricing.scss
│   │       │   │   ├── _card-profile.scss
│   │       │   │   ├── _card-signup.scss
│   │       │   │   ├── _card-stats-mini.scss
│   │       │   │   ├── _card-stats.scss
│   │       │   │   ├── _card-subcategories.scss
│   │       │   │   ├── _card-testimonials.scss
│   │       │   │   ├── _card-user.scss
│   │       │   │   └── _card-wizard.scss
│   │       │   ├── _cards.scss
│   │       │   ├── _carousel.scss
│   │       │   ├── _checkboxes-radio.scss
│   │       │   ├── _dropdown.scss
│   │       │   ├── _example-pages.scss
│   │       │   ├── _fixed-plugin.scss
│   │       │   ├── _footers.scss
│   │       │   ├── _images.scss
│   │       │   ├── _info-areas.scss
│   │       │   ├── _inputs.scss
│   │       │   ├── _media-queries.scss
│   │       │   ├── _misc.scss
│   │       │   ├── mixins
│   │       │   │   ├── _badges.scss
│   │       │   │   ├── _buttons.scss
│   │       │   │   ├── _cards.scss
│   │       │   │   ├── _dropdown.scss
│   │       │   │   ├── _inputs.scss
│   │       │   │   ├── _modals.scss
│   │       │   │   ├── _page-header.scss
│   │       │   │   ├── _popovers.scss
│   │       │   │   ├── _sidebar.scss
│   │       │   │   ├── _social-buttons.scss
│   │       │   │   ├── _tags.scss
│   │       │   │   ├── _transparency.scss
│   │       │   │   ├── _vendor-prefixes.scss
│   │       │   │   └── _wizard.scss
│   │       │   ├── _mixins.scss
│   │       │   ├── _modals.scss
│   │       │   ├── _navbar.scss
│   │       │   ├── _nucleo-outline.scss
│   │       │   ├── _page-header.scss
│   │       │   ├── _pagination.scss
│   │       │   ├── _pills.scss
│   │       │   ├── plugins
│   │       │   │   ├── _plugin-animate-bootstrap-notify.scss
│   │       │   │   ├── _plugin-bootstrap-select.scss
│   │       │   │   ├── _plugin-bootstrap-switch.scss
│   │       │   │   ├── _plugin-card-wizard.scss
│   │       │   │   ├── _plugin-datatables.net.scss
│   │       │   │   ├── _plugin-datetimepicker.scss
│   │       │   │   ├── _plugin-fullcalendar.scss
│   │       │   │   ├── _plugin-jasny-fileupload.scss
│   │       │   │   ├── _plugin-jquery.jvectormap.scss
│   │       │   │   ├── _plugin-nouislider.scss
│   │       │   │   ├── _plugin-perfect-scrollbar.scss
│   │       │   │   └── _plugin-tagsinput.scss
│   │       │   ├── _popups.scss
│   │       │   ├── _progress.scss
│   │       │   ├── _responsive.scss
│   │       │   ├── _rtl.scss
│   │       │   ├── _sections.scss
│   │       │   ├── _sidebar-and-main-panel.scss
│   │       │   ├── _social-buttons.scss
│   │       │   ├── _tables.scss
│   │       │   ├── _tabs.scss
│   │       │   ├── _timeline.scss
│   │       │   ├── _typography.scss
│   │       │   └── _variables.scss
│   │       └── now-ui-dashboard.scss
│   ├── css
│   │   ├── bootstrap.min.css
│   │   ├── bootstrap.min.css.map
│   │   ├── now-ui-dashboard.css
│   │   ├── now-ui-dashboard.css.map
│   │   └── now-ui-dashboard.min.css
│   ├── fonts
│   │   ├── nucleo-license.md
│   │   ├── nucleo-outline.eot
│   │   ├── nucleo-outline.ttf
│   │   ├── nucleo-outline.woff
│   │   └── nucleo-outline.woff2
│   ├── img
│   │   ├── apple-icon.png
│   │   ├── AU.png
│   │   ├── balmain.jpg
│   │   ├── bg13.jpg
│   │   ├── bg14.jpg
│   │   ├── bg15.jpg
│   │   ├── bg16.jpg
│   │   ├── bg1.jpg
│   │   ├── bg3.jpg
│   │   ├── bg5.jpg
│   │   ├── BR.png
│   │   ├── default-avatar.png
│   │   ├── DE.png
│   │   ├── emilyz.jpg
│   │   ├── favicon.png
│   │   ├── GB.png
│   │   ├── header.jpg
│   │   ├── image_placeholder.jpg
│   │   ├── james.jpg
│   │   ├── mike.jpg
│   │   ├── now-logo.png
│   │   ├── placeholder.jpg
│   │   ├── prada.jpg
│   │   ├── RO.png
│   │   ├── saint-laurent.jpg
│   │   └── US.png
│   ├── index.php
│   ├── now
│   │   ├── css
│   │   │   ├── bootstrap.min.css
│   │   │   ├── bootstrap.min.css.map
│   │   │   ├── now-ui-dashboard.css
│   │   │   ├── now-ui-dashboard.css.map
│   │   │   └── now-ui-dashboard.min.css
│   │   ├── demo
│   │   │   ├── demo.css
│   │   │   └── demo.js
│   │   ├── img
│   │   │   ├── apple-icon.png
│   │   │   ├── AU.png
│   │   │   ├── balmain.jpg
│   │   │   ├── bg13.jpg
│   │   │   ├── bg14.jpg
│   │   │   ├── bg15.jpg
│   │   │   ├── bg16.jpg
│   │   │   ├── bg1.jpg
│   │   │   ├── bg3.jpg
│   │   │   ├── bg5.jpg
│   │   │   ├── BR.png
│   │   │   ├── default-avatar.png
│   │   │   ├── DE.png
│   │   │   ├── emilyz.jpg
│   │   │   ├── favicon.png
│   │   │   ├── GB.png
│   │   │   ├── header.jpg
│   │   │   ├── image_placeholder.jpg
│   │   │   ├── james.jpg
│   │   │   ├── mike.jpg
│   │   │   ├── now-logo.png
│   │   │   ├── placeholder.jpg
│   │   │   ├── prada.jpg
│   │   │   ├── RO.png
│   │   │   ├── saint-laurent.jpg
│   │   │   └── US.png
│   │   └── js
│   │       ├── core
│   │       │   ├── bootstrap.min.js
│   │       │   ├── jquery.min.js
│   │       │   └── popper.min.js
│   │       ├── now-ui-dashboard.js
│   │       ├── now-ui-dashboard.js.map
│   │       ├── now-ui-dashboard.min.js
│   │       └── plugins
│   │           ├── bootstrap-datetimepicker.js
│   │           ├── bootstrap-notify.js
│   │           ├── bootstrap-selectpicker.js
│   │           ├── bootstrap-switch.js
│   │           ├── bootstrap-tagsinput.js
│   │           ├── chartjs.min.js
│   │           ├── fullcalendar.min.js
│   │           ├── jasny-bootstrap.min.js
│   │           ├── jquery.bootstrap-wizard.js
│   │           ├── jquery.dataTables.min.js
│   │           ├── jquery-jvectormap.js
│   │           ├── jquery.validate.min.js
│   │           ├── moment.min.js
│   │           ├── nouislider.min.js
│   │           ├── perfect-scrollbar.jquery.min.js
│   │           └── sweetalert2.min.js
│   └── scss
│       ├── now-ui-dashboard
│       │   ├── _alerts.scss
│       │   ├── _badges.scss
│       │   ├── _buttons.scss
│       │   ├── cards
│       │   │   ├── _card-background.scss
│       │   │   ├── _card-chart.scss
│       │   │   ├── _card-collapse.scss
│       │   │   ├── _card-contributions.scss
│       │   │   ├── _card-info-area.scss
│       │   │   ├── _card-lock.scss
│       │   │   ├── _card-map.scss
│       │   │   ├── _card-plain.scss
│       │   │   ├── _card-pricing.scss
│       │   │   ├── _card-profile.scss
│       │   │   ├── _card-signup.scss
│       │   │   ├── _card-stats-mini.scss
│       │   │   ├── _card-stats.scss
│       │   │   ├── _card-subcategories.scss
│       │   │   ├── _card-testimonials.scss
│       │   │   ├── _card-user.scss
│       │   │   └── _card-wizard.scss
│       │   ├── _cards.scss
│       │   ├── _carousel.scss
│       │   ├── _checkboxes-radio.scss
│       │   ├── _dropdown.scss
│       │   ├── _example-pages.scss
│       │   ├── _fixed-plugin.scss
│       │   ├── _footers.scss
│       │   ├── _images.scss
│       │   ├── _info-areas.scss
│       │   ├── _inputs.scss
│       │   ├── _media-queries.scss
│       │   ├── _misc.scss
│       │   ├── mixins
│       │   │   ├── _badges.scss
│       │   │   ├── _buttons.scss
│       │   │   ├── _cards.scss
│       │   │   ├── _dropdown.scss
│       │   │   ├── _inputs.scss
│       │   │   ├── _modals.scss
│       │   │   ├── _page-header.scss
│       │   │   ├── _popovers.scss
│       │   │   ├── _sidebar.scss
│       │   │   ├── _social-buttons.scss
│       │   │   ├── _tags.scss
│       │   │   ├── _transparency.scss
│       │   │   ├── _vendor-prefixes.scss
│       │   │   └── _wizard.scss
│       │   ├── _mixins.scss
│       │   ├── _modals.scss
│       │   ├── _navbar.scss
│       │   ├── _nucleo-outline.scss
│       │   ├── _page-header.scss
│       │   ├── _pagination.scss
│       │   ├── _pills.scss
│       │   ├── plugins
│       │   │   ├── _plugin-animate-bootstrap-notify.scss
│       │   │   ├── _plugin-bootstrap-select.scss
│       │   │   ├── _plugin-bootstrap-switch.scss
│       │   │   ├── _plugin-card-wizard.scss
│       │   │   ├── _plugin-datatables.net.scss
│       │   │   ├── _plugin-datetimepicker.scss
│       │   │   ├── _plugin-fullcalendar.scss
│       │   │   ├── _plugin-jasny-fileupload.scss
│       │   │   ├── _plugin-jquery.jvectormap.scss
│       │   │   ├── _plugin-nouislider.scss
│       │   │   ├── _plugin-perfect-scrollbar.scss
│       │   │   └── _plugin-tagsinput.scss
│       │   ├── _popups.scss
│       │   ├── _progress.scss
│       │   ├── _responsive.scss
│       │   ├── _rtl.scss
│       │   ├── _sections.scss
│       │   ├── _sidebar-and-main-panel.scss
│       │   ├── _social-buttons.scss
│       │   ├── _tables.scss
│       │   ├── _tabs.scss
│       │   ├── _timeline.scss
│       │   ├── _typography.scss
│       │   └── _variables.scss
│       └── now-ui-dashboard.scss
├── README.md
├── resources
│   ├── js
│   │   ├── app.js
│   │   ├── bootstrap.js
│   │   └── components
│   │       └── ExampleComponent.vue
│   ├── lang
│   │   └── en
│   │       ├── auth.php
│   │       ├── pagination.php
│   │       ├── passwords.php
│   │       └── validation.php
│   ├── sass
│   │   ├── app.scss
│   │   └── _variables.scss
│   └── views
│       ├── alerts
│       │   ├── errors.blade.php
│       │   ├── error_self_update.blade.php
│       │   ├── feedback.blade.php
│       │   ├── migrations_check.blade.php
│       │   └── success.blade.php
│       ├── auth
│       │   ├── login.blade.php
│       │   ├── passwords
│       │   │   ├── email.blade.php
│       │   │   └── reset.blade.php
│       │   ├── register.blade.php
│       │   └── verify.blade.php
│       ├── categories
│       │   ├── create.blade.php
│       │   ├── edit.blade.php
│       │   └── index.blade.php
│       ├── home.blade.php
│       ├── items
│       │   ├── create.blade.php
│       │   ├── edit.blade.php
│       │   └── index.blade.php
│       ├── layouts
│       │   ├── app.blade.php
│       │   ├── footer.blade.php
│       │   ├── navbars
│       │   │   ├── auth.blade.php
│       │   │   ├── guest.blade.php
│       │   │   └── navs
│       │   │       ├── auth.blade.php
│       │   │       └── guest.blade.php
│       │   └── page_template
│       │       ├── auth.blade.php
│       │       └── guest.blade.php
│       ├── pages
│       │   ├── buttons.blade.php
│       │   ├── calendar.blade.php
│       │   ├── charts.blade.php
│       │   ├── forms
│       │   │   ├── extended.blade.php
│       │   │   ├── regular.blade.php
│       │   │   ├── validation.blade.php
│       │   │   └── wizard.blade.php
│       │   ├── grid.blade.php
│       │   ├── icons.blade.php
│       │   ├── lock.blade.php
│       │   ├── maps
│       │   │   ├── fullscreen.blade.php
│       │   │   ├── google.blade.php
│       │   │   └── vector.blade.php
│       │   ├── notifications.blade.php
│       │   ├── panels.blade.php
│       │   ├── pricing.blade.php
│       │   ├── support.blade.php
│       │   ├── sweet-alert.blade.php
│       │   ├── tables
│       │   │   ├── datatables.blade.php
│       │   │   ├── extendedt.blade.php
│       │   │   └── regulart.blade.php
│       │   ├── timeline.blade.php
│       │   ├── typography.blade.php
│       │   └── widgets.blade.php
│       ├── profile
│       │   └── edit.blade.php
│       ├── roles
│       │   ├── create.blade.php
│       │   ├── edit.blade.php
│       │   └── index.blade.php
│       ├── tags
│       │   ├── create.blade.php
│       │   ├── edit.blade.php
│       │   └── index.blade.php
│       ├── users
│       │   ├── create.blade.php
│       │   ├── edit.blade.php
│       │   └── index.blade.php
│       └── welcome.blade.php
├── routes
│   ├── api.php
│   ├── channels.php
│   ├── console.php
│   └── web.php
├── server.php
├── storage
│   ├── app
│   │   └── public
│   │       ├── pictures
│   │       └── profile
│   ├── framework
│   │   ├── cache
│   │   ├── sessions
│   │   ├── testing
│   │   └── views
│   └── logs
├── tests
│   ├── CreatesApplication.php
│   ├── Feature
│   │   └── ExampleTest.php
│   ├── TestCase.php
│   └── Unit
│       └── ExampleTest.php
├── webpack.mix.js
└── yarn.lock
```

## Browser Support

At present, we officially aim to support the last two versions of the following browsers:

<img src="https://github.com/creativetimofficial/public-assets/blob/master/logos/chrome-logo.png?raw=true" width="64" height="64"> <img src="https://raw.githubusercontent.com/creativetimofficial/public-assets/master/logos/firefox-logo.png" width="64" height="64"> <img src="https://raw.githubusercontent.com/creativetimofficial/public-assets/master/logos/edge-logo.png" width="64" height="64"> <img src="https://raw.githubusercontent.com/creativetimofficial/public-assets/master/logos/safari-logo.png" width="64" height="64"> <img src="https://raw.githubusercontent.com/creativetimofficial/public-assets/master/logos/opera-logo.png" width="64" height="64">


## Resources
- Demo: <https://www.creative-tim.com/live/now-ui-dashboard-pro-laravel/?ref=ndl-readme>
- Download Page: <https://www.creative-tim.com/product/now-ui-dashboard-pro-laravel?ref=ndl-readme>
- Documentation: <https://www.creative-tim.com/live/now-ui-dashboard-pro-laravel/?start-page=/docs/getting-started/laravel-setup.html&ref=ndl-readme>
- License Agreement: <https://www.creative-tim.com/license?ref=ndl-readme>
- Support: <https://www.creative-tim.com/contact-us?ref=ndl-readme>
- Issues: [Github Issues Page](https://github.com/creativetimofficial/now-ui-dashboard-pro-laravel/issues)
- **Dashboards:**

| HTML | LARAVEL |
| --- | --- |
| [![now-ui Dashboard Pro HTML](https://s3.amazonaws.com/creativetim_bucket/products/72/original/opt_nudp_thumbnail.jpg?1517588807)](https://demos.creative-tim.com/now-ui-dashboard-pro/examples/dashboard.html?ref=ndl-readme) | [![now-ui Dashboard Pro Laravel](https://s3.amazonaws.com/creativetim_bucket/products/210/original/opt_nudp_laravel_thumbnail.jpg?1567087239)](https://www.creative-tim.com/live/now-ui-dashboard-pro-laravel/?ref=ndl-readme)

## Change log

Please see the [changelog](CHANGELOG.md) for more information on what has changed recently.

## Credits

- [Creative Tim](https://creative-tim.com/?ref=ndl-readme)
- [UPDIVISION](https://updivision.com)

## Reporting Issues

We use GitHub Issues as the official bug tracker for the now-ui Dashboard Laravel. Here are some advices for our users that want to report an issue:

1. Make sure that you are using the latest version of the now-ui Dashboard Laravel. Check the CHANGELOG from your dashboard on our [website](https://www.creative-tim.com/?ref=ndl-readme).
2. Providing us reproducible steps for the issue will shorten the time it takes for it to be fixed.
3. Some issues may be browser specific, so specifying in what browser you encountered the issue might help.

## Licensing

- Copyright 2019 Creative Tim (https://www.creative-tim.com/?ref=ndl-readme)
- [Creative Tim License](https://www.creative-tim.com/license?ref=ndl-readme).


## Useful Links

- [Tutorials](https://www.youtube.com/channel/UCVyTG4sCw-rOvB9oHkzZD1w?ref=ndl-readme)
- [Affiliate Program](https://www.creative-tim.com/affiliates/new?ref=ndl-readme) (earn money)
- [Blog Creative Tim](http://blog.creative-tim.com/?ref=ndl-readme)
- [Free Products](https://www.creative-tim.com/bootstrap-themes/free?ref=ndl-readme) from Creative Tim
- [Premium Products](https://www.creative-tim.com/bootstrap-themes/premium?ref=ndl-readme) from Creative Tim
- [React Products](https://www.creative-tim.com/bootstrap-themes/react-themes?ref=ndl-readme) from Creative Tim
- [Angular Products](https://www.creative-tim.com/bootstrap-themes/angular-themes?ref=ndl-readme) from Creative Tim
- [VueJS Products](https://www.creative-tim.com/bootstrap-themes/vuejs-themes?ref=ndl-readme) from Creative Tim
- [More products](https://www.creative-tim.com/bootstrap-themes?ref=ndl-readme) from Creative Tim
- Check our Bundles [here](https://www.creative-tim.com/bundles??ref=ndl-readme)

## Social Media

### Creative Tim:

Twitter: <https://twitter.com/CreativeTim?ref=ndl-readme>

Facebook: <https://www.facebook.com/CreativeTim?ref=ndl-readme>

Dribbble: <https://dribbble.com/creativetim?ref=ndl-readme>

Instagram: <https://www.instagram.com/CreativeTimOfficial?ref=ndl-readme>


### Updivision:

Twitter: <https://twitter.com/updivision?ref=ndl-readme>

Facebook: <https://www.facebook.com/updivision?ref=ndl-readme>

Linkedin: <https://www.linkedin.com/company/updivision?ref=ndl-readme>

Updivision Blog: <https://updivision.com/blog/?ref=ndl-readme>

## Credits

- [Creative Tim](https://creative-tim.com/?ref=ndl-readme)
- [UPDIVISION](https://updivision.com)
