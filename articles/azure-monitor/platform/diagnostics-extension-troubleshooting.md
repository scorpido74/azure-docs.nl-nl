---
title: Azure Diagnostics-extensie oplossen
description: Problemen oplossen bij het gebruik van Azure-diagnoses in Azure Virtual Machines, Service Fabric of Cloud Services.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 043369bd6112c4cac36539bbd764393d889439c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274578"
---
# <a name="azure-diagnostics-troubleshooting"></a>Probleemoplossing met Azure Diagnostics
In dit artikel worden probleemoplossingsgegevens beschreven die relevant is voor het gebruik van Azure Diagnostics. Zie overzicht azure diagnostics voor meer informatie over [Azure-diagnostiek.](diagnostics-extension-overview.md)

## <a name="logical-components"></a>Logische componenten
**Diagnostics Plugin Launcher (DiagnosticsPluginLauncher.exe)**: Hiermee wordt de Azure Diagnostics-extensie gestart. Dient als het ingangspuntproces.

**Diagnostics Plugin (DiagnosticsPlugin.exe)**: Configureert, lanceert en beheert de levensduur van de monitoringagent. Het is het belangrijkste proces dat wordt gelanceerd door de launcher.

**Monitoring Agent (MonAgent\*.exe processen)**: Bewaakt, verzamelt en draagt de diagnostische gegevens over.  

## <a name="logartifact-paths"></a>Logboek-/artefactpaden
Hieronder volgen de paden naar enkele belangrijke logboeken en artefacten. We verwijzen naar deze informatie in de rest van het document.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefact | Pad |
| --- | --- |
| **Configuratiebestand azure diagnostics** | %SystemDrive%\Pakketten\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics-versie\<>\Config.txt |
| **Logboekbestanden** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics-versie\<>\ |
| **Lokale winkel voor diagnostische gegevens** | C:\Resources\Directory\<CloudServiceDeploymentID>. \<RoleName>. DiagnosticStore\WAD0107\Tabellen |
| **Configuratiebestand van de bewakingsagent** | C:\Resources\Directory\<CloudServiceDeploymentID>. \<RoleName>. DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure Diagnostics-uitbreidingspakket** | %SystemDrive%\Pakketten\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versie> |
| **Hulpprogrammapad voor logboekverzamelingen** | %SystemDrive%\Pakketten\GuestAgent\ |
| **MonAgentHost-logboekbestand** | C:\Resources\Directory\<CloudServiceDeploymentID>. \<RoleName>. DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Virtuele machines
| Artefact | Pad |
| --- | --- |
| **Configuratiebestand azure diagnostics** | C:\Pakketten\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics-versie\<>\RuntimeSettings |
| **Logboekbestanden** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Lokale winkel voor diagnostische gegevens** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Configuratiebestand van de bewakingsagent** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Statusbestand** | C:\Pakketten\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics-versie\<>\Status |
| **Azure Diagnostics-uitbreidingspakket** | C:\Pakketten\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Hulpprogrammapad voor logboekverzamelingen** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **MonAgentHost-logboekbestand** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Metrische gegevens worden niet weergegeven in de Azure-portal
Azure Diagnostics biedt metrische gegevens die kunnen worden weergegeven in de Azure-portal. Als u problemen hebt met het zien\* van de gegevens in de portal, controleert u de tabel WADMetrics in het Azure Diagnostics-opslagaccount om te zien of de bijbehorende metrische records aanwezig zijn en controleert u of de [resourceprovider](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) Microsoft.Insights is geregistreerd.

Hier is de **PartitionKey** van de tabel de resource-ID, virtuele machine of virtuele machineschaalset. **RowKey** is de metrische naam (ook wel de naam van het prestatiemeterrecht genoemd).

Als de resource-id onjuist is, schakelt > u **ResourceId** diagnostische**ResourceId** **configuratiestatistieken** > **Metrics**in om te zien of de bron-id correct is ingesteld.

Als er geen gegevens zijn voor de specifieke statistiek, schakelt u **Diagnostics Configuration** > **PerformanceCounter** in om te zien of de statistiek (prestatiemeter) is opgenomen. We schakelen standaard de volgende tellers in:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET Toepassingen( Totaal)\Aanvragen/Sec__Total__
- \ASP.NET Toepassingen(__Totaal)\Totaal__aantal fouten/sec
- \ASP.NET\Aanvragen in de wachtrij
- \ASP.NET\Afgewezen aanvragen
- \Processortijd(w3wp)\%
- \Proces(w3wp)\Privébytes
- \Proces(WaIISHost)\% Processortijd
- \Proces(WaIISHost)\Privébytes
- \Proces(WaWorkerHost)\% processortijd
- \Proces(WaWorkerHost)\Privébytes
- \Geheugen\Paginafouten per seconde
- \.NET CLR_Global_Memory(\% Global ) Tijd in GC
- \LogicalDisk(C:)\Bytes voor schijfschrijfschrijfberichten per seconde
- \LogicalDisk(C:)\Bytes voor schijflezen per seconde
- \LogicalDisk(D:)\Bytes voor schijfschrijfschrijfberichten per seconde
- \LogicalDisk(D:)\Bytes voor schijflezen per seconde

Als de configuratie correct is ingesteld, maar u de metrische gegevens nog steeds niet zien, gebruikt u de volgende richtlijnen om problemen op te lossen.


## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics wordt niet gestart
Zie de **diagnosticsPluginLauncher.log-** en **DiagnosticsPlugin.log-bestanden** in de eerder opgegeven logbestanden voor informatie over waarom Azure Diagnostics niet is gestart.

Als deze `Monitoring Agent not reporting success after launch`logboeken aangeven, betekent dit dat er een storing is geweest bij de lancering van MonAgentHost.exe. Bekijk de logboeken op de locatie `MonAgentHost log file` die in de vorige sectie is aangegeven.

De laatste regel van de logbestanden bevat de exitcode.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Als u een **negatieve** exitcode vindt, raadpleegt u de [tabel met de exitcode](#azure-diagnostics-plugin-exit-codes) in de [sectie Verwijzingen](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnostische gegevens worden niet geregistreerd bij Azure Storage
Bepaal of geen van de gegevens wordt weergegeven of dat sommige gegevens worden weergegeven.

### <a name="diagnostics-infrastructure-logs"></a>Logboeken van diagnostische infrastructuur
Diagnostische gegevens registreert alle fouten in de logboeken van de diagnostische infrastructuur. Zorg ervoor dat u de [logboeken van](#how-to-check-diagnostics-extension-configuration)de diagnostische infrastructuur in uw configuratie hebt ingeschakeld. Vervolgens u snel zoeken naar relevante `DiagnosticInfrastructureLogsTable` fouten die worden weergegeven in de tabel in uw geconfigureerde opslagaccount.

### <a name="no-data-is-appearing"></a>Er worden geen gegevens weergegeven
De meest voorkomende reden dat gebeurtenisgegevens helemaal niet worden weergegeven, is dat de opslagaccountgegevens onjuist zijn gedefinieerd.

Oplossing: Corrigeer uw diagnoseconfiguratie en installeer Diagnostics opnieuw.

Als het opslagaccount correct is geconfigureerd, controleert u op afstand toegang tot de machine en controleert u of *DiagnosticsPlugin.exe* en *MonAgentCore.exe* worden uitgevoerd. Als ze niet worden uitgevoerd, voert u de stappen in [Azure Diagnostics niet start.](#azure-diagnostics-is-not-starting)

Als de processen worden uitgevoerd, ga dan naar [Wordt gegevens lokaal vastgelegd?](#is-data-getting-captured-locally)

Als dit het probleem niet oplost, probeert u:

1. Agent verwijderen
2. Map C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics verwijderen
3. Opnieuw installatieagent


### <a name="part-of-the-data-is-missing"></a>Een deel van de gegevens ontbreekt
Als u bepaalde gegevens krijgt, maar niet alle, betekent dit dat de pijplijn voor het verzamelen/overdragen van gegevens correct is ingesteld. Volg de onderafdelingen hier om het probleem te beperken.

#### <a name="is-the-collection-configured"></a>Is de verzameling geconfigureerd?
De configuratie Diagnostische diagnose bevat instructies voor een bepaald type gegevens dat moet worden verzameld. [Controleer uw configuratie](#how-to-check-diagnostics-extension-configuration) om te controleren of u alleen op zoek bent naar gegevens die u voor de verzameling hebt geconfigureerd.

#### <a name="is-the-host-generating-data"></a>Genereert de host gegevens?
- **Prestatietellers**: Open perfmon en controleer de teller.

- **Trace logs:** Remote access into the VM and add a TextWriterTraceListener to the app's config file.  Zie https://msdn.microsoft.com/library/sk36c28t.aspx om de tekstlistener in te stellen.  Zorg ervoor `<trace>` dat `<trace autoflush="true">`het element .<br />
Zie Meer informatie over ontbrekende traceringslogboeken als u geen traceerlogboeken ziet.

- **ETW traces**: Remote access into the VM and install PerfView.  Voer in PerfView Het**beluisteren** > van het opdracht **Bestandsgebruiker** > **luisteren etwprovder1** > **etwprovider2**uit, enzovoort. De opdracht **Luisteren** is hoofdlettergevoelig en er kunnen geen spaties zijn tussen de door komma's gescheiden lijst van ETW-providers. Als de opdracht niet wordt uitgevoerd, u de knop **Logboek** rechtsonder in het gereedschap Perfview selecteren om te zien wat er is geprobeerd uit te voeren en wat het resultaat was.  Ervan uitgaande dat de invoer juist is, verschijnt er een nieuw venster. In een paar seconden, begin je etw sporen te zien.

- **Gebeurtenislogboeken:** externe toegang tot de VM. Open `Event Viewer`en zorg ervoor dat de gebeurtenissen bestaan.

#### <a name="is-data-getting-captured-locally"></a>Worden gegevens lokaal vastgelegd?
Zorg er vervolgens voor dat de gegevens lokaal worden vastgelegd.
De gegevens worden lokaal `*.tsf` opgeslagen in bestanden in de lokale winkel voor diagnostische gegevens. Verschillende soorten logs worden `.tsf` verzameld in verschillende bestanden. De namen zijn vergelijkbaar met de tabelnamen in Azure Storage.

Bijvoorbeeld, `Performance Counters` krijgen verzameld `PerformanceCountersTable.tsf`in . Gebeurtenislogboeken worden `WindowsEventLogsTable.tsf`verzameld in . Gebruik de instructies in de sectie [Lokale logboekextractie](#local-log-extraction) om de lokale verzamelingsbestanden te openen en te controleren of u ziet dat ze op schijf worden verzameld.

Als u niet ziet dat logboeken lokaal worden verzameld en u al hebt geverifieerd dat de host gegevens genereert, hebt u waarschijnlijk een configuratieprobleem. Bekijk uw configuratie zorgvuldig.

Bekijk ook de configuratie die is gegenereerd voor MonitoringAgent MaConfig.xml. Controleer of er een sectie is die de relevante logboekbron beschrijft. Controleer vervolgens of het niet verloren gaat in de vertaling tussen de configuratie diagnostische gegevens en de configuratie van de bewakingsagent.

#### <a name="is-data-getting-transferred"></a>Worden gegevens overgedragen?
Als u hebt geverifieerd dat de gegevens lokaal worden vastgelegd, maar u deze nog steeds niet ziet in uw opslagaccount, voert u de volgende stappen uit:

- Controleer of u een correct opslagaccount hebt opgegeven en of u geen sleutels voor het opgegeven opslagaccount hebt overgerold. Voor Azure Cloud Services zien we soms `useDevelopmentStorage=true`dat mensen niet updaten.

- Controleer of het meegeleverde opslagaccount juist is. Zorg ervoor dat u geen netwerkbeperkingen hebt die voorkomen dat de onderdelen eindpunten voor openbare opslag bereiken. Een manier om dat te doen is om op afstand toegang tot de machine, en dan proberen om iets te schrijven naar dezelfde opslag account zelf.

- Ten slotte u kijken naar welke storingen worden gemeld door de monitoring agent. De monitoring agent schrijft `maeventtable.tsf`zijn logs in , die is gevestigd in de lokale winkel voor diagnostische gegevens. Volg de instructies in de sectie [Lokale logboekextractie](#local-log-extraction) voor het openen van dit bestand. Probeer vervolgens te bepalen `errors` of er zijn dat fouten lezen aan lokale bestanden schrijven naar opslag.

### <a name="capturing-and-archiving-logs"></a>Logboeken vastleggen en archiveren
Als u denkt over het contact ermet ondersteuning, het eerste wat ze je zou kunnen vragen is om logs te verzamelen van uw machine. U tijd besparen door dat zelf te doen. Voer `CollectGuestLogs.exe` het hulpprogramma uit op het hulpprogrammapad logboekverzameling. Het genereert een .zip-bestand met alle relevante Azure-logboeken in dezelfde map.

## <a name="diagnostics-data-tables-not-found"></a>Diagnostische gegevenstabellen niet gevonden
De tabellen in Azure-opslag met ETW-gebeurtenissen worden benoemd met behulp van de volgende code:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Hier volgt een voorbeeld:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Deze code genereert vier tabellen:

| Gebeurtenis | Tabelnaam |
| --- | --- |
| provider="prov1" &lt;Event id="1" /&gt; |WADEvent+MD5("prov1")+"1" |
| provider="prov1" &lt;Event id="2" eventDestination="dest1" /&gt; |WADdest1 WADdest1 |
| provider="prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| provider="prov2" &lt;DefaultEvents eventDestination="dest2" /&gt; |WADdest2 WADdest2 |

## <a name="references"></a>Verwijzingen

### <a name="how-to-check-diagnostics-extension-configuration"></a>De configuratie van diagnostische extensie controleren
De eenvoudigste manier om uw extensieconfiguratie te controleren, is door naar [Azure Resource Explorer](https://resources.azure.com)te gaan en vervolgens naar de virtuele machine- of cloudservice te gaan waar de Azure Diagnostics-extensie (IaaSDiagnostics / PaaDiagnostics) zich bevindt.

U ook extern bureaublad in de machine plaatsen en kijken naar het azure diagnostics-configuratiebestand dat wordt beschreven in de sectie Pad van Logboekartefacten.

Zoek in beide gevallen naar **Microsoft.Azure.Diagnostics**en vervolgens naar het veld **xmlCfg** of **WadCfg.**

Als u zoekt op een virtuele machine en het **WadCfg-veld** aanwezig is, betekent dit dat de config in JSON-formaat is. Als het **xmlCfg-veld** aanwezig is, betekent dit dat de config zich in XML bevindt en is gebasis64 gecodeerd. U moet [deze decoderen](https://www.bing.com/search?q=base64+decoder) om de XML te zien die door Diagnostics is geladen.

Voor de rol cloudservice, als u de configuratie van schijf kiest, zijn de gegevens base64-gecodeerd, dus u moet [deze decoderen](https://www.bing.com/search?q=base64+decoder) om de XML te zien die door Diagnostics is geladen.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Exitcodes voor Azure Diagnostics-plug-ins
De plug-in retourneert de volgende exitcodes:

| Afsluitcode | Beschrijving |
| --- | --- |
| 0 |Geslaagd. |
| -1 |Algemene fout. |
| -2 |Kan het rcf-bestand niet laden.<p>Deze interne fout mag alleen optreden als de gastagent-plug-outlauncher handmatig onjuist wordt aangeroepen op de VM. |
| -3 |Het configuratiebestand Diagnostische gegevens kan niet worden geladen.<p><p>Oplossing: Veroorzaakt door een configuratiebestand dat de schemavalidatie niet doorstaat. De oplossing is om een configuratiebestand te bieden dat voldoet aan het schema. |
| -4 |Een ander exemplaar van de bewakingsagent Diagnostics maakt al gebruik van de lokale bronmap.<p><p>Oplossing: Geef een andere waarde op voor **LocalResourceDirectory**. |
| -6 |De plug-inlauncher van de gastagent heeft geprobeerd Diagnostics te starten met een ongeldige opdrachtregel.<p><p>Deze interne fout mag alleen optreden als de gastagent-plug-outlauncher handmatig onjuist wordt aangeroepen op de VM. |
| -10 |De Diagnostics-plug-in is afgesloten met een niet-afgehandelde uitzondering. |
| -11 |De gastagent was niet in staat om het proces te maken dat verantwoordelijk is voor het starten en bewaken van de bewakingsagent.<p><p>Oplossing: Controleer of er voldoende systeembronnen beschikbaar zijn om nieuwe processen te starten.<p> |
| -101 |Ongeldige argumenten bij het aanroepen van de aanwijsplugining.<p><p>Deze interne fout mag alleen optreden als de gastagent-plug-outlauncher handmatig onjuist wordt aangeroepen op de VM. |
| -102 |Het pluginproces kan zichzelf niet initialiseren.<p><p>Oplossing: Controleer of er voldoende systeembronnen beschikbaar zijn om nieuwe processen te starten. |
| -103 |Het pluginproces kan zichzelf niet initialiseren. In het bijzonder is het niet in staat om het loggerobject te maken.<p><p>Oplossing: Controleer of er voldoende systeembronnen beschikbaar zijn om nieuwe processen te starten. |
| -104 |Kan het rcf-bestand van de gastagent niet laden.<p><p>Deze interne fout mag alleen optreden als de gastagent-plug-outlauncher handmatig onjuist wordt aangeroepen op de VM. |
| -105 |De plug-in Diagnostische gegevens kan het configuratiebestand Diagnostische diagnose niet openen.<p><p>Deze interne fout mag alleen optreden als de diagnostische plug-in handmatig onjuist wordt aangeroepen op de VM. |
| -106 |Kan het configuratiebestand Diagnostische gegevens niet lezen.<p><p>Veroorzaakt door een configuratiebestand dat de schemavalidatie niet doorstaat. <br><br>Oplossing: Geef een configuratiebestand op dat voldoet aan het schema. Zie [Configuratie van diagnostische extensie controleren](#how-to-check-diagnostics-extension-configuration)voor meer informatie. |
| -107 |De dooru-de-plaats van resourcedirectory naar de bewakingsagent is ongeldig.<p><p>Deze interne fout mag alleen optreden als de bewakingsagent handmatig onjuist wordt aangeroepen op de VM.</p> |
| -108 |Kan het configuratiebestand Diagnostics niet converteren naar het configuratiebestand van de bewakingsagent.<p><p>Deze interne fout mag alleen optreden als de diagnostische plug-in handmatig wordt aangeroepen met een ongeldig configuratiebestand. |
| -110 |Configuratiefout algemene diagnostiek.<p><p>Deze interne fout mag alleen optreden als de diagnostische plug-in handmatig wordt aangeroepen met een ongeldig configuratiebestand. |
| -111 |Kan de bewakingsagent niet starten.<p><p>Oplossing: Controleer of er voldoende systeembronnen beschikbaar zijn. |
| -112 |Algemene fout |

### <a name="local-log-extraction"></a>Lokale logboekextractie
De bewakingsagent verzamelt logboeken `.tsf` en artefacten als bestanden. Het `.tsf` bestand is niet leesbaar, maar `.csv` u het omzetten in een als volgt:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Er wordt `<relevantLogFile>.csv` een nieuw bestand aangeroepen `.tsf` op hetzelfde pad als het bijbehorende bestand.

>[!NOTE]
> U hoeft dit hulpprogramma alleen tegen het hoofdbestand .tsf uit te voeren (bijvoorbeeld PerformanceCountersTable.tsf). De bijbehorende bestanden (bijvoorbeeld\*\*PerformanceCountersTables_ 001.tsf, PerformanceCountersTables_\*\*002.tsf, enzovoort) worden automatisch verwerkt.

### <a name="more-about-missing-trace-logs"></a>Meer informatie over ontbrekende tracelogs

>[!NOTE]
> De volgende informatie is meestal van toepassing op Azure Cloud Services, tenzij u de DiagnosticsMonitorTraceListener hebt geconfigureerd op een toepassing die wordt uitgevoerd op uw IaaS VM.

- Controleer of de **DiagnosticMonitorTraceListener** is geconfigureerd in web.config of app.config.  Dit is standaard geconfigureerd in cloudserviceprojecten. Echter, sommige klanten commentaar uit, waardoor de sporenverklaringen niet worden verzameld door diagnostiek.

- Als logboeken niet worden geschreven vanuit de methode **OnStart** of **Uitvoeren,** controleert u of de **DiagnosticMonitorTraceListener** zich in app.config bevindt.  Standaard is het in de web.config, maar dat geldt alleen voor code die wordt uitgevoerd binnen w3wp.exe. Dus je hebt het nodig in app.config om sporen vast te leggen die worden uitgevoerd in WaIISHost.exe.

- Zorg ervoor dat u **Diagnostics.Trace.TraceXXX** gebruikt in plaats van **Diagnostics.Debug.WriteXXX.** De foutopsporingsinstructies worden verwijderd uit een releasebuild.

- Zorg ervoor dat de gecompileerde code daadwerkelijk de **Diagnostics.Trace-regels** heeft (gebruik Reflector, ildasm of ILSpy om te verifiëren). **Diagnostics.Trace-opdrachten** worden verwijderd uit de gecompileerde binaire, tenzij u het voorwaardelijke compilatiesymbool TRACE gebruikt. Dit is een veelvoorkomend probleem dat optreedt wanneer u msbuild gebruikt om een project te bouwen.   

## <a name="known-issues-and-mitigations"></a>Bekende problemen en oplossingen
Hier is een lijst van bekende problemen met bekende oplossingen:

**1. .NET 4.5 afhankelijkheid**

Windows Azure Diagnostics Extension heeft een runtime afhankelijkheid van .NET 4.5 framework of hoger. Op het moment van schrijven hebben alle machines die zijn ingericht voor Azure Cloud Services, evenals alle officiële afbeeldingen die zijn gebaseerd op virtuele Azure-machines, .NET 4.5 of hoger geïnstalleerd.

Het is nog steeds mogelijk om een situatie tegen te komen waarin u Windows Azure Diagnostics Extension probeert uit te voeren op een machine die geen .NET 4.5 of hoger heeft. Dit gebeurt wanneer u uw machine maakt op basis van een oude afbeelding of momentopname of wanneer u uw eigen aangepaste schijf meeneemt.

Dit manifesteert zich over het algemeen als een exit code **255** bij het uitvoeren van **DiagnosticsPluginLauncher.exe.** Fout gebeurt als gevolg van de volgende niet-behandelde uitzondering:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Mitigatie:** Installeer .NET 4.5 of hoger op uw machine.

**2. Prestatiemetergegevens zijn beschikbaar in de opslag, maar worden niet weergegeven in de portal**

De portalervaring in de virtuele machines toont standaard bepaalde prestatiemeteritems. Als u de prestatiemeteritems niet ziet en u weet dat de gegevens worden gegenereerd omdat deze beschikbaar zijn in de opslag, controleert u het volgende:

- Of de gegevens in opslag tellernamen in het Engels hebben. Als de tellernamen niet in het Engels zijn, kan de portalmetrische grafiek deze niet herkennen. **Mitigatie:** Wijzig de taal van de machine in het Engels voor systeemaccounts. Selecteer hiervoor de instellingen voor**beheerkopie-instellingen****Administrative** > van **het Configuratieschermgebied** > **Region** > . Schakel vervolgens **het welkomstscherm en systeemaccounts** uit, zodat de aangepaste taal niet wordt toegepast op het systeemaccount.

- Als u wildcards\*( ) gebruikt in de namen van uw prestatiemeter, kan de portal de geconfigureerde en verzamelde teller niet correleren wanneer de prestatiemeteritems naar de azure-opslagsink worden verzonden. **Mitigatie:** om ervoor te zorgen dat u jokertekens\*gebruiken en de portal de ( ) laten uitvouwen, u uw prestatiemeteritems naar de Azure Monitor-sink leiden.

