---
title: Een instantie van Azure SignalR-service schalen
description: Meer informatie over het schalen van een Azure SignalR Service-instantie om de capaciteit toe te voegen of te verkleinen, via Azure portal of Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659284"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Een Azure SignalR-service-instantie schalen?
In dit artikel ziet u hoe u uw exemplaar van Azure SignalR Service schaalt. Er zijn twee scenario's voor schalen, opschalen en uitschalen.

* [Opschalen:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)krijg meer eenheden, verbindingen, berichten en meer. U schaalt op door de prijscategorie te wijzigen van Gratis naar Standaard.
* [Uitschalen](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Verhoog het aantal SignalR-eenheden. U uitschalen naar maar liefst 100 eenheden.

De schaalinstellingen hebben enkele minuten nodig om toe te passen. In zeldzame gevallen kan het ongeveer 30 minuten duren om toe te passen. U hoeft uw code niet te wijzigen of uw servertoepassing opnieuw te implementeren.

Zie Details van azure [SignalR Service Pricing](https://azure.microsoft.com/pricing/details/signalr-service/)voor informatie over de prijzen en capaciteiten van afzonderlijke SignalR-service.  

> [!NOTE]
> Als u de SignalR-service wijzigt van **Gratis** laag naar **Standaardlaag** of vice versa, wordt het IP-adres van de openbare dienst gewijzigd en duurt het meestal 30-60 minuten om de wijziging in DNS-servers over het hele internet te verspreiden. Uw service is mogelijk onbereikbaar voordat DNS wordt bijgewerkt. Over het algemeen is het niet aan te raden om uw prijscategorie te vaak te wijzigen.


## <a name="scale-on-azure-portal"></a>Schalen op Azure-portal

1. Open [Azure Portal](https://portal.azure.com) in uw browser.

2. Selecteer op de pagina SignalR Service in het linkermenu de optie **Schalen**.
   
3. Kies uw prijscategorie en klik op **Selecteren**. Stel het aantal eenheden in voor **Standaardlaag.**
   
    ![Schaal op portal](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Klik op **Opslaan**.

## <a name="scale-using-azure-cli"></a>Schalen met Azure CLI

Met dit script wordt een nieuwe SignalR Service-bron van **Free** Tier en een nieuwe resourcegroep gemaakt en wordt deze geschaald naar **Standaardlaag.** 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

Noteer de naam die wordt gegenereerd voor de nieuwe resourcegroep. U hebt deze naam nodig als u later alle groepsresources wilt verwijderen.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>Prijsniveaus vergelijken

Zie [Detaildetails](https://azure.microsoft.com/pricing/details/signalr-service/)van de serviceprijzen voor gedetailleerde informatie, zoals inbegrepen berichten en verbindingen voor elke prijscategorie.

Zie [SignalR-servicelimieten](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits)voor een tabel met servicelimieten, quota en beperkingen in elke laag.

## <a name="next-steps"></a>Volgende stappen

In deze handleiding leerde u hoe u één SignalR Service-exemplaar schalen.

Meerdere eindpunten worden ook ondersteund voor het schalen, sharden en cross-region scenario's.

> [!div class="nextstepaction"]
> [Seinservice schalen met meerdere exemplaren](./signalr-howto-scale-multi-instances.md)
