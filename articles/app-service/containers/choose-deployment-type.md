---
title: Implementatieopties voor Linux-containers
description: Kies tussen aangepaste Docker-containerimplementatie, multi-container en een ingebouwd toepassingsframework voor App Service op Linux.
keywords: azure app service, web app, linux, oss
author: msangapu-msft
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c4025e393c196fb98455bac26e94e5f252840b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687517"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Aangepaste afbeelding, multi-container of ingebouwde platformafbeelding?

[App Service op Linux](app-service-linux-intro.md) biedt drie verschillende paden om uw toepassing gepubliceerd op het web:

- **Aangepaste afbeeldingsimplementatie:** uw app 'dockeriseren' in een Docker-afbeelding die al uw bestanden en afhankelijkheden bevat in een kant-en-klaar pakket.
- **Implementatie met meerdere containers:** uw app 'dockeriseren' over meerdere containers met behulp van een configuratiebestand docker compose.
- **App-implementatie met een ingebouwde platformafbeelding:** onze ingebouwde platformafbeeldingen bevatten veelvoorkomende web-app-looptijden en -afhankelijkheden, zoals Knooppunt en PHP. Gebruik een van de [implementatiemethoden](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) van Azure App Service om uw app te implementeren in de opslag van uw web-app en gebruik vervolgens een ingebouwde platformafbeelding om deze uit te voeren.

## <a name="which-method-is-right-for-your-app"></a>Welke methode is geschikt voor uw app? 

De belangrijkste factoren om rekening mee te houden zijn:

- **Beschikbaarheid van Docker in uw ontwikkelworkflow:** Voor het ontwikkelen van aangepaste afbeeldingen is basiskennis van de Docker-ontwikkelworkflow vereist. Voor het implementeren van een aangepaste afbeelding naar een web-app moet uw aangepaste afbeelding worden gepubliceerd naar een repositoryhost zoals Docker Hub. Als u bekend bent met Docker en Docker-taken toevoegen aan uw buildworkflow, of als u uw app al publiceert als een Docker-afbeelding, is een aangepaste afbeelding vrijwel zeker de beste keuze.
- **Architectuur met meerdere lagen**: Implementeer meerdere containers, zoals een webtoepassingslaag en een API-laag, om mogelijkheden te scheiden met behulp van meerdere containers. 
- **Toepassingsprestaties:** verhoog de prestaties van uw multicontainer-app met een cachelaag zoals Redis. Selecteer meerdere containers om dit te bereiken.
- **Unieke runtime-vereisten**: De ingebouwde platformafbeeldingen zijn ontworpen om te voldoen aan de behoeften van de meeste web-apps, maar zijn beperkt in hun aanpasbaarheid. Uw app kan unieke afhankelijkheden of andere runtime-vereisten hebben die hoger zijn dan waartoe de ingebouwde afbeeldingen in staat zijn.
- **Build-vereisten:** met [continue implementatie](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)u uw app rechtstreeks vanuit de broncode op Azure aan de slag krijgen. Er is geen extern build- of publicatieproces vereist. Er is echter een limiet aan de aanpasbaarheid en [Kudu](https://github.com/projectkudu/kudu/wiki) beschikbaarheid van buildtools binnen de Kudu-implementatieengine. Uw app kan de mogelijkheden van Kudu ontgroeien naarmate deze groter wordt in zijn afhankelijkheden of vereisten voor aangepaste buildlogica.
- Vereisten voor **het lezen/schrijven van schijven**: Alle web-apps krijgen een opslagvolume toegewezen voor webinhoud. Dit volume, ondersteund door Azure Storage, `/home` wordt in het bestandssysteem van de app weergegeven. In tegenstelling tot bestanden in het containerbestandssysteem zijn bestanden in het inhoudsvolume toegankelijk voor alle schaalinstanties van een app en blijven wijzigingen bestaan in de app opnieuw opstarten. De schijflatentie van het inhoudsvolume is echter hoger en variabeler dan de latentie van het lokale containerbestandssysteem en de toegang kan worden beïnvloed door platformupgrades, ongeplande downtime en problemen met de netwerkconnectiviteit. Apps die zware alleen-lezen toegang tot inhoudsbestanden vereisen, kunnen profiteren van de aangepaste implementatie van afbeeldingen, waardoor bestanden in het afbeeldingsbestandssysteem worden geplaatst in plaats van op het inhoudsvolume.
- **Resourcegebruik bouwen:** wanneer een app vanuit de bron wordt geïmplementeerd, gebruiken de implementatiescripts die door Kudu worden uitgevoerd dezelfde reken- en opslagbronnen van het App Service Plan als de draaiende app. Grote app-implementaties kunnen meer resources of tijd verbruiken dan gewenst. In het bijzonder genereren veel implementatieworkflows zware schijfactiviteit op het inhoudsvolume van de app, die niet is geoptimaliseerd voor dergelijke activiteiten. Een aangepaste afbeelding levert alle bestanden en afhankelijkheden van uw app naar Azure in één pakket zonder extra bestandsoverdrachten of implementatieacties nodig te hebben.
- **Noodzaak voor snelle iteratie:** Dockerizing een app vereist extra build stappen. Als u wijzigingen wilt aanbrengen, moet u uw nieuwe afbeelding bij elke update naar een opslagplaats pushen. Deze updates worden vervolgens naar de Azure-omgeving getrokken. Als een van de ingebouwde containers voldoet aan de behoeften van uw app, kan het implementeren vanuit de bron een snellere ontwikkelworkflow bieden.

## <a name="next-steps"></a>Volgende stappen

Aangepaste container:
* [Een aangepaste container uitvoeren](quickstart-docker-go.md)

Meerdere containers:
* [Een app met meerdere containers maken](quickstart-multi-container.md)

De volgende artikelen helpen u aan de slag met App Service op Linux met een ingebouwde platformafbeelding:

* [.NET Core](quickstart-dotnetcore.md)
* [Php](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)