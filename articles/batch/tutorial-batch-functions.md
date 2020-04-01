---
title: Een batchtaak activeren met Azure-functies
description: 'Zelfstudie : OCR toepassen op gescande documenten terwijl ze worden toegevoegd aan een opslagblob'
author: LauraBrenner
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: a967fdc14b85f294ee11cbcc57a8d2280dba38e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77017187"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Zelfstudie: Een batchtaak activeren met Azure-functies

In deze zelfstudie leert u hoe u een batchtaak activeert met Azure-functies. We doorlopen een voorbeeld waarin documenten die zijn toegevoegd aan een Azure Storage blob-container hebben optische tekenherkenning (OCR) toegepast op hen via Azure Batch. Om de OCR-verwerking te stroomlijnen, configureren we een Azure-functie die een Batch OCR-taak uitvoert telkens wanneer een bestand aan de blobcontainer wordt toegevoegd.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als je nog geen account hebt, maak je een [gratis account](https://azure.microsoft.com/free/) aan voordat je begint.
* Een Azure Batch-account en een gekoppeld Azure Storage-account. Zie [Een batchaccount maken](quick-create-portal.md#create-a-batch-account) voor meer informatie over het maken en koppelen van accounts.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Een batchgroep en batchtaak maken met Batch Explorer

In deze sectie gebruikt u Batch Explorer om de batchgroep en batchtaak te maken waarmee OCR-taken worden uitgevoerd. 

### <a name="create-a-pool"></a>Een groep maken

1. Meld u aan bij Batch Explorer met uw Azure-referenties.
1. Maak een pool door **Pools** aan de linkerkant te selecteren en vervolgens de knop **Toevoegen** boven het zoekformulier. 
    1. Kies een id en weergavenaam. We gebruiken `ocr-pool` voor dit voorbeeld.
    1. Stel het schaaltype in op **Vaste grootte**en stel het aantal speciale knooppunten in op 3.
    1. Selecteer **Ubuntu 18.04-LTS** als besturingssysteem.
    1. Kies `Standard_f2s_v2` als de virtuele machinegrootte.
    1. Schakel de starttaak in `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`en voeg de opdracht toe. Zorg ervoor dat u de gebruikersidentiteit instelt als **Taakstandaardgebruiker (Beheerder),** waarmee taken kunnen worden gestart om opdrachten op te nemen met `sudo`.
    1. Selecteer **OK**.
### <a name="create-a-job"></a>Een taak maken

1. Maak een taak in de groep door **Vacatures** aan de linkerkant te selecteren en vervolgens de knop **Toevoegen** boven het zoekformulier. 
    1. Kies een id en weergavenaam. We gebruiken `ocr-job` voor dit voorbeeld.
    1. Stel het `ocr-pool`zwembad op , of welke naam u voor uw zwembad koos.
    1. Selecteer **OK**.


## <a name="create-blob-containers"></a>Blobcontainers maken

Hier maakt u blobcontainers die uw invoer- en uitvoerbestanden opslaan voor de ocrbatchtaak.

1. Meld u aan bij Storage Explorer met uw Azure-referenties.
1. Maak met het opslagaccount dat is gekoppeld aan uw Batch-account twee blobcontainers (één voor invoerbestanden, één voor uitvoerbestanden) door de stappen te volgen bij [Een blobcontainer maken.](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container)

In dit voorbeeld wordt de `input` invoercontainer benoemd en worden alle documenten zonder OCR in eerste instantie geüpload voor verwerking. De uitvoercontainer `output` heeft de naam en is de plaats waar de batchtaak verwerkte documenten met OCR schrijft.  
    * In dit voorbeeld noemen we onze `input`invoercontainer en `output`onze uitvoercontainer .  
    * De invoercontainer is waar alle documenten zonder OCR in eerste instantie worden geüpload.  
    * De uitvoercontainer is de plaats waar de batchtaak documenten met OCR schrijft.  

Maak een handtekening voor gedeelde toegang voor uw uitvoercontainer in Storage Explorer. Dit doe je door met de rechtermuisknop op de uitvoercontainer te klikken en **Gedeelde toegangshandtekening te selecteren...**. Controleer **Onder Machtigingen**de controle **Schrijven**. Er zijn geen andere machtigingen nodig.  

## <a name="create-an-azure-function"></a>Een Azure-functie maken

In deze sectie maakt u de Azure-functie die de ocrbatch-taak activeert wanneer een bestand wordt geüpload naar uw invoercontainer.

1. Volg de stappen in [Een functie maken die wordt geactiveerd door Azure Blob-opslag](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) om een functie te maken.
    1. Gebruik hetzelfde opslagaccount als u aan uw Batch-account hebt gekoppeld wanneer u om een opslagaccount wordt gevraagd.
    1. Kies voor **runtime stack**.NET. We schrijven onze functie in C# om gebruik te maken van de Batch .NET SDK.
1. Zodra de blob-geactiveerde functie [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) is [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) gemaakt, gebruikt u de functie en van GitHub in de functie.
    * `run.csx`wordt uitgevoerd wanneer een nieuwe blob wordt toegevoegd aan uw invoerblobcontainer.
    * `function.proj`bevat de externe bibliotheken in uw functiecode, bijvoorbeeld de Batch .NET SDK.
1. Wijzig de tijdelijke aanduidingswaarden van `Run()` de `run.csx` variabelen in de functie van het bestand om uw batch- en opslagreferenties weer te geven. U uw batch- en opslagaccountreferenties vinden in de Azure-portal in het gedeelte **Sleutels** van uw Batch-account.
    * Haal uw batch- en opslagaccountreferenties op in de Azure-portal in het gedeelte **Sleutels** van uw Batch-account. 

## <a name="trigger-the-function-and-retrieve-results"></a>De functie activeren en resultaten ophalen

Upload een of alle gescande [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) bestanden uit de directory op GitHub naar uw invoercontainer. Controleer Batch Explorer om te controleren `ocr-pool` of er voor elk bestand een taak wordt toegevoegd. Na enkele seconden wordt het bestand met OCR toegepast toegevoegd aan de uitvoercontainer. Het bestand is dan zichtbaar en kan worden opgehaald in Storage Explorer.

Bovendien u het logboekbestand onder aan het webeditorvenster Azure Functions bekijken, waar u berichten als deze ziet voor elk bestand dat u uploadt naar uw invoercontainer:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Als u de uitvoerbestanden van Storage Explorer naar uw lokale machine wilt downloaden, selecteert u eerst de gewenste bestanden en selecteert u vervolgens het **downloaden** op het bovenste lint. 

> [!TIP]
> De gedownloade bestanden zijn doorzoekbaar als ze worden geopend in een PDF-lezer.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd: 

> [!div class="checklist"]
> * Batch Explorer gebruiken om groepen en taken te maken
> * Storage Explorer gebruiken om blobcontainers en een gedeelde toegangshandtekening (SAS) te maken
> * Een Azure-functie met blobs maken
> * Invoerbestanden uploaden naar Storage
> * Taakuitvoering controleren
> * Uitvoerbestanden ophalen

* Zie [de voorbeelden op GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)voor meer voorbeelden van het gebruik van de .NET API voor het plannen en verwerken van batchworkloads. 

* Zie [de documentatie azure-functies](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings)voor meer Azure-functies die u gebruiken om batchworkloads uit te voeren.
