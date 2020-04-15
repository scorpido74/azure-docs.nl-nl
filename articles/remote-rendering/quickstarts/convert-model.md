---
title: Een model converteren
description: Snelstart met de conversiestappen voor een aangepast model.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: 7ba8d201c29b5e3835fec52d8c479a388ca07f71
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313000"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Snelstart: een model converteren voor rendering

In [Quickstart: Een model renderen met Unity,](render-model.md)heb je geleerd hoe je het Unity-voorbeeldproject gebruiken om een ingebouwd model weer te geven. Deze handleiding laat zien hoe u uw eigen modellen converteren.

U leert het volgende:

> [!div class="checklist"]
>
> * Een Azure blob-opslagaccount instellen voor invoer en uitvoer
> * Een 3D-model uploaden en converteren voor gebruik met Azure Remote Rendering
> * Het geconverteerde 3D-model opnemen in een toepassing voor rendering

## <a name="prerequisites"></a>Vereisten

* Snelstart [voltooien: een model weergeven met Unity](render-model.md)
* Azure PowerShell [installeren (documentatie)](https://docs.microsoft.com/powershell/azure/)
  * Een PowerShell openen met beheerdersrechten
  * Uitvoeren:`Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Overzicht

De renderer op de server kan niet direct werken met bronmodelformaten zoals FBX of GLTF. In plaats daarvan, het vereist het model te worden in een eigen binaire formaat.
De conversieservice verbruikt modellen uit Azure blob-opslag en schrijft geconverteerde modellen terug naar een meegeleverde Azure blob-opslagcontainer.

U hebt de volgende zaken nodig:

* Een Azure-abonnement
* Een 'StorageV2'-account in uw abonnement
* Een blobopslagcontainer voor uw invoermodel
* Een blobopslagcontainer voor uw uitvoergegevens
* Een model om te converteren, zie [voorbeeldmodellen](../samples/sample-model.md)
  * Bekijk de lijst [met ondersteunde bronindelingen](../how-tos/conversion/model-conversion.md#supported-source-formats)
  * Als u het voorbeeldconversiescript wilt gebruiken, moet u een invoermap voorbereiden die het model en alle externe afhankelijkheden bevat (zoals externe structuren of geometrie)

## <a name="azure-setup"></a>Azure-installatie

Als je nog geen account hebt, ga dan naar [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/), klik op de gratis account optie, en volg de instructies.

Zodra u een Azure-account [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home)hebt, gaat u naar .

### <a name="storage-account-creation"></a>Aanmaak van opslagaccount

Als u blob-opslag wilt maken, hebt u eerst een opslagaccount nodig.
Als u er een wilt maken, klikt u op de knop Een resource maken:

![Azure - bron toevoegen](media/azure-add-a-resource.png)

Kies in het nieuwe scherm **Opslag** aan de linkerkant en vervolgens **Opslagaccount - blob, bestand, tabel, wachtrij** in de volgende kolom:

![Azure - opslag toevoegen](media/azure-add-storage.png)

Als u op deze knop klikt, wordt het volgende scherm weergegeven met opslageigenschappen die u moet invullen:

![Azure-installatie](media/azure-setup1.png)

Vul het formulier als volgt in:

* Maak een nieuwe resourcegroep via de link onder het vervolgkeuzevak en geef deze **ARR_Tutorial**
* Voer hier een unieke naam in voor de **naam van het opslagaccount.** **Deze naam moet wereldwijd uniek zijn,** anders zal er een prompt zijn die u informeert dat de naam klaar is gegeven. In het toepassingsgebied van deze quickstart noemen we het **arrtutorialstorage.** Dienovereenkomstig moet u deze vervangen door uw naam voor elke gebeurtenis in deze quickstart.
* Selecteer een **locatie** in uw buurt. Gebruik idealiter dezelfde locatie als gebruikt voor het instellen van de rendering in de andere quickstart.
* **Prestaties** ingesteld op 'Standaard'
* **Accountsoort** ingesteld op 'StorageV2 (general purpose v2)'
* **Replicatie** ingesteld op 'Read-access geo-redundantstorage (RA-GRS)'
* **Toegangslaag** ingesteld op 'Hot'

Geen van de eigenschappen in andere tabbladen hoeft te worden gewijzigd, dus u doorgaan met **'Controleren + maken'** en vervolgens de stappen volgen om de installatie te voltooien.

De website informeert u nu over de voortgang van uw implementatie en rapporteert uiteindelijk "Uw implementatie is voltooid". Klik op de knop **'Ga naar bron'** voor de volgende stappen:

![Azure Storage-creatie voltooid](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>Blob-opslagmaken

Vervolgens hebben we twee blob containers, een voor input en een voor output.

Via de knop **'Ga naar bron'** hierboven, ga je naar een pagina met een paneel aan de linkerkant dat een lijstmenu bevat. Klik in die lijst onder de categorie **'Blob-service'** op de knop **'Containers':**

![Azure - Containers toevoegen](./media/azure-add-containers.png)

Druk op de knop **'+ container'** om de **opslagcontainer voor invoerblobs** te maken.
Gebruik de volgende instellingen bij het maken van het:
  
* Naam = arrinput
* Openbaar toegangsniveau = Privé

Nadat de container is gemaakt, klikt u nogmaals op **+ Container** en herhaalt u met de volgende instellingen voor de **uitvoercontainer:**

* Naam = arroutput
* Openbaar toegangsniveau = Privé

U moet nu twee blobopslagcontainers hebben:

![Blob-opslaginstellingen](./media/blob-setup.png)

## <a name="run-the-conversion"></a>De conversie uitvoeren

Om het eenvoudiger te maken om de asset conversie service aan te roepen, bieden we een hulpprogramma script. Het bevindt zich in de map *Scripts* en heet **Conversion.ps1**.

In het bijzonder is dit script

1. uploadt alle bestanden in een bepaalde map van lokale schijf naar de invoeropslagcontainer
1. roept de [REST-API voor assetconversie](../how-tos/conversion/conversion-rest-api.md) aan die de gegevens uit de invoeropslagcontainer ophaalt en een conversie start die een conversie-id retourneert
1. poll de conversiestatus API met de opgehaalde conversie-id totdat het conversieproces eindigt met succes of fout
1. hiermee wordt een koppeling naar het geconverteerde element in de uitvoeropslag opgehaald

Het script leest de configuratie van het bestand *Scripts\arrconfig.json*. Open dat JSON-bestand in een teksteditor.

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

De configuratie binnen de **groep accountInstellingen** (account-ID en sleutel) moet analoog worden ingevuld met de referenties in het [renderen van een model met Unity quickstart](render-model.md).

Zorg ervoor dat u **resourceGroup,** **blobInputContainerName**en **blobOutputContainerName** zoals hierboven wordt weergegeven, in de groep **assetConversionSettings** wijzigt.
Houd er rekening mee dat de waarde **arrtutorialstorage** moet worden vervangen door de unieke naam die u hebt gekozen tijdens het aanmaken van opslagaccount.

Wijzig **localAssetDirectoryPath** om de map op uw schijf aan te wijzen die het model bevat dat u wilt converteren. Wees voorzichtig om goed te\\ontsnappen backslashes (" ")\\\\in het pad met behulp van dubbele backslashes (" ").

Alle gegevens van het pad in **localAssetDirectoryPath** worden geüpload naar de **blobInvoerContainerName** onder een subpad dat wordt gegeven door **inputFolderPath**. Dus in het voorbeeld configuratie boven de\\inhoud\\van de "D: tmp robot" directory zal worden geüpload naar de blob container "arrinput" van de opslag account "arrtutorialstorage" onder het pad "robotConversion". Reeds bestaande bestanden worden overschreven.

**InvoerAssetPath** wijzigen in het pad van het model dat moet worden geconverteerd - het pad is relatief ten opzichte van localAssetDirectoryPath. Gebruik "/" in\\plaats van " " als padscheidingsteken. Dus voor een "robot.fbx" bestand dat zich\\direct\\in "D: tmp robot" gebruik maken van "robot.fbx".

Zodra het model is geconverteerd, wordt het teruggeschreven naar de opslagcontainer die wordt gegeven door **blobOutputContainerName**. Een subpad kan worden opgegeven door het optionele **uitvoerMapPath**op te geven . In het bovenstaande voorbeeld wordt de resulterende "robot.arrAsset" gekopieerd naar de uitvoerblobcontainer onder "geconverteerd/robot".

De **uitvoeroutput outputAssetFileName** van config bepaalt de naam van het geconverteerde element : de parameter is optioneel en de uitvoerbestandsnaam wordt afgeleid uit de invoerbestandsnaam anders. 

Open een PowerShell, zorg ervoor dat u de *Azure PowerShell* hebt geïnstalleerd zoals vermeld in de [vereisten.](#prerequisites) Meld je dan aan bij je abonnement met de volgende opdracht en volg de aanwijzingen op het scherm:

```PowerShell
Connect-AzAccount
```

> [!NOTE]
> Als uw organisatie meer dan één abonnement heeft, moet u mogelijk de argumenten SubscriptionId en Tenant opgeven. Meer informatie vindt u in de [connect-azaccount-documentatie.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

Wijzig de `azure-remote-rendering\Scripts` map en voer het conversiescript uit:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Je zou zoiets als ![dit moeten zien: Conversion.ps1](./media/successful-conversion.png)

Het conversiescript genereert een *SAS-uri (Shared Access Signature)* voor het geconverteerde model. U deze URI nu als **modelnaam** kopiëren naar de quickstartvoorbeeld-app (zie [Snelstart: Een model weergeven met Unity).](render-model.md)

![Model vervangen in Unity](./media/replace-model-in-unity.png)

 Het voorbeeld moet nu laden en renderen van uw aangepaste model!

## <a name="optional-re-creating-a-sas-uri"></a>Optioneel: een SAS URI opnieuw maken

De SAS URI die door het conversiescript is gemaakt, is slechts 24 uur geldig. Nadat het echter is verlopen, hoeft u uw model niet opnieuw om te zetten. In plaats daarvan u een nieuwe SAS maken in de portal zoals beschreven in de volgende stappen:

1. Ga naar [Azure Portal](https://www.portal.azure.com)
1. Klik op uw **opslagaccountbron:** ![Signature Access](./media/portal-storage-accounts.png)
1. Klik in het volgende scherm op **Storage explorer** in het linkerdeelvenster en zoek het uitvoermodel *(arrAsset-bestand)* in de *opslagcontainer arroutputblob.* Klik met de rechtermuisknop op het bestand en selecteer ![ **Gedeelde toegangshandtekening ophalen** in het contextmenu: Handtekeningtoegang](./media/portal-storage-explorer.png)
1. Er wordt een nieuw scherm geopend waar u een vervaldatum selecteren. Druk op **Maken**en kopieer de URI die in het volgende dialoogvenster wordt weergegeven. Deze nieuwe URI vervangt de tijdelijke URI die het script heeft gemaakt.

## <a name="next-steps"></a>Volgende stappen

Nu je de basis kent, bekijk onze tutorials om meer diepgaande kennis op te doen.

Als u de details van modelconversie wilt leren, raadpleegt u [de API VOOR de restvan het modelconversie.](../how-tos/conversion/conversion-rest-api.md)

> [!div class="nextstepaction"]
> [Zelfstudie: Een Unity-project vanaf nul instellen](../tutorials/unity/project-setup.md)
