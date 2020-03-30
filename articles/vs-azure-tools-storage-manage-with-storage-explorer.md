---
title: Aan de slag met Storage Explorer | Microsoft Documenten
description: Azure-opslagbronnen beheren met Storage Explorer
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279791"
---
# <a name="get-started-with-storage-explorer"></a>Aan de slag met Storage Explorer

## <a name="overview"></a>Overzicht

Microsoft Azure Storage Explorer is een zelfstandige app waarmee u eenvoudig werken met Azure Storage-gegevens op Windows, macOS en Linux. In dit artikel leert u verschillende manieren om verbinding te maken met en uw Azure-opslagaccounts te beheren.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Vereisten

# <a name="windows"></a>[Windows](#tab/windows)

De volgende versies van Windows ondersteunen Storage Explorer:

* Windows 10 (aanbevolen)
* Windows 8
* Windows 7

Voor alle versies van Windows vereist Storage Explorer .NET Framework 4.6.2 of hoger.

# <a name="macos"></a>[Macos](#tab/macos)

De volgende versies van macOS ondersteunen Storage Explorer:

* macOS 10.12 Sierra en latere versies

# <a name="linux"></a>[Linux](#tab/linux)

Storage Explorer is beschikbaar in de [Snap Store](https://snapcraft.io/storage-explorer) voor de meest voorkomende distributies van Linux. Wij raden Snap Store voor deze installatie. De Module Storage Explorer installeert al zijn afhankelijkheden en updates wanneer nieuwe versies worden gepubliceerd in de Snap Store.

Zie de [pagina vastgelopen installatie](https://snapcraft.io/docs/installing-snapd)voor ondersteunde distributies .

Storage Explorer vereist het gebruik van een wachtwoordmanager. Mogelijk moet u handmatig verbinding maken met een wachtwoordmanager. U Storage Explorer verbinden met de wachtwoordmanager van uw systeem door de volgende opdracht uit te voeren:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer is ook beschikbaar als *.tar.gz* download. U moet afhankelijkheden handmatig installeren. De volgende distributies van Linux ondersteunen *.tar.gz* installatie:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

De *.tar.gz* installatie zou kunnen werken op andere distributies, maar alleen deze genoemde worden officieel ondersteund.

Zie [Linux-afhankelijkheden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) in de azure storage explorer voor meer hulp bij het installeren van Storage Explorer op Linux.

---

## <a name="download-and-install"></a>Downloaden en installeren

Zie [Azure Storage Explorer](https://www.storageexplorer.com)als u Storage Explorer wilt downloaden en installeren.

## <a name="connect-to-a-storage-account-or-service"></a>Verbinding maken met een opslagaccount of -service

Storage Explorer biedt verschillende manieren om verbinding te maken met opslagaccounts. In het algemeen u ofwel:

* [Aanmelden bij Azure om toegang te krijgen tot uw abonnementen en hun bronnen](#sign-in-to-azure)
* [Een specifieke opslag- of CosmosDB-bron koppelen](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

> [!NOTE]
> Om volledige toegang te krijgen tot bronnen nadat u zich hebt aangemeld, heeft Storage Explorer zowel machtigingen voor beheer (Azure Resource Manager) als gegevenslaag vereist. Dit betekent dat u Azure Active Directory -machtigingen (Azure AD) nodig hebt, waarmee u toegang hebt tot uw opslagaccount, de containers in het account en de gegevens in de containers. Als u alleen machtigingen op de gegevenslaag hebt, u overwegen [een bron toe te voegen via Azure AD](#add-a-resource-via-azure-ad). Zie de handleiding voor probleemoplossing van Azure Storage Explorer voor meer informatie over de specifieke machtigingen waarvoor Storage Explorer vereist [is.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)

1. Selecteer in Storage Explorer **Accountbeheer weergeven** > **Account Management** of selecteer de knop **Accounts beheren.**

    ![Accounts beheren][1]

1. **Accountbeheer** geeft nu alle Azure-accounts weer waarop u bent aangemeld. Als u verbinding wilt maken met een ander account, selecteert u **Een account toevoegen**.

1. Selecteer in **Verbinding maken met Azure Storage**een Azure-cloud uit **azure-omgeving** om u aan te melden bij een nationale cloud of een Azure Stack. Nadat u uw omgeving hebt gekozen, selecteert u **Volgende**.

    ![Optie om in te loggen][2]

    Storage Explorer opent een pagina waarop u zich aanmelden. Zie [Storage Explorer verbinden met een Azure Stack-abonnement of opslagaccount](/azure-stack/user/azure-stack-storage-connect-se)voor meer informatie.

1. Nadat u zich met een Azure-account hebt aangemeld, worden het account en de Azure-abonnementen die aan dat account zijn gekoppeld, weergegeven onder **ACCOUNTBEHEER**. Selecteer **Alle abonnementen** om uw selectie tussen alle of geen van de vermelde Azure-abonnementen te schakelen. Selecteer de Azure-abonnementen waarmee u wilt werken en selecteer vervolgens **Toepassen**.

    ![Selecteer Azure-abonnementen][3]

    **EXPLORER** geeft de opslagaccounts weer die zijn gekoppeld aan de geselecteerde Azure-abonnementen.

    ![Geselecteerde Azure-abonnementen][4]

### <a name="attach-a-specific-resource"></a>Een specifieke resource koppelen

Er zijn verschillende manieren om aan een resource te koppelen in Storage Explorer:

* [Een resource toevoegen via Azure AD](#add-a-resource-via-azure-ad). Als u alleen machtigingen hebt op de gegevenslaag, gebruikt u deze optie om een blobcontainer of een Azure Data Lake Storage Gen2 Blob-opslagcontainer toe te voegen.
* [Gebruik een verbindingstekenreeks](#use-a-connection-string). Gebruik deze optie als u een verbindingstekenreeks hebt voor een opslagaccount. Storage Explorer ondersteunt zowel de tekenreeksen van de verbinding met de sleutel als [de gedeelde verbinding met gedeelde toegang.](storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Gebruik een uri met gedeelde toegangshandtekening](#use-a-shared-access-signature-uri). Als u uri [met een gedeelde toegangshandtekening hebt](storage/common/storage-dotnet-shared-access-signature-part-1.md) voor een blobcontainer, bestandsshare, wachtrij of tabel, gebruikt u deze om aan de bron te koppelen. Als u uri met een gedeelde toegangshandtekening wilt krijgen, u [Storage Explorer](#generate-a-sas-in-storage-explorer) of de [Azure-portal](https://portal.azure.com)gebruiken.
* [Gebruik een naam en sleutel.](#use-a-name-and-key) Als u een van de accountsleutels van uw opslagaccount kent, u deze optie gebruiken om snel verbinding te maken. Zoek uw sleutels op de pagina met opslagaccount door**Toegangssleutels** **voor instellingen** > te selecteren in de [Azure-portal.](https://portal.azure.com)
* [Koppelen aan een lokale emulator](#attach-to-a-local-emulator). Als u een van de beschikbare Azure Storage-emulators gebruikt, gebruikt u deze optie om eenvoudig verbinding te maken met uw emulator.
* [Maak verbinding met een Azure Cosmos DB-account met behulp van een verbindingstekenreeks](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Gebruik deze optie als u een verbindingstekenreeks hebt met een Instantie CosmosDB.
* [Maak verbinding met Azure Data Lake Store door URI](#connect-to-azure-data-lake-store-by-uri). Gebruik deze optie als u een URI naar Azure Data Lake Store hebt.

#### <a name="add-a-resource-via-azure-ad"></a>Een resource toevoegen via Azure AD

1. Selecteer het symbool **Verbinding maken** om Verbinding maken met Azure Storage **te openen**.

    ![Verbinding maken met de optie Azure Storage][9]

1. Als u dit nog niet hebt gedaan, gebruikt u de optie **Een Azure-account toevoegen** om u aan te melden bij het Azure-account dat toegang heeft tot de bron. Nadat u zich hebt aangemeld, gaat u terug naar **Verbinding maken met Azure Storage.**

1. Selecteer **Een resource toevoegen via Azure Active Directory (Azure AD)** en selecteer **Volgende**.

1. Selecteer een Azure-account en -tenant. Deze waarden moeten toegang hebben tot de opslagbron waaraan u wilt koppelen. Selecteer **Volgende**.

1. Kies het resourcetype dat u wilt koppelen. Voer de gegevens in die nodig zijn om verbinding te maken. 

   Welke informatie u op deze pagina invoert, is afhankelijk van het type resource dat u toevoegt. Zorg ervoor dat u het juiste type resource kiest. Nadat u de vereiste gegevens hebt ingevoerd, selecteert u **Volgende**.

1. Bekijk het **verbindingsoverzicht** om te controleren of alle informatie correct is. Als dit het is, selecteert u **Verbinding maken**. Selecteer Anders **Terug** om terug te keren naar de vorige pagina's om onjuiste informatie op te lossen.

Nadat de verbinding is toegevoegd, gaat de resourcestructuur naar het knooppunt dat de verbinding vertegenwoordigt. De resource wordt weergegeven onder**Blobcontainers**voor **lokale & gekoppelde** > **opslagaccounts** > **(gekoppelde containers).** >  Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [handleiding voor probleemoplossing van Azure Storage Explorer.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)

#### <a name="use-a-connection-string"></a>Een verbindingstekenreeks gebruiken

1. Selecteer het symbool **Verbinding maken** om Verbinding maken met Azure Storage **te openen**.

    ![Verbinding maken met de optie Azure Storage][9]

1. Selecteer **Een verbindingstekenreeks gebruiken**en selecteer **Volgende**.

1. Kies een weergavenaam voor uw verbinding en voer uw verbindingstekenreeks in. Selecteer vervolgens **Volgende**.

1. Bekijk het **verbindingsoverzicht** om te controleren of alle informatie correct is. Als dit het is, selecteert u **Verbinding maken**. Selecteer Anders **Terug** om terug te keren naar de vorige pagina's om onjuiste informatie op te lossen.

Nadat de verbinding is toegevoegd, gaat de resourcestructuur naar het knooppunt dat de verbinding vertegenwoordigt. De resource wordt weergegeven onder **Lokale & gekoppelde** > **opslagaccounts**. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [handleiding voor probleemoplossing van Azure Storage Explorer.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)

#### <a name="use-a-shared-access-signature-uri"></a>Uri met gedeelde toegangshandtekening gebruiken

1. Selecteer het symbool **Verbinding maken** om Verbinding maken met Azure Storage **te openen**.

    ![Verbinding maken met de optie Azure Storage][9]

1. Selecteer **Een urivoor gedeelde toegangshandtekening (SAS) gebruiken**en selecteer **Volgende**.

1. Kies een weergavenaam voor uw verbinding en voer uw uri voor gedeelde toegangshandtekening URI in. Het serviceeindpunt voor het type resource dat u aanmaakt, moet automatisch worden ingevuld. Als u een aangepast eindpunt gebruikt, is het mogelijk dat dit niet het is. Selecteer **Volgende**.

1. Bekijk het **verbindingsoverzicht** om te controleren of alle informatie correct is. Als dit het is, selecteert u **Verbinding maken**. Selecteer Anders **Terug** om terug te keren naar de vorige pagina's om onjuiste informatie op te lossen.

Nadat de verbinding is toegevoegd, gaat de resourcestructuur naar het knooppunt dat de verbinding vertegenwoordigt. De resource wordt weergegeven onder **Lokale & gekoppelde** > **opslagaccounts** > **(gekoppelde containers)** > *het serviceknooppunt voor het type container dat u hebt gekoppeld.* Zie de [azure Storage Explorer,](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)als u uw verbinding niet toevoegen. Raadpleeg de handleiding voor probleemoplossing als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd.

#### <a name="use-a-name-and-key"></a>Een naam en sleutel gebruiken

1. Selecteer het symbool **Verbinding maken** om Verbinding maken met Azure Storage **te openen**.

    ![Verbinding maken met de optie Azure Storage][9]

1. Selecteer **De naam en sleutel van een opslagaccount**gebruiken en selecteer **Volgende**.

1. Kies een weergavenaam voor uw verbinding.

1. Voer de naam van uw opslagaccount en een van de toegangssleutels in.

1. Kies het **domein Opslag** dat u wilt gebruiken en selecteer **Volgende**.

1. Bekijk het **verbindingsoverzicht** om te controleren of alle informatie correct is. Als dit het is, selecteert u **Verbinding maken**. Selecteer Anders **Terug** om terug te keren naar de vorige pagina's om onjuiste informatie op te lossen.

Nadat de verbinding is toegevoegd, gaat de resourcestructuur naar het knooppunt dat de verbinding vertegenwoordigt. De resource wordt weergegeven onder **Lokale & gekoppelde** > **opslagaccounts**. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [handleiding voor probleemoplossing van Azure Storage Explorer.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)

#### <a name="attach-to-a-local-emulator"></a>Koppelen aan een lokale emulator

Storage Explorer ondersteunt momenteel twee officiële Storage-emulators:

* [Azure Storage emulator](storage/common/storage-use-emulator.md) (alleen Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS of Linux)

Als uw emulator luistert op de standaardpoorten, u het **emulator- standaardpoortknooppunt gebruiken** om toegang te krijgen tot uw emulator. Zoek naar **Emulator - Standaardpoorten** onder **lokale & gekoppelde** > **opslagaccounts**.

Als u een andere naam voor uw verbinding wilt gebruiken of als uw emulator niet op de standaardpoorten wordt uitgevoerd, voert u de volgende stappen uit:

1. Start je emulator. Voer de `AzureStorageEmulator.exe status` opdracht in om de poorten voor elk servicetype weer te geven.

   > [!IMPORTANT]
   > Storage Explorer start uw emulator niet automatisch. Je moet het handmatig starten.

1. Selecteer het symbool **Verbinding maken** om Verbinding maken met Azure Storage **te openen**.

    ![Verbinding maken met de optie Azure Storage][9]

1. Selecteer **Koppelen aan een lokale emulator**en selecteer **Volgende**.

1. Kies een weergavenaam voor uw verbinding en voer de poorten in waarop uw emulator luistert voor elk servicetype. **Koppelen aan een lokale Emulator** suggereert de standaard poortwaarden voor de meeste emulators. **Bestanden poort** is leeg, omdat geen van de officiële emulators momenteel ondersteuning voor de Files service. Als de emulator die u gebruikt bestanden ondersteunt, u de poort invoeren die u wilt gebruiken. Selecteer vervolgens **Volgende**.

1. Bekijk het **verbindingsoverzicht** en controleer of alle informatie correct is. Als dit het is, selecteert u **Verbinding maken**. Selecteer Anders **Terug** om terug te keren naar de vorige pagina's om onjuiste informatie op te lossen.

Nadat de verbinding is toegevoegd, gaat de resourcestructuur naar het knooppunt dat de verbinding vertegenwoordigt. Het knooppunt moet worden weergegeven onder **Lokale & gekoppelde** > **opslagaccounts**. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [handleiding voor probleemoplossing van Azure Storage Explorer.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Verbinding maken met een Azure Cosmos DB-account met behulp van een verbindingstekenreeks

In plaats van Azure Cosmos DB-accounts te beheren via een Azure-abonnement, u verbinding maken met Azure Cosmos DB met behulp van een verbindingstekenreeks. Volg deze stappen om verbinding te maken:

1. Vouw **onder EXPLORER**Lokale & **bijvoegen**uit , klik met de rechtermuisknop op Cosmos **DB-accounts**en selecteer Verbinding maken met Azure Cosmos **DB**.

    ![Verbinding maken met Azure Cosmos DB met een verbindingsreeks][21]

1. Selecteer de Azure Cosmos DB API, voer uw **verbindingstekenreeksgegevens in** en selecteer **OK** om het Azure Cosmos DB-account te verbinden. Zie [Een Azure Cosmos-account beheren](https://docs.microsoft.com/azure/cosmos-db/manage-account)voor informatie over het ophalen van de verbindingstekenreeks.

    ![Verbindingsreeks][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Verbinding maken met Azure Data Lake Store door URI

U hebt toegang tot een bron die niet in uw abonnement staat. Je hebt iemand nodig die toegang heeft tot die bron om je de bron URI te geven. Nadat u zich hebt aangemeld, maakt u verbinding met Data Lake Store met behulp van de URI. Volg deze stappen om verbinding te maken:

1. Vouw Local **& Attached**onder **EXPLORER**uit .

1. Klik met de rechtermuisknop op **Data Lake Storage Gen1**en selecteer **Verbinding maken met Data Lake Storage Gen1**.

    ![Verbinding maken met het contextmenu Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Voer de URI in en selecteer **OK**. Uw Data Lake Store wordt weergegeven onder **Data Lake Storage**.

    ![Verbinding maken met het resultaat van Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

In dit voorbeeld wordt Data Lake Storage Gen1 gebruikt. Azure Data Lake Storage Gen2 is nu beschikbaar. Zie [Wat is Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md)voor meer informatie.

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Een handtekening voor gedeelde toegang genereren in Storage Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Handtekening voor gedeelde toegang op accountniveau

1. Klik met de rechtermuisknop op het opslagaccount dat u wilt delen en selecteer **Vervolgens Gedeelde toegangshandtekening opvragen**.

    ![Menumenu voor het menu voor handtekening van gedeelde toegang openen][14]

1. Geef in **Gedeelde toegangshandtekening**het gewenste tijdsbestek en machtigingen op voor het account en selecteer **Vervolgens Maken**.

    ![Een handtekening voor gedeelde toegang][15]

1. Kopieer de **verbindingstekenreeks** of de tekenreeks met ruwe **query naar** het klembord.

### <a name="service-level-shared-access-signature"></a>Gedeelde toegangshandtekening op serviceniveau

U een handtekening voor gedeelde toegang krijgen op serviceniveau. Zie [De SAS voor een blobcontainer opvragen voor](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)meer informatie.

## <a name="search-for-storage-accounts"></a>Zoeken naar opslagaccounts

Als u een opslagbron wilt vinden, u zoeken in het deelvenster **EXPLORER.**

Wanneer u tekst invoert in het zoekvak, geeft Storage Explorer alle bronnen weer die overeenkomen met de zoekwaarde die u tot dat punt hebt ingevoerd. In dit voorbeeld wordt een zoekopdracht naar **eindpunten**weergegeven:

![Zoeken naar Storage-account][23]

> [!NOTE]
> Als u uw zoekopdracht wilt versnellen, gebruikt u **Accountbeheer** om alle abonnementen uit te zoeken die niet het item bevatten dat u zoekt. U ook met de rechtermuisknop op een knooppunt klikken en **Zoeken vanaf hier** selecteren om te beginnen met zoeken vanaf een specifiek knooppunt.
>
>

## <a name="next-steps"></a>Volgende stappen

* [Azure Blob-opslagbronnen beheren met Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Werken met gegevens in Azure Storage Explorer](./cosmos-db/storage-explorer.md)
* [Azure Data Lake Store-bronnen beheren met Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
