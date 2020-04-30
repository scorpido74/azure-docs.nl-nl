---
title: Een batch-taak activeren met behulp van Azure Functions
description: 'Zelf studie: OCR Toep assen op gescande documenten die worden toegevoegd aan een opslag-BLOB'
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: 01c3ab167239affa4d7ae94f5649d60072c3c270
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82117162"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Zelf studie: een batch-taak activeren met behulp van Azure Functions

In deze zelf studie leert u hoe u een batch-taak kunt activeren met behulp van Azure Functions. We gaan een voor beeld bekijken van de documenten die aan een Azure Storage BLOB-container worden toegevoegd, bevatten optische teken herkenning (OCR) die via Azure Batch wordt toegepast. Om de OCR-verwerking te stroom lijnen, zullen we een Azure-functie configureren waarmee een batch-OCR-taak wordt uitgevoerd telkens wanneer een bestand wordt toegevoegd aan de BLOB-container.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u er nog geen hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een Azure Batch-account en een gekoppeld Azure Storage-account. Zie [een batch-account maken](quick-create-portal.md#create-a-batch-account) voor meer informatie over het maken en koppelen van accounts.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Opslagverkenner](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Maak een batch-pool en een batch-taak met behulp van Batch Explorer

In deze sectie gebruikt u Batch Explorer om de batch-pool en batch-taak te maken waarmee OCR-taken worden uitgevoerd. 

### <a name="create-a-pool"></a>Een groep maken

1. Meld u aan bij Batch Explorer met uw Azure-referenties.
1. Maak een pool door **Pools** te selecteren op de balk aan de linkerkant en klik vervolgens op de knop **toevoegen** boven het zoek formulier. 
    1. Kies een ID en weergave naam. Voor dit voor `ocr-pool` beeld gebruiken we.
    1. Stel het schaal type in op **vaste grootte**en stel het toegewezen aantal knoop punten in op 3.
    1. Selecteer **Ubuntu 18,04-LTS** als besturings systeem.
    1. Kies `Standard_f2s_v2` als grootte van de virtuele machine.
    1. Schakel de start taak in en voeg de `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`opdracht toe. Zorg ervoor dat u de gebruikers identiteit instelt als **standaard gebruiker voor de taak (beheerder)**, zodat Start taken kunnen worden `sudo`gestart met opdrachten.
    1. Selecteer **OK**.
### <a name="create-a-job"></a>Een taak maken

1. Maak een taak in de pool door **taken** te selecteren op de balk aan de linkerkant en vervolgens op de knop **toevoegen** boven het zoek formulier. 
    1. Kies een ID en weergave naam. Voor dit voor `ocr-job` beeld gebruiken we.
    1. Stel de groep in `ocr-pool`op of welke naam u hebt gekozen voor uw pool.
    1. Selecteer **OK**.


## <a name="create-blob-containers"></a>BLOB-containers maken

Hier maakt u BLOB-containers waarmee de invoer-en uitvoer bestanden voor de OCR batch-taak worden opgeslagen.

1. Meld u aan bij Storage Explorer met uw Azure-referenties.
1. Met het opslag account dat is gekoppeld aan uw batch-account, maakt u twee BLOB-containers (één voor invoer bestanden, één voor uitvoer bestanden) door de stappen te volgen in [een BLOB-container maken](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

In dit voor beeld heeft de invoer container de `input` naam en is de plaats waar alle documenten zonder OCR eerst worden geüpload voor verwerking. De uitvoer container heet `output` en is waar de batch-taak verwerkte documenten schrijft met OCR.  
    * In dit voor beeld noemen we onze invoer container `input`en onze uitvoer container. `output`  
    * De invoer container is waar alle documenten zonder OCR eerst worden geüpload.  
    * De uitvoer container is waar de batch-taak documenten met OCR schrijft.  

Maak een gedeelde toegangs handtekening voor uw uitvoer container in Storage Explorer. Hiertoe klikt u met de rechter muisknop op de uitvoer container en selecteert u **Shared Access Signature ophalen...**. Controleer onder **machtigingen**de optie **schrijven**. Er zijn geen andere machtigingen nodig.  

## <a name="create-an-azure-function"></a>Een Azure-functie maken

In deze sectie maakt u de Azure-functie waarmee de OCR batch-taak wordt geactiveerd wanneer een bestand wordt geüpload naar de invoer container.

1. Volg de stappen in [een functie maken die wordt geactiveerd door Azure Blob-opslag](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) om een functie te maken.
    1. Wanneer u wordt gevraagd om een opslag account, gebruikt u hetzelfde opslag account dat u hebt gekoppeld aan uw batch-account.
    1. Kies voor **runtime stack**.net. We schrijven onze functie in C# om gebruik te maken van de batch .NET SDK.
1. Nadat de functie met Blob-activering is gemaakt, gebruikt u [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) de [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) en van github in de functie.
    * `run.csx`wordt uitgevoerd wanneer een nieuwe BLOB wordt toegevoegd aan de container van de invoer-blob.
    * `function.proj`Hier worden de externe bibliotheken in uw functie code weer gegeven, bijvoorbeeld de batch .NET SDK.
1. Wijzig de waarden van de tijdelijke aanduidingen van `Run()` de variabelen in `run.csx` de functie van het bestand om uw batch-en opslag referenties weer te geven. U vindt uw batch-en Storage-account referenties in de Azure Portal in de sectie **sleutels** van uw batch-account.
    * Haal de referenties voor uw batch-en opslag account op in de Azure Portal in de sectie **sleutels** van uw batch-account. 

## <a name="trigger-the-function-and-retrieve-results"></a>De functie activeren en resultaten ophalen

Upload een of meer van de gescande bestanden [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) vanuit de map op github naar de invoer container. Controleer Batch Explorer om te bevestigen dat een taak wordt toegevoegd `ocr-pool` aan voor elk bestand. Na enkele seconden wordt het bestand met OCR toegepast toegevoegd aan de uitvoer container. Het bestand is vervolgens zichtbaar en kan worden opgehaald op Storage Explorer.

Daarnaast kunt u het logboek bestand bekijken onder aan het venster van de Azure Functions-webeditor, waar u berichten ziet als dit voor elk bestand dat u uploadt naar uw invoer container:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Als u de uitvoer bestanden van Storage Explorer wilt downloaden naar uw lokale computer, selecteert u eerst de gewenste bestanden en selecteert u vervolgens de **down load** op het bovenste lint. 

> [!TIP]
> De gedownloade bestanden zijn doorzoekbaar als ze worden geopend in een PDF-lezer.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd: 

> [!div class="checklist"]
> * Batch Explorer gebruiken om Pools en taken te maken
> * Storage Explorer gebruiken om BLOB-containers en een Shared Access Signature (SAS) te maken
> * Een Azure-functie met Blob-activering maken
> * Invoerbestanden uploaden naar Storage
> * Taakuitvoering controleren
> * Uitvoerbestanden ophalen

* Zie [de voor beelden op github](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)voor meer voor beelden van het gebruik van de .net API om batch-workloads te plannen en te verwerken. 

* Zie [de Azure functions documentatie](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings)voor meer Azure functions triggers die u kunt gebruiken om batch-workloads uit te voeren.
