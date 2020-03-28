---
title: De Azure CLI uitvoeren met Jenkins
description: Leer hoe u de Azure CLI kunt gebruiken om een Java-web-app te implementeren naar Azure in de Jenkins-pijplijn
keywords: jenkins, azure, devops, app service, cli
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: bd9192974f6860d08d84a9028702ce2203f562e7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74158831"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Implementeren in Azure App Service met Jenkins en de Azure CLI
Als u een Java-web-app wilt implementeren in Azure, kunt u de Azure CLI in de [Jenkins-pijplijn](https://jenkins.io/doc/book/pipeline/) gebruiken. In deze zelfstudie maakt u een CI/CD-pijplijn op een virtuele machine in Azure. U leert onder andere:

> [!div class="checklist"]
> * Een Jenkins-VM maken
> * Jenkins configureren
> * Een web-app maken in Azure
> * Een GitHub-opslagplaats voorbereiden
> * Een Jenkins-pijplijn maken
> * De pijplijn uitvoeren en de web-app controleren

Voor deze zelfstudie is versie 2.0.4 of hoger van de Azure CLI vereist. Voer `az --version` uit om de versie te bekijken. Als u wilt upgraden, raadpleegt u [Azure CLI installeren]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Een Jenkins-exemplaar maken en configureren
Als u nog geen Jenkins-master hebt, begint u met de [oplossingssjabloon](install-jenkins-solution-template.md), die standaard de vereiste [Azure-invoeginstellingen](https://plugins.jenkins.io/azure-credentials) bevat. 

Met de Azure Credential-plug-in u microsoft Azure-servicehoofdreferenties opslaan in Jenkins. In versie 1.2 hebben we de ondersteuning toegevoegd, zodat de Jenkins-pijplijn de Azure-referenties kan ophalen. 

Controleer of u versie 1.2 of hoger hebt:
* Klik in het dashboard van Jenkins op **Jenkins beheren -> Invoegtoepassingenbeheer ->** en zoek naar **Azure Credential**. 
* Werk de plug-in bij als de versie eerder is dan 1.2.

Java JDK en Maven zijn ook vereist in de Jenkins-master. Als u deze wilt installeren, meldt u zich aan bij de Jenkins-master met SSH en voert u de volgende opdrachten uit:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Azure-service-principal toevoegen aan Jenkins-referenties

Azure-referenties zijn nodig om de Azure CLI uit te voeren.

* Klik in het dashboard van Jenkins op **Referenties -> Systeem ->**. Klik op **Algemene referenties (onbeperkt)**.
* Klik op **Referenties toevoegen** als u een [Microsoft Azure-service-principal](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) wilt toevoegen door Abonnements-id, Client-id, Clientgeheim en OAuth 2.0 Token-eindpunt in te vullen. Geef een id op voor gebruik in de volgende stap.

![Referenties toevoegen](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Een Azure App Service maken voor het implementeren van de Java-web-app

Maak een Azure App Service-plan met de prijscategorie **GRATIS** met behulp van de CLI-opdracht [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create). Het App Service-plan definieert de fysieke resources die gebruikt worden voor het hosten van uw apps. Alle toepassingen die zijn toegewezen aan een App Service-plan delen deze resources, zodat u kosten kunt besparen als u meerdere apps host. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Wanneer het plan klaar is, laat de Azure CLI uitvoer zien die vergelijkbaar is met het volgende voorbeeld:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Een Azure-web-app maken

 Gebruik de CLI-opdracht [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) om de definitie van een web-app te maken in het `myAppServicePlan` App Service-plan. De definitie van de web-app biedt een URL voor toegang tot uw toepassing en configureert diverse opties voor het implementeren van uw code naar Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Vervang de tijdelijke aanduiding `<app_name>` met uw eigen unieke app-naam. Deze naam maakt deel uit van de standaarddomeinnaam voor de web-app, daarom moet de naam uniek zijn voor alle apps in Azure. U kunt een aangepaste domeinnaam toewijzen aan de web-app voordat u deze beschikbaar maakt voor uw gebruikers.

Wanneer de definitie van de web-app is gemaakt, toont de Azure CLI soortgelijke informatie als in het volgende voorbeeld: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Java configureren 

Stel de Java-runtimeconfiguratie in die uw app nodig heeft met de opdracht [az appservice web config update](/cli/azure/webapp/config).

De volgende opdracht configureert de web-app om op een recente Java 8 JDK en [Apache Tomcat](https://tomcat.apache.org/) 8.0 te worden uitgevoerd.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Een GitHub-opslagplaats voorbereiden
Open de opslagplaats [Eenvoudige Java-web-App voor Azure](https://github.com/azure-devops/javawebappsample). Om de opslagplaats naar uw eigen GitHub-account te vertakken, klikt u rechtsboven op de knop **Fork**.

* Op het bestand **Jenkinsfile** in de GitHub-webgebruikersinterface. Klik op het potloodpictogram om in dit bestand de resourcegroep en de naam van uw web-app bij te werken op respectievelijk regel 20 en 21.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Wijzig regel 23 om de referentie-id in uw Jenkins-exemplaar bij te werken

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Een Jenkins-pijplijn maken
Open Jenkins in een webbrowser en klik op **Nieuw item**. 

* Geef een naam voor de taak op en selecteer **Pijplijn**. Klik op **OK**.
* Klik nu op het tabblad **Pijplijn**. 
* Selecteer bij **Definitie** de optie **Pijplijnscript uit SCM**.
* Bij **SCM** selecteert u **Git**.
* Voer de GitHub-URL voor de vertakte opslagplaats in: https:\<uw vertakte opslagplaats\>.git
* Klik **op Opslaan**

## <a name="test-your-pipeline"></a>Test uw pijplijn
* Ga naar de pijplijn die u hebt gemaakt en klik op **Nu maken**
* Dit is binnen een paar seconden voltooid, waarna u hierheen kunt gaan en op **Console-uitvoer** kunt klikken om de details te bekijken

## <a name="verify-your-web-app"></a>Uw web-app verifiëren
Verifiëren dat het WAR-bestand in uw web-app is geïmplementeerd. Open een webbrowser:

* Ga naar http://&lt;app-naam>.azurewebsites.net/api/calculator/ping  
U ziet:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Ga naar http://&lt;app-naam>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (vervang &lt;x> en &lt;y> door getallen) om de som van x en y te berekenen

![Calculator: optellen](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Implementeren in Azure Web App on Linux
Nu u weet hoe u Azure CLI gebruikt in uw Jenkins-pijplijn, kunt u het script wijzigen om te implementeren in Azure Web App on Linux.

Web App on Linux ondersteunt een andere implementatiemethode. Hierbij wordt namelijk Docker gebruikt. Om uw web-app te implementeren met Docker moet u een Dockerfile opgeven waarmee uw web-app met een serviceruntime in een Docker-installatiekopie wordt verpakt. De plug-in bouwt vervolgens de afbeelding, duwt deze naar een Docker-register en implementeert de afbeelding in uw web-app.

* Voer de stappen [hier](../app-service/containers/quickstart-nodejs.md) uit om een Azure-web-app te maken die wordt uitgevoerd op Linux.
* Installeer Docker op uw Jenkins-exemplaar door de instructies in dit [artikel](https://docs.docker.com/engine/installation/linux/ubuntu/) te volgen.
* Maak een containerregister in de Azure-portal door [deze](/azure/container-registry/container-registry-get-started-azure-cli) stappen uit te voeren.
* In dezelfde opslagplaats [Eenvoudige Java-web-app voor Azure](https://github.com/azure-devops/javawebappsample) die u hebt vertakt, bewerkt u het bestand **Jenkinsfile2**:
    * Werk regel 18-21 bij naar de namen van uw resourcegroep, web-app en ACR. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Werk op regel 24 \<azsrvprincipal\> bij naar uw referentie-id
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Maak een nieuwe Jenkins-pijplijn, net zoals bij de implementatie in de Azure-web-app in Windows, maar gebruik nu **Jenkinsfile2**.
* Voer uw nieuwe taak uit.
* Voer het volgende uit in de Azure CLI om te verifiëren:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    U krijgt het volgende resultaat:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Ga naar http://&lt;app-naam>.azurewebsites.net/api/calculator/ping. U ziet het bericht: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Ga naar http://&lt;app-naam>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (vervang &lt;x> en &lt;y> door getallen) om de som van x en y te berekenen
    
## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een Jenkins-pijplijn geconfigureerd waarmee de broncode in de GitHub-opslagplaats wordt uitgecheckt. Gebruikt Maven om een WAR-bestand te maken en gebruikt vervolgens de Azure CLI om te implementeren in Azure App Service. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Jenkins-VM maken
> * Jenkins configureren
> * Een web-app maken in Azure
> * Een GitHub-opslagplaats voorbereiden
> * Een Jenkins-pijplijn maken
> * De pijplijn uitvoeren en de web-app controleren
