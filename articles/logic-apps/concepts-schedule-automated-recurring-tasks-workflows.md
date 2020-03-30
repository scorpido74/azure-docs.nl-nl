---
title: Terugkerende taken en werkstromen plannen in Azure Logic Apps
description: Een overzicht over het plannen van terugkerende geautomatiseerde taken, processen en werkstromen met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0f6ec158cf6ab855191e6796be3abec7d37439a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270561"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Terugkerende en geautomatiseerde taken, processen en werkstromen plannen en uitvoeren met Azure Logic Apps

Logic Apps helpt u bij het maken en uitvoeren van geautomatiseerde terugkerende taken en processen volgens een planning. Door een logische app-werkstroom te maken die begint met een ingebouwde recidieftrigger of schuifvenstertrigger, die triggers van het type Planning zijn, u taken onmiddellijk, op een later tijdstip of op een terugkerend interval uitvoeren. U services binnen en buiten Azure bellen, zoals HTTP- of HTTPS-eindpunten, berichten plaatsen naar Azure-services zoals Azure Storage en Azure Service Bus of bestanden laten uploaden naar een bestandsshare. Met de recidief-trigger u ook complexe schema's en geavanceerde herhalingen instellen voor het uitvoeren van taken. Zie [Triggers plannen](#schedule-triggers) en [Acties plannen](#schedule-actions)voor meer informatie over de ingebouwde triggers en acties voor het plannen. 

> [!TIP]
> U terugkerende workloads plannen en uitvoeren zonder een aparte logische app te maken voor elke geplande taak en de [limiet voor werkstromen per regio en abonnement](../logic-apps/logic-apps-limits-and-config.md#definition-limits)in te lopen. In plaats daarvan u het logische app-patroon gebruiken dat is gemaakt door de [Azure QuickStart-sjabloon: Logische apps-taakplanner](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> Met de sjabloon Logische Apps maakt de taakplanner-sjabloon Een CreateTimerJob-logica-app die een timerjoblogische-logica-app aanroept. U vervolgens de CreateTimerJob-logica-app als API aanroepen door een HTTP-aanvraag in te dienen en een planning door te geven als invoer voor de aanvraag. Elke aanroep naar de createtimerjob-logica-app roept ook de timerjoblogische-logica-app aan, die een nieuwe TimerJob-instantie maakt die continu wordt uitgevoerd op basis van het opgegeven schema of tot het voldoen aan een opgegeven limiet. Op die manier u zoveel TimerJob-exemplaren uitvoeren als u wilt zonder u zorgen te maken over werkstroomlimieten, omdat instanties geen definities of bronnen van afzonderlijke logische app-werkstroom zijn.

In deze lijst worden enkele voorbeeldtaken weergegeven die u uitvoeren met de ingebouwde triggers plannen:

* Ontvang interne gegevens, zoals elke dag een SQL-opgeslagen procedure uitvoeren.

* Ontvang externe gegevens, zoals elke 15 minuten weerberichten ophalen bij NOAA.

* Stuur rapportgegevens, zoals een overzicht e-mail voor alle bestellingen die groter zijn dan een specifiek bedrag in de afgelopen week.

* Procesgegevens, zoals het comprimeren van de geüploade afbeeldingen van vandaag elke weekdag tijdens daluren.

* Gegevens opschonen, zoals alle tweets verwijderen die ouder zijn dan drie maanden.

* Archiveer gegevens, zoals het de komende negen maanden elke dag om 01:00 uur naar een back-upservice pushen.

U de ingebouwde acties plannen ook gebruiken om uw werkstroom te pauzeren voordat de volgende actie wordt uitgevoerd, bijvoorbeeld:

* Wacht tot een doordeweekse dag om een statusupdate via e-mail te verzenden.

* Stel de werkstroom uit totdat een HTTP-gesprek tijd heeft om te voltooien voordat u het resultaat hervat en ophaalt.

In dit artikel worden de mogelijkheden beschreven voor de ingebouwde triggers en acties plannen.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Triggers plannen

U de werkstroom van uw logische app starten met de trigger voor herhaling of schuifvenster, die niet is gekoppeld aan een specifieke service of systeem, bijvoorbeeld Office 365 Outlook of SQL Server. Deze triggers starten en voeren uw werkstroom uit op basis van de opgegeven herhaling waarbij u het interval en de frequentie selecteert, zoals het aantal seconden, minuten en uren voor beide triggers of het aantal dagen, weken of maanden voor de recidieftrigger. U ook de begindatum en -tijd en de tijdzone instellen. Elke keer dat een trigger wordt geactiveerd, maakt en voert Logic Apps een nieuwe werkstroominstantie voor uw logische app uit.

Hier zijn de verschillen tussen deze triggers:

* **Herhaling:** Voert uw werkstroom op regelmatige tijdstippen uit op basis van uw opgegeven schema. Als herhalingen worden gemist, verwerkt de recidief-trigger de gemiste herhalingen niet, maar worden herhalingen opnieuw gestart met het volgende geplande interval. U een begindatum en -tijd en de tijdzone opgeven. Als u 'Dag' selecteert, u bijvoorbeeld elke dag om 14.30 uur uren van de dag en minuten van het uur opgeven. Als u 'Week' selecteert, u ook dagen van de week selecteren, zoals woensdag en zaterdag. Zie [Terugkerende taken en werkstromen maken, plannen en uitvoeren met de recidieftrigger](../connectors/connectors-native-recurrence.md)voor meer informatie.

* **Schuifvenster:** voert uw werkstroom op regelmatige tijdstippen uit die gegevens in continue segmenten verwerken. Als herhalingen worden gemist, gaat de trigger schuifvenster terug en verwerkt de gemiste herhalingen. U een begindatum en -tijd, tijdzone en een duur opgeven om elke herhaling in uw werkstroom uit te stellen. Deze trigger heeft geen opties om dagen, weken en maanden, uren van de dag, minuten van het uur en dagen van de week op te geven. Zie [Terugkerende taken en werkstromen maken, plannen en uitvoeren met de trigger Schuifvenster](../connectors/connectors-native-sliding-window.md)voor meer informatie.

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Acties plannen

Na enige actie in uw logica-app-werkstroom u de acties Vertragen en vertragen tot uitvoeren om uw werkstroom te laten wachten voordat de volgende actie wordt uitgevoerd.

* **Vertraging:** wacht op de volgende actie voor het opgegeven aantal tijdeenheden, zoals seconden, minuten, uren, dagen, weken of maanden. Zie [De volgende actie in werkstromen uitstellen](../connectors/connectors-native-delay.md)voor meer informatie .

* **Vertraging tot**: Wacht met het uitvoeren van de volgende actie tot de opgegeven datum en tijd. Zie [De volgende actie in werkstromen uitstellen](../connectors/connectors-native-delay.md)voor meer informatie .

## <a name="patterns-for-start-date-and-time"></a>Patronen voor begindatum en -tijd

<a name="start-time"></a>

Hier volgen enkele patronen die laten zien hoe u herhaling beheren met de begindatum en -tijd en hoe de Logic Apps-service deze herhalingen uitvoert:

| Begintijd | Recurrence zonder schedule | Herhaling met schema (alleen herhalingstrigger) |
|------------|-----------------------------|----------------------------------------------------|
| {none} | Draait de eerste werkbelasting direct uit. <p>Met toekomstige workloads wordt toekomstige workloads uitgevoerd op basis van de laatste runtime. | Draait de eerste werkbelasting direct uit. <p>Voert toekomstige workloads uit op basis van het opgegeven schema. |
| Begintijd in het verleden | **Recidieftrigger:** berekent de looptijden op basis van de opgegeven begintijd en verwijdert eerdere looptijden. Met de eerste werkbelasting wordt de volgende volgende runtime uitgevoerd. <p>Voert toekomstige workloads uit op basis van berekeningen van de laatste runtime. <p><p>**Schuifvenstertrigger:** berekent de looptijden op basis van de opgegeven starttijd en eer aan eerdere looptijden. <p>Voert toekomstige workloads uit op basis van berekeningen vanaf de opgegeven begintijd. <p><p>Zie het voorbeeld na deze tabel voor meer uitleg. | Voert de eerste werkbelasting *niet eerder* uit dan de begintijd, op basis van het schema dat is berekend vanaf de begintijd. <p>Voert toekomstige workloads uit op basis van het opgegeven schema. <p>**Let op:** Als u een herhaling opgeeft met een schema, maar geen uren of minuten opgeeft voor het schema, worden toekomstige looptijden berekend met respectievelijk de uren of minuten vanaf de eerste runtime. |
| Begintijd op dit moment of in de toekomst | Met de eerste werkbelasting op de opgegeven begintijd wordt de eerste werkbelasting uitgevoerd. <p>Voert toekomstige workloads uit op basis van berekeningen van de laatste runtime. | Voert de eerste werkbelasting *niet eerder* uit dan de begintijd, op basis van het schema dat is berekend vanaf de begintijd. <p>Voert toekomstige workloads uit op basis van het opgegeven schema. <p>**Let op:** Als u een herhaling opgeeft met een schema, maar geen uren of minuten opgeeft voor het schema, worden toekomstige looptijden berekend met respectievelijk de uren of minuten vanaf de eerste runtime. |
||||

> [!IMPORTANT]
> Wanneer herhalingen geen geavanceerde planningsopties opgeven, zijn toekomstige herhalingen gebaseerd op de laatste uitvoeringstijd.
> De begintijden voor deze herhalingen kunnen afdwalen als gevolg van factoren zoals latentie tijdens opslagoproepen. Gebruik een van de volgende opties om ervoor te zorgen dat uw logica-app geen herhaling mist, vooral wanneer de frequentie binnen dagen of langer is:
> 
> * Geef een begintijd op voor de herhaling.
> 
> * Geef de uren en minuten op voor wanneer u de herhaling moet uitvoeren met de eigenschappen **Op deze uren** en Bij deze **minuten.**
> 
> * Gebruik de [trigger schuifvenster](../connectors/connectors-native-sliding-window.md)in plaats van de recidieftrigger.

*Voorbeeld voor eerdere begintijd en herhaling, maar geen schema*

Stel dat de huidige datum en tijd 8 september 2017 om 13:00 uur is. U geeft de begindatum en -tijd op als 7 september 2017 om 14:00 uur, wat in het verleden is, en een herhaling die elke twee dagen wordt uitgevoerd.

| Begintijd | Huidige tijd | Terugkeerpatroon | Planning |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09-**07** om 14:00 uur) | 2017-09-**08**T13:00:00Z <br>(2017-09-**08** om 13:00 uur) | Elke twee dagen | {none} |
|||||

Voor de recidieftrigger berekent de Logic Apps-engine de looptijden op basis van de begintijd, verwijdert u eerdere runtijden, gebruikt de volgende toekomstige begintijd voor de eerste run en berekent u toekomstige uitvoeringen op basis van de laatste runtime.

Zo ziet deze herhaling eruit:

| Begintijd | Eerste looptijd | Toekomstige looptijden |
|------------|----------------|------------------|
| 2017-09-**07** om 14:00 | 2017-09-**09** om 14:00 | 2017-09-**11** om 14:00 </br>2017-09-**13** om 14:00 </br>2017-09-**15** om 14:00 </br>en ga zo maar door ... |
||||

Dus hoe ver in het verleden u de begintijd ook opgeeft, bijvoorbeeld 2017-09-**05** om 14:00 uur of 2017-09-**01** om 14:00 uur, uw eerste run gebruikt altijd de volgende toekomstige begintijd.

Voor de trigger schuifvenster berekent de Logic Apps-engine de looptijden op basis van de begintijd, eert de afgelopen looptijden, gebruikt de begintijd voor de eerste run en berekent de toekomstige uitvoeringen op basis van de begintijd.

Zo ziet deze herhaling eruit:

| Begintijd | Eerste looptijd | Toekomstige looptijden |
|------------|----------------|------------------|
| 2017-09-**07** om 14:00 | 2017-09-**07** om 14:00 | 2017-09-**09** om 14:00 </br>2017-09-**11** om 14:00 </br>2017-09-**13** om 14:00 </br>2017-09-**15** om 14:00 </br>en ga zo maar door ... |
||||

Dus hoe ver u in het verleden ook de begintijd hebt opgegeven, bijvoorbeeld 2017-09-**05** om 14:00 of 2017-09-**01** om 14:00 uur, uw eerste run gebruikt altijd de opgegeven begintijd.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Voorbeeld herhalingen

Hier volgen verschillende voorbeeldherhalingen die u instellen voor de triggers die de opties ondersteunen:

| Trigger | Terugkeerpatroon | Interval | Frequency | Begintijd | Deze dagen | Deze uren | Deze minuten | Opmerking |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Herhaling <br>Sliding window | Elke 15 minuten uitvoeren (geen begindatum en -tijd) | 15 | Minuut | {none} | {niet beschikbaar} | {none} | {none} | Dit schema begint onmiddellijk en berekent vervolgens toekomstige herhalingen op basis van de laatste runtime. |
| Herhaling <br>Sliding window | Elke 15 minuten uitvoeren (met begindatum en -tijd) | 15 | Minuut | *startDatum* T*startTime*Z | {niet beschikbaar} | {none} | {none} | Dit schema start niet *eerder* dan de opgegeven begindatum en -tijd en berekent toekomstige herhalingen op basis van de laatste runtime. |
| Herhaling <br>Sliding window | Elk uur uitvoeren, op het uur (met begindatum en -tijd) | 1 | Uur | *startDatum* Thh:00:00Z | {niet beschikbaar} | {none} | {none} | Dit schema start niet *eerder* dan de opgegeven begindatum en -tijd. Toekomstige herhalingen worden elk uur uitgevoerd op de "00"-minutenmarkering, die wordt berekend vanaf de begintijd. <p>Als de frequentie "Week" of "Maand" is, wordt dit schema respectievelijk slechts één dag per week of één dag per maand uitgevoerd. |
| Herhaling <br>Sliding window | Run elk uur, elke dag (geen startdatum en tijd) | 1 | Uur | {none} | {niet beschikbaar} | {none} | {none} | Dit schema begint onmiddellijk en berekent toekomstige herhalingen op basis van de laatste run time. <p>Als de frequentie "Week" of "Maand" is, wordt dit schema respectievelijk slechts één dag per week of één dag per maand uitgevoerd. |
| Herhaling <br>Sliding window | Run elk uur, elke dag (met startdatum en tijd) | 1 | Uur | *startDatum* T*startTime*Z | {niet beschikbaar} | {none} | {none} | Dit schema start niet *eerder* dan de opgegeven begindatum en -tijd en berekent toekomstige herhalingen op basis van de laatste runtime. <p>Als de frequentie "Week" of "Maand" is, wordt dit schema respectievelijk slechts één dag per week of één dag per maand uitgevoerd. |
| Herhaling <br>Sliding window | Run elke 15 minuten na het uur, elk uur (met startdatum en tijd) | 1 | Uur | *startDatum* T00:15:00Z | {niet beschikbaar} | {none} | {none} | Dit schema start niet *eerder* dan de opgegeven begindatum en -tijd. Toekomstige herhalingen worden uitgevoerd op de "15" minuten markering, die wordt berekend vanaf de begintijd, dus om 00:15 uur, 1:15 am, 2:15 am, enzovoort. |
| Terugkeerpatroon | Run elke 15 minuten na het uur, elk uur (geen startdatum en tijd) | 1 | Dag | {none} | {niet beschikbaar} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Dit schema loopt om 00:15 uur, 1:15 uur, 02:15 uur, enzovoort. Ook is dit schema gelijk aan een frequentie van "Uur" en een begintijd met "15" minuten. |
| Terugkeerpatroon | Voer elke 15 minuten uit op de opgegeven minutenmarkeringen (geen begindatum en -tijd). | 1 | Dag | {none} | {niet beschikbaar} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Dit schema begint pas bij de volgende opgegeven markering van 15 minuten. |
| Terugkeerpatroon | Dagelijks om 8 uur ' *s 8* uur ' s avonds en minuten maken vanaf het moment dat u uw logica-app opslaat | 1 | Dag | {none} | {niet beschikbaar} | 8 | {none} | Zonder begindatum en -tijd wordt dit schema uitgevoerd op basis van het tijdstip waarop u de logische app (PUT-bewerking) opslaat. |
| Terugkeerpatroon | Dagelijks om 8:00 uur (met startdatum en tijd) | 1 | Dag | *startDatum* T08:00:00Z | {niet beschikbaar} | {none} | {none} | Dit schema start niet *eerder* dan de opgegeven begindatum en -tijd. Toekomstige gebeurtenissen lopen dagelijks om 8:00 uur. | 
| Terugkeerpatroon | Dagelijks om 8:30 uur (geen startdatum en tijd) | 1 | Dag | {none} | {niet beschikbaar} | 8 | 30 | Dit schema rijdt elke dag om 8:30 uur. |
| Terugkeerpatroon | Dagelijks om 8:30 en 16:30 uur | 1 | Dag | {none} | {niet beschikbaar} | 8, 16 | 30 | |
| Terugkeerpatroon | Dagelijks om 8:30, 8:45 uur, 16:30 en 16:45 uur | 1 | Dag | {none} | {niet beschikbaar} | 8, 16 | 30, 45 | |
| Terugkeerpatroon | Run elke zaterdag om 17.00 uur (geen startdatum en tijd) | 1 | Wekelijks | {none} | "Zaterdag" | 17 | 00 | Dit schema loopt elke zaterdag om 17:00 uur. |
| Terugkeerpatroon | Run elke zaterdag om 17.00 uur (met startdatum en tijd) | 1 | Wekelijks | *startDatum* T17:00:00Z | "Zaterdag" | {none} | {none} | Dit schema begint niet *eerder* dan de opgegeven begindatum en -tijd, in dit geval 9 september 2017 om 17:00 uur. Toekomstige herhalingen lopen elke zaterdag om 17:00. |
| Terugkeerpatroon | Elke dinsdag, donderdag om 17.00 uur *en* de minutenmarkering van wanneer u uw logica-app opslaat| 1 | Wekelijks | {none} | "Dinsdag", "donderdag" | 17 | {none} | |
| Terugkeerpatroon | Elk uur draaien tijdens de werkuren | 1 | Wekelijks | {none} | Selecteer alle dagen behalve zaterdag en zondag. | Selecteer de uren van de gewenste dag. | Selecteer alle minuten van het gewenste uur. | Als uw werkuren bijvoorbeeld van 8:00 tot 17:00 uur zijn, selecteert u '8, 9, 10, 11, 12, 13, 14, 15, 16, 17' als de uren van de dag. <p>Als uw werkuren van 08:30 tot 17:30 uur zijn, selecteert u de vorige uren van de dag plus "30" als minuten van het uur. |
| Terugkeerpatroon | Eén keer per dag in het weekend | 1 | Wekelijks | {none} | "Zaterdag", "Zondag" | Selecteer de uren van de gewenste dag. | Selecteer alle minuten van het uur naar gelang van het geval. | Dit schema loopt elke zaterdag en zondag volgens het opgegeven schema. |
| Terugkeerpatroon | Run elke 15 minuten tweewekelijks op alleen maandag | 2 | Wekelijks | {none} | "Maandag" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Dit schema loopt om de andere maandag op elke 15 minuten merk. |
| Terugkeerpatroon | Elke maand hardlopen | 1 | Month | *startDatum* T*startTime*Z | {niet beschikbaar} | {niet beschikbaar} | {niet beschikbaar} | Deze planning start niet *eerder* dan de opgegeven begindatum en -tijd en berekent toekomstige herhalingen op de begindatum en -tijd. Als u geen begindatum en -tijd opgeeft, gebruikt deze planning de aanmaakdatum en -tijd. |
| Terugkeerpatroon | Voer elk uur uit gedurende één dag per maand | 1 | Month | {zie noot} | {niet beschikbaar} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {zie noot} | Als u geen begindatum en -tijd opgeeft, gebruikt deze planning de aanmaakdatum en -tijd. Als u de minuten voor het herhalingsschema wilt beheren, geeft u de minuten van het uur, een begintijd of de aanmaaktijd in. Als de begin- of aanmaaktijd bijvoorbeeld 8:25 uur is, wordt dit schema uitgevoerd om 8:25 uur, 9:25 uur, 10:25 uur, enzovoort. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Slechts één keer uitvoeren

Als u uw logische app in de toekomst slechts in één keer wilt uitvoeren, u de **sjabloon Scheduler: Run once jobs** gebruiken. Nadat u een nieuwe logische app hebt gemaakt, maar voordat u de Logic Apps Designer opent, selecteert u in de sectie **Sjablonen** in de lijst **Categorie** de optie **Planning**en selecteert u deze sjabloon:

![De sjabloon 'Scheduler: Run once jobs' selecteren](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Of als u uw logische app starten met de **HTTP-aanvraag wanneer deze wordt ontvangen - Trigger aanvragen** en de begintijd doorgeven als parameter voor de trigger. Gebruik voor de eerste actie de **actie Vertraging tot - Plannen** en geef de tijd op voor het moment waarop de volgende actie wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

* [Terugkerende taken en werkstromen maken, plannen en uitvoeren met de recidieftrigger](../connectors/connectors-native-recurrence.md)
* [Terugkerende taken en werkstromen maken, plannen en uitvoeren met de trigger Schuifvenster](../connectors/connectors-native-sliding-window.md)
* [Werkstromen onderbreken met vertragingsacties](../connectors/connectors-native-delay.md)
