# Symfony Installing Packages
Explanation of installing packages in Symfony using Flex and Packs.  

## Flex
Package is a ready-to-use functionality that adds new features to simplify development process.  
In Symfony, packages are called bundles and are installed via Composer.  

In order for bundle to work, it needs to be attached to framework. This usually requires doing some extra work - adding a bundle class in `config/bundles.php` and adding a config file in `config/packages/*.yaml` and possibly some others.  

In order for bundle to automatically attach after installation, plugin for Composer called Flex was created.
Flex after installing bundle executes recipes, that is instructions for automating attachment and configuration of bundle in Symfony.  
Executed recipes are written to `symfony.lock` file.  

There are two recipe repositories - [main](https://github.com/symfony/recipes) and [contributor](https://github.com/symfony/recipes-contrib).  
The main repository contains quality recipes for popular bundles.  
The contributor repository contains recipes for the rest of bundles developed by community.  

### Bundle installation example using Flex
For example, you need to install logger (Monolog Bundle by default)
```bash
composer require logger
```

As a result, the following actions will be performed:
- Added class `Symfony\Bundle\MonologBundle\MonologBundle::class` to `config/bundles.php` file;
```php
<?php

return [
    Symfony\Bundle\FrameworkBundle\FrameworkBundle::class => ['all' => true],
    Symfony\Bundle\MonologBundle\MonologBundle::class => ['all' => true],
];
```

- Added configuration file `config/packages/monolog.yaml`;
```yaml
#...
when@dev:
    monolog:
        handlers:
            main:
                type: stream
                path: "%kernel.logs_dir%/%kernel.environment%.log"
                level: debug
                channels: ["!event"]
            # uncomment to get logging in your browser
            # you may have to allow bigger header sizes in your Web server configuration
            #firephp:
            #    type: firephp
            #    level: info
            #chromephp:
            #    type: chromephp
            #    level: info
            console:
                type: console
                process_psr_3_messages: false
                channels: ["!event", "!doctrine", "!console"]
#...
```

- Added executed recipe to `symfony.lock`;
```json
"symfony/monolog-bundle": {
        "version": "3.8",
        "recipe": {
            "repo": "github.com/symfony/recipes",
            "branch": "main",
            "version": "3.7",
            "ref": "213676c4ec929f046dfde5ea8e97625b81bc0578"
        },
        "files": [
            "config/packages/monolog.yaml"
        ]
    },
```
