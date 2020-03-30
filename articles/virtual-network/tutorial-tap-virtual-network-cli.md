---
title: Een VNet TAP - Azure CLI maken, wijzigen of verwijderen
description: Meer informatie over het maken, wijzigen of verwijderen van een virtueel netwerk TAP met behulp van azure cli.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.openlocfilehash: 56288a65dc9e5b12a12393965b9670e394146181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234952"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Werken met een virtueel netwerk TAP met de Azure CLI

Azure virtual network TAP (Terminal Access Point) stelt u in staat om uw netwerkverkeer voor virtuele machines continu te streamen naar een netwerkpakketverzamelaar of analysetool. De collector- of analysetool wordt geleverd door een [netwerkpartner voor virtuele apparaten.](https://azure.microsoft.com/solutions/network-appliances/) Zie [partneroplossingen](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions)voor een lijst met partneroplossingen die zijn gevalideerd om met het virtuele netwerk TAP te werken. 

## <a name="create-a-virtual-network-tap-resource"></a>Een TAP-bron voor virtueel netwerk maken

Lees [vereisten](virtual-network-tap-overview.md#prerequisites) voordat u een tap-bron voor virtueel netwerk maakt. U de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de Azure command-line interface (CLI) vanaf uw computer uit te voeren. De Azure Cloud Shell is een gratis interactieve shell, waarvoor de Azure CLI niet op uw computer hoeft te worden geïnstalleerd. U moet zich aanmelden bij Azure met een account dat over de juiste [machtigingen beschikt.](virtual-network-tap-overview.md#permissions) Dit artikel vereist de Azure CLI-versie 2.0.46 of hoger. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Virtueel netwerk TAP is momenteel beschikbaar als extensie. Als u de extensie `az extension add -n virtual-network-tap`wilt installeren, moet u deze uitvoeren. Als u de Azure CLI lokaal uitvoert, `az login` moet u ook worden uitgevoerd om een verbinding met Azure te maken.

1. Haal de id van uw abonnement op in een variabele die in een latere stap wordt gebruikt:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Stel de abonnements-id in die u gebruikt om een TAP-bron voor virtueel netwerk te maken.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Registreer de abonnements-id die u gebruikt om een TAP-bron voor virtueel netwerk te maken opnieuw. Als u een registratiefout krijgt wanneer u een TAP-bron maakt, voert u de volgende opdracht uit:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Als de bestemming voor het virtuele netwerk TAP de netwerkinterface op het virtuele netwerktoestel is voor collector- of analysetool -

   - Haal de IP-configuratie van de netwerknetwerkinterface van het netwerk op in een variabele die in een latere stap wordt gebruikt. De ID is het eindpunt dat het TAP-verkeer verzamelt. In het volgende voorbeeld wordt de ID van de *IP-configuratie ipconfig1* opgehaald voor een netwerkinterface met de naam *myNetworkInterface,* in een brongroep met de naam *myResourceGroup:*

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Maak het virtuele netwerk TAP in het Azure-gebied westcentralus met de ID van de IP-configuratie als bestemming en een optionele poorteigenschap. De poort geeft de doelpoort op de IP-configuratie van de netwerkinterface aan waar het TAP-verkeer wordt ontvangen:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Als de bestemming voor het virtuele netwerk TAP een azure internal load balancer is:
  
   - Haal de front-end IP-configuratie van de interne load balancer van Azure op in een variabele die in een latere stap wordt gebruikt. De ID is het eindpunt dat het TAP-verkeer verzamelt. In het volgende voorbeeld wordt de ID van de *frontendipconfig1* front-end IP-configuratie opgehaald voor een load balancer met de naam *myInternalLoadBalancer,* in een resourcegroep met de naam *myResourceGroup:*

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Maak het virtuele netwerk TAP met de ID van de IP-configuratie aan de frontend als bestemming en een optionele poorteigenschap. De poort geeft de doelpoort op front-end IP-configuratie waar het TAP-verkeer zal worden ontvangen:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Bevestig de creatie van het virtuele netwerk TAP:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Een TAP-configuratie toevoegen aan een netwerkinterface

1. Haal de ID van een bestaande TAP-bron voor virtueel netwerk op. In het volgende voorbeeld wordt een virtueel netwerk TAP met de naam *myTap* opgehaald in een resourcegroep met de naam *myResourceGroup:*

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Maak een TAP-configuratie op de netwerkinterface van de bewaakte virtuele machine. In het volgende voorbeeld wordt een TAP-configuratie voor een netwerkinterface met de naam *myNetworkInterface:*

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Bevestig het maken van de TAP-configuratie:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>De TAP-configuratie op een netwerkinterface verwijderen

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Virtuele netwerk-TAP's in een abonnement weergeven

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Een virtueel netwerk TAP in een resourcegroep verwijderen

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
