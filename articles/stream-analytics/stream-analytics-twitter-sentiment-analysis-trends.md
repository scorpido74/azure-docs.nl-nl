---
title: Realtime Twitter-sentimentanalyse met Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u Stream Analytics gebruiken voor realtime Twitter-sentimentsanalyse. Stapsgewijze begeleiding van het genereren van gebeurtenissen tot gegevens op een live dashboard.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 4b265bb574895e4728ad93ee25c9dad0da226ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240306"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analyse van realtime Twitter-gevoel in Azure Stream Analytics

In dit artikel leert u hoe u een oplossing voor de analyse van het sentiment op sociale media bouwen door realtime Twitter-gebeurtenissen in Azure Event Hubs te brengen. U schrijft een Azure Stream Analytics-query om de gegevens te analyseren en de resultaten op te slaan voor later gebruik of een [Power BI-dashboard](https://powerbi.com/) te maken om in realtime inzichten te bieden.

Social media analytics tools helpen organisaties inzicht te krijgen in trending topics. Trending topics zijn onderwerpen en attitudes die een groot aantal berichten op sociale media hebben. Sentiment analyse, die ook wel *opinion mining*, maakt gebruik van sociale media analytics tools om de houding ten opzichte van een product of idee te bepalen. 

Real-time Twitter trend analyse is een goed voorbeeld van een analytics tool, omdat de hashtag abonnement model u luisteren naar specifieke zoekwoorden (hashtags) en sentiment analyse van de feed te ontwikkelen.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: Social media sentiment analyse in real time

Een bedrijf dat een nieuws media website heeft is geïnteresseerd in het verkrijgen van een voordeel ten opzichte van haar concurrenten door met site-inhoud die onmiddellijk relevant is voor haar lezers. Het bedrijf maakt gebruik van sociale media-analyse over onderwerpen die relevant zijn voor lezers door het doen van real-time sentiment analyse van Twitter-gegevens.

Om trending topics in real time op Twitter te identificeren, heeft het bedrijf real-time analyses nodig over het tweetvolume en het sentiment voor belangrijke onderwerpen.

## <a name="prerequisites"></a>Vereisten

In deze handleiding gebruik je een clientapplicatie die verbinding maakt met Twitter en zoekt naar tweets met bepaalde hashtags (die je instellen). Als u de toepassing wilt uitvoeren en de tweets wilt analyseren met Azure Streaming Analytics, moet u het volgende hebben:

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/)aan.

* Een [Twitter](https://twitter.com) Twitter-account.

* De TwitterClientCore applicatie, die de Twitter-feed leest. Download [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore)om deze toepassing te downloaden.

* Installeer de [.NET Core CLI-versie](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) 2.1.0.

## <a name="create-an-event-hub-for-streaming-input"></a>Een gebeurtenishub maken voor streaming-invoer

De voorbeeldtoepassing genereert gebeurtenissen en duwt deze naar een Azure-gebeurtenishub. Azure Event Hubs zijn de voorkeursmethode voor het inslikken van gebeurtenissen voor Stream Analytics. Zie de documentatie [van Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)voor meer informatie .

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Een naamruimte voor gebeurtenishub en gebeurtenishub maken
In deze sectie maakt u een naamruimte voor gebeurtenishubs en voegt u een gebeurtenishub toe aan die naamruimte. Naamruimten voor gebeurtenishubs worden gebruikt om logisch gerelateerde gebeurtenisbus-exemplaren te groeperen. 

1. Meld u aan bij de Azure-portal en selecteer **Een resource maken**. Dan. zoeken naar **gebeurtenishubs** en selecteer **Maken**.

2. Voer op de pagina **Naamruimte maken** een naamruimtenaam in. U elke naam voor de naamruimte gebruiken, maar de naam moet geldig zijn voor een URL en deze moet uniek zijn in Azure. 
    
3. Selecteer een prijscategorie en abonnement en maak of kies een resourcegroep. Kies vervolgens een locatie en selecteer **Maken**. 
 
4. Wanneer de naamruimte is voltooid, navigeert u naar uw resourcegroep en vindt u de naamruimte van de gebeurtenishub in uw lijst met Azure-bronnen. 

5. Selecteer ** + &nbsp;gebeurtenishub**in de nieuwe naamruimte . 

6. Noem de nieuwe event hub *socialtwitter-eh*. U kunt ook een andere naam gebruiken. Als je dat doet, maak er dan een notitie van, want je hebt de naam later nodig. U hoeft geen andere opties in te stellen voor de gebeurtenishub.
 
7. Selecteer **Maken**.

### <a name="grant-access-to-the-event-hub"></a>Toegang verlenen tot de gebeurtenishub

Voordat een proces gegevens naar een gebeurtenishub kan verzenden, heeft de gebeurtenishub een beleid nodig dat toegang biedt. Het toegangsbeleid genereert een verbindingsreeks die autorisatiegegevens bevat.

1.  Selecteer in de navigatiebalk aan de linkerkant van de naamruimte van uw gebeurtenishubs de optie **Gebeurtenishubs**die zich in de sectie **Entiteiten** bevinden. Selecteer vervolgens de gebeurtenishub die u zojuist hebt gemaakt.

2.  Selecteer op de navigatiebalk aan de linkerkant beleid **voor gedeelde toegang** onder **Instellingen**.

    >[!NOTE]
    >Er is een optie voor gedeelde toegangsbeleidsregels voor de naamruimte van de gebeurtenishub en voor de gebeurtenishub. Zorg ervoor dat u werkt in de context van uw gebeurtenishub, niet in de naamruimte van de algehele gebeurtenishub.

3.  Selecteer **+ Toevoegen**op de pagina toegangsbeleid . Voer vervolgens *socialtwitter-toegang in* voor de **naam Beleid** en schakel het selectievakje **Beheren in.**
 
4.  Selecteer **Maken**.

5.  Nadat het beleid is geïmplementeerd, selecteert u het beleid in de lijst met beleid voor gedeelde toegang.

6.  Zoek de primaire toets met de **tekstVerbindingstekenreeks** en selecteer de kopieerknop naast de verbindingstekenreeks.
 
7.  Plak de verbindingsreeks in een teksteditor. U hebt deze verbindingstekenreeks nodig voor de volgende sectie nadat u enkele kleine bewerkingen hebt uitgevoerd.

   De verbindingstekenreeks ziet er als volgt uit:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   De verbindingstekenreeks bevat meerdere sleutelwaardeparen, gescheiden met `Endpoint`puntkomma's: `SharedAccessKeyName`, , `SharedAccessKey`en `EntityPath`.  

   > [!NOTE]
   > Voor beveiliging zijn delen van de verbindingstekenreeks in het voorbeeld verwijderd.

## <a name="configure-and-start-the-twitter-client-application"></a>De Twitter-clienttoepassing configureren en starten

De clientapplicatie krijgt tweetgebeurtenissen rechtstreeks van Twitter. Om dit te doen, heeft het toestemming nodig om de Twitter Streaming API's te bellen. Als u die machtiging wilt configureren, maakt u een toepassing in Twitter, die unieke referenties genereert (zoals een OAuth-token). U vervolgens de clienttoepassing configureren om deze referenties te gebruiken wanneer deze API-aanroepen maakt. 

### <a name="create-a-twitter-application"></a>Een Twitter-toepassing maken
Als u nog geen Twitter-toepassing hebt die u gebruiken voor deze handleiding, u er een maken. Je moet al een Twitter-account hebben.

> [!NOTE]
> Het exacte proces in Twitter voor het maken van een toepassing en het verkrijgen van de sleutels, geheimen en token kan veranderen. Als deze instructies niet overeenkomen met wat je ziet op de Twitter-site, raadpleegdan de documentatie van de Twitter-ontwikkelaars.

1. Ga vanuit een webbrowser naar [Twitter Voor ontwikkelaars,](https://developer.twitter.com/en/apps)maak een ontwikkelaarsaccount en selecteer **Een app maken.** Mogelijk ziet u een bericht waarin staat dat u een Twitter-ontwikkelaarsaccount moet aanvragen. Voel je vrij om dit te doen, en nadat uw aanvraag is goedgekeurd, ziet u een bevestigingse-mail. Het kan enkele dagen duren voordat een ontwikkelaarsaccount is goedgekeurd.

   ![Details van twitter-toepassingen](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Details van twitter-toepassingen")

2. Voer op de pagina **Create an application** de gegevens voor de nieuwe app in en selecteer **Create your Twitter application**.

   ![Details van twitter-toepassingen](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Details van twitter-toepassingen")

3. Selecteer op de toepassingspagina het tabblad **Sleutels en tokens** en kopieer de waarden voor Consumer API **Key** en Consumer API **Secret Key**. Selecteer ook **Maken** onder **Access Token en Access Token Secret** om de toegangstokens te genereren. Kopieer de waarden voor **Access Token** en **Access Token Secret**.

   Sla de waarden op die u hebt opgehaald voor de Twitter-toepassing. Je hebt de waarden later nodig.

> [!NOTE]
> De sleutels en geheimen voor de Twitter-applicatie bieden toegang tot uw Twitter-account. Behandel deze informatie als gevoelig, hetzelfde als je je Twitter-wachtwoord. Sluit deze informatie bijvoorbeeld niet in in een toepassing die u aan anderen geeft. 

### <a name="configure-the-client-application"></a>De clienttoepassing configureren

We hebben een clienttoepassing gemaakt die verbinding maakt met Twitter-gegevens met behulp van [Twitter-API's](https://dev.twitter.com/streaming/overview) voor het verzamelen van tweetgebeurtenissen over een specifieke reeks onderwerpen.

Voordat de toepassing wordt uitgevoerd, vereist deze bepaalde informatie van u, zoals de Twitter-toetsen en de tekenreeks van de gebeurtenishubverbinding.

1. Zorg ervoor dat u de [TwitterClientCore-toepassing](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) hebt gedownload, zoals vermeld in de vereisten.

2. Gebruik een teksteditor om het *bestand App.config* te openen. Breng de volgende `<appSettings>` wijzigingen aan in het element:

   * Stel `oauth_consumer_key` in op de Twitter Consumer Key (API-toets). 
   * Ingesteld `oauth_consumer_secret` op de Twitter Consumer Secret (API secret key).
   * Stel `oauth_token` in op het Twitter Access-token.
   * Stel `oauth_token_secret` in op het Twitter Access-tokengeheim.
   * Instellen `EventHubNameConnectionString` op de verbindingstekenreeks.
   * Ingesteld `EventHubName` op de naam van de gebeurtenishub (dat is de waarde van het entiteitspad).

3. Open de opdrachtregel en navigeer naar de map waar uw TwitterClientCore-app zich bevindt. Gebruik de `dotnet build` opdracht om het project te bouwen. Gebruik vervolgens `dotnet run` de opdracht om de app uit te voeren. De app stuurt Tweets naar je Event Hub.

## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

Nu tweetgebeurtenissen in realtime worden gestreamd vanaf Twitter, u een Stream Analytics-taak instellen om deze gebeurtenissen in realtime te analyseren.

1. Navigeer in de Azure-portal naar uw brongroep en selecteer **+ Toevoegen**. Zoek vervolgens naar **de taak Stream Analytics** en selecteer **Maken**.

2. Geef de `socialtwitter-sa-job` taak een naam en geef een abonnement, resourcegroep en locatie op.

    Het is een goed idee om de taak en de gebeurtenishub in dezelfde regio te plaatsen voor de beste prestaties en zodat u niet betaalt om gegevens tussen regio's over te dragen.

3. Selecteer **Maken**. Navigeer vervolgens naar uw taak wanneer de implementatie is voltooid.

## <a name="specify-the-job-input"></a>De taakinvoer opgeven

1. Selecteer in de taak Stream Analytics de optie **Invoer** in het linkermenu onder **Job Topology**.

2.  > Selecteer ** + &nbsp;Gebeurtenishub voor streaminvoer****toevoegen**. Vul het **nieuwe invoerformulier** in met de volgende informatie:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Invoeralias| *TwitterStream TwitterStream* | Voer een alias in voor de invoer. |
   |Abonnement  | \<Uw abonnement\> |  Selecteer het Azure-abonnement dat u wilt gebruiken. |
   |Event hub-naamruimte | *asa-twitter-eventhub* |
   |Event Hub-naam | *socialtwitter-eh* | Kies *Bestaand gebruiken*. Selecteer vervolgens de gebeurtenishub die u hebt gemaakt.|
   |Gebeurteniscompressietype| Gzip | Het type gegevenscompressie.|

   Laat de resterende standaardwaarden achter en selecteer **Opslaan**.

## <a name="specify-the-job-query"></a>De taakquery opgeven

Stream Analytics ondersteunt een eenvoudig, declaratief querymodel dat transformaties beschrijft. Zie de [naslaggids voor querytaal van Azure Stream Analytics.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) Deze handleiding helpt u bij het schrijven en testen van verschillende query's via Twitter-gegevens.

Om het aantal vermeldingen tussen onderwerpen te vergelijken, u een [Tumbling-venster](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) gebruiken om elke vijf seconden de telling van vermeldingen per onderwerp te krijgen.

1. Selecteer Query **bewerken** in uw **taakoverzicht**in de rechterbovenhoek van het vak Query. Azure bevat de ingangen en uitvoer die zijn geconfigureerd voor de taak en laat u een query maken om de invoerstroom te transformeren terwijl deze naar de uitvoer wordt verzonden.

2. Wijzig de query in de queryeditor als volgt:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Gebeurtenisgegevens uit de berichten moeten worden weergegeven in het **venster Invoervoorbeeld** onder uw query. Controleer of de **weergave** is ingesteld op **JSON**. Als u geen gegevens ziet, moet u ervoor zorgen dat uw gegevensgenerator gebeurtenissen naar uw gebeurtenishub verzendt en dat u **GZip** hebt geselecteerd als compressietype voor de invoer.

4. Selecteer **Testquery** en let op de resultaten in het venster **Testresultaten** onder uw query.

5. Wijzig de query in de codeeditor in de volgende en selecteer **Testquery:**

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Met deze query worden alle tweets geretourneerd die het zoekwoord *Azure*bevatten.

## <a name="create-an-output-sink"></a>Een uitvoergootsteen maken

U hebt nu een gebeurtenisstream, een gebeurtenishub-invoer voor het opnemen van gebeurtenissen en een query gedefinieerd om een transformatie via de stream uit te voeren. De laatste stap is het definiëren van een uitvoergootsteen voor de taak.  

In deze handleiding schrijft u de geaggregeerde tweetgebeurtenissen van de taakquery naar Azure Blob-opslag.  U uw resultaten ook naar Azure SQL Database, Azure Table storage, Event Hubs of Power BI pushen, afhankelijk van de toepassingsbehoeften.

## <a name="specify-the-job-output"></a>De taakuitvoer opgeven

1. Selecteer **Job Topology** Uitvoer **.** 

2. Klik **op** de pagina ** + &nbsp;** Uitvoer op Toevoegen en **Blob-opslag/Gegevensmeeropslag Gen2:**

   * **Uitvoeralias**: Gebruik `TwitterStream-Output`de naam . 
   * **Importopties:** Selecteer **Opslag selecteren in uw abonnementen**.
   * **Opslagaccount**. Selecteer uw opslagaccount.
   * **Container**. Selecteer **Nieuw** maken `socialtwitter`en voer .
   
4. Selecteer **Opslaan**.   

## <a name="start-the-job"></a>Taak starten

Er worden een taakinvoer, query en uitvoer opgegeven. U bent klaar om de streamanalytics-taak te starten.

1. Controleer of de TwitterClientCore-toepassing wordt uitgevoerd. 

2. Selecteer **Start**in het taakoverzicht .

3. Selecteer Nu op de taakpagina **Start** voor **De uitvoer van taak**en selecteer **Nu** en selecteer **Vervolgens Start**.

## <a name="get-support"></a>Ondersteuning krijgen
Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
