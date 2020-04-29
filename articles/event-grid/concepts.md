---
title: Azure Event Grid concepten
description: Beschrijving van Azure Event Grid en de concepten ervan. Definieert verschillende belang rijke onderdelen van Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 348d82f704b89b97e11a09b8f88e92831901b3bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393464"
---
# <a name="concepts-in-azure-event-grid"></a>Concepten in Azure Event Grid

In dit artikel worden de belangrijkste concepten in Azure Event Grid beschreven.

## <a name="events"></a>Gebeurtenissen

Een gebeurtenis is de kleinste hoeveelheid informatie die in het systeem volledig beschrijft wat er is gebeurd. Elke gebeurtenis heeft algemene informatie, zoals: bron van de gebeurtenis, het tijdstip waarop het evenement plaatsvond en de unieke id. Elke gebeurtenis bevat ook specifieke informatie die alleen relevant is voor het specifieke gebeurtenis type. Een gebeurtenis over het maken van een nieuw bestand in Azure Storage bevat bijvoorbeeld informatie over het bestand, zoals de waarde `lastTimeModified`. Een Event Hubs-gebeurtenis bevat de URL van het Capture-bestand. 

Een gebeurtenis met een grootte van Maxi maal 64 KB wordt gedekt door de algemene Beschik baarheid (GA) Service Level Agreement (SLA). De ondersteuning voor een gebeurtenis met een grootte van Maxi maal 1 MB is momenteel als preview-versie beschikbaar. Gebeurtenissen van meer dan 64 KB worden in rekening gebracht in stappen van 64-KB. 


Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor de eigenschappen die in een gebeurtenis worden verzonden.

## <a name="publishers"></a>Uitgevers

Een uitgever is de gebruiker of organisatie die besluit gebeurtenissen te verzenden naar Event Grid. Micro soft publiceert gebeurtenissen voor verschillende Azure-Services. U kunt gebeurtenissen publiceren via uw eigen toepassing. Organisaties die services buiten Azure hosten, kunnen via Event Grid gebeurtenissen publiceren.

## <a name="event-sources"></a>Gebeurtenisbronnen

Een gebeurtenis bron is de plaats waar de gebeurtenis plaatsvindt. Elke gebeurtenisbron is gerelateerd aan één of meer gebeurtenistypen. Azure Storage is bijvoorbeeld de gebeurtenisbron bij door een blob gemaakte gebeurtenissen. IoT Hub is de gebeurtenisbron bij door een apparaat gemaakte gebeurtenissen. Uw toepassing is de gebeurtenisbron bij aangepaste gebeurtenissen die u definieert. Gebeurtenisbronnen zijn verantwoordelijk voor het verzenden van gebeurtenissen naar Event Grid.

Zie [gebeurtenis bronnen in azure Event grid](overview.md#event-sources)voor informatie over het implementeren van een van de ondersteunde Event grid bronnen.

## <a name="topics"></a>Onderwerpen

Het event grid-onderwerp bevat een eind punt waar gebeurtenissen door de bron worden verzonden. De uitgever maakt het onderwerp Event grid en bepaalt of een gebeurtenis bron één onderwerp of meer dan één onderwerp nodig heeft. Er wordt een onderwerp gebruikt voor een verzameling van gerelateerde gebeurtenissen. Voor het reageren op bepaalde typen gebeurtenissen bepalen abonnees welke onderwerpen moeten worden geabonneerd.

Systeemonderwerpen zijn ingebouwde onderwerpen die worden aangeboden door Azure-services. Er staan geen systeemonderwerpen in uw Azure-abonnement omdat de uitgever er eigenaar van is, maar u kunt zich wel op de onderwerpen abonneren. Als u zich wilt abonneren, moet u informatie opgeven over de resource waar u gebeurtenissen van wilt ontvangen. Als u toegang hebt tot een bepaalde resource, kunt u zich abonneren op diens gebeurtenissen.

Aangepaste onderwerpen zijn onderwerpen van toepassingen en derden. Wanneer u een aangepast onderwerp maakt of u er toegang toe krijgt, ziet u dat aangepaste onderwerp in uw abonnement.

Bij het ontwerpen van uw toepassing hebt u de flexibiliteit bij het bepalen van het aantal te maken onderwerpen. Voor grote oplossingen maakt u een aangepast onderwerp voor elke categorie gerelateerde gebeurtenissen. Neem bijvoorbeeld een toepassing die gebeurtenissen over het bewerken van gebruikersaccounts en het verwerken van orders verzendt. De kans is klein dat er gebeurtenis-handlers zijn die beide gebeurteniscategorieën kunnen verwerken. Maak daarom twee aangepaste onderwerpen en laat gebeurtenis-handlers zich abonneren op het onderwerp dat hen interesseert. Voor kleine oplossingen kunt u de voor keur geven aan het verzenden van alle gebeurtenissen naar één onderwerp. Gebeurtenis abonnees kunnen filteren op de gewenste gebeurtenis typen.

## <a name="event-subscriptions"></a>Gebeurtenisabonnementen

Met een abonnement wordt Event Grid welke gebeurtenissen in een onderwerp u wilt ontvangen. Wanneer u het abonnement maakt, geeft u een eind punt op voor het afhandelen van de gebeurtenis. U kunt de gebeurtenissen filteren die naar het eind punt worden verzonden. U kunt filteren op gebeurtenis type of onderwerps patroon. Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie.

Zie voor voor beelden van het maken van abonnementen:

* [Azure CLI-voorbeelden voor Event Grid](cli-samples.md)
* [Voorbeelden van Azure PowerShell voor Event Grid](powershell-samples.md)
* [Azure Resource Manager-sjablonen voor Event Grid](template-samples.md)

Zie [Query Event grid-abonnementen](query-event-subscriptions.md)voor meer informatie over het ophalen van uw huidige Event grid-abonnementen.

## <a name="event-subscription-expiration"></a>Verloop datum van gebeurtenis abonnement
Het gebeurtenisabonnement is na die datum automatisch verlopen. Stel een verval datum in voor gebeurtenis abonnementen die alleen gedurende een beperkte periode nodig zijn. u hoeft zich geen zorgen te maken over het opschonen van die abonnementen. Wanneer u bijvoorbeeld een gebeurtenis abonnement maakt om een scenario te testen, wilt u mogelijk een verval datum instellen. 

Zie [abonneren met geavanceerde filters](how-to-filter-events.md#subscribe-with-advanced-filters)voor een voor beeld van het instellen van een verval datum.

## <a name="event-handlers"></a>Event Handlers

Vanuit een Event Grid perspectief is een gebeurtenis-handler het punt waar de gebeurtenis wordt verzonden. De handler heeft een aantal verdere acties nodig om de gebeurtenis te verwerken. Event Grid ondersteunt verschillende typen handlers. U kunt een ondersteunde Azure-service of uw eigen webhook gebruiken als handler. Afhankelijk van het type handler, worden Event Grid verschillende mechanismen gevolgd om de levering van de gebeurtenis te garanderen. Voor gebeurtenis-handlers voor HTTP-webhooks wordt de gebeurtenis opnieuw geprobeerd totdat de handler de status `200 – OK`code retourneert van. Voor Azure Storage wachtrij worden de gebeurtenissen opnieuw geprobeerd totdat de Queue-service de bericht push naar de wachtrij heeft verwerkt.

Zie [gebeurtenis-handlers in azure Event grid](event-handlers.md)voor informatie over het implementeren van een van de ondersteunde Event grid handlers.

## <a name="security"></a>Beveiliging

Event Grid biedt beveiliging voor het abonneren op onderwerpen en het publiceren van onderwerpen. Wanneer u zich abonneert, moet u over de juiste machtigingen beschikken voor het onderwerp resource of event grid. Bij het publiceren moet u een SAS-token of sleutel verificatie hebben voor het onderwerp. Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie.

## <a name="event-delivery"></a>Gebeurtenisverzending

Als Event Grid niet kan bevestigen dat er een gebeurtenis is ontvangen door het eind punt van de abonnee, wordt de gebeurtenis opnieuw geleverd. Zie [Event grid aflevering van berichten en probeer het opnieuw](delivery-and-retry.md).

## <a name="batching"></a>Batchverwerking

Wanneer u een aangepast onderwerp gebruikt, moeten gebeurtenissen altijd in een matrix worden gepubliceerd. Dit kan een batch zijn van één voor scenario's met een lage door Voer, maar voor het gebruik van grote volumes is het raadzaam om meerdere gebeurtenissen per publicatie op te nemen voor een hogere efficiëntie. Batches kunnen Maxi maal 1 MB groot zijn. Elke gebeurtenis mag nog steeds niet groter zijn dan 64 KB (algemene Beschik baarheid) of 1 MB (preview-versie).

> [!NOTE]
> Een gebeurtenis met een grootte van Maxi maal 64 KB wordt gedekt door de algemene Beschik baarheid (GA) Service Level Agreement (SLA). De ondersteuning voor een gebeurtenis met een grootte van Maxi maal 1 MB is momenteel als preview-versie beschikbaar. Gebeurtenissen van meer dan 64 KB worden gefactureerd in stappen van 64 KB. 

## <a name="next-steps"></a>Volgende stappen

* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [aangepaste gebeurtenissen maken en routeren met Azure Event grid](custom-event-quickstart.md)om snel aan de slag te gaan met Event grid.
