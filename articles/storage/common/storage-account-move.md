---
title: Een Azure Storage-account naar een andere regio verplaatsen | Microsoft Docs
description: Laat zien hoe u een Azure Storage account naar een andere regio kunt verplaatsen.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 8ce949ac997ba7ee38cb057752d89f4b4d22388f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838708"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Een Azure Storage-account naar een andere regio verplaatsen

Als u een opslag account wilt verplaatsen, maakt u een kopie van uw opslag account in een andere regio. Verplaats uw gegevens vervolgens naar het account met behulp van AzCopy of een ander hulp programma van uw keuze.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> 
> * Een sjabloon exporteren.
> * Wijzig de sjabloon door de doel regio en de naam van het opslag account toe te voegen.
> * Implementeer de sjabloon om het nieuwe opslag account te maken.
> * Configureer het nieuwe opslag account.
> * Verplaats gegevens naar het nieuwe opslag account.
> * Verwijder de resources in de bron regio.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de services en functies die uw account gebruikt worden ondersteund in de doel regio.

- Zorg ervoor dat uw abonnement white list is voor de doel regio voor preview-functies.

<a id="prepare" />

## <a name="prepare"></a>Voorbereiden

Om aan de slag te gaan, een resource manager-sjabloon te exporteren en vervolgens te wijzigen. 

### <a name="export-a-template"></a>Een sjabloon exporteren

Deze sjabloon bevat instellingen die uw opslag account beschrijven. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Een sjabloon exporteren met behulp van Azure Portal:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Selecteer **alle resources** en selecteer vervolgens uw opslag account.

3. Selecteer > **instellingen** > **sjabloon exporteren**.

4. Kies **downloaden** in de Blade **sjabloon exporteren** .

5. Zoek het zip-bestand dat u hebt gedownload van de portal en pak het bestand uit naar een map van uw keuze.

   Dit zip-bestand bevat de. json-bestanden waaruit de sjabloon en scripts bestaan voor het implementeren van de sjabloon.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Een sjabloon exporteren met behulp van Power shell:

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) en volg de instructies op het scherm:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account dat u wilt verplaatsen.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exporteer de sjabloon van uw bron-opslag account. Met deze opdrachten wordt een JSON-sjabloon opgeslagen in de huidige map.

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

Wijzig de sjabloon door de naam en regio van het opslag account te wijzigen.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

De sjabloon implementeren met behulp van Azure Portal:

1. Selecteer in Azure Portal **Een resource maken**.

2. In **Marketplace doorzoeken** typt u **sjabloonimplementatie**. Druk vervolgens op **ENTER**.

3. Selecteer **Sjabloonimplementatie**.

    ![Azure Resource Manager-sjabloonbibliotheek](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Selecteer **Maken**.

5. Selecteer **Bouw uw eigen sjabloon in de editor**.

6. Selecteer **bestand laden**en volg de instructies voor het laden van het **sjabloon. json** -bestand dat u hebt gedownload in de laatste sectie.

7. Geef in het bestand **Template. json** de naam van het doel-opslag account op door de standaard waarde van de naam van het opslag account in te stellen. In dit voor beeld wordt de standaard waarde van de naam van het opslag account ingesteld op `mytargetaccount`.
    
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
    Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van regio-locatie codes.  De code voor een regio is de naam van de regio zonder spaties, **centrale amerikaanse** = **centralus**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

De sjabloon implementeren met behulp van Power shell:

1. Geef in het bestand **Template. json** de naam van het doel-opslag account op door de standaard waarde van de naam van het opslag account in te stellen. In dit voor beeld wordt de standaard waarde van de naam van het opslag account ingesteld op `mytargetaccount`.
    
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

2. Bewerk de eigenschap **Location** in het bestand **Template. json** in de doel regio. In dit voor beeld wordt de doel regio ingesteld op `eastus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    U kunt regio codes verkrijgen door de opdracht [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) uit te voeren.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Verplaatsen

Implementeer de sjabloon om een nieuw opslag account te maken in de doel regio. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Sla het bestand **Template. json** op.

2. Voer de eigenschaps waarden in of Selecteer deze:

- **Subscription**: selecteer een Azure-abonnement.

- **Resourcegroep**: selecteer **Nieuwe maken** en geef de resourcegroep een naam.

- **Locatie**: Selecteer een Azure-locatie.

3. Klik op het selectie vakje **Ik ga akkoord met de bovenstaande voor waarden** en klik vervolgens op de knop **aankoop selecteren** .

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Vraag de abonnements-ID op waar u het doel-open bare IP-adres wilt implementeren met [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Gebruik de volgende opdrachten om uw sjabloon te implementeren:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Het nieuwe opslag account configureren

Sommige functies worden niet naar een sjabloon geëxporteerd, dus moet u deze toevoegen aan het nieuwe opslag account. 

De volgende tabel bevat een overzicht van deze functies en richt lijnen voor het toevoegen van ze aan uw nieuwe opslag account.

| Functie    | Hulp    |
|--------|-----------|
| **Levenscyclus beheer beleid** | [De levenscyclus van Azure Blob-opslag beheren](../blobs/storage-lifecycle-management-concepts.md) |
| **Statische websites** | [Een statische website hosten in Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Gebeurtenis abonnementen** | [Reageren op gebeurtenissen van Blob Storage](../blobs/storage-blob-event-overview.md) |
| **Waarschuwingen** | [Waarschuwingen voor activiteiten logboek maken, weer geven en beheren met behulp van Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Azure CDN gebruiken om toegang te krijgen tot blobs met aangepaste domeinen via HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Als u een CDN hebt ingesteld voor het bron-opslag account, wijzigt u alleen de oorsprong van uw bestaande CDN in het eind punt van de primaire BLOB-service (of het primaire eind punt van een statische website) van uw nieuwe account. 

### <a name="move-data-to-the-new-storage-account"></a>Gegevens verplaatsen naar het nieuwe opslag account

Hier volgen enkele manieren om uw gegevens te verplaatsen.

: heavy_check_mark: **Azure Storage Explorer**

  Het is eenvoudig te gebruiken en geschikt voor kleine gegevens sets. U kunt containers en bestands shares kopiëren en deze vervolgens in het doel account plakken.

  Zie [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/);

: heavy_check_mark: **AzCopy**

  Dit is de aanbevolen benadering. Het is geoptimaliseerd voor prestaties.  Een van de manieren waarop het sneller is, is dat gegevens rechtstreeks tussen opslag servers worden gekopieerd, zodat AzCopy niet de netwerk bandbreedte van uw computer gebruikt. Gebruik AzCopy op de opdracht regel of als onderdeel van een aangepast script.

  Zie [aan de slag met AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

: heavy_check_mark: **Azure Data Factory** 

  Gebruik dit hulp programma alleen als u functionaliteit nodig hebt die niet wordt ondersteund in de huidige versie van AzCopy. In de huidige release van AzCopy kunt u bijvoorbeeld geen blobs kopiëren tussen accounts die een hiërarchische naam ruimte hebben. AzCopy behoudt ook bestands toegangscontrole lijsten of bestands tempels (bijvoorbeeld: Create and modified time Stamps). 

  Zie de volgende koppelingen:
  - [Gegevens kopiëren naar of van Azure Blob-opslag met behulp van Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Gegevens kopiëren naar of van Azure Data Lake Storage Gen2 met behulp van Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Gegevens kopiëren van of naar Azure File Storage met behulp van Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Gegevens kopiëren van en naar Azure-tabel opslag met behulp van Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Verwijderen of opschonen

Als u na de implementatie wilt beginnen, kunt u het doel opslag account verwijderen en de stappen herhalen die worden beschreven in de secties [voorbereiden](#prepare) en [verplaatsen](#move) van dit artikel.

Als u de wijzigingen wilt door voeren en de verplaatsing van een opslag account wilt volt ooien, moet u het bron opslag account verwijderen.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Een opslag account verwijderen met behulp van de Azure Portal:

1. Vouw in het Azure Portal het menu aan de linkerkant uit om het menu met services te openen en kies **opslag accounts** om de lijst met uw opslag accounts weer te geven.

2. Zoek het doel-opslag account dat u wilt verwijderen en klik met de rechter muisknop op de knop **meer** ( **...** ) aan de rechter kant van de vermelding.

3. Selecteer **verwijderen**en bevestigen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de resource groep en de bijbehorende resources wilt verwijderen, met inbegrip van het nieuwe opslag account, gebruikt u de opdracht [Remove-AzStorageAccount](/powershell/module/az.resources/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure Storage-account verplaatst van de ene regio naar een andere en de bron resources opgeschoond.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
