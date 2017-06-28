---
title: Distributor Widget
---

## Script

Include the following script with Distributor in your website:

~~~html
<script src="https://www.mews.li/distributor/distributor.min.js"></script>
~~~

The script should be included in the `<head>` section and without the `async` option. Size of the script is kept as minimal as possible to allow quick initialization. The main widget script is then downloaded asynchronously and inserted automatically into your website afterwards.

Please note, that serving the script from our servers ensures seamless releases of new features and improvements. Do not, therefore, pack contents of this script file into your own javascript bundle and make sure to follow the recommended way of including scripts via `<script>` HTML tag.

## Usage

Once required script is loaded, you can initialize Distributor with the following minimal code. This creates a separate overlay on your website and loads Distributor into it.

~~~html
<!-- Distributor's initialization call, creating new instance of Distributor. Use id of your hotel. -->
<script>
    Mews.Distributor({
        hotelIds: ['aaaa-bbbb-cccc-dddd-eeeeeeee'],
        openElements: '.open-distributor-button'
    });
</script>
~~~

Do not forget to **use a hotelId of your hotel** instead of the sample hotelId `aaaa-bbbb-cccc-dddd-eeeeeeee`. You can get the id of your hotel from the details page in Commander (under Settings > "Your hotel's name" ). The id is shown under the General settings as Identifier.

**Important:** The initialization needs to be called only after the website is loaded, to ensure everything is ready. The easiest way to achieve this is to place it just before the closing `</body>` tag.

The overlay is not visible by default - to actually show it to your users, you should bind its opening to some action (i.e. clicking on a button). Distributor can do it automatically for you, if you provide the second option - a string of comma-separated CSS selectors of elements, whose click events will be binded with opening of Distributor. The event is delegated, so you can pass selectors to elements that don't exist on the website yet.

If you need a more specific setup for opening Distributor, or you want to call some API functions on a Distributor instance, you can provide a callback function as the second argument to the initialization call - the instance is provided as an argument to the callback.

~~~html
<script>
    Mews.Distributor({
        hotelIds: ['aaaa-bbbb-cccc-dddd-eeeeeeee']
    }, function(distributor) {
        // you can call API functions on a distributor instance here
    });
</script>
~~~

To see a list of all available API calls, please consult [API](./widget/reference.html#api-reference) section.

Closing of Distributor is provided in the overlay by default, so you don't have to worry about that.

### Chain Distributor

Distributor can run in two basic modes - for a *Single* property or for a *Chain*. The mode is chosen automatically during initialisation, based on the count of hotel ids you have provided in the options. Whenever two or more hotels are loaded, the Distributor will start in the *Chain* mode. That means that it will add one more step to the booking flow - hotel selection. To add more hotels, simply pass their ids into the `hotelIds` array option:

~~~html
<script>
    Mews.Distributor({
        hotelIds: [
            'aaaa-bbbb-cccc-dddd-eeeeeeee',
            'uuuu-vvvv-xxxx-yyyy-zzzzzzzz',
            // and more...
        ]
    });
</script>
~~~

## Styles

Distributor does not use separate CSS files, everything is packed inside the script. For possible customizations, consult [Customization](./widget/reference.html#customization) section.

## Done!

This is all you need for the basic setup of Mews Distributor.
