---
title: 'Fouten opsporen en herhalen op Kubernetes: Visual Studio en .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: In deze quickstart ziet u hoe u Azure Dev Spaces en Visual Studio gebruikt om fouten op te sporen en snel een .NET Core-toepassing te herhalen in Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 8279a32ece16209c1dd5bca13d08e22b283677ee
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87007001"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Quickstart: Fouten opsporen en herhalen op Kubernetes: Visual Studio en .NET Core - Azure Dev Spaces

In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Iteratief code ontwikkelen in containers met Visual Studio.
- Fouten opsporen in code die in uw cluster wordt uitgevoerd met Visual Studio.

Met Azure Dev Spaces kunt u ook fouten opsporen en herhalen met:
- [Java en Visual Studio Code](quickstart-java.md)
- [Node.js en Microsoft Visual Studio Code](quickstart-nodejs.md)
- [.NET Core en Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account aanmaken](https://azure.microsoft.com/free).
- Visual Studio 2019 in Windows met de Azure Development-workload geïnstalleerd. Als Visual Studio nog niet is geïnstalleerd, downloadt u het [hier](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een upgrade maken van een Azure Kubernetes Service-cluster

U moet een AKS-cluster maken in een [ondersteunde regio][supported-regions]. Met de onderstaande opdrachten maakt u een resourcegroep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure Dev Spaces instellen op uw AKS-cluster inschakelen

Gebruik de opdracht `use-dev-spaces` om Dev Spaces in te schakelen voor uw AKS-cluster en volg de prompts. Met de onderstaande opdracht schakelt u Dev Spaces in voor het cluster *MyAKS* in de groep *MyResourceGroup* en maakt u een *standaard* dev-ruimte.

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

## <a name="create-a-new-aspnet-web-app"></a>Een nieuwe ASP.NET-web-app maken

1. Open Visual Studio.
1. Een nieuw project maken.
1. Kies *ASP.NET Core-webtoepassing* en klik op *Volgende*.
1. Geef uw project de naam *webfrontend* en klik op *Maken*.
1. Als hier om wordt gevraagd, kies *Webtoepassing (Model-View-Controller)* voor het sjabloon.
1. Selecteer *.NET Core* en *ASP.NET Core 2.1* bovenaan.
1. Klik op *Create*.

## <a name="connect-your-project-to-your-dev-space"></a>Verbind uw project met uw ontwikkelruimte

In uw project, selecteert u **Azure Dev Spaces** in de vervolgkeuzelijst met opstartinstellingen zoals hieronder wordt weergegeven.

![Schermopname van de gebruikersinterface van Visual Studio met de optie IIS Express gemarkeerd en geselecteerd en de optie Azure Dev Spaces gemarkeerd.](media/get-started-netcore-visualstudio/LaunchSettings.png)

Selecteer in het dialoogvenster Azure Dev Spaces uw *Abonnement* en *Azure Kubernetes-cluster*. Laat *Ruimte* ingesteld op *Standaard* en schakel het selectievakje *Openbaar toegankelijk* in. Klik op *OK*.

![Schermopname van het dialoogvenster Azure Dev Spaces.](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Met dit proces wordt uw service geïmplementeerd naar de *standaard* ontwikkelruimte met een openbaar toegankelijke URL. Als u een cluster kiest dat niet is geconfigureerd om te werken met Azure Dev Spaces, ziet u een bericht waarin wordt gevraagd of u dit wilt configureren. Klik op *OK*.

![Schermopname van het dialoogvenster voor het toevoegen van een resource in Azure Dev Spaces.](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

De openbare URL voor de service die wordt uitgevoerd in de *standaard* ontwikkelruimte wordt weergegeven in het venster *Uitvoer*:

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

In het bovenstaande voorbeeld is de openbare URL `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`. 

Selecteer **Fouten opsporen** en vervolgens **Fouten opsporen starten**. Na een paar seconden wordt uw service gestart en opent Visual Studio een browser met de openbare URL van de service. Als een browser niet automatisch wordt geopend, navigeert u naar de openbare URL van uw service in een browser en communiceert u met de service die wordt uitgevoerd in uw ontwikkelruimte.

Dit proces heeft mogelijk openbare toegang tot uw service uitgeschakeld. Als u openbare toegang wilt inschakelen, kunt u de [inkomende waarden bijwerken in de *values.yaml*][ingress-update].

## <a name="update-code"></a>Code bijwerken

Als Visual Studio nog steeds is verbonden met uw ontwikkelruimte, klikt u op de knop stoppen. Wijzig regel 20 in `Controllers/HomeController.cs` naar:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Sla de wijzigingen op en selecteer **Fouten opsporen** en vervolgens **Fouten opsporen starten**. Na een paar seconden wordt uw service gestart en opent Visual Studio een browser met de openbare URL van de service. Als een browser niet automatisch wordt geopend, navigeert u naar de openbare URL van uw service in een browser en klikt u op *Over*. Houd er rekening mee dat het bijgewerkte bericht wordt weergegeven.

In plaats van telkens als codewijzigingen zijn aangebracht een nieuwe containerinstallatiekopie opnieuw te bouwen en opnieuw te implementeren, hercompileert Azure Dev Spaces incrementeel code binnen de bestaande container voor een snellere bewerkings-/foutopsporingslus.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Onderbrekingspunten instellen en gebruiken voor fouten opsporen

Als Visual Studio nog steeds is verbonden met uw ontwikkelruimte, klikt u op de knop stoppen. Open `Controllers/HomeController.cs` en klik ergens op regel 20 om de cursor daar te plaatsen. Als u een onderbrekingspunt wilt instellen, klik op *F9* of *Fouten opsporen* en vervolgens *Onderbrekingspunt inschakelen*. Als u de service in de modus voor fouten opsporen in uw ontwikkelruimte, drukt u op *F5* of klikt u op *Fouten opsporen* en dan *Fouten opsporen starten*.

Open uw service in een browser en let op dat er geen bericht wordt weergegeven. Ga terug naar Visual Studio en houd er rekening mee dat regel 20 is gemarkeerd. Het onderbrekingspunt dat u hebt ingesteld, heeft de service op regel 20 onderbroken. Als u de service wilt hervatten, drukt u op *F5* of klikt u op *Fouten opsporen* vervolgens *Doorgaan*. Ga terug naar uw browser en u ziet dat het bericht nu wordt weergegeven.

Tijdens het uitvoeren van uw service in Kubernetes met een foutopsporingsprogramma, hebt u volledige toegang tot foutopsporingsgegevens, zoals de aanroepstack, lokale variabelen en uitzonderingsgegevens.

Verwijder het onderbrekingspunt door de cursor op regel 20 in `Controllers/HomeController.cs` te plaatsen en op *F9* te klikken.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

Navigeer naar uw resourcegroep in de Azure-portal en klik op *Resourcegroep verwijderen*. U kunt ook de opdracht [az aks delete](/cli/azure/aks#az-aks-delete) gebruiken:

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Werken met meerdere containers en teamontwikkeling](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
