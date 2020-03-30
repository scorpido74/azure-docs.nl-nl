---
title: Azure Stream Analytics-integratie met Azure Machine Learning
description: In dit artikel wordt beschreven hoe u snel een eenvoudige Azure Stream Analytics-taak instellen waarin Azure Machine Learning wordt geïntegreerd met behulp van een door de gebruiker gedefinieerde functie.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: b33aeeee03fa57d87a60fd4c1904d5e4a86dd004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067092"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Sentimentanalyse uitvoeren met Azure Stream Analytics en Azure Machine Learning Studio (klassiek)

In dit artikel wordt beschreven hoe u snel een eenvoudige Azure Stream Analytics-taak instellen waarin Azure Machine Learning Studio (klassiek) wordt geïntegreerd. U gebruikt een Machine Learning sentiment analytics model van de Cortana Intelligence Gallery om streaming tekstgegevens te analyseren en de sentimentscore in realtime te bepalen. Met behulp van de Cortana Intelligence Suite u deze taak uitvoeren zonder u zorgen te maken over de fijne kneepjes van het bouwen van een sentiment analytics-model.

> [!TIP]
> Het wordt ten zeerste aanbevolen om [Azure Machine Learning UDF's](machine-learning-udf.md) te gebruiken in plaats van Azure Machine Learning Studio (klassieke) UDF voor betere prestaties en betrouwbaarheid.

U wat u uit dit artikel leert toepassen op scenario's zoals deze:

* Het analyseren van real-time sentiment op streaming Twitter-gegevens.
* Het analyseren van records van klantchats met ondersteunend personeel.
* Het evalueren van reacties op forums, blogs en video's. 
* Veel andere real-time, voorspellende scoring scenario's.

In een real-world scenario, zou je de gegevens rechtstreeks uit een Twitter datastroom. Om de zelfstudie te vereenvoudigen, is deze zo geschreven dat de taak Streaming Analytics tweets krijgt van een CSV-bestand in Azure Blob-opslag. U uw eigen CSV-bestand maken of een csv-voorbeeldbestand gebruiken, zoals in de volgende afbeelding wordt weergegeven:

![Voorbeeldtweets die worden weergegeven in een CSV-bestand](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

De taak Streaming Analytics die u maakt, past het sentimentanalysemodel toe als een door de gebruiker gedefinieerde functie (UDF) op de voorbeeldtekstgegevens van het blobarchief. De uitvoer (het resultaat van de sentimentanalyse) wordt naar hetzelfde blobarchief in een ander CSV-bestand geschreven. 

De volgende figuur toont deze configuratie. Zoals opgemerkt, u voor een realistischer scenario blob-opslag vervangen door het streamen van Twitter-gegevens van een Azure Event Hubs-invoer. Bovendien u een Microsoft Power BI-real-time visualisatie van het geaggregeerde sentiment maken. [Microsoft Power BI](https://powerbi.microsoft.com/)    

![Overzicht van De Integratie van Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u het volgende hebt voordat u begint:

* Een actief Azure-abonnement.
* Een CSV-bestand met wat gegevens erin. U het eerder getoonde bestand downloaden van [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)of u uw eigen bestand maken. Voor dit artikel wordt ervan uitgegaan dat u het bestand van GitHub gebruikt.

Op een hoog niveau, om de taken die in dit artikel worden gedemonstreerd, te voltooien, gaat u als volgt te werk:

1. Maak een Azure-opslagaccount en een blobopslagcontainer en upload een csv-indelingsinvoerbestand naar de container.
3. Voeg een sentimentanalysemodel van de Cortana Intelligence Gallery toe aan uw Azure Machine Learning Studio (klassieke) werkruimte en implementeer dit model als webservice in de Machine Learning-werkruimte.
5. Maak een Stream Analytics-taak die deze webservice als functie aanroept om het sentiment voor de tekstinvoer te bepalen.
6. Start de taak Stream Analytics en controleer de uitvoer.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Een opslagcontainer maken en het CSV-invoerbestand uploaden
Voor deze stap u elk CSV-bestand gebruiken, zoals het bestand dat beschikbaar is bij GitHub.

1. Klik in de Azure-portal op **Een** > **bronopslagaccount voor****resources** > maken .

2. Geef een`samldemo` naam op (in het voorbeeld). De naam kan alleen kleine letters en cijfers gebruiken en moet uniek zijn in Azure. 

3. Geef een bestaande resourcegroep op en geef een locatie op. Voor locatie raden we aan dat alle bronnen die in deze zelfstudie zijn gemaakt, dezelfde locatie gebruiken.

    ![opslagaccountgegevens verstrekken](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Selecteer in de Azure-portal het opslagaccount. Klik in het opslagaccountblad op **Containers** en klik vervolgens op ** + &nbsp;Container** om blobopslag te maken.

    ![Blobopslagcontainer maken voor invoer](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Geef een naam op`azuresamldemoblob` voor de container (in het voorbeeld) en controleer of **het access-type** is ingesteld op **Blob.** Wanneer u klaar bent, klikt u op **OK**.

    ![blobcontainerdetails opgeven](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Selecteer in het **blad Containers** de nieuwe container, waarmee het blad voor die container wordt geopend.

7. Klik op **Uploaden**.

    ![Knop 'Uploaden' voor een container](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Upload in het **blobblad uploaden** het **sampleinput.csv-bestand** dat u eerder hebt gedownload. Selecteer **voor Blob-type** **Blob blob** en stel de blokgrootte in op 4 MB, wat voldoende is voor deze zelfstudie.

9. Klik op de knop **Uploaden** onder aan het blad.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Het sentimentanalysemodel toevoegen vanuit de Cortana Intelligence Gallery

Nu de voorbeeldgegevens zich in een blob bevinden, u het sentimentanalysemodel inschakelen in Cortana Intelligence Gallery.

1. Ga naar de [modelpagina voor voorspellende sentimentanalyses](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) in de Cortana Intelligence Gallery.  

2. Klik **op Openen in de studio**.  
   
   ![Stream Analytics Machine Learning, open Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Meld u aan om naar de werkruimte te gaan. Selecteer een locatie.

4. Klik onder aan de pagina op **Uitvoeren.** Het proces loopt, wat ongeveer een minuut duurt.

   ![experiment uitvoeren in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Nadat het proces is uitgevoerd, selecteert u **Webservice implementeren** onder aan de pagina.

   ![experiment implementeren in Machine Learning Studio als webservice](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Als u wilt valideren dat het sentimentanalysemodel klaar is voor gebruik, klikt u op de knop **Testen.** Geef tekstinvoer zoals "Ik hou van Microsoft". 

   ![testexperiment in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Als de test werkt, ziet u een resultaat dat vergelijkbaar is met het volgende voorbeeld:

   ![testresultaten in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Klik **in** de kolom Apps op de koppeling **Excel 2010 of eerdere werkmap** om een Excel-werkmap te downloaden. De werkmap bevat de API-sleutel en de URL die u later nodig hebt om de taak Stream Analytics in te stellen.

    ![Stream Analytics Machine Learning, snelle blik](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Een Stream Analytics-taak maken die het Machine Learning-model gebruikt

U nu een Stream Analytics-taak maken die de voorbeeldtweets uit het CSV-bestand in blobopslag leest. 

### <a name="create-the-job"></a>De taak maken

1. Ga naar de [Azure-portal.](https://portal.azure.com)  

2. Klik **op Een vacature** > **Internet of Things** > **Stream Analytics**maken . 

3. Geef de `azure-sa-ml-demo`taak een naam, geef een abonnement op, geef een bestaande resourcegroep op of maak een nieuwe en selecteer de locatie voor de taak.

   ![instellingen opgeven voor de nieuwe Stream Analytics-taak](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>De taakinvoer configureren
De taak krijgt zijn invoer van het CSV-bestand dat u eerder hebt geüpload naar blob-opslag.

1. Nadat de taak is gemaakt, klikt u onder **Job Topology** in het taakblad op de optie **Invoer.**    

2. Klik in het blad **Ingangen** op **Opslag Streaminvoerblob** >**toevoegen**

3. Vul het **blobopslagblad** in met de volgende waarden:

   
   |Veld  |Waarde  |
   |---------|---------|
   |**Invoeralias** | De naam `datainput` gebruiken en **blobopslag selecteren in uw abonnement**       |
   |**Opslagaccount**  |  Selecteer het opslagaccount dat u eerder hebt gemaakt.  |
   |**Container**  | Selecteer de container die`azuresamldemoblob`u eerder hebt gemaakt ( )        |
   |**Serialisatie-indeling voor gebeurtenissen**  |  **Csv selecteren**       |

   ![Instellingen voor nieuwe Stream Analytics-taakinvoer](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Klik op **Opslaan**.

### <a name="configure-the-job-output"></a>De taakuitvoer configureren
De taak stuurt resultaten naar dezelfde blobopslag waar deze invoer krijgt. 

1. Klik **onder Taaktopologie** in het taakblad op de optie **Uitvoer.**  

2. Klik in het blad **Uitvoer** op**Blob-opslag** **toevoegen** >en `datamloutput`voeg vervolgens een uitvoer toe met de alias . 

3. Vul het **blobopslagblad** in met de volgende waarden:

   |Veld  |Waarde  |
   |---------|---------|
   |**Uitvoeralias** | De naam `datamloutput` gebruiken en **blobopslag selecteren in uw abonnement**       |
   |**Opslagaccount**  |  Selecteer het opslagaccount dat u eerder hebt gemaakt.  |
   |**Container**  | Selecteer de container die`azuresamldemoblob`u eerder hebt gemaakt ( )        |
   |**Serialisatie-indeling voor gebeurtenissen**  |  **Csv selecteren**       |

   ![Instellingen voor de nieuwe stream analytics-taakuitvoer](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Klik op **Opslaan**.   


### <a name="add-the-machine-learning-function"></a>De functie Machine Learning toevoegen 
Eerder publiceerde u een Machine Learning-model voor een webservice. In dit scenario, wanneer de taak Stream Analysis wordt uitgevoerd, wordt elke voorbeeldtweet van de invoer naar de webservice gestuurd voor sentimentanalyse. De Machine Learning-webservice`positive`retourneert een gevoel ( , `neutral`of `negative`) en een waarschijnlijkheid dat de tweet positief is. 

In dit gedeelte van de zelfstudie definieert u een functie in de taak StreamAnalysis. De functie kan worden aangeroepen om een tweet naar de webservice te sturen en het antwoord terug te krijgen. 

1. Zorg ervoor dat u de URL en API-sleutel van de webservice hebt die u eerder in de Excel-werkmap hebt gedownload.

2. Navigeer naar uw taakblad > **Functies** > **+ AzureML toevoegen** > **AzureML**

3. Vul het **azure machine learning-functieblad** in met de volgende waarden:

   |Veld  |Waarde  |
   |---------|---------|
   | **Functiealias** | Gebruik de `sentiment` naam en selecteer **Azure Machine Learning-functie-instellingen handmatig opgeven,** waardoor u de optie krijgt om de URL en de sleutel in te voeren.      |
   | **Url**| Plak de URL van de webservice.|
   |**Sleutel** | Plak de API-sleutel. |
  
   ![Instellingen om de functie Machine Learning toe te voegen aan de taak Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Klik op **Opslaan**.

### <a name="create-a-query-to-transform-the-data"></a>Een query maken om de gegevens te transformeren

Stream Analytics gebruikt een declaratieve, SQL-gebaseerde query om de invoer te onderzoeken en te verwerken. In deze sectie maakt u een query die elke tweet van invoer leest en vervolgens de functie Machine Learning aanroept om sentimentanalyse uit te voeren. De query stuurt het resultaat vervolgens naar de uitvoer die u hebt gedefinieerd (blobopslag).

1. Ga terug naar het overzichtsblad van de taak.

2.  Klik **onder Taaktopologie**op het vak **Query.**

3. Voer de volgende query in:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    De query roept de functie aan`sentiment`die u eerder hebt gemaakt ( ) om sentimentanalyse uit te voeren op elke tweet in de invoer. 

4. Klik op **Save** om de query op te slaan.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>De Stream Analytics-taak starten en uitvoer controleren

Je nu de streamanalytics-taak starten.

### <a name="start-the-job"></a>Taak starten
1. Ga terug naar het overzichtsblad van de taak.

2. Klik boven aan het blad op **Start.**

3. Selecteer in de **taak Start**de optie **Aangepast**en selecteer vervolgens een dag voordat u het CSV-bestand uploadde naar blobopslag. Als u klaar bent, klikt u op **Start**.  


### <a name="check-the-output"></a>Controleer de uitvoer
1. Laat de taak enkele minuten duren totdat u activiteit ziet in het vak **Controle.** 

2. Als u een hulpmiddel hebt dat u normaal gesproken gebruikt om de `azuresamldemoblob` inhoud van blobopslag te onderzoeken, gebruikt u dat gereedschap om de container te onderzoeken. Als u ook de volgende stappen in de Azure-portal uitvoeren:

    1. Zoek in het `samldemo` portaal het opslagaccount en vind `azuresamldemoblob` binnen het account de container. U ziet twee bestanden in de container: het bestand met de voorbeeldtweets en een CSV-bestand dat wordt gegenereerd door de taak Stream Analytics.
    2. Klik met de rechtermuisknop op het gegenereerde bestand en selecteer **Download**. 

   ![CSV-taakuitvoer downloaden vanuit Blob-opslag](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Open het gegenereerde CSV-bestand. U ziet zoiets als het volgende voorbeeld:  
   
   ![Stream Analytics Machine Learning, CSV-weergave](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Metrische gegevens bekijken
U ook azure Machine Learning-functiegerelateerde statistieken bekijken. De volgende functiegerelateerde statistieken worden weergegeven in het vak **Controle** in het taakblad:

* **Functieaanvragen** geven het aantal aanvragen aan dat naar een Machine Learning-webservice wordt verzonden.  
* **Functiegebeurtenissen** geven het aantal gebeurtenissen in de aanvraag aan. Standaard bevat elk verzoek voor een Machine Learning-webservice maximaal 1.000 gebeurtenissen.  


## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API en Machine Learning integreren](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)



