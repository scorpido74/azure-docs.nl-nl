---
title: Azure-toepassingsaanbieding | Azure Marketplace
description: Overzicht van het proces voor het publiceren van een Azure Application-aanbieding op de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: dsindona
ms.openlocfilehash: ed086ffdc49e21b819c0ee05b38ad882b4e269d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285313"
---
# <a name="azure-application-offer"></a>Aanbieding voor Azure-toepassing

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> In deze sectie wordt uitgelegd hoe u een nieuwe Azure-toepassingsaanbieding publiceert naar de [Azure Marketplace.](https://azuremarketplace.microsoft.com)  Elke Azure-toepassing bevat een Azure Resource Manager-sjabloon die alle technische elementen definieert die door de toepassing worden gebruikt, die doorgaans een of meer virtuele machines en andere ondersteunende Azure- of Webservices bevat. Alle Azure-app-aanbiedingen moeten toegangsbeveiliging inschakelen via [Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/)  </div> | ![Pictogram Azure-apps](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Publicatieoverzicht

De volgende video, [oplossingssjablonen voor bouwen en beheerde toepassingen voor de Azure Marketplace,](https://channel9.msdn.com/Events/Build/2018/BRK3603)is een inleiding: welke aanbiedingstypen beschikbaar zijn, welke technische assets nodig zijn, hoe u een Azure Resource Manager-sjabloon maakt, de gebruikersinterface van de app ontwikkelt en test, hoe u de app-aanbieding publiceert en het beoordelingsproces van de app.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Typen Azure-toepassingen

Er zijn twee soorten Azure-toepassingen: beheerde toepassingen en oplossingssjablonen. 

- Oplossingssjablonen zijn een van de belangrijkste manieren om een oplossing in de Marketplace te publiceren. Dit aanbiedingstype wordt gebruikt wanneer uw oplossing extra implementatie- en configuratieautomatisering vereist buiten één virtuele machine (VM). U het leveren van meer dan één VM automatiseren met behulp van een oplossingssjabloon. Deze automatisering omvat het inrichten van netwerk- en opslagbronnen om complexe IaaS-oplossingen te bieden. Zie [Azure Applications: oplossingssjablonen](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates)voor een overzicht van de vereisten voor oplossingssjablonen en het factureringsmodel.

- Beheerde toepassingen zijn vergelijkbaar met oplossingssjablonen, met één belangrijk verschil. In een beheerde toepassing worden de resources geïmplementeerd vanuit een resourcegroep die wordt beheerd door de uitgever van de app. De resourcegroep is opgenomen in het abonnement van de consument, maar een identiteit in de tenant van de uitgever heeft toegang tot de resourcegroep. De uitgever bepaalt de kosten voor de voortdurende ondersteuning van de oplossing. Gebruik Azure Managed-toepassingen om eenvoudig volledig beheerde, turnkey-toepassingen aan uw klanten te bouwen en te leveren.

Naast de Azure Marketplace u ook beheerde toepassingen aanbieden in een servicecatalogus. De servicecatalogus is een interne catalogus van goedgekeurde oplossingen voor gebruikers in een organisatie. U gebruikt de catalogus om te voldoen aan de organisatienormen en tegelijkertijd oplossingen aan te bieden voor groepen in een organisatie. Werknemers gebruiken de catalogus om eenvoudig toepassingen te vinden die worden aanbevolen en zijn goedgekeurd door hun IT-afdelingen.

>[!Note]
>Cloud Solution Providers (CSP) partnerkanaal opt-in is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](../../cloud-solution-providers.md) voor meer informatie over het op de markt brengen van uw aanbieding via de Microsoft CSP-partnerkanalen.

Zie het overzicht van [beheerde toepassingen](https://docs.microsoft.com/azure/managed-applications/overview)voor Azure voor meer informatie over de voordelen en typen beheerde toepassingen.


## <a name="publishing-process-workflow"></a>Werkstroom voor publicatieproces

In het volgende diagram ziet u het proces op hoog niveau voor het publiceren van een Azure-toepassingsaanbieding.

![Werkstroom voor publicatieaanbieding](./media/new-offer-process.png)

De stappen op hoog niveau voor het publiceren van een Azure-toepassingsaanbieding zijn:

1. Voldoe aan [de vereisten](./cpp-prerequisites.md) - (Niet weergegeven) Controleer of u hebt voldaan aan de zakelijke en technische vereisten voor het publiceren van een Azure-app naar de Azure Marketplace. 

1. [Maak de aanbieding](./cpp-create-offer.md) - Geef gedetailleerde informatie over de aanbieding. Deze informatie omvat: de aanbiedingsbeschrijving, marketingmateriaal, ondersteuningsinformatie en assetspecificaties.

1. [Bestaande bedrijfs- en technische activa maken of verzamelen](./cpp-create-technical-assets.md) - Maak de bedrijfsmiddelen (juridische documenten en marketingmaterialen) en technische activa voor de bijbehorende oplossing.

1. [Maak de SKU](./cpp-skus-tab.md) - Maak de SKU(s) die aan de aanbieding zijn gekoppeld. Voor elke afbeelding die u wilt publiceren, is een unieke SKU vereist.

1. Certificeren en [publiceren van de aanbieding](./cpp-publish-offer.md) - Nadat de aanbieding en de technische activa zijn voltooid, u de aanbieding indienen. Deze indiening start het publicatieproces. Tijdens dit proces wordt de oplossing getest, gevalideerd, gecertificeerd en vervolgens 'live' op de Azure Marketplace.

## <a name="next-steps"></a>Volgende stappen

Voordat u deze stappen overweegt, moet u voldoen aan de [technische en zakelijke vereisten](./cpp-prerequisites.md) voor het publiceren van een beheerde toepassing naar de Microsoft Azure Marketplace.
