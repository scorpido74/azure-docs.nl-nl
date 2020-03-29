---
title: Aanbevolen procedures voor autoscale
description: Patronen automatisch schalen in Azure voor webapps, sets voor virtuele machineschalen en CloudServices
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a05cf87e660cc6c388ea2055bb174c47b99da4a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248916"
---
# <a name="best-practices-for-autoscale"></a>Aanbevolen procedures voor Automatisch schalen
Azure Monitor autoscale is alleen van toepassing op [Virtual Machine Scale Sets,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [Cloud Services](https://azure.microsoft.com/services/cloud-services/), App Service - [Web Apps](https://azure.microsoft.com/services/app-service/web/)en API [Management services](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="autoscale-concepts"></a>Concepten voor autoschaal

* Een resource kan slechts *één* instelling voor automatisch schalen hebben
* Een instelling voor automatische schaal kan een of meer profielen hebben en elk profiel kan een of meer regels voor automatisch schalen hebben.
* Met een instelling voor automatische schaal worden instanties horizontaal geschaald, wat wordt *uitgevoerd* door de exemplaren te verhogen en *door* het aantal exemplaren te verminderen.
  Een instelling voor automatische schaal heeft een maximale, minimale en standaardwaarde van instanties.
* Een taak voor automatisch schalen leest altijd de bijbehorende statistiek om op te schalen en controleert of deze de geconfigureerde drempelwaarde voor scale-out of scale-in heeft overschreden. U een lijst met statistieken weergeven die automatisch schalen kan door middel van [Algemene statistieken voor het automatisch schalen van Azure Monitor](autoscale-common-metrics.md).
* Alle drempelwaarden worden berekend op instantieniveau. Bijvoorbeeld, "schaal uit met één instantie wanneer de gemiddelde CPU > 80% wanneer het aantal exemplaren 2 is", betekent uitschalen wanneer de gemiddelde CPU in alle gevallen groter is dan 80%.
* Alle autoscale fouten worden geregistreerd bij het activiteitenlogboek. U vervolgens een [waarschuwing voor het activiteitenlogboek](./../../azure-monitor/platform/activity-log-alerts.md) configureren, zodat u via e-mail, sms of webhooks op de hoogte worden gesteld wanneer er een autoscale-storing optreedt.
* Op dezelfde manier worden alle succesvolle schaalacties op het activiteitenlogboek geplaatst. U vervolgens een waarschuwing voor het activiteitenlogboek configureren, zodat u via e-mail, sms of webhooks op de hoogte worden gesteld wanneer er een succesvolle actie voor automatisch schalen is. U e-mail- of webhookmeldingen ook configureren om een melding te ontvangen voor succesvolle schaalacties via het tabblad Meldingen op de instelling automatisch schalen.

## <a name="autoscale-best-practices"></a>Aanbevolen procedures voor automatisch schalen

Gebruik de volgende aanbevolen procedures terwijl u autoscale gebruikt.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Zorg ervoor dat de maximale en minimale waarden verschillen, en dat er voldoende marge tussen deze twee zit

Als u een instelling hebt met minimaal=2, maximum=2 en het huidige aantal instanties 2, kan er geen schaalactie plaatsvinden. Houd een voldoende marge tussen de maximale en minimale instantietellingen, die inclusief zijn. Autoscale schaalt altijd tussen deze limieten.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Handmatig schalen wordt opnieuw ingesteld door het minimum en maximum voor automatisch schalen

Als u het aantal instanties handmatig bijwerkt tot een waarde boven of onder het maximum, schaalt de autoscale-engine automatisch terug naar het minimum (indien hieronder) of het maximum (indien boven). U stelt bijvoorbeeld het bereik in tussen 3 en 6. Als u één lopende instantie hebt, schaalt de automatische motor op naar drie exemplaren bij de volgende run. Als u de schaal handmatig instelt op acht exemplaren, wordt deze op de volgende automatische schaal bij de volgende run automatisch schalen naar zes exemplaren op de volgende run.  Handmatig schalen is tijdelijk, tenzij u de regels voor automatisch schalen ook opnieuw instelt.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Gebruik altijd een scale-out- en scale-in-regelcombinatie die een toename en afname uitvoert
Als u slechts één deel van de combinatie gebruikt, onderneemt autoscale slechts actie in één richting (uitschalen of in) totdat het maximum of het minimale aantal instanties van gedefinieerd in het profiel is bereikt. Dit is niet optimaal, idealiter wilt u dat uw resource opschaalt in tijden van hoog gebruik om beschikbaarheid te garanderen. Op dezelfde manier wilt u in tijden van laag gebruik dat uw resource wordt geschaald, zodat u kostenbesparingen realiseren.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Kies de juiste statistieken voor uw diagnostische metrische gegevens
Voor diagnostische statistieken u kiezen uit *Gemiddeld*, *Minimum*, *Maximum* en *Totaal* als statistiek om op te schalen. De meest voorkomende statistiek is *Gemiddeld*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Kies zorgvuldig de drempelwaarden voor alle typen metrische gegevens
We raden u aan zorgvuldig verschillende drempels te kiezen voor scale-out en scale-in op basis van praktische situaties.

We raden instellingen voor automatisch schalen *af,* zoals de onderstaande voorbeelden met dezelfde of vergelijkbare drempelwaarden voor uit en onder omstandigheden:

* Exemplaren met 1 aantal verhogen wanneer het aantal thread>= 600
* Aantal exemplaren met 1 aantal verlagen wanneer het aantal thread's <= 600

Laten we eens kijken naar een voorbeeld van wat kan leiden tot een gedrag dat verwarrend kan lijken. Denk aan de volgende volgorde.

1. Stel dat er twee exemplaren zijn om mee te beginnen en dan groeit het gemiddelde aantal threads per instantie tot 625.
2. Autoscale schaalt het toevoegen van een derde instantie uit.
3. Ga er vervolgens van uit dat het gemiddelde aantal thread's in instantie daalt tot 575.
4. Voordat u wordt geschaald, probeert autoscale in te schatten wat de uiteindelijke status zal zijn als deze wordt ingeschaald. Bijvoorbeeld 575 x 3 (huidig aantal exemplaren) = 1.725 / 2 (eindaantal exemplaren bij verkleind) = 862,5 threads. Dit betekent dat autoscale zou moeten onmiddellijk weer schaal uit, zelfs nadat het geschaald in, als de gemiddelde draad tellen blijft hetzelfde of zelfs valt slechts een klein bedrag. Echter, als het weer opgeschaald, het hele proces zou herhalen, wat leidt tot een oneindige lus.
5. Om deze situatie te voorkomen (aangeduid als "flapping"), schaalt autoscale helemaal niet naar beneden. In plaats daarvan wordt de voorwaarde opnieuw overgeslagen en opnieuw geëvalueerd wanneer de taak van de service de volgende keer wordt uitgevoerd. De klapperende staat kan verwarren veel mensen, omdat autoscale niet zou lijken te werken wanneer de gemiddelde draad tellen was 575.

Schatting tijdens een scale-in is bedoeld om te voorkomen dat "flapperen" situaties, waar scale-in en scale-out acties voortdurend heen en weer gaan. Houd rekening met dit gedrag wanneer u dezelfde drempelwaarden kiest voor scale-out en in.

Wij raden u aan een adequate marge te kiezen tussen de scale-out en in drempels. Denk bijvoorbeeld aan de volgende combinatie van betere regels.

* Toename exemplaren met 1 telling wanneer CPU% >= 80
* Aantal exemplaren met 1 aantal verlagen wanneer CPU% <= 60

In dit geval  

1. Stel dat er 2 exemplaren zijn om mee te beginnen.
2. Als de gemiddelde CPU% in alle gevallen naar 80 gaat, schaalt autoscale het toevoegen van een derde instantie.
3. Nu aannemen dat na verloop van tijd de CPU% daalt tot 60.
4. De scale-in-regel van Autoscale schat de uiteindelijke status als deze moet worden inschaald. Bijvoorbeeld 60 x 3 (huidig aantal exemplaren) = 180 / 2 (eindaantal exemplaren bij afslanking) = 90. Autoscale schaalt dus niet in omdat het onmiddellijk weer moet uitschalen. In plaats daarvan wordt het verkleinen overgeslagen.
5. De volgende keer autoscale controles, de CPU blijft dalen tot 50. Het schat opnieuw - 50 x 3 instantie = 150 / 2 exemplaren = 75, dat is onder de scale-out drempel van 80, dus het schaalt met succes naar 2 instanties.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Overwegingen voor het schalen van drempelwaarden voor speciale metrische gegevens
 Voor speciale statistieken, zoals de lengtestatistiek opslag- of servicebuswachtrij, is de drempelwaarde het gemiddelde aantal berichten dat beschikbaar is per huidig aantal exemplaren. Kies zorgvuldig de drempelwaarde voor deze statistiek.

Laten we het illustreren met een voorbeeld om ervoor te zorgen dat u het gedrag beter begrijpt.

* Exemplaren met 1 aantal verhogen wanneer het aantal >van het opslagwachtrijbericht = 50
* Aantal exemplaren met 1 aantal verlagen wanneer het aantal meldingen van een opslagwachtrij <= 10

Overweeg de volgende volgorde:

1. Er zijn twee opslagwachtrijexemplaren.
2. Berichten blijven komen en wanneer u de opslagwachtrij bekijkt, leest het totale aantal 50. U ervan uitgaan dat automatisch schalen een scale-outactie moet starten. Houd er echter rekening mee dat het nog steeds 50/2 = 25 berichten per exemplaar is. Scale-out treedt dus niet op. Voor de eerste scale-out moet het totale aantal berichten in de opslagwachtrij 100 zijn.
3. Ga er vervolgens van uit dat het totale aantal berichten 100 bereikt.
4. Een derde opslagwachtrijinstantie wordt toegevoegd vanwege een scale-outactie.  De volgende scale-outactie vindt pas plaats als het totale aantal berichten in de wachtrij 150 bereikt omdat 150/3 = 50.
5. Nu wordt het aantal berichten in de wachtrij kleiner. Bij drie instanties vindt de eerste scale-in-actie plaats wanneer de totale berichten in alle wachtrijen oplopen tot 30 omdat 30/3 = 10 berichten per instantie, de scale-in drempel.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Overwegingen voor het schalen wanneer meerdere profielen zijn geconfigureerd in een instelling voor automatisch schalen
In een instelling voor automatisch schalen u een standaardprofiel kiezen, dat altijd wordt toegepast zonder dat u afhankelijk is van planning of tijd, of u een terugkerend profiel of een profiel kiezen voor een vaste periode met een datum- en tijdsbereik.

Wanneer de autoscale-service ze verwerkt, wordt altijd in de volgende volgorde gecontroleerd:

1. Profiel van vaste datum
2. Terugkerend profiel
3. Standaardprofiel ('Altijd')

Als aan een profielvoorwaarde is voldaan, controleert autoscale niet de volgende profielvoorwaarde eronder. Autoscale verwerkt slechts één profiel tegelijk. Dit betekent dat als u ook een verwerkingsvoorwaarde wilt opnemen vanuit een lager profiel, u deze regels ook in het huidige profiel moet opnemen.

Laten we een voorbeeld bekijken:

De onderstaande afbeelding toont een instelling voor automatisch schalen met een standaardprofiel van minimale exemplaren = 2 en maximale exemplaren = 10. In dit voorbeeld worden regels geconfigureerd om uit te schalen wanneer het aantal berichten in de wachtrij groter is dan 10 en de inschaing wanneer het aantal berichten in de wachtrij kleiner is dan drie. Dus nu kan de bron schalen tussen twee en tien exemplaren.

Daarnaast is er een terugkerend profiel ingesteld voor maandag. Het is ingesteld voor minimale exemplaren = 3 en maximale exemplaren = 10. Dit betekent dat op maandag, de eerste keer autoscale controles voor deze voorwaarde, als de instantie tellen is twee, het schalen naar het nieuwe minimum van drie. Zolang autoscale blijft deze profiel voorwaarde afgestemd (maandag) te vinden, het alleen verwerkt de CPU-gebaseerde scale-out / in regels geconfigureerd voor dit profiel. Op dit moment wordt niet gecontroleerd op de wachtrijlengte. Als u echter ook wilt dat de voorwaarde voor de wachtrijlengte wordt gecontroleerd, moet u deze regels ook in het standaardprofiel opnemen in uw maandagprofiel.

Wanneer autoscale terugschakelt naar het standaardprofiel, wordt eerst gecontroleerd of aan de minimum- en maximumvoorwaarden is voldaan. Als het aantal exemplaren op dat moment 12 is, wordt het geschaald naar 10, het maximum dat is toegestaan voor het standaardprofiel.

![instellingen voor automatisch schalen](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Overwegingen voor het schalen wanneer meerdere regels zijn geconfigureerd in een profiel

Er zijn gevallen waarin u mogelijk meerdere regels in een profiel moet instellen. De volgende regels voor automatische schaal worden gebruikt door de autoscale-engine wanneer er meerdere regels zijn ingesteld.

Bij *scale-out*wordt automatisch schalen uitgevoerd als aan een regel wordt voldaan.
Op *scale-in*, autoscale vereisen dat aan alle regels wordt voldaan.

Ga er ter illustratie van uit dat u de volgende vier regels voor autoschaal hebt:

* Als cpu-< 30%, schaal-in door 1
* Als het geheugen < 50%, schaal-in door 1
* Als CPU > 75%, schaal-out met 1
* Als geheugen > 75%, schaal-out door 1

Dan volgt het volgende:

* Als CPU 76% is en het geheugen 50%, schalen we uit.
* Als CPU 50% is en het geheugen 76%, schalen we uit.

Aan de andere kant, als CPU is 25% en het geheugen is 51% autoscale **niet** schaal-in. Om in te schalen, moet CPU 29% zijn en Geheugen 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Selecteer altijd een veilig standaardaantal exemplaren
Het standaardaantal instance is belangrijk omdat autoscale uw service schaalt naar dat aantal wanneer statistieken niet beschikbaar zijn. Selecteer daarom een standaardaantal instanties dat veilig is voor uw workloads.

### <a name="configure-autoscale-notifications"></a>Configureer meldingen over automatisch schalen
Autoscale wordt op het activiteitenlogboek geplaatst als een van de volgende voorwaarden zich voordoet:

* Autoscale geeft een schaalbewerking uit.
* De autoscale-service voltooit een schaalactie.
* Autoscale-service neemt geen schaalactie uit.
* Statistieken zijn niet beschikbaar voor autoscale service om een schaal beslissing te nemen.
* Statistieken zijn weer beschikbaar (herstel) om een schaalbeslissing te nemen.

U ook een waarschuwing voor het activiteitenlogboek gebruiken om de status van de autoscale-engine te controleren. Hier volgen voorbeelden om [een activiteitslogboekwaarschuwing te maken om alle bewerkingen van de autoscale-engine op uw abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) te controleren of om een [activiteitslogboekwaarschuwing te maken om alle mislukte automatische schaalin/scale-outbewerkingen op uw abonnement te controleren.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

Naast het gebruik van waarschuwingen voor activiteitenlogboeken, u ook e-mail- of webhookmeldingen configureren om een melding te ontvangen voor succesvolle schaalacties via het tabblad Meldingen op de instelling automatisch schalen.

## <a name="next-steps"></a>Volgende stappen
- [Maak een activiteitslogboekwaarschuwing om alle automatisch toekalkende enginebewerkingen op uw abonnement te controleren.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Een statusmeldingslogboekwaarschuwing maken om alle mislukte automatische schaalschaal-in/scale-outbewerkingen op uw abonnement te controleren](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

