# dotenv-demo

Demo project explaining .env files and composer package management

## Why environment variables

Some data is valuable and should be handled in a secure manner. In some applications data cannot be  hard-coded into the code. Examples: are:

* usernames
* passwords
* tokens
* authentication data
* application or server settings

If hard-coded values are used, they will be hard to exclude from version control like git. This is especially a problem for public projects. You do not want to share your private information with other people. Its just like your VISA card number, you don't want any one to get your number.

This is real, and happens all of the time: [Dev put AWS keys on Github. Then BAD THINGS happened(https://www.theregister.co.uk/2015/01/06/dev_blunder_shows_github_crawling_with_keyslurping_bots/)

A solution can be to make use of *environment variables*.

## What are environment variables

Environment variables are pieces of data or information that we can *inject* into the execution environment. Injecting these variables can be achieved in many ways. For example using the command-line.

```cli
php -f index.php TOKEN=1234
```

In this example, `TOKEN=1234` is not an argument to the `php` command, but an environment variable.

## `.env` files

Managing environment variables using the command-line is not practical. For this reasons many projects, programming languages and libraries make use of `.env` files.

`.env` files separate all the environment variables to a single location. It separates the concerns, making it more easy to manage these variables.

`.env` files use the same notation in a text file. You can add multiple variables by placing them each on a single line. For example:

```env
TOKEN=1234
ANOTHER_VAR=foo
```

## Preventing git from tracking the `.env` files

`.env` files are a file like any other file to Git. Just try to create a `.env` add some data, and ask git for a status with `git status`. If you are not careful you might unintentionally add the file in a commit.

Ignoring it yourself might be a solution, but what with other files that git has no business with. This can quickly become unpractical and it will not prevent you to make mistakes

### `.gitignore` to the rescue

To prevent the `.env` from being tracked and added to your git repository, you can make use of an `.gitignore` file. In this file you can list all files and directories that git must ignore. These files and directories won't show up when you ask for a `git status`. This prevents accidentally adding them to a commit.

An example of a `.gitignore` file configured to ignore a `.env` will look like this:

```text
.env
```

It is important to add and commit the `.gitignore` file to your project. This will make sure that other developers won't make the same mistakes.

## Using environment variables in PHP

Now that we have a save way to manage any environment variables, lets make use of them in PHP.

To accomplish this we need an external library. To manage libraries and applications we can make use of an *package manager*. The default package manager for PHP is called **Composer**.

### Packagist

Packagist is a website that bundles the libraries and applications that can be managed with Composer. You can find Packagist at [packagist.org](https://packagist.org/).

There are many libraries that manage `.env` files available on Packagist for Composer. We will make use of a library called `phpdotenv` from the developer `vlucas`. Packagist and Composer call this library `vlucas/phpdotenv`, and can be found at [packagist.org/packages/vlucas/phpdotenv](https://packagist.org/packages/vlucas/phpdotenv).

### Installing the library using Composer

To install the library into your project, just execute the following command:

```cli
composer require vlucas/phpdotenv
```

This will install the library into your project, and will add a configuration inside a `composer.json` and `composer.lock` file.

The php code will be installed inside a `vendor` directory.

Any code coming from Composer is code that we do not own. It is code that is developed and managed by other developers, teams or companies. It is a bad thing to commit any code into your project that is developed by someone else. If any updates are released in the library it is hard to get things synchronized. Thats where the package manager comes in action. Updating, and managing libraries is the task of the package manager, not of the developer and certainly not of git. So lets prevent git from adding any library files to commits.
Luckily we already saw how to achieve this.

### Adding the `vendor` directory to the `.gitignore`

To prevent git from tracking any library files we just need to add `vendor/` to the `.gitignore` file.

The `.gitignore` file will look like this now:

```text
.env
vendor/
```

Note: Add a trailing `/` to a directory in a `.gitignore` file. This makes sure you are ignoring only a `vendor` directory and not a file called `vendor`.

### Committing `composer.*` files to git

The `composer.json` and `composer.lock` files are important for anyone that wants to run or develop the project. These must be added and committed into git.

```cli
git add composer.*
git commit -m"add composer files to project"
```

## Using the new library in your project

Inside your `index.php` file you can make use of the library. PHP Libraries make use of PSR-4 autoloading that will search for classes at run-time without the need to include or require all necessary PHP files.

### Autoloading

Add the following line to the rood document of the project. This is almost always the `index.php` file.

```php
require(__DIR__ . '/vendor/autoload.php');
```

All classes will now be loaded automatically without specifying every include or require for each use of the library.

### Create an instance of the `Dotenv` class

To make use of the library, just create an instance of the `Dotenv` class. In the constructor you need to pass the directory of the `.env` file. If the file exist in the same directory as the `index.php` file, the contant `__DIR__` can be used to specify the current directory.

Next, the `load()` method must be called to load the environment variables into the application.

```php
$dotenv = new Dotenv\Dotenv(__DIR__);
$dotenv->load();
```

### Accessing an environment variable from PHP

When ever you need to access one of the *environment variables*, you can use the `getenv()` method to get the value of a variable.

```php
$token = getenv('TOKEN');

echo $token;
```

## `.env.example` example file

Because the `.env` file is not tracked and ignored by git. It could be wise to add an example file to the project. It gives the user an idea of what the content of the `.env` file must be. Many user copy/paste the file, and change the values inside the file. An example file can be tracked by git without any problem. It does not contain important or valuable information, just some dummy data.

```text
TOKEN=abcd
```

