---
title: 'Power shell-script: gegevens stapsgewijs laden met behulp van Azure Data Factory '
description: Dit Power shell-script laat zien hoe u Azure Data Factory kunt gebruiken om gegevens stapsgewijs van een Azure SQL Database naar een Azure-Blob Storage te kopiëren.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: ef7b00172f0371e09d6462ed357744166b5fb08a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684328"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>Power shell-script: gegevens stapsgewijs laden met behulp van Azure Data Factory
Met dit Power shell-voorbeeld script worden alleen nieuwe of bijgewerkte records van een brongegevens archief naar een Sink-gegevens archief geladen na de eerste volledige kopie van gegevens van de bron naar de sink.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Zie [zelf studie: incrementele kopie](../tutorial-incremental-copy-powershell.md#prerequisites) voor de vereisten voor het uitvoeren van dit voor beeld. 

## <a name="sample-script"></a>Voorbeeldscript

> [!IMPORTANT]
> Met dit script worden JSON-bestanden gemaakt waarmee Data Factory entiteiten (gekoppelde service, gegevensset en pijp lijn) op de vaste schijf worden gedefinieerd in de c:\ map.

[!code-powershell[main](../../../powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1 "Incremental copy from Azure SQL Database to Azure Blob Storage")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Nadat u het voorbeeld script hebt uitgevoerd, kunt u de volgende opdracht gebruiken om de resource groep en alle bijbehorende resources te verwijderen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Als u de data factory uit de resource groep wilt verwijderen, voert u de volgende opdracht uit: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Maak een gegevensfactory. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Hiermee maakt u een gekoppelde service in de data factory. Een gekoppelde service koppelt een gegevens archief of kan worden berekend op een data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Hiermee maakt u een gegevensset in de data factory. Een gegevensset vertegenwoordigt invoer/uitvoer voor een activiteit in een pijp lijn. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Hiermee maakt u een pijp lijn in de data factory. Een pijp lijn bevat een of meer activiteiten die een bepaalde bewerking uitvoeren. In deze pijp lijn kopieert een Kopieer activiteit gegevens van de ene locatie naar een andere locatie in een Azure-Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Hiermee maakt u een uitvoering voor de pijp lijn. Met andere woorden, de pijp lijn uitvoeren. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Haalt Details op over de uitvoering van de activiteit (uitvoering van de activiteit) in de pijp lijn. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Factory Power shell-script voorbeelden vindt u in de [Azure Data Factory Power shell-scripts](../samples-powershell.md).
