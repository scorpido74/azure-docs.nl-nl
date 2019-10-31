---
title: Azure Kubernetes-netwerk beleid | Microsoft Docs
description: Meer informatie over Kubernetes-netwerk beleid voor het beveiligen van uw Kubernetes-cluster.
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
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159694"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Overzicht van Azure Kubernetes-netwerk beleid

Netwerk beleidsregels bieden micro segmentatie voor heel wat is net als netwerk beveiligings groepen (Nsg's) micro segmenting voor Vm's. De implementatie van het Azure-netwerk beleid ondersteunt de standaard Kubernetes-netwerk beleids specificatie. U kunt labels gebruiken om een groep van peulen te selecteren en een lijst met regels voor binnenkomend en uitgaand verkeer te definiëren waarmee wordt aangegeven welk verkeers type is toegestaan voor en van deze peulen. Meer informatie over het Kubernetes-netwerk beleid in de [Kubernetes-documentatie](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Overzicht van Kubernetes-netwerk beleid](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure-netwerk beleid werkt in combi natie met de Azure-CNI die VNet-integratie voor containers biedt. Het wordt momenteel alleen ondersteund op Linux-knoop punten. De implementaties configureren Linux IP-tabel regels op basis van het gedefinieerde beleid voor het afdwingen van het filteren van verkeer.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Beveiliging plannen voor uw Kubernetes-cluster
Gebruik netwerk beveiligings groepen (Nsg's) bij het implementeren van de beveiliging voor uw cluster om het Noord-Zuid verkeer te filteren, dat wil zeggen verkeer dat uw cluster subnet binnenkomt en verlaat en Kubernetes-netwerk beleid gebruikt voor Oost-West verkeer, dat wil zeggen verkeer tussen peul in uw cluster.

## <a name="using-azure-kubernetes-network-policies"></a>Azure Kubernetes-netwerk beleid gebruiken
Azure-netwerk beleid kan worden gebruikt op de volgende manieren om micro segmentering te bieden voor peulen.

### <a name="acs-engine"></a>ACS-engine
ACS-engine is een hulp programma waarmee een Azure Resource Manager sjabloon wordt gegenereerd voor de implementatie van een Kubernetes-cluster in Azure. De configuratie van het cluster is opgenomen in een JSON-bestand dat aan het hulpprogramma wordt doorgegeven bij het genereren van de sjabloon. Zie Microsoft Azure container service-engine-cluster definitie voor meer informatie over de volledige lijst met ondersteunde cluster instellingen en de bijbehorende beschrijvingen.

Als u beleids regels wilt inschakelen voor clusters die zijn geïmplementeerd met ACS-engine, geeft u de waarde van de networkPolicy-instelling in het cluster definitie bestand op "Azure".

#### <a name="example-configuration"></a>Voorbeeldconfiguratie

De onderstaande JSON-voorbeeld configuratie maakt een nieuw virtueel netwerk en subnet en implementeert een Kubernetes-cluster met Azure CNI. U kunt het beste Klad blok gebruiken om het JSON-bestand te bewerken. 
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
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Uw eigen Kubernetes-cluster maken in azure
De implementatie kan worden gebruikt om netwerk beleid te bieden voor een Kubernetes-cluster dat u zelf implementeert, zonder dat u gebruikmaakt van hulpprogram ma's zoals de ACS-engine. In dit geval installeert u eerst de CNI-invoeg toepassing en schakelt u deze in op elke virtuele machine in een cluster. Zie [Deploy plug-in for a Kubernetes cluster](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster) (Invoegtoepassing implementeren voor een Kubernetes-cluster) voor gedetailleerde instructies.

Zodra het cluster is geïmplementeerd, voert u de volgende `kubectl` opdracht uit om de Azure Network Policy *daemonset* te downloaden en toe te passen op het cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
De oplossing is ook open source en de code is beschikbaar op de [Azure container-netwerk opslagplaats](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [Azure Kubernetes-service](../aks/intro-kubernetes.md).
-  Meer informatie over [container netwerken](container-networking-overview.md).
- [Implementeer de invoeg toepassing](deploy-container-networking.md) voor Kubernetes-clusters of docker-containers.
