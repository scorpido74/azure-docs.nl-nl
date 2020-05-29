---
title: Azure private endpoint ARM-sjabloon
description: Meer informatie over persoonlijke Azure-koppelingen
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 97b38d9db53db5c53090df54b283e6e2c85f3e88
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84172338"
---
# <a name="create-a-private-endpoint---resource-manager-template"></a>Een privé-eind punt maken-Resource Manager-sjabloon

In deze Quick Start gebruikt u een resource manager-sjabloon om een persoonlijk eind punt te maken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

U kunt deze Snelstartgids ook volt ooien met behulp van de [Azure Portal](create-private-endpoint-portal.md), [Azure POWERSHELL](create-private-endpoint-powershell.md)of [Azure cli](create-private-endpoint-cli.md).

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-an-private-endpoint"></a>Een persoonlijk eind punt maken

met deze sjabloon maakt u een persoonlijk eind punt voor een Azure SQL-Server.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/101-private-endpoint-sql/azuredeploy.json) start-sjablonen

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json" range="001-295" highlight="131-156":::

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:


- [**Micro soft. Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints) : persoonlijk eind punt voor toegang tot de Azure SQL-Server
- [**Micro soft. Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones) : wordt gebruikt om het IP-adres van het privé-eind punt op te lossen 
- [**Micro soft. Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Micro soft. Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups) : persoonlijk eind punt koppelen aan een privé-DNS-zone
- [**Micro soft. SQL/servers**](/azure/templates/microsoft.sql/servers) : Azure SQL Server met de voorbeeld database
- [**Micro soft. SQL/servers/data bases**](/azure/templates/microsoft.sql/servers/databases) : voorbeeld database
- [**Micro soft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : Virtual Network waar het persoonlijke eind punt wordt geïmplementeerd 
- [**Micro soft. Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses) : openbaar IP-adres voor toegang tot de virtuele machine
- [**Micro soft. Compute/informatie**](/azure/templates/microsoft.compute/virtualmachines) : virtuele machine voor het testen van de particuliere verbinding met het persoonlijke eind punt naar de Azure SQL-Server
- [**Micro soft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : de netwerk interface voor de virtuele machine 

### <a name="deploy-the-template"></a>De sjabloon implementeren

Resource Manager-sjabloon implementeren in Azure:

1. Selecteer **implementeren naar Azure** om u aan te melden bij Azure en de sjabloon te openen. Met de sjabloon maakt u het persoonlijke eind punt, de Azure SQL-Server, de netwerk infrastructuur en de virtuele machines die u wilt valideren.

   [![Implementeren op Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Selecteer of maak een resource groep.
3. Typ de aanmeldings naam en het wacht woord voor de SQL-beheerder.
3. Typ de gebruikers naam en het wacht woord voor de beheerder van de virtuele machine.
3. Selecteer **Ik ga akkoord met de bovenstaande voor waarden** en selecteer vervolgens **kopen**. Het volt ooien van de implementatie kan 20 minuten of langer duren.

## <a name="validate-the-deployment"></a>De implementatie valideren
> [!NOTE]
> de ARM-sjabloon genereert een unieke naam voor de virtuele-myVm<b>{UniqueID}</b> -resource en voor de Azure SQL server sqlserver<b>{UniqueID}</b> -resource, vervangt u <b>{UniqueID}</b> door de gegenereerde waarde.

### <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM- *myVm {UniqueID}* van het Internet:

1. Voer *myVm {UniqueID}* in de zoek balk van de portal in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het bestand gedownloade. rdp *.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **meer opties**selecteren  >  **een ander account gebruiken**om de referenties op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

### <a name="access-sql-database-server-privately-from-the-vm"></a>SQL Database Server privé benaderen vanuit de VM

In deze sectie maakt u via het persoonlijke eind punt verbinding met de SQL Database-Server via de VM.

 1. Open Power shell in de Extern bureaublad van *myVM {UniqueID}*.
 2. Voer nslookup sqlserver {UniqueID}. data base. Windows. net   in, wordt er een bericht weer gegeven dat er ongeveer als volgt uitziet: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    sqlserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  sqlserver.database.windows.net 
```
 3. SQL Server Management Studio installeren 
 4. Typ of Selecteer in verbinding maken met server de volgende informatie: server type: Selecteer data base-engine.
 Server naam: Selecteer sqlserver {UniqueID}. data base. Windows. net username: Voer een gebruikers naam in die tijdens het maken is opgegeven.
 Wacht woord: Voer een wacht woord in dat u hebt opgegeven tijdens het maken.
 Wacht woord onthouden: Selecteer Ja.
 
 5. Selecteer **Verbinding maken**.
 6. Bladeren door **data bases** vanuit het menu links.
 7. Eventueel Gegevens uit een voor beeld van een *Data Base* maken of er query's op uitvoeren
 8. Sluit de verbinding met extern bureau blad met *myVm {UniqueID}*.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt met het persoonlijke eind punt niet meer nodig hebt, verwijdert u de resource groep. Hiermee verwijdert u het persoonlijke eind punt en alle gerelateerde resources.

Als u de resource groep wilt verwijderen, roept u de `Remove-AzResourceGroup` cmdlet aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [persoonlijke Azure-koppelingen](private-link-overview.md)
