---
title: Peering instellen met Microsoft
titleSuffix: Azure
description: Overzicht van peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 576bc3e37711851acd7d6c7ac811a10e40080710
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75908921"
---
# <a name="internet-peering-overview"></a>Overzicht van internetpeering

Peering is de koppeling tussen het wereldwijde netwerk van Microsoft (AS8075) en uw netwerk met het oog op het uitwisselen van internetverkeer van/naar microsoft online services en Microsoft Azure Services. Providers of serviceproviders kunnen op elk van onze Edge-locaties contact opnemen met Microsoft. Elk verzoek wordt beoordeeld door Microsoft om ervoor te zorgen dat het voldoet aan ons peeringbeleid. U een peering met microsoft-netwerk op twee manieren instellen:

* **Direct peering:**

    Peering wordt tot stand gebracht via directe fysieke verbindingen tussen microsoft-netwerk op een Microsoft Edge en uw netwerk. BGP-sessies worden geconfigureerd voor deze verbindingen volgens ons routeringsbeleid en met behulp van vooraf onderhandelde overeenkomst. Dit wordt ook wel PNI genoemd.

* **Exchange-peering:**

    Dit verwijst naar standaard openbare peeringverbindingen bij Internet Exchanges (IX). De fysieke verbindingen tussen het Microsoft-netwerk en uw netwerk zijn via switchfabric die door de IX wordt beheerd. BGP-sessies worden geconfigureerd met behulp van IP-ruimte die door de IX wordt geleverd.

## <a name="benefits-of-peering-with-microsoft"></a>Voordelen van peering met Microsoft
* Verlaag uw transitkosten door Microsoft-verkeer te leveren met behulp van peering met Microsoft.
* Verbeter de prestaties voor uw klanten door de netwerkhop en latentie naar het Microsoft Edge-netwerk te verminderen.
* Bescherm het verkeer van klanten tegen storingen in het netwerk of het netwerk van de transitprovider door met Microsoft te turen op redundante locaties.
* Leer prestatiestatistieken over je peering-verbindingen en gebruik inzichten om problemen met je netwerk op te lossen.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Voordelen van het gebruik van Azure voor het instellen van peering

U vragen om peering met Microsoft met behulp van Azure PowerShell of portal. Peering die op deze manier is ingesteld, wordt beheerd als een Azure-bron en biedt de volgende voordelen:
* Vereenvoudigde en automatiseerbare stappen voor het instellen en beheren van peering met Microsoft.
* Snelle en eenvoudige manier om al uw peerings op één plek te bekijken en te beheren.
* Houd status- en bandbreedtegegevens bij voor al uw verbindingen.
* U hetzelfde abonnement gebruiken om toegang te krijgen tot uw Azure Cloud Services.

Als u al peerings met Microsoft hebt ingesteld, worden ze aangeduid als **legacy-peerings.** U ervoor kiezen om dergelijke peerings als Azure-bron te beheren om te profiteren van de bovenstaande voordelen. Als u een nieuwe peeringaanvraag wilt indienen of oudere peering wilt converteren naar Azure-bron, volgt u de koppelingen in het gedeelte **Volgende stappen** hieronder.

## <a name="peering-policy"></a>Peeringbeleid
Microsoft heeft een selectief, maar over het algemeen open peeringbeleid. Peers worden geselecteerd op basis van prestaties, capaciteit en waar er wederzijds voordeel is, en zijn onderworpen aan bepaalde technische, commerciële en juridische vereisten. Zie [peeringbeleid](policy.md)voor meer informatie .

## <a name="faq"></a>Veelgestelde vragen
Voor veelgestelde vragen over peering, zie [Internet peering - VEELGESTELDE VRAGEN](faqs.md).

## <a name="next-steps"></a>Volgende stappen

* Volg [Direct peering walkthrough](walkthrough-direct-all.md) voor meer informatie over stappen voor het instellen van Direct-peering met Microsoft
* Volg [Exchange-peering-walkthrough](walkthrough-exchange-all.md) voor meer informatie over stappen voor het instellen van Exchange-peering met Microsoft
* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](https://docs.microsoft.com/azure/networking/networking-overview) van Azure.
