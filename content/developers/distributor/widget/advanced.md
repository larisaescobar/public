---
title: Advanced Guide
ordering: 20
---

### Full options example

Example with all possible options and their default values follows. To get further informations about options and api call, see [reference](./reference.html).

**Important: This is just an example, do not copy this directly to your website!**

~~~html
<script>
Mews.Distributor({
    // required
    hotelIds: ['aaaa-bbbb-cccc-dddd-eeeeeeee'],

    // optionals
    openElements: '',
    cityId: null,
    language: null,
    currency: null,
    endDate: null,
    startDate: null,
    voucherCode: null,
    adultCount: 2,
    childCount: 0,
    daysInterval: 2,
    rooms: null,
    hideSpecialRequests: false,
    showRateCompare: false,
    competitors: [
        'Booking.com',
        'Expedia.com',
        'HRS.com',
    ],
    introVideoSrc: null,
    
    // integrations
    gtmContainerId: null,

    // theme
    theme: {
        primaryColor: null
    }
}, function(distributor) {
    // Always available api calls
    
    // distributor.open();
    // distributor.setStartDate(date);
    // distributor.setEndDate(date);
    // distributor.setVoucherCode(code);
    // distributor.setRooms(rooms);
    
    // Singlehotel mode api calls
    // distributor.showRooms();
    // distributor.showRates(roomId);
    
    // Multihotel mode api calls
    // distributor.showHotels();
    // distributor.showRooms(hotelId);
});
</script>
~~~

#### Note
See that you have just one `<script>` tag containing `Mews.distributorEmbed` call in your page.

### Deeplinks

Distributor recognizes a set of parameteres passed to it in URL query. This allows you to deeplink into booking engine from other websites. **Important: This should not be used as a standard way to open Distributor from your own website**

Recognized parameters are:

| Name | Description
| --- | --- |
| mewsDistributorOpened | Tells the Distributor to open automatically.
| mewsStart | an arrival date in ISO 8601 format |
| mewsEnd | a departure date in ISO 8601 format |
| mewsVoucherCode | a voucher code
| language | a language code

#### Examples

- presets start date, voucher code and language, but Distributor stays closed
```
http://www.yourwebsite.com/?mewsStart=2015-01-01&mewVoucherCode=special-discount&language=en-US
```

- opens Distributor and starts on the Rooms step, showing availability for given dates
```
http://www.yourwebsite.com/?mewsEnterpriseId=aaaa-bbbb-cccc-dddd-eeeeeeee&mewsStart=2015-01-01&mewsEnd=2015-01-02
```

- opens Distributors and start on the Rates step, given that RoomTypeId is valid and that room is available for given dates
```
http://www.yourwebsite.com/?mewsEnterpriseId=aaaa-bbbb-cccc-dddd-eeeeeeee&mewsStart=2015-01-01&mewsEnd=2015-01-02&mewsRoomTypeId=mmmm-nnnn-oooo-pppppp
```

### Payment Gateways

Payement gateway is used to safely collect information about customer's credit card. Configuration is done once, when hotel is set up, and Distributor will use it automatically. Currently Distributor supports these gateways:

- [Braintree](https://www.braintreepayments.com/)
- [Adyen](https://www.adyen.com/home)
- Mews Merchant

Using payment gateway is not mandatory though as reservations can be created even without providing credit card information.

**Important:**
PCI Security Standard requires you to use **SSL Certificate** on you website in order to be allowed collecting any payments info, which is happening when using Braintree or Adyen gateway.

#### Mews Merchant

When using the Mews Merchant gateway integration in Distributor on your website, the customer will be redirected to a mirroring Distributor hosted at https://wwww.mews.li/ just before entering payment details. This is required when using the Mews Merchant. When closing the Distributor, the customer will be redirected back to your website