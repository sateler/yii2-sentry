# [Sentry](https://sentry.io) logger for Yii2

[![Build Status](https://travis-ci.org/notamedia/yii2-sentry.svg)](https://travis-ci.org/notamedia/yii2-sentry)
[![Latest Stable Version](https://poser.pugx.org/notamedia/yii2-sentry/v/stable)](https://packagist.org/packages/notamedia/yii2-sentry) 
[![Total Downloads](https://poser.pugx.org/notamedia/yii2-sentry/downloads)](https://packagist.org/packages/notamedia/yii2-sentry) 
[![License](https://poser.pugx.org/notamedia/yii2-sentry/license)](https://packagist.org/packages/notamedia/yii2-sentry)

## Installation

```bash
composer require notamedia/yii2-sentry
```

Add target class in the application config:

```php
return [
    'components' => [
	    'log' => [
		    'traceLevel' => YII_DEBUG ? 3 : 0,
		    'targets' => [
			    [
				    'class' => 'notamedia\sentry\SentryTarget',
				    'dsn' => 'http://2682ybvhbs347:235vvgy465346@sentry.io/1',
				    'levels' => ['error', 'warning'],
				    'context' => true // Write the context information. The default is true.
			    ],
		    ],
	    ],
    ],
];
```

## Usage

Writing simple message:

```php
\Yii::error('message', 'category');
```

Writing messages with extra data:

```php
\Yii::warning([
    'msg' => 'message',
    'extra' => 'value',
], 'category');
```

### User Data

To add the user info to Sentry's context data, provide the attribute names of the Identity Class (User Model) properties that contain such data. Id, Username and Email can be provided. See https://docs.sentry.io/learn/context/#capturing-the-user and http://www.yiiframework.com/doc-2.0/guide-security-authentication.html#configuring-user for more details.
 
```php
    'targets' => [
        [
            'class' => 'notamedia\sentry\SentryTarget',
            'dsn' => 'http://2682ybvhbs347:235vvgy465346@sentry.io/1',
            'levels' => ['error', 'warning'],
            'context' => true, // Write the context information. The default is true.
            // User info:
            'userIdAttribute' => 'id',
            'userUsernameAttribute' => 'username',
            'userEmailAttribute' => 'email',
        ],
    ],
```

### Extra callback

`extraCallback` property can modify extra's data as callable function:
 
```php
    'targets' => [
        [
            'class' => 'notamedia\sentry\SentryTarget',
            'dsn' => 'http://2682ybvhbs347:235vvgy465346@sentry.io/1',
            'levels' => ['error', 'warning'],
            'context' => true, // Write the context information. The default is true.
            'extraCallback' => function ($message, $extra) {
                // some manipulation with data
                $extra['some_data'] = \Yii::$app->someComponent->someMethod();
                return $extra;
            }
        ],
    ],
```

### Tags

Writing messages with additional tags. If need to add additional tags for event, add `tags` key in message. Tags are various key/value pairs that get assigned to an event, and can later be used as a breakdown or quick access to finding related events.

Example:

```php
\Yii::warning([
    'msg' => 'message',
    'extra' => 'value',
    'tags' => [
        'extraTagKey' => 'extraTagValue',
    ]
], 'category');
```

More about tags see https://docs.sentry.io/learn/context/#tagging-events

## Log levels

Yii2 log levels converts to Sentry levels:

```
\yii\log\Logger::LEVEL_ERROR => 'error',
\yii\log\Logger::LEVEL_WARNING => 'warning',
\yii\log\Logger::LEVEL_INFO => 'info',
\yii\log\Logger::LEVEL_TRACE => 'debug',
\yii\log\Logger::LEVEL_PROFILE_BEGIN => 'debug',
\yii\log\Logger::LEVEL_PROFILE_END => 'debug',
```
