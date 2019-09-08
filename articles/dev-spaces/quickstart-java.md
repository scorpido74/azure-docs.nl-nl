---
title: Fouten opsporen en herhalen met Visual Studio code en Java op Kubernetes met behulp van Azure dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Snelle Kubernetes ontwikkeling met containers, micro Services en Java op Azure
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, Java, helm, Service-Mesh, Service-Mesh-route ring, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 28b6a2ecff756c878bb3bc0a69976e7cede5f7ee
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772664"
---
# <a name="quickstart-debug-and-iterate-with-visual-studio-code-and-java-on-kubernetes-using-azure-dev-spaces"></a>Quickstart: Fouten opsporen en herhalen met Visual Studio code en Java op Kubernetes met behulp van Azure dev Spaces

In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Ontwikkel iteratieve code in containers met Visual Studio code.
- Fout opsporing voor de code in uw dev-ruimte vanuit Visual Studio code.


## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account aanmaken](https://azure.microsoft.com/free).
- [Visual Studio code is geïnstalleerd](https://code.visualstudio.com/download).
- De [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) en [Java debugger voor Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) -extensies voor Visual Studio code geïnstalleerd.
- [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Maven is geïnstalleerd en geconfigureerd](https://maven.apache.org).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes service-cluster maken

U moet een AKS-cluster maken in een [ondersteunde regio][supported-regions]. Met de onderstaande opdrachten maakt u een resource groep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure dev Spaces inschakelen op uw AKS-cluster

Gebruik de `use-dev-spaces` opdracht om ontwikkel ruimten in uw AKS-cluster in te scha kelen en de prompts te volgen. Met de onderstaande opdracht maakt u ontwikkel ruimten in het *MyAKS* -cluster in de groep *MyResourceGroup* en maakt u een *standaard* dev-ruimte.

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

Open Visual Studio code, klik op *bestand* en vervolgens op *openen...* Navigeer naar de map *dev-Spaces/samples/Java/Getting-Started/webfrontend* en klik op *openen*.

U hebt nu het *webfrontend* -project geopend in Visual Studio code. Als u de toepassing in uw ontwikkelaars ruimte wilt uitvoeren, genereert u de helm-grafiek assets met de extensie Azure dev Spaces in het opdracht palet.

Als u het opdracht palet wilt openen in Visual Studio code, klikt u op *weer gave* en vervolgens op *opdracht palet*. Begin met `Azure Dev Spaces` typen en `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`Klik op.

![Configuratie bestanden voorbereiden voor Azure-ontwikkel ruimten](./media/common/command-palette.png)

Wanneer Visual Studio code ook u vraagt om uw basis installatie kopieën, belichte poort en openbaar eind `Azul Zulu OpenJDK for Azure (Free LTS)` punt te configureren, kiest `8080` u voor de basis installatie kopie `Yes` , voor de beschik bare poort en om een openbaar eind punt in te scha kelen.

![Basis installatie kopie selecteren](media/get-started-java/select-base-image.png)

![Beschik bare poort selecteren](media/get-started-java/select-exposed-port.png)

![Openbaar eind punt selecteren](media/get-started-java/select-public-endpoint.png)

Met deze opdracht wordt het project voor bereid om te worden uitgevoerd in azure dev Spaces door een Dockerfile-en helm-grafiek te genereren. Er wordt ook een *vscode* -map gegenereerd met de configuratie van fout opsporing in de hoofdmap van uw project.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Code maken en uitvoeren in Kubernetes vanuit Visual Studio

Klik op het pictogram *fout opsporing* aan de linkerkant en klik bovenaan op *Java-programma starten (AZDS)* .

![Java-programma starten](media/get-started-java/debug-configuration.png)

Met deze opdracht bouwt en voert u uw service uit in azure dev Spaces. In het *Terminal* venster onderaan ziet u de uitvoer en url's van de build voor uw service met Azure dev Spaces. In de *console fout opsporing* wordt de logboek uitvoer weer gegeven.

> [!Note]
> Als er geen Azure dev Space-opdrachten in het *opdracht palet*worden weer gegeven, moet u ervoor zorgen dat u de [Visual Studio code Extension voor Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)hebt geïnstalleerd. Controleer ook of u de map *dev-Spaces/samples/Java/Getting-Started/webfrontend* hebt geopend in Visual Studio code.

U ziet de service die wordt uitgevoerd door de open bare URL te openen.

Klik op *fout* opsporing en vervolgens op *fout opsporing stoppen* om het fout opsporingsprogramma te stoppen.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, kunt u elk bestand in het project bijwerken en het *Java-programma starten (AZDS)* opnieuw uitvoeren. Bijvoorbeeld:

1. Als uw toepassing nog steeds wordt uitgevoerd, klikt u op *fout* opsporing en *stopt u fout opsporing* .
1. [Regel 19 `src/main/java/com/ms/sample/webfrontend/Application.java` ](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) bijwerken naar:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Sla uw wijzigingen op.
1. *Start Java-programma (AZDS)* opnieuw.
1. Navigeer naar uw actieve service en Bekijk uw wijzigingen.
1. Klik op *fout* opsporing en vervolgens op *fout opsporing stoppen* om uw toepassing te stoppen.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Onderbrekings punten instellen en gebruiken voor fout opsporing

Start uw service met *Java-programma starten (AZDS)* . Hiermee wordt ook uw service uitgevoerd in de foutopsporingsmodus.

Ga terug naar de weer gave van de *Verkenner* door te klikken op *weer gave* en vervolgens op *Explorer*. Open `src/main/java/com/ms/sample/webfrontend/Application.java` en klik ergens op regel 19 om de cursor daar te plaatsen. Stel een onderbrekings punt in op *F9* of klik op *debug* en vervolgens op *onderbrekings punt*.

Open uw service in een browser en Let op dat er geen bericht wordt weer gegeven. Ga terug naar Visual Studio code en kijk regel 19 is gemarkeerd. Het onderbrekings punt dat u instelt, heeft de service op regel 19 onderbroken. Als u de service wilt hervatten, druk op *F5* of klik op *debug* en vervolgens op *door gaan*. Ga terug naar uw browser en u ziet dat het bericht nu wordt weer gegeven.

Tijdens het uitvoeren van uw service in Kubernetes met een fout opsporingsprogramma, hebt u volledige toegang tot fout opsporingsgegevens, zoals de aanroep stack, lokale variabelen en uitzonderings gegevens.

Verwijder het onderbrekings punt door de cursor op regel 19 `src/main/java/com/ms/sample/webfrontend/Application.java` in te plaatsen en op *F9 te drukken*.

## <a name="update-code-from-visual-studio-code"></a>Code bijwerken vanuit Visual Studio code

Terwijl de service wordt uitgevoerd in de foutopsporingsmodus, werkt u regel 19 `src/main/java/com/ms/sample/webfrontend/Application.java`bij. Bijvoorbeeld:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Sla het bestand op. Klik op *fouten opsporen* en vervolgens op fout *opsporing opnieuw starten* of Klik in de *werk balk fout opsporing*op de knop *fout opsporing opnieuw opstarten* .

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


[supported-regions]: about.md#supported-regions-and-configurations
