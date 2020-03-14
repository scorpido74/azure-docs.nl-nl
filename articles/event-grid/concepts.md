---
title: Azure Event Grid concepten
description: Beschrijving van Azure Event Grid en de concepten ervan. Definieert verschillende belang rijke onderdelen van Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 0821c749a6cb718e1b8abb74a2925bc041850eaf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265062"
---
# <a name="concepts-in-azure-event-grid"></a>Concepten in Azure Event Grid

In dit artikel worden de belangrijkste concepten in Azure Event Grid beschreven.

## <a name="events"></a>Gebeurtenissen

Een gebeurtenis is de kleinste hoeveelheid informatie die in het systeem volledig beschrijft wat er is gebeurd. Elke gebeurtenis heeft algemene informatie, zoals: bron van de gebeurtenis, het tijdstip waarop het evenement plaatsvond en de unieke id. Elke gebeurtenis bevat ook specifieke informatie die alleen relevant is voor het specifieke gebeurtenis type. Bijvoorbeeld: een gebeurtenis over een nieuw bestand dat wordt gemaakt in Azure Storage bevat details over het bestand, zoals de `lastTimeModified` waarde. Of een Event Hubs gebeurtenis heeft de URL van het opname bestand. 

Een gebeurtenis met een grootte van Maxi maal 64 KB wordt gedekt door de algemene Beschik baarheid (GA) Service Level Agreement (SLA). De ondersteuning voor een gebeurtenis met een grootte van Maxi maal 1 MB is momenteel als preview-versie beschikbaar. Gebeurtenissen van meer dan 64 KB worden in rekening gebracht in stappen van 64-KB. 


Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor de eigenschappen die in een gebeurtenis worden verzonden.

## <a name="publishers"></a>Uitgevers

Een uitgever is de gebruiker of organisatie die besluit gebeurtenissen te verzenden naar Event Grid. Micro soft publiceert gebeurtenissen voor verschillende Azure-Services. U kunt gebeurtenissen vanuit uw eigen toepassing publiceren. Organisaties die services buiten Azure hosten, kunnen gebeurtenissen publiceren via Event Grid.

## <a name="event-sources"></a>Gebeurtenisbronnen

Een gebeurtenis bron is de plaats waar de gebeurtenis plaatsvindt. Elke gebeurtenis bron heeft betrekking op een of meer gebeurtenis typen. Azure Storage is bijvoorbeeld de gebeurtenis bron voor het maken van BLOB-gebeurtenissen. IoT Hub is de gebeurtenis bron voor het maken van apparaat-gebeurtenissen. Uw toepassing is de gebeurtenis bron voor aangepaste gebeurtenissen die u definieert. Gebeurtenis bronnen zijn verantwoordelijk voor het verzenden van gebeurtenissen naar Event Grid.

Zie [gebeurtenis bronnen in azure Event grid](event-sources.md)voor informatie over het implementeren van een van de ondersteunde Event grid bronnen.

## <a name="topics"></a>Onderwerpen

Het event grid-onderwerp bevat een eind punt waar gebeurtenissen door de bron worden verzonden. De uitgever maakt het onderwerp Event grid en bepaalt of een gebeurtenis bron één onderwerp of meer dan één onderwerp nodig heeft. Er wordt een onderwerp gebruikt voor een verzameling van gerelateerde gebeurtenissen. Voor het reageren op bepaalde typen gebeurtenissen bepalen abonnees welke onderwerpen moeten worden geabonneerd.

Systeem onderwerpen zijn ingebouwde onderwerpen van Azure-Services. U ziet geen systeem onderwerpen in uw Azure-abonnement omdat de uitgever eigenaar is van de onderwerpen, maar u er wel op kunt abonneren. Als u zich wilt abonneren, geeft u informatie op over de resource waarvan u gebeurtenissen wilt ontvangen. Als u toegang tot de resource hebt, kunt u zich abonneren op de gebeurtenissen.

Aangepaste onderwerpen zijn toepassingen en onderwerpen van derden. Wanneer u toegang tot een aangepast onderwerp maakt of toewijst, ziet u dat aangepaste onderwerp in uw abonnement.

Bij het ontwerpen van uw toepassing hebt u de flexibiliteit bij het bepalen van het aantal te maken onderwerpen. Voor grote oplossingen maakt u een aangepast onderwerp voor elke categorie gerelateerde gebeurtenissen. Denk bijvoorbeeld aan een toepassing die gebeurtenissen met betrekking tot het wijzigen van gebruikers accounts en het verwerken van orders verzendt. Het is niet onwaarschijnlijk dat elke gebeurtenis-handler beide categorieën gebeurtenissen wil. Maak twee aangepaste onderwerpen en laat gebeurtenis-handlers zich abonneren op het abonnement. Voor kleine oplossingen kunt u de voor keur geven aan het verzenden van alle gebeurtenissen naar één onderwerp. Gebeurtenis abonnees kunnen filteren op de gewenste gebeurtenis typen.

## <a name="event-subscriptions"></a>Gebeurtenisabonnementen

Met een abonnement wordt Event Grid welke gebeurtenissen in een onderwerp u wilt ontvangen. Wanneer u het abonnement maakt, geeft u een eind punt op voor het afhandelen van de gebeurtenis. U kunt de gebeurtenissen filteren die naar het eind punt worden verzonden. U kunt filteren op gebeurtenis type of onderwerps patroon. Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie.

Zie voor voor beelden van het maken van abonnementen:

* [Voor beelden van Azure CLI voor Event Grid](cli-samples.md)
* [Voor beelden Azure PowerShell voor Event Grid](powershell-samples.md)
* [Azure Resource Manager sjablonen voor Event Grid](template-samples.md)

Zie [Query Event grid-abonnementen](query-event-subscriptions.md)voor meer informatie over het ophalen van uw huidige Event grid-abonnementen.

## <a name="event-subscription-expiration"></a>Verloop datum van gebeurtenis abonnement
Het gebeurtenisabonnement is na die datum automatisch verlopen. Stel een verval datum in voor gebeurtenis abonnementen die alleen gedurende een beperkte periode nodig zijn. u hoeft zich geen zorgen te maken over het opschonen van die abonnementen. Wanneer u bijvoorbeeld een gebeurtenis abonnement maakt om een scenario te testen, wilt u mogelijk een verval datum instellen. 

Zie [abonneren met geavanceerde filters](how-to-filter-events.md#subscribe-with-advanced-filters)voor een voor beeld van het instellen van een verval datum.

## <a name="event-handlers"></a>Event Handlers

Vanuit een Event Grid perspectief is een gebeurtenis-handler het punt waar de gebeurtenis wordt verzonden. De handler heeft een aantal verdere acties nodig om de gebeurtenis te verwerken. Event Grid ondersteunt verschillende typen handlers. U kunt een ondersteunde Azure-service of uw eigen webhook gebruiken als handler. Afhankelijk van het type handler, worden Event Grid verschillende mechanismen gevolgd om de levering van de gebeurtenis te garanderen. Voor gebeurtenis-handlers voor HTTP-webhooks wordt de gebeurtenis opnieuw geprobeerd totdat de handler de status code van `200 – OK`retourneert. Voor Azure Storage wachtrij worden de gebeurtenissen opnieuw geprobeerd totdat de Queue-service de bericht push naar de wachtrij heeft verwerkt.

Zie [gebeurtenis-handlers in azure Event grid](event-handlers.md)voor informatie over het implementeren van een van de ondersteunde Event grid handlers.

## <a name="security"></a>Beveiliging

Event Grid biedt beveiliging voor het abonneren op onderwerpen en het publiceren van onderwerpen. Wanneer u zich abonneert, moet u over de juiste machtigingen beschikken voor het onderwerp resource of event grid. Bij het publiceren moet u een SAS-token of sleutel verificatie hebben voor het onderwerp. Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie.

## <a name="event-delivery"></a>Gebeurtenis levering

Als Event Grid niet kan bevestigen dat er een gebeurtenis is ontvangen door het eind punt van de abonnee, wordt de gebeurtenis opnieuw geleverd. Zie [Event grid aflevering van berichten en probeer het opnieuw](delivery-and-retry.md).

## <a name="batching"></a>Batchverwerking

Wanneer u een aangepast onderwerp gebruikt, moeten gebeurtenissen altijd in een matrix worden gepubliceerd. Dit kan een batch zijn van één voor scenario's met een lage door Voer, maar voor het gebruik van grote volumes is het raadzaam om meerdere gebeurtenissen per publicatie op te nemen voor een hogere efficiëntie. Batches kunnen Maxi maal 1 MB groot zijn. Elke gebeurtenis mag nog steeds niet groter zijn dan 64 KB (algemene Beschik baarheid) of 1 MB (preview-versie).

> [!NOTE]
> Een gebeurtenis met een grootte van Maxi maal 64 KB wordt gedekt door de algemene Beschik baarheid (GA) Service Level Agreement (SLA). De ondersteuning voor een gebeurtenis met een grootte van Maxi maal 1 MB is momenteel als preview-versie beschikbaar. Gebeurtenissen van meer dan 64 KB worden gefactureerd in stappen van 64 KB. 

## <a name="next-steps"></a>Volgende stappen

* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [aangepaste gebeurtenissen maken en routeren met Azure Event grid](custom-event-quickstart.md)om snel aan de slag te gaan met Event grid.
