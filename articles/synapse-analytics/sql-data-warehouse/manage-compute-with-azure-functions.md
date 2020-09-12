---
title: 'Zelf studie: Compute beheren met Azure Functions'
description: Azure functions gebruiken voor het beheren van de reken kracht van uw SQL-groep in azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 11cb0c30a1a6ed70cca82e494fcec73936975f39
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442218"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-synapse-analytics-sql-pool"></a>Azure Functions gebruiken om reken resources te beheren in azure Synapse Analytics SQL-groep

In deze zelf studie wordt gebruikgemaakt van Azure Functions om reken resources te beheren voor een SQL-groep in azure Synapse Analytics.

Als u Azure functie-app wilt gebruiken met SQL-pool, moet u een [Service-Principal-account](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) maken met toegang tot Inzender onder hetzelfde abonnement als uw exemplaar van SQL-groep.

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Schalen op basis van een timer met een Azure Resource Manager sjabloon implementeren

Als u de sjabloon wilt implementeren, hebt u de volgende informatie nodig:

- De naam van de resource groep waaraan uw exemplaar van SQL-groep is in
- De naam van de server waar de SQL-pool instantie zich bevindt
- De naam van het exemplaar van de SQL-groep
- Tenant-id (directory-id) van de Azure Active Directory
- Abonnements-id
- Toepassings-id van de service-principal
- Geheime sleutel van de service-principal

Wanneer u de voor gaande informatie hebt, implementeert u deze sjabloon:

[![Afbeelding met een knop met het label Implementeren naar Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

Nadat u de sjabloon hebt geïmplementeerd, moet u drie nieuwe resources vinden: een gratis Azure App Service plan, een functie-app plan op basis van verbruik en een opslag account waarmee de logboek registratie en de bewerkings wachtrij worden verwerkt. Lees ook de overige secties om te zien hoe u de geïmplementeerde functies aan uw behoeften kunt aanpassen.

## <a name="change-the-compute-level"></a>Het reken niveau wijzigen

1. Ga naar de functie-app-service. Als u de sjabloon met de standaardwaarden hebt geïmplementeerd, wordt *DWOperations* de naam van deze service. Als de functie-app is geopend, ziet u dat er vijf functies voor de functie-app-service zijn geïmplementeerd.

   ![Functies die met sjabloon zijn geïmplementeerd](./media/manage-compute-with-azure-functions/five-functions.png)

2. Selecteer *DWScaleDownTrigger* of *DWScaleUpTrigger*, afhankelijk van of u de tijd voor omhoog of omlaag schalen wilt wijzigen. Selecteer integreren in de vervolg keuzelijst.

   ![Integreren als functie selecteren](./media/manage-compute-with-azure-functions/select-integrate.png)

3. De waarde die momenteel moeten worden weergegeven is *%ScaleDownTime%* of *%ScaleUpTime%*. Deze waarden geven aan dat de planning is gebaseerd op waarden die zijn gedefinieerd in de [Toepassingsinstellingen](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). U kunt deze waarde nu negeren en de planning wijzigen in uw voorkeurs tijd op basis van de volgende stappen.

4. Voeg in het plannings gebied het tijdstip toe waarop de CRON-expressie u wilt weer geven hoe vaak Azure Synapse Analytics moet worden geschaald.

   ![Functieplanning wijzigen](./media/manage-compute-with-azure-functions/change-schedule.png)

   De waarde voor `schedule` is een [CRON-expressie](https://en.wikipedia.org/wiki/Cron#CRON_expression) die de volgende zes velden omvat:

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   *"0 30 9 * * 1-5"* zou bijvoorbeeld elke weekdag op 9: om 9:30 uur weer geven. Ga naar Azure Functions [Voorbeelden van de planning](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example) voor meer informatie.

## <a name="change-the-time-of-the-scale-operation"></a>De tijd van de schaal bewerking wijzigen

1. Ga naar de functie-app-service. Als u de sjabloon met de standaardwaarden hebt geïmplementeerd, wordt *DWOperations* de naam van deze service. Als de functie-app is geopend, ziet u dat er vijf functies voor de functie-app-service zijn geïmplementeerd.

2. Selecteer *DWScaleDownTrigger* of *DWScaleUpTrigger*, afhankelijk van of u de rekenwaarde voor omhoog of omlaag schalen wilt wijzigen. Na het selecteren van de functies, moet in het deelvenster het bestand *index.js* te zien zijn.

   ![Rekenniveau van functietrigger wijzigen](././media/manage-compute-with-azure-functions/index-js.png)

3. Wijzig de waarde van *ServiceLevelObjective* tot het gewenste niveau en druk op Opslaan. Deze waarde is het reken niveau dat door uw data warehouse-instantie wordt geschaald op basis van de planning die is gedefinieerd in de sectie integreren.

## <a name="use-pause-or-resume-instead-of-scale"></a>Onderbreken of Hervatten gebruiken in plaats van Schalen

De functies die momenteel standaard zijn ingeschakeld zijn *DWScaleDownTrigger* en *DWScaleUpTrigger*. Als u in plaats daarvan de functies voor onderbreken en hervatten wilt gebruiken, kunt u *DWPauseTrigger* of *DWResumeTrigger* inschakelen.

1. Ga naar het deelvenster Functies.

   ![Deelvenster Functies](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Klik op de schuifschakelaar van de trigger(s) die u wilt inschakelen.

3. Ga naar de tabbladen *Integreren* van de bijbehorende triggers om de planning te wijzigen.

   > [!NOTE]
   > Het functionele verschil tussen de triggers voor schalen en onderbreken/hervatten is het bericht dat naar de wachtrij wordt verzonden. Zie [een nieuwe trigger functie toevoegen](manage-compute-with-azure-functions.md#add-a-new-trigger-function)voor meer informatie.

## <a name="add-a-new-trigger-function"></a>Een nieuwe triggerfunctie toevoegen

Er zijn momenteel slechts twee schaalfuncties in de sjabloon opgenomen. Met deze functies kunt u in de loop van een dag slechts één keer omhoog en omlaag schalen. Voor een gedetailleerdere controle, zoals het omlaag schalen van meerdere tijden per dag of het afwijkend gedrag van schalen in het weekend, moet u een andere trigger toevoegen.

1. Maak een nieuwe, lege functie. Selecteer de *+* knop in de buurt van de locatie waar u het deel venster functie sjabloon wilt weer geven.

   ![Nieuwe functie maken](./media/manage-compute-with-azure-functions/create-new-function.png)

2. Selecteer vanuit taal *Java script*en selecteer vervolgens *Timer trigger*.

   ![Nieuwe functie maken](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Geef de functie een naam en stel de planning in. In de afbeelding ziet u hoe u elke zaterdag om middernacht (van vrijdag op zaterdag) een functie kunt laten activeren.

   ![Zaterdag omlaag schalen](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Kopieer de inhoud van *index.js* vanuit een van de andere triggerfuncties.

   ![index.js kopiëren](././media/manage-compute-with-azure-functions/index-js.png)

5. Stel de bewerkings variabele als volgt in op het gewenste gedrag:

   ```JavaScript
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

In deze sectie wordt uitgelegd wat u nodig hebt om een complexere planning van onderbrekingen, cv's en schaal mogelijkheden te verkrijgen.

### <a name="example-1"></a>Voorbeeld 1

Elke dag om 8:00 uur omhoog schalen naar DW600 en om 20:00 uur omlaag schalen naar DW200.

| Functie  | Schema     | Bewerking                                |
| :-------- | :----------- | :--------------------------------------- |
| Functie1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600"}` |
| Functie2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Voorbeeld 2

Schaal dagelijks op 8 a.m. naar DW1000, schaal omlaag naar DW600 op 4 p.m. en schaal omlaag naar 10pm naar DW200.

| Functie  | Schema     | Bewerking                                |
| :-------- | :----------- | :--------------------------------------- |
| Functie1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Functie2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Functie3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Voorbeeld 3

Op weekdagen om 8:00 uur omhoog schalen naar DW1000 en om 16:00 uur omlaag schalen naar DW600. Onderbreken op vrijdag om 23:00 uur, hervatten op maandag om 7:00 uur.

| Functie  | Schema       | Bewerking                                |
| :-------- | :------------- | :--------------------------------------- |
| Functie1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Functie2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Functie3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Functie4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure-functies die door een [timertrigger](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) worden geactiveerd.

De [opslag plaats voor beelden](https://github.com/Microsoft/sql-data-warehouse-samples)van SQL-pool wordt uitgecheckt.
