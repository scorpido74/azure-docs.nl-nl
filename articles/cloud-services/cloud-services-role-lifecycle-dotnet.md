---
title: Gebeurtenissen in de levenscyclus van Cloud Service verwerken | Microsoft Documenten
description: Meer informatie over hoe de levenscyclusmethoden van een Cloud Service-rol kunnen worden gebruikt in .NET
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: a6030cbb756525137497834ac911835033858401
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652077"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>De levenscyclus van een web- of werknemersrol aanpassen in .NET
Wanneer u een werknemersrol maakt, breidt u de klasse [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) uit, die methoden biedt waarmee u overschrijven waarmee u reageren op levenscyclusgebeurtenissen. Voor webrollen is deze klasse optioneel, dus u moet deze gebruiken om te reageren op levenscyclusgebeurtenissen.

## <a name="extend-the-roleentrypoint-class"></a>De klasse RoleEntryPoint uitbreiden
De klasse [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) bevat methoden die door Azure worden aangeroepen wanneer deze **wordt gestart,** **wordt uitgevoerd**of een web- of werknemersrol **wordt** gestopt. U deze methoden optioneel overschrijven om de initialisatie van rollen, taakafsluitingsreeksen of de uitvoeringsthread van de rol te beheren. 

Wanneer u **RoleEntryPoint**uitbreidt, moet u zich bewust zijn van het volgende gedrag van de methoden:

* De [Methode OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) retourneert een booleaanse waarde, dus het is mogelijk om **false** van deze methode terug te sturen.
  
   Als uw code **false**retourneert, wordt het rolproces abrupt beëindigd, zonder dat u een afsluitvolgorde uitvoert die u mogelijk hebt uitgevoerd. In het algemeen moet u voorkomen dat false van de **OnStart-methode** **wordt** geretourneerd.
* Elke niet-gevangen uitzondering binnen een overbelasting van een **RoleEntryPoint-methode** wordt behandeld als een niet-behandelde uitzondering.
  
   Als er een uitzondering optreedt binnen een van de levenscyclusmethoden, verhoogt Azure de gebeurtenis [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) en wordt het proces beëindigd. Nadat uw rol offline is gehaald, wordt deze opnieuw gestart door Azure. Wanneer een niet-verwerkte uitzondering optreedt, wordt de gebeurtenis [Stoppen](/previous-versions/azure/reference/ee758136(v=azure.100)) niet verhoogd en wordt de methode **OnStop** niet aangeroepen.

Als uw rol niet wordt gestart of wordt gerecycled tussen de initialiserende, drukke en stoppende statussen, kan het zijn dat uw code een onverwerkte uitzondering binnen een van de levenscyclusgebeurtenissen gooit telkens wanneer de rol opnieuw wordt opgestart. Gebruik in dit geval de gebeurtenis [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) om de oorzaak van de uitzondering te bepalen en deze op de juiste manier af te handelen. Uw rol kan ook terugkeren van de methode [Uitvoeren,](/previous-versions/azure/reference/ee772746(v=azure.100)) waardoor de rol opnieuw wordt opgestart. Zie Algemene problemen die [leiden tot het recyclen van rollen voor](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)meer informatie over implementatiestatuss.

> [!NOTE]
> Als u de **Azure Tools voor Microsoft Visual Studio** gebruikt om uw toepassing te ontwikkelen, worden de roleprojectsjablonen automatisch de **roleEntryPoint-klasse** voor u uitgebreid in de *WebRole.cs* en *WorkerRole.cs* bestanden.
> 
> 

## <a name="onstart-method"></a>OnStart-methode
De **Methode OnStart** wordt aangeroepen wanneer uw rolinstantie online wordt gebracht door Azure. Terwijl de OnStart-code wordt uitgevoerd, wordt de rolinstantie gemarkeerd als **Bezet** en wordt er geen extern verkeer naar deze door de load balancer geleid. U deze methode overschrijven om initialisatiewerk uit te voeren, zoals het implementeren van gebeurtenishandlers en het starten van [Azure Diagnostics.](cloud-services-how-to-monitor.md)

Als **OnStart** **true**retourneert, wordt de instantie geparafeerd en roept Azure de methode **RoleEntryPoint.Run aan.** Als **OnStart** **false**retourneert, wordt de rol onmiddellijk beëindigd, zonder geplande afsluitreeksen uit te voeren.

In het volgende codevoorbeeld ziet u hoe u de **Methode OnStart** overschrijft. Met deze methode wordt een diagnostische monitor geconfigureerd en gestart wanneer de rolinstantie wordt gestart en wordt een overdracht van logboekgegevens naar een opslagaccount ingesteld:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>OnStop-methode
De **Methode OnStop** wordt aangeroepen nadat een rolinstantie offline is gehaald door Azure en voordat het proces wordt afgesloten. U deze methode overschrijven om code aan te roepen die vereist is voor uw rolinstantie om netjes af te sluiten.

> [!IMPORTANT]
> Code die wordt uitgevoerd in de **OnStop-methode** heeft een beperkte tijd om te voltooien wanneer deze wordt aangeroepen om andere redenen dan een door de gebruiker geïnitieerde afsluiting. Nadat deze tijd is verstreken, wordt het proces beëindigd, dus u moet ervoor zorgen dat code in de **OnStop-methode** snel kan worden uitgevoerd of dat het niet wordt voltooid. De **methode OnStop** wordt aangeroepen nadat de gebeurtenis **Stoppen** is verhoogd.
> 
> 

## <a name="run-method"></a>Methode uitvoeren
U de methode **Uitvoeren** overschrijven om een langlopende thread voor uw rolinstantie te implementeren.

Het overschrijven van de **runmethode** is niet vereist; de standaardimplementatie begint een thread die voor altijd slaapt. Als u de **methode Uitvoeren** overschrijft, moet uw code voor onbepaalde tijd worden geblokkeerd. Als de methode **Uitvoeren** terugkeert, wordt de rol automatisch gracieus gerecycled; Met andere woorden, Azure verhoogt de **gebeurtenis Stoppen** en roept de **OnStop-methode** aan, zodat uw afsluitreeksen kunnen worden uitgevoerd voordat de rol offline wordt gehaald.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>De ASP.NET levenscyclusmethoden implementeren voor een webrol
U de ASP.NET levenscyclusmethoden gebruiken, naast de methoden die worden geleverd door de klasse **RoleEntryPoint,** om initialisatie- en afsluitsequenties voor een webrol te beheren. Dit kan handig zijn voor compatibiliteitsdoeleinden als u een bestaande ASP.NET toepassing overgeeft naar Azure. De ASP.NET levenscyclusmethoden worden aangeroepen vanuit de **RoleEntryPoint-methoden.** De methode **Toepassingsstart\_** wordt aangeroepen nadat de methode **RoleEntryPoint.OnStart** is voltooid. De methode **Toepassingseinde\_** wordt aangeroepen voordat de **methode RoleEntryPoint.OnStop** wordt aangeroepen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [maken van een cloudservicepakket.](cloud-services-model-and-package.md)




