---
title: Azure-brongebruik controleren aan de hand van limieten | Microsoft Documenten
description: Meer informatie over het controleren van uw Azure-brongebruik aan de hand van azure-abonnementslimieten.
services: networking
documentationcenter: na
author: KumudD
ms.author: kumud
tags: azure-resource-manager
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.openlocfilehash: cffa5677c5531f3887639c049998523d7d07586a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455559"
---
# <a name="check-resource-usage-against-limits"></a>Resourcegebruik controleren aan de hand van limieten

In dit artikel leert u hoe u het aantal van elk netwerkbrontype ziet dat u in uw abonnement hebt geïmplementeerd en wat uw [abonnementslimieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) zijn. De mogelijkheid om het gebruik van resources te bekijken tegen limieten is handig om het huidige gebruik bij te houden en te plannen voor toekomstig gebruik. U de [Azure Portal,](#azure-portal) [PowerShell](#powershell)of de [Azure CLI](#azure-cli) gebruiken om het gebruik bij te houden.

## <a name="azure-portal"></a>Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer Alle **services**boven, links van de Azure-portal .
3. Voer *Abonnementen* in het vak **Filter** in. Wanneer **Abonnementen** in de zoekresultaten wordt weergegeven, selecteert u deze optie.
4. Selecteer de naam van het abonnement waarvoor u gebruiksgegevens wilt weergeven.
5. Selecteer **onder INSTELLINGEN**de optie Gebruik + **quotum**.
6. U kunt de volgende opties selecteren:
   - **Resourcetypen:** u alle resourcetypen selecteren of de specifieke typen resources selecteren die u wilt weergeven.
   - **Providers**: U alle resourceproviders selecteren of **Compute,** **Network**of **Storage**selecteren.
   - **Locaties**: U alle Azure-locaties selecteren of specifieke locaties selecteren.
   - U selecteren om alle resources weer te geven, of alleen de resources waar ten minste één is geïmplementeerd.

     In het voorbeeld in de volgende afbeelding worden alle netwerkbronnen weergegeven met ten minste één resource die in de Oostelijke VS is geïmplementeerd:

       ![Gebruiksgegevens weergeven](./media/check-usage-against-limits/view-usage.png)

     U de kolommen sorteren door de kolomkop te selecteren. De weergegeven limieten zijn de limieten voor uw abonnement. Als u een standaardlimiet wilt verhogen, selecteert u **Verhoging aanvragen**en voltooit u vervolgens de ondersteuningsaanvraag en dient u deze in. Alle resources hebben een maximumlimiet die wordt vermeld in [Azure-limieten.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) Als uw huidige limiet al op het maximale aantal zit, kan de limiet niet worden verhoogd.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell vanaf uw computer uit te voeren. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u PowerShell vanaf uw computer uitvoert, hebt u de Azure PowerShell-module, versie 1.0.0 of hoger, nodig. Voer `Get-Module -ListAvailable Az` uit op uw computer om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u `Login-AzAccount` ook worden uitgevoerd om u aan te melden bij Azure.

Bekijk uw gebruik tegen limieten met [Get-AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage). In het volgende voorbeeld wordt het gebruik voor resources waarbij ten minste één resource is geïmplementeerd op de locatie Oost-VS:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

U ontvangt uitvoer die hetzelfde is opgemaakt als de volgende voorbeelduitvoer:

```output
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure-CLI

Als u CLI-opdrachten (Azure Command-line interface) gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Dit artikel vereist de Azure CLI-versie 2.0.32 of hoger. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal uitvoert, moet `az login` u ook worden uitgevoerd om u aan te melden bij Azure.

Bekijk uw gebruik tegen limieten met [az-netwerklijstgebruik.](/cli/azure/network?view=azure-cli-latest#az-network-list-usages) In het volgende voorbeeld wordt het gebruik voor resources op de locatie Oost-VS:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

U ontvangt uitvoer die hetzelfde is opgemaakt als de volgende voorbeelduitvoer:

```output
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```
