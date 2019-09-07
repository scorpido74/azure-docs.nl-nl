---
title: Configuratie schema Azure Diagnostics-extensie 1,3 en hoger
description: De schema versie 1,3 en later worden Azure Diagnostics geleverd als onderdeel van de Microsoft Azure SDK 2,4 of hoger.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: e303fe5ca1869249d57373aab9c60a5f92b7ea9c
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735103"
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Configuratie schema voor Azure Diagnostics 1,3 en hoger
> [!NOTE]
> De uitbrei ding Azure Diagnostics is het onderdeel dat wordt gebruikt voor het verzamelen van prestatie meter items en andere statistieken uit:
> - Azure Virtual Machines
> - Virtual Machine Scale Sets
> - Service Fabric
> - Cloud Services
> - Netwerkbeveiligingsgroepen
>
> Deze pagina is alleen relevant als u een van deze services gebruikt.

Deze pagina is geldig voor versie 1,3 en hoger (Azure SDK 2,4 en hoger). Nieuwere configuratie secties zijn een opmerking die wordt weer gegeven in de versie die ze hebben toegevoegd. Versie 1,0 en 1,2 van het schema zijn gearchiveerd en zijn niet meer beschikbaar. 

Het configuratie bestand dat hier wordt beschreven, wordt gebruikt om diagnostische configuratie-instellingen in te stellen wanneer de diagnostische monitor wordt gestart.  

De uitbrei ding wordt gebruikt in combi natie met andere micro soft Diagnostics-producten, zoals Azure Monitor, waaronder Application Insights en Log Analytics.

Down load de schema definitie van het open bare configuratie bestand door de volgende Power shell-opdracht uit te voeren:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Zie [Azure Diagnostics-extensie](diagnostics-extension-overview.md)voor meer informatie over het gebruik van Azure Diagnostics.  

## <a name="example-of-the-diagnostics-configuration-file"></a>Voor beeld van het configuratie bestand voor diagnostische gegevens  
 In het volgende voor beeld ziet u een typisch configuratie bestand voor diagnostische gegevens:  

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
> De Sink-definitie voor de open bare configuratie Azure Monitor heeft twee eigenschappen, resourceId en regio. Deze zijn alleen vereist voor klassieke Vm's en klassieke Cloud Services. Deze eigenschappen mogen niet worden gebruikt voor Resource Manager-Virtual Machines of schaal sets voor virtuele machines.
> Er is ook een extra persoonlijk configuratie-element voor de Azure Monitor sink, die wordt door gegeven in een principal-id en een geheim. Dit is alleen vereist voor klassieke Vm's en klassieke Cloud Services. Voor Resource Manager-Vm's en VMSS de Azure Monitor definitie in het persoonlijke configuratie-element kan worden uitgesloten.
>

JSON-equivalent van het vorige XML-configuratie bestand.

De PublicConfig en PrivateConfig worden gescheiden omdat de meeste JSON-gebruiks gevallen worden door gegeven als verschillende variabelen. Deze gevallen zijn onder andere Resource Manager-sjablonen, Power shell-Schaalset voor virtuele machines en Visual Studio.

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
> De Sink-definitie voor de open bare configuratie Azure Monitor heeft twee eigenschappen, resourceId en regio. Deze zijn alleen vereist voor klassieke Vm's en klassieke Cloud Services.
> Deze eigenschappen mogen niet worden gebruikt voor Resource Manager-Virtual Machines of schaal sets voor virtuele machines.
>

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

> [!NOTE]
> Er is een extra persoonlijk configuratie-element voor de Azure Monitor sink, die wordt door gegeven in een principal-id en geheim. Dit is alleen vereist voor klassieke Vm's en klassieke Cloud Services. Voor Resource Manager-Vm's en VMSS de Azure Monitor definitie in het persoonlijke configuratie-element kan worden uitgesloten.
>


## <a name="reading-this-page"></a>Deze pagina lezen  
 De labels die volgen, zijn ongeveer de volg orde die in het vorige voor beeld wordt weer gegeven.  Zoek op de pagina naar het element of kenmerk als u de volledige beschrijving niet ziet.  

## <a name="common-attribute-types"></a>Algemene kenmerk typen  
 het kenmerk **scheduledTransferPeriod** wordt in verschillende elementen weer gegeven. Het is het interval tussen de geplande overdrachten naar de dichtstbijzijnde minuut. De waarde is een [XML-duur-gegevens type.](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration-element  
 *Hardwarestructuur Root-DiagnosticsConfiguration*

Toegevoegd in versie 1,3.  

Het element op het hoogste niveau van het diagnostische configuratie bestand.  

**Kenmerk** xmlns-de XML-naam ruimte voor het diagnostische configuratie bestand is:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Onderliggende elementen|Description|  
|--------------------|-----------------|  
|**PublicConfig**|Vereist. Zie de beschrijving elders op deze pagina.|  
|**PrivateConfig**|Optioneel. Zie de beschrijving elders op deze pagina.|  
|**IsEnabled**|True. Zie de beschrijving elders op deze pagina.|  

## <a name="publicconfig-element"></a>PublicConfig-element  
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig*

 Beschrijft de configuratie van de open bare diagnostische gegevens.  

|Onderliggende elementen|Description|  
|--------------------|-----------------|  
|**WadCfg**|Vereist. Zie de beschrijving elders op deze pagina.|  
|**StorageAccount**|De naam van het Azure Storage-account waarin de gegevens moeten worden opgeslagen. Kan ook worden opgegeven als een para meter bij het uitvoeren van de cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Kan *Table*, *BLOB*of *TableAndBlob*zijn. De tabel is standaard. Wanneer TableAndBlob is gekozen, worden diagnostische gegevens twee keer naar elk type geschreven.|  
|**LocalResourceDirectory**|De map op de virtuele machine waar gebeurtenis gegevens worden opgeslagen met de bewakings agent. Als dat niet zo is, wordt ingesteld, wordt de standaard Directory gebruikt:<br /><br /> Voor een werk nemer/Web-rol:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Voor een virtuele machine:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> De vereiste kenmerken zijn:<br /><br /> - **pad** : de map op het systeem die moet worden gebruikt door Azure Diagnostics.<br /><br /> - **expandEnvironment** : Hiermee bepaalt u of omgevings variabelen in de padnaam worden uitgevouwen.|  

## <a name="wadcfg-element"></a>WadCFG-element  
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG*

 Identificeert en configureert de telemetriegegevens die moeten worden verzameld.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration-element
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration*

 Vereist

|Kenmerken|Description|  
|----------------|-----------------|  
| **overallQuotaInMB** | De maximale hoeveelheid lokale schijf ruimte die kan worden gebruikt door de verschillende typen diagnostische gegevens die worden verzameld door Azure Diagnostics. De standaard instelling is 4096 MB.<br />
|**useProxyServer** | Configureer Azure Diagnostics om de instellingen van de proxy server te gebruiken zoals ingesteld in de instellingen van Internet Explorer.|
|**wastafel** | Toegevoegd in 1,5. Optioneel. Verwijst naar een Sink-locatie om ook diagnostische gegevens te verzenden voor alle onderliggende elementen die sinks ondersteunen. Sink-voor beeld is Application Insights of Event Hubs.|  


<br /> <br />

|Onderliggende elementen|Description|  
|--------------------|-----------------|  
|**CrashDumps**|Zie de beschrijving elders op deze pagina.|  
|**DiagnosticInfrastructureLogs**|Verzamelen van logboeken die zijn gegenereerd door Azure Diagnostics inschakelen. De logboeken voor de diagnose infrastructuur zijn handig voor het oplossen van problemen met het diagnose systeem zelf. Optionele kenmerken zijn:<br /><br /> - **scheduledTransferLogLevelFilter** : Hiermee configureert u het minimale Ernst niveau van de verzamelde Logboeken.<br /><br /> - **scheduledTransferPeriod** : het interval tussen geplande transfers naar opslag die is afgerond op de dichtstbijzijnde minuut. De waarde is een [XML-duur-gegevens type.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Adreslijsten**|Zie de beschrijving elders op deze pagina.|  
|**EtwProviders**|Zie de beschrijving elders op deze pagina.|  
|**Metrische gegevens**|Zie de beschrijving elders op deze pagina.|  
|**Performance Counters**|Zie de beschrijving elders op deze pagina.|  
|**WindowsEventLog**|Zie de beschrijving elders op deze pagina.|
|**DockerSources**|Zie de beschrijving elders op deze pagina. |



## <a name="crashdumps-element"></a>CrashDumps-element  
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-CrashDumps*

 Schakel de verzameling van crash dumps in.  

|Kenmerken|Description|  
|----------------|-----------------|  
|**containerName**|Optioneel. De naam van de BLOB-container in uw Azure Storage-account dat moet worden gebruikt voor het opslaan van crash dumps.|  
|**crashDumpType**|Optioneel.  Hiermee configureert u Azure Diagnostics voor het verzamelen van Mini maal of volledige crash dumps.|  
|**directoryQuotaPercentage**|Optioneel.  Hiermee configureert u het percentage **overallQuotaInMB** dat moet worden gereserveerd voor crash dumps op de virtuele machine.|  

|Onderliggende elementen|Description|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Vereist. Definieert configuratie waarden voor elk proces.<br /><br /> Het volgende kenmerk is ook vereist:<br /><br /> naam **verwerkings** proces: de namen van de processen die u Azure Diagnostics wilt voor het verzamelen van een crash dump voor.|  

## <a name="directories-element"></a>Element directory's
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Directory's*

 Hiermee schakelt u de verzameling van de inhoud van een map in, IIS heeft geen toegang tot logboeken en/of IIS-logboeken.  

 Optioneel **scheduledTransferPeriod** -kenmerk. Zie de uitleg eerder.  

|Onderliggende elementen|Description|  
|--------------------|-----------------|  
|**IISLogs**|Met dit element in de configuratie kan IIS-logboeken worden verzameld:<br /><br /> **containerName** : de naam van de BLOB-container in uw Azure Storage-account die moet worden gebruikt om de IIS-logboeken op te slaan.|   
|**FailedRequestLogs**|Met inbegrip van dit element in de configuratie kan het verzamelen van logboeken over mislukte aanvragen naar een IIS-site of-toepassing. U moet ook tracerings opties inschakelen onder **systeem. Webserver** in **Web. config**.|  
|**Gegevens bronnen**|Een lijst met mappen die moeten worden bewaakt.|




## <a name="datasources-element"></a>Element gegevens bronnen  
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Directory's-data sources*

 Een lijst met mappen die moeten worden bewaakt.  

|Onderliggende elementen|Description|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Vereist. Vereist kenmerk:<br /><br /> **containerName** : de naam van de BLOB-container in uw Azure Storage-account die moet worden gebruikt om de logboek bestanden op te slaan.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration-element  
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Directory's-data sources-DirectoryConfiguration*

 Kan het **absolute** -of **LocalResource** -element bevatten, maar niet beide.  

|Onderliggende elementen|Description|  
|--------------------|-----------------|  
|**Absolute**|Het absolute pad naar de map die u wilt bewaken. De volgende kenmerken zijn vereist:<br /><br /> - **Pad** : het absolute pad naar de map die u wilt bewaken.<br /><br /> - **expandEnvironment** : Hiermee configureert u of omgevings variabelen in het pad worden uitgevouwen.|  
|**LocalResource**|Het pad ten opzichte van een lokale bron die moet worden bewaakt. De vereiste kenmerken zijn:<br /><br /> - **Naam** : de lokale resource die de directory bevat die moet worden bewaakt<br /><br /> - **relativePath** : het pad ten opzichte van de naam die de te bewaken Directory bevat|  



## <a name="etwproviders-element"></a>EtwProviders-element  
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders*

 Hiermee configureert u de verzameling van ETW-gebeurtenissen van Event source-en/of ETW-manifest providers.  

|Onderliggende elementen|Description|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Hiermee configureert u de verzameling van gebeurtenissen die zijn gegenereerd met de [klasse Event source](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Vereist kenmerk:<br /><br /> **provider** : de klassenaam van de gebeurtenis Event source.<br /><br /> Optionele kenmerken zijn:<br /><br /> - **scheduledTransferLogLevelFilter** : het minimale Ernst niveau dat u wilt overdragen naar uw opslag account.<br /><br /> - **scheduledTransferPeriod** : het interval tussen geplande transfers naar opslag die is afgerond op de dichtstbijzijnde minuut. De waarde is een [XML-duur-gegevens type.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Vereist kenmerk:<br /><br /> **provider** -de GUID van de gebeurtenis provider<br /><br /> Optionele kenmerken zijn:<br /><br /> - **scheduledTransferLogLevelFilter** : het minimale Ernst niveau dat u wilt overdragen naar uw opslag account.<br /><br /> - **scheduledTransferPeriod** : het interval tussen geplande transfers naar opslag die is afgerond op de dichtstbijzijnde minuut. De waarde is een [XML-duur-gegevens type.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwEventSourceProviderConfiguration*

 Hiermee configureert u de verzameling van gebeurtenissen die zijn gegenereerd met de [klasse Event source](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Onderliggende elementen|Description|  
|--------------------|-----------------|  
|**DefaultEvents**|Optioneel kenmerk:<br/><br/> **eventDestination** : de naam van de tabel waarin de gebeurtenissen moeten worden opgeslagen|  
|**Gebeurtenis**|Vereist kenmerk:<br /><br /> **id** : de id van de gebeurtenis.<br /><br /> Optioneel kenmerk:<br /><br /> **eventDestination** : de naam van de tabel waarin de gebeurtenissen moeten worden opgeslagen|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwManifestProviderConfiguration*

|Onderliggende elementen|Description|  
|--------------------|-----------------|  
|**DefaultEvents**|Optioneel kenmerk:<br /><br /> **eventDestination** : de naam van de tabel waarin de gebeurtenissen moeten worden opgeslagen|  
|**Gebeurtenis**|Vereist kenmerk:<br /><br /> **id** : de id van de gebeurtenis.<br /><br /> Optioneel kenmerk:<br /><br /> **eventDestination** : de naam van de tabel waarin de gebeurtenissen moeten worden opgeslagen|  



## <a name="metrics-element"></a>Element metrische gegevens  
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Metrics*

 Hiermee kunt u een tabel voor prestatie meter items genereren die is geoptimaliseerd voor snelle query's. Elk prestatie meter item dat in het **Performance Counters** -element is gedefinieerd, wordt opgeslagen in de tabel metrische gegevens, naast de tabel prestatie meter items.  

 Het kenmerk **resourceId** is vereist.  De resource-ID van de virtuele machine of virtuele-machine Schaalset waarmee u Azure Diagnostics implementeert. Haal de **resourceID** op uit de [Azure Portal](https://portal.azure.com). Selecteer **Bladeren** -> **resource groepen** ->  **< naam.\>** Klik op de tegel **Eigenschappen** en kopieer de waarde uit het veld **id** .  

|Onderliggende elementen|Description|  
|--------------------|-----------------|  
|**MetricAggregation**|Vereist kenmerk:<br /><br /> **scheduledTransferPeriod** : het interval tussen geplande transfers naar opslag die is afgerond op de dichtstbijzijnde minuut. De waarde is een [XML-duur-gegevens type.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>Performance Counters-element  
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Performance Counters*

 Hiermee schakelt u het verzamelen van prestatie meter items in.  

 Optioneel kenmerk:  

 Optioneel **scheduledTransferPeriod** -kenmerk. Zie de uitleg eerder.

|Onderliggend element|Description|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|De volgende kenmerken zijn vereist:<br /><br /> - **counterSpecifier** : de naam van het prestatie meter item. Bijvoorbeeld `\Processor(_Total)\% Processor Time`. Voer de opdracht `typeperf`uit om een lijst met prestatie meter items op uw host op te halen.<br /><br /> - **sampleRate** : hoe vaak de teller moet worden gesampled.<br /><br /> Optioneel kenmerk:<br /><br /> **eenheid** : de maat eenheid van het prestatie meter item.|
|**wastafel** | Toegevoegd in 1,5. Optioneel. Verwijst naar een Sink-locatie om ook diagnostische gegevens te verzenden. Bijvoorbeeld Azure Monitor of Event Hubs.|    




## <a name="windowseventlog-element"></a>WindowsEventLog-element
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-WindowsEventLog*

 Hiermee schakelt u de verzameling van Windows-gebeurtenis Logboeken.  

 Optioneel **scheduledTransferPeriod** -kenmerk. Zie de uitleg eerder.  

|Onderliggend element|Description|  
|-------------------|-----------------|  
|**Bron**|De Windows-gebeurtenis logboeken die moeten worden verzameld. Vereist kenmerk:<br /><br /> **naam** : de XPath-query die de Windows-gebeurtenissen beschrijft die moeten worden verzameld. Bijvoorbeeld:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Als u alle gebeurtenissen wilt verzamelen, geeft u * op|  




## <a name="logs-element"></a>Logboek element  
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-logs*

 Aanwezig in versie 1,0 en 1,1. Ontbreekt in 1,2. Weer toegevoegd in 1,3.  

 Hiermee definieert u de buffer configuratie voor Basic Azure-Logboeken.  

|Kenmerk|type|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Optioneel. Hiermee geeft u de maximale hoeveelheid bestandssysteem opslag op die beschikbaar is voor de opgegeven gegevens.<br /><br /> De standaard waarde is 0.|  
|**scheduledTransferLogLevelFilter**|**string**|Optioneel. Hiermee geeft u het minimale Ernst niveau voor logboek vermeldingen die worden overgebracht. De standaard waarde is niet **gedefinieerd**, waardoor alle logboeken worden overgedragen. Andere mogelijke waarden (ten opzichte van de meest waarschijnlijke informatie) zijn **uitgebreide**, **informatie**, **waarschuwing**, **fout**en **kritiek**.|  
|**scheduledTransferPeriod**|**Duur**|Optioneel. Hiermee geeft u het interval op tussen de geplande overdracht van gegevens, naar boven afgerond op de dichtstbijzijnde minuut.<br /><br /> De standaard waarde is PT0S.|  
|**wastafel** |**string**| Toegevoegd in 1,5. Optioneel. Verwijst naar een Sink-locatie om ook diagnostische gegevens te verzenden. Bijvoorbeeld Application Insights of Event Hubs.|  

## <a name="dockersources"></a>DockerSources
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-DockerSources*

 Toegevoegd in 1,9.

|Element naam|Description|  
|------------------|-----------------|  
|**Statistieken**|Laat het systeem statistieken verzamelen voor docker-containers|  

## <a name="sinksconfig-element"></a>SinksConfig-element  
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig*

 Een lijst met locaties voor het verzenden van diagnostische gegevens naar en de configuratie die is gekoppeld aan die locaties.  

|Element naam|Description|  
|------------------|-----------------|  
|**Sink**|Zie de beschrijving elders op deze pagina.|  

## <a name="sink-element"></a>Sink-element
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink*

 Toegevoegd in versie 1,5.  

 Hiermee worden locaties gedefinieerd voor het verzenden van diagnostische gegevens naar. Bijvoorbeeld de Application Insights-service.  

|Kenmerk|type|Description|  
|---------------|----------|-----------------|  
|**name**|string|Een teken reeks die de Sink-id aangeeft.|  

|Element|type|Description|  
|-------------|----------|-----------------|  
|**Application Insights**|string|Wordt alleen gebruikt bij het verzenden van gegevens naar Application Insights. De instrumentatie sleutel voor een actief Application Insights account bevatten waartoe u toegang hebt.|  
|**Detailhandelkanalen**|string|Een voor elke extra filtering die stream die u|  

## <a name="channels-element"></a>Channels-element  
 *Hardwarestructuur Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels*

 Toegevoegd in versie 1,5.  

 Definieert filters voor streams van logboek gegevens die via een Sink worden door gegeven.  

|Element|type|Description|  
|-------------|----------|-----------------|  
|**Kanalen**|string|Zie de beschrijving elders op deze pagina.|  

## <a name="channel-element"></a>Kanaal element
 *Hardwarestructuur Root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink-kanalen-Channel*

 Toegevoegd in versie 1,5.  

 Hiermee worden locaties gedefinieerd voor het verzenden van diagnostische gegevens naar. Bijvoorbeeld de Application Insights-service.  

|Kenmerken|type|Description|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Hiermee geeft u het minimale Ernst niveau voor logboek vermeldingen die worden overgebracht. De standaard waarde is niet **gedefinieerd**, waardoor alle logboeken worden overgedragen. Andere mogelijke waarden (ten opzichte van de meest waarschijnlijke informatie) zijn **uitgebreide**, **informatie**, **waarschuwing**, **fout**en **kritiek**.|  
|**name**|**string**|Een unieke naam van het kanaal waarnaar moet worden verwezen|  


## <a name="privateconfig-element"></a>PrivateConfig-element
 *Hardwarestructuur Root-DiagnosticsConfiguration-PrivateConfig*

 Toegevoegd in versie 1,3.  

 Optioneel  

 Hiermee worden de persoonlijke gegevens van het opslag account (naam, sleutel en eind punt) opgeslagen. Deze informatie wordt verzonden naar de virtuele machine, maar kan niet worden opgehaald.  

|Onderliggende elementen|Description|  
|--------------------|-----------------|  
|**StorageAccount**|Het opslag account dat moet worden gebruikt. De volgende kenmerken zijn vereist<br /><br /> - **naam** : de naam van het opslag account.<br /><br /> - **sleutel** : de sleutel voor het opslag account.<br /><br /> - **endpoint** : het eind punt voor toegang tot het opslag account. <br /><br /> -**sasToken** (toegevoegde 1.8.1): u kunt in de persoonlijke configuratie een SAS-token opgeven in plaats van een opslag account sleutel. Als u deze opgeeft, wordt de sleutel van het opslag account genegeerd. <br />Vereisten voor het SAS-token: <br />-Ondersteunt alleen het account-SAS-token <br />- *b*, *t* service types zijn vereist. <br /> - *a*, *c*, *u*, *w* machtigingen zijn vereist. <br /> - *c*, *o* -resource typen zijn vereist. <br /> -Ondersteunt alleen het HTTPS-protocol <br /> -Start en verloop tijd moeten geldig zijn.|  


## <a name="isenabled-element"></a>IsEnabled-element  
 *Hardwarestructuur Root-DiagnosticsConfiguration-IsEnabled*

 True. Gebruiken `true` om de diagnostische gegevens in `false` te scha kelen of de diagnostische gegevens uit te scha kelen.

