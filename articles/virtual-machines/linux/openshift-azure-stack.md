---
title: OpenShift implementeren in Azure Stack
description: Deploy OpenShift in Azure Stack.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: d6c73b8cd33aa85793a2ce839410065e03b97be7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035533"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>OpenShift Container Platform of OKD implementeren in Azure Stack

OpenShift kan worden geïmplementeerd in Azure Stack. Er zijn enkele belangrijke verschillen tussen Azure en Azure Stack, zodat de implementatie enigszins zal verschillen en de mogelijkheden ook enigszins zullen verschillen.

Momenteel werkt de Azure Cloud Provider niet in Azure Stack. Daarom u schijfbijlage niet gebruiken voor permanente opslag in Azure Stack. In plaats daarvan u andere opslagopties configureren, zoals NFS, iSCSI, GlusterFS, enz. Als alternatief u CNS inschakelen en GlusterFS gebruiken voor permanente opslag. Als CNS is ingeschakeld, worden drie extra knooppunten geïmplementeerd met extra opslag voor GlusterFS-gebruik.

U een van de verschillende methoden gebruiken om OpenShift Container Platform of OKD te implementeren in Azure Stack:

- U de benodigde Azure-infrastructuurcomponenten handmatig implementeren en vervolgens de documentatie van [het OpenShift Container Platform](https://docs.openshift.com/container-platform) of [DED-documentatie](https://docs.okd.io)volgen.
- U ook een bestaande [Resource Manager-sjabloon](https://github.com/Microsoft/openshift-container-platform/) gebruiken die de implementatie van het cluster OpenShift Container Platform vereenvoudigt.
- U ook een bestaande [resourcemanagersjabloon](https://github.com/Microsoft/openshift-origin) gebruiken die de implementatie van het OKD-cluster vereenvoudigt.

Als u de sjabloon Resourcebeheer gebruikt, selecteert u de juiste vertakking (azurestack-release-3.x). De sjablonen voor Azure werken niet omdat de API-versies verschillen tussen Azure en Azure Stack. De RHEL-afbeeldingsverwijzing is momenteel hardgecodeerd als variabele in het azuredeploy.json-bestand en moet worden gewijzigd om aan uw afbeelding te voldoen.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Voor alle opties is een Red Hat-abonnement vereist. Tijdens de implementatie wordt het Red Hat Enterprise Linux-exemplaar geregistreerd bij het Red Hat-abonnement en gekoppeld aan de Pool-ID die de rechten voor OpenShift Container Platform bevat.
Zorg ervoor dat je een geldige gebruikersnaam, wachtwoord en Pool ID hebt. U ook een activeringssleutel, oorspronkelijke-id en pool-id gebruiken.  U deze gegevens verifiëren https://access.redhat.comdoor u aan te melden bij.

## <a name="azure-stack-prerequisites"></a>Azure Stack-vereisten

Er moet een RHEL-afbeelding (OpenShift Container Platform) of CentOS-afbeelding (OKD) worden toegevoegd aan uw Azure Stack-omgeving om een OpenShift-cluster te implementeren. Neem contact op met uw Azure Stack-beheerder om deze afbeeldingen toe te voegen. Instructies zijn hier te vinden:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Implementeren met behulp van de sjabloon OpenShift Container Platform of OKD Resource Manager

Als u wilt implementeren met de sjabloon Resourcebeheer, gebruikt u een parametersbestand om de invoerparameters te leveren. Als u de implementatie verder wilt aanpassen, vorkt u de GitHub-repo en wijzigt u de juiste items.

Enkele veelvoorkomende aanpassingsopties zijn, maar zijn niet beperkt tot:

- Bastion VM-grootte (variabele in azuredeploy.json)
- Naamgevingsconventies (variabelen in azuredeploy.json)
- OpenShift-clusterspecificaties, gewijzigd via hosts-bestand (deployOpenShift.sh)
- RHEL-afbeeldingsverwijzing (variabele in azuredeploy.json)

Volg de juiste sectie in de sectie [OpenShift Container Platform](./openshift-container-platform-3x.md) of de sectie [OKD](./openshift-okd.md) voor de stappen die u wilt implementeren met de Azure CLI.

## <a name="next-steps"></a>Volgende stappen

- [Taken na implementatie](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift-implementatie in Azure oplossen](./openshift-container-platform-3x-troubleshooting.md)