---
title: Een Azure-bestandsshare maken
titleSuffix: Azure Files
description: Een Azure-bestandsshare maken met de Azure-portal, PowerShell of Azure CLI.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596878"
---
# <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken
Als u een Azure-bestandsshare wilt maken, moet u drie vragen beantwoorden over hoe u het gaat gebruiken:

- **Wat zijn de prestatievereisten voor uw Azure-bestandsshare?**  
    Azure Files biedt standaard bestandsshares, die worden gehost op hardware op basis van harde schijven (HDD) en premium bestandsshares, die worden gehost op hardware op basis van solid-state schijven (SSD-gebaseerde hardware).

- **Welke grootte bestandsshare heb je nodig?**  
    Standaard bestandsshares kunnen maximaal 100 TiB omvatten, maar deze functie is niet standaard ingeschakeld; Als u een bestandsshare nodig hebt dat groter is dan 5 TiB, moet u de functie voor het delen van bestanden voor uw opslagaccount inschakelen. Premium bestandsaandelen kunnen tot 100 TiB omvatten zonder speciale instelling, maar premium bestandsaandelen zijn ingericht, in plaats van te betalen terwijl u gaat als standaard bestandsaandelen. Dit betekent dat het inrichten van een bestandsaandeel veel groter is dan wat u nodig hebt, waardoor de totale opslagkosten toenemen.

- **Wat zijn uw redundantievereisten voor uw Azure-bestandsshare?**  
    Standaard bestandsshares bieden lokaal redundante (LRS), zone redundante (ZRS), geo-redundante (GRS) of geo-zone-redundante (GZRS) opslag, maar de grote file share-functie wordt alleen ondersteund op lokaal redundante en zone redundante bestandsshares. Premium bestandsaandelen ondersteunen geen enkele vorm van georedundantie.

    Premium bestandsshares zijn beschikbaar met lokaal redundantie in de meeste regio's die opslagaccounts aanbieden en met zoneredundantie in een kleinere subset van regio's. Zie de producten die beschikbaar zijn [op regiopagina](https://azure.microsoft.com/global-infrastructure/services/?products=storage) voor Azure om erachter te komen of er momenteel premium bestandsshares beschikbaar zijn in uw regio. Zie [Redundantie azure storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor informatie over regio's die ZRS ondersteunen.

Zie [Plannen voor een Azure-bestandsimplementatie voor](storage-files-planning.md)meer informatie over deze drie opties.

## <a name="prerequisites"></a>Vereisten
- In dit artikel wordt ervan uitgegaan dat u al een Azure-abonnement hebt gemaakt. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Als u Azure PowerShell wilt gebruiken, [installeert u de nieuwste versie.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Als u de Azure CLI wilt gebruiken, [installeert u de nieuwste versie.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-storage-account"></a>Een opslagaccount maken
Azure-bestandsshares worden geïmplementeerd in *opslagaccounts,* objecten op het hoogste niveau die een gedeelde opslaggroep vertegenwoordigen. Deze opslaggroep kan worden gebruikt om meerdere bestandsshares te implementeren. 

Azure ondersteunt meerdere typen opslagaccounts voor verschillende opslagscenario's die klanten kunnen hebben, maar er zijn twee belangrijke typen opslagaccounts voor Azure-bestanden. Welk opslagaccounttype u moet maken, is afhankelijk van of u een standaardbestandsshare of een premium bestandsshare wilt maken: 

- **GPv2-opslagaccounts (Version 2) voor**algemeen gebruik: met GPv2-opslagaccounts u Azure-bestandsshares implementeren op standaard/harde schijfgebaseerde (HDD-gebaseerde) hardware. Naast het opslaan van Azure-bestandsshares kunnen GPv2-opslagaccounts andere opslagbronnen opslaan, zoals blobcontainers, wachtrijen of tabellen. 

- **FileStorage-opslagaccounts:** met opslagaccounts voor Bestandsopslag u Azure-bestandsshares implementeren op hardware op basis van schijfgebaseerde schijfgebaseerde schijfgebaseerde schijven op basis van premium/solid-state. FileStorage-accounts kunnen alleen worden gebruikt om Azure-bestandsshares op te slaan. er kunnen geen andere opslagbronnen (blobcontainers, wachtrijen, tabellen, enz.) worden geïmplementeerd in een FileStorage-account.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een opslagaccount wilt maken via de Azure-portal, selecteert u **+ Een resource maken** vanuit het dashboard. Zoek in het resulterende Azure Marketplace-zoekvenster naar **een opslagaccount** en selecteer het resulterende zoekresultaat. Dit zal leiden tot een overzichtspagina voor opslagaccounts; selecteer **Maken** om verder te gaan met de wizard Opslagaccount maken.

![Een schermafbeelding van de optie Snel maken van het opslagaccount in een browser](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>De sectie Basisbeginselen
De eerste sectie die moet worden voltooid om een opslagaccount te maken, heeft het label **Basics.** Dit bevat alle vereiste velden om een opslagaccount te maken. Als u een GPv2-opslagaccount wilt maken, moet u ervoor zorgen dat de keuzerondje **Prestaties** is ingesteld op *Standaard* en de vervolgkeuzelijst **Accountsoort** is geselecteerd op *StorageV2 (v2 voor algemeen gebruik).*

![Een schermafbeelding van de knop Prestatiekeuzerondje met standaard geselecteerd en Accountsoort met StorageV2 geselecteerd](media/storage-how-to-create-file-share/create-storage-account-1.png)

Als u een FileStorage-opslagaccount wilt maken, moet u ervoor zorgen dat de keuzerondje **Prestaties** is ingesteld op *Premium* en dat de vervolgkeuzelijst **Accountsoort** is geselecteerd in *FileStorage.*

![Een schermafbeelding van de knop Prestatiekeuzerondje met Premium geselecteerd en Accountsoort met FileStorage geselecteerd](media/storage-how-to-create-file-share/create-storage-account-2.png)

De andere basisvelden zijn onafhankelijk van de keuze van het opslagaccount:
- **Abonnement**: Het abonnement voor het opslagaccount dat moet worden geïmplementeerd. 
- **Resourcegroep:** de resourcegroep voor het opslagaccount dat moet worden geïmplementeerd. U een nieuwe resourcegroep maken of een bestaande resourcegroep gebruiken. Een resourcegroep is een logische container voor het groeperen van uw Azure-services. Wanneer u een opslagaccount maakt, kunt u een nieuwe resourcegroep maken of een bestaande resourcegroep gebruiken.
- **Naam van het opslagaccount:** de naam van de bron van het opslagaccount die moet worden gemaakt. Deze naam moet wereldwijd uniek zijn, maar anders kan elke naam die u wenst. De naam van het opslagaccount wordt gebruikt als servernaam wanneer u een Azure-bestandsshare monteert via SMB.
- **Locatie**: De regio waar het opslagaccount moet worden geïmplementeerd. Dit kan de regio zijn die is gekoppeld aan de resourcegroep of een andere beschikbare regio.
- **Replicatie:** Hoewel dit is gelabeld replicatie, dit veld betekent eigenlijk **redundantie**; dit is het gewenste redundantieniveau: lokaal redundantie (LRS), zone redundantie (ZRS), geo-redundantie (GRS) en geo-zone-redundantie. Deze vervolgkeuzelijst bevat ook read-access georedundantie (RA-GRS) en read-access geo-zone redundantie (RA-GZRS), die niet van toepassing zijn op Azure-bestandsshares; elk bestandsaandeel dat is gemaakt in een opslagaccount met deze geselecteerde, is eigenlijk respectievelijk geo-redundant of geo-zone-redundant. Afhankelijk van uw regio of geselecteerde opslagaccounttype zijn sommige redundantieopties mogelijk niet toegestaan.
- **Toegangslaag:** dit veld is niet van toepassing op Azure-bestanden, zodat u een van de radioknoppen kiezen.

#### <a name="the-networking-blade"></a>Het netwerkblad
Met de netwerksectie u netwerkopties configureren. Deze instellingen zijn optioneel voor het aanmaken van het opslagaccount en kunnen desgewenst later worden geconfigureerd. Zie [Azure Files-netwerkoverwegingen](storage-files-networking-overview.md)voor meer informatie over deze opties.

#### <a name="the-advanced-blade"></a>Het geavanceerde mes
De geavanceerde sectie bevat een aantal belangrijke instellingen voor Azure-bestandsshares:

- **Beveiligde overdracht vereist**: Dit veld geeft aan of het opslagaccount versleuteling vereist tijdens het transport voor communicatie naar het opslagaccount. We raden u aan dit ingeschakeld te laten, maar als u ondersteuning voor SMB 2.1 nodig hebt, moet u dit uitschakelen. We raden u aan versleuteling uit te schakelen waarmee u de toegang tot uw opslagaccount beperkt tot een virtueel netwerk met serviceeindpunten en/of privéeindpunten.
- **Grote bestandsaandelen**: Met dit veld kan de opslagaccount voor bestandsshares tot 100 TiB worden verdeeld. Als u deze functie inschakelt, beperkt u uw opslagaccount tot alleen lokaal redundante en zoneredundante opslagopties. Zodra een GPv2-opslagaccount is ingeschakeld voor grote bestandsshares, u de grote bestandsshare-mogelijkheid niet uitschakelen. FileStorage-opslagaccounts (opslagaccounts voor premium bestandsshares) hebben deze optie niet, omdat alle premium bestandsshares kunnen worden opgeschaald tot 100 TiB. 

![Een schermafbeelding van de belangrijke geavanceerde instellingen die van toepassing zijn op Azure Files](media/storage-how-to-create-file-share/create-storage-account-3.png)

De andere instellingen die beschikbaar zijn op het tabblad geavanceerd (blob soft-delete, hiërarchische naamruimte voor Azure Data Lake-opslag gen 2 en NFSv3 voor blobopslag) zijn niet van toepassing op Azure-bestanden.

#### <a name="tags"></a>Tags
Tags zijn naam-/waardeparen waarmee u resources categoriseren en geconsolideerde facturering weergeven door dezelfde tag toe te passen op meerdere bronnen en resourcegroepen. Deze zijn optioneel en kunnen worden toegepast na het maken van een opslagaccount.

#### <a name="review--create"></a>Controleren + maken
De laatste stap om het opslagaccount te maken, is door de knop **Maken** te selecteren op het tabblad **Controleren + maken.** Deze knop is niet beschikbaar als alle vereiste velden voor een opslagaccount niet zijn gevuld.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Om een opslagaccount aan te maken `New-AzStorageAccount` met PowerShell, gebruiken we de cmdlet. Deze cmdlet heeft vele opties; alleen de vereiste opties worden weergegeven. Zie de [ `New-AzStorageAccount` cmdlet-documentatie](/powershell/module/az.storage/new-azstorageaccount)voor meer informatie over geavanceerde opties.

Om het aanmaken van het opslagaccount en het daaropvolgende bestandsaandeel te vereenvoudigen, slaan we verschillende parameters op in variabelen. U de variabele inhoud vervangen door de waarden die u wenst, maar houd er rekening mee dat de naam van het opslagaccount wereldwijd uniek moet zijn.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Als u een opslagaccount wilt maken dat standaard Azure-bestandsshares kan opslaan, gebruiken we de volgende opdracht. De `-SkuName` parameter heeft betrekking op het gewenste type redundantie; Als u een geo-redundant of geo-zone-redundante opslagaccount `-EnableLargeFileShare` wenst, moet u ook de parameter verwijderen.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Als u een opslagaccount wilt maken dat premium Azure-bestandsshares kan opslaan, gebruiken we de volgende opdracht. Houd er `-SkuName` rekening mee dat `Premium` de parameter is gewijzigd in`LRS`beide en het gewenste redundantieniveau van lokaal redundante ( ). De `-Kind` parameter `FileStorage` is `StorageV2` in plaats van omdat premium bestandsshares moeten worden gemaakt in een FileStorage-opslagaccount in plaats van een GPv2-opslagaccount.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u een opslagaccount wilt maken met de Azure CLI, gebruiken we de opdracht az-opslagaccount maken. Deze opdracht heeft vele opties; alleen de vereiste opties worden weergegeven. Zie de [ `az storage account create` opdrachtdocumentatie](/cli/azure/storage/account)voor meer informatie over de geavanceerde opties.

Om het aanmaken van het opslagaccount en het daaropvolgende bestandsaandeel te vereenvoudigen, slaan we verschillende parameters op in variabelen. U de variabele inhoud vervangen door de waarden die u wenst, maar houd er rekening mee dat de naam van het opslagaccount wereldwijd uniek moet zijn.

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Als u een opslagaccount wilt maken dat standaard Azure-bestandsshares kan opslaan, gebruiken we de volgende opdracht. De `--sku` parameter heeft betrekking op het gewenste type redundantie; Als u een geo-redundant of geo-zone-redundante opslagaccount `--enable-large-file-share` wenst, moet u ook de parameter verwijderen.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Als u een opslagaccount wilt maken dat premium Azure-bestandsshares kan opslaan, gebruiken we de volgende opdracht. Houd er `--sku` rekening mee dat `Premium` de parameter is gewijzigd in`LRS`beide en het gewenste redundantieniveau van lokaal redundante ( ). De `--kind` parameter `FileStorage` is `StorageV2` in plaats van omdat premium bestandsshares moeten worden gemaakt in een FileStorage-opslagaccount in plaats van een GPv2-opslagaccount.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Bestandsshare maken
Zodra u uw opslagaccount hebt gemaakt, blijft het alleen maar om uw bestandsshare te maken. Dit proces is meestal hetzelfde, ongeacht of u een premium bestandsshare of een standaard bestandsshare gebruikt. Het belangrijkste verschil is het **quotum** en wat het vertegenwoordigt.

Voor standaardbestandsshares is dit een bovengrens van het Azure-bestandsaandeel, waarboven eindgebruikers niet kunnen gaan. Het primaire doel voor quota voor een standaardbestandsaandeel is budgetteering: "Ik wil niet dat dit bestandsaandeel verder groeit dan dit punt". Als een quotum niet is opgegeven, kan standaard bestandsshare maximaal 100 TiB (of 5 TiB omvatten als de eigenschap grote bestandsshares niet is ingesteld voor een opslagaccount).

Voor premium bestandsaandelen wordt het quotum overbelast tot een **ingerichte grootte**. De ingerichte grootte is het bedrag waarvoor u in rekening wordt gebracht, ongeacht het werkelijke gebruik. Wanneer u een premium bestandsshare indient, wilt u twee factoren in overweging nemen: 1) de toekomstige groei van het aandeel vanuit het perspectief van ruimtegebruik en 2) de IOPS die nodig is voor uw werkbelasting. Elke ingerichte GiB geeft u recht op extra gereserveerde en burst IOPS. Zie [het inrichten van premium bestandsshares](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)voor meer informatie over het plannen van een premium bestandsaandeel .

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u zojuist uw opslagaccount hebt gemaakt, u ernaar navigeren vanaf het implementatiescherm door **Ga naar resource te**selecteren. Als u het opslagaccount eerder hebt gemaakt, u ernaar navigeren via de brongroep die het account bevat. Eenmaal in het opslagaccount selecteert u de tegel met het label **Bestandsshares** (u ook naar **Bestandsshares** navigeren via de inhoudsopgave voor het opslagaccount).

![Een schermafbeelding van de tegel Bestandsshares](media/storage-how-to-create-file-share/create-file-share-1.png)

In de lijst met bestandsshare's ziet u alle bestandsshares die u eerder in dit opslagaccount hebt gemaakt. een lege tabel als er nog geen bestandsshares zijn gemaakt. Selecteer **+ Bestandsshare** om een nieuwe bestandsshare te maken.

Het nieuwe bestandsshareblad moet op het scherm verschijnen. Vul de velden in het nieuwe bestandsshareblad in om een bestandsshare te maken:

- **Naam**: de naam van de bestandsshare die moet worden gemaakt.
- **Quotum**: Het quotum van het bestandsaandeel voor standaardbestandsaandelen; de ingerichte omvang van het bestandsaandeel voor premium bestandsaandelen.

Selecteer **Maken** om het nieuwe aandeel te maken. Houd er rekening mee dat als uw opslagaccount zich in een virtueel netwerk bevindt, u geen Azure-bestandsshare maken, tenzij uw client zich ook in het virtuele netwerk bevindt. U ook werken rond deze point-in-time `New-AzRmStorageShare` beperking met behulp van de Azure PowerShell cmdlet.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
U de Azure-bestandsshare maken met de [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet. De volgende PowerShell-opdrachten gaan ervan `$resourceGroupName` uit `$storageAccountName` dat u de variabelen hebt ingesteld en zoals hierboven gedefinieerd in het maken van een opslagaccount met Azure PowerShell. 

> [!Important]  
> Voor premium bestandsaandelen `-QuotaGiB` verwijst de parameter naar de ingerichte grootte van het bestandsaandeel. De ingerichte grootte van het bestandsaandeel is het bedrag waarvoor u in rekening wordt gebracht, ongeacht het gebruik. Standaard bestandsaandelen worden gefactureerd op basis van gebruik in plaats van ingerichte grootte.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> De naam van de bestandsshare mag alleen uit kleine letters bestaan. Zie [Aandelen, mappen, bestanden en metagegevens benoemen en verwijzen](https://msdn.microsoft.com/library/azure/dn167011.aspx)voor meer informatie over het benoemen van bestandsshares en bestanden.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Voordat we een Azure-bestandsshare kunnen maken met de Azure CLI, moet u een opslagaccountsleutel krijgen om de bewerking voor het maken van bestandsdelen te autoriseren. Dit kan met [`az storage account keys list`](/cli/azure/storage/account/keys) de opdracht:

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

Zodra u de opslagaccountsleutel hebt, u [`az storage share create`](/cli/azure/storage/share) de Azure-bestandsshare maken met de opdracht. 

> [!Important]  
> Voor premium bestandsaandelen `--quota` verwijst de parameter naar de ingerichte grootte van het bestandsaandeel. De ingerichte grootte van het bestandsaandeel is het bedrag waarvoor u in rekening wordt gebracht, ongeacht het gebruik. Standaard bestandsaandelen worden gefactureerd op basis van gebruik in plaats van ingerichte grootte.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

Deze opdracht mislukt als het opslagaccount zich in een virtueel netwerk bevindt en de computer waarvan u deze opdracht inroept, geen deel uitmaakt van het virtuele netwerk. U deze beperking van point-in-time omzeilen door de Azure PowerShell-cmdlet `New-AzRmStorageShare` zoals hierboven beschreven te gebruiken, of door de Azure CLI uit te voeren vanaf een computer die deel uitmaakt van het virtuele netwerk, ook via een VPN-verbinding.

---

> [!Note]  
> De naam van de bestandsshare mag alleen uit kleine letters bestaan. Zie [Aandelen, mappen, bestanden en metagegevens benoemen en verwijzen](https://msdn.microsoft.com/library/azure/dn167011.aspx)voor meer informatie over het benoemen van bestandsshares en bestanden.

## <a name="next-steps"></a>Volgende stappen
- [Plan voor een implementatie van Azure Files](storage-files-planning.md) of Plan voor een implementatie van Azure File [Sync](storage-sync-files-planning.md). 
- [Netwerkoverzicht](storage-files-networking-overview.md).
- Maak verbinding en monteer een bestandsshare op [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)en [Linux.](storage-how-to-use-files-linux.md)