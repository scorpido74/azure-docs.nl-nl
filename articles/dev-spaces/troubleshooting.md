---
title: Problemen oplossen
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Meer informatie over het oplossen en oplossen van veelvoorkomende problemen bij het in- en weermaken van Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: c12dfd385962d8dd7de8239a0d4ecd46746499c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239768"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure Dev Spaces probleemoplossing

Deze handleiding bevat informatie over veelvoorkomende problemen die u hebben bij het gebruik van Azure Dev Spaces.

Als u een probleem hebt bij het gebruik van Azure Dev Spaces, maakt u een [probleem in de GitHub-opslagplaats Azure Dev Spaces](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Voordat u begint

Om problemen effectiever op te lossen, kan het helpen om meer gedetailleerde logboeken te maken voor controle.

Stel voor de visual `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` studio-extensie de omgevingsvariabele in op 1. Zorg ervoor dat u Visual Studio opnieuw start zodat de omgevingsvariabele van kracht wordt. Eenmaal ingeschakeld, worden gedetailleerde logboeken naar uw `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` directory geschreven.

In de CLI u meer informatie uitvoeren `--verbose` tijdens de uitvoering van de opdracht met behulp van de schakelaar. U ook bladeren `%TEMP%\Azure Dev Spaces`door meer gedetailleerde logboeken in. Op een Mac is de *TEMP-map* te vinden door vanuit een terminalvenster te rennen. `echo $TMPDIR` Op een Linux-computer, de `/tmp` *TEMP* directory is meestal . Controleer bovendien of logboekregistratie is ingeschakeld in uw [Azure CLI-configuratiebestand.](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)

Azure Dev Spaces werkt ook het beste bij het debuggen van een enkele instantie of pod. Het `azds.yaml` bestand bevat een instelling, *replicaCount*, die het aantal pods aangeeft dat Kubernetes voor uw service uitvoert. Als u het *aantal replica's* wijzigt om de toepassing te configureren om meerdere pods voor een bepaalde service uit te voeren, wordt de foutopsporing gekoppeld aan de eerste pod, wanneer deze alfabetisch wordt weergegeven. De foutopsporing hecht aan een andere pod wanneer de oorspronkelijke pod wordt gerecycled, wat mogelijk resulteert in onverwacht gedrag.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Veelvoorkomende problemen bij het inschakelen van Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Fout 'Kan Azure Dev Spaces-controller niet maken'

Mogelijk ziet u deze fout wanneer er iets misgaat met de creatie van de controller. Als het een tijdelijke fout is, verwijdert en maakt u de controller opnieuw om deze op te lossen.

U ook proberen de controller te verwijderen:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Gebruik de CLI azure dev Spaces om een controller te verwijderen. Het is niet mogelijk om een controller uit Visual Studio te verwijderen. U de Azure Dev Spaces CLI ook niet installeren in de Azure Cloud Shell, zodat u een controller niet verwijderen uit de Azure Cloud Shell.

Als u de Azure Dev Spaces CLI niet hebt geïnstalleerd, u deze eerst installeren met de volgende opdracht en vervolgens uw controller verwijderen:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Het opnieuw maken van de controller kan vanuit de CLI of Visual Studio. Zie de [Team development](quickstart-team-development.md) of Ontwikkel [met .NET Core](quickstart-netcore-visualstudio.md) quickstarts voor voorbeelden.

### <a name="controller-create-failing-because-of-controller-name-length"></a>Controller maakt falen vanwege de lengte van de naam van de controller

De naam van een Azure Dev Spaces-controller mag niet langer zijn dan 31 tekens. Als de naam van uw controller meer dan 31 tekens bedraagt wanneer u Dev Spaces inschakelt op een AKS-cluster of een controller maakt, ontvangt u een foutmelding. Bijvoorbeeld:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Als u dit probleem wilt oplossen, maakt u een controller met een alternatieve naam. Bijvoorbeeld:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Dev-spaties inschakelen wanneer windows-knooppuntgroepen worden toegevoegd aan een AKS-cluster

Momenteel is Azure Dev Spaces alleen bedoeld om op Linux-pods en -knooppunten te draaien. Wanneer u een AKS-cluster hebt met een Windows-knooppuntgroep, moet u ervoor zorgen dat Azure Dev Spaces-pods alleen zijn gepland op Linux-knooppunten. Als een Azure Dev Spaces-pod is gepland om te worden uitgevoerd op een Windows-knooppunt, wordt die pod niet gestart en wordt Dev Spaces ingeschakeld.

Als u dit probleem wilt oplossen, [voegt u een taint toe](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) aan uw AKS-cluster om ervoor te zorgen dat Linux-pods niet op een Windows-knooppunt worden uitgevoerd.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Fout "Geen onbedorven Linux-knooppunten gevonden in de status Ready op het cluster. Er moet ten minste een onbezoedeldLinux-knooppunt in Ready-status zijn om pods in 'azds' namespace te implementeren."

Azure Dev Spaces kan geen controller maken op uw AKS-cluster omdat het geen onaangetast knooppunt in een *status Klaar* kon vinden om pods op te plannen. Azure Dev Spaces vereist ten minste één Linux-knooppunt in een *Status Klaar* waarmee pods kunnen worden gescheduld zonder toleranties op te geven.

Als u dit probleem wilt oplossen, [werkt u uw taint-configuratie](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) op uw AKS-cluster bij om ervoor te zorgen dat ten minste één Linux-knooppunt het mogelijk maakt om pods te plannen zonder toleranties op te geven. Zorg er ook voor dat ten minste één Linux-knooppunt waarmee pods kunnen worden gescheduld zonder toleranties te specificeren, in de *status Klaar* is. Als het lang duurt voordat uw knooppunt de status *Klaar* heeft bereikt, u proberen uw knooppunt opnieuw te starten.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Fout "Azure Dev Spaces CLI not installed properly" bij het uitvoeren van az aks use-dev-spaces

Een update van de Azure Dev Spaces CLI heeft het installatiepad gewijzigd. Als u een versie van de Azure CLI eerder dan 2.0.63 gebruikt, ziet u deze fout mogelijk. Als u uw versie van `az --version`azure cli wilt weergeven, gebruikt u .

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Ondanks de foutmelding `az aks use-dev-spaces` bij het uitvoeren met een versie van de Azure CLI vóór 2.0.63, slaagt de installatie wel. U zonder `azds` problemen blijven gebruiken.

Als u dit probleem wilt oplossen, werkt u de installatie van de [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) bij naar 2.0.63 of hoger. Met deze update wordt het foutbericht opgelost dat u ontvangt wanneer u wordt uitgevoerd. `az aks use-dev-spaces` U ook uw huidige versie van Azure CLI en Azure Dev Spaces CLI blijven gebruiken.

### <a name="error-unable-to-reach-kube-apiserver"></a>Fout "Kan kube-apiserver niet bereiken"

Deze fout wordt mogelijk weergegeven wanneer Azure Dev Spaces geen verbinding kan maken met de API-server van uw AKS-cluster. 

Als de toegang tot uw AKS-clusterAPI-server is vergrendeld of als [api-servergeautoriseerde IP-adresbereiken](../aks/api-server-authorized-ip-ranges.md) zijn ingeschakeld voor uw AKS-cluster, moet u ook uw cluster [maken](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) of [bijwerken](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) om extra bereiken op basis van uw regio toe te [staan.](https://github.com/Azure/dev-spaces/tree/master/public-ips)

Controleer of de API-server beschikbaar is door kubectl-opdrachten uit te voeren. Als de API-server niet beschikbaar is, neemt u contact op met de AKS-ondersteuning en probeert u het opnieuw wanneer de API-server werkt.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Veelvoorkomende problemen bij de voorbereiding van uw project voor Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Waarschuwing "Dockerfile kan niet worden gegenereerd vanwege niet-ondersteunde taal"
Azure Dev Spaces biedt native ondersteuning voor C# en Node.js. Wanneer u `azds prep` in een map wordt uitgevoerd met code die in een van deze talen is geschreven, maakt Azure Dev Spaces automatisch een geschikt Dockerfile voor u.

U Azure Dev Spaces nog steeds gebruiken met code die in andere talen `azds up` is geschreven, maar u moet het Dockerbestand handmatig maken voordat u voor de eerste keer wordt uitgevoerd.

Als uw toepassing is geschreven in een taal die Azure Dev Spaces niet native ondersteunt, moet u een geschikt Dockerfile bieden om een containerafbeelding met uw code te bouwen. Docker biedt een [lijst met aanbevolen procedures voor het schrijven van Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) en een [Dockerfile-verwijzing](https://docs.docker.com/engine/reference/builder/) waarmee u een Dockerfile schrijven dat aan uw behoeften voldoet.

Zodra u een geschikt Dockerbestand hebt `azds up` geïnstalleerd, voert u uw toepassing uit in Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Veelvoorkomende problemen bij het starten of stoppen van services met Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Fout "Config-bestand niet gevonden:"

Wanneer `azds up`u wordt uitgevoerd, ziet u deze fout mogelijk. Beide `azds up` `azds prep` en moeten worden uitgevoerd vanuit de hoofdmap van het project dat u wilt uitvoeren in uw dev-ruimte.

Dit probleem oplossen:
1. Wijzig uw huidige map in de hoofdmap met uw servicecode. 
1. Als u geen _azds.yaml-bestand_ in de codemap hebt, voert u uit `azds prep` om Docker-, Kubernetes- en Azure Dev Spaces-elementen te genereren.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Time-out bij "Wachten op containerimage build..." stap met virtuele AKS-knooppunten

Deze time-out treedt op wanneer u Dev Spaces probeert te gebruiken om een service uit te voeren die is geconfigureerd om op een [virtueel AKS-knooppunt](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)uit te voeren. Dev Spaces ondersteunt momenteel geen gebouw- of foutopsporingsservices op virtuele knooppunten.

Als u `azds up` met `--verbose` de switch wordt uitgevoerd of de logboekregistratie in Visual Studio inschakelt, ziet u extra details:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

De bovenstaande opdracht laat zien dat de pod van de service is toegewezen aan *virtual-node-aci-linux*, dat is een virtueel knooppunt.

Als u dit probleem wilt oplossen, werkt u de helmgrafiek voor de service bij om de waarden *van de nodeSelector* of *toleranties* te verwijderen waarmee de service op een virtueel knooppunt kan worden uitgevoerd. Deze waarden worden doorgaans gedefinieerd in `values.yaml` het bestand van de grafiek.

U nog steeds een AKS-cluster gebruiken waarop de functie virtuele knooppunten is ingeschakeld, als de service die u wilt bouwen of debuggen via Dev Spaces, wordt uitgevoerd op een VM-knooppunt. Het uitvoeren van een service met Dev Spaces op een VM-knooppunt is de standaardconfiguratie.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Fout "kon geen kant-en-klare helmstokpod vinden" bij de lancering van Dev Spaces

Deze fout treedt op als de client Helm niet meer kan praten met de pod Tiller die in het cluster wordt uitgevoerd.

Als u dit probleem wilt oplossen, start u de agentknooppunten in uw cluster opnieuw.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Fout "release azds-\<identifier\>-\<spacename\>-\<servicename\> failed: services '\<servicename\> \<'\>already exists" of "Pull access denied for servicename , repository does not exist or may require 'docker login'"

Deze `helm install`fouten kunnen optreden als u lopende helmopdrachten `helm upgrade`(zoals , of `helm delete`) mengt `azds up` met `azds down`opdrachten Voor Dev Spaces (zoals en ) in dezelfde dev-ruimte. Ze komen voor omdat Dev Spaces een eigen Tiller-instantie heeft, die in strijd is met uw eigen Tiller-instantie die in dezelfde dev-ruimte wordt uitgevoerd.

Het is prima om zowel helmopdrachten als Dev Spaces-opdrachten te gebruiken tegen hetzelfde AKS-cluster, maar elke naamruimte met Dev Spaces moet de ene of de andere gebruiken.

Stel dat u een opdracht Helm gebruikt om uw hele toepassing in een bovenliggende dev-ruimte uit te voeren. U onderliggende dev-ruimten maken voor die ouder, Dev Spaces gebruiken om afzonderlijke services in de onderliggende dev-ruimten uit te voeren en de services samen testen. Wanneer u klaar bent om de wijzigingen in te checken, gebruikt u een opdracht Helm om de bijgewerkte code te implementeren in de bovenliggende dev-ruimte. Gebruik de `azds up` bijgewerkte service niet om de bijgewerkte service in de bovenliggende dev-ruimte uit te voeren, omdat deze in strijd is met de service die in eerste instantie wordt uitgevoerd met Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Bestaand Dockerbestand dat niet wordt gebruikt om een container te bouwen

Azure Dev Spaces kan worden geconfigureerd om te wijzen op een specifiek _Dockerfile_ in uw project. Als het lijkt alsof Azure Dev Spaces het _Dockerfile_ niet gebruikt waarvan u verwacht dat u uw containers bouwt, moet u Azure Dev Spaces expliciet vertellen welk Dockerfile u moet gebruiken. 

Als u dit probleem wilt oplossen, opent u het _azds.yaml-bestand_ dat Azure Dev Spaces in uw project heeft gegenereerd. *Updateconfiguraties: ontwikkel: build: dockerfile* om naar het Dockerfile te wijzen dat u wilt gebruiken. Bijvoorbeeld:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Fout "ongeautoriseerd: verificatie vereist" bij het gebruik van een Docker-afbeelding uit een privéregister

U gebruikt een Docker-afbeelding uit een privéregister waarvoor verificatie vereist is.

Als u dit probleem wilt oplossen, u Dev Spaces toestaan om afbeeldingen uit dit privéregister te verifiëren en te trekken met behulp van [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Als u imagePullSecrets wilt gebruiken, [maakt u een Kubernetes-geheim](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) in de naamruimte waar u de afbeelding gebruikt. Geef dan het geheim als `azds.yaml`een afbeeldingPullSecret in .

Hieronder vindt u een voorbeeld van `azds.yaml`een opgevende afbeeldingPullSecrets in .

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
> Afbeelding instellenPullSecrets `azds.yaml` in zal imagePullSecrets `values.yaml`overschrijven die is opgegeven in de .

### <a name="error-service-cannot-be-started"></a>Fout 'Service kan niet worden gestart'.

Mogelijk ziet u deze fout wanneer uw servicecode niet wordt gestart. De oorzaak zit vaak in de gebruikerscode. Als u meer diagnostische informatie wilt krijgen, schakelt u meer gedetailleerde logboekregistratie in bij het starten van uw service.

Gebruik de opdrachtregel `--verbose` om meer gedetailleerde logboekregistratie in te schakelen via de opdrachtregel. U ook een `--output`uitvoerindeling opgeven met behulp van. Bijvoorbeeld:

```cmd
azds up --verbose --output json
```

In Visual Studio:

1. Open **tools > opties** en kies onder Projecten en **oplossingen**bouwen **en uitvoeren**.
2. Wijzig de instellingen voor **MSBuild-projectopbouwuitvoerinformatie** in **Gedetailleerd** of **Diagnostisch**.

    ![Schermafbeelding van het dialoogvenster Extra-opties](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Een service opnieuw uitvoeren na het opnieuw maken van de controller

U ontvangt een fout van een *service wanneer* u een service opnieuw probeert uit te voeren nadat u de Azure Dev Spaces-controller die aan dit cluster is gekoppeld, hebt verwijderd en vervolgens opnieuw hebt gemaakt. In deze situatie bevat de verbose-uitvoer de volgende tekst:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Deze fout treedt op omdat het verwijderen van de dev Spaces-controller services die eerder door die controller zijn geïnstalleerd, niet verwijdert. Het opnieuw maken van de controller en vervolgens proberen om de services uit te voeren met behulp van de nieuwe controller mislukt omdat de oude services nog steeds op hun plaats zijn.

Als u dit probleem `kubectl delete` wilt oplossen, gebruikt u de opdracht om de oude services handmatig uit uw cluster te verwijderen en vervolgens Dev Spaces opnieuw uit te voeren om de nieuwe services te installeren.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Fout 'Service kan niet worden gestart'. bij gebruik van dockerbestanden met meerdere fasen

U ontvangt een *fout van een service kan niet worden gestart* wanneer u een dockerbestand met meerdere fasen gebruikt. In deze situatie bevat de verbose-uitvoer de volgende tekst:

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

Deze fout treedt op omdat Azure Dev Spaces momenteel geen multi-stage builds ondersteunt. Als u builds met meerdere fasen wilt voorkomen, herschrijft u uw Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Netwerkverkeer wordt niet doorgestuurd naar uw AKS-cluster wanneer u uw ontwikkelingsmachine aansluit

Wanneer u Azure Dev Spaces gebruikt [om uw AKS-cluster aan te sluiten op uw ontwikkelingsmachine,](how-to/connect.md)u een probleem tegenkomen waarbij netwerkverkeer niet wordt doorgestuurd tussen uw ontwikkelmachine en uw AKS-cluster.

Wanneer u uw ontwikkelmachine aansluit op uw AKS-cluster, stuurt Azure Dev Spaces netwerkverkeer door tussen `hosts` uw AKS-cluster en uw ontwikkelmachine door het bestand van uw ontwikkelmachine aan te passen. Azure Dev Spaces maakt `hosts` een vermelding in het adres van de Kubernetes-service die u vervangt als hostnaam. Deze vermelding wordt gebruikt met port forwarding om netwerkverkeer tussen uw ontwikkelmachine en het AKS-cluster te leiden. Als een service op uw ontwikkelmachine in strijd is met de poort van de Kubernetes-service die u vervangt, kan Azure Dev Spaces netwerkverkeer niet doorsturen voor de Kubernetes-service. De Windows *BranchCache-service* is bijvoorbeeld meestal gebonden aan *0.0.0.0:80,* wat een conflict voor poort 80 op alle lokale IP's veroorzaakt.

Om dit probleem op te lossen, moet u alle services of processen stoppen die in strijd zijn met de poort van de Kubernetes-service die u probeert te vervangen. U tools, zoals *netstat,* gebruiken om te controleren welke services of processen op uw ontwikkelingsmachine in conflict zijn.

De *Windows BranchCache-service* bijvoorbeeld stoppen en uitschakelen:
* Uitvoeren `services.msc` vanaf de opdrachtprompt.
* Klik met de rechtermuisknop op *BranchCache* en selecteer *Eigenschappen*.
* Klik *op Stoppen*.
* Optioneel u het uitschakelen door *opstarttype* in te stellen *op Uitgeschakeld*.
* Klik op *OK*.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Veelvoorkomende problemen met Visual Studio en Visual Studio Code met Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Fout "Vereiste hulpprogramma's en configuraties ontbreken"

Deze fout kan optreden bij het starten van VS-code: '[Azure Dev Spaces] Vereiste hulpprogramma's en configuraties om '[projectnaam]' te bouwen en te debuggen ontbreken."
De fout betekent dat azds.exe zich niet in de PATH-omgevingsvariabele bevindt, zoals te zien is in VS Code.

Probeer VS-code te starten vanuit een opdrachtprompt waarbij de variabele PATH-omgevings correct is ingesteld.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Fout 'Vereiste hulpprogramma's om 'projectnaam' te bouwen en debuggen zijn verouderd.

Deze fout wordt weergegeven in Visual Studio Code als u een nieuwere versie van de VS-code-extensie voor Azure Dev Spaces hebt, maar een oudere versie van de AZURE Dev Spaces CLI.

Probeer de nieuwste versie van de Azure Dev Spaces CLI te downloaden en te installeren:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Fout: "Kan foutopsporingsextensie niet vinden voor type:coreclr"

Deze fout wordt mogelijk weergegeven wanneer u de foutopsporing van de Visual Studio Code uitvoert. U hebt mogelijk niet de VS Code-extensie voor C# geïnstalleerd op uw ontwikkelingsmachine. De C#-extensie bevat debugging-ondersteuning voor .NET Core (CoreCLR).

Als u dit probleem wilt oplossen, installeert u de [VS-code-extensie voor C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Fout "Geconfigureerd foutopsporingstype 'coreclr' wordt niet ondersteund"

Deze fout wordt mogelijk weergegeven wanneer u de foutopsporing van de Visual Studio Code uitvoert. Het is mogelijk dat de VS-code-extensie voor Azure Dev Spaces niet op uw ontwikkelmachine is geïnstalleerd.

Als u dit probleem wilt oplossen, installeert u de [VS-code-extensie voor Azure Dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Fout "Ongeldige 'cwd'-waarde '/src'. Het systeem kan het opgegeven bestand niet vinden." of "launch: program '/src/[path to project binary]' bestaat niet"

Deze fout wordt mogelijk weergegeven wanneer u de foutopsporing van de Visual Studio Code uitvoert. Standaard wordt de VS-code-extensie gebruikt `src` als werkmap voor het project op de container. Als u uw `Dockerfile` aangepaste map hebt bijgewerkt om een andere werkmap op te geven, ziet u deze fout mogelijk.

Als u dit probleem `launch.json` wilt `.vscode` oplossen, werkt u het bestand bij onder de submap van uw projectmap. Wijzig `configurations->cwd` de richtlijn om te wijzen `WORKDIR` op dezelfde map `Dockerfile`als die in uw project is gedefinieerd. Het kan ook nodig `configurations->program` zijn om de richtlijn bij te werken.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fout "Het pijpprogramma 'azds' ging onverwacht uit met code 126."

Deze fout wordt mogelijk weergegeven wanneer u de foutopsporing van de Visual Studio Code uitvoert.

Als u dit probleem wilt oplossen, sluit u Visual Studio Code en heropent u deze opnieuw. Start de foutopsporing opnieuw.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Fout "Intern horloge is mislukt: kijk ENOSPC" bij het koppelen van foutopsporing aan een Node.js-toepassing

Deze fout treedt op wanneer het knooppunt waarop de pod wordt uitgevoerd met de node.js-toepassing waaraan u probeert te koppelen met een foutopsporing de waarde *fs.inotify.max_user_watches* heeft overschreden. In sommige gevallen [is de standaardwaarde van *fs.inotify.max_user_watches* mogelijk te klein om een foutopsporing rechtstreeks aan een pod te koppelen.](https://github.com/Azure/AKS/issues/772)

Een tijdelijke tijdelijke oplossing voor dit probleem is het verhogen van de waarde van *fs.inotify.max_user_watches* op elk knooppunt in het cluster en het opnieuw starten van dat knooppunt voor de wijzigingen van kracht te worden.

## <a name="other-common-issues"></a>Andere veelvoorkomende problemen

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Fout "azds" wordt niet herkend als een interne of externe opdracht, bedienbaar programma of batchbestand

Deze fout kan `azds.exe` optreden als deze niet correct is geïnstalleerd of geconfigureerd.

Dit probleem oplossen:

1. Controleer de locatie %ProgramFiles%/Microsoft SDKs\Azure\Azure `azds.exe`Dev Spaces CLI voor . Als deze er is, voegt u die locatie toe aan de variabele PATH-omgevings.
2. Als `azds.exe` deze niet is geïnstalleerd, voert u de volgende opdracht uit:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Autorisatiefout "Microsoft.DevSpaces/register/action"

U hebt *eigenaar-* of *inzendertoegang* nodig in uw Azure-abonnement om Azure Dev Spaces te beheren. Als u Dev Spaces probeert te beheren en u geen *eigenaar* of *inzendertoegang* hebt tot het bijbehorende Azure-abonnement, ziet u mogelijk een autorisatiefout. Bijvoorbeeld:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Als u dit probleem wilt oplossen, registreert u met behulp van `Microsoft.DevSpaces` een account met *eigenaar* of *inzendertoegang* tot het Azure-abonnement de naamruimte handmatig:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nieuwe pods starten niet

De Kubernetes initializer kan de PodSpec niet toepassen voor nieuwe pods vanwege wijzigingen in de *RBAC-machtiging* in de clusterbeheerrol in het cluster. De nieuwe pod kan ook een ongeldige PodSpec hebben, bijvoorbeeld het serviceaccount dat aan de pod is gekoppeld, bestaat niet meer. Als u de pods wilt zien die in *behandeling* zijn `kubectl get pods` vanwege het probleem met de initialisator, gebruikt u de opdracht:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Dit probleem kan gevolgen hebben voor pods in *alle naamruimten* in het cluster, inclusief naamruimten waar Azure Dev Spaces niet is ingeschakeld.

Update [de CLI voor Dev Spaces naar de nieuwste versie](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) en verwijdert vervolgens de initializerconfiguration van *azds* uit de Azure Dev Spaces-controller:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Zodra u de *azds InitializerConfiguration* hebt verwijderd uit `kubectl delete` de Azure Dev Spaces-controller, gebruikt u de pods in een *status in behandeling* te verwijderen. Nadat alle in behandeling zijnde pods zijn verwijderd, moet u de pods opnieuw implementeren.

Als nieuwe pods na een herschikking nog steeds `kubectl delete` vastzitten in een status in *behandeling* na een herschikking, gebruikt u de pods in de status In *behandeling* te verwijderen. Nadat alle pods in behandeling zijn verwijderd, verwijdert u de controller uit het cluster en installeert u deze opnieuw:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Nadat de controller opnieuw is geïnstalleerd, wordt de pods opnieuw geïmplementeerd.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Onjuiste RBAC-machtigingen voor het aanroepen van de dev Spaces-controller en API's

De gebruiker die toegang heeft tot de Azure Dev Spaces-controller moet toegang hebben tot de beheerder *kubeconfig* op het AKS-cluster. Deze machtiging is bijvoorbeeld beschikbaar in de [ingebouwde Azure Kubernetes Service Cluster Admin-rol.](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions) De gebruiker die toegang heeft tot de Azure Dev Spaces-controller moet ook de *RBAC-rol inzender* of *eigenaar* voor de controller hebben. Meer informatie over het bijwerken van de machtigingen van een gebruiker voor een AKS-cluster vindt [u hier.](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)

Ga als u de RBAC-rol van de gebruiker voor de controller bij:

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Navigeer naar de resourcegroep met de controller, die meestal hetzelfde is als uw AKS-cluster.
1. Schakel het selectievakje *Verborgen typen weergeven* in.
1. Klik op de controller.
1. Open het deelvenster *Toegangsbeheer (IAM).*
1. Klik op het tabblad *Roltoewijzingen.*
1. Klik *op Toevoegen* en voeg *roltoewijzing toe*.
    * Selecteer *voor Rol* *bijdrager* of *eigenaar*.
    * Selecteer Azure AD-gebruiker, -groep of serviceprincipal *voor Toegang toewijzen*tot , selecteer *Azure AD-gebruiker, -groep of serviceprincipal*.
    * Zoek *voor Selecteren*naar de gebruiker die u machtigingen wilt geven.
1. Klik op *Opslaan*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>DNS-naamomzetting mislukt voor een openbare URL die is gekoppeld aan een Dev Spaces-service

U een openbaar URL-eindpunt voor uw `--enable-ingress` service `azds prep` configureren door de `Publicly Accessible` overstap naar de opdracht op te geven of door het selectievakje in Visual Studio in te schakelen. De openbare DNS-naam wordt automatisch geregistreerd wanneer u uw service uitvoert in Dev Spaces. Als deze DNS-naam niet is geregistreerd, ziet u dat een *pagina niet kan worden weergegeven* of kan de site niet worden *bereikt* fout in uw webbrowser bij het maken van verbinding met de openbare URL.

Dit probleem oplossen:

* Controleer de status van alle URL's die zijn gekoppeld aan uw Services Voor Dev Spaces:

  ```console
  azds list-uris
  ```

* Als een URL in de *status In behandeling* is, wacht Dev Spaces nog steeds op de voltooiing van de DNS-registratie. Soms duurt het een paar minuten voordat de registratie is voltooid. Dev Spaces opent ook een localhosttunnel voor elke service, die u gebruiken tijdens het wachten op DNS-registratie.
* Als een URL langer dan 5 minuten in de *status In behandeling* blijft, kan dit duiden op een probleem met de externe DNS-pod waarmee het openbare eindpunt of de nginx-ingress-controllerpod wordt gemaakt die het openbare eindpunt verwerft. Gebruik de volgende opdrachten om deze pods te verwijderen en AKS toe te staan deze automatisch opnieuw te maken:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fout "upstream connect error or disconnect/reset before headers" Fout "upstream connect error or disconnect/reset before headers"

Mogelijk ziet u deze fout wanneer u toegang probeert te krijgen tot uw service. Bijvoorbeeld wanneer u naar de URL van de service in een browser gaat. Deze fout betekent dat de containerpoort niet beschikbaar is. Dit kan om de volgende redenen:

* De container is nog in het proces van wordt gebouwd en ingezet. Dit probleem kan zich `azds up` voordoen als u de foutopsporing uitvoert of start en vervolgens probeert toegang te krijgen tot de container voordat deze is geïmplementeerd.
* Poortconfiguratie is niet consistent in uw _Dockerfile,_ Helm Chart en elke servercode die een poort opent.

Dit probleem oplossen:

1. Als de container wordt gebouwd/geïmplementeerd, u 2-3 seconden wachten en opnieuw toegang krijgen tot de service. 
1. Controleer uw poortconfiguratie in de volgende elementen:
    * **[Helm diagram](https://docs.helm.sh):** Opgegeven door de en `service.port` `deployment.containerPort` in values.yaml schavot door `azds prep` opdracht.
    * Poorten die worden geopend in toepassingscode, bijvoorbeeld in Node.js:`var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Het type of de naamruimtenaam 'Mijnbibliotheek' kan niet worden gevonden

Een bibliotheekproject dat u gebruikt, kan niet worden gevonden. Bij Dev Spaces bevindt de buildcontext zich standaard op project-/serviceniveau.  

Dit probleem oplossen:

1. Wijzig `azds.yaml` het bestand om de buildcontext in te stellen op het oplossingsniveau.
2. Wijzig `Dockerfile` de `Dockerfile.develop` bestanden en bestanden om te `.csproj`verwijzen naar de projectbestanden, bijvoorbeeld correct ten opzichte van de nieuwe build context.
3. Voeg `.dockerignore` een in dezelfde `.sln` map als het bestand toe.
4. Werk `.dockerignore` de met extra vermeldingen als dat nodig is.

U hier een voorbeeld [vinden.](https://github.com/sgreenmsft/buildcontextsample)

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Horizontaal pod automatisch schalen werkt niet in een dev-ruimte

Wanneer u een service uitvoert in een dev-ruimte, wordt de pod van die service [geïnjecteerd met extra containers voor instrumentatie](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) en moeten alle containers in een pod resourcelimieten en aanvragen hebben die zijn ingesteld voor Horizontaal Pod Autoscaling.

Als u dit probleem wilt oplossen, past u een resourceaanvraag toe en beperkt u deze op de ingespoten Dev Spaces-containers. Resourceaanvragen en -limieten kunnen worden toegepast voor de geïnjecteerde `azds.io/proxy-resources` container (devspaces-proxy) door de annotatie toe te voegen aan de specificaties van uw pod. De waarde moet worden ingesteld op een JSON-object dat de sectie resources van de containerspec voor de proxy vertegenwoordigt.

Hieronder vindt u een voorbeeld van een proxy-resources-annotatie die moet worden toegepast op uw pod-specificaties.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Azure Dev-spaties inschakelen op een bestaande naamruimte met lopende pods

Mogelijk hebt u een bestaand AKS-cluster en naamruimte met hardlooppods waar u Azure Dev Spaces wilt inschakelen.

Als u Azure Dev Spaces in een bestaande naamruimte in een AKS-cluster wilt inschakelen, voert u uit `use-dev-spaces` en gebruikt u `kubectl` alle pods in die naamruimte opnieuw.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Nadat uw pods opnieuw zijn gestart, u uw bestaande naamruimte gebruiken met Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Azure Dev Spaces inschakelen op AKS-cluster met beperkt uitgangsverkeer voor clusterknooppunten

Als u Azure Dev Spaces wilt inschakelen op een AKS-cluster waarvoor het uitgangsverkeer van clusterknooppunten is beperkt, moet u volgende FQDN's toestaan:

| FQDN                                    | Poort      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Linux alpine- en andere Azure Dev Spaces-afbeeldingen trekken |
| gcr.io | HTTP:443 | Om helm/helm/helmstokbeelden te trekken|
| storage.googleapis.com | HTTP:443 | Om helm/helm/helmstokbeelden te trekken|
| azds-<guid>. <location>.azds.io | HTTPS:443 | Om te communiceren met Azure Dev Spaces backend services voor uw controller. De exacte FQDN is te vinden in het "dataplaneFqdn" in %USERPROFILE%\.azds\settings.json|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Fout "Kan het \<clustercluster\> \<niet\>vinden in abonnementabonnementId"

Mogelijk ziet u deze fout als uw kubeconfig-bestand zich richt op een ander cluster of abonnement dan u probeert te gebruiken met de azure Dev Spaces-clienttooling. De azure Dev Spaces client side tooling repliceert het gedrag van *kubectl*, die [een of meer kubeconfig-bestanden](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) gebruikt om het cluster te selecteren en met het cluster te communiceren.

Dit probleem oplossen:

* De `az aks use-dev-spaces -g <resource group name> -n <cluster name>` huidige context bijwerken. Met deze opdracht u ook Azure Dev Spaces op uw AKS-cluster plaatsen inschakelen als deze nog niet is ingeschakeld. U ook `kubectl config use-context <cluster name>` de huidige context bijwerken.
* Hiermee `az account show` u het huidige Azure-abonnement weergeven dat u target en controleren of dit juist is. U het abonnement wijzigen `az account set`dat u target via.

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Fout met Dev Spaces na roterende AKS-certificaten

Na [het roteren van de certificaten in uw AKS-cluster,](../aks/certificate-rotation.md)bepaalde bewerkingen, zoals `azds space list` en `azds up` zal mislukken. U moet de certificaten ook vernieuwen op uw Azure Dev Spaces-controller nadat u de certificaten op uw cluster hebt geroteerd.

Als u dit probleem wilt oplossen, moet u `az aks get-credentials` ervoor `azds controller refresh-credentials` zorgen dat uw *kubeconfig* de bijgewerkte certificaten heeft met de opdracht en vervolgens de opdracht uitvoert. Bijvoorbeeld:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
