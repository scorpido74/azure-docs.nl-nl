---
title: Azure Cosmos DB-resources beheren met Azure Storage Explorer
description: Meer informatie over het maken van verbinding met Azure Cosmos DB en het beheren van de resources met behulp van Azure Storage Explorer.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: dech
ms.custom: seodec18
ms.openlocfilehash: 914551bab47ad9db4e0bca4d53226fbae74b92f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80411674"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Werken met gegevens in Azure Storage Explorer

Het gebruik van Azure Cosmos DB in Azure Storage Explorer stelt gebruikers in staat om Azure Cosmos DB entiteiten te beheren, gegevens te manipuleren, en opgeslagen procedures en triggers bij te werken samen met andere Azure entiteiten zoals opslagblobs en wachtrijen. U kunt nu hetzelfde hulpprogramma gebruiken om uw verschillende Azure entiteiten op één plek te beheren. Op dit moment biedt Azure Storage Explorer ondersteuning voor Cosmos-accounts die zijn geconfigureerd voor SQL-, MongoDB-, Graph-en Table-Api's.


## <a name="prerequisites"></a>Vereisten

Een Cosmos-account met de SQL-API of de API van Azure Cosmos DB voor MongoDB. Als u geen account hebt, kunt u er een maken in de Azure-portal, zoals beschreven in [Azure Cosmos DB: een SQL API-web-app ontwikkelen met .NET en de Azure Portal](create-sql-api-dotnet.md).

## <a name="installation"></a>Installatie

Installeer hier de nieuwste Azure Storage Explorer-bits: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), nu ondersteunen we Windows, Linux en MAC-versie.

## <a name="connect-to-an-azure-subscription"></a>Verbinding maken met een Azure-abonnement

1. Klik nadat u **Azure Storage Explorer** heb geïnstalleerd op het **invoegtoepassing**pictogram aan de linkerkant, zoals in de volgende afbeelding:
       
   ![Pictogram invoegtoepassing](./media/storage-explorer/plug-in-icon.png)
 
2. Selecteer **Een Azure-account toevoegen** en klik op **Aanmelden**.

   ![Verbinding maken met Azure-abonnement](./media/storage-explorer/connect-to-azure-subscription.png)

2. Selecteer **Aanmelden** in het **aanmeldingsvenster van Azure** en voer uw Azure-referenties in.

    ![Aanmelden](./media/storage-explorer/sign-in.png)

3. Selecteer uw abonnement in de lijst en klik op **Toepassen**.

    ![Toepassen](./media/storage-explorer/apply-subscription.png)

    Het Verkenner-venster wordt bijgewerkt en de accounts in het geselecteerde abonnement worden weergegeven.

    ![Lijst met accounts](./media/storage-explorer/account-list.png)

    U hebt uw **Cosmos DB-account** gekoppeld aan uw Azure-abonnement.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Verbinding maken met Azure Cosmos DB met behulp van een verbindingsreeks

Een andere manier van verbinding maken met een Azure Cosmos DB is het gebruik van een verbindingsreeks. Gebruik de volgende stappen om verbinding te maken met behulp van een verbindingsreeks.

1. Ga naar **Lokaal en gekoppeld** in het linkerdeelvenster, klik met de rechtermuisknop op **Cosmos DB-accounts** en kies **Verbinding maken met Cosmos DB...**

    ![Verbinding maken met Cosmos DB met een verbindingsreeks](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Momenteel is er alleen ondersteuning voor de SQL- en Table-API. Kies een API, plak uw **verbindingsreeks**, voer het **accountlabel** in, klik op **Volgende** om het overzicht te controleren en klik vervolgens op **Verbinden** om verbinding te maken met de Azure Cosmos DB-account. Zie [de Connection String ophalen](manage-with-powershell.md#list-keys)voor informatie over het ophalen van de primaire Connection String.

    ![Verbindingsreeks](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Verbinding maken met Azure Cosmos DB met behulp van een lokale emulator

Gebruik de volgende stappen uit om verbinding te maken met een Cosmos Azure DB via een emulator. Momenteel is er alleen ondersteuning voor SQL-accounts.

1. Installeer de emulator en start deze op. Zie [Cosmos DB Emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator) voor informatie over het installeren van Emulator.

2. Ga naar **Lokaal en gekoppeld** in het linkerdeelvenster, klik met de rechtermuisknop op **Cosmos DB-accounts** en kies **Verbinding maken met Cosmos DB Emulator...**

    ![Verbinding maken met Cosmos DB Emulator](./media/storage-explorer/emulator-entry.png)

3. Momenteel is er alleen ondersteuning voor de SQL-API. Plak uw **verbindingsreeks**, voer het **accountlabel** in, klik op **Volgende** om het overzicht te controleren en klik vervolgens op **Verbinden** om verbinding te maken met de Azure Cosmos DB-account. Zie [de Connection String ophalen](manage-with-powershell.md#list-keys)voor informatie over het ophalen van de primaire Connection String.

    ![Het dialoogvenster Verbinding maken met Cosmos DB Emulator](./media/storage-explorer/emulator-dialog.png)


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

     ![Openen in portal](./media/storage-explorer/open-in-portal.png)

* U kunt ook Azure Cosmos DB-account, database, verzameling toevoegen aan **Snelle toegang**.
* Met **Zoeken vanaf hier** kunt u naar sleutelwoorden zoeken onder het geselecteerde pad.

    ![zoeken vanaf hier](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Database- en verzamelingbeheer
#### <a name="create-a-database"></a>Een database maken 
-   Klik met de rechtermuisknop op het Azure Cosmos DB-account, kies **Database maken**, voer de naam van de database in en druk op **Enter**.
       
    ![Database maken](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Een database verwijderen
- Klik met de rechtermuisknop op de database, klik op **Database verwijderen **en klik in het pop-upvenster op **Ja**. Het databaseknooppunt wordt verwijderd en het Azure Cosmos DB die Cosmos-account wordt automatisch vernieuwd.

    ![Database1 verwijderen](./media/storage-explorer/delete-database1.png)  

    ![Database2 verwijderen](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Een verzameling maken
1. Klik met de rechter muisknop op uw data base, kies **verzameling maken**en geef de volgende informatie op, zoals **verzamelings-id**, **opslag capaciteit**, enzovoort. Klik op **OK** om te volt ooien. 

    ![Verzameling1 maken](./media/storage-explorer/create-collection.png)

    ![Verzameling2 maken](./media/storage-explorer/create-collection2.png) 

2. Selecteer **Onbeperkt** om de partitiesleutel te kunnen opgeven. Klik vervolgens op **OK** om te voltooien.

    Als bij het maken van een verzameling een partitiesleutel wordt gebruikt, kan de partitiesleutelwaarde van de verzameling niet meer worden gewijzigd.

    ![Partitiesleutel](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Een verzameling verwijderen
- Klik met de rechtermuisknop op de verzameling, klik op **Verzameling verwijderen**en klik vervolgens op **Ja** in het pop-upvenster. 

    Het verzamelingsknooppunt wordt verwijderd en de database wordt automatisch vernieuwd.

    ![Verzameling verwijderen](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Documentbeheer

#### <a name="create-and-modify-documents"></a>Documenten maken en wijzigen
- Als u een nieuw document wilt maken, opent u **Documenten** in het linker venster, klikt u op **Nieuw document**, bewerkt u de inhoud in het rechter deelvenster en klikt u vervolgens op **Opslaan**. U kunt ook een bestaand document bijwerken en vervolgens op **Opslaan** klikken. Wijzigingen kunnen worden genegeerd door op **Negeren** te klikken.

    ![Document](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Een document verwijderen
- Klik op de knop **Verwijderen** om het geselecteerde document te verwijderen.

#### <a name="query-for-documents"></a>Query voor documenten
- Bewerk het documentfilter door een [SQL query](how-to-sql-query.md) in te voeren en op **Toepassen** te klikken.

    ![Documentfilter](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Grafiekbeheer

#### <a name="create-and-modify-vertex"></a>Hoekpunt maken en wijzigen
1. Als u een nieuw hoekpunt wilt maken, opent u **Grafiek** vanuit het linkervenster, klikt u op **Nieuw hoekpunt**, bewerkt u de inhoud en klikt u vervolgens op **OK**.    
2. Als u een bestaand hoekpunt wilt wijzigen, klikt u op het penpictogram in het rechterdeelvenster.   

    ![Graph](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Een grafiek verwijderen
- Als u een hoekpunt wilt verwijderen, klikt u op het prullenbakpictogram naast de naam van het hoekpunt.

#### <a name="filter-for-graph"></a>Filteren op grafiek
- Bewerk het grafiekfilter door een [Gremlin-query](gremlin-support.md) in te voeren en op **Filter toepassen** te klikken.

    ![Grafiekfilter](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Tabelbeheer

#### <a name="create-and-modify-table"></a>Tabel maken en wijzigen
1. Ga als volgt te werk om een nieuwe tabel te maken: open **Entiteiten** vanuit het linkerdeelvenster, klik op **Toevoegen**, bewerk de inhoud in **Entiteit toevoegen**, voeg een eigenschap toe door te klikken op de knop **Eigenschap toevoegen** en klik vervolgens op **Invoegen**.
2. Als u een tabel wilt wijzigen, klikt u op **Bewerken**, wijzigt u de inhoud en klikt u vervolgens op **Bijwerken**.

    ![Tabel](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Tabel importeren en exporteren
1. Als u wilt importeren, klikt u op de knop **Importeren** en kiest u een bestaande tabel.
2. Als u wilt exporteren, klikt u op de knop **Exporteren** en kiest u een bestemming.

    ![Tabel importeren en exporteren](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Entiteiten verwijderen
- Selecteer de entiteiten en klik op knop **Verwijderen**.

    ![Tabel verwijderen](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Een query uitvoeren op een tabel
- Klik op de knop **Query**, voer queryvoorwaarden in en klik op de knop **Query uitvoeren**. Sluit het queryvenster door te klikken op de knop **Query sluiten**.

    ![Query uitvoeren op tabel](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Opgeslagen procedures, triggers en UDF's beheren
* Als u een opgeslagen procedure wilt maken, klikt u met de rechtermuisknop in de linkerboomstructuur op **Opgeslagen procedure**, kiest u **Opgeslagen procedure maken**, voert u links een naam in, typt u de opgeslagen-procedurescripts in het rechter venster en klikt u vervolgens op **Maken**. 
* U kunt ook bestaande opgeslagen procedures bewerken door erop te dubbelklikken, de procedure te bewerken en op **Bijwerken** te klikken om de wijzigingen op te slaan of op **Negeren** om de wijziging te annuleren.

    ![Opgeslagen procedure](./media/storage-explorer/stored-procedure.png)
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
6.  Open Storage Explorer en ga vervolgens naar**certificaten**voor het importeren van**SSL-certificaten** >  **bewerken** > . Gebruik de bestandskiezer en zoek, selecteer en open de cer-bestanden die u hebt gemaakt.

Als u met behulp van de bovenstaande stappen geen zelfondertekende certificaten kunt vinden, stuur ons dan feedback, zodat we u verder kunnen helpen.

#### <a name="unable-to-retrieve-subscriptions"></a>Kan geen abonnementen ophalen

Als u uw abonnementen niet kunt ophalen nadat u zich hebt aangemeld:

- Controleer of uw account toegang heeft tot de abonnementen door u aan te melden bij [Azure Portal](https://portal.azure.com/)
- Zorg ervoor dat u bent aangemeld via de juiste omgeving ([Azure](https://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Duitsland](https://portal.microsoftazure.de/), [Azure US Government](https://portal.azure.us/) of Aangepaste omgeving/Microsoft Azure Stack)
- Als u zich achter een proxy bevindt, controleert u of de Storage Explorer-proxy correct is geconfigureerd
- Probeer het account te verwijderen en opnieuw toe te voegen
- Verwijder de volgende bestanden uit de basismap (zoals: C:\Users\ContosoUser) en probeer vervolgens het account opnieuw toe te voegen:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Bekijk tijdens het aanmelden de console voor ontwikkelhulpprogramma's (F12) voor eventuele foutberichten

![console](./media/storage-explorer/console.png)

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

U kunt Microsoft Azure Cosmos DB-knooppunten in het linkerdeelvenster niet weergeven bij het configureren van een http/https-proxy in ASE. Dit is een bekend probleem en wordt opgelost in de volgende release. Op dit moment kunt u als tijdelijke oplossing Azure Cosmos DB Data Explorer in Azure Portal gebruiken. 

### <a name="development-node-under-local-and-attached-node-issue"></a>Probleem met het knooppunt 'Ontwikkeling' onder het knooppunt 'Lokaal en gekoppeld'

Er is geen reactie na klikken op het knooppunt 'Ontwikkeling' onder het knooppunt 'Lokaal en gekoppeld' in het linkerdeelvenster.  Dit gedrag is verwacht. De lokale emulator van Microsoft Azure Cosmos DB wordt in de volgende release ondersteund.

![Knooppunt 'Ontwikkeling'](./media/storage-explorer/development.png)

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Fout met toevoegen van Microsoft Azure Cosmos DB-account in het knooppunt 'Lokaal en gekoppeld'

Als u onderstaande fout ziet na het toevoegen van een Microsoft Azure Cosmos DB-account in het knooppunt 'Lokaal en gekoppeld', controleert u of u de juiste verbindingsreeks gebruikt.

![Fout met toevoegen van Microsoft Azure Cosmos DB-account in 'Lokaal en gekoppeld'](./media/storage-explorer/attached-error.png)

### <a name="expand-azure-cosmos-db-node-error"></a>Fout met uitvouwen van Azure-Cosmos DB-knooppunt

Mogelijk ziet u onderstaande fout tijdens een poging de structuurknooppunten links uit te vouwen. 

![Uitvouwfout](./media/storage-explorer/expand-error.png)

Probeer de volgende suggesties:

- Controleer of het Microsoft Azure Cosmos DB-account bezig is met inrichten en probeer het opnieuw wanneer het account is gemaakt.
- Als het account zich onder het knooppunt 'Snelle toegang' of 'Lokaal en gekoppelde' bevindt, controleert u of het account is verwijderd. Als dit het geval is, moet u het knooppunt handmatig verwijderen.

## <a name="contact-us"></a>Contact opnemen

Als geen van de oplossingen voor u werkt, stuurt u een e-mail naar Azure Cosmos DB dev tool ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) met informatie over het probleem om de problemen op te lossen.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de volgende video om te zien hoe u Azure Cosmos DB in Azure Storage Explorer kunt gebruiken: [Azure Cosmos DB gebruiken in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Meer informatie over Storage Explorer en verbinding maken met meer services vindt u in [Aan de slag met Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

