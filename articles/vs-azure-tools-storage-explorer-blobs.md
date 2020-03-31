---
title: Azure Blob-opslagbronnen beheren met Storage Explorer | Microsoft Documenten
description: Azure Blob-containers en blobs beheren met Storage Explorer
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 56c20c995a95058b5039b7268c7b7b1426e900fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244392"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Azure Blob-opslagbronnen beheren met Storage Explorer

## <a name="overview"></a>Overzicht

[Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) is een service voor het opslaan van grote hoeveelheden ongestructureerde gegevens, zoals tekst of binaire gegevens, die overal ter wereld toegankelijk zijn via HTTP of HTTPS.
U kunt Blob Storage gebruiken om gegevens openbaar te maken of om toepassingsgegevens privé op te slaan. In dit artikel leert u hoe u Storage Explorer gebruiken om met blobcontainers en blobs te werken.

## <a name="prerequisites"></a>Vereisten

U moet het volgende doen om de stappen in dit artikel uit te voeren:

* [Storage Explorer downloaden en installeren](https://www.storageexplorer.com)
* [Verbinding maken met een Azure-opslagaccount of -service](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Een blob-container maken

Alle blobs moeten zich in een blobcontainer bevinden, wat gewoon een logische groepering van blobs is. Een account kan een onbeperkt aantal containers bevatten en elke container kan een onbeperkt aantal blobs opslaan.

In de volgende stappen u zien hoe u een blobcontainer maakt in Storage Explorer.

1. Open Storage Explorer.
2. Vouw in het linkerdeelvenster het opslagaccount uit waarin u de blobcontainer wilt maken.
3. Klik met de rechtermuisknop op **Blob-containers**en selecteer - in het contextmenu - **Blobcontainer maken**.

   ![Het contextmenu Blobcontainers maken][0]
4. Er verschijnt een tekstvak onder de map **BlobContainers.** Voer een naam in voor de blobcontainer. Zie [Een container maken](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) voor informatie over regels en beperkingen voor het benoemen van blobcontainers.

   ![Tekstvak Blobcontainers maken][1]
5. Druk op **Enter** om de blobcontainer te maken of op **Esc** om te annuleren. Zodra de blobcontainer is gemaakt, wordt deze weergegeven onder de map **Blobcontainers** voor het geselecteerde opslagaccount.

   ![Blobcontainer gemaakt][2]

## <a name="view-a-blob-containers-contents"></a>De inhoud van een blobcontainer weergeven

Blob-containers bevatten blobs en mappen (die ook blobs kunnen bevatten).

In de volgende stappen u zien hoe u de inhoud van een blobcontainer in Storage Explorer weergeven:

1. Open Storage Explorer.
2. Vouw in het linkerdeelvenster het opslagaccount uit met de blobcontainer die u wilt weergeven.
3. Vouw de **Blob-containers**van het opslagaccount uit.
4. Klik met de rechtermuisknop op de blobcontainer die u wilt weergeven en selecteer - in het contextmenu - **Blob Container Editor openen**.
   U ook dubbelklikken op de blobcontainer die u wilt weergeven.

   ![Contextmenu blobcontainereditor openen][19]
5. In het hoofdvenster wordt de inhoud van de blobcontainer weergegeven.

   ![Blob-containereditor][3]

## <a name="delete-a-blob-container"></a>Een blobcontainer verwijderen

Blob-containers kunnen eenvoudig worden gemaakt en verwijderd als dat nodig is. (Als u wilt zien hoe u afzonderlijke blobs verwijdert, verwijst u naar de [sectie, Blobs beheren in een blobcontainer](#managing-blobs-in-a-blob-container).)

In de volgende stappen u zien hoe u een blobcontainer in Storage Explorer verwijdert:

1. Open Storage Explorer.
2. Vouw in het linkerdeelvenster het opslagaccount uit met de blobcontainer die u wilt weergeven.
3. Vouw de **Blob-containers**van het opslagaccount uit.
4. Klik met de rechtermuisknop op de blobcontainer die u wilt verwijderen en selecteer - in het contextmenu - **Verwijderen**.
   U ook op **Delete** drukken om de momenteel geselecteerde blobcontainer te verwijderen.

   ![Contextmenu blobcontainer verwijderen][4]
5. Selecteer **Ja** in het bevestigingsvenster.

   ![Bevestiging blobcontainer verwijderen][5]

## <a name="copy-a-blob-container"></a>Een blobcontainer kopiëren

Met Storage Explorer u een blobcontainer naar het klembord kopiëren en die blobcontainer vervolgens in een ander opslagaccount plakken. (Als u wilt zien hoe u afzonderlijke blobs kopieert, verwijst u naar de [sectie, Blobs beheren in een blobcontainer](#managing-blobs-in-a-blob-container).)

In de volgende stappen u zien hoe u een blobcontainer van het ene opslagaccount naar het andere kopieert.

1. Open Storage Explorer.
2. Vouw in het linkerdeelvenster het opslagaccount uit met de blobcontainer die u wilt kopiëren.
3. Vouw de **Blob-containers**van het opslagaccount uit.
4. Klik met de rechtermuisknop op de blobcontainer die u wilt kopiëren en selecteer - in het contextmenu - **BlobContainer kopiëren**.

   ![Contextmenu blobcontainer kopiëren][6]
5. Klik met de rechtermuisknop op het gewenste 'doel'-opslagaccount waarop u de blobcontainer wilt plakken en selecteer - in het contextmenu - **BlobContainer plakken**.

   ![Contextmenu blobcontainer plakken][7]

## <a name="get-the-sas-for-a-blob-container"></a>De SAS ophalen voor een blobcontainer

Een [SAS (Shared Access Signature; handtekening voor gedeelde toegang)](storage/common/storage-dotnet-shared-access-signature-part-1.md) biedt gedelegeerde toegang tot bronnen in uw opslagaccount.
Dit betekent dat u een client gedurende de opgegeven periode een beperkte set machtigingen kunt verlenen voor objecten in uw opslagaccount, zonder dat u hiervoor de toegangssleutels voor het account hoeft te delen.

In de volgende stappen wordt uitgelegd hoe u een SAS maakt voor een blobcontainer:

1. Open Storage Explorer.
2. Vouw in het linkerdeelvenster het opslagaccount uit met de blobcontainer waarvoor u een SAS wilt krijgen.
3. Vouw de **Blob-containers**van het opslagaccount uit.
4. Klik met de rechtermuisknop op de gewenste blobcontainer en selecteer - in het contextmenu - **Gedeelde toegangshandtekening ophalen**.

   ![SAS-contextmenu opgebruiken][8]
5. Geef in het dialoogvenster **Handtekening voor gedeelde toegang** waarden op voor het beleid, de begin- en eindtijd, de tijdzone en de toegangsniveaus die u wilt gebruiken voor de resource.

   ![SAS-opties bekijken][9]
6. Als u alle SAS-opties hebt opgegeven, selecteert u **Maken**.
7. In een tweede dialoogvenster **gedeelde toegangshandtekening** wordt vervolgens weergegeven met daarin de blobcontainer, samen met de URL en QueryStrings die u gebruiken om toegang te krijgen tot de opslagbron.
   Selecteer **Kopiëren** naast de URL die u naar het klembord wilt kopiëren.

   ![SAS-URL's kopiëren][10]
8. Als u klaar bent, selecteert u **Sluiten**.

## <a name="manage-access-policies-for-a-blob-container"></a>Toegangsbeleid voor een blobcontainer beheren

In de volgende stappen u zien hoe u toegangsbeleid voor een blobcontainer beheert (toevoegen en verwijderen):

1. Open Storage Explorer.
2. Vouw in het linkerdeelvenster het opslagaccount uit met de blobcontainer waarvan u het toegangsbeleid wilt beheren.
3. Vouw de **Blob-containers**van het opslagaccount uit.
4. Selecteer de gewenste blobcontainer en selecteer - in het contextmenu - **Toegangsbeleid beheren**.

   ![Contextmenu Toegangsbeleid beheren][11]
5. In het dialoogvenster **Toegangsbeleid** worden alle toegangsbeleidsregels weergegeven die al zijn gemaakt voor de geselecteerde blobcontainer.

   ![Opties voor toegangsbeleid][12]
6. Volg hieronder de stappen voor de beleidsbeheertaak die u wilt uitvoeren:

   * **Een nieuw toegangsbeleid toevoegen**: selecteer **Toevoegen**. Als de bewerking is voltooid, ziet u het dialoogvenster **Toegangsbeleid** met het zojuist toegevoegde toegangsbeleid (met de standaardinstellingen).
   * **Een toegangsbeleid bewerken** - De gewenste bewerkingen aanbrengen en **selecteer Opslaan**.
   * **Een toegangsbeleid verwijderen**: selecteer **Verwijderen** naast het toegangsbeleid dat u wilt verwijderen.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Het openbare toegangsniveau instellen voor een blobcontainer

Standaard is elke blobcontainer ingesteld op 'Geen openbare toegang'.

In de volgende stappen u zien hoe u een openbaar toegangsniveau voor een blobcontainer opgeeft.

1. Open Storage Explorer.
2. Vouw in het linkerdeelvenster het opslagaccount uit met de blobcontainer waarvan u het toegangsbeleid wilt beheren.
3. Vouw de **Blob-containers**van het opslagaccount uit.
4. Selecteer de gewenste blobcontainer en selecteer - in het contextmenu - **Openbaar toegangsniveau instellen**.

   ![Contextmenu op het niveau van openbare toegang instellen][13]
5. Geef in het dialoogvenster **Openbaar toegangsniveau voor containers instellen** het gewenste toegangsniveau op.

   ![Opties op het niveau van openbare toegang instellen][14]
6. Selecteer **Toepassen**.

## <a name="managing-blobs-in-a-blob-container"></a>Blobs beheren in een blobcontainer

Zodra u een blobcontainer hebt gemaakt, u een blob uploaden naar die blobcontainer, een blob downloaden naar uw lokale computer, een blob openen op uw lokale computer en nog veel meer.

In de volgende stappen u zien hoe u de blobs (en mappen) in een blobcontainer beheert.

1. Open Storage Explorer.
2. Vouw in het linkerdeelvenster het opslagaccount uit met de blobcontainer die u wilt beheren.
3. Vouw de **Blob-containers**van het opslagaccount uit.
4. Dubbelklik op de blobcontainer die u wilt weergeven.
5. In het hoofdvenster wordt de inhoud van de blobcontainer weergegeven.

   ![Blobcontainer weergeven][3]
6. In het hoofdvenster wordt de inhoud van de blobcontainer weergegeven.
7. Volg hieronder de stappen voor de bewerking die u wilt uitvoeren:

   * **Bestanden uploaden naar een blobcontainer**

     1. Selecteer **Uploaden** op de werkbalk van het hoofdvenster en selecteer vervolgens **Bestanden uploaden** in de vervolgkeuzelijst.

        ![Menu voor Bestanden uploaden][15]
     2. Selecteer in het dialoogvenster **Bestanden uploaden** de knop met de weglatingstekens (**... **) rechts van het tekstvak **Bestanden** om de bestanden te selecteren die u wilt uploaden.

        ![Opties voor bestanden uploaden][16]
     3. Geef het type **Blob-type**op . Zie [Een container maken](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) voor meer informatie.
     4. Geef eventueel een doelmap op waarin het geselecteerde bestand(en) wordt geüpload. Als de doelmap niet bestaat, wordt deze gemaakt.
     5. Selecteer **Uploaden**.
   * **Een map uploaden naar een blobcontainer**

     1. Selecteer **Uploaden** op de werkbalk van het hoofdvenster en selecteer vervolgens **Map uploaden** in de vervolgkeuzelijst.

        ![Menu voor uploaden van map][17]
     2. Selecteer in het dialoogvenster **Map uploaden** de knop met de weglatingstekens (**... **) rechts van het tekstvak **Map** om de map te selecteren waarvan u de inhoud wilt uploaden.

        ![Opties voor mappen uploaden][18]
     3. Geef het type **Blob-type**op . Zie [Een container maken](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) voor meer informatie.
     4. Geef desgewenst een doelmap op waarin de inhoud van de geselecteerde map moet worden geüpload. Als de doelmap niet bestaat, wordt deze gemaakt.
     5. Selecteer **Uploaden**.
   * **Een blob downloaden naar uw lokale computer**

     1. Selecteer de blob die u wilt downloaden.
     2. Selecteer **Downloaden** op de werkbalk van het hoofdvenster.
     3. Geef in het dialoogvenster Opgeven waar u **het gedownloade blobdialoogvenster wilt opslaan,** de locatie op waar de blob wilt worden gedownload en de naam die u deze wilt geven.  
     4. Selecteer **Opslaan**.
   * **Een blob openen op uw lokale computer**

     1. Selecteer de blob die u wilt openen.
     2. Selecteer **Openen** op de werkbalk van het hoofdvenster.
     3. De blob wordt gedownload en geopend met behulp van de toepassing die is gekoppeld aan het onderliggende bestandstype van de blob.
   * **Een blob naar het klembord kopiëren**

     1. Selecteer de blob die u wilt kopiëren.
     2. Selecteer **Kopiëren** op de werkbalk van het hoofdvenster.
     3. Navigeer in het linkerdeelvenster naar een andere blobcontainer en dubbelklik erop om deze in het hoofdvenster weer te geven.
     4. Selecteer **Plakken** op de werkbalk van het hoofdvenster om een kopie van de blob te maken.
   * **Een blob verwijderen**

     1. Selecteer de blob die u wilt verwijderen.
     2. Selecteer **Verwijderen** op de werkbalk van het hoofdvenster.
     3. Selecteer **Ja** in het bevestigingsvenster.

## <a name="next-steps"></a>Volgende stappen

* De [meest recente releaseopmerkingen en video's van Storage Explorer](https://www.storageexplorer.com) bekijken.
* Meer informatie lezen over [het maken van toepassingen met behulp van blobs, tabellen, wachtrijen en bestanden van Azure](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png
