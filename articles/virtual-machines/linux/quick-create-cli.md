---
title: 'Snelstart: Een virtuele Linux-machine maken met de Azure CLI | Microsoft Docs'
description: In deze snelstartgids leert u hoe u de Azure CLI gebruikt om een virtuele Linux-machine te maken
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 79a5f766c5766700fa31368ef7c0d18e0eaef0f1
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70033545"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Quickstart: Een virtuele Linux-machine maken met de Azure CLI

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze snelstartgids wordt beschreven hoe u de Azure CLI gebruikt om een virtuele Linux-machine (VM) in Azure te implementeren. In deze zelfstudie installeren we Ubuntu 16.04 LTS. Als u wilt zien hoe de virtuele machine in de praktijk werkt, moet u hiermee verbinding maken via SSH en de NGINX-webserver installeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browser tabblad door naar te [https://shell.azure.com/bash](https://shell.azure.com/bash)gaan. Selecteer **kopiëren** om de blokken code te kopiëren, plak deze in het Cloud shell en selecteer **Enter** om het programma uit te voeren.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u voor deze snelstart versie 2.0.30 of hoger van Azure CLI nodig. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Virtuele machine maken

Maak een VM met de opdracht [az vm create](/cli/azure/vm).

In het volgende voorbeeld wordt een virtuele machine met de naam *myVM* gemaakt en voegt u een gebruikersaccount met de naam *azureuser* toe. De parameter `--generate-ssh-keys` wordt gebruikt om automatisch een SSH-sleutel te genereren en deze te plaatsen in de standaardsleutellocatie ( *~/.ssh*). Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het maken van de virtuele machine en de ondersteunende resources duurt enkele minuten. In het volgende voorbeeld van uitvoer ziet u dat het maken van de virtuele machine is geslaagd.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Noteer uw eigen `publicIpAddress` in de uitvoer van uw virtuele machine. Dit adres wordt gebruikt voor toegang tot de virtuele machine in de volgende stappen.

## <a name="open-port-80-for-web-traffic"></a>Poort 80 openen voor webverkeer

Standaard worden alleen SSH-verbindingen geopend wanneer u een virtuele Linux-machine in Azure maakt. Gebruik [az vm open-port](/cli/azure/vm) om TCP-poort 80 te openen voor gebruik met de NGINX-webserver:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Verbinding maken met de virtuele machine

SSH met uw virtuele machine zoals normaal. Vervang **publicIPAddress** door het openbare IP-adres van uw VM, zoals is aangegeven in de vorige uitvoer van de VM:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Webserver installeren

Als u uw VM in actie wilt zien, installeert u de NGINX-webserver. Werk de pakketbronnen bij en installeer het meest recente NGINX-pakket.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Wanneer u klaar bent, typt u `exit` om de SSH-sessie te verlaten.

## <a name="view-the-web-server-in-action"></a>De webserver in actie zien

Gebruik een webbrowser naar keuze om de standaard NGINX-welkomstpagina weer te geven. Gebruik het openbare IP-adres van uw VM als webadres. In het volgende voorbeeld ziet u de NGINX-standaardwebsite:

![Standaardsite van NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een eenvoudige virtuele machine geïmplementeerd, een netwerkpoort geopend voor internetverkeer en een eenvoudige webserver geïnstalleerd. Voor meer informatie over virtuele machines in Azure, gaat u verder met de zelfstudie voor virtuele Linux-machines.


> [!div class="nextstepaction"]
> [Zelfstudies over virtuele Linux-machines](./tutorial-manage-vm.md)
