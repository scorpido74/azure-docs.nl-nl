---
title: Overzicht van de uitbetaling van commerciële marktplaatsen | Azure Marketplace
description: In het overzicht van de uitbetaling ziet u details over het geld dat u met uw aanbieding hebt verdiend. Het laat je ook weten wanneer je betalingen ontvangt en hoeveel je betaald krijgt.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 678dc8b058d0ae0694dafeb4222b2fc9f10ecda7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288696"
---
# <a name="payout-reporting"></a>Uitbetalingsrapportage

In [**het overzicht van de uitbetaling**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) ziet u details over het geld dat u met Microsoft hebt verdiend. Het laat je ook weten wanneer je betalingen ontvangt en hoeveel je betaald krijgt.

Als u aanbiedingen verkoopt in de Azure Marketplace, ziet u ook informatie over succesvolle uitbetalingen in het **uitbetalingsoverzicht**. Zie het [Microsoft Azure Marketplace-deelnamebeleid](https://go.microsoft.com/fwlink/p/?LinkId=722436) en de [Microsoft Azure Marketplace Publisher Agreement](https://go.microsoft.com/fwlink/p/?LinkID=699560)voor meer informatie over Azure Marketplace-betalingen.

> [!NOTE]
> Om in aanmerking te komen voor uitbetaling, moet je opbrengst de [betalingsdrempel](payment-thresholds-methods-timeframes.md) van $ 50 bereiken. Zie deze pagina voor meer informatie over de betalingsdrempel en bekijk de [Microsoft Azure Marketplace Publisher Agreement](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Rollen en toestemming om toegang te krijgen tot het uitbetalingsrapport](#roles-and-permission-to-access-the-payout-report)
- [Uitbetalingsrapport: verschil tussen Cloud Partner Portal en Partner Center](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Klanttypen](#customer-types)
- [Corelatie tussen uitbetaling en gebruik](#corelation-between-payout-and-usage)
- [Transactiegeschiedenis downloaden](#transaction-history-download-export)
- [Vragen en ondersteuning voor facturering](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Rollen en toestemming om toegang te krijgen tot het uitbetalingsrapport

| Rapporten/pagina's    | Accounteigenaar    | Manager  | Developer | Zakelijke bijdrager |  Financiële bijdrager | Marketeer |
|------------------|------------------|----------|-----------|----|----|-----|
| Acquisitierapport (inclusief near real-time-gegevens) | Kan bekijken | Kan bekijken | Geen toegang | Geen toegang | Kan bekijken | Geen toegang |
| Feedbackrapport/reacties | Feedback bekijken en verzenden | Feedback bekijken en verzenden | Feedback bekijken en verzenden | Geen toegang | Geen toegang | Feedback bekijken en verzenden |
| Gezondheidsrapport (inclusief bijna realtime gegevens) | Kan bekijken | Kan bekijken | Kan bekijken | Kan bekijken | Geen toegang | Geen toegang |
| Gebruiksrapport | Kan bekijken | Kan bekijken | Kan bekijken | Kan bekijken | Geen toegang | Geen toegang |
| Uitbetalingsrekening | Kan bijwerken | Geen toegang | Geen toegang | Geen toegang | Kan bijwerken | Geen toegang |
| Belastingprofiel | Kan bijwerken | Geen toegang | Geen toegang | Geen toegang | Kan bijwerken | Geen toegang |
| Betalingsoverzicht | Kan bekijken | Geen toegang | Geen toegang | Geen toegang | Kan bekijken | Geen toegang |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Uitbetalingsrapport: verschil tussen Cloud Partner Portal en Partner Center

| | Cloud Partner-portal | Partnercentrum |
|---------|---------|---------|
| Koppelingen | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory)En[https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Navigatie | Uitbetalingsrapportage in Insights-uitbetaling | Uitbetalingsrapportage in Partnercenter – Uitbetalingspictogram |
| Bereik | <ul> <li>Transactie per regelitem is zichtbaar voor het verzamelen in uitvoering, verzameld en betaald </li> <li>Rapportage : toont alle regelitems zodra de aankooporder is gemaakt, inclusief verzameling in uitvoering en facturering in uitvoering, en verzamelingsstatus en regelitems die nog niet in aanmerking komen om te worden betaald. </li> </ul> | <ul> <li>Hiermee worden de regelitems weergegeven zodra ze als in aanmerking komende inkomsten worden beschouwd.</li> <li>De klanten betalen eerst aan Microsoft en vervolgens kunnen ISV's het uitbetalingsrapport zien starten.</li> <li>In het uitbetalingsrapport wordt geen verzameling in uitvoering en lopende facturering weergegeven.  </li> </ul>  |
| Transactie nog niet klaar voor uitbetaling | Facturering in uitvoering | Volgende geschatte betaling: De uitbetalingsstatus is in de onverwerkte status.  |
| Uitbetalingsstatus |  | Onverwerkte: <br> Het verdienen komt in aanmerking voor betaling. Het blijft in deze staat voor een afkoelingsperiode zoals gedefinieerd in de programmagids voor het Incentive-programma. <br> <br> Komende: <br> Betalingsorder gegenereerd in afwachting van interne beoordelingen voordat de betaling wordt verwerkt. <br> <br> Verzonden: <br> De betaling is naar uw bank verzonden. |

## <a name="customer-types"></a>Klanttypen

### <a name="enterprise-agreement"></a>Ondernemingsovereenkomst

Klanten zonder enterprise-overeenkomst worden maandelijks gefactureerd voor marketplace-softwarelicenties. Klanten met een Enterprise Agreement worden maandelijks gefactureerd via een factuur die elk kwartaal wordt gepresenteerd.

### <a name="credit-cards-and-monthly-invoice"></a>Creditcards en maandelijkse factuur

Klanten kunnen ook betalen met een creditcard en een maandelijkse factuur. In dit geval worden uw softwarelicentiekosten maandelijks in rekening gebracht.

### <a name="csp-and-direct-pay-users"></a>CSP- en Direct Pay-gebruikers

Bijvoorbeeld als de klant koopt met een creditcard.

## <a name="corelation-between-payout-and-usage"></a>Corelatie tussen uitbetaling en gebruik

|Beschrijving    |    Date  | Bestellingen/gebruik  | Uitbetaling |
|----------|----------|-----------|-------------|
|Bestelperiode   | 15 augustus 2019 - 30 augustus 2019 | **Correlatiekenmerken Orders** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Gebruik** <br> <ul> <li>CustomerId </li> <li>Naam van de klant</li> <li>(UsageReference) AankoopRecordId/LineItemId</li> <li> Geschatte uitgebreide kosten <br> Geschatte uitbetaling (PC) </li> </ul> |  |
|Term eindigend (maand)   | 30 augustus 2019 | | |
|Factureringsdatum | 1 september 2019 | | |
|Betalingsdatum van klant | 1 september 2019 | | |
|Borgperiode (alleen creditcards, 30 dagen) | 1 september 2019 - 30 september 2019 | | **Correlatiekenmerken Orders:** <br> <ul><li>AssetId (AssetId)</li> <li>Klant-id</li> <li> Naam van de klant</li> </ul> <br> **Gebruik** <br> <ul> <li>AssetId (AssetId)</li> <li>CustomerId</li> <li>Naam van de klant</li> <li>OrderId</li> <li>LineItemId</li> <li>transactieBedrag</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Uitbetalingsstatus:** Onverwerkte |
|Beginperiode | 1 september 2019 | | |
|Einde van de inzamelperiode (maximaal 30 dagen) | 30 september 2019 | | |
|Uitbetalingsberekeningsdatum (maandelijks op de 15e) | 1 oktober 2019 | | **Correlatiekenmerken** <br> <ul><li>AssetId (AssetId)</li> <li>Klant-id</li> <li>Naam van de klant</li> </ul> <br> **Gebruik** <br> <ul> <li>AssetId (AssetId)</li> <li>CustomerId</li> <li>Naam van de klant</li> <li>OrderId</li> <li>LineItemId</li> <li>transactieBedrag</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Uitbetalingsstatus:** Komende |
|Uitbetalingsdatum | 15 okt 2019 | | **Correlatiekenmerken** <br> <ul><li>AssetId (AssetId)</li> <li>Klant-id</li> <li> Naam van de klant</li> </ul> <br> **Gebruik** <br> <ul> <li>AssetId (AssetId)</li> <li>CustomerId</li> <li>Naam van de klant</li> <li>OrderId</li> <li>LineItemId</li> <li>transactieBedrag</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Uitbetalingsstatus:** Betaling verzonden |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Enterprise-overeenkomst (kwartaal/maandklanten)

| Beschrijving |    Date  | Gebruik | Uitbetaling |
|----------|----------|---------|-----------|
|Bestelperiode | 15 augustus 2019 - 30 augustus 2019 | **Correlatiekenmerken orders** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Gebruiksrapport** <br> <ul> <li>CustomerId </li> <li>Naam van de klant</li> <li>(UsageReference) AankoopRecordId/LineItemId</li> <li> Geschatte uitgebreide kosten <br> Geschatte uitbetaling (PC) </li> </ul> | |
|Term Ending (kwartaal) | 30 september 2019 | | |
|Factureringsdatum | 15 okt 2019 | | |
|Borgperiode (alleen creditcards, 30 dagen) | N.v.t. | | |
|Beginperiode | 15 okt 2019 | | |
|Alleen creditcards, 30 dagen | 1 november 2019 - 30 november 2019 | | |
|Einde van de inzamelperiode (maximaal 90 dagen) | 15 jan 2020 | | |
|Betalingsdatum van klant | 30 december 2019 | | |
|Uitbetalingsberekening | 15 jan 2020 | | |
|Uitbetalingsdatum | 15 februari 2020 | | **Voor driemaandelijkse klanten** <br> <br> **Ordersrapport** <br> <ul><li>AssetId (AssetId)</li> <li>Klant-id</li> <li> Naam van de klant</li> </ul> <br> **Gebruik** <br> <ul> <li>AssetId (AssetId)</li> <li>CustomerId</li> <li>Naam van de klant</li> <li>OrderId</li> <li>LineItemId</li> <li>transactieBedrag</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Uitbetalingsstatus:** verzonden |

## <a name="transaction-history-download-export"></a>Export van transactiegeschiedenis downloaden

Deze optie biedt een download van elk verdienregelitem dat u ziet op de pagina Transactiegeschiedenis, verdientype, datum, gekoppeld transactiebedrag, klant, product en andere transactiegegevens die van toepassing zijn op het Incentives-programma.

| Kolomnaam     | Beschrijving    |
|-------------|-------------------------------|
| earningId                      | Unieke id voor elk verdienen                                                                                                       |
| deelnemerId                  | De primaire identiteit van de partner die in het kader van het programma verdient                                                                            |
| deelnemerIdType              | Meestal programma-ID voor incentiveprogramma's en verkoper ALS voor winkelprogramma's en Azure Marketplace                                          |
| deelnemerNaam                | Naam van de verdienpartner                                                                                                              |
| partnerCountryCode             | Locatie/land van de verdienpartner                                                                                                  |
| programmaNaam                    | Naam incentive/winkelprogramma                                                                                                             |
| transactionId                  | Unieke id voor de transactie                                                                                                    |
| transactieValuta            | Valuta waarin de oorspronkelijke klanttransactie heeft plaatsgevonden (het is niet de valuta van de partnerlocatie)                                     |
| transactieDatum                | Datum van de transactie. Handig voor programma's waar veel transacties bijdragen aan een verdienen                                           |
| transactieWisselkoers        | Wisselkoers die wordt gebruikt om het overeenkomstige transactie-USD-bedrag weer te geven                                                                 |
| transactieBedrag              | Transactiebedrag in de oorspronkelijke transactievaluta op basis waarvan het verdienen wordt gegenereerd                                              |
| transactieBedragUSD           | Transactiebedrag in USD                                                                                                                |
| hendel                          | Geeft bedrijfsregel voor het verdienen aan                                                                                                  |
| verdienenTarief                    | Incentivetarief toegepast op transactiebedrag om een                                                                      |
| quantity                       | Varieert op basis van het programma. Geeft gefactureerde hoeveelheid voor transactionele programma's aan                                                            |
| hoeveelheidType                   | Geeft type hoeveelheid aan, bijvoorbeeld: Gefactureerde hoeveelheid, MAU                                                                                     |
| verdienenType                    | Geeft aan of het vergoeding, korting, coop, verkopen etc.                                                                                          |
| verdienenBedrag                  | Geld verdienen in de oorspronkelijke transactievaluta                                                                                      |
| verdienenAmountUSD               | Verdienbedrag in USD                                                                                                                    |
| earningDate                    | Datum van het verdienen                                                                                                                      |
| berekeningDatum                | Datum waarop het verdienen in het systeem is berekend                                                                                            |
| verdienenWisselkoers            | Wisselkoers die wordt gebruikt om het overeenkomstige USD-bedrag weer te geven                                                                                  |
| exchangeRateDate               | Wisselkoersdatum die wordt gebruikt om het verdienbedrag USD te berekenen                                                                                   |
| betalingAmountWOTax             | Verdienbedrag (zonder belasting) in Pay To valuta voor alleen verzonden betalingen                                                                 |
| betalingValuta                | Betaal naar valuta die is gekozen door de partner in het betalingsprofiel. Alleen weergegeven voor verzonden betalingen                                                   |
| paymentExchangeRate            | Wisselkoers die wordt gebruikt om betalingAmountWOTax in betalingsvaluta te berekenen met ExchangeRateDate                                            |
| betalingId            | Unieke id voor de betaling. Dit nummer is zichtbaar in uw bankafschrift                                            |
| betalingsStatus            | Betalingsstatus                                            |
| paymentStatusBeschrijving            | Vriendelijke beschrijving van de betalingsstatus                                            |
| customerId                     | Zal altijd leeg zijn                                                                                                                     |
| customerName                   | Zal altijd leeg zijn                                                                                                                     |
| partNumber                     | Zal altijd leeg zijn                                                                                                                     |
| Productnaam                    | Productnaam gekoppeld aan transactie                                                                                                       |
| productId                      | Unieke product-id                                                                                                                |
| bovenliggendeProductId                | Unieke bovenliggende product-id. Opmerking: als er geen bovenliggend product voor de transactie is, dan bovenliggende product-id = product-id. |
| bovenliggendeProductName              | Naam van het bovenliggende product. Opmerking: als er geen bovenliggend product voor de transactie is, dan bovenliggende productnaam = productnaam.   |
| productType                    | Type product (zoals App, Add-on, Game, enz.)                                                                                        |
| factuurAantal                  | Factuurnummer (alleen van toepassing op EA)                                                                                                  |
| resellerId                     | Reseller-id                                                                                                                      |
| resellerName                   | Reseller name                                                                                                                            |
| transactieType                | Type transactie (zoals aankoop, terugbetaling, omkering, terugboeking, enz.)                                                               |
| localProviderSeller            | Lokale provider/verkoper van record                                                                                                          |
| taxRemitted                    | Bedrag van de kwijtgescholden belasting (verkoop, gebruik of BTW/GST-belastingen).                                                                                   |
| taxRemitModel                  | Partij die verantwoordelijk is voor het afdragen van belastingen (verkoop, gebruik of BTW/GST-belastingen).                                                                    |
| storeFee                       | Het bedrag dat Microsoft inhoudt als vergoeding voor het beschikbaar maken van de app of add-on in de Store.                                            |
| transactiePaymentMethod       | Klant betalingsinstrument gebruikt voor de transactie (zoals Card, Mobile Carrier Billing, PayPal, enz.)                                |
| tpan tpan                           | Geeft het advertentienetwerk van derden aan                                                                                                     |
| klantLand                | Klantland                                                                                                                         |
| customerCity                   | Klantstad                                                                                                                            |
| klantStaat                  | Klantstatus                                                                                                                           |
| customerZip                    | Postcode van de klant                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externReferentie-id            | Unieke id voor het programma                                                                                                        |
| externReferenceIdLabel       | Uniek id-label                                                                                                                  |
| transactionCountryCode       | Landcode waarin de transactie is gebeurd                                                                                                                  |
| taxCountry       | Verkocht aan klantland                                                                                                                  |
| taxState       | Verkocht aan de staat van de klant                                                                                                                  |
| taxCity       | Verkocht aan Customer City                                                                                                                  |
| taxZipCode       | Verkocht aan customer zip                                                                                                                  |
| Licentieprogrammanaam       |                                                                                                                   |
| Programmacode       | Tekenreeks om in kaart te brengen met de programmanaam                                                                                                                   |
| verdienenAmountInLastPaymentCurrency       | Verdienbedrag in de laatste betalingsvaluta (veld is leeg, als er geen betalingen zijn betaald)                                                                                                                   |
| laatstePaymentCurrency       | Laatste betalingsvaluta (veld is leeg, als er geen voorafgaande betaling is betaald)                                                                                                                   |
| AssetId (AssetId)       | De unieke id voor de klantorders voor uw marketplace-service.  Het vertegenwoordigt de afgehandelde aankoopregelitems. Er kunnen meerdere activa zijn.                                                                                                                   |
| OrderId       | betrekking heeft op de factuur van een klant                                                                                                                   |
| LineItemId       | individuele regel in de factuur van een klant                                                                                                                   |
| Land van klant       | De landnaam die door de klant wordt opgegeven.  Dit kan anders zijn dan het land in het Azure-abonnement van een klant.                                                                                                                   |
| E-mailadres van de klant       | Het e-mailadres van de eindklant.  Dit kan afwijken van het e-mailadres in het Azure-abonnement van een klant.                                                                                                                   |
| SkuId SkuId       | SKU-id zoals gedefinieerd tijdens het publiceren. Een aanbieding kan veel SKU's hebben, maar een SKU kan slechts aan één aanbieding worden gekoppeld.                                                                                                                   |

>[!Note]
>Alle rapportages en inzichten voor de transact publishing-optie zijn beschikbaar via de sectie Insights van de sectie Cloud Partner Portal of Analytics van partnercentrum.

## <a name="billing-questions-and-support"></a>Vragen en ondersteuning voor facturering

Neem contact op met de ondersteuning van [commerciële marktplaatsuitgevers](https://aka.ms/marketplacepublishersupport)om hulp te krijgen bij factureringsvragen.
