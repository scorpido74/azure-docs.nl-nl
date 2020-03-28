---
title: Apps implementeren in Azure Spring Cloud met Jenkins en de Azure CLI
description: Meer informatie over het gebruik van Azure CLI in een pijplijn voor continue integratie en implementatie om microservices te implementeren voor Azure Spring Cloud-service
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: 67ad97bb762ed302ef52c404d47c5755ea4b245b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75734976"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Zelfstudie: Apps implementeren in Azure Spring Cloud met Jenkins en de Azure CLI

[Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) is een volledig beheerde microservice-ontwikkeling met ingebouwde servicedetectie en configuratiebeheer. De service maakt het eenvoudig om op Spring Boot gebaseerde microservicetoepassingen te implementeren in Azure. In deze zelfstudie wordt uitgelegd hoe u Azure CLI in Jenkins gebruiken om continue integratie en levering (CI/CD) voor Azure Spring Cloud te automatiseren.

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een service-exemplaar inrichten en een Java Spring-toepassing starten
> * Uw Jenkins-server voorbereiden
> * De Azure CLI in een Jenkins-pijplijn gebruiken om de microservicetoepassingen te bouwen en te implementeren 

In deze zelfstudie wordt uitgegaan van tussentijdse kennis van de belangrijkste Azure-services, Azure Spring Cloud, [Jenkins-pijplijnen](https://jenkins.io/doc/book/pipeline/) en plug-ins en GitHub.

## <a name="prerequisites"></a>Vereisten

>[!Note]
> Azure Spring Cloud wordt momenteel aangeboden als een openbare preview. Openbare preview-aanbiedingen stellen klanten in staat om te experimenteren met nieuwe functies voordat ze officieel worden uitgebracht.  Openbare preview-functies en services zijn niet bedoeld voor productiegebruik.  Bekijk onze veelgestelde vragen over onze [veelgestelde vragen](https://azure.microsoft.com/support/faq/) voor meer informatie over ondersteuning tijdens previews of dien een [ondersteuningsverzoek](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) in voor meer informatie.

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

* Een GitHub-account. Als je geen GitHub-account hebt, maak je een [gratis account](https://github.com/) aan voordat je begint.

* Een Jenkins-masterserver. Als u nog geen Jenkins-master hebt, implementeert u [Jenkins](https://aka.ms/jenkins-on-azure) op Azure door de stappen in deze quickstart te [volgen.](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template) De volgende zijn vereist op de Jenkins-node/agent (bijvoorbeeld. buildserver):

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 of hoger](https://maven.apache.org/download.cgi)
    * [Azure CLI geïnstalleerd,](/cli/azure/install-azure-cli?view=azure-cli-latest)versie 2.0.67 of hoger

    >[!TIP]
    > Hulpprogramma's zoals Git, JDK, Az CLI en Azure plug-ius zijn standaard opgenomen in de Azure Marketplace Microsoft Jenkins-oplossingssjabloon. [Microsoft Jenkins](https://aka.ms/jenkins-on-azure) 
    
* [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Een service-exemplaar inrichten en een Java Spring-toepassing starten

We gebruiken [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) als voorbeeld van Microsoft-servicetoepassing en volgen dezelfde stappen in [Quickstart: Start een Java Spring-toepassing met de Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) om de service-instantie in te richten en de toepassingen in te stellen. Als u hetzelfde proces al hebt doorlopen, u naar de volgende sectie gaan. Anders zijn de Azure CLI-opdrachten opgenomen in het volgende. Raadpleeg [Quickstart: start een Java Spring-toepassing met de Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) om aanvullende achtergrondinformatie te krijgen.

Uw lokale machine moet aan dezelfde voorwaarde voldoen als de Jenkins build-server. Controleer of het volgende is geïnstalleerd om de microservicetoepassingen te bouwen en te implementeren:
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 of hoger](https://maven.apache.org/download.cgi)
    * [Azure CLI geïnstalleerd,](/cli/azure/install-azure-cli?view=azure-cli-latest)versie 2.0.67 of hoger

1. Installeer de Azure Spring Cloud-extensie:

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Maak een resourcegroep met uw Azure Spring Cloud-service:

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Een exemplaar van Azure Spring Cloud inrichten:

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Fork the [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) repo naar uw eigen GitHub-account. Kloon in uw lokale machine uw repo in een map met de naam: `source-code`

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Stel uw configuratieserver in. Zorg ervoor &lt;dat u uw&gt; GitHub-id vervangt door de juiste waarde.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Bouw het project:

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Maak de drie microservices: **gateway,** **auth-service**en **accountservice:**

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. Implementeer de toepassingen: 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. Openbare eindpunt toewijzen aan gateway:

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Vraag de gatewaytoepassing op om de url te krijgen, zodat u controleren of de toepassing wordt uitgevoerd.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Navigeer naar de URL die door de vorige opdracht wordt verstrekt om de toepassing PiggyMetrics uit te voeren. 

## <a name="prepare-jenkins-server"></a>Jenkins-server voorbereiden

In deze sectie bereidt u de Jenkins-server voor om een build uit te voeren, wat prima is voor het testen. Vanwege beveiligingsimplicaties moet u echter een [Azure VM-agent](https://plugins.jenkins.io/azure-vm-agents) of [Azure Container-agent](https://plugins.jenkins.io/azure-container-agents) gebruiken om een agent in Azure te draaien om uw builds uit te voeren. Zie het Jenkins-artikel over de [beveiligingsimplicaties van bouwen op de master](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master) voor meer informatie.

### <a name="install-plug-ins"></a>Plug-ins installeren

1. Meld u aan bij uw Jenkins-server. Kies **Jenkins beheren > Plug-ins beheren.**
2. Selecteer op het tabblad **Beschikbaar** de volgende plug-ins:
    * [GitHub-integratie](https://plugins.jenkins.io/github-pullrequest)
    * [Azure-referentie](https://plugins.jenkins.io/azure-credentials)

    Als deze plug-ins niet in de lijst worden weergegeven, controleert u het tabblad **Geïnstalleerd** om te zien of ze al zijn geïnstalleerd.

3. Als u de plug-ins wilt installeren, kiest u **Nu downloaden en installeren na het opnieuw opstarten.**

4. Start de Jenkins-server opnieuw op om de installatie te voltooien.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Uw Azure Service Principal-referentie toevoegen in het Jenkins-referentiearchief

1. U hebt een Azure Service Principal nodig om te implementeren in Azure. Zie de [hoofdsectie Service maken](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) in de zelfstudie Implementeren voor Azure App Service voor meer informatie. De output `az ad sp create-for-rbac` van ziet er ongeveer als volgt uit:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Selecteer op het Jenkins-dashboard **het systeem voor referenties** > **.** Selecteer vervolgens **Algemene referenties (onbeperkt)**.

3. Selecteer **Referenties toevoegen**. 

4. Selecteer **Microsoft Azure Service Principal** als soort.

5. Leveringswaarden voor: * Abonnements-ID: gebruik uw Azure-abonnements-ID * Client-id: gebruik `appId` * Client Secret: gebruik `password` * Tenant ID: gebruik `tenant` * Azure Environment: selecteer een vooraf ingestelde waarde. Gebruik bijvoorbeeld **Azure** voor Azure Global * ID: instellen als **azure_service_principal**. We gebruiken deze ID in een latere stap in dit artikel * Beschrijving: is een optioneel veld. Wij raden u aan om hier een zinvolle waarde te bieden.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Maven en Az CLI spring-cloud extensie installeren

De voorbeeldpijplijn gebruikt Maven om te bouwen en Az CLI te implementeren in de serviceinstantie. Wanneer Jenkins is geïnstalleerd, wordt een beheerdersaccount gemaakt met de naam *Jenkins*. Zorg ervoor dat de *gebruiker* toestemming heeft om de veerwolk-extensie uit te voeren.

1. Maak verbinding met de Jenkins-master via SSH. 

2. Maven installeren

    ```bash
        sudo apt-get install maven 
    ```

3. Installeer de Azure CLI. Zie [Azure CLI installeren voor](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)meer informatie. Azure CLI is standaard geïnstalleerd als u [Jenkins Master gebruikt op Azure.](https://aka.ms/jenkins-on-azure)

4. Overschakelen `jenkins` naar de gebruiker:

    ```bash
        sudo su jenkins
    ```

5. Voeg de **veerwolk-extensie** toe:

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Een Jenkinsfile maken
1. Maak in jehttps://github.com/&lteigen repo (&gt;;uw GitHub id / piggymetrics), maak een **Jenkinsfile** in de root.

2. Werk het bestand als volgt bij. Zorg ervoor dat u de waarden vervangt van de naam van ** \<de brongroep>** en ** \<de naam van de service>**. Vervang **azure_service_principal** door de juiste ID als u een andere waarde gebruikt wanneer u de referentie in Jenkins hebt toegevoegd. 

```groovy
    node {
      stage('init') {
        checkout scm
      }
      stage('build') {
        sh 'mvn clean package'
      }
      stage('deploy') {
        withCredentials([azureServicePrincipal('azure_service_principal')]) {
          // login to Azure
          sh '''
            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
            az account set -s $AZURE_SUBSCRIPTION_ID
          '''  
          // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
          sh 'az configure --defaults group=<resource group name>'
          sh 'az configure --defaults spring-cloud=<service name>'
          // Deploy applications
          sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
          sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
          sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
          sh 'az logout'
        }
      }
    }
```

3. Sla en bega de verandering.

## <a name="create-the-job"></a>De taak maken

1. Klik op het Jenkins-dashboard op **Nieuw item**.

2. Geef een naam op, *Deploy-PiggyMetrics* voor de taak en selecteer **Pijplijn**. Klik op OK.

3. Klik nu op het tabblad **Pijplijn**.

4. Selecteer bij **Definitie** de optie **Pijplijnscript uit SCM**.

5. Bij **SCM** selecteert u **Git**.

6. Voer de GitHub URL in voor uw gevorkte repo: ** https://github.com/&lt;uw&gt;GitHub id /piggymetrics.git**

7. Controleer of **Branch Specifier (zwart voor 'any')** ***/Azure** is

8. **Scriptpad** behouden als **Jenkinsfile**

7. Klik **op Opslaan**

## <a name="validate-and-run-the-job"></a>De taak valideren en uitvoeren

Voordat u de taak uitvoert, werken we de tekst in het invoervak bij om **inlog-id in**te voeren.

1. In uw eigen repo, open `index.html` in **/gateway/src/main/resources/static/**

2. Zoek naar 'voer je login in' en werk bij om 'inlog-ID in te voeren'

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. De wijzigingen vastleggen

4. Voer de klus in Jenkins handmatig uit. Klik op het Jenkins-dashboard op de taak *Deploy-PiggyMetrics* en **bouw nu**.

Nadat de taak is voltooid, navigeert u naar het openbare IP-adres van de **gatewaytoepassing** en controleert u of uw toepassing is bijgewerkt. 

![Bijgewerkte Piggy Metrics](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de bronnen die in dit artikel zijn gemaakt wanneer dit niet meer nodig is:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Azure CLI in Jenkins gebruiken om continue integratie en levering (CI/CD) voor Azure Spring Cloud te automatiseren.

Zie de Jenkins on Azure-site voor meer informatie over azure Jenkins-provider.

> [!div class="nextstepaction"]
> [Jenkins op Azure](/azure/jenkins/)
