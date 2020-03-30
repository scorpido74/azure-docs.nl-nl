---
title: Een Service Fabric Mesh-toepassing verplaatsen naar een andere regio
description: U de mesh-bronnen van Service Fabric verplaatsen door een kopie van uw huidige sjabloon te implementeren naar een nieuwe Azure-regio.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908161"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Een Service Fabric-mesh-toepassing verplaatsen naar een ander Azure-gebied

In dit artikel wordt beschreven hoe u uw Service Fabric Mesh-toepassing en de bijbehorende resources verplaatst naar een andere Azure-regio. U uw resources om een aantal redenen naar een andere regio verplaatsen. Bijvoorbeeld als reactie op uitval, om functies of services te verkrijgen die alleen beschikbaar zijn in specifieke regio's, om te voldoen aan interne beleids- en governancevereisten of om te voldoen aan de vereisten voor capaciteitsplanning.

 [Service Fabric Mesh biedt geen ondersteuning voor](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) de mogelijkheid om resources rechtstreeks over Azure-regio's te verplaatsen. U bronnen echter indirect verplaatsen door een kopie van uw huidige Azure Resource Manager-sjabloon te implementeren naar het nieuwe doelgebied en vervolgens ingressverkeer en afhankelijkheden om te leiden naar de nieuw gemaakte Service Fabric Mesh-toepassing.

## <a name="prerequisites"></a>Vereisten

* Ingress controller (zoals [Application Gateway)](https://docs.microsoft.com/azure/application-gateway/)om te dienen als tussenpersoon voor het routeren van verkeer tussen clients en uw Service Fabric Mesh-toepassing
* Beschikbaarheid van ServiceFabric Mesh (Preview) in`westus` `eastus`de `westeurope`doelregio Azure ( , of )

## <a name="prepare"></a>Voorbereiden

1. Maak een momentopname van de huidige status van uw Service Fabric Mesh-toepassing door de sjabloon Azure Resource Manager en parameters uit de meest recente implementatie te exporteren. Volg hiervoor de stappen in [De sjabloon Exporteren na implementatie](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) met de Azure-portal. U ook [Azure CLI,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)of [REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)gebruiken.

2. Exporteer indien van toepassing [andere resources in dezelfde resourcegroep](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group) voor herplaatsing in het doelgebied.

3. Controleer (en bewerk, indien nodig) de geëxporteerde sjabloon om ervoor te zorgen dat de bestaande eigenschapswaarden de waarden zijn die u in het doelgebied wilt gebruiken. De `location` nieuwe parameter (Azure-regio) is een parameter die u tijdens de herschikking levert.

## <a name="move"></a>Verplaatsen

1. Maak een nieuwe resourcegroep (of gebruik een bestaande) in de doelregio.

2. Volg met uw geëxporteerde sjabloon de stappen in [Resources implementeren van aangepaste sjabloon](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) met behulp van de Azure-portal. U ook [Azure CLI,](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli) [Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)of [REST API](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest)gebruiken.

3. Raadpleeg voor richtlijnen voor het verplaatsen van gerelateerde resources zoals [Azure Storage-accounts](../storage/common/storage-account-move.md)naar richtlijnen voor afzonderlijke services die worden vermeld onder het onderwerp [Azure-resources verplaatsen tussen regio's](../azure-resource-manager/management/move-region.md).

## <a name="verify"></a>Verifiëren

1. Wanneer de implementatie is voltooid, test u het eindpunt(en) van de toepassing om de functionaliteit van uw toepassing te controleren.

2. U ook de status van uw toepassing verifiëren door de toepassingsstatus[(az mesh-app show)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)te controleren en de toepassingslogboeken en ([az mesh-package-log)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)opdrachten te bekijken met behulp van de [Azure Service Fabric Mesh CLI](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Doorvoeren

Zodra u de gelijkwaardige functionaliteit van uw Service Fabric Mesh-toepassing in het doelgebied hebt bevestigd, configureert u uw invallende controller (bijvoorbeeld [Application Gateway)](../application-gateway/redirect-overview.md)om verkeer om te leiden naar de nieuwe toepassing.

## <a name="clean-up-source-resources"></a>Bronbronnen opschonen

Als u de verplaatsing van de toepassing Service Fabric Mesh wilt voltooien, [verwijdert u de brontoepassingsgroep en/of bovenliggende brongroep](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Volgende stappen

* [Azure-resources verplaatsen naar verschillende regio's](../azure-resource-manager/management/move-region.md)
* [Ondersteuning voor het verplaatsen van Azure-resources in verschillende regio's](../azure-resource-manager/management/region-move-support.md)
* [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Ondersteuning voor het verplaatsen van resources](../azure-resource-manager/management/move-support-resources.md
)
