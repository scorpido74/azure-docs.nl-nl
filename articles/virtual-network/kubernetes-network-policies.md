---
title: Azure Kubernetes-netwerkbeleid | Microsoft Documenten
description: Meer informatie over kubernetes-netwerkbeleid om uw Kubernetes-cluster te beveiligen.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73159694"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Overzicht van Kubernetes-netwerkbeleid

Netwerkbeleid biedt microsegmentatie voor pods, net als Network Security Groups (NSGs) bieden microsegmentatie voor VM's. De implementatie van Azure Network Policy ondersteunt de standaard specificatie van het Kubernetes-netwerkbeleid. U labels gebruiken om een groep pods te selecteren en een lijst met binnenkomende en uitgaande regels te definiëren die het soort verkeer opgeven dat van en naar deze pods is toegestaan. Meer informatie over het Kubernetes-netwerkbeleid in de [Kubernetes-documentatie](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Overzicht van Kubernetes-netwerkbeleid](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure-netwerkbeleid werkt samen met het Azure CNI dat VNet-integratie biedt voor containers. Het wordt tegenwoordig alleen ondersteund op Linux-knooppunten. De implementaties configureren Linux IP Table-regels op basis van het gedefinieerde beleid om verkeersfiltering af te dwingen.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Beveiliging plannen voor uw Kubernetes-cluster
Wanneer u beveiliging voor uw cluster implementeert, gebruikt u netwerkbeveiligingsgroepen (NSG's) om Noord-Zuidverkeer te filteren, dat wil zeggen verkeer dat uw clustersubnet invoert en verlaat, en gebruik kubernetes-netwerkbeleid voor Oost-Westverkeer, dat wil zeggen verkeer tussen pods in uw cluster.

## <a name="using-azure-kubernetes-network-policies"></a>Azure Kubernetes-netwerkbeleid gebruiken
Azure-netwerkbeleid kan op de volgende manieren worden gebruikt om microsegmentatie voor pods te bieden.

### <a name="acs-engine"></a>ACS-motor
ACS-Engine is een tool die een Azure Resource Manager-sjabloon genereert voor de implementatie van een Kubernetes-cluster in Azure. De configuratie van het cluster is opgenomen in een JSON-bestand dat aan het hulpprogramma wordt doorgegeven bij het genereren van de sjabloon. Zie Microsoft Azure Container Service Engine - Cluster Definition (Microsoft Azure Container Service Engine - Clusterdefinitie) voor de volledige lijst met ondersteunde clusterinstellingen en de bijbehorende beschrijvingen.

Als u beleid wilt inschakelen voor clusters die zijn geïmplementeerd met acs-engine, geeft u de waarde op van de instelling netwerkBeleid in het clusterdefinitiebestand als 'azure'.

#### <a name="example-configuration"></a>Voorbeeldconfiguratie

De onderstaande JSON-voorbeeldconfiguratie maakt een nieuw virtueel netwerk en subnet en implementeert er een Kubernetes-cluster in met Azure CNI. We raden u aan 'Kladblok' te gebruiken om het JSON-bestand te bewerken. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Uw eigen Kubernetes-cluster maken in Azure
De implementatie kan worden gebruikt om netwerkbeleid te bieden voor pods in Kubernetes-clusters die u zelf implementeert, zonder te vertrouwen op tools zoals de ACS-Engine. In dit geval installeert u eerst de CNI-plug-in en schakelt u deze in op elke virtuele machine in een cluster. Zie [Deploy plug-in for a Kubernetes cluster](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster) (Invoegtoepassing implementeren voor een Kubernetes-cluster) voor gedetailleerde instructies.

Zodra het cluster is geïmplementeerd, voert u de volgende `kubectl` opdracht uit om de *daemonset* van het Azure-netwerkbeleid te downloaden en toe te passen op het cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
De oplossing is ook open source en de code is beschikbaar in de [Azure Container Networking repository.](https://github.com/Azure/azure-container-networking/tree/master/npm)



## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Kubernetes Service](../aks/intro-kubernetes.md).
-  Meer informatie over [containernetwerken](container-networking-overview.md).
- [Implementeer de plug-in voor Kubernetes-clusters](deploy-container-networking.md) of Docker-containers.
