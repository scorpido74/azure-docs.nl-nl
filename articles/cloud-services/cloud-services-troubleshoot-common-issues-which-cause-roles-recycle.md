---
title: Veelvoorkomende oorzaken van het recyclen van cloudservicerollen | Microsoft Documenten
description: Een cloudservicerol die plotseling wordt gerecycled, kan aanzienlijke downtime veroorzaken. Hier volgen enkele veelvoorkomende problemen die ervoor zorgen dat rollen worden gerecycled, waardoor u downtime verminderen.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: a644e211cc933ca686f0bd6a13b0d2ba8ae20162
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114098"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Algemene problemen waardoor rollen worden herhaald
In dit artikel worden enkele van de veelvoorkomende oorzaken van implementatieproblemen besproken en worden tips voor het oplossen van problemen gegeven om u te helpen deze problemen op te lossen. Een indicatie dat er een probleem bestaat met een toepassing is wanneer de rolinstantie niet wordt gestart of wanneer deze wordt gecycled tussen de initialiserende, drukke en stoppende statussen.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Ontbrekende runtime-afhankelijkheden
Als een rol in uw toepassing is gebaseerd op een assemblage die geen deel uitmaakt van het .NET Framework of de Azure-beheerde bibliotheek, moet u die assemblage expliciet opnemen in het toepassingspakket. Houd er rekening mee dat andere Microsoft-frameworks standaard niet beschikbaar zijn op Azure. Als uw rol afhankelijk is van een dergelijk framework, moet u deze samenstellingen toevoegen aan het toepassingspakket.

Controleer het volgende voordat u uw toepassing bouwt en verpakt:

* Als u Visual Studio gebruikt, controleert u of de eigenschap **Lokaal kopiëren** is ingesteld op **True** voor elke vergadering waarnaar wordt verwezen in uw project en die geen deel uitmaakt van de Azure SDK of het .NET-framework.
* Zorg ervoor dat het web.config-bestand niet verwijst naar ongebruikte verzamelingen in het compilatieelement.
* De **buildactie** van elk .cshtml-bestand is ingesteld op **Inhoud**. Dit zorgt ervoor dat de bestanden correct worden weergegeven in het pakket en maakt het mogelijk andere naar de hand genoemde bestanden te verschijnen in het pakket.

## <a name="assembly-targets-wrong-platform"></a>Assemblage richt zich op verkeerd platform
Azure is een 64-bits omgeving. Daarom werken .NET-assemblages die zijn samengesteld voor een 32-bits doel niet op Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rol gooit onverwerkte uitzonderingen tijdens het initialiseren of stoppen
Uitzonderingen die worden gegenereerd door de methoden van de klasse [RoleEntryPoint,] waaronder de methoden [OnStart,] [OnStop]en [Uitvoeren,] zijn onverwerkte uitzonderingen. Als er in een van deze methoden een niet-behandelde uitzondering optreedt, wordt de rol gerecycled. Als de rol herhaaldelijk wordt gerecycled, kan het gooien van een onverwerkte uitzondering elke keer dat het probeert te starten.

## <a name="role-returns-from-run-method"></a>Rolretouren van methode Uitvoeren
De methode [Uitvoeren] is bedoeld om voor onbepaalde tijd uit te voeren. Als uw code de [methode Uitvoeren] overschrijft, moet deze voor onbepaalde tijd slapen. Als de methode [Uitvoeren] wordt geretourneerd, wordt de rol gerecycled.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Instelling voor onjuiste instelling Voor de verbinding met diagnosticsConnection
Als de toepassing Azure Diagnostics gebruikt, `DiagnosticsConnectionString` moet uw serviceconfiguratiebestand de configuratie-instelling opgeven. Met deze instelling moet een HTTPS-verbinding met uw opslagaccount in Azure worden opgegeven.

Controleer het `DiagnosticsConnectionString` volgende om ervoor te zorgen dat uw instelling correct is voordat u uw toepassingspakket implementeert naar Azure:  

* De `DiagnosticsConnectionString` instelling verwijst naar een geldig opslagaccount in Azure.  
  Standaard verwijst deze instelling naar het geëmuleerde opslagaccount, dus u moet deze instelling expliciet wijzigen voordat u uw toepassingspakket implementeert. Als u deze instelling niet wijzigt, wordt er een uitzondering gemaakt wanneer de rolinstantie de diagnostische monitor probeert te starten. Dit kan ertoe leiden dat de rolinstantie voor onbepaalde tijd wordt gerecycled.
* De verbindingstekenreeks is opgegeven in de volgende [indeling](../storage/common/storage-configure-connection-string.md). (Het protocol moet worden opgegeven als HTTPS.) Vervang *MyAccountName* door de naam van uw opslagaccount en *MyAccountKey* door uw toegangssleutel:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Als u uw toepassing ontwikkelt met Azure Tools voor Microsoft Visual Studio, u de eigenschapspagina's gebruiken om deze waarde in te stellen.

## <a name="exported-certificate-does-not-include-private-key"></a>Geëxporteerd certificaat bevat geen privésleutel
Als u een webrol onder TLS wilt uitvoeren, moet u ervoor zorgen dat uw geëxporteerde beheercertificaat de privésleutel bevat. Als u Windows *Certificate Manager* gebruikt om het certificaat te exporteren, selecteert u **Ja** voor de optie **Privésleutel exporteren.** Het certificaat moet worden geëxporteerd in de PFX-indeling, het enige formaat dat momenteel wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen
Bekijk meer [artikelen over probleemoplossing](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) voor cloudservices.

Bekijk meer rol recycling scenario's op [Kevin Williamson's blog serie](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Uitvoeren]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
