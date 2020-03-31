---
title: Veelvoorkomende fouten in codepakketten diagnosticeren met Service Fabric
description: Meer informatie over het oplossen van veelvoorkomende fouten in codepakketten met Azure Service Fabric
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 344fef70522240da2236a020c96308c472c9c545
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463113"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Veelvoorkomende fouten in codepakketten diagnosticeren met Service Fabric

In dit artikel wordt beschreven wat het betekent dat een codepakket onverwacht wordt beëindigd. Het biedt inzicht in mogelijke oorzaken van veelvoorkomende foutcodes, samen met stappen voor het oplossen van problemen.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Wanneer eindigt een proces of container onverwacht?

Wanneer Azure Service Fabric een verzoek ontvangt om een codepakket te starten, begint het de omgeving op het lokale systeem voor te bereiden op basis van de opties die zijn ingesteld in de app- en servicemanifesten. Deze voorbereidingen kunnen bestaan uit het reserveren van netwerkeindpunten of -resources, het configureren van firewallregels of het instellen van beperkingen voor resourcegovernance. 

Nadat de omgeving goed is geconfigureerd, probeert Service Fabric het codepakket ter sprake te brengen. Deze stap wordt als geslaagd beschouwd als de runtime van het besturingssysteem of de container meldt dat het proces of de container is geactiveerd. Als activering mislukt, ziet u een gezondheidsbericht in SFX dat lijkt op het volgende:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Nadat het codepakket met succes is geactiveerd, begint Service Fabric de levensduur ervan te controleren. Op dit moment kan een proces of container op elk moment om een aantal redenen worden beëindigd. Het kan bijvoorbeeld zijn dat het niet gelukt is om een DLL te initialiseren of het besturingssysteem kan zonder ruimte op de bureaubladheap zijn gekomen. Als uw codepakket is beëindigd, ziet u het volgende gezondheidsbericht in SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

De exitcode in dit gezondheidsbericht is de enige aanwijzing die het proces of de container biedt over waarom het is beëindigd. Het kan worden gegenereerd door elk niveau van de stapel. Deze exitcode kan bijvoorbeeld gerelateerd zijn aan een OS-fout of een .NET-probleem, of deze is mogelijk verhoogd door uw code. Gebruik dit artikel als uitgangspunt voor het diagnosticeren van de bron van beëindigingsuitgangscodes en mogelijke oplossingen. Houd er echter rekening mee dat dit algemene oplossingen zijn voor veelvoorkomende scenario's en mogelijk niet van toepassing zijn op de fout die u ziet.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Hoe weet ik of Service Fabric mijn codepakket heeft beëindigd?

Service Fabric kan om verschillende redenen verantwoordelijk zijn voor het beëindigen van uw codepakket. Het kan bijvoorbeeld besluiten om het codepakket op een ander knooppunt te plaatsen voor taakverdelingsdoeleinden. U controleren of Service Fabric uw codepakket heeft beëindigd als u een van de exitcodes in de volgende tabel ziet.

>[!NOTE]
> Als uw proces of container wordt beëindigd met een andere exitcode dan de codes in de volgende tabel, is Service Fabric niet verantwoordelijk voor het beëindigen ervan.

Afsluitcode | Beschrijving
--------- | -----------
7147 | Hiermee geeft u aan dat Service Fabric het proces of de container op een elegante manier afsluit door het een Ctrl+C-signaal te verzenden.
7148 | Geeft aan dat Service Fabric het proces of de container heeft beëindigd. Soms geeft deze foutcode aan dat het proces of de container niet tijdig reageerde nadat het een Ctrl+C-signaal was verzonden en dat het moest worden beëindigd.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Andere veelvoorkomende foutcodes en hun potentiële oplossingen

Afsluitcode | Hexadecimale waarde | Korte beschrijving | Hoofdoorzaak | Potentiële oplossing
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Deze fout betekent soms dat de machine geen ruimte meer heeft op het bureaublad. Deze oorzaak is vooral waarschijnlijk als u tal van processen die behoren tot uw toepassing draait op het knooppunt. | Als uw programma niet is gebouwd om te reageren op Ctrl+C-signalen, u de instelling **EnableActivateNoWindow** inschakelen in het clustermanifest inschakelen. Als u deze instelling inschakelt, wordt uw codepakket uitgevoerd zonder GUI-venster en ontvangt u geen Ctrl+C-signalen. Deze actie vermindert ook de hoeveelheid ruimte op de bureaubladhoop die elk proces verbruikt. Als uw codepakket Ctrl+C-signalen moet ontvangen, u de grootte van de bureaubladheap van uw knooppunt vergroten.
3762504530 | 0xe0434352 | N.v.t. | Deze waarde vertegenwoordigt de foutcode voor een niet-verwerkte uitzondering van beheerde code (dat wil zeggen .NET). | Deze afsluitcode geeft aan dat uw toepassing een uitzondering heeft gemaakt die niet is afgehandeld en die het proces heeft beëindigd. Als eerste stap bij het bepalen van wat deze fout heeft veroorzaakt, debugt u de logboeken van uw toepassing en dumpbestanden.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het diagnosticeren van andere veelvoorkomende scenario's](service-fabric-diagnostics-common-scenarios.md).
* Krijg een gedetailleerder overzicht van Azure Monitor-logboeken en wat ze bieden door het overzicht van [Azure Monitor te](../operations-management-suite/operations-management-suite-overview.md)lezen.
* Meer informatie over Azure Monitor-logboeken [die waarschuwen](../log-analytics/log-analytics-alerts.md) voor hulp bij detectie en diagnose.
* Maak kennis met de [zoek- en queryfuncties voor logboeken](../log-analytics/log-analytics-log-searches.md) die worden aangeboden als onderdeel van Azure Monitor-logboeken.
