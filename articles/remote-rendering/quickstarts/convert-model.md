---
title: Een model converteren
description: Quick Start waarin de conversie stappen voor een aangepast model worden weer gegeven.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: 7ba8d201c29b5e3835fec52d8c479a388ca07f71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81313000"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Snelstartgids: een model voor rendering converteren

In [Quick Start: een model samen stellen met Unit](render-model.md), u hebt geleerd hoe u het voor beeld-voorbeeld project gebruikt om een ingebouwd model weer te geven. In deze hand leiding wordt uitgelegd hoe u uw eigen modellen kunt converteren.

U leert het volgende:

> [!div class="checklist"]
>
> * Een Azure Blob-opslag account instellen voor invoer en uitvoer
> * Een 3D-model uploaden en converteren voor gebruik met de externe rendering van Azure
> * Het geconverteerde 3D-model in een toepassing voor rendering toevoegen

## <a name="prerequisites"></a>Vereisten

* Volledige [Snelstartgids: een model renderen met Unity](render-model.md)
* Azure PowerShell installeren [(documentatie)](https://docs.microsoft.com/powershell/azure/)
  * Open een Power shell met beheerders rechten
  * Uitvoeringsrun`Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Overzicht

De renderer op de server kan niet rechtstreeks werken met broncode model indelingen, zoals FBX of GLTF. In plaats daarvan moet het model een eigen binaire indeling hebben.
De conversie service verbruikt modellen van Azure Blob-opslag en schrijft geconverteerde modellen terug naar een Azure Blob Storage-container.

U hebt de volgende zaken nodig:

* Een Azure-abonnement
* Een ' StorageV2-account in uw abonnement
* Een BLOB storage-container voor uw invoer model
* Een BLOB storage-container voor uw uitvoer gegevens
* Een model dat u wilt converteren, zie voor [beelden van modellen](../samples/sample-model.md)
  * Bekijk de lijst met [ondersteunde bron indelingen](../how-tos/conversion/model-conversion.md#supported-source-formats)
  * Als u het voorbeeld script wilt gebruiken, moet u ervoor zorgen dat u een uitvoermap voorbereidt die het model en alle externe afhankelijkheden bevat (zoals externe bitmappatronen of geometrie)

## <a name="azure-setup"></a>Installatie van Azure

Als u nog geen account hebt, gaat u naar, [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/)klikt u op de optie gratis account en volgt u de instructies.

Als u een Azure-account hebt, gaat [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home)u naar.

### <a name="storage-account-creation"></a>Opslag account maken

Als u Blob Storage wilt maken, hebt u eerst een opslag account nodig.
Als u er een wilt maken, klikt u op de knop een resource maken:

![Azure-resource toevoegen](media/azure-add-a-resource.png)

Kies in het nieuwe scherm **opslag** aan de linkerkant en klik vervolgens op **opslag account-blob, bestand, tabel, wachtrij** in de volgende kolom:

![Azure-opslag toevoegen](media/azure-add-storage.png)

Als u op deze knop klikt, wordt het volgende scherm weer gegeven met de opslag eigenschappen die moeten worden ingevuld:

![Installatie van Azure](media/azure-setup1.png)

Vul het formulier op de volgende manier in:

* Maak een nieuwe resource groep via de koppeling onder de vervolg keuzelijst en geef deze **ARR_Tutorial**
* Voer hier een unieke naam in voor de naam van het **opslag account**. **Deze naam moet globaal uniek zijn**, anders wordt er een prompt weer gegeven waarin wordt gemeld dat de naam gereed is. In het bereik van deze Snelstartgids noemen we het **arrtutorialstorage**. Daarom moet u deze vervangen door uw naam voor elke vind plaats in deze Snelstartgids.
* Selecteer een **locatie** dicht bij u in de buurt. In het ideale geval gebruikt u dezelfde locatie als voor het instellen van de rendering in de Andere Snelstartgids.
* **Prestaties** ingesteld op Standard
* **Account type** ingesteld op ' StorageV2 (algemeen gebruik v2) '
* **Replicatie** is ingesteld op geografisch redundante opslag met lees toegang (RA-GRS)
* De **toegangs laag** is ingesteld op ' hot '

Geen van de eigenschappen in andere tabbladen moet worden gewijzigd, dus u kunt door gaan met **' controleren en maken '** en de stappen volgen om de installatie te volt ooien.

Op de website wordt u nu geïnformeerd over de voortgang van uw implementatie en rapporten "uw implementatie is voltooid". Klik op de knop **' go to resource '** voor de volgende stappen:

![Azure Storage is gemaakt](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>Blob-opslag maken

Nu hebben we twee BLOB-containers nodig, één voor invoer en één voor uitvoer.

Klik op de knop **' go to resource '** hierboven op een pagina met een deel venster aan de linkerkant die een lijst menu bevat. Klik in de lijst onder de categorie **' BLOB service '** op de knop **' containers '** :

![Azure-containers toevoegen](./media/azure-add-containers.png)

Druk op de knop **+ container** om de container voor de **invoer** -Blob-opslag te maken.
Gebruik de volgende instellingen wanneer u deze maakt:
  
* Name = arrinput
* Openbaar toegangs niveau = privé

Nadat de container is gemaakt, klikt u nogmaals op **+ container** en herhaalt u deze instellingen voor de **uitvoer** container:

* Name = arroutput
* Openbaar toegangs niveau = privé

U hebt nu twee Blob Storage-containers:

![Blob Storage instellen](./media/blob-setup.png)

## <a name="run-the-conversion"></a>De conversie uitvoeren

We bieden een hulp script voor het maken van de service voor de conversie van activa. Deze bevindt zich in de map *scripts* en heet **conversie. ps1**.

Met name dit script

1. Hiermee worden alle bestanden in een bepaalde map geüpload van de lokale schijf naar de invoer opslag container
1. roept de [activa conversie rest API](../how-tos/conversion/conversion-rest-api.md) op die de gegevens ophaalt uit de invoer opslag container en start een conversie, waardoor een conversie-id wordt geretourneerd
1. de API van de conversie status controleren met de opgehaalde conversie-id totdat het conversie proces wordt beëindigd met slagen of mislukken
1. Hiermee wordt een koppeling naar het geconverteerde element in de uitvoer opslag opgehaald

Het script leest de configuratie van het bestand *Scripts\arrconfig.json*. Open dat JSON-bestand in een tekst editor.

```json
{
    "accountSettings": {
        "arrAccountId": "8*******-****-****-****-*********d7e",
        "arrAccountKey": "R***************************************l04=",
        "region": "<your-region>"
    },
    "renderingSessionSettings": {
        "vmSize": "standard",
        "maxLeaseTime": "1:00:00"
    },
    "assetConversionSettings": {
        "localAssetDirectoryPath": "D:\\tmp\\robot",
        "resourceGroup": "ARR_Tutorial",
        "storageAccountName": "arrexamplestorage",
        "blobInputContainerName": "arrinput",
        "inputFolderPath": "robotConversion",
        "inputAssetPath": "robot.fbx",
        "blobOutputContainerName": "arroutput",
        "outputFolderPath":"converted/robot",
        "outputAssetFileName": "robot.arrAsset"
    }
}
```

De configuratie binnen de **accountSettings** -groep (account-ID en-sleutel) moet worden ingevuld op basis van de referenties in de [Snelstartgids een model samen stellen met unitruimte](render-model.md).

Zorg ervoor dat u in de **assetConversionSettings** -groep **resourceGroup**, **blobInputContainerName**en **blobOutputContainerName** , zoals hierboven weer gegeven, wijzigt.
Houd er rekening mee dat de waarde **arrtutorialstorage** moet worden vervangen door de unieke naam die u hebt gekozen tijdens het maken van het opslag account.

Wijzig **localAssetDirectoryPath** zodat deze verwijst naar de map op de schijf die het model bevat dat u wilt converteren. Wees voorzichtig met het weglaten van backslashes\\("") in het pad met behulp van\\\\dubbele backslashes ("").

Alle gegevens van het pad dat is opgegeven in **localAssetDirectoryPath** , worden geüpload naar de **blobInputContainerName** -BLOB-container onder een subpad dat is opgegeven door **inputFolderPath**. In de voorbeeld configuratie boven de inhoud van de map "D:\\tmp\\-robot" wordt dus geüpload naar de BLOB-container "arrinput" van het opslag account "arrtutorialstorage" onder het pad "robotConversion". Bestaande bestanden worden overschreven.

Wijzig **inputAssetPath** in het pad van het model dat moet worden geconverteerd-het pad is relatief ten opzichte van localAssetDirectoryPath. Gebruik "/" in plaats van\\"" als padscheidingsteken. Gebruik ' robot. fbx ' voor een bestand ' robot. fbx ' dat zich direct\\in\\' D: tmp-robot ' bevindt.

Zodra het model is geconverteerd, wordt het teruggeschreven naar de opslag container die is opgegeven door **blobOutputContainerName**. U kunt een subpad opgeven door de optionele **outputFolderPath**op te geven. In het bovenstaande voor beeld wordt het resulterende robot. arrAsset gekopieerd naar de uitvoer BLOB-container onder geconverteerd/robot.

De configuratie-instelling **outputAssetFileName** bepaalt de naam van het geconverteerde activum. de para meter is optioneel en de uitvoer bestandsnaam wordt afgeleid van de naam van het invoer bestand. 

Open een Power shell, Controleer of u de *Azure PowerShell* hebt geïnstalleerd zoals vermeld in de [vereisten](#prerequisites). Meld u vervolgens aan bij uw abonnement met de volgende opdracht en volg de instructies op het scherm:

```PowerShell
Connect-AzAccount
```

> [!NOTE]
> Als uw organisatie meer dan één abonnement heeft, moet u mogelijk de argumenten SubscriptionId en Tenant opgeven. Meer informatie vindt u in de [documentatie van Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

Ga naar de `azure-remote-rendering\Scripts` map en voer het conversie script uit:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Dit ziet er ongeveer als volgt uit ![: Conversion. ps1](./media/successful-conversion.png)

Het conversie script genereert een *SAS-URI (Shared Access Signature)* voor het geconverteerde model. U kunt deze URI nu als de naam van het **model** kopiëren naar de voor beeld-app van Quick Start (Zie [Quick Start: een model met eenheid weer geven](render-model.md)).

![Model vervangen in Unity](./media/replace-model-in-unity.png)

 Het voor beeld moet nu het aangepaste model laden en weer geven.

## <a name="optional-re-creating-a-sas-uri"></a>Optioneel: opnieuw maken van een SAS-URI

De SAS-URI die door het conversie script wordt gemaakt, is slechts 24 uur geldig. Nadat u het model echter hebt verlopen, hoeft u het niet opnieuw te converteren. In plaats daarvan kunt u in de portal een nieuwe SAS maken, zoals wordt beschreven in de volgende stappen:

1. Ga naar [Azure Portal](https://www.portal.azure.com)
1. Klik op uw **opslag account** resource: ![toegang tot hand tekening](./media/portal-storage-accounts.png)
1. Klik in het volgende scherm op **opslag Verkenner** in het linkerdeel venster en zoek uw uitvoer model (*. arrAsset* -bestand) in de *arroutput* Blob Storage-container. Klik met de rechter muisknop op het bestand en selecteer **Shared Access Signature ophalen** in het context ![menu: toegang tot hand tekening](./media/portal-storage-explorer.png)
1. Er wordt een nieuw scherm geopend waarin u een verval datum kunt selecteren. Druk op **maken**en kopieer de URI die in het volgende dialoog venster wordt weer gegeven. Deze nieuwe URI vervangt de tijdelijke URI die het script heeft gemaakt.

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes kent, kunt u de zelf studies bekijken om meer inzicht te krijgen.

Als u meer wilt weten over model conversie, raadpleegt u [de model conversie rest API](../how-tos/conversion/conversion-rest-api.md).

> [!div class="nextstepaction"]
> [Zelf studie: een geheel nieuw eenheids project instellen](../tutorials/unity/project-setup.md)
