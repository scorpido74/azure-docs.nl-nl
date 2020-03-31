---
title: Concepten - IoT-rand voor Azure-gebeurtenisraster | Microsoft Documenten
description: Concepten in Event Grid op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992546"
---
# <a name="event-grid-concepts"></a>Concepten binnen Event Grid

In dit artikel worden de belangrijkste concepten in Azure Event Grid beschreven.

## <a name="events"></a>Gebeurtenissen

Een gebeurtenis is de kleinste hoeveelheid informatie die volledig beschrijft iets dat is gebeurd in het systeem. Elke gebeurtenis heeft algemene informatie zoals: bron van de gebeurtenis, tijd dat de gebeurtenis plaatsvond en unieke id. Elk evenement heeft ook specifieke informatie die alleen relevant is voor het specifieke type evenement. De ondersteuning voor een gebeurtenis van maximaal 1 MB is momenteel in preview.

Zie [Azure Event Grid-gebeurtenisschema](event-schemas.md)voor de eigenschappen die in een gebeurtenis zijn opgenomen .

## <a name="publishers"></a>Uitgevers

Een uitgever is de gebruiker of organisatie die besluit gebeurtenissen naar gebeurtenisraster te sturen. U kunt gebeurtenissen publiceren via uw eigen toepassing.

## <a name="event-sources"></a>Gebeurtenisbronnen

Een gebeurtenisbron is waar de gebeurtenis plaatsvindt. Elke gebeurtenisbron is gerelateerd aan één of meer gebeurtenistypen. Azure Storage is bijvoorbeeld de gebeurtenisbron bij door een blob gemaakte gebeurtenissen. Uw toepassing is de gebeurtenisbron bij aangepaste gebeurtenissen die u definieert. Gebeurtenisbronnen zijn verantwoordelijk voor het verzenden van gebeurtenissen naar Event Grid.

## <a name="topics"></a>Onderwerpen

Het gebeurtenisrasteronderwerp biedt een eindpunt waarin de bron gebeurtenissen verzendt. De uitgever maakt het onderwerp van het gebeurtenisraster en bepaalt of een gebeurtenisbron één onderwerp of meer dan één onderwerp nodig heeft. Een onderwerp wordt gebruikt voor een verzameling gerelateerde evenementen. Om te reageren op bepaalde soorten gebeurtenissen, bepalen abonnees op welke onderwerpen ze zich moeten abonneren.

Bij het ontwerpen van uw toepassing hebt u de flexibiliteit om te beslissen hoeveel onderwerpen u wilt maken. Maak voor grote oplossingen een aangepast onderwerp voor elke categorie gerelateerde gebeurtenissen. Neem bijvoorbeeld een toepassing die gebeurtenissen over het bewerken van gebruikersaccounts en het verwerken van orders verzendt. De kans is klein dat er gebeurtenis-handlers zijn die beide gebeurteniscategorieën kunnen verwerken. Maak daarom twee aangepaste onderwerpen en laat gebeurtenis-handlers zich abonneren op het onderwerp dat hen interesseert. Voor kleine oplossingen u er de voorkeur aan geven om alle gebeurtenissen naar één onderwerp te sturen. Gebeurtenisabonnees kunnen filteren op de gewenste gebeurtenistypen.

Zie [REST API-documentatie](api.md) over het beheren van onderwerpen in Gebeurtenisraster.

## <a name="event-subscriptions"></a>Gebeurtenisabonnementen

Een abonnement vertelt Event Grid welke gebeurtenissen op een onderwerp dat u wilt ontvangen. Bij het maken van het abonnement geeft u een eindpunt voor het afhandelen van de gebeurtenis. U de gebeurtenissen filteren die naar het eindpunt worden verzonden. 

Zie [REST API-documentatie](api.md) over het beheren van abonnementen in Gebeurtenisraster.

## <a name="event-handlers"></a>Event Handlers

Vanuit het perspectief van eventgrid is een gebeurtenishandler de plaats waar de gebeurtenis wordt verzonden. De handler onderneemt verdere actie om de gebeurtenis te verwerken. Event Grid ondersteunt verschillende handlertypen. U een ondersteunde Azure-service of uw eigen webhaak als handler gebruiken. Afhankelijk van het type handler volgt Event Grid verschillende mechanismen om de levering van de gebeurtenis te garanderen. Als de doelgebeurtenishandler een HTTP-webhaak is, wordt de gebeurtenis `200 – OK`opnieuw geprobeerd wanneer de handler een statuscode van . Voor edge Hub wordt de gebeurtenis als deze zonder uitzondering wordt geleverd, als succesvol beschouwd.

## <a name="security"></a>Beveiliging

Event Grid biedt beveiliging voor het abonneren op onderwerpen en het publiceren van onderwerpen. Zie [Beveiliging en verificatie van gebeurtenisraster](security-authentication.md)voor meer informatie.

## <a name="event-delivery"></a>Levering van evenementen

Als Gebeurtenisgrid niet kan bevestigen dat een gebeurtenis is ontvangen door het eindpunt van de abonnee, wordt de gebeurtenis opnieuw geleverd. Zie [Berichtbezorging en opnieuw proberen](delivery-retry.md)voor meer informatie .

## <a name="batching"></a>Batchverwerking

Wanneer u een aangepast onderwerp gebruikt, moeten gebeurtenissen altijd in een array worden gepubliceerd. Voor scenario's met een lage doorvoer heeft de array slechts één waarde. Voor use cases met een hoog volume raden we u aan meerdere gebeurtenissen per publicatie samen te voegen om een hogere efficiëntie te bereiken. Batches kunnen maximaal 1 MB zijn. Elke gebeurtenis mag nog steeds niet groter zijn dan 1 MB (preview).