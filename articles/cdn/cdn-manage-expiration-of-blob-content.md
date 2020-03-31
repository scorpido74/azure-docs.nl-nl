---
title: Verloop van Azure Blob-opslag beheren
titleSuffix: Azure Content Delivery Network
description: Meer informatie over de opties voor het beheren van time-to-live voor blobs in Azure CDN-caching.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: f28282a802e4b38fadc05c7090fa2a2af154de54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083161"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Verloop van Azure Blob-opslag beheren in Azure CDN
> [!div class="op_single_selector"]
> * [Azure-webinhoud](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob-opslag](cdn-manage-expiration-of-blob-content.md)
> 
> 

De [Blob-opslagservice](../storage/common/storage-introduction.md#blob-storage) in Azure Storage is een van de verschillende op Azure gebaseerde origins die zijn geïntegreerd met Het Azure Content Delivery Network (CDN). Alle openbaar toegankelijke blob-inhoud kan in Azure CDN in de cache worden opgeslagen totdat de time-to-live (TTL) is verstreken. De TTL wordt `Cache-Control` bepaald door de header in het HTTP-antwoord van de origin-server. In dit artikel worden verschillende manieren `Cache-Control` beschreven waarop u de koptekst op een blob in Azure Storage instellen.

U ook cache-instellingen vanuit de Azure-portal beheren door cdn-cachingregels in te stellen. Als u een cachingregel maakt en het caching-gedrag instelt op **Cache overschrijven** of **omzeilen,** worden de in dit artikel besproken cacheinstellingen voor caching die in de oorsprong zijn opgenomen, genegeerd. Zie [Hoe caching werkt](cdn-how-caching-works.md)voor informatie over algemene cachingconcepten.

> [!TIP]
> U ervoor kiezen om geen TTL in te stellen op een blob. In dit geval past Azure CDN automatisch een standaard TTL van zeven dagen toe, tenzij u cachingregels hebt ingesteld in de Azure-portal. Deze standaard TTL is alleen van toepassing op algemene webdelivery-optimalisaties. Voor grote bestandsoptimalisaties is de standaard TTL één dag en voor mediastreamingoptimalisaties is de standaardTTL één jaar.
> 
> Zie [Overzicht van het Azure Content Delivery Network](cdn-overview.md)voor meer informatie over hoe Azure CDN werkt om de toegang tot blobs en andere bestanden te versnellen.
> 
> Zie [Inleiding tot Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)voor meer informatie over Azure Blob-opslag.
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Cache-Control-headers instellen met cdn-cachingregels
De voorkeursmethode voor het `Cache-Control` instellen van de koptekst van een blob is het gebruik van cachingregels in de Azure-portal. Zie Azure [CDN-cachinggedrag beheren met cachingregels](cdn-caching-rules.md)voor meer informatie over cdn-cachingregels.

> [!NOTE] 
> Caching-regels zijn alleen beschikbaar voor **Azure CDN Standard van Verizon** en Azure **CDN Standard van Akamai-profielen.** Voor **Azure CDN Premium van Verizon-profielen** moet u de [Azure CDN-regelsengine](cdn-rules-engine.md) in de **portal Beheren** gebruiken voor vergelijkbare functionaliteit.

**Ga als volgt te werk om naar de pagina REGELS voor cdn-caching te navigeren:**

1. Selecteer in de Azure-portal een CDN-profiel en selecteer vervolgens het eindpunt voor de blob.

2. Selecteer in het linkerdeelvenster onder Instellingen de optie **Regels voor opslaan in cache**.

   ![Knop REGELS voor cdn-caching](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   De pagina **Regels voor opslaan in cache** wordt weergegeven.

   ![CDN-cachepagina](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Ga als volgt te werk om de cachebeheerkoppen van een Blob-opslagservice in te stellen met behulp van algemene regels voor cachezetten:**

1. Stel **onder Regels voor cache globaal**query het **cachinggedrag** in **op Querytekenreeksen negeren** en Stel **Caching-gedrag** in **op Overschrijven**.
      
2. Voer 3600 in voor de vervaldatum van de **cache**in het vak **Seconden** of 1 in het vak **Uren.** 

   ![Voorbeeld van cdn-regels voor caching](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Deze algemene cachingregel stelt een cacheduur van één uur in en is van invloed op alle aanvragen voor het eindpunt. Het overschrijft `Cache-Control` `Expires` alle of HTTP-headers die worden verzonden door de oorsprongsserver die is opgegeven door het eindpunt.   

3. Selecteer **Opslaan**.
 
**Ga als volgt te werk om de cachebeheerkoppen van een blobbestand in te stellen met aangepaste cachingregels:**

1. Maak **onder Aangepaste cachingregels**twee overeenkomende voorwaarden:

     A. Stel de **voorwaarde Overeenkomen** in voor de eerste wedstrijdvoorwaarde op **Pad** en voer de waarde Overeenkomen `/blobcontainer1/*` **in.** **Stel Caching-gedrag** in **op Overschrijven** en voer 4 in het vak **Uren** in.

    B. Stel voor de tweede wedstrijdvoorwaarde de `/blobcontainer1/blob1.txt` voorwaarde **Overeenkomen** in op **Pad** en voer de waarde Overeenkomen **in.** **Stel Caching-gedrag** in **op Overschrijven** en voer 2 in het vak **Uren** in.

    ![Voorbeeld van aangepaste cachingregels van CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    De eerste aangepaste cachingregel stelt een cacheduur van `/blobcontainer1` vier uur in voor blobbestanden in de map op de oorsprongsserver die door uw eindpunt is opgegeven. De tweede regel overschrijft alleen `blob1.txt` de eerste regel voor het blobbestand en stelt hiervoor een cacheduur van twee uur in.

2. Selecteer **Opslaan**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Cache-control-headers instellen met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/overview) is een van de snelste en krachtigste manieren om uw Azure-services te beheren. Gebruik `Get-AzStorageBlob` de cmdlet om een verwijzing naar `.ICloudBlob.Properties.CacheControl` de blob te krijgen en stel vervolgens de eigenschap in. 

Bijvoorbeeld:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> U PowerShell ook gebruiken om [uw CDN-profielen en eindpunten](cdn-manage-powershell.md)te beheren.
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Cache-control-headers instellen met .NET
Als u de `Cache-Control` koptekst van een blob wilt opgeven met behulp van .NET-code, gebruikt u de [Azure Storage Client Library voor .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) om de eigenschap [CloudBlob.Properties.CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol) in te stellen.

Bijvoorbeeld:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Er zijn meer .NET-codevoorbeelden beschikbaar in [Azure Blob Storage Samples voor .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Cache-control-headers instellen met behulp van andere methoden

### <a name="azure-storage-explorer"></a>Azure Opslagverkenner
Met [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)u uw blobopslagbronnen weergeven en bewerken, inclusief eigenschappen zoals de eigenschap *CacheControl.* 

Ga als lid van het uitvoeren van de eigenschap *CacheControl* van een blob met Azure Storage Explorer:
   1. Selecteer een blob en selecteer **Eigenschappen** in het contextmenu. 
   2. Schuif omlaag naar de eigenschap *CacheControl.*
   3. Voer een waarde in en selecteer **Opslaan**.


![Eigenschappen van Azure Storage Explorer](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure-opdrachtregelinterface
Met de [Azure Command-Line Interface](https://docs.microsoft.com/cli/azure) (CLI) u Azure blob-bronnen beheren vanaf de opdrachtregel. Als u de header cachebeheer wilt instellen wanneer u een blob uploadt `-p` met de Azure CLI, stelt u de eigenschap *cacheControl* in met behulp van de switch. In het volgende voorbeeld ziet u hoe u de TTL instelt op één uur (3600 seconden):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>REST API voor Azure-opslagservices
U de [REST API voor Azure-opslagservices](/rest/api/storageservices/) gebruiken om de eigenschap *x-ms-blob-cache-control* expliciet in te stellen met behulp van de volgende bewerkingen op een verzoek:
  
   - [Blob plaatsen](/rest/api/storageservices/Put-Blob)
   - [Lijst met blokkeringplaatsen plaatsen](/rest/api/storageservices/Put-Block-List)
   - [Blob-eigenschappen instellen](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>De header Cache-Control testen
U eenvoudig de TTL-instellingen van uw blobs verifiëren. Test met de [hulpprogramma's voor ontwikkelaars](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) `Cache-Control` van uw browser of uw blob de antwoordkop bevat. U ook een tool zoals [Wget,](https://www.gnu.org/software/wget/) [Postman](https://www.getpostman.com/)of [Fiddler](https://www.telerik.com/fiddler) gebruiken om de antwoordkoppen te onderzoeken.

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het beheren van de vervaldatum van Cloud Service-inhoud in Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Meer informatie over cachingconcepten](cdn-how-caching-works.md)

