---
title: Details van het uitbetalings beleid-Azure Marketplace
description: Details over het uitzonderings beleid, waaronder schema's en rekoppeling.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: cb939d223d0aa91b6da62d3045ccad919f1bd277
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84887930"
---
# <a name="payout-policy-details"></a>Details van betaalbeleid

In dit artikel wordt het uitbetalings proces van micro soft beschreven, waar u de status van een uitbetaling en het terugkoppelings beleid kunt vinden.

## <a name="where-to-find-upcoming-payouts"></a>Waar vind ik aanstaande uitbetalingen?

In Partner Center selecteert u **uitbetaling** in de rechter bovenhoek van de portal:

![Illustreert het uitbetalings pictogram in de rechter bovenhoek van de portal van de partner centrum.](./media/payout-overview.png)

> [!TIP]
> Niet alle account rollen hebben toegang tot informatie over uitbetaling. Zie voor meer informatie [rollen en machtigingen voor toegang tot het rapport uitbetaling](./payout-summary-overview.md#roles-and-permissions).

## <a name="payment-schedules"></a>Betalings schema's

In de volgende secties wordt het proces voor uitbetaling beschreven.

### <a name="enterprise-agreement-transactions-after-may-1-2020"></a>Enterprise Agreement trans acties na 1 mei 2020

#### <a name="update-to-our-commercial-marketplace-publisher-payout-model"></a>Update naar ons uitbetalings model voor uitgevers van commerciële Marketplace

Vanaf 1 mei 2020 wordt ons beleid voor uitbetalingen bijgewerkt met betrekking tot producten die zijn gekocht in azure Marketplace of AppSource door klanten met een micro soft-Enterprise Agreement. Wanneer een klant een product koopt van Azure Marketplace of AppSource met behulp van hun bestaande micro soft-Enterprise Agreement voor trans acties na 1 mei 2020, gaan we uitbetalingen uitgeven in de volgende uitbetalings cyclus van 30 dagen na de klant factuur. Trans acties waarbij een klant gebruikmaakt van een credit card, zijn ongewijzigd en blijven een periode van 30 dagen hebben vóór de uitbetaling. Deze tabel bevat details over het schema voor uitbetaling.

> [!NOTE]
> Zie de onderstaande procedure voor het [verwerken van niet-betaalde klanten](#process-for-customer-non-payment) voor de acties die we ondernemen als de klant niet kan betalen, maar we al een uitbetaling aan u hebben verzonden.

| Gebeurtenis  | Date  | Zicht baarheid van partners: partner centrum uitbetalings rapport  |  Zicht baarheid van partners: Partner Center Analytics\* |
| --- | --- | --- | --- |
| Trans actie of gebruiks maand | 8/1/2020 – 8/31/2020 | N.v.t. | **Gebruiks rapport**: nieuw verbruik wordt weer gegeven (elke vier uur vernieuwd)<br>**Bestel rapport**: n.v.t. |
| Einde van de term (maand) | 8/31/2020 | N.v.t. | **Gebruiks rapport**: weer gegeven maand einde verbruik<br>**Bestel rapport**: n.v.t. |
| Order gegenereerd | 9/3/2020 – 9/7/2020 | N.v.t. | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
| Het verdienen van uitbetalingen berekenen | 9/4/2020 – 9/10/2020 | Gemarkeerd als niet- **verwerkt** in de transactie geschiedenis van het uitbetalings dashboard | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
| Maandelijkse uitbetaling | 10/5/2020 | Gemarkeerd als **gepland** in de transactie geschiedenis van het uitbetalings dashboard | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
| Uitbetalings datum | 10/15/2020 | Gemarkeerd als **verzonden** in transactie geschiedenis en in de sectie betalingen van het uitbetalings dashboard | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
| Klant factuur verzameld | 12/1/2020 | Gemarkeerd als **verzonden** in transactie geschiedenis en in de sectie betalingen van het uitbetalings dashboard | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief  |
|  |  |  |  |

\*Gebruiks-en order rapporten zijn toegankelijk in de sectie analyseren van het partner centrum.

### <a name="customers-who-pay-using-credit-card-or-invoice"></a>Klanten die betalen met een credit card of factuur

Voor alle aankopen met een credit card of een maandelijkse factuur geldt een periode van 30 dagen om ervoor te zorgen dat de fondsen worden gewist en er geen terugstortingen of verdachte fraude zijn.

| Gebeurtenis  | Date  | Zicht baarheid van partners: partner centrum uitbetalings rapport  |  Zicht baarheid van partners: Partner Center Analytics\*  |
| --- | --- | --- | --- |
| Trans actie of gebruiks maand | 8/1/2019 - 8/31/2019 | N.v.t. | **Gebruiks rapport**: nieuw verbruik wordt weer gegeven (elke vier uur vernieuwd)<br>**Bestel rapport**: n.v.t. |
| Einde van de term (maand) | 8/31/2019 | N.v.t. | **Gebruiks rapport**: weer gegeven maand einde verbruik<br>**Bestel rapport**: n.v.t. |
| Order gegenereerd | 9/3/2019 – 9/7/2019 | N.v.t. | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
| Klant factuur verzameld | 9/7/2019 – 9/10/2019 | N.v.t. | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
| Uitbetaling berekenen | 9/8/2019 -9/12/2019 | Gemarkeerd als niet- **verwerkt** in de transactie geschiedenis van het uitbetalings dashboard | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
| Maandelijkse uitbetaling | 11/5/2019\* | Gemarkeerd als **gepland** in de transactie geschiedenis van het uitbetalings dashboard | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
| Uitbetalings datum | 11/15/2019 | Gemarkeerd als **verzonden** in de transactie geschiedenis en in de sectie betalingen op het uitbetalings dashboard | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
|  |  |  |  |

\*Gebruiks-en order rapporten zijn toegankelijk in de sectie analyseren van het partner centrum.

### <a name="enterprise-agreement-transactions-prior-to-may-1-2020"></a>Enterprise Agreement trans acties vóór 1 mei 2020

Alle aankopen die vóór deze datum plaatsvinden, worden verwerkt en betaald volgens onderstaande planning, nadat micro soft de betaling van klanten heeft verzameld en de kosten voor Marketplace heeft verwerkt.

| Gebeurtenis  | Date  | Zicht baarheid van partners: partner centrum uitbetalings rapport  |  Zicht baarheid van partners: Partner Center Analytics\*  |
| --- | --- | --- | --- |
| Trans actie of gebruiks maand | 8/1/2019 – 8/31/2019 | N.v.t. | **Gebruiks rapport**: nieuw verbruik wordt weer gegeven (elke vier uur vernieuwd)<br>**Bestel rapport**: n.v.t. |
| Einde van de term (maand) | 8/31/2019 | N.v.t. | **Gebruiks rapport**: weer gegeven maand einde verbruik<br>**Bestel rapport**: n.v.t. |
| Order gegenereerd | 9/3/2019 – 9/7/2019 | N.v.t. | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
| Klant factuur verzameld | 12/1/2019 | N.v.t. | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
| Uitbetaling berekenen | 12/5/2019 – 12/7/2019 | Gemarkeerd als niet- **verwerkt** in de transactie geschiedenis van het uitbetalings dashboard | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
| Maandelijkse uitbetaling | 1/5/2019 | Gemarkeerd als **gepland** in de transactie geschiedenis van het uitbetalings dashboard | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
| Uitbetalings datum | 1/15/2019 | Gemarkeerd als **verzonden** in transactie geschiedenis en in de sectie betalingen op het uitbetalings dashboard | **Gebruiks rapport**: verbruik weer gegeven met OrderID/OrderLineItemID<br>**Bestel rapport**: klant orders weer gegeven als actief |
|  |  |  |  |

\*Gebruiks-en order rapporten zijn toegankelijk in de sectie analyseren van het partner centrum.

## <a name="process-for-customer-non-payment"></a>Proces voor niet-betaling van klant

In zeldzame gevallen kan micro soft geen betalingen van klanten verzamelen voor hun aankopen op de Commercial Marketplace. Wanneer een klant micro soft niet kan betalen volgens hun facturerings schema, beginnen we met het incasso proces. Dit proces duurt ongeveer vier maanden en houdt permanente communicatie van micro soft met zich mee. Als de betaling niet aan het einde van dit proces wordt ontvangen, schrijft micro soft de fondsen als niet-geïncasseerd.

Op basis van het uitbetalings proces dat hier is gegeleeerd, heeft micro soft mogelijk al fondsen betaald aan uitgevers (u) die uiteindelijk niet worden geïncasseerd. Daarom hebben we een proces voor het afstemmen van deze bedragen. Om ervoor te zorgen dat uw (al ontvangen) betaling kan worden afgestemd, ontvangt u een melding wanneer een klant zich in het incasso proces bevindt en worden de aankopen waarschijnlijk afgeschreven.

Micro soft brengt alle uitbetalingen die u aan u hebt betaald, aan met een van de volgende methoden: (1) micro soft kan de onbetaalde bedragen van toekomstige uitbetalingen aftrekken; Als bijvoorbeeld $1.000 in uitbetalingen als niet-verzamelend wordt beschouwd en wordt afgeschreven, worden uw toekomstige uitbetalingen Inge houden tot de $1.000 wordt hersteld, of (2) micro soft kan een restitutie of factuur uitgevers aanvragen voor alle niet-geïnde bedragen.

Hier volgt een voorbeeld schema:

| Gebeurtenis | Geschatte datum | Zicht baarheid partner |
| --- | --- | --- |
| Voor beeld van uitbetalings datum | 10/15/2020 | Gemarkeerd als **verzonden** in de sectie transactie geschiedenis en in betalingen in het uitbetalings dashboard |
| <font color="red">Als de klant micro soft niet betaalt</font> | 12/2/2020 – 12/5/2020 | Geen wijziging, hetzelfde als hierboven |
| Klant ontvangt eerste e-mail adres voor betaling | 12/6/2020 | Geen |
| De klant ontvangt regel matige e-mails met een oplopende urgentie | 12/7/2020 – 1/31/2021 | Geen |
| Publisher wordt op de hoogte gesteld van uitschrijving waarschijnlijk | 1/7/2021 | Er is een e-mail melding verzonden naar de uitgever dat de klant nog geen betaling heeft verzonden. De trans actie-ID en het dollar bedrag zijn opgenomen. |
| Opzeg ging van klant ontvangt | 2/1/2021 | Geen |
| Beëindiging van het verzamelings proces/fondsen zijn afgeschreven | 2/15/2021 | Er is een e-mail melding verzonden naar de uitgever die de fondsen afschrijven. De trans actie-ID en het dollar bedrag zijn opgenomen. |
| Uitbetaling wordt in mindering gebracht | 3/1/2021 | Er wordt een negatieve trans actie weer geven in de uitbetalings verklaring van partner Center |
| Uitbetaling wordt Inge houden | 3/15/2021 | Toekomstige uitbetalingen worden weer gegeven in de uitbetalings verklaring van partner Center. De betaling wordt pas ontvangen als het saldo niet langer negatief is.  |
|||

## <a name="next-step"></a>Volgende stap

- [Belastinginformatie](./tax-details-paid-transactions.md)
