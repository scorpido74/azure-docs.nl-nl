---
title: Aan de slag met Storage Explorer | Microsoft Docs
description: Azure storage-resources beheren met Storage Explorer
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: 43e76b9331eb8dbe95265810b9191a10d4caee08
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272029"
---
# <a name="get-started-with-storage-explorer"></a>Aan de slag met Storage Explorer

## <a name="overview"></a>Overzicht

Microsoft Azure Storage Explorer is een zelfstandige app waarmee u eenvoudig kunt werken met Azure Storage gegevens in Windows, macOS en Linux. In dit artikel leert u verschillende manieren om verbinding te maken met uw Azure Storage-accounts en hoe u deze kunt beheren.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Vereisten

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Storage Explorer wordt ondersteund in de volgende versies van Windows:

* Windows 10 (aanbevolen)
* Windows 8
* Windows 7

Voor alle versies van Windows is .NET Framework 4.6.2 of later vereist.

# <a name="macostabmacos"></a>[MacOS](#tab/macos)

Storage Explorer wordt ondersteund op de volgende versies van macOS:

* macOS 10,12 ' Sierra ' en latere versies

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Storage Explorer is beschikbaar in de [snap Store](https://snapcraft.io/storage-explorer) voor de meest voorkomende distributies van Linux en is de aanbevolen installatie methode. Met de Storage Explorer-uitlijning worden automatisch alle afhankelijkheden en updates geïnstalleerd wanneer nieuwe versies worden gepubliceerd in de snap Store.

Voor een lijst met ondersteunde distributies gaat u naar de [pagina gesnapte installatie](https://snapcraft.io/docs/installing-snapd).

Voor Storage Explorer is het gebruik van een wachtwoord beheerder vereist. u moet mogelijk hand matig verbinding maken voordat Storage Explorer goed werkt. U kunt Storage Explorer verbinding maken met het wachtwoord beheer van uw systeem door de volgende opdracht uit te voeren:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer is ook beschikbaar als een. tar. gz-down load, maar u moet afhankelijkheden hand matig installeren. De installatie van tar. gz wordt ondersteund in de volgende distributies van Linux:

* Ubuntu 18,04 x64
* Ubuntu 16.04 x64
* Ubuntu 14,04 x64

De installatie van het tar. gz-netwerk werkt mogelijk met andere distributies, maar alleen deze vermelde items worden officieel ondersteund.

Raadpleeg de [hand leiding voor probleem oplossing](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)voor meer informatie over het installeren van Storage Explorer op Linux.

---

## <a name="download-and-install"></a>Downloaden en installeren

[Storage Explorer downloaden en installeren](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Verbinding maken met een opslagaccount of -service

Storage Explorer biedt verschillende manieren om verbinding te maken met opslagaccounts. In het algemeen kunt u het volgende doen:

* [Meld u aan bij Azure om toegang te krijgen tot uw abonnementen en hun resources](#sign-in-to-azure)
* [Een specifieke opslag-of CosmosDB-resource koppelen](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

> [!NOTE]
> Om toegang te krijgen tot resources Nadat u zich hebt aangemeld, heeft Storage Explorer zowel beheer (Azure Resource Manager) als gegevenslaag machtigingen nodig. Dit betekent dat u de machtigingen voor Azure Active Directory (Azure AD) nodig hebt, waarmee u toegang krijgt tot uw opslag account, de containers in het account en de gegevens in de containers. Als u alleen machtigingen hebt op de gegevenslaag, kunt u overwegen om [een resource toe te voegen via Azure AD](#add-a-resource-via-azure-ad). Raadpleeg de [hand leiding voor probleem oplossing](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)voor meer informatie over de specifieke machtigingen die Storage Explorer vereist.

1. In Storage Explorer selecteert u **accounts beheren** om naar het account beheer paneel te gaan.

    ![Accounts beheren][1]

2. In het linkerdeel venster worden nu alle Azure-accounts weer gegeven waarop u bent aangemeld. Selecteer **een account toevoegen**om verbinding te maken met een ander account.

3. Als u zich wilt aanmelden bij een nationale Cloud of een Azure Stack, selecteert u de vervolg keuzelijst van de **Azure-omgeving** om de Azure-Cloud te kiezen die u wilt gebruiken. Nadat u uw omgeving hebt gekozen, selecteert u de knop **Aanmelden** . Zie [Connect Storage Explorer to a Azure stack Subscription](/azure-stack/user/azure-stack-storage-connect-se)(Engelstalig) voor meer informatie.

    ![Aanmeldings optie][2]

4. Nadat u zich hebt aangemeld met een Azure-account, worden het account en de Azure-abonnementen die aan dat account zijn gekoppeld, toegevoegd aan het linkerdeel venster. Selecteer de Azure-abonnementen waarmee u wilt werken en selecteer vervolgens **Toepassen**. (Door **alle abonnementen** te selecteren, schakelt u de selectie tussen alle of geen van de vermelde Azure-abonnementen.)

    ![Selecteer Azure-abonnementen][3]

    In het linkerdeelvenster worden de opslagaccounts weergegeven die aan de geselecteerde Azure-abonnementen zijn gekoppeld.

    ![Geselecteerde Azure-abonnementen][4]

### <a name="attach-a-specific-resource"></a>Een specifieke resource koppelen

Er zijn meerdere manieren om te koppelen aan een resource in Storage Explorer:

* [Voeg een resource toe via Azure AD](#add-a-resource-via-azure-ad). Als u alleen machtigingen hebt op de gegevenslaag, gebruikt u deze optie om een BLOB-container of een Azure Data Lake Storage Gen2 Blob Storage-container toe te voegen.
* [Gebruik een Connection String](#use-a-connection-string). Gebruik deze optie als u een connection string hebt voor een opslag account. Storage Explorer ondersteunt zowel sleutel-als [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) -verbindings reeksen.
* [Gebruik een SAS-URI](#use-a-sas-uri). Als u een [SAS-URI](storage/common/storage-dotnet-shared-access-signature-part-1.md) naar een BLOB-container, een bestands share, een wachtrij of een tabel hebt, kunt u deze gebruiken om aan de resource te koppelen. Als u een SAS-URI wilt ophalen, kunt u [Storage Explorer](#generate-a-sas-in-storage-explorer) of de [Azure Portal](https://portal.azure.com)gebruiken.
* [Gebruik een naam en sleutel](#use-a-name-and-key). Als u een van de account sleutels in uw opslag account kent, kunt u deze optie gebruiken om snel verbinding te maken. De sleutels voor uw opslag account bevinden zich in het deel venster **toegangs sleutels** voor opslag accounts in de [Azure Portal](https://portal.azure.com).
* [Koppelen aan een lokale emulator](#attach-to-a-local-emulator). Als u een van de beschik bare Azure Storage-emulators gebruikt, gebruikt u deze optie om eenvoudig verbinding te maken met uw emulator.
* [Verbinding maken met een Azure Cosmos DB-account met behulp van een Connection String](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Gebruik deze optie als u een connection string hebt naar een CosmosDB-exemplaar.
* [Maak verbinding met Azure data Lake Store op basis van de URI](#connect-to-azure-data-lake-store-by-uri). Gebruik deze optie als u een URI hebt die moet worden Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Een resource toevoegen via Azure AD

1. Open het dialoog venster **verbinding maken** door de knop **verbinden** te selecteren in de verticale werk balk aan de linkerkant:

    ![Verbinding maken met de optie Azure Storage][9]

2. Als u dit nog niet hebt gedaan, gebruikt u de optie **een Azure-account toevoegen** om u aan te melden bij het Azure-account dat toegang heeft tot de resource. Nadat u zich hebt aangemeld, keert u terug naar het dialoog venster **verbinding maken** .

3. Selecteer **een resource toevoegen via Azure Active Directory (Azure AD)** en selecteer vervolgens **volgende**.

4. Selecteer het Azure-account en de Tenant die toegang hebben tot de opslag resource die u wilt koppelen. Selecteer **Volgende**.

5. Kies het resource type dat u wilt koppelen en voer vervolgens de gegevens in die nodig zijn om verbinding te maken. De gegevens die u op deze pagina invoert, zijn afhankelijk van het type resource dat u wilt toevoegen. Zorg ervoor dat u het juiste type resource kiest. Nadat u de vereiste gegevens hebt ingevoerd, selecteert u **volgende**.

6. Bekijk de samen vatting van de verbinding en zorg ervoor dat alle informatie juist is. Als dat het geval is, selecteert u **verbinding maken**. Als dat niet het geval is, gebruikt u de knop **terug** om terug te keren naar de vorige pagina's om eventuele onjuiste gegevens op te lossen.

Nadat de verbinding is toegevoegd, gaat de resource structuur automatisch naar het knoop punt dat de verbinding vertegenwoordigt. Als deze niet naar het knoop punt gaat, kijkt u onder **lokale & gekoppelde** > **opslag accounts** >  **(gekoppelde containers) BLOB-**  > **containers**. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [hand leiding](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)voor het oplossen van problemen.

#### <a name="use-a-connection-string"></a>Een verbindingsreeks gebruiken

1. Open het dialoog venster **verbinding maken** door de knop **verbinden** te selecteren in de verticale werk balk aan de linkerkant:

    ![Verbinding maken met de optie Azure Storage][9]

2. Selecteer **een Connection String gebruiken**en selecteer **volgende**.

3. Kies een weergave naam voor de verbinding en voer uw connection string in. Selecteer vervolgens **Volgende**.

4. Bekijk de samen vatting van de verbinding en zorg ervoor dat alle informatie juist is. Als dat het geval is, selecteert u **verbinding maken**. Als dat niet het geval is, gebruikt u de knop **terug** om terug te keren naar de vorige pagina's om eventuele onjuiste gegevens op te lossen.

Nadat de verbinding is toegevoegd, gaat de resource structuur automatisch naar het knoop punt dat de verbinding vertegenwoordigt. Als deze niet naar het knoop punt gaat, kijkt u onder **lokale & gekoppelde** > **opslag accounts**. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [hand leiding](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)voor het oplossen van problemen.

#### <a name="use-a-sas-uri"></a>Een SAS-URI gebruiken

1. Open het dialoog venster **verbinding maken** door de knop **verbinden** te selecteren in de verticale werk balk aan de linkerkant:

    ![Verbinding maken met de optie Azure Storage][9]

2. Selecteer **een SAS-URI (Shared Access Signature) gebruiken**en selecteer **volgende**.

3. Kies een weergave naam voor de verbinding en voer de SAS-URI in. Het service-eind punt voor het type resource dat u wilt koppelen, moet automatisch door voeren worden. Als u een aangepast eind punt gebruikt, is dit mogelijk niet het geval. Selecteer **Volgende**.

4. Bekijk de samen vatting van de verbinding en zorg ervoor dat alle informatie juist is. Als dat het geval is, selecteert u **verbinding maken**. Als dat niet het geval is, gebruikt u de knop **terug** om terug te keren naar de vorige pagina's om eventuele onjuiste gegevens op te lossen.

Nadat de verbinding is toegevoegd, gaat de resource structuur automatisch naar het knoop punt dat de verbinding vertegenwoordigt. Als deze niet naar het knoop punt gaat, kijkt u onder **lokale & gekoppelde** > **opslag accounts** >  **(gekoppelde containers)**  > *het service knooppunt voor het type container dat u hebt gekoppeld*. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [hand leiding](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)voor het oplossen van problemen.

#### <a name="use-a-name-and-key"></a>Een naam en sleutel gebruiken

1. Open het dialoog venster **verbinding maken** door de knop **verbinden** te selecteren in de verticale werk balk aan de linkerkant:

    ![Verbinding maken met de optie Azure Storage][9]

2. Selecteer **een opslag accountnaam en-sleutel gebruiken**en selecteer **volgende**.

3. Kies een weergave naam voor de verbinding.

4. Voer de naam van uw opslag account en een van de bijbehorende toegangs sleutels in.

5. Kies het **opslag domein** dat u wilt gebruiken en selecteer **volgende**.

6. Bekijk de samen vatting van de verbinding en zorg ervoor dat alle informatie juist is. Als dat het geval is, selecteert u **verbinding maken**. Als dat niet het geval is, gebruikt u de knop **terug** om terug te keren naar de vorige pagina's om eventuele onjuiste gegevens op te lossen.

Nadat de verbinding is toegevoegd, gaat de resource structuur automatisch naar het knoop punt dat de verbinding vertegenwoordigt. Als deze niet naar het knoop punt gaat, kijkt u onder **lokale & gekoppelde** > **opslag accounts**. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [hand leiding](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)voor het oplossen van problemen.

#### <a name="attach-to-a-local-emulator"></a>Koppelen aan een lokale emulator

Storage Explorer ondersteunt momenteel twee officiële opslag emulators:
* [Azure Storage-emulator](storage/common/storage-use-emulator.md) (Alleen Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS of Linux)

Als uw emulator op de standaard poorten luistert, kunt u het **emulator-standaard poorten** knooppunt (gevonden onder **lokale & gekoppelde** > **opslag accounts**) gebruiken om snel toegang te krijgen tot uw emulator.

Als u een andere naam voor de verbinding wilt gebruiken of als uw emulator niet wordt uitgevoerd op de standaard poorten, voert u de volgende stappen uit:

1. Start uw emulator. Wanneer u dit doet, noteert u de poorten waarop de emulator luistert voor elk Service type.

   > [!IMPORTANT]
   > De emulator wordt niet automatisch door Storage Explorer gestart. U moet deze hand matig starten.

2. Open het dialoog venster **verbinding maken** door de knop **verbinden** te selecteren in de verticale werk balk aan de linkerkant:

    ![Verbinding maken met de optie Azure Storage][9]

3. Selecteer **koppelen aan een lokale emulator**en selecteer vervolgens **volgende**.

4. Kies een weergave naam voor de verbinding en voer de poorten in waarop uw emulator luistert voor elk Service type. De tekst vakken worden gestart met de standaard poort waarden voor de meeste emulators. Het vak **Bestands poort** is leeg, omdat geen van de officiële emulators de service bestanden ondersteunt. Als de emulator die u gebruikt, ondersteuning biedt voor bestanden, kunt u de poort opgeven die wordt gebruikt. Selecteer vervolgens **Volgende**.

5. Bekijk de samen vatting van de verbinding en zorg ervoor dat alle informatie juist is. Als dat het geval is, selecteert u **verbinding maken**. Als dat niet het geval is, gebruikt u de knop **terug** om terug te keren naar de vorige pagina's om eventuele onjuiste gegevens op te lossen.

Nadat de verbinding is toegevoegd, gaat de resource structuur automatisch naar het knoop punt dat de verbinding vertegenwoordigt. Als deze niet naar het knoop punt gaat, kijkt u onder **lokale & gekoppelde** > **opslag accounts**. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [hand leiding](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)voor het oplossen van problemen.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Verbinding maken met een Azure Cosmos DB-account met behulp van een connection string

Als alternatief voor het beheren van Azure Cosmos DB accounts via een Azure-abonnement kunt u ook verbinding maken met Azure Cosmos DB met behulp van een connection string. Voer de volgende stappen uit om dit te doen:

1. Vouw aan de linkerkant van de resource structuur **lokaal en gekoppeld**uit, klik met de rechter muisknop op **Azure Cosmos DB accounts**en selecteer **verbinding maken met Azure Cosmos DB**.

    ![Verbinding maken met Azure Cosmos DB met een verbindingsreeks][21]

2. Selecteer de Azure Cosmos DB-API, voer de gegevens voor de **verbindings reeks** in en selecteer **OK** om verbinding te maken met het Azure Cosmos DB-account. Zie [de Connection String ophalen](https://docs.microsoft.com/azure/cosmos-db/manage-account)voor informatie over het ophalen van de Connection String.

    ![Verbindingsreeks][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Verbinding maken met Azure Data Lake Store op URI

Als u toegang wilt krijgen tot een bron die zich niet in uw abonnement bevindt, moet u iemand die toegang heeft tot die resource, de resource-URI geven. Nadat u zich hebt aangemeld, kunt u verbinding maken met Data Lake Store met behulp van de URI. Voer de volgende stappen uit om dit te doen:

1. Open Storage Explorer.
2. Vouw **Lokaal en gekoppeld** uit in het linkerdeelvenster.
3. Klik met de rechter muisknop op **Data Lake Store**. Selecteer in het snelmenu **verbinding maken met data Lake Store**.

    ![verbinding maken met het context menu van Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Voer de URI in. Het hulp programma gaat naar de locatie van de URL die u zojuist hebt ingevoerd.

    ![Het dialoog venster verbinding maken met Data Lake Store context](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![verbinding maken met Data Lake Store resultaat](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Een SAS in Storage Explorer genereren

### <a name="account-level-sas"></a>SAS op account niveau

1. Klik met de rechter muisknop op het opslag account dat u wilt delen en selecteer vervolgens **Shared Access Signature ophalen**.

    ![Optie SAS-contextmenu ophalen][14]

2. Geef in het dialoog venster **Shared Access Signature genereren** het tijds bestek en de gewenste machtigingen voor het account op en selecteer vervolgens **maken**.

    ![Het dialoog venster SAS ophalen][15]

3. U kunt nu de **verbindings reeks** of de onbewerkte **query teken reeks** naar het klem bord kopiëren.

### <a name="service-level-sas"></a>SAS op service niveau

[Een SAS ophalen voor een BLOB-container in Storage Explorer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Zoeken naar opslagaccounts

Als u een opslag bron wilt zoeken en niet weet waar deze zich bevindt, kunt u het zoekvak boven aan het linkerdeel venster gebruiken om te zoeken naar de resource.

Wanneer u in het zoekvak typt, worden in het linkerdeel venster alle resources weer gegeven die overeenkomen met de zoek waarde die u tot dat punt hebt ingevoerd. Bijvoorbeeld, wordt een zoek opdracht voor **eind punten** weer gegeven in de volgende scherm afbeelding:

![Zoeken naar Storage-account][23]

> [!NOTE]
> Als u uw zoek opdracht wilt versnellen, gebruikt u het deel venster account beheer om alle abonnementen te deselecteren die niet het item bevatten dat u zoekt. U kunt ook met de rechter muisknop op een knoop punt klikken en **hier zoeken** selecteren om te beginnen met zoeken vanaf een specifiek knoop punt.
>
>

## <a name="next-steps"></a>Volgende stappen

* [Azure Blob Storage-resources beheren met Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Azure Cosmos DB beheren in Storage Explorer (preview-versie)](./cosmos-db/storage-explorer.md)
* [Azure Data Lake Store-resources beheren met Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
