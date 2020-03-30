---
title: Gegevens van on-premises naar Azure kopiëren met PowerShell
description: Met dit PowerShell-script worden gegevens uit een on-premises SQL Server-database gekopieerd naar een andere een Azure Blob Storage.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: jingwang
author: linda33wj
manager: shwang
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 10555defc4888af66bb88d19190b6543aa8ae0c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974690"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>PowerShell gebruiken om een pijplijn in gegevensfabrieken te maken om gegevens van on-premises naar Azure te kopiëren

Met dit voorbeeld maakt PowerShell-script een pijplijn in Azure Data Factory die gegevens kopieert van een on-premises SQL Server-database naar een Azure Blob Storage.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten

- **SQL Server**. U gebruikt een on-premises SQL Server-database als **brongegevensarchief** in dit voorbeeld.
- **Azure Storage-account**. U gebruikt Azure blob-opslag als **een doel-/sinkgegevensarchief** in dit voorbeeld. Als u geen Azure Storage-account hebt, raadpleegt u het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) voor de stappen voor het maken van een account.
- **Zelf gehoste runtime voor integratie**. Download MSI-bestand uit het [downloadcentrum](https://www.microsoft.com/download/details.aspx?id=39717) en voer het uit om een zelfgehoste ingebruikloop voor integratie op uw machine te installeren.  

### <a name="create-sample-database-in-sql-server"></a>Voorbeelddatabase maken in SQL Server
1. Maak in de on-premises SQL Server-database een tabel met de naam **emp** met behulp van het volgende SQL-script:

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

2. Voeg enkele voorbeeldgegevens in de tabel in:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Voorbeeldscript

> [!IMPORTANT]
> Met dit script worden JSON-bestanden gemaakt die entiteiten in Gegevensfabriek (gekoppelde service, gegevensset en pijplijn) definiëren op uw harde schijf in de c:\ Map.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


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
| [Nieuw-azdatafactoryv2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Hiermee versleutelt u referenties in een gekoppelde service en genereert u een nieuwe gekoppelde servicedefinitie met de versleutelde referenties.
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
