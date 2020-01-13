---
title: Peering met micro soft instellen
titleSuffix: Azure
description: Overzicht van peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 576bc3e37711851acd7d6c7ac811a10e40080710
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75908921"
---
# <a name="internet-peering-overview"></a>Overzicht van Internet peering

Peering is de onderlinge verbinding tussen het wereld wijde netwerk (AS8075) van micro soft en uw netwerk voor het uitwisselen van Internet verkeer van/naar micro soft onlineservices en Microsoft Azure Services. Vervoerders of service providers kunnen vragen om verbinding te maken met micro soft op een van onze Edge-locaties. Elke aanvraag wordt door micro soft gecontroleerd om te controleren of deze voldoet aan ons beleid voor peering. U kunt op twee manieren een peering met het micro soft-netwerk instellen:

* **Directe peering:**

    Peering wordt tot stand gebracht via directe fysieke verbindingen tussen micro soft-netwerken in een micro soft Edge en uw netwerk. BGP-sessies worden geconfigureerd via deze verbindingen volgens ons routerings beleid en met behulp van de vooraf-onderhandelde overeenkomst. Dit wordt ook wel PNI genoemd.

* **Exchange-peering:**

    Dit verwijst naar de standaard open bare peering-verbindingen op Internet Exchange (IX). De fysieke verbindingen tussen het micro soft-netwerk en uw netwerk bevinden zich via de switch structuur die wordt geëxploiteerd door de IX. BGP-sessies worden geconfigureerd met behulp van de IP-ruimte die wordt opgegeven door de IX.

## <a name="benefits-of-peering-with-microsoft"></a>Voor delen van peering met micro soft
* Verlaag uw doorvoer kosten door micro soft-verkeer te leveren met behulp van peering met micro soft.
* Verbeter de prestaties van uw klanten door de netwerk-hops en latentie naar het micro soft Edge-netwerk te verminderen.
* Bescherm klant verkeer tegen storingen in het netwerk van uw netwerk of door Voer van de provider door te koppelen aan micro soft op redundante locaties.
* Meer informatie over de prestaties van uw peering-verbindingen en hoe u inzichten kunt gebruiken om problemen met uw netwerk op te lossen.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Voor delen van het gebruik van Azure voor het instellen van peering

U kunt een aanvraag indienen voor peering met behulp van Azure PowerShell of portal van micro soft. Peering is op deze manier ingesteld als een Azure-resource en biedt de volgende voor delen:
* Vereenvoudigde en geautomatiseerde stappen voor het instellen en beheren van peering met micro soft.
* Snelle en eenvoudige manier om al uw peerings op één plek weer te geven en te beheren.
* Houd de status-en bandbreedte gegevens voor al uw verbindingen bij.
* U kunt hetzelfde abonnement gebruiken om toegang te krijgen tot uw Azure-Cloud Services.

Als u al met micro soft gevestigde peerings hebt, worden deze **verouderde peerings**genoemd. U kunt ervoor kiezen om dergelijke peerings als Azure-resource te beheren, zodat u optimaal gebruik kunt maken van de bovenstaande voor delen. Volg de koppelingen in de **volgende stappen** hieronder om een nieuwe peering-aanvraag in te dienen of verouderde peering te converteren naar een Azure-resource.

## <a name="peering-policy"></a>Peering-beleid
Micro soft heeft een selectief, maar heeft het beleid voor peering doorgaans geopend. Peers worden geselecteerd op basis van de prestaties, de mogelijkheden en de plaats waar er sprake is van wederzijds voor deel en zijn onderworpen aan bepaalde technische, commerciële en juridische vereisten. Zie [beleid voor peering](policy.md)voor meer informatie.

## <a name="faq"></a>Veelgestelde vragen
Zie [Internet peering-faq's](faqs.md)(Engelstalig) voor veelgestelde vragen over peering.

## <a name="next-steps"></a>Volgende stappen

* Voor meer informatie over de stappen om direct peering in te stellen met micro soft, volgt u het [scenario voor directe peering](walkthrough-direct-all.md)
* Voor meer informatie over de stappen voor het instellen van Exchange-peering met micro soft, volgt u de [Stapsgewijze instructies](walkthrough-exchange-all.md) voor het uitwisselen van Exchange
* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](https://docs.microsoft.com/azure/networking/networking-overview) van Azure.
