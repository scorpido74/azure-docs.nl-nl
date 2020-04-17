---
title: Zelfstudie - Aangepaste VM-afbeeldingen maken met de Azure CLI
description: In deze zelfstudie leert u hoe u Azure CLI gebruikt om een aangepaste installatiekopie van een virtuele machine te maken in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: dc7b395d46fd28cde9ccbbda8a8a55447efa61c9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460049"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Zelfstudie: Een aangepaste installatiekopie van een Azure-VM maken met Azure CLI

Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. In deze zelfstudie maakt u uw eigen aangepaste installatiekopie van een virtuele Azure-machine. Procedures voor:

> [!div class="checklist"]
> * De inrichting van virtuele machines ongedaan maken en virtuele machines generaliseren
> * Een aangepaste installatiekopie maken
> * Een VM maken van een aangepaste installatiekopie
> * Alle installatiekopieën in uw abonnement weergeven
> * Een aangepaste installatiekopie verwijderen

Deze zelfstudie maakt gebruik van de CLI in de [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert **u Probeer deze** boven aan een codeblok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Voordat u begint

In de onderstaande stappen wordt gedetailleerd beschreven hoe u van een bestaande virtuele machine een herbruikbare aangepaste installatiekopie maakt die u kunt gebruiken om nieuwe virtuele machines te maken.

Om het voorbeeld in deze zelfstudie uit te voeren, moet u een bestaande virtuele machine hebben. Indien nodig kan dit [scriptvoorbeeld](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) er een voor u maken. Vervang tijdens het volgen van de zelfstudie de namen van de resourcegroep en de virtuele machine waar nodig.

## <a name="create-a-custom-image"></a>Een aangepaste installatiekopie maken

Voor het maken van een installatiekopie van een virtuele machine moet u de virtuele machine voorbereiden door de inrichting ervan ongedaan te maken, de toewijzing ervan ongedaan te maken en de bron-VM vervolgens als gegeneraliseerd te markeren. Wanneer de VM is voorbereid, kunt u een installatiekopie maken.

### <a name="deprovision-the-vm"></a>De inrichting van de virtuele machine ongedaan maken 

Met het ongedaan maken van de inrichting wordt de virtuele machine gegeneraliseerd omdat machinespecifieke informatie wordt verwijderd. Dankzij deze generalisatie kunnen meerdere virtuele machines worden geïmplementeerd op basis van één installatiekopie. Tijdens het ongedaan maken van de inrichting wordt de naam van de host opnieuw ingesteld op *localhost.localdomain*. SSH-hostsleutels, naamserverconfiguraties hoofdwachtwoord en in de cache geplaatste DHCP-leases worden ook verwijderd.

> [!WARNING]
> Als u de VM als gegeneraliseerd dedint en markeert, wordt bron-VM onbruikbaar en kan deze niet opnieuw worden gestart. 

Voor het ongedaan maken van de inrichting van de virtuele machine gebruikt u de Azure VM-agent (waagent). De Azure VM-agent is geïnstalleerd op de virtuele machine en beheert de inrichting en interactie met de Azure-infrastructuurcontroller. Zie de [Gebruikershandleiding voor Azure Linux Agent](../extensions/agent-linux.md) voor meer informatie.

Maak verbinding met uw virtuele machine via SSH en voer de opdracht voor het ongedaan maken van de inrichting van de virtuele machine uit. Met het argument `+user` worden de laatste ingerichte gebruikersaccount en alle bijbehorende gegevens ook verwijderd. Vervang het IP-voorbeeldadres door het openbare IP-adres van de virtuele machine.

Maak via SSH verbinding met de virtuele machine.
```bash
ssh azureuser@52.174.34.95
```
Maak de inrichting van de virtuele machine ongedaan.

```bash
sudo waagent -deprovision+user -force
```
Sluit de SSH-sessie.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>De toewijzing van de virtuele machine ongedaan maken en de virtuele machine markeren als gegeneraliseerd

Voor het maken van een installatiekopie moet de toewijzing van de VM ongedaan worden gemaakt. Maak de toewijzing van de VM ongedaan met [az vm deallocate](/cli//azure/vm). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Tot slot stelt u de status van de virtuele machine in als gegeneraliseerd met [az vm generalize](/cli//azure/vm) zodat het Azure-platform weet de VM is gegeneraliseerd. U kunt een installatiekopie alleen maken van een gegeneraliseerde virtuele machine.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>De installatiekopie maken

U kunt nu een installatiekopie van de virtuele machine maken met behulp van [az image create](/cli//azure/image). In het volgende voorbeeld wordt een installatiekopie met de naam *myImage* gemaakt van een virtuele machine met de naam *myVM*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>VM's maken van de installatiekopie

Nu u een installatiekopie hebt gemaakt, kunt u een of meer nieuwe virtuele machines maken van de installatiekopie met behulp van [az vm create](/cli/azure/vm). In het volgende voorbeeld wordt een virtuele machine met de naam *myVMfromImage* gemaakt van een installatiekopie met de naam *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

We raden u aan het aantal gelijktijdige implementaties te beperken tot 20 VM's vanuit één afbeelding. Als u grootschalige, gelijktijdige implementaties van meer dan 20 VM's van dezelfde aangepaste afbeelding plant, moet u een [gedeelde afbeeldingsgalerie](shared-image-galleries.md) met meerdere afbeeldingsreplica's gebruiken. 

## <a name="image-management"></a>Installatiekopiebeheer 

Hier volgen enkele voorbeelden van algemene beheertaken voor installatiekopieën en hoe u deze kunt uitvoeren met de Azure CLI.

Een lijst weergeven met alle installatiekopieën op naam in een tabelindeling.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

Een installatiekopie verwijderen. In dit voorbeeld wordt de installatiekopie met de naam *myOldImage* verwijderd uit de *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een aangepaste installatiekopie voor een virtuele machine gemaakt. U hebt geleerd hoe u:

> [!div class="checklist"]
> * De inrichting van virtuele machines ongedaan maken en virtuele machines generaliseren
> * Een aangepaste installatiekopie maken
> * Een VM maken van een aangepaste installatiekopie
> * Alle installatiekopieën in uw abonnement weergeven
> * Een aangepaste installatiekopie verwijderen

Ga naar de volgende zelfstudie voor meer informatie over virtuele machines met hoge beschikbaarheid.

> [!div class="nextstepaction"]
> [Maak zeer beschikbare VM's](tutorial-availability-sets.md).

