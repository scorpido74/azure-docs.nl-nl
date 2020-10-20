---
title: DevOps voor Azure API Management het gebruik van ARM-sjablonen
description: Inleiding tot API DevOps met Azure API Management, met behulp van Azure Resource Manager-sjablonen voor het beheren van API-implementaties in een CI/CD-pijp lijn
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 144baa0944451adaf08f0df84325b58079e19d65
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209715"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>CI/CD voor API Management met behulp van Azure Resource Manager-sjablonen

In dit artikel vindt u een inleiding tot API DevOps met Azure API Management, met behulp van Azure Resource Manager-sjablonen. Met de strategische waarde van Api's is een pijp lijn voor continue integratie (CI) en continue implementatie (CD) een belang rijk aspect van API-ontwikkeling geworden. Hiermee kunnen organisaties de implementatie van API-wijzigingen zonder fout gevoelige hand matige stappen automatiseren, problemen eerder detecteren en uiteindelijk waarde leveren aan eind gebruikers. 

Zie voor meer informatie, hulpprogram ma's en code voorbeelden voor het implementeren van de DevOps-benadering die in dit artikel wordt beschreven, de open-source [Azure API Management DevOps Resource Kit](https://github.com/Azure/azure-api-management-devops-resource-kit) in github. Omdat klanten een breed scala aan technische cult uren en bestaande automatiserings oplossingen meebrengen, is de aanpak niet een oplossing met één omvang.

## <a name="the-problem"></a>Het probleem

Organisaties hebben normaal gesp roken meerdere implementatie omgevingen (zoals ontwikkelen, testen, productie) en gebruiken afzonderlijke API Management instanties voor elke omgeving. Sommige instanties worden gedeeld door meerdere ontwikkel teams, die verantwoordelijk zijn voor verschillende Api's met verschillende release-cadences.

Als gevolg hiervan hebben klanten de volgende uitdagingen:

* Hoe kan ik de implementatie van Api's automatiseren in API Management?
* Configuraties van de ene omgeving naar de andere migreren
* Hoe voorkom ik storingen tussen verschillende ontwikkel teams die hetzelfde API Management exemplaar delen?

## <a name="manage-configurations-in-resource-manager-templates"></a>Configuraties beheren in Resource Manager-sjablonen

De voorgestelde benadering wordt geïllustreerd in de volgende afbeelding. 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="DevOps met API Management":::

In dit voor beeld zijn er twee implementatie omgevingen: *ontwikkeling* en *productie*. Elk heeft een eigen API Management exemplaar. 

* API-Ontwikkel aars hebben toegang tot de ontwikkelings instantie en kunnen deze gebruiken voor het ontwikkelen en testen van hun Api's. 
* Het productie-exemplaar wordt beheerd door een aangewezen team dat de *API-uitgevers*wordt genoemd.

De sleutel in deze voorgestelde benadering is om alle API Management configuraties in [Azure Resource Manager sjablonen](../azure-resource-manager/resource-group-authoring-templates.md)te blijven gebruiken. Deze sjablonen moeten worden bewaard in een broncode beheer systeem zoals git. Zoals u in de afbeelding ziet, is er een Publisher-opslag plaats met alle configuraties van de productie-API Management exemplaar in een verzameling sjablonen:

|Template  |Beschrijving  |
|---------|---------|
|Servicesjabloon     | Configuraties op service niveau van het API Management-exemplaar, zoals prijs categorie en aangepaste domeinen         |
|Gedeelde sjablonen     |  Gedeelde bronnen in een API Management-exemplaar, zoals groepen, producten en Logboeken    |
|API-sjablonen     |  Configuraties van Api's en hun subresources: bewerkingen, beleids regels, diagnostische instellingen        |
|Hoofd sjabloon (hoofd)     |   BIND alles samen door te [koppelen](../azure-resource-manager/resource-group-linked-templates.md) aan alle sjablonen en te implementeren in de juiste volg orde. Als u alle configuraties wilt implementeren in een API Management-exemplaar, implementeert u de hoofd sjabloon. Elke sjabloon kan ook afzonderlijk worden geïmplementeerd.       |

API-Ontwikkel aars splitsen de opslag plaats van de uitgever naar een ontwikkelaars opslagplaats en werken aan de wijzigingen voor hun Api's. In de meeste gevallen zijn ze gericht op de API-sjablonen voor hun Api's en hoeven ze de gedeelde of service sjablonen niet te wijzigen.

## <a name="migrate-configurations-to-templates"></a>Configuraties migreren naar sjablonen
Er zijn uitdagingen voor API-Ontwikkel aars bij het werken met Resource Manager-sjablonen:

* API-Ontwikkel aars werken vaak met de [OpenAPI-specificatie](https://github.com/OAI/OpenAPI-Specification) en zijn mogelijk niet bekend met Resource Manager-schema's. Het hand matig ontwerpen van sjablonen kan fout gevoelig zijn. 

   Een hulp programma met de naam [**Maker**](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) in de Resource Kit kan helpen het maken van API-sjablonen te automatiseren op basis van een bestand met een open API-specificatie. Ontwikkel aars kunnen bovendien API Management-beleid opgeven voor een API in XML-indeling. 

* Voor klanten die al API Management gebruiken, is een andere uitdaging om bestaande configuraties uit te pakken in Resource Manager-sjablonen. Voor deze klanten kan een hulp programma dat [**Extractor**](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) in de Resource Kit wordt genoemd, helpen bij het genereren van sjablonen door configuraties te extra heren uit hun API Management exemplaren.  

## <a name="workflow"></a>Werkstroom

* Zodra API-Ontwikkel aars klaar zijn met het ontwikkelen en testen van een API en de API-sjablonen hebben gegenereerd, kunnen ze een pull-aanvraag indienen om de wijzigingen samen te voegen in de opslag plaats van de uitgever. 

* API-uitgevers kunnen de pull-aanvraag valideren en er zeker van zijn dat de wijzigingen veilig en compatibel zijn. Ze kunnen bijvoorbeeld controleren of alleen HTTPS mag communiceren met de API. De meeste validaties kunnen worden geautomatiseerd als een stap in de CI/CD-pijp lijn.

* Zodra de wijzigingen zijn goedgekeurd en samengevoegd, kunnen API-uitgevers ervoor kiezen deze op schema of op aanvraag te implementeren naar het productie-exemplaar. De implementatie van de sjablonen kan worden geautomatiseerd met behulp van [github-acties](https://github.com/Azure/apimanagement-devops-samples), [Azure-pijp lijnen](/devops/pipelines/), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md), [Azure cli](../azure-resource-manager/templates/deploy-cli.md)of andere hulpprogram ma's.

Met deze methode kan de implementatie van API-wijzigingen in API Management-instanties worden geautomatiseerd en is het eenvoudig om wijzigingen van de ene omgeving naar de andere te promo veren. Omdat verschillende API-Ontwikkel teams met verschillende sets van API-sjablonen en-bestanden werken, wordt er geen interferentie tussen verschillende teams voor komen.

## <a name="video"></a>Video

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de open-source [Azure API Management DevOps Resource Kit](https://github.com/Azure/azure-api-management-devops-resource-kit) voor meer informatie, hulpprogram ma's en voorbeeld sjablonen.