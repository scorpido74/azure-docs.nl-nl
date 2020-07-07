---
title: Verbinding maken met de Azure Kubernetes-service-Azure Database for MySQL
description: Meer informatie over het verbinden van Azure Kubernetes service met Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9e020d34b6cfb8117ccff1114cc938c966126b77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79537275"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Verbinding maken met Azure Kubernetes service en Azure Database for MySQL

Azure Kubernetes service (AKS) biedt een beheerd Kubernetes-cluster dat u kunt gebruiken in Azure. Hieronder vindt u enkele opties waarmee u rekening moet houden wanneer u AKS en Azure Database for MySQL samen gebruikt om een toepassing te maken.


## <a name="accelerated-networking"></a>Versneld netwerken
Gebruik versneld netwerken met onderliggende virtuele machines in uw AKS-cluster. Wanneer versneld netwerken op een virtuele machine is ingeschakeld, is er sprake van een lagere latentie, verminderde jitter en minder CPU-gebruik op de VM. Meer informatie over de werking van versneld netwerken, de ondersteunde versies van besturings systemen en ondersteunde VM-instanties voor [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Vanaf november 2018 ondersteunt AKS versneld netwerken op die ondersteunde VM-exemplaren. Versneld netwerken zijn standaard ingeschakeld voor nieuwe AKS-clusters die gebruikmaken van deze Vm's.

U kunt controleren of uw AKS-cluster versneld netwerken heeft:
1. Ga naar de Azure Portal en selecteer uw AKS-cluster.
2. Selecteer het tabblad Properties.
3. Kopieer de naam van de **infrastructuur resource groep**.
4. Gebruik de zoek balk van de portal om de resource groep voor de infra structuur te zoeken en te openen.
5. Selecteer een virtuele machine in die resource groep.
6. Ga naar het tabblad **netwerken** van de VM.
7. Controleer of **versneld netwerken** zijn ingeschakeld.

Of via de Azure CLI met behulp van de volgende twee opdrachten:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
De uitvoer is de gegenereerde resource groep die AKS maakt die de netwerk interface bevat. Voer de naam ' nodeResourceGroup ' in en gebruik deze in de volgende opdracht. **EnableAcceleratedNetworking** heeft de waarde True of False:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker voor Azure 
Met [Service Broker open voor Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) kunt u Azure-Services rechtstreeks vanuit Kubernetes of Cloud Foundry inrichten. Het is een [Open service BROKER API](https://www.openservicebrokerapi.org/) -implementatie voor Azure.

Met OSBA kunt u een Azure Database for MySQL-server maken en deze koppelen aan uw AKS-cluster met behulp van Kubernetes ' native taal '. Meer informatie over het gebruik van OSBA en Azure Database for MySQL samen op de [pagina OSBA github](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Volgende stappen
- [Een upgrade maken van een Azure Kubernetes Service-cluster](../aks/kubernetes-walkthrough.md)
- Meer informatie over het [installeren van WordPress vanuit een helm-diagram met OSBA en Azure database for MySQL](../aks/integrate-azure.md)
