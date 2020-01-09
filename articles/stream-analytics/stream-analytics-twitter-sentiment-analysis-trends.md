---
title: Realtime Twitter-sentiment analyse met Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u Stream Analytics gebruikt voor realtime Twitter-sentiment analyse. Stapsgewijze richt lijnen voor het genereren van gebeurtenissen naar gegevens op een live dash board.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: f3ab21d55b7d59bb58760bfba452b4ea2d103496
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369895"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Realtime Twitter-sentiment analyse in Azure Stream Analytics

Meer informatie over het bouwen van een sentiment-analyse oplossing voor sociale media-analyses door Realtime Twitter-gebeurtenissen in te brengen in azure Event Hubs. Schrijf vervolgens een Azure Stream Analytics query om de gegevens te analyseren en de resultaten op te slaan voor later gebruik of maak een [Power bi](https://powerbi.com/) -dash board om inzicht in realtime te bieden.

Hulpprogram ma's voor sociale media analyse helpen organisaties bij het begrijpen van trending-onderwerpen. Trending onderwerpen zijn onderwerpen en gedragingen die een groot aantal posts op sociale media hebben. Sentiment analyse, ook wel *opinie*analyse genoemd, maakt gebruik van hulpprogram ma's voor de analyse van sociale media om houding te bepalen voor een product of een idee. 

Realtime-trend analyse van Twitter is een goed voor beeld van een analyse programma omdat u met het hashtag-abonnements model kunt Luis teren naar specifieke sleutel woorden (Hashtags) en sentiment analyse van de feed ontwikkelt.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: analyse van sociale media-sentiment in realtime

Een bedrijf met een website voor nieuws media is geïnteresseerd in het verkrijgen van een voor deel van zijn concurrenten met behulp van site-inhoud die direct relevant is voor de lezers. Het bedrijf maakt gebruik van sociale media-analyses op onderwerpen die relevant zijn voor lezers door real-time sentiment analyse van Twitter-gegevens uit te voeren.

Voor het identificeren van trending onderwerpen in realtime op Twitter, heeft het bedrijf een real-time analyse nodig over het tweet-volume en de sentiment voor belangrijkste onderwerpen.

## <a name="prerequisites"></a>Vereisten
In deze hand leiding kunt u een client toepassing gebruiken die verbinding maakt met Twitter en zoekt naar tweets met bepaalde Hashtags (die u kunt instellen). Als u de toepassing wilt uitvoeren en de tweets wilt analyseren met behulp van Azure streaming Analytics, hebt u het volgende nodig:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan.
* Een [Twitter](https://twitter.com) -account.
* De TwitterWPFClient-toepassing, die de Twitter-feed leest. Als u deze toepassing wilt downloaden, moet u het bestand [TwitterWPFClient. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) downloaden van github en vervolgens het pakket uitpakken naar een map op uw computer. Als u de bron code wilt zien en de toepassing in een fout opsporingsprogramma wilt uitvoeren, kunt u de bron code ophalen van [github](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Een Event Hub voor het invoeren van streaming Analytics maken

De voorbeeld toepassing genereert gebeurtenissen en duwt deze naar een Azure-Event Hub. Azure Event hubs is de voorkeurs methode voor het opnemen van gebeurtenissen voor Stream Analytics. Zie de [documentatie van Azure Event hubs](../event-hubs/event-hubs-what-is-event-hubs.md)voor meer informatie.

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Een Event Hub naam ruimte en Event Hub maken
Maak een Event Hub naam ruimte en voeg vervolgens een Event Hub aan die naam ruimte toe. Event hub-naam ruimten worden gebruikt om gerelateerde exemplaren van de gebeurtenis-bus logisch te groeperen. 

1. Meld u aan bij de Azure Portal en klik op **een resource maken** > **Internet of Things** > **Event hub**. 

2. Voer in de Blade **naam ruimte maken** een naam van een naam ruimte in, zoals `<yourname>-socialtwitter-eh-ns`. U kunt een wille keurige naam voor de naam ruimte gebruiken, maar de naam moet geldig zijn voor een URL en moet uniek zijn binnen Azure. 
    
3. Selecteer een abonnement en maak of kies een resource groep en klik vervolgens op **maken**. 

    ![Een Event Hub-naamruimte maken](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Wanneer de implementatie van de naam ruimte is voltooid, zoekt u de Event Hub naam ruimte in de lijst met Azure-resources. 

5. Klik op de nieuwe naam ruimte en klik op de Blade naam ruimte op **+&nbsp;Event hub**. 

    ![De knop Event hub toevoegen voor het maken van een nieuwe Event Hub](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Geef een naam op voor het nieuwe Event Hub `socialtwitter-eh`. U kunt ook een andere naam gebruiken. Als u dat wel doet, noteert u deze, omdat u de naam later nodig hebt. U hoeft geen andere opties voor de Event Hub in te stellen.

    ![Blade voor het maken van een nieuwe Event Hub](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Klik op **Maken**.


### <a name="grant-access-to-the-event-hub"></a>Toegang verlenen tot de Event Hub

Voordat een proces gegevens naar een Event Hub kan verzenden, moet de Event Hub een beleid hebben waarmee de juiste toegang is toegestaan. Het toegangsbeleid genereert een verbindingsreeks die autorisatiegegevens bevat.

1.  Klik in de Blade gebeurtenis naam ruimte op **Event hubs** en klik vervolgens op de naam van uw nieuwe event hub.

2.  Klik op de Blade Event Hub op **beleid voor gedeelde toegang** en klik vervolgens op **+&nbsp;toevoegen**.

    >[!NOTE]
    >Zorg ervoor dat u werkt met de Event Hub, niet de Event Hub naam ruimte.

3.  Voeg een beleid toe met de naam `socialtwitter-access` en voor **claim**, selecteer **beheren**.

    ![Blade voor het maken van een nieuw Event Hub-toegangs beleid](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Klik op **Maken**.

5.  Nadat het beleid is geïmplementeerd, klikt u erop in de lijst met beleid voor gedeelde toegang.

6.  Zoek het vak **VERBINDINGS reeks met de naam primaire sleutel** en klik op de knop kopiëren naast het Connection String. 
    
    ![De primaire connection string sleutel van het toegangs beleid kopiëren](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Plak de verbindingsreeks in een teksteditor. U hebt deze connection string nodig voor de volgende sectie, nadat u enkele kleine wijzigingen hebt aangebracht.

    De connection string ziet er als volgt uit:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    U ziet dat de connection string meerdere sleutel-waardeparen bevat, gescheiden door punt komma's: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`en `EntityPath`.  

    > [!NOTE]
    > Voor de beveiliging zijn delen van de connection string in het voor beeld verwijderd.

8.  Verwijder in de tekst editor het `EntityPath` paar uit de connection string (Vergeet niet om de punt komma die eraan voorafgaat te verwijderen). Wanneer u klaar bent, ziet de connection string er als volgt uit:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>De Twitter-client toepassing configureren en starten
De client toepassing ontvangt Tweet-gebeurtenissen rechtstreeks van Twitter. Om dit te doen, heeft het toestemming nodig om de Twitter streaming Api's aan te roepen. Als u deze machtiging wilt configureren, maakt u een toepassing in Twitter, waarmee unieke referenties worden gegenereerd (zoals een OAuth-token). U kunt vervolgens de client toepassing configureren voor het gebruik van deze referenties bij het maken van API-aanroepen. 

### <a name="create-a-twitter-application"></a>Een Twitter-toepassing maken
Als u nog geen Twitter-toepassing hebt die u voor deze hand leiding kunt gebruiken, kunt u er een maken. U moet al een Twitter-account hebben.

> [!NOTE]
> Het exacte proces in Twitter voor het maken van een toepassing en het ophalen van de sleutels, geheimen en token kan veranderen. Als deze instructies niet overeenkomen met wat u ziet op de Twitter-site, raadpleegt u de Twitter-ontwikkelaars documentatie.

1. Ga in een webbrowser naar [Twitter voor ontwikkel aars](https://developer.twitter.com/en/apps)en selecteer **een app maken**. Mogelijk wordt er een bericht weer gegeven met de mede deling dat u moet aanvragen voor een Twitter-ontwikkelaars account. U kunt dit gewoon doen, en nadat uw toepassing is goedgekeurd, moet u een bevestigings-e-mail krijgen. Het kan enkele dagen duren voordat een ontwikkelaars account wordt goedgekeurd.

   ![Bevestiging van Twitter-ontwikkelaars account](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-dev-confirmation.png "Bevestiging van Twitter-ontwikkelaars account")

   ![Details van Twitter-toepassing](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Details van Twitter-toepassing")

2. Voer op de pagina **Create an application** de gegevens voor de nieuwe app in en selecteer **Create your Twitter application**.

   ![Details van Twitter-toepassing](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Details van Twitter-toepassing")

3. Selecteer op de pagina toepassing het tabblad **sleutels en tokens** en kopieer de waarden voor de **CONSUMer-API-sleutel** en de **geheime sleutel**van de consument-API. Selecteer ook **maken** onder **toegangs token en geheim toegangs token** om de toegangs tokens te genereren. Kopieer de waarden voor **Access Token** en **Access Token Secret**.

    ![Details van Twitter-toepassing](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-app-key-secret.png "Details van Twitter-toepassing")

Sla de waarden op die u hebt opgehaald voor de Twitter-toepassing. U hebt de waarden later in de instructies.

>[!NOTE]
>De sleutels en geheimen voor de Twitter-toepassing bieden toegang tot uw Twitter-account. Deze informatie wordt als vertrouwelijk beschouwd, op dezelfde manier als uw Twitter-wacht woord. U kunt deze informatie bijvoorbeeld niet insluiten in een toepassing die u aan anderen verstrekt. 


### <a name="configure-the-client-application"></a>De client toepassing configureren
We hebben een client toepassing gemaakt die verbinding maakt met Twitter-gegevens met behulp [van de streaming-api's van Twitter](https://dev.twitter.com/streaming/overview) voor het verzamelen van Tweet-gebeurtenissen over een specifieke set met onderwerpen. De toepassing maakt gebruik van het open source-hulp programma [Sentiment140](http://help.sentiment140.com/) , waarmee de volgende sentiment-waarde wordt toegewezen aan elke Tweet:

* 0 = negatief
* 2 = neutraal
* 4 = positief

Nadat aan de Tweet-gebeurtenissen een sentiment-waarde is toegewezen, worden deze gepusht naar de Event Hub die u eerder hebt gemaakt.

Voordat de toepassing wordt uitgevoerd, hebt u bepaalde gegevens van u nodig, zoals de Twitter-sleutels en de Event Hub connection string. U kunt de configuratie-informatie op de volgende manieren opgeven:

* Voer de toepassing uit en gebruik vervolgens de gebruikers interface van de toepassing om de sleutels, geheimen en connection string in te voeren. Als u dit doet, worden de configuratie gegevens gebruikt voor uw huidige sessie, maar deze worden niet opgeslagen.
* Bewerk het. config-bestand van de toepassing en stel de waarden daar in. Deze aanpak persistenteert de configuratie gegevens, maar dit betekent ook dat deze mogelijk gevoelige informatie wordt opgeslagen in tekst zonder opmaak op uw computer.

De volgende procedure documenteert beide benaderingen. 

1. Zorg ervoor dat u de [TwitterWPFClient. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) -toepassing hebt gedownload en uitgepakt, zoals vermeld in de vereisten.

2. Als u de waarden wilt instellen tijdens runtime (en alleen voor de huidige sessie), voert u de `TwitterWPFClient.exe`-toepassing uit. Wanneer u door de toepassing wordt gevraagd, voert u de volgende waarden in:

    * De Twitter-consument sleutel (API-sleutel).
    * Het Twitter-consument geheim (API-geheim).
    * Het Twitter-toegangs token.
    * Het geheim voor het Twitter-toegangs token.
    * De connection string informatie die u eerder hebt opgeslagen. Zorg ervoor dat u de connection string gebruikt waarvan u het `EntityPath` sleutel-waardepaar hebt verwijderd.
    * De Twitter-tref woorden waarvoor u sentiment wilt bepalen.

   ![TwitterWpfClient toepassing, met verborgen instellingen](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Als u de waarden permanent wilt instellen, gebruikt u een tekst editor om het bestand TwitterWpfClient. exe. config te openen. Ga vervolgens als volgt te werk in het element `<appSettings>`:

   * `oauth_consumer_key` instellen op de Twitter-consument sleutel (API-sleutel). 
   * Stel `oauth_consumer_secret` in op het Twitter-consument geheim (API-geheim).
   * Stel `oauth_token` in op het Twitter-toegangs token.
   * Stel `oauth_token_secret` in voor het geheim voor het Twitter-toegangs token.

     Ga later in het `<appSettings>`-element als volgt te wijzigen:

   * Stel `EventHubName` in op de naam van het Event Hub (dat wil zeggen, de waarde van het pad van de entiteit).
   * Stel `EventHubNameConnectionString` in op de connection string. Zorg ervoor dat u de connection string gebruikt waarvan u het `EntityPath` sleutel-waardepaar hebt verwijderd.

     De sectie `<appSettings>` ziet eruit als in het volgende voor beeld. (Voor duidelijkheid en beveiliging hebben we een aantal regels ingepakt en enkele tekens verwijderd.)

     ![TwitterWpfClient toepassings configuratie bestand in een tekst editor, met de Twitter-sleutels en-geheimen en de Event Hub connection string informatie](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Als u de toepassing nog niet hebt gestart, voert u TwitterWpfClient. exe nu uit. 

5. Klik op de groene start knop om Social sentiment te verzamelen. U ziet Tweet-gebeurtenissen met de waarden **CreatedAt**, **topic**en **SentimentScore** die naar uw event hub worden verzonden.

    ![TwitterWpfClient toepassing waarin een vermelding van tweets wordt weer gegeven](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Als er fouten worden weer gegeven en u geen stroom van tweets ziet in het onderste deel van het venster, controleert u de sleutels en geheimen. Controleer ook de connection string (zorg ervoor dat de `EntityPath` sleutel en de waarde niet worden vermeld.)


## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

Nu Tweet-gebeurtenissen in realtime worden gestreamd vanuit Twitter, kunt u een Stream Analytics taak instellen om deze gebeurtenissen in realtime te analyseren.

1. Klik in de Azure Portal op **een resource maken** > **Internet of Things** > **Stream Analytics taak**.

2. Noem de taak `socialtwitter-sa-job` en geef een abonnement, resource groep en locatie op.

    Het is een goed idee om de taak en de Event Hub in dezelfde regio te plaatsen voor de beste prestaties en om ervoor te zorgen dat u niet betaalt voor het overdragen van gegevens tussen regio's.

    ![Een nieuwe Stream Analytics-taak maken](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Klik op **Maken**.

    De taak wordt gemaakt en er worden taak details weer gegeven in de portal.


## <a name="specify-the-job-input"></a>De taak invoer opgeven

1. Klik in uw Stream Analytics-taak onder **taak topologie** in het midden van de Blade taak op **invoer**. 

2. Klik op de Blade **ingangen** op+&nbsp;de Blade met de volgende waarden **toe te voegen** en in te vullen:

   * **Invoer alias**: gebruik de naam `TwitterStream`. Als u een andere naam gebruikt, noteert u deze, omdat u deze later nodig hebt.
   * **Bron type**: Selecteer **gegevens stroom**.
   * **Bron**: Selecteer **Event hub**.
   * **Import optie**: Selecteer **Event hub gebruiken uit het huidige abonnement**. 
   * **Service Bus-naam ruimte**: selecteer de Event hub naam ruimte die u eerder hebt gemaakt (`<yourname>-socialtwitter-eh-ns`).
   * **Event hub**: selecteer de Event hub die u eerder hebt gemaakt (`socialtwitter-eh`).
   * **Event hub-beleids naam**: Selecteer het toegangs beleid dat u eerder hebt gemaakt (`socialtwitter-access`).

     ![Nieuwe invoer voor streaming Analytics-taak maken](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Klik op **Maken**.


## <a name="specify-the-job-query"></a>De taak query opgeven

Stream Analytics ondersteunt een eenvoudig, declaratief query model dat trans formaties beschrijft. Meer informatie over de taal vindt u in de [Azure stream Analytics query language-referentie](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).  Deze hand leiding helpt u bij het ontwerpen en testen van verschillende query's via Twitter-gegevens.

Als u het aantal vermeldingen tussen de onderwerpen wilt vergelijken, kunt u een [tumblingvenstertriggers-venster](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) gebruiken om elke vijf seconden het aantal vermeldingen per onderwerp op te halen.

1. Sluit de Blade **ingangen** als u dat nog niet hebt gedaan.

2. Klik op de Blade **overzicht** op **query bewerken** in de rechter bovenhoek van het query-vak. In Azure worden de invoer en uitvoer weer gegeven die zijn geconfigureerd voor de taak en kunt u een query maken waarmee u de invoer stroom kunt transformeren wanneer deze naar de uitvoer wordt verzonden.

3. Zorg ervoor dat de TwitterWpfClient-toepassing wordt uitgevoerd. 

3. Klik in de Blade **query** op de punten naast het `TwitterStream` invoer en selecteer vervolgens **voorbeeld gegevens uit invoer**.

    ![Menu opties voor het gebruik van voorbeeld gegevens voor de stream Analytics-taak vermelding, waarbij ' voorbeeld gegevens van invoer ' is geselecteerd](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Hiermee opent u een Blade waarmee u kunt opgeven hoeveel voorbeeld gegevens er moeten worden opgehaald, gedefinieerd in termen van hoe lang de invoer stroom moet worden gelezen.

4. Stel **minuten** in op 3 en klik vervolgens op **OK**. 
    
    ![Opties voor het bemonsteren van de invoer stroom, waarbij ' 3 minuten ' is geselecteerd.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure samples 3 minuten van gegevens uit de invoer stroom en brengt u op de hoogte wanneer de voorbeeld gegevens gereed zijn. (Dit duurt even.) 

    De voorbeeldgegevens worden tijdelijk opgeslagen en zijn beschikbaar zolang u het queryvenster geopend houdt. Als u het query venster sluit, worden de voorbeeld gegevens verwijderd en moet u een nieuwe set voorbeeld gegevens maken. 

5. Wijzig de query in de code-editor naar het volgende:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Als `TwitterStream` als alias voor de invoer niet gebruikt, vervangt u uw alias voor `TwitterStream` in de query.  

    Deze query gebruikt de **time stamp by** -sleutel woord om een time stamp-veld in de payload op te geven dat moet worden gebruikt in de tijdelijke berekening. Als dit veld niet is opgegeven, wordt de venster bewerking uitgevoerd met behulp van de tijd dat elke gebeurtenis is aangekomen op het Event Hub. Meer informatie vindt u in de sectie ' aankomst tijd versus toepassings tijd ' van [Stream Analytics query verwijzing](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

    Met deze query wordt ook een tijds tempel voor het einde van elk venster geopend met behulp van de eigenschap **System. time stamp** .

5. Klik op **testen**. De query wordt uitgevoerd op basis van de gegevens die u bemonstert.
    
6. Klik op **Opslaan**. Hiermee wordt de query opgeslagen als onderdeel van de streaming Analytics-taak. (De voorbeeld gegevens worden niet opgeslagen.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimenteren met verschillende velden uit de stroom 

De volgende tabel geeft een lijst van de velden die deel uitmaken van de JSON streaming-gegevens. U kunt experimenteren met de query-editor.

|JSON-eigenschap | Definitie|
|--- | ---|
|CreatedAt | Het tijdstip waarop de Tweet is gemaakt|
|Onderwerp | Het onderwerp dat overeenkomt met het opgegeven tref woord|
|SentimentScore | De sentiment-Score van Sentiment140|
|Auteur | De Twitter-handle die de Tweet heeft verzonden|
|Tekst | De volledige hoofd tekst van de Tweet|


## <a name="create-an-output-sink"></a>Een uitvoer Sink maken

U hebt nu een gebeurtenis stroom gedefinieerd, een Event Hub invoer voor opname gebeurtenissen en een query voor het uitvoeren van een trans formatie over de stroom. De laatste stap is het definiëren van een uitvoer filter voor de taak.  

In deze hand leiding schrijft u de geaggregeerde Tweet-gebeurtenissen van de taak query naar Azure Blob-opslag.  Afhankelijk van de behoeften van uw toepassing kunt u uw resultaten ook naar Azure SQL Database, Azure-tabel opslag, Event Hubs of Power BI pushen.

## <a name="specify-the-job-output"></a>De taak uitvoer opgeven

1. Klik in de sectie **taak topologie** op het vak **uitvoer** . 

2. Klik op de Blade **uitvoer** op **+&nbsp;** de Blade met de volgende waarden toe te voegen en in te vullen:

   * **Uitvoer alias**: gebruik de naam `TwitterStream-Output`. 
   * **Sink**: Selecteer **Blob Storage**.
   * **Opties voor importeren**: Selecteer **Blob-opslag gebruiken uit het huidige abonnement**.
   * **Opslagaccount**. Selecteer **een nieuw opslag account maken.**
   * **Opslag account** (tweede vak). Voer `YOURNAMEsa`in, waarbij `YOURNAME` uw naam of een andere unieke teken reeks is. De naam kan alleen kleine letters en cijfers gebruiken en deze moet uniek zijn binnen Azure. 
   * **Container**. Voer `socialtwitter` in.
     De naam van het opslag account en de container naam worden samen gebruikt om een URI voor de Blob-opslag op te geven, zoals: 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![De Blade nieuwe uitvoer voor Stream Analytics taak](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Klik op **Maken**. 

    Azure maakt het opslag account en genereert automatisch een sleutel. 

5. Sluit de Blade **uitvoer** . 


## <a name="start-the-job"></a>Taak starten

Er zijn een invoer-, query-en uitvoer taak opgegeven. U bent klaar om de Stream Analytics-taak te starten.

1. Zorg ervoor dat de TwitterWpfClient-toepassing wordt uitgevoerd. 

2. Klik op de Blade taak op **starten**.

    ![De Stream Analytics-taak starten](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. Selecteer op de Blade **Start taak** voor de **Start tijd van de taak uitvoer**de optie **nu** en klik vervolgens op **starten**. 

    ![De Blade ' taak starten ' voor de Stream Analytics taak](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    In azure wordt u gewaarschuwd wanneer de taak is gestart en op de Blade taak wordt de status weer gegeven als **actief**.

    ![Taak wordt uitgevoerd](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Uitvoer weer geven voor sentiment-analyse

Nadat de taak is gestart en de real-time Twitter-stroom is verwerkt, kunt u de uitvoer voor sentiment analyse bekijken.

U kunt een hulp programma als [Azure Storage Explorer](https://storageexplorer.com/) of [Azure Explorer](https://www.cerebrata.com/products/azure-explorer/introduction) gebruiken om uw taak uitvoer in realtime weer te geven. Hier kunt u [Power bi](https://powerbi.com/) gebruiken om uw toepassing uit te breiden met een aangepast dash board zoals de pagina die wordt weer gegeven in de volgende scherm afbeelding:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Maak een andere query om de onderwerpen over trends te identificeren

Een andere query die u kunt gebruiken om inzicht te krijgen in Twitter sentiment is gebaseerd op een [verschuivend venster](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics). Als u onderwerpen over trends wilt identificeren, zoekt u naar onderwerpen waarin een drempel waarde wordt overschreden voor een bepaalde periode.

Voor de doel einden van deze procedure controleert u op onderwerpen die in de afgelopen vijf seconden meer dan twintig keer worden vermeld.

1. Klik op de Blade taak op **stoppen** om de taak te stoppen. 

2. Klik in de sectie **taak topologie** op het **query** -vak. 

3. Wijzig de query in het volgende:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Klik op **Opslaan**.

5. Zorg ervoor dat de TwitterWpfClient-toepassing wordt uitgevoerd. 

6. Klik op **Start** om de taak opnieuw te starten met de nieuwe query.


## <a name="get-support"></a>Krijg ondersteuning
Voor verdere ondersteuning kunt u proberen onze [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
