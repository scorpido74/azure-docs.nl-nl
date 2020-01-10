---
title: Aanbevolen procedures voor implementatie
description: Meer informatie over de belangrijkste mechanismen voor het implementeren naar Azure App Service. Zoek taalspecifieke aanbevelingen en andere voor behoud.
keywords: Azure app service, Web-app, implementeren, implementeren, pijp lijnen, bouwen
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750464"
---
# <a name="deployment-best-practices"></a>Aanbevolen procedures voor implementatie

Elk ontwikkel team heeft unieke vereisten waardoor het implementeren van een efficiënte implementatie pijplijn moeilijk is voor elke Cloud service. In dit artikel worden de drie belangrijkste onderdelen beschreven van de implementatie van App Service: implementatie bronnen, bouw pijp lijnen en implementatie mechanismen. Dit artikel heeft ook betrekking op enkele aanbevolen procedures en tips voor specifieke taal stacks.

## <a name="deployment-components"></a>Implementatie onderdelen

### <a name="deployment-source"></a>Implementatie bron

Een implementatie bron is de locatie van de toepassings code. Voor productie-apps is de implementatie bron meestal een opslag plaats die wordt gehost door versie beheer software [, zoals github, BitBucket of Azure opslag plaatsen](deploy-continuous-deployment.md). Voor ontwikkelings-en test scenario's kan de implementatie bron [een project op uw lokale computer](deploy-local-git.md)zijn. App Service biedt ook ondersteuning voor [OneDrive-en Dropbox-mappen](deploy-content-sync.md) als implementatie bronnen. Met Cloud mappen kunt u gemakkelijk aan de slag met App Service. het wordt doorgaans niet aanbevolen deze bron te gebruiken voor productie toepassingen op ondernemings niveau. 

### <a name="build-pipeline"></a>Build-pipeline

Wanneer u een implementatie bron hebt gekozen, is de volgende stap het kiezen van een build-pijp lijn. Met een build-pijp lijn wordt de bron code van de implementatie bron gelezen en wordt een reeks stappen uitgevoerd (zoals het compileren van code, het minifying van HTML en Java script, het uitvoeren van tests en het inpakken van onderdelen) om de toepassing in een uitvoer bare-status te verkrijgen. De specifieke opdrachten die worden uitgevoerd door de build-pijp lijn, zijn afhankelijk van uw taal stack. Deze bewerkingen kunnen worden uitgevoerd op een bouw server zoals Azure-pijp lijnen of lokaal worden uitgevoerd.

### <a name="deployment-mechanism"></a>Implementatie mechanisme

Het implementatie mechanisme is de actie die wordt gebruikt om uw gemaakte toepassing in de */Home/site/wwwroot* -map van uw web-app te zetten. De */wwwroot* Directory is een gekoppelde opslag locatie die wordt gedeeld door alle exemplaren van uw web-app. Wanneer het implementatie mechanisme uw toepassing in deze map plaatst, ontvangen uw instanties een melding voor het synchroniseren van de nieuwe bestanden. App Service ondersteunt de volgende implementatie mechanismen:

- Kudu-eind punten: [kudu](https://github.com/projectkudu/kudu/wiki) is het open-source hulp programma voor ontwikkel aars dat wordt uitgevoerd als een afzonderlijk proces in Windows app service, en als een tweede container in Linux app service. Kudu verwerkt doorlopende implementaties en biedt HTTP-eind punten voor implementatie, zoals zipdeploy.
- FTP en Web Deploy: door gebruik te maken van uw [site-of gebruikers referenties](deploy-configure-credentials.md)kunt u bestanden uploaden [via FTP](deploy-ftp.md) of Web Deploy. Deze mechanismen lopen niet via kudu.  

Implementatie hulpprogramma's, zoals Azure-pijp lijnen, Jenkins en editor-invoeg toepassingen, gebruiken een van deze implementatie mechanismen.

## <a name="language-specific-considerations"></a>Taalspecifieke overwegingen

### <a name="java"></a>Java

Gebruik de kudu [zipdeploy/](deploy-zip.md) API voor het implementeren van jar-toepassingen en [WARDEPLOY/](deploy-zip.md#deploy-war-file) voor War-apps. Als u Jenkins gebruikt, kunt u deze Api's rechtstreeks in uw implementatie fase gebruiken. Raadpleeg [dit artikel](../jenkins/execute-cli-jenkins-pipeline.md) voor meer informatie.

### <a name="node"></a>Knooppunt

Kudu voert standaard de stappen voor het bouwen van de knooppunt toepassing (`npm install`) uit. Als u een build-service gebruikt, zoals Azure DevOps, is de kudu-build niet nodig. Als u de kudu-build wilt uitschakelen, maakt u een app-instelling `SCM_DO_BUILD_DURING_DEPLOYMENT`met de waarde `false`.

### <a name="net"></a>.NET 

Kudu voert standaard de stappen voor het bouwen van uw .NET-toepassing (`dotnet build`) uit. Als u een build-service gebruikt, zoals Azure DevOps, is de kudu-build niet nodig. Als u de kudu-build wilt uitschakelen, maakt u een app-instelling `SCM_DO_BUILD_DURING_DEPLOYMENT`met de waarde `false`.

## <a name="other-deployment-considerations"></a>Andere overwegingen bij de implementatie

### <a name="use-deployment-slots"></a>Implementatie sleuven gebruiken

Gebruik waar mogelijk [implementatie sleuven](deploy-staging-slots.md) bij het implementeren van een nieuwe productie-build. Wanneer u een Standard App Service plan-laag of beter gebruikt, kunt u uw app implementeren in een faserings omgeving, uw wijzigingen valideren en een rook testen. Wanneer u klaar bent, kunt u uw staging-en productie-sleuven wisselen. Met de wissel bewerking worden de benodigde Workers geheten zodat deze overeenkomen met uw productie schaal, waardoor uitval tijd wordt geëlimineerd. 

### <a name="local-cache"></a>Lokale cache

Azure App Service inhoud wordt opgeslagen op Azure Storage en wordt op een duurzame manier als een inhouds share geoppereerd. Sommige apps hebben echter alleen een hoogwaardige, alleen-lezen inhouds opslag nodig die kan worden uitgevoerd met hoge Beschik baarheid. Deze apps kunnen profiteren van het gebruik van [lokale cache](overview-local-cache.md). Lokale cache wordt niet aanbevolen voor content management-sites zoals WordPress.

Gebruik altijd lokale cache in combi natie met [implementatie sleuven](deploy-staging-slots.md) om uitval tijd te voor komen. Zie [deze sectie](overview-local-cache.md#best-practices-for-using-app-service-local-cache) voor meer informatie over het samen gebruiken van deze functies.

### <a name="high-cpu-or-memory"></a>Hoge CPU of geheugen

Als uw App Service-abonnement meer dan 90% beschik bare CPU of geheugen gebruikt, kan het zijn dat de onderliggende virtuele machine problemen heeft met het verwerken van uw implementatie. Als dit gebeurt, kunt u het aantal instanties tijdelijk opschalen om de implementatie uit te voeren. Zodra de implementatie is voltooid, kunt u het aantal exemplaren terugsturen naar de vorige waarde.

Voor meer informatie over best practices gaat u naar [app service diagnostische gegevens](https://docs.microsoft.com/azure/app-service/overview-diagnostics) om te bepalen welke best practices u kunt gebruiken voor uw resource.

- Navigeer naar uw web-app in het [Azure Portal](https://portal.azure.com).
- Klik op **problemen vaststellen en oplossen** in het navigatie venster aan de linkerkant, waarmee app service diagnostische gegevens worden geopend.
- Kies de tegel start pagina voor **Best practices** .
- Klik op **Aanbevolen procedures voor Beschik baarheid & prestaties** of **Aanbevolen procedures voor optimale configuratie** om de huidige status van uw app te bekijken met betrekking tot deze aanbevolen procedures.

U kunt deze koppeling ook gebruiken om direct App Service diagnostische gegevens voor uw resource te openen: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`.
