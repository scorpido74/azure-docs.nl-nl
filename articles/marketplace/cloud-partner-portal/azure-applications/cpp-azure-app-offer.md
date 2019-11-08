---
title: Azure-toepassing aanbieding | Azure Marketplace
description: Overzicht van het proces voor het publiceren van een Azure-toepassing aanbieding op de Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pabutler
ms.openlocfilehash: 9125b5c71b63b27c58ea72b7bfd49f730854b33d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818793"
---
# <a name="azure-application-offer"></a>Aanbieding voor Azure-toepassing

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> In deze sectie wordt uitgelegd hoe u een nieuwe Azure-toepassings aanbieding publiceert op de [Azure Marketplace](https://azuremarketplace.microsoft.com).  Elke Azure-toepassing bevat een Azure Resource Manager sjabloon waarmee alle technische middelen worden gedefinieerd die door de toepassing worden gebruikt. Dit omvat meestal een of meer virtuele machines en andere ondersteunende Azure-of webservices. Alle aanbiedingen van Azure app moeten toegang tot beveiliging mogelijk maken via [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Pictogram Azure-apps](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Overzicht van publiceren

De volgende video, het [bouwen van oplossings sjablonen en beheerde toepassingen voor de Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), is een inleiding: welke aanbiedings typen beschikbaar zijn, welke technische middelen zijn vereist, hoe u een Azure Resource Manager sjabloon ontwerpt, ontwikkelt en testen van de app-gebruikers interface, het publiceren van de app-aanbieding en het beoordelings proces van de app.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Typen Azure-toepassingen

Er zijn twee soorten Azure-toepassingen: beheerde toepassingen en oplossings sjablonen. 

- Oplossings sjablonen zijn een van de belangrijkste manieren om een oplossing op Marketplace te publiceren. Dit aanbiedings type wordt gebruikt wanneer voor uw oplossing extra implementatie-en configuratie automatisering is vereist dan één virtuele machine (VM). U kunt het leveren van meer dan één virtuele machine automatiseren met behulp van een oplossings sjabloon. Deze automatisering omvat het inrichten van netwerk-en opslag bronnen om complexe IaaS-oplossingen te bieden. Zie [Azure Applications: oplossings sjablonen](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates)voor een overzicht van de vereisten van de oplossings sjabloon en het facturerings model.

- Beheerde toepassingen zijn vergelijkbaar met oplossings sjablonen, met één belang rijk verschil. In een beheerde toepassing worden de resources geïmplementeerd vanuit een resourcegroep die wordt beheerd door de uitgever van de app. De resourcegroep is opgenomen in het abonnement van de consument, maar een identiteit in de tenant van de uitgever heeft toegang tot de resourcegroep. De uitgever bepaalt de kosten voor de voortdurende ondersteuning van de oplossing. Gebruik Azure Managed Applications om eenvoudig volledig beheerde, kant-en-klare toepassingen te bouwen en te leveren aan uw klanten.

Naast Azure Marketplace kunt u ook beheerde toepassingen aanbieden in een service catalogus. De servicecatalogus is een interne catalogus van goedgekeurde oplossingen voor gebruikers in een organisatie. U gebruikt de catalogus om te voldoen aan de organisatie standaarden en biedt oplossingen voor groepen in een organisatie. Werknemers gebruiken de catalogus om eenvoudig toepassingen te vinden die worden aanbevolen en zijn goedgekeurd door hun IT-afdelingen.

>[!Note]
>Opt-in voor Cloud Solution Providers (CSP)-partner kanaal is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](../../cloud-solution-providers.md) voor meer informatie over het marketing gebruik van uw aanbieding via de micro soft CSP-partner kanalen.

Zie het overzicht van door [Azure beheerde toepassingen](https://docs.microsoft.com/azure/managed-applications/overview)voor meer informatie over de voor-en andere typen beheerde toepassingen.


## <a name="publishing-process-workflow"></a>Proces werk stroom publiceren

In het volgende diagram ziet u het proces op hoog niveau voor het publiceren van een Azure-toepassings aanbieding.

![Werk stroom voor het publiceren van aanbiedingen](./media/new-offer-process.png)

De stappen op hoog niveau voor het publiceren van een Azure-toepassings aanbieding zijn:

1. Voldoen aan de [vereisten](./cpp-prerequisites.md) -(niet weer gegeven) Controleer of u aan de zakelijke en technische vereisten hebt voldaan voor het publiceren van een Azure-app naar de Azure Marketplace. 

1. [De aanbieding maken](./cpp-create-offer.md) : Geef gedetailleerde informatie op over de aanbieding. Deze informatie omvat: de beschrijving, het marketing materiaal, de ondersteunings informatie en de Asset-specificaties van de aanbieding.

1. [Bestaande bedrijfs-en technische activa maken of verzamelen](./cpp-create-technical-assets.md) : Maak de bedrijfs middelen (juridische documenten en marketing materiaal) en technische activa voor de bijbehorende oplossing.

1. [De SKU maken](./cpp-skus-tab.md) : Maak de SKU ('s) die aan de aanbieding zijn gekoppeld. Er is een unieke SKU vereist voor elke installatie kopie die u wilt publiceren.

1. [De aanbieding](./cpp-publish-offer.md) certificeren en publiceren: nadat de aanbieding en de technische middelen zijn voltooid, kunt u de aanbieding indienen. Met deze inzending wordt het publicatie proces gestart. Tijdens dit proces wordt de oplossing getest, gevalideerd, gecertificeerd en ' gaat live ' op de Azure Marketplace.

## <a name="next-steps"></a>Volgende stappen

Voordat u deze stappen overweegt, moet u voldoen aan de [technische en zakelijke vereisten](./cpp-prerequisites.md) voor het publiceren van een beheerde toepassing naar de Microsoft Azure Marketplace.
