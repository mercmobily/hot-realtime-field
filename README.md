
`<hot-realtime-field>` allows you to:
* Use a custom GET call to check in real time if a field is allowed, depending on what the server returns
* Use a custom PUT call to save that value in real time onto the server

You can have just a GET call, just a PUT call, or both. If you have both, the PUT call will only be called once the GET call's result is all clear.

This element is _extremely_ flexible. What is does is very simple, and it will work out of the box with the default settings. However, it can be configured extensively.

## Checking a value in real time using a GET

## Saving a value in real time using a PUT

## Checking and saving a value in real time using GET + put

## Pre-loading data into the field

## Using a custom target ID





Finally, if the field is a checkbox or a radio button, `<hot-immediate>` will toggle it back in case of error, so that the status will be consistent to what's on the server.

TODO:
* Document the lot
* If it's a checkbox or radio, toggle if error (make option)
bower_components bower.json demo hot-realtime-field.html index.html LICENSE make_readme.sh README.md test

