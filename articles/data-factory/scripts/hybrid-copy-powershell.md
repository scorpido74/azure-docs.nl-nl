---
title: Gegevens van on-premises naar Azure kopiëren met Power shell
description: Met dit Power shell-script worden gegevens gekopieerd van een on-premises SQL Server Data Base naar een andere Azure-Blob Storage.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.author: jingwang
author: linda33wj
manager: shwang
ms.date: 10/31/2017
ms.openlocfilehash: f75c4316383120e198ab57501e31c0c42a8917f2
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890841"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Power shell gebruiken om een data factory pijp lijn te maken voor het kopiëren van gegevens van on-premises naar Azure

Met dit Power shell-voorbeeld script maakt u een pijp lijn in Azure Data Factory waarmee gegevens van een on-premises SQL Server-Data Base naar een Azure-Blob Storage worden gekopieerd.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten

- **SQL Server**. In dit voor beeld gebruikt u een on-premises SQL Server-Data Base als een **brongegevens** opslag.
- **Een Azure Storage-account**. U gebruikt Azure Blob Storage als een **doel/Sink-** gegevens archief in dit voor beeld. Als u geen Azure Storage-account hebt, raadpleegt u het artikel [Een opslagaccount maken](../../storage/common/storage-quickstart-create-account.md) voor de stappen voor het maken van een account.
- **Zelf-hostende Integration runtime**. Down load het MSI-bestand uit het [Download centrum](https://www.microsoft.com/download/details.aspx?id=39717) en voer het uit om een zelf-hostende Integration runtime op uw computer te installeren.  

### <a name="create-sample-database-in-sql-server"></a>Voorbeeld database maken in SQL Server
1. Maak in de on-premises SQL Server-Data Base een tabel met de naam **EMP** met behulp van het volgende SQL-script: 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Voeg enkele voorbeeld gegevens in de tabel in:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Voorbeeldscript

> [!IMPORTANT]
> Met dit script worden JSON-bestanden gemaakt waarmee Data Factory entiteiten (gekoppelde service, gegevensset en pijp lijn) op de vaste schijf worden gedefinieerd in de c:\ map.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


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
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Een gegevensfactory maakt. |
| [New-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Versleutelt referenties in een gekoppelde service en genereert een nieuwe definitie van de gekoppelde service met de versleutelde referentie. 
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Hiermee maakt u een gekoppelde service in de data factory. Een gekoppelde service koppelt een gegevens archief of kan worden berekend op een data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Hiermee maakt u een gegevensset in de data factory. Een gegevensset vertegenwoordigt invoer/uitvoer voor een activiteit in een pijp lijn. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Hiermee maakt u een pijp lijn in de data factory. Een pijp lijn bevat een of meer activiteiten die een bepaalde bewerking uitvoeren. In deze pijp lijn kopieert een Kopieer activiteit gegevens van de ene locatie naar een andere locatie in een Azure-Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Hiermee maakt u een uitvoering voor de pijp lijn. Met andere woorden, de pijp lijn uitvoeren. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Haalt Details op over de uitvoering van de activiteit (uitvoering van de activiteit) in de pijp lijn. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Factory Power shell-voorbeeld scripts vindt u in de [Azure Data Factory Power shell](../samples-powershell.md)-voor beelden.
