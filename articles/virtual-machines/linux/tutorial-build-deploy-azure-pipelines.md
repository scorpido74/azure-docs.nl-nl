---
title: Zelfstudie - CI/CD naar Azure VM's met Azure Pipelines
description: In deze zelfstudie leert u hoe u permanente integratie (CI) en continue implementatie (CD) van een Node.js-app inStelt op Azure VM's met behulp van Op YAML gebaseerde Azure-pijplijn.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: bb7c773d02c5da5c115af79cd9e90c78e71eb6bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76988325"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Zelfstudie: Uw app implementeren op virtuele Linux-machines in Azure met Azure DevOps-services en Azure-pijplijnen

Continuous integration (CI) en continuous deployment (CD) vormen een pijplijn waarmee je je code bouwen, vrijgeven en implementeren na elke codecommit. Dit document bevat de stappen die zijn gekoppeld aan het instellen van een CI/CD-pijplijn voor het uitvoeren van implementaties met meerdere machines met Azure Pipelines.

Azure Pipelines biedt een complete, volledig uitgeruste set CI/CD-automatiseringstools voor implementaties naar virtuele machines, zowel on-prem als in elke cloud.

In deze zelfstudie stelt u een op YAML gebaseerde CI/CD-pijplijn in om uw app te implementeren in een Azure [Pipelines-omgeving](https://docs.microsoft.com/azure/devops/pipelines/process/environments?view=azure-devops) met Virtuele Linux-machines als resources, die elk dienen als webservers om de app uit te voeren.

Procedures voor:

> [!div class="checklist"]
> * Download een voorbeeld-app.
> * Maak een op YAML gebaseerde Azure Pipelines CI-pijplijn voor het bouwen van de voorbeeld-app.
> * Een Azure Pipelines-omgeving maken voor de virtuele Azure-machines
> * Maak een cd-pijplijnvoor Azure Pipelines.
> * Handmatige en door CI geactiveerde implementaties uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint

* Meld u aan bij uw Azure**https://dev.azure.com/** DevOps Services-organisatie ( ). 
  U kunt een [gratis Azure DevOps Services-organisatie](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308) krijgen.

  > [!NOTE]
  > Zie [Verbinding maken met Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts) voor meer informatie.

*  U hebt een virtuele Linux-machine nodig voor een implementatiedoel.  Zie [Virtuele Linux-machines maken en beheren met de Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm) voor meer informatie.

*  Open de binnenkomende poort 80 voor uw virtuele machine. Zie [Netwerkbeveiligingsgroepen maken met Azure Portal](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic) voor meer informatie.

## <a name="get-your-sample-app-code"></a>Uw voorbeeld-app-code downloaden

Als u al een app in GitHub hebt die u wilt implementeren, u proberen een pijplijn voor die code te maken.

Echter, als je een nieuwe gebruiker, dan kun je een betere start met behulp van onze sample code. In dat geval, vork deze repo in GitHub:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic is een [Java Spring Boot](https://spring.io/guides/gs/spring-boot) applicatie gebouwd met behulp van [Maven](https://spring.io/guides/gs/maven/).

#### <a name="javascript"></a>[Javascript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Deze Node.js app is gebouwd via [Yeoman](https://yeoman.io/learning/index.html). Hierin is gebruikgemaakt van Express, Bower en Grunt. Daarnaast bevat deze een aantal npm-pakketten als afhankelijkheden.
> Het voorbeeld bevat ook een script waarmee Nginx wordt ingesteld en de app wordt geïmplementeerd. Deze wordt uitgevoerd op de virtuele machines. Met name met het script:
> 1. Worden Node, Nginx en PM2 geïnstalleerd.
> 2. Worden Nginx en PM2 geconfigureerd.
> 3. Wordt de Node-app gestart.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Voorwaarden voor de Linux VM

Voorbeeld-apps hierboven vermeld zijn getest op Ubuntu 16.04, en we raden u aan dezelfde versie van Linux VM te gebruiken voor deze quickstart.
Volg de hieronder beschreven extra stappen op basis van de runtimestack die voor de app wordt gebruikt.

#### <a name="java"></a>[Java](#tab/java)

- Voor het implementeren van java spring boot- en springcloudgebaseerde apps maakt u een Linux-VM in Azure met behulp van [deze](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) sjabloon, die een volledig ondersteunde op OpenJDK gebaseerde runtime biedt.
- Voor het implementeren van Java-servlets op de Tomcat-server maakt u een Linux-VM met Java 8 met behulp van [deze](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) Azure-sjabloon en [configureert u Tomcat 9.x als service.](https://tomcat.apache.org/tomcat-9.0-doc/setup.html)
- Voor het implementeren van Java EE-gebaseerde app, gebruik een Azure template om een [Linux VM + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) of een Linux VM + Java + [WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) of een [Linux VM +Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) + WildFly/JBoss 14 

#### <a name="javascript"></a>[Javascript](#tab/java-script)

Als u een javascript-app of een Node.js-app wilt installeren, hebt u een Linux-VM met Nginx-webserver nodig om de app te implementeren.
Als u nog geen Linux-vm met Nginx hebt, maakt u er nu een in Azure met behulp van de stappen in [dit voorbeeld.](/azure/virtual-machines/linux/quick-create-cli)

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Een Azure Pipelines-omgeving maken met virtuele Azure-machines

Virtuele machines kunnen worden toegevoegd als resources binnen [omgevingen](https://docs.microsoft.com/azure/devops/pipelines/process/environments) en kunnen worden gericht op implementaties met meerdere machines. Implementatiegeschiedenisweergaven binnen de omgeving bieden traceerbaarheid van VM naar de pijplijn en vervolgens naar de commit.

U een omgeving maken in de hub '**Omgevingen**' binnen de sectie**Pijplijnen.**
1.  Meld u aan bij uw Azure DevOps-organisatie en navigeer naar uw project.
2.  Navigeer in uw project naar de pagina **Pijplijnen.** Kies vervolgens **Omgevingen** en klik op **Omgeving maken**. Geef een **naam** (vereist) op voor de omgeving en een **beschrijving**.
3.  Kies **Virtuele machines** als **resource** die aan de omgeving moet worden toegevoegd en klik op **Volgende**.
4.  Kies Besturingssysteem (Windows/Linux) en **kopieer PS-registratiescript**. 
5.  Voer nu het gekopieerde script uit van een powershell-opdrachtprompt van de beheerder op elk van de doel-VM's die bij deze omgeving moeten worden geregistreerd.
    > [!NOTE]
    > - Persoonlijke toegang Token van de ingelogde gebruiker wordt vooraf ingevoegd in het script dat op dezelfde dag verloopt en het gekopieerde script daarop onbruikbaar maakt.
    > - Als uw VM al een agent heeft die erop draait, geeft u een unieke naam op voor 'agent' om zich te registreren bij de omgeving.
6.  Zodra de VM is geregistreerd, wordt deze weergegeven als een omgevingsbron onder het tabblad 'resources' van de omgeving.

    ![VM-creatie](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Als u meer VM's wilt toevoegen, u het script opnieuw bekijken en kopiëren door op 'Resource toevoegen' te klikken en 'Virtuele machines' als resource te kiezen. Dit script zou hetzelfde blijven voor alle VM's worden toegevoegd aan deze omgeving. 
8.  Elke machine werkt samen met Azure Pipelines om de implementatie van uw app te coördineren.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. U tags toevoegen aan de VM als onderdeel van het interactieve PS-registratiescript (of) u hetzelfde toevoegen/verwijderen uit de bronweergave door op de driepunten aan het einde van elke VM-bron in de bronnenweergave te klikken.

   Met de tags die u toewijst, u de implementatie beperken tot specifieke virtuele machines wanneer de omgeving wordt gebruikt in een implementatietaak. Tags zijn elk beperkt tot 256 tekens, maar er is geen limiet aan het aantal tags dat u gebruiken.

   ![VMtags VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Uw CI-buildpijplijn definiëren

U hebt een CI-buildpijplijn (continuous integration) nodig die uw webtoepassing publiceert, evenals een implementatiescript dat lokaal kan worden uitgevoerd op de Ubuntu-server. Stel een CI-buildpijplijn in op basis van de runtime die u wilt gebruiken. 

1. Meld u aan bij uw Azure DevOps-organisatie en navigeer naar uw project.

1. Navigeer in uw project naar de pagina **Pijplijnen.** Kies vervolgens de actie om een nieuwe pijplijn te maken.

1. Loop door de stappen van de wizard door **eerst GitHub** te selecteren als de locatie van uw broncode.

1. Mogelijk wordt u doorgestuurd naar GitHub om u aan te melden. Voer dan uw GitHub-referenties in.

1. Wanneer de lijst met opslagplaatsen wordt weergegeven, selecteert u de gewenste voorbeeld-app-opslagplaats.

1. Azure Pipelines analyseert uw repository en beveelt een geschikte pijplijnsjabloon aan.

#### <a name="java"></a>[Java](#tab/java)

Selecteer de **startersjabloon** en kopieer het onderstaande YAML-fragment dat uw Java-project bouwt en voert tests uit met Apache Maven:

```YAML
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

Voor meer begeleiding, volg de stappen die worden genoemd in [Bouw uw Java-app met Maven](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/java).

#### <a name="javascript"></a>[Javascript](#tab/java-script)

Selecteer de **startersjabloon** en kopieer het onderstaande YAML-fragment waarmee een algemeen Node.js-project met npm wordt gebouwd.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

Volg voor meer begeleiding de stappen in [De app Node.js bouwen met gulp.](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/javascript)

- Neem een kijkje op de pijplijn om te zien wat het doet. Zorg ervoor dat alle standaardingangen geschikt zijn voor uw code.

- Selecteer **Opslaan en uitvoeren,** selecteer **Vervolgens Rechtstreeks vastleggen in de hoofdvertakking**en kies vervolgens Opslaan en opnieuw **uitvoeren.**

- Er wordt een nieuwe run gestart. Wacht tot de run is voltooid.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Cd-stappen definiëren om te implementeren op de Linux-VM

1. Bewerk de bovenstaande pijplijn en voeg een [implementatietaak toe](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs) door te verwijzen naar de omgeving en de VM-resources die u eerder hebt met de onderstaande yaml-syntaxis:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web1
     strategy:
   ```
2. U specifieke sets virtuele machines uit de omgeving selecteren om de implementatie te ontvangen door de **tags** op te geven die u hebt gedefinieerd voor elke virtuele machine in de omgeving.
[Hier](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#deployment-job) vindt u het volledige YAML-schema voor implementatietaak.

3. U `runOnce` eithor `rolling` of als implementatiestrategie opgeven. 

   `runOnce`is de eenvoudigste implementatiestrategie waarin alle levenscyclushaken, namelijk `preDeploy` `deploy`, `routeTraffic`en `postRouteTraffic`, eenmaal worden uitgevoerd. Dan, `on:` `success` een `on:` `failure` of wordt uitgevoerd.

   Hieronder is het voorbeeld YAML fragment voor: `runOnce`
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Hieronder vindt u een voorbeeld van het YAML-fragment dat u gebruiken om een rollende strategie voor virtuele machines te definiëren en maximaal 5 doelen in elke iteratie bij te werken. `maxParallel`zal tegelijkertijd het aantal doelen bepalen dat kan worden ingezet. De selectie is verantwoordelijk voor het absolute aantal of percentage doelen dat op elk moment beschikbaar moet blijven, met uitzondering van de doelen die worden geïmplementeerd. Het wordt ook gebruikt om het succes en de foutvoorwaarden tijdens de implementatie te bepalen.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   Bij elke run van deze taak wordt `<environment name>` de implementatiegeschiedenis geregistreerd tegen de omgeving die u hebt gemaakt en de VM's hebt geregistreerd.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Voer uw pijplijn uit en ontvang traceerbaarheidsweergaven in de omgeving
Implementaties weergave van de omgeving biedt volledige traceerbaarheid van commits en werkitems, en een cross-pipeline implementatie geschiedenis per omgeving / resource.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Volgende stappen
- U [doorgaan met het aanpassen van de pijplijn die](https://docs.microsoft.com/azure/devops/pipelines/customize-pipeline) u zojuist hebt gemaakt.
- Zie [YAML-schemaverwijzing](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema)voor meer informatie over wat u nog meer doen in YAML-pijplijnen.
- Ga naar de volgende zelfstudie voor meer informatie over het implementeren van een LAMP-stack (Linux, Apache, MySQL en PHP).

> [!div class="nextstepaction"]
> [LAMP-stack implementeren](tutorial-lamp-stack.md)
