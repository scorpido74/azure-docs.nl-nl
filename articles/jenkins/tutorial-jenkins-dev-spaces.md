---
title: De Azure dev Space-invoeg toepassing gebruiken voor Jenkins met de Azure Kubenetes-service
description: Meer informatie over het gebruik van de Azure dev Spaces-invoeg toepassing in een continue integratie pijplijn.
author: tomarchermsft
ms.author: tarcher
ms.service: jenkins
ms.topic: tutorial
ms.custom: mvc
ms.date: 07/31/2019
ms.openlocfilehash: 3d5e8ba8a29481a6f37ffd10f577d354fc5fbf0a
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71161494"
---
<!-- GMinchAQ, 06/18/19 -->

# <a name="tutorial-using-the-azure-dev-spaces-plugin-for-jenkins-with-azure-kubenetes-service"></a>Zelfstudie: De Azure dev Space-invoeg toepassing gebruiken voor Jenkins met de Azure Kubenetes-service 

Met Azure dev Spaces kunt u de micro service-toepassing die wordt uitgevoerd in azure Kubernetes service (AKS) testen en iteratief ontwikkelen zonder dat u afhankelijkheden hoeft te repliceren of te demodeln. De Azure dev Spaces-invoeg toepassing voor Jenkins helpt u bij het gebruik van ontwikkel ruimten in uw doorlopende integratie-en leverings pijplijn (CI/CD).

In deze zelf studie wordt ook gebruikgemaakt van Azure Container Registry (ACR). ACR slaat afbeeldingen op en een ACR-taak bouwt docker-en helm-artefacten. Als u de ACR-en ACR-taak gebruikt voor het genereren van artefacten, verwijdert u de nood zaak om extra software, zoals docker, op uw Jenkins-server te installeren. 

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een AKS-cluster maken dat is ingeschakeld voor Azure dev Spaces
> * Een multi-service toepassing implementeren op AKS
> * Uw Jenkins-server voorbereiden
> * Gebruik de invoeg toepassing Azure dev Spaces in een Jenkins-pijp lijn om voorbeeld code wijzigingen te bekijken voordat u deze samenvoegt in het project

In deze zelf studie wordt ervan uitgegaan dat de kern kennis van Azure-Services, AKS, ACR, Azure dev Spaces, Jenkins- [pijp lijnen](https://jenkins.io/doc/book/pipeline/) en-invoeg toepassingen en github. Basis kennis met ondersteunende hulpprogram ma's zoals kubectl en helm is nuttig.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account. Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* Een GitHub-account. Als u geen GitHub-account hebt, maakt u een [gratis account](https://github.com/) voordat u begint.

* [Visual Studio code](https://code.visualstudio.com/download) met de [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) -extensie geïnstalleerd.

* [Azure cli is geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest), versie 2.0.43 of hoger.

* Een Jenkins-masterserver. Als u nog geen Jenkins-master hebt, implementeert u [Jenkins](https://aka.ms/jenkins-on-azure) in azure door de stappen in deze [Snelstartgids](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)te volgen. 

* Op de Jenkins-server moeten zowel helm als kubectl zijn geïnstalleerd en beschikbaar zijn voor het Jenkins-account, zoals verderop in deze zelf studie wordt uitgelegd.

* VS code, de VS code terminal of WSL en bash. 


## <a name="create-azure-resources"></a>Azure-resources maken

In deze sectie maakt u Azure-resources:

* Een resource groep die alle Azure-resources voor deze zelf studie bevat.
* Een [Azure Kubernetes service](https://docs.microsoft.com/azure/aks/) -cluster (AKS).
* Een [Azure container Registry](https://docs.microsoft.com/azure/container-registry/) (ACR) voor het bouwen (met behulp van ACR-taken) en het opslaan van docker-installatie kopieën.

1. Maak een resourcegroep.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Maak een AKS-cluster. Maak het AKS-cluster in een [regio die ontwikkel ruimten ondersteunt](../dev-spaces/about.md#supported-regions-and-configurations).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Configureer AKS voor het gebruik van dev Spaces.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Met deze stap wordt `azds` de CLI-extensie geïnstalleerd.

4. Maak een container register.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Voor beeld-Apps implementeren in het AKS-cluster

In dit gedeelte stelt u een ontwikkelings ruimte in en implementeert u een voorbeeld toepassing naar het AKS-cluster dat u in de laatste sectie hebt gemaakt. De toepassing bestaat uit twee delen: *webfrontend* en *mywebapi*. Beide onderdelen worden geïmplementeerd in een dev-ruimte. Verderop in deze zelf studie verzendt u een pull-aanvraag tegen mywebapi om de CI-pijp lijn in Jenkins te activeren.

Voor meer informatie over het gebruik van Azure dev Spaces en multi-service ontwikkeling met Azure dev Spaces, zie aan de [slag met Azure dev Spaces met Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java)en [meerdere services ontwikkelen met Azure dev Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Deze zelf studies bevatten aanvullende achtergrond informatie die hier niet is opgenomen.

1. Down load https://github.com/Azure/dev-spaces de opslag plaats van github.

2. Open de `samples/java/getting-started/webfrontend` map in VS code. (U kunt eventuele standaardprompts negeren om foutopsporingsassets toe te voegen of om het project te herstellen.)

3. Update `/src/main/java/com/ms/sample/webfrontend/Application.java` moet er als volgt uitzien:

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. Klik op **weer gave** en vervolgens op **Terminal** om de geïntegreerde Terminal te openen in VS code.

5. Voer de `azds prep` opdracht uit om de toepassing voor te bereiden om te worden uitgevoerd in een dev-ruimte. Deze opdracht moet worden uitgevoerd `dev-spaces/samples/java/getting-started/webfrontend` om de toepassing op de juiste wijze voor te bereiden:

    ```bash
    azds prep --public
    ```

    De `azds prep` opdracht dev Spaces cli genereert docker-en Kubernetes-assets met standaard instellingen. Deze bestanden blijven voor de levens duur van het project bestaan en kunnen worden aangepast:

    * `./Dockerfile`en `./Dockerfile.develop` Beschrijf de container installatie kopie van de app en hoe de bron code wordt gebouwd en uitgevoerd in de container.
    * Met een [Helm-grafiek](https://helm.sh/docs/developing_charts/) die zich onder `./charts/webfrontend` bevindt, wordt beschreven hoe de container in Kubernetes moet worden geïmplementeerd.
    * `./azds.yaml`is het configuratie bestand voor de Azure dev Spaces.

    Zie [hoe Azure dev Spaces werkt en is geconfigureerd](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)voor meer informatie.

6. Bouw en voer de toepassing uit in AKS met `azds up` behulp van de opdracht:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Scan de console-uitvoer voor informatie over de URL die is gemaakt met `up` de opdracht. Het zal in deze vorm te zien zijn:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Open deze URL in een browser venster en de web-app wordt weer geven. Terwijl de container wordt uitgevoerd, wordt `stdout`- en `stderr`-uitvoer naar het terminalvenster gestreamd.

8. Vervolgens stelt u *mywebapi*in en implementeert u:

    1. Wijzig de map naar`dev-spaces/samples/java/getting-started/mywebapi`

    2. Voer

        ```bash
        azds prep
        ```

    3. Voer

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Jenkins-server voorbereiden

In deze sectie bereidt u de Jenkins-server voor op het uitvoeren van de voor beeld-CI-pijp lijn.

* Invoeg toepassingen installeren
* Helm en Kubernetes CLI installeren
* Referenties toevoegen

### <a name="install-plug-ins"></a>Invoeg toepassingen installeren

1. Meld u aan bij uw Jenkins-server. Kies **Manage Jenkins > plugins beheren**.
2. Selecteer de volgende invoeg toepassingen op het tabblad **beschikbaar** :
    * [Azure dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry taken](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Omgevings injectie](https://plugins.jenkins.io/envinject)
    * [GitHub-integratie](https://plugins.jenkins.io/github-pullrequest)

    Als deze invoeg toepassingen niet in de lijst worden weer gegeven, gaat u naar het tabblad **geïnstalleerd** om te controleren of ze al zijn geïnstalleerd.

3. Als u de invoeg toepassingen wilt installeren, kiest u **nu downloaden en installeren na opnieuw opstarten**.

4. Start de Jenkins-server opnieuw op om de installatie te volt ooien.

### <a name="install-helm-and-kubectl"></a>Helm en kubectl installeren

De voorbeeld pijplijn maakt gebruik van helm en kubectl om te implementeren op de dev-ruimte. Wanneer Jenkins is geïnstalleerd, wordt er een beheerders account gemaakt met de naam *Jenkins*. Zowel helm als kubectl moeten toegankelijk zijn voor de Jenkins-gebruiker.

1. Maak een SSH-verbinding met de Jenkins-Master. 

2. Schakel over naar `jenkins` de gebruiker:
    ```bash
    sudo su jenkins
    ```

3. Installeer de helm CLI. Zie [Installing helm](https://helm.sh/docs/using_helm/#installing-helm)(Engelstalig) voor meer informatie.

4. Installeer kubectl. Zie [**AZ ACS kubernetes install-cli (** ](https://helm.sh/docs/using_helm/#installing-helm)Engelstalig) voor meer informatie.

### <a name="add-credentials-to-jenkins"></a>Referenties toevoegen aan Jenkins

1. Jenkins heeft een Azure-Service-Principal nodig voor de verificatie en toegang tot Azure-resources. Als u de Service-Principal wilt maken, raadpleegt u de sectie [Service-Principal](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) maken in de zelf studie implementeren naar Azure app service. Zorg ervoor dat u een kopie van de uitvoer `create-for-rbac` opslaat, omdat u deze informatie nodig hebt om de volgende stap te volt ooien. De uitvoer ziet er ongeveer als volgt uit:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Voeg een *Microsoft Azure Service-Principal* -referentie type in Jenkins toe met behulp van de Service-Principal-informatie uit de vorige stap. De namen in de onderstaande scherm afbeelding komen overeen met de `create-for-rbac`uitvoer van.

    Het **id-** veld is de referentie naam van de Jenkins voor uw service-principal. In het voor beeld wordt de `displayName` waarde van (in dit `xxxxxxxjenkinssp`exemplaar,) gebruikt, maar u kunt elke gewenste tekst gebruiken. Deze referentie naam is de waarde voor de omgevings variabele AZURE_CRED_ID in de volgende sectie.

    ![Referenties voor Service-Principal toevoegen aan Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    De **Beschrijving** is optioneel. Zie de sectie [Service-Principal toevoegen aan Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) in de zelf studie implementeren in azure app service voor meer gedetailleerde instructies. 



3. Voer de volgende opdracht uit om uw ACR-referenties weer te geven:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Maak een kopie van de JSON-uitvoer, die er ongeveer als volgt uitziet:

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Voeg een *gebruikers naam met wachtwoord* referentie type toe in Jenkins. De **gebruikers naam** is de gebruikers naam van de laatste stap, in `acr01`dit voor beeld. Het **wacht woord** is de waarde voor het eerste wacht woord, in `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`dit voor beeld. De **id** van deze referentie is de waarde van ACR_CRED_ID.

5. Stel een AKS-referentie in. Voeg een *Kubernetes-configuratie (kubeconfig)-* referentie type toe aan Jenkins (gebruik de optie "rechtstreeks invoeren"). Voer de volgende opdracht uit om de toegangs referenties voor uw AKS-cluster op te halen:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   De **id** van deze referentie is de waarde van KUBE_CONFIG_ID in de volgende sectie.

## <a name="create-a-pipeline"></a>Een pijplijn maken

Het scenario dat is geselecteerd voor de voorbeeld pijplijn is gebaseerd op een echt patroon: Met een pull-aanvraag wordt een CI-pijp lijn geactiveerd waarmee de voorgestelde wijzigingen worden geïmplementeerd in een Azure dev-ruimte voor testen en controleren. Afhankelijk van het resultaat van de beoordeling, worden de wijzigingen samengevoegd en geïmplementeerd op AKS of verwijderd. Ten slotte wordt de ruimte voor ontwikkel aars verwijderd.

De Jenkins-pijplijn configuratie en Jenkinsfile definiëren de fasen in de CI-pijp lijn. Dit stroom diagram toont de pijplijn fasen en beslissings punten die zijn gedefinieerd door de Jenkinsfile:

![Stroom diagram Jenkins-pijp lijn](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Down load een gewijzigde versie van het *mywebapi* - https://github.com/azure-devops/mywebapi project van. Dit project bevat verschillende bestanden die nodig zijn om een pijp lijn te maken, met inbegrip van de *Jenkinsfile*-, *Dockerfiles*-en helm-grafiek.

2. Meld u aan bij Jenkins. Selecteer in het menu aan de linkerkant **item toevoegen**.

3. Selecteer **pijp lijn**en voer een naam in het vak **Voer een item naam** in. Selecteer **OK**en vervolgens wordt het venster pijplijn configuratie automatisch geopend.

4. Schakel op het tabblad **Algemeen** het selectie vakje **een omgeving voorbereiden voor de uitvoering**in. 

5. Schakel **Jenkins-omgevings variabelen blijven** in en **behoud Jenkins build-variabelen**.

6. Voer in het vak **Eigenschappen inhoud** de volgende omgevings variabelen in:

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    De lijst met omgevings variabelen die in de voor gaande secties worden weer gegeven, moet er ongeveer als volgt uitzien:

    ![Jenkins pijplijn omgevings variabelen](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Kies **pijplijn script uit SCM** in **> definitie van de pijp lijn**.
8. In **SCM**kiest u **Git** en voert u vervolgens uw opslag plaats-URL in.
9. Voer`refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`in **vertakkings aanduiding**in.
10. Vul de SCM opslag plaats-URL en het script-pad ' Jenkinsfile ' in.
11. **Lichtgewicht afhandeling** moet worden gecontroleerd.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Een pull-aanvraag maken om de pijp lijn te activeren

Als u stap 3 in deze sectie wilt volt ooien, moet u een opmerking toevoegen aan het Jenkinsfile, anders wordt er een fout 404 weer gegeven wanneer u probeert de nieuwe en oude versies naast elkaar te bekijken. Wanneer u ervoor kiest om de PR samen te voegen, wordt de vorige gedeelde versie van mywebapi standaard verwijderd en vervangen door de nieuwe versie. Breng de volgende wijziging aan in de Jenkinsfile voordat u stap 1:

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. Breng een wijziging aan `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`en maak vervolgens een pull-aanvraag. Bijvoorbeeld:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Meld u aan bij Jenkins en selecteer de naam van de pijp lijn en kies **nu bouwen**. 

    U kunt ook een *webhook* instellen om automatisch de Jenkins-pijp lijn te activeren. Wanneer een pull-aanvraag wordt ingevoerd, verzendt GitHub een bericht naar Jenkins, waardoor de pijp lijn wordt geactiveerd. Zie [verbinding maken tussen Jenkins en github](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github)voor meer informatie over het instellen van een webhook.

3. Wijzigingen in de huidige gedeelde versie vergelijken:

    1. Open uw browser en navigeer naar de gedeelde versie `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT bevat de URL.

    2. Open nog een tabblad en voer de URL van de PR dev-ruimte in. Dit is vergelijkbaar met `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. U vindt de koppeling in **Build History > < build # > >-console-uitvoer** voor de Jenkins-taak. Zoek op de pagina `aksapp`naar of Bekijk alleen het voor voegsel, `azdsprefix`zoek naar.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>De URL naar de onderliggende ontwikkel ruimte samen stellen

Wanneer u een pull-aanvraag bestandeert, maakt Jenkins een onderliggende ontwikkel ruimte op basis van de gedeelde ontwikkel ruimte van het team en voert de code uit van uw pull-aanvraag in die onderliggende ontwikkel ruimte. De URL naar de onderliggende ontwikkel ruimte neemt het formulier `http://$env.azdsprefix.<test_endpoint>`over. 

**$env. azdsprefix** wordt ingesteld tijdens de uitvoering van de pijp lijn door de invoeg toepassing Azure dev Spaces door **devSpacesCreate**:

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

De `test_endpoint` is de URL naar de Webfront-end-app die `azds up`u eerder hebt geïmplementeerd met in [voor beeld-Apps implementeren naar het AKS-cluster, stap 7](#test_endpoint). De waarde van `$env.TEST_ENDPOINT` wordt ingesteld in de pijplijn configuratie. 

Het volgende code fragment laat zien hoe de URL van de onderliggende ontwikkel ruimte wordt `smoketest` gebruikt in het werk gebied. Met de code wordt gecontroleerd of de TEST_ENDPOINT van de onderliggende ontwikkel ruimte beschikbaar is. als dit het geval is, wordt de begroetings tekst gedownload naar stdout:

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het gebruik van de voorbeeld toepassing, moet u Azure-resources opschonen door de resource groep te verwijderen:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de Azure dev Spaces-invoeg toepassing gebruikt voor Jenkins en de Azure Container Registry-invoeg toepassing om code te bouwen en te implementeren op een dev-ruimte.

De volgende lijst met resources biedt meer informatie over Azure dev Spaces, ACR-taken en CI/CD met Jenkins.

Azure dev Spaces:
* [Hoe Azure Dev Spaces werkt en is geconfigureerd](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

ACR taken:
* [OS- en frameworkpatching automatiseren met ACR Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [Automatische build van code door voeren](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

CI/CD met Jenkins op Azure:
* [Jenkins doorlopende implementatie](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
