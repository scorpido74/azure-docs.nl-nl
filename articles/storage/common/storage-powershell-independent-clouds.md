---
title: PowerShell gebruiken om gegevens te beheren in azure-onafhankelijke clouds
titleSuffix: Azure Storage
description: Opslag beheren in de China Cloud, Government Cloud en Duitse cloud met Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5fa515515c06466e121a5c0ee925fd4d14245363
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895242"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Opslag beheren in de Azure-onafhankelijke clouds met PowerShell

De meeste mensen gebruiken Azure Public Cloud voor hun wereldwijde Azure-implementatie. Er zijn ook enkele onafhankelijke implementaties van Microsoft Azure om redenen van soevereiniteit en ga zo maar door. Deze onafhankelijke implementaties worden 'omgevingen' genoemd. In de volgende lijst worden de onafhankelijke clouds beschreven die momenteel beschikbaar zijn.

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure China 21Vianet Cloud beheerd door 21Vianet in China](http://www.windowsazure.cn/)
* [Azure Duitse cloud](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Een onafhankelijke cloud gebruiken

Als u Azure Storage wilt gebruiken in een van de onafhankelijke clouds, maakt u verbinding met die cloud in plaats van Azure Public. Ga als een van de onafhankelijke clouds in plaats van Azure Public:

* U geeft de *omgeving* op waarmee u verbinding moet maken.
* U bepaalt en gebruikt de beschikbare regio's.
* U gebruikt het juiste eindpuntachtervoegsel, dat verschilt van Azure Public.

De voorbeelden vereisen Azure PowerShell module Az versie 0.7 of hoger. Voer in een PowerShell-venster uit `Get-Module -ListAvailable Az` om de versie te vinden. Zie [Azure PowerShell-module installeren](/powershell/azure/install-Az-ps)als er niets wordt vermeld of als u moet upgraden.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Voer de cmdlet [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) uit om de beschikbare Azure-omgevingen te bekijken:

```powershell
Get-AzEnvironment
```

Meld u aan bij uw account dat toegang heeft tot de cloud waarmee u verbinding wilt maken en de omgeving wilt instellen. In dit voorbeeld ziet u hoe u zich aanmeldt bij een account dat gebruikmaakt van de Azure Government Cloud.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Als u toegang wilt krijgen tot de China Cloud, gebruikt u de omgeving **AzureChinaCloud**. Als u toegang wilt krijgen tot de Duitse cloud, gebruikt u **AzureGermanCloud**.

Als u op dit moment de lijst met locaties nodig hebt om een opslagaccount of een andere bron te maken, u de beschikbare locaties voor de geselecteerde cloud opvragen met [Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

```powershell
Get-AzLocation | select Location, DisplayName
```

In de volgende tabel ziet u de locaties die zijn geretourneerd voor de Duitse cloud.

|Locatie | Weergavenaam |
|----|----|
| `germanycentral` | Duitsland - centraal|
| `germanynortheast` | Duitsland - noordoost |


## <a name="endpoint-suffix"></a>Eindpuntachtervoegsel

Het eindpuntachtervoegsel voor elk van deze omgevingen verschilt van het Azure Public-eindpunt. Het blob-eindpuntachtervoegsel voor Azure Public is bijvoorbeeld **blob.core.windows.net**. Voor de overheidscloud is het blob-eindpuntachtervoegsel **blob.core.usgovcloudapi.net**.

### <a name="get-endpoint-using-get-azenvironment"></a>Eindpunt krijgen met Get-AzEnvironment

Haal het eindpuntachtervoegsel op met [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). Het eindpunt is de eigenschap *StorageEndpointSuffix* van de omgeving.

In de volgende codefragmenten ziet u hoe u het achtervoegsel van het eindpunt ophaalt. Al deze commando's terug iets als "core.cloudapp.net" of "core.cloudapi.de", enz. Voeg het achtervoegsel toe aan de opslagservice om toegang te krijgen tot die service. 'queue.core.cloudapi.de' heeft bijvoorbeeld toegang tot de wachtrijservice in German Cloud.

In dit codefragment worden alle omgevingen en het eindpuntachtervoegsel voor elk achtervoegsel opgehaald.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Met deze opdracht worden de volgende resultaten geretourneerd.

| Name| OpslagEndpoint-achtervoegsel|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Als u alle eigenschappen voor de opgegeven omgeving wilt ophalen, belt u **Get-AzEnvironment** en geeft u de naam van de cloud op. Dit codefragment retourneert een lijst met eigenschappen; zoek naar **StorageEndpointSuffix** in de lijst. Het volgende voorbeeld is voor de Duitse Wolk.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

De resultaten zijn vergelijkbaar met de volgende waarden:

|Naam van eigenschap|Waarde|
|----|----|
| Name | `AzureGermanCloud` |
| InschakelenAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalerijURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl PublishSettingsFileUrl PublishSettingsFileUrl PublishSettings| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDns-achtervoegsel | `.database.cloudapi.de` |
| **OpslagEndpoint-achtervoegsel** | `core.cloudapi.de` |
| ... | ... |
Als u alleen de eigenschap van het opslageindpuntachtervoeg sel wilt ophalen, haalt u de specifieke cloud op en vraagt u om alleen die ene eigenschap.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Met deze opdracht worden de volgende gegevens geretourneerd:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Eindpunt ophalen uit een opslagaccount

U ook de eigenschappen van een opslagaccount onderzoeken om de eindpunten op te halen:

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Voor een opslagaccount in de overheidscloud retourneert deze opdracht de volgende uitvoer:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Na het instellen van de omgeving

Vanaf nu u dezelfde PowerShell gebruiken die wordt gebruikt om uw opslagaccounts te beheren en toegang te krijgen tot het gegevensvlak zoals beschreven in het artikel [Azure PowerShell gebruiken met Azure Storage.](storage-powershell-guide-full.md)

## <a name="clean-up-resources"></a>Resources opschonen

Als u een nieuwe resourcegroep en een opslagaccount voor deze oefening hebt gemaakt, u beide elementen verwijderen door de brongroep te verwijderen. Als u de resourcegroep verwijdert, worden alle resources verwijderd die deze groep bevat.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

* [Gebruikersaanmeldingen behouden tussen PowerShell-sessies](/powershell/azure/context-persistence)
* [Azure-opslag van de overheid](../../azure-government/documentation-government-services-storage.md)
* [Microsoft Azure-handleiding voor overheidsontwikkelaars](../../azure-government/documentation-government-developer-guide.md)
* [Opmerkingen voor ontwikkelaars voor Azure China 21Vianet-toepassingen](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Azure Duitsland-documentatie](../../germany/germany-welcome.md)
