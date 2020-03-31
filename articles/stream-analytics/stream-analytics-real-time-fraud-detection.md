---
title: Fraudedetectie in realtime met Azure Stream Analytics
description: Ontdek hoe u een real-time oplossing voor fraudedetectie maken met Stream Analytics. Gebruik een gebeurtenishub voor real-time gebeurtenisverwerking.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: c0b2943e1f0d7f2386ec09da03d297a570eede7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276475"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Aan de slag met Azure Stream Analytics: realtime fraudedetectie

Deze zelfstudie geeft een end-to-end illustratie van het gebruik van Azure Stream Analytics. Procedures voor: 

* Breng streaminggebeurtenissen naar een exemplaar van Azure Event Hubs. In deze zelfstudie gebruikt u een app die een stroom metagegevens van mobiele telefoons simuleert.

* Schrijf SQL-achtige Stream Analytics-query's om gegevens te transformeren, informatie te aggregeren of patronen te zoeken. U ziet hoe u een query gebruiken om de binnenkomende stream te onderzoeken en te zoeken naar oproepen die mogelijk frauduleus zijn.

* Stuur de resultaten naar een uitvoergootsteen (opslag) die u analyseren voor aanvullende inzichten. In dit geval stuurt u de verdachte oproepgegevens naar Azure Blob-opslag.

Deze zelfstudie maakt gebruik van het voorbeeld van real-time fraudedetectie op basis van telefoongegevens. De geïllustreerde techniek is ook geschikt voor andere vormen van fraudedetectie, zoals creditcardfraude of identiteitsdiefstal. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenario: Detectie van telecommunicatie- en SIM-fraude in realtime

Een telecommunicatiebedrijf heeft een grote hoeveelheid gegevens voor inkomende oproepen. Het bedrijf wil frauduleuze oproepen in realtime detecteren, zodat ze klanten op de hoogte kunnen stellen of de service voor een specifiek nummer kunnen afsluiten. Een type sim-fraude omvat meerdere gesprekken van dezelfde identiteit rond dezelfde tijd, maar op geografisch verschillende locaties. Om dit soort fraude op te sporen, moet het bedrijf binnenkomende telefoongegevens onderzoeken en zoeken naar specifieke patronen, in dit geval naar oproepen die rond dezelfde tijd in verschillende landen/regio's worden gevoerd. Alle telefoonrecords die in deze categorie vallen, worden naar de opslag geschreven voor latere analyse.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie simuleert u telefoongegevens met behulp van een client-app die metagegevens van voorbeeldgesprekken genereert. Sommige records die de app produceert, lijken op frauduleuze oproepen. 

Zorg ervoor dat u het volgende hebt voordat u begint:

* Een Azure-account.
* De call-event generator app, [TelcoGenerator.zip,](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)die kan worden gedownload van het Microsoft Download Center. Rits dit pakket uit in een map op uw computer. Als u de broncode wilt zien en de app in een foutopsporing wilt uitvoeren, u de broncode van de app van [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)ophalen. 

    >[!NOTE]
    >Windows kan het gedownloade .zip-bestand blokkeren. Als u het bestand niet uitpakken, klikt u met de rechtermuisknop op het bestand en selecteert u **Eigenschappen**. Als u het bericht 'Dit bestand afkomstig is van een andere computer en mogelijk geblokkeerd is om deze computer te beschermen' ziet, selecteert u de optie **Deblokkering opheffen** en klikt u op **Toepassen**.

Als u de resultaten van de taak Streaming Analytics wilt bekijken, hebt u ook een hulpmiddel nodig voor het bekijken van de inhoud van een Azure Blob Storage-container. Als u Visual Studio gebruikt, u [Azure Tools gebruiken voor Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) of Visual Studio Cloud [Explorer.](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) U ook zelfstandige hulpprogramma's zoals [Azure Storage Explorer](https://storageexplorer.com/) of [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage)installeren. 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Een Azure Event Hubs maken om gebeurtenissen in te nemen

Als u een gegevensstroom wilt analyseren, neemt u *deze* in Azure in. Een typische manier om gegevens in te nemen, is door [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)te gebruiken, waarmee u miljoenen gebeurtenissen per seconde opnemen en vervolgens de gebeurtenisgegevens verwerken en opslaan. Voor deze zelfstudie maakt u een gebeurtenishub en stuurt u vervolgens de oproepgenerator-app oproepgegevens naar die gebeurtenishub. Zie de [azure servicebusdocumentatie](https://docs.microsoft.com/azure/service-bus/)voor meer informatie over gebeurtenishubs.

>[!NOTE]
>Zie [Een naamruimte voor gebeurtenishubs maken en een gebeurtenishub maken met de Azure-portal](../event-hubs/event-hubs-create.md)voor een meer gedetailleerde versie van deze procedure. 

### <a name="create-a-namespace-and-event-hub"></a>Een naamruimte en gebeurtenishub maken
In deze procedure maakt u eerst een naamruimte voor gebeurtenishubs en voegt u vervolgens een gebeurtenishub toe aan die naamruimte. Naamruimten voor gebeurtenishubs worden gebruikt om logisch gerelateerde gebeurtenisbus-exemplaren te groeperen. 

1. Meld u aan bij de Azure-portal en klik op **Een resource** maken linksboven in het scherm.

2. Selecteer **Alle services** in het menu aan de linkerkant en selecteer de **ster (`*`)** naast **Event Hubs** in de categorie **Analyse**. Controleer of **Event Hubs** is toegevoegd aan **FAVORIETEN** in het navigatiemenu aan de linkerkant. 

   ![Zoeken naar Event Hubs](./media/stream-analytics-real-time-fraud-detection/select-event-hubs-menu.png)

3. Selecteer **Event Hubs** onder **FAVORIETEN** in het navigatiemenu links en selecteer **Toevoegen** op de werkbalk.

   ![Knop Toevoegen](./media/stream-analytics-real-time-fraud-detection/event-hubs-add-toolbar.png)

4. Voer in het deelvenster **Naamruimte maken** een `<yourname>-eh-ns-demo`naamruimtenaam in, zoals . U elke naam voor de naamruimte gebruiken, maar de naam moet geldig zijn voor een URL en deze moet uniek zijn in Azure. 
    
5. Selecteer een abonnement en maak of kies een resourcegroep en klik op **Maken**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

6. Wanneer de naamruimte is voltooid, zoekt u de naamruimte van de gebeurtenishub in uw lijst met Azure-bronnen. 

7. Klik op de nieuwe naamruimte en klik in het deelvenster Naamruimte op **Gebeurtenishub**.

   ![De knop Gebeurtenishub toevoegen voor het maken van een nieuwe gebeurtenishub](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
8. Geef de nieuwe `asa-eh-frauddetection-demo`gebeurtenishub een naam . U kunt ook een andere naam gebruiken. Als je dat doet, maak er dan een notitie van, want je hebt de naam later nodig. U hoeft op dit moment geen andere opties voor de gebeurtenishub in te stellen.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
9. Klik **op Maken**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Toegang verlenen tot de event hub en een verbindingsreeks ophalen

Voordat een proces gegevens naar een gebeurtenishub kan verzenden, moet de gebeurtenishub een beleid hebben dat de juiste toegang mogelijk maakt. Het toegangsbeleid genereert een verbindingsreeks die autorisatiegegevens bevat.

1. Klik in het deelvenster Naamruimte van de gebeurtenis op **Gebeurtenishubs** en klik vervolgens op de naam van uw nieuwe gebeurtenishub.

2. Klik in het deelvenster Gebeurtenishub op ** + &nbsp;** Beleid voor gedeelde **toegang** en klik vervolgens op Toevoegen .

    > [!NOTE]
    > Zorg ervoor dat u werkt met de gebeurtenishub, niet met de naamruimte van de gebeurtenishub.

3. Voeg een `asa-policy-manage-demo` beleid met de naam toe en selecteer **beheren**voor **Claim**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4. Klik **op Maken**.

5. Nadat het beleid is geïmplementeerd, klikt u erop in de lijst met beleid voor gedeelde toegang.

6. Zoek het vak met het label **CONNECTION STRING-PRIMARY KEY** en klik op de knop Kopiëren naast de verbindingstekenreeks. 

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7. Plak de verbindingsreeks in een teksteditor. U hebt deze verbindingstekenreeks nodig voor de volgende sectie, nadat u er enkele kleine bewerkingen aan hebt uitgevoerd.

    De verbindingstekenreeks ziet er als volgt uit:

    `Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo`

    De verbindingstekenreeks bevat meerdere sleutelwaardeparen, gescheiden met `Endpoint`puntkomma's: `SharedAccessKeyName`, , `SharedAccessKey`en `EntityPath`.  


## <a name="configure-and-start-the-event-generator-application"></a>De toepassing van de gebeurtenisgenerator configureren en starten

Voordat u de TelcoGenerator-app start, moet u deze zo configureren dat er oproeprecords worden verzonden naar de gebeurtenishub die u hebt gemaakt.

### <a name="configure-the-telcogenerator-app"></a>De TelcoGenerator-app configureren

1. In de editor waar u de verbindingstekenreeks hebt `EntityPath` gekopieerd, maakt `EntityPath` u een notitie van de waarde en verwijdert u het paar (vergeet niet de puntkomma te verwijderen die eraan voorafgaat). 

2. Open het bestand telcodatagen.exe.config in de map waar u het bestand TelcoGenerator.zip hebt uitgepakt in een editor. (Er is meer dan één .config-bestand, dus zorg ervoor dat u de juiste opent.)

3. In `<appSettings>` het element:

   * Stel de waarde `EventHubName` in van de sleutel naar de naam van de gebeurtenishub (dat wil zeggen op de waarde van het entiteitspad).
   * Stel de waarde `Microsoft.ServiceBus.ConnectionString` in van de toets op de verbindingstekenreeks. 

   De `<appSettings>` sectie ziet er als volgt uit:

    ```xml
    <appSettings>
     <!-- Service Bus specific app setings for messaging connections -->
     <add key="EventHubName" value="asa-eh-ns-demo"/>
     <add key="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://asa-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=GEcnTKf2//1MRn6SN1A2u0O76MP9pj3v0Ccyf1su4Zo="/>
   </appSettings>
    ```
 
4. Sla het bestand op. 

### <a name="start-the-app"></a>De app starten

1. Open een opdrachtvenster en ga over naar de map waarin de TelcoGenerator-app is uitgepakt.

2. Voer de volgende opdracht in:

    ```console
    telcodatagen.exe 1000 0.2 2
    ```

   De parameters zijn: 

   * Aantal CDR's per uur. 
   * Sim-kaartfraude waarschijnlijkheid: Hoe vaak, als percentage van alle oproepen, dat de app een frauduleus gesprek moet simuleren. Een waarde van 0.2 betekent dat ongeveer 20% van de gespreksrecords er frauduleus uitziet.
   * Duur in uren. Het aantal uren dat de app moet uitvoeren. U de app ook op elk gewenst moment stoppen door op Ctrl+C op de opdrachtregel te drukken.

   Na enkele seconden begint de app met het weergeven van telefoongesprekrecords op het scherm wanneer deze naar de event hub worden gestuurd.

Enkele van de belangrijkste gebieden die u zult gebruiken in deze real-time fraude detectie applicatie zijn de volgende:

|**Record**|**Definitie**|
|----------|--------------|
|`CallrecTime`|Het tijdstempel voor de begintijd van de oproep. |
|`SwitchNum`|Het schakelnummer van de oproep. In dit voorbeeld zijn de switches tekenreeksen die het land/de regio van herkomst vertegenwoordigen (VS, China, het Verenigd Koninkrijk, Duitsland of Australië). |
|`CallingNum`|Het telefoonnummer van de beller. |
|`CallingIMSI`|De International Mobile Subscriber Identity (IMSI). Dit is de unieke id van de beller. |
|`CalledNum`|Het telefoonnummer van de ontvanger. |
|`CalledIMSI`|De International Mobile Subscriber Identity (IMSI). Dit is de unieke id van de ontvanger van de oproep. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Een Stream Analytics-taak maken om streaminggegevens te beheren

Nu je een stroom aan oproepgebeurtenissen hebt, kun je een Stream Analytics-taak instellen. De taak leest gegevens uit de gebeurtenishub die u hebt ingesteld. 

### <a name="create-the-job"></a>De taak maken 

1. Klik in de Azure-portal op Een > vacature**Internet of Things** > **Stream Analytics** **maken.**

2. Geef de `asa_frauddetection_job_demo`taak een naam, geef een abonnement, resourcegroep en locatie op.

    Het is een goed idee om de taak en de gebeurtenishub in dezelfde regio te plaatsen voor de beste prestaties en zodat u niet betaalt om gegevens tussen regio's over te dragen.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Klik **op Maken**.

    De taak wordt gemaakt en de portal geeft taakgegevens weer. Er wordt echter nog niets uitgevoerd, maar u moet de taak configureren voordat deze kan worden gestart.

### <a name="configure-job-input"></a>Taakinvoer configureren

1. Zoek en selecteer de `asa_frauddetection_job_demo` taak Stream Analytics in het dashboard of het deelvenster Alle **bronnen.** 
2. Klik **in** het gedeelte Overzicht van het deelvenster Stream Analytics op het vak **Invoer.**

   ![Invoervak onder Topologie in het taakvenster Streaming Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Klik **op Streaminvoer toevoegen** en selecteer **Gebeurtenishub**. Vul vervolgens de nieuwe invoerpagina in met de volgende informatie:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Invoeralias  |  CallStream   |  Voer een naam in om de invoer van de taak te identificeren.   |
   |Abonnement   |  \<Uw abonnement\> |  Selecteer het Azure-abonnement met de gebeurtenishub die u hebt gemaakt.   |
   |Event hub-naamruimte  |  asa-eh-ns-demo |  Voer de naam in van de naamruimte van de gebeurtenishub.   |
   |Event Hub-naam  | asa-eh-frauddetection-demo | Selecteer de naam van uw gebeurtenishub.   |
   |Naam van het Event Hub-beleid  | asa-policy-manage-demo | Selecteer het toegangsbeleid dat u eerder hebt gemaakt.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Klik **op Maken**.

## <a name="create-queries-to-transform-real-time-data"></a>Query's maken om realtime gegevens te transformeren

Op dit moment hebt u een Stream Analytics-taak ingesteld om een binnenkomende gegevensstroom te lezen. De volgende stap is het maken van een query die de gegevens in realtime analyseert. Stream Analytics ondersteunt een eenvoudig, declaratief querymodel dat transformaties voor realtime verwerking beschrijft. De query's gebruiken een SQL-achtige taal met een aantal extensies die specifiek zijn voor Stream Analytics. 

Een eenvoudige query kan gewoon lezen alle inkomende gegevens. U maakt echter vaak query's die zoeken naar specifieke gegevens of naar relaties in de gegevens. In dit gedeelte van de zelfstudie maakt en test u verschillende query's om een aantal manieren te leren waarop u een invoerstroom voor analyse transformeren. 

De query's die u hier maakt, geven alleen de getransformeerde gegevens weer op het scherm. In een latere sectie configureert u een uitvoergootsteen en een query die de getransformeerde gegevens naar die gootsteen schrijft.

Zie de [naslaggids voor querytaal van Azure Stream Analytics.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

### <a name="get-sample-data-for-testing-queries"></a>Voorbeeldgegevens voor het testen van query's opvragen

De TelcoGenerator-app verzendt oproeprecords naar de gebeurtenishub en uw Stream Analytics-taak is geconfigureerd om te lezen vanuit de gebeurtenishub. U een query gebruiken om de taak te testen om ervoor te zorgen dat deze correct wordt gelezen. Als u een query in de Azure-console wilt testen, hebt u voorbeeldgegevens nodig. Voor deze walkthrough haalt u voorbeeldgegevens uit de stream die in de gebeurtenishub komt.

1. Zorg ervoor dat de TelcoGenerator-app wordt uitgevoerd en oproeprecords produceert.
2. Ga in de portal terug naar het taakvenster Streaming Analytics. (Als u het deelvenster `asa_frauddetection_job_demo` hebt gesloten, zoekt u in het deelvenster **Alle bronnen.)**
3. Klik op het vak **Query.** Azure bevat de ingangen en uitvoer die zijn geconfigureerd voor de taak en laat u een query maken waarmee u de invoerstroom transformeren terwijl deze naar de uitvoer wordt verzonden.
4. Klik **in** het deelvenster Query op `CallStream` de puntjes naast de invoer en selecteer **Voorbeeldgegevens uit invoer**.

   ![Menuopties voor het gebruik van voorbeeldgegevens voor de taakinvoer van Streaming Analytics, waarbij 'Voorbeeldgegevens uit invoer' zijn geselecteerd](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Stel **Minuten** in op 3 en klik op **OK**. 
    
   ![Opties voor het bemonsteren van invoerstream met 3 minuten geselecteerd](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure bemonstert 3 minuten aan gegevens uit de invoerstroom en waarschuwt u wanneer de voorbeeldgegevens klaar zijn. (Dit duurt even.) 

De voorbeeldgegevens worden tijdelijk opgeslagen en zijn beschikbaar zolang u het queryvenster geopend houdt. Als u het queryvenster sluit, worden de voorbeeldgegevens verwijderd en zult u een nieuwe set voorbeeldgegevens moeten maken. 

Als alternatief u een .json-bestand met voorbeeldgegevens [van GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)krijgen en vervolgens dat JSON-bestand uploaden om te gebruiken als voorbeeldgegevens voor de `CallStream` invoer. 

### <a name="test-using-a-pass-through-query"></a>Testen met een doorgeefquery

Als u elke gebeurtenis wilt archiveren, u een doorloopquery gebruiken om alle velden in de payload van de gebeurtenis te lezen.

1. Voer deze query in in het queryvenster:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Net als bij SQL zijn zoekwoorden niet hoofdlettergevoelig en is witruimte niet significant.

    In deze `CallStream` query is de alias die u hebt opgegeven toen u de invoer hebt gemaakt. Als u een andere alias hebt gebruikt, gebruikt u die naam in plaats daarvan.

2. Klik **op Testen**.

    Met de taak Stream Analytics wordt de query uitgevoerd op basis van de voorbeeldgegevens en wordt de uitvoer onder aan het venster weergegeven. De resultaten geven aan dat de gebeurtenishub en de taak Streaming Analytics correct zijn geconfigureerd. (Zoals opgemerkt, maakt u later een uitvoergootsteen waar naar de query gegevens kan schrijven.)

   ![Stream Analytics-taakuitvoer, met 73 gegenereerde records](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Het exacte aantal records dat u ziet, is afhankelijk van het aantal records dat is vastgelegd in uw steekproef van 3 minuten.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Het aantal velden verminderen met een kolomprojectie

In veel gevallen heeft uw analyse niet alle kolommen uit de invoerstroom nodig. U een query gebruiken om een kleinere set geretourneerde velden te projecteren dan in de doorgeefquery.

1. Wijzig de query in de codeeditor als volgt:

    ```SQL
    SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
    FROM 
        CallStream
    ```

2. Klik nogmaals **op Testen.** 

   ![Stream Analytics-taakuitvoer voor projectie toont 25 records](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Aantal inkomende oproepen per regio: Tumbling venster met aggregatie

Stel dat u het aantal inkomende oproepen per regio wilt tellen. Wanneer u geaggregeerde functies zoals tellen wilt uitvoeren, moet u de stream segmenteren in tijdelijke eenheden (omdat de gegevensstroom zelf effectief eindeloos is). U doet dit met behulp van een [vensterfunctie](stream-analytics-window-functions.md)Streaming Analytics. U dan als eenheid met de gegevens in dat venster werken.

Voor deze transformatie wilt u een reeks tijdelijke vensters die elkaar niet overlappen: elk venster heeft een afzonderlijke set gegevens die u groeperen en samenvoegen. Dit type venster wordt aangeduid als een *Tumbling venster*. Binnen het tumbling-venster u een telling krijgen `SwitchNum`van de inkomende oproepen gegroepeerd door, die het land/de regio vertegenwoordigt waar de oproep is ontstaan. 

1. Wijzig de query in de codeeditor als volgt:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Deze query `Timestamp By` gebruikt het `FROM` trefwoord in de clausule om op te geven welk tijdstempelveld in de invoerstroom moet worden gebruikt om het tumbling-venster te definiëren. In dit geval verdeelt het venster de `CallRecTime` gegevens in segmenten door het veld in elke record. (Als er geen veld is opgegeven, gebruikt de vensterbewerking de tijd dat elke gebeurtenis op de gebeurtenishub aankomt. Zie 'Aankomsttijd versus toepassingstijd' in [Naslaginformatie over de vraagnaar query van Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). 

    De projectie `System.Timestamp`bevat , die een tijdstempel voor het einde van elk venster retourneert. 

    Als u wilt opgeven dat u een Tumbling-venster wilt `GROUP BY` gebruiken, gebruikt u de functie [TUMBLINGWINDOW](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) in de clausule. In de functie geeft u een tijdseenheid op (overal van een microseconde tot een dag) en een venstergrootte (hoeveel eenheden). In dit voorbeeld bestaat het Tumbling-venster uit intervallen van 5 seconden, zodat u elke 5 seconden aan gesprekken per land/regio wordt geteld.

2. Klik nogmaals **op Testen.** In de resultaten wordt opgemerkt dat de tijdstempels onder **WindowEnd** in stappen van 5 seconden zijn.

   ![Stream Analytics-taakuitvoer voor aggregatie met 13 records](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Sim-fraude detecteren met behulp van een selfjoin

Overweeg bijvoorbeeld frauduleus gebruik als oproepen die afkomstig zijn van dezelfde gebruiker, maar op verschillende locaties binnen 5 seconden van elkaar. Zo kan dezelfde gebruiker niet op rechtmatige wijze op hetzelfde moment een telefoongesprek vanuit de Verenigde Staten en Australië initiëren. 

Om te controleren op deze gevallen, u gebruik maken van een self-join van de streaming gegevens om de stream aan zichzelf op basis van de `CallRecTime` waarde. U vervolgens zoeken naar `CallingIMSI` oproeprecords waarbij de waarde (het `SwitchNum` oorspronkelijke getal) hetzelfde is, maar de waarde (land/regio van herkomst) niet hetzelfde is.

Wanneer u een join met streaminggegevens gebruikt, moet de join enkele beperkingen bieden voor hoe ver de overeenkomende rijen op tijd kunnen worden gescheiden. (Zoals eerder opgemerkt, de streaming gegevens is effectief eindeloos.) De tijdgrenzen voor de relatie `ON` worden gespecificeerd in de `DATEDIFF` clausule van de join, met behulp van de functie. In dit geval is de join gebaseerd op een interval van 5 seconden van gespreksgegevens.

1. Wijzig de query in de codeeditor als volgt: 

    ```SQL
    SELECT  System.Timestamp as Time, 
        CS1.CallingIMSI, 
        CS1.CallingNum as CallingNum1, 
        CS2.CallingNum as CallingNum2, 
        CS1.SwitchNum as Switch1, 
        CS2.SwitchNum as Switch2 
    FROM CallStream CS1 TIMESTAMP BY CallRecTime 
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
        ON CS1.CallingIMSI = CS2.CallingIMSI 
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
    WHERE CS1.SwitchNum != CS2.SwitchNum
    ```

    Deze query is net als `DATEDIFF` elke SQL join, behalve voor de functie in de join. Deze versie `DATEDIFF` van is specifiek voor Streaming Analytics `ON...BETWEEN` en moet in de clausule worden weergegeven. De parameters zijn een tijdseenheid (seconden in dit voorbeeld) en de aliassen van de twee bronnen voor de join. Dit is anders dan `DATEDIFF` de standaard SQL-functie.

    De `WHERE` clausule bevat de voorwaarde dat de frauduleuze oproep wordt gevlagd: de oorspronkelijke switches zijn niet hetzelfde. 

2. Klik nogmaals **op Testen.** 

   ![Stream Analytics-taakuitvoer voor zelf-deelnemen, met 6 gegenereerde records](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Klik **op Opslaan** om de zelf-deelnemende query op te slaan als onderdeel van de functie Streaming Analytics. (De voorbeeldgegevens worden niet opgeslagen.)

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Een uitvoergootsteen maken om getransformeerde gegevens op te slaan

U hebt een gebeurtenisstream, een gebeurtenishubdie wordt ingevoerd voor het opnemen van gebeurtenissen en een query om een transformatie via de stream uit te voeren. De laatste stap is het definiëren van een uitvoergootsteen voor de taak, dat wil zeggen een plek om de getransformeerde stream naar te schrijven. 

U veel resources gebruiken als uitvoersinks: een SQL Server-database, tabelopslag, Gegevensmeeropslag, Power BI en zelfs een andere gebeurtenishub. Voor deze zelfstudie schrijft u de stream naar Azure Blob Storage, wat een typische keuze is voor het verzamelen van gebeurtenisgegevens voor latere analyses, omdat deze geschikt is voor ongestructureerde gegevens.

Als u een bestaand blob-opslagaccount hebt, u dat gebruiken. Voor deze zelfstudie leert u hoe u een nieuw opslagaccount maken.

### <a name="create-an-azure-blob-storage-account"></a>Een Azure Blob Storage-account maken

1. Selecteer linksboven in de Azure-portal **de**optie Een > **bronopslagopslagaccount** > **Storage**maken . Vul de taakpagina Opslagaccount in met **Naam** ingesteld op 'asaehstorage', **Locatie** ingesteld op 'Oost-VS', **Resourcegroep** ingesteld op 'asa-eh-ns-rg' (host het opslagaccount in dezelfde resourcegroep als de taak Streaming voor betere prestaties). De overige instellingen kunnen op de standaardwaarden blijven staan.  

   ![Opslagaccount maken in Azure-portal](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Ga in de Azure-portal terug naar het taakvenster Streaming Analytics. (Als u het deelvenster `asa_frauddetection_job_demo` hebt gesloten, zoekt u in het deelvenster **Alle bronnen.)**

3. Klik in de sectie **Taaktopologie** op het vak **Uitvoer.**

4. Klik **in** het deelvenster Uitvoer op **Toevoegen** en selecteer **Blob-opslag**. Vul vervolgens de pagina Nieuwe uitvoer in met de volgende informatie:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Uitvoeralias  |  CallStream-frauduleuze oproepen   |  Voer een naam in om de uitvoer van de taak te identificeren.   |
   |Abonnement   |  \<Uw abonnement\> |  Selecteer het Azure-abonnement met het opslagaccount dat u hebt gemaakt. Het opslagaccount kan voor hetzelfde of een ander abonnement gelden. Voor dit voorbeeld wordt aangenomen dat u een opslagaccount voor hetzelfde abonnement hebt gemaakt. |
   |Storage-account  |  asaehstorage |  Voer de naam in van het opslagaccount dat u hebt gemaakt. |
   |Container  | asa-frauduleuzeoproepen-demo | Kies Nieuw maken en voer een containernaam in. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Klik op **Opslaan**. 


## <a name="start-the-streaming-analytics-job"></a>De functie Streaming Analytics starten

De taak is nu geconfigureerd. U hebt een invoer (de gebeurtenishub), een transformatie (de query om te zoeken naar frauduleuze oproepen) en een uitvoer (blobopslag) opgegeven. Je nu beginnen met de klus. 

1. Zorg ervoor dat de TelcoGenerator-app actief is.

2. Klik in het taakvenster op **Start**. Selecteer **nu**in het taakvenster **Starten** voor de begintijd van de uitvoer van taak . 

   ![De functie Stream Analytics starten](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>De getransformeerde gegevens onderzoeken

Je hebt nu een volledige Streaming Analytics-taak. De taak is het onderzoeken van een stroom van telefoongesprekken metadata, op zoek naar frauduleuze telefoontjes in real time, en het schrijven van informatie over die frauduleuze gesprekken naar opslag. 

Als u deze zelfstudie wilt voltooien, u de gegevens bekijken die worden vastgelegd door de taak Streaming Analytics. De gegevens worden in stukjes (bestanden) naar Azure Blog Storage geschreven. U elk hulpprogramma gebruiken dat Azure Blob Storage leest. Zoals vermeld in de sectie Voorwaarden u Azure-extensies gebruiken in Visual Studio of een hulpprogramma gebruiken zoals [Azure Storage Explorer](https://storageexplorer.com/) of [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Wanneer u de inhoud van een bestand in blobopslag bekijkt, ziet u zoiets als het volgende:

   ![Azure blob-opslag met Streaming Analytics-uitvoer](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn aanvullende artikelen die doorgaan met het scenario voor fraudedetectie en de bronnen gebruiken die u in deze zelfstudie hebt gemaakt. Als u door wilt gaan, raadpleegt u de suggesties onder **Volgende stappen**.

Als u echter klaar bent en u de resources die u hebt gemaakt niet nodig hebt, u deze verwijderen zodat u geen onnodige Azure-kosten hoeft te maken. In dat geval raden we u aan het volgende te doen:

1. Stop de functie Streaming Analytics. Klik **boven** in het deelvenster Vacatures op **Stoppen.**
2. Stop de Telco Generator app. Druk in het opdrachtvenster waar u de app hebt gestart op Ctrl+C.
3. Als u een nieuw blob-opslagaccount hebt gemaakt alleen voor deze zelfstudie, verwijdert u het. 
4. Verwijder de taak Streaming Analytics.
5. Verwijder de gebeurtenishub.
6. Verwijder de naamruimte van de gebeurtenishub.

## <a name="get-support"></a>Ondersteuning krijgen

Probeer het Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

U deze zelfstudie voortzetten met het volgende artikel:

* [Stream Analytics en Power BI: een realtime analysedashboard voor het streamen van gegevens.](stream-analytics-power-bi-dashboard.md) In dit artikel ziet u hoe u de TelCo-uitvoer van de streamanalytics-taak naar Power BI verzendt voor realtime visualisatie en analyse.

Zie de volgende artikelen voor meer informatie over Stream Analytics in het algemeen:

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
