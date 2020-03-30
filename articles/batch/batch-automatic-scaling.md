---
title: Compute-knooppunten automatisch schalen in een Azure Batch-groep | Microsoft Documenten
description: Automatische schaling op een cloudgroep inschakelen om dynamisch het aantal compute nodes in de groep aan te passen.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 10/24/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017,fasttrack-edit
ms.openlocfilehash: 398b6d9c3fc05a6cf164b4003f57b94ecd6c1972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054021"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Een automatische formule maken voor het schalen van compute-knooppunten in een batchgroep

Azure Batch kan groepen automatisch schalen op basis van parameters die u definieert. Met automatisch schalen voegt Batch dynamisch knooppunten toe aan een groep naarmate de taakvereisten toenemen en worden rekenknooppunten verwijderd naarmate deze afnemen. U tijd en geld besparen door automatisch het aantal compute nodes aan te passen dat door uw Batch-toepassing wordt gebruikt.

U schakelt automatisch schalen in op een groep compute nodes door er een *automatisch schaalformule* mee te koppelen die u definieert. De batchservice gebruikt de formule voor automatisch schalen om het aantal compute nodes te bepalen dat nodig is om uw werkbelasting uit te voeren. Compute-knooppunten kunnen speciale knooppunten of [knooppunten met een lage prioriteit](batch-low-pri-vms.md)zijn. Batch reageert op servicemetrics gegevens die periodiek worden verzameld. Met behulp van deze metrische gegevens past Batch het aantal compute nodes in de groep aan op basis van uw formule en met een configureerbaar interval.

U automatisch schalen inschakelen wanneer een groep wordt gemaakt of op een bestaande groep. U ook een bestaande formule wijzigen in een groep die is geconfigureerd voor automatisch schalen. Met Batch u uw formules evalueren voordat u ze aan groepen toewijst en de status van automatische schalingruns controleert.

In dit artikel worden de verschillende entiteiten besproken die deel uitmaken van uw formules voor automatische schaal, waaronder variabelen, operatoren, bewerkingen en functies. We bespreken hoe u verschillende compute resource- en taakstatistieken binnen Batch verkrijgen. U deze statistieken gebruiken om het aantal knooppuntgegevens van uw groep aan te passen op basis van het gebruik van resources en de taakstatus. Vervolgens beschrijven we hoe u een formule maken en automatische schaling op een groep inschakelen met behulp van zowel de Batch REST als de .NET API's. Tot slot eindigen we met een paar voorbeeldformules.

> [!IMPORTANT]
> Wanneer u een Batch-account maakt, u de [accountconfiguratie](batch-api-basics.md#account)opgeven, die bepaalt of groepen worden toegewezen in een Batch-serviceabonnement (de standaard- of in uw gebruikersabonnement). Als u uw Batch-account hebt gemaakt met de standaard Batch Service-configuratie, is uw account beperkt tot een maximum aantal cores dat kan worden gebruikt voor verwerking. De batchservice schaalt compute nodes alleen tot die kernlimiet. Om deze reden kan de batchservice het doelaantal rekenknooppunten dat is opgegeven door een formule voor automatische schaal niet bereiken. Zie [Quota en limieten voor de Azure Batch-service](batch-quota-limit.md) voor informatie over het weergeven en verhogen van uw accountquota.
>
>Als u uw account hebt gemaakt met de configuratie van gebruikersabonnementen, wordt uw account gedeeld in het kernquotum voor het abonnement. Zie [Virtual Machines limits](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) (Limieten voor Virtuele Machines) in [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md) (Azure-abonnement en servicelimieten, -quota en -beperkingen) voor meer informatie.
>
>

## <a name="automatic-scaling-formulas"></a>Formules voor automatisch schalen

Een automatische schaalformule is een tekenreekswaarde die u definieert en die een of meer instructies bevat. De formule automatisch schalen is toegewezen aan het [autoScaleFormula-element][rest_autoscaleformula] (Batch REST) of de eigenschap [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (Batch .NET). De batchservice gebruikt uw formule om het doelaantal rekenknooppunten in de groep te bepalen voor het volgende interval van verwerking. De formuletekenreeks mag niet hoger zijn dan 8 KB, kan maximaal 100 instructies bevatten die worden gescheiden door puntkomma's en kan regeleinden en opmerkingen bevatten.

U automatische schaalformules zien als een batch-autoscale 'taal'. Formuleinstructies zijn vrijgevormde expressies die zowel door de service gedefinieerde variabelen (variabelen gedefinieerd door de Batch-service) als door de gebruiker gedefinieerde variabelen (variabelen die u definieert) kunnen bevatten. Ze kunnen verschillende bewerkingen uitvoeren op deze waarden met behulp van ingebouwde typen, operators en functies. Een instructie kan bijvoorbeeld het volgende formulier aannemen:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formules bevatten over het algemeen meerdere instructies die bewerkingen uitvoeren op waarden die zijn verkregen in eerdere instructies. Bijvoorbeeld, eerst verkrijgen we `variable1`een waarde voor, dan `variable2`doorgeven aan een functie te vullen:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Neem deze instructies op in uw formule voor automatisch schalen om te komen tot een doelaantal compute nodes. Speciale knooppunten en knooppunten met lage prioriteit hebben elk hun eigen doelinstellingen, zodat u een doel voor elk type knooppunt definiëren. Een formule voor automatische schaal kan een doelwaarde bevatten voor dedicated knooppunten, een doelwaarde voor knooppunten met lage prioriteit of beide.

Het doelaantal knooppunten kan hoger, lager of hetzelfde zijn als het huidige aantal knooppunten van dat type in de groep. Batch evalueert de automatische schaalformule van een groep met een specifiek interval (zie [automatische schaalintervallen).](#automatic-scaling-interval) Batch past het doelnummer van elk type knooppunt in de groep aan op het getal dat uw formule automatisch schalen opgeeft op het moment van evaluatie.

### <a name="sample-autoscale-formulas"></a>Voorbeeldformules voor automatische schaal

Hieronder vindt u voorbeelden van twee autoscale formules, die kunnen worden aangepast om te werken voor de meeste scenario's. De variabelen `startingNumberOfVMs` `maxNumberofVMs` en in de voorbeeldformules kunnen worden aangepast aan uw behoeften.

#### <a name="pending-tasks"></a>In behandeling zijnde taken

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

Met deze formule voor automatisch schalen wordt de groep in eerste instantie gemaakt met één vm. De `$PendingTasks` statistiek definieert het aantal taken dat wordt uitgevoerd of in de wachtrij staat. De formule vindt het gemiddelde aantal lopende taken in de `$TargetDedicatedNodes` laatste 180 seconden en stelt de variabele dienovereenkomstig in. De formule zorgt ervoor dat het doelaantal toegewezen knooppunten nooit hoger is dan 25 VM's. Als nieuwe taken worden ingediend, wordt de groep automatisch groter. Naarmate taken worden voltooid, worden VM's één voor één gratis en wordt de formule voor automatisch schalen de groep kleiner.

Deze formule schaalt speciale knooppunten, maar kan worden gewijzigd om ook te worden toegepast op schaal knooppunten met lage prioriteit.

#### <a name="preempted-nodes"></a>Voorkomknooppunten 

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

In dit voorbeeld wordt een groep gemaakt die begint met 25 knooppunten met een lage prioriteit. Elke keer dat een knooppunt met lage prioriteit wordt voorkom, wordt het vervangen door een speciaal knooppunt. Net als bij het `maxNumberofVMs` eerste voorbeeld voorkomt de variabele dat de pool meer dan 25 VM's bedraagt. Dit voorbeeld is handig om gebruik te maken van vm's met lage prioriteit en er tegelijkertijd voor te zorgen dat er slechts een vast aantal voorrangsinstanties optreedt voor de levensduur van de pool.

## <a name="variables"></a>Variabelen

U zowel **servicegedefinieerde** als **door de gebruiker gedefinieerde** variabelen gebruiken in uw formules voor automatische schaal. De door de service gedefinieerde variabelen zijn ingebouwd in de Batch-service. Sommige servicegedefinieerde variabelen zijn read-write en sommige zijn alleen-lezen. Door de gebruiker gedefinieerde variabelen zijn variabelen die u definieert. In de voorbeeldformule in de `$TargetDedicatedNodes` `$PendingTasks` vorige sectie en zijn servicegedefinieerde variabelen. Variabelen `startingNumberOfVMs` en `maxNumberofVMs` zijn door de gebruiker gedefinieerde variabelen.

> [!NOTE]
> Servicegedefinieerde variabelen worden altijd voorafgegaan door een dollarteken ($). Voor door de gebruiker gedefinieerde variabelen is het dollarteken optioneel.
>
>

In de volgende tabellen worden zowel lees-geschreven- als alleen-lezenvariabelen weergegeven die worden gedefinieerd door de batchservice.

U de waarden van deze door de service gedefinieerde variabelen opmaken en instellen om het aantal compute nodes in een groep te beheren:

| Door lezen schrijvenservice gedefinieerde variabelen | Beschrijving |
| --- | --- |
| $TargetDedicatedNodes |Het doelaantal speciale compute nodes voor de groep. Het aantal speciale knooppunten wordt als doel opgegeven omdat een groep mogelijk niet altijd het gewenste aantal knooppunten bereikt. Als bijvoorbeeld het doelaantal toegewezen knooppunten wordt gewijzigd door een evaluatie van automatisch schalen voordat de groep het oorspronkelijke doel heeft bereikt, kan het zijn dat de groep het doel niet bereikt. <br /><br /> Een groep in een account dat is gemaakt met de batchserviceconfiguratie, kan zijn doel niet bereiken als het doel een batchaccountknooppunt of kernquotum overschrijdt. Een groep in een account dat is gemaakt met de configuratie van gebruikersabonnementen, kan zijn doel niet bereiken als het doel het gedeelde kernquotum voor het abonnement overschrijdt.|
| $TargetLowPriorityNodes |Het doelaantal compute nodes met lage prioriteit voor de groep. Het aantal knooppunten met lage prioriteit wordt als doel opgegeven omdat een groep mogelijk niet altijd het gewenste aantal knooppunten bereikt. Als bijvoorbeeld het doelaantal knooppunten met lage prioriteit wordt gewijzigd door een evaluatie met automatische schaal voordat de groep het oorspronkelijke doel heeft bereikt, kan het zijn dat de groep het doel niet bereikt. Een groep kan ook zijn doel niet bereiken als het doel een batchaccountknooppunt of kernquotum overschrijdt. <br /><br /> Zie [Vm's met lage prioriteit gebruiken met batch](batch-low-pri-vms.md)voor meer informatie over compute nodes met lage prioriteit. |
| $NodeDeallocationOption |De actie die optreedt wanneer rekenknooppunten uit een groep worden verwijderd. Mogelijke waarden zijn:<ul><li>**opnieuw in de wachtrij**: de standaardwaarde. Hiermee worden taken onmiddellijk beëindigd en weer in de taakwachtrij geplaatst, zodat ze opnieuw worden gepland. Deze actie zorgt ervoor dat het doelaantal knooppunten zo snel mogelijk wordt bereikt, maar kan minder efficiënt zijn, omdat alle lopende taken worden onderbroken en opnieuw moeten worden gestart, waardoor al het werk dat ze al hadden gedaan, wordt verspeeld. <li>**taken onmiddellijk beëindigen**--Hiermee worden taken onmiddellijk beëindigd en uit de taakwachtrij verwijderd.<li>**taakvoltooiing**--Wacht tot momenteel taken worden uitgevoerd en verwijdert vervolgens het knooppunt uit de groep. Gebruik deze optie om te voorkomen dat taken worden onderbroken en opnieuw in de wachtrij worden geplaatst, waarbij het werk dat de taak heeft uitgevoerd, wordt verspillen. <li>**bewaarde gegevens**--Wacht tot alle lokale taakbewaarde gegevens op het knooppunt worden opgeschoond voordat het knooppunt uit het zwembad wordt verwijderd.</ul> |

> [!NOTE]
> De `$TargetDedicatedNodes` variabele kan ook worden `$TargetDedicated`opgegeven met behulp van de alias . Op dezelfde `$TargetLowPriorityNodes` manier kan de variabele `$TargetLowPriority`worden opgegeven met behulp van de alias . Als zowel de variabele met volledige naam als de alias door de formule zijn ingesteld, heeft de waarde die is toegewezen aan de volledig benoemde variabele voorrang.
>
>

U de waarde van deze door service gedefinieerde variabelen ophalen om aanpassingen aan te brengen die zijn gebaseerd op statistieken van de Batch-service:

| Alleen-lezen servicegedefinieerde variabelen | Beschrijving |
| --- | --- |
| $CPUPercent |Het gemiddelde percentage van het CPU-gebruik. |
| $WallClockSeconds |Het aantal verbruikte seconden. |
| $MemoryBytes |Het gemiddelde aantal gebruikte megabytes. |
| $DiskBytes |Het gemiddelde aantal gigabytes dat op de lokale schijven wordt gebruikt. |
| $DiskReadBytes |Het aantal gelezen bytes. |
| $DiskWriteBytes |Het aantal geschreven bytes. |
| $DiskReadOps |Het aantal uitgevoerde leesschijfbewerkingen. |
| $DiskWriteOps |Het aantal uitgevoerde schrijfschijfbewerkingen. |
| $NetworkInBytes |Het aantal binnenkomende bytes. |
| $NetworkOutBytes |Het aantal uitgaande bytes. |
| $SampleNodeCount |Het aantal compute nodes. |
| $ActiveTasks |Het aantal taken dat klaar is om uit te voeren, maar nog niet wordt uitgevoerd. Het aantal $ActiveTasks omvat alle taken die zich in de actieve status bevinden en waarvan de afhankelijkheden zijn voldaan. Taken die zich in de actieve status bevinden maar waarvan de afhankelijkheden niet zijn voldaan, worden uitgesloten van het aantal $ActiveTasks. Voor een taak met meerdere instanties $ActiveTasks het aantal instanties dat op de taak is ingesteld.|
| $RunningTasks |Het aantal taken in een actieve status. |
| $PendingTasks |De som van $ActiveTasks en $RunningTasks. |
| $SucceededTasks |Het aantal taken dat is voltooid. |
| $FailedTasks |Het aantal taken dat is mislukt. |
| $CurrentDedicatedNodes |Het huidige aantal speciale compute nodes. |
| $CurrentLowPriorityNodes |Het huidige aantal compute nodes met lage prioriteit, inclusief knooppunten die zijn voorgekomen. |
| $PreemptedNodeCount | Het aantal knooppunten in de groep dat zich in een voorloopstatus begeeft. |

> [!TIP]
> De alleen-lezen, servicegedefinieerde variabelen die in de vorige tabel worden weergegeven, zijn *objecten* die verschillende methoden bieden om toegang te krijgen tot gegevens die aan elk van deze objecten zijn gekoppeld. Zie [Voorbeeldgegevens](#getsampledata) later in dit artikel verkrijgen voor meer informatie.
>
>

## <a name="types"></a>Typen

Deze typen worden ondersteund in een formule:

* double
* doubleVec
* doubleVecList
* tekenreeks
* tijdstempel- tijdstempel is een samengestelde structuur die de volgende leden bevat:

  * jaar
  * maand (1-12)
  * dag (1-31)
  * weekdag (in de notatie van nummer; bijvoorbeeld 1 voor maandag)
  * uur (in 24-uurs getalnotatie; bijvoorbeeld 13 betekent 1 PM)
  * minuut (00-59)
  * tweede (00-59)
* tijdsinterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Bewerkingen

Deze bewerkingen zijn toegestaan op de typen die in de vorige sectie worden vermeld.

| Bewerking | Ondersteunde operatoren | Resultaattype |
| --- | --- | --- |
| dubbele *operator* dubbel |+, -, *, / |double |
| tijdsinterval voor dubbele *operatoren* |* |tijdsinterval |
| doubleVec *operator* dubbel |+, -, *, / |doubleVec |
| doubleVec *operator* doubleVec |+, -, *, / |doubleVec |
| *tijdintervaloperator* dubbel |*, / |tijdsinterval |
| tijdsinterval *operator* tijdsinterval |+, - |tijdsinterval |
| tijdstempel *tijdsintervaloperator* |+ |tijdstempel |
| *tijdstempeloperatortijdinterval* |+ |tijdstempel |
| tijdstempel *operator* tijdstempel |- |tijdsinterval |
| *operator*dubbel |-, ! |double |
| *operatortijdinterval* |- |tijdsinterval |
| dubbele *operator* dubbel |<, <=,,, >=, >, != |double |
| *tekenreekstekenreeks* |<, <=,,, >=, >, != |double |
| tijdstempel *operator* tijdstempel |<, <=,,, >=, >, != |double |
| tijdsinterval *operator* tijdsinterval |<, <=,,, >=, >, != |double |
| dubbele *operator* dubbel |&&,  &#124;&#124; |double |

Bij het testen van een`double ? statement1 : statement2`dubbele met een ternaire operator ( ), nonzero is **waar**, en nul is **vals**.

## <a name="functions"></a>Functions
Deze vooraf gedefinieerde **functies** zijn beschikbaar voor u om te gebruiken bij het definiëren van een automatische schaling formule.

| Functie | Retourtype | Beschrijving |
| --- | --- | --- |
| avg(doubleVecList) |double |Geeft als resultaat de gemiddelde waarde voor alle waarden in de doubleVecList. |
| len(doubleVecList) |double |Geeft als resultaat de lengte van de vector die is gemaakt met de doubleVecList. |
| lg(dubbel) |double |Geeft als resultaat de logbasis 2 van de dubbel. |
| lg(doubleVecList) |doubleVec |Geeft als resultaat de component-wise log base 2 van de doubleVecList. Een vec(dubbel) moet expliciet worden doorgegeven voor de parameter. Anders wordt de dubbele lg(dubbele) versie aangenomen. |
| Ln(dubbel) |double |Geeft als resultaat het natuurlijke logboek van de dubbel. |
| ln(doubleVecList) |doubleVec |Geeft als resultaat het natuurlijke logboek van de dubbel. |
| log(dubbel) |double |Geeft als resultaat de logbasis 10 van de dubbel. |
| log(doubleVecList) |doubleVec |Geeft als resultaat de component-wise log base 10 van de doubleVecList. Een vec(dubbel) moet expliciet worden doorgegeven voor de enkele dubbele parameter. Anders wordt de dubbele logversie aangenomen. |
| max(doubleVecList) |double |Geeft als resultaat de maximale waarde in de doubleVecList. |
| min(doubleVecList) |double |Geeft als resultaat de minimumwaarde in de doubleVecList. |
| norm(doubleVecList) |double |Geeft als resultaat de tweenorm van de vector die is gemaakt met de doubleVecList. |
| percentiel (doubleVec v, dubbele p) |double |Geeft als resultaat het percentielelement van de vector v. |
| rand() |double |Geeft als resultaat een willekeurige waarde tussen 0,0 en 1,0. |
| bereik(doubleVecList) |double |Geeft als resultaat het verschil tussen de min- en maximumwaarden in de doubleVecList. |
| std(doubleVecList) |double |Geeft als resultaat de voorbeeldstandaarddeviatie van de waarden in de doubleVecList. |
| stop() | |Hiermee stopt u de evaluatie van de expressie automatisch schalen. |
| som(doubleVecList) |double |Geeft als resultaat de som van alle onderdelen van de doubleVecList. |
| tijd(tekenreeksdatumTijd="") |tijdstempel |Geeft als resultaat de tijdstempel van de huidige tijd als er geen parameters worden doorgegeven of de tijdstempel van de tekenreeks dateTime als deze wordt doorgegeven. Ondersteunde dateTime-indelingen zijn W3C-DTF en RFC 1123. |
| val(doubleVec v, dubbel i) |double |Geeft als resultaat de waarde van het element dat zich op locatie i in vector v bevindt, met een beginindex van nul. |

Sommige functies die in de vorige tabel worden beschreven, kunnen een lijst als argument accepteren. De door komma's gescheiden lijst is een combinatie van *dubbel* en *dubbelVec.* Bijvoorbeeld:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

De *doubleVecList-waarde* wordt voor de evaluatie omgezet in één *doubleVec.* Als `v = [1,2,3]`u bijvoorbeeld belt `avg(v)` gelijk staat `avg(1,2,3)`aan bellen . Bellen `avg(v, 7)` staat gelijk `avg(1,2,3,7)`aan bellen.

## <a name="obtain-sample-data"></a><a name="getsampledata"></a>Voorbeeldgegevens verkrijgen

Formules voor automatisch schalen werken op metrische gegevens (voorbeelden) die worden geleverd door de batchservice. Een formule vergroot of krimpt de grootte van de groep op basis van de waarden die deze uit de service haalt. De eerder beschreven variabelen met service zijn objecten die verschillende methoden bieden om toegang te krijgen tot gegevens die aan dat object zijn gekoppeld. In de volgende expressie wordt bijvoorbeeld een verzoek weergegeven om de laatste vijf minuten cpu-gebruik te krijgen:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Methode | Beschrijving |
| --- | --- |
| GetSample() |De `GetSample()` methode retourneert een vector van gegevensmonsters.<br/><br/>Een voorbeeld is 30 seconden aan metrische gegevens. Met andere woorden, monsters worden elke 30 seconden verkregen. Maar zoals hieronder vermeld, is er een vertraging tussen het moment waarop een monster wordt verzameld en wanneer het beschikbaar is voor een formule. Als zodanig kunnen niet alle monsters voor een bepaalde periode beschikbaar zijn voor evaluatie door middel van een formule.<ul><li>`doubleVec GetSample(double count)`<br/>Hiermee geeft u het aantal monsters op dat moet worden verkregen uit de meest recente monsters die zijn verzameld.<br/><br/>`GetSample(1)`retourneert het laatst beschikbare voorbeeld. Voor statistieken `$CPUPercent`zoals , dit mag echter niet worden gebruikt, omdat het onmogelijk is om te weten *wanneer* het monster werd verzameld. Het kan recent zijn, of, als gevolg van systeemproblemen, kan het veel ouder zijn. Het is in dergelijke gevallen beter om een tijdsinterval te gebruiken zoals hieronder wordt weergegeven.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Hiermee geeft u een tijdskader op voor het verzamelen van voorbeeldgegevens. Optioneel geeft het ook het percentage monsters aan dat beschikbaar moet zijn in het gevraagde tijdsbestek.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`zou terugkeren 20 monsters als alle monsters voor de laatste 10 minuten aanwezig zijn in de CPUPercent geschiedenis. Als de laatste minuut van de geschiedenis niet beschikbaar was, zouden echter slechts 18 monsters worden geretourneerd. In dat geval:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`zou mislukken omdat slechts 90 procent van de monsters beschikbaar zijn.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`zou slagen.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Hiermee geeft u een tijdskader op voor het verzamelen van gegevens, met zowel een begintijd als een eindtijd.<br/><br/>Zoals hierboven vermeld, is er een vertraging tussen het moment waarop een monster wordt verzameld en wanneer het beschikbaar is voor een formule. Houd rekening met deze `GetSample` vertraging wanneer u de methode gebruikt. Zie `GetSamplePercent` hieronder. |
| GetSamplePeriod() |Geeft als resultaat de periode van monsters die zijn genomen in een historische steekproefgegevensset. |
| Aantal() |Geeft als resultaat het totale aantal monsters in de metrische geschiedenis. |
| HistoryBeginTime() |Geeft als resultaat de tijdstempel van het oudste beschikbare gegevensvoorbeeld voor de statistiek. |
| GetSamplePercent() |Geeft als resultaat het percentage monsters dat beschikbaar is voor een bepaald tijdsinterval. Bijvoorbeeld:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Omdat `GetSample` de methode mislukt als het percentage `samplePercent` geretourneerde monsters lager `GetSamplePercent` is dan het opgegeven percentage, u de methode gebruiken om eerst te controleren. Vervolgens u een alternatieve actie uitvoeren als er onvoldoende monsters aanwezig zijn, zonder de automatische schaling-evaluatie te stoppen. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Monsters, steekproefpercentage en de methode *GetSample()*
De kernbewerking van een formule voor automatische schaal is het verkrijgen van taak- en resourcemetrische gegevens en vervolgens de grootte van de groep op basis van die gegevens aan te passen. Als zodanig is het belangrijk om een duidelijk inzicht te hebben in hoe formules voor automatisch schalen omgaan met metrische gegevens (voorbeelden).

**Monsters**

De batchservice neemt periodiek voorbeelden van taak- en resourcestatistieken en maakt deze beschikbaar voor uw formules voor automatisch schalen. Deze monsters worden elke 30 seconden geregistreerd door de Batch-service. Er is echter meestal een vertraging tussen het moment waarop deze monsters zijn opgenomen en wanneer ze beschikbaar worden gesteld aan (en kan worden gelezen door) uw autoscale formules. Bovendien kunnen monsters, als gevolg van verschillende factoren, zoals netwerk- of andere infrastructuurproblemen, niet worden geregistreerd voor een bepaald interval.

**Steekproefpercentage**

Wanneer `samplePercent` wordt doorgegeven aan de `GetSample()` methode of de `GetSamplePercent()` methode wordt aangeroepen, verwijst het _percentage_ naar een vergelijking tussen het totale mogelijke aantal monsters dat wordt geregistreerd door de batchservice en het aantal monsters dat beschikbaar is voor uw autoscale-formule.

Laten we eens kijken naar een tijdspanne van 10 minuten als voorbeeld. Omdat monsters elke 30 seconden binnen een tijdspanne van 10 minuten worden opgenomen, is het maximale totale aantal monsters dat door Batch wordt geregistreerd 20 monsters (2 per minuut). Vanwege de inherente latentie van het rapportagemechanisme en andere problemen binnen Azure zijn er echter mogelijk slechts 15 voorbeelden beschikbaar voor uw autoscale-formule om te lezen. Voor die periode van 10 minuten is bijvoorbeeld slechts 75% van het totale aantal opgenomen monsters beschikbaar voor uw formule.

**GetSample() en voorbeeldbereiken**

Uw formules voor automatisch schalen zullen groeien en &mdash; uw pools verkleinen en knooppunten toevoegen of knooppunten verwijderen. Omdat knooppunten u geld kosten, wilt u ervoor zorgen dat uw formules een intelligente analysemethode gebruiken die is gebaseerd op voldoende gegevens. Daarom raden we u aan een trending-type analyse in uw formules te gebruiken. Dit type groeit en krimpt uw zwembaden op basis van een reeks verzamelde monsters.

Gebruik hiervoor een `GetSample(interval look-back start, interval look-back end)` vector van monsters terug:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Wanneer de bovenstaande regel door Batch wordt geëvalueerd, retourneert deze een reeks monsters als een vector van waarden. Bijvoorbeeld:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Zodra u de vector van monsters hebt verzameld, `min()` `max()`kunt `avg()` u functies gebruiken zoals , en om zinvolle waarden af te leiden uit het verzamelde bereik.

Voor extra beveiliging u een formule-evaluatie afdwingen als er minder dan een bepaald steekproefpercentage beschikbaar is voor een bepaalde periode. Wanneer u een formule-evaluatie forceert om te mislukken, geeft u Batch opdracht om de verdere evaluatie van de formule te staken als het opgegeven percentage monsters niet beschikbaar is. In dit geval wordt de grootte van het zwembad niet gewijzigd. Als u een vereist percentage monsters wilt opgeven dat de `GetSample()`evaluatie moet slagen, geeft u dit op als de derde parameter voor . Hier wordt een vereiste van 75 procent van de monsters opgegeven:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Omdat de beschikbaarheid van de steekproef vertraging kan oplopen, is het belangrijk om altijd een tijdsbereik op te geven met een terugkijktijd die langer is dan een minuut. Het duurt ongeveer een minuut voordat monsters door het systeem `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` worden verspreid, zodat monsters in het bereik mogelijk niet beschikbaar zijn. Nogmaals, u de `GetSample()` parameter percentage gebruiken om een bepaalde vereiste van het steekproefpercentage af te dwingen.

> [!IMPORTANT]
> We raden u **ten zeerste aan** **om niet *alleen* te vertrouwen in `GetSample(1)` uw formules voor automatische schaal.** Dit komt `GetSample(1)` omdat in wezen zegt tegen de Batch service, "Geef me het laatste monster dat je hebt, maakt niet uit hoe lang geleden je het opgehaald." Aangezien het slechts één steekproef is en het een oudere steekproef kan zijn, kan het niet representatief zijn voor het grotere beeld van de recente taak- of resourcestatus. Als u `GetSample(1)`dit wel gebruikt, moet u ervoor zorgen dat het deel uitmaakt van een grotere instructie en niet het enige gegevenspunt waarop uw formule is gebaseerd.
>
>

## <a name="metrics"></a>Metrische gegevens

U zowel resource- als taakstatistieken gebruiken wanneer u een formule definieert. U past het doelaantal toegewezen knooppunten in de groep aan op basis van de metrische gegevens die u verkrijgt en evalueert. Zie de sectie [Variabelen](#variables) hierboven voor meer informatie over elke statistiek.

<table>
  <tr>
    <th>Gegevens</th>
    <th>Beschrijving</th>
  </tr>
  <tr>
    <td><b>Resource</b></td>
    <td><p>Resourcestatistieken zijn gebaseerd op de CPU, de bandbreedte, het geheugengebruik van compute nodes en het aantal knooppunten.</p>
        <p> Deze door service gedefinieerde variabelen zijn handig voor het aanpassen van correcties op basis van het aantal nodes:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Deze door service gedefinieerde variabelen zijn handig voor het aanpassen van aanpassingen op basis van het gebruik van knooppuntbronnen:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Taak</b></td>
    <td><p>Taakstatistieken zijn gebaseerd op de status van taken, zoals Actief, In behandeling en voltooid. De volgende door de service gedefinieerde variabelen zijn handig voor het aanpassen van de grootte van de groep op basis van taakstatistieken:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Een formule voor automatische schaal schrijven

U bouwt een formule voor automatische schaal door instructies te vormen die de bovenstaande componenten gebruiken en combineert deze instructies vervolgens in een volledige formule. In deze sectie maken we een voorbeeldvan autoscale formule die een aantal real-world scaling beslissingen kan uitvoeren.

Laten we eerst de vereisten voor onze nieuwe autoscale-formule definiëren. De formule moet:

1. Verhoog het doelaantal speciale compute nodes in een groep als het CPU-gebruik hoog is.
1. Verlaag het doelaantal toegewezen compute nodes in een groep wanneer het CPU-gebruik laag is.
1. Beperk altijd het maximum aantal speciale knooppunten tot 400.
1. Verwijder bij het verminderen van het aantal knooppunten geen knooppunten waarop taken worden uitgevoerd. wacht indien nodig tot de taken zijn voltooid om knooppunten te verwijderen.

Als u het aantal knooppunten wilt verhogen tijdens een hoog CPU-gebruik,`$totalDedicatedNodes`definieert u de instructie waarin een door de gebruiker gedefinieerde variabele ( ) wordt gevuld met een waarde die 110 procent van het huidige doelaantal toegewezen knooppunten is, maar alleen als het minimale gemiddelde CPU-gebruik tijdens de laatste 10 minuten boven de 70 procent lag. Gebruik anders de waarde voor het huidige aantal toegewezen knooppunten.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Om het aantal dedicated nodes tijdens een laag CPU-gebruik te `$totalDedicatedNodes` *verminderen,* stelt de volgende instructie in onze formule dezelfde variabele in op 90 procent van het huidige doelaantal toegewezen knooppunten als het gemiddelde CPU-gebruik in de afgelopen 60 minuten minder dan 20 procent was. Gebruik anders de huidige `$totalDedicatedNodes` waarde van de bovenstaande instructie.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Beperk nu het doelaantal toegewezen compute nodes tot maximaal 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Hier is de volledige formule:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Een groep met automatische schaalingeschakeld maken met Batch-SDK's

Groep autoscaling kan worden geconfigureerd met behulp van een van de [Batch SDKs,](batch-apis-tools.md#azure-accounts-for-batch-development)de [Batch REST API](https://docs.microsoft.com/rest/api/batchservice/) Batch [PowerShell cmdlets,](batch-powershell-cmdlets-get-started.md)en de [Batch CLI](batch-cli-get-started.md). In deze sectie ziet u voorbeelden voor zowel .NET als Python.

### <a name="net"></a>.NET

Voer de volgende stappen uit om een groep te maken waarin automatisch schalen is ingeschakeld in .NET:

1. Maak de groep met [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Stel de eigenschap [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) in op `true`.
1. Stel de eigenschap [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) in met uw formule voor automatisch schalen.
1. (Optioneel) Stel de eigenschap [CloudPool.AutoScaleEvaluationInterval in](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (standaard is 15 minuten).
1. Verbind de pool met [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) of [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

In het volgende codefragment wordt een groep met automatische schaal gemaakt in .NET. De autoscale-formule van de groep stelt het doelaantal toegewezen knooppunten in op 5 op maandag en 1 op elke andere dag van de week. Het [automatische schaalinterval](#automatic-scaling-interval) is ingesteld op 30 minuten. In dit en de andere C# `myBatchClient` fragmenten in dit artikel, is een goed geïnitialiseerde instantie van de [BatchClient][net_batchclient] klasse.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Wanneer u een groep met automatische schaal maakt, geeft u de parameter _targetDedicatedNodes_ of de parameter _targetLowPriorityNodes_ niet op in de aanroep naar **CreatePool**. Geef in plaats daarvan de eigenschappen **AutoScaleEnabled** en **AutoScaleFormula** op in de groep. De waarden voor deze eigenschappen bepalen het doelnummer van elk type knooppunt. U ook het formaat van een groep met automatische schaal (bijvoorbeeld met [BatchClient.PoolOperations.ResizePoolAsync)][net_poolops_resizepoolasync]handmatig wijzigen, en vervolgens het formaat **ervan wijzigen.**
>
>

#### <a name="automatic-scaling-interval"></a>Automatisch schaalinterval

Standaard past de batchservice de grootte van een groep aan op basis van de formule voor automatisch schalen om de 15 minuten. Dit interval kan worden geconfigureerd met behulp van de volgende pooleigenschappen:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

Het minimale interval is vijf minuten en het maximum is 168 uur. Als een interval buiten dit bereik is opgegeven, retourneert de batchservice een fout slecht verzoek (400).

> [!NOTE]
> Automatisch schalen is momenteel niet bedoeld om te reageren op wijzigingen in minder dan een minuut, maar is eerder bedoeld om de grootte van uw groep geleidelijk aan te passen wanneer u een werkbelasting uitvoert.
>
>

### <a name="python"></a>Python

Op dezelfde manier u een automatisch uitgeruste pool maken met de Python SDK door:

1. Maak een groep en geef de configuratie op.
1. Voeg de groep toe aan de serviceclient.
1. Schakel automatisch schalen op de pool in met een formule die u schrijft.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18; 
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5; 
             $isWorkingWeekdayHour = $workHours && $isWeekday; 
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10), 
                                            pool_enable_auto_scale_options=None, 
                                            custom_headers=None, raw=False)
```

> [!TIP]
> Meer voorbeelden van het gebruik van de Python SDK zijn te vinden in de [Batch Python Quickstart repository](https://github.com/Azure-Samples/batch-python-quickstart) op GitHub.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Automatisch schalen inschakelen op een bestaande groep

Elke Batch SDK biedt een manier om automatisch schalen in te schakelen. Bijvoorbeeld:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (Batch .NET)
* [Automatische schaling op een groep][rest_enableautoscale] inschakelen (REST API)

Wanneer u automatisch schalen inschakelt op een bestaande groep, moet u rekening houden met de volgende punten:

* Als automatisch schalen momenteel is uitgeschakeld in de groep wanneer u het verzoek uitgeeft om automatisch schalen in te schakelen, moet u een geldige formule voor automatisch schalen opgeven wanneer u de aanvraag uitgeeft. U optioneel een evaluatie-interval voor automatische schaal opgeven. Als u geen interval opgeeft, wordt de standaardwaarde van 15 minuten gebruikt.
* Als autoschaal momenteel is ingeschakeld in de groep, u een formule voor automatische schaal, een evaluatie-interval of beide opgeven. U moet ten minste één van deze eigenschappen opgeven.

  * Als u een nieuw evaluatie-interval voor automatische schaal opgeeft, wordt het bestaande evaluatieschema gestopt en wordt een nieuwe planning gestart. De begintijd van de nieuwe planning is het tijdstip waarop het verzoek om automatisch schalen in te schakelen is uitgegeven.
  * Als u de formule voor automatisch schalen of het evaluatie-interval weglaat, blijft de batchservice de huidige waarde van die instelling gebruiken.

> [!NOTE]
> Als u waarden hebt opgegeven voor de *parameters targetDedicatedNodes* of *targetLowPriorityNodes* van de methode **CreatePool** wanneer u de groep in .NET hebt gemaakt of voor de vergelijkbare parameters in een andere taal, worden deze waarden genegeerd wanneer de automatische schaalformule wordt geëvalueerd.
>
>

Dit C#-codefragment gebruikt de [Batch .NET-bibliotheek][net_api] om automatisch schalen op een bestaande groep in te schakelen:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Een formule voor automatisch schalen bijwerken

Als u de formule wilt bijwerken in een bestaande groep met automatische schaal, roept u de bewerking aan om automatisch schalen opnieuw in te schakelen met de nieuwe formule. Als autoscaling bijvoorbeeld al is `myexistingpool` ingeschakeld wanneer de volgende .NET-code wordt uitgevoerd, wordt `myNewFormula`de formule voor automatische schaal vervangen door de inhoud van .

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Het interval voor automatisch schalen bijwerken

Als u het evaluatie-interval voor automatische schaal van een bestaande groep met automatische schaal wilt bijwerken, roept u de bewerking aan om automatisch schalen opnieuw in te schakelen met het nieuwe interval. Als u bijvoorbeeld het evaluatie-interval voor automatische schaal wilt instellen op 60 minuten voor een groep die al automatisch is ingeschakeld in .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Een formule voor automatisch schalen evalueren

U een formule evalueren voordat u deze toepast op een groep. Op deze manier u de formule testen om te zien hoe de instructies worden geëvalueerd voordat u de formule in productie neemt.

Als u een formule voor automatisch schalen wilt evalueren, moet u eerst automatisch schalen op de groep inschakelen met een geldige formule. Als u een formule wilt testen op een groep die nog niet `$TargetDedicatedNodes = 0` is ingeschakeld voor automatisch schalen, gebruikt u de formule met één regel wanneer u automatisch schalen voor het eerst inschakelt. Gebruik vervolgens een van de volgende opties om de formule te evalueren die u wilt testen:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) of [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Deze Batch .NET-methoden vereisen de id van een bestaande groep en een tekenreeks die de formule voor automatische schaal bevat om te evalueren.

* [Een automatische schaalformule evalueren](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Geef in deze API-aanvraag REST de pool-id in de URI en de formule automatisch schalen op in het element *autoScaleFormula* van de aanvraaginstantie. Het antwoord van de bewerking bevat foutgegevens die mogelijk gerelateerd zijn aan de formule.

In [dit batch.NET-codefragment][net_api] evalueren we een formule voor automatisch schalen. Als het zwembad geen automatisch schalen heeft ingeschakeld, schakelen we deze eerst in.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Een succesvolle evaluatie van de formule in dit codefragment levert resultaten op die vergelijkbaar zijn met:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Informatie over automatisch schalen

Om ervoor te zorgen dat uw formule presteert zoals verwacht, raden we u aan periodiek de resultaten te controleren van de automatische schaling-uitvoeringen die Batch uitvoert op uw groep. Als u dit wilt doen, krijgt u een verwijzing naar de groep (of vernieuwt u) en onderzoekt u de eigenschappen van de laatste automatische schaalrun.

In Batch .NET heeft de eigenschap [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) verschillende eigenschappen die informatie geven over de nieuwste automatische schalinguitgevoerd in de groep:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Resultaten](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoscaleRun.Error AutoScaleRun.Error AutoScaleRun.Error AutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

In de REST-API [retourneert](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) de informatie over een poolaanvraag informatie over de groep, die de meest recente automatische schalingsruninformatie bevat in de eigenschap [autoScaleRun.](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool)

In het volgende C#-codefragment wordt de Batch .NET-bibliotheek gebruikt om informatie af te drukken over de laatste automatisch schalen op groep _myPool:_

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Voorbeelduitvoer van het voorgaande fragment:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Voorbeeldformules voor automatisch schalen

Laten we eens kijken naar een paar formules die verschillende manieren laten zien om de hoeveelheid rekenresources in een groep aan te passen.

### <a name="example-1-time-based-adjustment"></a>Voorbeeld 1: Aanpassing op tijd

Stel dat u de grootte van het zwembad wilt aanpassen op basis van de dag van de week en het tijdstip van de dag. In dit voorbeeld ziet u hoe u het aantal knooppunten in de groep dienovereenkomstig verhogen of verkleinen.

De formule krijgt eerst de huidige tijd. Als het een weekdag (1-5) en binnen de werkuren (8:00 tot 18:00 uur), de doelgroep pool grootte is ingesteld op 20 knooppunten. Anders is het ingesteld op 10 knooppunten.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```
`$curTime`kan worden aangepast aan uw lokale `time()` tijdzone door `TimeZoneInterval_Hour` toe te voegen aan het product van en uw UTC-offset. Gebruik bijvoorbeeld `$curTime = time() + (-6 * TimeInterval_Hour);` voor Mountain Daylight Time (MDT). Houd er rekening mee dat de offset moet worden aangepast aan het begin en einde van de zomertijd (indien van toepassing).

### <a name="example-2-task-based-adjustment"></a>Voorbeeld 2: Aanpassing op basis van taken

In dit voorbeeld wordt de grootte van de groep aangepast op basis van het aantal taken in de wachtrij. Zowel opmerkingen als regeleinden zijn acceptabel in formuletekenreeksen.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Voorbeeld 3: Parallelle taken verantwoorden

In dit voorbeeld wordt de grootte van de groep aangepast op basis van het aantal taken. Deze formule houdt ook rekening met de [MaxTasksPerComputeNode-waarde][net_maxtasks] die is ingesteld voor de groep. Deze aanpak is handig in situaties waarin [parallelle taakuitvoering](batch-parallel-node-tasks.md) is ingeschakeld op uw pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Voorbeeld 4: Een eerste poolgrootte instellen

In dit voorbeeld wordt een C#-codefragment weergegeven met een formule voor automatisch schalen die de grootte van de groep instelt op een opgegeven aantal knooppunten voor een initiële periode. Vervolgens wordt de grootte van de groep aangepast op basis van het aantal actieve en actieve taken nadat de initiële periode is verstreken.

De formule in het volgende codefragment:

* Hiermee stelt u de oorspronkelijke poolgrootte in op vier knooppunten.
* Past de grootte van het zwembad niet aan binnen de eerste 10 minuten van de levenscyclus van het zwembad.
* Na 10 minuten behaalt u de maximale waarde van het aantal lopende en actieve taken in de afgelopen 60 minuten.
  * Als beide waarden 0 zijn (als u aangeeft dat er in de afgelopen 60 minuten geen taken zijn uitgevoerd of actief zijn uitgevoerd), is de grootte van de groep ingesteld op 0.
  * Als een van beide waarden groter is dan nul, wordt er geen wijziging aangebracht.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Volgende stappen

* [Maximaliseer het gebruik van azure batch-gegevensresources met gelijktijdige knooppunttaken](batch-parallel-node-tasks.md) bevat details over hoe u meerdere taken tegelijk uitvoeren op de compute-knooppunten in uw groep. Naast automatisch schalen kan deze functie helpen om de taakduur voor sommige workloads te verlagen, waardoor u geld bespaart.
* Voor een andere efficiëntiebooster moet u ervoor zorgen dat uw Batch-toepassing de Batch-service op de meest optimale manier opvraagt. Zie [De Azure Batch-service efficiënt opvragen](batch-efficient-list-queries.md) om te leren hoe u de hoeveelheid gegevens beperken die de draad overschrijdt wanneer u de status van mogelijk duizenden compute nodes of taken opvraagt.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
