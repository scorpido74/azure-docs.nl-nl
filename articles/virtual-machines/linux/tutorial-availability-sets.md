---
title: 'Zelfstudie: Hoge beschikbaarheid voor virtuele Linux-machines in Azure | Microsoft Docs'
description: In deze zelfstudie leert u hoe u de Azure CLI gebruikt om maximaal beschikbare virtuele machines in beschikbaarheidssets te implementeren
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 10458e3c5f1e4dc9034206470fdfec19e13417fb
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299445"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli"></a>Zelfstudie: Virtuele machines met hoge beschikbaarheid maken en implementeren met de Azure CLI

In deze zelfstudie leert u hoe u de beschikbaarheid en betrouwbaarheid van uw Virtual Machine-oplossingen op Azure kunt verhogen met behulp van beschikbaarheidssets. Beschikbaarheidssets zorgen ervoor dat de VM's die u op Azure implementeert, verdeeld worden over meerdere geïsoleerde hardwareclusters. Dit zorgt ervoor dat als er zich binnen Azure een hardware- of softwarestoring voordoet, er slechts een subset van uw VM's wordt beïnvloed en dat uw totale oplossing beschikbaar en operationeel blijft.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een beschikbaarheidsset maken
> * Een VM maken in een beschikbaarheidsset
> * Beschikbare VM-grootten controleren

In deze zelf studie wordt gebruikgemaakt van de CLI binnen de [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert u **deze** in het begin van een wille keurig code blok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="high-availability-in-azure-overview"></a>Overzicht van hoge Beschik baarheid in azure
Hoge Beschik baarheid in azure kan op diverse manieren worden gemaakt. Twee opties die u hebt, zijn beschikbaarheids sets en beschikbaarheids zones. Door beschikbaarheids sets te gebruiken, worden uw Vm's beschermd tegen storingen die zich in een Data Center kunnen voordoen. Dit omvat hardwarefouten en Azure-software fouten. Door gebruik te maken van beschikbaarheids zones, worden uw Vm's op een fysiek afzonderlijke infra structuur geplaatst zonder gedeelde bronnen en worden ze daarom beschermd tegen het hele fout in het Data Center.

Gebruik beschikbaarheids sets of Beschikbaarheidszones wanneer u betrouw bare VM-oplossingen wilt implementeren in Azure.

### <a name="availability-set-overview"></a>Overzicht beschikbaarheidsset

Een beschikbaarheidsset is een logische groeperingsmogelijkheid die u in Azure kunt gebruiken om ervoor te zorgen dat de VM-resources die u erin plaatst, van elkaar worden geïsoleerd wanneer ze in een Azure-datacenter worden geïmplementeerd. Azure zorgt ervoor dat de VM's die u in een beschikbaarheidsset plaatst, op meerdere fysieke servers, rekenrekken, opslageenheden en netwerkswitches worden uitgevoerd. Als er zich een hardware- of softwarestoring in Azure voordoet, wordt slechts een subset van uw VM's getroffen en blijft uw totale toepassing actief en beschikbaar voor uw klanten. Beschikbaarheidssets zijn essentieel wanneer u betrouwbare cloudoplossingen wilt bouwen.

Laten we eens kijken naar een typische VM-oplossing met vier front-end webservers en twee back-end VM's die een database hosten. Met Azure wilt u twee beschikbaarheidssets definiëren voordat u uw VM's implementeert: een beschikbaarheidsset voor de ‘web’-laag en een beschikbaarheidsset voor de ‘database’-laag. Bij het maken van een nieuwe VM kunt u vervolgens de beschikbaarheidsset opgeven als parameter voor de opdracht az vm create, en zorgt Azure er automatisch voor dat de VM's die u binnen de beschikbare set maakt, worden geïsoleerd over meerdere fysieke hardwareresources. Als er een probleem is met de fysieke hardware waarop een van uw webserver- of databaseserver-VM's draait, weet u dat de andere instanties van uw webserver en database-VM's actief blijven, omdat ze worden uitgevoerd op andere hardware.

### <a name="availability-zone-overview"></a>Overzicht van beschikbaarheids zone

Beschikbaarheidszones is een aanbieding met hoge Beschik baarheid die uw toepassingen en gegevens beveiligt tegen Data Center-fouten. Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om tolerantie te garanderen, zijn er mini maal drie afzonderlijke zones in alle ingeschakelde regio's. De fysieke scheiding tussen beschikbaarheidszones binnen een Azure-regio beschermt toepassingen en gegevens tegen storingen op zoneniveau. Zone-redundante Services repliceren uw toepassingen en gegevens op Beschikbaarheidszones om te beschermen tegen enkele punten van een storing. Met Beschikbaarheidszones biedt Azure een toonaangevende SLA voor de VM-uptime van 99,99%.

Net als bij beschikbaarheids sets, gaan we een typische VM-oplossing gebruiken waar u vier front-end webservers hebt en twee back-end-Vm's gebruikt die een Data Base hosten. Net als bij beschikbaarheids sets, wilt u uw virtuele machines in twee afzonderlijke beschikbaarheids zones implementeren: één beschikbaarheids zone voor de ' Web '-laag en één beschikbaarheids zone voor de laag ' data base '. Wanneer u een nieuwe virtuele machine maakt en de beschikbaarheids zone opgeeft als een para meter voor de opdracht AZ VM Create, zorgt Azure er automatisch voor dat de virtuele machines die u maakt, worden geïsoleerd in volledig verschillende beschikbaarheids zones. Als er een probleem is met het hele Data Center waarop een van uw webserver-of database server-Vm's worden uitgevoerd, weet u dat de andere exemplaren van uw webserver en data base-Vm's actief blijven omdat ze worden uitgevoerd op volledig gescheiden data centers.

## <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken

U kunt een beschikbaarheidsset maken met behulp van [az vm availability-set create](/cli/azure/vm/availability-set). In dit voorbeeld is het aantal update- en foutdomeinen ingesteld op *2* voor de beschikbaarheidsset met de naam *myAvailabilitySet* in de resourcegroep *ResourceGroupAvailability*.

Maak eerst een resourcegroep met [az group create](/cli/azure/group#az-group-create) en maak daarna de beschikbaarheidsset:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Met beschikbaarheidssets kunt u bronnen isoleren tussen foutdomeinen en domeinen bijwerken. Een **foutdomein** vertegenwoordigt een geïsoleerde verzameling van server- plus netwerk- en opslagresources. In het voorgaande voorbeeld wordt de beschikbaarheidsset verdeeld over minstens twee foutdomeinen wanneer de VM's worden geïmplementeerd. De beschikbaarheidsset is ook verdeeld over twee **updatedomeinen**. Twee updatedomeinen zorgen ervoor dat wanneer er software-updates worden uitgevoerd op Azure, de VM-resources worden geïsoleerd, zodat wordt voorkomen dat alle software onder de VM tegelijk wordt bijgewerkt.


## <a name="create-vms-inside-an-availability-set"></a>VM's maken in een beschikbaarheidsset

VM's moeten worden gemaakt binnen de beschikbaarheidsset om ervoor te zorgen dat ze correct over de hardware worden verdeeld. U kunt een bestaande VM niet toevoegen aan een beschikbaarheidsset nadat deze is gemaakt.

Wanneer er een VM is gemaakt met [az vm create](/cli/azure/vm), gebruikt u de parameter `--availability-set` om de naam van de beschikbaarheidsset op te geven.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

De beschikbaarheidsset bevat nu twee virtuele machines. Omdat ze zich in dezelfde beschikbaarheidsset bevinden, zorgt Azure ervoor dat de virtuele machines en alle bijbehorende resources (inclusief gegevensschijven) worden gedistribueerd over geïsoleerde fysieke hardware. Door deze verdeling is de beschikbaarheid van de algehele VM-oplossing veel hoger.

U kunt de verdeling van de beschikbaarheidsset bekijken in de portal door te gaan naar Resourcegroepen > myResourceGroupAvailability > myAvailabilitySet. De VM's zijn verdeeld over de twee fout- en updatedomeinen, zoals wordt weergegeven in het volgende voorbeeld:

![Beschikbaarheidsset in de portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Beschikbare VM-grootten controleren

Extra VM's kunnen later worden toegevoegd aan de beschikbaarheidsset, als VM-grootten beschikbaar zijn op de hardware. Gebruik [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az-vm-availability-set-list-sizes) om een lijst weer te geven met alle beschikbare grootten voor de beschikbaarheidsset op de hardwarecluster:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een beschikbaarheidsset maken
> * Een VM maken in een beschikbaarheidsset
> * Beschikbare VM-grootten controleren

Ga naar de volgende zelfstudie voor meer informatie over virtuele-machineschaalsets.

> [!div class="nextstepaction"]
> [Een virtuele-machineschaalset maken](tutorial-create-vmss.md)

* Ga naar de [Beschikbaarheidszones-documentatie](../../availability-zones/az-overview.md)voor meer informatie over beschikbaarheids zones.
* Meer documentatie over zowel beschikbaarheids sets als beschikbaarheids zones is [hier](./manage-availability.md)ook beschikbaar.
* Als u beschikbaarheids zones wilt uitproberen, gaat u naar [een virtuele Linux-machine maken in een beschikbaarheids zone met de Azure cli](./create-cli-availability-zone.md)
