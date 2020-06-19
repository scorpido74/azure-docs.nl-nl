---
title: Een persoonlijke koppelings service maken in een persoonlijke Azure-koppeling
description: In deze Snelstartgids gebruikt u een Azure Resource Manager sjabloon om een persoonlijke koppelings service te maken.
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: c9ed628501e8fa02b816a1564b91620404dfc379
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84817618"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-azure-resource-manager-template"></a>Snelstartgids: een persoonlijke koppelings service maken met behulp van een Azure Resource Manager sjabloon

In deze Snelstartgids gebruikt u een Azure Resource Manager sjabloon om een persoonlijke koppelings service te maken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

U kunt deze Snelstartgids ook volt ooien met behulp van de [Azure Portal](create-private-link-service-portal.md), [Azure PowerShell](create-private-link-service-powershell.md)of de [Azure cli](create-private-link-service-cli.md).

## <a name="prerequisite"></a>Vereiste

U hebt een Azure-account met een actief abonnement nodig. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-private-link-service"></a>Een persoonlijke koppelings service maken

Met deze sjabloon maakt u een persoonlijke koppelings service.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/).

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Micro soft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): er is één virtueel netwerk voor elke virtuele machine.
- [**Micro soft. Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): de Load Balancer waarmee de virtuele machines worden weer gegeven die als host voor de service optreden.
- [**Micro soft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): er zijn twee netwerk interfaces, één voor elke virtuele machine.
- [**Micro soft. Compute/informatie**](/azure/templates/microsoft.compute/virtualmachines): er zijn twee virtuele machines, één die als host fungeert voor de service en een die de verbinding met het persoonlijke eind punt test.
- [**Micro soft. Compute/informatie/Extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions): de uitbrei ding voor het installeren van een webserver.
- [**Micro soft. Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices): de service private link om de service beschikbaar te maken.
- [**Micro soft. Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): er zijn twee open bare IP-adressen, één voor elke virtuele machine.
- [**Micro soft. Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints): het persoonlijke eind punt voor toegang tot de service.

### <a name="deploy-the-template"></a>De sjabloon implementeren

U kunt als volgt de Azure Resource Manager-sjabloon implementeren in Azure:

1. Als u zich wilt aanmelden bij Azure en de sjabloon wilt openen, selecteert **u implementeren naar Azure**. Met de sjabloon maakt u een virtuele machine, een standaard load balancer, een persoonlijke koppelings service, een persoonlijk eind punt, netwerken en een virtuele machine die u wilt valideren.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Selecteer of maak een resource groep.
3. Typ de gebruikers naam en het wacht woord voor de beheerder van de virtuele machine.
4. Lees de instructie voor waarden. Als u akkoord gaat, selecteert u **Ik ga akkoord met de voor waarden die hierboven worden vermeld**  >  **Purchase**.

## <a name="validate-the-deployment"></a>De implementatie valideren

> [!NOTE]
> De Azure Resource Manager sjabloon genereert een unieke naam voor de resource van de virtuele-myConsumerVm<b>{UniqueID}</b> . Vervang de gegenereerde waarde voor **{UniqueID}**.

### <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM- _myConsumerVm {UniqueID}_ van het Internet:

1.  Voer _myConsumerVm {UniqueID}_ in de zoek balk van de portal in.

2.  Selecteer **Verbinden**. **Verbinding maken met de virtuele machine** wordt geopend.

3.  Selecteer **RDP-bestand downloaden**. In Azure wordt een _RDP_-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

4.  Open het gedownloade RDP-bestand.

    a. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    b. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven tijdens het maken van de virtuele machine.
    
    > [!NOTE]
    > Mogelijk moet u **meer opties**selecteren  >  **een ander account gebruiken**om de referenties op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine.

5.  Selecteer **OK**.

6.  Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

7.  Wanneer het VM-bureau blad wordt weer gegeven, minimaliseert u het om terug te gaan naar het lokale bureau blad.

### <a name="access-the-http-service-privately-from-the-vm"></a>De http-service persoonlijk vanuit de VM openen

Hier volgt een beschrijving van het maken van een verbinding met de http-service van de virtuele machine met behulp van het privé-eind punt.

1.  Ga naar de Extern bureaublad van _myConsumerVm {UniqueID}_.
2.  Open een browser en voer het adres van het persoonlijke eind punt in: http://10.0.0.5/ .
3.  De standaard-IIS-pagina wordt weer gegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt met de persoonlijke koppelings service niet meer nodig hebt, verwijdert u de resource groep. Hiermee verwijdert u de persoonlijke koppelings service en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [persoonlijke Azure-koppelingen](private-link-overview.md).
