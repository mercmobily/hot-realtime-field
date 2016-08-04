
`<hot-realtime-field>` allows you to:
* Use a custom GET call to check in real time if a field is allowed, depending on what the server returns
* Use a custom PUT call to save that value in real time onto the server

You can have just a GET call, just a PUT call, or both. If you have both, the PUT call will only be called once the GET call's result is all clear.

This element is _extremely_ flexible. What is does is very simple, and it will work out of the box with the default settings. However, it can be configured extensively.

## Saving a value in real time using a PUT, using `put-url`



## Checking a value in real time using a GET, using `get-url` and `get-check`

When specifying `get-url`, your common paper-input element will run the query provided in `get-url` and will expect a JSON response with an array. If `get-check` is `is-empty`, validation will _pass_ if the server returned an empty array (think of it as "pick your username"). If `get-check` is `is-not-empty`, validation will _pass_ if the server returned a non-empty array (think of it as "recover your password").

Example:

<hot-realtime-field get-url="/stores/polymer?name={{name3}}" get-check="is-empty">
<paper-input ID="TOPINPUT" value="{{fieldName3}}" required id="name" name="name" label="Your name"></paper-input>
</hot-realtime-field>

### Extra parameters

You can specify the following attributes:

*  `get-check-message`. Defines the error displayed to the user if `get-check` fails. For example: `User name taken!`. Default: `Error!`

Example:

    <hot-realtime-field get-url="/stores/polymer?name={{name3}}" get-check="is-empty" get-check-message="Username taken!">
      <paper-input ID="TOPINPUT" value="{{fieldName3}}" required id="name" name="name" label="Your name"></paper-input>
    </hot-realtime-field>

* `handle-get-errors`. It's a comma-separated list of error status that will be handled directly. For example, `400,401,403`. Errors that are "handled" will turn the field `invalid`, and will set the `errorMessage` of the field element to the returned error message. Please note that this parameter is mainly here for consistency: its counterpart `handle-put-errors` makes much more sense. If an error isn't handle, an event `user-message-error` is fired, with `detial` set to `{message: "The error message"}`. This will give your application a chance to display the error to the user (otherwise, the error will fall completely silent). Default: `400`

Example:

    <hot-realtime-field get-url="/stores/polymer?name={{name3}}" get-check="is-empty" handleGetErrors="400,401,403">
      <paper-input ID="TOPINPUT" value="{{fieldName3}}" required id="name" name="name" label="Your name"></paper-input>
    </hot-realtime-field>

* `get-default-error-message`.

By default `hot-realtime-field` will try to extrapolate the error message from the response's body (which is expected to be a JSON) using `error-path` (see the next parameter explained). If extrapolation failed (in case the server didn't respond at all, for example), this is the error message that will be shown. Remember that this is only considered in case of HTTP errors. Default: `Error querying`.

* `error-path` The error will be extrapolated from the response's body (which is expected to be a JSON). An example could be { "errors":[ {"field": "name", "error":"The name is too long"} ] }. With the default path,  `errors.0.message`, "The name is too long" will be extrapolated. Default: `errors.0.message`.


## Checking and saving a value in real time using GET + put

## Pre-loading data into the field

### Usage with hot-network

## Using a custom target ID

## Checkbox-type fields

If the field is a checkbox or a radio button, `<hot-immediate>` will toggle it back in case of error, so that the status will be consistent to what's on the server. Also, since checkbox-like buttons often don't provide validation theming,

TODO:

