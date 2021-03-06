# Hydra

Simple and clean testing for Laravel and Lumen packages. Supports Laravel/Lumen 5.8.x, 6.x, 7.x and 8.x.

## Stability disclaimer

This package is still in early development stage, and there are many bugs and planned features. Use it at your own risk.

## Requirements

- PHP 7.1.3 to 8.0

## Acknowledgements

This package what greatly inspired by the [orchestra/testbench](https://github.com/orchestral/testbench) package.

## Configuration

Hydra configuration must be written at the base of your project. The recognized file names are the following:

- hydra.yaml
- hydra.yml
- hydra.json
- hydra.php
- .hydra.yaml
- .hydra.yml
- .hydra.json
- .hydra.php

### `sandbox` (defaults to `./hydra`)

The hydra base directory, where the benches will be installed.

### `benches`

A bench is described by a name (its key) and the framework (`laravel` or `lumen`) and the framework version constraint.
For example, the following configuration:

```yaml
benches:
  laravel-7:
    framework: laravel
    constraint: ^7.0
```

will create a bench `laravel-7` using the `laravel/laravel` framework and the SemVer constraint `^7.0`.

### `composer` (defaults to `composer`)

Since Hydra relies on the composer executable, you may want to override the composer executable.

For example:

```yaml
composer: /user/bin/composer.phar
```

## Usage

### Installing benches

To install the benches defined in the configuration file, run:

```shell
vendor/bin/hydra install
```

You may use the `--only [bench]` to install only a single bench (during matrix pipelines).


### Cleaning benches

To delete all your benches, simply run:

```shell
vendor/bin/hydra clean
```

### In PHPUnit tests

#### Setup PHPUnit

Since Hydra will basically hijack composer autoloader, you will need to replace the PHPUnit bootstrap file. During
installation, Hydra generates a new bootstrap file which has to be specified in PHPUnit configuration.

For example, in a phpunit.xml file:

```xml
<phpunit
  bootstrap="hydra/bootstrap.php">
    <!-- Your other configurations -->
</phpunit>
```

#### Choose the test bench

The test bench selection is achieved through the `HYDRA_BENCH` environment variable.

You can run phpunit like this:

```shell
HYDRA_BENCH=laravel-7 phpunit [args]
```

#### Use the `HydraTestCase`

In order to set up tests for your package, you have to use the `Windy\Hydra\Testing\HydraTestCase` class.

##### Define a configuration:

```php
use Windy\Hydra\Testing\HydraTestCase;

class ExampleTest extends HydraTestCase
{
    protected function setUpConfig(): array
    {
        return [
            'your-package' => [
                'foo' => 'bar'
            ]
        ];
    }
}
```

##### Define your providers

```php
use Windy\Hydra\Testing\HydraTestCase;

class ExampleTest extends HydraTestCase
{
    protected function setUpProviders(): array
    {
        return [
            \Me\MyPackage\MyPackageProvider::class
        ];
    }
}
```

##### Specific setup for Laravel and Lumen
If you need to run specific setup for Laravel or Lumen, you may use:

```php
use Windy\Hydra\Testing\HydraTestCase;

class ExampleTest extends HydraTestCase
{
    /**
     * Set up Laravel application.
     */
    protected function setUpLaravel(): void
    {
        // Run only for Laravel applications
    }

    /**
     * Setup the Lumen application.
     */
    protected function setUpLumen(): void
    {
        // Run only for Lumen applications
    }
}
```
