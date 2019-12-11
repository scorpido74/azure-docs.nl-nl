---
title: Samen vatting van uitbetaling commerciële Marketplace | Azure Marketplace
description: In de samen vatting van de betaling ziet u details over het geld dat u hebt behaald met uw aanbieding. U kunt ook zien wanneer u betalingen ontvangt en hoeveel u wordt betaald.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 0d05802c9d5d80f91913291d710b674369f0ff17
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980268"
---
# <a name="payout-reporting"></a>Rapportage voor betaling

In de **samen vatting van de uitbetaling** ziet u details over het geld dat u hebt behaald met micro soft. U kunt ook zien wanneer u betalingen ontvangt en hoeveel u wordt betaald.

Als u aanbiedingen in azure Marketplace verkoopt, ziet u ook informatie over geslaagde uitbetalingen in de **samen vatting**van de betaling. Voor meer informatie over Azure Marketplace-betaling raadpleegt u het [Microsoft Azure Marketplace deelname beleid](https://go.microsoft.com/fwlink/p/?LinkId=722436) en de [Microsoft Azure Marketplace Publisher-overeenkomst](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Om in aanmerking te komen voor toekenning, moet uw opbrengst de [betalings drempelwaarde](payment-thresholds-methods-timeframes.md) van $50 bereiken. Zie deze pagina voor meer informatie over de betalings drempelwaarde en Bekijk de [Microsoft Azure Marketplace Publisher-overeenkomst](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Uitbetalings rapport: verschil tussen Cloud Partner-portal en partner centrum](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Klant typen](#customer-types)
- [Relatie tussen uitbetaling en gebruik](#corelation-between-payout-and-usage)
- [Transactie geschiedenis downloaden](#transaction-history-download-export)
- [Vragen en ondersteuning voor facturering](#billing-questions-and-support)

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Uitbetalings rapport: verschil tussen Cloud Partner-portal en partner centrum

| | Cloud Partner-portal | Partnercentrum |
|---------|---------|---------|
| Koppelingen | https://cloudpartner.azure.com/ | https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory en https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| Navigatie | Uitbetalings rapportage die is opgenomen in Insights-uitbetaling | Uitbetalings rapportage die is opgenomen in Partner Center: het pictogram voor uitbetaling |
| Scope | <ul> <li>Trans actie per regel item is zichtbaar voor de verzameling die wordt uitgevoerd, verzameld en betaald </li> <li>Rapportage: geeft alle regel items weer zodra de aankoop order is gemaakt, inclusief de verzameling die wordt uitgevoerd en de facturering wordt uitgevoerd, en de verzamelings status en regel items die nog niet kunnen worden betaald. </li> </ul> | <ul> <li>Toont de regel items zodra deze als in aanmerking komende winst worden beschouwd.</li> <li>Klanten betalen eerst aan micro soft en vervolgens kunnen Isv's het rapport voor uitbetaling starten.</li> <li>In het uitbetalings rapport wordt de verzameling niet weer gegeven die wordt uitgevoerd en facturering wordt uitgevoerd.  </li> </ul>  |
| Trans actie niet gereed voor uitbetaling | Facturering wordt uitgevoerd | Volgende geschatte betaling: de status van de uitbetaling is in de niet-verwerkte staat.  |
| Status uitbetaling |  | Verwerkte <br> Het verdienen komt in aanmerking voor betaling. Deze status blijft van toepassing op een koel periode zoals gedefinieerd in de programma gids voor het prestatie programma. <br> <br> Verschijnen <br> Door de betaling gegenereerde interne beoordelingen die in behandeling zijn, voordat de betaling wordt verwerkt. <br> <br> Verzonden: <br> De betaling is naar uw bank verzonden. |

## <a name="customer-types"></a>Klant typen 

### <a name="enterprise-agreement"></a>Enter prise overeenkomst

Klanten zonder een Enterprise Agreement op locatie worden maandelijks gefactureerd voor software licenties voor Marketplace. Klanten met een Enterprise Agreement worden maandelijks gefactureerd via een factuur die elk kwar taal wordt gepresenteerd.

### <a name="credit-cards-and-monthly-invoice"></a>Credit cards en maandelijkse factuur

Klanten kunnen ook betalen met een credit card en een maandelijkse factuur. In dit geval worden uw software licentie kosten maandelijks in rekening gebracht.

### <a name="csp-and-direct-pay-users"></a>CSP-en direct betalen-gebruikers

Bijvoorbeeld als de klant inkopen met een credit card.

## <a name="corelation-between-payout-and-usage"></a>Relatie tussen uitbetaling en gebruik 

|Beschrijving    |    Datum  | Orders/gebruik  | Betaling |
|----------|----------|-----------|-------------|
|Bestel periode   | 15 augustus 2019-aug 30, 2019 | **Correlatie kenmerken orders** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Gebruik** <br> <ul> <li>CustomerId </li> <li>Klant naam</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Geschatte berekende toeslag <br> Geschatte uitbetaling (PC) </li> </ul> |  |
|Einde van de term (maand)   | 30 augustus 2019 | | |
|Factuur datum | 1 Sept 2019 | | |
|Betalings datum van klant | 1 Sept 2019 | | |
|Borg periode (alleen voor credit cards, 30 dagen) | Sept 1 september 2019-september 30, 2019 | | **Correlatie kenmerken orders:** <br> <ul><li>AssetId</li> <li>Klant-ID</li> <li> Klant naam</li> </ul> <br> **Gebruik** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Klant naam</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Uitbetalings status:** Verwerkte |
|Begin van verzamelings periode | 1 Sept 2019 | | |
|Einde van de verzamelings periode (Maxi maal 30 dagen) | Sept 30, 2019 | | |
|Berekenings datum van betaling (maandelijks op de 15e) | Okt 1, 2019 | | **Correlatie kenmerken** <br> <ul><li>AssetId</li> <li>Klant-ID</li> <li>Klant naam</li> </ul> <br> **Gebruik** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Klant naam</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Uitbetalings status:** Verschijnen |
|Uitbetalings datum | 15 oktober 2019 | | **Correlatie kenmerken** <br> <ul><li>AssetId</li> <li>Klant-ID</li> <li> Klant naam</li> </ul> <br> **Gebruik** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Klant naam</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Uitbetalings status:** Betaling verzonden |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Enter prise Agreement (kwartaal klanten/maandelijks)

| Beschrijving |    Datum  | Gebruik | Betaling |
|----------|----------|---------|-----------|
|Bestel periode | 15 augustus 2019-aug 30, 2019 | **Correlatie kenmerken orders** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Gebruiks rapport** <br> <ul> <li>CustomerId </li> <li>Klant naam</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Geschatte berekende toeslag <br> Geschatte uitbetaling (PC) </li> </ul> | |
|Einde van de term (kwar taal) | Sept 30, 2019 | | |
|Factuur datum | 15 oktober 2019 | | |
|Borg periode (alleen voor credit cards, 30 dagen) | n.v.t. | | |
|Begin van verzamelings periode | 15 oktober 2019 | | |
|Alleen credit cards, 30 dagen | 1 november 2019 tot 30 november 2019 | | |
|Einde van verzamelings periode (Maxi maal 90 dagen) | 15 januari 2020 | | |
|Betalings datum van klant | 30 dec, 2019 | | |
|Berekening van uitbetaling | 15 januari 2020 | | |
|Uitbetalings datum | 15 februari 2020 | | **Voor elk kwar taal op basis van klanten** <br> <br> **Rapport Orders** <br> <ul><li>AssetId</li> <li>Klant-ID</li> <li> Klant naam</li> </ul> <br> **Gebruik** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Klant naam</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Status van uitbetaling:** verzonden |

<!---
## Billing

Depending on the transaction option used, the publisher’s software license fees can be presented as follows:

* Free: No charge for software licenses.
* **Bring the own license (BYOL)**: Any applicable charges for software licenses are managed directly between the publisher and customer. Microsoft only passes through Azure infrastructure usage fees. (Virtual Machines and Azure Applications only).
* **Pay-as-you-go**: Software license fees are presented as a per-hour, per-core (VCPU) pricing rate based on the Azure infrastructure used. This only applies to Virtual Machines and Azure Applications. 
* **Subscription pricing**: Software license fees are presented as a monthly or annual recurring fee billed as a flat rate or per-seat. This only applies to SaaS Apps and Azure Applications - Managed Apps.

### Pay as you go

If you enable the Pay-As-You-Go option, then you have the following cost structure. 

* If you enable the Pay-As-You-Go option, then you have the following cost structure.

|Your license cost  | $1.00 per hour  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$1.14 per hour*       |

* In this scenario, Microsoft bills $1.14 per hour for use of your published VM image.

|Microsoft bills  | $1.14 per hour  |
|---------|---------|
|Microsoft pays you 80% of your license cost|   $0.80 per hour     |
|Microsoft keeps 20% of your license cost  |  $0.20 per hour       |
|Microsoft keeps 100% of the Azure usage cost | $0.14 per hour |

### Bring Your Own License (BYOL)

* If you enable the BYOL option, then you have the following cost structure.

|Your license cost  | License fee negotiated and billed by you  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$0.14 per hour*       |

* In this scenario, Microsoft bills $0.14 per hour for use of your published VM image.

|Microsoft bills  | $0.14 per hour  |
|---------|---------|
|Microsoft keeps the Azure usage cost    |   $0.14 per hour     |
|Microsoft keeps 0% of your license cost   |  $0.00 per hour       |

### SaaS App subscription

This option must be configured to sell through Microsoft and can be priced at a flat rate or per user on a monthly or annual basis.
•   If you enable the Sell through Microsoft option for a SaaS offer, then you have the following cost structure.

|Your license cost       | $100.00 per month  |
|--------------|---------|
|Azure usage cost (D1/1-Core)    | Billed directly to the publisher, not the customer |
|*Customer is billed by Microsoft*    |  *$100.00 per month (note: publisher must account for any incurred or pass-through infrastructure costs in the license fee)*  |

* In this scenario, Microsoft bills $100.00 for your software license and pays out $80.00 to the publisher.
* Partners who have qualified for the Reduced Marketplace Service Fee will see a reduced transaction fee on the SaaS offers from May 2019 until June 2020. In this scenario, Microsoft bills $100.00 for your software license and pays out $90.00 to the publisher.

|Microsoft bills  | $100.00 per month  |
|---------|---------|
|Microsoft pays you 80% of your license cost <br> \* Microsoft pays you 90% of your license cost for any qualified SaaS apps   |   $80.00 per month <br> \* $90.00 per month    |
|Microsoft keeps 20% of your license cost <br> \* Microsoft keeps 10% of your license cost for any qualified SaaS apps.  |  $20.00 per month <br> \* $10.00     |

**Reduced Marketplace Service Fee:** For certain SaaS Products that you publish on our Commercial Marketplace, Microsoft will reduce its Marketplace Service Fee from 20% (as described in the Microsoft Publisher Agreement) to 10%.  In order for your Product to qualify, at least one of your products must be designated by Microsoft as either IP co-sell ready or IP co-sell prioritized. To receive this reduced Marketplace Service Fee for the month, eligibility must be met at least five (5) business days before the end of the previous calendar month. Reduced Marketplace Service fee will not apply to VMs, Managed Apps or any other products made available through our Commercial Marketplace.  This Reduced Marketplace Service Fee will be available to qualified offers, with license charges collected by Microsoft between May 1, 2019 and June 30, 2020.  After that time, the Marketplace Service Fee will return to its normal amount.

### Customer invoicing, payment, billing, and collections

**Invoicing and payment**

Publisher can use the customer's preferred invoicing method to deliver subscription or PAYGO software license fees.

**Enterprise agreement** 

If the customer's preferred invoicing method is the Microsoft Enterprise Agreement, your software license fees will be billed using this invoicing method as an itemized cost, separate from any Azure-specific usage costs.

**Credit cards and monthly invoice** 

Customers can also pay using a credit card and a monthly invoice. In this case, your software license fees will be billed just like the Enterprise Agreement scenario, as an itemized cost, separate from any Azure-specific usage costs.



**Billing and collections** 

Publisher software license billing is presented using the customer selected method of invoicing and follows the invoicing timeline. Customers without an Enterprise Agreement in place are billed monthly for marketplace software licenses. Customers with an Enterprise Agreement are billed monthly via an invoice that is presented quarterly.

When subscription or Pay-as-You-Go pricing models are selected, Microsoft acts as the agent of the publisher and is responsible for all aspects of billing, payment, and collection.

### Publisher payout and reporting

* Any software licensing fees collected by Microsoft as an agent are subject to a 20% transaction fee unless otherwise specified and are deducted at the time of publisher payout.

* Customers typically purchase using the Enterprise Agreement or a credit-card enabled pay-as-you-go agreement. The agreement type determines billing, invoicing, collection, and payout timing.
--->

## <a name="transaction-history-download-export"></a>Transactie geschiedenis downloaden exporteren

Deze optie biedt een down load van elk item op de pagina met de transactie geschiedenis, het type, de datum, het bijbehorende transactie bedrag, de klant, het product en andere transactionele gegevens die van toepassing zijn op het prikkel programma. 

| Kolomnaam     | Beschrijving    | 
|-------------|-------------------------------|
| earningId                      | Unieke id voor elk verdienen                                                                                                       |
| participantId                  | De primaire identiteit van de partner die onder het programma is aangegaan                                                                            | 
| participantIdType              | Voornamelijk programma-ID voor prikkel Programma's en verkopers als voor Store-Program ma's en Azure Marketplace                                          | 
| deel nemer                | Naam van de verdienen partner                                                                                                              | 
| partnerCountryCode             | Locatie/land van de verdienen partner                                                                                                  |
| programName                    | Naam van het prestatie/archief programma                                                                                                             | 
| transactionId                  | De unieke id voor de trans actie                                                                                                    | 
| transactionCurrency            | Valuta waarin de oorspronkelijke klant transactie heeft plaatsgevonden (dit is niet de valuta van de partner locatie)                                     | 
| transactionDate                | De datum van de trans actie. Handig voor Program ma's waarbij veel trans acties bijdragen aan één verdienen                                           | 
| transactionExchangeRate        | De wissel koers die wordt gebruikt voor het weer geven van het bijbehorende bedrag voor de trans actie USD                                                                 | 
| transactionAmount              | Transactie bedrag in de oorspronkelijke transactie valuta op basis waarvan het verdienen is gegenereerd                                              | 
| transactionAmountUSD           | Transactie bedrag in USD                                                                                                                | 
| kern                          | Geeft bedrijfs regel voor het verdienen aan                                                                                                  | 
| earningRate                    | Prestatie percentage toegepast op transactie bedrag om een verdienen te genereren                                                                      | 
| quantity                       | Is afhankelijk van het programma. Hiermee wordt de gefactureerde hoeveelheid voor transactionele Program ma's aangegeven                                                            | 
| quantityType                   | Hiermee wordt het type van de hoeveelheid aangegeven, bijvoorbeeld: Gefactureerd aantal, MAU                                                                                     |
| earningType                    | Geeft aan of het kosten, kortingen, Coop, verkoop enz. is.                                                                                          | 
| earningAmount                  | Het verdienen van het bedrag in de oorspronkelijke transactie valuta                                                                                      |
| earningAmountUSD               | Verdienen bedrag in USD                                                                                                                    |
| earningDate                    | Datum van het verdienen                                                                                                                      |
| calculationDate                | De datum waarop het verdienen is berekend in het systeem                                                                                            |
| earningExchangeRate            | De wissel koers die wordt gebruikt om het overeenkomstige USD-bedrag weer te geven                                                                                  |
| exchangeRateDate               | Wisselkoers datum die wordt gebruikt voor het berekenen van EarningAmount USD                                                                                   | 
| paymentAmountWOTax             | Betaalde hoeveelheid (zonder BTW) voor de factuur voor ' verzonden ' betalingen                                                                 |
| paymentCurrency                | Betalen voor de valuta die wordt gekozen door de partner in het betalings profiel. Alleen weer gegeven voor verzonden betalingen                                                   |
| paymentExchangeRate            | De wissel koers die wordt gebruikt om paymentAmountWOTax in de betalings valuta te berekenen met behulp van ExchangeRateDate                                            |
| paymentId            | De unieke id voor de betaling. Dit nummer is zichtbaar in uw bank afschrift                                            |
| paymentStatus            | Betalings status                                            |
| paymentStatusDescription            | Beschrijvende beschrijving van de betalings status                                            |
| customerId                     | Is altijd leeg                                                                                                                     |
| customerName                   | Is altijd leeg                                                                                                                     |
| partNumber                     | Is altijd leeg                                                                                                                     |
| Product                    | Product naam gekoppeld aan trans actie                                                                                                       |
| productId                      | Unieke product-id                                                                                                                |
| parentProductId                | Unieke id van het bovenliggende product. Opmerking: als er geen bovenliggend product voor de trans actie is, dan is de product-id van het bovenliggende product ID. |
| parentProductName              | De naam van het bovenliggende product. Opmerking: als er geen bovenliggend product voor de trans actie is, dan is de naam van het bovenliggende product = product naam.   |
| productType                    | Type product (zoals app, invoeg toepassing, spelletje, enz.)                                                                                        |
| invoiceNumber                  | Factuur nummer (alleen van toepassing op EA)                                                                                                  |
| resellerId                     | Wederverkoper-id                                                                                                                      |
| Verkoperstatus                   | Reseller name                                                                                                                            |
| transactionType                | Type trans actie (zoals aankoop, restitutie, omkering, terugstorting, enz.)                                                               |
| localProviderSeller            | Lokale provider/verkoper van record                                                                                                          |
| taxRemitted                    | Het bedrag aan overgeschreven BTW (verkoop, gebruik of btw/GST-belastingen).                                                                                   |
| taxRemitModel                  | Partij verantwoordelijk voor het remitteren van belastingen (verkoop, gebruik of btw/GST-belastingen).                                                                    |
| storeFee                       | Het bedrag dat door micro soft wordt bewaard als een vergoeding voor het maken van de app of invoeg toepassing die beschikbaar is in de Store.                                            |
| transactionPaymentMethod       | Het betalings instrument van de klant dat wordt gebruikt voor de trans actie (zoals kaart, Mobile Carrier billing, PayPal, enzovoort)                                |
| tpan                           | Hiermee wordt het AD-netwerk van derden aangegeven                                                                                                     |
| customerCountry                | Klant land                                                                                                                         |
| customerCity                   | Klant plaats                                                                                                                            |
| customerState                  | Klant status                                                                                                                           |
| customerZip                    | Post code van de klant                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | De unieke id voor het programma                                                                                                        |
| externalReferenceIdLabel       | Label voor unieke id                                                                                                                  |
| transactionCountryCode       | Land code waarin trans actie plaatsvond                                                                                                                  |
| taxCountry       | Verkocht aan klant land                                                                                                                  |
| taxState       | Verkocht aan klant status                                                                                                                  |
| taxCity       | Verkocht aan klant plaats                                                                                                                  |
| taxZipCode       | Verkocht aan klant post                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Programma code       | Teken reeks die moet worden toegewezen aan de programma naam                                                                                                                   |
| earningAmountInLastPaymentCurrency       | Het verdienen van het bedrag in de laatste betalings valuta (veld is leeg als er geen eerdere betaling is betaald)                                                                                                                   |
| lastPaymentCurrency       | Valuta van laatste betaling (veld is leeg als er geen eerdere betaling is betaald)                                                                                                                   |
| AssetId       | De unieke id voor de klant orders voor uw Marketplace-service.  Het vertegenwoordigt de transactionele inkoop regel items. Er kunnen meerdere assets zijn.                                                                                                                   |
| OrderId       | is gekoppeld aan de factuur van een klant                                                                                                                   |
| LineItemId       | afzonderlijke regel in de factuur van een klant                                                                                                                   |
| Land/regio van de klant       | De land naam van de klant.  Dit kan afwijken van het land van het Azure-abonnement van de klant.                                                                                                                   |
| EmailAddress klant       | Het e-mail adres van de eind gebruiker.  Dit kan afwijken van het e-mail adres in het Azure-abonnement van een klant.                                                                                                                   |
| SkuId       | SKU-ID zoals gedefinieerd tijdens het publiceren. Een aanbieding kan veel Sku's hebben, maar een SKU kan slechts worden gekoppeld aan één aanbieding.                                                                                                                   |

>[!Note]
>Alle rapportage en inzichten voor de optie voor het publiceren van Transact zijn beschikbaar via de sectie inzichten van het gedeelte Cloud Partner-portal of Analytics van het partner centrum.

## <a name="billing-questions-and-support"></a>Vragen en ondersteuning voor facturering

Neem contact op met de [ondersteuning voor commerciële Marketplace-Uitgever](https://aka.ms/marketplacepublishersupport)om hulp te krijgen bij vragen over facturering.
