---
title: Integratie van Azure Stream Analytics met Azure Machine Learning
description: In dit artikel wordt beschreven hoe u snel een eenvoudige Azure Stream Analytics-taak die is geïntegreerd Azure Machine Learning, met behulp van een gebruiker gedefinieerde functie instellen.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.custom: seodec18
ms.openlocfilehash: ab510f7ee2f95a2c3373c0514c31554598451b27
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173319"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-preview"></a>Sentiment-analyse uitvoeren met Azure Stream Analytics en Azure Machine Learning Studio (preview-versie)
In dit artikel wordt beschreven hoe u snel een eenvoudige Azure Stream Analytics-taak kunt instellen waarmee Azure Machine Learning Studio worden geïntegreerd. Kunt u een Machine Learning sentiment analytics-model van de Cortana Intelligence Gallery streaming gegevens analyseren en te bepalen van de gevoelsscore in realtime. Met behulp van de Cortana Intelligence Suite kunt u deze taak wordt uitgevoerd zonder dat u de complexiteit van het bouwen van een gevoel analytics-model.

U kunt het geleerde toepast in dit artikel voor dergelijke scenario's:

* Analyse van realtime sentimentanalyse voor streaming-gegevens van Twitter.
* Analyseren van de records van de klant chats met ondersteuningsmedewerkers.
* Evaluatie van opmerkingen over forums, blogs en video's. 
* Veel andere realtime, voorspellende scoring scenario's.

In een Praktijkscenario krijgt u de gegevens rechtstreeks vanuit een Twitter-gegevensstroom. Ter vereenvoudiging van de zelfstudie, wordt zodat de Streaming Analytics-taak tweets opgehaald uit een CSV-bestand in Azure Blob-opslag geschreven. U kunt uw eigen CSV-bestand maken of kunt u een voorbeeld-CSV-bestand, zoals wordt weergegeven in de volgende afbeelding:

![Voorbeeld van tweets wordt weergegeven in een CSV-bestand](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

De stream Analytics-taak die u maakt past het sentiment analytics-model als een gebruiker gedefinieerde functie (UDF's) op de voorbeeldgegevens van de tekst van de blob-archief. De uitvoer (het resultaat van de sentimentanalyse) wordt aan dezelfde opslag-blob in een andere CSV-bestand geschreven. 

De volgende afbeelding ziet u deze configuratie. Zoals opgemerkt, voor een realistischer scenario, kunt u blob-opslag vervangen met het streamen van Twitter-gegevens uit een Azure Event Hubs-invoer. U kunt bovendien bouwen een [Microsoft Power BI](https://powerbi.microsoft.com/) realtime visualisatie van de cumulatieve sentiment.    

![Overzicht van Stream Analytics Machine Learning-integratie](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u het volgende hebt voordat u begint:

* Een actief Azure-abonnement.
* Een CSV-bestand met gegevens erin. U kunt de eerder weergegeven uit bestand downloaden [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), of u kunt uw eigen bestand maken. In dit artikel wordt ervan uitgegaan dat u het bestand vanuit GitHub.

Op een hoog niveau voor het voltooien van de taken in dit artikel wordt gedemonstreerd doen u het volgende:

1. Maak een Azure storage-account en een blob storage-container en een CSV-indeling invoerbestand uploaden naar de container.
3. Voeg een sentiment Analytics-model van de Cortana Intelligence Gallery toe aan uw Azure Machine Learning Studio-werk ruimte en implementeer dit model als een webservice in de Machine Learning-werk ruimte.
5. Maak een Stream Analytics-taak die deze webservice als een functie aanroept om te bepalen van de tekstinvoer sentiment.
6. Start de Stream Analytics-taak en controleer de uitvoer.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Een storage-container maken en uploaden van het CSV-bestand voor invoer
Voor deze stap kunt u een CSV-bestand, zoals die beschikbaar is via GitHub.

1. Klik in de Azure-portal op **een resource maken** > **opslag** > **opslagaccount**.

2. Geef een naam (`samldemo` in het voorbeeld). De naam kan alleen kleine letters en cijfers gebruiken en deze moet uniek zijn binnen Azure. 

3. Geef een bestaande resourcegroep en een locatie opgeven. Voor de locatie, is het raadzaam dat alle resources die zijn gemaakt in deze zelfstudie gebruikt u dezelfde locatie.

    ![Geef de accountgegevens voor opslag](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Selecteer het opslagaccount in de Azure-portal. Klik in de blade opslagaccount op **Containers** en klik vervolgens op  **+ &nbsp;Container** te maken van blob-opslag.

    ![Blob storage-container voor invoer maken](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Geef een naam op voor de container (`azuresamldemoblob` in het voorbeeld) en Controleer **toegangstype** is ingesteld op **Blob**. Wanneer u klaar bent, klikt u op **OK**.

    ![Geef details op blob-container](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. In de **Containers** blade, selecteert u de nieuwe container, zodat de blade voor die container.

7. Klik op **Uploaden**.

    ![Knop voor een container uploaden](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. In de **blob uploaden** blade, upload het **sampleinput.csv** -bestand dat u eerder hebt gedownload. Voor **Blobtype**, selecteer **blok-blob** en de blokgrootte ingesteld op 4 MB, hetgeen voldoende is voor deze zelfstudie.

9. Klik op de **uploaden** knop aan de onderkant van de blade.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Het sentiment analytics-model van de Cortana Intelligence Gallery toevoegen

De voorbeeldgegevens is in een blob, kunt u het model sentiment-analyse in Cortana Intelligence Gallery kunt inschakelen.

1. Ga naar de [sentiment predictive analytics-model](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) pagina in de Cortana Intelligence Gallery.  

2. Klik op **Open in Studio**.  
   
   ![Stream Analytics Machine Learning, open Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Meld u aan om te gaan naar de werkruimte. Selecteer een locatie.

4. Klik op **uitvoeren** aan de onderkant van de pagina. Het proces wordt uitgevoerd, die duurt ongeveer een minuut.

   ![Voer experiment in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Nadat het proces met succes is uitgevoerd, selecteert u **webservice implementeren** aan de onderkant van de pagina.

   ![experiment in Machine Learning Studio implementeren als een webservice](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Als u wilt valideren dat het sentiment analytics-model klaar voor gebruik is, klikt u op de **Test** knop. Tekstinvoer, zoals "Ik graag Microsoft" bevatten. 

   ![Test-experiment in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Als de test werkt, ziet u een resultaat vergelijkbaar met het volgende voorbeeld:

   ![de resultaten in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. In de **Apps** kolom, klikt u op de **Excel 2010 of ouder werkmap** koppeling naar een Excel-werkmap downloaden. De werkmap bevat de API-sleutel en de URL die u later nodig hebt voor het instellen van de Stream Analytics-taak.

    ![Stream Analytics, Machine Learning, snelle weergave](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Een Stream Analytics-taak die gebruikmaakt van de Machine Learning-model maken

U kunt nu een Stream Analytics-taak die de tweets voorbeeld van het CSV-bestand in blob-opslag leest maken. 

### <a name="create-the-job"></a>De taak maken

1. Ga naar de [Azure Portal](https://portal.azure.com).  

2. Klik op **een resource maken** > **Internet of Things** > **Stream Analytics-taak**. 

3. Naam van de taak `azure-sa-ml-demo`, Geef een abonnement op, Geef een bestaande resourcegroep of maak een nieuwe en selecteert u de locatie voor de taak.

   ![instellingen opgeven voor nieuwe Stream Analytics-taak](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>De Taakinvoer configureren
De taak haalt de invoer uit het CSV-bestand dat u eerder hebt geüpload naar blob-opslag.

1. Nadat de taak is gemaakt, onder **Taaktopologie** in de taakblade klikt u op de **invoer** optie.    

2. In de **invoer** blade, klikt u op **toevoegen Stream Input** >**Blob storage**

3. Vul de **Blob-opslag** blade met deze waarden:

   
   |Veld  |Waarde  |
   |---------|---------|
   |**Invoeralias** | Gebruik de naam `datainput` en selecteer **blob-opslag van uw abonnement selecteren**       |
   |**Opslagaccount**  |  Selecteer het opslagaccount dat u eerder hebt gemaakt.  |
   |**Container**  | Selecteer de container die u eerder hebt gemaakt (`azuresamldemoblob`)        |
   |**Serialisatie-indeling voor gebeurtenissen**  |  Selecteer **CSV**       |

   ![Instellingen voor nieuwe Stream Analytics-Taakinvoer](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Klik op **Opslaan**.

### <a name="configure-the-job-output"></a>De taakuitvoer configureren
De taak verzendt resultaten naar dezelfde blob storage waar deze wordt ingevoerd. 

1. Onder **Taaktopologie** in de taakblade klikt u op de **uitvoer** optie.  

2. In de **uitvoer** blade, klikt u op **toevoegen** >**Blob storage**, en voeg deze uitvoer met de alias `datamloutput`. 

3. Vul de **Blob-opslag** blade met deze waarden:

   |Veld  |Waarde  |
   |---------|---------|
   |**Uitvoeralias** | Gebruik de naam `datamloutput` en selecteer **blob-opslag van uw abonnement selecteren**       |
   |**Opslagaccount**  |  Selecteer het opslagaccount dat u eerder hebt gemaakt.  |
   |**Container**  | Selecteer de container die u eerder hebt gemaakt (`azuresamldemoblob`)        |
   |**Serialisatie-indeling voor gebeurtenissen**  |  Selecteer **CSV**       |

   ![Instellingen voor nieuwe Stream Analytics-taakuitvoer](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Klik op **Opslaan**.   


### <a name="add-the-machine-learning-function"></a>De Machine Learning-functie toevoegen 
Eerder hebt u een Machine Learning-model gepubliceerd naar een webservice. In dit scenario wordt elke tweet voorbeeld verzonden wanneer de analyse van de Stream-taak wordt uitgevoerd, uit de invoer met de webservice voor sentimentanalyse. De Machine Learning-webservice retourneert een sentiment (`positive`, `neutral`, of `negative`) en een kans van de tweet wordt positief. 

In deze sectie van de zelfstudie definieert u een functie in de Stream Analysis-taak. De functie kan worden aangeroepen voor het verzenden van een tweet met de webservice en het antwoord weer toegang te krijgen. 

1. Zorg ervoor dat u hebt de web service-URL en API-sleutel die u eerder in de Excel-werkmap hebt gedownload.

2. Navigeer naar de taakblade > **functies** >  **+ toevoegen** > **AzureML**

3. Vul de **Azure Machine Learning-functie** blade met deze waarden:

   |Veld  |Waarde  |
   |---------|---------|
   | **Functiealias** | Gebruik de naam `sentiment` en selecteer **bieden Azure Machine Learning functie-instellingen handmatig** waarmee u een optie voor het invoeren van de URL en de sleutel.      |
   | **URL**| Plak de URL van de webservice.|
   |**Sleutel** | Plak de API-sleutel. |
  
   ![Instellingen voor het Machine Learning-functie toevoegen aan Stream Analytics-taak](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Klik op **Opslaan**.

### <a name="create-a-query-to-transform-the-data"></a>Een query maken om de gegevens te transformeren

Stream Analytics maakt gebruik van een declaratieve, op basis van SQL-query om te controleren van de invoer en te verwerken. In deze sectie maakt u een query maken die elke tweet van invoer leest en roept vervolgens de Machine Learning-functie voor het uitvoeren van sentimentanalyse. De query verzendt het resultaat vervolgens naar de uitvoer die u hebt gedefinieerd (blob storage).

1. Ga terug naar de overzichtsblade van de taak.

2.  Onder **Taaktopologie**, klikt u op de **Query** vak.

3. Voer de volgende query:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    De query roept de functie die u eerder hebt gemaakt (`sentiment`) om te kunnen uitvoeren van sentimentanalyse voor elke tweet in de invoer. 

4. Klik op **opslaan** de query op te slaan.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>De Stream Analytics-taak starten en uitvoer controleren

U kunt nu de Stream Analytics-taak starten.

### <a name="start-the-job"></a>Taak starten
1. Ga terug naar de overzichtsblade van de taak.

2. Klik op **Start** aan de bovenkant van de blade.

3. In de **starttaak**, selecteer **aangepaste**, en selecteer vervolgens één dag voor wanneer u het CSV-bestand geüpload naar blob-opslag. Wanneer u klaar bent, klikt u op **Start**.  


### <a name="check-the-output"></a>De uitvoer controleren
1. De taak voor een paar minuten totdat u activiteit in ziet, kunnen de **bewaking** vak. 

2. Hebt u een hulpprogramma waarmee u gesproken gebruikt om de inhoud van de blob-opslag bekijken, dit hulpprogramma gebruiken om te controleren de `azuresamldemoblob` container. U kunt ook de volgende stappen uit in Azure portal doen:

    1. Zoek in de portal de `samldemo` storage-account en binnen het account, vinden de `azuresamldemoblob` container. U ziet u twee bestanden in de container: het bestand met de voorbeeld-tweets en een CSV-bestand dat is gegenereerd door de Stream Analytics-taak.
    2. Met de rechtermuisknop op het gegenereerde bestand en selecteer vervolgens **downloaden**. 

   ![CSV-taakuitvoer downloaden uit de Blob-opslag](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Open de gegenereerde CSV-bestand. U ziet er ongeveer als volgt:  
   
   ![Stream Analytics, Machine Learning, CSV-weergave](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Metrische gegevens bekijken
U kunt ook Azure Machine Learning functie-gerelateerde metrische gegevens weergeven. De volgende functie-gerelateerde metrische gegevens worden weergegeven in de **bewaking** vak op de taakblade:

* **Functie aanvragen** geeft het aantal aanvragen dat is verzonden naar een Machine Learning-webservice.  
* **Gebeurtenissen werken** geeft het aantal gebeurtenissen in de aanvraag. Standaard bevat elke aanvraag aan een Machine Learning-webservice maximaal 1000 gebeurtenissen.  


## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integratie van REST-API en Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)



