---
title: Azure Event Grid-concepten
description: Beschrijving van Azure Event Grid en de concepten ervan. Hiermee definieert u verschillende belangrijke onderdelen van gebeurtenisraster.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 0821c749a6cb718e1b8abb74a2925bc041850eaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265062"
---
# <a name="concepts-in-azure-event-grid"></a>Concepten in Azure-gebeurtenisraster

In dit artikel worden de belangrijkste concepten in Azure Event Grid beschreven.

## <a name="events"></a>Gebeurtenissen

Een gebeurtenis is de kleinste hoeveelheid informatie die volledig beschrijft iets dat is gebeurd in het systeem. Elke gebeurtenis heeft algemene informatie zoals: bron van de gebeurtenis, tijd dat de gebeurtenis plaatsvond en unieke id. Elk evenement heeft ook specifieke informatie die alleen relevant is voor het specifieke type evenement. Een gebeurtenis over het maken van een nieuw bestand in Azure Storage bevat bijvoorbeeld informatie over het bestand, zoals de waarde `lastTimeModified`. Een Event Hubs-gebeurtenis bevat de URL van het Capture-bestand. 

Een gebeurtenis van grootte tot 64 KB wordt gedekt door de ALGEMENE Beschikbaarheid (GA) Service Level Agreement (SLA). De ondersteuning voor een gebeurtenis van maximaal 1 MB is momenteel in preview. Gebeurtenissen van meer dan 64 KB worden in stappen van 64 KB in rekening gebracht. 


Zie [Azure Event Grid-gebeurtenisschema](event-schema.md)voor de eigenschappen die in een gebeurtenis worden verzonden.

## <a name="publishers"></a>Uitgevers

Een uitgever is de gebruiker of organisatie die besluit gebeurtenissen naar gebeurtenisraster te sturen. Microsoft publiceert gebeurtenissen voor verschillende Azure-services. U kunt gebeurtenissen publiceren via uw eigen toepassing. Organisaties die services buiten Azure hosten, kunnen via Event Grid gebeurtenissen publiceren.

## <a name="event-sources"></a>Gebeurtenisbronnen

Een gebeurtenisbron is waar de gebeurtenis plaatsvindt. Elke gebeurtenisbron is gerelateerd aan één of meer gebeurtenistypen. Azure Storage is bijvoorbeeld de gebeurtenisbron bij door een blob gemaakte gebeurtenissen. IoT Hub is de gebeurtenisbron bij door een apparaat gemaakte gebeurtenissen. Uw toepassing is de gebeurtenisbron bij aangepaste gebeurtenissen die u definieert. Gebeurtenisbronnen zijn verantwoordelijk voor het verzenden van gebeurtenissen naar Event Grid.

Zie [Gebeurtenisbronnen in Azure Event Grid](event-sources.md)voor informatie over het implementeren van een van de ondersteunde gebeurtenisrasterbronnen.

## <a name="topics"></a>Onderwerpen

Het gebeurtenisrasteronderwerp biedt een eindpunt waarin de bron gebeurtenissen verzendt. De uitgever maakt het onderwerp van het gebeurtenisraster en bepaalt of een gebeurtenisbron één onderwerp of meer dan één onderwerp nodig heeft. Een onderwerp wordt gebruikt voor een verzameling gerelateerde evenementen. Om te reageren op bepaalde soorten gebeurtenissen, bepalen abonnees op welke onderwerpen ze zich moeten abonneren.

Systeemonderwerpen zijn ingebouwde onderwerpen die worden aangeboden door Azure-services. Er staan geen systeemonderwerpen in uw Azure-abonnement omdat de uitgever er eigenaar van is, maar u kunt zich wel op de onderwerpen abonneren. Als u zich wilt abonneren, moet u informatie opgeven over de resource waar u gebeurtenissen van wilt ontvangen. Als u toegang hebt tot een bepaalde resource, kunt u zich abonneren op diens gebeurtenissen.

Aangepaste onderwerpen zijn onderwerpen van toepassingen en derden. Wanneer u een aangepast onderwerp maakt of u er toegang toe krijgt, ziet u dat aangepaste onderwerp in uw abonnement.

Bij het ontwerpen van uw toepassing hebt u flexibiliteit bij het bepalen van het aantal onderwerpen dat u wilt maken. Maak voor grote oplossingen een aangepast onderwerp voor elke categorie gerelateerde gebeurtenissen. Neem bijvoorbeeld een toepassing die gebeurtenissen over het bewerken van gebruikersaccounts en het verwerken van orders verzendt. De kans is klein dat er gebeurtenis-handlers zijn die beide gebeurteniscategorieën kunnen verwerken. Maak daarom twee aangepaste onderwerpen en laat gebeurtenis-handlers zich abonneren op het onderwerp dat hen interesseert. Voor kleine oplossingen u er de voorkeur aan geven om alle gebeurtenissen naar één onderwerp te sturen. Gebeurtenisabonnees kunnen filteren op de gewenste gebeurtenistypen.

## <a name="event-subscriptions"></a>Gebeurtenisabonnementen

Een abonnement vertelt Event Grid welke gebeurtenissen op een onderwerp dat u wilt ontvangen. Bij het maken van het abonnement geeft u een eindpunt voor het afhandelen van de gebeurtenis. U de gebeurtenissen filteren die naar het eindpunt worden verzonden. U filteren op gebeurtenistype of onderwerppatroon. Zie [Abonnementsschema gebeurtenisraster](subscription-creation-schema.md)voor meer informatie .

Zie voor voorbeelden van het maken van abonnementen:

* [Azure CLI-voorbeelden voor Event Grid](cli-samples.md)
* [Voorbeelden van Azure PowerShell voor Event Grid](powershell-samples.md)
* [Azure Resource Manager-sjablonen voor Event Grid](template-samples.md)

Zie [Queryeventgrid-abonnementen voor](query-event-subscriptions.md)informatie over het verkrijgen van uw huidige gebeurtenisrasterabonnementen .

## <a name="event-subscription-expiration"></a>Verlopen gebeurtenisabonnement
Het gebeurtenisabonnement is na die datum automatisch verlopen. Stel een vervaldatum in voor gebeurtenisabonnementen die slechts voor een beperkte tijd nodig zijn en u wilt zich geen zorgen maken over het opschonen van die abonnementen. Wanneer u bijvoorbeeld een gebeurtenisabonnement maakt om een scenario te testen, u een vervaldatum instellen. 

Zie [Abonneren met geavanceerde filters](how-to-filter-events.md#subscribe-with-advanced-filters)voor een voorbeeld van het instellen van een vervaldatum.

## <a name="event-handlers"></a>Event Handlers

Vanuit het perspectief van eventgrid is een gebeurtenishandler de plaats waar de gebeurtenis wordt verzonden. De handler onderneemt verdere actie om de gebeurtenis te verwerken. Event Grid ondersteunt verschillende handlertypen. U een ondersteunde Azure-service of uw eigen webhook als handler gebruiken. Afhankelijk van het type handler volgt Event Grid verschillende mechanismen om de levering van de gebeurtenis te garanderen. Voor HTTP-webhook-gebeurtenishandlers wordt de gebeurtenis opnieuw geprobeerd `200 – OK`totdat de handler een statuscode van . Voor Azure Storage Queue worden de gebeurtenissen opnieuw geprobeerd totdat de wachtrijservice het bericht in de wachtrij verwerkt.

Zie [Gebeurtenishandlers in Azure Event Grid](event-handlers.md)voor informatie over het implementeren van een van de ondersteunde gebeurtenisrasterhandlers.

## <a name="security"></a>Beveiliging

Event Grid biedt beveiliging voor het abonneren op onderwerpen en het publiceren van onderwerpen. Wanneer u zich abonneert, moet u over voldoende machtigingen beschikken voor het onderwerp resource of gebeurtenisraster. Bij publicatie moet u een SAS-token of sleutelverificatie voor het onderwerp hebben. Zie [Beveiliging en verificatie van gebeurtenisraster](security-authentication.md)voor meer informatie.

## <a name="event-delivery"></a>Levering van evenementen

Als Gebeurtenisgrid niet kan bevestigen dat een gebeurtenis is ontvangen door het eindpunt van de abonnee, wordt de gebeurtenis opnieuw geleverd. Zie [Berichtbezorging en opnieuw proberen](delivery-and-retry.md)voor meer informatie .

## <a name="batching"></a>Batchverwerking

Wanneer u een aangepast onderwerp gebruikt, moeten gebeurtenissen altijd in een array worden gepubliceerd. Dit kan een batch van een voor low-throughput scenario's, echter, voor een hoog volume use cases, het is raadzaam dat u batch verschillende gebeurtenissen samen per publiceren om een hogere efficiëntie te bereiken. Batches kunnen maximaal 1 MB zijn. Elke gebeurtenis mag nog steeds niet groter zijn dan 64 KB (algemene beschikbaarheid) of 1 MB (voorbeeld).

> [!NOTE]
> Een gebeurtenis van grootte tot 64 KB wordt gedekt door de ALGEMENE Beschikbaarheid (GA) Service Level Agreement (SLA). De ondersteuning voor een gebeurtenis van maximaal 1 MB is momenteel in preview. Gebeurtenissen van meer dan 64 KB worden in stappen van 64 KB in rekening gebracht. 

## <a name="next-steps"></a>Volgende stappen

* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [Aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md)om snel aan de slag te gaan met gebeurtenisraster.
