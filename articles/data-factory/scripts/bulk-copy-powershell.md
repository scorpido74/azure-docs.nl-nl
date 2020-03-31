---
title: Gegevens in bulk kopiëren met PowerShell
description: Dit PowerShell-script laat zien hoe u Azure Data Factory gebruikt om gegevens uit een brongegevensarchief in bulk naar een doelgegevensarchief te kopiëren.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: efc79f82a2181099f832da0d4a17fc370bf4f7f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929870"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell-script - meerdere tabellen in bulk kopiëren met Azure Data Factory

Met dit voorbeeld kopieert PowerShell-script gegevens uit meerdere tabellen in een Azure SQL-database naar een Azure SQL-gegevensmagazijn.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Zie [zelfstudie: bulkkopie](../tutorial-bulk-copy.md#prerequisites) voor de vereisten voor het uitvoeren van dit voorbeeld.

## <a name="sample-script"></a>Voorbeeldscript

> [!IMPORTANT]
> Met dit script worden JSON-bestanden gemaakt die entiteiten in Gegevensfabriek (gekoppelde service, gegevensset en pijplijn) definiëren op uw harde schijf in de c:\ Map.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Nadat u het voorbeeldscript hebt uitgevoerd, u de volgende opdracht gebruiken om de resourcegroep en alle bijbehorende resources te verwijderen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Voer de volgende opdracht uit om het gegevensfabriek uit de resourcegroep te verwijderen: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Een gegevensfactory maken. |
| [Set-azdatafactoryv2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Hiermee maakt u een gekoppelde service in de gegevensfabriek. Een gekoppelde service koppelt een gegevensarchief of gegevensbestand aan een gegevensfabriek. |
| [Set-azdatafactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Hiermee maakt u een gegevensset in de gegevensfabriek. Een gegevensset vertegenwoordigt invoer/uitvoer voor een activiteit in een pijplijn. | 
| [Set-azdatafactoryV2-pijplijn](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Hiermee maakt u een pijplijn in de gegevensfabriek. Een pijplijn bevat een of meer activiteiten die een bepaalde bewerking uitvoeren. In deze pijplijn kopieert een kopieeractiviteit gegevens van de ene locatie naar de andere locatie in een Azure Blob Storage. |
| [Invoke-azdatafactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Hiermee maakt u een run voor de pijplijn. Met andere woorden, loopt de pijplijn. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | Hier vindt u meer informatie over de uitvoering van de activiteit (activiteitsuitvoering) in de pijplijn. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Factory PowerShell-scriptvoorbeelden zijn te vinden in de [Azure Data Factory PowerShell-scripts.](../samples-powershell.md)
