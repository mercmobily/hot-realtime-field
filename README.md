`<hot-realtime-field>` allows you to:
* Save that value in real time onto the server, with the option `save-url`
* Pre-load data from the server, with the option `preload-url`
* Check if a value is already "taken" or not, with the option `taken-url`.

You can use one, two or all three functionalities on the same field at the same time if needed.

## Saving a value in real time using a PUT, using `save-url`

If you have a field on your site, and want to save its value real-time onto the server, you can use `hot-realtime-field` like this:

    <hot-realtime-field save-url="/users/579c/surname">
      <paper-input required name="surname" label="Your surname"></paper-input>
    </hot-realtime-field>

Every time a key is pressed, a PUT will be issued to the server. This also works for check-like fields:

    <hot-realtime-field save-url="/users/579c/accept">
      <paper-toggle-button name="accept" label="Accept?">Accept?</paper-toggle-button>
    </hot-realtime-field>

Unlike input fields, for check-like fields you might want to make it disabled during the AJAX queries, in order to give users feedback about the state being changed:

    <hot-realtime-field disable-in-flight save-url="/users/579c/accept">
      <paper-toggle-button name="accept" label="Accept?">Accept?</paper-toggle-button>
    </hot-realtime-field>

If the PUT fails, check-like fields are reset to their previous state.

### Related attributes

* `save-extra-body-properties`. It's an object (so, the parameter will need to be valid JSON) that will extend the request's body when doing a PUT. it is useful when you want to add things like ID etc. to the PUT query, should the server require it.  By default, a PUT call will have, in its body, an object with one element where the key is the field name and the value is the new value (e.g. `{"surname":"Mobily"}`). However, the body object is enriched with whatever is passed to `save-extra-body-properties`. Note that before running the PUT call, `hot-realtime-field` also emits an event called 'hot-realtime-field-before-put' which will give developers a way to change the `request` property (representing the iron-ajax element about to make the PUT call). For example:

    <hot-realtime-field
      save-url="/users/579c/surname"
      save-extra-body-properties='{"id":"579c"}'>

      <paper-input required name="surname" label="Your surname"></paper-input>
    </hot-realtime-field>


* `handle-save-errors`. It's a comma-separated list of error status that will end up displaying the error message directly within the field, by setting it as "invalid" and setting the target field's `errorMessage` property. If an error isn't handled, the field is left untouched and an event `user-message-error` is fired instead, with `detail` set to `{ message: errorMessage }`; this will give your application a chance to still display the error (which would otherwise fall silent) by having an element listen for `user-message-error` higher up in the DOM. The error message depends on the `error-path` property (see next section), or from `save-default-erroault-error-message` (which is by default `Error saving!`). Default for `handle-save-errors`: `400,422`.

    <hot-realtime-field
      save-url="/users/579c/surname"
      handle-save-errors="400,422">

      <paper-input class="realtime" name="surname" label="Your surname"></paper-input>
    </hot-realtime-field>

* `error-path` In case of error, the error message is extrapolated from the response's body (which is expected to be a JSON, if present). An example could be { "errors":[ {"field": "name", "error":"The name is too long"} ] }. With the default path,  `errors.0.message`, "The name is too long" will be extrapolated. Default: `errors.0.message`.

* `save-default-error-message`. By default `hot-realtime-field` will try to extrapolate the error message from the response's body (which is expected to be a JSON) using `error-path` (see the previous attribute explained). If extrapolation failed (in case the server didn't respond at all, for example, or the response wasn't compliant), `save-default-error-message` will be shown instead. Default: `Error querying`.

## Pre-loading of data

Saving a field in real time is great. However, the ability to load the field's current value from the server is what makes `hot-realtime-field` really shine. It's possible to do this easily with the `preload-url` option:

    <hot-realtime-field
      save-url="/users/579c/surname">
      preload-url="/users/579c/surname">

      <paper-input name="surname" label="Your surname"></paper-input>
    </hot-realtime-field>

Or with a check-like field:

    <hot-realtime-field
      disable-in-flight
      save-url="/users/579c/accept"
      preload-url="/users/579c/accept">

      <paper-toggle-button name="accept" label="Accept?">Accept?</paper-toggle-button>
    </hot-realtime-field>

Note the "disable-in-flight" option, which should always be set for check-like widgets.

Robust applications will want to check if the initial GET to load the information worked or not. The easiest way to do it is by using a `hot-network` widget wrapping `hot-realtime-field`:

    <hot-network manage-errors>
      <hot-realtime-field
        preload-url="/users/579c/surname"
        save-url="/users/579c/surname">

        <paper-input name="surname" label="Your surname"></paper-input>
      </hot-realtime-field>
    </hot-network>

Or using a check-like field:

    <hot-network manage-errors>
      <hot-realtime-field
        disable-in-flight
        preload-url="/users/579c/accept"
        save-url="/users/579c/accept">

        <paper-toggle-button name="accept" label="Accept?">Accept?</paper-toggle-button>
      </hot-realtime-field>
    </hot-network>

`hot-network` by default monitors the events of the `request` property of its first child. In this case, the first child is indeed `hot-realtime-field`, which has a `request` property pointing to the `iron-ajax` object which will do the preloading. The `manage-error` option means that in case of error, `hot-network` will give the users a visual cue that the operation didn't work, and will also provide a way to re-run the failed AJAX operation. All for free!

Note that you can have real time, self-saving elements within forms. However, it would likely confuse users and should not be encouraged.

Before running the GET call to preload data, `hot-realtime-field` also emits an event called 'hot-realtime-field-before-preload' which will give developers a way to change the `preloaderRequest` property (representing the iron-ajax element about to make the GET call).

### Server cooperation with `save-url`, and the `preloaded-value-path` attribute

The server needs to cooperate in order to preload values. In the examples above, it would need to return:

    {
      "surname":"Mobily"
    }

Where `surname` corresponds to the field's `name` attribute.
You can change the path within the returned object changing the `preloadedValuePath` attribute:

    <hot-realtime-field
      preloadedValuePath="someOtherKey"
      preload-url="/users/579c/surname"

      <paper-input name="surname" label="Your surname"></paper-input>
    </hot-realtime-field>

In this case, the server will be expected to respond with:

    {
      "someOtherKey":"Mobily"
    }

## Real time availability check

When specifying `taken-url`, `hot-realtime-field` will run the query provided in `taken-url` and will expect a JSON response with an array. If `taken-check` is `is-not-taken`, validation will _pass_ if the server returned an empty array (think of it as "pick your username"). If `taken-check` is `is-taken`, validation will _pass_ if the server returned a non-taken array (think of it as "recover your password").

Example:

    <hot-realtime-field taken-url="/users?username={{fieldUsername}}" taken-check="is-taken">
      <paper-input value="{{fieldUsername}}" name="username" label="Your username"></paper-input>
    </hot-realtime-field>

### Related attributes

*  `taken-check-message`. Defines the error displayed to the user if `taken-check` fails. For example: `User name taken!`. Default: `Error!`

Example:

    <hot-realtime-field
      get-check-message="Name taken sorry!"
      taken-url="/users?username={{fieldUsername}}"
      taken-check="is-taken">

      <paper-input value="{{fieldUsername}}" name="username" label="Your username"></paper-input>
    </hot-realtime-field>

Note that the query to the server represents a query to the server, where matching records will be expected to be returned.

* `handle-taken-errors`. It's a comma-separated list of error status that will end up displaying the error message directly within the field, setting it as "invalid" and setting the target field's `errorMessage` property. If an error isn't handled, the field is left untouched and an event `user-message-error` is fired instead, with `detail` set to `{ message: errorMessage }`; this will give your application a chance to display the error (which would otherwise fall silent).  The error message depends on the `error-path` property (see next section), or from `taken-default-error-message` (which is by default `Error querying!`). Default for `handle-taken-errors`: `400`.

* `error-path` In case of error, the error message is extrapolated from the response's body (which is expected to be a JSON, if present). An example could be { "errors":[ {"field": "name", "error":"The name is too long"} ] }. With the default path,  `errors.0.message`, "The name is too long" will be extrapolated. Default: `errors.0.message`.

* `taken-default-error-message`.

By default `hot-realtime-field` will try to extrapolate the error message from the response's body (which is expected to be a JSON) using `error-path` (see the previous attribute explained). If extrapolation failed (in case the server didn't respond at all, for example, or the response wasn't compliant), `save-default-error-message` will be shown instead. Default: `Error querying`.

## Combining real-time validation (`taken-url`) and auto-save (`save-url`) together

You can use both `taken-url` and `save-url` at the same time on the same element. Keep in mind that `taken-url` will check if the server returned an empty set (therefore checking if the value is "avaialble"), whereas `save-url` will save the value onto the server.

_**If both `taken-url` and `save-url` are specified, then `save-url` will only get triggered once `taken-url` has returned with a non-error**_ This means for example that if the username comes back as "unavailable", the `put` on the server won't be attempted.

For example this code will check if a username is available, and if it is, it will save it in real time onto the server:

    <hot-realtime-field
      taken-url="/users?username={{fieldUsername}}"
      taken-check="is-taken"
      taken-check-message="Name taken sorry!"
      save-url="/users/579c/username">
      <paper-input value="{{fieldUsername}}" name="username" label="Your username"></paper-input>
    </hot-realtime-field>

## Combining pre-loading (`preload-url`), real-time validation (`taken-url`) and auto-save (`save-url`) together

There is nothing stopping you from pre-loading data, as well as checking for availability _and_ saving the value in real time onto the server:

    <hot-realtime-field
      taken-url="/users?username={{fieldUsername}}"
      taken-check="is-taken"
      taken-check-message="Name taken sorry!"
      save-url="/users/579c/username"
      preload-url="/users/579c/username">
      <paper-input value="{{fieldUsername}}" required name="username" label="Your username"></paper-input>
    </hot-realtime-field>

This is quite a definition. However, remember the sheer number of things this widget will do: real-time saving, real-time checking of availability, and preloading.

### Adding hot-network to the mix

Adding hot-network will ensure that the preload phase will be resilient.

    <hot-network manage-errors>
      <hot-realtime-field
        taken-url="/users?username={{fieldUsername}}"
        taken-check="is-taken"
        taken-check-message="Name taken sorry!"
        save-url="/users/579c/username"
        preload-url="/users/579c/username">
        <paper-input value="{{fieldUsername}}" required name="username" label="Your username"></paper-input>
      </hot-realtime-field>
    </hot-network>

Note that `hot-network` will by default look for the `request` property of the first child. In this case, the `request` property of `hot-realtime-field` happens to be the `iron-ajax` element used to do the preloading. This is intentional, to make sure that `hot-realtime-field` works well with `hot-network` using default values.

## Using a custom target ID

Although there haven't been any use cases where this has been necessary, it's also possible to specify the target ID directly and have the target input field further in the DOM, rather than being `<hot-realtime-field>`'s first child:

    <hot-realtime-field target-id="surname" save-url="/users/579c/surname">
      <p>Hello!</p>
      <paper-input id="surname" required name="surname" label="Your surname"></paper-input>
    </hot-realtime-field>

## Using alternative `iron-ajax` widgets

`hot-realtime-field` will use its internal `iron-ajax` request widgets `to carry out requests. If you would like to use your own `iron-ajax` widgets, you can set the properties `loader-iron-ajax-provided` and `preloader-iron-ajax-provided`, which will look for an element with id `loader` and `preloader` respectively, and use them to make the requests.

TODO:
* Write working examples with fake JSON working, using the examples written in the documentation as a template. Use
  hot-form as template.

