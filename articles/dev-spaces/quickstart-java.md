---
title: 'Fouten opsporen en herhalen op Kubernetes: Visual Studio Code en Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: In deze quickstart ziet u hoe u Azure Dev Spaces en Visual Studio Code gebruikt om fouten op te sporen en snel een Java-toepassing te herhalen in Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Java, Helm, servicemesh, servicemeshroutering, kubectl, k8s
manager: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: c0fd035a0341e5e92b2706d833b24d5e5b87e018
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371563"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Quickstart: Fouten opsporen en herhalen op Kubernetes met Visual Studio Code en Java - Azure Dev Spaces

In deze quickstart stelt u Azure Dev Spaces in met een beheerd Kubernetes-cluster en gebruikt u een Java-app in Visual Studio Code om iteratief code in containers te ontwikkelen en fouten in code in containers op te sporen. Met Azure Dev Spaces kunt u ook fouten opsporen en alle onderdelen van uw toepassing in Azure Kubernetes Service (AKS) testen met minimale instellingen van de ontwikkelcomputer. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Java Development Kit (JDK) 1.8.0+](https://aka.ms/azure-jdks).
- [Maven 3.5.0+](https://maven.apache.org/download.cgi).
- [Visual Studio Code](https://code.visualstudio.com/download).
- De foutopsporingsextensies [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) en [Java for Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) voor Visual Studio Code.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een upgrade maken van een Azure Kubernetes Service-cluster

U moet een AKS-cluster maken in een [ondersteunde regio][supported-regions]. Met de volgende opdrachten maakt u een resourcegroep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure Dev Spaces instellen op uw AKS-cluster inschakelen

Gebruik de opdracht `use-dev-spaces` om Dev Spaces in te schakelen voor uw AKS-cluster en volg de prompts. Met de volgende opdracht schakelt u Dev Spaces in voor het cluster *MyAKS* in de groep *MyResourceGroup* en maakt u een *standaard* dev-ruimte.

> [!NOTE]
> Met de opdracht `use-dev-spaces` wordt ook de CLI voor Azure Dev Spaces geïnstalleerd als die nog niet is geïnstalleerd. U kunt de CLI voor Azure Dev Spaces niet installeren in Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Voorbeeldtoepassingscode ophalen

In dit artikel gebruikt u de [voorbeeldtoepassing van Azure Dev Spaces](https://github.com/Azure/dev-spaces) om het gebruiken van Azure Dev Spaces te demonstreren.

Kloon de toepassing vanuit GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>De voorbeeldtoepassing voorbereiden in Visual Studio

Open Visual Studio Code, selecteer achtereenvolgens **Bestand** en **Openen**, navigeer naar de map *dev-spaces/samples/java/getting-started/webfrontend* en selecteer **Openen**.

U hebt nu het project *webfrontend* geopend in Visual Studio Code. Als u de toepassing in uw ontwikkelaarsruimte wilt uitvoeren, genereert u de Docker- en Helm-grafiekassets met behulp van de extensie Azure Dev Spaces in het Opdrachtpalet.

Open het opdrachtpalet in Visual Studio Code door achtereenvolgens **Beeld** en **Opdrachtpalet** te selecteren. Begin met het typen van `Azure Dev Spaces` en selecteer **Azure Dev Spaces: Configuratiebestanden voorbereiden voor Azure Dev Spaces**.

![Configuratiebestanden voorbereiden voor Azure Dev Spaces](./media/common/command-palette.png)

Wanneer Visual Studio code u ook vraagt om uw basisinstallatiekopieën, opengestelde poort en openbare eindpunt te configureren, kiest u `Azul Zulu OpenJDK for Azure (Free LTS)` als basisinstallatiekopie, `8080` als opengestelde poort en `Yes` om een openbaar eindpunt in te schakelen.

![Basisinstallatiekopie selecteren](media/get-started-java/select-base-image.png)

![Opengestelde poort selecteren](media/get-started-java/select-exposed-port.png)

![Openbaar eindpunt selecteren](media/get-started-java/select-public-endpoint.png)

Met deze opdracht wordt het project voorbereid om te worden uitgevoerd in Azure Dev Spaces door een Dockerfile en Helm-grafiek te genereren. Ook wordt er in de hoofdmap van uw project een *.vscode*-map gegenereerd met de configuratie voor foutopsporing.

> [!TIP]
> De [Dockerfile en Helm-grafiek](how-dev-spaces-works-prep.md#prepare-your-code) voor uw project worden gebruikt in Azure Dev Spaces om uw code te bouwen en uit te voeren, maar u kunt deze bestanden wijzigen als u wilt wijzigen hoe het project wordt gebouwd en uitgevoerd.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Code compileren en uitvoeren in Kubernetes vanuit Visual Studio Code

Selecteer het pictogram **Fouten opsporen** aan de linkerkant en selecteer **Java-programma starten (AZDS)** bovenaan.

![Java-programma starten](media/get-started-java/debug-configuration.png)

Met deze opdracht wordt uw service in Azure Dev Spaces gecompileerd en uitgevoerd. In het venster **Terminal** onderaan ziet u de uitvoer van de build en de URL's voor uw service die wordt uitgevoerd in Azure Dev Spaces. In de **Console voor foutopsporing** wordt de logboekuitvoer weergegeven.

> [!Note]
> Als u geen Azure Dev Spaces-opdrachten ziet in het **Opdrachtenpalet**, controleert u of u de [Visual Studio Code-extensie voor Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) hebt geïnstalleerd. Controleer ook of u de map *dev-spaces/samples/java/getting-started/webfrontend* hebt geopend in Visual Studio Code.

U kunt zien dat de service wordt uitgevoerd door de openbare URL te openen.

Selecteer **Fouten opsporen** en selecteer vervolgens **Foutopsporing stoppen** om het foutopsporingsprogramma te stoppen.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, kunt u een bestand in uw project bijwerken en **Java-programma starten (AZDS)** nogmaals uitvoeren. Bijvoorbeeld:

1. Als uw toepassing nog wordt uitgevoerd, selecteert u **Fouten opsporen** en vervolgens **Foutopsporing stoppen** om deze te stoppen.
1. Wijzig [regel 19 in `src/main/java/com/ms/sample/webfrontend/Application.java`](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) in:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Sla uw wijzigingen op.
1. Voer **Java-programma starten (AZDS)** opnieuw uit.
1. Navigeer naar uw actieve service en bekijk uw wijzigingen.
1. Selecteer **Fouten opsporen** en vervolgens **Foutopsporing stoppen** om uw toepassing te stoppen.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Onderbrekingspunten instellen en gebruiken voor fouten opsporen

Start uw service met **Java-programma starten (AZDS)** . Hiermee wordt uw service bovendien uitgevoerd in de foutopsporingsmodus.

Ga terug naar de **Explorer**-weergave door **Beeld** en vervolgens **Explorer** te selecteren. Open *src/main/java/com/ms/sample/webfrontend/Application.java* en klik ergens op regel 19 om de cursor daarnaar te verplaatsen. Als u een onderbrekingspunt wilt instellen, drukt u op **F9** of selecteert u **Fouten opsporen** en vervolgens **Onderbrekingspunt inschakelen**.

Open uw service in een browser en let op dat er geen bericht wordt weergegeven. Ga terug naar Visual Studio Code. U ziet dat regel 19 is gemarkeerd. Het onderbrekingspunt dat u hebt ingesteld, heeft de service op regel 19 onderbroken. Als u de service wilt hervatten, drukt u op **F5** of selecteert u **Fouten opsporen** en vervolgens **Doorgaan**. Ga terug naar uw browser en u ziet dat het bericht nu wordt weergegeven.

Tijdens het uitvoeren van uw service in Kubernetes met een foutopsporingsprogramma, hebt u volledige toegang tot foutopsporingsgegevens, zoals de aanroepstack, lokale variabelen en uitzonderingsgegevens.

Verwijder het onderbrekingspunt door de cursor op regel 19 in *src/main/Java/com/MS/sample/webfrontend/Application. java* te plaatsen en op **F9** te drukken.

## <a name="update-code-from-visual-studio-code"></a>Code bijwerken vanuit Visual Studio Code

Terwijl de service wordt uitgevoerd in de foutopsporingsmodus, werkt u regel 19 bij *src/main/Java/com/MS/sample/webfrontend/Application.java*. Bijvoorbeeld:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Sla het bestand op. Selecteer **Fouten opsporen** en vervolgens **Foutopsporing opnieuw starten** of selecteer op de werkbalk **Foutopsporing** de knop **Foutopsporing opnieuw starten**.

![Foutopsporing vernieuwen](media/common/debug-action-refresh.png)

Open uw service in een browser. U ziet dat uw bijgewerkte bericht wordt weergegeven.

In plaats van telkens als codewijzigingen zijn aangebracht een nieuwe containerinstallatiekopie opnieuw te bouwen en opnieuw te implementeren, hercompileert Azure Dev Spaces incrementeel code binnen de bestaande container voor een snellere bewerkings-/foutopsporingslus.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u kan helpen om complexere toepassingen te ontwikkelen binnen meerdere containers en hoe u ontwikkeling in samenwerkingsverband vereenvoudigt door in verschillende ruimten met verschillende versies of vertakkingen van uw code te werken.

> [!div class="nextstepaction"]
> [Werken met meerdere containers en teamontwikkeling](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
