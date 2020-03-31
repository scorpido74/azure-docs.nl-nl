---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175710"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Tabelentiteiten beheren

Nu u een tabel hebt, bekijken we hoe u entiteiten of rijen in de tabel beheren. 

Entiteiten kunnen maximaal 255 eigenschappen hebben, waaronder drie systeemeigenschappen: **PartitionKey,** **RowKey**en **Timestamp**. U bent verantwoordelijk voor het invoegen en bijwerken van de waarden **partitionkey** en **RowKey.** De server beheert de waarde van **Timestamp,** die niet kan worden gewijzigd. Samen identificeren **partitionkey** en **RowKey** op unieke wijze elke entiteit binnen een tabel.

* **PartitionKey:** hiermee bepaalt u de partitie waarin de entiteit is opgeslagen.
* **RowKey:** identificeert op unieke wijze de entiteit binnen de partitie.

U maximaal 252 aangepaste eigenschappen voor een entiteit definiëren. 

### <a name="add-table-entities"></a>Tabelentiteiten toevoegen

Voeg entiteiten toe aan een tabel met **Add-AzTableRow**. In deze voorbeelden worden `partition1` partitiesleutels gebruikt met waarden en `partition2`rijsleutels die gelijk zijn aan statusafkortingen. De eigenschappen in `username` elke `userid`entiteit zijn en . 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>De tabelentiteiten opvragen

U de entiteiten in een tabel opvragen met de opdracht **Get-AzTableRow.**

> [!NOTE]
> De cmdlets **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByName**en **Get-AzureStorageTableRowByCustomFilter** worden afgeschaft en worden verwijderd in een toekomstige versie-update.

#### <a name="retrieve-all-entities"></a>Alle entiteiten ophalen

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Met deze opdracht worden resultaten opgeleverd die vergelijkbaar zijn met de volgende tabel:

| userid | gebruikersnaam | partitie | rijsleutel |
|----|---------|---------------|----|
| 1 | Chris | partitie1 | CA |
| 3 | Christine | partitie1 | WA |
| 2 | Jessie | partitie2 | NM |
| 4 | Steven | partitie2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Entiteiten voor een specifieke partitie ophalen

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

De resultaten lijken op de volgende tabel:

| userid | gebruikersnaam | partitie | rijsleutel |
|----|---------|---------------|----|
| 1 | Chris | partitie1 | CA |
| 3 | Christine | partitie1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Entiteiten voor een specifieke waarde in een specifieke kolom ophalen

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Met deze query wordt één record opgehaald.

|veld|waarde|
|----|----|
| userid | 1 |
| gebruikersnaam | Chris |
| PartitionKey | partitie1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Entiteiten ophalen met een aangepast filter 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Met deze query wordt één record opgehaald.

|veld|waarde|
|----|----|
| userid | 1 |
| gebruikersnaam | Chris |
| PartitionKey | partitie1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Entiteiten bijwerken 

Er zijn drie stappen voor het bijwerken van entiteiten. Haal eerst de entiteit op die moet worden gewijzigd. Ten tweede, maak de verandering. Ten derde, commit the change using **Update-AzTableRow**.

Update de entiteit met gebruikersnaam = 'Jessie' om gebruikersnaam = 'Jessie2' te hebben. In dit voorbeeld wordt ook een andere manier weergegeven om een aangepast filter te maken met .NET-typen.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

De resultaten tonen de Jessie2 record.

|veld|waarde|
|----|----|
| userid | 2 |
| gebruikersnaam | Jessie2 (Jessie2) |
| PartitionKey | partitie2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Tabelentiteiten verwijderen

U één entiteit of alle entiteiten in de tabel verwijderen.

#### <a name="deleting-one-entity"></a>Eén entiteit verwijderen

Als u één entiteit wilt verwijderen, krijgt u een verwijzing naar die entiteit en pipetteert u deze naar **Remove-AzTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Alle entiteiten in de tabel verwijderen

Als u alle entiteiten in de tabel wilt verwijderen, haalt u deze op en leidt u de resultaten naar de verwijdercmdlet. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
