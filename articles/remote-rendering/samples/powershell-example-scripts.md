---
title: PowerShell-voorbeeldscripts
description: Voor beelden die laten zien hoe u de front-end kunt gebruiken via Power shell-scripts
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: c45d2fc34ccbab6d813f12563678d036f9f35753
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80891489"
---
# <a name="example-powershell-scripts"></a>PowerShell-voorbeeldscripts

Externe rendering van Azure biedt de volgende twee REST-Api's:

- [Conversie REST API](../how-tos/conversion/conversion-rest-api.md)
- [Sessie REST API](../how-tos/session-rest-api.md)

De [opslag plaats met ARR](https://github.com/Azure/azure-remote-rendering) -voor beelden bevat voorbeeld scripts in de map *scripts* voor interactie met de rest api's van de service. In dit artikel wordt het gebruik beschreven.

## <a name="prerequisites"></a>Vereisten

Als u de voorbeeld scripts wilt uitvoeren, hebt u een functionele installatie van [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)nodig.

1. Azure PowerShell installeren:
    1. Open een Power shell met beheerders rechten
    1. Uitvoeringsrun`Install-Module -Name Az -AllowClobber`

1. Als er fouten optreden met het uitvoeren van scripts, zorgt u ervoor dat het [uitvoerings beleid](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) op de juiste wijze is ingesteld:
    1. Open een Power shell met beheerders rechten
    1. Uitvoeringsrun`Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Een Azure Storage-account voorbereiden](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Meld u aan bij uw abonnement dat uw Azure-account voor externe rendering bevat:
    1. Een Power shell openen
    1. Uitvoeren: `Connect-AzAccount` en volg de aanwijzingen op het scherm.

> [!NOTE]
> Als uw organisatie meer dan één abonnement heeft, moet u mogelijk de argumenten SubscriptionId en Tenant opgeven. Meer informatie vindt u in de [documentatie van Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

1. Down load de map *scripts* van de [Azure remote rendering GithHub-opslag plaats](https://github.com/Azure/azure-remote-rendering).

## <a name="configuration-file"></a>Configuratiebestand

Naast de bestanden `.ps1` `arrconfig.json` die u nodig hebt om het volgende in te vullen:

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
> Zorg ervoor dat backslashes in het pad naar de LocalAssetDirectoryPath juist worden door gebruik te maken van\\\\dubbele backslashes: "" en slashes gebruiken "/" in alle andere paden zoals inputFolderPath en inputAssetPath.

### <a name="accountsettings"></a>accountSettings

Zie `arrAccountId` `arrAccountKey` [een Azure remote rendering-account maken](../how-tos/create-an-account.md)voor en.
Zie `region` voor een [lijst met beschik bare regio's](../reference/regions.md).

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Deze structuur moet worden ingevuld als u **RenderingSession. ps1**wilt uitvoeren.

- **vmSize:** Hiermee selecteert u de grootte van de virtuele machine. Selecteer *standaard* of *Premium*. Schakel rendering-sessies uit wanneer u deze niet meer nodig hebt.
- **maxLeaseTime:** De duur waarvoor u de virtuele machine wilt leasen. Het wordt afgesloten wanneer de lease verloopt. De lease tijd kan later worden verlengd (zie hieronder).

### <a name="assetconversionsettings"></a>assetConversionSettings

Deze structuur moet worden ingevuld als u **Conversion. ps1**wilt uitvoeren.

Zie [een Azure Storage-account voorbereiden](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)voor meer informatie.

## <a name="script-renderingsessionps1"></a>Script: RenderingSession. ps1

Dit script wordt gebruikt om rendering van sessies te maken, op te vragen en te stoppen.

> [!IMPORTANT]
> Zorg ervoor dat u de *accountSettings* -en *renderingSessionSettings* -secties in arrconfig. json hebt ingevuld.

### <a name="create-a-rendering-session"></a>Een rendering-sessie maken

Normaal gebruik met een volledig ingevuld arrconfig. json:

```PowerShell
.\RenderingSession.ps1
```

Het script roept de [rest API voor sessie beheer](../how-tos/session-rest-api.md) aan om een RENDERING-VM te maken met de opgegeven instellingen. Als dit is gelukt, wordt de *sessie-id*opgehaald. Vervolgens worden de sessie-eigenschappen gecontroleerd totdat de sessie gereed is of er een fout is opgetreden.

Een **alternatief configuratie** bestand gebruiken:

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

U kunt de **afzonderlijke instellingen** van het configuratie bestand overschrijven:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Als u alleen **een sessie zonder polling wilt starten**, kunt u het volgende gebruiken:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

De *sessie-id* die door het script wordt opgehaald, moet worden door gegeven aan de meeste andere sessie opdrachten.

### <a name="retrieve-session-properties"></a>Sessie-eigenschappen ophalen

Als u de eigenschappen van een sessie wilt ophalen, voert u de volgende handelingen uit:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Gebruik `-Poll` om te wachten tot de sessie *gereed* is of er is een fout opgetreden.

### <a name="list-active-sessions"></a>Actieve sessies weer geven

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
> De lease tijd wordt altijd geteld vanaf het moment dat de sessie-VM voor het eerst is gemaakt. Als u de sessie lease met een ander uur wilt uitbreiden, verhoogt u *maxLeaseTime* met één uur.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Script: Conversion. ps1

Dit script wordt gebruikt om invoer modellen te converteren naar de runtime-indeling van de externe rendering van Azure.

> [!IMPORTANT]
> Zorg ervoor dat u de *accountSettings* -en *assetConversionSettings* -secties in arrconfig. json hebt ingevuld.

Het script illustreert de twee opties voor het gebruik van opslag accounts bij de service:

- Opslag account gekoppeld aan Azure remote rendering-account
- Toegang verlenen tot opslag via Shared Access signatures (SAS)

### <a name="linked-storage-account"></a>Gekoppeld opslag account

Wanneer u arrconfig. json volledig hebt ingevuld en een opslag account hebt gekoppeld, kunt u de volgende opdracht gebruiken. Het koppelen van uw opslag account wordt beschreven in [een account maken](../how-tos/create-an-account.md#link-storage-accounts).

Het gebruik van een gekoppeld opslag account is de voorkeurs manier om de conversie service te gebruiken omdat er geen gedeelde toegangs handtekeningen hoeven te worden gegenereerd.

```PowerShell
.\Conversion.ps1
```

1. Upload alle bestanden die zijn opgenomen `assetConversionSettings.modelLocation` in de naar de container invoer-BLOB onder de opgegeven`inputFolderPath`
1. De [model conversie rest API](../how-tos/conversion/conversion-rest-api.md) aanroepen om de [model conversie](../how-tos/conversion/model-conversion.md) te starten
1. De conversie status pollen totdat de conversie is geslaagd of mislukt
1. Uitvoer Details van de geconverteerde bestands locatie (opslag account, uitvoer container en bestandspad in de container)

### <a name="access-to-storage-via-shared-access-signatures"></a>Toegang tot opslag via Shared Access signatures

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Dit is:

1. Upload het lokale bestand van de `assetConversionSettings.localAssetDirectoryPath` naar de BLOB-container voor invoer
1. Een SAS-URI voor de invoer container genereren
1. Een SAS-URI voor de uitvoer container genereren
1. De [model conversie rest API](../how-tos/conversion/conversion-rest-api.md) aanroepen om de [model conversie](../how-tos/conversion/model-conversion.md) te starten
1. De conversie status pollen totdat de conversie is geslaagd of mislukt
1. Uitvoer Details van de geconverteerde bestands locatie (opslag account, uitvoer container en bestandspad in de container)
1. Uitvoer van een SAS-URI naar het geconverteerde model in de uitvoer BLOB-container

### <a name="additional-command-line-options"></a>Aanvullende opdracht regel opties

Een **alternatief configuratie** bestand gebruiken:

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Als u **model conversie alleen wilt starten zonder polling**, kunt u het volgende gebruiken:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

U kunt de **afzonderlijke instellingen** van het configuratie bestand overschrijven met de volgende opdracht regel opties:

* **Id:** ConversionId gebruikt met GetConversionStatus
* **ArrAccountId:** ArrAccountId van accountSettings
* **ArrAccountKey:** overschrijven voor ArrAccountKey van accountSettings
* **Regio:** overschrijven voor regio accountSettings
* **ResourceGroup:** overschrijven voor ResourceGroup van assetConversionSettings
* **StorageAccountName:** overschrijven voor StorageAccountName van assetConversionSettings
* **BlobInputContainerName:** overschrijven voor BlobInputContainer van assetConversionSettings
* **LocalAssetDirectoryPath:** overschrijven voor LocalAssetDirectoryPath van assetConversionSettings
* **InputAssetPath:** overschrijven voor InputAssetPath van assetConversionSettings
* **BlobOutputContainerName:** overschrijven voor BlobOutputContainerName van assetConversionSettings
* **OutputFolderPath:** overschrijven voor de OutputFolderPath van assetConversionSettings
* **OutputAssetFileName:** overschrijven voor OutputAssetFileName van assetConversionSettings

U kunt bijvoorbeeld een aantal van de opgegeven opties combi neren:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>De afzonderlijke conversie fasen uitvoeren

Als u afzonderlijke stappen van het proces wilt uitvoeren, kunt u het volgende gebruiken:

Alleen gegevens uit de opgegeven LocalAssetDirectoryPath uploaden

```PowerShell
.\Conversion.ps1 -Upload
```

Het conversie proces van een model dat al is geüpload naar de Blob-opslag starten (niet uitvoeren, de conversie status niet controleren) het script retourneert een *conversionId*.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

En u kunt de conversie status van deze conversie ophalen met behulp van:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Gebruik `-Poll` om te wachten tot de conversie is voltooid of er is een fout opgetreden.

## <a name="next-steps"></a>Volgende stappen

- [Quick Start: een model met Unit renderen](../quickstarts/render-model.md)
- [Snelstartgids: een model voor rendering converteren](../quickstarts/convert-model.md)
- [Model conversie](../how-tos/conversion/model-conversion.md)
