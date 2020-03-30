---
title: Klassieke Windows VM-statistieken verzenden naar Azure Monitor-statistiekendatabase
description: Statistieken van gastbesturingssysteemverzenden naar het Azure Monitor-gegevensarchief voor een virtuele Windows-machine (klassiek)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 65bb1a3915ece384974da12b4e7a1ad0c1e08133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655807"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Gaststatistieken van besturingssysteem verzenden naar de Azure Monitor-metrische gegevensdatabase voor een virtuele Windows-machine (klassiek)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Met de azure monitor [diagnostics-extensie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (bekend als WAD of Diagnostics) u statistieken en logboeken verzamelen van het gastbesturingssysteem (Guest OS) dat wordt uitgevoerd als onderdeel van een virtuele machine, cloudservice of Service Fabric-cluster. De extensie kan telemetrie naar [veel verschillende locaties verzenden.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

In dit artikel wordt het proces beschreven voor het verzenden van gastbesturingssysteemprestatiestatistieken voor een virtuele Windows-machine (klassiek) naar de azure monitor-metrische database. Vanaf Diagnostics versie 1.11 u statistieken rechtstreeks schrijven naar de Azure Monitor-statistiekenwinkel, waar standaardplatformstatistieken al zijn verzameld. 

Als u ze op deze locatie opslaat, hebt u toegang tot dezelfde acties als voor platformstatistieken. Acties omvatten near-real-time waarschuwingen, grafieken, routering, toegang vanuit een REST API en meer. In het verleden is de extensie Diagnostische gegevens geschreven naar Azure Storage, maar niet naar het Azure Monitor-gegevensarchief. 

Het proces dat in dit artikel wordt beschreven, werkt alleen op klassieke virtuele machines waarop het Windows-besturingssysteem wordt uitgevoerd.

## <a name="prerequisites"></a>Vereisten

- U moet een [servicebeheerder of co-beheerder](../../cost-management-billing/manage/add-change-subscription-administrator.md) zijn van uw Azure-abonnement. 

- Uw abonnement moet zijn geregistreerd bij [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- U moet [Azure PowerShell](/powershell/azure) of [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) hebben geïnstalleerd.

- Uw VM-bron moet zich in een [regio bevinden die aangepaste statistieken ondersteunt.](metrics-custom-overview.md#supported-regions)

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Een klassiek virtueel machine- en opslagaccount maken

1. Maak een klassieke VM met behulp van de Azure-portal.
   ![Klassieke VM maken](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Wanneer u deze virtuele machine maakt, kiest u de optie om een nieuw klassiek opslagaccount te maken. We gebruiken dit opslagaccount in latere stappen.

1. Ga in de Azure-portal naar het bronblad **voor opslagaccounts.** Selecteer **Sleutels**en neem nota van de naam van het opslagaccount en de opslagaccountsleutel. U hebt deze informatie in latere stappen nodig.
   ![Opslagtoegangssleutels](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Een service-principal maken

Maak een serviceprincipe in uw Azure Active Directory-tenant met behulp van de instructies bij [Een serviceprincipal maken.](../../active-directory/develop/howto-create-service-principal-portal.md) Let op het volgende tijdens het doorlopen van dit proces: 
- Maak nieuwe client geheim voor deze app.
- Bewaar de sleutel en de client-id voor gebruik in latere stappen.

Geef deze app 'Monitoring Metrics Publisher'-machtigingen aan de bron waartegen u statistieken wilt uitzenden. U een resourcegroep of een volledig abonnement gebruiken.  

> [!NOTE]
> De extensie Diagnostische gegevens gebruikt de serviceprincipal om te verifiëren tegen Azure Monitor en statistieken uit te zenden voor uw klassieke VM.

## <a name="author-diagnostics-extension-configuration"></a>Configuratie van de extensie Auteur Diagnostics

1. Bereid het configuratiebestand voor diagnostische extensie voor. Dit bestand bepaalt welke logboeken en prestatietellers de diagnostische extensie moet verzamelen voor uw klassieke VM. Hier volgt een voorbeeld:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. Definieer in het gedeelte 'SinksConfig' van uw diagnosebestand een nieuwe Azure Monitor-sink als volgt:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. In het gedeelte van uw configuratiebestand waar de lijst met prestatiemeteritems wordt vermeld, wordt de prestatiemeteritems doorverwezen naar de Azure Monitor-sink "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Definieer in de privéconfiguratie het Azure Monitor-account. Voeg vervolgens de belangrijkste servicegegevens toe die u wilt gebruiken om statistieken uit te zenden.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Sla dit bestand lokaal op.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>De diagnostics-extensie implementeren voor uw cloudservice

1. Start PowerShell en meld je aan.

    ```powershell
    Login-AzAccount
    ```

1. Begin met het instellen van de context voor uw klassieke VM.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Stel de context in van het klassieke opslagaccount dat met de VM is gemaakt.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Stel het gegevenspad van Diagnostics in op een variabele met de volgende opdracht:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Bereid de update voor uw klassieke VM voor met het diagnostischbestand waarop de Azure Monitor-sink is geconfigureerd.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Implementeer de update naar uw VM door de volgende opdracht uit te voeren:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Het is nog steeds verplicht om een opslagaccount aan te bieden als onderdeel van de installatie van de Diagnostics-extensie. Logboeken of prestatiemeteritems die zijn opgegeven in het config-bestand Diagnostics, worden naar het opgegeven opslagaccount geschreven.

## <a name="plot-the-metrics-in-the-azure-portal"></a>De statistieken in de Azure-portal uitzetten

1.  Ga naar Azure Portal. 

1.  Selecteer Monitor in het **linkermenu.**

1.  Selecteer op het **blad Monitor** de optie **Metrische gegevens**.

    ![Navigeren door statistieken](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. Selecteer in de vervolgkeuzelijst resources uw klassieke vm.

1. Selecteer **azure.vm.windows.guest**in het vervolgkeuzemenu namespaces .

1. Selecteer in de vervolgkeuzelijst Statistieken de optie **Geheugen\Vastgelegde bytes in gebruik**.
   ![Plotstatistieken](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [aangepaste statistieken](metrics-custom-overview.md).

