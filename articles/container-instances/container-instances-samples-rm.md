---
title: Azure Resource Manager-voorbeeldsjablonen
description: Azure Resource Manager sjabloon voorbeelden zoeken om Azure Container Instances te implementeren in verschillende configuraties
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: ddea823525002662363d61a795f34d6094c897d0
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533443"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Resource Manager sjablonen voor Azure Container Instances

De volgende voorbeeld sjablonen implementeren container instanties in verschillende configuraties.

Zie de sectie [implementatie](#deployment) voor implementatie opties. Als u uw eigen sjablonen wilt maken, gaat u naar de sjabloon indeling van de Azure Container Instances [Resource Manager-sjabloon][ref] en de beschik bare eigenschappen.

## <a name="sample-templates"></a>Voorbeeldsjablonen

| | |
|-|-|
| **Toepassingen** ||
| [WordPress][app-wp] | Hiermee maakt u een WordPress-website en de MySQL-data base in een container groep. De WordPress site-inhoud en MySQL-data base worden opgeslagen in een Azure Files share. Maakt ook een toepassings gateway om open bare netwerk toegang tot WordPress beschikbaar te maken. |
| [MS NAV met SQL Server en IIS][app-nav] | Hiermee implementeert u één Windows-container met een volledig functionele, op zichzelf staande Dynamics NAV/Dynamics 365 Business Central-omgeving. |
| **Omvang** ||
| [emptyDir][vol-emptydir] | Hiermee worden twee Linux-containers geïmplementeerd die een emptyDir-volume delen. |
| [gitRepo][vol-gitrepo] | Hiermee wordt een Linux-container geïmplementeerd waarmee een GitHub-opslag plaats wordt gekloond en gekoppeld als een volume. |
| [secret][vol-secret] | Hiermee implementeert u een Linux-container met een PFX-certificaat dat is gekoppeld als een geheim volume. |
| **Netwerken** ||
| [UDP-weer gegeven container][net-udp] | Hiermee wordt een Windows-of Linux-container geïmplementeerd die een UDP-poort beschikbaar maakt. |
| [Linux-container met openbaar IP-adres][net-publicip] | Implementeert één Linux-container die toegankelijk is via een openbaar IP-adres. |
| [Een container groep met een virtueel netwerk (preview-versie) implementeren][net-vnet] | Hiermee implementeert u een nieuw virtueel netwerk, subnet, netwerk profiel en container groep. |
| **Azure-resources** ||
| [Azure Storage account en bestanden share maken][az-files] | Maakt gebruik van de Azure CLI in een container exemplaar voor het maken van een opslag account en een Azure Files share.

## <a name="deployment"></a>Implementatie

U hebt verschillende mogelijkheden voor het implementeren van resources met Resource Manager-sjablonen:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure Portal][deploy-portal]

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
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
