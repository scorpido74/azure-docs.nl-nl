---
title: HTTP 502- en HTTP 503-fouten oplossen
description: Problemen met 502 foute gateway- en 503-servicefouten oplossen die niet beschikbaar zijn in uw app die worden gehost in Azure App Service.
tags: top-support-issue
keywords: 502 slechte gateway, 503 service niet beschikbaar, fout 503, fout 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 9345b6fb28aa282e85f1167f6f2531e5f990e3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688328"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>HTTP-fouten van '502 slechte gateway' en '503-service niet beschikbaar' in Azure App Service oplossen
'502 slechte gateway' en '503-service niet beschikbaar' zijn veelvoorkomende fouten in uw app die wordt gehost in [Azure App Service.](https://go.microsoft.com/fwlink/?LinkId=529714) In dit artikel u deze fouten oplossen.

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de MSDN Azure- en de Stack Overflow-forums.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure Support-site](https://azure.microsoft.com/support/options/) en klik op **Ondersteuning krijgen**.

## <a name="symptom"></a>Symptoom
Wanneer u naar de app bladert, wordt een HTTP-fout "502 Bad Gateway" of een HTTP-fout '503-service niet beschikbaar' geretourneerd.

## <a name="cause"></a>Oorzaak
Dit probleem wordt vaak veroorzaakt door problemen op toepassingsniveau, zoals:

* verzoeken die lang duren
* toepassing met behulp van een hoog geheugen /CPU
* toepassing crashen als gevolg van een uitzondering.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Stappen oplossen om fouten in '502 slechte gateway' en '503-service niet beschikbaar' op te lossen
Probleemoplossing kan worden onderverdeeld in drie verschillende taken, in opeenvolgende volgorde:

1. [Toepassingsgedrag observeren en controleren](#observe)
2. [Gegevens verzamelen](#collect)
3. [Het probleem beperken](#mitigate)

[App Service](overview.md) biedt u verschillende opties bij elke stap.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Het gedrag van toepassingen observeren en controleren
#### <a name="track-service-health"></a>De status van de service bijhouden
Microsoft Azure maakt elke keer bekend wanneer er een onderbreking van de service of prestatiedegradatie is. U de status van de service volgen op de [Azure Portal.](https://portal.azure.com/) Zie [De status van de service bijhouden](../monitoring-and-diagnostics/insights-service-health.md)voor meer informatie.

#### <a name="monitor-your-app"></a>Uw app controleren
Met deze optie u zien of uw toepassing problemen heeft. Klik in het blad van uw app op de tegel **Aanvragen en fouten.** Het **metrische** blad toont u alle statistieken die u toevoegen.

Enkele van de statistieken die u mogelijk wilt controleren voor uw app, zijn

* Gemiddelde geheugenwerkset
* Gemiddelde responstijd
* CPU-tijd
* Geheugenwerkset
* Aanvragen

![monitor app naar het oplossen van HTTP-fouten van 502 slechte gateway en 503 service niet beschikbaar](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Zie voor meer informatie:

* [Apps controleren in Azure App Service](web-sites-monitor.md)
* [Waarschuwingen ontvangen](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Gegevens verzamelen
#### <a name="use-the-diagnostics-tool"></a>Het diagnosegereedschap gebruiken
App Service biedt een intelligente en interactieve ervaring om u te helpen problemen op te lossen met uw app zonder configuratie vereist. Wanneer u problemen met uw app tegenkomt, geeft de diagnostische tool aan wat er mis is om u naar de juiste informatie te leiden om het probleem gemakkelijker en sneller op te lossen en op te lossen.

Als u toegang wilt krijgen tot diagnostische gegevens van App Service, navigeert u naar uw App Service-app of App Service-omgeving in de [Azure-portal.](https://portal.azure.com) Klik in de linkernavigatie op **Diagnosticeren en los problemen op.**

#### <a name="use-the-kudu-debug-console"></a>De Kudu-foutopsporingsconsole gebruiken
App Service wordt geleverd met een foutopsporingsconsole die u gebruiken voor het debuggen, verkennen, uploaden van bestanden, evenals JSON-eindpunten voor het verkrijgen van informatie over uw omgeving. Dit wordt de *Kudu-console* of het *SCM-dashboard* voor uw app genoemd.

U dit dashboard openen door naar de link te gaan **https://&lt;Uw app-naam>.scm.azurewebsites.net/**.

Enkele van de dingen die Kudu biedt zijn:

* omgevingsinstellingen voor uw toepassing
* logboekstroom
* diagnostische dump
* foutopsporingsconsole waarin u Powershell-cmdlets en eenvoudige DOS-opdrachten uitvoeren.

Een ander handig kenmerk van Kudu is dat, in het geval uw toepassing is het gooien van eerste kans uitzonderingen, u Kudu en de SysInternals tool Procdump gebruiken om geheugen dumps te maken. Deze geheugendumps zijn momentopnamen van het proces en kunnen u vaak helpen bij het oplossen van meer gecompliceerde problemen met uw app.

Zie online tools voor [Azure Websites die u moet kennen](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)voor meer informatie over functies die beschikbaar zijn in Kudu.

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Het probleem beperken
#### <a name="scale-the-app"></a>De app schalen
In Azure App Service u voor betere prestaties en doorvoer de schaal aanpassen waarop u uw toepassing uitvoert. Het opschalen van een app omvat twee gerelateerde acties: het wijzigen van uw App Service-abonnement naar een hogere prijscategorie en het configureren van bepaalde instellingen nadat u bent overgestapt naar de hogere prijscategorie.

Zie [Een app schalen in Azure App Service](manage-scale-up.md)voor meer informatie over schalen.

Bovendien u ervoor kiezen om uw toepassing op meer dan één exemplaar uit te voeren. Dit biedt u niet alleen meer verwerkingsmogelijkheden, maar geeft u ook een zekere mate van fouttolerantie. Als het proces op de ene instantie wordt afgebroken, wordt in de andere instantie nog steeds aanvragen weergegeven.

U de schaling instellen als Handmatig of automatisch.

#### <a name="use-autoheal"></a>AutoHeal gebruiken
AutoHeal recyclet het werkproces voor uw app op basis van instellingen die u kiest (zoals configuratiewijzigingen, aanvragen, limieten op basis van geheugen of de tijd die nodig is om een aanvraag uit te voeren). De meeste van de tijd, recyclen van het proces is de snelste manier om te herstellen van een probleem. Hoewel u de app altijd rechtstreeks vanuit de Azure Portal opnieuw starten, doet AutoHeal dit automatisch voor u. Het enige wat u hoeft te doen is een aantal triggers toevoegen in de root web.config voor uw app. Houd er rekening mee dat deze instellingen op dezelfde manier zouden werken, zelfs als uw toepassing geen .NET-toepassing is.

Zie [Azure-websites voor](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)meer informatie .

#### <a name="restart-the-app"></a>De app opnieuw starten
Dit is vaak de eenvoudigste manier om te herstellen van eenmalige problemen. Op de [Azure Portal](https://portal.azure.com/), op het blad van uw app, hebt u de opties om uw app te stoppen of opnieuw te starten.

 ![herstart app om HTTP-fouten van 502 slechte gateway en 503-service niet beschikbaar op te lossen](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

U uw app ook beheren met Azure Powershell. Zie [Azure PowerShell gebruiken met Azure Resource Manager](../powershell-azure-resource-manager.md) voor meer informatie.

