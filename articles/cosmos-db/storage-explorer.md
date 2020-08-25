---
title: Azure Cosmos DB-resources beheren met Azure Storage Explorer
description: Meer informatie over het maken van verbinding met Azure Cosmos DB en het beheren van de resources met behulp van Azure Storage Explorer.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: b892e4c5078b50bb865a715ddf12aebc1eb05f57
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799108"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Werken met gegevens in Azure Storage Explorer

Het gebruik van Azure Cosmos DB in Azure Storage Explorer stelt gebruikers in staat om Azure Cosmos DB entiteiten te beheren, gegevens te manipuleren, en opgeslagen procedures en triggers bij te werken samen met andere Azure entiteiten zoals opslagblobs en wachtrijen. U kunt nu hetzelfde hulpprogramma gebruiken om uw verschillende Azure entiteiten op één plek te beheren. Op dit moment biedt Azure Storage Explorer ondersteuning voor Cosmos-accounts die zijn geconfigureerd voor SQL-, MongoDB-, Graph-en Table-Api's.


## <a name="prerequisites"></a>Vereisten

Een Cosmos-account met de SQL-API of de API van Azure Cosmos DB voor MongoDB. Als u geen account hebt, kunt u er een maken in de Azure-portal, zoals beschreven in [Azure Cosmos DB: een SQL API-web-app ontwikkelen met .NET en de Azure Portal](create-sql-api-dotnet.md).

## <a name="installation"></a>Installatie

Installeer hier de nieuwste Azure Storage Explorer-bits: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), nu ondersteunen we Windows, Linux en MAC-versie.

## <a name="connect-to-an-azure-subscription"></a>Verbinding maken met een Azure-abonnement

1. Nadat u de **Azure Storage Explorer**hebt geïnstalleerd, selecteert u het pictogram voor de **invoeg toepassing** aan de linkerkant, zoals in de volgende afbeelding wordt weer gegeven:

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Selecteer het pictogram van de invoeg toepassing om verbinding te maken":::

2. Selecteer **Een Azure-account toevoegen** en selecteer vervolgens **Aanmelden**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Verbinding maken met het vereiste Azure-abonnement":::

2. Selecteer **Aanmelden**in het dialoog venster **Azure-aanmelding** en voer uw Azure-referenties in.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Meld u aan bij uw Azure-abonnement":::

3. Selecteer uw abonnement in de lijst en selecteer vervolgens **Toep assen**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Kies een abonnements-ID uit de lijst om te filteren":::

    Het Verkenner-venster wordt bijgewerkt en de accounts in het geselecteerde abonnement worden weergegeven.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Selecteer een Azure Cosmos DB-account in de lijst beschik bare":::

    U hebt uw **Cosmos DB-account** gekoppeld aan uw Azure-abonnement.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Koppelen aan Azure Cosmos DB via een verbindingsreeks

Een andere manier van verbinding maken met een Azure Cosmos DB is het gebruik van een verbindingsreeks. Gebruik de volgende stappen om verbinding te maken met behulp van een verbindingsreeks.

1. Ga naar **Lokaal en gekoppeld** in het linkerdeelvenster, klik met de rechtermuisknop op **Cosmos DB-accounts** en kies **Verbinding maken met Cosmos DB...**

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Koppelen aan Azure Cosmos DB via een verbindingsreeks":::

2. Momenteel is er alleen ondersteuning voor de SQL- en Table-API. Kies API, **verbindings reeks**plakken, label voor invoer **account**, selecteer **volgende** om de samen vatting te controleren en selecteer vervolgens **verbinding maken** om verbinding te maken met Azure Cosmos DB account. Zie [de Connection String ophalen](manage-with-powershell.md#list-keys)voor informatie over het ophalen van de primaire Connection String.

    :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Voer uw connection string in":::

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Verbinding maken met Azure Cosmos DB met behulp van een lokale emulator

Gebruik de volgende stappen uit om verbinding te maken met een Cosmos Azure DB via een emulator. Momenteel is er alleen ondersteuning voor SQL-accounts.

1. Installeer de emulator en start deze op. Zie [Cosmos DB Emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator) voor informatie over het installeren van Emulator.

2. Ga naar **Lokaal en gekoppeld** in het linkerdeelvenster, klik met de rechtermuisknop op **Cosmos DB-accounts** en kies **Verbinding maken met Cosmos DB Emulator...**

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Verbinding maken met Azure Cosmos DB van de emulator":::

3. Momenteel is er alleen ondersteuning voor de SQL-API. Plak de **verbindings reeks**, **het label**van het invoer account, selecteer **volgende** om de samen vatting te controleren en selecteer **verbinding maken** om Azure Cosmos DB account te verbinden. Zie [de Connection String ophalen](manage-with-powershell.md#list-keys)voor informatie over het ophalen van de primaire Connection String.

    :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Verbinding maken met Cosmos DB vanuit het Emulator-dialoog venster":::


## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB-resourcebeheer

U kunt een Azure Cosmos DB account beheren door de volgende bewerkingen uit te voeren:
* Open het account in de Azure portal
* Voeg de resource toe aan de lijst Snelle toegang
* Resources zoeken en vernieuwen
* Databases maken en verwijderen
* Verzamelingen maken en verwijderen
* Documenten maken, bewerken, verwijderen en filteren
* Opgeslagen procedures, triggers en door gebruikers gedefinieerde functies beheren

### <a name="quick-access-tasks"></a>Snelle toegang tot taken

Door met de rechtermuisknop op een abonnement in het deelvenster Verkenner te klikken, kunt u veel actietaken snel uitvoeren:

* Klik met de rechtermuisknop op een Azure Cosmos DB-account of een database. U kunt **Openen in Portal** kiezen en de bron beheren in de browser op de Azure-portal.

     :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Openen in portal":::

* U kunt ook Azure Cosmos DB-account, database, verzameling toevoegen aan **Snelle toegang**.
* Met **Zoeken vanaf hier** kunt u naar sleutelwoorden zoeken onder het geselecteerde pad.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="zoeken vanaf hier":::

### <a name="database-and-collection-management"></a>Database- en verzamelingbeheer

#### <a name="create-a-database"></a>Een database maken

-   Klik met de rechtermuisknop op het Azure Cosmos DB-account, kies **Database maken**, voer de naam van de database in en druk op **Enter**.

    :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Een Data Base maken in uw Azure Cosmos-account":::

#### <a name="delete-a-database"></a>Een database verwijderen

- Klik met de rechter muisknop op de data base, selecteer **Data Base verwijderen**en selecteer **Ja** in het pop-upvenster. Het databaseknooppunt wordt verwijderd en het Azure Cosmos DB die Cosmos-account wordt automatisch vernieuwd.

    :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="De eerste data base verwijderen":::

    :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="De tweede data bases verwijderen":::

#### <a name="create-a-collection"></a>Een verzameling maken

1. Klik met de rechter muisknop op uw data base, kies **verzameling maken**en geef de volgende informatie op, zoals **verzamelings-id**, **opslag capaciteit**, enzovoort. Klik op **OK** om te volt ooien.

    :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Eerste verzameling in de data base maken":::

    :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Tweede verzameling in de data base maken":::

2. Selecteer **onbeperkt** om de partitie sleutel te kunnen opgeven en selecteer vervolgens **OK** om te volt ooien.

    Als bij het maken van een verzameling een partitiesleutel wordt gebruikt, kan de partitiesleutelwaarde van de verzameling niet meer worden gewijzigd.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Een partitie sleutel configureren":::

#### <a name="delete-a-collection"></a>Verwijder een collectie

- Klik met de rechter muisknop op de verzameling, selecteer **verzameling verwijderen**en selecteer vervolgens **Ja** in het pop-upvenster.

    Het verzamelingsknooppunt wordt verwijderd en de database wordt automatisch vernieuwd.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Een van de verzamelingen verwijderen":::

### <a name="document-management"></a>Documentbeheer

#### <a name="create-and-modify-documents"></a>Documenten maken en wijzigen

- Als u een nieuw document wilt maken, opent u **documenten** in het linkerdeel venster, selecteert u **Nieuw document**, bewerkt u de inhoud in het rechterdeel venster en selecteert u **Opslaan**. U kunt ook een bestaand document bijwerken en vervolgens **Opslaan**selecteren. Wijzigingen kunnen worden genegeerd door op **Negeren** te klikken.

    :::image type="content" source="./media/storage-explorer/document.png" alt-text="Een nieuw document maken":::

#### <a name="delete-a-document"></a>Een document verwijderen

- Klik op de knop **Verwijderen** om het geselecteerde document te verwijderen.

#### <a name="query-for-documents"></a>Query voor documenten

- Bewerk het document filter door een [SQL-query](how-to-sql-query.md) in te voeren en selecteer vervolgens **Toep assen**.

    :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Query's uitvoeren voor specifieke documenten":::

### <a name="graph-management"></a>Grafiekbeheer

#### <a name="create-and-modify-vertex"></a>Hoekpunt maken en wijzigen

1. Als u een nieuw hoek punt wilt maken, opent u **Graph** vanuit het linkerdeel venster, selecteert u **Nieuw hoek punt**, bewerkt u de inhoud en selecteert u **OK**.
2. Als u een bestaand hoek punt wilt wijzigen, selecteert u het pictogram pen in het rechterdeel venster.

    :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Het hoek punt van een grafiek wijzigen":::

#### <a name="delete-a-graph"></a>Een grafiek verwijderen

- Als u een hoek punt wilt verwijderen, selecteert u het prullenbak pictogram naast de naam van het hoek punt.

#### <a name="filter-for-graph"></a>Filteren op grafiek

- Bewerk het grafiek filter door een [Gremlin-query](gremlin-support.md) in te voeren en vervolgens **filter Toep assen**te selecteren.

    :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Een grafiek query uitvoeren":::

### <a name="table-management"></a>Tabelbeheer

#### <a name="create-and-modify-table"></a>Tabel maken en wijzigen

1. Als u een nieuwe tabel wilt maken, opent u **entiteiten** in het linkerdeel venster, selecteert u **toevoegen**, de inhoud bewerken in het dialoog venster **entiteit** toevoegen, eigenschap toevoegen door op de knop **eigenschap toevoegen**te klikken en vervolgens **Invoegen**te selecteren.
2. Als u een tabel wilt wijzigen, selecteert u **bewerken**, wijzigt u de inhoud en selecteert u vervolgens **bijwerken**.

    :::image type="content" source="./media/storage-explorer/table.png" alt-text="Een tabel maken en wijzigen":::

#### <a name="import-and-export-table"></a>Tabel importeren en exporteren

1. Als u wilt importeren, selecteert u de knop **importeren** en kiest u een bestaande tabel.
2. Als u wilt exporteren, selecteert u de knop **exporteren** en kiest u een bestemming.

    :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Een tabel importeren of exporteren":::

#### <a name="delete-entities"></a>Entiteiten verwijderen

- Selecteer de entiteiten en selecteer de knop **verwijderen**.

    :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Een tabel verwijderen":::

#### <a name="query-table"></a>Een query uitvoeren op een tabel

- Klik op **query** knop, invoer query voorwaarde en selecteer knop **query uitvoeren** . Sluit het queryvenster door te klikken op de knop **Query sluiten**.

    :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Gegevens uit de tabel opvragen":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Opgeslagen procedures, triggers en UDF's beheren

* Als u een opgeslagen procedure wilt maken, klikt u in de linker boom structuur met de rechter muisknop op **opgeslagen procedure**, kiest u **opgeslagen procedure maken**, voert u een naam in de linkerkant in, typt u de opgeslagen procedure scripts in het rechter venster en selecteert u **maken**.
* U kunt ook bestaande opgeslagen procedures bewerken door te dubbel klikken, de update uit te voeren en op **bijwerken** te klikken om op te slaan, of op **verwijderen** om de wijziging te annuleren.

    :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Opgeslagen procedures maken en beheren":::
* De bewerkingen voor **triggers** en **UDF** zijn vergelijkbaar met die voor **opgeslagen procedures**.

## <a name="troubleshooting"></a>Problemen oplossen

[Azure Cosmos DB in Azure Storage Explorer](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) is een zelfstandige app waarmee u verbinding kunt maken met Azure Cosmos DB-accounts die worden gehost in Azure en onafhankelijke clouds vanuit Windows, Mac OS of Linux. Het gebruik van Azure Cosmos DB stelt u in staat om Azure Cosmos DB-entiteiten te beheren, gegevens te manipuleren, en opgeslagen procedures en triggers bij te werken, samen met andere Azure-entiteiten zoals opslag-blobs en wachtrijen.

Dit zijn oplossingen voor problemen die regelmatig voorkomen bij Azure Cosmos DB in Storage Explorer.

### <a name="sign-in-issues"></a>Problemen met aanmelden

Probeer uw toepassing opnieuw op te starten en kijk of de problemen kunnen worden opgelost voordat u doorgaat.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Zelfondertekend certificaat in de certificaatketen

Er zijn enkele redenen waarom u deze fout mogelijk ziet. Dit zijn de twee meest voorkomende:

+ U bevindt zich achter een *transparante proxy*, wat betekent dat iemand (zoals uw IT-afdeling) HTTPS-verkeer onderschept, ontsleutelt en vervolgens versleutelt met een zelfondertekend certificaat.

+ U voert software uit, zoals antivirus software, die een zelfondertekend TLS/SSL-certificaat injecteert in de HTTPS-berichten die u ontvangt.

Wanneer Storage Explorer een van deze zelfondertekende certificaten tegenkomt, kan de toepassing niet meer weten of met het ontvangen HTTPS-bericht is geknoeid. Als u echter een kopie van het zelfondertekende certificaat hebt, kunt u Storage Explorer instrueren om dit te vertrouwen. Als u niet zeker weet wie het certificaat injecteert, kunt u dit zelf proberen uit te zoeken met de volgende stappen:

1. OpenSSL installeren
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (een van de lichte versies is prima)
     - Mac- en Linux: moet bij het besturingssysteem zijn inbegrepen
2. OpenSSL uitvoeren
    - Windows: ga naar de installatiemap, vervolgens naar **/bin/** en dubbelklik op **openssl.exe**.
    - Mac- en Linux: voer **openssl** uit vanaf een terminal
3. Voer `s_client -showcerts -connect microsoft.com:443` uit
4. Zoek naar zelfondertekende certificaten. Als u niet zeker weet welke certificaten zelfondertekend zijn, zoek dan overal waar het onderwerp ('s:') en de certificaatverlener ('i') hetzelfde zijn.
5.  Als u zelfondertekende certificaten hebt gevonden, kopieert en plakt u alles vanaf **---BEGIN CERTIFICATE---** tot en met **---END CERTIFICATE---** naar een nieuw .cer-bestand. Doe dit voor elk certificaat.
6.  Open Storage Explorer en ga vervolgens naar certificaten voor het importeren van SSL-certificaten **bewerken**  >  **SSL Certificates**  >  **Import Certificates**. Gebruik de bestandskiezer en zoek, selecteer en open de cer-bestanden die u hebt gemaakt.

Als u met behulp van de bovenstaande stappen geen zelfondertekende certificaten kunt vinden, stuur ons dan feedback, zodat we u verder kunnen helpen.

#### <a name="unable-to-retrieve-subscriptions"></a>Kan geen abonnementen ophalen

Als u uw abonnementen niet kunt ophalen nadat u zich hebt aangemeld:

- Controleer of uw account toegang heeft tot de abonnementen door u aan te melden bij de [Azure Portal](https://portal.azure.com/)
- Zorg ervoor dat u bent aangemeld via de juiste omgeving ([Azure](https://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Duitsland](https://portal.microsoftazure.de/), [Azure US Government](https://portal.azure.us/) of Aangepaste omgeving/Microsoft Azure Stack)
- Als u zich achter een proxy bevindt, controleert u of de Storage Explorer-proxy correct is geconfigureerd
- Probeer het account te verwijderen en opnieuw toe te voegen
- Verwijder de volgende bestanden uit de basismap (zoals: C:\Users\ContosoUser) en probeer vervolgens het account opnieuw toe te voegen:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Bekijk tijdens het aanmelden de console voor ontwikkelhulpprogramma's (F12) voor eventuele foutberichten

:::image type="content" source="./media/storage-explorer/console.png" alt-text="De console voor ontwikkel hulpprogramma's controleren op fouten":::

#### <a name="unable-to-see-the-authentication-page"></a>Kan de verificatiepagina niet zien

Als u de verificatiepagina niet kunt zien:

- Afhankelijk van de snelheid van uw verbinding kan het even duren voordat de aanmeldingspagina is geladen. Wacht minstens één minuut voordat u het dialoogvenster voor verificatie sluit.
- Als u zich achter een proxy bevindt, controleert u of de Storage Explorer-proxy correct is geconfigureerd
- Start de ontwikkelaarsconsole door op de F12-toets te drukken. Bekijk de antwoorden van de ontwikkelaarsconsole en ga na of u een aanwijzing kunt vinden waarom de verificatie niet werkt

#### <a name="cannot-remove-account"></a>Kan het account niet verwijderen

Als het niet lukt om een account te verwijderen of als de koppeling om opnieuw te verifiëren niet werkt:

- Verwijder de volgende bestanden uit de basismap en probeer vervolgens het account opnieuw toe te voegen:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Als u aan SAS gekoppelde opslagbronnen wilt verwijderen, verwijder dan:
  - De map %AppData%/StorageExplorer voor Windows
  - /Gebruikers/<your_name>/Library/Application-ondersteuning/StorageExplorer voor Mac
  - ~/.config/StorageExplorer voor Linux
  - **U moet echter al uw referenties opnieuw invoeren** als u deze bestanden verwijdert.


### <a name="httphttps-proxy-issue"></a>Probleem met http/https-proxy

U kunt Microsoft Azure Cosmos DB-knooppunten in het linkerdeelvenster niet weergeven bij het configureren van een http/https-proxy in ASE. Op dit moment kunt u als tijdelijke oplossing Azure Cosmos DB Data Explorer in Azure Portal gebruiken.

### <a name="development-node-under-local-and-attached-node-issue"></a>Probleem met het knooppunt 'Ontwikkeling' onder het knooppunt 'Lokaal en gekoppeld'

Er is geen reactie na het selecteren van het knoop punt ' ontwikkeling ' onder het knoop punt ' lokaal en gekoppeld ' in de linker boom structuur.  Dit gedrag is verwacht. De lokale emulator van Microsoft Azure Cosmos DB wordt in de volgende release ondersteund.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Knooppunt 'Ontwikkeling'":::

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Fout met toevoegen van Microsoft Azure Cosmos DB-account in het knooppunt 'Lokaal en gekoppeld'

Als u onderstaande fout ziet na het toevoegen van een Microsoft Azure Cosmos DB-account in het knooppunt 'Lokaal en gekoppeld', controleert u of u de juiste verbindingsreeks gebruikt.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Fout met toevoegen van Microsoft Azure Cosmos DB-account in 'Lokaal en gekoppeld'":::

### <a name="expand-azure-cosmos-db-node-error"></a>Fout met uitvouwen van Azure-Cosmos DB-knooppunt

Mogelijk ziet u onderstaande fout tijdens een poging de structuurknooppunten links uit te vouwen.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Fout met uitvouwen van Azure-Cosmos DB-knooppunt":::

Probeer de volgende suggesties:

- Controleer of het Microsoft Azure Cosmos DB-account bezig is met inrichten en probeer het opnieuw wanneer het account is gemaakt.
- Als het account zich onder het knooppunt 'Snelle toegang' of 'Lokaal en gekoppelde' bevindt, controleert u of het account is verwijderd. Als dit het geval is, moet u het knooppunt handmatig verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de volgende video om te zien hoe u Azure Cosmos DB in Azure Storage Explorer kunt gebruiken: [Azure Cosmos DB gebruiken in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Meer informatie over Storage Explorer en verbinding maken met meer services vindt u in [Aan de slag met Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
