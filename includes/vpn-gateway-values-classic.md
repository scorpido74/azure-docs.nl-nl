---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 75aec9f3509881c35de9309fa1532b961fb2bc03
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875541"
---
Wanneer u een klassiek VNets maakt in de Azure Portal, is de naam die u wilt weer geven niet de volledige naam die u gebruikt voor Power shell. Zo kan een VNet dat **TestVNet1** in de portal heet, een veel langere naam hebben in het netwerk configuratie bestand. De naam kan er ongeveer als volgt uitzien: **Group ClassicRG TestVNet1**. Wanneer u uw verbindingen maakt, is het belang rijk dat u de waarden gebruikt die u in het netwerk configuratie bestand ziet.

In de volgende stappen maakt u verbinding met uw Azure-account en downloadt en bekijkt u het netwerk configuratie bestand om de vereiste waarden voor uw verbindingen te verkrijgen.

1. Down load en installeer de meest recente versie van de Power shell-cmdlets voor Azure Service Management (SM). De meeste mensen hebben de Resource Manager-modules lokaal geïnstalleerd, maar hebben geen service management-modules. Service management-modules zijn verouderd en moeten afzonderlijk worden geïnstalleerd. Zie [Service Management-cmdlets installeren](/powershell/azure/servicemanagement/install-azure-ps)voor meer informatie.

1. Open de PowerShell-console met verhoogde rechten en maak verbinding met uw account. Gebruik de volgende voor beelden om u te helpen verbinding te maken. U moet deze opdrachten lokaal uitvoeren met de module Power shell-Service beheer. Maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

   ```powershell
   Add-AzureAccount
   ```
1. Controleer de abonnementen voor het account.

   ```powershell
   Get-AzureSubscription
   ```
1. Als u meerdere abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. Het netwerk configuratie bestand exporteren en weer geven. Maak een map op de computer en exporteer vervolgens het netwerkconfiguratiebestand naar de map. In dit voor beeld wordt het netwerk configuratie bestand geëxporteerd naar **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. Open het bestand met een tekst editor en Bekijk de namen voor uw VNets en sites. Deze namen zijn de namen die u gebruikt wanneer u uw verbindingen maakt.<br>VNet-namen worden vermeld als **VirtualNetworkSite name =**<br>Site namen worden vermeld als **LocalNetworkSiteRef name =**