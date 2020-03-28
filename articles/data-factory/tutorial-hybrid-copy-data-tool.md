---
title: On-premises gegevens kopiëren met het hulpprogramma Voor Gegevens kopiëren van Azure
description: Maak een Azure-gegevensfactory en gebruik vervolgens het hulpprogramma Copy Data om gegevens uit een on-premises SQL Server-database te kopiëren naar Azure Blob-opslag.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/09/2018
ms.openlocfilehash: 1d8c68550d294534178fd6094b71fd6a7f1d1c46
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977361"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Gegevens uit een on-premises SQL-serverdatabase naar Azure Blob-opslag kopiëren met behulp van het hulpprogramma Copy Data.
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Huidige versie](tutorial-hybrid-copy-data-tool.md)

In deze zelfstudie gebruikt u Azure Portal om een gegevensfactory te maken. Vervolgens gebruikt u het hulpprogramma Copy Data om een pijplijn te maken waarmee gegevens uit een on-premises SQL Server-database worden gekopieerd naar Azure Blob Storage.

> [!NOTE]
> - Zie [Inleiding tot Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren

## <a name="prerequisites"></a>Vereisten
### <a name="azure-subscription"></a>Azure-abonnement
Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

### <a name="azure-roles"></a>Azure-rollen
Als u gegevensfabrieksexemplaren wilt maken, moet het gebruikersaccount dat u gebruikt om u aan te melden bij Azure, een *rol inzender* of *eigenaar* toegewezen krijgen of *beheerder* van het Azure-abonnement zijn.

Ga naar Azure Portal als u de machtigingen wilt weergeven die u hebt in het abonnement. Selecteer uw gebruikersnaam in de rechterbovenhoek en selecteer vervolgens **Machtigingen**. Als u toegang tot meerdere abonnementen hebt, moet u het juiste abonnement selecteren. Zie [Toegang beheren met RBAC en de Azure-portal](../role-based-access-control/role-assignments-portal.md) voor voorbeeldinstructies voor het toevoegen van een gebruiker aan een rol.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 en 2017
In deze zelfstudie gebruikt u een on-premises SQL Server-database als een *brongegevensopslag*. De pijplijn in de data factory die u in deze zelfstudie gaat maken, kopieert gegevens van deze on-premises SQL Server-database (bron) naar Blob-opslag (sink). Vervolgens maakt u een tabel met de naam **emp** in uw SQL Server-database en voegt u een paar voorbeeldvermeldingen in de tabel in.

1. Start SQL Server Management Studio. Als dit niet al is geïnstalleerd op uw computer, gaat u naar [SQL Server Management Studio downloaden](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

1. Maak verbinding met SQL Server-exemplaar met behulp van uw referenties.

1. Maak een voorbeelddatabase. Klik in de structuurweergave met de rechtermuisknop op **Databases** en selecteer **Nieuwe database**.

1. Voer in het venster **Nieuwe database** een naam in voor de database en selecteer **OK**.

1. Voer het volgende queryscript uit voor de database. Hiermee wordt de **emp**-tabel gemaakt en worden enkele voorbeeldgegevens ingevoegd in deze tabel. In de structuurweergave klikt u met de rechtermuisknop op de database die u hebt gemaakt en selecteert u **Nieuwe query**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Azure Storage-account
In deze zelfstudie gaat u een algemeen Azure Storage-account (en dan met name Blob Storage) gebruiken als een doel/sink-gegevensopslag. Zie het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) als u geen opslagaccount voor algemeen gebruikt hebt en er een wilt maken. De pijplijn in de data factory die u in deze zelfstudie gaat maken, kopieert gegevens van de on-premises SQL Server-database (bron) naar deze Blob-opslag (sink). 

#### <a name="get-the-storage-account-name-and-account-key"></a>De naam en sleutel van een opslagaccount ophalen
In deze zelfstudie gaat u de naam en sleutel van uw opslagaccount gebruiken. Voer de volgende stappen uit om de naam en sleutel van uw opslagaccount op te halen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met uw Azure-gebruikersnaam en -wachtwoord.

1. Selecteer **Alle services** in het linkerdeelvenster. Filter met behulp van het sleutelwoord **Opslag** en selecteer vervolgens **Opslagaccounts**.

    ![Zoeken naar Storage-account](media/doc-common-process/search-storage-account.png)

1. Filter indien nodig in de lijst met opslagaccounts op uw opslagaccount. Selecteer vervolgens uw opslagaccount.

1. Selecteer in het venster **Opslagaccount** de optie **Toegangssleutels**.


1. Kopieer de waarden in de vakken **opslagaccountnaam** en **key1** en plak deze in Kladblok of een andere editor voor later gebruik in de zelfstudie.

#### <a name="create-the-adftutorial-container"></a>De container adftutorial maken
In deze sectie maakt u in uw Blob Storage een blobcontainer met de naam **adftutorial**.

1. Schakel in het venster **Opslagaccount** over naar **Overzicht** en klik vervolgens op **Blobs**.

1. Selecteer in het **blobs-venster** **+ Container**.

1. Voer in het **venster Nieuw container** onder **Naam** **adftutorial**in en selecteer **OK**.

1. Selecteer **adftutorial** in de lijst met containers.


1. Houd het **venster Container** voor **adftutorial** open. U gebruikt het om de uitvoer aan het einde van de zelfstudie te verifiëren. In Data Factory wordt automatisch in deze container de uitvoermap gemaakt, zodat u er zelf geen hoeft te maken.


## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer in het menu aan de linkerkant **+ Maak een resource** > **Analytics** > **Data Factory**.

   ![Nieuwe data factory maken](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in bij **Naam**.

   De naam van de data factory moet *wereldwijd uniek* zijn. Als het volgende foutbericht wordt weergegeven voor het naamveld, wijzigt u de naam van de data factory (bijvoorbeeld uwnaamADFTutorialDataFactory). Zie [Data Factory naming rules](naming-rules.md) (Naamgevingsregels Data Factory) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

   ![Naam nieuwe data factory](./media/doc-common-process/name-not-available-error.png)
1. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
1. Voer een van de volgende stappen uit voor **Resourcegroep**:

   - Selecteer **Bestaande gebruiken**en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

   - Selecteer **Nieuw maken**en voer de naam van een resourcegroep in. 
        
     Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.
1. Selecteer **V2** onder **Versie**.
1. Selecteer bij **Locatie** de locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en -berekeningen (bijvoorbeeld Azure HDInsight) die door Data Factory worden gebruikt, kunnen zich in andere locaties/regio's bevinden.
1. Selecteer **Maken**.

1. Nadat de creatie is voltooid, ziet u de pagina **Gegevensfabriek** zoals weergegeven in de afbeelding.

     ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)
1. Selecteer **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Het hulpprogramma Copy Data gebruiken om een pijplijn te maken

1. Selecteer op de pagina **Aan de slag****Copy Data** om het hulpprogramma Copy Data te starten.

   ![Pagina Aan de slag](./media/doc-common-process/get-started-page.png)

1. Typ op de pagina **Eigenschappen** van het hulpprogramma Copy Data **CopyFromOnPremSqlToAzureBlobPipeline** bij **Taaknaam**. Selecteer **vervolgens Volgende**. Met het hulpprogramma Copy Data wordt een pijplijn gemaakt met de naam die u opgeeft in dit veld.
  ![Taaknaam](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. Klik op de pagina **Brongegevensarchief** op **Nieuwe verbinding maken**.


1. Zoek onder **Nieuwe gekoppelde service**naar **SQL Server**en selecteer **Doorgaan**.

1. Voer in het dialoogvenster **Nieuwe Linked Service (SQL Server)** onder **Naam** **SqlServerLinkedService**in . Selecteer **+ Nieuw** onder **Verbinding maken via integratieruntime**. U moet een zelf-hostende integratieruntime maken, deze downloaden op de computer en registreren bij Data Factory. Met de zelf-hostende integratieruntime worden gegevens gekopieerd tussen uw on-premises omgeving en de cloud.


1. Selecteer **Zelf gehost**in het dialoogvenster Instellingen voor verloop van **de integratie** . Selecteer **vervolgens Volgende**.

   ![Integratie-runtime maken](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog0.png)

1. Voer in het dialoogvenster Instellingen voor **insteltijd van integratie** onder **Naam** **de enter-inlooptijd voor zelfstudie**. Selecteer **vervolgens Volgende**.


1. Selecteer in het dialoogvenster Instellingen voor **insteltijd van integratie** de optie **Klik hier om de expresinstelling voor deze computer te starten.** Met deze actie wordt de integratieruntime op de computer geïnstalleerd en geregistreerd bij Data Factory. U kunt er ook voor kiezen om handmatig te configureren door het installatiebestand te downloaden, uit te voeren, en de sleutel te gebruiken om de integratieruntime te registreren.

1. Voer de gedownloade toepassing uit. U ziet de status van de snelle installatie in het venster.

    ![Status van snelle installatie](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. Controleer in het dialoogvenster **Nieuwe Gekoppelde Service (SQL Server)** of **TutorialIntegrationRuntime** is geselecteerd voor het veld Runtime voor integratie. Neem vervolgens de volgende stappen:

    a. Voer **SqlServerLinkedService** in bij **Naam**.

    b. Voer de naam van uw on-premises SQL Server-exemplaar in bij **Servernaam**.

    c. Voer de naam van uw on-premises database in bij **Databasenaam**.

    d. Selecteer de juiste verificatie bij **Verificatietype**.

    e. Voer bij **Gebruikersnaam** de naam in van de gebruiker die toegang heeft tot de on-premises SQL Server.

    f. Voer het **wachtwoord** voor de gebruiker in.

    g. Test verbinding en selecteer **Voltooien**.

      ![Integratieruntime geselecteerd](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. Selecteer **Volgende**op de pagina **Brongegevensarchief** .

1. Selecteer op de pagina **Tabellen selecteren waaruit de gegevens moeten worden gekopieerd of een aangepaste query gebruiken** de tabel **[dbo].[emp]** in de lijst en selecteer **Volgende**. U kunt een andere tabel selecteren op basis van uw database.

1. Selecteer op de pagina **Doelgegevensopslag** de optie **Nieuwe verbinding maken**


1. Zoek in **Nieuwe gekoppelde service** **azure blob**en selecteer Vervolgens **Doorgaan**.

   ![Selectie Blob-opslag](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. Voer de volgende stappen uit in het dialoogvenster **Nieuwe gekoppelde service (Azure Blob Storage)**:

   a. Voer **onder Naam** **AzureStorageLinkedService**in .

   b. Selecteer onder **Verbinden via integratieruntime** de optie **TutorialIntegrationRuntime**

   c. Selecteer bij **Opslagaccountnaam** uw opslagaccount in de vervolgkeuzelijst.

   d. Selecteer **Finish**.

1. Controleer in het dialoogvenster **Doelgegevensarchief** of **Azure Blob Storage** is geselecteerd. Selecteer **vervolgens Volgende**.

1. Voer onder **Mappad****adftutorial/fromonprem** in het dialoogvenster **Het uitvoerbestand of de uitvoermap kiezen** in. U hebt de container **adftutorial** gemaakt als onderdeel van de vereisten. Als de uitvoermap (in dit geval **fromonprem**) niet bestaat, wordt deze automatisch aangemaakt in Data Factory. U ook de knop **Bladeren** gebruiken om door de blob-opslag en de containers/mappen te bladeren. Als u geen waarde opgeeft onder **Bestandsnaam** wordt standaard de naam van de bron gebruikt (in dit geval **dbo.emp**).

   ![Het uitvoerbestand of de uitvoermap kiezen](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. Selecteer op de pagina **Instellingen bestandsindelingen** de optie **Volgende**.

1. Selecteer in het dialoogvenster **Instellingen** de optie **Volgende**.

1. Bekijk in het dialoogvenster **Overzicht** de waarden voor alle instellingen en selecteer **Volgende**.

1. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak die u hebt gemaakt, te controleren.

   ![De pagina Implementatie](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)

1. Op het tabblad **Controleren** kunt u de status bekijken van de pijplijn die u hebt gemaakt. Met de koppelingen in de kolom **Acties** kunt u de uitvoeringen van activiteit weergeven die zijn gekoppeld aan de pijplijnuitvoering, en de pijplijn opnieuw uitvoeren.

1. Selecteer de koppeling **Uitvoeringen van activiteit weergeven** in de kolom **Acties** om de activiteituitvoeringen te zien die zijn gekoppeld aan de pijplijnuitvoering. Selecteer de koppeling **Details** (pictogram van een bril) in de kolom **Acties** om details over de kopieerbewerking te zien. Als u wilt teruggaan naar de weergave **Pijplijnuitvoeringen,** selecteert u **Pipeline Runs** bovenaan.

1. Controleer of een uitvoerbestand wordt weergegeven in de map **fromonprem** van de container **adftutorial**.


1. Selecteer het tabblad **Bewerken** aan de linkerkant om over te schakelen naar de bewerkingsmodus. U kunt de gekoppelde services, gegevenssets en pijplijnen die zijn gemaakt met het hulpprogramma, bijwerken met behulp van de editor. Selecteer **Code** om de JSON-code weer te geven die is gekoppeld aan de entiteit die in de editor is geopend. Bekijk [de Azure Portal-versie van deze tutorial](tutorial-copy-data-portal.md) voor details over hoe u entiteiten in de Data Factory-gebruikersinterface kunt bewerken.

   ![Tabblad Bewerken](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Volgende stappen
Met de pijplijn in dit voorbeeld worden gegevens uit een on-premises SQL Server-database gekopieerd naar Blob Storage. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren

Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensopslag die wordt ondersteund door Data Factory.

Ga door naar de volgende zelfstudie voor informatie over het bulksgewijs kopiëren van gegevens uit een bron naar een bestemming:

> [!div class="nextstepaction"]
>[Gegevens bulksgewijs kopiëren](tutorial-bulk-copy-portal.md)
