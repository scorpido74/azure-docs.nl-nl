---
title: Zelf studie-CI/CD naar Azure Vm's met behulp van Azure-pijp lijnen
description: In deze zelf studie leert u hoe u doorlopende integratie (CI) en doorlopende implementatie (CD) van een node. js-app naar Azure Vm's kunt instellen met behulp van YAML Azure-pijp lijn.
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
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988325"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Zelf studie: uw app implementeren op virtuele Linux-machines in azure met behulp van Azure DevOps Services en Azure-pijp lijnen

Doorlopende integratie (CI) en doorlopende implementatie (CD) vormen een pijp lijn waarmee u uw code kunt bouwen, vrijgeven en implementeren nadat elke code is doorgevoerd. Dit document bevat de stappen die zijn gekoppeld aan het instellen van een CI/CD-pijp lijn voor het uitvoeren van implementaties met meerdere machines met behulp van Azure-pijp lijnen.

Azure-pijp lijnen bieden een volledige, volledig uitgeruste set hulpprogram ma's voor het automatiseren van CI/CD voor implementaties op virtuele machines, zowel on-premises als in elke Cloud.

In deze zelf studie stelt u een op YAML gebaseerde CI/CD-pijp lijn in om uw app te implementeren in een Azure pipelines- [omgeving](https://docs.microsoft.com/azure/devops/pipelines/process/environments?view=azure-devops) met virtuele Linux-machines als bronnen, die allemaal fungeren als webservers om de app uit te voeren.

Procedures voor:

> [!div class="checklist"]
> * Een voor beeld-app ophalen.
> * Maak een op YAML gebaseerde Azure pipelines CI-pijp lijn voor het bouwen van de voor beeld-app.
> * Een Azure-pipeline-omgeving maken voor de virtuele machines van Azure
> * Een CD-pijp lijn met Azure-pijp lijnen maken.
> * Handmatige en door CI geactiveerde implementaties uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint

* Meld u aan bij uw Azure DevOps Services-organisatie ( **https://dev.azure.com/** ). 
  U kunt een [gratis Azure DevOps Services-organisatie](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308) krijgen.

  > [!NOTE]
  > Zie [Verbinding maken met Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts) voor meer informatie.

*  U hebt een virtuele Linux-machine nodig voor een implementatiedoel.  Zie [Virtuele Linux-machines maken en beheren met de Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm) voor meer informatie.

*  Open de binnenkomende poort 80 voor uw virtuele machine. Zie [Netwerkbeveiligingsgroepen maken met Azure Portal](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic) voor meer informatie.

## <a name="get-your-sample-app-code"></a>Uw voor beeld-app-code ophalen

Als u al een app in GitHub hebt die u wilt implementeren, kunt u proberen een pijp lijn voor die code te maken.

Als u echter een nieuwe gebruiker bent, kunt u aan de slag met de voorbeeld code. In dat geval splitst u deze opslag plaats in GitHub:

#### <a name="javatabjava"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic is een [Java Spring boot](https://spring.io/guides/gs/spring-boot) -toepassing die is gebouwd met behulp van [maven](https://spring.io/guides/gs/maven/).

#### <a name="javascripttabjava-script"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Deze node. js-app is gebouwd via [Yeoman](https://yeoman.io/learning/index.html). Hierin is gebruikgemaakt van Express, Bower en Grunt. Daarnaast bevat deze een aantal npm-pakketten als afhankelijkheden.
> Het voorbeeld bevat ook een script waarmee Nginx wordt ingesteld en de app wordt geïmplementeerd. Deze wordt uitgevoerd op de virtuele machines. Met name met het script:
> 1. Worden Node, Nginx en PM2 geïnstalleerd.
> 2. Worden Nginx en PM2 geconfigureerd.
> 3. Wordt de Node-app gestart.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Vereisten voor de virtuele Linux-machine

Voor beeld-apps die hierboven worden vermeld, zijn getest op Ubuntu 16,04. u wordt aangeraden dezelfde versie van de Linux-VM te gebruiken voor deze Quick Start.
Volg de aanvullende stappen die hieronder worden beschreven, op basis van de runtime stack die voor de app wordt gebruikt.

#### <a name="javatabjava"></a>[Java](#tab/java)

- Voor het implementeren van Java Spring-en lente-Cloud-apps, maakt u een virtuele Linux-machine in azure met behulp van [deze](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) sjabloon. Dit biedt een volledig ondersteunde openjdk-runtime.
- Voor het implementeren van Java-Servlets op Tomcat-server maakt u een virtuele Linux-machine met Java 8 met behulp van [deze](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) Azure-sjabloon en [configureert u Tomcat 9. x as a Service](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- Voor het implementeren van op Java EE gebaseerde app gebruikt u een Azure-sjabloon voor het maken van een [virtuele Linux-machine + Java + WebSphere 9. x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) of een Linux-VM [+ Java + WebLogic 12.](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) x of een [Linux-VM +](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) Java + WildFly/JBoss 14 

#### <a name="javascripttabjava-script"></a>[JavaScript](#tab/java-script)

Als u een Java script-app of een node. js-app wilt installeren, hebt u een virtuele Linux-machine met nginx web server nodig om de app te implementeren.
Als u nog geen virtuele Linux-machine met nginx hebt, maakt u er nu in azure met behulp van de stappen in [dit voor beeld](/azure/virtual-machines/linux/quick-create-cli).

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Een Azure pipeline-omgeving maken met Azure virtual machines

Virtuele machines kunnen worden toegevoegd als resources in [omgevingen](https://docs.microsoft.com/azure/devops/pipelines/process/environments) en kunnen worden gericht op implementaties met meerdere machines. Implementatie geschiedenis weergaven in een omgeving bieden traceer baarheid van de VM naar de pijp lijn en vervolgens naar de door voer.

U kunt een omgeving maken in de hub '**omgevingen**' in de sectie '**pijp lijnen**'.
1.  Meld u aan bij uw Azure DevOps-organisatie en navigeer naar uw project.
2.  Navigeer in uw project naar de pagina **pijp lijnen** . Kies vervolgens **omgevingen** en klik op **omgeving maken**. Geef een **naam** (vereist) voor de omgeving en een **Beschrijving**op.
3.  Kies **virtual machines** als **resource** die moet worden toegevoegd aan de omgeving en klik op **volgende**.
4.  Kies besturings systeem (Windows/Linux) en **Kopieer het PS-registratie script**. 
5.  Voer nu het gekopieerde script uit vanuit een Power shell-opdracht prompt met beheerders rechten op elk van de doel-Vm's die in deze omgeving moeten worden geregistreerd.
    > [!NOTE]
    > - Het persoonlijke toegangs token van de aangemelde gebruiker wordt vooraf ingevoegd in het script dat op dezelfde dag verloopt, waardoor het gekopieerde script onbruikbaar wordt gemaakt.
    > - Als op uw virtuele machine al een agent wordt uitgevoerd, geeft u een unieke naam op voor de agent om te registreren bij de omgeving.
6.  Zodra de VM is geregistreerd, wordt deze weer gegeven als een omgevings bron op het tabblad resources van de omgeving.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Als u meer Vm's wilt toevoegen, kunt u het script opnieuw weer geven en kopiëren door te klikken op ' resource toevoegen ' en ' Virtual Machines ' als resource te kiezen. Dit script blijft hetzelfde voor alle Vm's die moeten worden toegevoegd aan deze omgeving. 
8.  Elke computer communiceert met Azure-pijp lijnen voor het coördineren van de implementatie van uw app.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. U kunt labels toevoegen aan de virtuele machine als onderdeel van het interactieve PS-registratie script (of) u kunt deze ook toevoegen aan/verwijderen uit de resource weergave door te klikken op de drie puntjes aan het einde van elke VM-resource in de weer gave resources.

   Met de labels die u toewijst kunt u de implementatie beperken tot specifieke virtuele machines wanneer de omgeving wordt gebruikt in een implementatie taak. Labels zijn allemaal beperkt tot 256 tekens, maar er is geen limiet voor het aantal tags dat u kunt gebruiken.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Uw CI-build-pijp lijn definiëren

U hebt een pijp lijn voor continue integratie (CI) nodig die uw webtoepassing publiceert, evenals een implementatie script dat lokaal kan worden uitgevoerd op de Ubuntu-Server. Stel een CI-pijp lijn in op basis van de runtime die u wilt gebruiken. 

1. Meld u aan bij uw Azure DevOps-organisatie en navigeer naar uw project.

1. Navigeer in uw project naar de pagina **pijp lijnen** . Kies vervolgens de actie om een nieuwe pijp lijn te maken.

1. Door loop de stappen van de wizard door eerst **github** te selecteren als de locatie van de bron code.

1. U wordt mogelijk omgeleid naar GitHub om u aan te melden. Als dit het geval is, voert u uw GitHub-referenties in.

1. Wanneer de lijst met opslag plaatsen wordt weer gegeven, selecteert u de gewenste opslag plaats voor de voor beeld-app.

1. Azure-pijp lijnen analyseren uw opslag plaats en raden een geschikte pijplijn sjabloon aan.

#### <a name="javatabjava"></a>[Java](#tab/java)

Selecteer de sjabloon **Start** en kopieer het onderstaande YAML-fragment dat uw Java-project bouwt en voert tests uit met Apache Maven:

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

Volg de stappen in [uw Java-app bouwen met maven](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/java)voor meer informatie.

#### <a name="javascripttabjava-script"></a>[JavaScript](#tab/java-script)

Selecteer de **Start** sjabloon en kopieer het onderstaande YAML-fragment dat een algemeen node. js-project bouwt met NPM.

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

Volg de stappen in [uw node. js-app bouwen met Gulp](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/javascript)voor meer informatie.

- Bekijk de pijp lijn om te zien wat het doet. Zorg ervoor dat alle standaard invoer geschikt zijn voor uw code.

- Selecteer **opslaan en uitvoeren**, selecteer **rechtstreeks door voeren naar de hoofd vertakking**en kies vervolgens **opslaan en opnieuw uitvoeren** .

- Er wordt een nieuwe uitvoering gestart. Wacht totdat de uitvoering is voltooid.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>CD-stappen definiëren voor de implementatie van de virtuele Linux-machine

1. Bewerk de bovenstaande pijp lijn en voeg een [implementatie taak](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs) toe door te verwijzen naar de omgeving en de VM-bronnen die u eerder met de yaml-syntaxis hebt gebruikt:

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
2. U kunt specifieke sets virtuele machines selecteren in de omgeving om de implementatie te ontvangen door de **Tags** op te geven die u voor elke virtuele machine in de omgeving hebt gedefinieerd.
[Dit](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#deployment-job) is het volledige YAML-schema voor de implementatie taak.

3. U kunt eithor `runOnce` of `rolling` als implementatie strategie opgeven. 

   `runOnce` is de eenvoudigste implementatie strategie waarbij alle levens cyclus hooks, te weten `preDeploy` `deploy`, `routeTraffic`en `postRouteTraffic`, eenmaal worden uitgevoerd. Een `on:` `success` of `on:` `failure` wordt uitgevoerd.

   Hieronder ziet u het voorbeeld fragment YAML voor `runOnce`:
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

4. Hieronder ziet u een voor beeld van het YAML-fragment dat u kunt gebruiken voor het definiëren van een rollen strategie voor virtuele machines die zijn bijgewerkt tot 5 doelen in elke iteratie. `maxParallel` bepaalt het aantal doelen dat parallel kan worden geïmplementeerd. De selectie accounts voor een absoluut getal of percentage van doelen die op elk gewenst moment beschikbaar moeten blijven, met uitzonde ring van de doelen waarop wordt geïmplementeerd. Het wordt ook gebruikt om de geslaagde en fout situaties tijdens de implementatie te bepalen.

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

   Bij elke uitvoering van deze taak wordt de implementatie geschiedenis vastgelegd in de `<environment name>` omgeving dat u de Vm's hebt gemaakt en geregistreerd.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Uw pijp lijn uitvoeren en tracerings weergaven in de omgeving ophalen
De weer gave van implementaties van de omgeving biedt volledige traceer baarheid van door voeringen en werk items en een implementatie geschiedenis van meerdere pijp lijnen per omgeving/resource.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Volgende stappen
- U kunt door gaan met [het aanpassen van de pijp lijn](https://docs.microsoft.com/azure/devops/pipelines/customize-pipeline) die u zojuist hebt gemaakt.
- Zie [YAML-schema referentie](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema)voor meer informatie over wat u kunt doen in YAML-pijp lijnen.
- Ga naar de volgende zelfstudie voor meer informatie over het implementeren van een LAMP-stack (Linux, Apache, MySQL en PHP).

> [!div class="nextstepaction"]
> [LAMP-stack implementeren](tutorial-lamp-stack.md)
