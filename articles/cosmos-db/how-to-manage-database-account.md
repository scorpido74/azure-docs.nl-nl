---
title: Informatie over het beheren van databaseaccounts in Azure Cosmos DB
description: Meer informatie over het beheren van Azure Cosmos DB-resources met behulp van de azure-portal-, PowerShell-, CLI- en Azure Resource Manager-sjablonen
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247382"
---
# <a name="manage-an-azure-cosmos-account"></a>Een Azure Cosmos-account beheren

In dit artikel wordt beschreven hoe u verschillende taken in een Azure Cosmos-account kunt beheren met behulp van de Azure-portal, Azure PowerShell, Azure CLI en Azure Resource Manager-sjablonen.

## <a name="create-an-account"></a>Een account maken

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Azure-portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure-CLI

Zie [Een Azure Cosmos DB-account maken met Azure CLI](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Zie [Een Azure Cosmos DB-account maken met Powershell](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Azure Resource Manager-sjabloon

Met deze sjabloon Azure Resource Manager wordt een Azure Cosmos-account voor SQL API gemaakt dat is geconfigureerd met twee regio's en opties om consistentieniveau, automatische failover en multimaster te selecteren. Als u deze sjabloon wilt implementeren, klikt u op Implementeren naar Azure op de [leespagina, Azure Cosmos-account maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## <a name="addremove-regions-from-your-database-account"></a>Regio's toevoegen aan of verwijderen uit uw databaseaccount

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Ga naar uw Azure Cosmos-account en open het **menu Gegevens repliceren wereldwijd.**

1. Als u regio's wilt toevoegen, **+** selecteert u de zeshoeken op de kaart met het label dat overeenkomt met de gewenste regio(s). U ook een regio toevoegen, de optie **Regio + Toevoegen** selecteren en een regio kiezen in het vervolgkeuzemenu.

1. Als u regio's wilt verwijderen, wist u een of meer regio's van de kaart door de blauwe zeshoeken met vinkjes te selecteren. Of selecteer het prullenbakpictogram (🗑) rechts van de regio.

1. Selecteer **OK** om uw wijzigingen op te slaan.

   ![Menu Regio's toevoegen of verwijderen](./media/how-to-manage-database-account/add-region.png)

In een schrijfmodus voor één regio kunt u de schrijfregio niet verwijderen. U dient dan failover toe te passen naar een andere regio voordat u de huidige schrijfregio kunt verwijderen.

In een schrijfmodus voor meerdere regio's kunt u elke regio toevoegen of verwijderen als er minstens één regio is.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure-CLI

Zie [Regio's toevoegen of verwijderen met Azure CLI](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Zie [Regio's toevoegen of verwijderen met Powershell](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Meerdere schrijfregio's configureren

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Azure-portal

Open het tabblad **Gegevens repliceren Wereldwijd** en selecteer Inschakelen om schrijven met meerdere regio's in te schakelen. **Enable** Nadat u meerdere regio-schrijfbewerkingen hebt ingeschakeld, worden alle leesregio's die u momenteel op het account hebt, lees- en schrijfregio's.

![Azure Cosmos-account configureert schermafbeelding van meerdere master](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure-CLI

Zie [Regio's met meerdere schrijfgebieden inschakelen met Azure CLI](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Zie [Regio's voor meerdere schrijven inschakelen met Powershell](manage-with-powershell.md#multi-master)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Sjabloon ResourceManager

Een account kan worden gemigreerd van single-master naar multi-master door de `enableMultipleWriteLocations: true`resourcemanagersjabloon te implementeren die wordt gebruikt om het account en de instelling te maken. De volgende Azure Resource Manager-sjabloon is een sjabloon met een absoluut minimum sjabloon waarmee een Azure Cosmos-account voor SQL API wordt geïmplementeerd met twee regio's en meerdere schrijflocaties ingeschakeld.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Automatische failover inschakelen voor uw Azure Cosmos-account

Met de optie Automatische failover kan Azure Cosmos DB mislukken naar de regio met de hoogste failoverprioriteit zonder dat een regio niet beschikbaar is. Wanneer automatische failover is ingeschakeld, kan de prioriteit van de regio worden gewijzigd. Account moet twee of meer regio's hebben om automatische failover in te schakelen.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Azure-portal

1. Open vanuit uw Azure Cosmos-account het deelvenster **Gegevens repliceren wereldwijd.**

2. Selecteer bovenin het deelvenster de knop **Automatische failover**.

   ![Menu Gegevens globaal repliceren](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Controleer in het deelvenster **Automatische failover** of **Automatische failover inschakelen** is ingesteld op **AAN**. 

4. Selecteer **Opslaan**.

   ![Portalmenu Automatische failover](./media/how-to-manage-database-account/automatic-failover.png)

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure-CLI

Zie [Automatische failover inschakelen met Azure CLI](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Zie [Automatische failover inschakelen met Powershell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Failoverprioriteiten instellen voor uw Azure Cosmos-account

Nadat een Cosmos-account is geconfigureerd voor automatische failover, kan de failoverprioriteit voor regio's worden gewijzigd.

> [!IMPORTANT]
> U het schrijfgebied (failoverprioriteit van nul) niet wijzigen wanneer het account is geconfigureerd voor automatische failover. Als u het schrijfgebied wilt wijzigen, moet u automatische failover uitschakelen en een handmatige failover doen.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Azure-portal

1. Open vanuit uw Azure Cosmos-account het deelvenster **Gegevens repliceren wereldwijd.**

2. Selecteer bovenin het deelvenster de knop **Automatische failover**.

   ![Menu Gegevens globaal repliceren](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Controleer in het deelvenster **Automatische failover** of **Automatische failover inschakelen** is ingesteld op **AAN**.

4. Als u de failoverprioriteit wilt wijzigen, sleept u de leesregio's met behulp van de drie puntjes aan de linkerkant van de rij, die worden weergegeven wanneer u de muisaanwijzer erboven houdt.

5. Selecteer **Opslaan**.

   ![Portalmenu Automatische failover](./media/how-to-manage-database-account/automatic-failover.png)

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure-CLI

Zie [Failoverprioriteit instellen met Azure CLI](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Zie [Failoverprioriteit instellen met Powershell](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Handmatige failover uitvoeren op een Azure Cosmos-account

> [!IMPORTANT]
> Het Azure Cosmos-account moet zijn geconfigureerd voor handmatige failover om deze bewerking te laten slagen.

Het proces voor het uitvoeren van een handmatige failover omvat het wijzigen van het schrijfgebied van het account (failoverprioriteit = 0) naar een andere regio die is geconfigureerd voor het account.

> [!NOTE]
> Multimaster-accounts kunnen niet handmatig worden mislukt. Voor toepassingen die de Azure Cosmos SDK gebruiken, detecteert de SDK wanneer een regio niet beschikbaar is en wordt deze automatisch omgeleid naar de volgende dichtstbijzijnde regio als u een API met meerdere instanties in de SDK gebruikt.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Azure-portal

1. Ga naar uw Azure Cosmos-account en open het **menu Gegevens repliceren wereldwijd.**

2. Selecteer bovenaan het menu de optie **Handmatige failover**.

   ![Menu Gegevens globaal repliceren](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Selecteer in het menu **Handmatige failover** de nieuwe schrijfregio. Schakel het selectievakje in om aan te geven dat u begrijpt dat met deze optie de schrijfregio wordt gewijzigd.

4. Selecteer **OK** om de failover te activeren.

   ![Portalmenu Handmatige failover](./media/how-to-manage-database-account/manual-failover.png)

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure-CLI

Zie [Handmatige failover activeren met Azure CLI](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Zie [Handmatige failover activeren met Powershell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie en voorbeelden over het beheren van het Azure Cosmos-account en de database en containers:

* [Azure Cosmos DB beheren met Azure PowerShell](manage-with-powershell.md)
* [Azure Cosmos DB beheren met behulp van Azure CLI](manage-with-cli.md)
