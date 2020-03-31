---
title: Gegevens transformeren met behulp van een toewijzingsgegevensstroom
description: Deze zelfstudie bevat stapsgewijze instructies voor het gebruik van Azure Data Factory om gegevens te transformeren met de gegevensstroom van toewijzingen
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: e6ca8007a96cc63b51b4f79b69029cbf0799e71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979181"
---
# <a name="transform-data-using-mapping-data-flows"></a>Gegevens transformeren met behulp van toewijzingsgegevensstromen

Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie gebruikt u de gebruikersinterface van Azure Data Factory (UX) om een pijplijn te maken die gegevens kopieert en transformeert van een ADLS-bron (Azure Data Lake Storage) naar een ADLS Gen2-sink met behulp van toewijzingsgegevensstroom. Het configuratiepatroon in deze zelfstudie kan worden uitgebreid bij het transformeren van gegevens met behulp van kaartgegevensstroom

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Maak een pijplijn met een activiteit Gegevensstroom.
> * Bouw een kaartgegevensstroom met vier transformaties.
> * De uitvoering van de pijplijn testen.
> * Een gegevensstroomactiviteit controleren

## <a name="prerequisites"></a>Vereisten
* **Azure-abonnement**. Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* **Azure-opslagaccount**. U gebruikt ADLS-opslag als *bron-* en *sinkgegevensopslag.* Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken.

Het bestand dat we transformeren in deze tutorial is MoviesDB.csv, die [hier](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)kan worden gevonden. Als u het bestand wilt ophalen bij GitHub, kopieert u de inhoud naar een teksteditor van uw keuze om lokaal op te slaan als CSV-bestand. Zie [Blobs uploaden met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)als u het bestand wilt uploaden naar uw opslagaccount. De voorbeelden verwijzen naar een container met de naam 'sample-data'.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

In deze stap maakt u een gegevensfabriek en opent u de UX van Data Factory om een pijplijn in de gegevensfabriek te maken.

1. Open **Microsoft Edge** of **Google Chrome**. Momenteel wordt de gebruikersinterface van Data Factory alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
2. Selecteer links in het menu Een resource > **Analytics** > **Data Factory maken:** **Create a resource**

   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in bij **Naam**.

   De naam van de Azure-gegevensfabriek moet *wereldwijd uniek*zijn. Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. (bijvoorbeeld yournameADFTutorialDataFactory). Zie [Data Factory naming rules](naming-rules.md) (Naamgevingsregels Data Factory) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

     ![Nieuwe data factory](./media/doc-common-process/name-not-available-error.png)
4. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
5. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken**en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuw maken**en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 
6. Selecteer **V2** onder **Versie**.
7. Selecteer onder **Locatie** een locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. Gegevensopslag (bijvoorbeeld Azure Storage en SQL Database) en computes (bijvoorbeeld Azure HDInsight) die door de gegevensfabriek worden gebruikt, kunnen zich in andere regio's bevinden.
8. Selecteer **Maken**.
9. Nadat de creatie is voltooid, ziet u de melding in het berichtencentrum. Selecteer **Ga naar resource om** naar de fabriekspagina Gegevens te navigeren.
10. Selecteer de tegel **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Een pijplijn maken met een activiteit gegevensstroom

In deze stap maakt u een pijplijn die een activiteit Gegevensstroom bevat.

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**.

   ![Pijplijn maken](./media/doc-common-process/get-started-page.png)

1. Voer op het tabblad **Algemeen** voor de pijplijn **TransformMovies** in voor **Naam** van de pijplijn.
1. Schuif in de bovenste balk van de fabriek de schuifregelaar **Foutopsporingsgegevens van gegevensstroom** in. De foutopsporingsmodus maakt het mogelijk om transformatielogica interactief te testen op een live Spark-cluster. Data Flow clusters nemen 5-7 minuten om op te warmen en gebruikers wordt aangeraden om foutopsporing eerst in te schakelen als ze van plan zijn om data flow ontwikkeling te doen. Zie [Foutopsporingsmodus](concepts-data-flow-debug-mode.md)voor meer informatie .

    ![Activiteit gegevensstroom](media/tutorial-data-flow/dataflow1.png)
1. Vouw in het deelvenster **Activiteiten** de accordeon **Verplaatsen en Transformeren uit.** Sleep en drop de **activiteit Gegevensstroom** van het deelvenster naar het pijplijncanvas.

    ![Activiteit gegevensstroom](media/tutorial-data-flow/activity1.png)
1. Selecteer in de pop-up **Gegevensstroom toevoegen** de optie **Nieuwe gegevensstroom maken** en geef vervolgens de naam van uw gegevensstroom **TransformMovies**. Klik op Voltooien wanneer u klaar bent.

    ![Activiteit gegevensstroom](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Transformatielogica bouwen in het canvas voor gegevensstromen

Zodra u uw gegevensstroom hebt gemaakt, wordt u automatisch naar het canvas van de gegevensstroom verzonden. In deze stap bouwt u een gegevensstroom die de moviesDB.csv in ADLS-opslag neemt en de gemiddelde beoordeling van komedies van 1910 tot 2000 verzamelt. U schrijft dit bestand vervolgens terug naar de ADLS-opslag.

1. Voeg in het canvas van de gegevensstroom een bron toe door op het vak **Bron toevoegen** te klikken.

    ![Gegevensstroomcanvas](media/tutorial-data-flow/dataflow2.png)
1. Noem uw bron **MoviesDB**. Klik op **Nieuw** om een nieuwe brongegevensset te maken.

    ![Gegevensstroomcanvas](media/tutorial-data-flow/dataflow3.png)
1. Kies **Azure Data Lake Storage Gen2**. Klik op Doorgaan.

    ![Gegevensset](media/tutorial-data-flow/dataset1.png)
1. Kies **DelimitedText**. Klik op Doorgaan.

    ![Gegevensset](media/tutorial-data-flow/dataset2.png)
1. Geef uw gegevensset **MoviesDB een**naam . **Kies**nieuw in de vervolgkeuzelijst gekoppelde service .

    ![Gegevensset](media/tutorial-data-flow/dataset3.png)
1. Geef in het scherm voor het maken van gekoppelde service van de gekoppelde service **ADLSGen2** de naam en geef uw verificatiemethode op. Voer vervolgens uw verbindingsreferenties in. In deze zelfstudie gebruiken we accountsleutel om verbinding te maken met ons opslagaccount. U op **Verbinding testen** klikken om te controleren of uw referenties correct zijn ingevoerd. Klik op Maken als u klaar bent.

    ![Gekoppelde service](media/tutorial-data-flow/ls1.png)
1. Als u weer in het scherm voor het maken van gegevenssets bent, voert u in waar uw bestand zich bevindt onder het veld **Bestandspad.** In deze zelfstudie bevindt het bestand moviesDB.csv zich in containersamplegegevens. Aangezien het bestand kopteksten heeft, controleert u **Eerste rij als koptekst**. Selecteer **Uit verbinding/winkel** om het headerschema rechtstreeks uit het opslagbestand te importeren. Klik op OK als u klaar bent.

    ![Gegevenssets](media/tutorial-data-flow/dataset4.png)
1. Als uw foutopsporingscluster is gestart, gaat u naar het tabblad **Gegevensvoorbeeld** van de brontransformatie en klikt u op **Vernieuwen** om een momentopname van de gegevens te krijgen. U gegevensvoorbeeld gebruiken om te controleren of uw transformatie correct is geconfigureerd.

    ![Gegevensstroomcanvas](media/tutorial-data-flow/dataflow4.png)
1. Klik naast het bronknooppunt op het canvas van de gegevensstroom op het pluspictogram om een nieuwe transformatie toe te voegen. De eerste transformatie die u toevoegt, is een **filter.**

    ![Gegevensstroomcanvas](media/tutorial-data-flow/dataflow5.png)
1. Geef uw filtertransformatie **FilterJaren een**naam . Klik op het expressievak naast **Filter op** om de expressiebouwer te openen. Hier geeft u de filterconditie op.

    ![Filteren](media/tutorial-data-flow/filter1.png)
1. Met de opbouw van gegevensstromenexpressie u interactief expressies bouwen om te gebruiken in verschillende transformaties. Expressies kunnen ingebouwde functies, kolommen uit het invoerschema en door de gebruiker gedefinieerde parameters bevatten. Zie Opbouwvan expressies gegevensstroom voor meer informatie over het maken van [expressies.](concepts-data-flow-expression-builder.md)

    In deze tutorial wilt u films filteren van genrekomedie die uitkwam tussen de jaren 1910 en 2000. Aangezien het jaar momenteel een tekenreeks is, moet ```toInteger()``` u deze converteren naar een geheel getal met behulp van de functie. Gebruik de groter dan of gelijken aan (>=) en minder dan of gelijk aan (<=) operators om te vergelijken met letterlijke jaarwaarden 1910 en 200-. Union deze uitdrukkingen samen met de en (&&) operator. De uitdrukking komt uit als:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Om te vinden welke films komedies ```rlike()``` zijn, u de functie gebruiken om patroon 'Comedy' te vinden in de kolomgenres. Unie de rlike uitdrukking met de jaarvergelijking om te krijgen:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Als u een foutopsporingscluster actief hebt, u uw logica verifiëren door op **Vernieuwen** te klikken om de expressieuitvoer te zien in vergelijking met de gebruikte ingangen. Er is meer dan één juist antwoord op hoe u deze logica verwezenlijken met behulp van de taal van de gegevensstroomexpressie.

    ![Filteren](media/tutorial-data-flow/filter2.png)

    Klik **op Opslaan en voltooien** zodra u klaar bent met uw expressie.

1. Haal een **gegevensvoorbeeld op** om te controleren of het filter correct werkt.

    ![Filteren](media/tutorial-data-flow/filter3.png)
1. De volgende transformatie die u toevoegt, is een **totale** transformatie onder **Schemamodifier**.

    ![Samenvoegen](media/tutorial-data-flow/agg1.png)
1. Geef een naam aan uw geaggregeerde transformatie **AggregateComedyRatings**. Selecteer **in het** tabblad Groeperen **op jaar** uit de vervolgkeuzelijst om de aggregaties te groeperen op het jaar dat de film uitkwam.

    ![Samenvoegen](media/tutorial-data-flow/agg2.png)
1. Ga naar het tabblad **Aggregaten.** Geef in het linkertekstvak de naam van de aggregaatkolom **AverageComedyRating**. Klik op het juiste expressievak om de samengevoegde expressie in te voeren via de expressiebouwer.

    ![Samenvoegen](media/tutorial-data-flow/agg3.png)
1. Als u het gemiddelde van ```avg()``` **kolomclassificatie**wilt krijgen, gebruikt u de samengevoegde functie. Aangezien **Rating** een ```avg()``` tekenreeks is en een numerieke invoer in zich ```toInteger()``` opneemt, moeten we de waarde via de functie converteren naar een getal. Dit is expressie ziet eruit als:

    ```avg(toInteger(Rating))```

    Klik **op Opslaan en voltooien** wanneer u klaar bent.

    ![Samenvoegen](media/tutorial-data-flow/agg4.png)
1. Ga naar het tabblad **Gegevensvoorbeeld** om de transformatie-uitvoer weer te geven. Let op slechts twee kolommen zijn er, **jaar** en **AverageComedyRating**.

    ![Samenvoegen](media/tutorial-data-flow/agg3.png)
1. Vervolgens wilt u een **sinktransformatie** toevoegen onder **Bestemming**.

    ![Sink](media/tutorial-data-flow/sink1.png)
1. Noem je gootsteen **gootsteen**. Klik **op Nieuw** om de sink-gegevensset te maken.

    ![Sink](media/tutorial-data-flow/sink2.png)
1. Kies **Azure Data Lake Storage Gen2**. Klik op Doorgaan.

    ![Gegevensset](media/tutorial-data-flow/dataset1.png)
1. Kies **DelimitedText**. Klik op Doorgaan.

    ![Gegevensset](media/tutorial-data-flow/dataset2.png)
1. Geef uw sinkdataset **MoviesSink een**naam. Kies voor gekoppelde service de ADLS gen2-gekoppelde service die u in stap 6 hebt gemaakt. Voer een uitvoermap in om uw gegevens naar te schrijven. In deze zelfstudie schrijven we naar map 'output' in container 'sample-data'. De map hoeft niet van tevoren te bestaan en kan dynamisch worden gemaakt. Eerste **rij als koptekst instellen** als true en selecteer **Geen** voor **schema importeren**. Klik op Voltooien.

    ![Sink](media/tutorial-data-flow/sink3.png)

Nu bent u klaar met het bouwen van uw gegevensstroom. Je bent klaar om het in je pijplijn te laten lopen.

## <a name="running-and-monitoring-the-data-flow"></a>De gegevensstroom uitvoeren en bewaken

U een pijplijn debuggen voordat u deze publiceert. In deze stap activeert u een foutopsporingsrun van de pijplijn voor gegevensstromen. Terwijl data preview geen gegevens schrijft, wordt er een foutopsporing uitgevoerd, die gegevens naar uw gootsteenbestemming schrijft.

1. Ga naar het pijplijncanvas. Klik op **Foutopsporing** om een foutopsporingsrun te activeren.

    ![Pijplijn](media/tutorial-data-flow/pipeline1.png)
1. Pipeline debug of Data Flow activities uses the active debug cluster but still take at least a minute to initialize. U de voortgang volgen via het tabblad **Uitvoer.** Zodra de run succesvol is, klikt u op het pictogram van de bril om het controlevenster te openen.

    ![Pijplijn](media/tutorial-data-flow/pipeline2.png)
1. In het bewakingsvenster ziet u het aantal rijen en de tijd die in elke transformatiestap is doorgebracht.

    ![Bewaking](media/tutorial-data-flow/pipeline3.png)
1. Klik op een transformatie voor gedetailleerde informatie over de kolommen en het partitioneren van de gegevens.

    ![Bewaking](media/tutorial-data-flow/pipeline4.png)

Als u deze zelfstudie correct hebt gevolgd, moet u 83 rijen en 2 kolommen in uw gootsteenmap hebben geschreven. U controleren of de gegevens correct zijn door de blobopslag te controleren.

## <a name="next-steps"></a>Volgende stappen

De pijplijn in deze zelfstudie voert een gegevensstroom uit die de gemiddelde beoordeling van komedies van 1910 tot 2000 samenvoegt en de gegevens naar ADLS schrijft. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Maak een pijplijn met een activiteit Gegevensstroom.
> * Bouw een kaartgegevensstroom met vier transformaties.
> * De uitvoering van de pijplijn testen.
> * Een gegevensstroomactiviteit controleren

Meer informatie over de taal van de [gegevensstroomexpressie](data-flow-expression-functions.md).
