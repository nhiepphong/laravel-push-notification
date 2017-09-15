# Laravel Push Notification v1.0.1

Based on https://github.com/davibennun/laravel-push-notification forked from https://github.com/larkinwhitaker/laravel-push-notification

Added support for Laravel 5.2 => 5.5.*.

Installation
----

Installing the package
```json
composer require nhiepphong/laravel-push-notification
```


Register the PushNotification service provider by adding it to the providers array in the `config/app.php` file.
```php
'providers' => array(
    'Nhiepphong\LaravelPushNotification\PushNotificationServiceProvider'
)
```

Alias the PushNotification facade by adding it to the aliases array in the `config/app.php` file.
```php
'aliases' => array(
	'PushNotification'      => 'Nhiepphong\LaravelPushNotification\PushNotification',
)
```

# Configuration

Copy the config file into your project by running
```
php artisan vendor:publish
```

This will generate a config file like this
```php
array(
    'iOS'     => [
        'environment' => env('IOS_PUSH_ENV', 'development'),
        'certificate' => env('IOS_PUSH_CERT', __DIR__ . '/ios-push-notification-certificates/development/certificate.pem'),  
        'passPhrase'  => env('IOS_PUSH_PASSWORD', '291923Job'),
        'service'     => 'apns'
    ],

    'android' => [
        'environment' => env('ANDROID_PUSH_ENV', 'development'),
        'apiKey'      => env('ANDROID_PUSH_API_KEY', 'yourAPIKey'),
        'service'     => 'gcm'
    ]
);
```
Where all first level keys corresponds to an service configuration, each service has its own properties, android for instance have `apiKey` and IOS uses `certificate` and `passPhrase`. You can set as many services configurations as you want, one for each app.  A directory with the name 'ios-push-notification-certificates' will be added to the config folder for you to store both development and production certificates.

##### Dont forget to set `service` key to identify iOS `'service'=>'apns'` and Android `'service'=>'gcm'`

# Usage
```php

PushNotification::app('iOS')
                ->to($deviceToken)
                ->send('Hello World, i`m a push message');

```
Where app argument `appNameIOS` refers to defined service in config file.
To multiple devices and optioned message:
```php
$devices = PushNotification::DeviceCollection(array(
    PushNotification::Device('token', array('badge' => 5)),
    PushNotification::Device('token1', array('badge' => 1)),
    PushNotification::Device('token2')
));
$message = PushNotification::Message('Message Text',array(
    'badge' => 1,
    'sound' => 'example.aiff',
    
    'actionLocKey' => 'Action button title!',
    'locKey' => 'localized key',
    'locArgs' => array(
        'localized args',
        'localized args',
    ),
    'launchImage' => 'image.jpg',
    
    'custom' => array('custom data' => array(
        'we' => 'want', 'send to app'
    ))
));

collection = PushNotification::app('appNameIOS')
    ->to($devices)
    ->send($message);

// get response for each device push
foreach ($collection->pushManager as $push) {
    $response = $push->getAdapter()->getResponse();
}

// access to adapter for advanced settings
$push = PushNotification::app('appNameAndroid');
$push->adapter->setAdapterParameters(['sslverifypeer' => false]);
```
This package is wrapps [Notification Package] and adds some flavor to it.
```

#### Usage advice
This package should be used with [Laravel Queues], so pushes dont blocks the user and are processed in the background, meaning a better flow.



[Laravel Queues]:http://laravel.com/docs/queues