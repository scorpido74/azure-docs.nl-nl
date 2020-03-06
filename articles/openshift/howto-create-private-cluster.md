---
title: Een persoonlijk cluster maken met Azure Red Hat open Shift 3,11 | Microsoft Docs
description: Een persoonlijk cluster maken met Azure Red Hat open Shift 3,11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: Aro, open Shift, persoonlijk cluster, Red Hat
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399417"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Een persoonlijk cluster maken met Azure Red Hat open Shift 3,11

> [!IMPORTANT]
> Azure Red Hat open Shift (ARO)-particuliere clusters zijn momenteel alleen beschikbaar als particuliere preview in VS-Oost 2. Privé preview-acceptatie is alleen via de uitnodiging. Zorg ervoor dat u uw abonnement registreert voordat u deze functie inschakelt.

Particuliere clusters bieden de volgende voor delen:

* Persoonlijke clusters bieden geen onderdelen voor cluster beheer vlak (zoals de API-servers) op een openbaar IP-adres.
* Het virtuele netwerk van een persoonlijk cluster kan door klanten worden geconfigureerd, zodat u netwerken kunt instellen om peering met andere virtuele netwerken, waaronder ExpressRoute-omgevingen, toe te staan. U kunt ook aangepaste DNS configureren in het virtuele netwerk om te integreren met interne services.

## <a name="before-you-begin"></a>Voordat u begint

> [!NOTE]
> Voor deze functie is versie 2019-10-27-preview van de ARO HTTP API vereist. Het wordt nog niet ondersteund in de Azure CLI.

De velden in het volgende configuratie fragment zijn nieuw en moeten worden opgenomen in de cluster configuratie. `managementSubnetCidr` moet zich in het virtuele cluster netwerk bevindt en door Azure wordt gebruikt om het cluster te beheren.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Een persoonlijk cluster kan worden geïmplementeerd met behulp van de onderstaande voorbeeld scripts. Zodra het cluster is geïmplementeerd, voert u de opdracht `cluster get` uit en bekijkt u de eigenschap `properties.FQDN` om het privé-IP-adres van de open Shift API-server te bepalen.

Het virtuele cluster netwerk is gemaakt met machtigingen, zodat u het kunt wijzigen. U kunt vervolgens netwerken instellen voor toegang tot het virtuele netwerk (ExpressRoute, VPN, virtuele netwerk peering) zoals vereist voor uw behoeften.

Als u de DNS-naam servers op het virtuele cluster netwerk wijzigt, moet u een update uitgeven op het cluster waarop de eigenschap `properties.RefreshCluster` is ingesteld op `true`, zodat de installatie kopie van de Vm's kan worden gewijzigd. Met deze update kunnen de nieuwe naam servers worden opgehaald.

## <a name="sample-configuration-scripts"></a>Voorbeeld configuratie scripts

Gebruik de voorbeeld scripts in deze sectie om uw persoonlijke cluster in te stellen en te implementeren.

### <a name="environment"></a>Omgeving

Vul de volgende omgevings variabelen in om uw eigen waarden te gebruiken.

> [!NOTE]
> De locatie moet worden ingesteld op `eastus2`, omdat dit momenteel de enige ondersteunde locatie voor privé clusters is.

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

### <a name="private-clusterjson"></a>persoonlijk-cluster. json

Als u de hierboven gedefinieerde omgevings variabelen gebruikt, is hier een voor beeld van een cluster configuratie waarvoor een persoonlijk cluster is ingeschakeld.

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

## <a name="deploy-a-private-cluster"></a>Een persoonlijk cluster implementeren

Nadat u uw persoonlijke cluster hebt geconfigureerd met de bovenstaande voorbeeld scripts, voert u de volgende opdracht uit om uw persoonlijke cluster te implementeren.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Volgende stappen

Zie [webconsole-overzicht](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html)voor meer informatie over het openen van de open Shift-console.
