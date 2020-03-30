---
title: Azure-diagnose (.NET) gebruiken met Cloud Services | Microsoft Documenten
description: Azure-diagnose gebruiken om gegevens uit Azure-cloudservices te verzamelen voor foutopsporing, het meten van prestaties, monitoring, verkeersanalyse en meer.
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: tagore
ms.openlocfilehash: 1e49a0935a70a2470267e5458fa1f55e3059e965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469762"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Azure Diagnostics inschakelen in Azure Cloud Services
Zie [Overzicht van Azure Diagnostics](../azure-diagnostics.md) voor een achtergrond in Azure Diagnostics.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Diagnostische gegevens inschakelen in een werkrol
In deze walkthrough wordt beschreven hoe u een Azure-werkrol implementeert die telemetriegegevens uitzendt met de klasse .NET EventSource. Azure Diagnostics wordt gebruikt om de telemetriegegevens te verzamelen en op te slaan in een Azure-opslagaccount. Bij het maken van een werkrol schakelt Visual Studio Diagnostics 1.0 automatisch in als onderdeel van de oplossing in Azure SDKs voor .NET 2.4 en eerder. In de volgende instructies wordt het proces beschreven voor het maken van de werkrol, het uitschakelen van Diagnostics 1.0 van de oplossing en het implementeren van Diagnostics 1.2 of 1.3 in uw werkrol.

### <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u een Azure-abonnement hebt en Visual Studio gebruikt met de Azure SDK. Als u geen Azure-abonnement hebt, u zich aanmelden voor de [gratis proefversie.][Free Trial] Zorg ervoor dat [Azure PowerShell-versie 0.8.7 of hoger][Install and configure Azure PowerShell version 0.8.7 or later]wordt geïnstalleerd en geconfigureerd.

### <a name="step-1-create-a-worker-role"></a>Stap 1: Een werkrol maken
1. Start **Visual Studio**.
2. Maak een **Azure Cloud Service-project** op basis van de **cloudsjabloon** die zich richt op .NET Framework 4.5.  Geef het project een naam "WadExample" en klik op Ok.
3. Selecteer **Werkrol** en klik op Ok. Het project wordt gemaakt.
4. Dubbelklik in **Solution Explorer**op het eigenschappenbestand **WorkerRole1.**
5. Schakel op het tabblad **Configuratie** de controle **Diagnostische gegevens** uit om Diagnostics 1.0 (Azure SDK 2.4 en eerder) uit te schakelen.
6. Bouw uw oplossing om te controleren of u geen fouten hebt.

### <a name="step-2-instrument-your-code"></a>Stap 2: Instrument uw code
Vervang de inhoud van WorkerRole.cs door de volgende code. De klasse SampleEventSourceWriter, overgenomen van de [klasse EventSource,][EventSource Class]implementeert vier registratiemethoden: **SendEnums**, **MessageMethod**, **SetOther** en **HighFreq**. De eerste parameter voor de **methode WriteEvent** definieert de id voor de betreffende gebeurtenis. Met de methode Uitvoeren wordt elke 10 seconden een oneindige lus geïmplementeerd die elk van de logboekmethoden aanroept die in de klasse **SampleEventSourceWriter** zijn geïmplementeerd.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Stap 3: Uw werkrol implementeren

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Implementeer uw werkrol vanuit Visual Studio naar Azure door het **WadExample-project** te selecteren in de Solution Explorer **en publiceer vervolgens** in het menu **Bouwen.**
2. Kies uw abonnement.
3. Selecteer in het dialoogvenster **Microsoft Azure-publicatie-instellingen** de optie **Nieuw maken...**.
4. Voer in het dialoogvenster **Cloudservice en opslagaccount maken** een **naam** in (bijvoorbeeld 'WadExample') en selecteer een regio of affiniteitsgroep.
5. De **omgeving** instellen op **Fasering**.
6. Wijzig alle andere **instellingen** naar gelang van het geval en klik op **Publiceren**.
7. Nadat de implementatie is voltooid, controleert u in de Azure-portal of uw cloudservice in **een status actief** is.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Stap 4: Uw configuratiebestand Diagnostics maken en de extensie installeren
1. Download de definitie van het openbare configuratiebestandsschema door de volgende PowerShell-opdracht uit te voeren:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Voeg een XML-bestand toe aan uw **WorkerRole1-project** door met de rechtermuisknop op het **WorkerRole1-project** te klikken en nieuw item **toe te voegen...** -> **New Item…** -> **Xml-bestand met visuele C#-items** -> **Data** -> **XML File**. Geef het bestand "WadExample.xml" een naam.

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Koppel de WadConfig.xsd aan het configuratiebestand. Controleer of het venster WadExample.xml-editor het actieve venster is. Druk op **F4** om het venster **Eigenschappen** te openen. Klik op de eigenschap **Schema's** in het venster **Eigenschappen.** Klik op de **...** in de eigenschap **Schema's.** Klik op de knop **Toevoegen...** en navigeer naar de locatie waar u het XSD-bestand hebt opgeslagen en selecteer het bestand WadConfig.xsd. Klik op **OK**.

4. Vervang de inhoud van het configuratiebestand WadExample.xml door de volgende XML en sla het bestand op. Dit configuratiebestand definieert een paar prestatiemeteritems om te verzamelen: een voor CPU-gebruik en een voor geheugengebruik. Vervolgens definieert de configuratie de vier gebeurtenissen die overeenkomen met de methoden in de klasse SampleEventSourceWriter.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Stap 5: Diagnostische gegevens installeren op uw werkrol
De PowerShell-cmdlets voor het beheren van diagnostische gegevens op een web- of werknemersrol zijn: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension en Remove-AzureServiceDiagnosticsExtension.

1. Open Azure PowerShell.
2. Voer het script uit om Diagnostics op uw werkrol te installeren (vervang *StorageAccountKey* door de opslagaccountsleutel voor uw opslagaccount en *config_path* met het pad naar het *WadExample.xml-bestand):*

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Stap 6: Bekijk uw telemetriegegevens
Navigeer in de Visual Studio **Server Explorer**naar het opslagaccount van Wadexample. Nadat de cloudservice ongeveer vijf (5) minuten heeft uitgevoerd, ziet u de tabellen **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** en **WADSetOtherTable**. Dubbelklik op een van de tabellen om de verzamelde telemetrie weer te geven.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Configuratiebestandsschema
In het configuratiebestand Diagnostische gegevens worden waarden gedefinieerd die worden gebruikt om diagnostische configuratie-instellingen te initialiseren wanneer de diagnoseagent wordt gestart. Zie de [laatste schemareferentie](/azure/azure-monitor/platform/diagnostics-extension-schema) voor geldige waarden en voorbeelden.

## <a name="troubleshooting"></a>Problemen oplossen
Zie Azure Diagnostics [oplossen](../azure-diagnostics-troubleshooting.md) voor hulp bij veelvoorkomende problemen als u problemen hebt.

## <a name="next-steps"></a>Volgende stappen
[Bekijk een lijst met gerelateerde diagnostische artikelen voor virtuele azure's](../azure-monitor/platform/diagnostics-extension-overview.md) om de gegevens die u verzamelt te wijzigen, problemen op te lossen of meer te weten te komen over diagnostische gegevens in het algemeen.

[EventSource Class]: https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: https://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/



