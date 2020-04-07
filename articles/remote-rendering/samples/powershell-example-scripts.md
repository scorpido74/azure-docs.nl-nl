---
title: Voorbeeld PowerShell-scripts
description: Voorbeelden die laten zien hoe je de front-end gebruiken via PowerShell-scripts
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: d7238c899301d76edde86d52c6db74468ffe18ad
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679476"
---
# <a name="example-powershell-scripts"></a>Voorbeeld PowerShell-scripts

Azure Remote Rendering biedt de volgende twee REST-API's:

- [API voor conversierest](../how-tos/conversion/conversion-rest-api.md)
- [API sessieREST](../how-tos/session-rest-api.md)

De [ARR-voorbeeldopslagplaats](https://github.com/Azure/azure-remote-rendering) bevat voorbeeldscripts in de map *Scripts* voor interactie met de REST-API's van de service. In dit artikel wordt het gebruik ervan beschreven.

## <a name="prerequisites"></a>Vereisten

Als u de voorbeeldscripts wilt uitvoeren, hebt u een functionele installatie van [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)nodig.

1. Azure PowerShell installeren:
    1. Een PowerShell openen met beheerdersrechten
    1. Uitvoeren:`Install-Module -Name Az -AllowClobber`

1. Als er fouten worden gemaakt bij het uitvoeren van scripts, moet u ervoor zorgen dat uw [uitvoeringsbeleid](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) op de juiste manier is ingesteld:
    1. Een PowerShell openen met beheerdersrechten
    1. Uitvoeren:`Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Een Azure Storage-account voorbereiden](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Meld u aan bij het abonnement dat uw Azure Remote Rendering-account bevat:
    1. Een PowerShell openen
    1. Uitvoeren:`Connect-AzAccount -Subscription "<your Azure subscription id>"`

1. Download de map *Scripts* in de [Azure Remote Rendering GithHub-repository.](https://github.com/Azure/azure-remote-rendering)

## <a name="configuration-file"></a>Configuratiebestand

Naast de `.ps1` bestanden is `arrconfig.json` er een die je moet invullen:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Zorg ervoor dat u backslashes in het LocalAssetDirectoryPath-pad\\\\goed verwijderen door dubbele backslashes te gebruiken: en voorwaartse slashes / te gebruiken in alle andere paden zoals inputFolderPath en inputAssetPath.

### <a name="accountsettings"></a>accountInstellingen

Zie `arrAccountId` `arrAccountKey`Een [Azure Remote Rendering-account maken voor](../how-tos/create-an-account.md)en zie Een Azure Remote Rendering-account maken .
Zie `region` de [lijst met beschikbare regio's](../reference/regions.md)voor zie .

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Deze structuur moet worden ingevuld als u **RenderingSession.ps1**wilt uitvoeren.

- **vmSize:** Hiermee selecteert u de grootte van de virtuele machine. Selecteer *standaard* of *premium*. Schakel renderingsessies uit als je ze niet meer nodig hebt.
- **maxLeaseTime:** De duur waarvoor u de VM wilt leasen. Het zal worden stilgelegd wanneer de huurovereenkomst afloopt. De leasetijd kan later worden verlengd (zie hieronder).

### <a name="assetconversionsettings"></a>assetConversieinstellingen

Deze structuur moet worden ingevuld als u **Conversion.ps1**wilt uitvoeren.

Zie Een [Azure Storage-account voorbereiden](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)voor meer informatie .

## <a name="script-renderingsessionps1"></a>Script: RenderingSession.ps1

Dit script wordt gebruikt voor het maken, query's en stoppen met renderingsessies.

> [!IMPORTANT]
> Zorg ervoor dat je de secties *AccountInstellingen* en *renderingSessionSettings* in arrconfig.json hebt ingevuld.

### <a name="create-a-rendering-session"></a>Een renderingsessie maken

Normaal gebruik met een volledig ingevuldarrconfig.json:

```PowerShell
.\RenderingSession.ps1
```

Het script roept de [REST API voor sessiebeheer](../how-tos/session-rest-api.md) aan om een rendering VM met de opgegeven instellingen op te draaien. Bij succes, zal het de *sessionId*ophalen. Vervolgens wordt de sessie-eigenschappen gepeild totdat de sessie klaar is of er een fout is opgetreden.

Ga als een **alternatief config-bestand** te werk:

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

U afzonderlijke instellingen uit het config-bestand **overschrijven:**

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Als u een sessie alleen **wilt starten zonder polling,** u het als:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

De *sessionId* die het script ophaalt, moet worden doorgegeven aan de meeste andere sessieopdrachten.

### <a name="retrieve-session-properties"></a>Sessie-eigenschappen ophalen

Voer het als instellen dat de eigenschappen van een sessie worden uitgevoerd:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Als `-Poll` u wilt wachten tot de sessie *gereed* is of er een fout is opgetreden.

### <a name="list-active-sessions"></a>Actieve sessies weergeven

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Een sessie stoppen

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Sessie-eigenschappen wijzigen

Op dit moment ondersteunen we alleen het wijzigen van de maxLeaseTime van een sessie.

> [!NOTE]
> De leasetijd wordt altijd meegeteld vanaf het moment dat de sessie-VM in eerste instantie werd gemaakt. Dus om de sessie lease te verlengen met een uur, verhoging *maxLeaseTime* met een uur.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Script: Conversion.ps1

Dit script wordt gebruikt om invoermodellen om te zetten in de specifieke runtime-indeling azure extern renderen.

> [!IMPORTANT]
> Zorg ervoor dat je de secties *AccountInstellingen* en *assetConversionSettings* in arrconfig.json hebt ingevuld.

Het script toont de twee opties om opslagaccounts met de service te gebruiken:

- Opslagaccount gekoppeld aan Azure Remote Rendering Account
- Toegang bieden tot opslag via Shared Access Signatures (SAS)

### <a name="linked-storage-account"></a>Gekoppelde opslagaccount

Zodra je arrconfig.json volledig hebt ingevuld en een opslagaccount hebt gekoppeld, kun je de volgende opdracht gebruiken. Het koppelen van uw opslagaccount wordt beschreven bij [Een account maken.](../how-tos/create-an-account.md#link-storage-accounts)

Het gebruik van een gekoppeld opslagaccount is de beste manier om de conversieservice te gebruiken, omdat het niet nodig is om handtekeningen voor gedeelde toegang te genereren.

```PowerShell
.\Conversion.ps1
```

1. Upload alle bestanden in `assetConversionSettings.modelLocation` de invoerblobcontainer onder de opgegeven`inputFolderPath`
1. Roep de [REST API voor modelconversie](../how-tos/conversion/conversion-rest-api.md) om de [modelconversie](../how-tos/conversion/model-conversion.md) te starten
1. De conversiestatus peilen totdat de conversie is geslaagd of mislukt
1. Uitvoergegevens van de geconverteerde bestandslocatie (opslagaccount, uitvoercontainer, bestandspad in de container)

### <a name="access-to-storage-via-shared-access-signatures"></a>Toegang tot opslag via handtekeningen voor gedeelde toegang

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Dit zal:

1. Het lokale bestand `assetConversionSettings.localAssetDirectoryPath` uploaden van de invoerblobcontainer naar de invoerblob
1. Een SAS URI genereren voor de invoercontainer
1. Een SAS URI genereren voor de uitvoercontainer
1. Roep de [REST API voor modelconversie](../how-tos/conversion/conversion-rest-api.md) om de [modelconversie](../how-tos/conversion/model-conversion.md) te starten
1. De conversiestatus peilen totdat de conversie is geslaagd of mislukt
1. Uitvoergegevens van de geconverteerde bestandslocatie (opslagaccount, uitvoercontainer, bestandspad in de container)
1. Een SAS URI uitzetten naar het geconverteerde model in de uitvoerblobcontainer

### <a name="additional-command-line-options"></a>Aanvullende opdrachtregelopties

Ga als een **alternatief config-bestand** te werk:

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Als u alleen **modelconversie wilt starten zonder polling,** u het als:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

U afzonderlijke instellingen uit het config-bestand **overschrijven** met de volgende opdrachtregelswitches:

* **Id:** ConversionId gebruikt met GetConversionStatus
* **ArrAccountId:** arrAccountId van accountInstellingen
* **ArrAccountKey:** overschrijven voor arrAccountKey van accountInstellingen
* **Regio:** overschrijven voor regio van accountInstellingen
* **ResourceGroup:** overschrijven voor resourceGroep van assetConversionSettings
* **StorageAccountName:** overschrijven voor storageAccountName van assetConversionSettings
* **BlobInputContainerName:** overschrijven voor blobInputContainer van assetConversionSettings
* **LocalAssetDirectoryPath:** overschrijven voor localAssetDirectoryPath van assetConversionSettings
* **InputAssetPath:** overschrijven voor invoerAssetPath van assetConversionSettings
* **BlobOutputContainerName:** overschrijven voor blobOutputContainerName van assetConversieinstellingen
* **OutputFolderPath:** overschrijven voor de uitvoerMapPath van assetConversieInstellingen
* **OutputAssetFileName:** overschrijven voor uitvoerAssetFileName van assetConversieinstellingen

U bijvoorbeeld een aantal van de gegeven opties als deze combineren:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>De afzonderlijke conversiefasen uitvoeren

Als u afzonderlijke stappen van het proces wilt uitvoeren, u het volgende gebruiken:

Upload alleen gegevens van de opgegeven LocalAssetDirectoryPath

```PowerShell
.\Conversion.ps1 -Upload
```

Start alleen het conversieproces van een model dat al is geüpload naar blobopslag (niet uploaden, peil de conversiestatus niet) Het script retourneert een *conversieId.*

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

En u de conversiestatus van deze conversie ophalen met:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Met `-Poll` gebruiken om te wachten tot de conversie is gedaan of er een fout is opgetreden.

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: een model weergeven met Unity](../quickstarts/render-model.md)
- [Snelstart: een model converteren voor rendering](../quickstarts/convert-model.md)
- [Modelconversie](../how-tos/conversion/model-conversion.md)
