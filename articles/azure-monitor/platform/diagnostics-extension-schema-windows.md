---
title: Windows-schema voor diagnostische extensie
description: Configuratieschemareferentie voor Windows diagnostics extension (WAD) in Azure Monitor.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: 4c711e1b0a63fbcf978c0e4467eadaed8d91f3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274708"
---
# <a name="windows-diagnostics-extension-schema"></a>Windows-schema voor diagnostische extensie
Azure Diagnostics-extensie is een agent in Azure Monitor die bewakingsgegevens verzamelt van het gastbesturingssysteem en workloads van Azure-rekenbronnen. In dit artikel wordt het schema beschreven dat wordt gebruikt voor de configuratie van de diagnostische extensie op virtuele Windows-machines en andere rekenbronnen.

> [!NOTE]
> Het schema in dit artikel is geldig voor versies 1.3 en nieuwer (Azure SDK 2.4 en nieuwer). Nieuwere configuratiesecties worden becommentarieerd om te laten zien in welke versie ze zijn toegevoegd. Versie 1.0 en 1.2 van het schema zijn gearchiveerd en niet meer beschikbaar. 

## <a name="public-configuration-file-schema"></a>Bestandsschema voor openbare configuratie

Download de definitie van het openbare configuratiebestandsschema door de volgende PowerShell-opdracht uit te voeren:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>Algemene kenmerktypen  
 het kenmerk **scheduledTransferPeriod** wordt in verschillende elementen weergegeven. Het is het interval tussen geplande overdrachten naar opslag afgerond op de dichtstbijzijnde minuut. De waarde is een [XML -type 'Duurgegevens'.](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>Configuratie-element diagnostische configuratie  
 *Boom: Root - DiagnosticsConfiguration*

Toegevoegd in versie 1.3.  

Het element op het hoogste niveau van het configuratiebestand voor diagnostiek.  

**Xmlns voor kenmerk** - De XML-naamruimte voor het configuratiebestand voor diagnose is:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**PublicConfig**|Vereist. Zie beschrijving elders op deze pagina.|  
|**PrivateConfig PrivateConfig**|Optioneel. Zie beschrijving elders op deze pagina.|  
|**IsEnabled**|Booleaanse. Zie beschrijving elders op deze pagina.|  

## <a name="publicconfig-element"></a>PublicConfig-element  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig*

 Beschrijft de configuratie van openbare diagnostiek.  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**WadCfg WadCfg**|Vereist. Zie beschrijving elders op deze pagina.|  
|**StorageAccount**|De naam van het Azure Storage-account om de gegevens op te slaan. Kan ook als parameter worden opgegeven bij het uitvoeren van de cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Kan *tabel,* *blob*of *tableandblob*zijn. Tabel is standaard. Wanneer TableAndBlob wordt gekozen, worden diagnostische gegevens twee keer geschreven - één keer naar elk type.|  
|**LocalResourceDirectory (LocalResourceDirectory)**|De map op de virtuele machine waar de monitoringagent gebeurtenisgegevens opslaat. Als dit niet het zo is, wordt de standaardmap gebruikt:<br /><br /> Voor een werknemer/webrol:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Voor een virtuele machine:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Vereiste kenmerken zijn:<br /><br /> - **pad** - De map op het systeem dat door Azure Diagnostics moet worden gebruikt.<br /><br /> - **expandEnvironment** - Hiermee bepaalt u of omgevingsvariabelen worden uitgebreid in de padnaam.|  

## <a name="wadcfg-element"></a>WadCFG-element  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG*

 Identificeert en configureert de te verzamelen telemetriegegevens.  


## <a name="diagnosticmonitorconfiguration-element"></a>Diagnostisch monitorconfiguratie-element
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Vereist

|Kenmerken|Beschrijving|  
|----------------|-----------------|  
| **algemeneQuotaInMB** | De maximale hoeveelheid lokale schijfruimte die kan worden verbruikt door de verschillende typen diagnostische gegevens die door Azure Diagnostics zijn verzameld. De standaardinstelling is 4096 MB.<br />
|**proxyserver gebruiken** | Configureer Azure Diagnostics om de proxyserverinstellingen te gebruiken zoals ingesteld in IE-instellingen.|
|**Putten** | Toegevoegd in 1.5. Optioneel. Wijst naar een gootsteenlocatie om ook diagnostische gegevens te verzenden voor alle onderliggende elementen die putten ondersteunen. Sink voorbeeld is Application Insights of Event Hubs.|  


<br /> <br />

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**CrashDumps**|Zie beschrijving elders op deze pagina.|  
|**DiagnostischeInfrastructuurLogboeken**|Gegevensverzameling inschakelen die zijn gegenereerd door Azure Diagnostics. De logboeken van diagnostische infrastructuur zijn handig voor het oplossen van problemen met het diagnosesysteem zelf. Optionele kenmerken zijn:<br /><br /> - **scheduledTransferLogLevelFilter** - Configureert het minimale ernstniveau van de verzamelde logboeken.<br /><br /> - **scheduledTransferPeriod** - Het interval tussen geplande overdrachten naar opslag afgerond op de dichtstbijzijnde minuut. De waarde is een [XML -type 'Duurgegevens'.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Mappen**|Zie beschrijving elders op deze pagina.|  
|**EtwProviders**|Zie beschrijving elders op deze pagina.|  
|**Statistieken**|Zie beschrijving elders op deze pagina.|  
|**Prestatiemeteritems**|Zie beschrijving elders op deze pagina.|  
|**WindowsEventLog (WindowsEventLog)**|Zie beschrijving elders op deze pagina.|
|**DockerBronnen**|Zie beschrijving elders op deze pagina. |



## <a name="crashdumps-element"></a>CrashDumps Element  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

 Schakel het verzamelen van crashdumps in.  

|Kenmerken|Beschrijving|  
|----------------|-----------------|  
|**containerNaam**|Optioneel. De naam van de blobcontainer in uw Azure Storage-account die moet worden gebruikt om crashdumps op te slaan.|  
|**crashDumpType**|Optioneel.  Hiermee configureert u Azure Diagnostics om mini- of volledige crashdumps te verzamelen.|  
|**directoryQuotapercentage**|Optioneel.  Hiermee configureert u het percentage **van de totaleQuotaInMB** dat moet worden gereserveerd voor crashdumps op de VM.|  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**CrashDumpConfiguratie**|Vereist. Hiermee definieert u configuratiewaarden voor elk proces.<br /><br /> Het volgende kenmerk is ook vereist:<br /><br /> **processName** - De naam van het proces waarvoor Azure Diagnostics een crashdump moet verzamelen.|  

## <a name="directories-element"></a>Mappen Element
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories*

 Hiermee u de inhoud van een map, iIS-logboeken en/of IIS-logboeken verzamelen.  

 Optioneel **geplandOverdrachtsperiode-kenmerk.** Zie uitleg eerder.  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**IISLogs**|Door dit element in de configuratie op te nemen, kan het verzamelen van IIS-logboeken worden gebruikt:<br /><br /> **containerName** - De naam van de blobcontainer in uw Azure Storage-account die moet worden gebruikt om de IIS-logboeken op te slaan.|   
|**FailedRequestLogs**|Als u dit element in de configuratie opneemt, kunnen logboeken over mislukte aanvragen naar een IIS-site of -toepassing worden geplaatst. U moet ook traceringsopties inschakelen onder **het systeem. WebServer** in **Web.config**.|  
|**Datasources**|Een lijst van mappen om te controleren.|




## <a name="datasources-element"></a>Element Gegevensbronnen  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources*

 Een lijst van mappen om te controleren.  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**DirectoryConfiguratie**|Vereist. Vereist kenmerk:<br /><br /> **containerName** - De naam van de blobcontainer in uw Azure Storage-account die moet worden gebruikt om de logboekbestanden op te slaan.|  





## <a name="directoryconfiguration-element"></a>MapConfiguratie-element  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources - DirectoryConfiguration*

 Kan het element **Absolute** of **LocalResource bevatten,** maar niet beide.  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**Absoluut**|Het absolute pad naar de map om te controleren. De volgende kenmerken zijn vereist:<br /><br /> - **Pad** - Het absolute pad naar de map om te controleren.<br /><br /> - **expandEnvironment** - Configureert of omgevingsvariabelen in Pad zijn uitgebreid.|  
|**LocalResource (LocalResource)**|Het pad ten opzichte van een lokale resource die u wilt controleren. Vereiste kenmerken zijn:<br /><br /> - **Naam** - De lokale bron die de map bevat om te controleren<br /><br /> - **relativena** - Het pad ten opzichte van naam dat de map bevat om te controleren|  



## <a name="etwproviders-element"></a>EtwProviders Element  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - etwproviders*

 Hiermee configureert u verzameling ETW-gebeurtenissen van op EventSource en/of ETW Manifest gebaseerde providers.  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguratie**|Hiermee configureert u verzameling gebeurtenissen die zijn gegenereerd vanuit [de klasse EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Vereist kenmerk:<br /><br /> **provider** - De klassennaam van de gebeurtenis EventSource.<br /><br /> Optionele kenmerken zijn:<br /><br /> - **scheduledTransferLogLevelFilter** - Het minimale ernstniveau om over te zetten naar uw opslagaccount.<br /><br /> - **scheduledTransferPeriod** - Het interval tussen geplande overdrachten naar opslag afgerond op de dichtstbijzijnde minuut. De waarde is een [XML -type 'Duurgegevens'.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguratie**|Vereist kenmerk:<br /><br /> **provider** - De GUID van de eventprovider<br /><br /> Optionele kenmerken zijn:<br /><br /> - **scheduledTransferLogLevelFilter** - Het minimale ernstniveau om over te zetten naar uw opslagaccount.<br /><br /> - **scheduledTransferPeriod** - Het interval tussen geplande overdrachten naar opslag afgerond op de dichtstbijzijnde minuut. De waarde is een [XML -type 'Duurgegevens'.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguratie-element  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFg - DiagnosticMonitorConfiguration - etwproviders- etweventsourceproviderconfiguration*

 Hiermee configureert u verzameling gebeurtenissen die zijn gegenereerd vanuit [de klasse EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**Standaardgebeurtenissen**|Optioneel kenmerk:<br/><br/> **eventDestination** - De naam van de tabel om de gebeurtenissen op te slaan in|  
|**Gebeurtenis**|Vereist kenmerk:<br /><br /> **id** - De id van het evenement.<br /><br /> Optioneel kenmerk:<br /><br /> **eventDestination** - De naam van de tabel om de gebeurtenissen op te slaan in|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguratie-element  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFg - DiagnosticMonitorConfiguration - etwproviders - etwmanifestproviderconfiguration*

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**Standaardgebeurtenissen**|Optioneel kenmerk:<br /><br /> **eventDestination** - De naam van de tabel om de gebeurtenissen op te slaan in|  
|**Gebeurtenis**|Vereist kenmerk:<br /><br /> **id** - De id van het evenement.<br /><br /> Optioneel kenmerk:<br /><br /> **eventDestination** - De naam van de tabel om de gebeurtenissen op te slaan in|  



## <a name="metrics-element"></a>Statistiekenelement  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metrics*

 Hiermee u een prestatiemetertabel genereren die is geoptimaliseerd voor snelle query's. Elke prestatiemeter die is gedefinieerd in het element **Prestatiemeterwordt** naast de tabel Prestatiemeter opgeslagen in de tabel Statistieken.  

 Het **kenmerk resourceId** is vereist.  De bron-id van de virtuele machine of de virtuele machineschaalset waarop u Azure Diagnostics implementeert. Haal de **resource-ID** uit de [Azure-portal.](https://portal.azure.com) Selecteer -> **Brongroepen**  ->  ** bladeren\><naam**. **Browse** Klik op de tegel **Eigenschappen** en kopieer de waarde uit het veld **ID.**  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**Metrische aggregatie**|Vereist kenmerk:<br /><br /> **scheduledTransferPeriod** - Het interval tussen geplande overdrachten naar opslag afgerond op de dichtstbijzijnde minuut. De waarde is een [XML -type 'Duurgegevens'.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>Element Prestatiemeteritems  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFg - DiagnosticMonitorConfiguration - Performancecounters*

 Hiermee u prestatiemeteritems verzamelen.  

 Optioneel kenmerk:  

 Optioneel **geplandOverdrachtsperiode-kenmerk.** Zie uitleg eerder.

|Onderliggend element|Beschrijving|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguratie**|De volgende kenmerken zijn vereist:<br /><br /> - **counterSpecifier** - De naam van de prestatieteller. Bijvoorbeeld `\Processor(_Total)\% Processor Time`. Voer de opdracht `typeperf`uit om een lijst met prestatiemeteritems op uw host te krijgen.<br /><br /> - **sampleRate** - Hoe vaak de teller moet worden bemonsterd.<br /><br /> Optioneel kenmerk:<br /><br /> **eenheid** - De maateenheid van de toonbank. Waarden zijn beschikbaar bij [UnitType Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.sql.models.unittype?view=azure-dotnet) |
|**Putten** | Toegevoegd in 1.5. Optioneel. Wijst naar een gootsteenlocatie om ook diagnostische gegevens te verzenden. Bijvoorbeeld Azure Monitor of Event Hubs.|    




## <a name="windowseventlog-element"></a>WindowsEventLog-element
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFg - DiagnosticMonitorConfiguration - WindowsEventLog*

 Hiermee u Windows-gebeurtenislogboeken verzamelen.  

 Optioneel **geplandOverdrachtsperiode-kenmerk.** Zie uitleg eerder.  

|Onderliggend element|Beschrijving|  
|-------------------|-----------------|  
|**Datasource**|De Windows Event-logboeken die u wilt verzamelen. Vereist kenmerk:<br /><br /> **naam** - De XPath-query beschrijft de windows-gebeurtenissen die moeten worden verzameld. Bijvoorbeeld:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Als u alle gebeurtenissen wilt verzamelen, geeft u '*' op|  




## <a name="logs-element"></a>Logboekenelement  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs*

 Aanwezig in versie 1.0 en 1.1. Vermist in 1.2. Toegevoegd terug in 1.3.  

 Hiermee definieert u de bufferconfiguratie voor basisazure-logboeken.  

|Kenmerk|Type|Beschrijving|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Optioneel. Hiermee geeft u de maximale hoeveelheid bestandssysteemopslag op die beschikbaar is voor de opgegeven gegevens.<br /><br /> De standaardwaarde is 0.|  
|**geplandTransferLogLevelFilter**|**tekenreeks**|Optioneel. Hiermee geeft u het minimumernstniveau op voor logboekvermeldingen die worden overgedragen. De standaardwaarde is **Niet gedefinieerd,** waardoor alle logboeken worden overgedragen. Andere mogelijke waarden (in volgorde van de meeste tot minste informatie) zijn **Verbose**, **Informatie**, **Waarschuwing**, **Fout**en **Kritiek**.|  
|**geplandTransferPeriod**|**Duur**|Optioneel. Hiermee geeft u het interval op tussen geplande overdrachten van gegevens, naar boven afgerond op de dichtstbijzijnde minuut.<br /><br /> De standaardinstelling is PT0S.|  
|**Putten** |**tekenreeks**| Toegevoegd in 1.5. Optioneel. Wijst naar een gootsteenlocatie om ook diagnostische gegevens te verzenden. Bijvoorbeeld Application Insights of Event Hubs.|  

## <a name="dockersources"></a>DockerBronnen
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Toegevoegd in 1.9.

|Naam van element|Beschrijving|  
|------------------|-----------------|  
|**Statistieken**|Vertelt het systeem om statistieken te verzamelen voor Docker containers|  

## <a name="sinksconfig-element"></a>SinksConfig Element  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 Een lijst met locaties waarnaar diagnostische gegevens moeten worden verzonden en de configuratie die aan deze locaties is gekoppeld.  

|Naam van element|Beschrijving|  
|------------------|-----------------|  
|**Sink**|Zie beschrijving elders op deze pagina.|  

## <a name="sink-element"></a>Gootsteen-element
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink*

 Toegevoegd in versie 1.5.  

 Hiermee definieert u locaties waarnaar diagnostische gegevens moeten worden verzonden. Bijvoorbeeld de Application Insights-service.  

|Kenmerk|Type|Beschrijving|  
|---------------|----------|-----------------|  
|**Naam**|tekenreeks|Een tekenreeks die de sinkname identificeert.|  

|Element|Type|Beschrijving|  
|-------------|----------|-----------------|  
|**Application Insights**|tekenreeks|Alleen gebruikt bij het verzenden van gegevens naar Application Insights. De instrumentatiesleutel bevatten voor een actief Application Insights-account waartoe u toegang hebt.|  
|**Kanalen**|tekenreeks|Eén voor elke extra filtering die stream die u|  

## <a name="channels-element"></a>Kanaalelement  
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels*

 Toegevoegd in versie 1.5.  

 Hiermee definieert u filters voor logboekgegevens die door een gootsteen gaan.  

|Element|Type|Beschrijving|  
|-------------|----------|-----------------|  
|**Kanaal**|tekenreeks|Zie beschrijving elders op deze pagina.|  

## <a name="channel-element"></a>Kanaalelement
 *Boom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels - Channel*

 Toegevoegd in versie 1.5.  

 Hiermee definieert u locaties waarnaar diagnostische gegevens moeten worden verzonden. Bijvoorbeeld de Application Insights-service.  

|Kenmerken|Type|Beschrijving|  
|----------------|----------|-----------------|  
|**Loglevel**|**tekenreeks**|Hiermee geeft u het minimumernstniveau op voor logboekvermeldingen die worden overgedragen. De standaardwaarde is **Niet gedefinieerd,** waardoor alle logboeken worden overgedragen. Andere mogelijke waarden (in volgorde van de meeste tot minste informatie) zijn **Verbose**, **Informatie**, **Waarschuwing**, **Fout**en **Kritiek**.|  
|**Naam**|**tekenreeks**|Een unieke naam van het kanaal om naar te verwijzen|  


## <a name="privateconfig-element"></a>PrivateConfig Element
 *Boom: Root - DiagnosticsConfiguration - PrivateConfig*

 Toegevoegd in versie 1.3.  

 Optioneel  

 Slaat de privégegevens van het opslagaccount op (naam, sleutel en eindpunt). Deze informatie wordt naar de virtuele machine verzonden, maar kan er niet uit worden opgehaald.  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**StorageAccount**|Het opslagaccount dat u wilt gebruiken. De volgende kenmerken zijn vereist<br /><br /> - **naam** - De naam van het opslagaccount.<br /><br /> - **sleutel** - De sleutel tot de opslagrekening.<br /><br /> - **eindpunt** - Het eindpunt om toegang te krijgen tot het opslagaccount. <br /><br /> -**sasToken** (toegevoegd 1.8.1)- U een SAS-token opgeven in plaats van een opslagaccountsleutel in de privé-config. Indien aanwezig, wordt de opslagaccountsleutel genegeerd. <br />Vereisten voor het SAS-token: <br />- Ondersteunt alleen sas-token voor account <br />- *b,* *t-servicetypen* zijn vereist. <br /> - *a*, *c*, *u*, *w* machtigingen zijn vereist. <br /> - *c*, *o* resourcetypes zijn vereist. <br /> - Ondersteunt alleen het HTTPS-protocol <br /> - De begin- en vervaldatum moeten geldig zijn.|  


## <a name="isenabled-element"></a>IsEnabled-element  
 *Boom: Root - DiagnosticsConfiguration - IsEnabled*

 Booleaanse. Gebruik `true` om de `false` diagnostiek in te schakelen of om de diagnostiek uit te schakelen.

## <a name="example-configuration"></a>Voorbeeldconfiguratie
 Hieronder volgt een volledige voorbeeldconfiguratie voor Windows-diagnostische extensie die wordt weergegeven in zowel JSON als XML.

 
### <a name="json"></a>JSON

De *PublicConfig* en *PrivateConfig* zijn gescheiden omdat ze in de meeste JSON-gebruiksgevallen worden doorgegeven als verschillende variabelen. Deze cases omvatten Resource Manager-sjablonen, PowerShell en Visual Studio.

> [!NOTE]
> De definitie van de openbare config Azure Monitor-sink heeft twee *eigenschappen, resourceId* en *regio.* Deze zijn alleen vereist voor klassieke VM's en Classic Cloud-services. Deze eigenschappen mogen niet worden gebruikt voor andere resources.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> De private config Azure Monitor sink definition heeft twee eigenschappen, *PrincipalId* en *Secret*. Deze zijn alleen vereist voor klassieke VM's en Classic Cloud-services. Deze eigenschappen mogen niet worden gebruikt voor andere resources.


```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

### <a name="xml"></a>XML

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> De definitie van de openbare config Azure Monitor-sink heeft twee eigenschappen, resourceId en regio. Deze zijn alleen vereist voor klassieke VM's en Classic Cloud-services. Deze eigenschappen mogen niet worden gebruikt voor virtuele machines van Resource Manager of virtuele machineschaalsets.
> Er is ook een extra Private Config-element voor de Azure Monitor-sink, die wordt doorgegeven in een hoofd-id en geheim. Dit is alleen vereist voor klassieke VM's en Classic Cloud Services. Voor Resource Manager VM's en VMSS kan de Azure Monitor-definitie in het private config-element worden uitgesloten.
>
