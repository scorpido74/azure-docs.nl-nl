---
title: Azure Application biedt vereisten | Azure Marketplace
description: De vereisten voor het publiceren van een Azure-toepassingsaanbieding op de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b927404758dc59419fb3c5e638ac32758d534681
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281745"
---
# <a name="azure-application-prerequisites"></a>Vereisten voor Azure-toepassingen

In dit artikel worden de technische en zakelijke vereisten beschreven voor het publiceren van een beheerde toepassingsaanbieding op de Azure Marketplace.  Als u dit nog niet hebt gedaan, bekijkt u de volgende informatiebronnen:
- Afhankelijk van uw SKU-type, [azure-toepassingen: publicatiegids voor oplossingssjablonen of](../../marketplace-solution-templates.md) [Azure-toepassingen: publicatiegids voor beheerde toepassingsaanbiedingen](../../marketplace-managed-apps.md)
- [Oplossingssjablonen en beheerde toepassingen voor de Azure Marketplace-video](https://channel9.msdn.com/Events/Build/2018/BRK3603) bouwen


## <a name="technical-requirements"></a>Technische vereisten

De technische voorschriften omvatten de volgende punten:

*   Azure Resource Manager-sjablonen Zie [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)voor meer informatie. In dit artikel wordt de structuur van een Azure Resource Manager-sjabloon beschreven. Het presenteert de verschillende secties van een sjabloon en de eigenschappen die beschikbaar zijn in deze secties. De sjabloon bestaat uit JSON en expressies die u gebruiken om waarden voor uw implementatie te construeren. 
* Azure Quickstart-sjablonen.<br> Zie voor meer informatie:

  * [Azure-snelstartsjablonen](https://azure.microsoft.com/documentation/templates/). Implementeer Azure-resources via Azure Resource Manager met door de community bijgedragen sjablonen om meer gedaan te krijgen. Met Azure Resource Manager kunt u uw toepassingen inrichten aan de hand van een declaratieve sjabloon. U kunt in één enkele sjabloon meerdere services plus de bijbehorende afhankelijkheden implementeren. U gebruikt dezelfde sjabloon om uw toepassing herhaaldelijk te implementeren in elke fase van de levenscyclus van de toepassing.
  * [GitHub: Quickstart-sjablonen voor Azure Resource Manager](https://github.com/azure/azure-quickstart-templates). Deze repo bevat alle momenteel beschikbare Azure Resource Manager-sjablonen die door de community zijn bijgedragen. Een doorzoekbare sjabloonindex https://azure.microsoft.com/documentation/templates/wordt gehandhaafd op .
* UI-definitie maken<br>
Zie [Gebruikersinterface van Azure portal maken voor uw beheerde toepassing voor](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)meer informatie. Dit artikel introduceert de kernconcepten van het bestand createUiDefinition.json. De Azure-portal gebruikt dit bestand om de gebruikersinterface te genereren voor het maken van een beheerde toepassing.


## <a name="business-requirements"></a>Zakelijke vereisten

De zakelijke vereisten omvatten de volgende procedurele, contractuele en wettelijke verplichtingen:

* Je moet een geregistreerde Cloud Marketplace-uitgever zijn. Als je niet bent geregistreerd, volg je de stappen in het artikel [Word een Cloud Marketplace Publisher](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>U moet hetzelfde Microsoft Developer Center-registratieaccount gebruiken om u aan te melden bij de Cloud Partner Portal. U moet slechts één Microsoft-account hebben voor uw Azure Marketplace-aanbiedingen. Dit account mag niet specifiek zijn voor individuele services of aanbiedingen.

* Uw bedrijf (of zijn dochteronderneming) moet zich in een verkoop-uit-land/regio bevinden die wordt ondersteund door de Azure Marketplace. Zie [Microsoft Azure Marketplace-deelnamebeleid](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)voor een actuele lijst met deze landen/regio's .
* Uw product moet een licentie hebben op een manier die compatibel is met factureringsmodellen die worden ondersteund door de Azure Marketplace. Zie [factureringsopties](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) in de Azure Marketplace voor meer informatie.
* Je bent verantwoordelijk voor het op een commercieel redelijke manier beschikbaar maken van technische ondersteuning aan klanten. Deze ondersteuning kan gratis, betaald of via communitybenaderingen zijn.
* U bent verantwoordelijk voor het verlenen van licenties voor uw software en eventuele softwareafhankelijkheden van derden.
* U moet inhoud leveren die voldoet aan de criteria voor uw aanbod om te worden aangeboden op Azure Marketplace en in de Azure-portal.
* U moet akkoord gaan met de voorwaarden van de Microsoft Azure Marketplace-deelnamebeleid en uitgeversovereenkomst.
* U moet voldoen aan de gebruiksvoorwaarden van de Microsoft Azure-website, de privacyverklaring van Microsoft en de Microsoft Azure Certified Program Agreement.


## <a name="publishing-requirements"></a>Publicatievereisten

Als u een nieuwe Azure-toepassingsaanbieding wilt publiceren, moet u aan de volgende vereisten voldoen:

* Heb je metadata klaar voor gebruik. De volgende lijst (niet-uitputtend) toont een voorbeeld van deze metagegevens:
  * Een titel
  * Een beschrijving (in HTML-indeling)
  * Een logoafbeelding (in PNG-formaat) en in deze vaste afbeeldingsformaten: 40 x 40 pixels, 90 x 90 pixels, 115 x 115 pixels en 255 x 115 pixels.
* Een *gebruiksvoorwaarden* en een *privacybeleid*
* Toepassingsdocumentatie
* Contactpersonen voor ondersteuning


## <a name="next-steps"></a>Volgende stappen

Zodra u aan alle vereisten hebt voldaan, bent u klaar om [een Azure-toepassingsaanbieding](./cpp-create-offer.md)te maken. 
 
