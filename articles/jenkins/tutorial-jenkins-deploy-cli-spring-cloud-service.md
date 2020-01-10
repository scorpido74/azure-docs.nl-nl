---
title: Apps implementeren in azure lente-Cloud met Jenkins en Azure CLI
description: Meer informatie over hoe u Azure CLI in een continue integratie-en implementatie pijplijn kunt gebruiken om micro services te implementeren in azure lente-Cloud service
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: 67ad97bb762ed302ef52c404d47c5755ea4b245b
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75734976"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Zelf studie: Apps implementeren in azure lente-Cloud met Jenkins en Azure CLI

[Azure lente Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) is een volledig beheerde micro service-ontwikkeling met ingebouwd Service detectie-en configuratie beheer. Met deze service kunt u eenvoudig op micro service gebaseerde micro Services-toepassingen implementeren in Azure. In deze zelf studie ziet u hoe u Azure CLI in Jenkins kunt gebruiken om doorlopende integratie en levering (CI/CD) voor Azure lente-Cloud te automatiseren.

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een service-exemplaar inrichten en een Java lente-toepassing starten
> * Uw Jenkins-server voorbereiden
> * De Azure CLI gebruiken in een Jenkins-pijp lijn voor het bouwen en implementeren van de micro service-toepassingen 

In deze zelf studie wordt ervan uitgegaan dat de kern kennis van Azure-Services, Azure veer Cloud, Jenkins- [pijp lijnen](https://jenkins.io/doc/book/pipeline/) en-invoeg toepassingen en github.

## <a name="prerequisites"></a>Vereisten

>[!Note]
> Azure lente Cloud wordt momenteel aangeboden als een open bare preview. Met open bare preview-aanbiedingen kunnen klanten experimenteren met nieuwe functies vóór hun officiële release.  Open bare preview-functies en-services zijn niet bedoeld voor gebruik in productie omgevingen.  Raadpleeg voor meer informatie over ondersteuning tijdens previews onze [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) of bestand a [ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) voor meer informatie.

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

* Een GitHub-account. Als u geen GitHub-account hebt, maakt u een [gratis account](https://github.com/) voordat u begint.

* Een Jenkins-masterserver. Als u nog geen Jenkins-master hebt, implementeert u [Jenkins](https://aka.ms/jenkins-on-azure) op Azure door de stappen in deze [Snelstartgids](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)te volgen. Het volgende is vereist voor het Jenkins-knoop punt/de agent (bijvoorbeeld. Build-server):

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3,0 of hoger](https://maven.apache.org/download.cgi)
    * [Azure cli geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest), versie 2.0.67 of hoger

    >[!TIP]
    > Hulpprogram ma's zoals git, JDK, AZ CLI en Azure plug-Ius zijn standaard opgenomen in de Azure Marketplace [micro soft Jenkins](https://aka.ms/jenkins-on-azure) Solution-sjabloon.
    
* [Registreren voor een Azure-abonnement](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Een service-exemplaar inrichten en een Java lente-toepassing starten

We gebruiken [Piggy-metrische gegevens](https://github.com/Azure-Samples/piggymetrics) als de voor beeld-service toepassing van micro soft en volgen dezelfde stappen in [Quick Start: een Java-lente toepassing starten met de Azure cli](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) om het service-exemplaar in te richten en de toepassingen in te stellen. Als u hetzelfde proces al hebt door lopen, kunt u door gaan naar de volgende sectie. In de volgende gevallen zijn de Azure CLI-opdrachten opgenomen. Raadpleeg [Quick Start: een Java-lente toepassing starten met behulp van de Azure cli](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) om aanvullende achtergrond informatie te krijgen.

Uw lokale machine moet voldoen aan dezelfde vereisten als de Jenkins-buildserver. Zorg ervoor dat het volgende is geïnstalleerd voor het bouwen en implementeren van de micro service-toepassingen:
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3,0 of hoger](https://maven.apache.org/download.cgi)
    * [Azure cli geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest), versie 2.0.67 of hoger

1. De Azure veer Cloud-extensie installeren:

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Maak een resource groep die uw Azure lente-Cloud service bevat:

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Een exemplaar van de Azure lente-Cloud inrichten:

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Splits de [metrische Piggy-gegevens](https://github.com/Azure-Samples/piggymetrics) opslag plaats naar uw eigen github-account. Kloon uw opslag plaats in een map met de naam `source-code`op uw lokale computer:

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Stel de configuratie server in. Zorg ervoor dat u &lt;uw GitHub-id&gt; vervangen door de juiste waarde.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Bouw het project:

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Maak de drie micro Services: **Gateway**, **auth-service**en **account-service**:

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. De toepassingen implementeren: 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. Openbaar eind punt toewijzen aan gateway:

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Vraag de gateway toepassing om de URL op te halen, zodat u kunt controleren of de toepassing wordt uitgevoerd.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Ga naar de URL die door de vorige opdracht is gegeven om de PiggyMetrics-toepassing uit te voeren. 

## <a name="prepare-jenkins-server"></a>Jenkins-server voorbereiden

In deze sectie bereidt u de Jenkins-server voor op het uitvoeren van een build. Dit is een goed voor testen. Vanwege beveiligings implicatie moet u echter een [Azure VM-agent](https://plugins.jenkins.io/azure-vm-agents) of [Azure container agent](https://plugins.jenkins.io/azure-container-agents) gebruiken om een agent in azure uit te breiden om uw builds uit te voeren. Zie het Jenkins-artikel over de [beveiligingsimplicaties van bouwen op de master](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master) voor meer informatie.

### <a name="install-plug-ins"></a>Invoeg toepassingen installeren

1. Meld u aan bij uw Jenkins-server. Kies **Manage Jenkins > plugins beheren**.
2. Selecteer de volgende invoeg toepassingen op het tabblad **beschikbaar** :
    * [GitHub-integratie](https://plugins.jenkins.io/github-pullrequest)
    * [Azure-referentie](https://plugins.jenkins.io/azure-credentials)

    Als deze invoeg toepassingen niet in de lijst worden weer gegeven, gaat u naar het tabblad **geïnstalleerd** om te controleren of ze al zijn geïnstalleerd.

3. Als u de invoeg toepassingen wilt installeren, kiest u **nu downloaden en installeren na opnieuw opstarten**.

4. Start de Jenkins-server opnieuw op om de installatie te volt ooien.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Uw Azure-Service-Principal-referenties toevoegen in Jenkins referentie archief

1. U hebt een Azure-Service-Principal nodig om te implementeren in Azure. Zie de sectie [Service-Principal maken](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) in de zelf studie implementeren naar Azure app service voor meer informatie. De uitvoer van `az ad sp create-for-rbac` ziet er ongeveer als volgt uit:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Selecteer **Referenties** > **Systeem** op het Jenkins-dashboard. Selecteer vervolgens **Algemene referenties (onbeperkt)** .

3. Selecteer **referenties toevoegen**. 

4. Selecteer **Microsoft Azure Service-Principal** als soort.

5. Leverings waarden voor: * abonnements-ID: gebruik uw Azure-abonnement-ID * client-ID: gebruik `appId` * client geheim: gebruik `password` * Tenant-ID: gebruik `tenant` * Azure-omgeving: Selecteer een vooraf ingestelde waarde. Gebruik bijvoorbeeld **Azure** voor Azure Global * id: ingesteld als **azure_service_principal**. We gebruiken deze ID in een latere stap in dit artikel * beschrijving: is een optioneel veld. We raden u aan hier een zinvolle waarde op te geven.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Maven en AZ CLI lente-Cloud extensie installeren

De voorbeeld pijplijn gebruikt maven om te bouwen en AZ CLI om te implementeren in het service-exemplaar. Wanneer Jenkins is geïnstalleerd, wordt er een beheerders account gemaakt met de naam *Jenkins*. Zorg ervoor dat de gebruiker *Jenkins* toestemming heeft de uitbrei ding van de lente-Cloud uit te voeren.

1. Maak via SSH verbinding met de Jenkins-Master. 

2. Maven installeren

    ```bash
        sudo apt-get install maven 
    ```

3. Installeer de Azure CLI. Zie [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)voor meer informatie. Azure CLI wordt standaard geïnstalleerd als u de [Jenkins-Master op Azure](https://aka.ms/jenkins-on-azure)gebruikt.

4. Schakel over naar de `jenkins` gebruiker:

    ```bash
        sudo su jenkins
    ```

5. Voeg de uitbrei ding voor de **lente-Cloud** toe:

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Een Jenkinsfile maken
1. Maak in uw eigen opslag plaats (https://github.com/&lt ; uw GitHub-id&gt; /piggymetrics) een **Jenkinsfile** in de hoofdmap.

2. Werk het bestand als volgt bij. Zorg ervoor dat u de waarden van **\<resource groeps naam >** en **\<service naam >** vervangt. Vervang **azure_service_principal** door de juiste id als u een andere waarde gebruikt bij het toevoegen van de referentie in Jenkins. 

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

3. Sla de wijziging op en voer deze door.

## <a name="create-the-job"></a>De taak maken

1. Klik op het Jenkins-dash board op **Nieuw item**.

2. Geef een naam op, *Implementeer PiggyMetrics* voor de taak en selecteer **pijp lijn**. Klik op OK.

3. Klik nu op het tabblad **Pijplijn**.

4. Selecteer bij **Definitie** de optie **Pijplijnscript uit SCM**.

5. Bij **SCM** selecteert u **Git**.

6. Voer de GitHub-URL in voor uw gevorkte opslag plaats: **https://github.com/&lt ; uw github-id&gt; /piggymetrics.git**

7. Zorg ervoor dat **de vertakkings aanduiding (zwart voor ' any ')** * **/Azure** is

8. Het **pad naar het script** blijven als **Jenkinsfile**

7. Klik op **Opslaan**.

## <a name="validate-and-run-the-job"></a>De taak valideren en uitvoeren

Voordat u de taak uitvoert, moet u de tekst in het invoervak login bijwerken om **aanmeldings-id**in te voeren.

1. Open `index.html` in uw eigen opslag plaats in **/Gateway/src/main/resources/static/**

2. Zoek naar "Voer uw aanmelding in en werk bij" aanmeldings-ID opgeven "

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. De wijzigingen door voeren

4. Voer de taak hand matig uit in Jenkins. Klik op het Jenkins-dash board op taak *implementeren-PiggyMetrics* en **nu maken**.

Nadat de taak is voltooid, gaat u naar het open bare IP-adres van de **Gateway** toepassing en controleert u of uw toepassing is bijgewerkt. 

![Piggy metrische gegevens bijgewerkt](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die in dit artikel zijn gemaakt:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Azure CLI in Jenkins kunt gebruiken om doorlopende integratie en levering (CI/CD) voor Azure lente-Cloud te automatiseren.

Zie de Jenkins op Azure-site voor meer informatie over de Azure Jenkins-provider.

> [!div class="nextstepaction"]
> [Jenkins op Azure](/azure/jenkins/)
