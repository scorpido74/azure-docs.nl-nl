---
title: Aanbevolen best practices voor implementatie
description: Meer informatie over de belangrijkste mechanismen voor implementatie naar Azure App Service. Vind taalspecifieke aanbevelingen en andere kanttekeningen.
keywords: azure app-service, web-app, implementeren, implementeren, pijplijnen, build
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750464"
---
# <a name="deployment-best-practices"></a>Aanbevolen procedures voor implementatie

Elk ontwikkelteam heeft unieke vereisten die het implementeren van een efficiënte implementatiepijplijn op elke cloudservice moeilijk kunnen maken. In dit artikel worden de drie belangrijkste onderdelen van implementatie naar App Service geïntroduceerd: implementatiebronnen, buildpipelines en implementatiemechanismen. Dit artikel bevat ook een aantal aanbevolen procedures en tips voor specifieke taalstapels.

## <a name="deployment-components"></a>Implementatiecomponenten

### <a name="deployment-source"></a>Implementatiebron

Een implementatiebron is de locatie van uw toepassingscode. Voor productie-apps is de implementatiebron meestal een opslagplaats die wordt gehost door software voor versiebeheer, zoals [GitHub, BitBucket of Azure Repos.](deploy-continuous-deployment.md) Voor ontwikkel- en testscenario's kan de implementatiebron [een project op uw lokale machine](deploy-local-git.md)zijn. App Service ondersteunt ook [OneDrive- en Dropbox-mappen](deploy-content-sync.md) als implementatiebronnen. Hoewel cloudmappen het gemakkelijk kunnen maken om aan de slag te gaan met App Service, wordt het meestal niet aanbevolen om deze bron te gebruiken voor productietoepassingen op bedrijfsniveau. 

### <a name="build-pipeline"></a>Build-pipeline

Zodra u een implementatiebron kiest, kiest u een buildpijplijn. Een buildpijplijn leest uw broncode uit de implementatiebron en voert een reeks stappen uit (zoals het compileren van code, het minifying HTML en JavaScript, het uitvoeren van tests en verpakkingscomponenten) om de toepassing in een runnable status te krijgen. De specifieke opdrachten die door de buildpijplijn worden uitgevoerd, zijn afhankelijk van uw taalstack. Deze bewerkingen kunnen worden uitgevoerd op een buildserver, zoals Azure Pipelines, of lokaal worden uitgevoerd.

### <a name="deployment-mechanism"></a>Implementatiemechanisme

Het implementatiemechanisme is de actie die wordt gebruikt om uw ingebouwde toepassing in de */home/site/wwwroot-map* van uw web-app te plaatsen. De */wwwroot-map* is een gemonteerde opslaglocatie die wordt gedeeld door alle exemplaren van uw web-app. Wanneer het implementatiemechanisme uw toepassing in deze map plaatst, ontvangen uw instanties een melding om de nieuwe bestanden te synchroniseren. App Service ondersteunt de volgende implementatiemechanismen:

- Kudu-eindpunten: [Kudu](https://github.com/projectkudu/kudu/wiki) is de productiviteitstool voor open-sourceontwikkelaars die wordt uitgevoerd als een afzonderlijk proces in Windows App Service en als tweede container in Linux App Service. Kudu verwerkt continue implementaties en biedt HTTP-eindpunten voor implementatie, zoals zipdeploy.
- FTP en WebDeploy: met behulp van uw [site of gebruikersreferenties](deploy-configure-credentials.md)u bestanden uploaden [via FTP](deploy-ftp.md) of WebDeploy. Deze mechanismen gaan niet via Kudu.  

Implementatietools zoals Azure Pipelines, Jenkins en editor-plugins maken gebruik van een van deze implementatiemechanismen.

## <a name="language-specific-considerations"></a>Taalspecifieke overwegingen

### <a name="java"></a>Java

Gebruik de Kudu [zipdeploy/](deploy-zip.md) API voor het implementeren van JAR-toepassingen en [wardeploy/](deploy-zip.md#deploy-war-file) voor WAR-apps. Als u Jenkins gebruikt, u deze API's rechtstreeks in uw implementatiefase gebruiken. Raadpleeg [dit artikel](../jenkins/execute-cli-jenkins-pipeline.md) voor meer informatie.

### <a name="node"></a>Knooppunt

Kudu voert standaard de buildstappen uit voor`npm install`uw Node-toepassing ( ). Als u een buildservice zoals Azure DevOps gebruikt, is de Kudu-build overbodig. Als u de Kudu-build wilt `SCM_DO_BUILD_DURING_DEPLOYMENT`uitschakelen, maakt `false`u een app-instelling met een waarde van .

### <a name="net"></a>.NET 

Kudu voert standaard de buildstappen uit voor`dotnet build`uw .Net-toepassing ( ). Als u een buildservice zoals Azure DevOps gebruikt, is de Kudu-build overbodig. Als u de Kudu-build wilt `SCM_DO_BUILD_DURING_DEPLOYMENT`uitschakelen, maakt `false`u een app-instelling met een waarde van .

## <a name="other-deployment-considerations"></a>Andere implementatieoverwegingen

### <a name="use-deployment-slots"></a>Implementatiesleuven gebruiken

Gebruik waar mogelijk [implementatiesleuven](deploy-staging-slots.md) bij het implementeren van een nieuwe productiebuild. Wanneer u een standaardapp-serviceplanlaag of beter gebruikt, u uw app implementeren in een faseringsomgeving, uw wijzigingen valideren en rooktests uitvoeren. Wanneer u klaar bent, u uw faserings- en productieslots ruilen. De swapbewerking warmt de benodigde werkexemplaren op die overeenkomen met uw productieschaal, waardoor downtime wordt geëlimineerd. 

### <a name="local-cache"></a>Lokale cache

Azure App Service-inhoud wordt opgeslagen in Azure Storage en wordt op een duurzame manier opgedoken als inhoudsshare. Sommige apps hebben echter alleen een krachtige, alleen-lezen inhoudswinkel nodig die ze met hoge beschikbaarheid kunnen uitvoeren. Deze apps kunnen profiteren van het gebruik van [lokale cache.](overview-local-cache.md) Lokale cache wordt niet aanbevolen voor sites voor inhoudsbeheer, zoals WordPress.

Gebruik altijd lokale cache in combinatie met [implementatiesleuven](deploy-staging-slots.md) om downtime te voorkomen. Zie [deze sectie](overview-local-cache.md#best-practices-for-using-app-service-local-cache) voor informatie over het samen gebruiken van deze functies.

### <a name="high-cpu-or-memory"></a>Hoge CPU of geheugen

Als uw App Service Plan meer dan 90% van de beschikbare CPU of geheugen gebruikt, kan de onderliggende virtuele machine problemen hebben met het verwerken van uw implementatie. Wanneer dit gebeurt, schaalt u het aantal instance's tijdelijk op om de implementatie uit te voeren. Zodra de implementatie is voltooid, u het aantal instance's terugzetten naar de vorige waarde.

Ga voor meer informatie over aanbevolen procedures naar [App Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) voor meer informatie over bruikbare aanbevolen procedures die specifiek zijn voor uw resource.

- Navigeer naar uw web-app in de [Azure-portal.](https://portal.azure.com)
- Klik op **Diagnosticeren en los problemen op** in de linkernavigatie, waarmee App Service Diagnostics wordt geopend.
- Kies de startpaginategel **Aanbevolen procedures.**
- Klik **op Aanbevolen procedures voor beschikbaarheid & Prestaties** of Aanbevolen procedures voor optimale **configuratie** om de huidige status van uw app met betrekking tot deze aanbevolen procedures weer te geven.

U deze koppeling ook gebruiken om App `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`Service Diagnostics direct te openen voor uw resource:.
