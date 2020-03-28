---
title: De Azure Dev Spaces-plug-in voor Jenkins gebruiken met Azure Kubernetes-service
description: Meer informatie over het gebruik van de Azure Dev Spaces-plug-in in een pijplijn voor continue integratie.
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 281565cec5ee947781ab8ee9f62a00e01f9ababb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037025"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>Zelfstudie: De Plug-in Azure Dev Spaces gebruiken voor Jenkins met Azure Kubernetes-service 

Met Azure Dev Spaces u uw microservicetoepassing testen en iteratief ontwikkelen die wordt uitgevoerd in Azure Kubernetes Service (AKS) zonder dat u afhankelijkheden hoeft te repliceren of bespotten. Met de Azure Dev Spaces-plug-in voor Jenkins u Dev Spaces gebruiken in uw CI/CD-pijplijn (Continuous Integration and Delivery).

Deze zelfstudie maakt ook gebruik van Azure Container Registry (ACR). ACR slaat afbeeldingen op en een ACR-taak bouwt Docker- en Helm-artefacten. Als u ACR- en ACR-taak gebruikt voor het genereren van artefacten, hoeft u extra software, zoals Docker, op uw Jenkins-server te installeren. 

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een AKS-cluster met Azure Dev Spaces maken
> * Een multiservicetoepassing implementeren op AKS
> * Uw Jenkins-server voorbereiden
> * De plug-in Azure Dev Spaces in een Jenkins-pijplijn gebruiken om codewijzigingen te bekijken voordat deze worden samengevoegd in het project

In deze zelfstudie wordt uitgegaan van tussentijdse kennis van de belangrijkste Azure-services, AKS, ACR, Azure Dev Spaces, [Jenkins-pijplijnen](https://jenkins.io/doc/book/pipeline/) en plug-ins en GitHub. Basisvertrouwdheid met ondersteunende tools zoals kubectl en Helm is nuttig.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

* Een GitHub-account. Als je geen GitHub-account hebt, maak je een [gratis account](https://github.com/) aan voordat je begint.

* [Visual Studio Code](https://code.visualstudio.com/download) met de [Azure Dev Spaces-extensie](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) geïnstalleerd.

* [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest), versie 2.0.43 of hoger.

* Een Jenkins-masterserver. Als u nog geen Jenkins-master hebt, implementeert u [Jenkins](https://aka.ms/jenkins-on-azure) op Azure door de stappen in deze quickstart te [volgen.](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template) 

* De Jenkins-server moet zowel Helm als kubectl hebben geïnstalleerd en beschikbaar zijn voor het Jenkins-account, zoals later in deze zelfstudie wordt uitgelegd.

* VS Code, de VS Code Terminal of WSL en Bash. 


## <a name="create-azure-resources"></a>Azure-resources maken

In deze sectie maakt u Azure-resources:

* Een resourcegroep met alle Azure-bronnen voor deze zelfstudie.
* Een AKS-cluster [(Azure Kubernetes Service)](https://docs.microsoft.com/azure/aks/)
* Een [Azure-containerregister](https://docs.microsoft.com/azure/container-registry/) (ACR) om te bouwen (met ACR-taken) en Docker-afbeeldingen op te slaan.

1. Maak een resourcegroep.

    ```azurecli
    az group create --name MyResourceGroup --location westus2
    ```

2. Maak een AKS-cluster. Maak het AKS-cluster in een [regio die Dev Spaces ondersteunt.](../dev-spaces/about.md#supported-regions-and-configurations)

    ```azurecli
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Configureer AKS om Dev Spaces te gebruiken.

    ```azurecli
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Met deze stap `azds` wordt de CLI-extensie geïnstalleerd.

4. Maak een containerregister.

    ```azurecli
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Voorbeeld-apps implementeren in het AKS-cluster

In deze sectie stelt u een dev-ruimte in en implementeert u een voorbeeldtoepassing naar het AKS-cluster dat u in de laatste sectie hebt gemaakt. De applicatie bestaat uit twee delen, *webfrontend* en *mywebapi*. Beide componenten worden geïmplementeerd in een dev-ruimte. Later in deze zelfstudie dien je een pull-verzoek in tegen mywebapi om de CI-pijplijn in Jenkins te activeren.

Zie [Aan de slag op Azure Dev Spaces met Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java)en [Multi-service-ontwikkeling met Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java)voor meer informatie over het gebruik van Azure Dev Spaces en multiservice-ontwikkeling met Azure Dev Spaces. Deze tutorials bieden extra achtergrondinformatie die hier niet is opgenomen.

1. Download https://github.com/Azure/dev-spaces de repo van GitHub.

2. Open `samples/java/getting-started/webfrontend` de map in VS Code. (U kunt eventuele standaardprompts negeren om foutopsporingsassets toe te voegen of om het project te herstellen.)

3. Update `/src/main/java/com/ms/sample/webfrontend/Application.java` om er als volgt uit te zien:

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

4. Klik **op Weergave** vervolgens **Terminal** om de geïntegreerde terminal in VS-code te openen.

5. Voer `azds prep` de opdracht uit om uw toepassing voor te bereiden op een dev-ruimte. Deze opdracht moet `dev-spaces/samples/java/getting-started/webfrontend` worden uitgevoerd om uw toepassing correct voor te bereiden:

    ```bash
    azds prep --public
    ```

    De `azds prep` opdracht van Dev Spaces CLI genereert Docker- en Kubernetes-assets met standaardinstellingen. Deze bestanden blijven bestaan gedurende de levensduur van het project en kunnen worden aangepast:

    * `./Dockerfile`en `./Dockerfile.develop` beschrijf de containerafbeelding van de app en hoe de broncode is gebouwd en in de container wordt uitgevoerd.
    * Met een [Helm-grafiek](https://helm.sh/docs/topics/charts/) die zich onder `./charts/webfrontend` bevindt, wordt beschreven hoe de container in Kubernetes moet worden geïmplementeerd.
    * `./azds.yaml`is het configuratiebestand Azure Dev Spaces.

    Zie [Hoe Azure Dev Spaces werkt en is geconfigureerd](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)voor meer informatie.

6. De toepassing in AKS bouwen `azds up` en uitvoeren met de opdracht:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Scan de console-uitvoer voor informatie over `up` de URL die door de opdracht is gemaakt. Het zal in deze vorm te zien zijn:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Open deze URL in een browservenster en u moet de web-app zien. Terwijl de container wordt uitgevoerd, wordt `stdout`- en `stderr`-uitvoer naar het terminalvenster gestreamd.

8. Vervolgens u *mywebapi*instellen en implementeren:

    1. Map wijzigen in`dev-spaces/samples/java/getting-started/mywebapi`

    2. Voer

        ```bash
        azds prep
        ```

    3. Voer

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Jenkins-server voorbereiden

In deze sectie bereidt u de Jenkins-server voor om de bisteekproef-CI-pijplijn uit te voeren.

* Plug-ins installeren
* Helm en Kubernetes CLI installeren
* Referenties toevoegen

### <a name="install-plug-ins"></a>Plug-ins installeren

1. Meld u aan bij uw Jenkins-server. Kies **Jenkins beheren > Plug-ins beheren.**
2. Selecteer op het tabblad **Beschikbaar** de volgende plug-ins:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry Tasks](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Milieuinjector](https://plugins.jenkins.io/envinject)
    * [GitHub-integratie](https://plugins.jenkins.io/github-pullrequest)

    Als deze plug-ins niet in de lijst worden weergegeven, controleert u het tabblad **Geïnstalleerd** om te zien of ze al zijn geïnstalleerd.

3. Als u de plug-ins wilt installeren, kiest u **Nu downloaden en installeren na het opnieuw opstarten.**

4. Start de Jenkins-server opnieuw op om de installatie te voltooien.

### <a name="install-helm-and-kubectl"></a>Installeer Helm en kubectl

De voorbeeldpijplijn gebruikt Helm en kubectl om te implementeren in de dev-ruimte. Wanneer Jenkins is geïnstalleerd, wordt een beheerdersaccount gemaakt met de naam *Jenkins*. Zowel Helm als kubectl moeten toegankelijk zijn voor de jenkins gebruiker.

1. Maak een SSH-verbinding met de Jenkins-master. 

2. Overschakelen `jenkins` naar de gebruiker:
    ```bash
    sudo su jenkins
    ```

3. Installeer de Helm CLI. Zie [Helm installeren voor](https://helm.sh/docs/using_helm/#installing-helm)meer informatie.

4. Installeer kubectl. Zie voor meer informatie [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Referenties toevoegen aan Jenkins

1. Jenkins heeft een Azure-serviceprincipal nodig voor het verifiëren en openen van Azure-bronnen. Als u de serviceprincipal wilt maken, verwijst u naar de [hoofdsectie Service maken](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) in de zelfstudie Van Azure App Service implementeren. Zorg ervoor dat u een `create-for-rbac` kopie van de uitvoer opslaat, omdat u die informatie nodig hebt om de volgende stap te voltooien. De output ziet er ongeveer als volgt uit:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Voeg een *Microsoft Azure Service Principal-referentietype* toe aan Jenkins met behulp van de hoofdgegevens van de service uit de vorige stap. De namen in de screenshot hieronder `create-for-rbac`komen overeen met de uitvoer van .

    Het **id-veld** is de jenkins-referentienaam voor uw serviceprincipal. In het voorbeeld `displayName` wordt de waarde `xxxxxxxjenkinssp`van (in dit geval) gebruikt, maar u elke gewenste tekst gebruiken. Deze referentienaam is de waarde voor de AZURE_CRED_ID omgevingsvariabele in de volgende sectie.

    ![Servicehoofdreferenties toevoegen aan Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    De **beschrijving** is optioneel. Zie [Serviceprincipal toevoegen aan jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) in de zelfstudie Implementeren aan Azure App Service voor meer gedetailleerde instructies. 



3. Voer de opdracht uit om uw ACR-referenties weer te geven:

    ```azurecli
    az acr credential show -n <yourRegistryName>
    ```

    Maak een kopie van de JSON-uitvoer, die er ongeveer zo uit moet zien:

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

4. Een *gebruikersnaam met wachtwoordreferentietype* toevoegen in Jenkins. De **gebruikersnaam** is de gebruikersnaam van de `acr01`laatste stap, in dit voorbeeld . Het **wachtwoord** is de waarde voor het `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`eerste wachtwoord, in dit voorbeeld. De **id** van deze referentie is de waarde van ACR_CRED_ID.

5. Een AKS-referentie instellen. Voeg een *Type Kubernetes-configuratie (kubeconfig)* toe in Jenkins (gebruik de optie 'Voer rechtstreeks in'). Voer de volgende opdracht uit om de toegangsreferenties voor uw AKS-cluster op te halen:

    ```azurecli
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   De **ID** deze referentie is de waarde van KUBE_CONFIG_ID in de volgende sectie.

## <a name="create-a-pipeline"></a>Een pijplijn maken

Het scenario dat is geselecteerd voor de voorbeeldpijplijn is gebaseerd op een patroon in de echte wereld: een pull-aanvraag activeert een CI-pijplijn die de voorgestelde wijzigingen in een Azure-dev-ruimte voor testen en controle implementeert en vervolgens implementeert. Afhankelijk van de uitkomst van de beoordeling worden de wijzigingen samengevoegd en geïmplementeerd op AKS of verwijderd. Ten slotte wordt de dev-ruimte verwijderd.

De Jenkins-pijplijnconfiguratie en Jenkinsfile definiëren de fasen in de CI-pijplijn. In dit stroomdiagram worden de pijplijnfasen en beslissingspunten weergegeven die zijn gedefinieerd door het Jenkinsfile:

![Jenkins-pijplijnstroom](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Download een aangepaste versie van het [https://github.com/azure-devops/mywebapi](https://github.com/azure-devops/mywebapi) *mywebapi-project* van . Dit project bevat verschillende bestanden die nodig zijn om een pijplijn te maken, waaronder de *jenkinsfile,* *Dockerfiles*en Helm-diagram.

2. Log in bij Jenkins. Selecteer **Item toevoegen**in het menu aan de linkerkant .

3. Selecteer **Pijplijn**en voer een naam in het vak **Een itemnaam invoeren** in. Selecteer **OK**en vervolgens wordt het scherm van de pijplijnconfiguratie automatisch geopend.

4. Controleer op het tabblad **Algemeen** de **controle Een omgeving voorbereiden voor de run**. 

5. Controleer **De omgevingsvariabelen van Jenkins houden** en Jenkins **Build-variabelen behouden.**

6. Voer in het vak **Eigenschappeninhoud** de volgende omgevingsvariabelen in:

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

    Met behulp van de voorbeeldwaarden in de voorgaande secties moet de lijst met omgevingsvariabelen er ongeveer als volgt uitzien:

    ![Jenkins-pijplijnomgevingsvariabelen](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Kies **Pipeline-script in SCM** in **Pipeline > Definitie**.
8. Kies **in SCM** **Git** en voer vervolgens uw repo-URL in.
9. Voer in **Branch Specifier**. `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`
10. Vul de SCM repo URL en script pad "Jenkinsfile".
11. **Lichtgewicht kassa** moet worden gecontroleerd.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Een pull-aanvraag maken om de pijplijn te activeren

Om stap 3 in deze sectie te voltooien, moet je een deel van het Jenkinsfile becommentariëren, anders krijg je een 404-foutmelding wanneer je de nieuwe en oude versies naast elkaar probeert te bekijken. Wanneer u ervoor kiest om de PR samen te voegen, wordt de vorige gedeelde versie van mywebapi standaard verwijderd en vervangen door de nieuwe versie. Breng de volgende wijziging aan in het Jenkinsfile voordat u stap 1 voltooit:

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

1. Breng een `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`wijziging aan en maak vervolgens een pull-aanvraag. Bijvoorbeeld:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Meld u aan bij Jenkins en selecteer de naam van de pijplijn en kies **Nu bouwen**. 

    U ook een *webhook* instellen om de Jenkins-pijplijn automatisch te activeren. Wanneer een pull-aanvraag wordt ingevoerd, geeft GitHub een bericht uit aan Jenkins, waardoor de pijplijn wordt geactiveerd. Zie [Jenkins verbinden met GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github)voor meer informatie over het instellen van een webhook.

3. Wijzigingen vergelijken met de huidige gedeelde versie:

    1. Open uw browser en navigeer `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`naar de gedeelde versie. TEST_ENDPOINT bevat de URL.

    2. Open een ander tabblad en voer vervolgens de URL van de PR-dev-spatie in. Het zal vergelijkbaar `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`zijn met . Je vindt de link in **Build History > <build#> > Console Output** voor de Jenkins-taak. Zoek op `aksapp`de pagina naar , of om `azdsprefix`alleen het voorvoegsel te zien, zoeken naar .

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>De URL naar de onderliggende dev-ruimte maken

Wanneer u een pull-aanvraag indient, maakt Jenkins een onderliggende dev-ruimte op basis van de gedeelde dev-ruimte van het team en voert u de code uit van uw pull-verzoek in die onderliggende dev-ruimte. De URL naar de onderliggende `http://$env.azdsprefix.<test_endpoint>`dev-ruimte neemt de vorm aan. 

**$env.azdsprefix** wordt ingesteld tijdens de uitvoering van de pijplijn door de Azure Dev Spaces-plug-in door **devSpacesCreate:**

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

De `test_endpoint` URL naar de webfrontend-app die `azds up`u eerder hebt geïmplementeerd in [Voorbeeld-apps implementeren in het AKS-cluster, Stap 7.](#test_endpoint) De waarde `$env.TEST_ENDPOINT` van de pijplijnconfiguratie is ingesteld. 

In het volgende codefragment ziet u hoe de `smoketest` URL van de onderliggende dev-ruimte in de fase wordt gebruikt. De code controleert of de onderliggende dev-ruimte TEST_ENDPOINT beschikbaar is, en zo ja, downloadt de begroetingstekst naar stdout:

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

Wanneer u klaar bent met het gebruik van de voorbeeldtoepassing, ruimt u Azure-resources op door de brongroep te verwijderen:

```azurecli
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [CI/CD met Jenkins op Azure](jenkins-continuous-deployment.md)
