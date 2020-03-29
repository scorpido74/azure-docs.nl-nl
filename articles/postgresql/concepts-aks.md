---
title: Verbinding maken met Azure Kubernetes Service - Azure Database voor PostgreSQL - Single Server
description: Meer informatie over het verbinden van Azure Kubernetes Service (AKS) met Azure Database voor PostgreSQL - Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 5/6/2019
ms.topic: conceptual
ms.openlocfilehash: 46aa411826dd3ea578a2d98b0fe631ab0a12ef4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769877"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Azure Kubernetes-service en Azure-database verbinden voor PostgreSQL - Single Server

Azure Kubernetes Service (AKS) biedt een beheerd Kubernetes-cluster dat u in Azure gebruiken. Hieronder vindt u enkele opties om rekening mee te houden bij het gebruik van AKS en Azure Database voor PostgreSQL om samen een toepassing te maken.


## <a name="accelerated-networking"></a>Versneld netwerken
Gebruik onderliggende VM's met versnelde netwerken in uw AKS-cluster. Wanneer versnelde netwerken is ingeschakeld op een VM, is er een lagere latentie, verminderde jitter en een verminderd CPU-gebruik op de VM. Meer informatie over hoe versnelde netwerken werken, de ondersteunde OS-versies en ondersteunde VM-exemplaren voor [Linux.](../virtual-network/create-vm-accelerated-networking-cli.md)

Vanaf november 2018 ondersteunt AKS versnelde netwerken op deze ondersteunde VM-exemplaren. Versnelde netwerken is standaard ingeschakeld op nieuwe AKS-clusters die deze VM's gebruiken.

U bevestigen of uw AKS-cluster de netwerken heeft versneld:
1. Ga naar de Azure-portal en selecteer uw AKS-cluster.
2. Selecteer het tabblad Properties.
3. Kopieer de naam van de **infrastructuurbrongroep**.
4. Gebruik de zoekbalk van de portal om de infrastructuurbrongroep te zoeken en te openen.
5. Selecteer een VM in die resourcegroep.
6. Ga naar het tabblad **Netwerken van** de virtuele machine.
7. Controleer of **Accelerated networking** 'Enabled' is.

Of via de Azure CLI met behulp van de volgende twee opdrachten:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
De uitvoer is de gegenereerde brongroep die DOOR AKS maakt met de netwerkinterface. Neem de naam 'nodeResourceGroup' en gebruik deze in de volgende opdracht. **EnableAcceleratedNetworking** is waar of onwaar:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker voor Azure 
[Met Open Service Broker voor Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) u Azure-services rechtstreeks vanuit Kubernetes of Cloud Foundry inrichten. Het is een [Open Service Broker API-implementatie](https://www.openservicebrokerapi.org/) voor Azure.

Met OSBA u een Azure Database voor PostgreSQL-server maken en deze aan uw AKS-cluster binden met de moedertaal van Kubernetes. Meer informatie over het samen gebruiken van OSBA en Azure Database voor PostgreSQL op de [OSBA GitHub-pagina.](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md) 


## <a name="connection-pooling"></a>Verbindingspooling
Een verbindingspooler minimaliseert de kosten en tijd die gepaard gaan met het maken en sluiten van nieuwe verbindingen met de database. Het zwembad is een verzameling verbindingen die hergebruikt kunnen worden. 

Er zijn meerdere verbindingspoolers die u gebruiken met PostgreSQL. Een van deze is [PgBouncer](https://pgbouncer.github.io/). In het Microsoft Container Registry bieden we een lichtgewicht containerized PgBouncer die kan worden gebruikt in een zijspan om verbindingen van AKS naar Azure Database voor PostgreSQL te bundelen. Ga naar de [dockerhubpagina](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) voor meer informatie over het openen en gebruiken van deze afbeelding. 


## <a name="next-steps"></a>Volgende stappen
-  [Een Azure Kubernetes Service-cluster maken](../aks/kubernetes-walkthrough.md)
