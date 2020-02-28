---
title: Snapshot Debugger voor .NET-apps in Azure App Service inschakelen | Microsoft Docs
description: Snapshot Debugger voor .NET-apps in Azure App Service inschakelen
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: c23da585034e74d85be5a3c41b124f00408a0f4a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671423"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Snapshot Debugger voor .NET-apps in Azure App Service inschakelen

Snapshot Debugger werkt momenteel voor ASP.NET-en ASP.NET Core-apps die worden uitgevoerd op Azure App Service op Windows-service plannen.

## <a id="installation"></a>Snapshot Debugger inschakelen
Volg de onderstaande instructies om Snapshot Debugger voor een app in te scha kelen. Als u een ander type Azure-service gebruikt, zijn hier instructies voor het inschakelen van Snapshot Debugger op andere ondersteunde platforms:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines-en virtuele-machine schaal sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [On-premises virtuele of fysieke machines](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Als u een preview-versie van .NET Core gebruikt, volgt u de instructies voor het [inschakelen van Snapshot debugger in andere omgevingen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) eerst om het [micro soft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-pakket op te nemen met de toepassing en voert u de overige instructies hieronder uit. 

Application Insights Snapshot Debugger wordt vooraf geïnstalleerd als onderdeel van de App Services runtime, maar u moet deze inschakelen om moment opnamen voor uw App Service-app op te halen. Nadat u een app hebt geïmplementeerd, moet u, zelfs als u de SDK van Application Insights in de bron code hebt opgenomen, de volgende stappen volgen om de fout opsporing voor moment opnamen in te scha kelen.

1. Ga naar het deel venster **app Services** in het Azure Portal.
2. Navigeer naar **instellingen >** deel venster Application Insights.

   ![App Insights inschakelen in App Services-portal](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Volg de instructies in het deel venster om een nieuwe resource te maken of selecteer een bestaande app Insights-resource om uw app te controleren. Zorg er ook voor dat beide Schakel opties voor Snapshot Debugger zijn **ingeschakeld**.

   ![App Insights-site-extensie toevoegen][Enablement UI]

4. Snapshot Debugger is nu ingeschakeld met behulp van een App Services app-instelling.

    ![App-instelling voor Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Snapshot Debugger uitschakelen

Volg dezelfde stappen als voor **inschakelen snapshot debugger**, maar schakel beide Schakel opties voor Snapshot debugger in **uit**.
We raden u aan Snapshot Debugger in al uw apps in te scha kelen om de diagnose van toepassings uitzonderingen te vereenvoudigen.

## <a name="next-steps"></a>Volgende stappen

- Verkeer genereren voor uw toepassing die een uitzonde ring kan veroorzaken. Wacht vervolgens 10 tot 15 minuten totdat moment opnamen naar het Application Insights-exemplaar worden verzonden.
- Zie [moment opnamen](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) in de Azure Portal.
- Zie [Snapshot debugger Troubleshooting](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)(Engelstalig) voor hulp bij het oplossen van problemen met snapshot debugger.

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

