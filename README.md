# Mr. Spicy

The objective of Mr. Spicy (forms), is to literally allow you to make a form in less than 2 minutes. It might feel as if you're on fire! It's an API that taps into TacoWordPress to create form configuration post types that allow both you the developer as well as the client admin to setup.

## A Basic example
```php
<?php
$contact_form_config = new \Taco\MrSpicy(
  array(
    'conf_name' => 'General Contact Form Configuration',
    'fields' => array(
      'first_name' => array('type' => 'text'),
      'email' => array('type' => 'email'),
      'message' => array('type' => 'textarea')
    )
  )
);

echo $contact_form_config->render();
```
#### So what's happening here?
* First we create a new MrSpicy object
* Then we pass in array of settings
* Define some fields to be used in the form
* Lastly, render the form with the object's render method.

All of that gives you a form in the frontend that visitors can fill out, and be recorded as an entry post type in WordPress. It also gives the admin access to overriding common settings like admin emails (for notifications), success, and error messages.

#### Mr. Spicy Requirements

* Mr. Spicy currently requries the "taco-2016-boilerplate" located [here](https://github.com/tacowordpress/taco-2016-boilerplate). This will be fixed soon.
* PHP >=5.4.0
* TacoWordPress


#### Setup

1. Plese make sure to install the dependency of TacoWordPress first. https://github.com/tacowordpress/tacowordpress/
2. Add MrSpicy to your composer.json file right after TacoWordPress:

```
{
  "require": {
    "tacowordpress/tacowordpress: "dev-master",
    "tacowordpress/mr-spicy": "dev-master"
  }
}
```

#### Mr. Spicy API configuration settings

These are the properties and values (defaults shown below) that can be used to setup a form configuration.

```php

    array(
      'form_unique_key' => '',
      'fields' => array(),
      'css_class' => '',
      'id' => '',
      'method' => 'post', // currently only works with POST
      'action' => null,
      'lock' => (ENVIRONMENT == 'prod') // set this to true in production environments
      'novalidate' => false,
      'use_ajax' => false, // coming soon
      'hide_labels' => false,
      'column_classes' => 'small-12 columns',
      'exclude_post_content' => false,
      'submit_button_text' => 'submit',
      'success_message' => null,
      'error_message' => null,
      'success_redirect_url' => null,
      'label_field_wrapper' => '\Taco\MrSpicy::rowColumnWrap',
      'use_honeypot' => true,
      'honeypot_field_name' => 'your_website_url',
      'use_recaptcha' => false,
      'google_recaptcha_site_key' => null,
      'google_recaptcha_secret_key' => null
    );
```
Details on what each property/value does is coming soon.

## Customizing how the form gets rendered
The simplicity of Mr. Spicy doesn't stop with the above. Custom rendering of a form is also a breeze and comes with a few different options.

##### Example 1
```php
<?php

echo (new \Taco\MrSpicy(
  array(
    'conf_name' => 'General Contact Form Configuration',
    'novalidate' => true,
    'fields' => array(
      'first_name' => array(),
      'email' => array('type' => 'email'),
      'message' => array('type' => 'textarea')
    ),
  )
))->render(function($form_conf) { ?>

  <div class="row">
    <div class="small-12 columns">
      %first_name%
    </div>
  </div>

  <div class="row">
    <div class="small-12 columns">
      %email%
    </div>
  </div>

  <div class="row">
    <div class="small-12 columns">
      %message%
    </div>
  </div>

   <div class="row">
    <div class="small-12 columns">
      <button type="submit">Submit</button>
    </div>
  </div>

  <div class="row">
    <div class="small-12 columns">
      %edit_link%
    </div>
  </div>

<?php }); ?>
```
Example 1 shows how easy it is to use your own template with HTML and template tags.
Using %my_field_name% will render that field according to the configuration settings. Let's say you wanted to hide the labels for fields and use placeholders instead. In your config settings just set `"hide_labels" => true`.
You will notice "%edit_link%" in the example above. This renders a link to the WordPress admin, where client admin can go to the configuration to edit fields like success/error messages and admin emails.

##### Example 2 changing form messages

The below example demonstrates a few things: customizing the form's general success/error messages and the position of them. Note: The WordPress admin can override these fields and that's done by design.

```php
<?php

echo (new \Taco\MrSpicy(
  array(
    'conf_name' => 'General Contact Form Configuration',
    'novalidate' => true,
    'success_message' => 'Success! Thanks for your inquiry.',
    'error_message' => 'Oops! Looks like there was an error in the form. Please correct and try again.',
    'fields' => array(
      'first_name' => array(),
      'last_name' => array(),
      'email' => array('type' => 'email'),
      'message' => array('type' => 'textarea')
    ),

  )
))->render(function($form_conf) { ?>

  <div class="row">
    <div class="small-12 columns">
      %form_messages% //This is where success or error messages will print
    </div>
  </div>

  <div class="row">
    <div class="small-12 columns">
      %first_name%
    </div>
  </div>

  <div class="row">
    <div class="small-12 columns">
      %last_name%
    </div>
  </div>

  <div class="row">
    <div class="small-12 columns">
      %email%
    </div>
  </div>

  <div class="row">
    <div class="small-12 columns">
      %message%
    </div>
  </div>

  <div class="row">
    <div class="small-12 columns">
      <button type="submit">Submit</button>
    </div>
  </div>


<?php }); ?>
```

##### Why is the template syntax atypical?
You will notice how you close php to start defining your custom template. If you've been using php for a bit you might have guessed that I'm using output buffering to capture the html and replace %template_tags% with rendered html. This happens behind the scenes. In my very humble opinion, *if you can get past the weirdness, it's a very elegant way to customize*.


```php

<?php $contact_form = new \Taco\MrSpicy(
  array(
    'form_unique_key' => 'Contact Form Configuration',
    'fields' => 'auto', // don't define the fields here
  )
); ?>

<?php
echo $contact_form->render(function($form_conf) { ?>

  <div class="row">
    <div class="small-12 medium-8 medium-centered columns">
      %post_content%
    </div>
  </div>

  <div class="row">
    <div class="small-12 medium-8 medium-centered columns">
      %form_messages%
    </div>
  </div>

  <div class="row">
    <div class="small-12 medium-8 medium-centered columns">
      %first_name|required=true%
    </div>
  </div>

  <div class="row">
    <div class="small-12 medium-8 medium-centered columns">
      %last_name%
    </div>
  </div>

  <div class="row">
    <div class="small-12 medium-8 medium-centered columns">
      %email|required=true%
    </div>
  </div>

  <div class="row">
    <div class="small-12 medium-8 medium-centered columns">
      %message|type=textarea|required=true%
    </div>
  </div>

  <div class="row">
    <div class="small-12 medium-8 medium-centered columns">
     <button type="submit">Submit</button>
    </div>
  </div>

  <div class="row">
    <div class="small-12 medium-8 medium-centered columns">
      %edit_link%
    </div>
  </div>
<?php }); ?>
```

Yes, we are defining the fields in the template.


##### Callbacks (events)
Giving "on_success" a value of closure in the form's conf settings will allow you to trigger a function/method after the form's success.
```php
$my_contact_form = new \Taco\MrSpicy(
  array(
    'form_unique_key' => 'contact-form-configuration',
    'on_success' => function($entry_object, $form_conf) {

      // send mail on the form's success
      $to = 'info@yourwebsite.com';
      $subject = 'A message from the site\'s general contact form';
      $message = '';
      mail($to, $subject, $message, $headers);
    }
  )
);
...
```
Please note that you must define your callback with 2 parameters i.e. (Object $entry, Object $form_conf)
If you do not wish to use these, set them to null.

Important: Using a closure in tangent with the "success_redirect_url" specified will throw an error.
To get around this, you must specify a string callback instead. See below.

```php

// the right way
...
'success_redirect_url' => 'http://mywebsite.com/thankyou-message',
'on_success' => 'MyClass::myMethod'

// and then define your class method (must be static)
class MyClass {
  public static function myMethod($entry, $form_conf) {}
}
...

// the wrong way - this will throw an error
...
'success_redirect_url' => 'http://mywebsite.com/thankyou-message',
'on_success' => function($entry, $form_conf) { }
...

```
##### Form Status
A form developed using MrSpicy has the data attribute "data-form-status". You can use this to tell your custom js script what the form's state is. Below are the three states.


`data-form-status="idle"` – form is idle and has not been submitted yet

`data-form-status="has_errors"` – form has invalid values

`data-form-status="success"` – form submission was successful




##### Form Security

Mr. Spicy forms has backend validation, but if you needed some extra help, you can use the honeypot and Google Recaptcha options.
In your settings just pass this in:

```php
    array(
      ...
      'use_honeypot' => true,
      'honeypot_field_name' => 'your_website_url'
    );
```
Currently there isn't a custom error message for honeypot fields.
It's probably better not to be obvious about it.

###### Google reCAPTCHA
Before using Google's reCAPTCHA with a Mr. Spicy form, you will need a Google account and have to register your site.

[https://www.google.com/recaptcha/](https://www.google.com/recaptcha/)

Immediately following, take note of the site and secret keys that Google provides, as you will need to use this in the form settings.

```php
    array(
      ...
      'use_recaptcha' => true,
      'google_recaptcha_site_key' => $google_recaptcha_site_key,
      'google_recaptcha_secret_key' => $google_recaptcha_secret_key
      
      // let's also keep the honeypot
      'use_honeypot' => true,
      'honeypot_field_name' => 'your_website_url'
    );
```


##### Further Customization
###### Fields auto
When fields are defined and rendered automatically, there may be instances where you still need to override certain elements.

```html
%email|type=textarea|required=true%
```
What if you wanted to customize the label that renders automatically?
You can do this.

```html
%email|type|label=false% // don't show it
// or
%email|type|label=email (required)% // to customize the text
```

###### Fields custom (not auto)
Maybe you don't want any of the "%%" template syntax for field defining and rendering and just want to do your own thing. Be our guest. You can type
the form fields out how you would normally do it. Just keep the field names the same as the configuration. If you want the error messages and values to populate after submission, you still have to use the "%%" syntax.


```php
<?php

echo (new \Taco\MrSpicy(
  array(
    'form_unique_key' => 'general-contact-form-configuration',
      'first_name' => array('type' => 'text', 'required' => true),
      'last_name' => array(),
      'email' => array('type' => 'email', 'required' => true),
      'message' => array('type' => 'textarea', 'required' => true)
    ),
  )
))->render(function($form_conf) { ?>

  ...

  <div class="row">
    <div class="small-12 columns">
      %error_first_name%
      <label for="first-name">first name *</label>
      <input type="text" name="first_name" id="first-name" value="%value_first_name%">
    </div>
  </div>

  <div class="row">
    <div class="small-12 columns">
      <label for="last-name">last name</label>
      <input type="text" name="last_name" id="last-name" value="%value_last_name%">
    </div>
  </div>

  <div class="row">
    <div class="small-12 columns">
      %error_email%
      <label for="email">email</label>
      <input type="text" name="email" id="email" value="%value_email%">
    </div>
  </div>

  <div class="row">
    <div class="small-12 columns">
      %error_message%
      <label for="message">message</label>
      <input type="text" name="message" id="message" value="%value_message%">
    </div>
  </div>

  ...
```

#### Overriding the Global Configuration
There are some settings you may wish to customize. These properties are however global and will effect all form configurations. You can find these settings in "vendor/tacowordpress/mr-spicy/src/forms-defaults.php". Please do not edit this file. Instead copy it to a folder of your choice outside of vendor so it won't get overriden from a future composer udpate. Once you have done this, you will need to tell Mr. Spicy where this file exists. 

```php
// add this line after the composer autoload include
\Taco\MrSpicy::setDefaultsPath(get_template_directory().'/forms-defaults.php'); // this can be changed
```

These are the current global settings you can override:
```php
return array(
  'form_action' => null, // This is set automatically, but you can override it.
  'error_message' => 'There were some errors with your form submission. Please correct and try again.',
  'success_message' => 'Thanks for your message',
  'shared_configuration_extra_fields' => [] // use this if you need common (shared across all form configurations) admin columns like "email" or "name" to be added
);
```


More to come...
