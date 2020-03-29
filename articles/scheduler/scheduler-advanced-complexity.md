---
title: Geavanceerde werkschema's en herhalingen maken
description: Meer informatie over het maken van geavanceerde schema's en herhalingen voor taken in Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: b85932bf0d4fd080afadef2bc28d6a218b2d627a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898591"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Geavanceerde schema's en herhalingen maken voor taken in Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) vervangt Azure Scheduler, dat [wordt uitgeschakeld.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Als u wilt blijven werken met de taken die u in Scheduler hebt ingesteld, migreert u zo snel mogelijk [naar Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Scheduler is niet langer beschikbaar in de Azure-portal, maar de [REST API-](/rest/api/scheduler) en [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taakverzamelingen beheren.

Binnen een [Azure Scheduler-taak](../scheduler/scheduler-intro.md) is de planning de kern die bepaalt wanneer en hoe de Scheduler-service de taak uitvoert. U meerdere eenmalige en terugkerende schema's voor een taak instellen bij Scheduler. Eenmalige schema's worden slechts één keer op een bepaald tijdstip uitgevoerd en zijn in principe terugkerende schema's die slechts één keer worden uitgevoerd. Terugkerende schema's worden uitgevoerd op een bepaalde frequentie. Met deze flexibiliteit u Scheduler gebruiken voor verschillende bedrijfsscenario's, bijvoorbeeld:

* **Gegevens regelmatig opschonen**: Maak een dagelijkse taak die alle tweets verwijdert die ouder zijn dan drie maanden.

* **Archiefgegevens:** maak een maandelijkse taak die de factuurgeschiedenis naar een back-upservice duwt.

* **Externe gegevens aanvragen**: Maak een taak die elke 15 minuten wordt uitgevoerd en haalt een nieuw weerbericht van NOAA.

* **Afbeeldingen verwerken:** maak een werk op weekdag dat tijdens daluren wordt uitgevoerd en cloud computing gebruikt voor het comprimeren van afbeeldingen die gedurende de dag zijn geüpload.

In dit artikel worden voorbeeldtaken beschreven die u maken met Scheduler en de [Azure Scheduler REST API](/rest/api/scheduler)en bevat u de JSON-definitie (JavaScript Object Notation) voor elk schema. 

## <a name="supported-scenarios"></a>Ondersteunde scenario's

In deze voorbeelden wordt het bereik van scenario's weergegeven dat Azure Scheduler ondersteunt en hoe u schema's maakt voor verschillende gedragspatronen, bijvoorbeeld:

* Voer één keer uit op een bepaalde datum en tijd.
* Een bepaald aantal keren uitvoeren en herhalen.
* Ren onmiddellijk en recureren.
* Uitvoeren en terugkeren elke *n* minuten, uren, dagen, weken of maanden, vanaf een bepaald tijdstip.
* Uitvoeren en terugkeren wekelijks of maandelijks, maar alleen op specifieke dagen van de week of op specifieke dagen van de maand.
* Voer en recur meer dan eens voor een bepaalde periode. Bijvoorbeeld elke maand op de laatste vrijdag en maandag, of dagelijks om 5:15 uur en om 17:15 uur.

In dit artikel worden deze scenario's later nader beschreven.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Planning maken met REST API

Voer de volgende stappen uit om een basisplanning te maken met de [Azure Scheduler REST API:](/rest/api/scheduler)

1. Registreer uw Azure-abonnement bij een resourceprovider met behulp van de [registerbewerking - Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/providers). De providernaam voor de Azure Scheduler-service is **Microsoft.Scheduler**. 

1. Maak een taakverzameling met de [bewerking Maken of Bijwerken voor taakverzamelingen](https://docs.microsoft.com/rest/api/scheduler/jobcollections) in de SCHEDULER REST API. 

1. Maak een taak met de [bewerking Maken of Bijwerken voor taken](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Taakschema-elementen

Deze tabel biedt een overzicht op hoog niveau voor de belangrijkste JSON-elementen die u gebruiken bij het instellen van herhalingen en planningen voor taken. 

| Element | Vereist | Beschrijving | 
|---------|----------|-------------|
| **startTime** | Nee | Een datumtekenreekswaarde in [de ISO 8601-notatie](https://en.wikipedia.org/wiki/ISO_8601) die aangeeft wanneer de taak voor het eerst wordt gestart in een basisplanning. <p>Voor complexe schema's begint de taak niet eerder dan **startTime**. | 
| **recurrence** | Nee | De recidief regels voor wanneer de taak wordt uitgevoerd. Het **herhalingsobject** ondersteunt deze elementen: **frequentie**, **interval**, **planning**, **telling**en **eindtijd**. <p>Als u het **herhalingselement** gebruikt, moet u ook het **frequentie-element** gebruiken, terwijl andere **herhalingselementen** optioneel zijn. |
| **frequency** | Ja, wanneer u **herhaling** gebruikt | De tijdseenheid tussen gebeurtenissen en ondersteunt deze waarden: "Minuut", "Uur", "Dag", "Week", "Maand" en "Jaar" | 
| **Interval** | Nee | Een positief geheel getal dat het aantal tijdeenheden tussen gebeurtenissen bepaalt op basis van **de frequentie.** <p>Als **interval** bijvoorbeeld 10 is en **de frequentie** 'Week' is, komt de taak elke 10 weken terug. <p>Hier is het meeste aantal intervallen voor elke frequentie: <p>- 18 maanden <br>- 78 weken <br>- 548 dagen <br>- Voor uren en minuten is het bereik 1 <= <*interval*> <= 1000. | 
| **Schema** | Nee | Hiermee definieert u wijzigingen in de herhaling op basis van de opgegeven minutenmarkeringen, uurmarkeringen, dagen van de week en dagen van de maand | 
| **Tellen** | Nee | Een positief geheel getal dat het aantal keren aangeeft dat de taak wordt uitgevoerd voordat deze wordt uitgevoerd. <p>Wanneer een dagelijkse taak bijvoorbeeld **is** ingesteld op 7 en de begindatum maandag is, eindigt de taak op zondag. Als de begindatum al is verstreken, wordt de eerste run berekend op basis van de aanmaaktijd. <p>Zonder **endTime** of **count**wordt de taak oneindig uitgevoerd. U niet zowel **tellen** als **endTime** gebruiken in dezelfde taak, maar de regel die als eerste eindigt, wordt gehonoreerd. | 
| **endTime** | Nee | Een datum- of datetime-tekenreekswaarde in [de ISO 8601-notatie](https://en.wikipedia.org/wiki/ISO_8601) die aangeeft wanneer de taak niet meer wordt uitgevoerd. U een waarde instellen voor **endTime** die in het verleden is. <p>Zonder **endTime** of **count**wordt de taak oneindig uitgevoerd. U niet zowel **tellen** als **endTime** gebruiken in dezelfde taak, maar de regel die als eerste eindigt, wordt gehonoreerd. |
|||| 

Dit JSON-schema beschrijft bijvoorbeeld een basisschema en herhaling voor een taak: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Datums en Datumtijdwaarden*

* Datums in Scheduler-taken bevatten alleen de datum en volgen de [ISO 8601-specificatie](https://en.wikipedia.org/wiki/ISO_8601).

* Datumtijden in Scheduler-taken omvatten zowel datum als tijd, volgen de [ISO 8601-specificatie](https://en.wikipedia.org/wiki/ISO_8601)en worden verondersteld UTC te zijn wanneer er geen UTC-verschuiving is opgegeven. 

Zie [Concepten, terminologie en entiteiten](../scheduler/scheduler-concepts-terms.md)voor meer informatie.

<a name="start-time"></a>

## <a name="details-starttime"></a>Details: startTime

In deze tabel wordt beschreven hoe **startTime** bepaalt hoe een taak wordt uitgevoerd:

| startTime | Geen herhaling | Herhaling, geen schema | Recurrence met schedule |
|-----------|---------------|-------------------------|--------------------------|
| **Geen begintijd** | Ren één keer onmiddellijk. | Ren één keer onmiddellijk. Voer latere uitvoeringen uit, berekend op basis van de laatste uitvoeringstijd. | Ren één keer onmiddellijk. Voer latere uitvoeringen uit op basis van een herhalingsschema. | 
| **Begintijd in het verleden** | Ren één keer onmiddellijk. | Bereken de eerste toekomstige runtijd na de begintijd en voer op dat moment uit. <p>Voer latere uitvoeringen uit, berekend op basis van de laatste uitvoeringstijd. <p>Zie het voorbeeld na deze tabel. | Start taak *niet eerder dan* de opgegeven begintijd. De eerste uitvoering is gebaseerd op de planning die wordt berekend op basis van de starttijd. <p>Voer latere uitvoeringen uit op basis van een herhalingsschema. | 
| **Begintijd in de toekomst of de huidige tijd** | Voer eenmaal uit op de opgegeven begintijd. | Voer eenmaal uit op de opgegeven begintijd. <p>Voer latere uitvoeringen uit, berekend op basis van de laatste uitvoeringstijd. | Start taak *niet eerder dan* de opgegeven begintijd. De eerste uitvoering is gebaseerd op de planning die wordt berekend op basis van de starttijd. <p>Voer latere uitvoeringen uit op basis van een herhalingsschema. |
||||| 

Stel dat u dit voorbeeld met deze voorwaarden: een begintijd in het verleden met een herhaling, maar geen schema.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* De huidige datum en tijd is 08 april 2015 om 13:00 uur.

* De begindatum en -tijd is 07 april 2015 om 14:00 uur, dat is vóór de huidige datum en tijd.

* De herhaling is om de twee dagen.

1. Onder deze omstandigheden is de eerste executie op 9 april 2015 om 14:00 uur. 

   Scheduler berekent de uitvoeringstaken op basis van de begintijd, verwijdert alle exemplaren in het verleden en gebruikt de volgende instantie in de toekomst. 
   In dit geval is **startTijd** op 07 april 2015 om 14:00 uur, dus de volgende instantie is twee dagen vanaf die tijd, dat is 9 april 2015 om 14:00 uur.

   De eerste uitvoering is hetzelfde of **startTime** 2015-04-05 14:00 of 2015-04-01 14:00 is. Na de eerste uitvoering worden latere uitvoeringen berekend op basis van de planning. 
   
1. De executies volgen dan in deze volgorde: 
   
   1. 2015-04-11 om 14:00
   1. 2015-04-13 om 14:00 
   1. 2015-04-15 om 14:00
   1. En zo verder...

1. Ten slotte, wanneer een taak een planning heeft, maar geen opgegeven uren en minuten, zijn deze waarden standaard voor respectievelijk de uren en minuten in de eerste uitvoering.

<a name="schedule"></a>

## <a name="details-schedule"></a>Details: planning

U **planning** gebruiken om het aantal taakuitvoeringen te *beperken.* Als een taak met een **frequentie** van 'maand' bijvoorbeeld een schema heeft dat alleen op dag 31 wordt uitgevoerd, wordt de taak alleen uitgevoerd in maanden die een 31e dag hebben.

U ook **schema** gebruiken om het aantal taakuitvoeringen uit te *breiden.* Als een taak met een **frequentie** van 'maand' bijvoorbeeld een schema heeft dat wordt uitgevoerd op maanddagen 1 en 2, wordt de taak uitgevoerd op de eerste en tweede dag van de maand in plaats van slechts één keer per maand.

Als u meer dan één planningselement opgeeft, is de volgorde van evaluatie van het grootste tot de kleinste: weeknummer, maanddag, weekdag, uur en minuut.

In de volgende tabel worden de schedule-elementen in detail beschreven:

| JSON-naam | Beschrijving | Geldige waarden |
|:--- |:--- |:--- |
| **minutes** |Minuten van het uur waarop de taak wordt uitgevoerd. |Een array van gehele getallen. |
| **hours** |Uren van de dag waarop de taak wordt uitgevoerd. |Een array van gehele getallen. |
| **weekDays** |Dagen van de week loopt de klus. Kan slechts met een wekelijkse frequentie worden gespecificeerd. |Een array van een van de volgende waarden (maximale matrixgrootte is 7):<br />- "Maandag"<br />- "Dinsdag"<br />- "Woensdag"<br />- "Donderdag"<br />- "Vrijdag"<br />- "Zaterdag"<br />- "Zondag"<br /><br />Niet case-gevoelig. |
| **monthlyOccurrences** |Hiermee bepaalt u op welke dagen van de maand de taak wordt uitgevoerd. Kan slechts met een maandelijkse frequentie worden gespecificeerd. |Een array met **objecten maandelijksVoorkomen:**<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **dag** is de dag van de week de taak loopt. *{Sunday}* is bijvoorbeeld elke zondag van de maand. Vereist.<br /><br />**voorkomen** is het optreden van de dag tijdens de maand. *{Zondag, -1}* is bijvoorbeeld de laatste zondag van de maand. Optioneel. |
| **monthDays** |Dag van de maand loopt de taak. Kan slechts met een maandelijkse frequentie worden gespecificeerd. |Een array met de volgende waarden:<br />- Alle waarden < = -1 en > =-31<br />- Alle waarden > = 1 en < =31|

## <a name="examples-recurrence-schedules"></a>Voorbeelden: Herhalingsschema's

In de volgende voorbeelden worden verschillende herhalingsschema's weergegeven. De voorbeelden richten zich op het object planning en de subelementen.

Deze schema's gaan ervan uit dat **het interval** is ingesteld op 1\. In de voorbeelden worden ook de juiste **frequentiewaarden** voor de waarden in **planning aangenomen.** U bijvoorbeeld geen **frequentie** van 'dag' gebruiken en een **maanddagenwijziging** in **planning hebben.** We beschrijven deze beperkingen eerder in het artikel.

| Voorbeeld | Beschrijving |
|:--- |:--- |
| `{"hours":[5]}` |Run om 5 uur elke dag.<br /><br />Scheduler matcht elke waarde in "uren" met elke waarde in "minuten", een voor een, om een lijst te maken van alle tijden waarop de taak wordt uitgevoerd. |
| `{"minutes":[15], "hours":[5]}` |Wordt elke dag om 5:15 uur uitgevoerd. |
| `{"minutes":[15], "hours":[5,17]}` |Wordt elke dag om 05:15 en 17:15 uur uitgevoerd. |
| `{"minutes":[15,45], "hours":[5,17]}` |Wordt elke dag om 05:15, 5:45, 17:15 en 17:45 uur uitgevoerd. |
| `{"minutes":[0,15,30,45]}` |Wordt elke 15 minuten uitgevoerd. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Wordt elk uur uitgevoerd.<br /><br />Deze klus loopt elk uur. De minuut wordt bepaald door de waarde voor **startTime,** als deze is opgegeven. Als er geen **startTime-waarde** is opgegeven, wordt de minuut bepaald door de aanmaaktijd. Als de begin- of aanmaaktijd (wat van toepassing is) bijvoorbeeld 12:25 uur is, wordt de taak uitgevoerd om 00:25, 01:25, 02:25, ..., 23:25.<br /><br />Het schema is hetzelfde als een taak met een **frequentie** van "uur", een **interval** van 1 en geen **planningswaarde.** Het verschil is dat u deze planning met verschillende **frequentie-** en **intervalwaarden** gebruiken om andere taken te maken. Als **frequentie** bijvoorbeeld 'maand' is, wordt de planning slechts één keer per maand uitgevoerd in plaats van elke dag (als **frequentie** 'dag' is). |
| `{minutes:[0]}` |Wordt elk uur op het hele uur uitgevoerd.<br /><br />Deze taak loopt ook elk uur, maar op het uur (12 uur, 1 am, 2 am, enzovoort). Dit schema is hetzelfde als een taak met een **frequentie** van "uur", een **startTime-waarde** van nul minuten en geen **schema**, als de frequentie "dag" is. Als de **frequentie** echter "week" of "maand" is, wordt het schema respectievelijk slechts één dag per week of één dag per maand uitgevoerd. |
| `{"minutes":[15]}` |Ren elk uur om 15 minuten na het uur.<br /><br />Rijdt elk uur, vanaf 00:15 uur, 01:15 uur, 2:15 uur, enzovoort. Het eindigt om 23:15 uur. |
| `{"hours":[17], "weekDays":["saturday"]}` |Run om 5 uur op zaterdag elke week. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Ren elke week om 17.00 uur op maandag, woensdag en vrijdag. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Wordt elke week op maandag, woensdag en vrijdag om 17:15 en 17:45 uur uitgevoerd. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Run elke week om 5.00 en 17.00 uur op maandag, woensdag en vrijdag. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Run op maandag, woensdag en vrijdag om 05:15, 05:45 uur, 17:15 uur en elke week om 17:45 uur. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Wordt op weekdagen elke 15 minuten uitgevoerd. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Ren elke 15 minuten op weekdagen, tussen 9.00 en 16.45 uur. |
| `{"weekDays":["sunday"]}` |Run op zondag bij starttijd. |
| `{"weekDays":["tuesday", "thursday"]}` |Run op dinsdag en donderdag bij aanvang. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Voer om 6 uur 's 6 uur 's avonds uit op de 28e dag van elke maand (uitgaande van een **frequentie** van "maand"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Run om 6 uur op de laatste dag van de maand.<br /><br />Als u een taak wilt uitvoeren op de laatste dag van een maand, gebruikt u -1 in plaats van dag 28, 29, 30 of 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Run om 6 uur op de eerste en laatste dag van elke maand. |
| `{monthDays":[1,-1]}` |Run op de eerste en laatste dag van elke maand bij starttijd. |
| `{monthDays":[1,14]}` |Run op de eerste en 14e dag van elke maand bij aanvang. |
| `{monthDays":[2]}` |Run op de tweede dag van de maand bij starttijd. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Run op de eerste vrijdag van elke maand om 5 uur. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Run op de eerste vrijdag van elke maand bij starttijd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Run op de derde vrijdag vanaf het einde van de maand, elke maand, bij starttijd. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Wordt op de eerste en laatste vrijdagdag van elke maand om 5:15 uur uitgevoerd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Run op de eerste en laatste vrijdag van elke maand bij starttijd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Run op de vijfde vrijdag van elke maand bij starttijd.<br /><br />Als er geen vijfde vrijdag in een maand is, loopt de klus niet. U overwegen -1 in plaats van 5 te gebruiken voor het optreden als u de taak op de laatste vrijdag van de maand wilt uitvoeren. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Wordt op de laatste vrijdag van de maand elke 15 minuten uitgevoerd. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Wordt elke maand op de derde woensdag om 5:15, 5:45, 17:15 en 17:45 uur uitgevoerd. |

## <a name="next-steps"></a>Volgende stappen

* [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Naslaginformatie over REST API van Azure Scheduler](/rest/api/scheduler)
* [Naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)
* [Azure Scheduler-limieten, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)