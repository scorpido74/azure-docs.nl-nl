---
title: Implementeren naar Azure App Service met behulp van de Jenkins-invoegtoepassing
description: Leer hoe u de Jenkins-invoegtoepassing voor Azure App Service kunt gebruiken om een Java-web-app te implementeren naar Azure in Jenkins
keywords: jenkins, azure, devops, app service
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: de1bf0ea06210c86ff1da21dcac667754f11d7f4
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158515"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Implementeren naar Azure App Service met behulp van de Jenkins-invoegtoepassing 

Om een Java-web-app te implementeren naar Azure, kunt u de Azure CLI in [Jenkins Pipeline](/azure/jenkins/execute-cli-jenkins-pipeline) of de [Jenkins-invoegtoepassing voor Azure App Service](https://plugins.jenkins.io/azure-app-service) gebruiken. De Jenkins-invoegtoepassing versie 1.0 ondersteunt doorlopende implementatie door middel van de Web Apps-functie van Azure App Service via:
* Bestand uploaden
* Docker voor Web Apps op Linux

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Jenkins configureren om Web Apps te implementeren via bestandsupload.
> * Jenkins configureren om Web App voor Containers te implementeren

## <a name="create-and-configure-a-jenkins-instance"></a>Een Jenkins-exemplaar maken en configureren

Als u nog geen Jenkins-master hebt, begint u met de [oplossingssjabloon](install-jenkins-solution-template.md), die de Java Development Kit (JDK) versie 8 en de volgende vereiste Jenkins-invoegtoepassingen bevat:

* [Jenkins Git-clientinvoegtoepassing](https://plugins.jenkins.io/git-client) versie 2.4.6 
* [Docker Commons-invoegtoepassing](https://plugins.jenkins.io/docker-commons) versie 1.4.0
* [Azure Credentials](https://plugins.jenkins.io/azure-credentials) versie 1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-service) versie 0.1

U kunt de Jenkins-invoeg toepassing gebruiken om een web-app te implementeren in elke taal die wordt ondersteund door C#web apps, zoals PHP, Python, Java en node. js. In deze zelfstudie gebruiken we een [eenvoudige Java-web-app voor Azure](https://github.com/azure-devops/javawebappsample). Om de opslagplaats naar uw eigen GitHub-account te vertakken, selecteert u de knop **Fork** in de rechterbovenhoek van de GitHub-interface.  

> [!NOTE]
> De Java JDK en Maven zijn vereist om de build van het Java-project uit te voeren. Installeer deze onderdelen op de Jenkins-master, of op de VM-agent als u de agent gebruikt voor continue integratie. Als u een Java SE-toepassing implementeert, is ook ZIP nodig op de build-server.
>

Om de onderdelen te installeren, meldt u zich bij het Jenkins-exemplaar aan met SSH en voert u de volgende opdrachten uit:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Om te implementeren naar Web App voor Containers, installeert u Docker op de Jenkins-master of op de VM-agent die voor de build wordt gebruikt. Zie [Docker installeren op Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/) voor instructies.

## <a name="service-principal"></a> Een Azure-service-principal toevoegen aan de Jenkins-referenties

U hebt een Azure-service-principal nodig om te kunnen implementeren naar Azure. 


1. Gebruik de [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) of de [Azure-portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal) om een Azure-service-principal te maken.
2. Selecteer **Referenties** > **Systeem** op het Jenkins-dashboard. Selecteer vervolgens **Algemene referenties (onbeperkt)** .
3. Selecteer **Referenties toevoegen** om een Microsoft Azure-service-principal toe te voegen. Geef waarden op voor de velden **Abonnements-ID**, **Client-ID**, **Clientgeheim** en **OAuth 2.0-tokeneindpunt**. Stel het veld **ID** in op **mySp**. We gebruiken deze ID in volgende stappen in dit artikel.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Jenkins configureren om Web Apps te implementeren door bestanden te uploaden

Als u uw project wilt implementeren in Web Apps, kunt u uw build-artefacten uploaden door middel van een bestandsupload. Azure App Service biedt ondersteuning voor meerdere implementatieopties. De Jenkins-invoegtoepassing van de Azure App Service maakt het eenvoudig voor u: de implementatieoptie wordt afgeleid van het bestandstype. 

* Voor Java EE-toepassingen wordt er een [WAR-implementatie](/azure/app-service/deploy-zip#deploy-war-file) toegepast.
* Voor Java SE-toepassingen wordt er een [ZIP-implementatie](/azure/app-service/deploy-zip#deploy-zip-file) toegepast.
* Voor andere talen wordt er een [Git-implementatie](/azure/app-service/deploy-local-git) toegepast.

Voordat u de taak in Jenkins instelt, hebt u een Azure App Service-plan en een web-app nodig om de Java-app uit te voeren.


1. Maak een Azure App Service-plan met de prijscategorie **GRATIS** met behulp van de `az appservice plan create` [Azure CLI-opdracht](/cli/azure/appservice/plan#az-appservice-plan-create). Het App Service-plan definieert de fysieke resources die worden gebruikt voor het hosten van uw apps. Alle toepassingen die zijn toegewezen aan een App Service-plan, delen deze resources. Gedeelde resources helpen u kosten te besparen wanneer meerdere apps worden gehost.
2. Maak een web-app. U kunt de [Azure-portal](/azure/app-service/configure-common) of de volgende `az` Azure CLI-opdracht gebruiken:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Stel de Java-runtime-configuratie in die uw app nodig heeft. De volgende Azure CLI-opdracht configureert de web-app om op een recente JDK 8 en [Apache Tomcat](https://tomcat.apache.org/) versie 8.0 te worden uitgevoerd:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>De Jenkins-taak instellen

1. Maak een nieuw **Freestyle**-project op het Jenkins-dashboard.
2. Configureer het veld **Broncodebeheer** om uw lokale fork van de [eenvoudige Java-web-app voor Azure](https://github.com/azure-devops/javawebappsample) te gebruiken. Geef een waarde op bij **URL van opslagplaats**. Bijvoorbeeld: http:\//github.com/&lt;your_ID >/javawebappsample.
3. Voeg een stap toe om de build van het project uit te voeren met behulp van Maven door de opdracht **Shell uitvoeren** toe te voegen. Voor dit voorbeeld hebben we een aanvullende opdracht nodig om de naam van het \*.war-bestand in de doelmap te wijzigen in **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Voeg een actie na maken toe door **Een Azure-web-app publiceren** te selecteren.
5. Geef **mySp** op als de Azure-service-principal. Deze principal was in een vorige stap opgeslagen als de [Azure-referenties](#service-principal).
6. Kies in de sectie **App-configuratie** de resourcegroep en web-app in uw abonnement. De Jenkins-invoegtoepassing detecteert automatisch of de web-app op Windows of Linux is gebaseerd. Voor een Windows-web-app wordt de optie **Bestanden publiceren** weergegeven.
7. Vul de bestanden in die u wilt implementeren. Geef bijvoorbeeld het WAR-pakket op als u Java gebruikt. Gebruik de optionele parameters **Bronmap** en **Doelmap** om op te geven welke bron- en doelmap moeten worden gebruikt voor het uploaden van bestanden. De Java-web-app op Azure wordt uitgevoerd in een Tomcat-server. Voor Java uploadt u uw WAR-pakket dus naar de map webapps. In dit voorbeeld stelt u de waarde **Bronmap** in op **doel** en de waarde **Doelmap** op **web-apps**.
8. Als u wilt implementeren naar een andere site dan productie, kunt u ook de **Site**-naam instellen.
9. Sla het project op en voer de build uit. Uw web-app wordt naar Azure geïmplementeerd wanneer de build is voltooid.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Web Apps implementeren door bestanden te uploaden met behulp van Jenkins Pipeline

De Jenkins-invoegtoepassing voor Azure App Service is pijplijn-gereed. U kunt het volgende voorbeeld in de GitHub-opslagplaats raadplegen.

1. Open het bestand **Jenkinsfile_ftp_plugin** in de GitHub-interface. Selecteer het potloodpictogram om het bestand te bewerken. Werk de definities van **resourceGroup** en **webAppName** voor uw web-app respectievelijk op regel 11 en 12 bij:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Stel de definitie van **withCredentials** op regel 14 in op de referentie-ID in uw Jenkins-exemplaar:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Een Jenkins-pijplijn maken

1. Open Jenkins in een webbrowser. Selecteer **Nieuw Item**.
2. Geef een naam voor de taak op en selecteer **Pijplijn**. Selecteer **OK**.
3. Selecteer het tabblad **Pijplijn**.
4. Selecteer bij **Definitie** de waarde **Pijplijnscript uit SCM**.
5. Selecteer bij **SCM** de waarde **Git**. Voer de GitHub-URL voor uw vertakte opslagplaats in. Bijvoorbeeld: https://&lt;uw_vertakte_opslagplaats>.git.
6. Werk de waarde voor **Scriptpad** bij naar **Jenkinsfile_ftp_plugin**.
7. Selecteer **Opslaan** en voer de taak uit.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Jenkins configureren om Web App voor Containers te implementeren

Web Apps op Linux ondersteunt implementatie met behulp van Docker. Om uw web-app te implementeren met behulp van Docker, moet u een Docker-bestand opgeven dat uw web-app met een serviceruntime in een Docker-installatiekopie verpakt. De Jenkins-invoegtoepassing voert vervolgens de build van de installatiekopie uit, pusht deze naar een Docker-register en implementeert deze naar uw web-app.

Web Apps op Linux ondersteunt ook traditionele implementatiemethoden, zoals Git en bestandsupload, maar alleen voor ingebouwde talen (.NET Core, Node.js, PHP en Ruby). Voor andere talen moet u uw toepassingscode en serviceruntime samen in een Docker-installatiekopie verpakken en Docker gebruiken voor de implementatie.

Voordat u de taak in Jenkins instelt, hebt u een web-app op Linux nodig. U hebt ook een containerregister nodig om uw persoonlijke installatiekopieën voor Docker-containers op te slaan en te beheren. U kunt DockerHub gebruiken om het containerregister te maken. In dit voorbeeld gebruiken we Azure Container Registry.

* [Maak uw web-app op Linux](../app-service/containers/quickstart-nodejs.md).
* Azure Container Registry is een beheerde service voor [Docker-registers](https://docs.docker.com/registry/) die is gebaseerd op de open-source Docker Registry versie 2.0. [Maak een Azure-containerregister](/azure/container-registry/container-registry-get-started-azure-cli). U kunt ook DockerHub gebruiken.

### <a name="set-up-the-jenkins-job-for-docker"></a>De Jenkins-taak instellen voor Docker

1. Maak een nieuw **Freestyle**-project op het Jenkins-dashboard.
2. Configureer het veld **Broncodebeheer** om uw lokale fork van de [eenvoudige Java-web-app voor Azure](https://github.com/azure-devops/javawebappsample) te gebruiken. Geef een waarde op bij **URL van opslagplaats**. Bijvoorbeeld: http:\//github.com/&lt;your_ID >/javawebappsample.
3. Voeg een stap toe om de build van het project uit te voeren met behulp van Maven door de opdracht **Shell uitvoeren** toe te voegen. Neem de volgende regel in de opdracht op:
    ```bash
    mvn clean package
    ```

4. Voeg een actie na maken toe door **Een Azure-web-app publiceren** te selecteren.
5. Geef **mySp** op als de Azure-service-principal. Deze principal was in een vorige stap opgeslagen als de [Azure-referenties](#service-principal).
6. Kies in de sectie **App-configuratie** de resourcegroep en een Linux-web-app in uw abonnement.
7. Kies **Publiceren via Docker**.
8. Vul bij **Dockerfile** de padwaarde in. U kunt de standaardwaarde /Dockerfile behouden.
Geef bij **Docker-register-URL** de URL op in de indeling https://&lt;uwRegister>.azurecr.io als u Azure Container Registry gebruikt. Laat de waarde leeg als u DockerHub gebruikt.
9. Geef bij **Registerreferenties** de referenties voor het containerregister op. U kunt de gebruikersnaam en het wachtwoord ophalen door de volgende opdrachten uit te voeren in de Azure CLI. De eerste opdracht schakelt het beheerdersaccount in:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. De naam en tagwaarde van de Docker-installatiekopie op het tabblad **Geavanceerd** zijn optioneel. De waarde voor de installatiekopienaam wordt standaard opgehaald uit de installatiekopienaam die u in de Azure-portal in de instelling **Docker-container** hebt geconfigureerd. De tag wordt gegenereerd uit $BUILD_NUMBER.
    > [!NOTE]
    > Zorg ervoor dat u de naam van de installatie kopie opgeeft in de Azure Portal of geef een **docker-installatie kopie** op op het tabblad **Geavanceerd** . Voor dit voor beeld stelt u de waarde van de **docker-installatie kopie** in op &lt;your_Registry >. azurecr. io/Calculator en laat u de waarde van de **docker-tag** leeg.

11. De implementatie mislukt als u een ingebouwde instelling voor de Docker-installatiekopie gebruikt. Wijzig de Docker-configuratie zodanig dat er een aangepaste installatiekopie wordt gebruikt in de instelling **Docker-container** in de Azure-portal. Voor een ingebouwde installatiekopie gebruikt u de bestandsuploadbenadering voor de implementatie.
12. Net zoals bij de bestandsuploadbenadering kunt u een andere **Site**-naam dan **productie** kiezen.
13. Sla het project op en voer de build uit. Uw containerinstallatiekopie wordt naar uw register gepusht en de web-app wordt geïmplementeerd.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Web App for Containers implementeren met behulp van Jenkins Pipeline

1. Open het bestand **Jenkinsfile_container_plugin** in de GitHub-interface. Selecteer het potloodpictogram om het bestand te bewerken. Werk de definities van **resourceGroup** en **webAppName** voor uw web-app respectievelijk op regel 11 en 12 bij:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Wijzig regel 13 in uw containerregisterserver:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Wijzig regel 16 om de referentie-ID in uw Jenkins-exemplaar te gebruiken:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Een Jenkins-pijplijn maken    

1. Open Jenkins in een webbrowser. Selecteer **Nieuw Item**.
2. Geef een naam voor de taak op en selecteer **Pijplijn**. Selecteer **OK**.
3. Selecteer het tabblad **Pijplijn**.
4. Selecteer bij **Definitie** de waarde **Pijplijnscript uit SCM**.
5. Selecteer bij **SCM** de waarde **Git**. Voer de GitHub-URL voor uw vertakte opslagplaats in. Bijvoorbeeld: https://&lt;uw_vertakte_opslagplaats>.git.
7. Werk de waarde voor **Scriptpad** bij naar **Jenkinsfile_container_plugin**.
8. Selecteer **Opslaan** en voer de taak uit.

## <a name="verify-your-web-app"></a>Uw web-app verifiëren

1. Om te verifiëren dat het WAR-bestand succesvol naar uw web-app is geïmplementeerd, opent u een webbrowser.
2. Ga naar http://&lt;naam_van_uw_app>.azurewebsites.net/api/calculator/ping. Vervang &lt;naam_van_uw_app> door de naam van uw web-app. U ziet het bericht:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Ga naar http://&lt;naam_van_uw_app>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Vervang &lt;x> en &lt;y> door getallen om de som van x + y te verkrijgen. De calculator toont de som: ![Calculator: optellen](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Voor Azure App Service op Linux

1. Om uw web-app te verifiëren, voert u de volgende opdracht uit in de Azure CLI:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Het volgende bericht wordt weergegeven:
    ```CLI
    ["calculator"]
    ```
    
2. Ga naar http://&lt;naam_van_uw_app>.azurewebsites.net/api/calculator/ping. Vervang &lt;naam_van_uw_app> door de naam van uw web-app. U ziet het bericht: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Ga naar http://&lt;naam_van_uw_app>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Vervang &lt;x> en &lt;y> door getallen om de som van x + y te verkrijgen.
    
## <a name="troubleshooting-the-jenkins-plugin"></a>Problemen met de Jenkins-invoegtoepassing oplossen

Als u problemen ondervindt met de Jenkins-invoegtoepassingen, kunt u in [Jenkins JIRA](https://issues.jenkins-ci.org/) een ticket openen voor het specifieke onderdeel.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de Jenkins-invoegtoepassing voor Azure App Service gebruikt om te implementeren naar Azure.

U hebt geleerd hoe u:

> [!div class="checklist"]
> * Jenkins configureert om Azure App Service te implementeren via bestandsupload 
> * Jenkins configureert om Web App voor Containers te implementeren 
