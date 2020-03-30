---
title: Momentopnamefoutopsporing inschakelen voor .NET-apps in Azure App Service | Microsoft Documenten
description: Momentopnamefoutopsporing inschakelen voor .NET-apps in Azure App Service
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8af688e38003e0613a06d7d8622ce279a3838589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298271"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Momentopnamefoutopsporing inschakelen voor .NET-apps in Azure App Service

Snapshot Debugger werkt momenteel voor ASP.NET en ASP.NET Core-apps die worden uitgevoerd op Azure App Service op Windows-serviceplannen.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>Momentopnamefoutopsporing inschakelen
Als u Momentopnamefoutopsporing voor een app wilt inschakelen, volgt u de onderstaande instructies. Als u een ander type Azure-service uitvoert, vindt u hier instructies voor het inschakelen van Momentopnamefoutopsporing op andere ondersteunde platforms:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines en virtuele machineschaalsets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [On-premises virtuele of fysieke machines](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Als u een preview-versie van .NET Core gebruikt, volgt u eerst de instructies [voor Momentopnamefoutbugger inschakelen voor andere omgevingen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) om het [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-pakket bij de toepassing op te nemen en vul vervolgens de rest van de onderstaande instructies in. 

Application Insights Snapshot Debugger is vooraf geïnstalleerd als onderdeel van de runtime van App Services, maar u moet deze inschakelen om momentopnamen voor uw App Service-app te krijgen. Zodra u een app hebt geïmplementeerd, zelfs als u de Application Insights SDK in de broncode hebt opgenomen, volgt u de onderstaande stappen om de momentopnamefoutopsporing in te schakelen.

1. Ga naar het deelvenster **App-services** in de Azure-portal.
2. Navigeer naar het deelvenster **Instellingen > Toepassingsstatistieken.**

   ![App-inzichten inschakelen op de Portal van App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Volg de instructies in het deelvenster om een nieuwe bron te maken of selecteer een bestaande App Insights-bron om uw app te controleren. Zorg er ook voor dat beide switches voor Momentopnamefoutopsporing **zijn ingeschakeld.**

   ![App Insights-site-extensie toevoegen][Enablement UI]

4. Snapshot Debugger is nu ingeschakeld met behulp van een App-instelling voor App Services.

    ![App-instelling voor momentopnamefoutopsporing][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Momentopnamefoutopsporing uitschakelen

Volg dezelfde stappen als voor **Momentopnamefoutopsporing inschakelen,** maar schakel beide switches voor Momentopnamefoutopsporing in naar **Uit**.
We raden u aan snapshotdebugger in al uw apps te laten inschakelen om de diagnose van toepassingsuitzonderingen te vergemakkelijken.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

Voor een Azure App-service u app-instellingen instellen in een azure resource manager-sjabloon om Momentopnamefoutopsporing en Profiler in te schakelen. U voegt een config-bron toe die de app-instellingen bevat als onderliggende bron van de website:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Volgende stappen

- Verkeer genereren naar uw toepassing dat een uitzondering kan activeren. Wacht vervolgens 10 tot 15 minuten voordat momentopnamen naar het exemplaar Application Insights worden verzonden.
- Bekijk [momentopnamen](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) in de Azure-portal.
- Zie Problemen met [momentopnamefoutopsporing](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)voor hulp bij het oplossen van problemen met momentopnamenbugger .

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

