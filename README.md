﻿# REM-Cloud Message Bus API
[REM](https://www.garaio-rem.ch/) (Real Estate Management) is a suite of products and systems developed by GARAIO AG. The REM-Cloud Message Bus connects
these systems and enables other organisations to integrate with REM. The REM-Cloud Message Bus is a [RabbitMQ Message Bus](https://www.rabbitmq.com/).
RabbitMQ itself implements [AMQP](https://www.amqp.org/).
## Access
If you wish to access the REM-Cloud Message Bus please contact us on [garaio-rem@garaio.com](mailto:youraddress@ucsc.edu).

Once you're granted access, credentials and access details are sent to you. The access details contain:

Detail | Description
-|-
app-id | An app-id (Unique Application ID) (e.g: 'YourService'). The app-id is also the name of your queue from which you can retrieve your messages
userid / password | Login  credentials for RabbitMQ
exchange-name | The name of the exchange to which you can send messages

## Messages
### Headers
Unless specified otherwise all messages must specify at least the following AMQP header properties:

Property | Value | Example | Description
-|-|-|-
app-id| \<app-id> | 'YourService' or 'REMCustomerA'  | Uniquely identifies the sender of a message
content-type| application/json || Message content is formatted as JSON
message-id | \<app-id>-\<app-specific-uid>| 'YourService-7712897' | Uniquely identifies a message. The app specific uid is an alphanumeric, app wide unique key
timestamp | Unix timestamp | '1553245964' | A timestamp to indicate when the message was created

Apps may add additional properties as needed

### Events
Events are messages that can be received by multiple subscribers. The message body contains a json data structure (see details for each event below). Following messages are defined as events:

Type | Description
-|-
[Masterdata.Property.Created](#masterdatapropertycreated) | A new property has been created
[Masterdata.Property.Updated](#masterdatapropertyupdated) | Data associated to a property has changed; you get changed attributes only
[Masterdata.House.Created](#masterdatahousecreated) | A house has been created
[Masterdata.House.Updated](#masterdatahouseupdated) | Data associated to a house has changed; you get the reference plus all changed attributes
[Masterdata.House.Deleted](#masterdatahousedeleted) | The house was deleted
[Masterdata.Unit.Created](#masterdataunitcreated) | A rentable unit has been created
[Masterdata.Unit.Updated](#masterdataunitupdated) | Data associated to a rentable unit has changed; you get the reference plus all changed attributes
[Masterdata.Unit.Deleted](#masterdataunitdeleted) | The unit was deleted
|||
Rental.Unit.TenantMovedIn | Informs that the tenant started renting the unit; Spec / Implementation pending
Rental.Unit.TenantMovedOut | Informs that the tenant stopped renting the unit; Spec / Implementation pending

#### Masterdata.Property.Created
Field | Type | Content / Remarks
-|-|-
eventType | string | Masterdata.Property.Created
data | hash |
&nbsp;&nbsp;reference | string | unique identifier for the property
&nbsp;&nbsp;name | string |
&nbsp;&nbsp;zipCode | string |
&nbsp;&nbsp;city | string |
&nbsp;&nbsp;countryCode | string | ISO country code, eg 'CH'

##### Example

```json
{ "eventType": "Masterdata.Property.Created",
  "data": {
    "reference": "1234",
    "name": "my property",
    "zipCode": "3000",
    "city": "Bern",
    "countryCode": "CH"
  }
}
```
#### Masterdata.Property.Updated
Field | Type | Content / Remarks
-|-|-
eventType | string | Masterdata.Property.Updated
data | hash |
&nbsp;&nbsp;reference | string | unique identifier for the property
&nbsp;&nbsp;name | string |
&nbsp;&nbsp;zipCode | string |
&nbsp;&nbsp;city | string |
&nbsp;&nbsp;countryCode | string | ISO country code, eg 'CH'

##### Example

```json
{ "eventType": "Masterdata.Property.Updated",
  "data": {
    "reference": "1234",
    "name": "my property renamed"
  }
}
```

#### Masterdata.House.Created
Field | Type | Content / Remarks
-|-|-
eventType | string | Masterdata.House.Created
data | hash |
&nbsp;&nbsp;reference | string | unique identifier for the house; the first segment of the key is the property reference, eg '1234.01'
&nbsp;&nbsp;numberOfElevators | integer | might be null
&nbsp;&nbsp;numberOfFloorsAboveGround | integer | might be null
&nbsp;&nbsp;numberOfFloorsBelowGround | integer | might be null
&nbsp;&nbsp;egid | integer | [read about it](https://www.bfs.admin.ch/bfs/de/home/register/personenregister/registerharmonisierung/minimaler-inhalt-einwohnerregister/egid-ewid.html), might be null
&nbsp;&nbsp;street | string | street name including the house number where appropriate
&nbsp;&nbsp;zipCode | string |
&nbsp;&nbsp;city | string |
&nbsp;&nbsp;countryCode | string | ISO country code, eg 'CH'

##### Example

```json
{ "eventType": "Masterdata.House.Created",
  "data": {
    "reference": "1234.01",
    "numberOfElevators": null,
    "numberOfFloorsAboveGround": 3,
    "numberOfFloorsBelowGround": null,
    "egid": 123456,
    "street": "Bahnhofstrasse 23",
    "zipCode": "3000",
    "city": "Bern",
    "countryCode": "CH"
  }
}
```

#### Masterdata.House.Updated
Field | Type | Content / Remarks
-|-|-
eventType | string | Masterdata.House.Updated
data | hash |
&nbsp;&nbsp;reference | string | unique identifier for the house; the first segment of the key is the property reference, eg '1234.01'
&nbsp;&nbsp;numberOfElevators | integer | might be null
&nbsp;&nbsp;numberOfFloorsAboveGround | integer | might be null
&nbsp;&nbsp;numberOfFloorsBelowGround | integer | might be null
&nbsp;&nbsp;egid | integer | [read about it](https://www.bfs.admin.ch/bfs/de/home/register/personenregister/registerharmonisierung/minimaler-inhalt-einwohnerregister/egid-ewid.html), might be null
&nbsp;&nbsp;street | string | street name including the house number where appropriate
&nbsp;&nbsp;zipCode | string |
&nbsp;&nbsp;city | string |
&nbsp;&nbsp;countryCode | string | ISO country code, eg 'CH'

##### Example

```json
{ "eventType": "Masterdata.House.Updated",
  "data": {
    "reference": "1234.01",
    "street": "Bahnhofstrasse 23a"
  }
}
```

#### Masterdata.House.Deleted
Field | Type | Content / Remarks
-|-|-
eventType | string | Masterdata.House.Deleted
data | hash |
&nbsp;&nbsp;reference | string | unique identifier for the house; the first segment of the key is the property reference, eg '1234.01'

##### Example

```json
{ "eventType": "Masterdata.House.Deleted",
  "data": {
    "reference": "1234.01"
  }
}
```

#### Masterdata.Unit.Created
Field | Type | Content / Remarks
-|-|-
eventType | string | Masterdata.Unit.Created
data | hash |
&nbsp;&nbsp;reference | string | unique identifier for the unit; the first segment of the key is the property reference, the second is the house reference eg '1234.01.0001'
&nbsp;&nbsp;unitTypeCode | string | code to identify the unit type; the list of unit types is part of the initial load data
&nbsp;&nbsp;rooms | string | number of rooms as a decimal, eg "3.5"; might be null
&nbsp;&nbsp;ewid | integer | [read about it](https://www.bfs.admin.ch/bfs/de/home/register/personenregister/registerharmonisierung/minimaler-inhalt-einwohnerregister/egid-ewid.html), might be null
&nbsp;&nbsp;bfsId | string | [read about it](https://www.bfs.admin.ch/bfs/de/home/register/gebaeude-wohnungsregister/gebaeudeadressen.html), might be null

##### Example

```json
{ "eventType": "Masterdata.Unit.Created",
  "data": {
    "reference": "1234.01.0001",
    "unitTypeCode": "01",
    "rooms": "3.5",
    "ewid": 123456,
    "bfsId": "A654321",
  }
}
```

#### Masterdata.Unit.Updated
Field | Type | Content / Remarks
-|-|-
eventType | string | Masterdata.House.Updated
data | hash |
&nbsp;&nbsp;reference | string | unique identifier for the unit; the first segment of the key is the property reference, the second is the house reference eg '1234.01.0001'
&nbsp;&nbsp;unitTypeCode | string | code to identify the unit type; the list of unit types is part of the initial load data
&nbsp;&nbsp;rooms | string | number of rooms as a decimal, eg "3.5"; might be null
&nbsp;&nbsp;ewid | integer | [read about it](https://www.bfs.admin.ch/bfs/de/home/register/personenregister/registerharmonisierung/minimaler-inhalt-einwohnerregister/egid-ewid.html), might be null
&nbsp;&nbsp;bfsId | string | [read about it](https://www.bfs.admin.ch/bfs/de/home/register/gebaeude-wohnungsregister/gebaeudeadressen.html), might be null

##### Example

```json
{ "eventType": "Masterdata.Unit.Updated",
  "data": {
    "reference": "1234.01.0001",
    "rooms": "3.0"
  }
}
```

#### Masterdata.Unit.Deleted
Field | Type | Content / Remarks
-|-|-
eventType | string | Masterdata.House.Deleted
data | hash |
&nbsp;&nbsp;reference | string | unique identifier for the unit; the first segment of the key is the property reference, the second is the house reference eg '1234.01.0001'

##### Example

```json
{ "eventType": "Masterdata.Unit.Deleted",
  "data": {
    "reference": "1234.01.0001"
  }
}
```
