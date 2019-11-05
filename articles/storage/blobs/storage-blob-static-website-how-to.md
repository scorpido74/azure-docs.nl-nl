---
title: Een statische website hosten in Azure Storage
description: Meer informatie over hoe u statische inhoud (HTML-, CSS-, java script-en afbeeldings bestanden) rechtstreeks vanuit een container in een Azure Storage GPv2-account kunt verwerken.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: d5b8121c7888903f3e4552a21a6ddc175ecc5176
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489094"
---
# <a name="host-a-static-website-in-azure-storage"></a>Een statische website hosten in Azure Storage

U kunt statische inhoud (HTML-, CSS-, java script-en afbeeldings bestanden) rechtstreeks vanuit een container in een Azure Storage GPv2-account verwerken. Zie voor meer informatie [statische website-hosting in azure Storage](storage-blob-static-website.md).

Dit artikel laat u zien hoe u statische website-hosting kunt inschakelen met behulp van de Azure Portal, de Azure CLI of Power shell.

<a id="portal" />

## <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Zie [zelf studie: een statische website hosten op Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)voor een stapsgewijze zelf studie.

Nadat u de functie voor het hosten van statische websites hebt ingeschakeld, kunt u de pagina's van uw site vanuit een browser weer geven met behulp van de open bare URL van de website.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>De URL van de website zoeken met behulp van de Azure Portal

In het deel venster dat wordt weer gegeven naast de pagina account overzicht van uw opslag account, selecteert u **statische website**. De URL van uw site wordt weer gegeven in het veld voor het **primaire eind punt** .

![Metrische gegevens voor metrische gegevens van statische websites Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

U kunt statisch website hosting inschakelen met behulp van de [Azure-opdracht regel interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Open eerst de [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)of als u de Azure cli lokaal hebt [geïnstalleerd](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , opent u een opdracht console toepassing zoals Windows Power shell.

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account dat als host voor uw statische website gaat.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Vervang de waarde van de tijdelijke `<subscription-id>` door de ID van uw abonnement.

3. Schakel statisch website hosting in.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

   * Vervang de tijdelijke aanduiding `<error-document-name>` door de naam van het fout document dat voor gebruikers wordt weer gegeven wanneer een browser een pagina op uw site aanvraagt die niet bestaat.

   * Vervang de tijdelijke aanduiding `<index-document-name>` door de naam van het index document. Dit document is vaak "index. html".

4. Objecten uploaden naar de *$Web* container vanuit een bronmap.

   > [!NOTE]
   > Als u Azure Cloud Shell gebruikt, moet u ervoor zorgen dat u een `\` escape-teken toevoegt bij het verwijzen naar de `$web` container (bijvoorbeeld `\$web`). Als u een lokale installatie van Azure CLI gebruikt, hoeft u het escape teken niet te gebruiken.

   In dit voor beeld wordt ervan uitgegaan dat u opdrachten uit Azure Cloud Shell sessie uitvoert.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

   * Vervang de tijdelijke aanduiding `<source-path>` door een pad naar de locatie van de bestanden die u wilt uploaden.

   > [!NOTE]
   > Als u een locatie-installatie van Azure CLI gebruikt, kunt u het pad naar een wille keurige locatie op de lokale computer gebruiken (bijvoorbeeld: `C:\myFolder`.
   >
   > Als u Azure Cloud Shell gebruikt, moet u verwijzen naar een bestands share die zichtbaar is voor de Cloud Shell. Deze locatie kan de bestands share zijn van de Cloud share zelf of een bestaande bestands share die u koppelt van de Cloud Shell. Zie [bestanden in azure Cloud shell persistent](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)maken voor meer informatie over hoe u dit doet.

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>De URL van de website zoeken met behulp van de Azure CLI

U kunt inhoud vanuit een browser weer geven met behulp van de open bare URL van de website.

Zoek de URL met behulp van de volgende opdracht:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

* Vervang de waarde van de tijdelijke aanduiding `<resource-group-name>` door de naam van uw resource groep.

<a id="powershell" />

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt statisch website hosting inschakelen met behulp van de module Azure PowerShell.

1. Open een Windows Power shell-opdracht venster.

2. Controleer of u Azure PowerShell module AZ versie 0,7 of hoger hebt.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

3. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

   ```powershell
   Connect-AzAccount
   ```

4. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account dat als host voor uw statische website gaat.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Vervang de waarde van de tijdelijke `<subscription-id>` door de ID van uw abonnement.

5. Haal de context van het opslag account op waarmee het opslag account wordt gedefinieerd dat u wilt gebruiken.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Vervang de waarde van de tijdelijke aanduiding `<resource-group-name>` door de naam van uw resource groep.

   * Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

6. Schakel statisch website hosting in.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Vervang de tijdelijke aanduiding `<error-document-name>` door de naam van het fout document dat voor gebruikers wordt weer gegeven wanneer een browser een pagina op uw site aanvraagt die niet bestaat.

   * Vervang de tijdelijke aanduiding `<index-document-name>` door de naam van het index document. Dit document is vaak "index. html".

7. Objecten uploaden naar de *$Web* container vanuit een bronmap.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Vervang de waarde van de tijdelijke aanduiding `<path-to-file>` door het volledig gekwalificeerde pad naar het bestand dat u wilt uploaden (bijvoorbeeld: `C:\temp\index.html`).

   * Vervang de waarde van de tijdelijke aanduiding `<blob-name>` door de naam die u de resulterende BLOB wilt geven (bijvoorbeeld: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>De URL van de website zoeken met behulp van Power shell

U kunt inhoud vanuit een browser weer geven met behulp van de open bare URL van de website.

Zoek de URL met behulp van de volgende opdracht:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Vervang de waarde van de tijdelijke aanduiding `<resource-group-name>` door de naam van uw resource groep.

* Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

<a id="metrics" />

---

## <a name="enable-metrics-on-static-website-pages"></a>Metrische gegevens inschakelen op de vaste website pagina's

Wanneer u metrische gegevens hebt ingeschakeld, worden verkeers statistieken voor bestanden in de **$Web** container gerapporteerd in het dash board metrische gegevens.

1. Klik op **instellingen** > **bewaking** > **metrische gegevens**.

   Metrische gegevens worden gegenereerd door te hooken in verschillende metrische Api's. In de portal worden alleen de API-leden weer gegeven die binnen een bepaalde periode zijn gebruikt, zodat ze alleen kunnen worden gericht op leden die gegevens retour neren. Om ervoor te zorgen dat u het benodigde API-lid kunt selecteren, is de eerste stap om het tijds bestek uit te breiden.

2. Klik op de knop tijds bestek en selecteer **laatste 24 uur** en klik vervolgens op **Toep assen**.

   ![Tijds bereik voor metrische gegevens van statische websites Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selecteer **BLOB** in de vervolg keuzelijst *naam ruimte* .

   ![Naam ruimte voor metrische gegevens van statische websites Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Selecteer **vervolgens de waarde** voor uitgaand verkeer.

   ![Metrische gegevens voor metrische gegevens van statische websites Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selecteer **som** uit de *aggregatie* kiezer.

   ![Aggregatie van metrische gegevens van statische websites Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klik op de knop **filter toevoegen** en kies de naam van de **API** in de *Eigenschappen* kiezer.

   ![API-naam van metrische gegevens voor de Azure Storage van statische websites](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Schakel het selectie vakje naast **GetWebContent** in de *waarde* selector in om het meet rapport in te vullen.

   ![Azure Storage metrische gegevens van statische websites GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Volgende stappen

* [Een statische website hosten in Azure Storage](storage-blob-static-website.md)
* [De Azure CDN gebruiken om toegang te krijgen tot blobs met aangepaste domeinen via HTTPS](storage-https-custom-domain-cdn.md)
* [Een aangepaste domein naam configureren voor uw BLOB of Web-eind punt](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Uw eerste serverloze web-app bouwen](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Zelf studie: uw domein hosten in Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
