---
title: Een statische website hosten in Azure Storage
description: Meer informatie over het rechtstreeks weergeven van statische inhoud (HTML,CSS, JavaScript en afbeeldingsbestanden) vanuit een container in een Azure Storage GPv2-account.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: 056e23f0f0cf1a3a1c70042cef3c92dd41f14f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247007"
---
# <a name="host-a-static-website-in-azure-storage"></a>Een statische website hosten in Azure Storage

U statische inhoud (HTML,CSS, JavaScript en afbeeldingsbestanden) rechtstreeks vanuit een container weergeven in een GPv2-account voor Azure Storage. Zie [Statische websitehosting in Azure Storage](storage-blob-static-website.md)voor meer informatie.

In dit artikel ziet u hoe u statische websitehosting inschakelen met behulp van de Azure-portal, de Azure CLI of PowerShell.

## <a name="enable-static-website-hosting"></a>Statische websitehosting inschakelen

Statische websitehosting is een functie die u moet inschakelen op het opslagaccount.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) om aan de slag te gaan.

2. Zoek uw opslagaccount en geef het accountoverzicht weer.

3. Selecteer **Statische website** om de configuratiepagina voor statische websites weer te geven.

4. Selecteer **Ingeschakeld** om het hosten van statische websites in te schakelen voor het opslagaccount.

5. Geef in het veld **Documentnaam indexindex** een standaardindexpagina op (bijvoorbeeld *index.html*). 

   De standaardindexpagina wordt weergegeven wanneer een gebruiker naar de hoofdmap van uw statische website navigeert.  

6. Geef in het veld **Pad fouteen** standaardfoutpagina op (bijvoorbeeld: *404.html*). 

   De standaardfoutpagina wordt weergegeven wanneer een gebruiker probeert te navigeren naar een pagina die niet in uw statische website bestaat.

7. Klik op **Opslaan**. Het eindpunt voor uw statische website wordt nu weergegeven in de Azure-portal. 

    ![Het hosten van een statische website inschakelen voor een opslagaccount](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

<a id="cli" />

U statische websitehosting inschakelen met behulp van de [Azure Command-Line Interface (CLI).](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

1. Open eerst de [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)of open u de Azure CLI lokaal, open een opdrachtconsoletoepassing zoals Windows PowerShell. [installed](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op een abonnement op het opslagaccount dat uw statische website host.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Vervang `<subscription-id>` de tijdelijke aanduidingswaarde door de id van uw abonnement.

3. Statische websitehosting inschakelen.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

   * Vervang `<error-document-name>` de tijdelijke aanduiding door de naam van het foutdocument dat aan gebruikers wordt weergegeven wanneer een browser een pagina op uw site opvraagt die niet bestaat.

   * Vervang `<index-document-name>` de tijdelijke aanduiding door de naam van het indexdocument. Dit document is meestal "index.html".

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell" />

U statische websitehosting inschakelen met behulp van de Azure PowerShell-module.

1. Open een Windows PowerShell-opdrachtvenster.

2. Controleer of u Azure PowerShell-module Az-versie 0.7 of hoger hebt.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

3. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

   ```powershell
   Connect-AzAccount
   ```

4. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op een abonnement op het opslagaccount dat uw statische website host.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Vervang `<subscription-id>` de tijdelijke aanduidingswaarde door de id van uw abonnement.

5. Download de context van het opslagaccount waarin het opslagaccount wordt gedefinieerd dat u wilt gebruiken.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Vervang `<resource-group-name>` de tijdelijke aanduidingswaarde door de naam van uw resourcegroep.

   * Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

6. Statische websitehosting inschakelen.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Vervang `<error-document-name>` de tijdelijke aanduiding door de naam van het foutdocument dat aan gebruikers wordt weergegeven wanneer een browser een pagina op uw site opvraagt die niet bestaat.

   * Vervang `<index-document-name>` de tijdelijke aanduiding door de naam van het indexdocument. Dit document is meestal "index.html".

---

## <a name="upload-files"></a>Bestanden uploaden 

### <a name="portal"></a>[Portal](#tab/azure-portal)

In deze instructies ziet u hoe u bestanden uploadt met behulp van de versie van Storage Explorer die wordt weergegeven in de Azure-portal. U echter ook de versie van [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken die buiten de Azure-portal wordt uitgevoerd. U [AzCopy,](../common/storage-use-azcopy-v10.md)PowerShell, CLI of een aangepaste toepassing gebruiken die bestanden kan uploaden naar de **$web** container van uw account. Zie [Zelfstudie: Host een statische website op Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)voor een stapsgewijze zelfstudie die bestanden uploadt met behulp van Visual Studio-code.

1. Selecteer **Storage Explorer (voorbeeld)**.

2. Vouw het **blob-containerknooppunt uit** en selecteer vervolgens de **$web** container.

3. Kies de knop **Uploaden** om bestanden te uploaden.

   ![Bestanden uploaden](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Als u van plan bent dat de browser de inhoud van het bestand `text/html`weergeeft, controleert u of het inhoudstype van dat bestand is ingesteld op . 

   ![Inhoudstypen controleren](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Storage Explorer stelt deze `text/html` eigenschap automatisch in op `.html`algemeen erkende extensies, zoals . Echter, in sommige gevallen, moet je dit zelf in te stellen. Als u deze eigenschap niet `text/html`instelt op , vraagt de browser gebruikers om het bestand te downloaden in plaats van de inhoud weer te geven. Als u deze eigenschap wilt instellen, klikt u met de rechtermuisknop op het bestand en klikt u vervolgens op **Eigenschappen**.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Objecten uploaden naar de *$web* container vanuit een bronmap.

> [!NOTE]
> Als u Azure Cloud Shell gebruikt, moet `\` u een escape-teken toevoegen wanneer u naar de `$web` container verwijst (bijvoorbeeld: ). `\$web` Als u een lokale installatie van de Azure CLI gebruikt, hoeft u het escape-teken niet te gebruiken.

In dit voorbeeld wordt ervan uitgegaan dat u opdrachten uitvoert vanuit de Azure Cloud Shell-sessie.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

* Vervang `<source-path>` de tijdelijke aanduiding door een pad naar de locatie van de bestanden die u wilt uploaden.

> [!NOTE]
> Als u een locatie-installatie van Azure CLI gebruikt, u het pad naar `C:\myFolder`elke locatie op uw lokale computer gebruiken (bijvoorbeeld:.
>
> Als u Azure Cloud Shell gebruikt, moet u verwijzen naar een bestandsshare dat zichtbaar is voor de Cloud Shell. Deze locatie kan het bestandsaandeel zijn van het Cloud-aandeel zelf of een bestaand bestandsaandeel dat u vanuit de Cloud Shell hebt gemonteerd. Zie [Blijvende bestanden in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)voor meer informatie over hoe u dit doen.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Objecten uploaden naar de *$web* container vanuit een bronmap.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* Vervang `<path-to-file>` de tijdelijke aanduidingswaarde door het volledig gekwalificeerde pad naar `C:\temp\index.html`het bestand dat u wilt uploaden (bijvoorbeeld: ).

* Vervang `<blob-name>` de tijdelijke aanduidingswaarde door de naam die u de `index.html`resulterende blob wilt geven (bijvoorbeeld: ).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>De URL van de website zoeken met behulp van de Azure-portal

U de pagina's van uw site bekijken vanuit een browser met behulp van de openbare URL van de website.

### <a name="portal"></a>[Portal](#tab/azure-portal)

<a id="portal-find-url" />

Selecteer **Statische website**in het deelvenster dat wordt weergegeven naast de pagina met accountoverzicht van uw opslagaccount . De URL van uw site wordt weergegeven in het veld **Primair eindpunt.**

![Metrische gegevens metriek voor statische websites azure storage](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

<a id="cli-find-url" />

Zoek de openbare URL van uw statische website met behulp van de volgende opdracht:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

* Vervang `<resource-group-name>` de tijdelijke aanduidingswaarde door de naam van uw resourcegroep.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell-find-url" />

Zoek de openbare URL van uw statische website met behulp van de volgende opdracht:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Vervang `<resource-group-name>` de tijdelijke aanduidingswaarde door de naam van uw resourcegroep.

* Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Statistieken inschakelen op statische websitepagina's

Zodra u statistieken hebt ingeschakeld, worden verkeersstatistieken over bestanden in de **$web** container gerapporteerd in het dashboard met statistieken.

1. Klik **op Statistieken** onder het gedeelte **Monitor** van het menu voor opslagaccount.

   > [!div class="mx-imgBorder"]
   > ![Koppeling Statistieken](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Statistieken gegevens worden gegenereerd door haaking in verschillende metrics API's. De portal toont alleen API-leden die binnen een bepaald tijdsbestek worden gebruikt om zich alleen te richten op leden die gegevens retourneren. Om ervoor te zorgen dat u het benodigde API-lid selecteren, is de eerste stap het uitbreiden van het tijdsbestek.

2. Klik op de knop tijdsbestek, kies een tijdsbestek en klik op **Toepassen**.

   ![Statische websites voor Azure Storage metrics tijdsbereik](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selecteer **Blob** in de vervolgkeuzelijst *Naamruimte.*

   ![Statische websites voor azure storage metrics namespace](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Selecteer vervolgens de statistiek **Uitgang.**

   ![Metrische gegevens metriek voor statische websites azure storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selecteer **Som** in de *groep Aggregatie.*

   ![Aggregatie van statische websites voor Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klik **op** de knop Filter toevoegen en kies **API-naam** in de *eigenschappenkiezer.*

   ![API-naam api-naam azure storage statische websites](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Schakel het selectievakje naast **GetWebContent** in de selector *Waarden* in om het rapport met statistieken in te vullen.

   ![Statische websites met Azure Storage-statistieken GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > Het selectievakje **GetWebContent** wordt alleen weergegeven als dat API-lid binnen een bepaald tijdsbestek is gebruikt. De portal toont alleen API-leden die binnen een bepaald tijdsbestek worden gebruikt om zich alleen te richten op leden die gegevens retourneren. Als u geen specifiek API-lid in deze lijst vinden, vouwt u het tijdsbestek uit.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het configureren van een aangepast domein met uw statische website. Zie [Een aangepast domein toewijzen aan een Azure Blob Storage-eindpunt](storage-custom-domain-name.md).

