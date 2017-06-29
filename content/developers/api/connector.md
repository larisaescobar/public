---
title: Connector API (v1)
---

The Connector API serves as en endpoint for communication between MEWS and external systems. Or for applications that mediate communication between MEWS and the third party systems. Typically the external systems are running on site in the enterprise (e.g. POS systems, printers and other physical devices, kiosks etc), but the API may also be used by other cloud systems (e.g. revenue management systems, cloud POS systems). 

First of all, please have a look at [API Guidelines](../api.html) which describe general usage guidelines of MEWS APIs. If you are interested in changes and updates of this API, check [Changelog](#changelog). To see some scenarios when this API might be used, have a look at the [Use Cases](#use-cases) section.

## Contents

- [Authorization](#authorization)
    - [Environments](#environments)
- Operations
    - Configuration
        - [Get Configuration](#get-configuration)
    - Enterprises
        - [Get All Companies](#get-all-companies)
        - [Get All Spaces](#get-all-spaces)
        - [Get All Space Blocks](#get-all-space-blocks)
        - [Update Space State](#update-space-state)
        - [Add Task](#add-task)
    - Services
        - [Get All Services](#get-all-services)
        - [Get All Products](#get-all-products)
        - [Get All Business Segments](#get-all-business-segments)
        - [Get All Rates](#get-all-rates)
        - [Get Rate Pricing](#get-rate-pricing)
        - [Update Rate Price](#update-rate-price)
    - Reservations
        - [Get All Reservations](#get-all-reservations)
        - [Get All Reservations By Ids](#get-all-reservations-by-ids)
        - [Get All Reservation Items](#get-all-reservation-items)
        - [Start Reservation](#start-reservation)
        - [Process Reservation](#process-reservation)
        - [Cancel Reservation](#cancel-reservation)
        - [Update Reservation Interval](#update-reservation-interval)
        - [Update Reservation Space](#update-reservation-space)
        - [Update Reservation Requested Category](#update-reservation-requested-category)
        - [Add Reservation Companion](#add-reservation-companion)
        - [Delete Reservation Companion](#delete-reservation-companion)
        - [Add Reservation Product](#add-reservation-product)
    - Customers
        - [Get All Customers](#get-all-customers)
        - [Get All Customers By Ids](#get-all-customers-by-ids)
        - [Search Customers](#search-customers)
        - [Get Customers Open Items](#get-customers-open-items)
        - [Add Customer](#add-customer)
        - [Update Customer](#update-customer)
        - [Charge Customer](#charge-customer)
    - Finance
        - [Get All Accounting Categories](#get-all-accounting-categories)
        - [Get All Accounting Items](#get-all-accounting-items)
        - [Add Credit Card Payment](#add-credit-card-payment)
    - Commands
        - [Get All Commands](#get-all-commands)
        - [Update Command](#update-command)
        - [Devices](#devices)
 - [Websockets](#websockets)
 - [Use Cases](#use-cases)
 - [Changelog](#changelog)

## Authorization

All operations of the API require `AccessToken` to be present in the request. In production environment, the `AccessToken` will be provided to you by the enterprise admin. For development purposes, use the  [Demo Environment](#demo-environment).

### Environments

#### Demo Environment

This environment is meant to be used during implementation of the client applications.

- **Platform Address** - `https://demo.mews.li`
- **Access Token** - `C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D`

The enterprise is based in UK, it accepts `GBP`, `EUR` and `USD` currencies (any of them may be used), as a tax rate, either `0.0`, `0.05` or `0.20` can be used. The predefined accounting categories have codes: `FOOD`, `BVG` and `ABVG`. To sign into the system, use the following credentials:

- **Address** - `https://demo.mews.li`
- **Email** - `connector-api@mews.li`
- **Password** - `connector-api`

#### Production Environment

- **Platform Address** - `https://www.mews.li`
- **Access Token** - Depends on the enterprise, should be provided to you by the enterprise admin.

## Operations

### Get Configuration

Returns configuration of the enterprise and the client.

#### Request `[PlatformAddress]/api/connector/v1/configuration/get`

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
    "Enterprise": {
        "Id": "851df8c8-90f2-4c4a-8e01-a4fc46b25178",
        "Name": "Connector API Hotel"
    }
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Enterprise` | [Enterprise](#enterprise) | required | The enterprise (e.g. hotel, hostel) associated with the access token. |

##### Enterprise

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the enterprise. |
| `Name` | string | required | Name of the enterprise. |

### Get All Companies

Returns all company profiles of the enterprise, possible filtered by their identifiers.

#### Request `[PlatformAddress]/api/connector/v1/companies/getAll`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `Ids` | array of string | optional | If specified, returns only companies with the specified identifiers.  |

#### Response

```json
{
    "Companies": [
        {
            "Id": "207b9da3-1c2a-45df-af20-54e57a13368c",
            "Name": "IBM"
        },
        {
            "Id": "217b9da3-1c2a-45df-af20-54e57a13368c",
            "Name": "Booking.com"
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Companies` | array of [Company](#company) | required | The company profiles of the enterprise. |

##### Company

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the company. |
| `Name` | string  | required | Name of the company. |

### Get All Spaces

Returns all spaces of an enterprise associated with the connector integration.

#### Request `[PlatformAddress]/api/connector/v1/spaces/getAll`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "Extent": {
        "Spaces": true,
        "SpaceCategories": true,
        "SpaceFeatures": false
    }
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `Extent` | [Space Extent](#space-extent) | optional | Extent of data to be returned. If not specified, `Spaces` and `SpaceCategories` is used as the default extent. |

##### Space Extent

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Spaces` | bool | optional | Whether the response should contain spaces. |
| `SpaceCategories` | bool | optional | Whether the response should contain space categories. |
| `SpaceFeatures` | bool | optional | Whether the response should contain space features and their assignments. |

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
    ],
    "SpaceFeatures": [
        {
            "Description": null,
            "Id": "a693dd8c-21fe-4dae-b450-ea3bd9ab3bb0",
            "Name": "123"
        }
    ],
    "SpaceFeatureAssignments": [
        {
            "SpaceFeatureId": "a693dd8c-21fe-4dae-b450-ea3bd9ab3bb0",
            "SpaceId": "18019693-c66f-4be8-a893-c3d89fd291cc"
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Spaces` | array of [Space](#space) | required | The spaces of the enterprise. |
| `SpaceCategories` | array of [Space Category](#space-category) | required | Categories of spaces in the enterprise. |
| `SpaceFeatures` | array of [Space Feature](#space-feature) | optional | Features of spaces in the enterprise. |
| `SpaceFeatureAssignments` | array of [Space Feature Assignment](#space-feature-assignment) | optional | Assignments of space features to spaces. |

##### Space

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the space. |
| `Type` | string [Space Type](#space-type) | required | Type of the space. |
| `Number` | string | required | Number of the space (e.g. room number). |
| `ParentSpaceId` | string | optional | Identifier of the parent [Space](#space) (e.g. room of a bed). |
| `CategoryId` | string | required | Identifier of the [Space Category](#space-category) assigned to the space. |
| `State` | string [Space State](#space-state) | required | State of the room. |

##### Space Type

- `Room`
- `Dorm`
- `Bed`
- ...

##### Space State

- `Dirty`
- `Clean`
- `Inspected`
- `OutOfService`
- `OutOfOrder`

##### Space Category

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the category. |
| `Name` | string | required | Name of the category. |
| `ShortName` | string | optional | Short name (e.g. code) of the category. |

##### Space Feature

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the feature. |
| `Name` | string | required | Name of the feature. |
| `Description` | string | optional | Description of the feature. |

##### Space Feature Assignment

| Property | Type | | Description |
| --- | --- | --- | --- |
| `SpaceId` | string | required | Unique identifier [Space](#space). |
| `SpaceFeatureId` | string | required | Unique identifier [Space Feature](#space-feature). |

### Get All Space Blocks

Returns all space blocks (out of order blocks or house use blocks) colliding with the specified interval.

#### Request `[PlatformAddress]/api/connector/v1/spaceBlocks/getAll`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "StartUtc": "2016-01-01T00:00:00Z",
    "EndUtc": "2017-01-01T00:00:00Z"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `StartUtc` | string | required | Start of the interval in UTC timezone in ISO 8601 format. |
| `EndUtc` | string | required | End of the interval in UTC timezone in ISO 8601 format. |

#### Response

```json
{
    "SpaceBlocks": [
        {
            "AssignedSpaceId": "5ee074b1-6c86-48e8-915f-c7aa4702086f",
            "CreatedUtc": "2016-03-29T22:02:34Z",
            "EndUtc": "2016-01-01T16:00:00Z",
            "Id": "5ab9d519-2485-4d77-85c4-2a619cbdc4e7",
            "StartUtc": "2016-01-01T10:00:00Z",
            "Type": "HouseUse",
            "UpdatedUtc": "2016-03-29T22:02:34Z"
        },
        {
            "AssignedSpaceId": "f7c4b4f5-ac83-4977-a41a-63d27cc6e3e9",
            "CreatedUtc": "2016-03-29T15:14:06Z",
            "EndUtc": "2016-01-01T16:00:00Z",
            "Id": "4d98ad40-a726-409e-8bf3-2c12ff3c0331",
            "Type": "OutOfOrder",
            "StartUtc": "2016-01-01T10:00:00Z",
            "UpdatedUtc": "2016-03-29T15:14:06Z"
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `SpaceBlocks` | array of [Space Block](#space-block) | required | The space blocks colliding with the interval. |

##### Space Block

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the block. |
| `AssignedSpaceId` | string | required | Unique identifier of the assigned [Space](#space). |
| `Type` | string [Space Block Type](#space-block-type) | required | Type of the space block. |
| `StartUtc` | string | required | Start of the block in UTC timezone in ISO 8601 format. |
| `EndUtc` | string | required | End of the block in UTC timezone in ISO 8601 format. |
| `CreatedUtc` | string | required | Creation date and time of the block in UTC timezone in ISO 8601 format. |
| `UpdatedUtc` | string | required | Last update date and time of the block in UTC timezone in ISO 8601 format. |

##### Space Block Type

- `OutOfOrder`
- `HouseUse`

### Update Space State

Updates state of the specified space. Note that the state is also updated on the child spaces of the specified space. So if e.g. dorm space is set to `Dirty`, ale subspaces (beds) are also set to `Dirty`.

#### Request `[PlatformAddress]/api/connector/v1/spaces/updateState`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "SpaceId": "41b3e3a2-3400-4d72-86d4-1e341ccf8977",
    "State": "Inspected"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `SpaceId` | string | required | Unique identifier of the [Space](#space) to be updated. |
| `State` | string [Space State](#space-state) | required | New state of the space (`Dirty`, `Clean`, `Inspected` or `OutOfService`). |

#### Response

Empty object.

### Add Task

Adds a new task to the enterprise, optionally to a specified department.

#### Request `[PlatformAddress]/api/connector/v1/tasks/add`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "DepartmentId": null,
    "Name": "Test",
    "Description": "Task description",
    "DeadlineUtc": "2016-01-01T14:00:00Z"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `DepartmentId` | string | optional | Unique identifier of the department the task is addressed to. |
| `Name` | string | required | Name (or title) of the task. |
| `Description` | string | optional | Further decription of the task. |
| `DeadlineUtc` | string | required | Deadline of the task in UTC timezone in ISO 8601 format. |

#### Response

Empty object.

### Get All Services

Raturns all services offered by the enterprise.

#### Request `[PlatformAddress]/api/connector/v1/services/getAll`

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
    "Services": [
        {
            "EndTime": null,
            "Id": "fc79a518-bc69-45b8-93bd-83326201bd14",
            "IsActive": true,
            "Name": "Restaurant",
            "StartTime": null,
            "Promotions": {
                "BeforeCheckIn": false,
                "AfterCheckIn": false,
                "DuringStay": false,
                "BeforeCheckOut": false,
                "AfterCheckOut": false
            }
        },
        {
            "EndTime": "PT12H",
            "Id": "bd26d8db-86da-4f96-9efc-e5a4654a4a94",
            "IsActive": true,
            "Name": "Accommodation",
            "StartTime": "PT14H",
            "Promotions": {
                "BeforeCheckIn": false,
                "AfterCheckIn": false,
                "DuringStay": false,
                "BeforeCheckOut": false,
                "AfterCheckOut": false
            }
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Services` | array of [Service](#service) | required | Services offered by the enterprise. |

##### Service

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the service. |
| `IsActive` | boolean | required | Whether the service is still active. |
| `Name` | string | required | Name of the service. |
| `StartTime` | string | optional | Default start time of the service orders in ISO 8601 duration format. |
| `EndTime` | string | optional | Default end time of the service orders in ISO 8601 duration format. |
| `Promotions` | [Promotions](#promotions) | required | Promotions of the service. |

##### Promotions

| Property | Type | | Description |
| --- | --- | --- | --- |
| `BeforeCheckIn` | boolean | required | Whether it can be promoted before check-in. |
| `AfterCheckIn` | boolean | required | Whether it can be promoted after check-in. |
| `DuringStay` | boolean | required | Whether it can be promoted during stay. |
| `BeforeCheckOut` | boolean | required | Whether it can be promoted before check-out. |
| `AfterCheckOut` | boolean | required | Whether it can be promoted after check-out. |

### Get All Products

Raturns all products offered together with the specified services.

#### Request `[PlatformAddress]/api/connector/v1/products/getAll`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ServiceIds": [
        "bd26d8db-86da-4f96-9efc-e5a4654a4a94"
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ServiceIds` | array of string | required | Unique identifiers of the [Service](#service)s. |

#### Response

```json
{
    "Products": [
        {
            "Id": "198bc308-c1f2-4a1c-a827-c41d99d52f3d",
            "IsActive": true,
            "Name": "Breakfast",
            "ServiceId": "bd26d8db-86da-4f96-9efc-e5a4654a4a94",
            "ShortName": "BFST",
            "Promotions": {
                "BeforeCheckIn": false,
                "AfterCheckIn": false,
                "DuringStay": false,
                "BeforeCheckOut": false,
                "AfterCheckOut": false
            }
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Products` | array of [Product](#product) | required | Products offered with the service. |

##### Product

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the product. |
| `ServiceId` | string | required | Unique identifier of the [Service](#service). |
| `IsActive` | boolean | required | Whether the product is still active. |
| `Name` | string | required | Name of the product. |
| `ShortName` | string | required | Short name of the product. |
| `Promotions` | [Promotions](#promotions) | required | Promotions of the service. |

### Get All Business Segments

Returns all business segments of the default service provided by the enterprise.

#### Request `[PlatformAddress]/api/connector/v1/businessSegments/getAll`

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
    "BusinessSegments": [
        {
            "Id": "7760b5cb-a666-41bb-9758-76bf5d1df399",
            "IsActive": true,
            "Name": "Business"
        },
        {
            "Id": "54ec08b6-e6fc-48e9-b8ae-02943e0ac693",
            "IsActive": true,
            "Name": "Leisure"
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `BusinessSegments` | array of [Business Segment](#business-segment) | required | Business segments of the default service. |

##### Business Segment

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the segment. |
| `IsActive` | boolean | required | Whether the business segment is still active. |
| `Name` | string | required | Name of the segment. |

### Get All Rates

Returns all rates (pricing setups) and rate groups (condition settings) of the default service provided by the enterprise.

#### Request `[PlatformAddress]/api/connector/v1/rates/getAll`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "Extent": {
        "Rates": true,
        "RateGroups": true,
        "RateRestrictions": false
    }
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `Extent` | [Rate Extent](#reservation-extent) | optional | Extent of data to be returned. If not specified, `Rates` and `RateGroups` is used as the default extent. |

##### Rate Extent

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Rates` | bool | optional | Whether the response should contain rates. |
| `RateGroups` | bool | optional | Whether the response should contain rate groups. |
| `RateRestrictions` | bool | optional | Whether the response should contain rate restrictions. |

#### Response

```json
{
    "Rates": [
        {
            "BaseRateId": null,
            "GroupId": "c8b866b3-be2e-4a47-9486-034318e9f393",
            "Id": "ed4b660b-19d0-434b-9360-a4de2ea42eda",
            "IsActive": true,
            "Name": "Fully Flexible",
            "ShortName": "FF"
        }
    ],
    "RateGroups": [
        {
            "Id": "c8b866b3-be2e-4a47-9486-034318e9f393",
            "IsActive": true,
            "Name": "Default"
        }
    ],
    "RateRestrictions": {
        "DateRestrictions": [
            {
                "Id": "cd12a0db-1eeb-4eda-965c-229efff4bd5d",
                "IsInherited": true,
                "RateId": "b7e30382-ccd2-4982-8a29-0eb8d9386e1a",
                "EndUtc": "2019-12-31T23:00:00Z",
                "IsInverted": false,
                "StartUtc": "2016-12-31T23:00:00Z"
            }
        ],
        "EarlinessRestrictions": [
            {
                "Id": "0b9f74e7-3b7b-4472-a476-8ac1f01696ea",
                "IsInherited": true,
                "RateId": "b7e30382-ccd2-4982-8a29-0eb8d9386e1a",
                "MaxAdvance": null,
                "MinAdvance": "P7D"
            }
        ],
        "LengthRestrictions": [
            {
                "Id": "c91dcd27-fd53-4bc6-bb2a-a783e39c61f1",
                "IsInherited": true,
                "RateId": "b7e30382-ccd2-4982-8a29-0eb8d9386e1a",
                "EndUtc": null,
                "MaxLength": null,
                "MinLength": "P4D",
                "StartUtc": null
            }
        ]
    }
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Rates` | array of [Rate](#rate) | required | Rates of the default service. |
| `RateGroups` | array of [Rate Group](#rate-group) | required | Rate groups of the default service. |
| `RateRestrictions` | [Rate Restrictions](#rate-restrictions) | required | Rate restrictions of the rates. |

##### Rate

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the rate. |
| `GroupId` | string | required | Unique identifier of [Rate Group](#rate-group) where the rate belongs. |
| `BaseRateId` | string | required | Unique identifier of the base [Rate](#rate). |
| `IsActive` | boolean | required | Whether the rate is still active. |
| `Name` | string | required | Name of the rate. |
| `ShortName` | string | required | Short name of the rate. |

##### Rate Group

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the group. |
| `IsActive` | boolean | required | Whether the rate group is still active. |
| `Name` | string | required | Name of the rate group. |

##### Rate Restrictions

| Property | Type | | Description |
| --- | --- | --- | --- |
| `DateRestrictions` | array of [Date Restriction](#date-restriction) | optional | The date restrictions. |
| `EarlinessRestrictions` | array of [Earliness Restriction](#earliness-restriction) | optional | The earliness restrictions. |
| `LengthRestrictions` | array of [Length Restriction](#length-restriction) | optional | The length restrictions. |

##### Date Restriction

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the restriction. |
| `RateId` | string | required | Unique identifier of the restricted [Rate](#rate). |
| `IsInherited` | boolean | required | Whether child rates inherit the restriction. |
| `IsInverted` | boolean | required | Whether restriction disallows booking in the interval (if `false` then reservations are allowed only within the interval, if `true` then reservations are not allowed within the interval).  |
| `StartUtc` | string | required | Start of the allowed interval in UTC timezone in ISO 8601 format. |
| `EndUtc` | string | required | End of the allowed interval in UTC timezone in ISO 8601 format. |

##### Earliness Restriction

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the restriction. |
| `RateId` | string | required | Unique identifier of the restricted [Rate](#rate). |
| `IsInherited` | boolean | required | Whether child rates inherit the restriction. |
| `MinAdvance` | string | optional | Minimal advance for reservation creation in ISO 8601 duration format. |
| `MaxAdvance` | string | optional | Maximal advance for reservation creation in ISO 8601 duration format. |

##### Length Restriction

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the restriction. |
| `RateId` | string | required | Unique identifier of the restricted [Rate](#rate). |
| `IsInherited` | boolean | required | Whether child rates inherit the restriction. |
| `MinLength` | string | optional | Minimal reservation length in ISO 8601 duration format. |
| `MaxLength` | string | optional | Maximal reservation length in ISO 8601 duration format. |
| `StartUtc` | string | optional | Start of the restriction applicability interval in UTC timezone in ISO 8601 format. |
| `EndUtc` | string | optional | End of the restriction applicability interval in UTC timezone in ISO 8601 format. |

### Get Rate Pricing

Returns prices of a rate in the specified interval. Note that response contains prices for all dates that the specified interval intersects. So e.g. interval `1st Jan 13:00 - 1st Jan 14:00` will result in one price for `1st Jan`. Interval `1st Jan 23:00 - 2nd Jan 01:00` will result in two prices for `1st Jan` and `2nd Jan`.

#### Request `[PlatformAddress]/api/connector/v1/rates/getPricing`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "RateId": "ed4b660b-19d0-434b-9360-a4de2ea42eda",
    "StartUtc":"2017-01-01T00:00:00.000Z",
    "EndUtc":"2017-01-03T00:00:00.000Z"
}
```

#### Response

```json
{
    "BasePrices": [
        20,
        20,
        20
    ],
    "CategoryPrices": [
        {
            "CategoryId": "e3aa3117-dff0-46b7-b49a-2c0391e70ff9",
            "Prices": [
                20,
                20,
                20
            ]
        }
    ],
    "DatesUtc": [
        "2016-12-31T23:00:00Z",
        "2017-01-01T23:00:00Z",
        "2017-01-02T23:00:00Z"
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `BasePrices` | array of Number | required | Base prices of the rate in the covered dates. |
| `CategoryPrices` | array of [Space Category Pricing](#space-category-pricing) | required | Space category prices. |
| `DatesUtc` | array of string | required | Covered dates in UTC timezone in ISO 8601 format. |

##### Space Category Pricing


| Property | Type | | Description |
| --- | --- | --- | --- |
| `CategoryId` | string | required | Unique identifier of the [Space Category](#space-category). |
| `Prices` | array of Number | required | Prices of the rate for the space category in the covered dates. |


### Update Rate Price

Updates price of a rate in the specified intervals. If the `CategoryId` is specified, updates price of the corresponding [Space Category](#space-category), otherwise updates the base price. Note that prices are defined daily, so when the server receives the UTC interval, it first converts it to enterprise timezone and updates the price on all dates that the interval intersects.

#### Request `[PlatformAddress]/api/connector/v1/rates/updatePrice`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "RateId": "ed4b660b-19d0-434b-9360-a4de2ea42eda",
    "PriceUpdates": [
        {
            "StartUtc": "2016-09-01T00:00:00Z",
            "EndUtc": "2016-09-02T00:00:00Z",
            "Value": 111
        },
        {
            "CategoryId": "e3aa3117-dff0-46b7-b49a-2c0391e70ff9",
            "StartUtc": "2016-09-04T00:00:00Z",
            "EndUtc": "2016-09-05T00:00:00Z",
            "Value": 222
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `RateId` | string | required | Unique identifier of the [Rate](#rate) to update. |
| `PriceUpdates` | array of [Price Update](#price-update) | required | Price updates. |

##### Price Update

| Property | Type | | Description |
| --- | --- | --- | --- |
| `CategoryId` | string | optional | Unique identifier of the [Space Category](#space-category) whose prices to update. If not specified, base price is updated. |
| `StartUtc` | string | required | Start of the interval in UTC timezone in ISO 8601 format. |
| `EndUtc` | string | required | End of the interval in UTC timezone in ISO 8601 format. |
| `Value` | number | required | New value of the rate on the interval. |

#### Response

Empty object.

### Get All Reservations

Returns all reservations from the specified interval according to the time filter (e.g. colliding with that interval or created in that interval).

#### Request `[PlatformAddress]/api/connector/v1/reservations/getAll`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "StartUtc": "2016-01-01T00:00:00Z",
    "EndUtc": "2016-01-07T00:00:00Z",
    "Extent": {
        "Reservations": true,
        "ReservationGroups": true,
        "Customers": true
    }
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `TimeFilter` | string [Reservation Time Filter](#reservation-time-filter) | optional | Time filter of the interval. If not specified, reservations `Colliding` with the interval are returned. |
| `StartUtc` | string | required | Start of the interval in UTC timezone in ISO 8601 format. |
| `EndUtc` | string | required | End of the interval in UTC timezone in ISO 8601 format. |
| `States` | array of string [Reservation State](#reservation-state) | optional | States the reservations should be in. If not specified, reservations in `Confirmed`, `Started` or `Processed` states are returned. |
| `Extent` | [Reservation Extent](#reservation-extent) | optional | Extent of data to be returned. E.g. it is possible to specify that together with the reservations, customers, groups and rates should be also returned. If not specified, `Reservations`, `Groups` and `Customers` is used as the default extent. |

##### Reservation Time Filter

- `Colliding` - reservation interval collides with the interval.
- `Created` - reservation created within the interval.
- `Updated` - reservation updated within the interval.
- `Start`- reservation start (= arrival) within the interval.
- `End` - reservation end (= departure) within the interval.
- `Overlapping` - reservation interval contains the specified interval.
- `Cancelled` - reservation cancelled within the specified interval.

##### Reservation Extent

| Property | Type | | Description |
| --- | --- | --- | --- |
| `BusinessSegments` | bool | optional | Whether the response should contain business segmentation. |
| `Customers` | bool | optional | Whether the response should contain customers of the reservations. |
| `Items` | bool | optional | Whether the response should contain reservation items. |
| `Products` | bool | optional | Whether the response should contain products orderable with the reservations. |
| `Rates` | bool | optional | Whether the response should contain rates and rate groups. |
| `Reservations` | bool | optional | Whether the response should contain reservations. |
| `ReservationGroups` | bool | optional | Whether the response should contain groups of the reservations. |
| `Services` | bool | optional | Whether the response should contain services reserved by the reservations. |
| `Spaces` | bool | optional | Whether the response should contain spaces and space categories. |

#### Response

```json
{
    "BusinessSegments": null,
    "Customers": [
        {
            "Address": null,
            "BirthDate": null,
            "CategoryId": null,
            "Email": null,
            "FirstName": "John",
            "Gender": null,
            "Id": "35d4b117-4e60-44a3-9580-c582117eff98",
            "LanguageCode": null,
            "LastName": "Smith",
            "NationalityCode": "US",
            "Passport": null,
            "Phone": "00420123456789",
            "SecondLastName": null,
            "Title": null
        }
    ],
    "Items": null,
    "Products": null,
    "RateGroups": null,
    "Rates": null,
    "ReservationGroups": [
        {
            "Id": "c704dff3-7811-4af7-a3a0-7b2b0635ac59",
            "Name": "13-12-Smith-F712"
        }
    ],
    "Reservations": [
        {
            "AdultCount": 2,
            "AssignedSpaceId": "20e00c32-d561-4008-8609-82d8aa525714",
            "AssignedSpaceLocked": false,
            "BusinessSegmentId": null,
            "ChannelNumber": "1337614414",
            "ChannelManagerNumber": "01",
            "ChannelManagerGroupNumber": "JX8PA2",
            "ChannelManager": "AvailPro",
            "ChildCount": 0,
            "CompanionIds": [
                "35d4b117-4e60-44a3-9580-c582117eff98"
            ],
            "CompanyId": null,
            "CreatedUtc": "2016-02-20T14:58:02Z",
            "CustomerId": "35d4b117-4e60-44a3-9580-c582117eff98",
            "EndUtc": "2016-02-22T11:00:00Z",
            "GroupId": "94843f6f-3be3-481b-a1c7-06458774c3df",
            "Id": "bfee2c44-1f84-4326-a862-5289598f6e2d",
            "Number": "52",
            "RateId": "ed4b660b-19d0-434b-9360-a4de2ea42eda",
            "RequestedCategoryId": "773d5e42-de1e-43a0-9ce6-f940faf2303f",
            "ServiceId": "bd26d8db-86da-4f96-9efc-e5a4654a4a94",
            "StartUtc": "2016-02-20T13:00:00Z",
            "State": "Processed",
            "TravelAgencyId": null,
            "UpdatedUtc": "2016-02-20T14:58:02Z"
        }
    ],
    "Services": null,
    "SpaceCategories": null,
    "Spaces": null
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `BusinessSegments` | array of [Business Segment](#business-segment) | optional | Business segments of the reservations. |
| `Customers` | array of [Customer](#customer) | optional | Customers that are members of the reservations. |
| `Items` | array of [Accounting Item](#accounting-item) | optional | Revenue items of the reservations. |
| `Products` | array of [Product](#product) | optional | Products orderable with reservations. |
| `RateGroups` | array of [Rate Group](#rate-group) | optional | Rate groups of the reservation rates. |
| `Rates` | array of [Rate](#rate) | optional | Rates of the reservations. |
| `ReservationGroups` | array of [Reservation Group](#reservation-group) | optional | Reservation groups that the reservations are members of. |
| `Reservations` | array of [Reservation](#reservation) | optional | The reservations that collide with the specified interval. |
| `Services` | array of [Service](#service) | optional | Services that have been reserved. |
| `SpaceCategories` | array of [Space Category](#space-category) | optional | Space categories of the spaces. |
| `Spaces` | array of [Space](#space) | optional | Assigned spaces of the reservations. |


##### Reservation

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the reservation. |
| `ServiceId` | string | required | Unique identifier of the [Service](#service) that is reserved. |
| `GroupId` | string | required | Unique identifier of the [Reservation Group](#reservation-group). |
| `Number` | string | required | Confirmation number of the reservation in Mews. |
| `ChannelNumber` | string | optional | Number of the reservation within the Channel (i.e. OTA, GDS, CRS, etc) in case the reservation group originates there (e.g. Booking.com confirmation number). |
| `ChannelManagerNumber` | string | optional | Unique number of the reservation within the reservation group. |
| `ChannelManagerGroupNumber` | string | optional | Number of the reservation group within a Channel manager that transferred the reservation from Channel to Mews. |
| `ChannelManager` | string | optional | Name of the Channel manager (e.g. AvailPro, SiteMinder, TravelClick, etc). |
| `State` | string [Reservation State](#reservation-state) | required | State of the reservation. |
| `CreatedUtc` | string | required | Creation date and time of the reservation in UTC timezone in ISO 8601 format. |
| `UpdatedUtc` | string | required | Last update date and time of the reservation in UTC timezone in ISO 8601 format. |
| `StartUtc` | string | required | Start of the reservation (arrival) in UTC timezone in ISO 8601 format. |
| `EndUtc` | string | required | End of the reservation (departure) in UTC timezone in ISO 8601 format. |
| `RequestedCategoryId` | string | required | Identifier of the requested [Space Category](#space-category). |
| `AssignedSpaceId` | string | optional | Identifier of the assigned [Space](#space). |
| `AssignedSpaceLocked` | bool | required | Whether the reservation is locked in the assigned [Space](#space) and cannot be moved. |
| `BusinessSegmentId` | string | optional | Identifier of the reservation [Business Segment](#business-segment). |
| `CompanyId` | string | optional | Identifier of the [Company](#company) on behalf of which the reservation was made. |
| `TravelAgencyId` | string | optional | Identifier of the [Company](#company) that mediated the reservation. |
| `RateId` | string | required | Identifier of the reservation [Rate](#rate). |
| `AdultCount` | number | required | Count of adults the reservation was booked for. |
| `ChildCount` | number | required | Count of children the reservation was booked for. |
| `CustomerId` | string | required | Unique identifier of the [Customer](#customer) who owns the reservation. |
| `CompanionIds` | array of string | required | Unique identifiers of [Customer](#customer)s that will occupy the space. |

##### Reservation State

- `Enquired` - Confirmed neither by the customer or enterprise.
- `Requested` - Confirmed by the customer but not by the enterprise (waitlist).
- `Optional` - Confirmed by enterprise but not by the guest (the enterprise is holding space for the guest).
- `Confirmed` - Confirmed by both parties, before check-in.
- `Started` - Checked in.
- `Processed` - Checked out.
- `Canceled` - Canceled, not active anymore.

##### Reservation Group

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the reservation group. |
| `Name` | string | optional | Name of the reservation group, might be empty or same for multiple groups. |

### Get All Reservations By Ids

Returns all reservations with the specified unique identifiers.

#### Request `[PlatformAddress]/api/connector/v1/reservations/getAllByIds`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ReservationIds": [
        "2b6212d4-55d5-47ba-b8d2-da07be15bce9",
        "0e2983e9-5ac1-4fd9-9f76-76565c1a9b67"
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ReservationIds` | array of string | required | Unique identifiers of [Reservation](#reservation)s to be returned. |

#### Response

Same strucutre as in [Get All Reservations](#get-all-reservations) operation.

### Get All Reservation Items

Returns all revenue items associated with the specified reservations.

#### Request `[PlatformAddress]/api/connector/v1/reservations/getAllItems`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ReservationIds": [
        "e6ea708c-2a2a-412f-a152-b6c76ffad49b"
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ReservationIds` | array of string | required | Unique identifiers of the [Reservation](#reservation)s. |
| `Currency` | string | optional | ISO-4217 currency code the item costs should be converted to. |

#### Response

```json
{
    "Reservations": [
        {
            "Items": [
                {
                    "AccountingCategoryId": "0cf7aa90-736f-43e9-a7dc-787704548d86",
                    "Amount": {
                        "Currency": "GBP",
                        "Tax": 3.33,
                        "TaxRate": 0.2,
                        "Value": 20
                    },
                    "BillId": null,
                    "ConsumptionUtc": "2016-03-10T13:00:00Z",
                    "Id": "784a29df-6196-4402-96a0-58695a881239",
                    "Name": "Night 3/10/2016",
                    "Notes": null,
                    "OrderId": "e6ea708c-2a2a-412f-a152-b6c76ffad49b",
                    "ProductId": null,
                    "ServiceId": "bd26d8db-86da-4f96-9efc-e5a4654a4a94",
                    "Type": "ServiceRevenue"
                }
            ],
            "ReservationId": "e6ea708c-2a2a-412f-a152-b6c76ffad49b"
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Reservations` | array of [Reservation Items](#reservation-items) | required | The reservations with their items. |

##### Reservation Items

| Property | Type | | Description |
| --- | --- | --- | --- |
| `ReservationId` | string | required | Unique identifier of the [Reservation](#reservations). |
| `Items` | array of [Accounting Item](#accounting-item) | required | The items associated with the reservation. |

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
| `ReservationId` | string | required | Unique identifier of the [Reservation](#reservation) to start. |

#### Response

Empty object.

### Process Reservation

Marks a reservation as `Processed` (= checked out). Succeeds only if all processing conditions are met (the reservation has the `Started` state, balance of all reservation members is zero etc).

#### Request `[PlatformAddress]/api/connector/v1/reservations/process`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ReservationId": "e6ea708c-2a2a-412f-a152-b6c76ffad49b",
    "CloseBills": false
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ReservationId` | string | required | Unique identifier of the [Reservation](#reservation) to process. |
| `CloseBills` | bool | optional | Whether closable bills of the reservation members should be automatically closed. |

#### Response

Empty object.

### Cancel Reservation

Cancels a reservation. Succeeds only if the reservation is cancellable.

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
| `ReservationId` | string | required | Unique identifier of the [Reservation](#reservation) to cancel. |
| `ChargeCancellationFee` | boolean | required | Whether cancellation fees should be charged according to rate conditions. |
| `Notes` | string | required | Addiotional notes describing the cancellation. |

#### Response

Empty object.

### Update Reservation Interval

Updates reservation interval (start, end or both).

#### Request `[PlatformAddress]/api/connector/v1/reservations/updateInterval`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ReservationId": "209d984d-4985-4efb-96ec-f6591fc597bf",
    "StartUtc": "2017-08-12T15:00:00Z",
    "EndUtc": "2017-08-15T12:00:00Z",
    "ChargeCancellationFee": false
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ReservationId` | string | required | Unique identifier of the [Reservation](#reservation) to be updated. |
| `StartUtc` | string | optional | New reservation start in UTC timezone in ISO 8601 format. |
| `EndUtc` | string | optional | New reservation end in UTC timezone in ISO 8601 format. |
| `ChargeCancellationFee` | boolean | required | Whether cancellation fee should be charged for potentially cancelled nights. |

#### Response

Empty object.

### Update Reservation Space

Updates reservation allocation to space, e.g. to different room.

#### Request `[PlatformAddress]/api/connector/v1/reservations/updateSpace`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ReservationId": "e6ea708c-2a2a-412f-a152-b6c76ffad49b",
    "SpaceId": "5ee074b1-6c86-48e8-915f-c7aa4702086f"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ReservationId` | string | required | Unique identifier of the [Reservation](#reservation) to be reassigned. |
| `SpaceId` | string | required | Unique identifier of the [Space](#space) where the reservation should be assigned. |

#### Response

Empty object.

### Update Reservation Requested Category

Updates reservation category requested by the customer to a different one.

#### Request `[PlatformAddress]/api/connector/v1/reservations/updateRequestedCategory`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ReservationId": "e6ea708c-2a2a-412f-a152-b6c76ffad49b",
    "CategoryId": "773d5e42-de1e-43a0-9ce6-f940faf2303f",
    "Reprice": false
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ReservationId` | string | required | Unique identifier of the [Reservation](#reservation) to be updated. |
| `CategoryId` | string | required | Unique identifier of the [Space Category](#space-category). |
| `Reprice` | bool | required | Whether reservation should be repriced according to new category pricing. |

#### Response

Empty object.

### Add Reservation Companion

Adds a customer as a companion to the reservation. Succeeds only if there is space for the new companion (count of current companions is less than `AdultCount + ChildCount`).

#### Request `[PlatformAddress]/api/connector/v1/reservations/addCompanion`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ReservationId": "e6ea708c-2a2a-412f-a152-b6c76ffad49b",
    "CustomerId": "35d4b117-4e60-44a3-9580-c582117eff98"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ReservationId` | string | required | Unique identifier of the [Reservation](#reservation). |
| `CustomerId` | string | required | Unique identifier of the [Customer](#customer). |

#### Response

Empty object.

### Delete Reservation Companion

Removes customer companionship from the reservation. Note that the customer profile stays untouched, only the relation between the customer and reservation is deleted. 

#### Request `[PlatformAddress]/api/connector/v1/reservations/deleteCompanion`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ReservationId": "e6ea708c-2a2a-412f-a152-b6c76ffad49b",
    "CustomerId": "35d4b117-4e60-44a3-9580-c582117eff98"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ReservationId` | string | required | Unique identifier of the [Reservation](#reservation). |
| `CustomerId` | string | required | Unique identifier of the [Customer](#customer). |

#### Response

Empty object.

### Add Reservation Product

Adds a new product order of the specified product to the reservation.

#### Request `[PlatformAddress]/api/connector/v1/reservations/addProduct`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "ReservationId": "c2e37cec-11a0-4fe7-8467-fbc50b54722f",
    "ProductId": "3dc5d79b-67ce-48ed-9238-47fcf5d1a59f",
    "Count": 1
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `ReservationId` | string | required | Unique identifier of the [Reservation](#reservation). |
| `ProductId` | string | required | Unique identifier of the [Product](#product). |
| `Count` | int | required | The amount of the products to be added. Note that if the product is charged e.g. per night, count 1 means a single product every night. Count 2 means two products every night. |

#### Response

Empty object.

### Get All Customers

Returns all customers from the specified interval according to the time filter (e.g. customers created in that interval).

#### Request `[PlatformAddress]/api/connector/v1/customers/getAll`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "TimeFilter": "Created",
    "StartUtc": "2016-01-01T00:00:00Z",
    "EndUtc": "2016-01-07T00:00:00Z"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `TimeFilter` | string [Customer Time Filter](#customer-time-filter) | required | Time filter of the interval. |
| `StartUtc` | string | required | Start of the interval in UTC timezone in ISO 8601 format. |
| `EndUtc` | string | required | End of the interval in UTC timezone in ISO 8601 format. |

##### Customer Time Filter

- `Created` - customer created within the interval.
- `Updated` - customer updated within the interval.

#### Response

```json
{
    "Customers": [
        {
            "Address": null,
            "BirthDate": null,
            "BirthPlace": null,
            "CategoryId": null,
            "CreatedUtc": "2016-01-01T00:00:00Z",
            "Email": null,
            "FirstName": "John",
            "Gender": null,
            "Id": "35d4b117-4e60-44a3-9580-c582117eff98",
            "LanguageCode": null,
            "LastName": "Smith",
            "NationalityCode": "US",
            "Passport": null,
            "Phone": "00420123456789",
            "SecondLastName": null,
            "Title": null,
            "UpdatedUtc": "2016-01-01T00:00:00Z"
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Customers` | array of [Customer](#customer) | required | The customers. |

##### Customer

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the customer. |
| `FirstName` | string | optional | First name of the customer. |
| `LastName` | string | required | Last name of the customer. |
| `SecondLastName` | string | optional | Second last name of the customer. |
| `Title` | string [Title](#title) | optional | Title prefix of the customer. |
| `Gender` | string [Gender](#gender) | optional | Gender of the customer. |
| `NationalityCode` | string | optional | ISO 3166-1 alpha-2 country code (two letter country code) of the nationality. |
| `LanguageCode` | string | optional | Language and culture code of the customers preferred language. E.g. `en-US` or `fr-FR`. |
| `BirthDate` | string | optional | Date of birth in ISO 8601 format. |
| `BirthPlace` | string | optional | Place of birth. |
| `Email` | string | optional | Email address of the customer. |
| `Phone` | string | optional | Phone number of the customer (possibly mobile). |
| `CategoryId` | string | optional | Unique identifier of the customer category. |
| `Passport` | [Document](#document) | optional | Passport details of the customer. |
| `Address` | [Address](#address) | optional | Address of the customer. |
| `CreatedUtc` | string | required | Creation date and time of the customer in UTC timezone in ISO 8601 format. |
| `UpdatedUtc` | string | required | Last update date and time of the customer in UTC timezone in ISO 8601 format. |

##### Title

- `Mister`
- `Miss`
- `Misses`

##### Gender

- `Male`
- `Female`

##### Document

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Number` | string | optional | Number of the document (e.g. passport number). |
| `Issuance` | string | optional | Date of issuance in ISO 8601 format. |
| `Expiration` | string | optional | Expiration date in ISO 8601 format. |

##### Address

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Line1` | string | optional | First line of the address. |
| `Line2` | string | optional | Second line of the address. |
| `City` | string | optional | The city. |
| `PostalCode` | string | optional | Postal code. |
| `CountryCode` | string | optional | ISO 3166-1 alpha-2 country code (two letter country code). |

### Get All Customers By Ids

Returns all customers with the specified ids.

#### Request `[PlatformAddress]/api/connector/v1/customers/getAllByIds`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "CustomerIds": [
        "35d4b117-4e60-44a3-9580-c582117eff98"
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `CustomerIds` | array of string | optional | Identifiers of [Customer](#customer)s. |

#### Response

```json
{
    "Customers": [
        {
            "Address": null,
            "BirthDate": null,
            "BirthPlace": null,
            "CategoryId": null,
            "CreatedUtc": "2016-01-01T00:00:00Z",
            "Email": null,
            "FirstName": "John",
            "Gender": null,
            "Id": "35d4b117-4e60-44a3-9580-c582117eff98",
            "LanguageCode": null,
            "LastName": "Smith",
            "NationalityCode": "US",
            "Passport": null,
            "Phone": "00420123456789",
            "SecondLastName": null,
            "Title": null,
            "UpdatedUtc": "2016-01-01T00:00:00Z"
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Customers` | array of [Customer](#customer) | required | The customers. |

### Search Customers

Searches for customers that are active at the moment in the enterprise (e.g. companions of on checked-in reservations or paymasters).

#### Request `[PlatformAddress]/api/connector/v1/customers/search`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "Name": "Smith"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `Name` | string | optional | Name to search by (applies to first name, last name and full name). |
| `SpaceId` | string | optional | Identifier of [Space](#space) to search by (members of [Reservation](#reservation) assigned there will be returned). |

#### Response

```json
{
    "Customers": [
        {
            "Customer": {
                 "Address": null,
                "BirthDate": null,
                "BirthPlace": null,
                "CategoryId": null,
                "CreatedUtc": "2016-01-01T00:00:00Z",
                "Email": null,
                "FirstName": "John",
                "Gender": null,
                "Id": "35d4b117-4e60-44a3-9580-c582117eff98",
                "LanguageCode": null,
                "LastName": "Smith",
                "NationalityCode": "US",
                "Passport": null,
                "Phone": "00420123456789",
                "SecondLastName": null,
                "Title": null,
                "UpdatedUtc": "2016-01-01T00:00:00Z"
            },
            "Reservation": null
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Customers` | array of [Customer Search Result](#customer-search-result) | required | The customer search results. |

##### Customer Search Result

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Customer` | [Customer](#customer) | required | The found customer. |
| `Reservation` | [Reservation](#reservation) | optional | Reservation of the customer in case he currently stays in the enterprise. |

### Get Customers Open Items

Returns all open items of the specified customers, i.e. all unpaid items and all deposited payments. Sum of the open items is the balance of the customer. If the `Currency` is specified, costs of the items are converted to that currency.

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
| `CustomerIds` | array of string | required | Unique identifiers of the [Customer](#customer)s. |
| `Currency` | string | optional | ISO-4217 currency code the item costs should be converted to, e.g. `EUR` or `USD`. |

#### Response

```json
{
    "Customers": [
        {
            "CustomerId": "2a1a4315-7e6f-4131-af21-402cec59b8b9",
            "Items": [
                {
                    "AccountingCategoryId": "12345678-7e6f-4131-af21-402cec59b8b9",
                    "Amount": {
                        "Currency": "EUR",
                        "Tax": null,
                        "TaxRate": null,
                        "Value": -100
                    },
                    "BillId": null,
                    "ConsumptionUtc": "2016-05-25T15:56:54Z",
                    "Id": "79aa7645-fe3a-4e9e-9311-e11df4686fca",
                    "Name": "Cash Payment EUR",
                    "Notes": null,
                    "OrderId": null,
                    "ProductId": null,
                    "ServiceId": null,
                    "Type": "Payment"
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
| `CustomerId` | string | required | Unique identifier of the [Customer](#customer). |
| `Items` | array of [Accounting Item](#accounting-item) | required | The open items. |

### Add Customer

Adds a new customer to the system and returns details of the added customer. If a customer with the specified email already exists, nothing is added to the system and profile information of the existing customer, which are left intact, are returned.

When it comes to dates in the customer data (e.g. birth date or passport expiration), they are all represented as dates in UTC timezone with time set to 12:00. That ensures that the date won't change no matter the timezone it is converted to. Practically it would be too complicated to obtain the timezone (e.g. timezone of place of birth or timezone of passport issuance) if we wanted to represent exact date and time in UTC.

#### Request `[PlatformAddress]/api/connector/v1/customers/add`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "FirstName": "John",
    "LastName": "Doe",
    "SecondLastName": "the Second",
    "Title": "Mister",
    "NationalityCode": "US",
    "BirthDate": "2000-01-01",
    "BirthPlace": "Prague, Czech Republic",
    "Email": "john@doe.com",
    "Phone": "00420123456789"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `FirstName` | string | optional | First name of the customer. |
| `LastName` | string | required | Last name of the customer. |
| `SecondLastName` | string | optional | Second last name of the customer. |
| `Title` | string [Title](#title) | optional | Title prefix of the customer. |
| `NationalityCode` | string | optional | ISO 3166-1 alpha-2 country code (two letter country code) of the nationality. |
| `BirthDate` | string | optional | Date of birth in ISO 8601 format. |
| `BirthPlace` | string | optional | Place of birth. |
| `Email` | string | optional | Email address of the customer. |
| `Phone` | string | optional | Phone number of the customer (possibly mobile). |
| `Passport` | [Document](#document) | optional | Passport details of the customer. |
| `Address` | [Address](#address) | optional | Address of the customer. |

#### Response

The created [Customer](#customer) or an existing [Customer](#customer) with the specified email. 

### Update Customer

Updates personal information of a customer. Note that if any of the fields is left blank, it won't clear the field in Mews. The field will be left intact. In case of email update, the email will change in Mews only if there is no other customer profile in the hotel with such email. Otherwise the email is not updated and the customer in Mews is left without email.

#### Request `[PlatformAddress]/api/connector/v1/customers/update`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "CustomerId": "35d4b117-4e60-44a3-9580-c582117eff98",
    "FirstName": "John",
    "LastName": "Smith",
    "SecondLastName": "the Second",
    "Title": "Mister",
    "NationalityCode": "US",
    "BirthDate": "2000-01-01",
    "BirthPlace": "Prague, Czech Republic",
    "Email": "john.smith@gmail.com",
    "Phone": "00420123456789",
    "Passport": {
        "Number": "123456",
        "Expiration": "2020-01-01",
        "Issuance": "2016-01-01"
    }
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `CustomerId` | string | required | Unique identifier of the [Customer](#customer). |
| `FirstName` | string | optional | New first name. |
| `LastName` | string | optional | New last name. |
| `SecondLastName` | string | optional | New second last name. |
| `Title` | string [Title](#title) | optional | New title. |
| `BirthDate` | string | optional | New birth date in ISO 8601 format. |
| `BithPlace` | string | optional | New birth place. |
| `NationalityCode` | string | optional | ISO 3166-1 alpha-2 country code (two letter country code) of the new nationality. |
| `Email` | string | optional | New email address. |
| `Phone` | string | optional | New phone number. |
| `Passport` | [Document](#document) | optional | New passport details. |
| `Address` | [Address](#address) | optional | New address details. |

#### Response

The updated [Customer](#customer).

### Charge Customer

Charges a customer, i.e. creates a new order attached to his profile with the specified items. Only positive charges are allowed by default, in order to post negative charges (rebates), the connector integration has to be configured in Mews to allow it.

#### Request `[PlatformAddress]/api/connector/v1/customers/charge`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "CustomerId": "794dbb77-0a9a-4170-9fa9-62ea4bf2a56e",
    "ServiceId": "0f7f56db-b8b3-42b0-8b53-2df4c8a87997",
    "Items": [
        {
            "Name": "Beer",
            "UnitCount": 10,
            "UnitCost": {
                "Amount": 2.50,
                "Currency": "GBP",
                "Tax": 0.20
            },
            "Category": {
                "Code": "ABVG"
            }
        },
        {
            "Name": "Steak",
            "UnitCount": 1,
            "UnitCost": {
                "Amount": 12.8,
                "Currency": "GBP",
                "Tax": 0.05
            }
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `CustomerId` | string | required | Identifier of the [Customer](#customer) to be charged. |
| `ServiceId` | string | optional | Identifier of the [Service](#service) to be charged. |
| `Items` | array of [Charge Item](#charge-item) | required | Items of the charge. |
| `Notes` | string | optional | Additional notes of the charge. |

##### Charge Item

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Name` | string | required | Name of the item. |
| `UnitCount` | number | required | Count of units to be charged, e.g. 10 in case of 10 beers. |
| `UnitCost` | [Charge Cost](#charge-cost) | required | Unit cost, e.g. cost for one beer (note that total cost of the item is therefore `UnitCount` times `UnitCost`). |
| `Category` | [Accounting Category Parameters](#accounting-category-parameters) | optional | Category of the item. |

##### Charge Cost

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Amount` | decimal | required | Amount including tax. |
| `Currency` | string | required | ISO-4217 currency code, e.g. `EUR` or `USD`. |
| `Tax` | decimal | required | Tax rate, e.g. `0.21` in case of 21% tax rate.  |

#### Response

```json
{
    "ChargeId": "cdfd5caa-2868-411b-ba95-322e70035f1a"
}
```


| Property | Type | | Description |
| --- | --- | --- | --- |
| `ChargeId` | string | required | Unique identifier of the created charge. |

### Get All Accounting Categories

Returns all accounting categories of the enterprise associated with the connector integration.

#### Request `[PlatformAddress]/api/connector/v1/accountingCategories/getAll`

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
    "AccountingCategories": [
        {
            "Classification": "Accommodation",
            "Code": "345",
            "CostCenterCode": "2589",
            "ExternalCode": "3010",
            "Id": "0cf7aa90-736f-43e9-a7dc-787704548d86",
            "IsActive": true,
            "LedgerAccountCode": "311100",
            "Name": "Accommodation",
            "PostingAccountCode": "602020"
        },
        {
            "Classification": null,
            "Code": "100",
            "CostCenterCode": "2589",
            "ExternalCode": "ABVG",
            "Id": "0b9560fb-055d-47d3-a6d4-e579c44ca558",
            "IsActive": true,
            "LedgerAccountCode": "311100",
            "Name": "Alcoholic Beverage",
            "PostingAccountCode": "602020"
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccountingCategories` | array of [Accounting Category](#accounting-category) | required | Accounting categories of the enterprise. |

##### Accounting Category

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the category. |
| `IsActive` | boolean | required | Whether the accounting category is still active. |
| `Name` | string | required | Name of the category. |
| `Code` | string | optional | Code of the category within Mews. |
| `Classification` | string [Accounting Category Classification](#accounting-category-classification) | optional | Classification of the accounting category allowing cross-enterprise reporting. |
| `ExternalCode` | string | optional | Code of the category in external systems. |
| `LedgerAccountCode` | string | optional | Code of the ledger account (double entry accounting). |
| `PostingAccountCode` | string | optional | Code of the posting account (double entry accounting). |
| `CostCenterCode` | string | optional | Code of cost center. |

##### Accounting Category Classification

- `Accommodation`
- ...

### Get All Accounting Items

Returns all accounting items of the enterprise that were consumed (posted) or will be consumed within the specified interval. If the `Currency` is specified, costs of the items are converted to that currency.

#### Request `[PlatformAddress]/api/connector/v1/accountingItems/getAll`

```json
{
    "AccessToken": "C66EF7B239D24632943D115EDE9CB810-EA00F8FD8294692C940F6B5A8F9453D",
    "StartUtc": "2016-01-01T00:00:00Z",
    "EndUtc": "2017-01-01T00:00:00Z"
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccessToken` | string | required | Access token of the client application. |
| `StartUtc` | string | required | Start of the consumption interval in UTC timezone in ISO 8601 format. |
| `EndUtc` | string | required | End of the consumption interval in UTC timezone in ISO 8601 format. |
| `Currency` | string | optional | ISO-4217 currency code the item costs should be converted to, e.g. `EUR` or `USD`. |

#### Response

```json
{
    "AccountingItems": [
        {
            "AccountingCategoryId": "4ac8ce68-5732-4f1d-bf0d-e557072c926f",
            "Amount": {
                "Currency": "GBP",
                "Tax": 0.42,
                "TaxRate": 0.2,
                "Value": 2.5
            },
            "BillId": null,
            "ConsumptionUtc": "2016-07-27T12:48:39Z",
            "Id": "89b93f7c-5c63-4de2-bd17-ec5fee5e3120",
            "Name": "Caramel, Pepper & Chilli Popcorn",
            "Notes": null,
            "OrderId": "810b8c3a-d358-4378-84a9-534c830016fc",
            "ProductId": null,
            "ServiceId": "bd26d8db-86da-4f96-9efc-e5a4654a4a94",
            "Type": "ServiceRevenue"
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `AccountingItems` | array of [Accounting Item](#accounting-item) | required | The consumed accounting items. |

##### Accounting Item

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Id` | string | required | Unique identifier of the item. |
| `ProductId` | string | optional | Unique identifier of the [Product](#product). |
| `ServiceId` | string | optional | Unique identifier of the [Service](#service) the item belongs to. |
| `OrderId` | string | optional | Unique identifier of the order (or [Reservation](#reservation)) the item belongs to. |
| `BillId` | string | optional | Unique identifier of the bill the item is assigned to. |
| `AccountingCategoryId` | string | optional | Unique identifier of the [Accounting Category](#accounting-category) the item belongs to. |
| `Amount` | [Currency Value](#currency-value) | required | Amount the item costs, negative amount represents either rebate or a payment. |
| `Type` | string [Accounting Item Type](#accounting-item-type) | required | Type of the item. |
| `Name` | string | required | Name of the item. |
| `Notes` | string | optional | Additional notes. |
| `ConsumptionUtc` | string | required | Date and time of the item consumption in UTC timezone in ISO 8601 format. |

##### Accounting Item Type

- `ServiceRevenue`
- `ProductRevenue`
- `AdditionalRevenue`
- `Payment`

##### Currency Value

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Currency` | string | required | ISO-4217 currency code, e.g. `EUR` or `USD`. |
| `Value` | number | required | Amount in the currency (including tax if taxed). |
| `TaxRate` | number | optional | Tax rate in case the item is taxed (e.g. `0.21`). |
| `Tax` | number | optional | Tax value in case the item is taxed. |

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
| `CustomerId` | string | required | Unique identifier of the [Customer](#customer). |
| `BillId` | string | optional | Unique identifier of an open bill of the customer where to assign the payment. |
| `Amount` | [Currency Value](#currency-value) | required | Amount of the credit card payment. |
| `CreditCard` | [Credit Card](#credit-card) | required | Credit card details. |
| `Category` | [Accounting Category Parameters](#accounting-category-parameters) | optional | Accounting category to be assigned to the payment. |
| `ReceiptIdentifier` | string | optional | Identifier of the payment receipt. |
| `Notes` | string | optional | Additional payment notes. |

##### Credit Card

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Type` | string | required | Type of the credit card, one of: `Visa`, `MasterCard`, `Amex`, `Discover`, `DinersClub`, `Jcb`, `EnRoute`, `Maestro`, `UnionPay`. |
| `Number` | string | required | Obfuscated credit card number. At most first six digits and last four digits can be specified, the digits in between should be replaced with `*`. It is possible to provide even more obfuscated number or just last four digits. **Never provide full credit card number**. For example `411111******1111`. |
| `Expiration` | string | required | Expiration of the credit card in format `MM/YYYY`, e.g. `12/2016` or `04/2017`. |
| `Name` | string | required | Name of the card holder. |

##### Accounting Category Parameters

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Code` | string | required | Code of the accounting category in MEWS. |
| `Name` | string | optional | Name of the category, used if no category is matched using the code. |

#### Response

Empty object.

### Get All Commands

Returns all commands the are still active from the client application point of view. That means commands that are in either `Pending` or `Received` state.

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
| `State` | string [Command State](#command-state) | required | State of the command. |
| `CreatedUtc` | string | required | Creation date and time of the command. |
| `Creator` | [User](#user) | optional | Creator of the command. |
| `Device` | [Device](#device) | required | Device that the command should be executed on. |
| `Data` | object | optional | Data of the command depending on device type and command type. Details in the [devices](#devices) section. |

##### Command State

- `Pending` - Created in MEWS, but not yet received by the client application.
- `Received` - Received by the client application.
- `Processing` - Being processed by the client application.
- `Processed` - Successfully processed command.
- `Cancelled` - A command whose execution has been cancelled before (or during) processing.
- `Error` - A command whose execution or processing was terminated by an error.

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
| `Type` | string | optional | Type of the device. Details in the [devices](#devices) section. |

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
| `CommandId` | string | required | Identifier of the [Command](#command) to be updated. |
| `State` | string [Command State](#command-state) | required | New state of the command. |
| `Progress` | number | optional | Progress of the command processing. Only used if the `State` is `Processing`, otherwise ignored. |
| `Result` | object | optional | Result of the command depending on device type and command type. Only used if the `State` is `Processed`, otherwise ignored. Details in the [devices](#devices) section. |
| `Notes` | string | optional | Notes about command execution. Only used if the `State` is `Processed`, `Cancelled` or `Error`, otherwise ignored. |

#### Response

Empty object.

### Devices

#### Printers (device type `Printer`)

| Property | Type | | Description |
| --- | --- | --- | --- |
| `CopyCount` | number | required | Number of copies to be printed. |
| `FileType` | string | required | MIME type of the file to be printed (e.g. `application/pdf`). |
| `FileData` | string | required | Base64-encoded data of the file to be printed. |
| `PrinterName` | string | required | Name of the printer. |
| `PrinterDriverName` | string | required | Name of the printer driver. |
| `PrinterPortName` | string | required | Name of the printer port. |

#### Key Cutters (device type `KeyCutter`)

| Property | Type | | Description |
| --- | --- | --- | --- |
| `StartUtc` | string | required | Reservation start. |
| `EndUtc` | string | required | Reservation end. |
| `FirstName` | string | optional | First name of the reservation owner. |
| `LastName` | string | required | Last name of the reservation owner. |
| `KeyCutterId` | string | optional | Identifier of the key cutter. |
| `LockIds` | array of string | required | Identifiers of locks/rooms the key should open. |

#### VisiOnline Key Cutters (device type `VisiOnlineKeyCutter`)

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

## Websockets

For use cases, when polling would be too resource consuming or not enough "real-time", websockets should be used. After successful connection, the client will start receiving messages depending on configuration of the Connector integration in Commander.

### Endpoint `[PlatformAddress]/ws/connector?AccessToken=[AccessToken]`

Note that protocol of the `[PlatformAddress]` should be changed to `ws(s)://`. If `[PlatformAddress]` starts with `http://` then `ws://` should be used. If it starts with `https://` then `wss://` should be used. Access token of the client application should be used as `[AccessToken]` in the endpoint address. It is the same access token as in the API operations, for further details, consult the [Authorization](#authorization) section.

### Message

```json
{
    "Events": [
        {
            "Type": "DeviceCommand",
            "Id": "2391a3df-1c61-4131-b6f8-c85b4234adcb"
        }
    ]
}
```

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Events` | array of [Event](#event) | required | The events that happened during the connection. |

#### Event

| Property | Type | | Description |
| --- | --- | --- | --- |
| `Type` | string | required | Type of the event. |
| `Id` | string | optional | Unique identifier of entity depending on the event type. |

##### Device Command Events (event type `DeviceCommand`)

If the Connector integration is configured to handle commands for some devices, it will receive events whenever a device command is updated. So if a command to one of the devices is created or updated, the client will receive an event with identifier of that command.

## Use Cases

### Revenue Management System

Revenue management systems obtain information about reservations, revenue and pricing from MEWS. And based on the data they may recommend or directly update rate prices, give future revenue estimates, predict occupancy etc. In bigger hotels, there are might be more than 50k reservations in a year, so it is necessary to always limit the API calls in terms of potential data size, in order to avoid timeouts, network errors etc. A recommended approach, how to implement a RMS client is described below. Folowing these guidelines should ensure that both our servers and RMS clients are not unnecessiraly overutilized. 

#### Initial Data Pull

Performed once when setting up the connection, because the RMS needs to obtain historical data.
    
RMS should obtain the reservations in time-limitted batches using [Get All Reservations](#get-all-reservations) with [Reservation Time Filter](#reservation-time-filter) set to `Start`. Size of the batches depends on size of the hotel and its occupancy, but in general weekly batches are recommended and should work well even for big hotels (1000+ units). In order to get reservations e.g. in the past year, RMS should call [Get All Reservations](#get-all-reservations) sequentially 52 times (one call for each week in the past year). That would give RMS all reservations that have arrival within the past year. To obtain revenue items associated with reservations, `Items` should be set to `true` in the `Extent` parameter of the [Get All Reservations](#get-all-reservations) operation.

Sometimes the data obtained through the previous two steps are not sufficient enough for RMS. So additionally, RMS can pull e.g. business segments via [Get All Business Segments](#get-all-business-segments) or rates via [Get All Rates](#get-all-rates). Note that it is important to get the reservations and revenue first and the additional data later after that. If done the other way around, it might happen that RMS would receive a reservation with e.g. `RateId` which does not correspond to any rate that was pulled beforehand. Rates, business segments etc. are dynamic and hotel employee could create a new one and assign it to a reservation right before the reservation gets pulled to RMS.

#### Periodic Future Update

Performed periodically after the connection is set up so that RMS has future reservations and revenue up to date. Length of the period is not specified, but it is recommended to update the future data once or twice a day.

The workflow can be similar as during the initial data pull, just applied to future, not past. One can take advantage of the fact that reservations are usually booked a few weeks or months in advance. The further in future, the lower the occupancy, so the reservation batch length may increase with the distance to future from current date. E.g. weekly batches can be used only for the first three months of the future year when there is higher occupancy. And for the remaining 9 months, monthly batches would be sufficient. This would reduce the API call count from 52 to 21 (12 weekly batches + 9 monthly batches). 

#### Notes

When calculating occupancy, it is important to take hierarchy of spaces into account. For example if there is a reservation for whole dorm, it occupies the dorm but also all child spaces in the hierarchy (the beds). And vice versa, if there is a bed reservation, it occupies the bed but also all parent spaces (the dorm).

We consider a space occupied if there is a reservation colliding with interval 18:00 to 24:00 on that day. So e.g. reservation from 14:00 to 16:00 is not calculated towards occupancy.
    
## Changelog

#### 13th June 2017 21:00 UTC

- Introduced [Websockets](#websockets) and first use case for device command events.

#### 7th June 2017 11:00 UTC

- Deprecated `BirthDateUtc` on [Customer](#customer) and in [Update Customer](#update-customer) parameters. `BirthDate` without time specified should be used instead.
- Deprecated `IssuanceUtc` and `ExpirationUtc` on [Document](#document). `Issuance` and `Expiration` without time should be used instead.

#### 10th May 2017 23:00 UTC

- Added `ServiceId` to [Accounting Item](#accounting-item).

#### 3rd May 2017 21:30 UTC

- Added [Update Space State](#update-space-state) oepration.
- Added [Add Reservation Product](#add-reservation-product) operation.
- Extended [Product](#product) with `ShortName`.
- Extended [Reservation Time Filter](#reservation-time-filter) with `Cancelled` option.

#### 12th April 2017 22:30 UTC

- Extended [Get All Reservations](#get-all-reservations) operation with `Extent` parameter. This simplifies some of integrations, since now it is possible to fetch both reservations and their items at the same time. It is no longer necessary to first get all reservations and then obtain their items using [Get All Reservation Items](#get-all-reservation-items). Also clients that do not use reservation groups or reservation customers should specify the `Extent` to be only `Reservations` and nothing else in order to reduce unnecessary network traffic. On the other hand, if the client makes many successive calls to [Get All Reservations](#get-all-reservations) (e.g. week by week or month by month), it is not recommended to include things that do not vary into `Extent`. E.g. `Spaces` or `Rates` should still be fetched once, not together with every reservation fetch.
- Added `Overlapping` time filter to [Get All Reservations](#get-all-reservations) operation.
- Added [Get Configuration](#get-configuration) operation.
- Added [Update Reservation Space](#update-reservation-space) operation.
- Added [Update Reservation Requested Category](#update-reservation-requested-category) operation.

#### 22nd February 2017 22:00 UTC

- Extended [Update Customer](#update-customer) operation parameters with `Email`.
- Extended [Accounting Category](#accounting-category) with `Classification`.

#### 9th February 2017 00:00 UTC

- Added [Delete Reservation Companion](#delete-reservation-companion) operation.
- Added [Get All Customers](#get-all-customers) operation.
- Extended [Customer](#customer) with `CreatedUtc` and `UpdatedUtc`.

#### 26th January 2017 00:30 UTC

- Extended [Customer](#customer) with `BirthPlace` (affected update customer and add customer operations).
- Extended [Document](#document) with `Issuance`.

#### 18th January 2017 22:00 UTC

- Extended [Accounting category](#accounting-category) with `LedgerAccountCode`, `PostingAccountCode` and `CostCenterCode`.
- Extended [Customer](#customer) with `LanguageCode`.

#### 14th December 2016 21:50 UTC

- Added [Get All Customers By Ids](#get-all-customers-by-ids) operation.
- Extended [Reservation](#reservation) with `ChannelManagerNumber`, `ChannelManagerGroupNumber` and `ChannelManager`.
- Extended [Get All Products](#get-all-products) to return products of mulitple services at once.
- Extended [Accounting Category](#accounting-category) with `Code` and `ExternalCode`.

#### 22nd November 2016 23:15 UTC

- Added `Notes` to [Accounting Item](#accounting-item).
- Deprecated response of [Add Credit Card Payment](#add-credit-card-payment).

#### 15th November 2016 22:00 UTC

- Added `BaseRateId` and `ShortName` to [Rate](#rate).
- Added [Get All Reservations By Ids](#get-all-reservations-by-ids) operation.

#### 17th October 2016 23:00 UTC

- Removed the deprecated data fields and operations.
- Added `Start` and `End` [Reservation Time Filter](#reservation-time-filter).
- Added `ProductId`, `BillId` and `Type` to [Accounting Item](#accounting-item).
- Added `ServiceId` to [Reservation](#reservation).
- Added optional `BillId` parameter to [Add Credit Card Payment](#add-credit-card-payment).
- Added [Get All Services](#get-all-services) operation.
- Added [Get All Products](#get-all-products) operation.
- Added [Get Rate Pricing](#get-rate-pricing) operation.
- Generalized `Update Rate Base Price` to [Update Rate Price](#update-rate-price).

#### 1st September 2016 22:00 UTC

- Added operation [Update Rate Base Price](#update-rate-base-price) that allows e.g. revenue management systems to provide recommended rates to MEWS.
- Added operation [Get All Reservation Items](#get-all-reservation-items) that returns revenue items of selected reservations.
- Added `Currency` parameter to operations [Get Customers Open Items](#get-customers-open-items) and [Get All Accounting Items](#get-all-accounting-items).
- Deprecated operation [Get Customer Balance](#get-customer-balance). Operation [Get Customer Open Items](#get-customer-open-items) should be used instead, since it provides more complete information.
- Deprecated properties `Customer` and `Companions` on [Reservation](#reservation). `CustomerId` an `CompanionIds` should be used instead. The customer data are part of the result of [Get All Reservations](#get-all-reservations). This removes redundancy in the response data, especially in hostels where the customer is mostly the only companion and currently the customer data were twice in the result.
- We plan to make `Address` on [Customer](#customer) optional in order to reduce response sizes, in many cases the customers do not have address details filled in.
