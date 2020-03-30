---
title: De beschikbaarheid en reactiesnelheid van een website bewaken | Microsoft Docs
description: Stel webtests in Application Insights in. Ontvang een waarschuwing wanneer een website niet meer beschikbaar is of traag reageert.
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 61358051a8ddc32bc01ec5e231f4c28ebfa18ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670029"
---
# <a name="monitor-the-availability-of-any-website"></a>De beschikbaarheid van een website controleren

Nadat u uw web-app/website hebt geïmplementeerd, u terugkerende tests instellen om de beschikbaarheid en responsiviteit te controleren. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) verzendt regelmatig webaanvragen naar uw toepassing vanaf verschillende punten over de hele wereld. Het kan u waarschuwen als uw toepassing niet reageert, of als het te traag reageert.

U kunt beschikbaarheidstests instellen voor alle HTTP- en HTTPS-eindpunten die toegankelijk zijn op het openbare internet. U hoeft geen wijzigingen aan te brengen in de website die u test. In feite hoeft het niet eens een site te zijn die u bezit. U de beschikbaarheid van een REST API testen waarvan uw service afhankelijk is.

### <a name="types-of-availability-tests"></a>Soorten beschikbaarheidstests:

Er zijn drie soorten beschikbaarheidstests:

* [URL-pingtest](#create-a-url-ping-test): een eenvoudige test die u in Azure Portal kunt instellen.
* [Multi-step webtest](availability-multistep.md): Een opname van een reeks webverzoeken, die kan worden afgespeeld om complexere scenario's te testen. Webtests met meerdere stappen worden gemaakt in Visual Studio Enterprise en geüpload naar de portal voor uitvoering.
* [Aangepaste trackbeschikbaarheidstests:](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet)als u besluit een aangepaste toepassing te `TrackAvailability()` maken om beschikbaarheidstests uit te voeren, kan de methode worden gebruikt om de resultaten naar Application Insights te verzenden.

**U maximaal 100 beschikbaarheidstests maken per Application Insights-bron.**

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Als u een beschikbaarheidstest wilt maken, moet u eerst een Application Insights-bron maken. Als u al een resource hebt gemaakt, gaat u door naar de volgende sectie om [een URL-pingtest](#create-a-url-ping-test)te maken.

Selecteer in de Azure-portal de optie**AppInsights voor** **hulpprogramma's voor hulpprogramma's** > **Developer Tools** > voor resources maken en [maak een Application Insights-bron](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Een URL-pingtest aanmaken

De naam "URL ping test" is een beetje een verkeerde benaming. Voor alle duidelijkheid, deze test maakt geen gebruik van ICMP (Internet Control Message Protocol) om de beschikbaarheid van uw site te controleren. In plaats daarvan gebruikt het meer geavanceerde HTTP-aanvraagfunctionaliteit om te valideren of een eindpunt reageert. Het meet ook de prestaties die aan dat antwoord zijn gekoppeld en voegt de mogelijkheid toe om aangepaste succescriteria in te stellen in combinatie met meer geavanceerde functies, zoals het ontzeggen van afhankelijke aanvragen en het toestaan van nieuwe pogingen.

Als u uw eerste beschikbaarheidsaanvraag wilt maken, opent u het deelvenster Beschikbaarheid en selecteert **u Test maken**.

![Vul in elk geval de URL van uw website in](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Een test maken

|Instelling| Uitleg
|----|----|----|
|**Url** |  De URL kan iedere webpagina zijn die u wilt testen, maar deze moet zichtbaar zijn vanaf het openbare internet. De URL kan een queryreeks bevatten. Zo kunt u bijvoorbeeld oefenen met uw database. Als de URL naar een omleiding is opgelost, kunnen we deze tot maximaal 10 omleidingen opvolgen.|
|**Parse afhankelijke aanvragen**| Test vraagt afbeeldingen, scripts, stijlbestanden en andere bestanden die deel uitmaken van de webpagina die wordt getest. De opgenomen reactietijd is inclusief de tijd die nodig is om deze bestanden op te halen. De test mislukt als een van deze bronnen niet met succes kan worden gedownload binnen de time-out voor de hele test. Als de optie niet is ingeschakeld, vraagt de test alleen het bestand op van de URL die u hebt opgegeven. Het inschakelen van deze optie resulteert in een strengere controle. De test kan mislukken voor gevallen, die mogelijk niet merkbaar zijn bij het handmatig surfen op de site.
|**Nieuwe pogingen inschakelen**|wanneer de test mislukt, wordt deze na een korte interval opnieuw geprobeerd. Fouten worden pas gerapporteerd als er drie opeenvolgende pogingen mislukken. Daaropvolgende tests worden vervolgens met de gebruikelijke testfrequentie uitgevoerd. Volgende pogingen worden tijdelijk uitgesteld tot er weer een test slaagt. Deze regel wordt onafhankelijk toegepast op elke testlocatie. **Wij raden deze optie aan.** Gemiddeld verdwijnt ongeveer 80% van de fouten na het opnieuw proberen.|
|**Testfrequentie**| Hiermee bepaalt u hoe vaak de test wordt uitgevoerd vanaf elke testlocatie. Met een standaardfrequentie van vijf minuten en vijf testlocaties wordt uw site gemiddeld per minuut getest.|
|**Testlocaties**| Zijn de plaatsen van waar onze servers webverzoeken naar uw URL sturen. **Ons minimum aantal aanbevolen testlocaties is vijf** om ervoor te zorgen dat u problemen op uw website onderscheiden van netwerkproblemen. U kunt maximaal 16 locaties selecteren.

**Als uw URL niet zichtbaar is vanaf het openbare internet, u ervoor kiezen om selectief uw firewall te openen om alleen de testtransacties toe te staan via**. Raadpleeg de [IP-adreshandleiding](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests)voor meer informatie over de firewalluitzonderingen voor onze testagents voor beschikbaarheid.

> [!NOTE]
> We raden u ten zeerste aan om te testen vanaf meerdere locaties met **een minimum van vijf locaties.** Dit is om vals alarm te voorkomen dat kan voortvloeien uit tijdelijke problemen met een specifieke locatie. Daarnaast hebben we geconstateerd dat de optimale configuratie is om het aantal testlocaties gelijk te zijn **aan de drempelwaarde voor waarschuwingslocaties + 2**.

### <a name="success-criteria"></a>Succescriteria

|Instelling| Uitleg
|----|----|----|
| **Time-out van de test** |Verlaag deze waarde om te worden gewaarschuwd voor trage reacties. De test wordt als mislukt beschouwd als er binnen deze periode geen reactie van uw site is ontvangen. Als u **Parse onafhankelijke aanvragen** hebt geselecteerd, moeten alle afbeeldingen, stijlbestanden, scripts en andere afhankelijke resources binnen deze periode worden ontvangen.|
| **HTTP-antwoord** | De geretourneerde statuscode die als een succes wordt geteld. 200 is de code die aangeeft dat er een normale webpagina is geretourneerd.|
| **Inhoudsovereenkomst** | Een string, zoals "Welkom!" Er wordt getest of er in elke respons een exacte (hoofdlettergevoelige) overeenkomst wordt gevonden. Het moet een eenvoudige tekenreeks zijn, zonder jokertekens. Als uw pagina-inhoud wordt gewijzigd, moet u deze tekenreeks mogelijk ook bijwerken. **Alleen Engelse tekens worden ondersteund met inhoudsovereenkomst** |

### <a name="alerts"></a>Waarschuwingen

|Instelling| Uitleg
|----|----|----|
|**Bijna realtime (Preview)** | We raden u aan near-realtime waarschuwingen te gebruiken. Het configureren van dit type waarschuwing gebeurt nadat uw beschikbaarheidstest is gemaakt.  |
|**Klassiek** | We raden niet langer aan om klassieke waarschuwingen te gebruiken voor nieuwe beschikbaarheidstests.|
|**Drempelwaarde voor waarschuwingslocatie**|Wij raden minimaal 3/5 locaties aan. De optimale relatie tussen waarschuwingslocatiedrempel en het aantal testlocaties is **waarschuwingslocatiedrempel** = **aantal testlocaties - 2, met een minimum van vijf testlocaties.**|

## <a name="see-your-availability-test-results"></a>De resultaten van de beschikbaarheidstest bekijken

Beschikbaarheidstestresultaten kunnen worden gevisualiseerd met zowel lijn- als spreidingsplotweergaven.

Klik na enkele minuten op **Vernieuwen** om de testresultaten te bekijken.

![Lijnweergave](./media/monitor-web-app-availability/availability-refresh-002.png)

De scatterplot-weergave toont voorbeelden van de testresultaten met diagnostische teststapdetails. De testengine slaat diagnostische gegevens op voor tests met fouten. Bij geslaagde tests wordt diagnostische informatie voor een subset van de uitvoeringen opgeslagen. Plaats de ruimte over een van de groen/rode stippen om de test, testnaam en locatie te zien.

![Lijnweergave](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Selecteer een bepaalde test of locatie, of verklein de periode om meer resultaten te zien uit de periode die voor u van belang is. Gebruik Search Explorer om resultaten van alle uitvoeringen weer te geven, of gebruik Analytics-query's om aangepaste rapporten uit te voeren op deze gegevens.

## <a name="inspect-and-edit-tests"></a> Tests bekijken en bewerken

Als u een test wilt bewerken, tijdelijk wilt uitschakelen of verwijderen, klikt u op de ellipsen naast een testnaam. Het kan tot 20 minuten duren voordat configuratiewijzigingen worden doorgevoerd naar alle testagents nadat een wijziging is aangebracht.

![Bekijk testgegevens. Een webtest bewerken en uitschakelen](./media/monitor-web-app-availability/edit.png)

Het is verstandig beschikbaarheidstests of de regels voor waarschuwingen die eraan zijn gekoppeld uit te schakelen wanneer u onderhoud uitvoert op uw service.

## <a name="if-you-see-failures"></a>Als u mislukte tests ziet

Klik op een rode punt.

![Op een rode punt klikken](./media/monitor-web-app-availability/open-instance-3.png)

Aan de basis van een testresultaat voor beschikbaarheid u de transactiegegevens van alle onderdelen bekijken. Hier u:

* De reactie inspecteren die is ontvangen van uw server.
* Diagnose fout met gecorreleerde server-side telemetrie verzameld tijdens het verwerken van de mislukte beschikbaarheid test.
* Meld een probleem of werkitem aan in Git- of Azure-borden om het probleem bij te houden. De bug bevat een koppeling naar deze gebeurtenis.
* Het webtestresultaat openen in Visual Studio.

Lees hier meer over de ervaring met transactiediagnostiek [aan](../../azure-monitor/app/transaction-diagnostics.md)het einde.

Klik op de uitzonderingsrij om de details van de server-side uitzondering te zien waardoor de synthetische beschikbaarheidstest is mislukt. U ook de [foutopsporingsmomentopname](../../azure-monitor/app/snapshot-debugger.md) voor rijkere diagnose codeniveau krijgen.

![Diagnose aan serverzijde](./media/monitor-web-app-availability/open-instance-4.png)

Naast de ruwe resultaten u ook twee belangrijke beschikbaarheidsstatistieken bekijken in [Metrics Explorer:](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

1. Beschikbaarheid: percentage van de tests die zijn geslaagd, bekeken over alle testuitvoeringen.
2. Testduur: gemiddelde testduur van alle testuitvoeringen.

## <a name="automation"></a>Automation

* Gebruik [PowerShell-scripts om automatisch een beschikbaarheidstest in te stellen](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Stel een [webhook](../../azure-monitor/platform/alerts-webhooks.md) in die wordt aangeroepen wanneer er een waarschuwing wordt gegenereerd.

## <a name="troubleshooting"></a>Problemen oplossen

Specifiek [artikel over probleemoplossing](troubleshoot-availability.md).

## <a name="next-steps"></a>Volgende stappen

* [Beschikbaarheidswaarschuwingen](availability-alerts.md)
* [Webtests met meerdere stappen](availability-multistep.md)


