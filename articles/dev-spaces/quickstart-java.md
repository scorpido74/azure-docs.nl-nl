---
title: 'Fouten opsporen en herhalen op Kubernetes: Visual Studio code & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: In deze Quick start ziet u hoe u Azure dev Spaces en Visual Studio code kunt gebruiken om fouten op te sporen en snel een Java-toepassing te herhalen in azure Kubernetes service
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, Java, helm, Service-Mesh, Service-Mesh-route ring, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 8ceb48bf60438442b63fab698091fdb5064793af
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025194"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Quick Start: fouten opsporen en herhalen op Kubernetes met Visual Studio code en Java-Azure dev Spaces

In deze Quick Start stelt u Azure dev Spaces in met een beheerd Kubernetes-cluster en gebruikt u een Java-app in Visual Studio code om iteratieve code in containers te ontwikkelen en fouten in de fout opsporing uit te stellen. Met Azure dev Spaces kunt u fouten opsporen en testen van alle onderdelen van uw toepassing in azure Kubernetes service (AKS) met minimale ontwikkel machine-instellingen. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Java Development Kit (JDK) 1.8.0 +](https://aka.ms/azure-jdks).
- [Maven 3.5.0 +](https://maven.apache.org/download.cgi).
- [Visual Studio Code](https://code.visualstudio.com/download).
- De [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) en [Java debugger voor Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) -extensies voor Visual Studio code.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes service-cluster maken

U moet een AKS-cluster maken in een [ondersteunde regio][supported-regions]. Met de volgende opdrachten maakt u een resource groep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure dev Spaces inschakelen op uw AKS-cluster

Gebruik de `use-dev-spaces` opdracht om ontwikkel ruimten in uw AKS-cluster in te scha kelen en de prompts te volgen. Met de volgende opdracht worden ontwikkel ruimten in het *MyAKS* -cluster in de *MyResourceGroup* -groep ingeschakeld en wordt er een *standaard* -ontwikkel ruimte gemaakt.

> [!NOTE]
> De `use-dev-spaces`-opdracht installeert ook de Azure dev Space CLI als deze nog niet is geïnstalleerd. Het is niet mogelijk om de Azure dev Spaces CLI te installeren in de Azure Cloud Shell.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS


'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Voorbeeld toepassings code ophalen

In dit artikel gebruikt u de [voorbeeld toepassing Azure dev Spaces](https://github.com/Azure/dev-spaces) om te demonstreren hoe u Azure dev Spaces gebruikt.

Kloon de toepassing vanuit GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>De voorbeeld toepassing voorbereiden in Visual Studio code

Open Visual Studio code, selecteer **bestand** en vervolgens **openen**, navigeer naar de map *dev-Spaces/samples/Java/Getting-Started/webfrontend* en selecteer **openen**.

U hebt nu het *webfrontend* -project geopend in Visual Studio code. Als u de toepassing in uw ontwikkelaars ruimte wilt uitvoeren, genereert u de helm-grafiek assets met de extensie Azure dev Spaces in het opdracht palet.

Selecteer **weer gave** en **opdracht palet**om het opdracht palet te openen in Visual Studio code. Begin met het typen van `Azure Dev Spaces` en selecteer **Azure dev Spaces: configuratie bestanden voorbereiden voor Azure dev Spaces**.

![Configuratie bestanden voorbereiden voor Azure-ontwikkel ruimten](./media/common/command-palette.png)

Wanneer Visual Studio code ook u vraagt om uw basis installatie kopieën, belichte poort en openbaar eind punt te configureren, kiest u `Azul Zulu OpenJDK for Azure (Free LTS)` voor de basis installatie kopie, `8080` voor de beschik bare poort en `Yes` om een openbaar eind punt in te scha kelen.

![Basis installatie kopie selecteren](media/get-started-java/select-base-image.png)

![Beschik bare poort selecteren](media/get-started-java/select-exposed-port.png)

![Openbaar eind punt selecteren](media/get-started-java/select-public-endpoint.png)

Met deze opdracht wordt het project voor bereid om te worden uitgevoerd in azure dev Spaces door een Dockerfile-en helm-grafiek te genereren. Er wordt ook een *vscode* -map gegenereerd met de configuratie van fout opsporing in de hoofdmap van uw project.

> [!TIP]
> De [Dockerfile-en helm-grafiek](how-dev-spaces-works.md#prepare-your-code) voor uw project wordt gebruikt door Azure dev Spaces om uw code te bouwen en uit te voeren, maar u kunt deze bestanden wijzigen als u wilt wijzigen hoe het project wordt gemaakt en uitgevoerd.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Code maken en uitvoeren in Kubernetes vanuit Visual Studio code

Selecteer het pictogram **fout opsporing** aan de linkerkant en selecteer bovenaan **Java-programma starten (AZDS)** .

![Java-programma starten](media/get-started-java/debug-configuration.png)

Met deze opdracht bouwt en voert u uw service uit in azure dev Spaces. In het **Terminal** venster onderaan ziet u de uitvoer en url's van de build voor uw service met Azure dev Spaces. In de **console fout opsporing** wordt de logboek uitvoer weer gegeven.

> [!Note]
> Als er geen Azure dev Space-opdrachten in het **opdracht palet**worden weer gegeven, moet u ervoor zorgen dat u de [Visual Studio code Extension voor Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)hebt geïnstalleerd. Controleer ook of u de map *dev-Spaces/samples/Java/Getting-Started/webfrontend* hebt geopend in Visual Studio code.

U ziet de service die wordt uitgevoerd door de open bare URL te openen.

Selecteer **fout** opsporing en **Stop fout opsporing** om het fout opsporingsprogramma te stoppen.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, kunt u elk bestand in het project bijwerken en het **Java-programma starten (AZDS)** opnieuw uitvoeren. Bijvoorbeeld:

1. Als uw toepassing nog steeds wordt uitgevoerd, selecteert u **fout** opsporing en **stopt u fout opsporing** .
1. [Regel 19 in `src/main/java/com/ms/sample/webfrontend/Application.java`](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) bijwerken naar:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Sla uw wijzigingen op.
1. **Start Java-programma (AZDS)** opnieuw.
1. Navigeer naar uw actieve service en Bekijk uw wijzigingen.
1. Selecteer **fout opsporing** en **Stop fout opsporing** om uw toepassing te stoppen.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Onderbrekings punten instellen en gebruiken voor fout opsporing

Start uw service met **Java-programma starten (AZDS)** . Hiermee wordt ook uw service uitgevoerd in de foutopsporingsmodus.

Ga terug naar de weer gave van de **Verkenner** door **weer gave** en vervolgens **Verkenner**te selecteren. Open *src/main/Java/com/MS/sample/webfrontend/Application. java* en klik ergens op regel 19 om de cursor daar te plaatsen. Als u een onderbrekings punt wilt instellen, drukt u op **F9** of selecteert u **fout opsporing** in-en **uitschakelen**.

Open uw service in een browser en Let op dat er geen bericht wordt weer gegeven. Ga terug naar Visual Studio code en kijk regel 19 is gemarkeerd. Het onderbrekings punt dat u instelt, heeft de service op regel 19 onderbroken. Als u de service wilt hervatten, drukt u op **F5** of selecteert u **fout opsporing** en vervolgens **door gaan**. Ga terug naar uw browser en u ziet dat het bericht nu wordt weer gegeven.

Tijdens het uitvoeren van uw service in Kubernetes met een fout opsporingsprogramma, hebt u volledige toegang tot fout opsporingsgegevens, zoals de aanroep stack, lokale variabelen en uitzonderings gegevens.

Verwijder het onderbrekings punt door de cursor op regel 19 in *src/main/Java/com/MS/sample/webfrontend/Application. java* te plaatsen en op **F9**te drukken.

## <a name="update-code-from-visual-studio-code"></a>Code bijwerken vanuit Visual Studio code

Terwijl de service wordt uitgevoerd in de foutopsporingsmodus, werkt u regel 19 bij in *src/main/Java/com/MS/sample/webfrontend/Application. java*. Bijvoorbeeld:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Sla het bestand op. Selecteer **fouten** opsporen en vervolgens **opnieuw starten** of klik op de **werk balk fout**opsporing op de knop **fout opsporing opnieuw opstarten** .

![Fout opsporing vernieuwen](media/common/debug-action-refresh.png)

Open uw service in een browser en kijk of het bijgewerkte bericht wordt weer gegeven.

In plaats van een nieuwe container installatie kopie opnieuw te maken en te implementeren elke keer dat er code bewerkingen worden uitgevoerd, compileert Azure dev Spaces incrementeel code binnen de bestaande container om een snellere bewerking/debug-lus te bieden.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure dev Spaces u helpt om complexere toepassingen te ontwikkelen in meerdere containers en hoe u samenwerkings ontwikkeling kunt vereenvoudigen door te werken met verschillende versies of vertakkingen van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Werken met meerdere containers en teamontwikkeling](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
