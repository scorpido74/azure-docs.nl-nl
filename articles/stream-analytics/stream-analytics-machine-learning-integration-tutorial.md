---
title: Integratie met Azure Machine Learning Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u snel een eenvoudige Azure Stream Analytics-taak kunt instellen die Azure Machine Learning integreert met behulp van een door de gebruiker gedefinieerde functie.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: b33aeeee03fa57d87a60fd4c1904d5e4a86dd004
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80067092"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Sentiment-analyse uitvoeren met Azure Stream Analytics en Azure Machine Learning Studio (klassiek)

In dit artikel wordt beschreven hoe u snel een eenvoudige Azure Stream Analytics-taak kunt instellen die Azure Machine Learning Studio (klassiek) integreert. U gebruikt een Machine Learning sentiment Analytics-model van de Cortana Intelligence Gallery voor het analyseren van streaming-tekst gegevens en het bepalen van de sentiment-Score in realtime. Met behulp van de Cortana Intelligence Suite kunt u deze taak uitvoeren zonder dat u zich zorgen hoeft te maken over de complexiteit van het bouwen van een sentiment Analytics-model.

> [!TIP]
> Het is raadzaam om [Azure machine learning UDFs](machine-learning-udf.md) te gebruiken in plaats van Azure machine learning Studio (klassiek) UDF voor verbeterde prestaties en betrouw baarheid.

U kunt de informatie in dit artikel over de volgende scenario's gebruiken:

* Het analyseren van real-time sentiment voor het streamen van Twitter-gegevens.
* Het analyseren van records van klant chats met ondersteunings personeel.
* Opmerkingen over forums, blogs en Video's evalueren. 
* Veel andere scenario's in realtime, voorspellende scores.

In een praktijk scenario haalt u de gegevens rechtstreeks op uit een Twitter-gegevens stroom. Om de zelf studie te vereenvoudigen, wordt deze geschreven, zodat de streaming Analytics-taak tweets uit een CSV-bestand in Azure Blob Storage krijgt. U kunt uw eigen CSV-bestand maken of u kunt een voor beeld van een CSV-bestand gebruiken, zoals wordt weer gegeven in de volgende afbeelding:

![Voorbeeld Tweets weer gegeven in een CSV-bestand](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

De streaming Analytics-taak die u maakt, past het sentiment Analytics-model toe als een door de gebruiker gedefinieerde functie (UDF) op de voorbeeld tekst gegevens uit de Blob-opslag. De uitvoer (het resultaat van de analyse van de sentiment) wordt geschreven naar dezelfde Blob-opslag in een ander CSV-bestand. 

In de volgende afbeelding ziet u deze configuratie. Zoals vermeld, kunt u voor een realistischer scenario Blob Storage vervangen door Twitter-gegevens van een Azure-Event Hubs invoer. Daarnaast kunt u een [micro soft power bi](https://powerbi.microsoft.com/) realtime-visualisatie van het aggregatie sentiment bouwen.    

![Overzicht van Stream Analytics Machine Learning-integratie](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u het volgende hebt voordat u begint:

* Een actief Azure-abonnement.
* Een CSV-bestand met een aantal gegevens. U kunt het bestand downloaden dat u eerder hebt weer gegeven via [github](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)of u kunt uw eigen bestand maken. In dit artikel wordt ervan uitgegaan dat u het bestand gebruikt vanuit GitHub.

Op hoog niveau kunt u het volgende doen om de taken uit te voeren die in dit artikel worden getoond:

1. Maak een Azure-opslag account en een BLOB storage-container en upload een invoer bestand in CSV-indeling naar de container.
3. Voeg een sentiment Analytics-model van de Cortana Intelligence Gallery toe aan uw Azure Machine Learning Studio (klassieke) werk ruimte en implementeer dit model als een webservice in de Machine Learning-werk ruimte.
5. Maak een Stream Analytics-taak die deze webservice aanroept als functie om sentiment te bepalen voor de tekst invoer.
6. Start de Stream Analytics-taak en controleer de uitvoer.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Een opslag container maken en het CSV-invoer bestand uploaden
Voor deze stap kunt u elk CSV-bestand gebruiken, zoals de versie die beschikbaar is via GitHub.

1. Klik in de Azure Portal op een opslag account voor opslag van **een resource maken**  >  **Storage**  >  **Storage account**.

2. Geef een naam `samldemo` op (in het voor beeld). De naam mag alleen kleine letters en cijfers bevatten en moet uniek zijn binnen Azure. 

3. Geef een bestaande resource groep op en geef een locatie op. Voor de locatie is het raadzaam dat alle resources die in deze zelf studie worden gemaakt dezelfde locatie gebruiken.

    ![Details van opslag account opgeven](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Selecteer in de Azure Portal het opslag account. Klik op de Blade opslag account op **containers** en klik vervolgens op ** + &nbsp; container** om Blob-opslag te maken.

    ![BLOB storage-container maken voor invoer](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Geef een naam op voor de container ( `azuresamldemoblob` in het voor beeld) en controleer of het **toegangs type** is ingesteld op **BLOB**. Wanneer u klaar bent, klikt u op **OK**.

    ![Details van BLOB-container opgeven](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Selecteer op de Blade **containers** de nieuwe container, waarmee de Blade voor die container wordt geopend.

7. Klik op **Uploaden**.

    ![De knop uploaden voor een container](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Upload in de Blade **BLOB uploaden** het **sampleinput.csv** bestand dat u eerder hebt gedownload. Selecteer voor **BLOB-type**de optie blok- **BLOB** en stel de blok grootte in op 4 MB. Dit is voldoende voor deze zelf studie.

9. Klik op de knop **uploaden** onder aan de Blade.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Voeg het sentiment Analytics-model toe vanuit de Cortana Intelligence Gallery

Nu de voorbeeld gegevens zich in een BLOB bevindt, kunt u het sentiment-analyse model inschakelen in Cortana Intelligence Gallery.

1. Ga naar de pagina [Predictive sentiment Analytics model](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) in de Cortana Intelligence Gallery.  

2. Klik op **openen in Studio**.  
   
   ![Open Machine Learning Studio Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Meld u aan om naar de werk ruimte te gaan. Selecteer een locatie.

4. Klik onder aan de pagina op **uitvoeren** . Het proces wordt uitgevoerd. dit duurt ongeveer een minuut.

   ![experiment uitvoeren in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Nadat het proces is uitgevoerd, selecteert u **webservice implementeren** onder aan de pagina.

   ![experiment in Machine Learning Studio implementeren als een webservice](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Als u wilt controleren of het sentiment Analytics-model klaar is voor gebruik, klikt u op de knop **testen** . Geef tekst invoer op zoals ' Ik hou van micro soft '. 

   ![test experiment in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Als de test werkt, ziet u een resultaat zoals in het volgende voor beeld:

   ![test resultaten in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Klik in de kolom **apps** op de koppeling **Excel 2010 of een eerdere werkmap** om een Excel-werkmap te downloaden. De werkmap bevat de API-sleutel en de URL die u later nodig hebt om de Stream Analytics-taak in te stellen.

    ![Stream Analytics Machine Learning, snel in de oogopslag](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Een Stream Analytics-taak maken die gebruikmaakt van het Machine Learning model

U kunt nu een Stream Analytics-taak maken die de voorbeeld tweets leest uit het CSV-bestand in Blob Storage. 

### <a name="create-the-job"></a>De taak maken

1. Ga naar de [Azure Portal](https://portal.azure.com).  

2. Klik op **een resource maken**  >  **Internet of Things**  >  **Stream Analytics taak**. 

3. Noem de taak `azure-sa-ml-demo` , geef een abonnement op, geef een bestaande resource groep op of maak een nieuwe, en selecteer de locatie voor de taak.

   ![instellingen voor nieuwe Stream Analytics taak opgeven](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>De taak invoer configureren
De taak haalt de invoer op uit het CSV-bestand dat u eerder hebt geüpload naar de Blob-opslag.

1. Nadat de taak is gemaakt, klikt u onder **taak topologie** op de Blade taak op de optie **invoer** .    

2. Klik op de Blade **ingangen** op **Stream-invoer toevoegen**  > **Blob-opslag**

3. Vul de **Blob Storage** Blade in met de volgende waarden:

   
   |Veld  |Waarde  |
   |---------|---------|
   |**Invoeralias** | Gebruik de naam `datainput` en selecteer **Blob Storage selecteren bij uw abonnement**       |
   |**Opslagaccount**  |  Selecteer het opslagaccount dat u eerder hebt gemaakt.  |
   |**Container**  | Selecteer de container die u eerder hebt gemaakt ( `azuresamldemoblob` )        |
   |**Serialisatie-indeling voor gebeurtenissen**  |  **CSV** selecteren       |

   ![Instellingen voor nieuwe Stream Analytics taak invoer](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Klik op **Opslaan**.

### <a name="configure-the-job-output"></a>De taak uitvoer configureren
De taak verzendt resultaten naar dezelfde Blob-opslag waar de invoer wordt ingevoerd. 

1. Klik onder **taak topologie** op de Blade taak op de optie **uitvoer** .  

2. Klik op de Blade **uitvoer** op **Add**  > **Blob-opslag**toevoegen en voeg vervolgens een uitvoer met de alias toe `datamloutput` . 

3. Vul de **Blob Storage** Blade in met de volgende waarden:

   |Veld  |Waarde  |
   |---------|---------|
   |**Uitvoeralias** | Gebruik de naam `datamloutput` en selecteer **Blob Storage selecteren bij uw abonnement**       |
   |**Opslagaccount**  |  Selecteer het opslagaccount dat u eerder hebt gemaakt.  |
   |**Container**  | Selecteer de container die u eerder hebt gemaakt ( `azuresamldemoblob` )        |
   |**Serialisatie-indeling voor gebeurtenissen**  |  **CSV** selecteren       |

   ![Instellingen voor nieuwe Stream Analytics taak uitvoer](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Klik op **Opslaan**.   


### <a name="add-the-machine-learning-function"></a>De functie Machine Learning toevoegen 
Eerder hebt u een Machine Learning model gepubliceerd op een webservice. Als in dit scenario de stroom analyse taak wordt uitgevoerd, verzendt deze elke voorbeeld Tweet van de invoer naar de webservice voor sentiment analyse. De Machine Learning-webservice retourneert een sentiment ( `positive` , `neutral` of `negative` ) en een kans dat de Tweet positief is. 

In deze sectie van de zelf studie definieert u een functie in de analyse taak voor streams. De functie kan worden aangeroepen om een Tweet te verzenden naar de webservice en de reactie terug te halen. 

1. Zorg ervoor dat u de URL en API-sleutel van de webservice hebt die u eerder in de Excel-werkmap hebt gedownload.

2. Navigeer naar uw taak blad > **functies**  >  **+**  >  **AzureML** toevoegen

3. Vul de Blade **functie Azure machine learning** in met de volgende waarden:

   |Veld  |Waarde  |
   |---------|---------|
   | **Functiealias** | Gebruik de naam `sentiment` en selecteer **Azure machine learning functie-instellingen hand matig opgeven** , waarmee u de URL en de sleutel kunt invoeren.      |
   | **URL**| Plak de URL van de webservice.|
   |**Sleutel** | Plak de API-sleutel. |
  
   ![Instellingen om Machine Learning functie toe te voegen aan Stream Analytics-taak](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Klik op **Opslaan**.

### <a name="create-a-query-to-transform-the-data"></a>Een query maken om de gegevens te transformeren

Stream Analytics maakt gebruik van een declaratieve SQL-query om de invoer te controleren en te verwerken. In deze sectie maakt u een query die elke Tweet leest uit invoer en roept vervolgens de functie Machine Learning aan om sentiment analyse uit te voeren. De query verzendt vervolgens het resultaat naar de uitvoer die u hebt gedefinieerd (Blob Storage).

1. Ga terug naar de Blade overzicht van taken.

2.  Klik onder **taak topologie**op het **query** -vak.

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

    Met de query wordt de functie aangeroepen die u eerder hebt gemaakt ( `sentiment` ) om sentiment analyse te kunnen uitvoeren op elke tweet in de invoer. 

4. Klik op **Save** om de query op te slaan.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>De Stream Analytics-taak starten en uitvoer controleren

U kunt nu de Stream Analytics-taak starten.

### <a name="start-the-job"></a>Taak starten
1. Ga terug naar de Blade overzicht van taken.

2. Klik boven aan de Blade op **starten** .

3. Selecteer in de **taak starten**de optie **aangepast**en selecteer vervolgens één dag voorafgaand aan het moment waarop u het CSV-bestand naar de Blob-opslag uploadt. Wanneer u klaar bent, klikt u op **Start**.  


### <a name="check-the-output"></a>De uitvoer controleren
1. Laat de taak enkele minuten uitvoeren totdat u de activiteit in het vak **controle** ziet. 

2. Als u een hulp programma hebt dat u normaal gesp roken gebruikt om de inhoud van de Blob-opslag te controleren, gebruikt u dat hulp programma om de container te onderzoeken `azuresamldemoblob` . U kunt ook de volgende stappen uitvoeren in de Azure Portal:

    1. Zoek in de portal naar het `samldemo` opslag account en ga binnen het account naar de `azuresamldemoblob` container. U ziet twee bestanden in de container: het bestand met het voor beeld-tweets en een CSV-bestand dat door de Stream Analytics-taak wordt gegenereerd.
    2. Klik met de rechter muisknop op het gegenereerde bestand en selecteer vervolgens **downloaden**. 

   ![CSV-taak uitvoer downloaden uit Blob Storage](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Open het gegenereerde CSV-bestand. U ziet iets zoals in het volgende voor beeld:  
   
   ![Stream Analytics Machine Learning, CSV-weer gave](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Metrische gegevens bekijken
U kunt ook Azure Machine Learning metrische gegevens over de functie weer geven. De volgende metrische functie gegevens worden weer gegeven in het vak **bewaking** op de Blade taak:

* Met **functie aanvragen** wordt het aantal aanvragen aangegeven dat is verzonden naar een machine learning-webservice.  
* **Functie gebeurtenissen** geeft het aantal gebeurtenissen in de aanvraag aan. Elke aanvraag voor een Machine Learning-webservice bevat standaard Maxi maal 1.000 gebeurtenissen.  


## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API en Machine Learning integreren](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)



