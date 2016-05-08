# TacoForms - IN DEVELOPMENT. DO NOT USE YET.

The objective of TacoForms in tangent with this boilerplate, it to literally allow you to make a form in less than 2 minutes. It's an API that taps into TacoWordPress to create Form configuration post types that allow both you the developer as well as the client admin to setup.

## A Basic example
```php
<?php
$contact_form_config = new TacoForm(
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
* First we create a new TacoForm object
* Then we pass in array of settings
* Define some fields to be used in the form
* Lastly, render the form with the object's render method.

All of that gives you a form in the frontend that visitors can fill out, and be recorded as an entry post type in WordPress. It also gives the admin access to overriding common settings like admin emails (for notifications), success, and error messages.

#### TacoForm API configuration settings

These are the properties and values (defaults shown below) that can be used to setup a form configuration.

```php

    array(
      'conf_name' => '',
      'fields' => array(),
      'css_class' => '',
      'id' => '',
      'method' => 'post',
      'action' => null,
      'novalidate' => false,
      'use_ajax' => false,
      'hide_labels' => false,
      'column_classes' => 'small-12 columns',
      'exclude_post_content' => false,
      'submit_button_text' => 'submit',
      'success_message' => null,
      'error_message' => null,
      'success_redirect_url' => null,
      'label_field_wrapper' => 'TacoForm::rowColumnWrap',
      'use_honeypot' => true,
      'honeypot_field_name' => 'your_website_url'
    );
```
Details on what each property/value does is coming soon.

## Customizing how the form gets rendered
The simplicity of TacoForms doesn't stop with the above. Custom rendering of a form is also a breeze and comes with a few different options.

##### Example 1
```php
<?php

echo (new TacoForm(
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

echo (new TacoForm(
  array(
    'conf_name' => 'General Contact Form Configuration',
    'novalidate' => true,
    'success_message' => 'Success! Thanks for your inquiry.',
    'error_message' => 'Oops! Looks like there was an error in the form. Please correct and try again.'
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
You will notice how you close php to start defining your custom template. If you've been using php for a bit you might have guessed that I'm using output buffering to capture the html and replace %template_tags% with rendered html. This happens behind the scenes. In my very humble opinion, if you get passed the weirdness, it's a very elegant way to customize.

##### If you can get passed the weirdness you will really like this!

```php

<?php $contact_form = new TacoForm(
  array(
    'conf_name' => 'Contact Form Configuration',
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
$my_contact_form = new TacoForm(
  array(
    'conf_name' => 'contact form configuration',
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
A form developed using TacoForm has the data attribute "data-form-status". You can use this to tell your custom js script what the form's state is. Below are the three states.


`data-form-status="idle"` – form is idle and has not been submitted yet

`data-form-status="has_errors"` – form has invalid values

`data-form-status="success"` – form submission was successful




##### Form Security

TacoForms has backend validation, but if you needed some extra help, you can use the honeypot option.
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


More to come...
