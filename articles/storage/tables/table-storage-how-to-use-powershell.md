---
title: Azure Table-opslagbewerkingen uitvoeren met PowerShell | Microsoft Documenten
description: Meer informatie over het uitvoeren van algemene taken, zoals het maken, opvragen en verwijderen van gegevens uit Azure Table-opslagaccount met PowerShell.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.openlocfilehash: f1846fae4cbf473df688a2b184c307d72ab2f8d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721469"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Azure Table-opslagbewerkingen uitvoeren met Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table-opslag is een NoSQL-gegevensarchief dat u gebruiken om enorme sets gestructureerde, niet-relationele gegevens op te slaan en op te vragen. De belangrijkste onderdelen van de service zijn tabellen, entiteiten en eigenschappen. Een tabel is een verzameling entiteiten. Een entiteit is een set eigenschappen. Elke entiteit kan maximaal 252 eigenschappen hebben, allemaal naam-waardeparen. In dit artikel wordt ervan uitgegaan dat u al bekend bent met de azure table storage service-concepten. Zie [Inzicht in het tabelservicegegevensmodel](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) en Aan de slag met Azure [Table-opslag met .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)voor gedetailleerde informatie.

Dit artikel over de eigen lijst heeft betrekking op veelvoorkomende Azure Table-opslagbewerkingen. Procedures voor: 

> [!div class="checklist"]
> * Een tabel maken
> * Een tabel ophalen
> * Tabelentiteiten toevoegen
> * Een tabel opvragen
> * Tabelentiteiten verwijderen
> * Een tabel verwijderen

In dit artikel ziet u hoe u een nieuw Azure Storage-account maakt in een nieuwe brongroep, zodat u het eenvoudig verwijderen wanneer u klaar bent. Als u liever een bestaand opslagaccount gebruikt, u dat in plaats daarvan doen.

De voorbeelden vereisen Az `Az.Storage (1.1.0 or greater)` PowerShell-modules en `Az.Resources (1.2.0 or greater)`. Voer in een PowerShell-venster uit `Get-Module -ListAvailable Az*` om de versie te vinden. Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps)als er niets wordt weergegeven of als u moet upgraden.

> [!IMPORTANT]
> Als u deze Azure-functie van `Az` PowerShell gebruikt, moet u de module hebben geïnstalleerd. De huidige `AzTable` versie van is niet compatibel met de oudere AzureRM-module.
> Volg indien nodig de [laatste installatie-instructies voor het installeren van de Az-module.](/powershell/azure/install-az-ps)

Nadat Azure PowerShell is geïnstalleerd of bijgewerkt, moet u module **AzTable**installeren, die de opdrachten heeft voor het beheren van de entiteiten. Als u deze module wilt installeren, voert u PowerShell uit als beheerder en gebruikt u de opdracht **Installatiemodule.**

> [!IMPORTANT]
> Om redenen van modulenaam publiceren we nog steeds `AzureRmStorageTables` dezelfde module onder de oude naam in PowerShell Gallery. In dit document wordt alleen naar de nieuwe naam verwezen.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de opdracht `Add-AzAccount` en volg de instructies op het scherm.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Lijst met locaties ophalen

Als u niet weet welke locatie u kunt gebruiken, kunt u een lijst met de beschikbare locaties weergeven. Selecteer de gewenste locatie in de lijst. Deze voorbeelden gebruiken **eastus**. Sla deze waarde op in de variabele **locatie** voor toekomstig gebruik.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Sla de naam van de resourcegroep op in een variabele voor toekomstig gebruik. In dit voorbeeld wordt een resourcegroep met de naam *pshtablesrg* gemaakt in de *regio Eastus.*

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Een opslagaccount maken

Maak een standaard opslagaccount voor algemene doeleinden met lokaal redundante opslag (LRS) met behulp van [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Zorg ervoor dat u een unieke naam van het opslagaccount opgeeft. Download vervolgens de context die het opslagaccount vertegenwoordigt. Wanneer u handelt op een opslagaccount, u verwijzen naar de context in plaats van herhaaldelijk uw referenties op te geven.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Een nieuwe tabel maken

Als u een tabel wilt maken, gebruikt u de cmdlet [Nieuw-AzStorageTable.](/powershell/module/az.storage/New-AzStorageTable) In dit voorbeeld wordt `pshtesttable`de tabel genoemd .

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Een lijst met tabellen in het opslagaccount ophalen

Een lijst met tabellen in het opslagaccount ophalen met [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Een verwijzing naar een specifieke tabel ophalen

Als u bewerkingen op een tabel wilt uitvoeren, hebt u een verwijzing naar de specifieke tabel nodig. Ontvang een referentie met [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Eigenschap ReferentieCloudTable van een specifieke tabel

> [!IMPORTANT]
> Het gebruik van CloudTable is verplicht bij het werken met **de AzTable** PowerShell-module. Bel de opdracht **Get-AzTableTable** om de verwijzing naar dit object te krijgen. Met deze opdracht wordt de tabel ook gemaakt als deze nog niet bestaat.

Als u bewerkingen op een tabel wilt uitvoeren met **AzTable,** hebt u een verwijzing naar de eigenschap CloudTable van een specifieke tabel nodig.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Een tabel verwijderen

Als u een tabel wilt verwijderen, gebruikt u [Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Deze cmdlet verwijdert de tabel, inclusief alle gegevens.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u aan het begin van deze how-to een nieuwe brongroep en opslagaccount hebt gemaakt, u alle elementen die u in deze oefening hebt gemaakt verwijderen door de brongroep te verwijderen. Met deze opdracht worden alle bronnen in de groep en de resourcegroep zelf verwijderd.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over algemene Azure Table-opslagbewerkingen met PowerShell, waaronder hoe u: 

> [!div class="checklist"]
> * Een tabel maken
> * Een tabel ophalen
> * Tabelentiteiten toevoegen
> * Een tabel opvragen
> * Tabelentiteiten verwijderen
> * Een tabel verwijderen

Zie de volgende artikelen voor meer informatie

* [PowerShell Storage-cmdlets](/powershell/module/az.storage#storage)

* [Werken met Azure-tabellen van PowerShell - AzureRmStorageTable/AzTable PS-module v2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.
