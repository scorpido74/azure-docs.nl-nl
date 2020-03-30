---
title: 'Zelfstudie: Compute beheren met Azure-functies'
description: Azure-functies gebruiken om de compute van uw SQL-groep in Azure Synapse Analytics te beheren.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e0317b3a3e7ab13a78a5d1fe3672d664030436ab
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346638"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-synapse-analytics-sql-pool"></a>Azure-functies gebruiken om rekenresources te beheren in Azure Synapse Analytics SQL-groep

Deze zelfstudie gebruikt Azure-functies om rekenresources voor een SQL-groep in Azure Synapse Analytics te beheren.

Als u Azure Function App met SQL-groep wilt gebruiken, moet u een [Service Principal-account](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) maken met toegang tot bijdragen onder hetzelfde abonnement als uw SQL-poolinstantie. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Schalen op basis van timer implementeren met een Azure Resource Manager-sjabloon

Als u de sjabloon wilt implementeren, hebt u de volgende informatie nodig:

- Naam van de resourcegroep waarin uw SQL-groep instantie zich bevindt
- Naam van de logische server waarin uw SQL-poolinstantie zich bevindt
- Naam van uw SQL-poolinstantie
- Tenant-id (directory-id) van de Azure Active Directory
- Abonnements-id 
- Toepassings-id van de service-principal
- Geheime sleutel van de service-principal

Zodra u de voorgaande informatie hebt, implementeert u deze sjabloon:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Zodra u de sjabloon hebt geïmplementeerd, moet u drie nieuwe bronnen vinden: een gratis Azure App Service Plan, een op verbruik gebaseerd Functie-app-plan en een opslagaccount dat de logboekregistratie en de wachtrij voor bewerkingen afhandelt. Lees ook de overige secties om te zien hoe u de geïmplementeerde functies aan uw behoeften kunt aanpassen.

## <a name="change-the-compute-level"></a>Het rekenniveau wijzigen

1. Ga naar de functie-app-service. Als u de sjabloon met de standaardwaarden hebt geïmplementeerd, wordt *DWOperations* de naam van deze service. Als de functie-app is geopend, ziet u dat er vijf functies voor de functie-app-service zijn geïmplementeerd. 

   ![Functies die met sjabloon zijn geïmplementeerd](./media/manage-compute-with-azure-functions/five-functions.png)

2. Selecteer *DWScaleDownTrigger* of *DWScaleUpTrigger*, afhankelijk van of u de tijd voor omhoog of omlaag schalen wilt wijzigen. Selecteer In het vervolgkeuzemenu de optie Integreren.

   ![Integreren als functie selecteren](./media/manage-compute-with-azure-functions/select-integrate.png)

3. De waarde die momenteel moeten worden weergegeven is *%ScaleDownTime%* of *%ScaleUpTime%*. Deze waarden geven aan dat de planning is gebaseerd op waarden die zijn gedefinieerd in de [Toepassingsinstellingen](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). Voor nu u deze waarde negeren en de planning wijzigen in de gewenste tijd op basis van de volgende stappen.

4. Voeg in het gedeelte met de planning de tijd toe die door de CRON-expressie moet worden uitgedrukt. Deze tijd geeft aan hoe vaak u SQL Data Warehouse omhoog wilt schalen. 

   ![Functieplanning wijzigen](./media/manage-compute-with-azure-functions/change-schedule.png)

   De waarde voor `schedule` is een [CRON-expressie](https://en.wikipedia.org/wiki/Cron#CRON_expression) die de volgende zes velden omvat: 
   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Bijvoorbeeld, *"0 30 9 * * 1-5"* zou een trigger elke weekdag om 9:30 uur weerspiegelen. Ga naar Azure Functions [Voorbeelden van de planning](../../azure-functions/functions-bindings-timer.md#example) voor meer informatie.


## <a name="change-the-time-of-the-scale-operation"></a>De tijd van de schaalbewerking wijzigen

1. Ga naar de functie-app-service. Als u de sjabloon met de standaardwaarden hebt geïmplementeerd, wordt *DWOperations* de naam van deze service. Als de functie-app is geopend, ziet u dat er vijf functies voor de functie-app-service zijn geïmplementeerd. 

2. Selecteer *DWScaleDownTrigger* of *DWScaleUpTrigger*, afhankelijk van of u de rekenwaarde voor omhoog of omlaag schalen wilt wijzigen. Na het selecteren van de functies, moet in het deelvenster het bestand *index.js* te zien zijn.

   ![Rekenniveau van functietrigger wijzigen](././media/manage-compute-with-azure-functions/index-js.png)

3. Wijzig de waarde van *ServiceLevelObjective* tot het gewenste niveau en druk op Opslaan. Deze waarde is het rekenniveau waarop uw gegevensmagazijninstantie wordt geschaald op basis van de planning die is gedefinieerd in de sectie Integreren.

## <a name="use-pause-or-resume-instead-of-scale"></a>Onderbreken of Hervatten gebruiken in plaats van Schalen 

De functies die momenteel standaard zijn ingeschakeld zijn *DWScaleDownTrigger* en *DWScaleUpTrigger*. Als u in plaats daarvan de functies voor onderbreken en hervatten wilt gebruiken, kunt u *DWPauseTrigger* of *DWResumeTrigger* inschakelen.

1. Ga naar het deelvenster Functies.

   ![Deelvenster Functies](./media/manage-compute-with-azure-functions/functions-pane.png)



2. Klik op de schuifschakelaar van de trigger(s) die u wilt inschakelen.

3. Ga naar de tabbladen *Integreren* van de bijbehorende triggers om de planning te wijzigen.

   > [!NOTE]
   > Het functionele verschil tussen de schalingstriggers en de pauze-/hervattingstriggers is het bericht dat naar de wachtrij wordt verzonden. Zie [Een nieuwe triggerfunctie toevoegen](manage-compute-with-azure-functions.md#add-a-new-trigger-function)voor meer informatie.


## <a name="add-a-new-trigger-function"></a>Een nieuwe triggerfunctie toevoegen

Er zijn momenteel slechts twee schaalfuncties in de sjabloon opgenomen. Met deze functies u in de loop van een dag slechts één keer omlaag en omhoog. Voor meer gedetailleerde besturingselementen, zoals meerdere keren per dag verkleinen of verschillende schalinggedrag hebben in het weekend, moet u een andere trigger toevoegen.

1. Maak een nieuwe, lege functie. Selecteer *+* de knop in de buurt van de locatie Functies om het functiesjabloonvenster weer te geven.

   ![Nieuwe functie maken](./media/manage-compute-with-azure-functions/create-new-function.png)

2. Selecteer in Taal de optie *JavaScript* en vervolgens *TimerTrigger*.

   ![Nieuwe functie maken](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Geef de functie een naam en stel de planning in. In de afbeelding ziet u hoe u elke zaterdag om middernacht (van vrijdag op zaterdag) een functie kunt laten activeren.

   ![Zaterdag omlaag schalen](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Kopieer de inhoud van *index.js* vanuit een van de andere triggerfuncties.

   ![index.js kopiëren](././media/manage-compute-with-azure-functions/index-js.png)

5. Stel uw bewerkingsvariabele als volgt in op het gewenste gedrag:

   ```javascript
   // Resume the SQL pool instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the SQL pool instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the SQL pool instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


## <a name="complex-scheduling"></a>Complex plannen

In deze sectie wordt kort getoond wat nodig is om complexere plannings-, hervattings- en schaalmogelijkheden te krijgen.

### <a name="example-1"></a>Voorbeeld 1:

Elke dag om 8:00 uur omhoog schalen naar DW600 en om 20:00 uur omlaag schalen naar DW200.

| Functie  | Planning     | Bewerking                                |
| :-------- | :----------- | :--------------------------------------- |
| Functie1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600"}` |
| Functie2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Voorbeeld 2: 

Dagelijks opschalen om 8.00 uur naar DW1000, eenmaal naar DW600 om 16.00 uur en om 22.00 uur naar DW200 schalen.

| Functie  | Planning     | Bewerking                                |
| :-------- | :----------- | :--------------------------------------- |
| Functie1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Functie2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Functie3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Voorbeeld 3: 

Op weekdagen om 8:00 uur omhoog schalen naar DW1000 en om 16:00 uur omlaag schalen naar DW600. Onderbreken op vrijdag om 23:00 uur, hervatten op maandag om 7:00 uur.

| Functie  | Planning       | Bewerking                                |
| :-------- | :------------- | :--------------------------------------- |
| Functie1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Functie2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Functie3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Functie4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure-functies die door een [timertrigger](../../azure-functions/functions-create-scheduled-function.md) worden geactiveerd.

De [SQL-poolsamplesrepository afrekenen.](https://github.com/Microsoft/sql-data-warehouse-samples)

