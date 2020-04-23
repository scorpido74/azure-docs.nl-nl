---
title: Azure PowerShell gebruiken met Azure Storage | Microsoft Docs
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

Azure PowerShell wordt gebruikt om Azure-resources te maken en te beheren via de Power shell-opdracht regel of in scripts. Voor Azure Storage worden deze cmdlets onderverdeeld in twee categorieën: het besturings vlak en het gegevens vlak. De Control vlak-cmdlets worden gebruikt voor het beheren van het opslag account, voor het maken van opslag accounts, het instellen van eigenschappen, het verwijderen van opslag accounts, het draaien van de toegangs sleutels, enzovoort. De data vlak-cmdlets worden gebruikt voor het beheren van de gegevens die zijn opgeslagen *in* het opslag account. Bijvoorbeeld: het uploaden van blobs, het maken van bestands shares en het toevoegen van berichten aan een wachtrij.

In dit artikel wordt beschreven hoe u algemene bewerkingen kunt gebruiken voor het beheren van opslag accounts met behulp van de management plan-cmdlets. Procedures voor:

> [!div class="checklist"]
> * Opslag accounts weer geven
> * Een verwijzing naar een bestaand opslag account ophalen
> * Create a storage account
> * Eigenschappen van opslag account instellen
> * De toegangs sleutels ophalen en opnieuw genereren
> * Toegang tot uw opslag account beveiligen
> * Opslaganalyse inschakelen

Dit artikel bevat koppelingen naar verschillende andere Power Shell-artikelen voor opslag, zoals het inschakelen en openen van de Opslaganalyse, het gebruiken van de data plan-cmdlets en het openen van de onafhankelijke Azure-Clouds, zoals China-Cloud, Duitse Cloud en Government Cloud.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Deze oefening vereist de Azure PowerShell-module AZ versie 0,7 of hoger. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

Voor deze oefening kunt u de opdrachten in een normaal Power shell-venster typen, maar u kunt ook de [Windows Power shell Integrated Scripting Environment (ISE)](/powershell/scripting/components/ise/exploring-the-windows-powershell-ise) gebruiken en de opdrachten in een editor typen en vervolgens een of meer opdrachten tegelijk testen door de voor beelden door te lopen. U kunt de rijen markeren die u wilt uitvoeren en op geselecteerde uitvoeren klikken om deze opdrachten uit te voeren.

Zie [Inleiding tot opslag](storage-introduction.md) en [over Azure Storage-accounts](storage-create-storage-account.md)voor meer informatie over opslag accounts.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>De opslag accounts in het abonnement weer geven

Voer de cmdlet [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) uit om de lijst met opslag accounts in het huidige abonnement op te halen.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Een verwijzing naar een opslag account ophalen

Vervolgens hebt u een verwijzing naar een opslag account nodig. U kunt een nieuw opslag account maken of een verwijzing naar een bestaand opslag account ophalen. In de volgende sectie worden beide methoden weer gegeven.

### <a name="use-an-existing-storage-account"></a>Een bestaand opslag account gebruiken

Als u een bestaand opslag account wilt ophalen, hebt u de naam van de resource groep en de naam van het opslag account nodig. Stel de variabelen voor deze twee velden in en gebruik vervolgens de cmdlet [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) .

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

U hebt nu $storageAccount, die verwijst naar een bestaand opslag account.

### <a name="create-a-storage-account"></a>Create a storage account

Het volgende script toont hoe u een opslag account voor algemeen gebruik maakt met behulp van [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Nadat u het account hebt gemaakt, haalt u de context op, die in volgende opdrachten kan worden gebruikt in plaats van dat u de verificatie voor elke aanroep opgeeft.

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

Het script maakt gebruik van de volgende Power shell-cmdlets:

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) : haalt een lijst met geldige locaties op. In het voor `eastus` beeld wordt de locatie gebruikt.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) --maakt een nieuwe resource groep. Een resource groep is een logische container waarin uw Azure-resources worden geïmplementeerd en beheerd. Ren heet `teststoragerg`.

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) : maakt het opslag account. In het voor `testpshstorage`beeld wordt gebruikt.

De SKU-naam geeft het type replicatie voor het opslag account aan, zoals LRS (lokaal redundante opslag). Zie [Azure storage-replicatie](storage-redundancy.md)voor meer informatie over replicatie.

> [!IMPORTANT]
> De naam van uw opslag account moet uniek zijn binnen Azure en moet kleine letters zijn. Zie [containers, blobs en meta gegevens benoemen en hiernaar verwijzen](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)voor naam conventies en beperkingen.
>

U hebt nu een nieuw opslag account en een verwijzing naar het.

## <a name="manage-the-storage-account"></a>Het opslag account beheren

Nu u een verwijzing hebt naar een nieuw opslag account of een bestaand opslag account, ziet u in de volgende sectie enkele opdrachten die u kunt gebruiken om uw opslag account te beheren.

### <a name="storage-account-properties"></a>Eigenschappen van opslag account

Als u de instellingen voor een opslag account wilt wijzigen, gebruikt u [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). U kunt de locatie van een opslag account of de resource groep waarin deze zich bevindt, echter wijzigen. Hieronder vindt u enkele van de eigenschappen die u kunt wijzigen met Power shell.

* Het **aangepaste domein** dat aan het opslag account is toegewezen.

* De **labels** die aan het opslag account zijn toegewezen. Tags worden vaak gebruikt om resources te categoriseren voor facturerings doeleinden.

* De **SKU** is de replicatie-instelling voor het opslag account, zoals LRS voor lokaal redundante opslag. U kunt bijvoorbeeld wijzigen van standaard\_LRS naar standaard\_-GRS of standaard\_RAGRS. Houd er rekening mee dat u\_de standaard ZRS\_, standaard GZRS\_, standaard RAGZRS of\_Premium LRS niet kunt wijzigen in andere sku's, of andere sku's kunt wijzigen.

* De **toegangs laag** voor Blob Storage-accounts. De waarde voor de toegangs laag is ingesteld op **dynamisch** of **koud**en Hiermee kunt u de kosten tot een minimum beperken door de toegangs laag te selecteren die wordt uitgelijnd met de manier waarop u het opslag account gebruikt. Zie [opslag lagen dynamisch, koud en archief](../blobs/storage-blob-storage-tiers.md)voor meer informatie.

* Sta alleen HTTPS-verkeer toe.

### <a name="manage-the-access-keys"></a>De toegangs sleutels beheren

Een Azure Storage-account wordt geleverd met twee account sleutels. Gebruik [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey)om de sleutels op te halen. In dit voor beeld wordt de eerste sleutel opgehaald. Als u het andere wilt ophalen, `Value[1]` gebruikt u `Value[0]`in plaats van.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Gebruik [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey)om de sleutel opnieuw te genereren.

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Als u de andere sleutel opnieuw wilt genereren `key2` , gebruikt u als sleutel naam `key1`in plaats van.

Genereer een van de sleutels opnieuw en haal deze opnieuw op om de nieuwe waarde weer te geven.

> [!NOTE]
> U moet een zorgvuldige planning uitvoeren voordat u de sleutel voor een productie-opslag account opnieuw genereert. Wanneer een of beide sleutels opnieuw worden gegenereerd, wordt de toegang voor elke toepassing ongeldig met behulp van de sleutel die opnieuw is gegenereerd. Zie [toegangs sleutels voor opslag accounts beheren](storage-account-keys-manage.md)voor meer informatie.


### <a name="delete-a-storage-account"></a>Een opslagaccount verwijderen

Gebruik [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount)om een opslag account te verwijderen.

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Wanneer u een opslag account verwijdert, worden ook alle activa verwijderd die in het account zijn opgeslagen. Als u een account per ongeluk verwijdert, roept u onmiddellijk ondersteuning aan en opent u een ticket om het opslag account te herstellen. Het herstellen van uw gegevens is niet gegarandeerd, maar het werkt soms wel. Maak geen nieuw opslag account met dezelfde naam als het oude, totdat het ondersteunings ticket is opgelost.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Uw opslag account beveiligen met VNets en firewalls

Standaard zijn alle opslag accounts toegankelijk voor elk netwerk dat toegang tot internet heeft. U kunt echter netwerk regels configureren om alleen toepassingen van specifieke virtuele netwerken toegang tot een opslag account te geven. Zie [Azure Storage firewalls en virtuele netwerken configureren](storage-network-security.md)voor meer informatie.

In dit artikel wordt beschreven hoe u deze instellingen beheert met behulp van de volgende Power shell-cmdlets:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Opslag analyse gebruiken

[Azure Opslaganalyse](storage-analytics.md) bestaat uit [Opslaganalyse metrische gegevens](/rest/api/storageservices/about-storage-analytics-metrics) en [Opslaganalyse logboek registratie](/rest/api/storageservices/about-storage-analytics-logging).

**Opslaganalyse metrische gegevens** worden gebruikt voor het verzamelen van metrische gegevens voor uw Azure-opslag accounts die u kunt gebruiken om de status van een opslag account te bewaken. Metrieken kunnen worden ingeschakeld voor blobs, bestanden, tabellen en wacht rijen.

**Opslaganalyse logboek registratie** wordt uitgevoerd aan de server zijde en kunt u details vastleggen voor zowel geslaagde als mislukte aanvragen voor uw opslag account. In deze logboeken kunt u de details van lees-, schrijf-en verwijder bewerkingen voor uw tabellen, wacht rijen en blobs en de redenen voor mislukte aanvragen bekijken. Logboek registratie is niet beschikbaar voor Azure Files.

U kunt de bewaking configureren met de [Azure Portal](https://portal.azure.com), Power shell of via een programma met behulp van de Storage-client bibliotheek.

> [!NOTE]
> U kunt minuut analyse inschakelen met behulp van Power shell. Deze mogelijkheid is niet beschikbaar in de portal.
>

* Voor informatie over het inschakelen en weer geven van metrische gegevens over opslag met behulp van Power shell, Zie [metrische opslag analyse-metrieken](storage-analytics-metrics.md).

* Voor informatie over het inschakelen en ophalen van gegevens van opslag vastleggen met behulp van Power shell raadpleegt u [logboek registratie van opslag inschakelen met Power shell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) en [de logboek gegevens van uw opslag logboeken zoeken](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Zie voor gedetailleerde informatie over het gebruik van metrische gegevens voor opslag en logboek registratie voor het oplossen van opslag problemen de [Microsoft Azure Storage bewaking, diagnose en probleem oplossing](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>De gegevens in het opslag account beheren

Nu u begrijpt hoe u uw opslag account beheert met Power shell, kunt u de volgende artikelen gebruiken om te leren hoe u toegang krijgt tot de gegevens objecten in het opslag account.

* [Blobs beheren met Power shell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Bestanden beheren met Power shell](../files/storage-how-to-use-files-powershell.md)
* [Wacht rijen beheren met Power shell](../queues/storage-powershell-how-to-use-queues.md)
* [Azure Table Storage-bewerkingen uitvoeren met Power shell](../../storage/tables/table-storage-how-to-use-powershell.md)

Azure Cosmos DB Table-API biedt Premium-functies voor tabel opslag, zoals kant en klare wereld wijde distributie, lees-en schrijf bewerkingen met lage latentie, automatische secundaire indexering en toegewezen door voer.

* Zie [Azure Cosmos DB Table-API](../../cosmos-db/table-introduction.md)voor meer informatie.

## <a name="independent-cloud-deployments-of-azure"></a>Onafhankelijke Cloud implementaties van Azure

De meeste mensen gebruiken de open bare Azure-Cloud voor hun wereld wijde Azure-implementatie. Er zijn ook enkele onafhankelijke implementaties van Microsoft Azure om redenen van soevereiniteit, enzovoort. Deze onafhankelijke implementaties worden ' omgevingen ' genoemd. Dit zijn de beschik bare omgevingen:

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure China 21Vianet-Cloud die wordt beheerd door 21Vianet in China](http://www.windowsazure.cn/)
* [Azure Duitse Cloud](../../germany/germany-welcome.md)

Zie [opslag beheren in de onafhankelijke Azure-Clouds met behulp van Power shell](storage-powershell-independent-clouds.md)voor meer informatie over het openen van deze Clouds en hun opslag met Power shell.

## <a name="clean-up-resources"></a>Resources opschonen

Als u voor deze oefening een nieuwe resource groep en een opslag account hebt gemaakt, kan yous alle activa verwijderen die u hebt gemaakt door de resource groep te verwijderen. Hiermee verwijdert u ook alle resources binnen de groep. In dit geval worden het opslag account dat is gemaakt en de resource groep zelf verwijderd.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u algemene bewerkingen kunt gebruiken voor het beheren van opslag accounts met behulp van de management plan-cmdlets. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Opslag accounts weer geven
> * Een verwijzing naar een bestaand opslag account ophalen
> * Create a storage account
> * Eigenschappen van opslag account instellen
> * De toegangs sleutels ophalen en opnieuw genereren
> * Toegang tot uw opslag account beveiligen
> * Opslaganalyse inschakelen

In dit artikel vindt u ook verwijzingen naar verschillende andere artikelen, zoals het beheren van de gegevens objecten, het inschakelen van de Opslaganalyse en het openen van de onafhankelijke Azure-Clouds, zoals China Cloud, Duitse Cloud en Government Cloud. Hier vindt u een aantal verwante artikelen en bronnen ter referentie:

* [Power shell-cmdlets voor Azure Storage Control vlak](/powershell/module/az.storage/)
* [Power shell-cmdlets voor Azure Storage Data-vlak](/powershell/module/azure.storage/)
* [Naslaginformatie over Windows PowerShell](/powershell/scripting/overview)
