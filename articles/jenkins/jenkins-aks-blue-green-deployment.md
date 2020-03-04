---
title: Implementeren naar de Azure Kubernetes-service met Jenkins en het blauw/groen-implementatie patroon
description: Leer hoe u kunt implementeren naar Azure Kubernetes Service (AKS) met behulp van Jenkins en het blauw/groen-implementatiepatroon.
keywords: jenkins, azure, devops, kubernetes, k8s, aks, blauw/groen-implementatie, continue levering, cd
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 9d6551f910bd99322f844b44130ebb03732df83c
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251481"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Implementeren naar Azure Kubernetes Service (AKS) met behulp van Jenkins en het blauw/groen-implementatiepatroon

Azure Kubernetes Service (AKS) beheert uw gehoste Kubernetes-omgeving en zorgt dat de implementatie en het beheer van toepassingen in containers snel en eenvoudig zijn. Expertise in containerindeling is niet nodig. AKS verlicht ook de last van voortdurende bewerkingen en onderhoud door inrichten, upgraden en resources op aanvraag schalen mogelijk te maken. Toepassingen offline halen is niet nodig. Zie de [AKS-documentatie](/azure/aks/) voor meer informatie over AKS.

Blauw/groen-implementatie is een Azure DevOps-patroon voor continue levering dat erop vertrouwt een bestaande (blauwe) versie live te houden terwijl een nieuwe (groene) wordt geïmplementeerd. Dit patroon maakt meestal gebruik van taakverdeling om steeds meer verkeer naar de groene implementatie te leiden. Als de controle een incident ontdekt, kan verkeer worden omgeleid naar de blauwe implementatie, die nog steeds actief is. Zie [Wat is continue levering?](/azure/devops/what-is-continuous-delivery) voor meer informatie over continue levering.

In deze zelfstudie leert u hoe u de volgende taken uitvoert:

> [!div class="checklist"]
> * Leren over het blauw/groen-implementatiepatroon
> * Een beheerde Kubernetes-cluster maken
> * Een voorbeeldscript uitvoeren om een Kubernetes-cluster te configureren
> * Een Kubernetes-cluster handmatig configureren
> * Een Jenkins-taak maken en uitvoeren

## <a name="prerequisites"></a>Vereisten
- [GitHub-account](https://github.com): U hebt een GitHub-account nodig om de voorbeeldopslagplaats te kunnen klonen.
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest): U gebruikt de Azure CLI 2.0 om de Kubernetes-cluster te maken.
- [Chocolatey](https://chocolatey.org): Een pakketmanager die u gebruikt om kubectl te installeren.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): Een opdrachtregelinterface die u gebruikt om opdrachten uit te voeren op Kubernetes-clusters.
- [jq](https://stedolan.github.io/jq/download/): Een lichtgewicht opdrachtregel-JSON-processor.

## <a name="clone-the-sample-app-from-github"></a>De voorbeeld-app klonen vanuit GitHub

In de Microsoft-opslagplaats in GitHub vindt u een voorbeeld-app die illustreert hoe u kunt implementeren naar AKS met behulp van Jenkins en het blauw/groen-patroon. In deze sectie maakt u een fork van die opslagplaats in uw GitHub en kloont u de app naar uw lokale systeem.

1. Blader naar de GitHub-opslagplaats voor de voorbeeld-app [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Schermafbeelding van voorbeeld-app in Microsoft GitHub-opslagplaats](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Selecteer rechts bovenaan de pagina **Fork** en volg de instructies om de opslagplaats naar uw GitHub-account te vertakken.

    ![Schermafbeelding van GitHub-optie om te vertakken](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Nadat u de opslagplaats hebt vertakt, ziet u dat de accountnaam verandert in uw accountnaam en toont een notitie vanwaaruit de opslagplaats is vertakt (Microsoft).

    ![Schermafbeelding van GitHub-accountnaam en notitie](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Selecteer **Klonen of downloaden**.

    ![Schermafbeelding van GitHub-optie om een opslagplaats te klonen of downloaden](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. Selecteer in het venster **Klonen met HTTPS** het **kopieerpictogram**.

    ![Schermafbeelding van GitHub-optie om de kloon-URL naar het klembord te kopiëren](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Open een terminal of Git Bash-venster.

1. Stel de directory in op de gewenste locatie waar u de lokale kopie (kloon) van de opslagplaats wilt opslaan.

1. Gebruik de opdracht `git clone` om de URL te klonen die u eerder hebt gekopieerd.

    ![Schermafbeelding van git-kloonopdracht in Git Bash](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Druk op de Enter-toets om het kloonproces te starten.

    ![Schermafbeelding van resultaten van git-kloonopdracht in Git Bash](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Ga naar de zojuist gemaakte directory die de kloon van de app-bron bevat.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Een beheerde Kubernetes-cluster maken en configureren

In deze sectie voert u de volgende stappen uit:

- De Azure CLI 2.0 gebruiken om een beheerde Kubernetes-cluster te maken
- Leren hoe u een cluster instelt, met het installatiescript of handmatig
- Een exemplaar van de Azure Container Registry-service maken

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>De Azure CLI 2.0 gebruiken om een beheerde Kubernetes-cluster te maken
Als u een beheerde Kubernetes-cluster wilt maken met de [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), moet u Azure CLI versie 2.0.25 of hoger gebruiken.

1. Meld u aan bij uw Azure-account. Nadat u de volgende opdracht hebt ingevoerd, ontvangt u instructies voor het voltooien van de aanmelding. 
    
    ```azurecli
    az login
    ```

1. Wanneer u in de vorige stap de opdracht `az login` uitvoert, verschijnt er een lijst met al uw Azure-abonnementen (samen met hun abonnements-ID’s). In deze stap stelt u het Azure-standaardabonnement in. Vervang de tijdelijke aanduiding &lt;your-subscription-id> door de gewenste Azure-abonnements-ID. 

    ```azurecli
    az account set -s <your-subscription-id>
    ```

1. Maak een resourcegroep. Vervang de tijdelijke aanduiding &lt;your-resource-group-name> door de naam van uw nieuwe resourcegroep, en vervang de tijdelijke aanduiding &lt;your-location> door de locatie. De opdracht `az account list-locations` toont alle Azure-locaties. Tijdens de AKS-preview zijn niet alle locaties beschikbaar. Als u een locatie invoert die momenteel niet geldig is, toont het foutbericht de beschikbare locaties.

    ```azurecli
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Maak de Kubernetes-cluster. Vervang &lt;your-resource-group-name> door de naam van de resourcegroep die u in de vorige stap hebt gemaakt, en vervang &lt;your-kubernetes-cluster-name> door de naam van uw nieuwe cluster. (Dit proces kan enkele minuten in beslag nemen.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>De Kubernetes-cluster instellen

U kunt een blauw/groen-implementatie in AKS handmatig instellen of met een installatiescript dat in het eerder gekloonde voorbeeld wordt geboden. In deze sectie ziet u hoe u beide kunt doen.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>De Kubernetes-cluster instellen via het installatiescript uit het voorbeeld
1. Bewerk het bestand **deploy/aks/setup/setup.sh** door de volgende tijdelijke aanduidingen te vervangen door de toepasselijke waarden voor uw omgeving: 

   - **&lt;your-resource-group-name>**
   - **&lt;your-kubernetes-cluster-name>**
   - **&lt;your-location>**
   - **&lt;your-dns-name-suffix>**

     ![Schermafbeelding van setup.sh-script in Bash, met een aantal tijdelijke aanduidingen gemarkeerd](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Voer het installatiescript uit.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Een Kubernetes-cluster handmatig instellen 
1. Download de Kubernetes-configuratie naar uw profielmap.

    ```azurecli
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Ga naar de directory **deploy/aks/setup**. 

1. Voer de volgende **kubectl**-opdrachten uit om de services in te stellen voor het openbare eindpunt en de twee testeindpunten.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Werk de DNS-naam bij voor het openbare eindpunt en de testeindpunten. Wanneer u een Kubernetes-cluster maakt, maakt u ook een [aanvullende resourcegroep](https://github.com/Azure/AKS/issues/3), met het naamgevingspatroon **MC_&lt;your-resource-group-name> _&lt;your-kubernetes-cluster-name>_ &lt;your-location>** .

    Zoek de openbare IP’s in de resourcegroep.

    ![Schermafbeelding van de openbare IP’s in de resourcegroep](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Zoek voor elk van de services het externe IP-adres door de volgende opdracht uit te voeren:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Werk de DNS-naam voor het bijbehorende IP-adres bij met de volgende opdracht:

    ```azurecli
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Herhaal de aanroep voor `todoapp-test-blue` en `todoapp-test-green`:

    ```azurecli
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    De DNS-naam moet uniek zijn in uw abonnement. Om de uniekheid te verzekeren, kunt u `<your-dns-name-suffix>` gebruiken.

### <a name="create-an-instance-of-container-registry"></a>Een exemplaar van Container Registry maken

1. Voer de opdracht `az acr create` uit om een exemplaar van Container Registry te maken. In de volgende sectie kunt u dan `login server` als de Docker-register-URL gebruiken.

    ```azurecli
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Voer de opdracht `az acr credential` uit om uw Container Registry-referenties weer te geven. Noteer de gebruikersnaam en het wachtwoord voor het Docker-register, want u hebt deze nodig in de volgende sectie.

    ```azurecli
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>De Jenkins-server voorbereiden

In deze sectie ziet u hoe u de Jenkins-server kunt voorbereiden om een build uit te voeren, die goed is voor testdoeleinden. U moet echter een [Azure VM-agent](https://plugins.jenkins.io/azure-vm-agents) of [Azure Container-agent](https://plugins.jenkins.io/azure-container-agents) gebruiken om een agent in Azure te implementeren voor het uitvoeren van uw builds. Zie het Jenkins-artikel over de [beveiligingsimplicaties van bouwen op de master](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master) voor meer informatie.

1. Implementeer een [Jenkins-master op Azure](https://aka.ms/jenkins-on-azure).

1. Maak verbinding met de server via SSH en installeer de build-hulpprogramma’s op de server waar u uw build uitvoert.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Installeer Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Zorg dat de gebruiker `jenkins` toestemming heeft om de `docker`-opdrachten uit te voeren.

1. [Installeer kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Download jq](https://stedolan.github.io/jq/download/).

1. Installeer jq met de volgende opdracht:

   ```bash
   sudo apt-get install jq
   ```
   
1. Installeer de invoegtoepassingen in Jenkins door op het Jenkins-dashboard de volgende stappen uit te voeren:

    1. Selecteer **Jenkins beheren > Invoegtoepassingen beheren > Beschikbaar**.
    1. Zoek naar en installeer de invoegtoepassing Azure Container Service.

1. Voeg referenties toe om resources in Azure te beheren. Als u de invoeg toepassing nog niet hebt, installeert u de **Azure Credential** -invoeg toepassing.

1. Voeg uw referenties voor de Azure-service-principal in als het type **Microsoft Azure-service-principal**.

1. Voeg uw gebruikersnaam en wachtwoord voor het Azure Docker-register (zoals verkregen in de sectie ‘Een exemplaar van Container Registry maken’) in als het type **Gebruikersnaam met wachtwoord**.

## <a name="edit-the-jenkinsfile"></a>Het Jenkinsfile bewerken

1. Ga in uw eigen opslagplaats naar `/deploy/aks/` en open `Jenkinsfile`.

2. Werk het bestand als volgt bij:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Werk de ID van Container Registry-referenties bij:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>De taak maken
1. Voeg een nieuwe taak toe als het type **Pijplijn**.

1. Selecteer **Pijplijn** > **Definitie** > **Pijplijnscript uit SCM**.

1. Voer de URL van de SCM-opslagplaats in met &lt;uw-vertakte-opslagplaats>.

1. Voer het scriptpad in als `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>De taak uitvoeren

1. Verifieer dat u uw project succesvol kunt uitvoeren in uw lokale omgeving. U doet dit als volgt: [Project uitvoeren op lokale machine](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Voer de Jenkins-taak uit. De eerste keer dat u de taak uitvoert, implementeert Jenkins de taken-app in de blauwe omgeving, die de standaard inactieve omgeving is. 

1. Ga naar de volgende URL’s om te verifiëren dat de taak is uitgevoerd:
    - Openbaar eindpunt - `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Blauw eindpunt - `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Groen eindpunt - `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Het openbare eindpunt en het blauwe testeindpunt hebben dezelfde update, terwijl het groene eindpunt de standaard Tomcat-afbeelding toont. 

Als u de build meer dan één keer uitvoert, doorloopt deze de blauwe en groene implementatie. Met andere woorden, als de huidige omgeving blauw is, implementeert en test de taak in de groene omgeving. Als de tests succesvol zijn, werkt de taak het openbare eindpunt van de toepassing vervolgens bij om verkeer naar de groene omgeving te leiden.

## <a name="additional-information"></a>Aanvullende informatie

Zie deze [snelstartsjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment) voor meer informatie over implementatie zonder downtime. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u in deze zelfstudie hebt gemaakt niet meer nodig hebt, kunt u ze verwijderen.

```azurecli
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Probleemoplossing

Als u problemen ondervindt met de Jenkins-invoegtoepassingen, kunt u in [Jenkins JIRA](https://issues.jenkins-ci.org/) een ticket openen voor het specifieke onderdeel.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u kunt implementeren naar AKS met behulp van Jenkins en het blauw/groen-implementatiepatroon. Zie de website Jenkins op Azure voor meer informatie over de Azure Jenkins-provider.

> [!div class="nextstepaction"]
> [Jenkins op Azure](/azure/jenkins/)