---
title: Concepten-Azure Event Grid IoT Edge | Microsoft Docs
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
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992546"
---
# <a name="event-grid-concepts"></a>Concepten binnen Event Grid

In dit artikel worden de belangrijkste concepten in Azure Event Grid beschreven.

## <a name="events"></a>Evenements

Een gebeurtenis is de kleinste hoeveelheid informatie die in het systeem volledig beschrijft wat er is gebeurd. Elke gebeurtenis heeft algemene informatie, zoals: bron van de gebeurtenis, het tijdstip waarop het evenement plaatsvond en de unieke id. Elke gebeurtenis bevat ook specifieke informatie die alleen relevant is voor het specifieke gebeurtenis type. De ondersteuning voor een gebeurtenis met een grootte van Maxi maal 1 MB is momenteel als preview-versie beschikbaar.

Zie [Azure Event grid-gebeurtenis schema](event-schemas.md)voor de eigenschappen die in een gebeurtenis zijn opgenomen.

## <a name="publishers"></a>Uitgevers

Een uitgever is de gebruiker of organisatie die besluit gebeurtenissen te verzenden naar Event Grid. U kunt gebeurtenissen vanuit uw eigen toepassing publiceren.

## <a name="event-sources"></a>Gebeurtenisbronnen

Een gebeurtenis bron is de plaats waar de gebeurtenis plaatsvindt. Elke gebeurtenis bron heeft betrekking op een of meer gebeurtenis typen. Azure Storage is bijvoorbeeld de gebeurtenis bron voor het maken van BLOB-gebeurtenissen. Uw toepassing is de gebeurtenis bron voor aangepaste gebeurtenissen die u definieert. Gebeurtenis bronnen zijn verantwoordelijk voor het verzenden van gebeurtenissen naar Event Grid.

## <a name="topics"></a>Topics

Het event grid-onderwerp bevat een eind punt waar gebeurtenissen door de bron worden verzonden. De uitgever maakt het onderwerp Event grid en bepaalt of een gebeurtenis bron één onderwerp of meer dan één onderwerp nodig heeft. Er wordt een onderwerp gebruikt voor een verzameling van gerelateerde gebeurtenissen. Voor het reageren op bepaalde typen gebeurtenissen bepalen abonnees welke onderwerpen moeten worden geabonneerd.

Bij het ontwerpen van uw toepassing hebt u de flexibiliteit om te bepalen hoeveel onderwerpen u moet maken. Voor grote oplossingen maakt u een aangepast onderwerp voor elke categorie gerelateerde gebeurtenissen. Denk bijvoorbeeld aan een toepassing die gebeurtenissen met betrekking tot het wijzigen van gebruikers accounts en het verwerken van orders verzendt. Het is niet onwaarschijnlijk dat elke gebeurtenis-handler beide categorieën gebeurtenissen wil. Maak twee aangepaste onderwerpen en laat gebeurtenis-handlers zich abonneren op het abonnement. Voor kleine oplossingen kunt u de voor keur geven aan het verzenden van alle gebeurtenissen naar één onderwerp. Gebeurtenis abonnees kunnen filteren op de gewenste gebeurtenis typen.

Zie [rest API documentatie](api.md) over het beheren van onderwerpen in Event grid.

## <a name="event-subscriptions"></a>Gebeurtenisabonnementen

Met een abonnement wordt Event Grid welke gebeurtenissen in een onderwerp u wilt ontvangen. Wanneer u het abonnement maakt, geeft u een eind punt op voor het afhandelen van de gebeurtenis. U kunt de gebeurtenissen filteren die naar het eind punt worden verzonden. 

Zie [rest API documentatie](api.md) over het beheren van abonnementen in Event grid.

## <a name="event-handlers"></a>Event Handlers

Vanuit een Event Grid perspectief is een gebeurtenis-handler het punt waar de gebeurtenis wordt verzonden. De handler moet verdere actie ondernemen om de gebeurtenis te verwerken. Event Grid ondersteunt verschillende typen handlers. U kunt een ondersteunde Azure-service of uw eigen webhook gebruiken als handler. Afhankelijk van het type handler, worden Event Grid verschillende mechanismen gevolgd om de levering van de gebeurtenis te garanderen. Als de doel gebeurtenis-handler een HTTP-webhook is, wordt de gebeurtenis opnieuw geprobeerd wanneer de handler de status code van `200 – OK`retourneert. Als de gebeurtenis wordt geleverd zonder uitzonde ring, wordt de hub als geslaagd beschouwd.

## <a name="security"></a>Beveiliging

Event Grid biedt beveiliging voor het abonneren op onderwerpen en het publiceren van onderwerpen. Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie.

## <a name="event-delivery"></a>Gebeurtenis levering

Als Event Grid niet kan bevestigen dat er een gebeurtenis is ontvangen door het eind punt van de abonnee, wordt de gebeurtenis opnieuw geleverd. Zie [Event grid aflevering van berichten en probeer het opnieuw](delivery-retry.md).

## <a name="batching"></a>Batchverwerking

Wanneer u een aangepast onderwerp gebruikt, moeten gebeurtenissen altijd in een matrix worden gepubliceerd. Voor scenario's met een lage door Voer geldt er slechts één waarde in de matrix. Voor grote hoeveel heden gebruik wordt u aangeraden meerdere gebeurtenissen per publicatie op te nemen voor een hogere efficiëntie. Batches kunnen Maxi maal 1 MB groot zijn. Elke gebeurtenis mag nog steeds niet groter zijn dan 1 MB (preview-versie).