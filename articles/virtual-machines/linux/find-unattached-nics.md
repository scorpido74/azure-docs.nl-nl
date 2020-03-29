---
title: Ongekoppelde Azure NIC's zoeken en verwijderen
description: Azure NIC's die niet aan VM's zijn gekoppeld met de Azure CLI zoeken en verwijderen
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 8142b95ee666e205a8328eafd5930f1f386e49af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945126"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Ongekoppelde netwerkinterfacekaarten (NIC's) voor Azure VM's zoeken en verwijderen
Wanneer u een virtuele machine (VM) in Azure verwijdert, worden de netwerkinterfacekaarten (NIC's) niet standaard verwijderd. Als u meerdere VM's maakt en verwijdert, blijven de ongebruikte NIC's de interne IP-adresleases gebruiken. Als u andere VM NIC's maakt, kunnen ze mogelijk geen IP-lease verkrijgen in de adresruimte van het subnet. In dit artikel ziet u hoe u niet-gekoppelde NIC's vinden en verwijderen.

## <a name="find-and-delete-unattached-nics"></a>Niet-gekoppelde NIC's zoeken en verwijderen

De eigenschap *virtualMachine* voor een NIC slaat de ID- en resourcegroep van de VM op waaraan de NIC is gekoppeld. Het volgende script loopt door alle NIC's in een abonnement en controleert of de eigenschap *virtualMachine* null is. Als deze eigenschap null is, is de NIC niet gekoppeld aan een virtuele machine.

Om alle niet-gekoppelde NIC's te bekijken, is het ten zeerste aan te raden om het script eerst uit te voeren met de *variabele deleteUnattachedNics* naar *0.* Als u alle niet-gekoppelde NIC's wilt verwijderen nadat u de lijstuitvoer hebt gecontroleerd, voert u het script met *deleteUnattachedNics* uit op *1*.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Volgende stappen

Zie [VM-netwerken maken en beheren](tutorial-virtual-network.md)voor meer informatie over het maken en beheren van virtuele netwerken in Azure.
