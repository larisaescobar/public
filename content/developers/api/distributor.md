---
title: Distributor API (v1)
---

The Distributor API allows external applications (generally known as booking widgets) to get information about offered rooms and products for given hotel, to check availability of those rooms and finally to create new reservations.

First of all, please have a look at [API Guidelines](../api.html) which describe general usage guidelines of MEWS APIs.

## Contents

- [Authorization](#authorization)
- [Operations](#operations)
    - [Get Hotel Info](#get-hotel-info)
    - [Validate Voucher](#validate-voucher)
    - [Get Availability](#get-availability)
    - [Get Reservations Pricing](#get-reservations-pricing)
    - [Get Braintree Client Token](#get-braintree-client-token)
    - [Get Adyen Client Token](#get-adyen-client-token)
    - [Create Reservation Group](#create-reservation-group)
    - [Get Reservation Group](#get-reservation-group)
- [Images](#images)
- [Payment Gateway Data](#payment-gateway-data)
- [Environments](#environments)
    - [Test Environment](#test-environment)
    - [Production Environment](#production-environment)
- [Changelog](#changelog)

## Authorization

The Distributor API is a public API with no authorization, it suffices to know unique identifier of a hotel in order to access it. However it is required that the client would identify itself by providing the `Client` property in all requests made to the API. As an example, have a look at [Get Hotel Info](#get-hotel-info) operation.

It is also a good practice to include version info, which makes potential problem investigation much simpler. For example our client uses the following identification `MEWS Distributor [version]`.

## Operations

### Get Hotel Info

Initial call used to obtain all static data about hotel relevant for the client.

#### Request `[PlatformAddress]/api/distributor/v1/hotels/get`

```json
{
    "Client": "My Client 1.0.0",
    "HotelId": "8dbb4b86-e6c5-4282-a996-e823afeef343"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Client` | string | required | Identification of the client as described in [Authorization](#authorization). |
| `HotelId` | string | required | Unique identifier of hotel. |

#### Response

```json
{
    "Countries": [
        {
            "Code": "US",
            "Name": "United States"
        }
    ],
    "Currencies": [
        {
            "Code": "EUR",
            "DecimalPlaces": 2,
            "Symbol": "€",
            "SymbolIsBehindValue": false,
            "ValueFormat": "€#,##0.00;- €#,##0.00"
        }
    ],
    "DefaultCurrencyCode": "EUR",
    "DefaultLanguageCode": "en-US",
    "DefaultRateCurrencyCode": "CZK",
    "Description":{
        "en-US": "Sample Hotel Description"
    },
    "IanaTimeZoneIdentifier": "Europe/Prague",
    "ImageId": "1627aea5-8e0a-4371-9022-9b504344e724",
    "IntroImageId": "1627aea5-8e0a-4371-9022-9b504344e724",
    "Languages":[
        {
            "Code": "en-US",
            "DefaultCulture":{
                "CurrencyDecimalSeparator": ".",
                "CurrencyGroupSeparator": "."
            },
            "Name": "English (United States)"
        }
    ],
    "Name":{
        "en-US": "Sample Hotel"
    },
    "PaymentGateway": null,
    "Products": [
        {
            "AlwaysIncluded": true,
            "Description": {
                "en-US": "Continental breakfast served in the morning."
            },
            "Id": "1627aea5-8e0a-4371-9022-9b504344e724",
            "ImageId": "1627aea5-8e0a-4371-9022-9b504344e724",
            "IncludedByDefault": true,
            "Name": {
                "en-US": "Breakfast"
            },
            "Prices": {
                "EUR": 5,
                "CZK": 150
            }
        }
    ],
    "RoomCategories":[
        {
            "Description": {
                 "en-US": "Very cozy room with nice bed."
            },
            "ExtraBedCount": 1,
            "Id": "1627aea5-8e0a-4371-9022-9b504344e724",
            "ImageIds": [
                "1627aea5-8e0a-4371-9022-9b504344e724"
            ],
            "Name": {
                "en-US": "Room"
            },
            "NormalBedCount": 2,
            "SpaceType": "Room"
        }
    ],
    "TermsAndConditionsUrl": "https://website.com/terms-and-conditions.html",
    "ImageBaseUrl": "https://cdn.demo.mews.li/Media/Image"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Countries` | array of [Country](#country) | required | Countries supported by hotel. |
| `Currencies` | array of [Currency](#currency) | required | Currencies accepted by hotel. |
| `DefaultCurrencyCode` | string | required | Code of hotel's default currency. |
| `DefaultLanguageCode` | string | required | Code of hotel's default language. |
| `DefaultRateCurrencyCode` | string | required | Code of currency of hotel's default rate. |
| `IanaTimezoneIdentifier` | string | required | Iana identifier of hotel's time zone |
| `ImageId` | string | optional | Unique identifier of hotel's logo image. |
| `IntroImageId` | string | optional | Unique identifier of hotel's intro image (usable as background image). |
| `Languages` | array of [Language](#language) | required | Languages supported by the hotel. |
| `Name` | [LocalizedText](#localizedtext) | required | Name of the hotel. |
| `Description` | [LocalizedText](#localizedtext) | required | Description of the hotel. |
| `PaymentGateway` | one of [PaymentGateway](#paymentgateway) types | optional | Info about payment gateway used by the hotel. |
| `Products` | array of [Product](#product) | required | All products orderable with rooms. |
| `RoomCategories` | array of [RoomCategory](#roomcategory) | required | All room categories offered by hotel. |
| `TermsAndConditionsUrl` | string | optional | URL of hotel's terms and conditions. |
| `ImageBaseUrl` | string | required | Base URL of images. |

##### Country

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Code` | string | required | ISO 3166-1 Aplha-2 code of the country. |
| `Name` | string | required | Name of the country. |

##### Currency

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Code` | string | required | Code of the currency in the ISO format. |
| `Symbol` | string | required | Symbol of the currency. |
| `ValueFormat` | string | required | Format of a currency value (for both positive and negative values, including symbol). |
| `DecimalPlaces` | number | required | Number of decimal places used with the currency value. |
| `SymbolIsBehindValue` | boolean | required | Indicates whether the symbol stands behind a value in standard formatting. |

##### Language

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Code` | string | required | Code of the language in the ISO format. |
| `Name` | string | required | Name of the language. |
| `DefaultCulture` | [Culture](#cuture) | required | Specifics of a default culture for the language. |

##### Culture

| Property | Type | | Description |
| --- | --- | --- | --- |
| `CurrencyDecimalSeparator` | string | required | Symbol used to separate decimal places in the currency value format. |
| `CurrencyGroupSeparator` | string | required | Symbol used to separate thousands in the currency value format. |

##### LocalizedText

A localized text is an object of the property values localized into languages supported by hotel, indexed by language codes.

##### Payment Gateway

If the hotel does not use any payment gateway, the value is null. If it does, then you should use a specific api call and the gateway's library to encode credit card data. The main purpose of a payment gateway is to securely obtain credit card of the customer before a reservation is created. You can decide not to support any of them and just ignore it, in which case reservations are created with note about missing credit card.

| Property | Type | | Description |
| --- | --- | --- | --- |
| `PaymentGatewayType` | string | required | Type of the payment gateway (`Braintree` or `Adyen`). |
| `IsMerchant` | boolean | required | Whether the gateway is processed through Mews Merchant or not. |
| `SupportedCreditCardTypes` | array of string | required | The list of supported credit cards, should be used to enhance UX. |

##### Product

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the product. |
| `Name` | [LocalizedText](#localizedtext) | required | Name of the product localized into all supported languages. |
| `Description` | [LocalizedText](#localizedtext) | required | Description of the product localized into all supported languages. |
| `ImageId` | string | optional | Unique identifier of the product's image. |
| `IncludedByDefault` | boolean | required | Indicates whether the product should be added to order by default. |
| `AlwaysIncluded` | boolean | required | Indicates whether the product is always included (= cannot be removed). |
| `Prices` | [CurrencyValues](#currencyvalues) | required | Price of the product. |

##### CurrencyValues

An object where field names correspond to currency ISO codes and field values to amounts. Only currencies that the hotel accepts are listed, for example:

```json
{
    "EUR": 100.00,
    "USD": 120.50,
    "CZK": 2500
}
```

##### RoomCategory

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the room category. |
| `Name` | [LocalizedText](#localizedtext) | required | Name of the room category localized into all supported languages. |
| `Description` | [LocalizedText](#localizedtext) | required | Description of the room category localized into all supported languages. |
| `ImageIds` | array of strings | required | Unique identifiers of images attached with the room category. |
| `NormalBedCount` | number | required | Number of normal beds in the room category. |
| `ExtraBedCount` | number | required | Number of extra beds possible in the room category. |
| `SpaceType` | string | required | Type of the room category - "Room" or "Bed". |

### Validate Voucher

Can be used to deterimne whether a voucher code is valid.

#### Request `[PlatformAddress]/api/distributor/v1/vouchers/validate`

```json
{
    "Client": "My Client 1.0.0",
    "HotelId": "8dbb4b86-e6c5-4282-a996-e823afeef343",
    "VoucherCode": "Discount2042"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Client` | string | required | Identification of the client as described in [Authorization](#authorization). |
| `HotelId` | string | required | Unique identifier of hotel. |
| `VoucherCode` | string | required | Code of voucher to validate, case sensitive. |

#### Response

```json
{
    "IsValid": false
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `IsValid` | boolean | required | Indicates whether the voucher code is valid. |

### Get Availability

Gives availabilities and pricings for given date interval with product prices included for each room category. Categorized by applicable rates and person counts from 1 to full room. If room category is not available, it is left out from response.

#### Request `[PlatformAddress]/api/distributor/v1/hotels/getAvailability`

```json
{
    "Client": "My Client 1.0.0",
    "HotelId": "8dbb4b86-e6c5-4282-a996-e823afeef343",
    "StartUtc": "2015-01-01T00:00:00Z",
    "EndUtc": "2015-01-03T00:00:00Z",
    "ProductIds": [
        "d0e88da5-ae64-411c-b773-60ed68954f64"
    ],
    "VoucherCode": "Discount2042",
    "AdultCount": 2,
    "ChildCount": 0
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Client` | string | required | Identification of the client as described in [Authorization](#authorization). |
| `HotelId` | string | required | Unique identifier of hotel. |
| `StartUtc` | string | required | Reservation start date (arrival date) in ISO 8601 format. |
| `EndUtc` | string | required | Reservation end date (departure date) in ISO 8601 format. |
| `ProductIds` | array of string | optional | Ids of products which should be included into pricing calculations. |
| `VoucherCode` | string | optional | Voucher code enabling special rate offerings. |
| `AdultCount` | number | optional | Requested number of adults. If provided together with `ChildCount`, then `RoomOccupancyAvailabilities` will be computed only for that combination instead of all possible. If `RoomCategory` doesn't support given values, nearest applicable are found. |
| `ChildCount` | number | optional | Requested number of children. |

#### Response

```json
{
    "Rates": [
        {
            "Id": "c1d48c54-9382-4ceb-a820-772bf370573d",
            "Name": {
                "en-US": "Rate"
            },
            "Description": {
                "en-US": "Best rate available."
            }
        }
    ],
    "RoomCategoryAvailabilities": [
        {
            "RoomCategoryId": "4037c0ec-a59d-43f1-9d97-d6c984764e8c",
            "AvailableRoomCount": 5,
            "RoomOccupancyAvailabilities": [
                {
                    "AdultCount": 1,
                    "ChildCount": 0,
                    "Pricing": [
                        {
                            "RateId": "c1d48c54-9382-4ceb-a820-772bf370573d",
                            "Price": { },
                            "MaxPrice": { }
                        }
                    ]
                },
                {
                    "AdultCount": 2,
                    "ChildCount": 0,
                    "Pricing": [
                        {
                            "RateId": "c1d48c54-9382-4ceb-a820-772bf370573d",
                            "Price": {
                                "Total": { },
                                "AveragePerNight": { }
                            },
                            "MaxPrice": {
                                "Total": { },
                                "AveragePerNight": { }
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Rates` | array of [Rate](#rate) | required | Information about all available rates. |
| `RoomCategoryAvailabilites` | array of [RoomCategoryAvailability](#roomcategoryavailability) | required | Availabilities of room categories. If a room category is not available, it is not included. |

##### Rate

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the rate. |
| `Name` | [LocalizedText](#localizedtext) | required | Name of the rate localized into all supported languages. |
| `Description` | [LocalizedText](#localizedtext) | required | Description of the rate localized into all supported languages. |

##### RoomCategoryAvailability

| Property | Type | | Description |
| --- | --- | --- | --- |
| `RoomCategoryId` | string | required | Unique identifier of the room category. |
| `RoomOccupancyAvailabilities` | array of [RoomOccupancyAvailability](#roomoccupancyavailability) | required | Availabilities of rooms in the category by the room occupancy. |
| `AvailableRoomCount` | number | required | Number of available rooms from the room category. |

##### RoomOccupancyAvailability

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AdultCount` | number | required | Number of adults for the associated pricing. |
| `ChildCount` | number | required | Number of childs for the associated pricing. |
| `Pricing` | array of [Pricing](#pricing) | required | Pricing information. |

##### Pricing

| Property | Type | | Description |
| --- | --- | --- | --- |
| `RateId` | string | required | Unique identifier of a rate. |
| `Price` | [RoomPrice](#roomprice) | required | Price of the room. |
| `MaxPrice` | [RoomPrice](#roomprice) | required | Max price of the room with the same parameters and conditions among other rates. Can be understood (and possibly displayed) as the value before discount. |

##### RoomPrice

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Total` | [CurrencyValues](#currencyvalues) | required | Total price of the room for whole reservation. |
| `AveragePerNight` | [CurrencyValues](#currencyvalues) | required | Average price per night. |

### Get Reservations Pricing

Gives a pricing information for the given configuration.

#### Request `[PlatformAddress]/api/distributor/v1/reservations/getPricing`

```json
{
    "Client": "My Client 1.0.0",
    "HotelId": "8dbb4b86-e6c5-4282-a996-e823afeef343",
    "StartUtc": "2015-01-01T00:00:00Z",
    "EndUtc": "2015-01-03T00:00:00Z",
    "VoucherCode": "Discount2042",
    "RoomCategoryId": "1627aea5-8e0a-4371-9022-9b504344e724",
    "Occupancies": [
        {
            "AdultCount": 2,
            "ChildCount": 0
        }
    ],
    "ProductIds": [
        "d0e88da5-ae64-411c-b773-60ed68954f64"
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Client` | string | required | Identification of the client as described in [Authorization](#authorization). |
| `HotelId` | string | required | Unique identifier of the hotel. |
| `StartUtc` | string | required | Start date of the reservation (arrival date). |
| `EndUtc` | string | required | End date of the reservation (departure date). |
| `VoucherCode` | string | optional | A voucher code. |
| `RoomCategoryId` | string | required | Identifier of the requested room category. |
| `Occupancies` | array of [Occupancy](#occupancy) | required | Occupancies of the reservations. |
| `ProductIds` | array of string | optional | Identifiers of the requested products. |

##### Occupancy

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AdultCount` | number | required | Number of adults. |
| `ChildCount` | number | required | Number of children. |

#### Response

```json
{
    "OccupancyPrices": [
        {
            "AdultCount": 1,
            "ChildCount": 0,
            "Pricing": [
                {
                    "MaxPrice": {
                        "AveragePerNight": {},
                        "Total": {}
                    },
                    "Price": {
                        "AveragePerNight": {},
                        "Total": {}
                    },
                    "RateId": "b34330be-7e19-453e-8959-592c4e820f85"
                }
            ]
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `OccupancyPrices` | array of [RoomOccupancyAvailability](#roomoccupancyavailability) | required | Pricing information. |

### Get Braintree Client Token

Braintree requires a special client token to be generated for each transaction. In case the hotel uses Braintree as a payment gateway, you need to obtain it to before processing payment.

#### Request `[PlatformAddress]/api/distributor/v1/payments/getBraintreeClientToken`

```json
{
    "Client": "My Client 1.0.0",
    "HotelId": "8dbb4b86-e6c5-4282-a996-e823afeef343"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Client` | string | required | Identification of the client as described in [Authorization](#authorization). |
| `HotelId` | string | required | Unique identifier of hotel. |

#### Response

```json
{
    "ClientToken": "..."
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `ClientToken` | string | required | Braintree client token generated on server. |

### Get Adyen Client Token

Adyen requires a public key and a server utc time to be used for client-side credit card encryption. In case the hotel uses Adyen as a payment gateway, you need to obtain it to before processing payment.

#### Request `[PlatformAddress]/api/distributor/v1/payments/getAdyenClientToken`

```json
{
    "Client": "My Client 1.0.0",
    "HotelId": "8dbb4b86-e6c5-4282-a996-e823afeef343"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Client` | string | required | Identification of the client as described in [Authorization](#authorization). |
| `HotelId` | string | required | Unique identifier of hotel. |

#### Response

```json
{
    "NowUtc": "2015-01-01T13:42:05Z",
    "PublicKey": "..."
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `NowUtc` | string | required | Server time. |
| `PublicKey` | string | required | Adyen Public key. |

### Create Reservation Group

#### Request `[PlatformAddress]/api/distributor/v1/reservationGroups/create`

```json
{
    "Client": "My Client 1.0.0",
    "HotelId": "8dbb4b86-e6c5-4282-a996-e823afeef343",
    "Customer": {
        "Email": "hiro@snow.com",
        "FirstName": "Hiro",
        "LastName": "Protagonist",
        "Telephone": "",
        "AddressLine1": "",
        "AddressLine2": "",
        "City": "",
        "PostalCode": "",
        "StateCode": "",
        "NationalityCode": ""
    },
    "Reservations": [
        {
            "RoomCategoryId": "4037c0ec-a59d-43f1-9d97-d6c984764e8c",
            "StartUtc": "2015-01-01T00:00:00Z",
            "EndUtc": "2015-01-03T00:00:00Z",
            "VoucherCode": "Discount2042",
            "RateId": "c1d48c54-9382-4ceb-a820-772bf370573d",
            "AdultCount": 3,
            "ChildCount": 0,
            "ProductIds": [
                "d0e88da5-ae64-411c-b773-60ed68954f64"
            ],
            "Notes": "Quiet room please."
        }
    ],
    "CreditCardData": {
        "PaymentGatewayData": "...",
        "ObfuscatedCreditCardNumber": "411111******1111"
    }
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Client` | string | required | Identification of the client as described in [Authorization](#authorization). |
| `HotelId` | string | required | Unique identifier of the hotel. |
| `Customer` | [Customer](#customer) | required | Information about customer who creates the order. |
| `Reservations` | array of [ReservationData](#reservationdata) | required | Parameters of reservations to be ordered. |
| `CreditCardData` | [CreditCardData](#creditcarddata) | optional | Credit card data, required if hotel has payment gateway. |

##### Customer

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Email` | string | required | Email of the customer. |
| `FirstName` | string | required | First name of the customer. |
| `LastName` | string | required | Last name of the customer. |
| `Telephone` | string | optional | Telephone number of the customer. |
| `AddressLine1` | string | optional | First line of the address. |
| `AddressLine2` | string | optional | Second line of the address. |
| `City` | string | optional | City. |
| `PostalCode` | string | optional | Postal code of the address. |
| `StateCode` | string | optional | ISO 3166-2 code of the state, e.g. `US-AL`.  |
| `NationalityCode` | string | optional | ISO 3166-1 Aplha-2 code of the customer's nation country, e.g. `US`.  |

##### ReservationData

| Property | Type | | Description |
| --- | --- | --- | --- |
| `RoomCategoryId` | string | required | Identifier of the requested room category. |
| `StartUtc` | string | required | Start date of the reservation (arrival date). |
| `EndUtc` | string | required | End date of the reservation (departure date). |
| `VoucherCode` | string | optional | A voucher code, set to be paired with reservation for later retrieval only. Actual voucher rate used is determined by setting a proper `RateId`. |
| `RateId` | string | required | Identifier of the chosen rate. |
| `AdultCount` | number | required | Number of adults. |
| `ChildCount` | number | required | Number of children. |
| `ProductIds` | array of string | optional | Identifiers of the requested products. |
| `Notes` | string | optional | Additional notes. |

##### CreditCardData

| Property | Type | | Description |
| --- | --- | --- | --- |
| `PaymentGatewayData` | string | required | Encoded credit card data obtained from the payment gateway specific library. More details [here](#payment-gateway-data) |
| `ObfuscatedCreditCardNumber` | string | required | Obfuscated credit card number, e.g. `411111******1111`. |

#### Response

```json
{
    "Id": "f6fa7e62-eb22-4176-bc49-e521d0524dee",
    "CustomerId": "7ac6ca0d-7c08-4ab1-8da8-9b44979d8855",
    "Reservations": [
        {
            "Id": "123456ec-a59d-43f1-9d97-d6c984764e8c",
            "RoomCategoryId": "4037c0ec-a59d-43f1-9d97-d6c984764e8c",
            "StartUtc": "2015-01-01T00:00:00Z",
            "EndUtc": "2015-01-03T00:00:00Z",
            "RateId": "c1d48c54-9382-4ceb-a820-772bf370573d",
            "Rate": {
                "Id": "c1d48c54-9382-4ceb-a820-772bf370573d",
                "Name": {
                    "en-US": "Rate"
                },
                "Description": {
                    "en-US": "Best rate available."
                }
            },
            "AdultCount": 3,
            "ChildCount": 0,
            "ProductIds": [
                "d0e88da5-ae64-411c-b773-60ed68954f64"
            ],
            "Notes": "Quiet room please.",
            "Cost": { },
            "Number": "1234"
        }
    ],
    "TotalCost": { }
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the created reservation group. |
| `CustomerId` | string | required | Unique identifier of customer who created reservation group. |
| `Reservations` | array of [Reservation](#reservation) | required | The created reservations in group. |
| `TotalCost` | [CurrencyValues](#currencyvalues) | required | Total cost of the whole group. |

#### Reservation

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Identifier of the reservation. |
| `Number` | string | required | Confirmation number of the reservation. |
| `RoomCategoryId` | string | required | Identifier of the requested room category. |
| `StartUtc` | string | required | Start date of the reservation (arrival date). |
| `EndUtc` | string | required | End date of the reservation (departure date). |
| `AdultCount` | number | required | Number of adults. |
| `ChildCount` | number | required | Number of children. |
| `ProductIds` | array of string | optional | Identifiers of the requested products. |
| `RateId` | string | required | Identifier of the chosen rate. |
| `Notes` | string | optional | Additional notes. |
| `Cost` | [CurrencyValues](#currencyvalues) | required | Total cost of the reservation. |

#### Error Response

In case of an error caused by insufficient availability (which might have decreased since the time it was provided to the client), the error response may contain the following fields on top the standard ones:

| Property | Type | | Description |
| --- | --- | --- | --- |
| `ExceedingReservationIndexes` | array of number | optional | Indexes of reservations from the request that are not available anymore. |

### Get Reservation Group

#### Request `[PlatformAddress]/api/distributor/v1/reservationGroups/get`

```json
{
    "Client": "My Client 1.0.0",
    "HotelId": "8dbb4b86-e6c5-4282-a996-e823afeef343",
    "ReservationGroupId": "f6fa7e62-eb22-4176-bc49-e521d0524dee"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Client` | string | required | Identification of the client as described in [Authorization](#authorization). |
| `HotelId` | string | required | Unique identifier of the hotel. |
| `ReservationGroupId` | string | required | Unique identifier of the reservation group. |

#### Response

Same as in [Create Reservation Group](#create-reservation-group).

## Images

To obtain URL of an image from `ImageId`, use the following address pattern:

```
[ImageBaseUrl]/[ImageId]?Width=[Width]&Height=[Height]&Mode=[Mode]
```

E.g.

```
[ImageBaseUrl]/1627aea5-8e0a-4371-9022-9b504344e724?Width=640&Height=480&Mode=1
```

The `Width` and `Height` parameters are optional. The `Mode` paramater can have following values:

| Value | Name | Description |
| --- | --- | --- |
| `0` | Scale | The image is rescaled to have exactly the specified dimensions. May change image aspect ratio. |
| `1` | Cover | The image is resized to cover the specified dimensions while keeping the aspect ratio. So the result might be larger than the specified size (only in one dimension). The result is smallest possible image that covers the specified size. |
| `3` | CoverExact | The image is resized and clipped to cover the specified dimensions while keeping the aspect ratio. So parts of the image might be missing from the result. |
| `4` | Fit | The image is resized to fit within the specified dimensions while keeping the aspect ratio. So the result might be smaller than the specified size. The result is largest possible image the fits into the specified size. |
| `5` | FitExact | The image is resized and padded to exactly fit within the specified dimensions while keeping the aspect ratio. So parts of the result image might be blank (black or transparent depending on the image format). |

## Payment Gateway Data

To obtain `PaymentGatewayData`, you have to use client side encryption library provided by given payment gateway. You can find them here: 

- [Braintree](https://github.com/braintree/braintree-web)
- [Adyen](https://github.com/Adyen/CSE-JS)

## Environments

### Test Environment

This environment is meant to be used during implementation of the client applications. We have prepared one hotel where you can fetch availability and create reservation. You can use the following information to access it:

- **Platform Address** - `https://demo.mews.li`
- **Hotel Id** - `3edbe1b4-6739-40b7-81b3-d369d9469c48`

You will also have access into the system so it is possible for you to check whether the reservations sent to the API are correctly posted to the system. To sign into the system, use the following credentials:

- **Address** - `https://demo.mews.li`
- **Email** - `distributor-api@mews.li`
- **Password** - `distributor-api`

### Production Environment

- **Platform Address** - `https://www.mews.li`
- **Hotel Id** - Depends on the hotel, should be provided to you by the hotel administrator.

## Changelog

#### 15.8.2016

Added the [Authorization](#authorization) section and extended all operations with the `Client` parameter.
