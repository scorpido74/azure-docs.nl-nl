---
title: Zelf studie-implementeren van GitHub naar Azure Kubernetes service (AKS) met Jenkins
description: Jenkins instellen voor continue integratie (CI) van GitHub en doorlopende implementatie (CD) naar Azure Kubernetes service (AKS)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: fd754b539a9b0ba3d47ddd2228365dbd09d6e6df
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595481"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Zelf studie: implementeren van GitHub naar Azure Kubernetes service (AKS) met Jenkins continue integratie en implementatie

In deze zelf studie wordt een voor beeld-app van GitHub geïmplementeerd naar een [Azure Kubernetes service-cluster (AKS)](/azure/aks/intro-kubernetes) door doorlopende integratie (CI) en doorlopende implementatie (cd) in Jenkins in te stellen. Op die manier wordt, wanneer u uw app bijwerkt door door voeringen door te voeren naar GitHub, Jenkins automatisch een nieuwe container build uitgevoerd, container installatie kopieën naar Azure Container Registry (ACR) pusht en vervolgens uw app uitgevoerd in AKS. 

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Implementeer een voor beeld van een Azure stem-app in een AKS-cluster.
> * Een Basic Jenkins-project maken.
> * Referenties instellen voor Jenkins om te communiceren met ACR.
> * Maak een Jenkins-build-taak en GitHub-webhook voor automatische builds.
> * Test de CI/CD-pijp lijn om een toepassing bij te werken in AKS op basis van GitHub code doorvoer.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u de volgende items nodig:

- Basis informatie over Kubernetes, Git, CI/CD en container installatie kopieën

- Een [AKS-cluster][aks-quickstart] en `kubectl` geconfigureerd met de [AKS-cluster referenties][aks-credentials]

- Een [Azure container Registry-REGI ster (ACR)][acr-quickstart], de naam van de ACR-aanmeldings server en het AKS-cluster dat is geconfigureerd voor [verificatie met het ACR-REGI ster][acr-authentication]

- De Azure CLI-versie 2.0.46 of hoger is geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren][install-azure-cli].

- [Docker geïnstalleerd][docker-install] in uw ontwikkel systeem

- Een GitHub-account, een [persoonlijk toegangs token van github][git-access-token]en een Git-client die is geïnstalleerd in uw ontwikkel systeem

- Als u uw eigen Jenkins-exemplaar opgeeft in plaats van deze voorbeeld script manier om Jenkins te implementeren, moet uw Jenkins-exemplaar [docker geïnstalleerd en geconfigureerd][docker-install] en [kubectl][kubectl-install]hebben.

## <a name="prepare-your-app"></a>Uw app voorbereiden

In dit artikel gebruikt u een Azure stem-voorbeeld toepassing die een webinterface bevat die wordt gehost in een of meer peulen en een tweede pod hosting redis voor tijdelijke gegevens opslag. Voordat u Jenkins en AKS integreert voor automatische implementaties, moet u eerst de Azure stem-toepassing hand matig voorbereiden en implementeren op uw AKS-cluster. Deze hand matige implementatie is versie één van de toepassing en laat u zien dat de toepassing in actie wordt uitgevoerd.

> [!NOTE]
> De Azure stem-voorbeeld toepassing maakt gebruik van een Linux-pod die is ingepland om te worden uitgevoerd op een Linux-knoop punt. De stroom die in dit artikel wordt beschreven, werkt ook voor een Windows Server-pod die is gepland op een Windows Server-knoop punt.

Splits de volgende GitHub-opslag plaats voor de voorbeeld toepassing- [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Om de opslagplaats naar uw eigen GitHub-account te vertakken, selecteert u de knop **Fork** in de rechterbovenhoek.

Kloon de Fork naar uw ontwikkel systeem. Zorg ervoor dat u de URL van uw Fork gebruikt bij het klonen van deze opslag plaats:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Ga naar de map van de gekloonde Fork:

```console
cd azure-voting-app-redis
```

Als u de container installatie kopieën wilt maken die nodig zijn voor de voorbeeld toepassing, gebruikt u het *yaml-bestand docker-* refiled met `docker-compose`:

```console
docker-compose up -d
```

De vereiste basis installatie kopieën worden opgehaald en de toepassings containers zijn gebouwd. U kunt vervolgens de opdracht [docker-installatie kopieën][docker-images] gebruiken om de gemaakte installatie kopie te bekijken. Er zijn drie installatiekopieën gedownload of gemaakt. De `azure-vote-front`-installatiekopie bevat de toepassing en gebruikt de `nginx-flask`-installatiekopie als basis. De `redis` installatie kopie wordt gebruikt om een redis-exemplaar te starten:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Voordat u de container installatie kopie van *Azure-stem voor front* kunt pushen naar ACR, moet u de ACR-aanmeldings server ophalen met de opdracht [AZ ACR List][az-acr-list] . In het volgende voor beeld wordt het adres van de ACR-aanmeldings server opgehaald voor een REGI ster in de resource groep met de naam *myResourceGroup*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Gebruik de opdracht [docker tag][docker-tag] om de installatie kopie te labelen met de naam van de ACR-aanmeldings server en een versie nummer van `v1`. Geef uw eigen `<acrLoginServer>` naam op die u in de vorige stap hebt verkregen:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Push vervolgens de installatie kopie van *Azure-stem voor front* naar uw ACR-REGI ster. Vervang opnieuw `<acrLoginServer>` door de naam van de aanmeldings server van uw eigen ACR-REGI ster, zoals `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>De voorbeeld toepassing implementeren in AKS

Als u de voorbeeld toepassing wilt implementeren in uw AKS-cluster, kunt u het Kubernetes-manifest bestand gebruiken in de hoofdmap van de Azure stemmings opslagplaats opslag plaats. Open het manifest bestand *Azure-stemmen-all-in-One-redis. yaml* met een editor zoals `vi`. Vervang `microsoft` door de naam van de ACR-aanmeldingsserver. Deze waarde is te vinden op regel **47** van het manifest bestand:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Gebruik vervolgens de opdracht [kubectl Toep assen][kubectl-apply] om de toepassing te implementeren in uw AKS-cluster:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Er wordt een Kubernetes-load balancer service gemaakt om de toepassing beschikbaar te maken voor Internet. Dit proces kan enkele minuten duren. Als u de voortgang van de load balancer-implementatie wilt controleren, gebruikt u de opdracht [kubectl Get service][kubectl-get] met het argument `--watch`. Zodra het *EXTERNE IP-adres* is gewijzigd van *in behandeling* in een *IP-adres*, gebruikt u `Control + C` om het controleproces van kubectl te stoppen.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Als u de toepassing in actie wilt zien, opent u een webbrowser op het externe IP-adres van uw service. De Azure stem-toepassing wordt weer gegeven, zoals wordt weer gegeven in het volgende voor beeld:

![Azure-voorbeeld toepassing die wordt uitgevoerd in AKS](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Jenkins implementeren op een virtuele machine van Azure

Als u Jenkins snel wilt implementeren voor gebruik in dit artikel, kunt u het volgende script gebruiken voor het implementeren van een virtuele machine van Azure, het configureren van netwerk toegang en het volt ooien van een basis installatie van Jenkins. Voor verificatie tussen Jenkins en het AKS-cluster kopieert het script uw Kubernetes-configuratie bestand van uw ontwikkel systeem naar het Jenkins-systeem.

> [!WARNING]
> Dit voorbeeld script is bedoeld voor demonstratie doeleinden om snel een Jenkins-omgeving in te richten die wordt uitgevoerd op een virtuele Azure-machine. Er wordt gebruikgemaakt van de aangepaste script extensie van Azure om een virtuele machine te configureren en vervolgens de vereiste referenties weer te geven. Uw *~/.Kube/config* wordt gekopieerd naar de Jenkins-VM.

Voer de volgende opdrachten uit om het script te downloaden en uit te voeren. U moet de inhoud van een script controleren voordat het [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)wordt uitgevoerd.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Het duurt enkele minuten om de virtuele machine te maken en de vereiste onderdelen voor docker en Jenkins te implementeren. Wanneer het script is voltooid, voert het een adres uit voor de Jenkins-server en een sleutel om het dash board te ontgrendelen, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Open een webbrowser op de weer gegeven URL en voer de ontgrendelings sleutel in. Volg de aanwijzingen op het scherm om de Jenkins-configuratie te volt ooien:

- Kies **voorgestelde invoeg toepassingen installeren**
- Maak de eerste gebruiker met beheerdersrechten. Voer een gebruikers naam in, zoals *azureuser*, en geef uw eigen beveiligde wacht woord op. Als laatste typt u een volledige naam en e-mailadres.
- Selecteer **Save and Finish**
- Zodra Jenkins gereed is, selecteert u **Start using Jenkins**
    - Als uw webbrowser een lege pagina weergeeft wanneer u Jenkins gaat gebruiken, moet u de Jenkins-service opnieuw starten. Als u de service opnieuw wilt starten, voert u SSH uit naar het open bare IP-adres van uw Jenkins-exemplaar en typt u `sudo service jenkins restart`. Als de service opnieuw is gestart, kunt u de webbrowser vernieuwen.
- Meld u aan bij Jenkins met de gebruikers naam en het wacht woord die u hebt gemaakt tijdens het installatie proces.

## <a name="create-a-jenkins-environment-variable"></a>Een Jenkins-omgevings variabele maken

Een Jenkins-omgevings variabele wordt gebruikt om de naam van de ACR-aanmeldings server op te slaan. Tijdens de Jenkins-build-taak wordt naar deze variabele verwezen. Als u deze omgevings variabele wilt maken, voert u de volgende stappen uit:

- Selecteer aan de linkerkant van de Jenkins-Portal de optie **Jenkins beheren** > **systeem configureren**
- Onder **globale eigenschappen**selecteert u **omgevings variabelen**. Voeg een variabele met de naam `ACR_LOGINSERVER` en de waarde van uw ACR-aanmeldings server toe.

    ![Omgevings variabelen Jenkins](media/aks-jenkins/env-variables.png)

- Wanneer u klaar bent, klikt u onder aan de pagina Jenkins-configuratie op **Opslaan** .

## <a name="create-a-jenkins-credential-for-acr"></a>Een Jenkins-referentie maken voor ACR

Als u Jenkins wilt toestaan om bijgewerkte container installatie kopieën te bouwen en vervolgens naar ACR te pushen, moet u referenties opgeven voor ACR. Deze verificatie kan gebruikmaken van Azure Active Directory-service-principals. In de vereisten hebt u de service-principal voor uw AKS-cluster met *Lees* machtigingen voor uw ACR-REGI ster geconfigureerd. Met deze machtigingen kan het AKS-cluster installatie kopieën uit het ACR-REGI ster *halen* . Tijdens het CI/CD-proces bouwt Jenkins nieuwe container installatie kopieën op basis van toepassings updates en moet deze installatie kopieën vervolgens naar het ACR-REGI ster *pushen* . Voor schei ding van rollen en machtigingen configureert u nu een service-principal voor Jenkins met *Inzender* machtigingen voor uw ACR-REGI ster.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Een service-principal voor Jenkins maken voor het gebruik van ACR

Maak eerst een service-principal met behulp van de opdracht [AZ AD SP create-for-RBAC][az-ad-sp-create-for-rbac] :

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

Noteer de *AppID* en het *wacht woord* die worden weer gegeven in de uitvoer. Deze waarden worden in de volgende stappen gebruikt voor het configureren van de referentie resource in Jenkins.

Haal de bron-ID van uw ACR-REGI ster op met de opdracht [AZ ACR show][az-acr-show] en sla deze op als een variabele. Geef de naam van de resource groep en de ACR op:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Maak nu een roltoewijzing om de Service Principal *contributor* -rechten toe te wijzen aan het ACR-REGI ster. In het volgende voor beeld geeft u uw eigen *AppID* op die wordt weer gegeven in de uitvoer van een vorige opdracht om de service-principal te maken:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Een referentie bron maken in Jenkins voor de Service-Principal ACR

Als de roltoewijzing is gemaakt in azure, slaat u uw ACR-referenties op in een Jenkins-referentie object. Er wordt naar deze referenties verwezen tijdens de Jenkins-build-taak.

Klik aan de linkerkant van de Jenkins-Portal op **referenties** > **Jenkins** > **globale referenties (onbeperkt)**  > **referenties toevoegen**

Zorg ervoor dat de referentie soort **gebruikers naam met wacht woord** is en voer de volgende items in:

- **Gebruikers naam** : de *AppID* van de service-principal die is gemaakt voor verificatie met uw ACR-REGI ster.
- **Wacht woord** : het *wacht woord* van de service-principal die is gemaakt voor verificatie met uw ACR-REGI ster.
- **Id** : referentie-id zoals *ACR-referenties*

Wanneer dit is voltooid, ziet het formulier referenties eruit als in het volgende voor beeld:

![Een Jenkins-referentie object maken met de gegevens van de Service-Principal](media/aks-jenkins/acr-credentials.png)

Klik op **OK** en ga terug naar de Jenkins-Portal.

## <a name="create-a-jenkins-project"></a>Een Jenkins-project maken

Op de start pagina van uw Jenkins-Portal selecteert u **Nieuw item** aan de linkerkant:

1. Voer *Azure-stemmen* in als taak naam. Kies **Freestyle project**en selecteer **OK** .
1. Selecteer in de sectie **Algemeen** **github project** en voer uw gevorkte opslag plaats-URL in, zoals *https:\//github.com/\<uw-github account\>/Azure-voting-app-redis*
1. Selecteer in de sectie **broncode beheer** de optie **Git**, voer uw gevorkte opslag plaats *. git* -URL in, zoals *https:\//github.com/\<uw-github-account\>/Azure-voting-app-redis.git*

1. Selecteer onder de sectie **Build triggers** de **github Hook-trigger voor GITscm polling**
1. Selecteer onder **build-omgeving**de optie **geheime teksten of bestanden gebruiken**
1. Onder **bindingen**selecteert u > **gebruikers naam en wacht woord toevoegen (gescheiden)**
   - Voer `ACR_ID` in voor de **variabele username**en `ACR_PASSWORD` voor de **wachtwoord variabele**

     ![Jenkins-bindingen](media/aks-jenkins/bindings.png)

1. Kies voor het toevoegen van een **Build-stap** van het type **Execute shell** en gebruik de volgende tekst. Met dit script wordt een nieuwe container installatie kopie gebouwd en naar uw ACR-REGI ster gepusht.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Voeg nog een **Build-stap** van het type **Execute shell** toe en gebruik de volgende tekst. Met dit script wordt de toepassings implementatie in AKS bijgewerkt met de nieuwe container installatie kopie van ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Zodra u klaar bent, klikt u op **Opslaan**.

## <a name="test-the-jenkins-build"></a>De Jenkins-build testen

Voordat u de taak automatiseert op basis van GitHub-door Voer, moet u eerst de Jenkins-build hand matig testen. Met deze hand matige build wordt gecontroleerd of de taak juist is geconfigureerd, is het juiste Kubernetes-verificatie bestand aanwezig en is de verificatie met ACR werkt.

Selecteer **nu samen stellen**in het menu aan de linkerkant van het project.

![Jenkins-test build](media/aks-jenkins/test-build.png)

De eerste build duurt enkele minuten, omdat de lagen van de docker-afbeelding worden opgetrokken naar de Jenkins-server. Volgende builds kunnen de afbeeldings lagen in de cache gebruiken om de bouw tijden te verbeteren.

Tijdens het bouw proces wordt de GitHub-opslag plaats gekloond naar de Jenkins-build-server. Er wordt een nieuwe container installatie kopie gemaakt en gepusht naar het ACR-REGI ster. Ten slotte wordt de Azure stem-toepassing die wordt uitgevoerd op het AKS-cluster bijgewerkt voor gebruik van de nieuwe installatie kopie. Omdat er geen wijzigingen zijn aangebracht aan de toepassings code, wordt de toepassing niet gewijzigd als u de voor beeld-app in een webbrowser bekijkt.

Zodra de build-taak is voltooid, klikt u op **build #1** onder build History. Selecteer **console-uitvoer** en Bekijk de uitvoer van het bouw proces. De laatste regel moet erop wijzen dat de build is geslaagd.

## <a name="create-a-github-webhook"></a>Een GitHub-webhook maken

Als een geslaagde hand matige build is voltooid, integreert u GitHub nu in de Jenkins-build. Een webhook kan worden gebruikt om de Jenkins-build-taak uit te voeren telkens wanneer een code wordt doorgevoerd in GitHub. Als u de GitHub-webhook wilt maken, voert u de volgende stappen uit:

1. Blader naar uw gevorkte GitHub-opslag plaats in een webbrowser.
1. Selecteer **Instellingen**, selecteer daarna **Webhooks** aan de linkerkant.
1. Kies voor het toevoegen van een **webhook**. Voer voor de URL van de *nettolading*`http://<publicIp:8080>/github-webhook/`in, waarbij `<publicIp>` het IP-adres van de Jenkins-server is. Zorg ervoor dat u de volg-en achterstallige opgeeft. Wijzig de andere standaard waarden voor het inhouds type en om te activeren op *Push* gebeurtenissen.
1. Selecteer **webhook toevoegen**.

    ![Een GitHub-webhook maken voor Jenkins](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>De volledige CI/CD-pijp lijn testen

U kunt nu de volledige CI/CD-pijp lijn testen. Wanneer u een code doorvoert naar GitHub, gebeurt het volgende:

1. De GitHub-webhook bereikt Jenkins.
1. Jenkins start de build-taak en haalt de nieuwste code door van GitHub.
1. Een docker-build wordt gestart met de bijgewerkte code en de nieuwe container installatie kopie wordt gelabeld met het meest recente build-nummer.
1. Deze nieuwe container installatie kopie wordt gepusht naar Azure Container Registry.
1. Uw toepassing is geïmplementeerd in azure Kubernetes service-updates met de nieuwste container installatie kopie uit het Azure Container Registry REGI ster.

Open op uw ontwikkel computer de gekloonde toepassing met een code-editor. Open het bestand met de naam **config_file. cfg**onder de Directory */Azure-vote/Azure-vote* . Werk de stem waarden in dit bestand bij naar iets anders dan katten en honden, zoals wordt weer gegeven in het volgende voor beeld:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Sla het bestand op, voer de wijzigingen door en push deze naar uw Fork van de GitHub-opslag plaats. De GitHub-webhook activeert een nieuwe build-taak in Jenkins. Bewaak het bouw proces in het Jenkins Web-dash board. Het duurt een paar seconden om de meest recente code te halen, de bijgewerkte installatie kopie te maken en te pushen en de bijgewerkte toepassing in AKS te implementeren.

Zodra de build is voltooid, vernieuwt u uw webbrowser van de voor beeld-Azure stem-toepassing. Uw wijzigingen worden weer gegeven, zoals wordt weer gegeven in het volgende voor beeld:

![Voor beeld van Azure stem in AKS bijgewerkt door de Jenkins-build-taak](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Jenkins kunt gebruiken als onderdeel van een CI/CD-oplossing. AKS kan worden geïntegreerd met andere CI/CD-oplossingen en Automation-hulpprogram ma's, zoals het [Azure DevOps-project][azure-devops] of het [maken van een AKS-cluster met Ansible][aks-ansible].

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[docker-install]: https://docs.docker.com/install/
[kubectl-install]: https://kubernetes.io/docs/tasks/tools/install-kubectl/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: cluster-container-registry-integration.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md
