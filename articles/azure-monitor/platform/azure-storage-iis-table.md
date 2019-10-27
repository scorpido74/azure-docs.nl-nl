---
title: Gebruik Blob Storage voor IIS en tabel opslag voor gebeurtenissen in Azure Monitor | Microsoft Docs
description: Azure Monitor kunt de logboeken lezen voor Azure-Services die diagnostische gegevens schrijven naar Table-opslag of IIS-logboeken die zijn geschreven naar Blob Storage.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 04/12/2017
ms.openlocfilehash: 8f70ecc96269783c29c566fb89bd617f034316b1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932672"
---
# <a name="collect-azure-diagnostic-logs-from-azure-storage"></a>Diagnostische Azure-logboeken van Azure Storage verzamelen

Azure Monitor kunt de logboeken lezen voor de volgende services die diagnostische gegevens schrijven naar Table-opslag of IIS-logboeken die zijn geschreven naar Blob Storage:

* Service Fabric clusters (preview-versie)
* Virtual Machines
* Web/werk rollen

Voordat Azure Monitor gegevens in een Log Analytics werkruimte voor deze resources kunt verzamelen, moet Azure Diagnostics zijn ingeschakeld.

Zodra de diagnostische gegevens zijn ingeschakeld, kunt u de werk ruimte Azure Portal of Power shell gebruiken om de logboeken te verzamelen.

Azure Diagnostics is een Azure-extensie waarmee u Diagnostische gegevens kunt verzamelen van een werk rollen, webrol of virtuele machine die wordt uitgevoerd in Azure. De gegevens worden opgeslagen in een Azure-opslag account en kunnen vervolgens worden verzameld door Azure Monitor.

Als Azure Monitor deze Azure Diagnostics-logboeken wilt verzamelen, moeten de logboeken zich op de volgende locaties bevinden:

| Logboek type | Resourcetype | Locatie |
| --- | --- | --- |
| IIS-logboeken |Virtual Machines <br> Webrollen <br> Werk rollen |wad-IIS-logboek bestanden (Blob Storage) |
| Syslog |Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Operationele gebeurtenissen Service Fabric |Service Fabric knooppunten |WADServiceFabricSystemEventTable |
| Service Fabric betrouw bare actor gebeurtenissen |Service Fabric knooppunten |WADServiceFabricReliableActorEventTable |
| Service Fabric betrouw bare service gebeurtenissen |Service Fabric knooppunten |WADServiceFabricReliableServiceEventTable |
| Windows-gebeurtenis logboeken |Service Fabric knooppunten <br> Virtual Machines <br> Webrollen <br> Werk rollen |WADWindowsEventLogsTable (Table Storage) |
| Windows ETW-logboeken |Service Fabric knooppunten <br> Virtual Machines <br> Webrollen <br> Werk rollen |WADETWEventTable (Table Storage) |

> [!NOTE]
> IIS-logboeken van Azure websites worden momenteel niet ondersteund.
>
>

Voor virtuele machines hebt u de mogelijkheid om de Log Analytics- [agent](../../azure-monitor/learn/quick-collect-azurevm.md) in uw virtuele machine te installeren om extra inzichten mogelijk te maken. Naast het analyseren van IIS-logboeken en-gebeurtenis logboeken, kunt u extra analyses uitvoeren, waaronder configuratie wijzigingen bijhouden, SQL-evaluatie en update-evaluatie.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Diagnostische gegevens van Azure inschakelen op een virtuele machine voor gebeurtenis logboeken en IIS-logboek verzameling

Gebruik de volgende procedure om Azure Diagnostics in te scha kelen in een virtuele machine voor gebeurtenis logboeken en IIS-logboek verzameling met behulp van de Microsoft Azure-portal.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Diagnostische gegevens van Azure inschakelen op een virtuele machine met de Azure Portal

1. Installeer de VM-agent tijdens het maken van een virtuele machine. Als de virtuele machine al bestaat, controleert u of de VM-agent al is geïnstalleerd.

   * Ga in het Azure Portal naar de virtuele machine, selecteer **optionele configuratie**, vervolgens **diagnoses** en stel **status** in **op aan**.

     Wanneer de virtuele machine is voltooid, is de Azure Diagnostics-uitbrei ding geïnstalleerd en wordt deze uitgevoerd. Deze uitbrei ding is verantwoordelijk voor het verzamelen van uw diagnostische gegevens.
2. Schakel bewaking in en configureer gebeurtenis logboek registratie op een bestaande VM. U kunt Diagnostische gegevens inschakelen op het niveau van de virtuele machine. Voer de volgende stappen uit om diagnostische gegevens in te scha kelen en vervolgens gebeurtenis logboek registratie te configureren:

   1. Selecteer de VM.
   2. Klik op **bewaking**.
   3. Klik op **Diagnostische gegevens**.
   4. Stel de **status** **in op on**.
   5. Selecteer elk diagnostische logboek dat u wilt verzamelen.
   6. Klik op **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Diagnostische gegevens van Azure inschakelen in een webrole voor IIS-logboek en gebeurtenis verzameling

Zie [Diagnostische gegevens inschakelen in een Cloud service](../../cloud-services/cloud-services-dotnet-diagnostics.md) voor algemene stappen voor het inschakelen van Azure Diagnostics. De onderstaande instructies gebruiken deze informatie en aanpassen deze voor gebruik met Log Analytics.

Met diagnostische gegevens van Azure ingeschakeld:

* IIS-logboeken worden standaard opgeslagen en logboek gegevens worden overgebracht naar het scheduledTransferPeriod-overdrachts interval.
* Windows-gebeurtenis logboeken worden niet standaard overgedragen.

### <a name="to-enable-diagnostics"></a>Diagnostische gegevens inschakelen

Als u Windows-gebeurtenis logboeken wilt inschakelen, of als u de scheduledTransferPeriod wilt wijzigen, configureert u Azure Diagnostics met behulp van het XML-configuratie bestand (Diagnostics. wadcfg), zoals wordt weer gegeven in [stap 4: uw diagnostische configuratie bestand maken en de uitbrei ding installeren](../../cloud-services/cloud-services-dotnet-diagnostics.md)

In het volgende voor beeld van een configuratie bestand worden IIS-logboeken en alle gebeurtenissen uit de toepassings-en systeem logboeken verzameld:

```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Zorg ervoor dat uw ConfigurationSettings een opslag account opgeeft, zoals in het volgende voor beeld:

```xml
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

De waarden **AccountName** en **AccountKey** vindt u in de Azure Portal in het dash board van het opslag account onder toegangs sleutels beheren. Het protocol voor de connection string moet **https**zijn.

Zodra de bijgewerkte diagnostische configuratie is toegepast op uw Cloud service en er diagnostische gegevens naar Azure Storage worden geschreven, kunt u de Log Analytics-werk ruimte nu configureren.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>De Azure Portal gebruiken voor het verzamelen van logboeken van Azure Storage

U kunt de Azure Portal gebruiken om een Log Analytics-werk ruimte te configureren in Azure Monitor om de logboeken te verzamelen voor de volgende Azure-Services:

* Service Fabric clusters
* Virtual Machines
* Web/werk rollen

Ga in het Azure Portal naar uw Log Analytics-werk ruimte en voer de volgende taken uit:

1. Klik op *Logboeken voor opslag accounts*
2. Klik op de taak *toevoegen*
3. Selecteer het opslag account dat de diagnostische Logboeken bevat
   * Dit account kan een klassiek opslag account of een Azure Resource Manager Storage-account zijn
4. Selecteer het gegevens type waarvoor u logboeken wilt verzamelen
   * De opties zijn IIS-logboeken. Evenementen Syslog (Linux); ETW-logboeken; Service Fabric gebeurtenissen
5. De waarde voor de bron wordt automatisch ingevuld op basis van het gegevens type en kan niet worden gewijzigd
6. Klik op OK om de configuratie op te slaan

Herhaal stap 2-6 voor extra opslag accounts en gegevens typen die u in de werk ruimte wilt verzamelen.

In ongeveer 30 minuten kunt u de gegevens van het opslag account in de werk ruimte Log Analytics bekijken. Er worden alleen gegevens weer gegeven die naar opslag zijn geschreven nadat de configuratie is toegepast. De bestaande gegevens van het opslag account worden niet door de werk ruimte gelezen.

> [!NOTE]
> De portal valideert niet dat de bron bestaat in het opslag account of dat er nieuwe gegevens worden geschreven.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Diagnostische gegevens van Azure inschakelen op een virtuele machine voor gebeurtenis logboeken en IIS-logboek verzameling met behulp van Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Gebruik de stappen in [configure Azure monitor voor het indexeren van Azure Diagnostics](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) voor het gebruik van Power shell om te lezen van Azure Diagnostics die zijn geschreven naar Table Storage.

Met Azure PowerShell kunt u de gebeurtenissen die naar Azure Storage worden geschreven nauw keuriger opgeven.
Zie voor meer informatie [Diagnostische gegevens inschakelen in Azure virtual machines](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

U kunt Azure Diagnostics inschakelen en bijwerken met behulp van het volgende Power shell-script.
U kunt dit script ook gebruiken met een aangepaste configuratie voor logboek registratie.
Wijzig het script om het opslag account, de service naam en de naam van de virtuele machine in te stellen.
Het script maakt gebruik van cmdlets voor klassieke virtuele machines.

Bekijk het volgende voorbeeld script, kopieer het, wijzig het indien nodig, sla het voor beeld op als Power shell-script bestand en voer het script uit.

```powershell
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Volgende stappen

* [Verzamel logboeken en metrische gegevens voor Azure-Services](collect-azure-metrics-logs.md) voor ondersteunde Azure-Services.
* [Schakel oplossingen](../../azure-monitor/insights/solutions.md) in om inzicht te krijgen in de gegevens.
* [Gebruik Zoek query's](../../azure-monitor/log-query/log-query-overview.md) om de gegevens te analyseren.