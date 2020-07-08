---
title: Implementatie opties voor Linux-containers
description: Bepaal tussen de implementatie van aangepaste docker-containers, meerdere containers en een ingebouwd toepassings raamwerk voor App Service op Linux.
keywords: Azure app service, Web-app, Linux, oss
author: msangapu-msft
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c4025e393c196fb98455bac26e94e5f252840b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74687517"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Aangepaste installatie kopie, meerdere containers of ingebouwde platform installatie kopie?

[App service op Linux](app-service-linux-intro.md) biedt drie verschillende paden om uw toepassing op het web te publiceren:

- **Aangepaste implementatie van installatie kopieën**: ' eenvoudig ' uw app in een docker-installatie kopie met al uw bestanden en afhankelijkheden in een Ready-to-run-pakket.
- **Implementatie met meerdere**containers: ' eenvoudig ' uw app over meerdere containers met behulp van een docker-opstel configuratie bestand.
- **App-implementatie met een ingebouwde platform installatie kopie**: onze ingebouwde platform installatie kopieën bevatten algemene Web-app-Runtimes en-afhankelijkheden, zoals node en PHP. Gebruik een van de [Azure app service implementatie methoden](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) om uw app te implementeren in de opslag ruimte van uw web-app en gebruik vervolgens een ingebouwde platform installatie kopie om deze uit te voeren.

## <a name="which-method-is-right-for-your-app"></a>Welke methode is geschikt voor uw app? 

De belangrijkste factoren die u moet overwegen:

- **Beschik baarheid van docker in uw ontwikkelings werk stroom**: voor het ontwikkelen van aangepaste installatie kopieën is basis kennis van de werk stroom docker-ontwikkeling vereist. Voor de implementatie van een aangepaste installatie kopie naar een web-app moet uw aangepaste installatie kopie worden gepubliceerd in een opslagplaats host zoals docker hub. Als u bekend bent met docker en u docker-taken aan uw build-werk stroom kunt toevoegen, of als u uw app al publiceert als een docker-installatie kopie, is een aangepaste installatie kopie bijna zeker de beste keuze.
- **Architectuur met meerdere lagen**: implementeer meerdere containers, zoals een Web Application Layer en een API-laag om de mogelijkheden te scheiden door gebruik te maken van een multi-container. 
- **Toepassings prestaties**: Verbeter de prestaties van uw app met meerdere containers met behulp van een cache-laag, zoals redis. Selecteer multi-container om dit te verzorgen.
- **Unieke runtime vereisten**: de ingebouwde platform installatie kopieën zijn ontworpen om te voldoen aan de behoeften van de meeste web-apps, maar zijn beperkt in hun aanpassings mogelijkheden. Uw app kan unieke afhankelijkheden of andere runtime vereisten hebben die groter zijn dan de ingebouwde installatie kopieën.
- **Build-vereisten**: met [continue implementatie](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)kunt u uw app rechtstreeks vanuit de bron code op Azure uitvoeren. Er is geen externe build of publicatie vereist. Er is echter een limiet voor de aanpassings mogelijkheden en beschik baarheid van hulpprogram ma's voor builds in de implementatie-engine van [kudu](https://github.com/projectkudu/kudu/wiki) . Uw app kan de mogelijkheden van kudu uitgroeien omdat deze zich in de afhankelijkheden of vereisten voor aangepaste compilatie logica verg Roten.
- **Vereisten voor lezen/schrijven van schijf**: alle web-apps hebben een opslag volume voor webinhoud toegewezen. Dit volume, dat wordt ondersteund door Azure Storage, is gekoppeld aan `/home` in het bestands systeem van de app. In tegens telling tot bestanden in het bestands systeem van de container zijn bestanden in het inhouds volume toegankelijk via alle schaal instanties van een app en worden wijzigingen behouden in de app die opnieuw wordt gestart. De schijf latentie van het inhouds volume is echter hoger en meer dan de latentie van het lokale container bestands systeem en de toegang kan worden beïnvloed door platform upgrades, ongeplande uitval tijd en problemen met de netwerk verbinding. Apps waarvoor zware alleen-lezen toegang tot inhouds bestanden is vereist, kunnen profiteren van de aangepaste implementatie van installatie kopieën, waarmee bestanden worden geplaatst in het bestands systeem van de installatie kopie in plaats van op het inhouds volume.
- **Resource gebruik samen stellen**: wanneer een app vanuit de bron wordt geïmplementeerd, gebruiken de implementatie scripts die worden uitgevoerd door kudu, dezelfde app service om reken-en opslag resources te plannen als de actieve app. Grote app-implementaties nemen mogelijk meer resources of tijd in beslag dan gewenst. Met name een groot aantal implementatie werk stromen genereren zware schijf activiteit op het volume van de app-inhoud, wat niet is geoptimaliseerd voor deze activiteit. Een aangepaste installatie kopie levert alle bestanden en afhankelijkheden van uw app in Azure in één pakket zonder dat hiervoor extra bestands overdrachten of implementatie acties nodig zijn.
- **Behoefte aan snelle herhaling**: Dockerizing voor een app zijn aanvullende build-stappen vereist. Als u de wijzigingen wilt door voeren, moet u de nieuwe installatie kopie naar een opslag plaats met elke update pushen. Deze updates worden vervolgens opgehaald naar de Azure-omgeving. Als een van de ingebouwde containers voldoet aan de behoeften van uw app, kan de implementatie vanaf bron een snellere Ontwikkel werk stroom bieden.

## <a name="next-steps"></a>Volgende stappen

Aangepaste container:
* [Een aangepaste container uitvoeren](quickstart-docker-go.md)

Meerdere containers:
* [Een app met meerdere containers maken](quickstart-multi-container.md)

Met de volgende artikelen kunt u aan de slag met App Service op Linux met een ingebouwde platform installatie kopie:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)