---
title: Problemen oplossen met behulp van logboeken met diagnostische gegevens van Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u diagnostische logboeken in Azure Stream Analytics analyseren.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: f318b373f6a6f46ee3a85703c6099c76568580ba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426151"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Azure Stream Analytics oplossen met behulp van logboeken met diagnostische gegevens

Soms wordt een Azure Stream Analytics-taak onverwacht beëindigd. Het is belangrijk om dit type gebeurtenissen te kunnen oplossen. Mislukte taken kunnen worden veroorzaakt door een onverwacht queryresultaat, door storing in de verbinding met apparaten of door een onverwachte serviceonderbreking. Met de diagnostische Logboeken in Stream Analytics kunt u de oorzaak van problemen vaststellen wanneer deze optreden en de herstel tijd te verminderen.

Het is raadzaam om Diagnostische logboeken in te scha kelen voor alle taken, omdat dit een grote uitkomst biedt bij fout opsporing en bewaking.

## <a name="log-types"></a>Typen logboeken

Stream Analytics biedt twee typen logboeken:

* [Activiteiten logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (altijd aan), die inzicht geven in bewerkingen die worden uitgevoerd op taken.

* [Diagnostische logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configureerbaar) waarmee u uitgebreid inzicht kunt krijgen in alles wat er gebeurt met een taak. Diagnostische logboeken worden gestart wanneer de taak wordt gemaakt en eindigen wanneer de taak wordt verwijderd. Deze gebeurtenissen van toepassing wanneer de taak wordt bijgewerkt en terwijl deze wordt uitgevoerd.

> [!NOTE]
> U kunt services zoals Azure Storage, Azure Event Hubs en Azure Monitor Logboeken gebruiken om niet-conforme gegevens te analyseren. U betaalt op basis van het prijsmodel voor deze services.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Fout opsporing met activiteiten logboeken

Activiteiten logboeken zijn standaard ingeschakeld en geven inzichten op hoog niveau in bewerkingen die worden uitgevoerd door uw Stream Analytics-taak. Informatie die aanwezig is in activiteiten logboeken kan de hoofd oorzaak van de problemen die invloed hebben op uw taak vinden. Voer de volgende stappen uit om activiteiten Logboeken in Stream Analytics te gebruiken:

1. Meld u aan bij de Azure Portal en selecteer het **activiteiten logboek** in het **overzicht**.

   ![Stream Analytics activiteiten logboek](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. U kunt een lijst weer geven met bewerkingen die zijn uitgevoerd. Elke bewerking waardoor uw taak is mislukt, heeft een rode info-bel.

3. Klik op een bewerking om de samenvattings weergave ervan weer te geven. Informatie is vaak beperkt. Klik op **JSON**voor meer informatie over de bewerking.

   ![Samen vatting van Stream Analytics activiteiten logboek bewerking](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Schuif omlaag naar de sectie **Eigenschappen** van de JSON. Deze bevat details over de fout die de mislukte bewerking heeft veroorzaakt. In dit voor beeld is de fout veroorzaakt door een runtime-fout van de waarden van de afhankelijke Latitude. Het verschil in de gegevens die door een Stream Analytics taak worden verwerkt, veroorzaakt een gegevens fout. U vindt meer informatie over de verschillende [invoer-en uitvoer gegevens fouten en waarom deze optreden](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![Details van JSON-fout](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. U kunt corrigerende acties uitvoeren op basis van het fout bericht in JSON. In dit voor beeld wordt gecontroleerd om ervoor te zorgen dat de breedte van de Latitude tussen-90 graden ligt en 90 graden aan de query moet worden toegevoegd.

6. Als het fout bericht in de activiteiten logboeken niet nuttig is om de hoofd oorzaak te identificeren, kunt u Diagnostische logboeken inschakelen en Azure Monitor-Logboeken gebruiken.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Diagnostische gegevens verzenden naar Azure Monitor-logboeken

Het wordt aanbevolen om Diagnostische logboeken in te scha kelen en deze naar Azure Monitor logboeken te verzenden. Logboeken met diagnostische gegevens zijn **uit** standaard. Als u wilt inschakelen op Logboeken met diagnostische gegevens, de volgende stappen uit:

1.  Meld u aan bij de Azure Portal en navigeer naar uw Stream Analytics-taak. Onder **bewaking**, selecteer **diagnoselogboeken**. Selecteer vervolgens **Diagnostische gegevens inschakelen**.

    ![Navigatie in de blade naar Logboeken met diagnostische gegevens](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Maak een **naam** in de **Diagnostische instellingen** en schakel het selectie vakje naast **verzenden naar log Analytics**in. Voeg vervolgens een bestaande **werk ruimte voor logboek analyse**toe of maak een nieuwe. Schakel de selectie vakjes in voor **uitvoering** en **ontwerpen** onder **logboek**en **AllMetrics** onder **metrische gegevens**. Klik op **Opslaan**. Het is raadzaam om een Log Analytics-werk ruimte te gebruiken in dezelfde Azure-regio als uw Stream Analytics-taak om extra kosten te voor komen.

    ![Instellingen voor Diagnostische logboeken](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Wanneer uw Stream Analytics-taak wordt gestart, worden Diagnostische logboeken gerouteerd naar uw Log Analytics-werk ruimte. Als u Diagnostische logboeken voor uw taak wilt weer geven, selecteert u **Logboeken** onder de sectie **bewaking** .

   ![Diagnostische logboeken onder bewaking](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics biedt vooraf gedefinieerde query's waarmee u eenvoudig naar de logboeken kunt zoeken waarin u geïnteresseerd bent. De drie categorieën zijn **Algemeen**, **fouten in de invoer gegevens** en **uitvoer gegevens**. Als u bijvoorbeeld een samen vatting van alle fouten van uw taak in de afgelopen 7 dagen wilt zien, kunt u **uitvoeren** van de juiste vooraf gedefinieerde query selecteren. 

   ![Diagnostische logboeken onder bewaking](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![Resultaten van Logboeken](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Categorieën-diagnoselogboek

Azure Stream Analytics worden twee categorieën Diagnostische logboeken vastgelegd:

* **Ontwerpen**: legt logboek gebeurtenissen vast die verband houden met het ontwerpen van taken, zoals het maken van een taak, het toevoegen en verwijderen van invoer en uitvoer, het toevoegen en bijwerken van de query, en het starten of stoppen van de taak.

* **Uitvoering**: legt gebeurtenissen vast die tijdens de uitvoering van de taak optreden.
    * Connectiviteitsfouten
    * Fouten van gegevensverwerking, met inbegrip van:
        * Gebeurtenissen die niet aan de definitie van de query (niet-overeenkomende veldtypen en waarden, ontbrekende velden, enzovoort voldoen)
        * Expressie evaluatie fouten
    * Andere gebeurtenissen en fouten

## <a name="diagnostics-logs-schema"></a>Diagnostics-schema voor logboeken

Alle logboeken worden opgeslagen in de JSON-indeling. Elk item heeft de volgende algemene tekenreeksvelden:

Name | Beschrijving
------- | -------
tijd | De tijdstempel (in UTC) van het logboek.
resourceId | ID van de resource die de bewerking plaatsgevonden, in hoofdletters heeft. Hiertoe behoren de abonnements-ID, de resourcegroep en de taaknaam. Bijvoorbeeld,   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Meld u categorie, ofwel **uitvoering** of **ontwerpen**.
operationName | Naam van de bewerking die wordt vastgelegd. Bijvoorbeeld, **gebeurtenissen verzenden: SQL-uitvoer schrijven fout naar mysqloutput**.
status | Status van de bewerking. Bijvoorbeeld, **mislukt** of **geslaagd**.
level | Logboek-niveau. Bijvoorbeeld, **fout**, **waarschuwing**, of **ter informatie**.
properties | Meld u post-specifieke details, geserialiseerd als een JSON-tekenreeks. Zie de volgende secties in dit artikel voor meer informatie.

### <a name="execution-log-properties-schema"></a>Schema voor uitvoering van logboek-eigenschappen

Logboeken van taakuitvoeringen bevat gegevens over gebeurtenissen die hebben plaatsgevonden tijdens het uitvoeren van Stream Analytics-taak. Het schema van eigenschappen varieert, afhankelijk van het feit of de gebeurtenis een gegevens fout of een algemene gebeurtenis is.

### <a name="data-errors"></a>Gegevensfouten

Elke fout die optreedt terwijl de taak is verwerken van gegevens is in deze categorie van Logboeken. Deze logboeken worden gemaakt tijdens het lezen, gegevens serialisatie, meestal als schrijfbewerkingen. Deze logboeken bevatten geen fouten in de basisnetwerkverbinding. Fouten in de basisnetwerkverbinding worden behandeld als algemene gebeurtenissen. Meer informatie over de oorzaak van verschillende [invoer-en uitvoer gegevens fouten](https://docs.microsoft.com/azure/stream-analytics/data-errors).

Name | Beschrijving
------- | -------
Bron | Naam van de taak invoer of uitvoer waar de fout is opgetreden.
Bericht | Bericht met betrekking tot de fout.
Type | Het type fout. Bijvoorbeeld, **DataConversionError**, **CsvParserError**, of **ServiceBusPropertyColumnMissingError**.
Gegevens | Bevat gegevens die is handig om nauwkeurig de bron van de fout te vinden. Kan worden afgekapt, afhankelijk van de grootte.

Afhankelijk van de **operationName** waarde, fouten met gegevens hebben de volgende schema:

* Er treden **gebeurtenissen** op tijdens het lezen van gebeurtenissen. Ze zich voordoen wanneer de gegevens op de invoer niet voldoet aan de Queryschema voor een van de volgende redenen:

   * *Niet-overeenkomend gegevenstype tijdens gebeurtenis (de) serialiseren*: geeft het veld dat de fout wordt veroorzaakt.

   * *Een gebeurtenis, een ongeldige serialisatie kan niet worden gelezen*: bevat informatie over de locatie in de ingevoerde gegevens waar de fout is opgetreden. Blobnaam van de voor blob-invoer, offset en een voorbeeld van de gegevens bevat.

* **Verzenden van gebeurtenissen** vindt plaats tijdens schrijf bewerkingen. De streaming-gebeurtenis die de fout heeft veroorzaakt, worden geïdentificeerd.

### <a name="generic-events"></a>Algemene gebeurtenissen

Algemene gebeurtenissen dekken alles anders.

Name | Beschrijving
-------- | --------
Fout | (optioneel) Foutgegevens. Normaal gesp roken is dit uitzonderings gegevens als deze beschikbaar zijn.
Bericht| Logboekbericht.
Type | Het type van het bericht. Toegewezen aan interne classificatie van fouten. Bijvoorbeeld, **JobValidationError** of **BlobOutputAdapterInitializationFailure**.
Correlatie-id | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) die uniek voor het uitvoeren van taak. Alle vermeldingen in het uitvoeringslogboek vanaf het moment dat de taak wordt gestart totdat de taak stopt dezelfde hebben **correlatie-ID** waarde.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Stream Analytics query language-referentie](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics gegevens fouten](https://docs.microsoft.com/azure/stream-analytics/data-errors)
