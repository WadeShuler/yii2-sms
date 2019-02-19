# Yii2 SMS

This is the base library for providing SMS support in your Yii2 application. You can make your own plugins for SMS providers off this base package. It is not to be directly used.

> **NOTE:** You can not use this library/package by itself. It is a parent/root package meant to be extended from! See [Yii2 SMS Twilio](https://github.com/wadeshuler/yii2-sms-twilio) for an example on how to make your own plugin extending this package.

This is just like the base mail namespace in Yii2. You don't use it directly, but rather use the Swiftmailer package which extends the built-in `yii\mail` classes.

The goal is to send SMS text messages similar to how you send emails in Yii2 and to be easily extended on to support an infinite number of SMS providers.


## Key take-aways

Configuration is in your main config (Advanced: `common/config/main-local.php`) as the `sms` component:

```php
'components' => [
    'sms' => [
        'class' => 'namespace\to\ExtendedSms',  // Class that extends BaseSms
        'viewPath' => '@common/sms',            // Optional: defaults to '@app/sms'

        // send all sms to a file by default.
        'useFileTransport' => true,             // false for real sending

        'messageConfig' => [
            'from' => '+15552221234',           // From number to send from
        ],

        'apiKey' => 'xxxxxxxxxxxxxxx',          // Example where to put keys
    ]
]
```

*Note:* The above configuration is only an example and will not actually send SMS messages. You will need to
make your own Sms class that extends `BaseSms`, as well as your own class that extends `BaseMessage`. Your extended
classes will handle the logic for using your own SMS service provider of choice. My [Yii2 SMS Twilio](https://github.com/wadeshuler/yii2-sms-twilio) extension is a great example of how to make a plugin for this package.

When `useFileTransport` is set to `true`, it will dump the text messages in the `fileTransportPath` which defaults to `@runtime/sms`. I have made slight modifications so these are more readable.

Use the `messageConfig` array to set default message configurations, such as `from`, as the Message class
will be instantiated with them automatically via the "getters" and "setters", thanks to `Yii::createObject()`.

You can set API key params in the `sms` configuration array directly, not inside `messageConfig`, as they have to do with
the transport (SMS provider) and not the message specifically. *Note:* `apiKey` is not defined in this package, and was
simply an example of where to place them.

The `viewPath` defaults to `@app/sms`, same as the Yii2 mailer, and can be overridden in the config. This is for using a view file, specified within the `compose()` function, similar to the Yii2 mailer.

*Note:* There are only "text" views. Since these are text messages, HTML views are not applicable. Therefore you just pass the view as a string, not an array with a `text` param in it! I removed that to streamline usage, as it made more sense to take it out.

#### View file example:

    Yii::$app->sms->compose('test-message', ['name' => 'Wade'])

Where `test-message` is a view file located in the `viewPath` destination.

View File: `common/sms/test-message.php`

```
Hello <?= $name ?> This is a test!

Thanks!
```

You will need to create the `sms` directory, as well as the view file, if you intend on using views and not just setting the message directly with `setMessage()`.

#### Without view file example:

    Yii::$app->sms->compose()
        //->setFrom('12345')  // if not set in config, or to override
        ->setTo('+15558881234')
        ->setMessage("Hey {$name} this is a test!\n\nThanks!")
        ->send();

If you set the `from` number in the main config, under the `messageConfig` array, you do not need to call `setFrom()` unless you want to override the number that was configured.

## So how do I use this?

I recommend you look at my [Yii2 SMS Twilio](https://github.com/wadeshuler/yii2-sms-twilio) package. You will see in the
`composer.json` that it requires this package. It's `Sms` class extends this `BaseSms`, and `Message` extends this `BaseMessage`.

## Packages using this library

 - [Yii2 SMS Twilio](https://github.com/wadeshuler/yii2-sms-twilio)

> Want to add yours to the list? Create an issue and I will add it so long as it works and extends this base package.

## TODO:

 - Update README
 - Update documentation/commenting
 - Add own logger class


## Donate

Please consider donating if you find my code useful.

[![PayPal Donate](https://i.ibb.co/YcM55mt/paypaldonate.png "Donate")](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=BEAUQFRMDPHT8&source=url)
