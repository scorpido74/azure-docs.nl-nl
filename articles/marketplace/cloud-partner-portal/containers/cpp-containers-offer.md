---
title: Aanbieding voor installatie kopieën van Azure containers | Azure Marketplace
description: Overzicht van het proces voor het publiceren van een container aanbod op de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: 81908de5c2fb3960684ed6cf37952e8815d8a5d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148300"
---
# <a name="containers"></a>Containers

> [!IMPORTANT]
> Vanaf 13 april 2020 gaan we het beheer van uw Azure-container aanbiedingen naar het partner centrum verplaatsen. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in [een Azure-container aanbieding maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) om uw gemigreerde aanbiedingen te beheren.

<table> <tr> <td>In deze sectie wordt uitgelegd hoe u een container installatie kopie publiceert naar de <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.  
Het type container aanbod ondersteunt docker-container installatie kopieën die zijn ingericht als <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes-service</a> -exemplaren of <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure container instances</a> en worden gehost in een <a href="https://docs.microsoft.com/azure/container-registry">Azure container Registry</a> opslag plaats. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Aanbiedings onderdelen

In deze sectie vindt u een overzicht van de elementen van het publiceren van een container en is bedoeld als een hand leiding voor de uitgever van de Azure Marketplace. De publicatie is onderverdeeld in de volgende hoofd onderdelen:

- [Vereisten: hier](./cpp-prerequisites.md) vindt u een lijst met technische en zakelijke vereisten voordat u een container aanbod maakt of publiceert.
- [De aanbieding maken](./cpp-create-offer.md) : een lijst met de stappen die nodig zijn om een nieuwe vermelding voor een container aanbieding te maken met behulp van de Cloud Partner-Portal.
- [De technische activa voorbereiden](./cpp-create-technical-assets.md) : de technische activa voor een container oplossing maken als aanbieding op de Azure Marketplace.
- [De aanbieding publiceren](./cpp-publish-offer.md) : de aanbieding voor publicatie naar Azure Marketplace verzenden.

## <a name="container-publishing-process"></a>Container publicatie proces

In het volgende diagram ziet u de stappen op hoog niveau bij het publiceren van een VM-aanbieding.
![Stappen voor het publiceren van een aanbieding](./media/containers-offer-process.png)

De stappen op hoog niveau voor het publiceren van een container aanbod zijn:

1. De aanbieding maken: Geef gedetailleerde informatie op over de aanbieding. Deze informatie omvat: de beschrijving, het marketing materiaal, de ondersteunings informatie en de Asset-specificaties van de aanbieding.
2. De bedrijfs-en technische activa maken: Maak de bedrijfs middelen (juridische documenten en marketing materialen) en technische activa voor de bijbehorende oplossing (de containers installatie kopieën die worden gehost in een Azure Container Registry.
3. De SKU maken: Maak de SKU ('s) die aan de aanbieding zijn gekoppeld. Er is een unieke SKU vereist voor elke installatie kopie die u wilt publiceren.
4. De aanbieding certificeren en publiceren: nadat de aanbieding en de technische middelen zijn voltooid, kunt u de aanbieding indienen. Met deze inzending wordt het publicatie proces gestart. Tijdens dit proces wordt de oplossing getest, gevalideerd, gecertificeerd en ' gaat live ' op de Azure Marketplace.

## <a name="next-steps"></a>Volgende stappen

Voordat u deze stappen overweegt, moet u voldoen aan de [technische en zakelijke vereisten](./cpp-prerequisites.md) voor het publiceren van een container naar de Microsoft Azure Marketplace.
