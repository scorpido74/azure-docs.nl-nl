---
title: Azure-toepassing vereisten aanbieden | Azure Marketplace
description: De vereisten voor het publiceren van een Azure-toepassings aanbieding op de Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 261af94e233bdb6189d7819f8f28c7e23b5dd112
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826148"
---
# <a name="azure-application-prerequisites"></a>Vereisten voor Azure-toepassingen

In dit artikel worden de technische en zakelijke vereisten beschreven voor het publiceren van een beheerde toepassings aanbieding op de Azure Marketplace.  Als u dit nog niet hebt gedaan, raadpleegt u de volgende informatie bronnen:
- Afhankelijk van uw SKU-type, kunt u [Azure-toepassingen: oplossings sjabloon bieden publicatie handleiding](../../marketplace-solution-templates.md) of [Azure-toepassingen: Managed Application aanbieding Publishing Guide (Engelstalig](../../marketplace-managed-apps.md) )
- [Oplossings sjablonen en beheerde toepassingen maken voor de video van Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)


## <a name="technical-requirements"></a>Technische vereisten

De technische vereisten omvatten de volgende items:

*   Azure Resource Manager sjablonen Zie [inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)voor meer informatie. In dit artikel wordt de structuur van een Azure Resource Manager sjabloon beschreven. Het biedt de verschillende secties van een sjabloon en de eigenschappen die beschikbaar zijn in deze secties. De sjabloon bestaat uit JSON en expressies die u kunt gebruiken om waarden voor uw implementatie samen te stellen. 
* Sjablonen voor Azure Quick Start.<br> Zie voor meer informatie:

  * [Azure-snelstartsjablonen](https://azure.microsoft.com/documentation/templates/). Implementeer Azure-bronnen via Azure Resource Manager met sjablonen die zijn aangeleverd door de community, zodat u meer gedaan krijgt. Met Azure Resource Manager kunt u uw toepassingen inrichten aan de hand van een declaratieve sjabloon. U kunt in één enkele sjabloon meerdere services plus de bijbehorende afhankelijkheden implementeren. U gebruikt dezelfde sjabloon om uw toepassing herhaaldelijk te implementeren in elke fase van de levenscyclus van de toepassing.
  * [Github: Azure Resource Manager Quick](https://github.com/azure/azure-quickstart-templates)start-sjablonen. Deze opslag plaats bevat alle momenteel beschik bare Azure Resource Manager sjablonen die door de community zijn bijgedragen. Er wordt een Doorzoek bare sjabloon Index onderhouden op https://azure.microsoft.com/documentation/templates/.
* UI-definitie maken<br>
Zie [Azure Portal gebruikers interface voor uw beheerde toepassing maken](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)voor meer informatie. In dit artikel worden de basis concepten van het bestand createUiDefinition. json geïntroduceerd. Het Azure Portal gebruikt dit bestand om de gebruikers interface te genereren voor het maken van een beheerde toepassing.


## <a name="business-requirements"></a>Zakelijke vereisten

De zakelijke vereisten omvatten de volgende procedures, contractuele en juridische verplichtingen:

* U moet een geregistreerde Cloud Marketplace-uitgever zijn. Als u niet bent geregistreerd, volgt u de stappen in het artikel [een Cloud Marketplace-uitgever worden](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>U moet hetzelfde micro soft Developer Center-registratie account gebruiken om u aan te melden bij de Cloud Partner-portal. U moet slechts één Microsoft-account voor uw Azure Marketplace-aanbiedingen hebben. Dit account mag niet specifiek zijn voor afzonderlijke services of aanbiedingen.

* Uw bedrijf (of een dochter onderneming) moet zich bevinden in een land/regio die wordt ondersteund door Azure Marketplace. Zie [Microsoft Azure Marketplace deelname beleid](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)voor een actuele lijst van deze landen/regio's.
* Uw product moet een licentie hebben op een manier die compatibel is met de facturerings modellen die worden ondersteund door de Azure Marketplace. Zie [facturerings opties](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) in azure Marketplace voor meer informatie.
* U bent zelf verantwoordelijk voor het maken van technische ondersteuning voor klanten op een commercieel redelijke manier. Deze ondersteuning kan gratis, betaald of via Community-benaderingen zijn.
* U bent zelf verantwoordelijk voor het licentiëren van uw software en eventuele software-afhankelijkheden van derden.
* U moet inhoud opgeven die voldoet aan de criteria voor uw aanbieding die worden vermeld op Azure Marketplace en in de Azure Portal.
* U moet akkoord gaan met de voor waarden van het Microsoft Azure Marketplace deelname beleid en de overeenkomst voor uitgevers.
* U moet voldoen aan de Microsoft Azure gebruiks voorwaarden van de website, de privacyverklaring van micro soft en Microsoft Azure Certified Program Agreement.


## <a name="publishing-requirements"></a>Publicatie vereisten

Als u een nieuwe Azure-toepassings aanbieding wilt publiceren, moet aan de volgende vereisten worden voldaan:

* Laat uw meta gegevens klaar voor gebruik. In de volgende lijst (niet-limitatief) ziet u een voor beeld van deze meta gegevens:
  * Een titel
  * Een beschrijving (in HTML-indeling)
  * Een logo afbeelding (in PNG-indeling) en in de volgende vaste afbeeldings grootten: 40 x 40 pixels, 90 x 90 pixels, 115 x 115 pixels en 255 x 115 pixels.
* *Gebruiks voorwaarden* en documenten met betrekking tot *Privacybeleid*
* Toepassings documentatie
* Ondersteunings contacten


## <a name="next-steps"></a>Volgende stappen

Zodra u aan alle vereisten hebt voldaan, bent u klaar om [een Azure-toepassings aanbieding te maken](./cpp-create-offer.md). 
 
