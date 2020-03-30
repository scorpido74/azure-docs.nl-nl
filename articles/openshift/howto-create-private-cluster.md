---
title: Een privécluster maken met Azure Red Hat OpenShift 3.11 | Microsoft Documenten
description: Een privécluster maken met Azure Red Hat OpenShift 3.11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, private cluster, rode hoed
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399417"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Een privécluster maken met Azure Red Hat OpenShift 3.11

> [!IMPORTANT]
> Azure Red Hat OpenShift (ARO) privéclusters zijn momenteel alleen beschikbaar in private preview in East US 2. Private preview-acceptatie is alleen op uitnodiging. Zorg ervoor dat u uw abonnement registreert voordat u deze functie probeert in te schakelen.

Particuliere clusters bieden de volgende voordelen:

* Privéclusters leggen clustercontrolevlakcomponenten (zoals de API-servers) niet bloot op een openbaar IP-adres.
* Het virtuele netwerk van een privécluster is configureerbaar voor klanten, zodat u netwerken instellen om peering met andere virtuele netwerken mogelijk te maken, waaronder ExpressRoute-omgevingen. U ook aangepaste DNS op het virtuele netwerk configureren om te integreren met interne services.

## <a name="before-you-begin"></a>Voordat u begint

> [!NOTE]
> Deze functie vereist versie 2019-10-27-preview van de ARO HTTP API. Het wordt nog niet ondersteund in de Azure CLI.

De velden in het volgende configuratiefragment zijn nieuw en moeten worden opgenomen in uw clusterconfiguratie. `managementSubnetCidr`moet zich binnen het virtuele clusternetwerk bevinden en wordt door Azure gebruikt om het cluster te beheren.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Een privécluster kan worden geïmplementeerd met behulp van de onderstaande voorbeeldscripts. Zodra het cluster is geïmplementeerd, voert u de `cluster get` opdracht uit en bekijkt u de `properties.FQDN` eigenschap om het privé-IP-adres van de OpenShift-API-server te bepalen.

Het virtuele clusternetwerk is gemaakt met machtigingen, zodat u het wijzigen. U vervolgens netwerken instellen om toegang te krijgen tot het virtuele netwerk (ExpressRoute, VPN, virtuele netwerkpeering) zoals vereist voor uw behoeften.

Als u de DNS-naamservers in het virtuele clusternetwerk wijzigt, moet `properties.RefreshCluster` u een `true` update op het cluster uitvoeren met de eigenschap die is ingesteld op, zodat de VM's opnieuw kunnen worden weergegeven. Met deze update kunnen ze de nieuwe nameservers ophalen.

## <a name="sample-configuration-scripts"></a>Voorbeeldconfiguratiescripts

Gebruik de voorbeeldscripts in deze sectie om uw privécluster in te stellen en te implementeren.

### <a name="environment"></a>Omgeving

Vul hieronder de omgevingsvariabelen in met uw eigen waarden.

> [!NOTE]
> De locatie moet `eastus2` worden ingesteld omdat dit momenteel de enige ondersteunde locatie voor privéclusters is.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>private-cluster.json

Met behulp van de hierboven gedefinieerde omgevingsvariabelen is hier een voorbeeldclusterconfiguratie met ingeschakeld privécluster.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>Een privécluster implementeren

Nadat u uw privécluster hebt geconfigureerd met de bovenstaande voorbeeldscripts, voert u de volgende opdracht uit om uw privécluster te implementeren.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Volgende stappen

Zie [Web Console Walkthrough](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html)voor meer informatie over hoe u toegang krijgt tot de OpenShift-console.
