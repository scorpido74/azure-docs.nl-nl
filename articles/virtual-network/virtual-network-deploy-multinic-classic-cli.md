---
title: Een virtuele machine (klassiek) met meerdere Nic's maken-Azure Classic CLI | Microsoft Docs
description: Meer informatie over het maken van een virtuele machine (klassiek) met meerdere Nic's met behulp van de klassieke Azure-opdracht regel interface (CLI).
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dc437b15f73866f76361da529690eac7a10af1a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058745"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-classic-cli"></a>Een virtuele machine (klassiek) met meerdere Nic's maken met behulp van de klassieke Azure-CLI

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

U kunt virtuele machines (Vm's) in azure maken en meerdere netwerk interfaces (Nic's) aan elke virtuele machine koppelen. Meerdere Nic's zorgen voor schei ding van verkeers typen op verschillende Nic's. Eén NIC kan bijvoorbeeld communiceren met het Internet, terwijl een andere netwerk adapter alleen communiceert met interne bronnen die niet zijn verbonden met internet. De mogelijkheid om netwerk verkeer over meerdere Nic's te scheiden, is vereist voor veel virtuele netwerk apparaten, zoals het leveren van toepassingen en WAN-optimalisatie oplossingen.

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources:  [Resource Manager en het klassieke model](../resource-manager-deployment-model.md). Dit artikel gaat over het gebruik van het klassieke implementatiemodel. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Meer informatie over het uitvoeren van deze stappen met het [Resource Manager-implementatie model](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

In de volgende stappen wordt een resource groep met de naam *IaaSStory* gebruikt voor de webservers en een resource groep met de naam *IaaSStory-back-end* voor de DB-servers.

## <a name="prerequisites"></a>Vereisten
Voordat u de DB-servers kunt maken, moet u de resource groep *IaaSStory* maken met alle benodigde resources voor dit scenario. Voer de volgende stappen uit om deze resources te maken. Maak een virtueel netwerk door de stappen in het artikel [een virtueel netwerk maken](virtual-networks-create-vnet-classic-cli.md) te volgen.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>De back-end-Vm's implementeren
De back-end-Vm's zijn afhankelijk van het maken van de volgende resources:

* **Opslag account voor gegevens schijven**. Voor betere prestaties gebruiken de gegevens schijven op de database servers een SSD-technologie (Solid State Drive), waarvoor een Premium-opslag account is vereist. Zorg ervoor dat de Azure-locatie die u implementeert voor de ondersteuning van Premium Storage.
* **NIC's**. Elke VM heeft twee Nic's: één voor database toegang en één voor beheer.
* **Beschikbaarheidsset**. Alle database servers worden toegevoegd aan één beschikbaarheidsset om ervoor te zorgen dat ten minste één van de Vm's tijdens het onderhoud actief is.

### <a name="step-1---start-your-script"></a>Stap 1: Start uw script
U kunt het volledige bash-script dat [hier](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh)wordt gebruikt, downloaden. Voer de volgende stappen uit om het script te wijzigen zodat het in uw omgeving werkt:

1. Wijzig de waarden van de onderstaande variabelen op basis van uw bestaande resource groep die hierboven is geïmplementeerd in [vereisten](#prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Wijzig de waarden van de onderstaande variabelen op basis van de waarden die u wilt gebruiken voor de back-end-implementatie.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Stap 2: Maak benodigde resources voor uw Vm's
1. Maak een nieuwe Cloud service voor alle back-end-Vm's. Let op het gebruik van `$backendCSName` de variabele voor de naam van de resource `$location` groep en voor de Azure-regio.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Maak een Premium-opslag account voor het besturings systeem en de gegevens schijven die door uw Vm's moeten worden gebruikt.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Stap 3: Vm's maken met meerdere Nic's
1. Een lus starten om meerdere vm's te maken op basis van `numberOfVMs` de variabelen.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Geef voor elke VM de naam en het IP-adres van elk van de twee Nic's op.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Maak de virtuele machine. Let op het gebruik van `--nic-config` de para meter, die een lijst bevat met alle nic's met de naam, het subnet en het IP-adres.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Voor elke VM maakt u twee gegevens schijven.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Stap 4: het script uitvoeren
Nu u het script hebt gedownload en gewijzigd op basis van uw behoeften, voert u het script uit om de back-end data base-Vm's te maken met meerdere Nic's.

1. Sla het script op en voer dit uit vanaf uw **bash** -Terminal. U ziet de eerste uitvoer, zoals hieronder wordt weer gegeven.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Na een paar minuten wordt de uitvoering beëindigd en ziet u de rest van de uitvoer zoals hieronder wordt weer gegeven.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Stap 5: route ring configureren in het besturings systeem van de virtuele machine

Azure DHCP wijst een standaard gateway toe aan de eerste (primaire) netwerk interface die is gekoppeld aan de virtuele machine. Azure wijst geen standaardgateway toe aan extra (secundaire) netwerkinterfaces die zijn gekoppeld aan een virtuele machine. Daarom kunt u standaard niet communiceren met resources buiten het subnet waarin een secundaire netwerkinterface zich bevindt. Secundaire netwerkinterfaces kunnen echter wel communiceren met resources buiten hun subnet. Als u route ring wilt configureren voor secundaire netwerk interfaces, raadpleegt u [route ring binnen een besturings systeem van een virtuele machine met meerdere netwerk interfaces](virtual-network-network-interface-vm.md).
