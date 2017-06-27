---
title: Integrations
ordering: 50
---

The main and only source of Distributor integrations with 3rd party tags and tracking services is [Google Tag Manager](https://www.google.com/analytics/tag-manager/).

## Google Tag Manager

### Notice of Usage

**Google Tag Manager is a 3rd party service and we provide this integration as is. We export a set of supported events and their data to the container, however, we have no control over what happens with them and how they are used. Below we provide a set of basic setup examples that have been tested and verified to work with Distributor. If you need a more complex setup, it is up to you to configure and test it.**

### Enabling Google Tag Manager in Distributor

You can enable it by passing your container's id in the [`gtmContainerId`](./reference.html#gtmContainerId) option. The id has the `GTM-XXXXXX` format and you can get it in Google Tag Manager.

**Important: It is not enough to just copy and paste the Google Tag Manager container code to a website, you have to use the 'gtmContainerId' option. However, if you use the container on your website, Distributor will connect to it and will not create a new one.**

### Migrating to Google Tag Manager

In previous versions, Distributor supported direct integrations with Google Analytics and Google AdWords. These legacy integrations still remain functional for a backward compatibility, but will be removed completely in the near future. We strongly suggest you migrate them to Google Tag Manager.

**Important: If you enable Google Tag Manager in Distributor, it will take precedence over any legacy integration, and those will not be triggered. Meaning that once you enable Google Tag Manager, you will have to migrate all of your integrations to it!**

### Triggers

This is a basic description of how to set up a Distributor event as a Trigger. You can get full reference of all Distributor events [here](#triggers-reference).

For an integration with Google Tag Manager, Distributor provides a set of *Custom Events* that you can set up as *Triggers*. To setup a Trigger for an event, match it with its name:

![trigger](./img/trigger.png)

If you want to track multiple events with one Trigger, you can easily use regex matching on an event name. For example `^distributor` will track every distributor event, which can be useful for setting a Trigger for Universal Analytics:

![regex trigger](./img/triggerRegex.png)

### Basic setups

#### Universal Analytics

You can track all the events for further statistical computations about behaviour of your customers. Use the Google Universal Analytics tag with the `Event` track type. The Trigger should be a regex grouping of all the events you want to track (to track *all* events, you can use `^distributor` regex as described [here](#triggers)).

![events_tag](./img/eventsTag.png) 

#### Google Ecommerce

You can track transactions with the Google Universal Analytics tag with the `Transaction` track type on the `distributorBookingFinished` event. All the needed data for tracking is set in the Tag Manager's *dataLayer* and will be passed automatically.

![ecommerce_tag](./img/ecommerceTag.png)

##### Tracking with Mews Merchant and source attribution

When you have Mews Merchant set up, a payment by a customer is legally required to happen on our domain. Therefore, all the transactions during a checkout are attributed to Mews domain. This is an unfortunate limitation of the checkout process that we cannot currently overcome.

### Troubleshooting

#### There are no events or ecommerce transactions tracked after a redirect to the Mews Merchant page

You have probably included the container in your website, however, you haven't set the container id in Distributor. Meaning that after the redirect, Distributor will not know anything about your container. You should use [`gtmContainerId`](#gtmContainerId).

#### I've set up the container correctly but there are still no events tracked

If you have everything set up correctly and you still cannot see events tracked, please, ensure that you're not using any ad-blocking or similar software in your browser. They tend to block not only ads, but also tracking software like Google Tag Manager. Disabling the software for testing or adding your website to the exceptions should solve the issue.

**Important:** If you are using Mews Merchant, you need to disable the software for the mews.li domain too.

#### The Tag Assistant Chrome extension shows me a warning about multiple installations, but I use only one

Distributor includes our Mews Google Tag Manager container (id `GTM-M7JV35D`) to keep statistics in our own Google Analytics. We use that data for a global Distributor performance measuring, to have an idea about performance in hotels that don't use Analytics and for the ability to build our own statistics on top of the Analytics API in Commander.

Having multiple installations is perfectly fine, if you keep common data layer name for all of them, which we do. Please, see the official documentation: https://developers.google.com/tag-manager/devguide#multiple-containers

### Triggers Reference

All events data is passed to Tag Manager through *dataLayer*. To use it in your tags, set up *Variable* with proper name as a variable of the data layer like this:

![variable](./img/variable.png)

Each event is fired with a standard set of data:

| Data Layer Variable Name | Description
| --- | --- |
| eventName | Name of the event in readable form without prefix, i.e. `Step Dates`. |

If a hotel is selected, information about it is also added to the event. (Note: The hotel is always selected in the *Singlehotel* mode)

| Data Layer Variable Name | Description
| --- | --- |
| hotelName | name of the hotel |
| hoteId | unique identifier of the hotel |

Some events expose additional data layer variables. They are described separately for each event.

#### distributorLoaded
The Distributor application was initialized (triggers once per session even with a Merchant redirect).

#### distributorOpened
Distributor was opened.

#### distributorClosed
Distributor was closed.

#### distributorStepDates
A Dates step was displayed.

#### distributorStepHotels
A Hotels step was displayed.

#### distributorStepRooms
A Rooms step was displayed.

#### distributorStepRates
A Rates step was displayed.

#### distributorStepSummary
A Summary step was displayed.

#### distributorStepCheckout
A Checkout step was displayed.

#### distributorStepConfirmation
A confirmation page was displayed.

#### distributorLanguageCodeChanged
A language code was changed.

| Data Layer Variable Name | Description
| --- | --- |
| languageCode | Language code of the selected language, i.e. `en-US`. |

#### distributorStartDateSelected
A start date of the reservation was selected.

| Data Layer Variable Name | Description
| --- | --- |
| startDate | Selected start date in ISO 8601 format YYYY-MM-DD, i.e. `2017-01-20`. |

#### distributorEndDateSelected
An end date of the reservation was selected.

| Data Layer Variable Name | Description
| --- | --- |
| endDate | Selected end date in ISO 8601 format YYYY-MM-DD, i.e. `2017-01-22`. |

#### distributorPromoCodeSelected
A promo code was set.

| Data Layer Variable Name | Description
| --- | --- |
| promoCode | Value of the inserted promo code as a string, i.e. `promo`. It is not validated. |

#### distributorAvailabilityLoaded
Availability of hotel was loaded.

| Data Layer Variable Name | Description
| --- | --- |
| availableRooms | Array of available rooms. |

Each item in the `availableRooms` array contains following data:

| Name | Description
| --- | --- |
| roomName | Name of the room. |
| roomId | Guid of the room. |
| availableRateIds | List of available rate ids for the room |

#### distributorOfferedDatesSelected
Alternative dates when there is no availability selected.

#### distributorRoomSelected
A room (or other space type) was selected.

| Data Layer Variable Name | Description
| --- | --- |
| roomId | Guid of the selected room. |
| roomName | Name of the selected room in the hotel's default language. |
| spaceType | Name of the selected room's space type, one of `Room`, `Bed` or `Dorm`.  |

#### distributorSpaceTypeCountChanged
A number of the selected "rooms" in the order was changed.

| Data Layer Variable Name | Description
| --- | --- |
| count | Number of the selected space types. |

#### distributorRoomOccupancyChanged
An occupation (adults and children counts) was changed for the one room (or similar) space type.

| Data Layer Variable Name | Description
| --- | --- |
| roomIndex | Index of the changed room. |
| adultCount | Number of the selected adults. |
| childCount | Number of the selected children. |

#### distributorBedOccupancyChanged
An occupation (adults and children counts) was changed for the bed space type.

| Data Layer Variable Name | Description
| --- | --- |
| adultCount | Number of the selected adults. |
| childCount | Number of the selected children. |

#### distributorProductAdded
A product was added to the order.

| Data Layer Variable Name | Description
| --- | --- |
| productId | Guid of the added product. |
| productName | Name of the product in the hotel's default language. |

#### distributorProductRemoved
A product was removed from the order.

| Data Layer Variable Name | Description
| --- | --- |
| productId | Guid of the removed product. |
| productName | Name of product in the hotel's default language. |

#### distributorBookingFinished
A booking was made. This event triggers once every reservation group is made.

| Data Layer Variable Name | Description
| --- | --- |
| reservationGroupId | id of the reservation group |
| totalCost | total cost of the reservation group, in the hotel's default currency |
| currencyCode | hotel's default currency code in ISO format |

#### distributorReservationCreated
A reservation was created. This event triggers when each reservation is made in the reservation group.

| Data Layer Variable Name | Description
| --- | --- |
| customerEmail | the customer's email |
| customerName | the customer's name |
| currencyCode | hotel's default currency code in ISO format |
| reservationGroupId | id of the reservation group |
| reservation.id | id of the reservation |
| reservation.rateId | if of the rate of the reservation |
| reservation.number | confirmation number of the reservation |
| reservation.roomName | name of the room|
| reservation.startDate | start date of the reservation |
| reservation.endDate | end date of the reservation |
| reservation.nights | total nights spent |
| reservation.cost | cost of the reservation in the hotel's default currency |

