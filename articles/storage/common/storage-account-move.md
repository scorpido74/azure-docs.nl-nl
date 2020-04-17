---
title: Een Azure Storage-account verplaatsen naar een andere regio | Microsoft Documenten
description: Hier ziet u hoe u een Azure Storage-account naar een andere regio verplaatst.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: c8578c518ac45bea147790028c2904c7ce36fffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459029"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Een Azure Storage-account verplaatsen naar een ander gebied

Als u een opslagaccount wilt verplaatsen, maakt u een kopie van uw opslagaccount in een andere regio. Verplaats vervolgens uw gegevens naar dat account met AzCopy of een ander hulpmiddel naar keuze.

In dit artikel leer je hoe je:

> [!div class="checklist"]
> 
> * Een sjabloon exporteren.
> * Wijzig de sjabloon door de naam van het doelgebied en het opslagaccount toe te voegen.
> * Implementeer de sjabloon om het nieuwe opslagaccount te maken.
> * Configureer het nieuwe opslagaccount.
> * Gegevens verplaatsen naar het nieuwe opslagaccount.
> * Verwijder de bronnen in het brongebied.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de services en functies die uw account gebruikt, worden ondersteund in de doelregio.

- Voor preview-functies moet u ervoor zorgen dat uw abonnement op de witte lijst staat voor de doelregio.

<a id="prepare" />

## <a name="prepare"></a>Voorbereiden

Als u aan de slag wilt gaan, exporteert u en wijzigt u vervolgens een sjabloon Resourcebeheer. 

### <a name="export-a-template"></a>Een sjabloon exporteren

Deze sjabloon bevat instellingen die uw opslagaccount beschrijven. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ga als lid van het werk om een sjabloon te exporteren met Azure-portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer **Alle resources** en selecteer vervolgens uw opslagaccount.

3. Selecteer > **>-instellingen-exportsjabloon**. **Settings**

4. Kies **Downloaden** in het **sjabloonblad Exporteren.**

5. Zoek het .zip-bestand dat u van de portal hebt gedownload en rits dat bestand uit naar een map naar keuze.

   Dit zip-bestand bevat de .json-bestanden die de sjabloon en scripts bevatten om de sjabloon te implementeren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ga als een voorbeeld met PowerShell:

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) en volg de aanwijzingen op het scherm:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement op het opslagaccount dat u wilt verplaatsen.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exporteer de sjabloon van uw bronopslagaccount. Met deze opdrachten wordt een json-sjabloon opgeslagen in uw huidige map.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>De sjabloon aanpassen 

Wijzig de sjabloon door de naam en het gebied van het opslagaccount te wijzigen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ga als een voorbeeld voor het implementeren van de sjabloon met Azure-portal:

1. Selecteer in Azure Portal **Een resource maken**.

2. In **Marketplace doorzoeken** typt u **sjabloonimplementatie**. Druk vervolgens op **ENTER**.

3. Selecteer **Sjabloonimplementatie**.

    ![Azure Resource Manager-sjabloonbibliotheek](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Selecteer **Maken**.

5. Selecteer **Bouw uw eigen sjabloon in de editor**.

6. Selecteer **Bestand laden**en volg de instructies om het **template.json-bestand** te laden dat u in de laatste sectie hebt gedownload.

7. Geef in het **bestand template.json** de naam van het doelopslagaccount door de standaardwaarde van de naam van het opslagaccount in te stellen. In dit voorbeeld wordt de standaardwaarde `mytargetaccount`van de naam van het opslagaccount ingesteld op .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    Zie Azure-locaties voor het verkrijgen van [locatiecodes voor regio's.](https://azure.microsoft.com/global-infrastructure/locations/)  De code voor een regio is de regionaam zonder spaties, **Central US** = **centralus**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ga als een voorbeeld voor het implementeren van de sjabloon met PowerShell:

1. Geef in het **bestand template.json** de naam van het doelopslagaccount door de standaardwaarde van de naam van het opslagaccount in te stellen. In dit voorbeeld wordt de standaardwaarde `mytargetaccount`van de naam van het opslagaccount ingesteld op .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Bewerk de **locatieeigenschap** in het **bestand template.json** naar het doelgebied. In dit voorbeeld wordt `eastus`het doelgebied ingesteld op .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    U regiocodes verkrijgen door de opdracht [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) uit te voeren.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Verplaatsen

Implementeer de sjabloon om een nieuw opslagaccount in het doelgebied te maken. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Sla het **bestand template.json** op.

2. Voer de eigenschapswaarden in of selecteer deze:

- **Subscription**: selecteer een Azure-abonnement.

- **Resourcegroep**: selecteer **Nieuwe maken** en geef de resourcegroep een naam.

- **Locatie:** Selecteer een Azure-locatie.

3. Klik **op** het selectievakje Akkoord gaan met de bovenstaande voorwaarden en klik vervolgens op Aankoop **selecteren.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verkrijg de abonnements-ID waar u het openbare doel-IP wilt implementeren met [Get-AzSubscription:](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Gebruik deze opdrachten om uw sjabloon te implementeren:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Het nieuwe opslagaccount configureren

Sommige functies worden niet geëxporteerd naar een sjabloon, dus u moet ze toevoegen aan het nieuwe opslagaccount. 

In de volgende tabel worden deze functies weergegeven, samen met richtlijnen voor het toevoegen ervan aan uw nieuwe opslagaccount.

| Functie    | Richtlijnen    |
|--------|-----------|
| **Beleid voor levenscyclusbeheer** | [De levenscyclus van Azure Blob-opslag beheren](../blobs/storage-lifecycle-management-concepts.md) |
| **Statische websites** | [Een statische website hosten in Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Gebeurtenisabonnementen** | [Reageren op gebeurtenissen van Blob Storage](../blobs/storage-blob-event-overview.md) |
| **Waarschuwingen** | [Waarschuwingen voor activiteitenlogboeken maken, weergeven en beheren met Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Azure CDN gebruiken om toegang te krijgen tot blobs met aangepaste domeinen via HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Als u een CDN instelt voor het bronopslagaccount, wijzigt u gewoon de oorsprong van uw bestaande CDN in het primaire eindpunt van de blobservice (of het primaire statische websiteeindpunt) van uw nieuwe account. 

### <a name="move-data-to-the-new-storage-account"></a>Gegevens verplaatsen naar het nieuwe opslagaccount

Hier zijn enkele manieren om uw gegevens te verplaatsen.

:heavy_check_mark: **Azure Storage Explorer**

  Het is gebruiksvriendelijk en geschikt voor kleine datasets. U containers en bestandsshares kopiëren en vervolgens in het doelaccount plakken.

  Zie [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/);

:heavy_check_mark: **AzCopy**

  Dit is de voorkeursbenadering. Het is geoptimaliseerd voor prestaties.  Een manier waarop het sneller is, is dat gegevens rechtstreeks tussen opslagservers worden gekopieerd, zodat AzCopy geen gebruik maakt van de netwerkbandbreedte van uw computer. Gebruik AzCopy op de opdrachtregel of als onderdeel van een aangepast script.

  Zie [Aan de slag met AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark: **Azure Data Factory** 

  Gebruik deze tool alleen als u functionaliteit nodig hebt die niet wordt ondersteund in de huidige versie van AzCopy. In de huidige versie van AzCopy u bijvoorbeeld geen blobs kopiëren tussen accounts met een hiërarchische naamruimte. Ook behoudt AzCopy geen lijsten met bestandstoegangsbeheer of bestandstijdstempels (bijvoorbeeld: tijdstempels maken en wijzigen). 

  Bekijk deze links:
  - [Gegevens kopiëren naar of vanuit Azure Blob-opslag met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Gegevens kopiëren naar of van Azure Data Lake Storage Gen2 met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Gegevens van of naar Azure-bestandsopslag kopiëren met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Gegevens kopiëren van en naar Azure Table-opslag met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Weggooien of opruimen

Als u na de implementatie opnieuw wilt beginnen, u het doelopslagaccount verwijderen en de stappen herhalen die zijn beschreven in de secties [Voorbereiden](#prepare) en [verplaatsen](#move) van dit artikel.

Als u de wijzigingen wilt vastleggen en de verplaatsing van een opslagaccount wilt voltooien, verwijdert u het bronopslagaccount.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ga als een mogelijk verwijderen uit een opslagaccount met de Azure-portal:

1. Vouw in de Azure-portal het menu aan de linkerkant uit om het menu met services te openen en kies **Opslagaccounts** om de lijst met uw opslagaccounts weer te geven.

2. Zoek het doelopslagaccount dat u wilt verwijderen en klik met de rechtermuisknop op de knop **Meer** (**...**) aan de rechterkant van de aanbieding.

3. Selecteer **Verwijderen**en bevestigen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de brongroep en de bijbehorende bronnen, waaronder het nieuwe opslagaccount, wilt verwijderen, gebruikt u de opdracht [Verwijderen-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure-opslagaccount van de ene regio naar de andere verplaatst en de bronbronnen opgeschoond.  Zie voor meer informatie over het verplaatsen van resources tussen regio's en disaster recovery in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
