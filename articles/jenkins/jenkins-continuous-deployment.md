---
title: Implementeren van GitHub naar AKS met Jenkins
titleSuffix: Azure Kubernetes Service
description: Jenkins instellen voor continue integratie (CI) van GitHub en continuous deployment (CD) naar Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: a93cfc77178ff7638217663c2ceda500aae4cfd7
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668640"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Zelfstudie: Implementeren van GitHub naar Azure Kubernetes Service (AKS) met continue integratie en implementatie van Jenkins

In deze zelfstudie wordt een voorbeeld-app van GitHub geïmplementeerd in een [AKS-cluster (Azure Kubernetes Service)](/azure/aks/intro-kubernetes) door continue integratie (CI) en continue implementatie (CD) in Jenkins in te stellen. Op die manier voert Jenkins, wanneer u uw app bijwerkt door commits naar GitHub te pushen, automatisch een nieuwe containerbuild uit, worden containerafbeeldingen naar Azure Container Registry (ACR) gepusht en voert u uw app vervolgens uit in AKS. 

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Implementeer een voorbeeld van een Azure-stem-app in een AKS-cluster.
> * Maak een basis Jenkins project.
> * Stel referenties in voor Jenkins om te communiceren met ACR.
> * Maak een Jenkins build job en GitHub webhook voor geautomatiseerde builds.
> * Test de CI/CD-pijplijn om een toepassing in AKS bij te werken op basis van GitHub-codecommits.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u de volgende items nodig:

- Basiskennis van Kubernetes, Git, CI/CD en containerafbeeldingen

- Een [AKS-cluster](../aks/kubernetes-walkthrough.md) en `kubectl` geconfigureerd met de [AKS-clusterreferenties](/cli/azure/aks#az-aks-get-credentials).

- Een [ACR-register (Azure Container Registry),](../container-registry/container-registry-get-started-azure-cli.md)de naam van de ACR-inlogserver en het AKS-cluster dat is geconfigureerd om te [verifiëren met het ACR-register](../aks/cluster-container-registry-integration.md).

- De Azure CLI-versie 2.0.46 of hoger is geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren](/cli/azure/install-azure-cli).

- [Docker geïnstalleerd](https://docs.docker.com/install/) op uw ontwikkelingssysteem

- Een GitHub-account, [GitHub-persoonlijke toegangstoken](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)en Git-client die op uw ontwikkelingssysteem zijn geïnstalleerd

- Als u uw eigen Jenkins-exemplaar opgeeft in plaats van deze voorbeeldgescripte manier om Jenkins te implementeren, moet [docker worden geïnstalleerd en geconfigureerd](https://docs.docker.com/install/) en [worden](https://kubernetes.io/docs/tasks/tools/install-kubectl/)geconfigureerd.

## <a name="prepare-your-app"></a>Uw app voorbereiden

In dit artikel gebruikt u een voorbeeld van Azure-stemtoepassing die een webinterface bevat die in een of meer pods wordt gehost, en een tweede pod met Redis voor tijdelijke gegevensopslag. Voordat u Jenkins en AKS integreert voor geautomatiseerde implementaties, moet u de Azure-stemtoepassing eerst handmatig voorbereiden en implementeren in uw AKS-cluster. Deze handmatige implementatie is versie één van de toepassing en laat u de toepassing in actie zien.

> [!NOTE]
> De voorbeeldtoepassing Azure-stem maakt gebruik van een Linux-pod die is gepland om te worden uitgevoerd op een Linux-knooppunt. De stroom die in dit artikel wordt beschreven, werkt ook voor een Windows Server-pod die is gepland op een Windows Server-knooppunt.

Fork de volgende GitHub repository [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis)voor de voorbeeldtoepassing - . Om de opslagplaats naar uw eigen GitHub-account te vertakken, selecteert u de knop **Fork** in de rechterbovenhoek.

Kloon de vork naar je ontwikkelingssysteem. Zorg ervoor dat u de URL van uw vork gebruikt bij het klonen van deze repo:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Wijzigen in de map van uw gekloonde vork:

```console
cd azure-voting-app-redis
```

Als u de containerafbeeldingen wilt maken die nodig zijn voor de `docker-compose`voorbeeldtoepassing, gebruikt u het *docker-compose.yaml-bestand* met:

```console
docker-compose up -d
```

De vereiste basisbeelden worden getrokken en de toepassingscontainers gebouwd. U vervolgens de opdracht [dockerafbeeldingen](https://docs.docker.com/engine/reference/commandline/images/) gebruiken om de gemaakte afbeelding te bekijken. Er zijn drie installatiekopieën gedownload of gemaakt. De `azure-vote-front`-installatiekopie bevat de toepassing en gebruikt de `nginx-flask`-installatiekopie als basis. De `redis` afbeelding wordt gebruikt om een Redis-exemplaar te starten:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Voordat u de *azure-vote-front* containerafbeelding naar ACR pushen, krijgt u uw ACR-inlogserver met de opdracht [az acr-lijst.](/cli/azure/acr#az-acr-list) In het volgende voorbeeld wordt het ACR-inlogserveradres voor een register in de brongroep *myResourceGroup gekregen:*

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Gebruik de opdracht [dockertag](https://docs.docker.com/engine/reference/commandline/tag/) om de afbeelding te taggen met `v1`de naam van de ACR-inlogserver en een versienummer van . Geef uw `<acrLoginServer>` eigen naam op die in de vorige stap is verkregen:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Tot slot, duw de *azure-vote-front* afbeelding naar uw ACR register. Nogmaals, `<acrLoginServer>` vervangen door de login server naam van `myacrregistry.azurecr.io`uw eigen ACR register, zoals:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>De voorbeeldtoepassing implementeren op AKS

Als u de voorbeeldtoepassing wilt implementeren in uw AKS-cluster, u het Kubernetes-manifestbestand gebruiken in de hoofdmap van de repo van de Azure-stemrepository. Open het *azure-vote-all-in-one-redis.yaml-manifestbestand* met `vi`een editor zoals . Vervang `microsoft` door de naam van de ACR-aanmeldingsserver. Deze waarde is te vinden op regel **47** van het manifestbestand:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Gebruik vervolgens de opdracht [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) om de toepassing te implementeren in uw AKS-cluster:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Er wordt een Kubernetes load balancer-service gemaakt om de toepassing bloot te stellen aan het internet. Dit proces kan enkele minuten duren. Als u de voortgang van de implementatie van de load balancer wilt controleren, gebruikt u de opdracht [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) met het `--watch` argument. Zodra het *EXTERNE-IP-adres* is gewijzigd van `Control + C` in *behandeling* naar een *IP-adres,* gebruikt u het kubectl-horlogeproces om te stoppen.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Als u de toepassing in actie wilt zien, opent u een webbrowser naar het externe IP-adres van uw service. De Azure-stemtoepassing wordt weergegeven, zoals in het volgende voorbeeld wordt weergegeven:

![Azure-voorbeeldstemtoepassing die wordt uitgevoerd in AKS](media/jenkins-continuous-deployment/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Jenkins implementeren op een Azure VM

Als u Jenkins snel wilt implementeren voor gebruik in dit artikel, u het volgende script gebruiken om een virtuele Azure-machine te implementeren, netwerktoegang te configureren en een basisinstallatie van Jenkins te voltooien. Voor verificatie tussen Jenkins en het AKS-cluster kopieert het script uw Kubernetes-configuratiebestand van uw ontwikkelingssysteem naar het Jenkins-systeem.

> [!WARNING]
> Dit voorbeeldscript is bedoeld voor demodoeleinden om snel een Jenkins-omgeving in te richten die wordt uitgevoerd op een Azure VM. Het maakt gebruik van de azure custom script extensie om een VM te configureren en vervolgens de vereiste referenties weer te geven. Uw *~/.kube/config* wordt gekopieerd naar de Jenkins VM.

Voer de volgende opdrachten uit om het script te downloaden en uit te voeren. U moet de inhoud van een script [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)bekijken voordat u het uitvoert - .

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Het duurt een paar minuten om de VM te maken en de vereiste componenten voor Docker en Jenkins te implementeren. Wanneer het script is voltooid, wordt een adres voor de Jenkins-server en een sleutel uitgevoerd om het dashboard te ontgrendelen, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Open een webbrowser op de weergegeven URL en voer de ontgrendelsleutel in. Volg de aanwijzingen op het scherm om de Jenkins-configuratie te voltooien:

- Kies **Voorgestelde plug-ins installeren**
- Maak de eerste gebruiker met beheerdersrechten. Voer een gebruikersnaam in, zoals *azureuser,* en geef vervolgens uw eigen beveiligde wachtwoord op. Als laatste typt u een volledige naam en e-mailadres.
- Selecteer **Save and Finish**
- Zodra Jenkins gereed is, selecteert u **Start using Jenkins**
    - Als uw webbrowser een lege pagina weergeeft wanneer u Jenkins gaat gebruiken, moet u de Jenkins-service opnieuw starten. Als u de service opnieuw wilt starten, gaat SSH naar het openbare IP-adres van uw Jenkins-exemplaar en typt u `sudo service jenkins restart`. Zodra de service opnieuw is opgestart, vernieuwt u uw webbrowser.
- Meld u aan bij Jenkins met de gebruikersnaam en het wachtwoord dat u in het installatieproces hebt gemaakt.

## <a name="create-a-jenkins-environment-variable"></a>Een jenkins-omgevingsvariabele maken

Een Jenkins-omgevingsvariabele wordt gebruikt om de naam van de ACR-inlogserver vast te houden. Naar deze variabele wordt verwezen tijdens de Jenkins-buildtaak. Voer de volgende stappen uit om deze omgevingsvariabele te maken:

- **Selecteer** > Jenkins**configureren** configureren aan de linkerkant van de Jenkins-portal
- Selecteer **onder Globale eigenschappen**de optie **Omgevingsvariabelen**. Voeg een variabele `ACR_LOGINSERVER` toe met de naam en de waarde van uw ACR-inlogserver.

    ![Jenkins-omgevingsvariabelen](media/jenkins-continuous-deployment/env-variables.png)

- Klik als u klaar bent, en klik onder aan de configuratiepagina van Jenkins op **Opslaan.**

## <a name="create-a-jenkins-credential-for-acr"></a>Een Jenkins-referentie voor ACR maken

Als u Jenkins wilt toestaan om bijgewerkte containerafbeeldingen naar ACR te bouwen en vervolgens naar ACR te duwen, moet u referenties voor ACR opgeven. Deze verificatie kan gebruik maken van Azure Active Directory-serviceprincipals. In de vereisten hebt u de serviceprincipal voor uw AKS-cluster geconfigureerd met *Reader-machtigingen* voor uw ACR-register. Met deze machtigingen kan het AKS-cluster afbeeldingen uit het ACR-register *halen.* Tijdens het CI/CD-proces bouwt Jenkins nieuwe containerafbeeldingen op basis van toepassingsupdates en moet deze afbeeldingen vervolgens naar het ACR-register *worden geduwd.* Voor scheiding van rollen en machtigingen configureert u nu een serviceprincipal voor Jenkins met *machtigingen voor inzender in* uw ACR-register.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Een serviceprincipal maken voor Jenkins om ACR te gebruiken

Maak eerst een serviceprincipal met de opdracht [AZ Ad SP create-for-rbac:](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Noteer de *appId* en *het wachtwoord* die in uw uitvoer worden weergegeven. Deze waarden worden gebruikt in de volgende stappen om de referentiebron in Jenkins te configureren.

Haal de resource-ID van je ACR-register op met de opdracht [AZ ACR Show](/cli/azure/acr#az-acr-show) en sla deze op als variabele. Geef de naam van uw resourcegroep en de Naam van ACR op:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Maak nu een roltoewijzing om de *servicehoofdbijdragerrechten* toe te wijzen aan het ACR-register. Geef in het volgende voorbeeld uw eigen *appId* op die in de uitvoer wordt weergegeven, een vorige opdracht om de serviceprincipal te maken:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Een referentiebron maken in Jenkins voor de ACR-serviceprincipal

Met de roltoewijzing die in Azure is gemaakt, slaat u nu uw ACR-referenties op in een Jenkins-referentieobject. Deze referenties worden verwezen tijdens de Jenkins build baan.

Klik aan de linkerkant van de Jenkins-portal op **Referenties** > **Jenkins** > **Global-referenties (onbeperkt)** > Referenties**toevoegen**

Controleer of de referentiesoort **gebruikersnaam met wachtwoord** is en voer de volgende items in:

- **Gebruikersnaam** - De *appId* van de serviceprincipal die is gemaakt voor verificatie met uw ACR-register.
- **Wachtwoord** - Het *wachtwoord* van de serviceprincipal die is gemaakt voor verificatie met uw ACR-register.
- **ID** - Identificatie-id, zoals *acr-credentials*

Wanneer het formulier is voltooid, ziet het volgende voorbeeld eruit:

![Een Jenkins-referentieobject maken met de hoofdgegevens van de service](media/jenkins-continuous-deployment/acr-credentials.png)

Klik **op OK** en ga terug naar de Jenkins-portal.

## <a name="create-a-jenkins-project"></a>Een Jenkins-project maken

Selecteer op de startpagina van uw Jenkins-portal **Nieuw item** aan de linkerkant:

1. Voer *azure-vote in* als taaknaam. Kies **Freestyle-project**en selecteer **OK**
1. Selecteer onder de sectie **Algemeen** **GitHub-project** en voer uw gevorkte repo-URL in, zoals *\/https: /github.com/\<your-github-account\>/azure-voting-app-redis*
1. Selecteer onder de sectie **Broncodebeheer** de optie **Git**, voer uw gevorkte repo *.git-URL* in, zoals *\/https: /github.com/\<your-github-account\>/azure-voting-app-redis.git*

1. Selecteer onder de sectie **Triggers bouwen** de optie **GitHub-haaktrigger voor GITscm-polling**
1. Selecteer **onder Omgeving bouwen**de optie Geheime **teksten of bestanden gebruiken**
1. Selecteer **Onder Bindingen**de optie Gebruikersnaam en wachtwoord **toevoegen** > **(gescheiden)**
   - Voer `ACR_ID` in voor de `ACR_PASSWORD` **gebruikersnaamvariabele**en voor de **wachtwoordvariabele**

     ![Jenkins bindingen](media/jenkins-continuous-deployment/bindings.png)

1. Kies ervoor om een **buildstap** van het type **Uitvoer shell toe** te voegen en gebruik de volgende tekst. Dit script bouwt een nieuwe containerafbeelding en duwt deze naar uw ACR-register.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Voeg nog een **buildstap** toe van de **shell Execute en** gebruik de volgende tekst. Dit script werkt de implementatie van de toepassing in AKS bij met de nieuwe containerafbeelding van ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Klik op **Opslaan**, eenmaal voltooid.

## <a name="test-the-jenkins-build"></a>Test de Jenkins build

Voordat je de taak automatiseert op basis van GitHub commits, test je eerst handmatig de Jenkins-build. Deze handmatige build valideert dat de taak correct is geconfigureerd, dat het juiste Kubernetes-verificatiebestand aanwezig is en dat de verificatie met ACR werkt.

Selecteer **Nu bouwen**in het linkermenu van het project .

![Jenkins test build](media/jenkins-continuous-deployment/test-build.png)

De eerste build duurt een minuut of twee als de Docker afbeelding lagen worden naar beneden getrokken naar de Jenkins server. Volgende builds kunnen de in de cache opgeslagen afbeeldingslagen gebruiken om de bouwtijden te verbeteren.

Tijdens het buildproces wordt de GitHub repository gekloond naar de Jenkins build server. Een nieuwe containerafbeelding wordt gebouwd en naar het ACR-register gepusht. Ten slotte wordt de Azure-stemtoepassing die op het AKS-cluster wordt uitgevoerd, bijgewerkt om de nieuwe afbeelding te gebruiken. Omdat er geen wijzigingen zijn aangebracht in de toepassingscode, wordt de toepassing niet gewijzigd als u de voorbeeld-app in een webbrowser bekijkt.

Zodra de build taak is voltooid, klik op **build #1** onder build geschiedenis. Selecteer **Console-uitvoer** en bekijk de uitvoer van het buildproces. De laatste regel moet wijzen op een succesvolle build.

## <a name="create-a-github-webhook"></a>Een GitHub-webhook maken

Met een succesvolle handmatige build compleet, integreer github nu in de Jenkins build. Een webhook kan worden gebruikt om de Jenkins build-taak uit te voeren elke keer dat er een codecommit wordt gemaakt in GitHub. Voer de volgende stappen uit om de GitHub-webhook te maken:

1. Blader naar uw gevorkte GitHub-repository in een webbrowser.
1. Selecteer **Instellingen**, selecteer daarna **Webhooks** aan de linkerkant.
1. Kies ervoor om **webhook toe**te voegen . Voer voor de *payload-URL*, `http://<publicIp:8080>/github-webhook/`waar `<publicIp>` is het IP-adres van de Jenkins-server. Zorg ervoor dat u de trailing opneemt /. Laat de andere standaardinstellingen voor inhoudstype en te activeren op *push* gebeurtenissen.
1. Selecteer **Webhook toevoegen**.

    ![Maak een GitHub webhook voor Jenkins](media/jenkins-continuous-deployment/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>De volledige CI/CD-pijplijn testen

Nu u de hele CI/CD-pijplijn testen. Wanneer u een codecommit naar GitHub pusht, worden de volgende stappen gezet:

1. De GitHub webhook reikt uit naar Jenkins.
1. Jenkins begint de build job en haalt de nieuwste code commit van GitHub.
1. Er wordt gestart met een Docker-build met de bijgewerkte code en de nieuwe containerafbeelding wordt getagd met het nieuwste buildnummer.
1. Deze nieuwe containerafbeelding wordt naar Azure Container Registry gepusht.
1. Uw toepassing die is geïmplementeerd voor Azure Kubernetes Service-updates met de nieuwste containerafbeelding uit het Azure Container Registry-register.

Open op uw ontwikkelingsmachine de gekloonde toepassing met een codeeditor. Open onder de map */azure-vote/azure-vote* het bestand met de naam **config_file.cfg**. Werk de stemwaarden in dit bestand bij naar iets anders dan katten en honden, zoals in het volgende voorbeeld wordt weergegeven:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Wanneer u wordt bijgewerkt, slaat u het bestand op, voert u de wijzigingen door en duwt u deze naar uw fork van de GitHub-opslagplaats. De GitHub webhook activeert een nieuwe build baan in Jenkins. Houd in het Jenkins-webdashboard het bouwproces in de gaten. Het duurt een paar seconden om de nieuwste code te trekken, de bijgewerkte afbeelding te maken en te pushen en de bijgewerkte toepassing in AKS te implementeren.

Zodra de build is voltooid, vernieuwt u uw webbrowser van de voorbeeldtoepassing Azure-stem. Uw wijzigingen worden weergegeven, zoals in het volgende voorbeeld wordt weergegeven:

![Voorbeeld van Azure-stemming in AKS, bijgewerkt door de buildtaak Jenkins](media/jenkins-continuous-deployment/azure-vote-updated.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Jenkins op Azure](/azure/jenkins)