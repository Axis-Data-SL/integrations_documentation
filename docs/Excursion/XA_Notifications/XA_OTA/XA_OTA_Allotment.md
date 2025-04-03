# OTA Notifications – Excursion Allotment

To communicate product availabilities to our partners we send a set of OTA_TourActivityAvailRS (Excursion) messages.

The purpose of this is to provide adequate availability information and application rules to package holidays and to enable adequate product management, through insight of product availability per arrival.

## Excursion – OTA_TourActivityAvailRS

Excursion availability information is communicated to our business partners using the OTA_TourActivityAvailRS message, availability can be identified by the target attribute in the Root node with value “Stock”.

```xml
<OTA_TourActivityAvailRS Version="2016.2" Target="Stock">
```

Each message sends a collection of **_TourActivityInfo_** elements describing availability for a given **_excursion-product-service_** for a certain period (say, 18 months).

Each **_TourActivityInfo_** contains **_availability_** for a specific product and service subject to certain **_booking rules_**, listing the availability for an specific participant rules (Adult , Child, Infant).

### Basic Info

BasicInfo node holds the combination of Excursion-Product-Service the availability applies, more than one TourActivityInfo element can be found for the same Excursion-Product-Service to specify conditions and or pax policies.
```xml
<BasicInfo SupplierServiceCode="TIC" SupplierProductCode="ST" TourActivityID="PESLPA4GTA"/>
```
Service codes can be found in the file Excursion > Common > Excursion related codes.

### Schedule

The information of the Service dates the service is available for are held in the Schedule section. One Detail element is include defining the date ranges the availability is applicable. Summary element holds the start time of the service
```xml
<Summary Start="09:15:00"/>

<Detail Start="2017-11-01" End="2018-04-30">
```
### Pickup points

All services including a pickup/dropoff from/to the hotel includes the list of available pickup/dropoff hotels. This information can be found in the TP_Extension section inside the Stock node. Any hotel included in the list is candidate for pickup/dropoff.
```xml
<PickupDropoffs>
    <PickupDropoff Code="70706" LocationName="Arenas del Mar Hotel" OtherInfo="Hotel" PickupInd="true"/>
    …
    <PickupDropoff Code="74815" LocationName="Grand Hotel Callao" OtherInfo="Hotel" PickupInd="true"/>
</PickupDropoffs>
```
This section also covers the specific allotment defined per pickup points. If there’s an allotment condition for one or more pickup points, a list of the pickup points will be included under that allotment definition.


### Availability patterns

Availability of each Service is communicated using three different concepts via a pattern system. Pattern string uses an specific amount of characters (PatternLength) to provide a value per day. Initial and final date of the pattern are indicated in the Detail section under Schedule node

In order to confirm an specific date is available, it must comply all three concepts (OperationTime, Release and Allotment)
```xml
<Detail Start="2018-11-01" End="2018-11-05">
…
<OperationTime Pattern="11111" PatternLength="1"/>
<Release Pattern="PT00HPT00HPT00HPT00HPT00H" PatternLength="5"/>
<Allotment Pattern="9999999999" PatternLength="2"/>
```
OperationTime: Operation time defines wheter the service is available or not. Any conditions preventing the service to be available (closeouts, day of week restrictions, etc) will mark the day as unavailable. Possible values 1 (available) 0 (unavailable). This concept uses a pattern of 1 character per value.

Release: defines the release value in XML period format (<https://www.w3schools.com/xml/schema_dtypes_date.asp>). Value can be expressed in hours, days, etc, pattern uses 5 characters per value

Allotment: Defines the amount of remaining units available. Pattern uses 2 characters per value, possible values:

- XX : Integer value, defines the remaining amount
- RQ : On request, service availability is on request
- 99 : 99 value is used to specify Free sales
- SS : Stop Sales

### Product deactivation

Excursion products that are deactivated will be communicated with a deactivation message. This message contains the product (TourActivityID) to deactivate and the dates this deactivation applies.

It uses the same structure as OTA_TourActivityAvailRS but with a small subset of sections to specify the TourActivityID and the status in the Stock section. In case Excursion is completely deactivated, no start-end dates will be included in the message.
```xml
<OTA_TourActivityAvailRS Version="2016.2" Target="Stock">
    <Success/>
    <TourActivityInfo>
        <BasicInfo TourActivityID="PESLPA4GTA"/>
        <Schedule>
            <Detail Start="2018-01-01" End="2018-12-31">
                <TPA_Extensions>
                    <Stock Type="Main" StockStatusType="Deactivated"/>
                </TPA_Extensions>
            </Detail>
        </Schedule>
    </TourActivityInfo>
</OTA_TourActivityAvailRS>
```

[Go back to index]({{ site.baseurl }}/)
