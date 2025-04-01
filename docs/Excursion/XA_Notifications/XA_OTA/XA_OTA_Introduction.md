# OTA Notifications – Excursion Introduction

This document covers the introduction and common information to all the messages realted to the OTA based notifications, needed for any “push” model integration based on the OTA format. All the basic definitions of the push model as well as the OTA definitions (XSD and WSDL) can be found in the Axisdata Integration Documentation. The other documents in this directory cover the specific messages (Allotment, Inventory and Rates)

The OTA format is defined and maintained by the Open Travel Alliance and is update twice per year with an “A” and “B” revision. The OTA specification can be downloaded from their website which also contains several very useful utilities are available, among which the OpenTravel Model Viewer (<https://opentravel.org/>).

## Notification schedule

Notifications can be scheduled to provide partners with updated information. AxisData and partner agree which number of months in advance is included in the notifications. A typical notification period would be 12 months or 18 months.

Notification types:

- Full notification – Inventories, rates and/or availabilities are transmitted for all properties and for the full notification period
- Delta notification:
- Inventory: upon change of any of the services in the OTA_HotelInvNotif message, including occupancy changes or amenity changes. The entire information for one property will be transmitted.
- Stock: upon status change of any day inside the notification period from open to close (or from close to open) an OTA_HotelAvailNotif (Hotel) or OTA_TourActivityAvailRS (Excursion) message will be transmitted, **only including the dates affected**.
- Rates: upon any contract change affecting rates like additional offers, corrections, an additional contract for a following season, a full OTA_HotelRateplanNotif (Hotel) or OTA_TourActivityAvailRS (Excursion) message will be transmitted for the entire notification period of the affected property.

To avoid repetitive generation, transmission and processing of the same data over and over again, **full notifications can be scheduled with a maximum frequency of once per week**, preferably with a lower frequency, up to once a month.

Delta notifications will be scheduled every 15 minutes between 09:00 and 22:59 h. and every hour from 23:00h to 08:59h CET.

## Basic data, content and amenities

Both OTA_HotelDescriptiveInfoRS and OTA_ProductDescriptiveInfoRS can be “pushed” to an FTP location. A full delivery in agreed languages can be scheduled for example on a monthly basis or may be requested to the integrations team upon demand.

A daily schedule with all information for hotels that have changed the day before (delta) will be installed when this option is chosen.

OTA_ProductDescriptiveInfoRS covering Excursions static data and NBC is available since 2018. In this message, the “textual description”, “room description” and “board description” will be multi-lingual as per agreed languages in multiple language specific sections, while the non-language sensitive information (facts and amenities) will be listed only once.

## Common Information

### Incorrect message

If a message is rejected during parsing or validation an OTA_ErrorRS is sent to the originator:
```xml
< OTA_ErrorRS xmlns:prefix_0="<http://www.opentravel.org/OTA/2003/05>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" ErrorCode="Unknown" ErrorMessage="Internal error" Version="0">
</ OTA_ErrorRS>
```

### Provider Identification

Mapping, notification and realtime messaging are now including the provider information in all the messages. This section will be included in a TPA_extension element under proper element for each message. I.E. OTA_HotelDescriptiveInfoRQ remains under each HotelDescriptiveContent. This extra information can be used for mapping purposes and future multi provider products.
```xml
<POS>
    <Source>
        <RequestorID ID_Context="AxisData" Type="47">
            <CompanyName Code="OTS" CompanyShortName="Open Travel Service AG"/>
        </RequestorID>
    </Source>
</POS>
```
### Packaging

The interface support two main package models, pre package and dynamic package.

### Pre Package

With the prepackage model, 2 or more services are offered together, services are grouped in two ways:

- A combination of two products (for example hotel and rent-a-car). Two different products are combined to a new room with an inventory Block code. But also different hotels can be combined to a round-trip package this way. Hotel code includes a 2 character extension, I.E. AR for Accomodation RentACar, AG for Accomodation Golf, etc

Car packages are notified as an additional Inventory Block Code (InvBlockCode) at room level. These car codes follow ACRISS estandart (<http://www.acriss.org/car-codes.asp>). Car codes can be found in the Appendix section

Car codes per hotel can be retrieved from the services section of the OTA_HotelDescriptiveInfo message using SUMMARY template (see OTA communication Model, mapping section). Prices and availability for these room + car packages are available inside the notification messaging.

- Hotel supplied extras are often offered as a different room code containing a compulsory extra. This is most commonly used for offers that include for example spa treatments. Disadvantage is poor visibility of room descriptions in the German market and dependency on room descriptions in the search results in other markets.