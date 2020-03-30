---
title: Azure Resource Manager-voorbeeldsjablonen
description: Sjabloonvoorbeelden van Azure Resource Manager zoeken om Azure Container Instances in verschillende configuraties te implementeren
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: a8f3c81c539562a3c56e4822cf6e4df77d04928f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981654"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Resource Manager-sjablonen voor Azure Container Instances

De volgende voorbeeldsjablonen implementeren containerexemplaren in verschillende configuraties.

Zie de sectie [Implementatie](#deployment) voor implementatieopties. Als u uw eigen sjablonen wilt maken, bevat de [sjabloonindeling azure][ref] Container Instances Resource Manager de sjabloonindeling en beschikbare eigenschappen.

## <a name="sample-templates"></a>Voorbeeldsjablonen

| | |
|-|-|
| **Toepassingen** ||
| [WordPress][app-wp] | Hiermee maakt u een WordPress-website en de MySQL-database in een containergroep. De WordPress-site-inhoud en MySQL-database blijven bestaan in een Azure-bestandenshare. Maakt ook een toepassingsgateway om openbare netwerktoegang tot WordPress bloot te leggen. |
| [MS NAV met SQL Server en IIS][app-nav] | Implementeert één Windows-container met een volledig uitgeruste zelfstandige Dynamics NAV / Dynamics 365 Business Central-omgeving. |
| **Volumes** ||
| [emptyDir][vol-emptydir] | Implementeert twee Linux-containers die een leegDir-volume delen. |
| [gitRepo][vol-gitrepo] | Implementeert een Linux-container die een GitHub repo kloont en deze als volume monteert. |
| [geheim][vol-secret] | Implementeert een Linux container met een PFX cert gemonteerd als een geheim volume. |
| **Networking** ||
| [Aan UDP blootgestelde container][net-udp] | Hiermee implementeert u een Windows- of Linux-container die een UDP-poort blootlegt. |
| [Linux container met openbare IP][net-publicip] | Implementeert een enkele Linux-container die toegankelijk is via een openbare IP. |
| [Een containergroep implementeren met een virtueel netwerk (voorbeeld)][net-vnet] | Implementeert een nieuw virtueel netwerk, subnet, netwerkprofiel en containergroep. |
| **Azure-resources** ||
| [Azure Storage-account en delen bestanden maken][az-files] | Gebruikt de Azure CLI in een containerinstantie om een opslagaccount en een Azure-bestandenaandeel te maken.

## <a name="deployment"></a>Implementatie

U hebt verschillende opties voor het implementeren van resources met Resource Manager-sjablonen:

[Azure-CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure-portal][deploy-portal]

[REST-API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
