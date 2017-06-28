---
title: Reference
ordering: 30
---

## Options Reference

| Name | Type | Default value | Description |
| --- | --- | --- | --- |
| <a name="hotelIds"></a>hotelIds (required) | array of `string` | `''` | Guid of the hotels used for identification in API calls. <br><br> You can get guid of a hotel from your hotel's details page in Commander (under Settings > "Your hotel's name" ). The guid is shown under the General settings as Identifier. |
| <a name="openElements"></a>openElements | `string` | `''` | A list of comma-separated CSS selectors of elements, which will automatically get attached click event listeners for opening Distributor. The string is given as an argument to the `document.querySelectorAll` function, you get more info about its resemblance [here](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) for example. <br><br> The click event is being delegated, meaning that each element is being looked up on a website dynamically after the click happens. This way you can pass a selector to the elements that don't exist yet during the initialization. |
| <a name="cityId"></a>cityId | `string` | `null` | An id of the preselected city in the *Multihotel* mode. An invalid or unset value will fallback to the first valid city of any of your hotels. For finding an id of a city, please contact our support at support@mewssystems.com. 
| <a name="language"></a>language | `string` | `null` | A language code for the default selected language of the localization. Supported values correspond to the codes of the allowed languages for your hotel as set in Commander. Invalid values will fallback to the default language of your hotel.
| <a name="currency"></a>currency | `string` | `null` | A currency code for the default selected currency. Supported values correspond to the codes of the allowed currencies for your hotel as set in Commander. Invalid values will fallback to the currency of the default rate of your hotel.
| <a name="startDate"></a>startDate | `Date` | `today` | Default value for a reservation start date in ISO 8601 format.
| <a name="endDate"></a>endDate | `Date` | `today + 2 days` | Default value for a reservation end date in ISO 8601 format.
| <a name="voucherCode"></a>voucherCode | `string` | `''` | Default value for a voucher code.
| <a name="adultCount"></a>adultCount | `number` | `2` | Default value for an adult count in a rate selection.
| <a name="childCount"></a>childCount | `number` | `0` | Default value for a child count in a rate selection.
| <a name="daysInterval"></a>daysInterval | `number` | `2` | Default interval (in days) between a start date and an end date.
| <a name="rooms"></a>rooms | `Array` | `null` | A list of guids of the room types to display in Distributor. If not set, all rooms are displayed.<br><br> You can get a guid of a room type from the room type's detail page in Commander. The page can be found on the Room criteria page (under Settings > "Your hotel's name" > Room criteria ) by selecting the Room type criterion, and then selecting a corresponding room type from the side menu. The guid is listed there as Identifier.<br><br> Order in which room types will be displayed is dependant on the order of guids inside of the array.
| <a name="hideSpecialRequests"></a>hideSpecialRequests | `boolean` | `false` | Hides the special requests field in the checkout form.
| <a name="showRateCompare"></a>showRateCompare | `boolean` | `false` | Enables the information bar on the second page of a booking that lists competitor prices.
| <a name="competitors"></a>competitors | `Array of string` | `['Booking.com', 'Expedia.com', 'HRS.com']` | An array of competitor names to be shown in the rate comparer. Max 3 names are used.
| <a name="introVideoSrc"></a>introVideoSrc | `string` | null | A URL link to optional intro video, which will be displayed instead of the intro image. The video must be publicly available, and it should be in the *MPEG4* format (`.mp4`) to work on the most of modern browsers ([example](http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4)). The video will be displayed only on screens that are big enough. 
| <a name="theme"></a>theme | `object` | `{}` | An object used for setting custom theme values. See [customization](#customization) for more info.
| <a name="gtmContainerId"></a>gtmContainerId | `string` | `null` | A Google Tag Manager container id is a string associated with your container in the `GTM-XXXX` format. More about how to find it is [here](https://support.google.com/tagmanager/answer/6103696?hl=en#AddingTheContainerSnippet).

### Customization

Distributor has all styles written in javascript and bundled into the script. This way we can limit a possibility of clashes when it's included into your website. To allow customization, we have the `theme` option, taking your custom values. Currently supported are:

| Name | Type | Description
| --- | --- | --- |
| <a name="primaryColor"></a>primaryColor | `string` | Value for the primary color. All possible denominations of color in CSS are accepted, except explicit color names (i.e 'red' will not work).

## API Reference

API calls are defined on the Distributor instance, which is created with the initialization call. This instance is returned to you as an argument of a callback function that you can pass as the second parameter to the initialization call. The following simple example shows how to use the calls to set up start and end dates, and then open the Distributor:

~~~html
<!-- Example of use of an instance to call the API --> 
<script>
Mews.Distributor({ 
    hotelIds: ['aaaa-bbbb-cccc-dddd-eeeeeeee'],
}, function(distributor) {
   $('.booking-button').click(function() {
     var start = new Date();
     var end = new Date();
     end.setDate(start.getDate() + 2);
     
     distributor.setStartDate(start);
     distributor.setEndDate(end);
     distributor.open();
   });
});
</script>
~~~

Beware that API is slightly different in the *Single* and the *Chain* modes. The list of all API calls follows:

### Common API calls

#### open()

Opens Distributor in it's overlay.

#### setStartDate(date)
- `date` Type: `string` - The start date to set

Sets start date for a new availability query, currently loaded availability list is not affected. If a `date` is not a valid Date object or its value isn't allowed as start date - nothing happens.

#### setEndDate(date)
- `date` Type: `string` - The end date to set

Sets end date for a new availability query, currently loaded availability list is not affected. If a `date` is not a valid Date object - nothing happens.

#### setVoucherCode(code)
- `code` Type: `string` - The voucher code to set

Sets a new voucher code value.

#### setRooms(rooms)
- `rooms` Type: `Array` - The list of guids of rooms to be displayed (see [`rooms`](#rooms) option for more details)

Sets a new list of the displayed room types, overwriting the initial Rooms option value. The currently loaded availability list is not affected.

### Only Single mode API calls

#### showRooms()

Sets Distributor to the `Rooms` step.

#### showRates(roomId)
- `roomId` Type: `string` - an ID of a room to be selected (see [`rooms`](#rooms) option for more details about those Ids)

Sets Distributor to the third step (`Rates`) as if you selected a room on the second screen.

### Only Chain mode API calls

#### showHotels()

Sets Distributor to the `Hotels` step.

#### showRooms(hotelId)
- `hotelId` Type: `string` - and ID of a hotel which rooms you want to display

Sets Distributor to the `Rooms` step.
