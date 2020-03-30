---
title: Prestatiemeteritems verzamelen en analyseren in Azure Monitor | Microsoft Documenten
description: Prestatiemeteritems worden verzameld door Azure Monitor om de prestaties op Windows- en Linux-agents te analyseren.  In dit artikel wordt beschreven hoe u verzameling prestatiemeteritems configureert voor zowel Windows- als Linux-agents, details van deze items die in de werkruimte zijn opgeslagen en hoe u deze analyseren in de Azure-portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: d1a972a1d89066b961f2dcc28fba830e3a04ebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274760"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Windows- en Linux-prestatiegegevensbronnen in Azure Monitor
Prestatiemeteritems in Windows en Linux geven inzicht in de prestaties van hardwarecomponenten, besturingssystemen en toepassingen.  Azure Monitor kan prestatiemeteritems op frequente tijdstippen verzamelen voor BIJNA Real Time -analyse (NRT) naast het aggregeren van prestatiegegevens voor analyse en rapportage op langere termijn.

![Prestatiemeteritems](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Prestatiemeteritems configureren
Prestatiemeteritems configureren in het [menu Gegevens in Geavanceerde instellingen](agent-data-sources.md#configuring-data-sources).

Wanneer u windows- of Linux-prestatiemeteritems voor het eerst configureert voor een nieuwe werkruimte, krijgt u de optie om snel een aantal algemene tellers te maken.  Ze worden weergegeven met een selectievakje ernaast.  Controleer of alle tellers die u in eerste instantie wilt maken, zijn aangevinkt en klik vervolgens op **De geselecteerde prestatiemeteritems toevoegen**.

Voor Windows-prestatiemeteritems u voor elke prestatiemeter een specifiek exemplaar kiezen. Voor Linux-prestatiemeteritems is de instantie van elke teller die u kiest van toepassing op alle onderliggende tellers van de bovenliggende teller. In de volgende tabel worden de algemene exemplaren weergegeven die beschikbaar zijn voor zowel De prestatiemetervan Linux als voor Windows.

| Exemplaarnaam | Beschrijving |
| --- | --- |
| \_Totaal |Totaal van alle exemplaren |
| \* |Alle instanties |
| (/&#124;/var) |Komt overeen met instanties met de naam: / of /var |

### <a name="windows-performance-counters"></a>Windows-prestatiemeteritems

![Windows Performance-tellers configureren](media/data-sources-performance-counters/configure-windows.png)

Volg deze procedure om een nieuw Windows-prestatiemeterwerk toe te voegen om te verzamelen.

1. Typ de naam van de teller in het tekstvak in het *opmaakobject(instantie)\teller*.  Wanneer u begint met typen, krijgt u een overeenkomende lijst met algemene tellers te zien.  U een teller selecteren in de lijst of een van uw eigen opties intypen.  U ook alle instanties voor een bepaalde teller retourneren door *object\teller*op te geven.  

    Bij het verzamelen van SQL Server-prestatiemeteritems van benoemde instanties beginnen alle benoemde instantietellers met *MSSQL$* en gevolgd door de naam van de instantie.  Als u bijvoorbeeld de teller voor de hitratio van logboekcache wilt verzamelen voor `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`alle databases van het prestatieobject Database voor de naam SQL-instantie INST2, geeft u op .

2. Klik **+** of druk op **Enter** om de teller aan de lijst toe te voegen.
3. Wanneer u een teller toevoegt, wordt de standaardwaarde van 10 seconden gebruikt voor het **voorbeeldinterval**.  U dit wijzigen in een hogere waarde van maximaal 1800 seconden (30 minuten) als u de opslagvereisten van de verzamelde prestatiegegevens wilt verminderen.
4. Wanneer u klaar bent met het toevoegen van tellers, klikt u op de knop **Opslaan** boven aan het scherm om de configuratie op te slaan.

### <a name="linux-performance-counters"></a>Linux prestatiemeteritems

![Linux-prestatiemeteritems configureren](media/data-sources-performance-counters/configure-linux.png)

Volg deze procedure om een nieuwe Linux performance teller toe te voegen om te verzamelen.

1. Standaard worden alle configuratiewijzigingen automatisch naar alle agents gepusht.  Voor Linux-agents wordt een configuratiebestand verzonden naar de Fluentd-gegevensverzamelaar.  Als u dit bestand handmatig wilt wijzigen op elke Linux-agent, schakel dan het selectievakje *Onderstaande configuratie toepassen op mijn Linux-machines* uit en volg de onderstaande richtlijnen.
2. Typ de naam van de teller in het tekstvak in het *opmaakobject(instantie)\teller*.  Wanneer u begint met typen, krijgt u een overeenkomende lijst met algemene tellers te zien.  U een teller selecteren in de lijst of een van uw eigen opties intypen.  
3. Klik **+** of druk op **Enter** om de teller toe te voegen aan de lijst met andere tellers voor het object.
4. Alle tellers voor een object gebruiken hetzelfde **voorbeeldinterval**.  De standaardinstelling is 10 seconden.  U wijzigt dit in een hogere waarde van maximaal 1800 seconden (30 minuten) als u de opslagvereisten van de verzamelde prestatiegegevens wilt verminderen.
5. Wanneer u klaar bent met het toevoegen van tellers, klikt u op de knop **Opslaan** boven aan het scherm om de configuratie op te slaan.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Linux-prestatiemeteritems configureren in configuratiebestand
In plaats van Linux-prestatiemeteritems te configureren met behulp van de Azure-portal, hebt u de mogelijkheid om configuratiebestanden op de Linux-agent te bewerken.  Prestatiestatistieken die moeten worden verzameld, worden gecontroleerd door de configuratie in **/etc/opt/microsoft/omsagent/\<workspace id\>/conf/omsagent.conf**.

Elk object of elke categorie van prestatiestatistieken die moeten worden verzameld, moet in het configuratiebestand worden gedefinieerd als één `<source>` element. De syntaxis volgt het onderstaande patroon.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


De parameters in dit element worden beschreven in de volgende tabel.

| Parameters | Beschrijving |
|:--|:--|
| objectnaam\_ | Objectnaam voor de verzameling. |
| instantie\_regex |  Een *reguliere expressie* die bepaalt welke instanties moeten worden verzameld. De waarde: `.*` geeft alle instanties op. Als u processorstatistieken \_wilt verzamelen voor `_Total`alleen de instantie Totaal, u opgeven. Als u processtatistieken wilt verzamelen voor alleen de crond- of sshd-exemplaren, u opgeven: `(crond\|sshd)`. |
| \_tellernaam\_regex | Een *reguliere expressie* die bepaalt welke tellers (voor het object) moeten worden verzameld. Geef op als u alle `.*`tellers voor het object wilt verzamelen: . Als u bijvoorbeeld alleen wisselruimtetellers voor het geheugenobject wilt verzamelen, u het volgende opgeven:`.+Swap.+` |
| interval | Frequentie waarmee de tellers van het object worden verzameld. |


In de volgende tabel worden de objecten en tellers weergegeven die u in het configuratiebestand opgeven.  Er zijn extra tellers beschikbaar voor bepaalde toepassingen zoals beschreven in [Prestatiemeteritems verzamelen voor Linux-toepassingen in Azure Monitor.](data-sources-linux-applications.md)

| Objectnaam | Tellernaam |
|:--|:--|
| Logische schijf | % Gratis Inodes |
| Logische schijf | % vrije ruimte |
| Logische schijf | % gebruikte inodes |
| Logische schijf | % gebruikte ruimte |
| Logische schijf | Bytes voor schijflezen per seconde |
| Logische schijf | Schijfleest/sec |
| Logische schijf | Schijfoverdrachten per seconde |
| Logische schijf | Bytes voor schijfschrijfschrijfberichten per seconde |
| Logische schijf | Schijfschrijft/sec |
| Logische schijf | Gratis Megabytes |
| Logische schijf | Logische schijfbytes per seconde |
| Geheugen | % beschikbaar geheugen |
| Geheugen | % beschikbare ruilruimte |
| Geheugen | % gebruikt geheugen |
| Geheugen | % gebruikte swapruimte |
| Geheugen | Beschikbaar MBytes-geheugen |
| Geheugen | Beschikbare MBytes-swap |
| Geheugen | Pagina leest/sec |
| Geheugen | Paginaschrijft/sec |
| Geheugen | Pagina's/sec |
| Geheugen | Gebruikte MBytes-wisselruimte |
| Geheugen | Bytes voor gebruikt geheugen |
| Netwerk | Totaal aantal verzonden bytes |
| Netwerk | Totaal aantal ontvangen bytes |
| Netwerk | Totaal aantal bytes |
| Netwerk | Totaal verzonden pakketten |
| Netwerk | Totaal ontvangen pakketten |
| Netwerk | Totaal aantal rx-fouten |
| Netwerk | Totaal aantal Tx-fouten |
| Netwerk | Totale botsingen |
| Fysieke schijf | Avg. Schijf sec/read |
| Fysieke schijf | Avg. Schijfsec/Overdracht |
| Fysieke schijf | Avg. Schijf sec/write |
| Fysieke schijf | Bytes van fysieke schijf per seconde |
| Proces | Pct bevoorrechte tijd |
| Proces | Pct-gebruikerstijd |
| Proces | KBytes voor gebruikt geheugen |
| Proces | Virtueel gedeeld geheugen |
| Processor | % DPC-tijd |
| Processor | Percentage niet-actieve tijd |
| Processor | % Onderbrekingstijd |
| Processor | % IO wachttijd |
| Processor | % Mooie tijd |
| Processor | % bevoorrechte tijd |
| Processor | Percentage processortijd |
| Processor | % gebruikerstijd |
| Systeem | Gratis fysiek geheugen |
| Systeem | Vrije ruimte in Paging-bestanden |
| Systeem | Gratis virtueel geheugen |
| Systeem | Processen |
| Systeem | Grootte opgeslagen in paging-bestanden |
| Systeem | Uptime |
| Systeem | Gebruikers |


Hieronder volgt de standaardconfiguratie voor prestatiestatistieken.

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>Gegevensverzameling
Azure Monitor verzamelt alle opgegeven prestatiemeteritems met het opgegeven monsterinterval voor alle agents die die teller hebben geïnstalleerd.  De gegevens worden niet samengevoegd en de onbewerkte gegevens zijn beschikbaar in alle logboekqueryweergaven voor de duur die door uw abonnement is opgegeven.

## <a name="performance-record-properties"></a>Eigenschappen van prestatierecord
Prestatierecords hebben een type **Perf** en hebben de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Computer |Computer waarvan de gebeurtenis is verzameld. |
| CounterNaam |Naam van het prestatiemeterrecht |
| Contrapad |Volledig pad van de \\ \\ \<teller \\in de\\>object(instance) teller. |
| Tegenwaarde |Numerieke waarde van de teller. |
| Instancename |Naam van de gebeurtenisinstantie.  Leeg als er geen instantie is. |
| ObjectName |Naam van het prestatieobject |
| SourceSystem |Type agent waarvan de gegevens zijn verzameld. <br><br>OpsManager – Windows-agent, direct connect of SCOM <br> Linux – Alle Linux-agents  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Datum en tijd van de gegevens is bemonsterd. |

## <a name="sizing-estimates"></a>Grootteschattingen
 Een ruwe schatting voor het verzamelen van een bepaalde teller met intervallen van 10 seconden is ongeveer 1 MB per dag per exemplaar.  U de opslagvereisten van een bepaalde teller schatten met de volgende formule.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Query's registreren met prestatierecords
In de volgende tabel vindt u verschillende voorbeelden van logboekquery's waarmee prestatierecords worden opgehaald.

| Query’s uitvoeren | Beschrijving |
|:--- |:--- |
| Prestaties |Alle prestatiegegevens |
| Perf &#124; waar Computer == "MyComputer" |Alle prestatiegegevens van een bepaalde computer |
| Perf &#124; waar CounterName == "Huidige schijfwachtrijlengte" |Alle prestatiegegevens voor een bepaalde teller |
| Perf &#124; waar ObjectName == "Processor" en CounterName == "% Processortijd" en InstanceName == "_Total" &#124; AVGCPU = avg(CounterValue) per computer samen te vatten |Gemiddeld CPU-gebruik op alle computers |
| Perf &#124; waar CounterName == "% Processortijd" &#124; samenvatten AggregatedValue = max(CounterValue) by Computer |Maximaal CPU-gebruik op alle computers |
| Perf &#124; waar ObjectName == "LogicalDisk" en CounterName == "Current Disk Queue Length" en Computer == "MyComputerName" &#124; aggregatedValue = avg(CounterValue) by InstanceName samenvatten |Gemiddelde huidige schijfwachtrijlengte over alle exemplaren van een bepaalde computer |
| Perf &#124; waar CounterName == "Schijfoverdrachten/sec" &#124; geaggregeerde waarde samenvatten = percentiel (tegenwaarde, 95) per computer |95e percentiel van schijfoverdrachten per seconde op alle computers |
| Perf &#124; waar CounterName == "% Processortijd" en InstanceName == "_Total" &#124; samen te vatten AggregatedValue = avg (CounterValue) per bin (TimeGenerated, 1h), Computer |Uurgemiddelde van CPU-gebruik op alle computers |
| Perf &#124; waar Computer == "MyComputer" en CounterName startswith_cs "%" en InstanceName == "_Total" &#124; geaggregeerde waarde samenvatten = percentiel (Tegenwaarde, 70) per opslaglocatie (TimeGenerated, 1h), CounterName | Per uur 70 percentiel van elke % teller voor een bepaalde computer |
| Perf &#124; waar CounterName == "% Processortijd" en InstanceName == "_Total" en Computer == "MyComputer" &#124; samenvatten ["min(CounterValue)"] = min(CounterValue), [avg(CounterValue)"] = avg(CounterValue), ["percentiel75(CounterValue)"] = percentiel (Tegenwaarde, 75), ["max(CounterValue)"] = max(CounterValue) per opslaglocatie (TimeGenerated, 1h), Computer |Uurgemiddelde, minimum, maximum en 75-percentiel CPU-gebruik voor een specifieke computer |
| Perf &#124; waar ObjectName == "MSSQL$INST2:Databases" en InstanceName == "master" | Alle prestatiegegevens van het prestatieobject Database voor de hoofddatabase van de benoemde SQL Server-instantie INST2.  




## <a name="next-steps"></a>Volgende stappen
* [Verzamel prestatiemeteritems van Linux-toepassingen,](data-sources-linux-applications.md) waaronder MySQL en Apache HTTP Server.
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) om de gegevens te analyseren die zijn verzameld uit gegevensbronnen en -oplossingen.  
* Exporteer verzamelde gegevens naar [Power BI](powerbi.md) voor extra visualisaties en analyses.
