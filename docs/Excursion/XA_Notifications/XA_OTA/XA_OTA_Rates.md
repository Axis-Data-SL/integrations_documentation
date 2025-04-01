# OTA Notifications – Excursion Rates

Partners that package our products with for example flights, transfers, rent-a-car, cruises, require to store and process price and availability information in their system, to be able to create the packages and distribute them. Searches for packages are supported by their systems.

Partners selling isolated products and/or “package on the fly” from multiple providers and using a shopping basket principle, search our system for price and availability in real-time normally. Each different search yields a different response and most partners using this model require limited price and availability information to be stored on their side, to be able to have an insight in available product and to be able to manage product with the destinations.

## Rateplan Notification messages

This message is the most complex element of the integration. It represents the full complexity of our contracting application and all functionality is translated into rateplan elements. It is unlikely that functionalities in our contracting system match the available functionality in the distribution system to the full extent. Conclusively the implemented result of the processed rateplans gives a „price idea“ and not a guaranteed booking value. When implementing the rateplans, our partners have to make decisions regarding elements that do not match in functionality. This might include ignoring offer conditions or entire offers and affects competitiveness at first presentation, but also drop-off percentages when a „quote“ is executed in the booking process.

- The only valid availability and invoicing amount is the amount returned in the realtime booking message OTA_HotelResRQ/RS (Hotel) or OTA_TourActivityBookRQ/RS (Excursion).
- For this reason, partners must never commit to fixed bookings at fixed selling prices, based on Notifications.
- Partners must never commit to fixed bookings at fixed selling prices, based on Searches.

The information provided in the rateplan is outdated as soon as it is generated per definition. Partner implementation, processing and updates in their distribution are not in our control. Conclusively no rights can be derived from the content of the rateplans. Your Agreement Annex specifically mentions this aspect and before go-live, the signed annex must be in possession of our HQ. Without a signed Agreement Annex, your IP’s will not be liberated to access our production environment.

## Purpose

Provide adequate price information and application rules to package holidays and to enable adequate product management, through insight of product available and the rates per arrival.

## Excursion – OTA_TourActivityAvailRS

Excursion price information is communicated to our business partners using the OTA_TourActivityAvailRS message; rates can be identified by the target attribute in the Root node with value “Rates”.
```xml
<OTA_TourActivityAvailRS Version="2016.2" Target="Rates">
```
Each message sends a collection of **_TourActivityInfo_** elements describing all possible prices for a given **_excursion-product-service_** for a certain period (say, 18 months).

Each **_TourActivityInfo_** contains **_rates_** for a specific product and service subject to certain **_booking rules_**, listing the rates for an specific participant rules (Adult , Child, Infant)


### Basic Info

BasicInfo node holds the combination of Excursion-Product-Service the rate applies, more than one TourActivityInfo element can be found for the same Excursion-Product-Service to specify specific brand rates and or pax policies.
```xml
<BasicInfo SupplierServiceCode="TIC" SupplierProductCode="ST" TourActivityID="PESLPA4GTA"/>
```
Service codes can be found in Excursion > Common > Excursion related codes

### Schedule

The information of the Service dates the rates are valid for are held in the Schedule section. One or more Detail elements can be found defining the date ranges the rates are applicable.
```xml
<Schedule>
    <Detail Start="2018-01-01" End="2018-06-30"/>
    <Detail Start="2018-07-01" End="2018-12-31"/>
</Schedule>
```
### Pricing and brands

Rate of the specific Excursion-Product-Service can be found under the Pricing section. This section specifies if the rate application is per pax (PerPerson) o per unit (PerGroup).
```xml
<Pricing>
    <Summary CurrencyCode="EUR" Amount="50.00">
        <PricingType>PerPerson</PricingType>
    </Summary>
</Pricing>
```
In case rate is specific per unit, total price must take into account the amount of units required to cover the pax group. Policies section cover the occupancy of the service.

I.E. if service is defined for 1 to 3 pax, price defined per unit and the total of pax in the booking is 4, then it is required to calculate 2 units.

Rates can be defined for specific partner brands, in case rates are specific for a group of brands, the pricing section will include the brands the rate applies to.
```xml
<Pricing>
    <Summary CurrencyCode="EUR" Amount="50.00">
        <PricingType>PerPerson</PricingType>
    </Summary>
    <Group GroupCode="BRAND1"/>
    <Group GroupCode="BRAND2"/>
</Pricing>
```
Calculation of the rate must take into account the brands in case it applies, a rate specified for a brand overrides the generic rate. I.E. If a rate with same conditions is defined for both specific BRAND1 and without brand (generic), calculation for BRAND1 should take the specific rate while other brands should take the generic one.

### Pickup points

Excursion services including a transfer service, will include the pickups the rate applies to, ticket services won’t include this info.

The hotel list is located in the PickupDropoff section in a TPA_Extension:
```xml
<PickupDropoff>
    <Schedule>
        <TPA_Extensions>
            <Stocks>
                <Stock>
                    <PickupDropoffs>
                        <PickupDropoff Code="74073" LocationName="Lux Mar Apartments" OtherInfo="Hotel" PickupInd="true"/>
                        …
                        <PickupDropoff Code="74074" LocationName="Tropical Gardens Apartments" OtherInfo="Hotel" PickupInd="true"/>
                    </PickupDropoffs>
                </Stock>
            </Stocks>
        </TPA_Extensions>
    </Schedule>
</PickupDropoff>
```
### Policies

For PerGroup based rates, MinParticipants and MaxParticipants ocupancy attributes defines the occupancy of the service. Rate applies to any booking request of a group in the range, booking requests for groups bigger than the max occupancy must be calculated as multiple services to cover the whole group.

For PerPax based rates, MaxParticipants and minParticipants defines the biggest group allowed in a booking.

Pax types the rate applies to, are expressed as age ranges. Services for specific pax type groups (Adult excursions, etc) can be identified using the policies, I.E. rates with policies for children will not be included in the notification.
```xml
<Policies>
    <Participant MinAge="13" MaxAge="99" MinParticipants="1" MaxParticipants="1"/>
</Policies>
```


[Go back to index](../)
