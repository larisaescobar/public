---
title: Quick Start Guide
ordering: 10
---

## Script

Include the following script with Distributor into your website:

~~~html
<script src="https://www.mews.li/distributor/distributor.min.js"></script>
~~~

The script should be included in `<head>` section and without `async` option. Size of the script is keeped as minimal as possible to allow quick initialization. A main widget script is then downloaded asynchronously and inserted automatically into your website afterwards.

Please note, that serving script from our servers ensures seamless release of new features and improvements. Do not therefore pack contents of this script file into your own javascript bundle and be sure to follow recommended way of including script via `<script>` HTML tag.

## Styles

Distributor Edge doesn't use separate css files, everything is packed inside the script. For possible customizations, consult [Customization](./reference.html#customization) section.

## Usage

Once required script is loaded, you can initialize Distributor Edge with the following minimal code. Do not forget to **use hotelId of your hotel** instead of the sample hotelId `aaaa-bbbb-cccc-dddd-eeeeeeee`. This will create a separate overlay in your website and loads Distributor into it.

**Important:** The initialization needs to be called only after the website is loaded to ensure everything is ready. Easiest way to achieve this is to place it just before closing `</body>` tag.

The overlay is not visible by default - to actually show it to your users, you should bind its opening to some action (i.e. clicking on button). Distributor can do it automatically for you if you provide second option - a string of comma separated css selectors of elements, whose click event will be binded with opening of Distributor. The event is delegated, so you can pass selectors to elements that don't exist in website yet.

~~~html
<!-- Distributor's initialization call, creating new instance of Distributor. Use id of your hotel. -->
<script>
    Mews.Distributor({
        hotelIds: ['aaaa-bbbb-cccc-dddd-eeeeeeee'],
        openElements: '.open-distributor-button'
    });
</script>
~~~

If you need more specific setup of opening Distributor, or you want to call some api functions on Distributor instance, you can provide a callback function as second argument to initialization call - the instance is provided as an argument to the callback.

~~~html
<script>
    Mews.Distributor({
        hotelIds: ['aaaa-bbbb-cccc-dddd-eeeeeeee']
    }, function(distributor) {
        // you can call api functions on distributor instance here
    });
</script>
~~~

To see a list of all available api calls, please consult [API](#api) section.

Closing of Distributor is provided in the overlay by default, so you don't have to worry about that.

### Chain Distributor

Distributor can run in two basic modes - for *Single* property or for *Chain*. The mode is decided automatically during initialization based on the count of hotel IDs you provide in options. Whenever 2 or more hotels are loaded, the Distributor will be in *Chain* mode. That means that it will add one more step to the booking flow - hotel selection. To add more hotels, simple pass their ids into the `hotelIds` array option:

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

## Done!

This is all you need for the basic setup of Mews Distributor.