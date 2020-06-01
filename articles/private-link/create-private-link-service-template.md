---
title: ARM-sjabloon Azure Private Link service
description: ARM-sjabloon voor persoonlijke koppelings service
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: article
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 93a66057ddb0034f7ac9ac62578292ca38f2d2fe
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84237127"
---
# <a name="create-a-private-link-service---resource-manager-template"></a>Een service voor persoonlijke koppelingen maken-Resource Manager-sjabloon

In deze Snelstartgids gebruikt u een resource manager-sjabloon om een persoonlijke koppelings service te maken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

U kunt deze Snelstartgids ook volt ooien met behulp van de [Azure Portal](create-private-link-service-portal.md), [Azure POWERSHELL](create-private-link-service-powershell.md)of [Azure cli](create-private-link-service-cli.md).

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-private-link-service"></a>Een persoonlijke koppelings service maken

met deze sjabloon maakt u een persoonlijke koppelings service.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/101-privatelink-service/azuredeploy.json) start-sjablonen

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Micro soft. Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices) : Private Link service om privé de service beschikbaar te maken
- [**Micro soft. Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints) : persoonlijk eind punt voor toegang tot de service persoonlijk
- [**Micro soft. Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers) : Load Balancer waarmee de virtuele machines worden weer gegeven die de service hosten
- [**Micro soft. Compute/informatie**](/azure/templates/microsoft.compute/virtualmachines) : 2 virtuele machines, een die als host fungeert voor de service en één om de verbinding met het persoonlijke eind punt te testen
- [**Micro soft. Compute/informatie/Extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions) : extensie waarmee webserver wordt geïnstalleerd
- [**Micro soft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : één voor elke virtuele machine
- [**Micro soft. Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses) : 2 openbaar IP-adres, één voor elke virtuele machine
- [**Micro soft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : 2 netwerk interfaces, één voor elke virtuele machine

### <a name="deploy-the-template"></a>De sjabloon implementeren

Resource Manager-sjabloon implementeren in Azure:

1. Selecteer **implementeren naar Azure** om u aan te melden bij Azure en de sjabloon te openen. Met de sjabloon maakt u een virtuele machine, een standaard load balancer, een persoonlijke koppelings service, een persoonlijk eind punt, netwerken en een virtuele machine die u wilt valideren.

   [![Implementeren op Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Selecteer of maak een resource groep,
3. Typ de gebruikers naam en het wacht woord voor de beheerder van de virtuele machine.
4. Selecteer **Ik ga akkoord met de bovenstaande voor waarden** en selecteer vervolgens **kopen**.

## <a name="validate-the-deployment"></a>De implementatie valideren

> [!NOTE]
> de ARM-sjabloon genereert een unieke naam voor de resource van de virtuele-myConsumerVm<b>{UniqueID}</b> , vervang <b>{UniqueID}</b> door de gegenereerde waarde.

### <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM- _myConsumerVm {UniqueID}_ van het Internet:

1.  Voer _myConsumerVm {UniqueID}_ in de zoek balk van de portal in.

2.  Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

3.  Selecteer **RDP-bestand downloaden**. In Azure wordt een _RDP_-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

4.  Open het gedownloade RDP- \* bestand.

    a. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    b. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.
    
    > [!NOTE]
    > Mogelijk moet u **meer opties**selecteren  >  **een ander account gebruiken**om de referenties op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine.

5.  Selecteer **OK**.

6.  Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

7.  Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.

### <a name="access-the-http-service-privately-from-the-vm"></a>De http-service persoonlijk vanuit de VM openen

In deze sectie maakt u verbinding met de http-service van de virtuele machine met behulp van het persoonlijke eind punt.

1.  In de Extern bureaublad van _myConsumerVm {UniqueID}_
2.  Open een browser en voer het adres van het persoonlijke eind punt inhttp://10.0.0.5/
3.  U ziet de standaard-IIS-pagina

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt met de persoonlijke koppelings service niet meer nodig hebt, verwijdert u de resource groep. Hiermee verwijdert u de persoonlijke koppelings service en alle gerelateerde resources.

Als u de resource groep wilt verwijderen, roept u de `Remove-AzResourceGroup` cmdlet aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [persoonlijke Azure-koppelingen](private-link-overview.md)
