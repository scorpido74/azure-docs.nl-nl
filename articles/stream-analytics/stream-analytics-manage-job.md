---
title: Zelfstudie - Een Stream Analytics-taak maken en beheren met Azure-portal
description: Deze zelfstudie biedt een end-to-end-demonstratie van het gebruik van Azure Stream Analytics voor het analyseren van frauduleuze gesprekken in een reeks telefoongesprekken.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/03/2019
ms.openlocfilehash: 488664b028568b3014b9b839122705d35104861e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239306"
---
# <a name="tutorial-analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Zelfstudie: Telefoongespreksgegevens analyseren met Stream Analytics en resultaten visualiseren in Power BI-dashboard

In deze zelfstudie leert u hoe u telefoongesprekken kunt analyseren met Azure Stream Analytics. De telefoongespreksgegevens, gegenereerd door een clienttoepassing, bevatten een aantal frauduleuze oproepen, die worden gefilterd door de streamanalytics-taak.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gegevens van een voorbeeld van een telefoongesprek genereren en deze verzenden naar Azure Event Hubs
> * Een Stream Analytics-taak maken
> * Taakinvoer en -uitvoer configureren
> * Een query definiëren om frauduleuze gesprekken eruit te filteren
> * De taak testen en starten
> * Resultaten visualiseren in Power BI

## <a name="prerequisites"></a>Vereisten

Ga voordat u begint met de volgende acties:

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/)aan.
* Meld u aan bij [Azure Portal](https://portal.azure.com/).
* Download de app voor het genereren van telefoongesprekken [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) in het Microsoft Downloadcentrum of haal de broncode op van [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* U hebt een Power BI-account nodig.

## <a name="create-an-azure-event-hub"></a>Een Azure Event Hub maken

Voordat Stream Analytics de gegevensstroom van frauduleuze gesprekken kan analyseren, moeten de gegevens naar Azure worden verzonden. In deze zelfstudie verzendt u gegevens naar Azure met behulp van [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs).

Gebruik de volgende stappen voor het maken van een Event Hub en verzenden van gespreksgegevens naar die Event Hub:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer **Een bron** > Internet of**Things-gebeurtenishubs****Internet of Things** > maken .

   ![Azure Event Hub in de portal maken](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. Voer in het deelvenster **Naamruimte maken** de volgende waarden in:

   |**Instelling**  |**Voorgestelde waarde** |**Beschrijving**  |
   |---------|---------|---------|
   |Name     | myEventHubsNS        |  Een unieke naam voor het identificeren van de event hub-naamruimte.       |
   |Abonnement     |   \<Uw abonnement\>      |   Selecteer een Azure-abonnement waarvoor u de event hub wilt maken.      |
   |Resourcegroep     |   MyASADemoRG      |  Selecteer **Nieuwe maken** en voer een naam voor de nieuwe resourcegroep voor uw account in.       |
   |Locatie     |   VS - west 2      |    De locatie waar de event hub-naamruimte kan worden geïmplementeerd.     |

4. Gebruik standaardopties voor de resterende instellingen en selecteer **Maken**.

   ![Event hub-naamruimte in de Azure-portal maken](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. Nadat de naamruimte is geïmplementeerd, gaat u naar **Alle resources** en zoekt u *myEventHubNS* in de lijst met Azure-resources. Selecteer *myEventHubsNS* om het te openen.
6. Selecteer vervolgens **+Event Hub** en voer voor **Naam***MyEventHub* in of een naam naar keuze. Gebruik standaardopties voor de resterende instellingen en selecteer **Maken**. Wacht tot de implementatie is voltooid.

   ![Event Hub-configuratie in de Azure-portal](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Toegang verlenen tot de event hub en een verbindingsreeks ophalen

Voordat een toepassing gegevens naar Azure Event Hubs kan verzenden, moet de event hub een beleid hebben waarmee de juiste toegang wordt verleend. Het toegangsbeleid genereert een verbindingsreeks die autorisatiegegevens bevat.

1. Navigeer naar de gebeurtenishub die u in de vorige stap hebt gemaakt, MyEventHub*. Selecteer onder **Instellingen****Beleid voor gedeelde toegang** en selecteer vervolgens **+ Toevoegen**.

2. Geef het beleid de naam **MyPolicy** en controleer of het selectievakje **Beheren** is ingeschakeld. Selecteer vervolgens **Maken**.

   ![Gedeeld toegangsbeleid voor event hub maken](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. Als het beleid is gemaakt, selecteert u het om het te openen en zoekt u **Verbindingsreeks - primaire sleutel**. Selecteer de blauw knop **kopiëren** naast de verbindingsreeks.

   ![De verbindingsreeks voor het beleid voor gedeelde toegang opslaan](media/stream-analytics-manage-job/save-connection-string.png)

4. Plak de verbindingsreeks in een teksteditor. U hebt deze verbindingsreeks nodig in de volgende sectie.

   De verbindingsreeks ziet er als volgt uit:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   U ziet dat de verbindingsreeks meerdere sleutel-waardeparen bevat, gescheiden door puntkomma's: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** en **EntityPath**.

## <a name="start-the-event-generator-application"></a>De app voor het genereren van gebeurtenissen starten

Voordat u de app TelcoGenerator start, moet u deze configureren voor het verzenden van gegevens naar de Azure Event Hubs die u eerder hebt gemaakt.

1. Pak de inhoud van het bestand [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) uit.
2. Open het bestand `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` in een teksteditor van uw keuze (er is meer dan één .config-bestand, let er dus op dat u het juiste bestand opent).

3. Update het `<appSettings>`-element in het configuratiebestand met de volgende details:

   * Stel de waarde van de sleutel *EventHubName* in op de waarde van EntityPath in de verbindingsreeks.
   * Stel de waarde in van de *microsoft.serviceBus.ConnectionString-toets* op de verbindingstekenreeks zonder de waarde EntityPath (vergeet niet de puntkomma te verwijderen die eraan voorafgaat).

4. Sla het bestand op.
5. Open vervolgens een opdrachtvenster en ga naar de map waar u de toepassing TelcoGenerator hebt uitgepakt. Voer vervolgens de volgende opdracht in:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   Voor deze opdracht worden de volgende parameters gebruikt:
   * Aantal records met gespreksgegevens per uur.
   * Percentage van fraudekans: hoe vaak de app een frauduleus gesprek moet simuleren. Een waarde van 0.2 betekent dat ongeveer 20% van de gespreksrecords er frauduleus uitziet.
   * Tijd in uren: het aantal uren dat de app moet worden uitgevoerd. U de app ook op elk gewenst moment stoppen door het proces **(Ctrl+C)** op de opdrachtregel te beëindigen.

   Na enkele seconden begint de app met het weergeven van telefoongesprekrecords op het scherm wanneer deze naar de event hub worden gestuurd. De telefoongesprekgegevens bevatten de volgende velden:

   |**Record**  |**Definitie**  |
   |---------|---------|
   |CallrecTime    |  Het tijdstempel voor de begintijd van de oproep.       |
   |SwitchNum     |  Het schakelnummer van de oproep. In dit voorbeeld zijn de switches tekenreeksen die het land/de regio van herkomst vertegenwoordigen (VS, China, het Verenigd Koninkrijk, Duitsland of Australië).       |
   |CallingNum     |  Het telefoonnummer van de beller.       |
   |CallingIMSI     |  De International Mobile Subscriber Identity (IMSI). Dit is een unieke id van de beller.       |
   |CalledNum     |   Het telefoonnummer van de ontvanger.      |
   |CalledIMSI     |  De International Mobile Subscriber Identity (IMSI). Dit is een unieke id van de ontvanger.       |

## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

Nu u een stream van gesprekgebeurtenissen hebt, kunt u een Stream Analytics-taak maken die gegevens uit de event hub leest.

1. Als u een Stream Analytics-taak wilt maken, gaat u naar de [Azure-portal](https://portal.azure.com/).

2. Selecteer > Een functie > Internet of Things Stream**Analytics** **maken.****Internet of Things**

3. Voer in het deelvenster **Nieuwe Stream Analytics-taak** de volgende waarden in:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Taaknaam     |  ASATutorial       |   Een unieke naam voor het identificeren van de event hub-naamruimte.      |
   |Abonnement    |  \<Uw abonnement\>   |   Selecteer een Azure-abonnement waarvoor u de taak wilt maken.       |
   |Resourcegroep   |   MyASADemoRG      |   Selecteer **Bestaande gebruiken** en voer een naam voor de nieuwe resourcegroep voor uw account in.      |
   |Locatie   |    VS - west 2     |      De locatie waar de taak kan worden geïmplementeerd. Het is raadzaam de taak en de event hub in dezelfde regio te plaatsen om de best mogelijke prestaties te realiseren en u niet hoeft te betalen voor het overbrengen van gegevens tussen regio's.      |
   |Hostingomgeving    | Cloud        |     Stream Analytics-taken kunnen worden geïmplementeerd in Cloud of in Edge. Cloud stelt u in staat om te implementeren in Azure Cloud en Edge u implementeren naar een IoT Edge-apparaat.    |
   |Streaming-eenheden     |    1       |      Streaming-eenheden vertegenwoordigen de computerresources die nodig zijn om een taak uit te voeren. Deze waarde is standaard ingesteld op 1. Zie het artikel [Streaming-eenheden begrijpen en aanpassen](stream-analytics-streaming-unit-consumption.md) voor meer informatie over het schalen van streaming-eenheden.      |

4. Gebruik standaardopties voor de overige instellingen, selecteer **Maken**en wacht tot de implementatie is geslaagd.

   ![Een Azure Stream Analytics-taak maken](media/stream-analytics-manage-job/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Taakinvoer configureren

In de volgende stap definieert u een invoerbron voor de taak om gegevens te kunnen lezen met de Event Hub die u in de vorige sectie hebt gemaakt.

1. Open het deelvenster **Alle resources** vanuit de Azure-portal en zoek de Stream Analytics-taak *ASATutorial*.

2. Selecteer in de sectie **Taaktopologie** van het deelvenster van de Stream Analytics-taak de optie **Invoer**.

3. Selecteer **+ Stroominvoer toevoegen** en **Event Hub**. Voer in het deelvenster de volgende waarden in:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Invoeralias     |  CallStream       |  Geef een beschrijvende naam op om uw invoer te identificeren. De invoeralias mag alleen alfanumerieke tekens, afbreekstreepjes en onderstrepingstekens bevatten en moet 3 tot 63 tekens lang zijn.       |
   |Abonnement    |   \<Uw abonnement\>      |   Selecteer het Azure-abonnement waarvoor u de event hub hebt gemaakt. De event hub kan zich in dezelfde of een ander abonnement als de Stream Analytics-taak bevinden.       |
   |Event hub-naamruimte    |  myEventHubsNS       |  Selecteer de event hub-naamruimte die u in de vorige sectie hebt gemaakt. Alle in uw huidige abonnement beschikbare event hub-naamruimten worden weergegeven in de vervolgkeuzelijst.       |
   |Event Hub-naam    |   MyEventHub      |  Selecteer de event hub die u in de vorige sectie hebt gemaakt. Alle in uw huidige abonnement beschikbare event hubs worden weergegeven in de vervolgkeuzelijst.       |
   |Naam van het Event Hub-beleid   |  Mijn beleid       |  Selecteer het door de event hub gedeelde toegangsbeleid dat u in de vorige sectie hebt gemaakt. Alle in uw huidige abonnement beschikbare beleidsregels voor event hubs worden weergegeven in de vervolgkeuzelijst.       |

4. Gebruik standaardopties voor de resterende instellingen en selecteer **Opslaan**.

   ![Azure Stream Analytics-invoer configureren](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Taakuitvoer configureren

De laatste stap is bedoeld voor het definiëren van een uitvoerlocatie voor de taak waar de getransformeerde gegevens naartoe kunnen worden geschreven. In deze zelfstudie voert u met behulp van Power BI gegevens uit en maakt u deze zichtbaar.

1. Open het deelvenster **Alle resources** vanuit de Azure-portal en zoek de Stream Analytics-taak *ASATutorial*.

2. Selecteer in de sectie **Taaktopologie** van het deelvenster van de Stream Analytics-taak de optie **Uitvoer**.

3. Selecteer **+ Power** > **BI**toevoegen . Vul vervolgens het formulier in met de volgende gegevens en selecteer **Autoriseren**:

   |**Instelling**  |**Voorgestelde waarde**  |
   |---------|---------|
   |Uitvoeralias  |  MyPBIoutput  |
   |Naam van de gegevensset  |   ASAdataset  |
   |Tabelnaam |  ASATable  |

   ![Azure Stream Analytics-uitvoer configureren](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

4. Als u **Autoriseren** hebt geselecteerd, wordt er een pop-upvenster geopend en wordt u gevraagd referenties te verstrekken als verificatie voor uw Power BI-account. Zodra de autorisatie geslaagd is, kunt u de instellingen **Opslaan**.

## <a name="define-a-query-to-analyze-input-data"></a>Een query voor het analyseren van invoergegevens definiëren

In de volgende stap maakt u een transformatie waarmee gegevens in realtime worden geanalyseerd. U definieert de transformatie-query met [Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). De query die in deze zelfstudie wordt gebruikt, detecteert frauduleuze gesprekken in de telefoongegevens.

In dit voorbeeld worden binnen vijf seconden door dezelfde gebruiker frauduleuze gesprekken gevoerd, maar vanaf afzonderlijke locaties. Zo kan dezelfde gebruiker niet op rechtmatige wijze op hetzelfde moment een telefoongesprek vanuit de Verenigde Staten en Australië initiëren. Ga als volgt te werk als u de transformatie-query voor uw Stream Analytics-taak wilt definiëren:

1. Open vanuit de Azure-portal het deelvenster **Alle bronnen** en navigeer naar de **ASATutorial** Stream Analytics-taak die u eerder hebt gemaakt.

2. Selecteer in de sectie **Taaktopologie** van het deelvenster van de Stream Analytics-taak de optie **Query**. Het queryvenster vermeldt de invoer en uitvoer die voor de taak zijn geconfigureerd en u kunt een query voor het transformeren van de invoerstroom maken.

3. Vervang de bestaande query in de editor door de volgende query, die een self-join uitvoert met een interval van 5 seconden aan gespreksgegevens:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Als u wilt controleren op frauduleuze gesprekken, voert u een self-join uit voor de streaminggegevens op basis van de waarde `CallRecTime`. U vervolgens zoeken naar `CallingIMSI` oproeprecords waarbij de waarde (het `SwitchNum` oorspronkelijke getal) hetzelfde is, maar de waarde (land/regio van herkomst) anders is. Wanneer u een JOIN-bewerking voor streaminggegevens gebruikt, moet de join enkele beperkingen bevatten met betrekking tot hoe ver de overeenkomende rijen in tijd kunnen worden gescheiden. Omdat er sprake is van een eindeloze gegevensstroom, worden de tijdsgrenzen voor de relatie opgegeven binnen de **ON**-component van de join met behulp van de functie [DATEDIFF](https://docs.microsoft.com/stream-analytics-query/datediff-azure-stream-analytics).

   Deze query is net als een normale SQL join, behalve voor de **DATEDIFF-functie.** De in deze query gebruikte functie **DATEDIFF** is specifiek voor Streaming Analytics en moet worden opgenomen in de `ON...BETWEEN`-component.

4. U moet de query **Opslaan**.

   ![Stream Analytics-query in de portal definiëren](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>De query testen

U kunt een query vanuit de query-editor testen met behulp van voorbeeldgegevens. Voer de volgende stappen uit om de query te testen:

1. Zorg ervoor dat de app TelcoGenerator wordt uitgevoerd en records van telefoongesprekken produceert.

2. Selecteer in het deelvenster **Query** de puntjes naast de *CallStream*-invoer en selecteer vervolgens **Voorbeeldgegevens van uitvoer**.

3. Stel **Minuten** in op 3 en selecteer **OK**. Uit de invoerstroom wordt vervolgens drie minuten aan gegevens opgehaald en u krijgt een melding wanneer de voorbeeldgegevens gereed zijn. U kunt de status van het samplen bijhouden op de meldingsbalk.

   De voorbeeldgegevens worden tijdelijk opgeslagen en zijn beschikbaar zolang u het queryvenster geopend houdt. Als u het queryvenster sluit, worden de voorbeeldgegevens verwijderd en u dient een nieuwe set voorbeeldgegevens te maken als u een test wilt uitvoeren. Als alternatief kunt u een JSON-bestand met voorbeeldgegevens ophalen uit [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) en dit JSON-bestand uploaden om te gebruiken als set met voorbeeldgegevens voor de *CallStream*-invoer.

   ![Visual van het verzamelen van invoergegevens voor Stream Analytics](media/stream-analytics-manage-job/sample-input-data-asa.png)

4. Selecteer **Testen** om de query te testen. U ziet de volgende resultaten:

   ![Uitvoer uit De querytest van Stream Analytics](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>De taak starten en uitvoer visualiseren

1. Als u de taak wilt starten, gaat u naar het deelvenster **Overzicht** van de taak en selecteert u **Starten**.

2. Selecteer **Nu** voor de starttijd van de taakuitvoer en selecteer **Starten**. U kunt de taakstatus bekijken in de meldingsbalk.

3. Nadat de taak is voltooid, gaat u naar [Power BI](https://powerbi.com/) en meldt u zich aan met uw werk- of schoolaccount. Als de Stream Analytics-query resultaten uitvoert, wordt de gegevensset *ASAdataset* die u hebt gemaakt, weergegeven onder het tabblad **Gegevenssets**.

4. Selecteer in de Power BI-werkruimte **+ Maken** om een nieuw dashboard te maken met de naam *Frauduleuze gesprekken*.

5. Selecteer boven in het venster de optie **Tegel toevoegen**. Selecteer **Aangepaste streaminggegevens** en **Volgende**. Kies **ASAdataset** onder **Uw gegevenssets**. Selecteer **Kaart** in de **vervolgkeuzelijst Visualisatietype** en voeg **frauduleuze oproepen** toe aan **Velden**. Selecteer **Volgende** om een naam voor de tegel in te voeren en selecteer vervolgens **Toepassen** om het bestand te maken.

   ![Tegels in Power BI-dashboard maken](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. Voer nogmaals stap 5 uit met de volgende opties:
   * Selecteer in Type visualisatie de optie Lijndiagram.
   * Voeg een as toe en selecteer **windowend**.
   * Voeg een waarde toe en selecteer **fraudulentcalls**.
   * Selecteer bij **Tijdvenster voor weergave** de laatste 10 minuten.

7. Als beide tegels zijn toegevoegd, moet uw dashboard eruitzien zoals in het onderstaande voorbeeld. Als uw toepassing voor de afzender van uw gebeurtenishub en de toepassing Streaming Analytics worden uitgevoerd, wordt uw Power BI-dashboard periodiek bijgewerkt wanneer nieuwe gegevens binnenkomen.

   ![Resultaten weergeven in Power BI-dashboard](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Uw Power BI-dashboard insluiten in een webtoepassing

Voor dit deel van de zelfstudie gebruikt u een voorbeeld [ASP.NET](https://asp.net/) webtoepassing die is gemaakt door het Power BI-team om uw dashboard in te sluiten. Zie het artikel [Insluiten met Power BI](https://docs.microsoft.com/power-bi/developer/embedding) voor meer informatie over het insluiten van dashboards.

Als u de toepassing wilt instellen, gaat u naar de [GitHub-opslagplaats powerbi-developer-samples](https://github.com/Microsoft/PowerBI-Developer-Samples) en volgt u de instructies onder de sectie **Gebruikerseigen gegevens** (gebruik de url's van omleiding en homepage onder de subsectie **integratie-web-app).** Aangezien we het voorbeeld dashboard gebruiken, gebruikt u de voorbeeldcode van de **integratie-web-app** in de [GitHub-opslagplaats.](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-web-app)
Zodra de toepassing in uw browser wordt uitgevoerd, volgt u deze stappen voor het insluiten van het dashboard dat u eerder in de webpagina hebt gemaakt:

1. Selecteer **Aanmelden bij Power BI**, waarmee de toepassing toegang krijgt tot de dashboards in uw Power BI-account.

2. Selecteer de knop **Dashboards ophalen**, waardoor de dashboards van uw account in een tabel worden weergegeven. Zoek de naam van het dashboard dat u eerder hebt gemaakt, **powerbi-embedded-dashboard** en kopieer de bijbehorende **EmbedUrl**.

3. Plak tot slot de **EmbedUrl** in het bijbehorende tekstveld en selecteer **Dashboard insluiten**. U kunt nu hetzelfde dashboard zien dat is ingesloten in een webtoepassing.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een eenvoudige Stream Analytics-taak gemaakt, de binnenkomende gegevens geanalyseerd en de resultaten gepresenteerd in een Power BI-dashboard. Ga voor meer informatie over Stream Analytics-taken verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Azure Functions uitvoeren binnen Stream Analytics-taken](stream-analytics-with-azure-functions.md)
