---
title: OpenShift Container Platform 3.11 implementeren in Azure
description: OpenShift Container Platform 3.11 implementeren in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 615d9a3c5c359174ef15028e82044a85da0dd733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561283"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>OpenShift Container Platform 3.11 implementeren in Azure

U een van de verschillende methoden gebruiken om OpenShift Container Platform 3.11 in Azure te implementeren:

- U handmatig de benodigde Azure-infrastructuurcomponenten implementeren en vervolgens de documentatie van het [OpenShift Container Platform](https://docs.openshift.com/container-platform)volgen.
- U ook een bestaande [Resource Manager-sjabloon](https://github.com/Microsoft/openshift-container-platform/) gebruiken die de implementatie van het cluster OpenShift Container Platform vereenvoudigt.
- Een andere optie is het gebruik van de [Azure Marketplace-aanbieding.](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)

Voor alle opties is een Red Hat-abonnement vereist. Tijdens de implementatie wordt het Red Hat Enterprise Linux-exemplaar geregistreerd bij het Red Hat-abonnement en gekoppeld aan de Pool-ID die de rechten voor OpenShift Container Platform bevat.
Zorg ervoor dat je een geldige gebruikersnaam, wachtwoord en Pool ID hebt. U een activeringssleutel, oorspronkelijke-id en pool-id gebruiken. U deze gegevens verifiëren https://access.redhat.comdoor u aan te melden bij.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Implementeren met de sjabloon OpenShift Container Platform Resource Manager 3.11

### <a name="private-clusters"></a>Privéclusters

Het implementeren van private OpenShift-clusters vereist meer dan alleen het niet hebben van een openbaar IP-adres dat is gekoppeld aan de hoofdloadbalancer (webconsole) of aan de infraload balancer (router).  Een privécluster maakt over het algemeen gebruik van een aangepaste DNS-server (niet de standaard Azure DNS), een aangepaste domeinnaam (zoals contoso.com) en vooraf gedefinieerde virtuele netwerk(en).  Voor privéclusters moet u uw virtuele netwerk vooraf configureren met alle juiste subnetten en DNS-serverinstellingen.  Gebruik vervolgens **bestaandeMasterSubnetReference**, **bestaandeInfraSubnetReference**, **bestaandeCnsSubnetReference**en **bestaandeNodeSubnetReference** om het bestaande subnet op te geven voor gebruik door het cluster.

Als de privémaster is geselecteerd **(masterClusterType**=private), moet een statisch privé-IP worden opgegeven voor **masterPrivateClusterIp**.  Dit IP wordt toegewezen aan de voorkant van de hoofdlastenbalans.  Het IP moet binnen de CIDR zijn voor het hoofdsubnet en niet in gebruik zijn.  **masterClusterDnsType** moet zijn ingesteld op 'aangepast' en de hoofd-DNS-naam moet worden opgegeven voor **masterClusterDns.**  De DNS-naam moet worden toegewezen aan het statische privé-IP-ip en wordt gebruikt om toegang te krijgen tot de console op de hoofdknooppunten.

Als een privérouter is geselecteerd **(routerClusterType**=privé), moet een statisch privé-IP-IP worden opgegeven voor **routerPrivateClusterIp**.  Dit IP wordt toegewezen aan de voorkant van de infraload balancer.  Het OT moet binnen de CIDR liggen voor het infrasubnet en niet in gebruik zijn.  **routingSubDomainType** moet worden ingesteld op 'aangepast' en de DNS-naam met jokertekens voor routering moet worden opgegeven voor **routingSubDomain.**  

Als privé-masters en privérouter zijn geselecteerd, moet de aangepaste domeinnaam ook worden ingevoerd voor **domeinNaam**

Na een succesvolle implementatie is het Bastion-knooppunt het enige knooppunt met een openbaar IP waar u in ssh.  Zelfs als de hoofdknooppunten zijn geconfigureerd voor openbare toegang, worden ze niet blootgesteld voor ssh-toegang.

Als u wilt implementeren met de sjabloon Resourcebeheer, gebruikt u een parametersbestand om de invoerparameters te leveren. Als u de implementatie verder wilt aanpassen, vorkt u de GitHub-repo en wijzigt u de juiste items.

Enkele veelvoorkomende aanpassingsopties zijn, maar zijn niet beperkt tot:

- Bastion VM-grootte (variabele in azuredeploy.json)
- Naamgevingsconventies (variabelen in azuredeploy.json)
- OpenShift-clusterspecificaties, gewijzigd via hosts-bestand (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Het parametersbestand configureren

De [sjabloon OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) heeft meerdere branches beschikbaar voor verschillende versies van OpenShift Container Platform.  Op basis van uw behoeften u rechtstreeks vanuit de repo implementeren of u de repo forken en aangepaste wijzigingen aanbrengen in de sjablonen of scripts voordat u deze implementeert.

Gebruik `appId` de waarde van de serviceprincipal `aadClientId` die u eerder hebt gemaakt voor de parameter.

In het volgende voorbeeld ziet u een parametersbestand met de naam azuredeploy.parameters.json met alle vereiste ingangen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Vervang de parameters door uw specifieke informatie.

Verschillende releases kunnen verschillende parameters hebben, dus controleer de vereiste parameters voor de branch die u gebruikt.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Parameters.json-bestand uitgelegd

| Eigenschap | Beschrijving | Geldige opties | Standaardwaarde |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL voor artefacten (json, scripts, enz.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Azure-regio om resources te implementeren |  |  |
| `masterVmSize` | Grootte van de master-VM. Kies uit een van de toegestane VM-formaten die worden vermeld in het azuredeploy.json-bestand |  | Standard_E2s_v3 |
| `infraVmSize` | Grootte van de Infra VM. Kies uit een van de toegestane VM-formaten die worden vermeld in het azuredeploy.json-bestand |  | Standard_D4s_v3 |
| `nodeVmSize` | Grootte van de VM voor app-knooppunt. Kies uit een van de toegestane VM-formaten die worden vermeld in het azuredeploy.json-bestand |  | Standard_D4s_v3 |
| `cnsVmSize` | Grootte van de CNS-knooppuntVM (Container Native Storage). Kies uit een van de toegestane VM-formaten die worden vermeld in het azuredeploy.json-bestand |  | Standard_E4s_v3 |
| `osImageType` | De RHEL-afbeelding om te gebruiken. defaultgallery: On-Demand; marktplaats: afbeelding van derden | standaardgalerie <br> marketplace | standaardgalerie |
| `marketplaceOsImage` | Als `osImageType` marktplaats is, voert u dan de juiste waarden in voor 'publisher', 'offer', 'sku', 'version' of the marketplace offer. Deze parameter is een objecttype |  |  |
| `storageKind` | Het type opslag dat moet worden gebruikt  | Beheerd<br> Onbeheerde | Beheerd |
| `openshiftClusterPrefix` | Clustervoorvoegsel dat wordt gebruikt om hostnamen voor alle knooppunten te configureren.  Tussen 1 en 20 tekens |  | mycluster |
| `minoVersion` | De kleine versie van OpenShift Container Platform 3.11 om te implementeren |  | 69 |
| `masterInstanceCount` | Aantal masterknooppunten dat moet worden geïmplementeerd | 1, 3, 5 | 3 |
| `infraInstanceCount` | Aantal infraknooppunten dat moet worden geïmplementeerd | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Aantal knooppunten dat moet worden geïmplementeerd | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Aantal CNS-knooppunten dat moet worden geïmplementeerd | 3, 4 | 3 |
| `osDiskSize` | Grootte van de osschijf voor de VM (in GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Grootte van de gegevensschijf die moet worden gekoppeld aan knooppunten voor dockervolume (in GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Grootte van de gegevensschijf die moet worden gekoppeld aan CNS-knooppunten voor gebruik door glusterfs (in GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Gebruikersnaam voor beheerder voor zowel DE VM-aanmelding als de eerste OpenShift-gebruiker |  | ocpadmin ocpadmin |
| `enableMetrics` | Statistieken inschakelen. Statistieken vereisen meer resources, dus selecteer de juiste grootte voor Infra VM | waar <br> false | false |
| `enableLogging` | Logboekregistratie inschakelen. elasticsearch pod vereist 8 GB RAM dus selecteer de juiste grootte voor Infra VM | waar <br> false | false |
| `enableCNS` | Containernative storage inschakelen | waar <br> false | false |
| `rhsmUsernameOrOrgId` | Gebruikersnaam of organisatie-id van Red Hat Subscription Manager |  |  |
| `rhsmPoolId` | De Red Hat Subscription Manager Pool ID die uw OpenShift-rechten voor compute nodes bevat |  |  |
| `rhsmBrokerPoolId` | De Red Hat Subscription Manager Pool ID die uw OpenShift-rechten voor masters en infraknooppunten bevat. Als u geen verschillende pool-id's hebt, voert u dezelfde pool-ID in als 'rhsmPoolId' |  |
| `sshPublicKey` | Kopieer hier uw SSH Public Key |  |  |
| `keyVaultSubscriptionId` | De abonnements-id van het abonnement dat de Key Vault bevat |  |  |
| `keyVaultResourceGroup` | De naam van de resourcegroep die de sleutelkluis bevat |  |  |
| `keyVaultName` | De naam van de Key Vault die u hebt gemaakt |  |  |
| `enableAzure` | Azure Cloud Provider inschakelen | waar <br> false | waar |
| `aadClientId` | Azure Active Directory Client ID ook wel bekend als Application ID for Service Principal |  |  |
| `domainName` | Naam van de aangepaste domeinnaam te gebruiken (indien van toepassing). Instellen op 'geen' als u geen volledig privécluster implementeert |  | geen |
| `masterClusterDnsType` | Domeintype voor OpenShift-webconsole. 'default' gebruikt DNS-label van master infra public IP. 'custom' stelt u in staat om uw eigen naam te definiëren | standaardinstelling <br> Aangepaste | standaardinstelling |
| `masterClusterDns` | De aangepaste DNS-naam die moet worden gebruikt om toegang te krijgen tot de OpenShift-webconsole als u 'aangepast' hebt geselecteerd voor`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Als ingesteld op 'nipio', `routingSubDomain` zal nip.io gebruiken.  Gebruik 'aangepast' als u uw eigen domein hebt dat u wilt gebruiken voor routering | nipio nipio <br> Aangepaste | nipio nipio |
| `routingSubDomain` | De DNS-naam met jokertekens die u wilt gebruiken voor routering als u 'aangepast' hebt geselecteerd voor`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Selecteer of u een bestaand virtueel netwerk wilt gebruiken of een nieuw virtueel netwerk wilt maken | Bestaande <br> nieuw | nieuw |
| `virtualNetworkResourceGroupName` | Naam van de resourcegroep voor het nieuwe virtuele netwerk als u 'nieuw' hebt geselecteerd voor`virtualNetworkNewOrExisting` |  | resourceGroep().naam |
| `virtualNetworkName` | De naam van het nieuwe virtuele netwerk dat u wilt maken als u 'nieuw' hebt geselecteerd voor`virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Adresvoorvoegsel van het nieuwe virtuele netwerk |  | 10.0.0.0/14 |
| `masterSubnetName` | De naam van het hoofdsubnet |  | mastersubnet |
| `masterSubnetPrefix` | CIDR gebruikt voor het hoofdsubnet - moet een subset zijn van het adresVoorvoegsel |  | 10.1.0.0/16 |
| `infraSubnetName` | De naam van het infrasubnet |  | infrasubnet |
| `infraSubnetPrefix` | CIDR gebruikt voor het infra subnet - moet een subset van het adresVoorvoegsel |  | 10.2.0.0/16 |
| `nodeSubnetName` | De naam van het subnet van het knooppunt |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR gebruikt voor het knooppunt subnet - moet een subset van het adresVoorvoegsel |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Volledige verwijzing naar bestaand subnet voor hoofdknooppunten. Niet nodig bij het maken van nieuwe vNet / Subnet |  |  |
| `existingInfraSubnetReference` | Volledige verwijzing naar bestaand subnet voor infraknooppunten. Niet nodig bij het maken van nieuwe vNet / Subnet |  |  |
| `existingCnsSubnetReference` | Volledige verwijzing naar bestaand subnet voor CNS-knooppunten. Niet nodig bij het maken van nieuwe vNet / Subnet |  |  |
| `existingNodeSubnetReference` | Volledige verwijzing naar bestaand subnet voor compute nodes. Niet nodig bij het maken van nieuwe vNet / Subnet |  |  |
| `masterClusterType` | Geef op of het cluster privé- of openbare hoofdknooppunten gebruikt. Als privé wordt gekozen, worden de hoofdknooppunten niet blootgesteld aan het internet via een openbaar IP-adres. In plaats daarvan zal het gebruik maken van het privé-IP dat is opgegeven in de`masterPrivateClusterIp` | Openbare <br> Privé | Openbare |
| `masterPrivateClusterIp` | Als private master-knooppunten zijn geselecteerd, moet een privé-IP-adres worden opgegeven voor gebruik door de interne load balancer voor hoofdknooppunten. Dit statische IP moet zich binnen het CIDR-blok voor het hoofdsubnet begeven en nog niet in gebruik zijn. Als openbare hoofdknooppunten zijn geselecteerd, wordt deze waarde niet gebruikt, maar moet deze nog steeds worden opgegeven |  | 10.1.0.200 |
| `routerClusterType` | Geef op of het cluster private of openbare infraknooppunten gebruikt. Als privé wordt gekozen, worden de infraknooppunten niet blootgesteld aan het internet via een openbaar IP. In plaats daarvan zal het gebruik maken van het privé-IP dat is opgegeven in de`routerPrivateClusterIp` | Openbare <br> Privé | Openbare |
| `routerPrivateClusterIp` | Als particuliere infraknooppunten zijn geselecteerd, moet een privé-IP-adres worden opgegeven voor gebruik door de interne load balancer voor infraknooppunten. Dit statische IP moet zich binnen het CIDR-blok voor het hoofdsubnet begeven en nog niet in gebruik zijn. Als openbare infraknooppunten zijn geselecteerd, wordt deze waarde niet gebruikt, maar moet deze nog steeds worden opgegeven |  | 10.2.0.200 |
| `routingCertType` | Aangepaste certificaat gebruiken voor routeringsdomein of het standaard zelfondertekende certificaat - volg instructies in de sectie **Aangepaste certificaten** | zelfondertekend <br> Aangepaste | zelfondertekend |
| `masterCertType` | Aangepast certificaat gebruiken voor hoofddomein of het standaard zelfondertekend certificaat - volg instructies in de sectie **Aangepaste certificaten** | zelfondertekend <br> Aangepaste | zelfondertekend |

<br>

### <a name="deploy-using-azure-cli"></a>Implementeren met behulp van Azure CLI

> [!NOTE] 
> Voor de volgende opdracht is Azure CLI 2.0.8 of hoger vereist. U de CLI-versie verifiëren met de `az --version` opdracht. Zie Azure CLI installeren als u de CLI-versie [wilt bijwerken.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)

In het volgende voorbeeld wordt het OpenShift-cluster en alle gerelateerde resources geïmplementeerd in een resourcegroep met de naam openshiftrg, met een implementatienaam van myOpenShiftCluster. De sjabloon wordt rechtstreeks vanuit de GitHub-repo verwezen en er wordt een bestand met lokale parameters met de naam azuredeploy.parameters.json-bestand gebruikt.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

De implementatie duurt ten minste 60 minuten, op basis van het totale aantal geïmplementeerde knooppunten en de geconfigureerde opties. De Bastion DNS FQDN en de URL van de OpenShift-console worden afgedrukt naar de terminal wanneer de implementatie is voltooid.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Als u de opdrachtregel niet wilt vastbinden in afwachting `--no-wait` van de voltooiing van de implementatie, voegt u als een van de opties voor de groepsimplementatie toe. De uitvoer van de implementatie kan worden opgehaald uit de Azure-portal in de implementatiesectie voor de brongroep.

## <a name="connect-to-the-openshift-cluster"></a>Verbinding maken met het OpenShift-cluster

Wanneer de implementatie is voltooid, haalt u de verbinding op uit het uitvoergedeelte van de implementatie. Maak verbinding met de OpenShift-console met uw browser met de URL van de **OpenShift-console**. U ook SSH naar de Bastion host. Hieronder volgt een voorbeeld waarbij de gebruikersnaam van de beheerder clusterbeheerder is en de ip-IP DNS-fqdn van het bastion bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht Verwijderen van de [AZ-groep](/cli/azure/group) om de brongroep, het OpenShift-cluster en alle gerelateerde bronnen te verwijderen wanneer deze niet meer nodig zijn.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Volgende stappen

- [Taken na implementatie](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift-implementatie in Azure oplossen](./openshift-container-platform-3x-troubleshooting.md)
- [Aan de slag met OpenShift Container Platform](https://docs.openshift.com)
