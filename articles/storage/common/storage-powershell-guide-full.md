---
title: Azure PowerShell gebruiken met Azure Storage | Microsoft Documenten
description: Meer informatie over het gebruik van de Azure PowerShell-cmdlets voor Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d2404ee58f5f44fbe5625f267e6d1c504d0bd237
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465089"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Azure PowerShell gebruiken met Azure Storage

Azure PowerShell wordt gebruikt voor het maken en beheren van Azure-bronnen via de PowerShell-opdrachtregel of in scripts. Voor Azure Storage vallen deze cmdlets in twee categorieën: het besturingsvlak en het gegevensvlak. De cmdlets van het besturingsvlak worden gebruikt om het opslagaccount te beheren- om opslagaccounts te maken, eigenschappen in te stellen, opslagaccounts te verwijderen, de toegangssleutels te roteren, enzovoort. De cmdlets van het gegevensvlak worden gebruikt om de gegevens te beheren die zijn opgeslagen *in* het opslagaccount. Bijvoorbeeld blobs uploaden, bestandsshares maken en berichten toevoegen aan een wachtrij.

Dit artikel over de algemene bewerkingen met behulp van de cmdlets van het beheervlak om opslagaccounts te beheren. Procedures voor:

> [!div class="checklist"]
> * Opslagaccounts aanbieden
> * Een verwijzing naar een bestaand opslagaccount
> * Een opslagaccount maken
> * Opslagaccounteigenschappen instellen
> * De toegangssleutels ophalen en regenereren
> * Toegang tot uw opslagaccount beveiligen
> * Storage Analytics inschakelen

In dit artikel vindt u koppelingen naar verschillende andere PowerShell-artikelen voor opslag, zoals het inschakelen en openen van de Storage Analytics, het gebruik van de cmdlets van het gegevensvlak en hoe u toegang krijgt tot de onafhankelijke Azure-clouds zoals China Cloud, German Cloud en Government Wolk.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voor deze oefening is de Azure PowerShell-module Az-versie 0.7 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

Voor deze oefening u de opdrachten in een normaal PowerShell-venster typen of de [Geïntegreerde Scripting-omgeving (Windows PowerShell- scriptingomgeving)](/powershell/scripting/components/ise/exploring-the-windows-powershell-ise) gebruiken en de opdrachten in een editor typen en vervolgens een of meer opdrachten tegelijk testen terwijl u de voorbeelden bekijkt. U de rijen markeren die u wilt uitvoeren en op Geselecteerd uitvoeren klikken om deze opdrachten alleen uit te voeren.

Zie [Inleiding tot opslag-](storage-introduction.md) en [Azure-opslagaccounts](storage-create-storage-account.md)voor meer informatie over opslagaccounts .

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>De opslagaccounts in het abonnement weergeven

Voer de cmdlet [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) uit om de lijst met opslagaccounts in het huidige abonnement op te halen.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Een verwijzing naar een opslagaccount

Vervolgens heb je een verwijzing naar een opslagaccount nodig. U een nieuw opslagaccount maken of een verwijzing naar een bestaand opslagaccount krijgen. In de volgende sectie worden beide methoden weergegeven.

### <a name="use-an-existing-storage-account"></a>Een bestaand opslagaccount gebruiken

Als u een bestaand opslagaccount wilt ophalen, hebt u de naam van de brongroep en de naam van het opslagaccount nodig. Stel de variabelen voor deze twee velden in en gebruik vervolgens de cmdlet [Get-AzStorageAccount.](/powershell/module/az.storage/Get-azStorageAccount)

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Nu heb je $storageAccount, die verwijst naar een bestaande opslag account.

### <a name="create-a-storage-account"></a>Een opslagaccount maken

In het volgende script ziet u hoe u een opslagaccount voor algemene doeleinden maakt met [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Nadat u het account hebt gemaakt, haalt u de context op, die kan worden gebruikt in volgende opdrachten in plaats van de verificatie bij elke oproep op te geven.

```powershell
# Get list of locations and select one.
Get-AzLocation | select Location
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzResourceGroup -Name $resourceGroup -Location $location

# Set the name of the storage account and the SKU name.
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context.
$ctx = $storageAccount.Context
```

Het script gebruikt de volgende PowerShell-cmdlets:

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) - haalt een lijst op met de geldige locaties. Het voorbeeld `eastus` wordt gebruikt voor locatie.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) -- maakt een nieuwe resourcegroep. Een resourcegroep is een logische container waarin uw Azure-resources worden geïmplementeerd en beheerd. Die van `teststoragerg`ons heet.

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) - maakt het opslagaccount. In het `testpshstorage`voorbeeld wordt gebruik gebruikt .

De SKU-naam geeft het type replicatie voor het opslagaccount aan, zoals LRS (LocalRedundant Storage). Zie [Azure Storage Replication](storage-redundancy.md)voor meer informatie over replicatie.

> [!IMPORTANT]
> De naam van uw opslagaccount moet uniek zijn binnen Azure en moet kleine letters zijn. Zie [Containers, blobs en metagegevens benoemen en verwijzen](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)voor het benoemen van conventies en beperkingen.
>

Nu heb je een nieuw opslagaccount en een verwijzing ernaar.

## <a name="manage-the-storage-account"></a>Het opslagaccount beheren

Nu u een verwijzing hebt naar een nieuw opslagaccount of een bestaand opslagaccount, worden in de volgende sectie enkele opdrachten weergegeven die u gebruiken om uw opslagaccount te beheren.

### <a name="storage-account-properties"></a>Eigenschappen van opslagaccount

Als u de instellingen voor een opslagaccount wilt wijzigen, gebruikt u [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Hoewel u de locatie van een opslagaccount of de resourcegroep waarin het zich bevindt niet wijzigen, u veel van de andere eigenschappen wijzigen. In de volgende lijsten worden enkele eigenschappen weergegeven die u wijzigen met PowerShell.

* Het **aangepaste domein** dat is toegewezen aan het opslagaccount.

* De **tags** die zijn toegewezen aan het opslagaccount. Tags worden vaak gebruikt om resources te categoriseren voor factureringsdoeleinden.

* De **SKU** is de replicatieinstelling voor het opslagaccount, zoals LRS voor lokaal redundante opslag. U bijvoorbeeld overstappen\_van Standaard\_LRS naar\_Standard GRS of Standard RAGRS. Houd er rekening mee\_dat u\_standaard ZRS, Standard GZRS, Standard\_RAGZRS of Premium\_LRS niet wijzigen in andere SKU's of andere SKU's wijzigen.

* De **toegangslaag** voor Blob-opslagaccounts. De waarde voor de toegangslaag is ingesteld op **heet** of **koel**en stelt u in staat om uw kosten te minimaliseren door de toegangslaag te selecteren die overeenkomt met de manier waarop u het opslagaccount gebruikt. Zie [Opslaglagen Hot, Cool en Archive voor](../blobs/storage-blob-storage-tiers.md)meer informatie.

* Sta alleen HTTPS-verkeer toe.

### <a name="manage-the-access-keys"></a>De toegangssleutels beheren

Een Azure Storage-account wordt geleverd met twee accountsleutels. Als u de sleutels wilt ophalen, gebruikt u [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). In dit voorbeeld wordt de eerste sleutel opgehaald. Als u de andere `Value[1]` wilt `Value[0]`ophalen, gebruikt u in plaats van .

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Als u de sleutel wilt regenereren, gebruikt u [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey).

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Als u de andere `key2` sleutel wilt regenereren, gebruikt u de sleutelnaam in plaats van `key1`.

Regenereert een van uw sleutels en haal deze vervolgens opnieuw op om de nieuwe waarde te zien.

> [!NOTE]
> U moet een zorgvuldige planning uitvoeren voordat u de sleutel voor een productieopslagaccount regenereert. Als u een of beide sleutels regenereert, wordt de toegang voor elke toepassing ongeldig gemaakt met behulp van de sleutel die is geregenereerd. Zie [Toegangssleutels voor opslagaccount beheren](storage-account-keys-manage.md)voor meer informatie .


### <a name="delete-a-storage-account"></a>Een opslagaccount verwijderen

Als u een opslagaccount wilt verwijderen, gebruikt u [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Wanneer u een opslagaccount verwijdert, worden alle activa die in het account zijn opgeslagen, ook verwijderd. Als u een account per ongeluk verwijdert, belt u onmiddellijk de ondersteuning en opent u een ticket om het opslagaccount te herstellen. Herstel van uw gegevens is niet gegarandeerd, maar het werkt soms wel. Maak geen nieuw opslagaccount aan met dezelfde naam als het oude totdat het ondersteuningsticket is opgelost.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Uw opslagaccount beveiligen met VNets en firewalls

Standaard zijn alle opslagaccounts toegankelijk voor elk netwerk dat toegang heeft tot internet. U echter netwerkregels configureren zodat toepassingen van specifieke virtuele netwerken alleen toegang hebben tot een opslagaccount. Zie [Azure Storage Firewalls en Virtual Networks configureren](storage-network-security.md)voor meer informatie.

In het artikel ziet u hoe u deze instellingen beheert met de volgende PowerShell-cmdlets:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Regels voor het verwijderen van azstorageaccountnetwerk](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Opslaganalyses gebruiken

[Azure Storage Analytics](storage-analytics.md) bestaat uit [Storage Analytics Metrics](/rest/api/storageservices/about-storage-analytics-metrics) en Storage Analytics [Logging.](/rest/api/storageservices/about-storage-analytics-logging)

**Storage Analytics Metrics** wordt gebruikt om statistieken te verzamelen voor uw Azure-opslagaccounts die u gebruiken om de status van een opslagaccount te controleren. Statistieken kunnen worden ingeschakeld voor blobs, bestanden, tabellen en wachtrijen.

**Storage Analytics Logging** gebeurt aan de serverzijde en stelt u in staat om gegevens op te nemen voor zowel succesvolle als mislukte aanvragen voor uw opslagaccount. Met deze logboeken u details van lees-, schrijf- en verwijderbewerkingen zien aan uw tabellen, wachtrijen en blobs, evenals de redenen voor mislukte aanvragen. Logboekregistratie is niet beschikbaar voor Azure Files.

U bewaking configureren met behulp van de [Azure-portal,](https://portal.azure.com)PowerShell of programmatisch met behulp van de opslagclientbibliotheek.

> [!NOTE]
> U minutenanalyse inschakelen met PowerShell. Deze mogelijkheid is niet beschikbaar in de portal.
>

* Zie [Storage analytics metrics](storage-analytics-metrics.md)voor meer informatie over het in- en weergeven van storage metrics met PowerShell.

* Zie [Opslaglogboekregistratie inschakelen met PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) en [Uw logboekgegevens voor opslaglogboekregistratie zoeken.](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)

* Zie [Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)voor meer informatie over het gebruik van opslagstatistieken en opslaglogboekregistratie om opslagproblemen op te lossen.

## <a name="manage-the-data-in-the-storage-account"></a>De gegevens beheren in het opslagaccount

Nu u begrijpt hoe u uw opslagaccount beheren met PowerShell, u de volgende artikelen gebruiken om te leren hoe u toegang krijgt tot de gegevensobjecten in het opslagaccount.

* [Blobs beheren met PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Bestanden beheren met PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Wachtrijen beheren met PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Azure Table-opslagbewerkingen uitvoeren met PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

Azure Cosmos DB Table API biedt premium functies voor tabelopslag, zoals turnkey global distribution, low latency reads and writes, automatic secondary indexing, and dedicated throughput.

* Zie [Azure Cosmos DB Table API](../../cosmos-db/table-introduction.md)voor meer informatie.

## <a name="independent-cloud-deployments-of-azure"></a>Onafhankelijke cloudimplementaties van Azure

De meeste mensen gebruiken Azure Public Cloud voor hun wereldwijde Azure-implementatie. Er zijn ook enkele onafhankelijke implementaties van Microsoft Azure om redenen van soevereiniteit en ga zo maar door. Deze onafhankelijke implementaties worden 'omgevingen' genoemd. Dit zijn de beschikbare omgevingen:

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure China 21Vianet Cloud beheerd door 21Vianet in China](http://www.windowsazure.cn/)
* [Azure Duitse cloud](../../germany/germany-welcome.md)

Zie [Opslag beheren in de Azure-onafhankelijke clouds met PowerShell](storage-powershell-independent-clouds.md)voor informatie over hoe u toegang krijgen tot deze clouds en hun opslag met PowerShell.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een nieuwe resourcegroep en een opslagaccount voor deze oefening hebt gemaakt, u alle elementen verwijderen die u hebt gemaakt door de brongroep te verwijderen. Hiermee verwijdert u ook alle resources binnen de groep. In dit geval worden het opslagaccount verwijderd dat is gemaakt en de brongroep zelf.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Volgende stappen

Dit artikel over de algemene bewerkingen met behulp van de cmdlets van het beheervlak om opslagaccounts te beheren. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Opslagaccounts aanbieden
> * Een verwijzing naar een bestaand opslagaccount
> * Een opslagaccount maken
> * Opslagaccounteigenschappen instellen
> * De toegangssleutels ophalen en regenereren
> * Toegang tot uw opslagaccount beveiligen
> * Storage Analytics inschakelen

In dit artikel worden ook verwijzingen naar verschillende andere artikelen opgenomen, zoals het beheren van de gegevensobjecten, het inschakelen van storageanalytics en hoe u toegang krijgt tot de onafhankelijke Azure-clouds zoals China Cloud, German Cloud en Government Cloud. Hier zijn enkele meer gerelateerde artikelen en bronnen voor referentie:

* [PowerShell-cmdlets van Azure Storage Control-beheer](/powershell/module/az.storage/)
* [PowerShell-cmdlets van Azure Storage-gegevensvlak](/powershell/module/azure.storage/)
* [Naslaginformatie over Windows PowerShell](/powershell/scripting/overview)
