---
title: Overzicht van overzicht van uitbetalingen-Azure Marketplace
description: In de samen vatting van de betaling ziet u details over het geld dat u hebt behaald met uw aanbieding. U kunt ook zien wanneer u betalingen ontvangt en hoeveel u wordt betaald.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen
ms.author: mingshen
ms.date: 04/24/2020
ms.openlocfilehash: 29a8eb72814d865cb02bd632e084cec0cc06a1ff
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118474"
---
# <a name="payout-summary-overview"></a>Betalingsoverzicht

In de [samen vatting van de uitbetaling](./payout-summary.md) ziet u details over het geld dat u hebt behaald met micro soft. U kunt ook zien wanneer u betalingen ontvangt en hoeveel u wordt betaald.

Als u aanbiedingen in azure Marketplace verkoopt, ziet u ook informatie over geslaagde uitbetalingen in de samen vatting van de betaling. Zie [deelname beleid voor Azure Marketplace](https://docs.microsoft.com/legal/marketplace/participation-policy) en [Microsoft Azure Marketplace Publisher-overeenkomst](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)voor meer informatie over Azure Marketplace-betaling.

> [!NOTE]
> Om in aanmerking te komen voor toekenning, moet uw opbrengst de [betalings drempelwaarde](./payment-thresholds-methods-timeframes.md) van $50 bereiken. Zie de [Microsoft Azure Marketplace Publisher-overeenkomst](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)voor meer informatie over de betalings drempel.

Alle rapportage en inzichten voor de optie voor het publiceren van Transact zijn beschikbaar in de sectie analyse van partner Center, geopend met behulp van dit pictogram in de rechter bovenhoek van de portal:

![Illustreert het uitbetalings pictogram in de rechter bovenhoek van de portal van de partner centrum.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Rollen en machtigingen

Dit zijn rollen en machtigingen voor toegang tot het uitbetalings rapport:

| Rapporten/pagina's | Accounteigenaar | Manager | Ontwikkelaar | Zakelijke Inzender | Bijdrage financier | Verkoper |
| --- | --- | --- | --- | --- | --- | --- |
| Verwervings rapport (inclusief bijna realtime gegevens) | Kan weer geven | Kan weer geven | Geen toegang | Geen toegang | Kan weer geven | Geen toegang |
| Feedback rapport/reacties | Kan feedback weer geven en verzenden | Kan feedback weer geven en verzenden | Kan feedback weer geven en verzenden | Geen toegang | Geen toegang | Kan feedback weer geven en verzenden |
| --- | --- | --- | --- | --- | --- | --- |
| Status rapport (inclusief bijna realtime gegevens) | Kan weer geven | Kan weer geven | Kan weer geven | Kan weer geven | Geen toegang | Geen toegang |
| Gebruiks rapport | Kan weer geven | Kan weer geven | Kan weer geven | Kan weer geven | Geen toegang | Geen toegang |
| Uitbetalings account | Kan bijwerken | Geen toegang | Geen toegang | Geen toegang | Kan bijwerken | Geen toegang |
| BTW-profiel | Kan bijwerken | Geen toegang | Geen toegang | Geen toegang | Kan bijwerken | Geen toegang |
| Betalingsoverzicht | Kan weer geven | Geen toegang | Geen toegang | Geen toegang | Kan weer geven | Geen toegang  |
| | | | | | | |

## <a name="payout-report-differences"></a>Verschillen in uitbetalings rapport

Dit zijn de verschillen in het uitbetalings rapport tussen Cloud Partner-portal (oud) en partner centrum (nieuw):

| Cloud Partner-portal | Partner centrum |
| --- | --- |
| **Koppeling**:https://cloudpartner.azure.com/ | **Koppeling**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory enhttps://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigatie**: uitbetalings rapportage die is opgenomen in Insights-uitbetaling | **Navigatie**: uitbetalings rapportage die is opgenomen in het partner centrum-pictogram voor uitbetaling |
| **Bereik**:<ul><li>Trans actie per regel item is zichtbaar voor de verzameling die wordt uitgevoerd, verzameld en betaald.</li><li>Rapportage: geeft alle regel items weer zodra de aankoop order is gemaakt, inclusief de verzameling die wordt uitgevoerd en de facturering wordt uitgevoerd, en de verzamelings status en regel items die nog niet kunnen worden betaald.</li></ul> | **Bereik**:<ul><li>De regel items worden weer gegeven nadat deze als in aanmerking komende winst worden beschouwd.</li><li>Klanten betalen eerst aan micro soft en vervolgens kunnen Isv's het rapport voor uitbetaling starten.</li><li>De verzameling wordt niet weer gegeven in het uitbetalings rapport en de facturering wordt momenteel uitgevoerd.</li></ul> |
| **Trans actie niet gereed voor uitbetaling**: facturering wordt uitgevoerd | **Trans actie niet gereed voor uitbetaling**: volgende geschatte betaling: de status van de uitbetaling is in de niet-verwerkte staat. |
| **Status van uitbetaling**: n.v.t. | **Uitbetalings status**:<ul><li>Niet verwerkt: het verdienen komt in aanmerking voor betaling.</li><li>Binnenkort: het verdienen wordt in de volgende maandelijkse uitbetaling naar de uitgever verzonden.</li><li>Verzonden: de betaling is naar uw bank verzonden.</li></ul> |
| | |

## <a name="payment-schedules"></a>Betalings schema's

Raadpleeg de sectie [betalings schema's](./payout-policy-details.md#payment-schedules) van het onderwerp **Details** van uitbetaling voor een bespreking van betalings schema's, waaronder bedrijfs perioden, inzicht in partners en wanneer de klant een credit card of factuur gebruikt.

## <a name="transaction-history-download-export"></a>Transactie geschiedenis downloaden exporteren

Deze optie biedt een down load van elk verdienen regel item dat u op de pagina transactie geschiedenis ziet. Dit omvat het type, de datum, de bijbehorende transactie hoeveelheid, de klant, het product en andere transactionele gegevens met betrekking tot het prikkel programma.

| Kolomnaam | Description |
| --- | --- |
| earningId | Unieke id voor elk verdienen |
| participantId | De primaire identiteit van de partner die onder het programma is aangegaan |
| participantIdType | Programma-ID voor prikkel Programma's en verkopers als het programma is voor opslag Programma's en Azure Marketplace |
| deel nemer | Naam van de verdienen partner |
| partnerCountryCode | Locatie/land/regio van de verdienen partner |
| programName | Naam van het prestatie/archief programma |
| transactionId | De unieke id voor de trans actie |
| transactionCurrency | Valuta waarin de oorspronkelijke klant transactie heeft plaatsgevonden (dit is niet de valuta van de partner locatie) |
| transactionDate | De datum van de trans actie. Handig voor Program ma's waarbij veel trans acties bijdragen aan één verdienen |
| transactionExchangeRate | De wissel koers die wordt gebruikt voor het weer geven van het bijbehorende bedrag voor de trans actie USD |
| transactionAmount | Transactie bedrag in de oorspronkelijke transactie valuta op basis waarvan het verdienen is gegenereerd |
| transactionAmountUSD | Transactie bedrag in Amerikaanse dollars (USD) |
| kern | Bedrijfs regel voor het verdienen |
| earningRate | Prestatie percentage toegepast op transactie bedrag om een verdienen te genereren |
| quantity | Gefactureerd aantal voor transactionele Program ma's. Varieert op basis van programma |
| quantityType | Type van aantal, bijvoorbeeld: Gefactureerd aantal, maandelijks actieve gebruikers (MAU) |
| earningType | Hiermee wordt aangegeven of het om kosten, kortingen, Coop, verkopen, enzovoort gaat |
| earningAmount | Het verdienen van het bedrag in de oorspronkelijke transactie valuta |
| earningAmountUSD | Verdienen bedrag in USD |
| earningDate | Datum van het verdienen |
| calculationDate | De datum waarop het verdienen is berekend in het systeem |
| earningExchangeRate | De wissel koers die wordt gebruikt om het overeenkomstige USD-bedrag weer te geven |
| exchangeRateDate | Wisselkoers datum die wordt gebruikt voor het berekenen van EarningAmount USD |
| paymentAmountWOTax | Betaalde hoeveelheid (zonder BTW) voor de valuta voor &quot; verzonden &quot; betalingen |
| paymentCurrency | Betalen voor de valuta die wordt gekozen door de partner in het betalings profiel. Alleen weer gegeven voor verzonden betalingen |
| paymentExchangeRate | De wissel koers die wordt gebruikt om paymentAmountWOTax in de betalings valuta te berekenen met behulp van ExchangeRateDate |
| paymentId | De unieke id voor de betaling. Dit nummer is zichtbaar in uw bank afschrift |
| paymentStatus | Betalingsstatus |
| paymentStatusDescription | Beschrijving van de betalings status |
| customerId | Is altijd leeg |
| customerName | Is altijd leeg |
| partNumber | Is altijd leeg |
| Product | Product naam die aan de trans actie is gekoppeld |
| productId | Unieke product-id |
| parentProductId | Unieke id van het bovenliggende product. Als er geen bovenliggend product voor de trans actie is, dan is de product-id van de bovenliggende product-id. |
| parentProductName | De naam van het bovenliggende product. Als er geen bovenliggend product voor de trans actie is, dan is de naam van het bovenliggende product = product naam. |
| productType | Type product (zoals app, invoeg toepassing en spel) |
| invoiceNumber | Factuur nummer (alleen voor Enter prise Agreements) |
| resellerId | Wederverkoper-id |
| Verkoperstatus | Reseller name |
| transactionType | Type trans actie (zoals aankoop, restitutie, omkering en terugstorting) |
| localProviderSeller | Lokale provider/verkoper van record |
| taxRemitted | Het bedrag aan overgeschreven BTW (verkoop, gebruik of btw/GST-belastingen). |
| taxRemitModel | Partij verantwoordelijk voor het remitteren van belastingen (verkoop, gebruik of btw/GST-belastingen). |
| storeFee | Het bedrag dat door micro soft wordt bewaard als een vergoeding voor het maken van de app of invoeg toepassing die beschikbaar is in de commerciële Marketplace. |
| transactionPaymentMethod | Het betalings instrument van de klant dat wordt gebruikt voor de trans actie (zoals kaart, Mobile provider-facturering en PayPal) |
| tpan | AD-netwerk van derden |
| customerCountry | Land/regio van klant |
| customerCity | Klant plaats |
| customerState | Klant status |
| customerZip | Post code van de klant |
| TenantID | De ID van de Tenant |
| externalReferenceId | De unieke id voor het programma |
| externalReferenceIdLabel | Label voor unieke id |
| transactionCountryCode | Land-/regiocode waarin de trans actie plaatsvond |
| taxCountry | Land/regio van de klant |
| taxState | Provincie van de klant |
| taxCity | Plaats van klant |
| taxZipCode | Post code van de klant |
| LicensingProgramName | Naam van het licentie programma |
| Programma code | Teken reeks die moet worden toegewezen aan de programma naam |
| earningAmountInLastPaymentCurrency | Het verdienen van het bedrag in de laatste betalings valuta (veld is leeg als er geen eerdere betaling is betaald) |
| lastPaymentCurrency | De laatste betalings valuta (veld is leeg als er geen eerdere betaling is betaald) |
| AssetId | De unieke id voor de klant orders voor uw Marketplace-service. Het vertegenwoordigt de inkoop regel items. Er kunnen meerdere assets zijn. |
| OrderId | Is gekoppeld aan de factuur van een klant |
| LineItemId | Afzonderlijke regel in de factuur van een klant |
| Land/regio van klant | De land/regio naam die door de klant wordt verschaft. Dit kan afwijken van het land/de regio in het Azure-abonnement van een klant. |
| EmailAddress klant | Het e-mail adres van de klant. Dit kan afwijken van het e-mail adres in het Azure-abonnement van een klant. |
| SkuId | SKU-ID zoals gedefinieerd tijdens het publiceren. Een aanbieding kan veel Sku's hebben, maar een SKU kan slechts worden gekoppeld aan één aanbieding. |

> [!NOTE]
> Alle rapportage en inzichten voor de optie voor het publiceren van transacts vindt u in de sectie analyse van partner centrum.

## <a name="billing-questions-and-support"></a>Vragen en ondersteuning voor facturering
Neem contact op met de ondersteuning voor commerciële Marketplace- [Uitgever](https://partner.microsoft.com/support/v2/?stage=1)voor ondersteuning bij facturering.

## <a name="next-step"></a>Volgende stap

- [Betalingsoverzichten](./payout-summary.md)
