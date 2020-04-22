---
title: Aanbevolen best practices voor implementatie
description: Meer informatie over de belangrijkste mechanismen voor implementatie naar Azure App Service. Vind taalspecifieke aanbevelingen en andere kanttekeningen.
keywords: azure app-service, web-app, implementeren, implementeren, pijplijnen, build
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 4dd959d75fd582d787e68db4a415a4a694b9cda8
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770692"
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

## <a name="use-deployment-slots"></a>Implementatiesleuven gebruiken

Gebruik waar mogelijk [implementatiesleuven](deploy-staging-slots.md) bij het implementeren van een nieuwe productiebuild. Wanneer u een standaardapp-serviceplanlaag of beter gebruikt, u uw app implementeren in een faseringsomgeving, uw wijzigingen valideren en rooktests uitvoeren. Wanneer u klaar bent, u uw faserings- en productieslots ruilen. De swapbewerking warmt de benodigde werkexemplaren op die overeenkomen met uw productieschaal, waardoor downtime wordt geëlimineerd.

### <a name="continuously-deploy-code"></a>Code continu implementeren

Als uw project branches heeft aangewezen voor testen, QA en staging, moet elk van deze branches continu worden geïmplementeerd in een faseringssleuf. (Dit staat bekend als het [Gitflow ontwerp](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) Hierdoor kunnen uw stakeholders eenvoudig de ingezette vestiging beoordelen en testen. 

Continue implementatie mag nooit worden ingeschakeld voor uw productiesleuf. In plaats daarvan moet uw productiebranch (vaak master) worden geïmplementeerd op een niet-productiesleuf. Wanneer u klaar bent om de basistak vrij te geven, verwisselt u deze in de productiesleuf. Het omzetten in productie- in plaats van implementeren in productie - voorkomt downtime en stelt u in staat om de wijzigingen terug te draaien door opnieuw te wisselen. 

![Visueel slotgebruik](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Continu containers inzetten

Voor aangepaste containers van Docker of andere containerregisters implementeert u de afbeelding in een faseringssleuf en wisselt u in productie om downtime te voorkomen. De automatisering is complexer dan code-implementatie omdat u de afbeelding naar een containerregister moet pushen en de afbeeldingstag op de webapp moet bijwerken.

Voor elke branch die u wilt implementeren in een sleuf, stelt u automatisering in om het volgende te doen op elke commit naar de branch.

1. **De afbeelding bouwen en taggen.** Tag als onderdeel van de buildpijplijn de afbeelding met de git commit-id, tijdstempel of andere identificeerbare informatie. Het is het beste om de standaard tag "latest" niet te gebruiken. Anders is het moeilijk om terug te vinden welke code momenteel wordt geïmplementeerd, wat het debuggen veel moeilijker maakt.
1. **Druk op de gelabelde afbeelding**. Zodra de afbeelding is gebouwd en getagd, duwt de pijplijn de afbeelding naar ons containerregister. In de volgende stap haalt de implementatiesleuf de gelabelde afbeelding uit het containerregister.
1. **Werk de implementatiesleuf bij met de nieuwe afbeeldingstag**. Wanneer deze eigenschap wordt bijgewerkt, wordt de site automatisch opnieuw opgestart en trekt de nieuwe containerafbeelding.

![Visueel slotgebruik](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Er zijn onderstaande voorbeelden voor gemeenschappelijke automatiseringsframeworks.

### <a name="use-azure-devops"></a>Azure DevOps gebruiken

App Service heeft [ingebouwde continue levering](deploy-continuous-deployment.md) voor containers via het Deployment Center. Navigeer naar uw app in de [Azure-portal](https://portal.azure.com/) en selecteer **Implementatiecentrum** onder **Implementatie**. Volg de instructies om uw opslagplaats en branch te selecteren. Hiermee configureert u een DevOps-build- en releasepijplijn om uw container automatisch te bouwen, te taggen en te implementeren wanneer nieuwe commits naar uw geselecteerde branch worden gepusht.

### <a name="use-github-actions"></a>GitHub-acties gebruiken

U uw containerimplementatie ook automatiseren [met GitHub Actions.](containers/deploy-container-github-action.md)  Het onderstaande werkstroombestand bouwt en tagt de container met de commit-id, duwt deze naar een containerregister en werkt de opgegeven sitesleuf bij met de nieuwe afbeeldingstag.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@master

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Andere automatiseringsproviders gebruiken

De eerder vermelde stappen zijn van toepassing op andere automatiseringshulpprogramma's zoals CircleCI of Travis CI. U moet de Azure CLI echter gebruiken om de implementatiesleuven in de laatste stap bij te werken met nieuwe afbeeldingstags. Als u de Azure CLI wilt gebruiken in uw automatiseringsscript, genereert u een Service Principal met de volgende opdracht.

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

Log in uw script `az login --service-principal`in met het verstrekken van informatie van de opdrachtgever. U `az webapp config container set` vervolgens de containernaam, tag, register-URL en registerwachtwoord instellen. Hieronder vindt u enkele handige links voor het bouwen van uw container CI-proces.

- [Inloggen op de Azure CLI op Circle CI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Taalspecifieke overwegingen

### <a name="java"></a>Java

Gebruik de Kudu [zipdeploy/](deploy-zip.md) API voor het implementeren van JAR-toepassingen en [wardeploy/](deploy-zip.md#deploy-war-file) voor WAR-apps. Als u Jenkins gebruikt, u deze API's rechtstreeks in uw implementatiefase gebruiken. Raadpleeg [dit artikel](../jenkins/execute-cli-jenkins-pipeline.md) voor meer informatie.

### <a name="node"></a>Knooppunt

Kudu voert standaard de buildstappen uit voor`npm install`uw Node-toepassing ( ). Als u een buildservice zoals Azure DevOps gebruikt, is de Kudu-build overbodig. Als u de Kudu-build wilt `SCM_DO_BUILD_DURING_DEPLOYMENT`uitschakelen, maakt `false`u een app-instelling met een waarde van .

### <a name="net"></a>.NET 

Kudu voert standaard de buildstappen uit voor`dotnet build`uw .NET-toepassing ( ). Als u een buildservice zoals Azure DevOps gebruikt, is de Kudu-build overbodig. Als u de Kudu-build wilt `SCM_DO_BUILD_DURING_DEPLOYMENT`uitschakelen, maakt `false`u een app-instelling met een waarde van .

## <a name="other-deployment-considerations"></a>Andere implementatieoverwegingen

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
