---
title: Gegevens in de cloud kopiëren met PowerShell
description: Met dit PowerShell-script worden gegevens van de ene locatie in een Azure Blob-opslag naar een andere locatie in dezelfde Blob-opslag kopieert.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 2e289f30ef1c1883c38884eb563a41bccc841329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79462648"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>PowerShell gebruiken om een pijplijn voor gegevensfabrieken te maken om gegevens in de cloud te kopiëren

Met dit voorbeeld maakt PowerShell-script een pijplijn in Azure Data Factory die gegevens kopieert van de ene locatie naar de andere locatie in een Azure Blob Storage.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten
* **Azure Storage-account**. U gebruikt de blob-opslag als de gegevensopslag voor de **bron** en de **sink**. Als u geen Azure Storage-account hebt, raadpleegt u het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) om er een te maken. 
* Maak een **blob-container** in Blob Storage, maak een **invoermap** in de container en upload een aantal bestanden naar de map. U kunt hulpprogramma's zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken om verbinding te maken met Azure Blob Storage, een blob-container te maken, een invoerbestand te uploaden en het uitvoerbestand te controleren.

## <a name="sample-script"></a>Voorbeeldscript

> [!IMPORTANT]
> Met dit script worden JSON-bestanden gemaakt die entiteiten in Gegevensfabriek (gekoppelde service, gegevensset en pijplijn) definiëren op uw harde schijf in de c:\ Map.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Een gegevensfactory maken. |
| [Set-azdatafactoryv2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Hiermee maakt u een gekoppelde service in de gegevensfabriek. Een gekoppelde service koppelt een gegevensarchief of gegevensbestand aan een gegevensfabriek. |
| [Set-azdatafactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Hiermee maakt u een gegevensset in de gegevensfabriek. Een gegevensset vertegenwoordigt invoer/uitvoer voor een activiteit in een pijplijn. |
| [Set-azdatafactoryV2-pijplijn](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Hiermee maakt u een pijplijn in de gegevensfabriek. Een pijplijn bevat een of meer activiteiten die een bepaalde bewerking uitvoeren. In deze pijplijn kopieert een kopieeractiviteit gegevens van de ene locatie naar de andere locatie in een Azure Blob Storage. |
| [Invoke-azdatafactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Hiermee maakt u een run voor de pijplijn. Met andere woorden, loopt de pijplijn. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Hier vindt u meer informatie over de uitvoering van de activiteit (activiteitsuitvoering) in de pijplijn.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Factory PowerShell-scriptvoorbeelden zijn te vinden in de [PowerShell-voorbeelden](../samples-powershell.md)van Azure Data Factory.
