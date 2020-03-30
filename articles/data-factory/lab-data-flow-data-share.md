---
title: Gegevensintegratie met Azure Data Factory en Azure Data Share
description: Gegevens kopiëren, transformeren en delen met Azure Data Factory en Azure Data Share
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 6c501205812ac72da8cd970b61b71e493888cef1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76156723"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Gegevensintegratie met Azure Data Factory en Azure Data Share

Als klanten beginnen aan hun moderne data warehouse en analytics projecten, ze vereisen niet alleen meer gegevens, maar ook meer inzicht in hun gegevens in hun data-domein. In deze workshop wordt ingegaan op hoe verbeteringen aan Azure Data Factory en Azure Data Share de gegevensintegratie en -beheer in Azure vereenvoudigen. Van het inschakelen van codevrije ETL/ELT tot het creëren van een uitgebreid overzicht van uw gegevens, verbeteringen in Azure Data Factory stellen uw gegevenstechnici in staat om met vertrouwen meer gegevens en dus meer waarde aan uw onderneming te toevoegen. Azure Data Share stelt u in staat om op een beheerste manier zaken te doen met het delen van bedrijven.

In deze workshop gebruikt u Azure Data Factory (ADF) om gegevens uit een Azure SQL-database (SQL DB) in te nemen in Azure Data Lake Storage gen2 (ADLS gen2). Zodra u de gegevens in het meer landt, transformeert u deze via kaartgegevensstromen, de native transformatieservice van de gegevensfabriek, en laat u deze in Azure Synapse Analytics (voorheen SQL DW) zinken. Vervolgens deelt u de tabel met getransformeerde gegevens, samen met enkele extra gegevens met Azure Data Share. 

De gegevens die in dit lab worden gebruikt, zijn taxigegevens van New York City. Als u deze wilt importeren in uw Azure SQL-database, downloadt u het [bacpac-bestand voor taxigegevens.](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

* **Azure SQL Database:** Als u geen SQL DB hebt, leest u hoe [u een SQL DB-account maakt](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

* **Azure Data Lake Storage Gen2-opslagaccount:** als u geen ADLS Gen2-opslagaccount hebt, leest u hoe [u een ADLS Gen2-opslagaccount maakt.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account)

* **Azure Synapse Analytics (voorheen SQL DW)**: Als u geen Azure Synapse Analytics (voorheen SQL DW) hebt, leest u hoe [u een Azure Synapse Analytics-instantie maakt.](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)

* **Azure Data Factory:** Als u geen gegevensfabriek hebt gemaakt, raadpleegt u hoe [u een gegevensfabriek maakt.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)

* **Azure-gegevensshare:** Als u geen gegevensshare hebt gemaakt, raadpleegt u hoe [u een gegevensshare maakt.](https://docs.microsoft.com/azure/data-share/share-your-data#create-a-data-share-account)

## <a name="set-up-your-azure-data-factory-environment"></a>Uw Azure Data Factory-omgeving instellen

In deze sectie leert u hoe u toegang krijgt tot de gebruikerservaring van Azure Data Factory (ADF UX) vanuit de Azure-portal. Eenmaal in de ADF-UX configureert u drie gekoppelde services voor elk van de gegevensopslag die we gebruiken: Azure SQL DB, ADLS Gen2 en Azure Synapse Analytics.

In gekoppelde services in Azure Data Factory definiëren de verbindingsgegevens voor externe bronnen. Azure Data Factory ondersteunt momenteel meer dan 85 connectoren.

### <a name="open-the-azure-data-factory-ux"></a>De UX van Azure Data Factory openen

1. Open de [Azure-portal](https://portal.azure.com) in Microsoft Edge of Google Chrome.
1. Zoek met de zoekbalk boven aan de pagina naar 'Data Factories'

    ![Portal](media/lab-data-flow-data-share/portal1.png)
1. Klik op uw bron van gegevensfabrieken om het bronblad te openen.

    ![Portal](media/lab-data-flow-data-share/portal2.png)
1. Klik op **Auteur en Monitor** om de ADF UX te openen. De ADF UX is ook toegankelijk op adf.azure.com.

    ![Portal](media/lab-data-flow-data-share/portal3.png)
1. Je wordt doorgestuurd naar de homepage van de ADF UX. Deze pagina bevat quick-starts, instructievideo's en koppelingen naar tutorials om concepten in de gegevensfabriek te leren. Als u wilt beginnen met ontwerpen, klikt u op het potloodpictogram in de linkerzijbalk.

    ![Portal](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Een Azure SQL-databasegekoppelde service maken

1. Op de ontwerppagina maakt u bronnen in de gegevensfabriek, zoals pijplijnen, gegevenssets, gegevensstromen, triggers en gekoppelde services. Als u een gekoppelde service wilt maken, klikt u op de knop **Verbindingen** in de rechterbenedenhoek.

    ![Portal](media/lab-data-flow-data-share/configure2.png)
1. Klik op het tabblad Verbindingen op **Nieuw** om een nieuwe gekoppelde service toe te voegen.

    ![Portal](media/lab-data-flow-data-share/configure3.png)
1. De eerste gekoppelde service die u configureert, is een Azure SQL DB. U de zoekbalk gebruiken om de lijst met gegevensopslag te filteren. Klik op de tegel **Azure SQL Database** en klik op Doorgaan.

    ![Portal](media/lab-data-flow-data-share/configure4.png)
1. Voer in het SQL DB-configuratievenster 'SQLDB' in als uw gekoppelde servicenaam. Voer uw referenties in zodat gegevensfabriek verbinding kan maken met uw database. Als u SQL-verificatie gebruikt, voert u de servernaam, de database, uw gebruikersnaam en wachtwoord in. U controleren of uw verbindingsgegevens correct zijn door op **Verbinding testen**te klikken. Klik **op Maken** als u klaar bent.

    ![Portal](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Een gekoppelde Azure Synapse Analytics-service maken

1. Herhaal hetzelfde proces om een gekoppelde Azure Synapse Analytics-service toe te voegen. Klik op het tabblad Verbindingen op **Nieuw**. Selecteer de tegel **Azure Synapse Analytics (voorheen SQL DW)** en klik op Doorgaan.

    ![Portal](media/lab-data-flow-data-share/configure6.png)
1. Voer in het gekoppelde serviceconfiguratievenster 'SQLDW' in als uw gekoppelde servicenaam. Voer uw referenties in zodat gegevensfabriek verbinding kan maken met uw database. Als u SQL-verificatie gebruikt, voert u de servernaam, de database, uw gebruikersnaam en wachtwoord in. U controleren of uw verbindingsgegevens correct zijn door op **Verbinding testen**te klikken. Klik **op Maken** als u klaar bent.

    ![Portal](media/lab-data-flow-data-share/configure7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Een gekoppelde Azure Data Lake Storage Gen2-service maken

1. De laatste gekoppelde service die nodig is voor dit lab is een Azure Data Lake Storage gen2.  Klik op het tabblad Verbindingen op **Nieuw**. Selecteer de tegel **Azure Data Lake Storage Gen2** en klik op Doorgaan.

    ![Portal](media/lab-data-flow-data-share/configure8.png)
1. Voer in het gekoppelde serviceconfiguratievenster 'ADLSGen2' in als uw gekoppelde servicenaam. Als u verificatie van accountsleutels gebruikt, selecteert u uw adls gen2-opslagaccount in de vervolgkeuzelijst **Opslagaccountnaam.** U controleren of uw verbindingsgegevens correct zijn door op **Verbinding testen**te klikken. Klik **op Maken** als u klaar bent.

    ![Portal](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Foutfoutmodus gegevensstroom inschakelen

In sectie *Gegevens transformeren met behulp van kaartgegevensstroom*bouwt u kaartgegevensstromen. Een aanbevolen toepassing voordat u gegevensstromen in kaart brengen, is het inschakelen van de foutopsporingsmodus, waarmee u transformatielogica in enkele seconden testen op een actief sparkcluster.

Als u foutopsporing wilt inschakelen, klikt u op de schuifregelaar **Foutopsporingsgegevens gegevensstromen** in de bovenste balk van de fabriek. Klik op ok wanneer de bevestigingsdialoogvenster pop-ups. Het cluster duurt ongeveer 5-7 minuten om op te starten. Ga verder met *het opnemen van gegevens van Azure SQL DB naar ADLS gen2 met behulp van de kopieeractiviteit* terwijl deze wordt geïnitialiseerd.

![Portal](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Gegevens opnemen met behulp van de kopieeractiviteit

In deze sectie maakt u een pijplijn met een kopieeractiviteit die één tabel van een Azure SQL DB inneemt naar een ADLS gen2-opslagaccount. U leert hoe u een pijplijn toevoegt, een gegevensset configureert en een pijplijn debugt via de ADF-UX. Het configuratiepatroon dat in deze sectie wordt gebruikt, kan worden gebruikt voor het kopiëren van een relationeel gegevensarchief naar een gegevensarchief op basis van bestanden.

In Azure Data Factory is een pijplijn een logische groepering van activiteiten die samen een taak uitvoeren. Een activiteit definieert een bewerking die moet worden uitgevoerd op uw gegevens. Een gegevensset verwijst naar de gegevens die u wilt gebruiken in een gekoppelde service.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Een pijplijn maken met een kopieeractiviteit

1. Klik in het deelvenster fabrieksbronnen op het pluspictogram om het nieuwe bronmenu te openen. Selecteer **Pijplijn**.

    ![Portal](media/lab-data-flow-data-share/copy1.png)
1. Geef in het tabblad **Algemeen** van het pijplijncanvas een naam aan uw pijplijn als iets beschrijvends, zoals 'IngestAndTransformTaxiData'.

    ![Portal](media/lab-data-flow-data-share/copy2.png)
1. Open in het deelvenster Activiteiten van het pijplijncanvas de accordeon **Verplaatsen en Transformeren** en sleep de activiteit Gegevens **kopiëren** naar het canvas. Geef de kopieeractiviteit een beschrijvende naam zoals 'InestIntoADLS'.

    ![Portal](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Azure SQL DB-brongegevensset configureren

1. Klik op het tabblad **Bron** van de kopieeractiviteit. Als u een nieuwe gegevensset wilt maken, klikt u op **Nieuw**. Uw bron zal de tafel 'dbo. TripData' in de eerder geconfigureerde linked service 'SQLDB'.

    ![Portal](media/lab-data-flow-data-share/copy4.png)
1. Zoek naar **Azure SQL Database** en klik op Doorgaan.

    ![Portal](media/lab-data-flow-data-share/copy5.png)
1. Bel uw dataset 'TripData'. Selecteer 'SQLDB' als uw gekoppelde service. Selecteer tabelnaam 'dbo. TripData' van de tabelnaam dropdown. Het schema **importeren uit verbinding/opslag**. Klik op OK als u klaar bent.

    ![Portal](media/lab-data-flow-data-share/copy6.png)

U hebt uw brongegevensset gemaakt. Zorg ervoor dat in de broninstellingen de **standaardwaardetabel** is geselecteerd in het queryveld gebruiken.

### <a name="configure-adls-gen-2-sink-dataset"></a>ADLS Gen 2-sinkgegevensset configureren

1. Klik op het tabblad **Sink** van de kopieeractiviteit. Als u een nieuwe gegevensset wilt maken, klikt u op **Nieuw**.

    ![Portal](media/lab-data-flow-data-share/copy7.png)
1. Zoek naar **Azure Data Lake Storage Gen2** en klik op Doorgaan.

    ![Portal](media/lab-data-flow-data-share/copy8.png)
1. Selecteer in het deelvenster Opmaak selecteren **DelimitedText** terwijl u naar een csv-bestand schrijft. Klik verder.

    ![Portal](media/lab-data-flow-data-share/copy9.png)
1. Noem uw sink dataset 'TripDataCSV'. Selecteer 'ADLSGen2' als uw gekoppelde service. Voer in waar u uw csv-bestand wilt schrijven. U bijvoorbeeld uw gegevens `trip-data.csv` schrijven `staging-container`om in de container in te dienen. Stel **eerste rij in als koptekst** op true als u wilt dat uw uitvoergegevens kopteksten hebben. Aangezien er nog geen bestand in de bestemming bestaat, stelt u **Het schema Importeren** in op **Geen**. Klik op OK als u klaar bent.

    ![Portal](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>De kopieeractiviteit testen met een foutopsporingsfoutfout op de pijplijn

1. Als u wilt controleren of uw kopieeractiviteit correct werkt, klikt u op **Foutopsporing** boven aan het pijplijncanvas om een foutopsporingsrun uit te voeren. Met een foutopsporingsrun u uw pijplijn end-to-end of tot een breekpunt testen voordat u deze publiceert naar de service voor gegevensfabrieken.

    ![Portal](media/lab-data-flow-data-share/copy11.png)
1. Als u de foutopsporingsrun wilt controleren, gaat u naar het tabblad **Uitvoer** van het pijplijncanvas. Het controlescherm wordt elke 20 seconden automatisch vernieuwd of wanneer u handmatig op de vernieuwingsknop klikt. De kopieeractiviteit heeft een speciale bewakingsweergave die toegankelijk is door op het brilpictogram in de kolom **Handelingen te** klikken.

    ![Portal](media/lab-data-flow-data-share/copy12.png)
1. De weergave kopieerbewaking geeft de uitvoeringsgegevens en prestatiekenmerken van de activiteit weer. U informatie zien zoals gelezen/geschreven gegevens, rijen gelezen/geschreven, gelezen/geschreven bestanden en doorvoer. Als u alles correct hebt geconfigureerd, ziet u 49.999 rijen die in één bestand in uw ADLS-gootsteen zijn geschreven.

    ![Portal](media/lab-data-flow-data-share/copy13.png)
1. Voordat u naar de volgende sectie gaat, wordt voorgesteld om uw wijzigingen in de gegevensfabriekservice te publiceren door op **Alles publiceren** in de bovenste balk van de fabriek te klikken. Azure Data Factory wordt niet behandeld in dit lab, maar ondersteunt volledige git-integratie. Git-integratie maakt versiebeheer, iteratieve besparing in een opslagplaats en samenwerking op een datafabriek mogelijk. Zie [bronbeheer in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#troubleshooting-git-integration)voor meer informatie.

    ![Portal](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Gegevens transformeren met toewijzingsgegevensstroom

Nu u gegevens met succes hebt gekopieerd naar Azure Data Lake Storage, is het tijd om die gegevens samen te voegen en samen te voegen in een gegevensmagazijn. We gebruiken de toewijzingsgegevensstroom, de visueel ontworpen transformatieservice van Azure Data Factory. Met gegevensstromen in kaart brengen, kunnen gebruikers transformatielogica codevrij ontwikkelen en uitvoeren op sparkclusters die worden beheerd door de ADF-service.

De gegevensstroom die in deze stap inner is gemaakt, voegt de 'TripDataCSV'-gegevensset die in de vorige sectie is gemaakt samen met een tabel 'dbo'. TripFares' opgeslagen in 'SQLDB' op basis van vier belangrijke kolommen. Vervolgens worden de gegevens samengevoegd `payment_type` op basis van kolom om het gemiddelde van bepaalde velden te berekenen en geschreven in een Azure Synapse Analytics-tabel.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Een activiteit voor gegevensstromen toevoegen aan uw pijplijn

1. Open in het deelvenster Activiteiten van het pijplijncanvas de accordeon **Verplaatsen en Transformeren** en sleep de activiteit **Gegevensstroom** naar het canvas.

    ![Portal](media/lab-data-flow-data-share/dataflow1.png)
1. Selecteer in het zijvenster dat wordt geopend de optie **Nieuwe gegevensstroom maken** en kies **Gegevensstroom toewijzen**. Klik op **OK**.

    ![Portal](media/lab-data-flow-data-share/dataflow2.png)
1. U wordt doorverwezen naar het canvas voor gegevensstromen waar u uw transformatielogica gaat bouwen. Geef in het algemene tabblad de naam van uw gegevensstroom 'JoinAndAggregateData'.

    ![Portal](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Csv-bron voor reisgegevens configureren

1. Het eerste wat u wilt doen is het configureren van uw twee brontransformaties. De eerste bron wijst naar de 'TripDataCSV' DelimitedText-gegevensset. Als u een brontransformatie wilt toevoegen, klikt u op het vak **Bron toevoegen** in het canvas.

    ![Portal](media/lab-data-flow-data-share/dataflow4.png)
1. Geef uw bron 'TripDataCSV' een naam en selecteer de 'TripDataCSV'-gegevensset in de vervolgkeuzelijst bron. Als u het zich herinnert, hebt u in eerste instantie geen schema geïmporteerd bij het maken van deze gegevensset, omdat er geen gegevens waren. Aangezien `trip-data.csv` u nu bestaat, klikt u op **Bewerken** om naar het tabblad gegevenssetinstellingen te gaan.

    ![Portal](media/lab-data-flow-data-share/dataflow5.png)
1. Ga naar **tabschema** en klik op **Schema importeren**. Selecteer **Uit verbinding/winkel** om rechtstreeks uit het archief te importeren. Er moeten 14 kolommen met teksttekenreeks worden weergegeven.

    ![Portal](media/lab-data-flow-data-share/dataflow6.png)
1. Ga terug naar dataflow 'JoinAndAggregateData'. Als uw foutopsporingscluster is gestart (aangegeven door een groene cirkel naast de schuifregelaar voor foutopsporing), u een momentopname van de gegevens krijgen op het tabblad **Gegevensvoorbeeld.** Klik op **Vernieuwen** om een gegevensvoorbeeld op te halen.

    ![Portal](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Data preview schrijft geen gegevens.

### <a name="configure-your-trip-fares-sql-db-source"></a>Uw reistarieven SQL DB-bron configureren

1. De tweede bron die u toevoegt, wijst naar de SQL DB-tabel 'dbo.The second source you're adding will point at the SQL DB table 'dbo. Tripfares'. Onder de bron 'TripDataCSV' staat nog een **add source-vak.** Klik erop om een nieuwe brontransformatie toe te voegen.

    ![Portal](media/lab-data-flow-data-share/dataflow8.png)
1. Noem deze bron 'TripFaresSQL'. Klik op **Nieuw** naast het veld brongegevensset om een nieuwe SQL DB-gegevensset te maken.

    ![Portal](media/lab-data-flow-data-share/dataflow9.png)
1. Selecteer de tegel **Azure SQL Database** en klik op Doorgaan. *Opmerking: U merkt dat veel van de connectors in de gegevensfabriek niet worden ondersteund in het toewijzen van gegevensstroom. Om gegevens uit een van deze bronnen om te zetten, neemt u deze in een ondersteunde bron op met behulp van de kopieeractiviteit.*

    ![Portal](media/lab-data-flow-data-share/dataflow10.png)
1. Bel uw dataset 'TripFares'. Selecteer 'SQLDB' als uw gekoppelde service. Selecteer tabelnaam 'dbo. TripFares' van de tabelnaam dropdown. Het schema **importeren uit verbinding/opslag**. Klik op OK als u klaar bent.

    ![Portal](media/lab-data-flow-data-share/dataflow11.png)
1. Als u uw gegevens wilt verifiëren, haalt u een gegevensvoorbeeld op het tabblad **Gegevensvoorbeeld.**

    ![Portal](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Inner join TripDataCSV en TripFaresSQL

1. Als u een nieuwe transformatie wilt toevoegen, klikt u op het pluspictogram in de rechterbenedenhoek van 'TripDataCSV'. Selecteer **Join onder** **Meerdere ingangen/uitgangen**.

    ![Portal](media/lab-data-flow-data-share/join1.png)
1. Noem je jointransformatie 'InnerJoinWithTripFares'. Selecteer 'TripFaresSQL' in de vervolgkeuzelijst van de rechterstream. Selecteer **Innerlijk** als jointype. Zie [Jointypen voor](https://docs.microsoft.com/azure/data-factory/data-flow-join#join-types)meer informatie over de verschillende jointypen in de toewijzingsgegevensstroom.

    Selecteer via de vervolgkeuzelijst **Join voorwaarden** welke kolommen u wilt matchen op elke stream. Als u een aanvullende joinvoorwaarde wilt toevoegen, klikt u op het pluspictogram naast een bestaande voorwaarde. Standaard worden alle joinvoorwaarden gecombineerd met een AND-operator, wat betekent dat aan alle voorwaarden voor een overeenkomst moet worden voldaan. In dit lab willen we `medallion`overeenkomen `hack_license` `vendor_id`met kolommen, , en`pickup_datetime`

    ![Portal](media/lab-data-flow-data-share/join2.png)
1. Controleer of u met 25 kolommen hebt samengevoegd, samen met een gegevensvoorbeeld.

    ![Portal](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Aggregaat per payment_type

1. Nadat u uw join-transformatie hebt voltooid, voegt u een geaggregeerde transformatie toe door op het pluspictogram naast 'InnerJoinWithTripFares' te klikken. Kies **Aggregaat** onder **Schema-modifier**.

    ![Portal](media/lab-data-flow-data-share/agg1.png)
1. Geef uw aggregaattransformatie 'AggregateByPaymentType' een naam. Selecteer `payment_type` als groep per kolom.

    ![Portal](media/lab-data-flow-data-share/agg2.png)
1. Ga naar het tabblad **Aggregaten.** Hier geeft u twee aggregaties op:
    * Het gemiddelde tarief gegroepeerd op betalingstype
    * De totale reisafstand gegroepeerd op betalingstype

    Eerst maakt u de gemiddelde tariefexpressie. Voer in het tekstvak met het label **Een kolom toevoegen of selecteren**'average_fare' in.

    ![Portal](media/lab-data-flow-data-share/agg3.png)
1. Als u een aggregatie-expressie wilt invoeren, klikt u op het blauwe vak met de vermelding **Expressie Invoeren**. Hiermee wordt de opbouw van gegevensstromenexpressiegeopend, een hulpmiddel dat wordt gebruikt om gegevensstroomexpressies visueel te maken met behulp van invoerschema' s, ingebouwde functies en bewerkingen en door de gebruiker gedefinieerde parameters. Zie de documentatie van de [expressiebouwer](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder)voor meer informatie over de mogelijkheden van de expressiebouwer.

    Als u het gemiddelde `avg()` tarief wilt krijgen, gebruikt u de aggregatiefunctie om de `total_amount` kolomcast samen te voegen tot een geheel getal met `toInteger()`. In de taal van de gegevensstroomexpressie wordt dit gedefinieerd als `avg(toInteger(total_amount))`. Klik **op Opslaan en voltooien** wanneer u klaar bent.

    ![Portal](media/lab-data-flow-data-share/agg4.png)
1. Als u een extra aggregatie-expressie `average_fare`wilt toevoegen, klikt u op het pluspictogram naast . Selecteer **Kolom toevoegen**.

    ![Portal](media/lab-data-flow-data-share/agg5.png)
1. Voer in het tekstvak met het label **Een kolom toevoegen of selecteren**'total_trip_distance' in. Net als in de laatste stap opent u de expressiebouwer om de expressie in te voeren.

    Als u de totale reisafstand wilt krijgen, gebruikt u de `sum()` aggregatiefunctie om de `trip_distance` kolomcast samen te voegen tot een geheel getal met `toInteger()`. In de taal van de gegevensstroomexpressie wordt dit gedefinieerd als `sum(toInteger(trip_distance))`. Klik **op Opslaan en voltooien** wanneer u klaar bent.

    ![Portal](media/lab-data-flow-data-share/agg6.png)
1. Test uw transformatielogica op het tabblad **Gegevensvoorbeeld.** Zoals u zien, zijn er aanzienlijk minder rijen en kolommen dan voorheen. Alleen de drie groep-en-aggregatiekolommen die in deze transformatie zijn gedefinieerd, gaan stroomafwaarts verder. Aangezien er slechts vijf groepen betalingstype in het voorbeeld zijn, worden slechts vijf rijen uitgevoerd.

    ![Portal](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Configureren van azure Synapse Analytics-sink

1. Nu we klaar zijn met onze transformatielogica, zijn we klaar om onze gegevens te laten zinken in een Azure Synapse Analytics-tabel. Voeg een gootsteentransformatie toe onder de sectie **Bestemming.**

    ![Portal](media/lab-data-flow-data-share/sink1.png)
1. Noem uw gootsteen 'SQLDWSink'. Klik op **Nieuw** naast het veld sinkdataset om een nieuwe Azure Synapse Analytics-gegevensset te maken.

    ![Portal](media/lab-data-flow-data-share/sink2.png)

1. Selecteer de tegel **Azure Synapse Analytics (voorheen SQL DW)** en klik op Doorgaan.

    ![Portal](media/lab-data-flow-data-share/sink3.png)
1. Bel uw dataset 'AggregatedTaxiData'. Selecteer 'SQLDW' als uw gekoppelde service. Selecteer **Nieuwe tabel maken** en de nieuwe tabeldbo een naam geven. AggregateTaxiData. Klik op OK als u klaar bent

    ![Portal](media/lab-data-flow-data-share/sink4.png)
1. Ga naar het tabblad **Instellingen** van de gootsteen. Aangezien we een nieuwe tabel maken, moeten we **tabel opnieuw maken** selecteren onder tabelactie. Schakel de optie **Fasering inschakelen uit**, die schakelt of we rij-voor-rij of in batch invoegen.

    ![Portal](media/lab-data-flow-data-share/sink5.png)

U hebt uw gegevensstroom gemaakt. Nu is het tijd om het uit te voeren in een pijplijn activiteit.

### <a name="debug-your-pipeline-end-to-end"></a>Bugt uw pijplijn end-to-end

1. Ga terug naar het tabblad voor de **IngestAndTransformData-pijplijn.** Let op het groene vak je op de 'InestIntoADLS' kopieeractiviteit. Sleep het naar de gegevensstroomactiviteit 'JoinAndAggregateData'. Dit creëert een 'op succes', waardoor de gegevensstroomactiviteit alleen wordt uitgevoerd als de kopie succesvol is.

    ![Portal](media/lab-data-flow-data-share/pipeline1.png)
1. Zoals we deden voor de kopieeractiviteit, klikt u op **Foutopsporing** om een foutopsporingsrun uit te voeren. Voor foutopsporing wordt de gegevensstroomactiviteit gebruikt het actieve foutopsporingscluster in plaats van een nieuw cluster op te draaien. Deze pijplijn duurt iets meer dan een minuut om uit te voeren.

    ![Portal](media/lab-data-flow-data-share/pipeline2.png)
1. Net als de kopieeractiviteit heeft de gegevensstroom een speciale bewakingsweergave die wordt geopend door het brilpictogram na voltooiing van de activiteit.

    ![Portal](media/lab-data-flow-data-share/pipeline3.png)
1. In de bewakingsweergave ziet u een vereenvoudigde gegevensstroomgrafiek samen met de uitvoeringstijden en rijen in elke uitvoeringsfase. Als u dit correct doet, moet u 49.999 rijen in vijf rijen in deze activiteit hebben samengevoegd.

    ![Portal](media/lab-data-flow-data-share/pipeline4.png)
1. U op een transformatie klikken om meer informatie te krijgen over de uitvoering ervan, zoals het partitioneren van informatie en nieuwe/bijgewerkte/gedropte kolommen.

    ![Portal](media/lab-data-flow-data-share/pipeline5.png)

Je hebt nu het gedeelte van de datafabriek van dit lab voltooid. Publiceer uw bronnen als u ze wilt operationaliseren met triggers. U hebt met de copy activity een pijplijn uitgevoerd die gegevens van Azure SQL Database naar Azure Data Lake Storage heeft ingenomen en die gegevens vervolgens hebt samengevoegd tot een Azure Synapse Analytics. U controleren of de gegevens met succes zijn geschreven door naar de SQL Server zelf te kijken.

## <a name="share-data-using-azure-data-share"></a>Gegevens delen met Azure Data Share

In deze sectie leert u hoe u een nieuwe gegevensshare instelt met behulp van de Azure-portal. Dit houdt in dat er een nieuw gegevensaandeel wordt gemaakt dat gegevenssets van Azure Data Lake Store Gen2 en Azure SQL Data Warehouse bevat. Vervolgens configureert u een momentopnameschema, waarmee de consumenten van gegevens de optie krijgen om de gegevens die met hen worden gedeeld, automatisch te vernieuwen. Vervolgens nodigt u ontvangers uit voor uw gegevensaandeel. 

Zodra u een gegevensaandeel hebt gemaakt, wisselt u van hoed en wordt u de *gegevensconsument.* Als gegevensconsument loopt u door de stroom van het accepteren van een uitnodiging voor gegevensshare, configureert u waar u de gegevens wilt ontvangen en brengt u gegevenssets in kaart naar verschillende opslaglocaties. Vervolgens activeert u een momentopname, waarmee de met u gedeelde gegevens worden gekopieerd naar de opgegeven bestemming. 

### <a name="sharing-data-data-provider-flow"></a>Gegevens delen (gegevensproviderstroom)

1. Open de Azure-portal in Microsoft Edge of Google Chrome.

1. Zoek met de zoekbalk boven aan de pagina naar **gegevensaandelen**

    ![Portal](media/lab-data-flow-data-share/portal-ads.png)

1. Selecteer het rekening voor gegevensdelen met 'Provider' in de naam. **Bijvoorbeeld DataProvider0102**. 

1. Selecteer **Uw gegevens delen**

    ![Beginnen met delen](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Selecteer **+Maken** om te beginnen met het configureren van uw nieuwe gegevensaandeel. 

1. Geef **onder Naam delen**een naam van uw keuze op. Dit is de naam van het aandeel die zal worden gezien door uw gegevens consument, dus zorg ervoor dat het een beschrijvende naam zoals TaxiData.

1. Onder **Beschrijving**, zet in een zin die de inhoud van de gegevens aandeel beschrijft. Het gegevensaandeel bevat gegevens over taxiritten wereldwijd die zijn opgeslagen in een aantal winkels, waaronder Azure SQL Data Warehouse en Azure Data Lake Store. 

1. Geef **onder Gebruiksvoorwaarden**een set voorwaarden op waaraan u wilt dat uw gegevensconsument zich houdt. Enkele voorbeelden zijn 'Verspreid deze gegevens niet buiten uw organisatie' of 'Verwijs naar juridische overeenkomst'. 

    ![Details delen](media/lab-data-flow-data-share/ads-details.png)

1. Selecteer **Doorgaan**. 

1. Gegevenssets **toevoegen selecteren** 

    ![Gegevensset toevoegen](media/lab-data-flow-data-share/add-dataset.png)

1. Selecteer **Azure SQL Data Warehouse** om een tabel te selecteren in het Azure SQL Data Warehouse waarin uw ADF-transformaties zijn geland.

    ![Gegevensset toevoegen](media/lab-data-flow-data-share/add-dataset-sql.png)

> [!NOTE]
> Azure SQL Data Warehouse staat nu bekend als Azure Synapse Analytics

1. U krijgt een script om uit te voeren voordat u verder gaan. Met het meegeleverde script wordt een gebruiker in de SQL-database gemaakt waarmee de MSI azure datashare namens deze database kan verifiëren. 

> [!IMPORTANT]
> Voordat u het script uitvoert, moet u uzelf instellen als active directory-beheerder voor de SQL Server. 

1. Open een nieuw tabblad en navigeer naar de Azure-portal. Kopieer het script dat is verstrekt om een gebruiker in de database te maken waarvan u gegevens wilt delen. Dit doe je door je aan te melden bij de EDW-database met Behulp van Query Explorer (preview) met AAD-verificatie. 

    U moet het script wijzigen zodat de gemaakte gebruiker zich tussen haakjes bevindt. Bijvoorbeeld:
    
    gebruiker [dataprovider-xxxx] maken vanuit externe aanmelding;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Schakel terug naar Azure Data Share, waar u gegevenssets aan uw gegevensaandeel toevoegde. 

1. Selecteer **EDW** voor het SQL Data Warehouse en selecteer **AggregatedTaxiData** voor de tabel. 

1. Gegevensset **toevoegen selecteren**

    We hebben nu een SQL-tabel die deel uitmaakt van onze gegevensset. Vervolgens voegen we extra gegevenssets toe van Azure Data Lake Store. 

1. Selecteer **Gegevensset toevoegen** en selecteer **Azure Data Lake Store Gen2**

    ![Gegevensset toevoegen](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Selecteer **Volgende**

1. *Wwtaxidata uitbreiden*. Boston *Taxi Data*uitbreiden. Merk op dat u delen tot op bestandsniveau. 

1. Selecteer de map *Boston Taxi Data* om de volledige map toe te voegen aan uw gegevensaandeel. 

1. Gegevenssets **toevoegen selecteren**

1. Bekijk de gegevenssets die zijn toegevoegd. U moet een SQL-tabel en een ADLSGen2-map toevoegen aan uw gegevensaandeel. 

1. Selecteer **Doorgaan**

1. In dit scherm u ontvangers toevoegen aan uw gegevensaandeel. De ontvangers die u toevoegt, ontvangen uitnodigingen voor uw gegevensaandeel. Voor het doel van dit lab moet u 2 e-mailadressen toevoegen:

    1. Het e-mailadres van het Azure-abonnement waarin u zich bevindt. 

        ![Ontvangers toevoegen](media/lab-data-flow-data-share/add-recipients.png)

    1. Voeg de fictieve gegevens *janedoe@fabrikam.com*toe die de consument met de naam .

1. In dit scherm u een momentopnameinstelling configureren voor uw gegevensconsument. Hierdoor kunnen ze regelmatig updates van uw gegevens ontvangen met een door u gedefinieerd interval. 

1. Controleer **Momentopnameschema** en configureer een vernieuwing van uw gegevens per uur met de *vervolgkeuzelijst Herhaling.*  

1. Selecteer **Maken**.

    U hebt nu een actief gegevensaandeel. Met een overzicht van wat u als gegevensprovider zien wanneer u een gegevensshare maakt. 

1. Selecteer de gegevensshare die u hebt gemaakt, getiteld **DataProvider**. U ernaar navigeren door **Verzonden aandelen** in **gegevensaandeel**te selecteren. 

1. Klik op Momentopnameschema. U het momentopnameschema uitschakelen als u dat wilt. 

1. Selecteer vervolgens het tabblad **Gegevenssets.** U extra gegevenssets toevoegen aan dit gegevensaandeel nadat deze is gemaakt. 

1. Selecteer het tabblad **Abonnementen delen.** Er bestaan nog geen abonnementen voor delen omdat uw gegevensconsument uw uitnodiging nog niet heeft geaccepteerd.

1. Navigeer naar het tabblad **Uitnodigingen.** Hier ziet u een lijst met in behandeling zijnde uitnodiging(en). 

    ![In behandeling zijnde uitnodigingen](media/lab-data-flow-data-share/pending-invites.png)

1. Selecteer de *janedoe@fabrikam.com*uitnodiging voor . Selecteer Verwijderen. Als de ontvanger de uitnodiging nog niet heeft geaccepteerd, kan deze niet meer. 

1. Selecteer het tabblad **Historie.** Er wordt nog niets weergegeven omdat uw gegevensconsument uw uitnodiging nog niet heeft geaccepteerd en een momentopname heeft geactiveerd. 

### <a name="receiving-data-data-consumer-flow"></a>Gegevens ontvangen (gegevensstroom van de consument)

Nu we ons gegevensaandeel hebben beoordeeld, zijn we klaar om van context te veranderen en onze hoed van de gegevensconsument te dragen. 

U moet nu een Azure Data Share-uitnodiging in uw postvak IN hebben van Microsoft Azure. Start Outlook Web Access (outlook.com) en meld u aan met de referenties die zijn geleverd voor uw Azure-abonnement.

Klik in de e-mail die u had moeten ontvangen op 'Uitnodiging > weergeven'. Op dit moment gaat u de ervaring van de gegevensconsumenten simuleren bij het accepteren van een uitnodiging voor gegevensproviders voor hun gegevensaandeel. 

![Uitnodiging per e-mail](media/lab-data-flow-data-share/email-invite.png)

Mogelijk wordt u gevraagd een abonnement te selecteren. Zorg ervoor dat u het abonnement selecteert waarin u voor dit lab hebt gewerkt. 

1. Klik op de uitnodiging met de titel *DataProvider*. 

1. In dit scherm Uitnodiging ziet u verschillende details over het gegevensaandeel dat u eerder als gegevensprovider hebt geconfigureerd. Bekijk de details en accepteer de gebruiksvoorwaarden indien aanwezig.

1. Selecteer de abonnements- en resourcegroep die al voor uw lab bestaat. 

1. Selecteer **DataConsumer**voor **rekening voor gegevensshare**. U ook een nieuw account voor gegevensshare maken. 

1. Naast **de naam Ontvangen delen**ziet u dat de standaardnaam van het aandeel de naam is die door de gegevensprovider is opgegeven. Geef het aandeel een vriendelijke naam die de gegevens beschrijft die u op het punt staat te ontvangen, bijvoorbeeld **TaxiDataShare.**

    ![Uitnodiging accepteert](media/lab-data-flow-data-share/consumer-accept.png)

1. U ervoor kiezen om **nu accepteren en configureren** of Later accepteren en **configureren.** Als u ervoor kiest om nu te accepteren en te configureren, geeft u een opslagaccount op waar alle gegevens moeten worden gekopieerd. Als u ervoor kiest om later te accepteren en te configureren, worden de gegevenssets in het aandeel niet in kaart gebracht en moet u ze handmatig in kaart brengen. Daar zullen we later voor kiezen. 

1. Selecteer **Later accepteren en configureren**. 

    Bij het configureren van deze optie wordt een aandelenabonnement gemaakt, maar er is nergens plaats voor de gegevens om te landen omdat er geen bestemming in kaart is gebracht. 

    Vervolgens configureren we datasettoewijzingen voor het gegevensaandeel. 

1. Selecteer het ontvangen aandeel (de naam die u in stap 5 hebt opgegeven).

    **Trigger momentopname** is grijs uit, maar het aandeel is Actief. 

1. Selecteer het tabblad **Gegevenssets.** Merk op dat elke setset niet in kaart is gebracht, wat betekent dat deze geen bestemming heeft om gegevens naar te kopiëren. 

    ![niet-toegewezen gegevenssets](media/lab-data-flow-data-share/unmapped.png)

1. Selecteer de SQL Data Warehouse Table en selecteer **+ Map naar Doel**.

1. Selecteer rechts van het scherm de vervolgkeuzelijst **Doelgegevenstype.** 

    U de SQL-gegevens toewijzen aan een breed scala aan gegevensarchieven. In dit geval worden we in kaart gebracht voor een Azure SQL-database.

    ![mapping](media/lab-data-flow-data-share/mapping-options.png)
    
    (Optioneel) Selecteer **Azure Data Lake Store Gen2** als doelgegevenstype. 
    
    (Optioneel) Selecteer het account Abonnement, Brongroep en Opslag waarin u hebt gewerkt. 
    
    (Optioneel) U ervoor kiezen om de gegevens in uw gegevensmeer te ontvangen in csv- of parketindeling. 

1. Selecteer naast **Doelgegevenstype**Azure SQL Database. 

1. Selecteer het account Abonnement, Brongroep en Opslag waarin u hebt gewerkt. 

    ![kaart naar sql](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Voordat u verder gaan, moet u een nieuwe gebruiker in de SQL Server maken door het opgegeven script uit te voeren. Kopieer eerst het script dat naar het klembord wordt geleverd. 

1. Open een nieuw azure-portaltabblad. Sluit uw bestaande tabblad niet, omdat u er zo snel mogelijk op terug moet komen. 

1. Navigeer in het nieuwe tabblad dat u hebt geopend naar **SQL-databases**.

1. Selecteer de SQL-database (er mag er maar één in uw abonnement staan). Zorg ervoor dat u het SQL Data Warehouse niet selecteert. 

1. **Queryeditor selecteren (voorbeeld)**

1. Aad-verificatie gebruiken om u aan te melden bij de queryeditor. 

1. Voer de query uit die in uw gegevensaandeel wordt weergegeven (gekopieerd naar klembord in stap 14). 

    Met deze opdracht kan de Azure Data Share-service Beheerde identiteiten voor Azure Services gebruiken om te verifiëren aan de SQL Server om gegevens erin te kunnen kopiëren. 

1. Ga terug naar het oorspronkelijke tabblad en selecteer **Kaart om te targeten**.

1. Selecteer vervolgens de map Azure Data Lake Gen2 die deel uitmaakt van de gegevensset en wijs deze toe aan een Azure Blob Storage-account. 

    ![storage](media/lab-data-flow-data-share/storage-map.png)

    Als alle gegevenssets in kaart zijn gebracht, bent u nu klaar om gegevens van de gegevensprovider te ontvangen. 

    ![Toegewezen](media/lab-data-flow-data-share/all-mapped.png)
    
1. Selecteer **Details**. 

    Merk op dat **trigger momentopname** is niet langer grijs uit, omdat de gegevens aandeel heeft nu bestemmingen te kopiëren in.

1. Selecteer Momentopname activeren > Volledige kopie. 

    ![Trigger](media/lab-data-flow-data-share/trigger-full.png)

    Hierdoor worden gegevens gekopieerd naar uw nieuwe account voor gegevensshare. In een scenario in de echte wereld zouden deze gegevens afkomstig zijn van een derde partij. 

    Het duurt ongeveer 3-5 minuten voordat de gegevens worden overgekomen. U de voortgang controleren door op het tabblad **Historie** te klikken. 

    Terwijl u wacht, navigeert u naar het oorspronkelijke gegevensaandeel (DataProvider) en bekijkt u de status van het tabblad Abonnementen en **geschiedenis delen.** **Share Subscriptions** 

1. Navigeer terug naar het gegevensaandeel van de gegevensvan de gegevensvan de gegevensvan de gegevens. Zodra de status van de trigger succesvol is, navigeert u naar de SQL-database en het gegevensmeer van bestemming om te zien dat de gegevens in de desbetreffende opslag zijn geland. 

Gefeliciteerd, je hebt het lab voltooid!


