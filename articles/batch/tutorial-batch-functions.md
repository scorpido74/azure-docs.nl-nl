---
title: Een batch-taak activeren met behulp van Azure Functions
description: 'Zelf studie: OCR Toep assen op gescande documenten die worden toegevoegd aan een opslag-BLOB'
services: batch
author: ju-shim
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: 6e3cdb6c7e2774eeb29df6986088f822cbb894cf
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029221"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Zelf studie: een batch-taak activeren met behulp van Azure Functions

In deze zelf studie leert u hoe u een batch-taak kunt activeren met behulp van Azure Functions. We gaan een voor beeld bekijken van de documenten die aan een Azure Storage BLOB-container worden toegevoegd, bevatten optische teken herkenning (OCR) die via Azure Batch wordt toegepast. Om de OCR-verwerking te stroom lijnen, zullen we een Azure-functie configureren waarmee een batch-OCR-taak wordt uitgevoerd telkens wanneer een bestand wordt toegevoegd aan de BLOB-container.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een Azure Batch-account en een gekoppeld Azure Storage-account. Zie [een batch-account maken](quick-create-portal.md#create-a-batch-account) voor meer informatie over het maken en koppelen van accounts.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure-opslagverkenner](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure Portal](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Maak een batch-pool en een batch-taak met behulp van Batch Explorer

In deze sectie gebruikt u Batch Explorer om de batch-pool en batch-taak te maken waarmee OCR-taken worden uitgevoerd. 

### <a name="create-a-pool"></a>Pool maken

1. Meld u aan bij Batch Explorer met uw Azure-referenties.
1. Maak een pool door **Pools** te selecteren op de balk aan de linkerkant en klik vervolgens op de knop **toevoegen** boven het zoek formulier. 
    1. Kies een ID en weergave naam. We gebruiken `ocr-pool` voor dit voor beeld.
    1. Stel het schaal type in op **vaste grootte**en stel het toegewezen aantal knoop punten in op 3.
    1. Selecteer **Ubuntu 18,04-LTS** als besturings systeem.
    1. Kies `Standard_f2s_v2` als de grootte van de virtuele machine.
    1. Schakel de begin taak in en voeg de opdracht `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`toe. Zorg ervoor dat u de gebruikers identiteit instelt als **standaard gebruiker van de taak (beheerder)** , zodat Start taken opdrachten kunnen bevatten met `sudo`.
    1. Selecteer **OK**.
### <a name="create-a-job"></a>Een taak maken

1. Maak een taak in de pool door **taken** te selecteren op de balk aan de linkerkant en vervolgens op de knop **toevoegen** boven het zoek formulier. 
    1. Kies een ID en weergave naam. We gebruiken `ocr-job` voor dit voor beeld.
    1. Stel de groep in op `ocr-pool`of de naam die u voor uw pool hebt gekozen.
    1. Selecteer **OK**.


## <a name="create-blob-containers"></a>BLOB-containers maken

Hier maakt u BLOB-containers waarmee de invoer-en uitvoer bestanden voor de OCR batch-taak worden opgeslagen.

1. Meld u aan bij Storage Explorer met uw Azure-referenties.
1. Met het opslag account dat is gekoppeld aan uw batch-account, maakt u twee BLOB-containers (één voor invoer bestanden, één voor uitvoer bestanden) door de stappen te volgen in [een BLOB-container maken](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

In dit voor beeld heeft de invoer container de naam `input` en is de plaats waar alle documenten zonder OCR eerst worden geüpload voor verwerking. De uitvoer container heet `output` en is waar de batch-taak verwerkte documenten schrijft met OCR.  
    * In dit voor beeld noemen we onze invoer container `input`en onze uitvoer container `output`.  
    * De invoer container is waar alle documenten zonder OCR eerst worden geüpload.  
    * De uitvoer container is waar de batch-taak documenten met OCR schrijft.  

Maak een gedeelde toegangs handtekening voor uw uitvoer container in Storage Explorer. Hiertoe klikt u met de rechter muisknop op de uitvoer container en selecteert u **Shared Access Signature ophalen...** . Controleer onder **machtigingen**de optie **schrijven**. Er zijn geen andere machtigingen nodig.  

## <a name="create-an-azure-function"></a>Een Azure-functie maken

In deze sectie maakt u de Azure-functie waarmee de OCR batch-taak wordt geactiveerd wanneer een bestand wordt geüpload naar de invoer container.

1. Volg de stappen in [een functie maken die wordt geactiveerd door Azure Blob-opslag](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) om een functie te maken.
    1. Wanneer u wordt gevraagd om een opslag account, gebruikt u hetzelfde opslag account dat u hebt gekoppeld aan uw batch-account.
    1. Kies voor **runtime stack**.net. We schrijven onze functie in om C# gebruik te maken van de batch .NET SDK.
1. Wanneer de functie voor BLOB-activering is gemaakt, gebruikt u de [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) en [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) van github in de functie.
    * `run.csx` wordt uitgevoerd wanneer een nieuwe BLOB wordt toegevoegd aan de container van de invoer-blob.
    * `function.proj` geeft een lijst van de externe bibliotheken in uw functie code, bijvoorbeeld de batch .NET SDK.
1. Wijzig de waarden van de tijdelijke aanduidingen van de variabelen in de `Run()` functie van het `run.csx`-bestand om uw batch-en opslag referenties weer te geven. U vindt uw batch-en Storage-account referenties in de Azure Portal in de sectie **sleutels** van uw batch-account.
    * Haal de referenties voor uw batch-en opslag account op in de Azure Portal in de sectie **sleutels** van uw batch-account. 

## <a name="trigger-the-function-and-retrieve-results"></a>De functie activeren en resultaten ophalen

Upload een of meer van de gescande bestanden uit de [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) Directory op github naar de invoer container. Controleer Batch Explorer om te bevestigen dat een taak wordt toegevoegd aan `ocr-pool` voor elk bestand. Na enkele seconden wordt het bestand met OCR toegepast toegevoegd aan de uitvoer container. Het bestand is vervolgens zichtbaar en kan worden opgehaald op Storage Explorer.

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
