---
title: 'Quickstart: een blob maken met Azure Portal'
titleSuffix: Azure Storage
description: In deze snelstart gebruikt u Azure Portal in object-opslag (Blob). Vervolgens gebruikt u Azure Portal om een blob in Azure Storage te uploaden, een blob te downloaden en de blobs in een container te vermelden.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 04/16/2020
ms.author: tamram
ms.openlocfilehash: f2e18b060aabcb849fb8e17722c530d199ebdbb8
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067742"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Snelstart: Blobs uploaden, downloaden en vermelden met Azure Portal

In deze snelstartgids leert u hoe u [Azure Portal](https://portal.azure.com/) gebruikt om een container te maken in Azure Storage, en om blok-blobs in deze container te uploaden en te downloaden.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Een container maken

Als u een container wilt maken in Azure Portal, volgt u deze stappen:

1. Navigeer naar het nieuwe opslagaccount in Azure Portal.
2. Schuif in het linkermenu voor het opslagaccount naar de sectie **Blob service**. Selecteer vervolgens **Containers**.
3. Selecteer de knop **+ Container**.
4. Typ een naam voor de nieuwe container. De containernaam mag alleen kleine letters bevatten, moet beginnen met een letter of cijfer en mag alleen letters, cijfers en het streepje (-) bevatten. Zie [Naamgeving van en verwijzen naar containers, blobs en metagegevens](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de namen van containers en blobs.
5. Stel het niveau van openbare toegang tot de container in. Het standaardniveau is **Persoonlijk (geen anonieme toegang)**.
6. Selecteer **OK** om de container te maken.

    ![Schermopname van het maken van een container in Azure Portal](media/storage-quickstart-blobs-portal/create-container.png)

## <a name="upload-a-block-blob"></a>Een blok-blob uploaden

Blok-blobs bestaan uit blokken met gegevens die worden samengevoegd tot een blob. In de meeste gebruiksscenario's voor Blob-opslag worden blok-blobs gebruikt. Blok-blobs zijn zeer geschikt om tekst en binaire gegevens op te slaan in de cloud, zoals bestanden, afbeeldingen en video's. Deze snelstartgids laat zien hoe u kunt werken met blok-blobs.

Als u een blok-blob wilt uploaden naar de nieuwe container in Azure Portal, volgt u deze stappen:

1. Navigeer in Azure Portal naar de container die u in de vorige sectie hebt gemaakt.
1. Selecteer de container om een lijst weer te geven met de blobs die deze bevat. Deze container is nieuw en bevat dus nog geen blobs.
1. Selecteer de knop **Uploaden** om de blade voor uploaden te openen en blader door het lokale bestandssysteem om een bestand te vinden dat u wilt uploaden als een blok-blob. U kunt de sectie **Geavanceerd** eventueel uitvouwen om andere instellingen voor de uploadbewerking te configureren.

    ![Schermopname van het uploaden van een blob vanaf de lokale schijf](media/storage-quickstart-blobs-portal/upload-blob.png)

1. Selecteer de knop **Uploaden** om de blob te uploaden.
1. U kunt op deze manier zoveel blobs uploaden als u wilt. De nieuwe blobs worden nu weergegeven in de container.

## <a name="download-a-block-blob"></a>Een blok-blob downloaden

U kunt een blok-blob downloaden om deze weer te geven in de browser of op te slaan op het lokale bestandssysteem. Als u een blok-blob wilt downloaden, volgt u deze stappen:

1. Navigeer naar de lijst met blobs die u in de vorige sectie hebt geüpload.
1. Klik met de rechtermuisknop op de blob die u wilt downloaden en selecteer **Downloaden**.

    ![Schermopname van het downloaden van een blob](media/storage-quickstart-blobs-portal/download-blob.png)

## <a name="delete-a-block-blob"></a>Een blok-blob verwijderen

U kunt een blok-blob downloaden om deze weer te geven in de browser of op te slaan op het lokale bestandssysteem. Als u een blok-blob wilt downloaden, volgt u deze stappen:

1. Navigeer naar de lijst met blobs die u in de vorige sectie hebt geüpload.
1. Selecteer de blobs die u wilt verwijderen en selecteer **Verwijderen** in de bovenste actiebalk.

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources wilt verwijderen die u in deze snelstartgids hebt gemaakt, verwijdert u gewoon de container. Alle blobs in de container worden dan ook verwijderd.

Ga als volgt te werk om de container te verwijderen:

1. Navigeer in Azure Portal naar de lijst met containers in uw opslagaccount.
1. Selecteer de container die u wilt verwijderen.
1. Klik op de knop **Meer** (**... **) en selecteer **Verwijderen**.
1. Bevestig dat u de container wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u bestanden overbrengt tussen een lokale schijf en Azure Blob-opslag met Azure Portal. Voor meer informatie over het werken met Blob-opslag, gaat u naar de instructies voor Blob-opslag.

> [!div class="nextstepaction"]
> [Instructies voor bewerkingen in Blob-opslag](storage-dotnet-how-to-use-blobs.md)
