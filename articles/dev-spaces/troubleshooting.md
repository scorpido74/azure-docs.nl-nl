---
title: Problemen oplossen
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Snelle Kubernetes-ontwikkeling met containers en microservices in Azure
keywords: 'Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S '
ms.openlocfilehash: 87aa96614b6aec4843723233a77d0a1dc1b66453
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300350"
---
# <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

Deze handleiding bevat informatie over veelvoorkomende problemen die mogelijk hebt u bij het gebruik van Azure Dev spaties.

Als u een probleem ondervindt bij het gebruik van Azure dev Spaces, maakt u een [probleem in de Azure dev Spaces github-opslag plaats](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Voordat u begint

Om problemen effectiever op te lossen, kan het helpen om meer gedetailleerde logboeken te maken voor beoordeling.

Voor de extensie voor Visual Studio, stelt u de `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` omgevingsvariabele op 1. Zorg ervoor dat opnieuw opstarten van Visual Studio voor de omgeving in te voeren. Wanneer deze functie is ingeschakeld, worden gedetailleerde logboeken naar uw `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` Directory geschreven.

In de CLI, kunt u meer informatie tijdens de uitvoering van de opdracht uitvoeren met behulp van de `--verbose` overschakelen. U kunt ook meer gedetailleerde logboeken in Bladeren `%TEMP%\Azure Dev Spaces`. De map TEMP op een Mac te vinden door te voeren `echo $TMPDIR` vanuit een terminal-venster. Op een Linux-computer, de map TEMP is meestal `/tmp`.

Azure dev Spaces werkt ook het beste bij het opsporen van fouten in één exemplaar of pod. Het `azds.yaml` bestand bevat een instelling, *replicaCount*, die het aantal peulen aangeeft dat Kubernetes voor uw service wordt uitgevoerd. Als u de *replicaCount* wijzigt om uw toepassing te configureren voor het uitvoeren van meerdere peulen voor een bepaalde service, wordt het fout opsporingsprogramma gekoppeld aan de eerste Pod, wanneer deze alfabetisch wordt weer gegeven. Het fout opsporingsprogramma koppelt aan een andere pod wanneer de oorspronkelijke pod wordt gerecycled, mogelijk als gevolg van onverwacht gedrag.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Veelvoorkomende problemen bij het inschakelen van Azure dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Fout: kan de Azure dev Space-controller niet maken

U kunt deze fout tegenkomen wanneer er iets met het maken van de controller misgaat. Als het een tijdelijke fout is, verwijdert u de controller en maakt u deze opnieuw om het probleem op te lossen.

U kunt ook proberen de controller te verwijderen:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Gebruik de Azure dev Spaces CLI om een controller te verwijderen. Het is niet mogelijk om een controller uit Visual Studio te verwijderen. Het is ook niet mogelijk om de Azure dev Spaces CLI te installeren in de Azure Cloud Shell zodat u een controller niet uit de Azure Cloud Shell kunt verwijderen.

Als u de CLI van Azure dev Spaces niet hebt geïnstalleerd, kunt u deze eerst installeren met de volgende opdracht en vervolgens de controller verwijderen:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

De controller opnieuw kan worden gedaan in de CLI of Visual Studio. Bekijk de [team ontwikkeling](quickstart-team-development.md) of [ontwikkel met .net core](quickstart-netcore-visualstudio.md) Quick starts voor voor beelden.

### <a name="controller-create-failing-because-of-controller-name-length"></a>De controller kan niet worden gemaakt vanwege een lengte van de naam van de controller

De naam van een controller van Azure dev Space mag niet langer zijn dan 31 tekens. Als de naam van de controller langer is dan 31 tekens wanneer u dev Spaces inschakelt in een AKS-cluster of een controller maakt, ontvangt u een fout melding. Bijvoorbeeld:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

U kunt dit probleem oplossen door een controller met een andere naam te maken. Bijvoorbeeld:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Fout bij het inschakelen van ontwikkel ruimten wanneer Windows-knooppunt groepen worden toegevoegd aan een AKS-cluster

Momenteel is Azure dev Spaces alleen bedoeld om te worden uitgevoerd op Linux en alleen knoop punten. Wanneer u een AKS-cluster met een Windows-knooppunt groep hebt, moet u ervoor zorgen dat Azure dev Spaces alleen worden gepland voor Linux-knoop punten. Als er een Azure dev Space Pod is gepland om te worden uitgevoerd op een Windows-knoop punt, zal die pod niet starten en kunnen er geen dev-ruimten worden ingeschakeld.

U kunt dit probleem oplossen door [een Taint toe te voegen](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) aan uw AKS-cluster om ervoor te zorgen dat Linux peul niet is gepland om te worden uitgevoerd op een Windows-knoop punt.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Fout "er zijn geen untainted Linux-knoop punten gevonden in de status gereed op het cluster. Er moet ten minste één untainted Linux-knoop punt in de status gereed zijn om een Peul te implementeren in de azds-naam ruimte.

Azure dev Spaces kan geen controller maken op uw AKS-cluster omdat er geen untainted-knoop punt met de status *gereed* kan worden gevonden voor het plannen van een Peul op. Voor Azure-ontwikkel ruimten is ten minste één Linux-knoop punt met de status *gereed* die het plannen van peulen toestaat zonder dat er toleranties worden opgegeven.

U kunt dit probleem oplossen door [de Taint-configuratie](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) op uw AKS-cluster bij te werken om ervoor te zorgen dat er ten minste één Linux-knoop punt kan worden gepland, zonder dat u de toleranties opgeeft. Zorg er ook voor dat er ten minste één Linux-knoop punt is *dat het plannen* van Peul toestaat zonder dat u de toleranties kunt opgeven. Als het knoop punt lange tijd neemt om de status *gereed* te bereiken, kunt u het knoop punt opnieuw starten.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Fout ' Azure dev Spaces is niet correct geïnstalleerd ' bij het uitvoeren`az aks use-dev-spaces`

Het installatiepad van een update voor de Azure dev Space CLI is gewijzigd. Als u een eerdere versie van Azure CLI dan 2.0.63 gebruikt, wordt deze fout weer geven. Als u uw versie van de Azure CLI wilt weer `az --version`geven, gebruikt u.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Ondanks het fout bericht bij het `az aks use-dev-spaces` uitvoeren van een versie van de Azure cli vóór 2.0.63, wordt de installatie voltooid. U kunt zonder problemen blijven `azds` gebruiken.

U kunt dit probleem oplossen door uw installatie van [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) bij te werken naar 2.0.63 of hoger. Met deze update wordt het fout bericht opgelost dat wordt weer `az aks use-dev-spaces`gegeven wanneer het wordt uitgevoerd. U kunt ook uw huidige versie van de Azure CLI en de Azure dev Spaces CLI blijven gebruiken.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Veelvoorkomende problemen bij het voorbereiden van het project voor Azure-ontwikkel ruimten

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Waarschuwing "Dockerfile kan niet worden gegenereerd als gevolg van niet-ondersteunde taal"
Azure Dev opslagruimten biedt systeemeigen ondersteuning voor C# en Node.js. Wanneer u uitvoert `azds prep` in een map met code die is geschreven in een van deze talen, worden door Azure dev Spaces automatisch een geschikte Dockerfile voor u gemaakt.

U kunt nog steeds Azure dev Spaces gebruiken met code geschreven in andere talen, maar u moet de Dockerfile hand matig maken `azds up` voordat u voor de eerste keer wordt uitgevoerd.

Als uw toepassing is geschreven in een taal die door Azure dev Spaces niet systeem eigen ondersteuning biedt, moet u een geschikte Dockerfile opgeven voor het bouwen van een container installatie kopie die uw code uitvoert. Docker bevat een [lijst met aanbevolen procedures voor het schrijven van Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) en een [Dockerfile-verwijzing](https://docs.docker.com/engine/reference/builder/) die u kan helpen bij het schrijven van een Dockerfile die aansluit bij uw behoeften.

Zodra er een geschikte Dockerfile aanwezig is, voert u uit `azds up` om uw toepassing uit te voeren in azure dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Veelvoorkomende problemen bij het starten of stoppen van services met Azure dev Spaces

### <a name="error-config-file-not-found"></a>Fout ' configuratie bestand niet gevonden: '

Wanneer u `azds up`deze uitvoert, wordt deze fout weer geven. Beide `azds up` en`azds prep` moeten worden uitgevoerd vanuit de hoofd directory van het project dat u wilt uitvoeren in uw ontwikkelaars ruimte.

Dit probleem oplossen:
1. Wijzig de huidige directory naar de hoofdmap met de servicecode van uw. 
1. Als u geen _azds. yaml_ -bestand in de map code hebt, voert `azds prep` u uit om assets voor docker-, Kubernetes-en Azure-ontwikkel ruimten te genereren.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Time-out bij wachten op build van container installatie kopie... stap met virtuele AKS-knoop punten

Deze time-out treedt op wanneer u probeert om dev Spaces te gebruiken om een service uit te voeren die is geconfigureerd om te worden uitgevoerd op een [virtueel AKS-knoop punt](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Ontwikkel ruimten biedt momenteel geen ondersteuning voor het bouwen of opsporen van fouten op virtuele knoop punten.

Als u de `azds up` `--verbose` switch uitvoert of uitgebreide logboek registratie inschakelt in Visual Studio, ziet u meer details:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

De bovenstaande opdracht toont aan dat de pod van de service is toegewezen aan *virtueel-node-ACI-Linux*. Dit is een virtueel knoop punt.

Om dit probleem op te lossen, werkt u de helm-grafiek voor de service bij om de *nodeSelector* *-of de* toegestane waarden te verwijderen waarmee de service op een virtueel knoop punt kan worden uitgevoerd. Deze waarden worden meestal gedefinieerd in het bestand van `values.yaml` de grafiek.

U kunt nog steeds een AKS-cluster gebruiken waarvoor de functie virtuele knoop punten is ingeschakeld, als de service die u wilt bouwen of fouten wilt opsporen via dev Spaces, worden uitgevoerd op een VM-knoop punt. Het uitvoeren van een service met ontwikkel ruimten op een VM-knoop punt is de standaard configuratie.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Fout "kan geen kant-en-klare Tiller-pod vinden" bij het starten van dev Spaces

Deze fout treedt op als de Helm-client kan niet meer communiceren met de Tiller-pod uitgevoerd in het cluster.

U kunt dit probleem oplossen door de agent knooppunten in het cluster opnieuw op te starten.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Fout ' release azds-\<id\>-\<ruimteservicenaam\>mislukt:Services\<servicenaam-\<\> \>bestaat al of pull \<\>-toegang geweigerd voor servicenaam, opslag plaats bestaat niet of vereist ' docker aanmelding '

Deze fouten kunnen zich voordoen als u direct helm-opdrachten (zoals `helm install`, `helm upgrade`of `helm delete` `azds up` ) combineert met de opdracht dev Spaces (zoals `azds down`en) binnen dezelfde ontwikkel ruimte. Dit gebeurt omdat ontwikkel ruimten een eigen Tiller-exemplaar hebben, wat een conflict veroorzaakt met uw eigen Tiller-exemplaar dat wordt uitgevoerd in dezelfde dev-ruimte.

Het is nauw keurig om zowel helm-opdrachten als dev Spaces-opdrachten te gebruiken voor hetzelfde AKS-cluster, maar elke naam ruimte die voor ontwikkel aars is ingeschakeld, moet beide gebruikmaken van een van beide.

Stel dat u een helm opdracht gebruikt om uw hele toepassing uit te voeren in een bovenliggende ontwikkel ruimte. U kunt onderliggende ontwikkel ruimten van die bovenliggende shape maken, ontwikkel ruimten gebruiken om afzonderlijke services uit te voeren binnen de onderliggende ontwikkel ruimten en de services tegelijk te testen. Wanneer u klaar bent om uw wijzigingen te controleren, gebruikt u een helm-opdracht om de bijgewerkte code te implementeren in de bovenliggende ontwikkel ruimte. Gebruik `azds up` niet voor het uitvoeren van de bijgewerkte service in de bovenliggende ontwikkel ruimte, omdat deze in eerste instantie een conflict veroorzaakt met de service die wordt uitgevoerd met behulp van helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Bestaande Dockerfile niet gebruikt voor het bouwen van een container

Azure Dev opslagruimten kunnen worden geconfigureerd om te verwijzen naar een specifieke _Dockerfile_ in uw project. Als er Azure dev Spaces worden gebruikt, wordt de _Dockerfile_ die u verwacht om uw containers te bouwen, mogelijk expliciet duidelijk te maken met Azure dev Spaces die Dockerfile moeten worden gebruikt. 

Om dit probleem op te lossen, opent u het _azds. yaml_ -bestand dat Azure-ontwikkel ruimten die in uw project zijn gegenereerd. Update *configuraties: ontwikkelen: Build: dockerfile* om te verwijzen naar de dockerfile die u wilt gebruiken. Bijvoorbeeld:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Fout "niet toegestaan: authenticatie vereist" bij het gebruik van een docker-installatie kopie uit een persoonlijk REGI ster

U gebruikt een docker-installatie kopie uit een persoonlijk REGI ster waarvoor verificatie is vereist.

Om dit probleem op te lossen, kunt u ontwikkel ruimten toestaan om installatie kopieën uit dit privé-REGI ster te verifiëren en te halen met behulp van [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Als u imagePullSecrets wilt gebruiken, [maakt u een Kubernetes-geheim](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) in de naam ruimte waarin u de installatie kopie gebruikt. Geef vervolgens het geheim op als een imagePullSecret `azds.yaml`in.

Hieronder ziet u een voor beeld van een opgegeven `azds.yaml`imagePullSecrets in.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Als u imagePullSecrets `azds.yaml` in instelt, worden imagePullSecrets die `values.yaml`zijn opgegeven in de.

### <a name="error-service-cannot-be-started"></a>Fout: de service kan niet worden gestart.

U kunt deze fout tegenkomen wanneer de servicecode van uw niet kan worden gestart. De oorzaak is vaak in de gebruikerscode. Voor meer informatie over diagnostische gegevens kunt u gedetailleerdere logboek registratie inschakelen bij het starten van de service.

Gebruik op de opdracht regel de `--verbose` om gedetailleerde logboek registratie in te scha kelen. U kunt ook een uitvoer indeling opgeven met `--output`. Bijvoorbeeld:

```cmd
azds up --verbose --output json
```

In Visual Studio:

1. Open **Extra > opties** en klikt u onder **projecten en oplossingen**, kiest u **bouwen en uitvoeren**.
2. Wijzig de instellingen voor **MSBuild-project build uitvoer uitgebreidheid** naar **gedetailleerd** of **diagnostische**.

    ![Schermafbeelding van de opties in menu Extra](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Een service opnieuw uitvoeren na het opnieuw maken van de controller

U ontvangt een fout bericht dat de *service niet kan worden gestart* wanneer u een service opnieuw probeert uit te voeren nadat u de Azure dev Space-controller die is gekoppeld aan dit cluster hebt verwijderd en opnieuw hebt gemaakt. In dit geval bevat de uitgebreide uitvoer de volgende tekst:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Deze fout treedt op omdat het verwijderen van de controller van de dev Spaces geen Services verwijdert die eerder door die controller zijn geïnstalleerd. De controller opnieuw te maken en vervolgens probeert om uit te voeren van de services met behulp van de nieuwe domeincontroller is mislukt omdat de oude services nog steeds voldaan is.

Om dit probleem op te lossen `kubectl delete` , gebruikt u de opdracht om de oude Services hand matig uit uw cluster te verwijderen en voert u vervolgens dev Spaces opnieuw uit om de nieuwe services te installeren.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Fout: de service kan niet worden gestart. bij gebruik van Dockerfiles met meerdere fasen

U ontvangt een fout melding dat een *service niet kan worden gestart* wanneer u een Dockerfile met meerdere fasen gebruikt. In dit geval bevat de uitgebreide uitvoer de volgende tekst:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Deze fout treedt op omdat AKS-knoop punten een oudere versie van docker uitvoeren die geen ondersteuning biedt voor het gebruik van meerdere fasen. Herschrijf uw Dockerfile om te voor komen dat meerdere fases worden gebouwd.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Veelvoorkomende problemen met het gebruik van Visual Studio en Visual Studio code met Azure dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Fout: de vereiste hulpprogram ma's en configuraties ontbreken

Deze fout kan optreden bij het starten van VS Code: "[Azure Dev spaties] vereiste hulpprogramma's en configuraties om te bouwen en fouten opsporen in '[naam project]' ontbreken."
De fout betekent dat azds.exe bevindt zich niet in de omgevingsvariabele PATH, zoals te zien is in VS Code.

Start VS code vanuit een opdracht prompt waarbij de omgevings variabele PATH correct is ingesteld.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Fout: de vereiste hulpprogram ma's voor het maken van de fout code ' ProjectName ' zijn verouderd. '

U ziet deze fout in Visual Studio code als u een nieuwere versie van de VS code-extensie voor Azure dev Spaces hebt, maar een oudere versie van de Azure dev Spaces-CLI.

Probeer de nieuwste versie van de Azure dev Spaces CLI te downloaden en te installeren:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Fout: Kan de uitbrei ding van het fout opsporingsprogramma voor type: coreclr niet vinden

Deze fout wordt mogelijk weer gegeven bij het uitvoeren van het fout opsporingsprogramma voor Visual Studio-code. Mogelijk beschikt u niet over de VS code- C# uitbrei ding voor geïnstalleerd op uw ontwikkel computer. De C# extensie bevat ondersteuning voor fout opsporing voor .net core (CoreCLR).

U kunt dit probleem oplossen door de [VS code-extensie C#voor ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)te installeren.

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Fout "het geconfigureerde type debug" coreclr "wordt niet ondersteund"

Deze fout wordt mogelijk weer gegeven bij het uitvoeren van het fout opsporingsprogramma voor Visual Studio-code. Mogelijk beschikt u niet over de VS code-uitbrei ding voor Azure dev Spaces die zijn geïnstalleerd op uw ontwikkel computer.

U kunt dit probleem oplossen door de [VS code-extensie voor Azure dev Spaces](get-started-netcore.md)te installeren.

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Fout ' ongeldige ' CWD-waarde '/src '. Het systeem het opgegeven bestand vinden niet." of "starten: programma/src / [pad naar een binair project] bestaat niet"

Deze fout wordt mogelijk weer gegeven bij het uitvoeren van het fout opsporingsprogramma voor Visual Studio-code. De VS Code-extensie gebruikt standaard `src` als de werkmap voor het project voor de container. Als u hebt bijgewerkt uw `Dockerfile` om op te geven van een andere werkmap, mag u deze fout ziet.

U kunt dit probleem oplossen door het `launch.json` bestand onder de `.vscode` submap van de projectmap bij te werken. Wijzig de `configurations->cwd` richtlijn om te verwijzen naar dezelfde map als de `WORKDIR` gedefinieerd in van uw project `Dockerfile`. U moet mogelijk ook om bij te werken de `configurations->program` ook richtlijn.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fout: het pipe programma azds is onverwacht afgesloten met code 126. "

Deze fout wordt mogelijk weer gegeven bij het uitvoeren van het fout opsporingsprogramma voor Visual Studio-code.

U kunt dit probleem oplossen door Visual Studio code te sluiten en opnieuw te openen. Start het fout opsporingsprogramma opnieuw.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Fout ' interne controle mislukt: Watch ENOSPC ' bij het toevoegen van fout opsporing aan een node. js-toepassing

Deze fout treedt op wanneer het knoop punt waarop de Pod wordt uitgevoerd met de node. js-toepassing die u wilt koppelen met een fout opsporingsprogramma de waarde *FS. inotify. Max _user_watches* overschrijdt. In sommige gevallen is [de standaard waarde van *FS. inotify. Max _user_watches* mogelijk te klein om een fout opsporingsprogramma rechtstreeks aan een pod te koppelen](https://github.com/Azure/AKS/issues/772).

Een tijdelijke oplossing voor dit probleem is om de waarde *FS. inotify. Max _user_watches* op elk knoop punt in het cluster te verhogen en het knoop punt opnieuw op te starten om de wijzigingen van kracht te laten worden.

## <a name="other-common-issues"></a>Andere veelvoorkomende problemen

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Fout "azds" wordt niet herkend als een intern of extern opdracht, programma of batch bestand

Deze fout kan optreden als `azds.exe` niet op de juiste wijze is geïnstalleerd of geconfigureerd.

Dit probleem oplossen:

1. Controleer de locatie% Program Files%/Microsoft SDKs\Azure\Azure dev Spaces `azds.exe`cli for. Als deze aanwezig is, moet u die locatie toevoegen aan de omgevingsvariabele PATH.
2. Als `azds.exe` niet is geïnstalleerd, voert u de volgende opdracht uit:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Autorisatie fout ' micro soft. DevSpaces/REGI ster/Action '

U hebt toegang tot de *eigenaar* of *Inzender* nodig in uw Azure-abonnement voor het beheren van Azure dev Spaces. Als u ontwikkelaars ruimten wilt beheren en u geen *eigenaar* of *Inzender* toegang hebt tot het gekoppelde Azure-abonnement, ziet u mogelijk een autorisatie fout. Bijvoorbeeld:

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Als u dit probleem wilt oplossen, moet u de `Microsoft.DevSpaces` naam ruimte hand matig registreren met behulp van een account met *eigenaar* of *Inzender* toegang tot het Azure-abonnement:

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nieuwe Peul wordt niet gestart

De initialisatie functie Kubernetes kan de PodSpec niet Toep assen op nieuwe peulen vanwege de RBAC-machtigings wijzigingen in de rol *cluster beheerder* in het cluster. De nieuwe pod kan ook een ongeldige PodSpec hebben, bijvoorbeeld het service account dat is gekoppeld aan het Pod bestaat niet meer. Gebruik de `kubectl get pods` opdracht om de peulen te zien die de status in *behandeling* hebben vanwege het probleem van de initialisatie functie:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Dit probleem kan van invloed zijn op *alle naam ruimten* in het cluster, met inbegrip van naam ruimten waar Azure dev Spaces niet is ingeschakeld.

U kunt dit probleem oplossen door [de ontwikkel ruimten cli bij te werken naar de meest recente versie](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) en vervolgens de *azds InitializerConfiguration* te verwijderen uit de Azure dev Spaces-controller:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Wanneer u de azds- *InitializerConfiguration* van de Azure dev Spaces-controller hebt `kubectl delete` verwijderd, gebruikt u voor het verwijderen van een wille keurig verschil met de status in *behandeling* . Nadat alle in behandeling zijnde peul zijn verwijderd, moet u uw peul opnieuw implementeren.

Als nieuwe peulen nog steeds zijn vastgelopen in de status *in behandeling* na een herimplementatie, gebruikt `kubectl delete` u om een wille keurig peul te verwijderen met de status in *behandeling* . Nadat alle in behandeling zijnde peul zijn verwijderd, verwijdert u de controller uit het cluster en installeert u deze opnieuw:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Nadat de controller opnieuw is geïnstalleerd, implementeert u uw peul opnieuw.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Onjuiste RBAC-machtigingen voor het aanroepen van de controller en Api's van dev Spaces

De gebruiker die toegang probeert te krijgen tot de Azure dev Spaces-controller moet toegang hebben om de beheerder *kubeconfig* te lezen op het AKS-cluster. Deze machtiging is bijvoorbeeld beschikbaar in de [ingebouwde rol Azure Kubernetes service cluster admin](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). De gebruiker die de Azure dev Spaces-controller opent, moet ook de RBAC-rol *Inzender* of *eigenaar* hebben voor de controller. Meer informatie over het bijwerken van de machtigingen van een gebruiker voor een AKS-cluster is [hier](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)beschikbaar.

De RBAC-rol van de gebruiker voor de controller bijwerken:

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Navigeer naar de resource groep die de controller bevat. Dit is meestal hetzelfde als uw AKS-cluster.
1. Schakel het selectie vakje *verborgen typen weer geven* in.
1. Klik op de controller.
1. Open het deel venster *Access Control (IAM)* .
1. Klik *op het tabblad* roltoewijzingen.
1. Klik op *toevoegen* en vervolgens op *functie toewijzing toevoegen*.
    * Selecteer voor *rol*de optie *Inzender* of *eigenaar*.
    * *Als u toegang wilt toewijzen*, selecteert u *Azure AD-gebruiker,-groep of Service-Principal*.
    * Zoek bij *selecteren*naar de gebruiker aan wie u machtigingen wilt verlenen.
1. Klik op *Opslaan*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>DNS-naamomzetting voor een openbare URL die is gekoppeld aan een Dev-Spaces-service is mislukt

U kunt een open bare URL-eind punt voor uw service configureren `--public` door de Schakel `azds prep` optie voor de opdracht op te `Publicly Accessible` geven of door het selectie vakje in Visual Studio in te scha kelen. De open bare DNS-naam wordt automatisch geregistreerd wanneer u uw service in dev Spaces uitvoert. Als deze DNS-naam niet is geregistreerd, ziet u dat er *geen pagina kan worden weer gegeven* of dat de *site niet* in uw webbrowser kan worden bereikt wanneer u verbinding maakt met de open bare URL.

Dit probleem oplossen:

* Controleer de status van alle Url's die zijn gekoppeld aan uw ontwikkelaars Spaces-Services:

  ```console
  azds list-uris
  ```

* Als een URL de status in *behandeling* heeft, wordt er nog steeds ontwikkel ruimten gewacht totdat de DNS-registratie is voltooid. Soms duurt een paar minuten voor de registratie te voltooien. Dev opslagruimten wordt ook geopend wanneer u een tunnel ' localhost ' voor elke service, die u gebruiken kunt tijdens het wachten op DNS-registratie.
* Als een URL langer dan 5 minuten in *behandeling* is, kan dit wijzen op een probleem met de externe DNS-pod die het open bare eind punt maakt of de pod van de nginx ingress die het open bare eind punt ophaalt. Gebruik de volgende opdrachten om deze velden te verwijderen en AKS toe te staan om ze automatisch opnieuw te maken:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fout bericht: upstream Connect-fout of verbinding verbreken/opnieuw instellen vóór headers

Mogelijk ziet u deze fout bij het openen van uw service. Bijvoorbeeld, wanneer u gaat naar de URL van de service in een browser. Deze fout betekent dat de container poort niet beschikbaar is. Dit kan de volgende oorzaken hebben:

* De container wordt nog steeds worden gebouwd en geïmplementeerd. Dit probleem kan zich voordoen als u uitvoeren `azds up` of het foutopsporingsprogramma start en vervolgens probeert te krijgen tot de container voordat deze is geïmplementeerd.
* Poortconfiguratie van de is niet consistent zijn tussen uw _Dockerfile_, Helm-diagram en servercode die wordt een poort geopend.

Dit probleem oplossen:

1. Als de container gebouwd wordt/geïmplementeerd, kunt u wacht 2-3 seconden en probeer het opnieuw openen van de service. 
1. Controleer de poortconfiguratie. De opgegeven poort nummers moeten **identiek** zijn in alle van de volgende assets:
    * **Dockerfile** Opgegeven door de `EXPOSE` instructie.
    * **[Helm grafiek](https://docs.helm.sh):** Opgegeven door de `externalPort` - `internalPort` en-waarden voor een service (vaak opgeslagen `values.yml` in een bestand),
    * Eventuele poorten die worden geopend in de toepassingscode, bijvoorbeeld in Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Het type of de naam van de naam ruimte ' MyLibrary ' is niet gevonden

Een bibliotheek project dat u gebruikt, is niet gevonden. Met dev Spaces is de build-context standaard op het niveau van project/service.  

Dit probleem oplossen:

1. Wijzig het `azds.yaml` bestand om de build-context in te stellen op het oplossings niveau.
2. Wijzig de `Dockerfile` en `Dockerfile.develop` de bestanden om te verwijzen naar de project bestanden, `.csproj`bijvoorbeeld correct ten opzichte van de nieuwe build-context.
3. Voeg een `.dockerignore` in dezelfde map als het `.sln` bestand toe.
4. Werk de `.dockerignore` met aanvullende vermeldingen naar behoefte bij.

[Hier](https://github.com/sgreenmsft/buildcontextsample)vindt u een voor beeld.

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Horizon taal pod automatisch schalen werkt niet in een dev-ruimte

Wanneer u een service in een dev-ruimte uitvoert, wordt de pod van die service [geïnjecteerd met extra containers voor instrumentatie](how-dev-spaces-works.md#prepare-your-aks-cluster) en moeten alle containers in een pod resource limieten en aanvragen instellen voor Horizon taal pod automatisch schalen.

Om dit probleem op te lossen, past u een resource aanvraag toe en beperkt u de containers voor de geïnjecteerde dev-ruimten. Resource aanvragen en-limieten kunnen worden toegepast voor de geïnjecteerde container (devspaces-proxy) door `azds.io/proxy-resources` de aantekening toe te voegen aan uw Pod spec. De waarde moet worden ingesteld op een JSON-object dat de sectie Resources vertegenwoordigt van de container specificatie van de proxy.

Hieronder ziet u een voor beeld van een aantekening voor proxy bronnen die moet worden toegepast op uw Pod-specificatie.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```