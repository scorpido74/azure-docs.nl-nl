---
title: OKD implementeren in Azure
description: OkD implementeren in Azure.
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
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: 6ecae09b0efbc0e35e591b31dbb54fb49ce101b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035406"
---
# <a name="deploy-okd-in-azure"></a>OKD implementeren in Azure

U op twee manieren OKD (voorheen OpenShift Origin) implementeren in Azure:

- U alle benodigde Azure-infrastructuurcomponenten handmatig implementeren en vervolgens de [OKD-documentatie](https://docs.okd.io)volgen.
- U ook een bestaande [resourcemanagersjabloon](https://github.com/Microsoft/openshift-origin) gebruiken die de implementatie van het OKD-cluster vereenvoudigt.

## <a name="deploy-using-the-okd-template"></a>Implementeren met de OKD-sjabloon

Als u wilt implementeren met de sjabloon Resourcebeheer, gebruikt u een parametersbestand om de invoerparameters te leveren. Als u de implementatie verder wilt aanpassen, vorkt u de GitHub-repo en wijzigt u de juiste items.

Enkele veelvoorkomende aanpassingsopties zijn, maar zijn niet beperkt tot:

- Bastion VM-grootte (variabele in azuredeploy.json)
- Naamgevingsconventies (variabelen in azuredeploy.json)
- OpenShift-clusterspecificaties, gewijzigd via hosts-bestand (deployOpenShift.sh)

De [OKD-sjabloon](https://github.com/Microsoft/openshift-origin) heeft meerdere branches beschikbaar voor verschillende versies van OKD.  Op basis van uw behoeften u rechtstreeks vanuit de repo implementeren of u de repo forken en aangepaste wijzigingen aanbrengen voordat u deze implementeert.

Gebruik `appId` de waarde van de serviceprincipal die `aadClientId` u eerder hebt gemaakt voor de parameter.

Het volgende is een voorbeeld van een parametersbestand met de naam azuredeploy.parameters.json met alle vereiste ingangen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Vervang de parameters door uw specifieke informatie.

Verschillende releases kunnen verschillende parameters hebben, dus controleer de benodigde parameters voor de branch die u gebruikt.

### <a name="deploy-using-azure-cli"></a>Implementeren met behulp van Azure CLI


> [!NOTE] 
> Voor de volgende opdracht is Azure CLI 2.0.8 of hoger vereist. U de CLI-versie verifiëren met de `az --version` opdracht. Zie Azure CLI installeren als u de CLI-versie [wilt bijwerken.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

In het volgende voorbeeld wordt het OKD-cluster en alle gerelateerde resources geïmplementeerd in een resourcegroep met de naam openshiftrg, met een implementatienaam van myOpenShiftCluster. De sjabloon wordt rechtstreeks vanuit de GitHub-repo verwezen terwijl u een bestand met lokale parameters met de naam azuredeploy.parameters.json gebruikt.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

De implementatie duurt ten minste 30 minuten om te voltooien, op basis van het totale aantal geïmplementeerde knooppunten. De URL van de OpenShift-console en de DNS-naam van het OpenShift-model worden afgedrukt op de terminal wanneer de implementatie is voltooid. U ook het uitvoergedeelte van de implementatie bekijken vanuit de Azure-portal.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Als u de opdrachtregel niet wilt vastbinden in afwachting `--no-wait` van de voltooiing van de implementatie, voegt u als een van de opties voor de groepsimplementatie toe. De uitvoer van de implementatie kan worden opgehaald uit de Azure-portal in de implementatiesectie voor de brongroep.

## <a name="connect-to-the-okd-cluster"></a>Verbinding maken met het OKD-cluster

Wanneer de implementatie is voltooid, maakt u verbinding `OpenShift Console Url`met de OpenShift-console met uw browser via de . U ook SSH naar de OKD-master. Hieronder volgt een voorbeeld dat de uitvoer van de implementatie gebruikt:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht Verwijderen van de [AZ-groep](/cli/azure/group) om de brongroep, het OpenShift-cluster en alle gerelateerde bronnen te verwijderen wanneer deze niet meer nodig zijn.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Volgende stappen

- [Taken na implementatie](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift-implementatie oplossen](./openshift-container-platform-3x-troubleshooting.md)
- [Aan de slag met OKD](https://docs.okd.io)
