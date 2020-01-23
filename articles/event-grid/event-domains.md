---
title: Gebeurtenis domeinen in Azure Event Grid
description: In dit artikel wordt beschreven hoe u gebeurtenis domeinen gebruikt voor het beheren van de stroom van aangepaste gebeurtenissen naar uw verschillende zakelijke organisaties, klanten of toepassingen.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: df560df21740d5396bc177e20de5d0eb4bf47713
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511379"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Informatie over gebeurtenis domeinen voor het beheren van Event Grid onderwerpen

In dit artikel wordt beschreven hoe u gebeurtenis domeinen gebruikt voor het beheren van de stroom van aangepaste gebeurtenissen naar uw verschillende zakelijke organisaties, klanten of toepassingen. Gebruik gebeurtenis domeinen voor het volgende:

* Architecturen voor multi tenant gebeurtenissen op schaal beheren.
* Uw autorisatie en verificatie beheren.
* Partitioneer uw onderwerpen zonder ze afzonderlijk te beheren.
* Vermijd afzonderlijk publiceren naar elk van uw onderwerp-eind punten.

## <a name="event-domain-overview"></a>Overzicht van gebeurtenis domeinen

Een gebeurtenis domein is een beheer programma voor een groot aantal Event Grid-onderwerpen die betrekking hebben op dezelfde toepassing. U kunt dit beschouwen als een meta onderwerp dat duizenden afzonderlijke onderwerpen kan hebben.

Gebeurtenis domeinen maken beschikbaar voor u dezelfde architectuur die wordt gebruikt door Azure-Services (zoals Storage en IoT Hub) om hun gebeurtenissen te publiceren. Hiermee kunt u gebeurtenissen naar duizenden onderwerpen publiceren. Domeinen bieden u ook autorisatie en verificatie controle over elk onderwerp, zodat u uw tenants kunt partitioneren.

### <a name="example-use-case"></a>Voorbeeld van een toepassing

Gebeurtenis domeinen worden het eenvoudigst uitgelegd met een voor beeld. Stel dat u contoso-bouw machines uitvoert, waarbij u tractoren, Blijf spitten-apparatuur en andere zware machines produceert. Als onderdeel van het uitvoeren van het bedrijf pusht u real-time informatie naar klanten over het onderhoud van apparaten, systeem status en contract updates. Al deze informatie gaat naar verschillende eind punten, met inbegrip van uw app, klant eindpunten en andere infra structuur die klanten hebben ingesteld.

Met gebeurtenis domeinen kunt u contoso-bouw machines als één gebeurtenis entiteit model leren. Elk van uw klanten wordt weer gegeven als een onderwerp binnen het domein. Verificatie en autorisatie worden afgehandeld met behulp van Azure Active Directory. Elk van uw klanten kan zich abonneren op hun onderwerp en hun gebeurtenissen ontvangen. Beheerde toegang via het gebeurtenis domein zorgt ervoor dat ze alleen toegang hebben tot hun onderwerp.

Het biedt u ook een enkel eind punt, waarmee u alle evenementen van uw klant kunt publiceren naar. Event Grid zorgt ervoor dat elk onderwerp alleen op de hoogte is van de gebeurtenissen die zijn afgestemd op de Tenant.

![Voor beeld van Contoso-constructie](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Toegangsbeheer

Met een domein krijgt u een nauw keurige autorisatie en verificatie controle voor elk onderwerp via het op rollen gebaseerde toegangs beheer (RBAC). U kunt deze rollen gebruiken om elke Tenant in uw toepassing te beperken tot alleen de onderwerpen waaraan u hen toegang wilt verlenen.

RBAC in gebeurtenis domeinen werkt op dezelfde manier als [Managed Access Control](security-authentication.md#management-access-control) werkt in de rest van Event grid en Azure. Gebruik RBAC om aangepaste roldefinities in gebeurtenis domeinen te maken en af te dwingen.

### <a name="built-in-roles"></a>Ingebouwde rollen

Event Grid heeft twee ingebouwde roldefinities om RBAC gemakkelijker te maken voor het werken met gebeurtenis domeinen. Deze rollen zijn **EventGrid EventSubscription Inzender (preview)** en **EventGrid EventSubscription Reader (preview)** . U wijst deze rollen toe aan gebruikers die zich moeten abonneren op onderwerpen in uw gebeurtenis domein. U bereikt de roltoewijzing alleen voor het onderwerp dat gebruikers moeten abonneren op.

Zie [ingebouwde rollen voor Event grid](security-authentication.md#built-in-roles)voor meer informatie over deze rollen.

## <a name="subscribing-to-topics"></a>Abonneren op onderwerpen

Abonneren op gebeurtenissen in een onderwerp binnen een gebeurtenis domein is hetzelfde als het [maken van een gebeurtenis abonnement op een aangepast onderwerp](./custom-event-quickstart.md) of het abonneren op een gebeurtenis van een Azure-service.

### <a name="domain-scope-subscriptions"></a>Domein bereik abonnementen

In gebeurtenis domeinen kunnen abonnementen op domein bereik worden toegepast. Een gebeurtenis abonnement op een gebeurtenis domein ontvangt alle gebeurtenissen die naar het domein worden verzonden, ongeacht het onderwerp waarnaar de gebeurtenissen worden verzonden. De domein bereik abonnementen kunnen nuttig zijn voor beheer-en controle doeleinden.

## <a name="publishing-to-an-event-domain"></a>Publiceren naar een gebeurtenis domein

Wanneer u een gebeurtenis domein maakt, krijgt u een publicatie-eind punt te zien als u een onderwerp in Event Grid hebt gemaakt. 

Als u gebeurtenissen naar een onderwerp in een gebeurtenis domein wilt publiceren, moet u de gebeurtenissen naar het eind punt van het domein pushen op [dezelfde manier als voor een aangepast onderwerp](./post-to-custom-topic.md). Het enige verschil is dat u het onderwerp moet opgeven waarnaar de gebeurtenis moet worden geleverd.

Als u bijvoorbeeld de volgende matrix met gebeurtenissen publiceert, wordt gebeurtenis verzonden met `"id": "1111"` naar het onderwerp `foo` terwijl de gebeurtenis met `"id": "2222"` zou worden verzonden naar het onderwerp `bar`:

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

Gebeurtenis domeinen verwerken het publiceren naar onderwerpen voor u. In plaats van gebeurtenissen te publiceren naar elk onderwerp dat u afzonderlijk beheert, kunt u al uw gebeurtenissen naar het eind punt van het domein publiceren. Event Grid zorgt ervoor dat elke gebeurtenis wordt verzonden naar het juiste onderwerp.

## <a name="limits-and-quotas"></a>Limieten en quota’s
Dit zijn de limieten en quota die betrekking hebben op gebeurtenis domeinen:

- 100.000 onderwerpen per gebeurtenis domein 
- 100 gebeurtenis domeinen per Azure-abonnement 
- 500 gebeurtenisabonnementen per onderwerp in een gebeurtenisdomein
- 50 domein bereik abonnementen 
- 5\.000 gebeurtenissen per seconde opname frequentie (in een domein)

Als deze limieten niet aansluiten bij u, kunt u het product team bereiken door een ondersteunings ticket te openen of door een e-mail bericht naar [askgrid@microsoft.com](mailto:askgrid@microsoft.com)te verzenden. 

## <a name="pricing"></a>Prijzen
Gebeurtenis domeinen gebruiken dezelfde [prijzen voor bewerkingen](https://azure.microsoft.com/pricing/details/event-grid/) die alle andere functies in Event grid gebruiken.

Bewerkingen werken hetzelfde in gebeurtenis domeinen als in aangepaste onderwerpen. Elke ingang van een gebeurtenis in een gebeurtenis domein is een bewerking en elke bezorgings poging voor een gebeurtenis is een bewerking.

## <a name="next-steps"></a>Volgende stappen

* Zie [gebeurtenis domeinen beheren](./how-to-event-domains.md)voor meer informatie over het instellen van gebeurtenis domeinen, het maken van onderwerpen, het maken van gebeurtenis abonnementen en het publiceren van gebeurtenissen.
