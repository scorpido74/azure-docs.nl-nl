---
title: Gegevens transformeren met een Azure Data Factory beheerde VNet-toewijzings gegevens stroom
description: Deze zelf studie bevat stapsgewijze instructies voor het gebruik van Azure Data Factory om gegevens te transformeren met toewijzings gegevens stroom
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/19/2019
ms.openlocfilehash: 5515f6c4dfbc5d3c0e391373e763597d7fdc89ed
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531755"
---
# <a name="transform-data-securely-using-mapping-data-flows"></a>Gegevens veilig transformeren met toewijzing van gegevens stromen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Zie [Inleiding tot Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) als u niet bekend bent met Azure Data Factory.

In deze zelf studie gebruikt u de Azure Data Factory gebruikers interface (UX) om een pijp lijn te maken waarmee gegevens worden gekopieerd en getransformeerd **van een Azure data Lake Storage (ADLS) Gen2-bron naar een ADLS Gen2 Sink (zowel toegang verlenen aan geselecteerde netwerken)** met toewijzing van gegevens stroom in [Azure Data Factory beheerde Virtual Network](managed-virtual-network-private-endpoint.md). Het configuratie patroon in deze zelf studie kan worden uitgebreid bij het transformeren van gegevens met behulp van de toewijzings gegevens stroom.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
>
> * Een data factory maken.
> * Maak een pijp lijn met een gegevens stroom activiteit.
> * Bouw een toewijzings gegevens stroom met vier trans formaties.
> * De uitvoering van de pijplijn testen.
> * Een gegevens stroom activiteit bewaken

## <a name="prerequisites"></a>Vereisten
* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**. U gebruikt ADLS-opslag als *bron* -en *sink* -gegevens opslag. Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) om een account te maken. **Zorg ervoor dat het opslag account alleen toegang toestaat vanuit geselecteerde netwerken.** 

Het bestand dat u in deze zelf studie transformeert, is MoviesDB.csv, dat u [hier](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)kunt vinden. Als u het bestand wilt ophalen uit GitHub, kopieert u de inhoud naar een tekst editor van uw keuze om lokaal op te slaan als een CSV-bestand. Als u het bestand naar uw opslag account wilt uploaden, raadpleegt u [blobs uploaden met de Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). De voor beelden verwijzen naar een container met de naam ' Sample-Data '.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

In deze stap maakt u een data factory en opent u de Data Factory UX om een pijp lijn te maken in de data factory.

1. Open **Microsoft Edge** of **Google Chrome**. Momenteel wordt Data Factory gebruikers interface alleen ondersteund in de micro soft Edge-en Google Chrome-webbrowsers.
2. Selecteer in het linkermenu **Een resource maken** > **Analyse** > **Data Factory**.
3. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**.

   De naam van de Azure-gegevensfactory moet *wereldwijd uniek* zijn. Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. (Gebruik dan bijvoorbeeld uwnaamADFTutorialDataFactory). Zie [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

4. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
5. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 
6. Selecteer **V2** onder **Versie**.
7. Selecteer onder **Locatie** een locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. Gegevens archieven (bijvoorbeeld Azure Storage en SQL Database) en berekeningen (zoals Azure HDInsight) die door de data factory worden gebruikt, kunnen zich in andere regio's bevindt.

8. Selecteer **Maken**.

9. Als het maken is voltooid, ziet u de melding in het meldingencentrum. Selecteer **Naar resource gaan** om naar de pagina Data factory te gaan.
10. Selecteer de tegel **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Een Azure Integration Runtime maken in een door ADF beheerde Virtual Network
In deze stap maakt u een Azure Integration Runtime en schakelt u beheerde Virtual Network in.

1. Ga in de ADF-Portal naar **Manage hub** en klik op **New** om een nieuwe Azure Integration runtime te maken.
   ![Nieuwe Azure Integration Runtime maken](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Kies voor het maken van een **Azure** Integration runtime.
   ![Nieuwe Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Schakel **Virtual Network**in.
   ![Nieuwe Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Selecteer **Maken**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Een pijp lijn maken met een activiteit voor gegevens stromen

In deze stap maakt u een pijp lijn die een gegevens stroom activiteit bevat.

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**.

   ![Pijplijn maken](./media/doc-common-process/get-started-page.png)

1. Voer in het deel venster **Eigenschappen** voor de pijp lijn **TransformMovies** in als **naam** van de pijp lijn.
1. Schuif in de bovenste balk van de fabriek de schuif regelaar voor **fout opsporing van gegevens stromen** aan. In de modus voor fout opsporing kunt u de transformatie logica interactief testen op een live Spark-cluster. Gegevens stroom clusters nemen 5-7 minuten in beslag en gebruikers worden aanbevolen om debug eerst in te scha kelen als de ontwikkeling van gegevens stromen wordt gepland. Zie [debug mode (foutopsporingsmodus](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode)) voor meer informatie.

    ![Fout opsporing voor gegevens stromen](media/tutorial-data-flow-private/dataflow-debug.png)
1. Vouw in het deel venster **activiteiten** de accordeon **verplaatsen en transformeren** uit. Sleep de activiteit **gegevens stroom** van het deel venster naar het pijp lijn-canvas.

1. Selecteer in het pop-upvenster **gegevens stroom toevoegen** de optie **nieuwe gegevens stroom maken** en selecteer vervolgens **gegevens stroom toewijzen**. Klik op **OK** wanneer u klaar bent.

    ![Toewijzingsgegevensstroom](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Geef uw gegevensstroom **TransformMovies** een naam in het deel venster Eigenschappen.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Transformatie logica bouwen in het canvas voor gegevens stromen

Wanneer u de gegevens stroom hebt gemaakt, wordt u automatisch naar het canvas voor gegevens stromen verzonden. In deze stap bouwt u een gegevens stroom die de moviesDB.csv in ADLS-opslag neemt en de gemiddelde beoordeling van comedies van 1910 tot 2000 samenvoegt. Vervolgens schrijft u dit bestand terug naar de ADLS-opslag.

### <a name="add-the-source-transformation"></a>De bron transformatie toevoegen

In deze stap stelt u Azure Data Lake Storage Gen2 in als bron.

1. Voeg in het canvas gegevens stroom een bron toe door te klikken op het vak **bron toevoegen** .

1. Geef uw bron **MoviesDB**een naam. Klik op **Nieuw** om een nieuwe bron gegevensset te maken.

1. Kies **Azure data Lake Storage Gen2**. Klik op door gaan.

1. Kies **DelimitedText**. Klik op door gaan.

1. Geef uw gegevensset een naam **MoviesDB**. Kies in de vervolg keuzelijst gekoppelde service de optie **Nieuw**.

1. Typ in het scherm gekoppelde service maken de naam van uw ADLS Gen2-gekoppelde service **ADLSGen2** en geef uw verificatie methode op. Voer vervolgens uw verbindings referenties in. In deze zelf studie gebruiken we de account sleutel om verbinding te maken met het opslag account. 

1. Zorg ervoor dat u **interactieve ontwerp**functie inschakelt. Het kan ongeveer 1 minuut duren voordat deze is ingeschakeld.

    ![Interactief ontwerpen](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Selecteer **verbinding testen**, de fout moet mislukken omdat het opslag account geen toegang inschakelt zonder een persoonlijk eind punt te maken en goed te keuren. In het fout bericht wordt een koppeling weer gegeven om een **persoonlijk eind punt** te maken dat u kunt volgen om een beheerd persoonlijk eind punt te maken. *U kunt ook rechtstreeks naar het tabblad beheren gaan en de instructies in [deze sectie](#create-a-managed-private-endpoint) volgen om een beheerd privé-eind punt te maken.*

1. Houd het dialoog venster geopend en ga vervolgens naar het hierboven geselecteerde opslag account.

1. Volg de instructies in [deze sectie](#approval-of-a-private-link-in-storage-account) om de persoonlijke koppeling goed te keuren.

1. Ga terug naar het dialoog venster. **Test de verbinding** opnieuw en selecteer **maken** om de gekoppelde service te implementeren.

1. Wanneer u weer op het scherm gegevensset maken hebt geklikt, voert u in waar het bestand zich bevindt in het veld **bestandspad** . In deze zelf studie wordt het bestand moviesDB.csv bevindt zich in container-voorbeeld gegevens. Controleer de **eerste rij als koptekst**, terwijl het bestand kopteksten bevat. Selecteer **in verbinding/archief** om het header-schema rechtstreeks te importeren uit het bestand in de opslag. Klik op OK wanneer u klaar bent.

    ![Bronpad](media/tutorial-data-flow-private/source-file-path.png)

1. Als het cluster voor fout opsporing is gestart, gaat u naar het tabblad **voor beeld van gegevens** van de bron transformatie en klikt u op **vernieuwen** om een moment opname van de gegevens op te halen. U kunt gegevens voorbeeld gebruiken om te controleren of uw trans formatie correct is geconfigureerd.

    ![Voorbeeld van gegevens](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Een beheerd privé-eind punt maken

Als u tijdens het testen van de bovenstaande verbinding niet op de Hyper Link hebt geklikt, volgt u het volgende pad. Nu moet u een beheerd privé-eind punt maken dat u maakt verbinding met de gekoppelde service die hierboven is gemaakt.

1. Ga naar het tabblad beheren.
> [!NOTE]
> Het tabblad beheren is mogelijk niet beschikbaar voor alle exemplaren van data factory. Als u dit niet ziet, hebt u nog steeds toegang tot persoonlijke eind punten via het tabblad '**Auteur**'--> '**verbindingen**'--> '**persoonlijk eind punt**'
1. Ga naar de sectie beheerde privé-eind punten.
1. Selecteer **+ Nieuw** onder beheerde persoonlijke eind punten.

    ![Nieuw beheerd persoonlijk eind punt](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Selecteer de tegel Azure Data Lake Storage Gen2 in de lijst en selecteer **door gaan**.
1. Voer de naam in van het opslag account dat u hierboven hebt gemaakt.
1. Selecteer **Maken**.
1. U moet zien nadat u enkele seconden hebt gewacht dat de persoonlijke koppeling een goed keuring vereist.
1. Selecteer het persoonlijke eind punt dat u hierboven hebt gemaakt. U ziet een Hyper link waarmee u het persoonlijke eind punt kunt goed keuren op het niveau van het opslag account.

    ![Privé-eind punt beheren](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Goed keuring van een persoonlijke koppeling in het opslag account

1. Ga in het opslag account naar **verbindingen voor privé-eind punten** in het gedeelte instellingen.

1. Tik het persoonlijke eind punt dat u hierboven hebt gemaakt en selecteer **goed keuren**.

    ![Persoonlijk eind punt goed keuren](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Voeg een beschrijving toe en klik op **Ja**.
1. Ga terug naar de sectie **beheerde persoonlijke eind punten** van het tabblad **beheren** in azure Data Factory.
1. Het duurt ongeveer 1 minuut voordat de goed keuring voor uw persoonlijke eind punt wordt weer gegeven.

### <a name="add-the-filter-transformation"></a>De filter transformatie toevoegen

1. Klik naast het bron knooppunt op het canvas voor gegevens stromen op het plus pictogram om een nieuwe trans formatie toe te voegen. De eerste trans formatie die u toevoegt, is een **filter**.

    ![Filter toevoegen](media/tutorial-data-flow-private/add-filter.png)
1. Geef een naam op voor de filter transformatie **FilterYears**. Klik op het expressievak naast **filteren op** om de opbouw functie voor expressies te openen. Hier geeft u uw filter voorwaarde op.

    ![Jaren filteren](media/tutorial-data-flow-private/filter-years.png)
1. Met de opbouw functie voor de data flow-expressie kunt u interactief expressies bouwen voor gebruik in verschillende trans formaties. Expressies kunnen ingebouwde functies, kolommen uit het invoer schema en door de gebruiker gedefinieerde para meters bevatten. Zie de [opbouw functie voor data flow](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder)-expressies voor meer informatie over het maken van expressies.

    * In deze zelf studie wilt u de films van genre Comedy filteren die zijn uitgekomen tussen de jaren 1910 en 2000. Als jaar momenteel een teken reeks is, moet u deze converteren naar een geheel getal met behulp van de ```toInteger()``` functie. Gebruik de Opera tors groter dan of gelijk aan (>=) en kleiner dan of gelijk aan (<=) om te vergelijken met de letterlijke jaar waarden 1910 en 200-. Voeg deze expressies samen met de operator and (&&). De expressie wordt als volgt opgehaald:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Als u wilt weten welke films comedies zijn, kunt u de ```rlike()``` functie gebruiken om het patroon ' comedy ' te vinden in de kolom genres. De rlike-expressie samen voegen met de jaar vergelijking om het volgende te krijgen:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Als u een debug-cluster hebt geactiveerd, kunt u uw logica controleren door op **vernieuwen** te klikken om de expressie-uitvoer te bekijken in vergelijking met de gebruikte invoer. Er is meer dan één recht antwoord op hoe u deze logica kunt uitvoeren met behulp van de data flow-expressie taal.

        ![Filterexpressie](media/tutorial-data-flow-private/filter-expression.png)

    * Klik op **opslaan en volt ooien** zodra u klaar bent met de expressie.

1. Een **voor beeld** van een gegevens ophalen om te controleren of het filter goed werkt.

    ![Voorbeeld weergave van gegevens filteren](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>De cumulatieve trans formatie toevoegen

1. De volgende trans formatie die u toevoegt, is een **statistische** trans formatie onder **schema wijzigings functie**.

    ![Aggregatie toevoegen](media/tutorial-data-flow-private/add-aggregate.png)
1. Geef uw aggregatie transformatie **AggregateComedyRatings**een naam. Selecteer in het tabblad **groeperen op** **jaar** in de vervolg keuzelijst om de aggregaties te groeperen op het jaar dat de film is uitkwam.

    ![Statistische groep](media/tutorial-data-flow-private/group-by-year.png)
1. Ga naar het tabblad **aggregaties** . Geef in het vak links de cumulatieve kolom **AverageComedyRating**. Klik op het vak rechter expressie om de statistische expressie op te geven via de opbouw functie voor expressies.

    ![Statistische kolom naam](media/tutorial-data-flow-private/name-column.png)
1. Gebruik de functie aggregate om het gemiddelde van de kolom **classificatie**te berekenen ```avg()``` . Als **classificatie** is een teken reeks die ```avg()``` in een numerieke invoer neemt, moeten we de waarde converteren naar een getal via de ```toInteger()``` functie. De expressie ziet er als volgt uit:

    ```avg(toInteger(Rating))```

    Klik op **opslaan en volt ooien** wanneer u klaar bent.

    ![Samen voegen opslaan](media/tutorial-data-flow-private/save-aggregate.png)
1. Ga naar het tabblad voor **beeld van gegevens** om de trans formatie-uitvoer weer te geven. U ziet slechts twee kolommen, **jaar** en **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>De Sink-trans formatie toevoegen

1. Vervolgens wilt u een **sink** -trans formatie toevoegen onder **bestemming**.

    ![Sink toevoegen](media/tutorial-data-flow-private/add-sink.png)
1. Geef **uw Sink een naam.** Klik op **Nieuw** om uw Sink-gegevensset te maken.

    ![Sink maken](media/tutorial-data-flow-private/create-sink.png)
1. Kies op de pagina nieuwe gegevensset de optie **Azure data Lake Storage Gen2**. Klik op door gaan.

1. Kies op de pagina indeling selecteren de optie **DelimitedText**. Klik op door gaan.

1. Geef uw Sink-gegevensset een naam **MoviesSink**. Kies voor gekoppelde service dezelfde ADLSGen2 gekoppelde service die u hebt gemaakt voor de bron transformatie. Voer een uitvoermap in om uw gegevens naar te schrijven. In deze zelf studie schrijven we naar map ' uitvoer ' in container ' voor beeld-gegevens '. De map hoeft niet vooraf te bestaan en kan dynamisch worden gemaakt. Stel de **eerste rij als koptekst** in op True en selecteer **geen** voor het **import schema**. Klik op OK.

    ![Sink-pad](media/tutorial-data-flow-private/sink-file-path.png)

Nu bent u klaar met het bouwen van uw gegevens stroom. U kunt deze nu uitvoeren in de pijp lijn.

## <a name="running-and-monitoring-the-data-flow"></a>De gegevens stroom wordt uitgevoerd en bewaakt

U kunt fouten opsporen in een pijp lijn voordat u deze publiceert. In deze stap gaat u een debug-uitvoering van de gegevensstroom pijplijn activeren. Terwijl voor beeld van de gegevens geen gegevens worden geschreven, worden gegevens naar de Sink-bestemming geschreven.

1. Ga naar het pijp lijn-canvas. Klik op **fout opsporing** om een uitvoering van de fout opsporing te activeren.

1. Bij het opsporen van pijp lijn voor gegevens stroom activiteiten wordt het actieve debug-cluster gebruikt, maar het kan wel ten minste een minuut duren om te initialiseren. U kunt de voortgang volgen via het tabblad **uitvoer** . Zodra de uitvoering is voltooid, klikt u op het pictogram bril voor details van de uitvoering.

1. Op de pagina Details ziet u het aantal rijen en tijd dat aan elke transformatie stap is besteed.

    ![Bewakings uitvoering](media/tutorial-data-flow-private/run-details.png)
1. Klik op een trans formatie om gedetailleerde informatie over de kolommen en het partitioneren van de gegevens op te halen.

Als u deze zelf studie correct hebt gevolgd, moet u 83 rijen en 2 kolommen in uw map Sink hebben geschreven. U kunt controleren of de gegevens correct zijn door uw Blob-opslag te controleren.

## <a name="summary"></a>Samenvatting

In deze zelf studie gebruikt u de Azure Data Factory gebruikers interface (UX) om een pijp lijn te maken waarmee gegevens **van een Azure data Lake Storage (ADLS) Gen2-bron worden gekopieerd en getransformeerd naar een ADLS Gen2 Sink (zowel toegang verlenen aan geselecteerde netwerken)** met toewijzing van gegevens stroom in [Azure Data Factory beheerde Virtual Network](managed-virtual-network-private-endpoint.md).
