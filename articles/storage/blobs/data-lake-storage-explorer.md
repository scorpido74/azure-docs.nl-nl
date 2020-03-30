---
title: Azure Storage Explorer gebruiken met Azure Data Lake Storage Gen2
description: Gebruik de Azure Storage Explorer om mappen en lijsten met bestands- en adresmaptoegangsbeheer (ACL) te beheren in opslagaccounts waarop hiërarchische naamruimte (HNS) is ingeschakeld.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: fca9fa8a964c6c9d69ffbb3036bd4774e0d1bd34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255546"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Azure Storage Explorer gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2

In dit artikel ziet u hoe u [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken om mappen, bestanden en machtigingen te maken en te beheren in opslagaccounts waarop hiërarchische naamruimte (HNS) is ingeschakeld.

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslagaccount met hiërarchische naamruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.
> * Azure Storage Explorer geïnstalleerd op uw lokale computer. Zie [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) voor meer informatie over het installeren van Azure Storage Explorer voor Windows, Macintosh of Linux.

## <a name="sign-in-to-storage-explorer"></a>Aanmelden bij Storage Explorer

Wanneer u Azure Storage Explorer de eerste keer start, wordt het venster **Microsoft Azure Storage Explorer - Verbinding maken** weergegeven. Hoewel Storage Explorer verschillende manieren biedt om verbinding te maken met opslagaccounts, wordt momenteel slechts één manier ondersteund voor het beheren van ACL's.

|Taak|Doel|
|---|---|
|Een Azure-account toevoegen | Leidt u door naar de aanmeldingspagina van uw organisatie om u te verifiëren naar Azure. Dit is momenteel de enige ondersteunde verificatiemethode als u ACL's wilt beheren en instellen.|
|Een verbindingsreeks of een SAS-URI (Shared Access Signature) gebruiken | Kan worden gebruikt voor rechtstreekse toegang tot een container of opslagaccount met behulp van een SAS-token of een gedeelde verbindingsreeks. |
|De naam en sleutel van een opslagaccount gebruiken| Gebruik de naam en sleutel van uw opslagaccount om verbinding te maken met Azure Storage.|

Selecteer **Een Azure-account toevoegen** en klik op **Aanmelden..**. Volg de aanwijzingen op het scherm om u aan te melden bij uw Azure-account.

![Het venster Microsoft Azure Storage Explorer - Verbinding maken](media/storage-quickstart-blobs-storage-explorer/connect.png)

Wanneer de verbinding tot stand is gebracht, wordt Azure Storage Explorer geladen en ziet u het tabblad **Explorer**. Deze weergave biedt u inzicht in al uw Azure Storage-accounts, evenals lokale opslag die is geconfigureerd via de [Azure-opslagemulator](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-accounts of [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-omgevingen.

![Het venster Microsoft Azure Storage Explorer - Verbinding maken](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Een container maken

Een container bevat mappen en bestanden. Als u er een wilt maken, vouwt u het opslagaccount uit dat u in de procedurestap hebt gemaakt. Selecteer **Blobcontainers**, klik met de rechtermuisknop en selecteer **Blobcontainer maken**. Voer de naam voor uw container in. Zie de [sectie Een container](storage-quickstart-blobs-dotnet.md#create-a-container) maken voor een lijst met regels en beperkingen voor het benoemen van containers. Druk op **Enter** om de container te maken wanneer u klaar bent. Zodra de container is gemaakt, wordt deze weergegeven onder de map **Blobcontainers** voor het geselecteerde opslagaccount.

![Microsoft Azure Storage Explorer - Een container maken](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Een map maken

Als u een map wilt maken, selecteert u de container die u in de procedurestap hebt gemaakt. Kies op het containerlint de knop **Nieuwe map.** Voer de naam voor uw map in. Druk op **Enter** om de map te maken wanneer u klaar bent. Zodra de map is gemaakt, wordt deze weergegeven in het editorvenster.

![Microsoft Azure Storage Explorer - Een map maken](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Blobs uploaden naar de directory

Kies op het adreslijstlint de knop **Uploaden.** Met deze bewerking kunt u een map of bestand uploaden.

Kies de bestanden of map die u wilt uploaden.

![Microsoft Azure Storage Explorer - een blob uploaden](media/data-lake-storage-explorer/upload-file.png)

Wanneer u **OK** selecteert, worden de geselecteerde bestanden in een wachtrij geplaatst om te worden geüpload. Elk bestand wordt geüpload. Wanneer het uploaden is voltooid, worden de resultaten weergegeven in het venster **Activiteiten**.

## <a name="view-blobs-in-a-directory"></a>Blobs in een directory weergeven

Selecteer in de toepassing **Azure Storage Explorer** een directory in een opslagaccount. In het hoofdvenster ziet u een lijst met de blobs in de geselecteerde directory.

![Microsoft Azure Storage Explorer - lijst met blobs in een directory](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Blobs downloaden

Als u bestanden wilt downloaden met Azure Storage Explorer , selecteert u **Downloaden op** het lint als u bestanden wilt downloaden met Azure **Storage Explorer,** waarbij een bestand is geselecteerd. Er wordt een dialoogvenster geopend waarin u een bestandsnaam kunt invoeren. Selecteer **Opslaan** om het downloaden van een bestand naar de lokale locatie te starten.

## <a name="managing-access"></a>Toegang beheren

U machtigingen instellen aan de basis van uw container. Hiervoor moet u zijn aangemeld bij Azure Storage Explorer met uw individuele account met de rechten om dit te doen (in tegenstelling tot met een verbindingstekenreeks). Klik met de rechtermuisknop op de container en selecteer **Machtigingen beheren**en het dialoogvenster **Machtigingen beheren** weergeven.

![Microsoft Azure Storage Explorer - Toegang tot mappen beheren](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Via het dialoogvenster **Machtiging beheren** kunt u machtigingen beheren voor de eigenaar en de groep eigenaren. Ook kunt u nieuwe gebruikers en groepen toevoegen aan de toegangsbeheerlijst voor wie u vervolgens machtigingen kunt beheren.

Selecteer het veld **Gebruiker of groep toevoegen** om een nieuwe gebruiker of groep toe te voegen aan de toegangsbeheerlijst.

Voer de bijbehorende AAD-vermelding (Azure Active Directory) in die u aan de lijst wilt toevoegen en selecteer vervolgens **Toevoegen**.

De gebruiker of groep wordt nu weergegeven in het veld **Gebruikers en groepen:**, zodat u kunt beginnen met het beheren van hun machtigingen.

> [!NOTE]
> Het is een best practice en het wordt aanbevolen om een beveiligingsgroep in AAD te maken en machtigingen te onderhouden voor de groep in plaats van afzonderlijke gebruikers. Zie [Best practices voor Data Lake Storage Gen2](data-lake-storage-best-practices.md) voor meer informatie over deze aanbeveling en tevens andere best practices.

Er zijn twee machtigingscategorieën die u kunt toewijzen: Toegangs-ACL’s en Standaard-ACL’s.

* **Toegang:** Toegangs-ACL's bepalen de toegang tot een object. Bestanden en mappen hebben beide Toegangs-ACL's.

* **Standaard:** een sjabloon met ACL's die zijn gekoppeld aan een map die de toegangsacl.l.v. onderliggende items bepaalt die onder die map zijn gemaakt. Bestanden hebben geen Standaard-ACL's.

Binnen beide categorieën zijn er drie machtigingen die u vervolgens toewijzen aan bestanden of mappen: **Lezen,** **Schrijven**en **Uitvoeren.**

>[!NOTE]
> Wanneer u hier selecties maakt, worden machtigingen voor een reeds bestaand item in de map niet ingesteld. U moet naar elk afzonderlijk item gaan en de machtigingen handmatig instellen als het bestand al bestaat.

U kunt machtigingen voor afzonderlijke mappen beheren, evenals de afzonderlijke bestanden waarmee voor u een gedetailleerd toegangsbeheer mogelijk wordt. Het proces voor het beheren van machtigingen voor zowel mappen als bestanden is hetzelfde als hierboven beschreven. Klik met de rechtermuisknop op het bestand of map waarvoor u machtigingen wilt beheren en volg hetzelfde proces.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over toegangscontrolelijsten in Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Toegangsbeheer in Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
