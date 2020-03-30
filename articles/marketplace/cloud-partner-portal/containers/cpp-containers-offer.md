---
title: Imageaanbieding azure containers | Azure Marketplace
description: Overzicht van het proces voor het publiceren van een containeraanbieding op de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: 9c0b4ca6e9a26f13d1539845ca9fb43f31a1a9dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281711"
---
# <a name="containers"></a>Containers

<table> <tr> <td>In deze sectie wordt uitgelegd hoe u een containerafbeelding publiceert naar de <a href="https://azuremarketplace.microsoft.com">Azure Marketplace.</a>  
Het type containeraanbieding ondersteunt Docker-containerafbeeldingen die zijn ingericht als <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes Service-exemplaren</a> of <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a> en worden gehost in een Azure Container <a href="https://docs.microsoft.com/azure/container-registry">Registry-opslagplaats.</a> </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Onderdelen aanbieden

In deze sectie worden de elementen van het publiceren van een container beschreven en is bedoeld als een handleiding voor de uitgever naar de Azure Marketplace. De publicatie is onderverdeeld in de volgende hoofdonderdelen:

- [Vereisten](./cpp-prerequisites.md) - geeft een overzicht van de technische en zakelijke vereisten voordat u een containeraanbieding maakt of publiceert.
- [Maak de aanbieding](./cpp-create-offer.md) - bevat de stappen die nodig zijn om een nieuwe containeraanbiedingsvermelding te maken met behulp van de Cloud Partner Portal.
- [Bereid de technische elementen voor](./cpp-create-technical-assets.md) - hoe u de technische elementen voor een containeroplossing maken als een aanbieding op de Azure Marketplace.
- [Publiceer de aanbieding](./cpp-publish-offer.md) - hoe u de publicatieaanbieding indienen bij de Azure Marketplace.

## <a name="container-publishing-process"></a>Publicatieproces voor containers

Het volgende diagram illustreert de stappen op hoog niveau bij het publiceren van een VM-aanbieding.
![Stappen voor het publiceren van een aanbieding](./media/containers-offer-process.png)

De stappen op hoog niveau voor het publiceren van een containeraanbieding zijn:

1. Maak de aanbieding - Geef gedetailleerde informatie over de aanbieding. Deze informatie omvat: de aanbiedingsbeschrijving, marketingmateriaal, ondersteuningsinformatie en assetspecificaties.
2. Zakelijke en technische assets maken - Maak de bedrijfsmiddelen (juridische documenten en marketingmaterialen) en technische elementen voor de bijbehorende oplossing (de containersafbeeldingen die worden gehost in een Azure Container Registry.
3. Maak de SKU - Maak de SKU(s) die aan de aanbieding zijn gekoppeld. Voor elke afbeelding die u wilt publiceren, is een unieke SKU vereist.
4. Certificeren en publiceren van de aanbieding - Nadat de aanbieding en de technische activa zijn voltooid, u de aanbieding indienen. Deze indiening start het publicatieproces. Tijdens dit proces wordt de oplossing getest, gevalideerd, gecertificeerd en vervolgens 'live' op de Azure Marketplace.

## <a name="next-steps"></a>Volgende stappen

Voordat u deze stappen overweegt, moet u voldoen aan de [technische en zakelijke vereisten](./cpp-prerequisites.md) voor het publiceren van een container naar de Microsoft Azure Marketplace.
