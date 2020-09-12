---
title: Veelvoorkomende oorzaken voor het recyclen van Cloud service rollen | Microsoft Docs
description: Een Cloud service functie die plotseling kan worden gerecycled, kan aanzienlijke uitval tijd veroorzaken. Hier volgen enkele veelvoorkomende problemen die ertoe leiden dat rollen worden gerecycled, zodat u downtime kunt verlagen.
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
ms.openlocfilehash: 4bf33db28d68b600d860a132027e5be440377b34
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460283"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Algemene problemen waardoor rollen worden herhaald
In dit artikel worden enkele veelvoorkomende oorzaken van implementatie problemen beschreven en tips voor het oplossen van problemen die u kunnen helpen bij het oplossen van deze problemen. Een indicatie dat er een probleem is met een toepassing is wanneer de rolinstantie niet kan worden gestart, of wordt gerecycled tussen de status initialisatie, bezet en gestopt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Ontbrekende runtime-afhankelijkheden
Als een rol in uw toepassing afhankelijk is van een assembly die geen deel uitmaakt van de .NET Framework of de door Azure beheerde bibliotheek, moet u deze assembly expliciet opnemen in het toepassings pakket. Denk eraan dat andere micro soft Frameworks niet standaard beschikbaar zijn in Azure. Als uw rol afhankelijk is van een dergelijk Framework, moet u deze assembly's toevoegen aan het toepassings pakket.

Controleer het volgende voordat u uw toepassing bouwt en inpakt:

* Als u Visual Studio gebruikt, moet u ervoor zorgen dat de **lokale eigenschap Copy** is ingesteld op **True** voor elke assembly waarnaar wordt verwezen in uw project die geen deel uitmaakt van de Azure SDK of de .NET Framework.
* Zorg ervoor dat het web.config bestand niet verwijst naar ongebruikte assembly's in het compilatie-element.
* De **Build-actie** van elk. cshtml-bestand is ingesteld op **inhoud**. Dit zorgt ervoor dat de bestanden correct worden weer gegeven in het pakket en dat andere bestanden waarnaar wordt verwezen, kunnen worden weer gegeven in het pakket.

## <a name="assembly-targets-wrong-platform"></a>Assembly doelen onjuist platform
Azure is een 64-bits omgeving. Daarom werken .NET-assembly's die zijn gecompileerd voor een 32-bits doel niet in Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>De rol genereert niet-verwerkte uitzonde ringen tijdens het initialiseren of stoppen
Uitzonde ringen die worden veroorzaakt door de methoden van de klasse [RoleEntryPoint] , die de methoden [onstart], [OnStop]en [Run] bevatten, zijn niet-verwerkte uitzonde ringen. Als er een onverwerkte uitzonde ring optreedt in een van deze methoden, wordt de rol opnieuw gerecycled. Als de rol herhaaldelijk wordt gerecycled, wordt er een onverwerkte uitzonde ring gegenereerd telkens wanneer deze wordt gestart.

## <a name="role-returns-from-run-method"></a>Rol wordt geretourneerd vanuit run-methode
De methode [Run] is bedoeld om voor onbepaalde tijd te worden uitgevoerd. Als uw code de methode [Run] overschrijft, moet de slaap stand voor onbepaalde tijd worden ingeschakeld. Als de [Run] -methode retourneert, wordt de rol gerecycled.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Onjuiste DiagnosticsConnectionString-instelling
Als de toepassing Azure Diagnostics gebruikt, moet uw service configuratie bestand de `DiagnosticsConnectionString` configuratie-instelling opgeven. Met deze instelling moet een HTTPS-verbinding met uw opslag account in Azure worden opgegeven.

Controleer het volgende om ervoor te zorgen dat uw `DiagnosticsConnectionString` instelling juist is voordat u het toepassings pakket implementeert in Azure:  

* De `DiagnosticsConnectionString` instelling wijst naar een geldig opslag account in Azure.  
  Deze instelling verwijst standaard naar het geëmuleerde opslag account, dus u moet deze instelling expliciet wijzigen voordat u het toepassings pakket implementeert. Als u deze instelling niet wijzigt, wordt er een uitzonde ring gegenereerd wanneer de rolinstantie de diagnostische monitor probeert te starten. Dit kan ertoe leiden dat de rolinstantie voor onbepaalde tijd wordt gerecycled.
* De connection string is opgegeven in de volgende [indeling](../storage/common/storage-configure-connection-string.md). (Het protocol moet worden opgegeven als HTTPS.) Vervang *MyAccountName* door de naam van uw opslag account en *MyAccountKey* met uw toegangs sleutel:    

```console
DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey
```

  Als u uw toepassing ontwikkelt met behulp van Azure-Hulpprogram Ma's voor micro soft Visual Studio, kunt u de eigenschappen pagina's gebruiken om deze waarde in te stellen.

## <a name="exported-certificate-does-not-include-private-key"></a>Het geëxporteerde certificaat bevat geen persoonlijke sleutel
Als u een webrole onder TLS wilt uitvoeren, moet u ervoor zorgen dat het geëxporteerde beheer certificaat de persoonlijke sleutel bevat. Als u *Windows certificaat beheer* gebruikt om het certificaat te exporteren, moet u **Ja** selecteren voor de optie **de persoonlijke sleutel exporteren** . Het certificaat moet worden geëxporteerd in de PFX-indeling. Dit is de enige indeling die momenteel wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen
Bekijk meer [artikelen over probleem oplossing](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) voor Cloud Services.

Bekijk meer scenario's voor het recyclen van rollen in [de blog serie van Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Uitvoeren]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
