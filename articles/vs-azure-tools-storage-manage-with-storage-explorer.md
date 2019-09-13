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
ms.openlocfilehash: c4aad2f2f5bca25ead03518b2de9ac9315172052
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934751"
---
# <a name="get-started-with-storage-explorer"></a>Aan de slag met Storage Explorer

## <a name="overview"></a>Overzicht

Microsoft Azure Storage Explorer is een zelfstandige app waarmee u eenvoudig met Azure Storage gegevens kunt werken in Windows, macOS en Linux. In dit artikel leert u verschillende manieren om verbinding te maken met uw Azure Storage-accounts en hoe u deze kunt beheren.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Vereisten

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Storage Explorer wordt ondersteund in de volgende versies van Windows:

* Windows 10 (aanbevolen)
* Windows 8
* Windows 7

.NET Framework 4.6.2 of hoger is vereist voor alle versies van Windows.

# <a name="macostabmacos"></a>[MacOS](#tab/macos)

Storage Explorer wordt ondersteund op de volgende versies van macOS:

* macOS 10,12 ' Sierra ' en latere versies

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Storage Explorer is beschikbaar in de [snap Store](https://snapcraft.io/storage-explorer) voor de meest voorkomende distributies van Linux en is de aanbevolen installatie methode. Met de Storage Explorer-snap worden automatisch alle afhankelijkheden en updates geïnstalleerd, omdat nieuwe versies worden gepubliceerd in de snap Store.

Ga voor een lijst met ondersteunde distributies naar de [pagina gesnapte installatie](https://snapcraft.io/docs/installing-snapd).

Voor Storage Explorer is het gebruik van een wachtwoord beheerder vereist, dat mogelijk hand matig moet worden aangesloten voordat Storage Explorer goed werkt. U kunt Storage Explorer verbinding maken met wachtwoord beheer van uw systeem met de volgende opdracht:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer is ook beschikbaar als een. tar. gz-down load, maar u moet afhankelijkheden hand matig installeren. De installatie van tar. gz wordt ondersteund in de volgende distributies van Linux:

* Ubuntu 18,04 x64
* Ubuntu 16.04 x64
* Ubuntu 14,04 x64

De installatie van. tar. gz kan worden uitgevoerd op andere distributies, maar alleen de hierboven genoemde items worden officieel ondersteund.

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
> Als u toegang wilt krijgen tot bronnen na het aanmelden, moet u voor Storage Explorer zowel beheer (ARM) als gegevenslaag machtigingen hebben. Dit betekent dat u Azure AD-machtigingen nodig hebt waarmee u toegang krijgt tot uw opslag account, de containers in het account en de gegevens in de containers. Als u alleen machtigingen hebt op de gegevenslaag, kunt u het beste [koppelen met Azure AD](#add-a-resource-via-azure-ad)gebruiken. Raadpleeg de [hand leiding voor probleem oplossing](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues)voor meer informatie over de exacte machtigingen Storage Explorer vereist.

1. In Storage Explorer selecteert u **accounts beheren** om naar het **account beheer paneel**te gaan.

    ![Accounts beheren][1]

2. In het linkerdeel venster worden nu alle Azure-accounts weer gegeven waarop u bent aangemeld. Selecteer **een account toevoegen** om verbinding te maken met een ander account

3. Als u zich wilt aanmelden bij een nationale Cloud of een Azure Stack, klikt u op de vervolg keuzelijst van de **Azure-omgeving** om te selecteren welke Azure-Cloud u wilt gebruiken. Wanneer u uw omgeving hebt gekozen, klikt u op de knop **aanmelden...** . Zie [Connect Storage Explorer to a Azure stack Subscription](/azure-stack/user/azure-stack-storage-connect-se)(Engelstalig) voor meer informatie.

    ![Optie voor aanmelden][2]

4. Nadat u zich hebt aangemeld met een Azure-account, worden het account en de Azure-abonnementen die aan dat account zijn gekoppeld, toegevoegd aan het linkerdeel venster. Selecteer de Azure-abonnementen waarmee u wilt werken en selecteer vervolgens **Toep assen** ( **alle abonnementen selecteren:** wissel knop selecteren alle of geen van de vermelde Azure-abonnementen).

    ![Selecteer Azure-abonnementen][3]

    In het linkerdeelvenster worden de opslagaccounts weergegeven die aan de geselecteerde Azure-abonnementen zijn gekoppeld.

    ![Geselecteerde Azure-abonnementen][4]

### <a name="attach-a-specific-resource"></a>Een specifieke resource koppelen

U kunt koppelen aan een resource in Storage Explorer met behulp van verschillende opties:

* [Een resource toevoegen via Azure AD](#add-a-resource-via-azure-ad): Als u alleen machtigingen hebt op de gegevenslaag, kunt u deze optie gebruiken om een BLOB-container of een ADLS Gen2 BLOB-container toe te voegen.
* [Een Connection String gebruiken](#use-a-connection-string): Als u een connection string hebt voor een opslag account. Storage Explorer ondersteunt zowel sleutel-als [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) -verbindings reeksen.
* [Een SAS-URI gebruiken](#use-a-sas-uri): Als u een [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) -URI hebt naar een BLOB-container, een bestands share, een wachtrij of een tabel. Als u een SAS-URI wilt ophalen, kunt u [Storage Explorer](#generate-a-sas-in-storage-explorer) of de [Azure Portal](https://portal.azure.com)gebruiken.
* [Een naam en sleutel gebruiken](#use-a-name-and-key): Als u een van de account sleutels in uw opslag account kent, kunt u deze optie gebruiken om snel verbinding te maken. De sleutels voor uw opslag account bevinden zich in het deel venster **toegangs sleutels** voor opslag accounts op het [Azure Portal](https://portal.azure.com).
* [Koppelen aan een lokale emulator](#attach-to-a-local-emulator): Als u een van de beschik bare Azure Storage-emulators gebruikt, gebruikt u deze optie om eenvoudig verbinding te maken met uw emulator.
* [Verbinding maken met een Azure Cosmos DB-account met behulp van een Connection String](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Als u een connection string hebt naar een CosmosDB-exemplaar.
* [Verbinding maken met Azure data Lake Store op URI](#connect-to-azure-data-lake-store-by-uri): Als u een URI naar een Azure Data Lake Store hebt.

#### <a name="add-a-resource-via-azure-ad"></a>Een resource toevoegen via Azure AD

1. Open het **dialoog venster verbinden** door te klikken op de **knop verbinding maken** op de verticale werk balk.

    ![Verbinding maken met de optie Azure Storage][9]

2. Als u dit nog niet hebt gedaan, gebruikt u de optie **een Azure-account toevoegen** om u aan te melden bij het Azure-account dat toegang heeft tot de resource. Nadat u zich hebt aangemeld, keert u terug naar het **dialoog venster verbinden**.

3. Selecteer de optie **een resource toevoegen via Azure Active Directory (Azure AD)** en klik op **volgende**.

4. Selecteer het Azure-account en de Tenant die toegang hebben tot de opslag resource die u wilt koppelen. Klik op **Volgende**.

5. Kies het resource type dat u wilt koppelen en voer vervolgens de gegevens in die nodig zijn om verbinding te maken. De invoer op deze pagina wordt gewijzigd, afhankelijk van het type resource dat u wilt toevoegen. Zorg ervoor dat u het juiste type resource kiest. Wanneer u de vereiste gegevens hebt ingevuld, klikt u op **volgende**.

6. Bekijk de samen vatting van de verbinding en zorg ervoor dat alle informatie juist is. Als alle informatie correct is, klikt u op **verbinding maken**. Als dat niet het geval is, keert u terug naar de vorige pagina's met de knop **terug** om onjuiste gegevens te corrigeren.

Zodra de verbinding is toegevoegd, gaat de resource structuur automatisch naar het knoop punt dat de verbinding vertegenwoordigt. U kunt ook zoeken onder **lokale & gekoppeld** → **opslag accounts** → **(gekoppelde containers)** → **BLOB-containers** als dat niet het geval is. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [hand leiding](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) voor het oplossen van problemen.

#### <a name="use-a-connection-string"></a>Een verbindingsreeks gebruiken

1. Open het **dialoog venster verbinden** door te klikken op de **knop verbinding maken** op de verticale werk balk.

    ![Verbinding maken met de optie Azure Storage][9]

2. Selecteer de optie **een Connection String gebruiken** en klik op **volgende**.

3. Kies een weergave naam voor de verbinding en voer uw connection string in. Klik op **Volgende**.

4. Bekijk de samen vatting van de verbinding en zorg ervoor dat alle informatie juist is. Als alle informatie correct is, klikt u op **verbinden**, anders keert u terug naar de vorige pagina's met de knop **terug** om eventuele onjuiste gegevens te corrigeren.

Zodra de verbinding is toegevoegd, gaat de resource structuur automatisch naar het knoop punt dat de verbinding vertegenwoordigt. U kunt ook zoeken onder **lokale & gekoppeld** → **opslag accounts** als dat niet het geval is. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [hand leiding](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) voor het oplossen van problemen.

#### <a name="use-a-sas-uri"></a>Een SAS-URI gebruiken

1. Open het **dialoog venster verbinden** door te klikken op de **knop verbinding maken** op de verticale werk balk.

    ![Verbinding maken met de optie Azure Storage][9]

2. Selecteer de optie **een SAS-URI (Shared Access Signature) gebruiken** en klik op **volgende**.

3. Kies een weergave naam voor de verbinding en voer de SAS-URI in. Het service-eind punt voor het type resource dat u wilt koppelen, moet automatisch door voeren worden. Als u een aangepast eind punt gebruikt, is dit mogelijk niet mogelijk. Klik op **Volgende**.

4. Bekijk de samen vatting van de verbinding en zorg ervoor dat alle informatie juist is. Als alle informatie correct is, klikt u op **verbinden**, anders keert u terug naar de vorige pagina's met de knop **terug** om onjuiste gegevens te corrigeren.

Zodra de verbinding is toegevoegd, gaat de resource structuur automatisch naar het knoop punt dat de verbinding vertegenwoordigt. U kunt ook zoeken onder **lokale & gekoppeld** → **opslag accounts** → **(gekoppelde containers)** → **het service knooppunt voor het type container dat u hebt gekoppeld** als dat niet het geval is. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [hand leiding](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) voor het oplossen van problemen.

#### <a name="use-a-name-and-key"></a>Een naam en sleutel gebruiken

1. Open het **dialoog venster verbinden** door te klikken op de **knop verbinding maken** op de verticale werk balk.

    ![Verbinding maken met de optie Azure Storage][9]

2. Selecteer de optie **een opslag accountnaam en-sleutel gebruiken** en klik op **volgende**.

3. Kies een weergave naam voor de verbinding.

4. Voer de naam van uw opslag account en een van de bijbehorende toegangs sleutels in.

5. Kies het **opslag domein** dat u wilt gebruiken en klik vervolgens op **volgende**.

4. Bekijk de samen vatting van de verbinding en zorg ervoor dat alle informatie juist is. Als alle informatie correct is, klikt u op **verbinden**, anders keert u terug naar de vorige pagina's met de knop **terug** om eventuele onjuiste gegevens te corrigeren.

Zodra de verbinding is toegevoegd, gaat de resource structuur automatisch naar het knoop punt dat de verbinding vertegenwoordigt. U kunt ook zoeken onder **lokale & gekoppeld** → **opslag accounts** als dat niet het geval is. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [hand leiding](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) voor het oplossen van problemen.

#### <a name="attach-to-a-local-emulator"></a>Koppelen aan een lokale emulator

Storage Explorer ondersteunt momenteel twee officiële opslag emulators:
* [Azure-opslag emulator](storage/common/storage-use-emulator.md) (Alleen Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS of Linux)

Als uw emulator luistert naar de standaard poorten, kunt u het knoop punt **emulator-standaard poorten** (gevonden onder **lokale & gekoppeld** → **opslag accounts**) gebruiken om snel toegang te krijgen tot uw emulator.

Als u een andere naam wilt gebruiken voor uw verbinding of als uw emulator niet wordt uitgevoerd op de standaard poorten:

1. Start uw emulator. Wanneer u dit doet, noteert u op welke poorten de emulator luistert voor elk Service type.

   > [!IMPORTANT]
   > De emulator wordt niet automatisch door Storage Explorer gestart. U moet het zelf starten.

2. Open het **dialoog venster verbinden** door te klikken op de **knop verbinding maken** op de verticale werk balk.

    ![Verbinding maken met de optie Azure Storage][9]

3. Selecteer de optie **koppelen aan een lokale emulator** en klik op **volgende**.

4. Kies een weergave naam voor de verbinding en voer in de poorten waarop uw emulator luistert voor elk Service type in. De tekst vakken worden gestart met de standaard poort waarden voor de meeste emulators. De **bestanden poort** is leeg, omdat geen van de officiële emulators de service bestanden ondersteunt. Als de emulator die u gebruikt, ondersteuning biedt voor bestanden, kunt u de poort opgeven die wordt gebruikt. Klik op **Volgende**.

5. Bekijk de samen vatting van de verbinding en zorg ervoor dat alle informatie juist is. Als alle informatie correct is, klikt u op **verbinden**, anders gaat u terug naar de vorige pagina's met de knop **terug** om onjuiste gegevens te corrigeren.

Zodra de verbinding is toegevoegd, gaat de resource structuur automatisch naar het knoop punt dat de verbinding vertegenwoordigt. U kunt ook zoeken onder **lokale & gekoppeld** → **opslag accounts** als dat niet het geval is. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [hand leiding](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) voor het oplossen van problemen.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Verbinding maken met een Azure Cosmos DB-account met behulp van een connection string

Naast het beheren van Azure Cosmos DB accounts via een Azure-abonnement, is het gebruik van een connection string een alternatieve manier om verbinding te maken met een Azure Cosmos DB. Gebruik de volgende stappen om verbinding te maken met behulp van een verbindingsreeks.

1. Ga naar **Lokaal en gekoppeld** in het linkerdeelvenster, klik met de rechtermuisknop op **Azure Cosmos DB-accounts**, en kies **Verbinding maken met Azure Cosmos DB...**

    ![verbinding maken met Azure Cosmos DB door connection string][21]

2. Kies Azure Cosmos DB-API, plak de **verbindings reeks**en klik vervolgens op **OK** om verbinding te maken met Azure Cosmos DB account. Zie [De verbindingsreeks ophalen](https://docs.microsoft.com/azure/cosmos-db/manage-account) voor informatie over het ophalen van de verbindingsreeks.

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Verbinding maken met Azure Data Lake Store op URI

Als u toegang nodig hebt tot een resource die niet in uw abonnement is, hebt u iemand die toegang nodig heeft om u de resource-URI te geven. Nadat u zich hebt aangemeld, kunt u verbinding maken met Data Lake Store met behulp van de URI door de volgende stappen uit te voeren:

1. Open Storage Explorer.
2. Vouw **Lokaal en gekoppeld** uit in het linkerdeelvenster.
3. Klik met uw rechtermuisknop op **Data Lake Store** en selecteer **Verbinding maken met Data Lake Store...** in het contextmenu.

    ![contextmenu Verbinding maken met Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Voer de URI in. Vervolgens navigeert het hulpprogramma naar de locatie van de URL die u zojuist hebt ingevoerd.

    ![dialoogvenster Verbinding maken met Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![resultaat Verbinding maken met Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Een SAS in Storage Explorer genereren

### <a name="account-level-sas"></a>SAS op account niveau

1. Klik met de rechter muisknop op het opslag account dat u wilt delen en selecteer vervolgens **Shared Access Signature ophalen...** .

    ![Optie SAS-contextmenu ophalen][14]

2. Geef in het dialoog venster **Shared Access Signature genereren** het tijds bestek en de gewenste machtigingen voor het account op. Klik op **Create**.

    ![Het dialoog venster SAS ophalen][15]

3. U kunt nu de **verbindings reeks** of de onbewerkte **query teken reeks** naar het klem bord kopiëren.

### <a name="service-level-sas"></a>SAS op service niveau

[Een SAS ophalen voor een BLOB-container in Storage Explorer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Zoeken naar opslagaccounts

Als u een opslag resource wilt zoeken en niet weet waar het is, kunt u het zoekvak boven aan het linkerdeel venster gebruiken om te zoeken naar de resource.

Wanneer u in het zoekvak typt, worden in het linkerdeel venster alle resources weer gegeven die overeenkomen met de zoek waarde die u tot dat punt hebt ingevoerd. Bijvoorbeeld, wordt een zoek opdracht voor **eind punten** weer gegeven in de volgende scherm afbeelding:

![Zoeken naar Storage-account][23]

> [!NOTE]
> Gebruik het **deel venster account beheer** om alle abonnementen te deselecteren die het item dat u zoekt, niet bevatten om de uitvoerings tijd van uw zoek opdracht te verbeteren. U kunt ook met de rechter muisknop op een knoop punt klikken en **hier zoeken** kiezen om te beginnen met zoeken vanaf een specifiek knoop punt.
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
