---
title: Taakverdeling voor meerdere IP-configuraties met Azure CLI
titleSuffix: Azure Load Balancer
description: Meer informatie over het toewijzen van meerdere IP-adressen aan een virtuele machine met Azure CLI.
services: virtual-network
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 69d324647af014a5122c404929c104a9077d5f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225311"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Taakverdeling voor meerdere IP-configuraties met Azure CLI

In dit artikel wordt beschreven hoe u Azure Load Balancer gebruiken met meerdere IP-adressen op een secundaire netwerkinterface (NIC). Voor dit scenario hebben we twee VM's met Windows, elk met een primaire en een secundaire NIC. Elk van de secundaire NIC's heeft twee IP-configuraties. Elke VM host zowel websites contoso.com als fabrikam.com. Elke website is gebonden aan een van de IP-configuraties op de secundaire NIC. We gebruiken Azure Load Balancer om twee frontend IP-adressen bloot te leggen, één voor elke website, om verkeer te distribueren naar de desbetreffende IP-configuratie voor de website. In dit scenario wordt hetzelfde poortnummer gebruikt voor beide frontends, evenals beide IP-adressen van de backendpool.

![AFBEELDING VAN LB-scenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Stappen om het saldo op meerdere IP-configuraties te laden

Voer de volgende stappen uit om het in dit artikel geschetste scenario te bereiken:

1. [Installeer en configureer de Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) door de stappen in het gekoppelde artikel te volgen en u aan te melden bij uw Azure-account.
2. Maak als volgt [een resourcegroep](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) genaamd *contosofabrikam:*

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. [Maak een beschikbaarheidsset](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) voor de twee VM's. Gebruik voor dit scenario de volgende opdracht:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Maak een virtueel netwerk](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) genaamd *myVNet* en een subnet genaamd *mySubnet:*

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. [Maak de load balancer](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) genaamd *mylb:*

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Maak twee dynamische openbare IP-adressen voor de frontend IP-configuraties van uw load balancer:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Maak de twee frontend IP-configuraties, *contosofe* en *fabrikamfe* respectievelijk:

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Maak uw backend adres pools - *contosopool* en *fabrikampool*, een [sonde](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - *HTTP*, en uw load balancing regels - *HTTPc* en *HTTPf:*

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Controleer de uitvoer om [te controleren of uw load balancer](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) correct is gemaakt door de volgende opdracht uit te voeren:

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Maak een openbare IP,](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address) *myPublicIp,* en [opslag account](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json), *mystorageaccont1* voor uw eerste virtuele machine VM1 als volgt:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Maak de netwerkinterfaces](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) voor VM1 en voeg een tweede IP-configuratie toe, *VM1-ipconfig2,* en [maak de VM](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) als volgt:

    ```azurecli
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Herhaal stap 10-11 voor uw tweede VM:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Ten slotte moet u DNS-bronrecords configureren om naar het respectievelijke ip-adres van de Load Balancer te wijzen. U uw domeinen hosten in Azure DNS. Zie [Azure DNS gebruiken met andere Azure-services](../dns/dns-for-azure-services.md)voor meer informatie over het gebruik van Azure DNS met Load Balancer.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het combineren van load balancing-services in Azure in [het gebruik van load-balancing services in Azure.](../traffic-manager/traffic-manager-load-balancing-azure.md)
- Meer informatie over hoe u verschillende typen logboeken in Azure gebruiken om load balancer te beheren en op te lossen in [Log-analyses voor Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
