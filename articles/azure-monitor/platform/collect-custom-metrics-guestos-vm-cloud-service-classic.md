---
title: Klassieke Cloud Services-statistieken verzenden naar Azure Monitor-statistiekendatabase
description: Beschrijft het proces voor het verzenden van gastosprestatiestatistieken voor klassieke Azure Cloud Services naar de Azure Monitor-metrische opslag.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3b390ffa20cf3cf79b8fb6311ad05b2978bd5d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655787"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Gaststatistieken van besturingssysteem verzenden naar de klassieke Cloud Services van Azure Monitor 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Met de Azure Monitor [Diagnostics-extensie](diagnostics-extension-overview.md)u statistieken en logboeken verzamelen van het gastbesturingssysteem (Guest OS) dat wordt uitgevoerd als onderdeel van een virtuele machine, cloudservice of Service Fabric-cluster. De extensie kan telemetrie naar [veel verschillende locaties verzenden.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

In dit artikel wordt het proces beschreven voor het verzenden van gastosprestatiestatistieken voor klassieke Azure Cloud Services naar de Azure Monitor-metrische opslag. Vanaf Diagnostics versie 1.11 u statistieken rechtstreeks schrijven naar de Azure Monitor-statistiekenwinkel, waar standaardplatformstatistieken al zijn verzameld. 

Als u ze op deze locatie opslaat, hebt u toegang tot dezelfde acties die u uitvoeren voor platformstatistieken. Acties omvatten near-real-time waarschuwingen, grafieken, routering, toegang vanuit een REST API en meer.  In het verleden is de extensie Diagnostische gegevens geschreven naar Azure Storage, maar niet naar het Azure Monitor-gegevensarchief.  

Het proces dat in dit artikel wordt beschreven, werkt alleen voor prestatiemeteritems in Azure Cloud Services. Het werkt niet voor andere aangepaste statistieken. 

## <a name="prerequisites"></a>Vereisten

- U moet een [servicebeheerder of co-beheerder](../../cost-management-billing/manage/add-change-subscription-administrator.md) zijn van uw Azure-abonnement. 

- Uw abonnement moet zijn geregistreerd bij [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- U moet [Azure PowerShell](/powershell/azure) of [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) hebben geïnstalleerd.

- Uw CloudService moet zich in een [regio bevinden die aangepaste statistieken ondersteunt.](metrics-custom-overview.md#supported-regions)

## <a name="provision-a-cloud-service-and-storage-account"></a>Een cloudservice- en opslagaccount inrichten 

1. Maak en implementeer een klassieke cloudservice. Een voorbeeld van klassieke Cloud Services-toepassingen en -implementatie vindt u bij [Aan de slag met Azure Cloud Services en ASP.NET.](../../cloud-services/cloud-services-dotnet-get-started.md) 

2. U een bestaand opslagaccount gebruiken of een nieuw opslagaccount implementeren. Het is het beste als het opslagaccount zich in dezelfde regio bevindt als de klassieke cloudservice die u hebt gemaakt. Ga in de Azure-portal naar het bronblad **voor opslagaccounts** en selecteer **Sleutels**. Let op de naam van het opslagaccount en de opslagaccountsleutel. U hebt deze informatie in latere stappen nodig.

   ![Opslagaccountsleutels](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Een service-principal maken 

Maak een serviceprincipe in uw Azure Active Directory-tenant met behulp van de instructies bij [Gebruiksportal om een Azure Active Directory-toepassing en serviceprincipal te maken die toegang heeft tot bronnen.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) Let op het volgende terwijl u dit proces doorloopt: 

- U elke URL voor de aanmeldings-URL inplaatsen.  
- Maak nieuwe client geheim voor deze app.  
- Bewaar de sleutel en de client-id voor gebruik in latere stappen.  

Geef de app die is gemaakt in de vorige stap *Machtigingen voor Monitoring Metrics Publisher* aan de resource waartegen u statistieken wilt uitzenden. Als u van plan bent de app te gebruiken om aangepaste statistieken uit te zenden tegen veel bronnen, u deze machtigingen toestaan op resourcegroep- of abonnementsniveau.  

> [!NOTE]
> De extensie Diagnostische gegevens gebruikt de serviceprincipal om te verifiëren tegen Azure Monitor en statistieken uit te zenden voor uw cloudservice.

## <a name="author-diagnostics-extension-configuration"></a>Configuratie van de extensie Auteur Diagnostics 

Bereid het configuratiebestand voor diagnostische extensie voor. Dit bestand bepaalt welke logboeken en prestatietellers de Diagnostische extensie moet verzamelen voor uw cloudservice. Hieronder volgt een voorbeeld van het configuratiebestand Diagnostische diagnose:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

Definieer in het gedeelte 'SinksConfig' van uw diagnosebestand een nieuwe Azure Monitor-sink: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

Voeg in het gedeelte van uw configuratiebestand waar u de prestatiemeteritems opgeeft die u wilt verzamelen, de azure-monitorgootlijst toe. Dit bericht zorgt ervoor dat alle prestatiemeteritems die u hebt opgegeven, worden doorgestuurd naar Azure Monitor als metrische gegevens. U prestatiemeteritems toevoegen of verwijderen op basis van uw behoeften. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Voeg ten slotte in de privéconfiguratie een *azure-monitoraccountsectie* toe. Voer de client-id en het geheim van de serviceclient in die u eerder hebt gemaakt. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Sla dit diagnosebestand lokaal op.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>De diagnostics-extensie implementeren voor uw cloudservice 

Start PowerShell en meld u aan bij Azure. 

```powershell
Login-AzAccount 
```

Gebruik de volgende opdrachten om de gegevens van het opslagaccount op te slaan dat u eerder hebt gemaakt. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Stel ook het route voor het diagnostisch bestand in op een variabele met de volgende opdracht:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Implementeer de diagnostische extensie voor uw cloudservice met het diagnosebestand met de Azure Monitor-sink die is geconfigureerd met de volgende opdracht:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Het is nog steeds verplicht om een opslagaccount aan te bieden als onderdeel van de installatie van de Diagnostics-extensie. Logboeken of prestatiemeteritems die zijn opgegeven in het diagnose-config-bestand, worden naar het opgegeven opslagaccount geschreven.  

## <a name="plot-metrics-in-the-azure-portal"></a>Plotstatistieken in de Azure-portal 

1. Ga naar Azure Portal. 

   ![Azure-portal met statistieken](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. Selecteer Monitor in het **linkermenu.**

3. Selecteer **op** het blad Monitor het tabblad **Voorvertoning met statistieken.**

4. Selecteer in de vervolgkeuzelijst resources uw klassieke cloudservice.

5. Selecteer **azure.vm.windows.guest**in het vervolgkeuzemenu namespaces . 

6. Selecteer in de vervolgkeuzelijst Statistieken de optie **Geheugen\Vastgelegde bytes in gebruik**. 

U gebruikt de mogelijkheden voor het filteren en splitsen van de dimensie om het totale geheugen weer te geven dat wordt gebruikt door een specifieke rol of rolinstantie. 

 ![Azure-portal met statistieken](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [aangepaste statistieken](metrics-custom-overview.md).

