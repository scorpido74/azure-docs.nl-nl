---
title: Ontvang weergegevens van weerpartners
description: In dit artikel wordt beschreven hoe u weergegevens van partners ophalen.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266160"
---
# <a name="get-weather-data-from-weather-partners"></a>Ontvang weergegevens van weerpartners

Azure FarmBeats helpt u om weergegevens van uw aanbieder(s) van weergegevens te gebruiken met behulp van een docker-gebaseerd Connector Framework. Met behulp van dit framework implementeren weerdataproviders een docker die kan worden geïntegreerd met FarmBeats. Momenteel worden de volgende aanbieders van weergegevens ondersteund:

[NOAA-gegevens uit Azure Open-gegevenssets](https://azure.microsoft.com/services/open-datasets/)

De weersgegevens kunnen worden gebruikt om bruikbare inzichten te genereren en AI/ML-modellen te bouwen op FarmBeats.

## <a name="before-you-start"></a>Voordat u begint

Als u weersgegevens wilt opvragen, moet u ervoor zorgen dat u FarmBeats hebt geïnstalleerd. **Weerintegratie wordt ondersteund in versie 1.2.11 of hoger**. Zie [FarmBeats installeren voor](https://aka.ms/farmbeatsinstalldocumentation)Azure FarmBeats .

## <a name="enable-weather-integration-with-farmbeats"></a>Weerintegratie inschakelen met FarmBeats

Voer de onderstaande stappen uit om weersgegevens op uw FarmBeats Data-hub te krijgen:

1. Ga naar uw FarmBeats Data hub swagger (https://yourdatahub.azurewebsites.net/swagger)

2. Navigeer naar /Partner API en maak een POST-verzoek met de volgende invoerpayload:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Als u bijvoorbeeld weergegevens van NOAA door Azure Open Datasets wilt ophalen, gebruikt u de onderstaande payload. U de naam en beschrijving naar uw voorkeur wijzigen.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > Zie [Bijlage](get-weather-data-from-weather-partner.md#appendix) voor meer informatie over het object Partner

   In de vorige stap worden de resources beschikbaar gesteld om de docker in staat te stellen deze uit te voeren in de FarmBeats-omgeving van de klant.  

   Het duurt ongeveer 10-15 minuten om de bovenstaande middelen in te richten.

3. Controleer de status van het object /Partner dat u in stap 2 hebt gemaakt. Om dit te doen, doe je een GET-aanvraag op /Partner API en controleer je op de **status** van het partnerobject. Zodra FarmBeats de partner met succes heeft ingericht, wordt de status ingesteld op **Actief**.

4. Navigeer naar /JobType API en maak een GET-aanvraag op hetzelfde. Controleer in stap 2 op de weertaken die zijn gemaakt als onderdeel van het partnertoevoegingsproces. Het veld **pipelineName** in de weertaken heeft de volgende indeling: 'partner-name_partner-type_job-naam'.

5. Nu heeft uw FarmBeats-exemplaar een actieve partner voor weergegevens en u taken uitvoeren om weergegevens op te vragen voor een bepaalde locatie (breedtegraad/lengtegraad) en een datumbereik. Het JobType(s) bevat details over welke parameters nodig zijn om weertaken uit te voeren.

   Voor NOAA-gegevens uit Azure Open Datasets worden bijvoorbeeld volgende JobType(s) gemaakt:

   - get_weather_data (Get ISD/historical weather data)
   - get_weather_forecast_data (GfS/weersvoorspellingsgegevens)

6. Noteer de **ID** en de parameters van het JobType(s).

7. Navigeer naar /Jobs API en maak een POST-aanvraag op /Jobs met de volgende invoerpayload:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Als u bijvoorbeeld **get_weather_data**uitvoert, gebruikt u de volgende payload:

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. In de vorige stap worden de weertaken uitgevoerd zoals gedefinieerd in de partnerdocker en worden weergegevens opgenomen in FarmBeats. U de status van de taak controleren door een GET-aanvraag op /Jobs in te dienen en in het antwoord naar **currentState** te zoeken. Eenmaal voltooid, is de huidige staat ingesteld op **Geslaagd**.

## <a name="query-ingested-weather-data"></a>Weergegevens opvragen

Nadat de weertaken zijn voltooid, u ingenomen weergegevens opvragen om modellen of bruikbare inzichten te bouwen. Er zijn twee manieren om weersgegevens van FarmBeats te openen en op te vragen:

- API en
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Query met REST API

Voer de onderstaande stappen uit om weergegevens op te vragen met farmbeats rest api:

1. Ga in uw FarmBeats Data-hub swagger (https://yourdatahub.azurewebsites.net/swagger), navigeer naar /WeatherDataLocation API en doe een GET-verzoek. Het antwoord heeft /WeatherDataLocation object(s) gemaakt voor de locatie (breedte/lengte) die is opgegeven als onderdeel van de taakrun. Noteer de **ID** en de **weatherDataModelId** van het object(en).

2. Maak een GET/{id} op /WeatherDataModel API voor de **weatherDataModelId** zoals vermeld in stap 1. Het "Weather Data Model" heeft alle metadata en details over de ingenomen weergegevens. **Metweermeting** in het object **Weergegevensmodel** bevat bijvoorbeeld details over welke weersinformatie wordt ondersteund en in welke typen en eenheden. Bijvoorbeeld:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Noteer het antwoord van de oproep GET/{id} voor het weergegevensmodel.

3. Navigeer naar **de Telemetrie-API** en doe een POST-verzoek met de volgende invoerpayload:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. Het antwoord met weersgegevens dat beschikbaar is voor het opgegeven tijdsbereik ziet er als volgt uit:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

In het voorgaande voorbeeld heeft het antwoord gegevens voor twee tijdstempels, samen met de maatnaam ('Temperatuur') en waarden van de gerapporteerde weergegevens in de twee tijdstempels. U moet verwijzen naar het bijbehorende weergegevensmodel (zoals beschreven in stap 2 hierboven) om het type en de eenheid van de gerapporteerde waarden te interpreteren.

### <a name="query-using-azure-time-series-insights-tsi"></a>Query met Azure Time Series Insights (TSI)

FarmBeats gebruikt [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) om gegevens op IoT-schaal in te nemen, op te slaan, op te vragen en te visualiseren- gegevens die sterk gecontextualiseerd en geoptimaliseerd zijn voor tijdreeksen.

Weersgegevens worden ontvangen op een EventHub en vervolgens naar een TSI-omgeving binnen FarmBeats-resourcegroep gepusht. Gegevens kunnen dan rechtstreeks worden opgevraagd vanuit de TSI. Zie [TSI-documentatie](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)voor meer informatie.

Volg de stappen om gegevens over TSI te visualiseren:

1. Ga naar **Azure portal** > **FarmBeats DataHub-brongroep** > selecteer **Time Series Insights-omgeving** (tsi-xxxx) > **Data Access-beleid**. Voeg de gebruiker toe met toegang tot reader of inzender.

2. Ga naar de **overzichtspagina** van **de Time Series Insights-omgeving** (tsi-xxxx) en selecteer de URL van Time Series **Insights Explorer.** U nu de ingenomen weergegevens visualiseren.

Naast het opslaan, opvragen en visualiseren van weergegevens, maakt TSI ook integratie met een Power BI-dashboard mogelijk. Zie [Gegevens visualiseren van time series insights in Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)voor meer informatie.

## <a name="appendix"></a>Bijlage

|        Partner   |  Details   |
| ------- | -------             |
|     DockerDetails - imageName         |          Docker afbeelding naam. Bijvoorbeeld docker.io/azurefarmbeats/farmbeats-noaa (afbeelding in hub.docker.com) OF myazureacr.azurecr.io/mydockerimage (afbeelding in Azure Container Registry) enzovoort. Als er geen register is opgegeven, wordt standaard hub.docker.com      |
|          DockerDetails - imageTag             |         Tag de naam van de dockerafbeelding. Standaard is 'laatste'     |
|  DockerDetails - referenties      |  Referenties om toegang te krijgen tot de privédocker. Dit wordt door de partner aan de klant verstrekt   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU. Zie [hier](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor alle Linux virtuele machines beschikbaar. Geldige waarden zijn: "Klein", 'ExtraLarge', 'Large', 'A8', 'A9', 'Medium', 'A5', 'A6', 'A7', 'STANDARD_D1', 'STANDARD_D2', 'STANDARD_D3', 'STANDARD_D4', 'STANDARD_D12 STANDARD_D11', 'STANDARD_D13', 'STANDARD_D14', 'a10', 'A11', 'STANDARD_D1_V2', 'STANDARD_D2_V2', 'STANDARD_D3_V2', 'STANDARD_D4_V2', 'STANDARD_D11_V2', 'STANDARD_D12_V2', 'STANDARD_D13_V2', 'STANDARD_D14_V2', 'STANDARD_G1', 'STANDARD_G2', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G4' , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", 'BASIC_A2', 'STANDARD_A7 BASIC_A3', 'BASIC_A4'STANDARD_A1, 'STANDARD_A11'STANDARD_A2, 'STANDARD_D15_V2'STANDARD_A3, 'STANDARD_F1 STANDARD_A4', 'STANDARD_F2', STANDARD_A5 'STANDARD_F4', 'STANDARD_F8 STANDARD_A6', 'STANDARD_F16', STANDARD_A8 'STANDARD_NV6', 'STANDARD_NV12 STANDARD_A9', STANDARD_A10 'STANDARD_NV24', 'STANDARD_NC6', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC24r STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24' , 'STANDARD_H8', 'STANDARD_H8m', 'STANDARD_H16', 'STANDARD_H16m', 'STANDARD_H16mr', 'STANDARD_H16r', 'STANDARD_A1_V2', 'STANDARD_A2_V2 STANDARD_A4_V2', 'STANDARD_A8_V2', 'STANDARD_A2m_V2', 'STANDARD_A4m_V2', 'STANDARD_A8m_V2', 'STANDARD_M64ms', 'STANDARD_M128s', 'STANDARD_D2_V3'. **Standaard is 'standard_d2_v2'**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Nee. van speciale computerknooppunten voor batchpool. De standaardwaarde is 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Azure Batch Node Agent SKU ID. Momenteel wordt alleen "batch.node.ubuntu 18.04" batch node agent ondersteund.    |
| DockerDetails - partnerReferenties | referenties voor het aanroepen van partner-API in docker. De partner moet deze informatie aan zijn klanten geven op basis van het auth-mechanisme dat bijvoorbeeld wordt ondersteund. Gebruikersnaam/wachtwoord of API-sleutels. |
| partnerType | "Weer" (Andere partnertypen in FarmBeats zijn "Sensor" en "Imagery")  |
|  name   |   Gewenste naam van de partner in FarmBeats systeem   |
|  description |  Beschrijving   |

## <a name="next-steps"></a>Volgende stappen

U hebt nu opgevraagde sensorgegevens van uw Azure FarmBeats-exemplaar. Leer nu hoe u kaarten voor uw boerderijen [genereren.](generate-maps-in-azure-farmbeats.md#generate-maps)
