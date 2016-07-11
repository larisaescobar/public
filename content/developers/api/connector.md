---
title: Connector API (v1)
---

The Connector API allows external applications to mediate communication between devices that or accessible over local network on the hotel site and the MEWS. There may be several types of connector clients. For example a client that would fetch device commands from the MEWS, execute them on physical devices that are locally accessible and send the command results back to MEWS. Or different client can retrieve some information from MEWS and forward the data to some other system (e.g. heating system, lock system).

First of all, please have a look at [API Guidelines](../api.html) which describe general usage guidelines of MEWS APIs.

## Contents

- [Authorization](#authorization)
- [Operations](#operations)
    - [Sign in](#sign-in)
    - [Get All Spaces](#get-all-spaces)
    - [Get All Reservations](#get-all-reservations)
    - [Start Reservation](#start-reservation)
    - [Process Reservation](#process-reservation)
    - [Cancel Reservation](#cancel-reservation)
    - [Get Customer Balance](#get-customer-balance)
    - [Get Customers Open Items](#get-customers-open-items)
    - [Update Customer](#update-customer)
    - [Add Credit Card Payment](#add-credit-card-payment)
    - [Get All Commands](#get-all-commands)
    - [Update Command](#update-command)
    - [Devices](#devices)
- [Environments](#environments)
    - [Demo Environment](#demo-environment)
    - [Production Environment](#production-environment)

## Authorization

All operations of the API require `AccessToken` to be present in the request. In production environment, the `Token` will be provided to you by the hotel admin. For development purposes, consult the  [Demo Environment](#demo-environment) section.

The API also supports more advanced scenario with session management, which makes it simple to ensure that only one client is active at a time. That is particulary useful if the client communicates with a physical device that does not support parallel connections/communication. For more information, see the [Sign in](#sign-in) operation.

## Operations

### Sign in

Signs in the client application to MEWS using a token that you would normally use as the `AccessToken` in all operations as described in [Authorization](#authorization) section. Returns a new `AccessToken` that should be passed to all other operations. Note that the returned `AccessToken` has limited validity - only until next successful sign in operation. After that, the `AccessToken` returned by the first sign in operation is no longer valid. As a consequence, there is always at most one client with valid `AccessToken`, i.e. the client that signed in last.

#### Request `[PlatformAddress]/api/connector/v1/app/signIn`

```json
{
    "ConnectorToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `ConnectorToken` | string | required | Unique token identifying the client application. Can be obtained from MEWS from enterprise settings. |

#### Response

```json
{
    "AccessToken": "210F2620DDAE4A988D26DEB3A5B75B2F-77EB7EA147D2EAB4863054EB85FFACE",
    "Enterprise": 
    {
        "Id": "222b5d8a-0492-4271-9941-cd6d89b81d43",
        "Name": "Test Hotel"
    }
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | An access token representing the client application session. |
| `Enterprise` | [Enterprise](#enterprise) | required | Enterprise whose data the connector client handles. |


##### Enterprise

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the enterprise. |
| `Name` | string | required | Name of the enterprise. |

### Get All Spaces

Returns all spaces of an enterprise associated with the connector integration.

#### Request `[PlatformAddress]/api/connector/v1/spaces/getAll`g

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |

#### Response

```json
{
    "Spaces": [
        {
            "Id": "5ee074b1-6c86-48e8-915f-c7aa4702086f",
            "Type": "Room",
            "Number": "101",
            "ParentSpaceId": null,
            "CategoryId": "aaed6e21-1c1f-4644-9872-e53f96a21bf9",
            "State": "Dirty"
        },
        {
            "Id": "c32386aa-1cd2-414a-a823-489325842fbe",
            "Type": "Room",
            "Number": "102",
            "ParentSpaceId": null,
            "CategoryId": "aaed6e21-1c1f-4644-9872-e53f96a21bf9",
            "State": "Clean"
        }
    ],
    "SpaceCategories": [
        {
            "Id": "aaed6e21-1c1f-4644-9872-e53f96a21bf9",
            "Name": "Best Room",
            "ShortName": "BR"
        }
    ]
}
```
c
| Property | Type | | Description |
| --- | --- | --- | --- |
| `Spaces` | array of [Space](#space) | required | The spaces of the enterprise. |
| `SpaceCategories` | array of [Space Category](#space-category) | required | Categories of spaces in the enterprise. |

##### Space

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the space. |
| `Type` | string | required | Type of the space. For example `Room` or `Bed`, other types might be added in the future. |
| `Number` | string | required | Number of the space (e.g. room number). |
| `ParentSpaceId` | string | optional | Identifier of the parent space (e.g. room of a bed). |
| `CategoryId` | string | required | Identifier of the cateogory assigned to the space. |
| `State` | string | required | State of the room. Either `Dirty`, `Clean`, `Inspected` or `OutOfService`. |

##### Space Category

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the category. |
| `Name` | string | required | Name of the category. |
| `ShortName` | string | optional | Short name (e.g. code) of the category. |

### Get All Reservations

Returns all reservations that collide with the specified interval.

#### Request `[PlatformAddress]/api/connector/v1/reservations/getAll`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "StartUtc": "2016-01-01T00:00:00Z",
    "EndUtc": "2016-01-07T00:00:00Z"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `StartUtc` | string | required | Start of the interval in UTC timezone in ISO 8601 format. |
| `EndUtc` | string | required | End of the interval in UTC timezone in ISO 8601 format. |
| `LoadItems` | bool | optional | Whether the reservation items should be loaded. |

#### Response

```json
{
    "ReservationGroups": [
        {
            "Id": "c704dff3-7811-4af7-a3a0-7b2b0635ac59",
            "Name": "13-12-Smith-F712"
        }
    ],
    "Reservations": [
        {
            "AssignedSpaceId": "5ee074b1-6c86-48e8-915f-c7aa4702086f",
            "BusinessSegmentId": "ad42231f-a5b6-466f-b9ef-0bc4d76e8c4c",
            "Companions": [
                {
                    "Email": null,
                    "FirstName": "Jane",
                    "Id": "2a1a4315-7e6f-4131-af21-402cec59b8b9",
                    "LastName": "Smith",
                    "NationalityCode": null,
                    "Phone": null
                },
                {
                    "Email": "john.smith@mews.li",
                    "FirstName": "John",
                    "Id": "35d4b117-4e60-44a3-9580-c582117eff98",
                    "LastName": "Smith",
                    "NationalityCode": "US",
                    "Phone": null
                }
            ],
            "ChannelNumber": "123456",
            "ChannelManagerId": "132-456-789",
            "Customer": {
                "Email": "john.smith@mews.li",
                "FirstName": "John",
                "Id": "35d4b117-4e60-44a3-9580-c582117eff98",
                "LastName": "Smith",
                "NationalityCode": "US",
                "Phone": null
            },
            "EndUtc": "2019-12-30T23:00:00Z",
            "GroupId": "c704dff3-7811-4af7-a3a0-7b2b0635ac59",
            "Id": "2bbb5d8a-0492-4271-9941-cd6d89b81d43",
            "Number": "123",
            "StartUtc": "2015-07-07T00:00:00Z",
            "State": "Started",
            "TravelAgencyId": "ea473215-fe52-4c1a-bd85-3dedc1c4bd56"
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Reservations` | array of [Reservation](#reservation) | required | The reservations that collide with the specified interval. |
| `ReservationGroups` | array of [ReservationGroup](#reservationgroup) | required | Reservation groups that the reservations are members of. |

##### Reservation

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the reservation. |
| `GroupId` | string | required | Unique identifier of the reservation group. |
| `Number` | string | required | Confirmation number of the reservation. |
| `ChannelNumber` | string | optional | Confirmation number of the reservation within a channel in case the reservation originates there (e.g. Booking.com confirmation number). |
| `ChannelManagerId` | string | optional | Identifier of the reservation within a channel manager in case the reservation came through it (e.g. Siteminder identifier). |
| `State` | string | required | State of the reservation. One of: `Confirmed` (before check-in), `Started` (checked-in) or `Processed` (checked-out). |
| `CreatedUtc` | string | required | Creation date and time of the reservation in UTC timezone in ISO 8601 format. |
| `StartUtc` | string | required | Start of the reservation (arrival) in UTC timezone in ISO 8601 format. |
| `EndUtc` | string | required | End of the reservation (departure) in UTC timezone in ISO 8601 format. |
| `RequestedCategoryId` | string | required | Identifier of the requested space category. |
| `AssignedCategoryId` | string | required | Identifier of the assigned space category (differs from requested e.g. when upgraded). |
| `AssignedSpaceId` | string | optional | Identifier of the assigned space. |
| `BusinessSegmentId` | string | optional | Identifier of the reservation business segment. |
| `CompanyId` | string | optional | Identifier of the company on behalf of which the reservation was made. |
| `TravelAgencyId` | string | optional | Identifier of the travel agency that mediated the reservation. |
| `RateId` | string | required | Identifier of the reservation rate. |
| `AdultCount` | number | required | Count of adults the reservation was booked for. |
| `ChildCount` | number | required | Count of children the reservation was booked for. |
| `Customer` | [Customer](#customer) | required | Owner of the reservation. |
| `Companions` | array of [Customer](#customer) | required | Customers that will occupy the space. |
| `Items` | array of [Item](#item) | required | Revenue items related to the reservation, populated only if the `LoadItems` is specified in the parameters, otherwise empty. |

##### Customer

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the customer. |
| `FirstName` | string | optional | First name of the customer. |
| `LastName` | string | required | Last name of the customer. |
| `Title` | string | optional | Title prefix of the customer (`Mister`, `Miss` or `Misses`). |
| `Gender` | string | optional | Gender of the customer (`Male` or `Female`). |
| `NationalityCode` | string | optional | ISO 3166-1 alpha-2 country code (two letter country code) of the nationality. |
| `BirthDateUtc` | string | optional | Date of birth in UTC timezone in ISO 8601 format. |
| `Email` | string | optional | Email address of the customer. |
| `Phone` | string | optional | Phone number of the customer (possibly mobile). |
| `CategoryId` | string | optional | Unique identifier of the customer category. |
| `Address` | [Address](#address) | required | Address of the customer. |

##### Address

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Line1` | string | optional | First line of the address. |
| `Line2` | string | optional | Second line of the address. |
| `City` | string | optional | The city. |
| `PostalCode` | string | optional | Postal code. |
| `CountryCode` | string | optional | ISO 3166-1 alpha-2 country code (two letter country code). |

##### ReservationGroup

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the reservation group. |
| `Name` | string | optional | Name of the reservation group, might be empty or same for multiple groups. |

### Start Reservation

Marks a reservation as `Started` (= checked in). Succeeds only if all starting conditions are met (the reservation has the `Confirmed` state, does not have start set to future, has an inspected room assigned etc).

#### Request `[PlatformAddress]/api/connector/v1/reservations/start`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ReservationId": "e6ea708c-2a2a-412f-a152-b6c76ffad49b"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ReservationId` | string | required | Unique identifier of the reservation to start. |

#### Response

Empty object.

### Process Reservation

Marks a reservation as `Processed` (= checked out). Succeeds only if all processing conditions are met (the reservation has the `Started` state, all customer bills are closed etc).

#### Request `[PlatformAddress]/api/connector/v1/reservations/process`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ReservationId": "e6ea708c-2a2a-412f-a152-b6c76ffad49b"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ReservationId` | string | required | Unique identifier of the reservation to process. |

#### Response

Empty object.

### Cancel Reservation

Cancels a reservation. Succeeds only if the reservation is cancellable

#### Request `[PlatformAddress]/api/connector/v1/reservations/cancel`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ReservationId": "e6ea708c-2a2a-412f-a152-b6c76ffad49b",
    "ChargeCancellationFee": true,
    "Notes": "Cancellation through Connector API"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ReservationId` | string | required | Unique identifier of the reservation to cancel. |
| `ChargeCancellationFee` | boolean | required | Whether cancellation fees should be charged according to rate conditions. |
| `Notes` | string | required | Addiotional notes describing the cancellation. |

#### Response

Empty object.

### Get Customer Balance

Returns current open balance of a customer. If the balance is positive, the customer has some unpaid items. Otherwise the customer does not owe anything to the hotel at the moment.

#### Request `[PlatformAddress]/api/connector/v1/customers/getBalance`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "CustomerId": "2a1a4315-7e6f-4131-af21-402cec59b8b9"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `CustomerId` | string | required | Unique identifier of the customer. |

#### Response

```json
{
    "Currency": "EUR",
    "Value": 100,
    "Tax": null,
    "TaxRate": null
}
```

##### Currency Value

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Currency` | string | required | ISO-4217 currency code, e.g. `EUR` or `USD`. |
| `Value` | number | required | Amount in the currency (including tax if taxed). |
| `TaxRate` | number | optional | Tax rate in case the item is taxed (e.g. `0.21`). |
| `Tax` | number | optional | Tax value in case the item is taxed. |

### Get Customers Open Items

Returns all open items of the specified customers, i.e. all unpaid items and all deposited payments. Sum of the open items is the balance of the customer.

#### Request `[PlatformAddress]/api/connector/v1/customers/getOpenItems`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "CustomerIds": [
        "2a1a4315-7e6f-4131-af21-402cec59b8b9"
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `CustomerIds` | array of string | required | Unique identifiers of the customers. |

#### Response

```json
{
    "Customers": [
        {
            "CustomerId": "2a1a4315-7e6f-4131-af21-402cec59b8b9",
            "Items": [
                {
                    "Amount": {
                        "Currency": "EUR",
                        "Value": -100
                    },
                    "ConsumptionUtc": "2016-05-25T15:56:54Z",
                    "Id": "79aa7645-fe3a-4e9e-9311-e11df4686fca",
                    "Name": "Cash Payment EUR",
                    "OrderId": null
                },
                {
                    "Amount": {
                        "Currency": "EUR",
                        "Value": 150
                    },
                    "ConsumptionUtc": "2016-05-25T18:00:00Z",
                    "Id": "ad679ead-6e8f-409b-85fd-1b0f73db393e",
                    "Name": "Night 25.5.2016",
                    "OrderId": "e4840894-42a1-4848-85b5-9eba71e6ffb5"
                }
            ]
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Customers` | array of [Customer Items](#customer-items) | required | The customers with their items. |

##### Customer Items

| Property | Type | | Description |
| --- | --- | --- | --- |
| `CustomerId` | string | required | Unique identifier of the customer. |
| `Items` | array of [Item](#item) | required | The open items. |

##### Item

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the item. |
| `OrderId` | string | optional | Unique identifier of the order (or reservation) the item belongs to. |
| `Name` | string | required | Name of the item. |
| `ConsumptionUtc` | string | required | Date and time of the item consumption in UTC timezone in ISO 8601 format. |
| `Amount` | [Currency Value](#currency-value) | required | Amount the item costs, negative amount represents either rebate or a payment. |

### Update Customer

Updates personal information of a customer.

#### Request `[PlatformAddress]/api/connector/v1/customers/update`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "CustomerId": "35d4b117-4e60-44a3-9580-c582117eff98",
    "FirstName": "John",
    "LastName": "Smith",
    "Phone": "00420123456789",
    "NationalityCode": "US"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `CustomerId` | string | required | Unique identifier of the customer. |
| `FirstName` | string | optional | New first name. |
| `LastName` | string | required | New last name. |
| `Phone` | string | optional | New phone number. |
| `NationalityCode` | string | optional | ISO 3166-1 alpha-2 country code (two letter country code) of the new nationality. |

#### Response

```json
{
    "Email": null,
    "FirstName": "John",
    "Id": "35d4b117-4e60-44a3-9580-c582117eff98",
    "LastName": "Smith",
    "NationalityCode": "US",
    "Phone": "00420123456789"
}
```

The updated [Customer](#customer) object.

### Add Credit Card Payment

Adds a new credit card payment to a customer. Returns updated balance of the customer.

#### Request `[PlatformAddress]/api/connector/v1/payments/addCreditCard`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "CustomerId": "35d4b117-4e60-44a3-9580-c582117eff98",
    "Amount": { 
        "Currency": "GBP", 
        "Value": 100
    },
    "CreditCard": {
        "Type": "Visa",
        "Number": "411111******1111",
        "Expiration": "12/2016",
        "Name": "John Smith"
    },
    "Category": {
        "Code": "CCP"
    },
    "ReceiptIdentifier": "123456",
    "Notes": "Terminal A"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `CustomerId` | string | required | Unique identifier of the customer. |
| `Amount` | [Currency Value](#currency-value) | required | Amount of the credit card payment. |
| `CreditCard` | [Credit Card](#credit-card) | required | Credit card details. |
| `Category` | [Accounting Category](#accounting-category) | optional | Accounting category to be assigned to the payment. |
| `ReceiptIdentifier` | string | optional | Identifier of the payment receipt. |
| `Notes` | string | optional | Additional payment notes. |

##### Credit Card

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Type` | string | required | Type of the credit card, one of: `Visa`, `MasterCard`, `Amex`, `Discover`, `DinersClub`, `Jcb`, `EnRoute`, `Maestro`, `UnionPay`. |
| `Number` | string | required | Obfuscated credit card number. At most first six digits and last four digits can be specified, the digits in between should be replaced with `*`. It is possible to provide even more obfuscated number or just last four digits. **Never provide full credit card number**. For example `411111******1111`. |
| `Expiration` | string | required | Expiration of the credit card in format `MM/YYYY`, e.g. `12/2016` or `04/2017`. |
| `Name` | string | required | Name of the card holder. |

##### Accounting Category

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Code` | string | required | Code of the accounting category in MEWS. |
| `Name` | string | optional | Name of the category, used if no category is matched using the code. |

#### Response

```json
{
    "Currency": "GBP",
    "Value": 100
}
```

Balance ([Currency Value](#customer)) of the customer after the payment is posted.

### Get All Commands

A device command is in one of the following states:

- `Pending` - A command that is created in MEWS, but not yet received by a client application.
- `Received` - A command received by a client application.
- `Processing` - A command that is being processed.
- `Processed` - A successfully processed command.
- `Cancelled` - A command whose execution has been cancelled before (or during) processing.
- `Error` - A command whose execution was or processing was terminated by an error.

This operation returns all commands the are still active from the client application point of view. That means commands that are in either `Pending` or `Received` state.

#### Request `[PlatformAddress]/api/connector/v1/commands/getAllActive`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |

#### Response

```json
{
    "Commands": [
        {
            "Id": "aa20961f-6d9e-4b35-ad25-071213530aec",
            "State": "Pending",
            "CreatedUtc": "2015-09-02T19:25:44Z",
            "Creator": {
                "FirstName": "Sample",
                "LastName": "User",
                "ImageUrl": "..."
            },
            "Device": {
                "Id": "63efb573-fc58-4065-b687-9bdd51568529",
                "Name": "Test Printer",
                "Type": "Printer"
            },
            "Data": {
                "CopyCount": 1,
                "FileType": "application/pdf",
                "FileData": "...",
                "PrinterName": "Printer",
                "PrinterDriverName": "",
                "PrinterPortName": ""
            }
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Commands` | array of [Command](#command) | required | The active commands. |

##### Command

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the command. |
| `State` | string | required | State of the command. |
| `CreatedUtc` | string | required | Creation date and time of the command. |
| `Creator` | [User](#user) | optional | Creator of the command. |
| `Device` | [Device](#device) | required | Device that the command should be executed on. |
| `Data` | object | optional | Data of the command depending on device type and command type. Details in the [devices](#devices) section. |

##### User

| Property | Type | | Description |
| --- | --- | --- | --- |
| `FirstName` | string | optional | First name of the user. |
| `LastName` | string | required | Last name of the user. |
| `ImageUrl` | string | optional | URL of the profile image. |

##### Device

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the device. |
| `Name` | string | required | Name of the device. |
| `Type` | string | optional | Type of the device. |

### Update Command

Updates state of a command.

#### Request `[PlatformAddress]/api/connector/v1/commands/update`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "CommandId": "aa20961f-6d9e-4b35-ad25-071213530aec",
    "State": "Processed"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `CommandId` | string | required | Identifier of the command to be updated. |
| `State` | string | required | New state of the command. |
| `Progress` | number | optional | Progress of the command processing. Only used if the `State` is `Processing`, otherwise ignored. |
| `Result` | object | optional | Result of the command depending on device type and command type. Only used if the `State` is `Processed`, otherwise ignored. Details in the [devices](#devices) section. |
| `Notes` | string | optional | Notes about command execution. Only used if the `State` is `Processed`, `Cancelled` or `Error`, otherwise ignored. |

#### Response

Empty object.

### Devices

#### Printers

Device type: `Printer`

##### Command Data

| Property | Type | | Description |
| --- | --- | --- | --- |
| `CopyCount` | number | required | Number of copies to be printed. |
| `FileType` | string | required | MIME type of the file to be printed (e.g. `application/pdf`). |
| `FileData` | string | required | Base64-encoded data of the file to be printed. |
| `PrinterName` | string | required | Name of the printer. |
| `PrinterDriverName` | string | required | Name of the printer driver. |
| `PrinterPortName` | string | required | Name of the printer port. |

##### Command Result

Not used.

#### VisiOnline Key Cutters

Device type: `VisiOnlineKeyCutter`

##### Command Data

| Property | Type | | Description |
| --- | --- | --- | --- |
| `ApiUrl` | string | required | VisiOnline API URL. |
| `UserName` | string | required | VisiOnline user name. |
| `Password` | string | required | VisiOnline password. |
| `KeyCutterId` | string | required | Identifier of the key cutter which should cut the keys. |
| `LockIds` | array of string | required | Identifiers of locks/rooms the key should open. |
| `ValidityStartUtc` | string | required | Start of the key validity interval in UTC timezone in ISO 8601 format. |
| `ValidityEndUtc` | string | required | End of the key validity interval in UTC timezone in ISO 8601 format. |
| `KeyCount` | number | required | Count of keys to cut. |
| `Reservation` | [Reservation](#reservation) | optional | Additional information about the reservation. |

##### Command Result

Not used.

## Environments

### Demo Environment

This environment is meant to be used during implementation of the client applications.

- **Platform Address** - `https://demo.mews.li`
- **Access Token** - `C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D`

The hotel is based in UK, it accepts `GBP`, `EUR` and `USD` currencies (any of them may be used), as a tax rate, either `0.0`, `0.05` or `0.20` can be used. The predefined accounting categories have codes: `FOOD`, `BVG` and `ABVG`. To sign into the system, use the following credentials:

- **Address** - `https://demo.mews.li`
- **Email** - `connector-api@mews.li`
- **Password** - `connector-api`

### Production Environment

- **Platform Address** - `https://www.mews.li`
- **Access Token** - Depends on the hotel, should be provided to you by the hotel admin.
