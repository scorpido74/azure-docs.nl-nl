---
title: IoT Edge-modules instellen in Azure SQL Edge
description: In deel twee van deze driedelige Azure SQL Edge-zelfstudie over het voorspellen van verontreinigingen in ijzererts gaat u IoT Edge-modules en -verbindingen instellen.
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: bbbbe09aac30165a2f9b7bbe54f58e0c09a6cf09
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593497"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>IoT Edge-modules en -verbindingen instellen

In deel twee van deze driedelige zelfstudie over het voorspellen van verontreinigingen in ijzererts in Azure SQL Edge gaat u de volgende IoT Edge-modules en -verbindingen instellen:

- Azure SQL Edge
- Gegevensgenerator IoT Edge-module

## <a name="create-azure-stream-analytics-module"></a>Azure Stream Analytics-module maken

Maak een Azure Stream Analytics-module om te gebruiken in deze zelfstudie. Zie [Streaming-taken gebruiken met SQL Database Edge](https://docs.microsoft.com/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge) voor meer informatie over het gebruiken van streaming-taken met SQL Edge.

Nadat de Azure Stream Analytics-taak is gemaakt met de hostomgeving ingesteld op Edge, stelt u de in- en uitvoer voor de zelfstudie in.

1. Klik op **+Stroominvoer toevoegen** om de **invoer** te maken. Vul de detailsectie in met de volgende gegevens:

   Veld|Waarde
   -----|-----
   Serialisatie-indeling voor gebeurtenissen|JSON
   Encoding|UTF-8
   Gebeurteniscompressietype|Geen

2. Klik op **+Toevoegen** en kies SQL Database om de **uitvoer** te maken. Vul de detailsectie in met de volgende gegevens.

   > [!NOTE]
   > Het in deze sectie opgegeven wachtwoord moet worden opgegeven als SQL SA-wachtwoord bij het implementeren van de SQL Edge-module in de sectie **De Azure SQL Edge-module implementeren**.

   Veld|Waarde
   -----|-----
   Database|IronOreSilicaPrediction
   Servernaam|tcp:.,1433
   Gebruikersnaam|sa
   Wachtwoord|Geef een sterk wachtwoord op
   Tabel|IronOreMeasurements1

3. Navigeer naar de **Query**-sectie en stel de query als volgt in:

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. Selecteer **Publiceren** onder **Configureren** en selecteer de knop **Publiceren**. Sla de SAS-URI op voor gebruik met de SQL Database Edge-module.

## <a name="specify-container-registry-credentials"></a>Referenties voor het containerregister opgeven

De referenties van de containerregisters waar de module-installatiekopieën worden gehost, moeten worden opgegeven. Deze zijn te vinden in het containerregister dat in uw resource groep is gemaakt. Navigeer naar de sectie **Toegangssleutels**. Noteer de volgende velden:

- Registernaam
- Aanmeldingsserver
- Gebruikersnaam
- Wachtwoord

Geef nu de containerreferenties op in de IoT Edge-module.

1. Navigeer naar de IoT-hub die in uw resourcegroep is gemaakt.

2. Klik in de sectie **IoT Edge** onder **Automatisch apparaatbeheer** op **Apparaat-id**. De id voor deze zelfstudie is `IronOrePredictionDevice`.

3. Selecteer de sectie **Modules instellen**.

4. Voer onder **Containerregisterreferenties** de volgende waarden in:

   _Veld_|_Waarde_
   -------|-------
   Naam|Registernaam
   Adres|Aanmeldingsserver
   Gebruikersnaam|Gebruikersnaam
   Wachtwoord|Wachtwoord
  
## <a name="deploy-the-data-generator-module"></a>De gegevensgeneratormodule implementeren

1. Klik in de sectie **IoT Edge-modules** op **+ Toevoegen** en selecteer **IoT Edge-module**.

2. Geef de naam van een IoT Edge-module en de URI van de installatiekopie op.
   De URI van de installatiekopie is te vinden in het containerregister in de resourcegroep. Selecteer de sectie **Opslagplaatsen** onder **Services**. Kies voor deze zelfstudie de opslagplaats `silicaprediction`. Selecteer de gewenste tag. De URI van de installatiekopie heeft de volgende indeling:

   *aanmeldingsserver van het containerregister*/*opslagplaatsnaam*:*tagnaam*

   Bijvoorbeeld:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. Klik op **Toevoegen**.

## <a name="deploy-the-azure-sql-edge-module"></a>De Azure SQL Edge-module implementeren

1. Implementeer de Azure SQL Edge-module door de stappen te volgen die worden beschreven in [Azure SQL Edge implementeren (preview-versie)](https://docs.microsoft.com/azure/sql-database-edge/deploy-portal#deploy-sql-database-edge).

2. Geef bij de **Route opgeven** op de pagina **Modules instellen** als volgt de routes op voor de communicatie van de module met de IoT Edge-hub. 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   Bijvoorbeeld:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. Zorg ervoor dat in de **moduledubbel**-instellingen SQLPackage en ASAJonInfo worden bijgewerkt met de relevante SAS-URL's die u eerder in de zelfstudie hebt opgeslagen.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>Volgende stappen

- [ML-model op Azure SQL Edge implementeren met behulp van ONNX](tutorial-run-ml-model-on-sql-edge.md)
