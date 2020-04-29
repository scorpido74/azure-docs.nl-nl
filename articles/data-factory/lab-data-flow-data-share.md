---
title: Gegevens integratie met behulp van Azure Data Factory en Azure-gegevens share
description: Gegevens kopiëren, transformeren en delen met behulp van Azure Data Factory en Azure-gegevens delen
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 7d453b2724c308e48366d653a51d9e6aa8e82c96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415926"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Gegevens integratie met behulp van Azure Data Factory en Azure-gegevens share

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Als klanten hun moderne Data Warehouse-en analyse projecten binnenbrengen, hebben ze niet alleen meer gegevens nodig, maar ook meer inzicht in hun gegevens in hun eigen gegevens. Met deze workshop Dives u hoe verbeteringen van Azure Data Factory en Azure data share gegevens integratie en-beheer in azure vereenvoudigen. 

Met de verbeteringen in Azure Data Factory kunnen uw gegevens technici meer gegevens en dus meer waarde aan uw onderneming toevoegen om het maken van code-Free ETL/ELT in te scha kelen voor een uitgebreide weer gave over uw gegevens. Met Azure data share kunt u op een geheerste manier zaken doen met het delen van Bedrijfs gegevens.

In deze workshop gebruikt u Azure Data Factory (ADF) om gegevens van een Azure SQL database (SQL DB) op te nemen in Azure Data Lake Storage Gen2 (ADLS Gen2). Zodra u de gegevens in het lakegebied hebt getransformeerd, kunt u deze transformeren via gegevens stromen toewijzen data factory, de systeem eigen trans formatie-service en deze opvangen in azure Synapse Analytics (voorheen SQL DW). Vervolgens deelt u de tabel met getransformeerde gegevens, samen met een aantal aanvullende gegevens met behulp van de Azure-gegevens share. 

De gegevens die in dit lab worden gebruikt, zijn nieuwe taxi's City-gegevens. Als u het wilt importeren in uw Azure SQL database, downloadt u het [Bacpac-bestand](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)met de gegevens van de taxi.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

* **Azure SQL database**: als u geen SQL-Data Base hebt, leert u hoe u [een SQL DB-account maakt](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

* **Azure data Lake Storage Gen2 Storage-account**: als u geen ADLS Gen2 opslag account hebt, leert u hoe u [een ADLS Gen2-opslag account maakt](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

* **Azure Synapse Analytics (voorheen SQL DW)**: als u geen Azure Synapse Analytics (voorheen SQL DW) hebt, leert u hoe u [een exemplaar van Azure Synapse Analytics maakt](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal).

* **Azure Data Factory**: als u nog geen Data Factory hebt gemaakt, raadpleegt u [een Data Factory maken](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).

* **Azure-gegevens share**: Zie [een gegevens share maken](https://docs.microsoft.com/azure/data-share/share-your-data#create-a-data-share-account)als u nog geen gegevens share hebt gemaakt.

## <a name="set-up-your-azure-data-factory-environment"></a>Uw Azure Data Factory omgeving instellen

In deze sectie leert u hoe u de Azure Data Factory gebruikers ervaring (ADF UX) kunt openen vanuit de Azure Portal. Eenmaal in de ADF UX configureert u drie gekoppelde services voor elk van de gegevens archieven die worden gebruikt: Azure SQL DB, ADLS Gen2 en Azure Synapse Analytics.

Definieer in Azure Data Factory gekoppelde services de verbindings gegevens voor externe resources. Azure Data Factory ondersteunt momenteel meer dan 85 connectors.

### <a name="open-the-azure-data-factory-ux"></a>Open de Azure Data Factory UX

1. Open de [Azure Portal](https://portal.azure.com) in micro soft Edge of Google Chrome.
1. Zoek op ' gegevens fabrieken ' in de zoek balk boven aan de pagina

    ![Portal](media/lab-data-flow-data-share/portal1.png)
1. Klik op uw data factory resource om de Blade van de resource te openen.

    ![Portal](media/lab-data-flow-data-share/portal2.png)
1. Klik op **auteur en monitor** om de ADF UX te openen. De ADF UX kan ook worden geopend op adf.azure.com.

    ![Portal](media/lab-data-flow-data-share/portal3.png)
1. U wordt omgeleid naar de start pagina van de ADF UX. Deze pagina bevat snel starten, instructie Video's en koppelingen naar zelf studies om data factory concepten te leren. Klik op het potlood pictogram in de linkerkant balk om te beginnen met ontwerpen.

    ![Portal](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL database-service maken

1. Op de pagina ontwerpen kunt u data factory resources maken, zoals pijp lijnen, gegevens sets, data stromen, triggers en gekoppelde services. Als u een gekoppelde service wilt maken, klikt u op de knop **verbindingen** in de rechter benedenhoek.

    ![Portal](media/lab-data-flow-data-share/configure2.png)
1. Klik op het tabblad verbindingen op **Nieuw** om een nieuwe gekoppelde service toe te voegen.

    ![Portal](media/lab-data-flow-data-share/configure3.png)
1. De eerste gekoppelde service die u configureert, is een Azure SQL-data base. U kunt de zoek balk gebruiken om de lijst met gegevens archieven te filteren. Klik op de tegel **Azure SQL database** en klik op door gaan.

    ![Portal](media/lab-data-flow-data-share/configure4.png)
1. Voer in het deel venster SQL DB-configuratie ' SQLDB ' in als de naam van de gekoppelde service. Voer uw referenties in om data factory verbinding te laten maken met uw data base. Als u SQL-verificatie gebruikt, voert u de server naam, de data base, uw gebruikers naam en wacht woord in. U kunt controleren of de verbindings gegevens juist zijn door te klikken op **verbinding testen**. Klik op **maken** wanneer u klaar bent.

    ![Portal](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Een gekoppelde Azure Synapse Analytics-service maken

1. Herhaal hetzelfde proces om een gekoppelde Azure Synapse Analytics-service toe te voegen. Klik op het tabblad verbindingen op **Nieuw**. Selecteer de tegel **Azure Synapse Analytics (voorheen SQL DW)** en klik op door gaan.

    ![Portal](media/lab-data-flow-data-share/configure6.png)
1. Voer in het deel venster gekoppelde service configuratie ' SQLDW ' in als de naam van de gekoppelde service. Voer uw referenties in om data factory verbinding te laten maken met uw data base. Als u SQL-verificatie gebruikt, voert u de server naam, de data base, uw gebruikers naam en wacht woord in. U kunt controleren of de verbindings gegevens juist zijn door te klikken op **verbinding testen**. Klik op **maken** wanneer u klaar bent.

    ![Portal](media/lab-data-flow-data-share/configure7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Een Azure Data Lake Storage Gen2 gekoppelde service maken

1. De laatste gekoppelde service die nodig is voor dit lab is een Azure Data Lake Storage Gen2.  Klik op het tabblad verbindingen op **Nieuw**. Selecteer de tegel **Azure data Lake Storage Gen2** en klik op door gaan.

    ![Portal](media/lab-data-flow-data-share/configure8.png)
1. Voer in het deel venster gekoppelde service configuratie ' ADLSGen2 ' in als de naam van de gekoppelde service. Als u account sleutel verificatie gebruikt, selecteert u uw ADLS Gen2-opslag account in de vervolg keuzelijst **opslag accountnaam** . U kunt controleren of de verbindings gegevens juist zijn door te klikken op **verbinding testen**. Klik op **maken** wanneer u klaar bent.

    ![Portal](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Foutopsporingsmodus inschakelen voor de gegevens stroom

In sectie gegevens *transformeren met toewijzings gegevens stroom*maakt u toewijzings gegevens stromen. Een best practice voordat u toewijzings gegevens stromen bouwt, is het inschakelen van de foutopsporingsmodus, waarmee u de transformatie logica in seconden in een actief Spark-cluster kunt testen.

Als u fout opsporing wilt inschakelen, klikt u op de schuif regelaar voor **fout opsporing van gegevens stromen** in de bovenste balk van de fabriek. Klik op OK wanneer de pop-ups met het bevestigings venster worden bevestigd. Het cluster duurt ongeveer 5-7 minuten om te starten. Ga door met *het opnemen van gegevens van Azure SQL DB in ADLS Gen2 met behulp van de Kopieer activiteit tijdens de* initialisatie.

![Portal](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Gegevens opnemen met behulp van de Kopieer activiteit

In deze sectie maakt u een pijp lijn met een Kopieer activiteit die een tabel van een Azure SQL-data base opneemt in een ADLS Gen2-opslag account. U leert hoe u een pijp lijn toevoegt, een gegevensset configureert en een pijp lijn opspoort via de ADF-UX. Het configuratie patroon dat in deze sectie wordt gebruikt, kan worden toegepast voor het kopiëren van een relationele gegevens opslag naar een gegevens archief op basis van een bestand.

In Azure Data Factory is een pijp lijn een logische groepering van activiteiten die samen een taak uitvoeren. Een activiteit definieert een bewerking die op uw gegevens moet worden uitgevoerd. Een gegevensset wijst naar de gegevens die u wilt gebruiken in een gekoppelde service.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Een pijp lijn maken met een Kopieer activiteit

1. Klik in het deel venster Factory resources op het plus pictogram om het menu nieuwe resource te openen. Selecteer **pijp lijn**.

    ![Portal](media/lab-data-flow-data-share/copy1.png)
1. Geef op het tabblad **Algemeen** van het pijplijn doek een beschrijvende naam op voor de pijp lijn, zoals ' IngestAndTransformTaxiData '.

    ![Portal](media/lab-data-flow-data-share/copy2.png)
1. Open in het deel venster activiteiten van het pijp lijn-canvas de accordeon **verplaatsen en transformeren** en sleep de activiteit **gegevens kopiëren** naar het canvas. Geef de Kopieer activiteit een beschrijvende naam zoals ' IngestIntoADLS '.

    ![Portal](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Een Azure SQL DB-bron gegevensset configureren

1. Klik op het tabblad **bron** van de Kopieer activiteit. Klik op **Nieuw**om een nieuwe gegevensset te maken. De bron is de tabel dbo. Trip data ' bevindt zich in de gekoppelde service ' SQLDB ' eerder geconfigureerd.

    ![Portal](media/lab-data-flow-data-share/copy4.png)
1. Zoek naar **Azure SQL database** en klik op door gaan.

    ![Portal](media/lab-data-flow-data-share/copy5.png)
1. Roep de gegevensset ' trip data ' aan. Selecteer ' SQLDB ' als uw gekoppelde service. Selecteer de tabel naam dbo. Trip data ' uit de vervolg keuzelijst voor de tabel naam. Importeer het schema **uit de verbinding/het archief**. Klik op OK wanneer u klaar bent.

    ![Portal](media/lab-data-flow-data-share/copy6.png)

U hebt de bron-gegevensset gemaakt. Zorg ervoor dat de bron instellingen, de **tabel** standaard waarde is geselecteerd in het veld query gebruiken.

### <a name="configure-adls-gen-2-sink-dataset"></a>ADLS gen 2-Sink-gegevensset configureren

1. Klik op het tabblad **sink** van de Kopieer activiteit. Klik op **Nieuw**om een nieuwe gegevensset te maken.

    ![Portal](media/lab-data-flow-data-share/copy7.png)
1. Zoek naar **Azure data Lake Storage Gen2** en klik op door gaan.

    ![Portal](media/lab-data-flow-data-share/copy8.png)
1. Selecteer **DelimitedText** in het deel venster opmaak selecteren als u naar een CSV-bestand schrijft. Klik op door gaan.

    ![Portal](media/lab-data-flow-data-share/copy9.png)
1. Noem uw Sink-gegevensset ' TripDataCSV '. Selecteer ' ADLSGen2 ' als uw gekoppelde service. Voer in waar u uw CSV-bestand wilt schrijven. U kunt bijvoorbeeld uw gegevens schrijven naar een bestand `trip-data.csv` in de container `staging-container`. Stel de **eerste rij als koptekst** in op waar als u wilt dat uw uitvoer gegevens kopteksten hebben. Omdat er nog geen bestand in de bestemming bestaat, stelt u het **import schema** in op **geen**. Klik op OK wanneer u klaar bent.

    ![Portal](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>De Kopieer activiteit testen met het uitvoeren van een pijp lijn fout opsporing

1. Als u wilt controleren of uw Kopieer activiteit correct werkt, klikt u op **fout opsporing** boven aan het canvas van de pijp lijn om een uitvoering van de fout opsporing uit te voeren. Met een debug-uitvoering kunt u de pijp lijn end-to-end testen of tot een onderbrekings punt voordat u deze naar de data factory-service publiceert.

    ![Portal](media/lab-data-flow-data-share/copy11.png)
1. Als u de uitvoering van de fout opsporing wilt controleren, gaat u naar het tabblad **uitvoer** van het pijp lijn-canvas. Het scherm controle wordt automatisch elke 20 seconden vernieuwd of wanneer u hand matig op de knop Vernieuwen klikt. De Kopieer activiteit heeft een speciale bewakings weergave die kan worden geopend door te klikken op het pictogram met de pijl-omlaag in de kolom **acties** .

    ![Portal](media/lab-data-flow-data-share/copy12.png)
1. De weer gave controle van kopiëren geeft de uitvoerings Details en prestatie kenmerken van de activiteit. U kunt gegevens weer geven, zoals lezen/schrijven van gegevens, rijen lezen/schrijven, bestanden lezen/schrijven en door voer. Als u alles op de juiste wijze hebt geconfigureerd, ziet u dat er 49.999 rijen zijn geschreven naar één bestand in uw ADLS-sink.

    ![Portal](media/lab-data-flow-data-share/copy13.png)
1. Voordat u doorgaat naar de volgende sectie, wordt u geadviseerd om uw wijzigingen te publiceren naar de data factory-service door te klikken op **Alles publiceren** in de bovenste balk van de fabriek. Hoewel dit lab niet wordt gedekt door Azure Data Factory, ondersteunt de volledige Git-integratie. Met git-integratie kunt u versie beheer, iteratief opslaan in een opslag plaats en samen werking op een data factory. Zie [broncode beheer in azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#troubleshooting-git-integration)voor meer informatie.

    ![Portal](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Gegevens transformeren met toewijzingsgegevensstroom

Nu u gegevens naar Azure Data Lake Storage hebt gekopieerd, is het tijd om die gegevens samen te voegen en samen te voegen in een Data Warehouse. We gebruiken de toewijzings gegevens stroom, de visueel ontworpen transformatie service van Azure Data Factory. Met toewijzing van gegevens stromen kunnen gebruikers transformatie Logic code gratis ontwikkelen en deze uitvoeren op Spark-clusters die worden beheerd door de ADF-service.

De gegevens stroom die in deze stap wordt gemaakt, wordt binnen de TripDataCSV-gegevensset die in de vorige sectie is gemaakt met de tabel dbo. TripFares ' opgeslagen in ' SQLDB ' op basis van vier sleutel kolommen. Vervolgens worden de gegevens geaggregeerd op basis van kolom `payment_type` om het gemiddelde van bepaalde velden te berekenen en te worden geschreven in een Azure Synapse Analytics-tabel.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Een gegevens stroom activiteit toevoegen aan uw pijp lijn

1. Open in het deel venster activiteiten van het pijp lijn-canvas de accordeon **verplaatsen en transformeren** en sleep de activiteit **gegevens stroom** naar het canvas.

    ![Portal](media/lab-data-flow-data-share/dataflow1.png)
1. Selecteer in het zijvenster dat wordt geopend, de optie **nieuwe gegevens stroom maken** en kies **gegevens stroom toewijzen**. Klik op **OK**.

    ![Portal](media/lab-data-flow-data-share/dataflow2.png)
1. U wordt omgeleid naar het gegevensstroom canvas waar u uw transformatie logica wilt maken. Geef op het tabblad Algemeen de naam van uw gegevens stroom ' JoinAndAggregateData '.

    ![Portal](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>De CSV-bron van uw reis gegevens configureren

1. Het eerste wat u wilt doen, is het configureren van uw twee bron transformaties. De eerste bron wijst naar de DelimitedText-gegevensset ' TripDataCSV '. Als u een bron transformatie wilt toevoegen, klikt u op het vak **bron toevoegen** op het canvas.

    ![Portal](media/lab-data-flow-data-share/dataflow4.png)
1. Geef de bron de naam TripDataCSV en selecteer de gegevensset ' TripDataCSV ' in de vervolg keuzelijst bron. Als u zich herinnert, hebt u geen schema geïmporteerd tijdens het maken van deze gegevensset, omdat er geen gegevens zijn. Omdat `trip-data.csv` nu bestaat, klikt u op **bewerken** om naar het tabblad instellingen van gegevensset te gaan.

    ![Portal](media/lab-data-flow-data-share/dataflow5.png)
1. Ga naar het tabblad **schema** en klik op **schema importeren**. Selecteer **in verbinding/archief** om rechtstreeks vanuit het bestands archief te importeren. Er moeten 14 kolommen van het type teken reeks worden weer gegeven.

    ![Portal](media/lab-data-flow-data-share/dataflow6.png)
1. Ga terug naar de gegevens stroom ' JoinAndAggregateData '. Als uw cluster voor fout opsporing is gestart (aangegeven door een groene cirkel naast de schuif regelaar voor fout opsporing), kunt u een moment opname van de gegevens op het tabblad **voor het voor beeld** van de gegevens ophalen. Klik op **vernieuwen** om een voor beeld van de gegevens op te halen.

    ![Portal](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Met de voorbeeld gegevens worden geen gegevens geschreven.

### <a name="configure-your-trip-fares-sql-db-source"></a>Uw reis tarieven configureren SQL DB-bron

1. De tweede bron die u wilt toevoegen, verwijst naar de SQL DB-tabel ' dbo. TripFares'. Onder de bron ' TripDataCSV ' bevindt zich een ander vak **bron toevoegen** . Klik hierop om een nieuwe bron transformatie toe te voegen.

    ![Portal](media/lab-data-flow-data-share/dataflow8.png)
1. Noem deze bron ' TripFaresSQL '. Klik op **Nieuw** naast het veld Bron gegevensset om een nieuwe SQL DB-gegevensset te maken.

    ![Portal](media/lab-data-flow-data-share/dataflow9.png)
1. Selecteer de tegel **Azure SQL database** en klik op door gaan. *Opmerking: het kan voor komen dat veel connectors in Data Factory niet worden ondersteund in de toewijzing van gegevens stroom. Als u gegevens uit een van deze bronnen wilt transformeren, neemt u deze op in een ondersteunde bron met behulp van de Kopieer activiteit*.

    ![Portal](media/lab-data-flow-data-share/dataflow10.png)
1. Roep de gegevensset ' TripFares ' aan. Selecteer ' SQLDB ' als uw gekoppelde service. Selecteer de tabel naam dbo. TripFares ' uit de vervolg keuzelijst voor de tabel naam. Importeer het schema **uit de verbinding/het archief**. Klik op OK wanneer u klaar bent.

    ![Portal](media/lab-data-flow-data-share/dataflow11.png)
1. Als u uw gegevens wilt controleren, haalt u een voor beeld van de gegevens op in het tabblad **voor beeld van gegevens** .

    ![Portal](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Inner join TripDataCSV en TripFaresSQL

1. Als u een nieuwe trans formatie wilt toevoegen, klikt u op het plus pictogram in de rechter benedenhoek van ' TripDataCSV '. Selecteer **samen voegen**onder **meerdere invoer/uitvoer**.

    ![Portal](media/lab-data-flow-data-share/join1.png)
1. Geef een naam op voor uw samenvoegings transformatie ' InnerJoinWithTripFares '. Selecteer ' TripFaresSQL ' in de vervolg keuzelijst voor de juiste stream. Selecteer **inner** as het join-type. Zie voor meer informatie over de verschillende typen samen voegen in gegevens stroom koppelen de [typen samen voegen](https://docs.microsoft.com/azure/data-factory/data-flow-join#join-types).

    Selecteer de kolommen die u wilt vergelijken vanuit elke stroom via de vervolg keuzelijst **voor waarden voor samen voegen** . Als u een extra samenvoegings voorwaarde wilt toevoegen, klikt u op het plus pictogram naast een bestaande voor waarde. Standaard worden alle samenvoegings voorwaarden gecombineerd met een AND-operator, wat betekent dat aan alle voor waarden moet worden voldaan voor een overeenkomst. In dit lab willen we zoeken op kolommen `medallion`, `hack_license`, en `vendor_id``pickup_datetime`

    ![Portal](media/lab-data-flow-data-share/join2.png)
1. Controleer of u 25 kolommen hebt samengevoegd met een gegevens voorbeeld.

    ![Portal](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Aggregatie per payment_type

1. Nadat u de samenvoeg transformatie hebt voltooid, voegt u een geaggregeerde trans formatie toe door te klikken op het plus pictogram naast ' InnerJoinWithTripFares '. Kies **samen voegen** onder **schema wijzigings functie**.

    ![Portal](media/lab-data-flow-data-share/agg1.png)
1. Geef een naam op voor uw geaggregeerde trans formatie ' AggregateByPaymentType '. Selecteer `payment_type` als kolom groeperen op.

    ![Portal](media/lab-data-flow-data-share/agg2.png)
1. Ga naar het tabblad **aggregaties** . Hier geeft u twee aggregaties op:
    * De gemiddelde ritbedrag gegroepeerd op betalings type
    * De totale reis afstand gegroepeerd op betalings type

    Eerst maakt u de gemiddelde tarief expressie. In het tekstvak met de naam **een kolom toevoegen of selecteren**, voert u ' average_fare ' in.

    ![Portal](media/lab-data-flow-data-share/agg3.png)
1. Als u een expressie voor aggregatie wilt invoeren, klikt u op het blauwe vak expressie met het label **Enter**. Hiermee opent u de opbouw functie voor de data flow-expressie, een hulp programma dat wordt gebruikt om gegevens stroom expressies visueel te maken met behulp van invoer schema's, ingebouwde functies en bewerkingen, en door de gebruiker gedefinieerde para meters. Zie de [documentatie over Expression Builder](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder)voor meer informatie over de mogelijkheden van de opbouw functie voor expressies.

    Als u het gemiddelde tarief wilt ophalen, `avg()` gebruikt u de aggregatie functie `total_amount` om de kolom conversie samen te `toInteger()`voegen met een geheel getal met. In de taal van de gegevens stroom expressie wordt dit gedefinieerd `avg(toInteger(total_amount))`als. Klik op **opslaan en volt ooien** wanneer u klaar bent.

    ![Portal](media/lab-data-flow-data-share/agg4.png)
1. Klik op het plus pictogram naast om `average_fare`een extra aggregatie-expressie toe te voegen. Selecteer **kolom toevoegen**.

    ![Portal](media/lab-data-flow-data-share/agg5.png)
1. In het tekstvak met de naam **een kolom toevoegen of selecteren**, voert u ' total_trip_distance ' in. Zoals in de laatste stap, opent u de opbouw functie voor expressies om in de expressie in te voeren.

    Als u de totale reis afstand wilt ophalen, `sum()` gebruikt u de aggregatie functie `trip_distance` om de kolom conversie samen te `toInteger()`voegen met een geheel getal met. In de taal van de gegevens stroom expressie wordt dit gedefinieerd `sum(toInteger(trip_distance))`als. Klik op **opslaan en volt ooien** wanneer u klaar bent.

    ![Portal](media/lab-data-flow-data-share/agg6.png)
1. Test uw transformatie logica op het tabblad **voor beeld van gegevens** . Zoals u kunt zien, zijn er aanzienlijk minder rijen en kolommen dan eerder. Alleen de drie Group by-en aggregatie kolommen die in deze trans formatie zijn gedefinieerd, gaan verder met downstream. Aangezien er slechts vijf groepen betalings typen in het voor beeld zijn, worden er slechts vijf rijen gegenereerd.

    ![Portal](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Uw Azure Synapse Analytics-Sink configureren

1. Nu we onze transformatie logica hebben voltooid, kunnen we onze gegevens in een Azure Synapse Analytics-tabel opvangen. Voeg een Sink-trans formatie toe onder de **doel** sectie.

    ![Portal](media/lab-data-flow-data-share/sink1.png)
1. Noem uw Sink ' SQLDWSink '. Klik op **Nieuw** naast het veld Sink dataset om een nieuwe Azure Synapse Analytics-gegevensset te maken.

    ![Portal](media/lab-data-flow-data-share/sink2.png)

1. Selecteer de tegel **Azure Synapse Analytics (voorheen SQL DW)** en klik op door gaan.

    ![Portal](media/lab-data-flow-data-share/sink3.png)
1. Roep de gegevensset ' AggregatedTaxiData ' aan. Selecteer ' SQLDW ' als uw gekoppelde service. Selecteer **nieuwe tabel maken** en noem de nieuwe tabel dbo. AggregateTaxiData. Klik op OK wanneer u klaar bent

    ![Portal](media/lab-data-flow-data-share/sink4.png)
1. Ga naar het tabblad **instellingen** van de sink. Omdat we een nieuwe tabel maken, moeten we de **tabel opnieuw maken** selecteren onder tabel actie. Schakel **fase ring inschakelen**uit. Hiermee wordt aangegeven of u rij-voor-rij of in batch invoegt.

    ![Portal](media/lab-data-flow-data-share/sink5.png)

De gegevens stroom is gemaakt. Nu is het tijd om het uit te voeren in een pijplijn activiteit.

### <a name="debug-your-pipeline-end-to-end"></a>End-to-end van de pijp lijn debuggen

1. Ga terug naar het tabblad voor de **IngestAndTransformData** -pijp lijn. Let op het groene vak op de Kopieer activiteit ' IngestIntoADLS '. Sleep het naar de gegevens stroom activiteit ' JoinAndAggregateData '. Hiermee maakt u een ' op geslaagd ', waardoor de activiteit van de gegevens stroom alleen wordt uitgevoerd als de kopie is geslaagd.

    ![Portal](media/lab-data-flow-data-share/pipeline1.png)
1. Zoals we voor de Kopieer activiteit hebben gedaan, klikt u op **fouten opsporen** om een fout opsporing uit te voeren. Voor de uitvoering van debug wordt het actieve cluster voor fout opsporing gebruikt in plaats van een nieuw cluster te draaien. Het duurt ongeveer een minuut voordat deze pijp lijn is uitgevoerd.

    ![Portal](media/lab-data-flow-data-share/pipeline2.png)
1. Net als bij de Kopieer activiteit heeft de gegevens stroom een speciale bewakings weergave die toegankelijk is voor het bril-pictogram na voltooiing van de activiteit.

    ![Portal](media/lab-data-flow-data-share/pipeline3.png)
1. In de weer gave controle kunt u een grafiek voor vereenvoudigde gegevens stroom samen met de uitvoerings tijden en-rijen in elke uitvoerings fase bekijken. Als u goed wilt doen, moet u 49.999 rijen hebben samengevoegd tot vijf rijen in deze activiteit.

    ![Portal](media/lab-data-flow-data-share/pipeline4.png)
1. U kunt op een trans formatie klikken om aanvullende informatie te krijgen over de uitvoering, zoals het partitioneren van gegevens en nieuwe/bijgewerkte/Verwijderde kolommen.

    ![Portal](media/lab-data-flow-data-share/pipeline5.png)

U hebt nu het gedeelte data factory van dit lab voltooid. Publiceer uw resources als u deze wilt operationeel maken met triggers. U hebt een pijp lijn met de gegevens van Azure SQL Database naar Azure Data Lake Storage met behulp van de Kopieer activiteit uitgevoerd en vervolgens hebt u deze gegevens samengevoegd in een Azure Synapse-analyse. U kunt controleren of de gegevens zijn geschreven door te kijken naar het SQL Server zelf.

## <a name="share-data-using-azure-data-share"></a>Gegevens delen met Azure data share

In deze sectie leert u hoe u een nieuwe gegevens share instelt met behulp van de Azure Portal. Hiervoor moet u een nieuwe gegevens share maken die gegevens sets bevat van Azure Data Lake Store Gen2 en Azure SQL Data Warehouse. U gaat vervolgens een momentopname schema configureren, zodat de gegevens gebruikers een optie hebben voor het automatisch vernieuwen van de gegevens die met hen worden gedeeld. Vervolgens kunt u ontvangers uitnodigen voor uw gegevens share. 

Zodra u een gegevens share hebt gemaakt, schakelt u de gegevens in de andere *data consumer*in. Als gegevens verbruiker loopt u door de stroom van het accepteren van een gegevens share-uitnodiging, het configureren van de locatie waar de gegevens moeten worden ontvangen en het toewijzen van data sets aan verschillende opslag locaties. Vervolgens wordt een moment opname geactiveerd, waarmee de gegevens die met u worden gedeeld, worden gekopieerd naar de opgegeven bestemming. 

### <a name="sharing-data-data-provider-flow"></a>Gegevens delen (stroom van gegevens provider)

1. Open de Azure Portal in micro soft Edge of Google Chrome.

1. Met de zoek balk aan de bovenkant van de pagina zoekt u naar **gegevens shares**

    ![Portal](media/lab-data-flow-data-share/portal-ads.png)

1. Selecteer in de naam het gegevens share account met provider. Bijvoorbeeld **DataProvider0102**. 

1. Selecteer **beginnen met delen van uw gegevens** selecteren

    ![Beginnen met delen](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Selecteer **+ maken** om te beginnen met het configureren van de nieuwe gegevens share. 

1. Geef onder **share naam**een naam op voor uw keuze. Dit is de share naam die wordt weer gegeven door uw gegevens verbruiker. Zorg er dus voor dat u een beschrijvende naam krijgt, zoals TaxiData.

1. Onder **Beschrijving**, plaatst u een zin waarmee de inhoud van de gegevens share wordt beschreven. De gegevens share bevat wereld wijde taxi's-reis gegevens die zijn opgeslagen in een aantal winkels, waaronder Azure SQL Data Warehouse en Azure Data Lake Store. 

1. Geef onder **Gebruiksvoorwaarden**een set voor waarden op waaraan uw gegevens verbruiker moet voldoen. Enkele voor beelden zijn ' deze gegevens niet distribueren buiten uw organisatie ' of ' Ga naar juridische overeenkomst '. 

    ![Details delen](media/lab-data-flow-data-share/ads-details.png)

1. Selecteer **Doorgaan**. 

1. Selecteer **gegevens sets toevoegen** 

    ![Gegevensset toevoegen](media/lab-data-flow-data-share/add-dataset.png)

1. Selecteer **Azure SQL Data Warehouse** om een tabel te selecteren in de Azure SQL Data Warehouse waarvoor uw ADF-trans formaties zijn aangevoerd.

    ![Gegevensset toevoegen](media/lab-data-flow-data-share/add-dataset-sql.png)

> [!NOTE]
> Azure SQL Data Warehouse is nu bekend als Azure Synapse Analytics

1. U krijgt een script om uit te voeren voordat u kunt door gaan. Met het door gegeven script wordt een gebruiker gemaakt in de SQL database zodat de Azure data share MSI namens deze kan worden geverifieerd. 

> [!IMPORTANT]
> Voordat u het script uitvoert, moet u zichzelf instellen als de Active Directory beheerder voor de SQL Server. 

1. Open een nieuw tabblad en navigeer naar het Azure Portal. Kopieer het script dat is gegeven voor het maken van een gebruiker in de data base waarvan u gegevens wilt delen. Meld u aan bij de EDW-data base met behulp van query Explorer (preview) met behulp van AAD-verificatie. 

    U moet het script wijzigen zodat de gebruiker is gemaakt tussen vier Kante haken. Aanbieding
    
    create user [Data Provider-xxxx] uit externe aanmelding;  exec sp_addrolemember db_owner, [Data Provider-xxxx];
    
1. Ga terug naar de Azure-gegevens share waar u gegevens sets aan uw gegevens share hebt toegevoegd. 

1. Selecteer **edw** voor de SQL Data Warehouse en selecteer **AggregatedTaxiData** voor de tabel. 

1. Selecteer **gegevensset toevoegen**

    We hebben nu een SQL-tabel die deel uitmaakt van de gegevensset. Vervolgens voegen we extra gegevens sets toe van Azure Data Lake Store. 

1. Selecteer **gegevensset toevoegen** en selecteer **Azure data Lake Store Gen2**

    ![Gegevensset toevoegen](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Selecteer **volgende**

1. Vouw *wwtaxidata*uit. Vouw *Boston taxi data*uit. U ziet dat u kunt delen op bestands niveau. 

1. Selecteer de map *Boston taxi data* om de volledige map toe te voegen aan uw gegevens share. 

1. Selecteer **gegevens sets toevoegen**

1. Controleer de gegevens sets die zijn toegevoegd. U moet een SQL-tabel en een map ADLSGen2 toevoegen aan uw gegevens share. 

1. Selecteer **door gaan**

1. In dit scherm kunt u ontvangers toevoegen aan uw gegevens share. De ontvangers die u toevoegt, ontvangen uitnodigingen voor uw gegevens share. Voor het doel van dit lab moet u twee e-mail adressen toevoegen:

    1. Het e-mail adres van het Azure-abonnement dat u gebruikt. 

        ![Ontvangers toevoegen](media/lab-data-flow-data-share/add-recipients.png)

    1. Voeg deze toe aan de fictieve gegevens *janedoe@fabrikam.com*verbruiker met de naam.

1. In dit scherm kunt u een moment opname-instelling voor uw gegevens verbruiker configureren. Op die manier kunnen ze regel matige updates van uw gegevens ontvangen met een interval dat door u is gedefinieerd. 

1. Controleer het **schema voor moment opnamen** en configureer uw gegevens per uur vernieuwen met behulp van de vervolg keuzelijst *terugkeer patroon* .  

1. Selecteer **Maken**.

    U hebt nu een actieve gegevens share. Hiermee kunt u zien wat u ziet als een gegevens provider wanneer u een gegevens share maakt. 

1. Selecteer de gegevens share die u hebt gemaakt met de titel **Data Provider**. U kunt hiernaar navigeren door **verzonden shares** te selecteren in **gegevens share**. 

1. Klik op schema voor moment opnamen. U kunt het momentopname schema uitschakelen als u dat hebt gekozen. 

1. Selecteer vervolgens het tabblad **gegevens sets** . U kunt extra gegevens sets toevoegen aan deze data share nadat deze is gemaakt. 

1. Selecteer het tabblad **abonnementen delen** . Er zijn nog geen abonnementen voor delen omdat uw gegevens consument de uitnodiging nog niet heeft geaccepteerd.

1. Navigeer naar het tabblad **uitnodigingen** . Hier ziet u een lijst met openstaande uitnodiging (en). 

    ![Uitnodigingen in behandeling](media/lab-data-flow-data-share/pending-invites.png)

1. Selecteer de uitnodiging voor *janedoe@fabrikam.com*. Selecteer verwijderen. Als uw ontvanger de uitnodiging nog niet heeft geaccepteerd, kunnen deze niet meer worden uitgevoerd. 

1. Selecteer het tabblad **geschiedenis** . Er wordt nog niets weer gegeven, omdat uw gegevens consument de uitnodiging nog niet heeft geaccepteerd en een moment opname heeft geactiveerd. 

### <a name="receiving-data-data-consumer-flow"></a>Ontvangen van gegevens (gegevens verbruiker stroom)

Nu we onze gegevens share hebben bekeken, zijn we klaar om te scha kelen tussen de context en onze data consumer-hoed. 

U hebt nu een uitnodiging voor een Azure-gegevens share in uw postvak in ontvangen van Microsoft Azure. Start Outlook Web Access (outlook.com) en meld u aan met de referenties die zijn opgegeven voor uw Azure-abonnement.

Klik in het e-mail bericht dat u wilt ontvangen op ' uitnodiging weer geven > '. Op dit moment gaat u de ervaring van de gegevens verbruiker simuleren wanneer u een uitnodiging voor gegevens providers accepteert aan hun gegevens share. 

![E-mail uitnodiging](media/lab-data-flow-data-share/email-invite.png)

U wordt mogelijk gevraagd om een abonnement te selecteren. Zorg ervoor dat u het abonnement selecteert waarmee u bent aangemeld voor dit lab. 

1. Klik op de uitnodiging met de titel *Data Provider*. 

1. In dit scherm van de uitnodiging ziet u verschillende details over de gegevens share die u eerder hebt geconfigureerd als een gegevens provider. Bekijk de details en accepteer de gebruiksrecht overeenkomst indien van toepassing.

1. Selecteer het abonnement en de resource groep die al bestaan voor uw Lab. 

1. Selecteer voor **gegevens share account** **DataConsumer**. U kunt ook een nieuw gegevens share-account maken. 

1. Naast de **ontvangen share naam**ziet u de standaard share naam is de naam die is opgegeven door de gegevens provider. Geef de share een beschrijvende naam die de gegevens beschrijft die u op het punt staat te ontvangen, bijvoorbeeld **TaxiDataShare**.

    ![Uitnodiging geaccepteerd](media/lab-data-flow-data-share/consumer-accept.png)

1. U kunt ervoor kiezen om **nu te accepteren en te configureren** of om later te **accepteren en te configureren**. Als u ervoor kiest om nu te accepteren en te configureren, geeft u een opslag account op waarin alle gegevens moeten worden gekopieerd. Als u ervoor kiest om later te accepteren en te configureren, worden de gegevens sets in de share niet-toegewezen en moet u ze hand matig toewijzen. We zullen er later voor kiezen. 

1. Selecteer **accepteren en later configureren**. 

    Bij het configureren van deze optie wordt een share abonnement gemaakt, maar er zijn geen gegevens meer om te worden gelandd, omdat er geen bestemming is toegewezen. 

    Daarna gaan we gegevensset-toewijzingen voor de gegevens share configureren. 

1. Selecteer de ontvangen share (de naam die u in stap 5 hebt opgegeven).

    De **moment opname** van de trigger wordt grijs weer gegeven, maar de share is actief. 

1. Selecteer het tabblad **gegevens sets** . u ziet dat elke gegevensset niet-toegewezen is, wat betekent dat er geen bestemming is om gegevens te kopiëren naar. 

    ![niet-toegewezen gegevens sets](media/lab-data-flow-data-share/unmapped.png)

1. Selecteer de tabel SQL Data Warehouse en selecteer vervolgens **+ toewijzen aan doel**.

1. Selecteer aan de rechter kant van het scherm de vervolg keuzelijst **doel gegevens type** . 

    U kunt de SQL-gegevens toewijzen aan een breed scala aan gegevens archieven. In dit geval worden we toegewezen aan een Azure SQL Database.

    ![mapping](media/lab-data-flow-data-share/mapping-options.png)
    
    Beschrijving Selecteer **Azure data Lake Store Gen2** als het doel gegevens type. 
    
    Beschrijving Selecteer het abonnement, de resource groep en het opslag account waarmee u bent aangemeld. 
    
    Beschrijving U kunt ervoor kiezen om de gegevens in uw data Lake te ontvangen in de CSV-of Parquet-indeling. 

1. Selecteer Azure SQL Database bij **doel gegevens type**. 

1. Selecteer het abonnement, de resource groep en het opslag account waarmee u bent aangemeld. 

    ![toewijzen aan SQL](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Voordat u kunt door gaan, moet u een nieuwe gebruiker maken in de SQL Server door het script uit te voeren dat wordt meegeleverd. Kopieer eerst het script dat u hebt ontvangen naar het klem bord. 

1. Open een nieuw Azure Portal tabblad. Sluit uw bestaande tabblad niet, omdat u er een ogen blik van hoeft te doen. 

1. Ga op het tabblad Nieuw dat u hebt geopend naar **SQL-data bases**.

1. Selecteer de SQL database (er mag zich slechts één in uw abonnement bevinden). Zorg ervoor dat u de SQL Data Warehouse niet selecteert. 

1. **Query-editor selecteren (preview-versie)**

1. Gebruik AAD-verificatie om u aan te melden bij de query-editor. 

1. Voer de query uit die is opgenomen in de gegevens share (gekopieerd naar het klem bord in stap 14). 

    Met deze opdracht staat u toe dat de Azure data share-service beheerde identiteiten voor Azure-Services gebruikt om te verifiëren dat de SQL Server gegevens kan kopiëren. 

1. Ga terug naar het oorspronkelijke tabblad en selecteer **toewijzen aan doel**.

1. Selecteer vervolgens de map Azure Data Lake Gen2 die deel uitmaakt van de gegevensset en wijs deze toe aan een Azure Blob Storage-account. 

    ![storage](media/lab-data-flow-data-share/storage-map.png)

    Als alle gegevens sets zijn toegewezen, bent u nu klaar om gegevens te ontvangen van de gegevens provider. 

    ![verwezen](media/lab-data-flow-data-share/all-mapped.png)
    
1. Selecteer **Details**. 

    De **moment opname** van de trigger wordt niet meer grijs weer gegeven, omdat de gegevens share nu bestemmingen heeft waarnaar moet worden gekopieerd.

1. Selecteer trigger-moment opname-> volledige kopie. 

    ![trigger](media/lab-data-flow-data-share/trigger-full.png)

    Hiermee wordt begonnen met het kopiëren van gegevens naar het nieuwe gegevens share-account. In een praktijk scenario zijn deze gegevens afkomstig van derden. 

    Het duurt ongeveer 3-5 minuten voordat de gegevens worden weer gegeven. U kunt de voortgang controleren door te klikken op het tabblad **geschiedenis** . 

    Terwijl u wacht, gaat u naar de oorspronkelijke gegevens share (Data Provider) en bekijkt u de status van het tabblad **abonnementen delen** en **geschiedenis** . u ziet dat er nu een actief abonnement is en als gegevens provider, kunt u ook controleren wanneer de consument de gegevens heeft ontvangen die met hen zijn gedeeld. 

1. Ga terug naar de gegevens share van de gegevens verbruiker. Zodra de status van de trigger is geslaagd, gaat u naar de doel SQL database en data Lake om te zien dat de gegevens in de respectieve winkels zijn aangeland. 

Gefeliciteerd, u hebt het lab voltooid.


