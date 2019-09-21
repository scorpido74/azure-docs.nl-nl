---
title: 'Zelfstudie: Implementeren van GitHub naar Azure App Service met Jenkins'
description: Jenkins instellen voor continue integratie (CI) vanuit GitHub en continue implementatie (CD) in Azure App Service voor Java-web-apps
services: jenkins
ms.service: jenkins
author: tomarchermsft
ms.author: tarcher
manager: jeconnoc
ms.topic: tutorial
ms.date: 11/15/2018
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: c4e4a984adc0ec6af99667ff36c009ca730acf48
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172836"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Zelfstudie: implementatie vanuit GitHub naar Azure App Service met continue integratie en implementatie in Jenkins

In deze zelfstudie implementeert u een voorbeeld van een Java-web-app vanuit GitHub in [Azure App Service op Linux](/azure/app-service/containers/app-service-linux-intro) door het instellen van continue integratie (CI) en continue implementatie (CD) in Jenkins. Wanneer u de app bijwerkt door doorvoeracties te pushen naar GitHub, wordt uw app automatisch door Jenkins gecompileerd en opnieuw gepubliceerd naar Azure App Service. De voorbeeld-app in deze zelfstudie is ontwikkeld met behulp van het [Spring Boot](https://projects.spring.io/spring-boot/)-framework. 

![Overzicht van GitHub naar Azure App Service-implementatie](media/tutorial-jenkins-deploy-web-app-azure-app-service/azure-continuous-integration-deployment-overview.png)

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Installeer Jenkins-invoegtoepassingen, zodat u vanuit GitHub kunt compileren, kunt implementeren in Azure App Service en andere gerelateerde taken kunt uitvoeren.
> * Maak een fork van de GitHub-voorbeeldopslagplaats, zodat u beschikt over een werkende kopie.
> * Verbind Jenkins met GitHub.
> * Maak een Azure-service-principal, zodat Jenkins toegang heeft tot Azure zonder uw referenties te gebruiken.
> * Voeg uw service-principal toe aan Jenkins.
> * Maak de Jenkins-pijplijn waarmee de voorbeeld-app wordt gebouwd en geïmplementeerd telkens wanneer u de app in GitHub bijwerkt.
> * Maak build- en implementatiebestanden voor uw Jenkins-pijplijn.
> * Wijs uw Jenkins-pijplijn naar het build- en implementatiescript.
> * Implementeer uw voorbeeld-app in Azure door een handmatige build uit te voeren.
> * Push een app-update in GitHub, waardoor Jenkins wordt geactiveerd om de app te compileren en te implementeren in Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u de volgende items nodig:

* Een [Jenkins](https://jenkins.io/)-server waarbij de Java Development Kit (JDK) en Maven-hulpprogramma's zijn geïnstalleerd op een virtuele Linux-machine in Azure

  Als u geen Jenkins-server hebt, volg deze stappen dan in de Azure-portal: [Jenkins-server maken in een virtuele Azure Linux-machine](/azure/jenkins/install-jenkins-solution-template)

* Een [GitHub](https://github.com)-account, zodat u een werkende kopie (fork) kunt verkrijgen voor de voorbeeld-Java-web-app. 

* [Azure CLI](/cli/azure/install-azure-cli), die u kunt uitvoeren vanaf uw lokale opdrachtregel of [Azure Cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>Jenkins-invoegtoepassingen configureren

1. Meld u aan bij uw Jenkins-webconsole op deze locatie:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Selecteer op de hoofdpagina van Jenkins de optie **Manage Jenkins** > **Manage Plugins**.

   ![Jenkins-invoegtoepassingen beheren](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. Selecteer op het tabblad **Available** de volgende invoegtoepassingen:

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [GitHub Branch Source](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [Environment Injector Plugin](https://plugins.jenkins.io/envinject)
   - [Azure Credentials](https://plugins.jenkins.io/azure-credentials)

   Als deze invoegtoepassingen niet worden weergegeven, controleer dan of niet al zijn geïnstalleerd. Daartoe opent u het tabblad **Installed**.

1. Selecteer **nu downloaden en installeren na opnieuw opstarten**om de geselecteerde invoeg toepassingen te installeren.

1. Wanneer u klaar bent, selecteert u in het Jenkins-menu de optie **Manage Jenkins** om naar de beheerpagina van Jenkins terug te keren voor verdere stappen.

## <a name="fork-sample-github-repo"></a>Fork maken van voorbeeld-GitHub-opslagplaats

1. [Meld u aan bij GitHub-opslagplaats voor de Spring Boot-voorbeeld-app](https://github.com/spring-guides/gs-spring-boot). 

1. Selecteer **Fork**in de rechter bovenhoek van github.

   ![Fork maken van voorbeeldopslagplaats vanuit GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Volg de aanwijzingen om uw GitHub-account te selecteren en de fork te voltooien.

Vervolgens stelt u Jenkins in met uw GitHub-referenties.

## <a name="connect-jenkins-to-github"></a>Jenkins verbinden met GitHub

Als u wilt dat Jenkins GitHub bewaakt en reageert wanneer er nieuwe doorvoeracties naar uw web-app in de GitHub-fork worden gepusht, schakelt u [GitHub webhooks](https://developer.github.com/webhooks/) in Jenkins in.

> [!NOTE]
> 
> Met deze stappen maakt u met behulp van uw GitHub-gebruikersnaam en -wachtwoord tokenreferenties zodat Jenkins persoonlijke toegang heeft om te werken met GitHub. 
> Als uw GitHub-account echter gebruikmaakt van de tweeledige verificatiemethode, maakt u het token in GitHub en stelt u Jenkins in om dat token te gebruiken. 
> Raadpleeg de Jenkins-documentatie [GitHub Plugin](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin) voor meer informatie.

1. Selecteer op de pagina **Manage Jenkins** de optie **Configure System**. 

   ![Het systeem in Jenkins configureren](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. Geef in de sectie **GitHub** details op voor uw GitHub-server. Selecteer in de lijst **Add GitHub Server** de optie **GitHub Server**. 

   ![GitHub-server toevoegen in Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Als de eigenschap **Manage hooks** niet is geselecteerd, selecteert u deze eigenschap. Selecteer **Geavanceerd** zodat u andere instellingen kunt opgeven. 

   ![Geavanceerde instellingen voor Jenkins voor GitHub-server opgeven](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. Selecteer in de lijst **Manage additional GitHub actions** de optie **Convert login and password to token**.

   ![De aanmelding en het wacht woord naar het token voor GitHub converteren](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Selecteer **From login and password** zodat u uw GitHub-gebruikersnaam en -wachtwoord kunt invoeren. Wanneer u klaar bent, selecteert u **token referenties maken**, waarmee u een [github Personal Access token (Pat)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)maakt.   

   ![GitHub-PAT maken op basis van gebruikersnaam en wachtwoord](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. Ga naar de sectie **GitHub Server** en selecteer het nieuwe token in de lijst **Credentials**. Controleer of verificatie werkt door **Test connection te kiezen**.

   ![Verbinding met GitHub-server controleren met nieuwe PAT](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Vervolgens maakt u de Azure-service-principal die Jenkins gebruikt voor verificatie van en toegang tot Azure-resources.

## <a name="create-service-principal"></a>Een service-principal maken

In een volgende sectie maakt u een Jenkins-pijplijntaak waarmee uw app wordt gecompileerd vanuit GitHub en wordt geïmplementeerd in Azure App Service. Om ervoor te zorgen dat Jenkins toegang heeft tot Azure zonder dat uw referenties moeten worden ingevoerd, maakt u voor Jenkins een [service-principal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) in Azure Active Directory. Een service-principal is een afzonderlijke identiteit die Jenkins kan gebruiken voor de verificatie van en toegang tot Azure-resources. Om deze service-principal te maken, voert u de Azure CLI-opdracht [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) uit vanuit uw lokale opdrachtregel of de Azure Cloud Shell, bijvoorbeeld: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Zorg dat u aanhalingstekens plaatst rond de naam van de service-principal. Zorg er ook voor dat u een sterk wachtwoord maakt op basis van [de regels en beperkingen voor wachtwoorden in Azure Active Directory](/azure/active-directory/active-directory-passwords-policy). Als u geen wachtwoord opgeeft, maakt de Azure CLI een wachtwoord voor u. 

De volgende uitvoer wordt gegenereerd door de opdracht **`create-for-rbac`** : 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> Als u al een service-principal hebt, kunt u die identiteit opnieuw gebruiken.
> Bij het opgeven van de service-principalwaarden voor verificatie, gebruikt u de eigenschapswaarden `appId`, `password` en `tenant`. 
> Gebruik de eigenschapswaarde `displayName` voor het zoeken naar een bestaande service-principal.

## <a name="add-service-principal-to-jenkins"></a>Service-principal toevoegen aan Jenkins

1. Selecteer **Credentials** > **Systeem** op de hoofdpagina van Jenkins. 

1. Selecteer op de pagina **System** onder **Domain** de optie **Global credentials (unrestricted)** .

1. Selecteer **Add Credentials** in het linkermenu.

1. Selecteer in de lijst **Kind** de optie **Azure Service Principal**.

1. Geef de informatie voor uw service-principal en het Azure-abonnement op in de eigenschappen die worden beschreven in de tabel van deze stap:

   ![Referenties voor Azure-service-principal toevoegen](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Eigenschap | Waarde | Description | 
   |----------|-------|-------------| 
   | **Subscription ID** | <*yourAzureSubscription-ID*> | De GUID-waarde voor uw Azure-abonnement <p>**Tip**: als u uw Azure-abonnements-id niet weet, voert u deze Azure CLI-opdracht uit vanaf de opdrachtregel of in de Cloud Shell en gebruikt u vervolgens GUID-waarde `id`: <p>`az account list` | 
   | **Client ID** | <*yourAzureServicePrincipal-ID*> | De GUID-waarde van `appId` die eerder is gegenereerd voor uw Azure-service-principal | 
   | **Client Secret** | <*yourSecurePassword*> | De waarde van `password` of het 'geheim' dat u hebt opgegeven voor uw Azure-service-principal | 
   | **Tenant ID** | <*yourAzureActiveDirectoryTenant-ID*> | De GUID-waarde van `tenant` voor uw Azure Active Directory-tenant | 
   | **ID** | <*yourAzureServicePrincipalName*> | De waarde van `displayName` voor uw Azure-service-principal | 

1. Selecteer **Service-Principal controleren**om te controleren of uw Service-Principal werkt. Wanneer u gereed bent, selecteert u **OK**.

Vervolgens maakt u de Jenkins-pijplijn waarmee uw app wordt gecompileerd en geïmplementeerd.

## <a name="create-jenkins-pipeline"></a>Een Jenkins-pijplijn maken

Maak in Jenkins de pijplijntaak voor het compileren en implementeren van uw app.

1. Ga terug naar de Jenkins-startpagina en selecteer **New Item**. 

   ![Een Jenkins-pijplijn maken](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Geef een naam op voor de pijplijntaak, bijvoorbeeld 'Mijn-Java-Web-App' en selecteer **Pijplijn**. Selecteer aan de onderkant **OK**.  

   ![De Jenkins-pijplijn taak een naam](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Stel Jenkins in met uw service-principal, zodat Jenkins kan implementeren in Azure zonder gebruik te maken van uw eigen referenties.

   1. Selecteer op het tabblad **General** de optie **Prepare an environment for the run**. 

   1. Voeg in het vak **Properties Content** dat nu wordt weergegeven deze omgevingsvariabelen en hun waarden toe. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Een omgeving voorbereiden voor de uitvoering en omgevings variabelen instellen](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-jenkins-run.png)

1. Selecteer **Opslaan** als u klaar bent.

Vervolgens maakt u de build- en implementatiescripts voor Jenkins.

## <a name="create-build-and-deployment-files"></a>Build- en implementatiebestanden maken

Maak nu de bestanden die Jenkins gebruikt om uw app te compileren en te implementeren.

1. Maak in de GitHub-forkmap `src/main/resources/` dit app-configuratiebestand met de naam `web.config`. Dit bestand bevat deze XML, maar u dient `$(JAR_FILE_NAME)` te vervangen door `gs-spring-boot-0.1.0.jar`:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. Maak in de hoofdmap van de GitHub-fork dit build- en implementatiescript met de naam `Jenkinsfile`. Dit bestand bevat deze tekst ([hier bron in GitHub](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. Voer de bestanden `web.config` en `Jenkinsfile` allebei door naar de GitHub-fork en push uw wijzigingen.

## <a name="point-pipeline-at-script"></a>Pijplijn wijzen naar script

Nu geeft u het build- en implementatiescript op dat Jenkins moet gebruiken.

1. Selecteer in Jenkins de eerder gemaakte pijplijntaak. 

   ![De Jenkins-pijplijn taak voor uw web-app selecteren](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. Selecteer **Configure** in het linkermenu.

1. Ga naar het tabblad **Pipeline** en selecteer in de lijst **Definition** de optie **Pipeline script from SCM**.

   1. Selecteer in het vak **SCM** dat wordt weergegeven de optie **Git** als het broncodebeheer. 

   1. Ga naar de sectie **Repositories** en voer bij **Repository URL** de URL van de GitHub-fork in, bijvoorbeeld: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. Selecteer bij **Credentials** het eerder gemaakte token voor persoonlijke toegang tot GitHub.

   1. Voeg in het vak **Script Path** het pad toe naar het script 'Jenkinsfile'.

   Wanneer u klaar bent, ziet de pijplijndefinitie er ongeveer uit als in dit voorbeeld: 

   ![Uw Jenkins-pijp lijn naar het script wijzen](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Selecteer **Opslaan** als u klaar bent.

Vervolgens gaat u de app compileren en implementeren in Azure App Service. 

## <a name="build-and-deploy-to-azure"></a>Compileren en implementeren in Azure

1. Met de Azure CLI maakt u vanaf de opdrachtregel of de Azure Cloud Shell een [Azure App Service-web-app op Linux](/azure/app-service/containers/app-service-linux-intro), waar Jenkins uw web-app implementeert nadat een build is voltooid. Zorg dat uw web-app een unieke naam heeft.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Voor meer informatie over deze Azure CLI-opdrachten raadpleegt u deze pagina's:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. Selecteer in Jenkins de pijplijntaak en selecteer **Build Now**.

   Wanneer de build is voltooid, wordt de app door Jenkins geïmplementeerd, zodat deze nu actief is in Azure op de publicatie-URL, bijvoorbeeld: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Uw geïmplementeerde app op Azure weergeven](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Wijzigingen pushen en opnieuw implementeren

1. Ga in uw webbrowser naar deze locatie in de GitHub-fork van uw web-app:

   `complete/src/main/java/Hello/Application.java`
   
1. Selecteer in de rechter bovenhoek van GitHub de optie **dit bestand bewerken**.

1. Breng deze wijziging aan in de methode `commandLineRunner()` en voer de wijziging door in de vertakking `master` van de opslagplaats. Door deze doorvoering in de vertakking `master` wordt een build in Jenkins gestart. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Wanneer de build is voltooid en Jenkins opnieuw implementeert in Azure, wordt de app vernieuwd en wordt de update weergegeven.

   ![Uw geïmplementeerde app op Azure weergeven](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Problemen met de Jenkins-invoegtoepassing oplossen

Als u problemen ondervindt met de Jenkins-invoegtoepassingen, kunt u in [Jenkins JIRA](https://issues.jenkins-ci.org/) een ticket openen voor het specifieke onderdeel.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure-VM's als bouwagenten gebruiken](/azure/jenkins/jenkins-azure-vm-agents)
