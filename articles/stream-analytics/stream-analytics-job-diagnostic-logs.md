---
title: Azure Stream Analytics oplossen met diagnostische logboeken
description: In dit artikel wordt beschreven hoe u diagnostische logboeken analyseren in Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: cdb6629441becd0a8356debe3360830ff11a7a9d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398424"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Azure Stream Analytics oplossen met diagnostische logboeken

Soms wordt een Azure Stream Analytics-taak onverwacht beëindigd. Het is belangrijk om dit type gebeurtenissen te kunnen oplossen. Mislukte taken kunnen worden veroorzaakt door een onverwacht queryresultaat, door storing in de verbinding met apparaten of door een onverwachte serviceonderbreking. De diagnostische logboeken in Stream Analytics kunnen u helpen de oorzaak van problemen te identificeren wanneer deze zich voordoen en de hersteltijd te verkorten.

Het wordt ten zeerste aanbevolen om diagnostische logboeken voor alle taken in te schakelen, omdat dit enorm zal helpen bij het debuggen en controleren.

## <a name="log-types"></a>Logboektypen

Stream Analytics biedt twee soorten logboeken:

* [Activiteitslogboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (altijd aan), die inzicht geven in bewerkingen die worden uitgevoerd op taken.

* [Diagnostische logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configureerbaar), die rijkere inzichten bieden in alles wat er met een taak gebeurt. Diagnostische logboeken beginnen wanneer de taak wordt gemaakt en eindigen wanneer de taak wordt verwijderd. Ze hebben betrekking op gebeurtenissen wanneer de taak wordt bijgewerkt en terwijl deze wordt uitgevoerd.

> [!NOTE]
> U services zoals Azure Storage, Azure Event Hubs en Azure Monitor-logboeken gebruiken om niet-conforme gegevens te analyseren. Er worden kosten in rekening gebracht op basis van het prijsmodel voor deze services.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Foutopsporing met behulp van activiteitslogboeken

Activiteitslogboeken zijn standaard ingeschakeld en geven inzicht op hoog niveau in bewerkingen die worden uitgevoerd door uw Stream Analytics-taak. Informatie die aanwezig is in activiteitenlogboeken kan helpen bij het vinden van de hoofdoorzaak van de problemen die van invloed zijn op uw taak. Volg de volgende stappen om activiteitslogboeken te gebruiken in Stream Analytics:

1. Meld u aan bij de Azure-portal en selecteer **Activiteitenlogboek** onder **Overzicht**.

   ![Activiteitslogboek van Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. U een lijst met bewerkingen zien die zijn uitgevoerd. Elke bewerking waardoor uw taak is mislukt, heeft een rode infoballon.

3. Klik op een bewerking om de overzichtsweergave weer te geven. Informatie hier is vaak beperkt. Klik op **JSON**voor meer informatie over de bewerking.

   ![Overzicht van de bewerkingslogboekvan Stream Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Schuif omlaag naar de sectie **Eigenschappen** van de JSON, die details geeft over de fout die de mislukte bewerking heeft veroorzaakt. In dit voorbeeld was de fout te wijten aan een runtime-fout van niet-gebonden breedtegraden. Een discrepantie in de gegevens die worden verwerkt door een taak Stream Analytics veroorzaakt een gegevensfout. U meer te weten komen over verschillende [fouten in invoer- en uitvoergegevens en waarom deze zich voordoen.](https://docs.microsoft.com/azure/stream-analytics/data-errors)

   ![JSON-foutgegevens](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. U corrigerende maatregelen nemen op basis van het foutbericht in JSON. In dit voorbeeld moeten controles worden uitgevoerd om ervoor te zorgen dat de breedtegraadwaarde tussen -90 graden en 90 graden ligt, aan de query moet worden toegevoegd.

6. Als het foutbericht in de activiteitslogboeken niet handig is bij het identificeren van de hoofdoorzaak, schakelt u diagnostische logboeken in en gebruikt u Azure Monitor-logboeken.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Diagnostische gegevens verzenden naar Azure Monitor-logboeken

Het is ten zeerste aan te raden diagnostische logboeken in te schakelen en deze naar Azure Monitor-logboeken te verzenden. Diagnostische logboeken zijn standaard **uitgeschakeld.** Voer de volgende stappen uit om diagnostische logboeken in te schakelen:

1.  Meld u aan bij de Azure-portal en navigeer naar uw Stream Analytics-taak. Selecteer **onder Controle**de optie Diagnostische **logboeken**. Selecteer vervolgens **Diagnostische gegevens inschakelen**.

    ![Bladnavigatie naar diagnostische logboeken](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Maak een **naam** in **diagnostische instellingen** en schakel het selectievakje naast Verzenden **naar Logboekanalyse in.** Voeg vervolgens een bestaande of maak een nieuwe **werkruimte logboekanalyse**. Schakel de selectievakjes voor **uitvoering** en **auteurworden** in onder **LOG**en **Allmetrics** in onder **METRISCHE .** Klik op **Opslaan**. Het wordt aanbevolen om een Log Analytics-werkruimte te gebruiken in dezelfde Azure-regio als uw Stream Analytics-taak om extra kosten te voorkomen.

    ![Instellingen voor diagnostische logboeken](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Wanneer uw Stream Analytics-taak wordt gestart, worden diagnostische logboeken doorgestuurd naar uw Log Analytics-werkruimte. Als u diagnostische logboeken voor uw taak wilt weergeven, selecteert u **Logboeken** onder de sectie **Controle.**

   ![Diagnostische logboeken onder monitoring](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics biedt vooraf gedefinieerde query's waarmee u eenvoudig zoeken naar de logboeken waarin u geïnteresseerd bent. De 3 categorieën zijn **Algemene,** **Fouten van de Inputgegevens** en **Fouten van de Outputgegevens**. Als u bijvoorbeeld een overzicht wilt zien van alle fouten van uw taak in de afgelopen zeven dagen, u **Uitvoeren** van de juiste vooraf gedefinieerde query selecteren. 

   ![Diagnostische logboeken onder monitoring](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![Resultaten van logboeken](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Logcategorieën voor diagnostische gegevens

Azure Stream Analytics legt twee categorieën diagnostische logboeken vast:

* **Ontwerpen:** Legt logboekgebeurtenissen vast die verband houden met taakontwerpbewerkingen, zoals het maken van taken, het toevoegen en verwijderen van invoer en uitvoer, het toevoegen en bijwerken van de query en het starten of stoppen van de taak.

* **Uitvoering:** Legt gebeurtenissen vast die optreden tijdens het uitvoeren van taken.
    * Connectiviteitsfouten
    * Fouten in de gegevensverwerking, waaronder:
        * Gebeurtenissen die niet voldoen aan de querydefinitie (niet-overeenkomende veldtypen en -waarden, ontbrekende velden, enzovoort)
        * Evaluatiefouten voor expressies
    * Andere gebeurtenissen en fouten

## <a name="diagnostics-logs-schema"></a>Schema voor diagnostische logboeken

Alle logboeken worden opgeslagen in JSON-indeling. Elk item heeft de volgende algemene tekenreeksvelden:

Name | Beschrijving
------- | -------
tijd | Tijdstempel (in UTC) van het logboek.
resourceId | ID van de bron waarop de bewerking heeft plaatsgevonden, in hoofdletters. Het bevat de abonnements-ID, de resourcegroep en de taaknaam. Bijvoorbeeld **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Logboekcategorie, **uitvoering** of **auteur .**
operationName | Naam van de bewerking die is geregistreerd. Bijvoorbeeld **gebeurtenissen verzenden: SQL Output schrijven niet naar mysqloutput**.
status | Status van de bewerking. Bijvoorbeeld **Mislukt** of **geslaagd**.
niveau | Logniveau. Bijvoorbeeld **Fout,** **waarschuwing**of **informatief**.
properties | Log entry-specifieke details, geserialiseerd als een JSON-tekenreeks. Zie de volgende secties in dit artikel voor meer informatie.

### <a name="execution-log-properties-schema"></a>Uitvoeringslogboekeigenschappenschema

Uitvoeringslogboeken bevatten informatie over gebeurtenissen die zijn gebeurd tijdens de uitvoering van de taak van Stream Analytics. Het schema van eigenschappen is afhankelijk van of de gebeurtenis een gegevensfout of een algemene gebeurtenis is.

### <a name="data-errors"></a>Gegevensfouten

Elke fout die optreedt terwijl de taak gegevens verwerkt, bevindt zich in deze categorie logboeken. Deze logboeken worden meestal gemaakt tijdens gegevenslezen, serialisatie en schrijfbewerkingen. Deze logboeken bevatten geen verbindingsfouten. Verbindingsfouten worden behandeld als algemene gebeurtenissen. U meer informatie krijgen over de oorzaak van verschillende [invoer- en uitvoergegevensfouten.](https://docs.microsoft.com/azure/stream-analytics/data-errors)

Name | Beschrijving
------- | -------
Bron | Naam van de taakinvoer of -uitvoer waar de fout is opgetreden.
Bericht | Bericht dat aan de fout is gekoppeld.
Type | Type fout. **Bijvoorbeeld DataConversionError,** **CsvParserError**of **ServiceBusPropertyColumnMissingError**.
Gegevens | Bevat gegevens die nuttig zijn om de bron van de fout nauwkeurig te lokaliseren. Onder voorbehoud van afgekaptheid, afhankelijk van de grootte.

Afhankelijk van de **waarde van de operationName** hebben gegevensfouten het volgende schema:

* **Serialiseren gebeurtenissen** optreden tijdens gebeurtenis lezen operaties. Ze treden op wanneer de gegevens bij de invoer om een van deze redenen niet voldoen aan het queryschema:

   * *Type mismatch tijdens gebeurtenis (de)serialiseren:* hiermee wordt het veld geïdentificeerd dat de fout veroorzaakt.

   * *Kan een gebeurtenis, ongeldige serialisatie:* geeft geen informatie weer over de locatie in de invoergegevens waar de fout is opgetreden. Bevat blobnaam voor blobinvoer, verschuiving en een voorbeeld van de gegevens.

* **Verzendgebeurtenissen** vinden plaats tijdens schrijfbewerkingen. Ze identificeren de streaminggebeurtenis die de fout heeft veroorzaakt.

### <a name="generic-events"></a>Algemene gebeurtenissen

Generieke evenementen hebben betrekking op al het andere.

Name | Beschrijving
-------- | --------
Fout | (facultatief) Foutgegevens. Meestal is dit uitzonderingsinformatie als deze beschikbaar is.
Bericht| Logboekbericht.
Type | Type bericht. Kaarten voor interne categorisering van fouten. **Bijvoorbeeld JobValidationError** of **BlobOutputAdapterInitializationFailure**.
Correlatie-id | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) die de taakuitvoering op een unieke wijze identificeert. Alle uitvoeringslogboekvermeldingen vanaf het moment dat de taak wordt gestart tot de taak wordt gestopt, hebben dezelfde **correlatie-id-waarde.**

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaginformatie over de queryvan Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Gegevensfouten van Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-errors)
