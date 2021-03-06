Symfony Health Check Bundle
=================================

Installation
============

Step 1: Download the Bundle
----------------------------------
Open a command console, enter your project directory and execute:

```console
composer require jpvdw86/symfony-health-check-bundle
```

This command requires you to have Composer installed globally, as explained
in the [installation chapter](https://getcomposer.org/doc/00-intro.md)
of the Composer documentation.

Step 2: Enable the Bundle
----------------------------------
Then, enable the bundle by adding it to the list of registered bundles
in the `config/bundels.php` file of your project:

```php
<?php
// config/bundels.php

return [
       // ...
       SymfonyHealthCheckBundle\SymfonyHealthCheckBundle::class => ['all' => true],
];

```

Create Symfony Health Check Bundle Config:
----------------------------------
`config/packages/symfony_health_check.yaml`

Configurating health check - all available you can see [here](https://github.com/jpvdw86/symfony-health-check-bundle/tree/master/src/Check).

```yaml
symfony_health_check:
  health_checks:
    - id: symfony_health_check.status_up_check
    - id: symfony_health_check.doctrine_check
    - id: symfony_health_check.environment_check
```

Create Symfony Health Check Bundle Routing Config:
----------------------------------
`config/routes/symfony_health_check.yaml`

```yaml
health_check:
    resource: '@SymfonyHealthCheckBundle/Resources/config/routes.xml'
```

Step 3: Configuration
=============

Security Optional:
----------------------------------
`config/packages/security.yaml`

If you are using [symfony/security](https://symfony.com/doc/current/security.html) and your health check is to be used anonymously, add a new firewall to the configuration

```yaml
    firewalls:
        healthcheck:
            pattern: ^/health
            security: false
```

Step 4: Additional settings
=============

Add Custom Check:
----------------------------------
It is possible to add your custom health check:

```php
<?php
// src/Check/CustomCheck.php
namespace App\Check;

use SymfonyHealthCheckBundle\Check\CheckInterface;

class CustomCheck implements CheckInterface
{
    private const CHECK_RESULT_KEY = 'customConnection';
    
    public function isHealthy(): bool
    {
        return true;
    }
    
    public function check(): array
    {
         return [
            'name' => self::CHECK_RESULT_KEY,
            'status' => $this->isHealthy()
        ];
    }
}
```

Then we add our custom health check to collection

```yaml
symfony_health_check:
  health_checks:
    - id: symfony_health_check.status_up_check
    - id: symfony_health_check.doctrine_check
    - id: symfony_health_check.environment_check
    - id: custom_check
```

How Change Route:
----------------------------------
You can change the default behavior with a light configuration, remember to return to Step 3 after that:
```yaml
health:
    path: /your/custom/url
    methods: GET
    controller: SymfonyHealthCheckBundle\Controller\HealthController::healthCheckAction

```

How To Use Healthcheck In Docker
----------------------------------
You can add to follow line to you docker-compose / docker-stack file.

```yaml
healthcheck:
  test: curl -sS http://localhost/health || exit 1
  interval: 5s
  timeout: 3s
  retries: 3
  start_period: 15s
```
Or in your docker file

```dockerfile
HEALTHCHECK --start-period=15s --interval=5s --timeout=3s --retries=3 CMD curl -sS http://localhost/health || exit 1
```
