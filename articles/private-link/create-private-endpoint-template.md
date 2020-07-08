---
title: Een persoonlijk eind punt maken in een persoonlijke Azure-koppeling
description: In deze Quick Start gebruikt u een Azure Resource Manager sjabloon om een persoonlijk eind punt te maken.
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: a60edde222a6200a0378cd8c9c4f4774da9c2e50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84817972"
---
# <a name="quickstart-create-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Snelstartgids: een persoonlijk eind punt maken met behulp van een Azure Resource Manager sjabloon

In deze Quick Start gebruikt u een Azure Resource Manager sjabloon om een persoonlijk eind punt te maken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

U kunt deze Snelstartgids ook volt ooien met behulp van de [Azure Portal](create-private-endpoint-portal.md), [Azure PowerShell](create-private-endpoint-powershell.md)of de [Azure cli](create-private-endpoint-cli.md).

## <a name="prerequisite"></a>Vereiste

U hebt een Azure-account met een actief abonnement nodig. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

Met deze sjabloon maakt u een persoonlijk eind punt voor een exemplaar van Azure SQL Database.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/).

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json" range="001-295" highlight="131-156":::

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Micro soft. SQL/servers**](/azure/templates/microsoft.sql/servers): het exemplaar van SQL database met de voorbeeld database.
- [**Micro soft. SQL/servers/data bases**](/azure/templates/microsoft.sql/servers/databases): de voorbeeld database.
- [**Micro soft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): het virtuele netwerk waarin het persoonlijke eind punt wordt geïmplementeerd.
- [**Micro soft. Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints): het persoonlijke eind punt om toegang te krijgen tot het exemplaar van SQL database.
- [**Micro soft. Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones): de zone die wordt gebruikt om het IP-adres van het privé-eind punt op te lossen.
- [**Micro soft. Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Micro soft. Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups): de zone groep die wordt gebruikt om het persoonlijke eind punt te koppelen aan een privé-DNS-zone.
- [**Micro soft. Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): het open bare IP-adres dat wordt gebruikt voor toegang tot de virtuele machine.
- [**Micro soft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): de netwerk interface voor de virtuele machine.
- [**Micro soft. Compute/informatie**](/azure/templates/microsoft.compute/virtualmachines): de virtuele machine die wordt gebruikt voor het testen van de particuliere verbinding met het persoonlijke eind punt naar het exemplaar van SQL database.

### <a name="deploy-the-template"></a>De sjabloon implementeren

U kunt als volgt de Azure Resource Manager-sjabloon implementeren in Azure:

1. Als u zich wilt aanmelden bij Azure en de sjabloon wilt openen, selecteert **u implementeren naar Azure**. Met de sjabloon maakt u het persoonlijke eind punt, het exemplaar van SQL Database, de netwerk infrastructuur en een virtuele machine die moet worden gevalideerd.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Selecteer of maak een resource groep.
3. Typ de aanmelding en het wacht woord voor de SQL-beheerder.
4. Typ de gebruikers naam en het wacht woord voor de beheerder van de virtuele machine.
5. Lees de instructie voor waarden. Als u akkoord gaat, selecteert u **Ik ga akkoord met de voor waarden die hierboven worden vermeld**  >  **Purchase**. De implementatie kan 20 minuten of langer duren.

## <a name="validate-the-deployment"></a>De implementatie valideren

> [!NOTE]
> De Azure Resource Manager sjabloon genereert een unieke naam voor de virtuele-myVm<b>{UniqueID}</b> -resource en voor de SQL database sqlserver<b>{UniqueID}</b> -resource. Vervang de gegenereerde waarde voor **{UniqueID}**.

### <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM- _myVm {UniqueID}_ van het Internet:

1. Voer _myVm {UniqueID}_ in de zoek balk van de portal in.

2. Selecteer **Verbinding maken**. **Verbinding maken met de virtuele machine** wordt geopend.

3. Selecteer **RDP-bestand downloaden**. In Azure wordt een _RDP_-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

4. Open het gedownloade RDP-bestand.

   a. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

   b. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven tijdens het maken van de virtuele machine.

      > [!NOTE]
      > Mogelijk moet u **meer opties**selecteren  >  **een ander account gebruiken**om de referenties op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine.

5. Selecteer **OK**.

6. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

7. Wanneer het VM-bureau blad wordt weer gegeven, minimaliseert u het om terug te gaan naar het lokale bureau blad.

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>De SQL Database-Server privé openen vanuit de VM

U kunt als volgt via het persoonlijke eind punt verbinding maken met de SQL Database-Server vanaf de VM.

1.  Open Power shell in de Extern bureaublad van _myVM {UniqueID}_.
2.  Voer het volgende in: nslookup sqlserver {UniqueID}. data base. Windows. net. 
    U ontvangt een bericht dat er ongeveer als volgt uitziet:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Installeer SQL Server Management Studio.
4.  Typ of Selecteer in **verbinding maken met server**de volgende informatie:
    - **Server type**: Selecteer **Data base-engine**.
    - **Server naam**: Selecteer **sqlserver {UniqueID}. data base. Windows. net**.
    - **Gebruikers naam**: Voer een gebruikers naam in die tijdens het maken is opgegeven.
    - **Wacht woord**: Voer een wacht woord in dat u hebt opgegeven tijdens het maken.
    - **Wacht woord onthouden**: Selecteer **Ja**.

5.  Selecteer **Verbinding maken**.
6.  Ga in het menu aan de linkerkant naar **data bases**.
7.  U kunt desgewenst informatie uit voor _beeld-DB_maken of er query's op uitvoeren.
8.  Sluit de Extern bureaublad verbinding met _myVm {UniqueID}_.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt met het persoonlijke eind punt niet meer nodig hebt, verwijdert u de resource groep. Hiermee verwijdert u het persoonlijke eind punt en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [persoonlijke Azure-koppelingen](private-link-overview.md).
