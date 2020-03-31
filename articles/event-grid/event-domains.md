---
title: Gebeurtenisdomeinen in Azure-gebeurtenisraster
description: In dit artikel wordt beschreven hoe u gebeurtenisdomeinen gebruiken om de stroom van aangepaste gebeurtenissen naar uw verschillende bedrijfsorganisaties, klanten of toepassingen te beheren.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: f6698f91d7659f9fc2c314a9291380301146f8ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898870"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Inzicht in gebeurtenisdomeinen voor het beheren van gebeurtenisrasteronderwerpen

In dit artikel wordt beschreven hoe u gebeurtenisdomeinen gebruiken om de stroom van aangepaste gebeurtenissen naar uw verschillende bedrijfsorganisaties, klanten of toepassingen te beheren. Gebruik gebeurtenisdomeinen om:

* Beheer multitenant eventingarchitecturen op schaal.
* Beheer uw autorisatie en verificatie.
* Partitie van uw onderwerpen zonder het beheer van elk afzonderlijk.
* Vermijd het individueel publiceren naar elk van uw onderwerpeindpunten.

## <a name="event-domain-overview"></a>Overzicht van gebeurtenisdomein

Een gebeurtenisdomein is een beheertool voor grote aantallen gebeurtenisrasteronderwerpen die verband houden met dezelfde toepassing. Je het zien als een meta-topic dat duizenden individuele onderwerpen kan hebben.

Gebeurtenisdomeinen maken u dezelfde architectuur beschikbaar die wordt gebruikt door Azure-services (zoals Storage en IoT Hub) om hun gebeurtenissen te publiceren. Hiermee u gebeurtenissen publiceren naar duizenden onderwerpen. Domeinen geven u ook autorisatie en verificatiecontrole over elk onderwerp, zodat u uw tenants partitioneren.

### <a name="example-use-case"></a>Voorbeeld van een toepassing

Gebeurtenisdomeinen zijn het gemakkelijkst te verklaren aan de hand van een voorbeeld. Stel dat u Contoso Construction Machinery runt, waar u tractoren, graafapparatuur en andere zware machines produceert. Als onderdeel van het runnen van het bedrijf, push je realtime informatie naar klanten over onderhoud van apparatuur, systeemstatus en contractupdates. Al deze informatie gaat naar verschillende eindpunten, waaronder uw app, klanteindpunten en andere infrastructuur die klanten hebben ingesteld.

Met gebeurtenisdomeinen u Contoso Construction Machinery modelleren als één gebeurtenisentiteit. Elk van uw klanten wordt weergegeven als een onderwerp binnen het domein. Verificatie en autorisatie worden verwerkt met Azure Active Directory. Elk van uw klanten kan zich abonneren op hun onderwerp en krijgen hun evenementen aan hen geleverd. Beheerde toegang via het gebeurtenisdomein zorgt ervoor dat ze alleen toegang hebben tot hun onderwerp.

Het geeft u ook één eindpunt, waar u al uw klantgebeurtenissen naar publiceren. Event Grid zorgt ervoor dat elk onderwerp alleen op de hoogte is van gebeurtenissen die aan de tenant zijn verbonden.

![Voorbeeld van Contoso Bouw](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Toegangsbeheer

Met een domein krijgt u autorisatie en verificatiecontrole van fijnkorrelige opties over elk onderwerp via het op rollen gebaseerde toegangscontrolebeheer (RBAC) van Azure. U deze rollen gebruiken om elke tenant in uw toepassing te beperken tot alleen de onderwerpen waartoe u hen toegang wilt verlenen.

RBAC in gebeurtenisdomeinen werkt op dezelfde manier [als beheerde toegangscontrole](security-authorization.md) werkt in de rest van Event Grid en Azure. Gebruik RBAC om aangepaste roldefinities in gebeurtenisdomeinen te maken en af te dwingen.

### <a name="built-in-roles"></a>Ingebouwde rollen

Event Grid heeft twee ingebouwde roldefinities om RBAC gemakkelijker te maken voor het werken met gebeurtenisdomeinen. Deze rollen zijn **EventGrid EventSubscription Contributor (Preview)** en **EventGrid EventSubscription Reader (Preview).** U wijst deze rollen toe aan gebruikers die zich moeten abonneren op onderwerpen in uw gebeurtenisdomein. U scopet de roltoewijzing alleen naar het onderwerp waarop gebruikers zich moeten abonneren.

Zie [Ingebouwde rollen voor gebeurtenisraster voor](security-authorization.md#built-in-roles)meer informatie over deze rollen .

## <a name="subscribing-to-topics"></a>Inschrijven op onderwerpen

Een abonnement op gebeurtenissen op een onderwerp binnen een gebeurtenisdomein is hetzelfde als [het maken van een gebeurtenisabonnement op een aangepast onderwerp](./custom-event-quickstart.md) of een abonnement op een gebeurtenis vanuit een Azure-service.

### <a name="domain-scope-subscriptions"></a>Domeinscope-abonnementen

Gebeurtenisdomeinen staan ook domeinbereikabonnementen toe. Een gebeurtenisabonnement op een gebeurtenisdomein ontvangt alle gebeurtenissen die naar het domein worden verzonden, ongeacht het onderwerp waarnaar de gebeurtenissen worden verzonden. Domeinscope-abonnementen kunnen handig zijn voor beheer- en controledoeleinden.

## <a name="publishing-to-an-event-domain"></a>Publiceren naar een gebeurtenisdomein

Wanneer u een gebeurtenisdomein maakt, krijgt u een publicatieeindpunt dat vergelijkbaar is met wanneer u een onderwerp in gebeurtenisraster had gemaakt. 

Als u gebeurtenissen wilt publiceren naar een onderwerp in een gebeurtenisdomein, duwt u de gebeurtenissen naar het eindpunt van het domein op [dezelfde manier als voor een aangepast onderwerp.](./post-to-custom-topic.md) Het enige verschil is dat u het onderwerp moet opgeven waaraan u de gebeurtenis wilt laten leveren.

Als u bijvoorbeeld de volgende reeks gebeurtenissen `"id": "1111"` publiceert, wordt gebeurtenis met naar onderwerp `foo` verzonden terwijl de gebeurtenis met `"id": "2222"` het onderwerp `bar`wordt verzonden:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Gebeurtenisdomeinen beheren publiceren naar onderwerpen voor u. In plaats van gebeurtenissen te publiceren op elk onderwerp dat u afzonderlijk beheert, u al uw gebeurtenissen publiceren naar het eindpunt van het domein. Event Grid zorgt ervoor dat elke gebeurtenis naar het juiste onderwerp wordt verzonden.

## <a name="limits-and-quotas"></a>Limieten en quota
Hier volgen de limieten en quota voor gebeurtenisdomeinen:

- 100.000 onderwerpen per gebeurtenisdomein 
- 100 gebeurtenisdomeinen per Azure-abonnement 
- 500 gebeurtenisabonnementen per onderwerp in een gebeurtenisdomein
- 50 domeinscope-abonnementen 
- 5.000 gebeurtenissen per seconde opnamepercentage (in een domein)

Als deze limieten niet bij u passen, neem dan contact op met het [askgrid@microsoft.com](mailto:askgrid@microsoft.com)productteam door een ondersteuningsticket te openen of door een e-mail te sturen naar . 

## <a name="pricing"></a>Prijzen
Gebeurtenisdomeinen gebruiken dezelfde [bewerkingsprijzen](https://azure.microsoft.com/pricing/details/event-grid/) die alle andere functies in gebeurtenisraster gebruiken.

Bewerkingen werken in gebeurtenisdomeinen hetzelfde als in aangepaste onderwerpen. Elke intis van een gebeurtenis naar een gebeurtenisdomein is een bewerking en elke leveringspoging voor een gebeurtenis is een bewerking.

## <a name="next-steps"></a>Volgende stappen

* Zie [Gebeurtenisdomeinen](./how-to-event-domains.md)beheren voor meer informatie over het instellen van gebeurtenisdomeinen, het maken van onderwerpen, het maken van gebeurtenisabonnementen en het publiceren van gebeurtenissen.
