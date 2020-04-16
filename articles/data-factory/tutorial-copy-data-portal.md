---
title: De Azure-portal gebruiken om een pijplijn voor gegevensfabrieken te maken
description: Deze zelfstudie bevat stapsgewijze instructies voor het maken van een data factory met een pijplijn met behulp van Azure Portal. De pijplijn gebruikt de kopieeractiviteit om gegevens uit Azure Blob-opslag naar een Azure SQL-database te kopiëren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 655a98ef1b6b8b2d4086b472ee7ce4d67346e5ca
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418707"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Gegevens kopiëren van Azure Blob-opslag naar een SQL database met Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie maakt u een data factory met behulp van de Azure Data Factory-gebruikersinterface. De pijplijn in deze gegevensfabriek kopieert gegevens uit Azure Blob-opslag naar een Azure SQL-database. Het configuratiepatroon in deze zelfstudie geldt voor het kopiëren van een gegevensarchief op basis van bestanden naar een relationeel gegevensarchief. Zie de tabel [Ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bron en als sink.

> [!NOTE]
> - Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Een pijplijn met kopieeractiviteit maken.
> * De uitvoering van de pijplijn testen.
> * De pijplijn handmatig activeren.
> * De pijplijn activeren volgens een schema.
> * De uitvoering van de pijplijn en van de activiteit controleren

## <a name="prerequisites"></a>Vereisten
* **Azure-abonnement**. Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* **Azure-opslagaccount**. U gebruikt de blobopslag als *bron*-gegevensopslag. Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken.
* **Azure SQL-database**. U gebruikt de database als *sink*-gegevensopslag. Zie [Een SQL-database maken](../sql-database/sql-database-get-started-portal.md) voor stappen om er een te maken als u geen Azure SQL-database hebt.

### <a name="create-a-blob-and-a-sql-table"></a>Een blob en een SQL-tabel maken

Voer nu de volgende stappen uit om uw blobopslag en SQL database voor te bereiden voor gebruik in deze zelfstudie.

#### <a name="create-a-source-blob"></a>Een bron-blob maken

1. Start Kladblok. Kopieer de volgende tekst en sla deze op uw schijf op in het bestand **emp.txt**:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Maak in de blobopslag een container met de naam **adftutorial**. Maak een map aan met de naam **invoer** in deze container. Vervolgens kunt u het bestand **emp.txt** uploaden naar de map **invoer**. Gebruik de Azure-portal of hulpprogramma's zoals [Azure Storage Explorer](https://storageexplorer.com/) voor deze taken.

#### <a name="create-a-sink-sql-table"></a>Een SQL-sink-tabel maken

1. Gebruik het volgende SQL-script om de tabel **dbo.emp** te maken in uw SQL database:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Geef Azure-services toegang tot de SQL-server. Zorg ervoor dat **Toegang tot Azure-services toestaan** is **ingeschakeld** voor SQL Server, zodat Data Factory gegevens naar SQL Server kan schrijven. Als u deze instelling wilt verifiëren en inschakelen, gaat u naar Azure SQL-server > Overzicht > Serverfirewall instellen> de optie **Toegang tot Azure-services toestaan** instellen in op **AAN**.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken
In deze stap maakt u een data factory en start u de Data Factory-gebruikersinterface om een pijplijn te maken in de data factory.

1. Open **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
2. Selecteer links in het menu Een resource > **Analytics** > **Data Factory maken.** **Create a resource**
3. Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in bij **Naam**.

   De naam van de Azure-gegevensfabriek moet *wereldwijd uniek*zijn. Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. (bijvoorbeeld yournameADFTutorialDataFactory). Zie [Data Factory naming rules](naming-rules.md) (Naamgevingsregels Data Factory) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

     ![Nieuwe data factory](./media/doc-common-process/name-not-available-error.png)
4. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
5. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken**en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuw maken**en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 
6. Selecteer **V2** onder **Versie**.
7. Selecteer onder **Locatie** een locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en berekenservices (bijvoorbeeld Azure HDInsight) die door de data factory worden gebruikt, kunnen zich in andere regio's bevinden.
8. Selecteer **Maken**.
9. Nadat de creatie is voltooid, ziet u de melding in het berichtencentrum. Selecteer **Ga naar resource om** naar de fabriekspagina Gegevens te navigeren.
10. Selecteer de tegel **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.


## <a name="create-a-pipeline"></a>Een pijplijn maken
In deze stap maakt u een pijplijn met kopieeractiviteit in de data factory. De kopieeractiviteit kopieert gegevens van de blobopslag naar SQL Database. In de [Quickstart](quickstart-create-data-factory-portal.md) hebt u een pijplijn gemaakt met de volgende stappen:

1. Maak een gekoppelde service.
1. Maak invoer- en uitvoergegevenssets.
1. Maak een pijplijn.

In deze zelfstudie begint u met het maken van de pijplijn. Vervolgens maakt u gekoppelde services en gegevenssets wanneer u deze nodig hebt om de pijplijn te configureren.

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**.

   ![Pijplijn maken](./media/doc-common-process/get-started-page.png)
1. Voer op het tabblad **Algemeen** voor de pijplijn **CopyPipeline** in voor de **Naam** van de pijplijn.

1. Vouw in het gereedschap **Activiteiten** de categorie **Verplaatsen en transformeren** uit en sleep en plaats de activiteit Gegevens **kopiëren** van het gereedschapsvak naar het oppervlak van de pijplijnontwerper. Geef **CopyFromBlobToSql** op bij **Naam**.

    ![Kopieeractiviteit](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Bron configureren

>[!TIP]
>In deze zelfstudie gebruikt u *accountsleutel* als verificatietype voor uw brongegevensarchief, maar u indien nodig andere ondersteunde verificatiemethoden kiezen: *SAS URI,**Service Principal* en *Managed Identity.* Raadpleeg de bijbehorende secties in [dit artikel](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties) voor meer informatie.
>Als u geheimen voor gegevensopslag veilig wilt opslaan, wordt het ook aanbevolen om een Azure Key Vault te gebruiken. Raadpleeg [dit artikel](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) voor gedetailleerde illustraties.

1. Ga naar het tabblad **Bron.** **Selecteer + Nieuw** om een brongegevensset te maken.

1. Selecteer azure **blob-opslag**in het dialoogvenster **Nieuwe gegevensset** en selecteer **Doorgaan**. De brongegevens bevinden zich in een blobopslag, daarom selecteert u **Azure Blob-opslag** voor de brongegevensset.

1. Kies in het dialoogvenster **Opmaak selecteren** het opmaaktype van uw gegevens en selecteer **Doorgaan**.

1. Voer in het dialoogvenster **Eigenschappen instellen** **SourceBlobDataset** voor naam in. Schakel het selectievakje voor **eerste rij als koptekst in**. Selecteer onder het tekstvak **Gekoppelde service** de optie **+ Nieuw**.

1. Voer in het dialoogvenster **Nieuwe Gekoppelde Service (Azure Blob Storage)** **AzureStorageLinkedService** in als naam uw opslagaccount in de lijst **Met de naam van het opslagaccount.** Test verbinding, selecteer **Maken** om de gekoppelde service te implementeren.

1. Nadat de gekoppelde service is gemaakt, wordt deze teruggezet naar de pagina **Eigenschappen instellen.** Selecteer naast **Bestandspad** de knop **Bladeren**.

1. Navigeer naar de **map adftutorial/invoer,** selecteer het **bestand emp.txt** en selecteer **OK**.

1. Selecteer **OK**. Het navigeert automatisch naar de pijplijnpagina. Controleer in het tabblad **Bron** of **SourceBlobDataset** is geselecteerd. Selecteer **Gegevens vooraf bekijken** om een voorbeeld van de gegevens op deze pagina te bekijken.

    ![Brongegevensset](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Sink configureren
>[!TIP]
>In deze zelfstudie gebruikt u *SQL-verificatie* als verificatietype voor uw sinkdataarchief, maar u indien nodig andere ondersteunde verificatiemethoden kiezen: *Service Principal* en *Managed Identity.* Raadpleeg de bijbehorende secties in [dit artikel](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) voor meer informatie.
>Als u geheimen voor gegevensopslag veilig wilt opslaan, wordt het ook aanbevolen om een Azure Key Vault te gebruiken. Raadpleeg [dit artikel](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) voor gedetailleerde illustraties.

1. Ga naar het tabblad **Sink** en selecteer **+Nieuw** om een sink-gegevensset te maken.

1. Voer in het dialoogvenster **Nieuwe gegevensset** 'SQL' in het zoekvak in om de connectors te filteren, Azure **SQL Database**te selecteren en selecteer **Doorgaan**. In deze zelfstudie kopieert u gegevens naar een SQL database.

1. Voer in het dialoogvenster **Eigenschappen instellen** **UitvoerSqlDataset** voor naam in. Selecteer **+ Nieuw**in de vervolgkeuzelijst **Gekoppelde service** . Een gegevensset moet worden gekoppeld aan een gekoppelde service. De gekoppelde service beschikt over de verbindingsreeks die door Data Factory wordt gebruikt om tijdens de uitvoering verbinding te maken met de SQL database. De dataset geeft informatie over de container, map en het bestand (optioneel) met de brongegevens.

1. Neem in het dialoogvenster **Nieuwe Gekoppelde Service (Azure SQL Database)** de volgende stappen:

    a. Geef **AzureSqlDatabaseLinkedService** op als **Naam**.

    b. Selecteer bij **Servernaam** uw SQL Server-exemplaar.

    c. Selecteer uw SQL-database bij **Databasenaam**.

    d. Voer bij **Gebruikersnaam** de naam van de gebruiker in.

    e. Geef bij **Wachtwoord** het wachtwoord van de gebruiker op.

    f. Als u de verbinding wilt testen, selecteert u **Verbinding testen**.

    g. Selecteer **Maken** om de gekoppelde service te implementeren.

    ![Nieuwe gekoppelde service opslaan](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Het wordt automatisch naar het dialoogvenster **Eigenschappen instellen** genavigeerd. Selecteer bij **Tabel****[dbo].[emp]**. Selecteer vervolgens **OK**.

1. Ga naar het tabblad met de pijplijn en controleer bij **Sink-gegevensset** of **OutputSqlDataset** is geselecteerd.

    ![Tabblad Pijplijn](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

U het schema van de bron optioneel toewijzen aan het bijbehorende bestemmingsschema door [Schematoewijzing in kopieeractiviteit](copy-activity-schema-and-type-mapping.md)te volgen.

## <a name="validate-the-pipeline"></a>De pijplijn valideren
Selecteer in de werkbalk **Valideren** om de pijplijn te valideren.

U de JSON-code zien die aan de pijplijn is gekoppeld door rechtsboven op **Code** te klikken.

## <a name="debug-and-publish-the-pipeline"></a>Fouten opsporen in de pijplijn en de pijplijn publiceren
U kunt fouten opsporen in een pijplijn voordat u artefacten (gekoppelde services, gegevenssets en pijplijn) publiceert naar Data Factory of uw eigen Azure Repos Git-opslagplaats.

1. Selecteer **Fouten opsporen** om fouten op te sporen in de pijplijn. De status van de pijplijnuitvoering wordt weergegeven op het tabblad **Uitvoer** onder in het venster.

1. Zodra de pijplijn succesvol kan worden uitgevoerd, selecteert u op de bovenste werkbalk **Alle publiceren**. Met deze actie publiceert u entiteiten (gegevenssets en pijplijnen) die u hebt gemaakt met Data Factory.

1. Wacht tot u het bericht **Publiceren gelukt** ziet. Om meldingsberichten te zien, klikt u op **Meldingen weergeven** rechts bovenin (belknop).

## <a name="trigger-the-pipeline-manually"></a>De pijplijn handmatig activeren
In deze stap moet u handmatig de pijplijn activeren, die u in de vorige stap heeft gepubliceerd.

1. Selecteer op de werkbalk de optie **Activeren** en selecteer vervolgens **Nu activeren**. Selecteer op de pagina **Pijplijnuitvoeren** de optie **OK**.  

1. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet een pijplijn die wordt geactiveerd door een handmatige trigger. U koppelingen onder de kolom **NAAM van** de pijplijn gebruiken om activiteitsdetails weer te geven en de pijplijn opnieuw uit te voeren.

    [![Pijplijnuitvoeringen controleren](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox)

1. Als u activiteitsruns wilt zien die zijn gekoppeld aan de pijplijnrun, selecteert u de koppeling **CopyPipeline** onder de kolom **PIPELINENAAM.** In dit voorbeeld is er slechts één activiteit, dus u ziet slechts één vermelding in de lijst. Selecteer de koppeling **Details** (brilpictogram) onder de kolom **ACTIVITEITSNAAM** voor meer informatie over de kopieerbewerking. Selecteer **Alle pijplijnuitvoeringen** bovenaan om terug te gaan naar de weergave Pijplijnuitvoeringen. Selecteer **Vernieuwen** om de weergave te vernieuwen.

    [![Activiteitsuitvoeringen controleren](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. Controleer of er twee extra rijen zijn toegevoegd aan de **emp**-tabel in de SQL database.

## <a name="trigger-the-pipeline-on-a-schedule"></a>De pijplijn activeren volgens een schema
In dit schema maakt u een planningstrigger voor de pijplijn. De trigger voert de pijplijn uit volgens de opgegeven planning, bijvoorbeeld elk uur of dagelijks. Hier stelt u de trigger in om elke minuut uit te voeren tot de opgegeven einddatum.

1. Ga naar het tabblad **Auteur** links boven op het tabblad Monitor.

1. Ga naar uw pijplijn, klik op **Activeren** in de werkbalk en selecteer **Nieuw/Bewerken**.

1. Selecteer **+ Nieuw** voor **triggergebied** kiezen in het dialoogvenster **Triggers toevoegen.**

1. Voer in het venster **Nieuwe trigger** de volgende stappen uit:

    a. Geef **RunEveryMinute** op bij **Naam**.

    b. Selecteer bij **Einde** de optie **Op datum**.

    c. Selecteer de vervolgkeuzelijst onder **Eindigen op**.

    d. Selecteer de optie **Huidige dag**. De dag is standaard ingesteld op de volgende dag.

    e. Werk het onderdeel **Eindtijd** bij tot een paar minuten na de huidige datum. De trigger wordt pas geactiveerd nadat u de wijzigingen publiceert. Als je het op slechts een paar minuten uit elkaar zet, en je publiceert het niet tegen die tijd, zie je geen trigger run.

    f. Selecteer **OK**.

    g. Selecteer Bij de optie **Geactiveerd** de optie **Ja**.

    h. Selecteer **OK**.

    > [!IMPORTANT]
    > Er zijn kosten verbonden aan elke pijplijnuitvoering, dus stel een geschikte einddatum in.

1. Controleer op de **triggerpagina Bewerken** de waarschuwing en selecteer **Opslaan**. De pijplijn in dit voorbeeld gebruikt geen parameters.

1. Klik **op Alles publiceren** om de wijziging te publiceren.

1. Ga naar het tabblad **Controleren** aan de linkerkant om de geactiveerde pijplijnuitvoeringen te bekijken.

    [![Geactiveerde pijplijnuitvoeringen](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. Als u wilt overschakelen van de weergave **Pijplijnuitvoeringen** naar de weergave **Trigger runs,** selecteert u **Trigger Runs** aan de linkerkant van het venster.

1. U ziet de triggeruitvoeringen in een lijst.

1. Controleer of er twee rijen per minuut (voor elke pijplijnuitvoering) tot de opgegeven eindtijd in de **emp**-tabel worden ingevoegd.

## <a name="next-steps"></a>Volgende stappen
Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in een blobopslag. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Een pijplijn met kopieeractiviteit maken.
> * De uitvoering van de pijplijn testen.
> * De pijplijn handmatig activeren.
> * De pijplijn activeren volgens een schema.
> * De uitvoering van de pijplijn en van de activiteit controleren


Ga verder met de volgende zelfstudie als u wilt weten hoe u on-premises gegevens kopieert naar de cloud:

> [!div class="nextstepaction"]
>[On-premises gegevens kopiëren naar de cloud](tutorial-hybrid-copy-portal.md)
