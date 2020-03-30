---
title: 'Debugen en herhalen op Kubernetes: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Met deze quickstart u Azure Dev Spaces en Visual Studio gebruiken om een .NET Core-toepassing op Azure Kubernetes Service te debuggen en snel te herhalen
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 5d0d1541600f4c10b021d5d7f7f435f1aa5ae589
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239727"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Snelstart: Foutopsporing en herhalen op Kubernetes: Visual Studio & .NET Core - Azure Dev Spaces

In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Iteratief code ontwikkelen in containers met Visual Studio.
- Foutopsporingscode die in uw cluster wordt uitgevoerd met Visual Studio.

Azure Dev Spaces u ook debuggen en herhalen met behulp van:
- [Java- en Visual Studio-code](quickstart-java.md)
- [Node.js en Visual Studio Code](quickstart-nodejs.md)
- [.NET Core en Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account aanmaken](https://azure.microsoft.com/free).
- Visual Studio 2019 op Windows met de Azure Development-workload geïnstalleerd. U Visual Studio 2017 ook op Windows gebruiken met de Web Development-workload en [Visual Studio Tools voor Kubernetes](https://aka.ms/get-vsk8stools) geïnstalleerd. Als u Visual Studio niet hebt geïnstalleerd, downloadt u deze [hier.](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes Service-cluster maken

U moet een AKS-cluster maken in een [ondersteund gebied][supported-regions]. Een cluster maken:

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Selecteer *+ Een bron maken > Kubernetes-service*. 
1. Voer het _voorvoegsel Abonnement_, _Resourcegroep_, _Kubernetes-cluster_, _Regio,_ _Kubernetes-versie_en _DNS-naam voor ._

    ![AKS maken in de Azure-portal](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Klik op *Controleren + maken*.
1. Klik *op Maken*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure Dev-ruimten inschakelen op uw AKS-cluster

Navigeer naar uw AKS-cluster in de Azure-portal en klik op *Dev Spaces*. *Dev-spaties gebruiken* wijzigen in *Ja* en op *Opslaan klikken*.

![Dev-spaties inschakelen in de Azure-portal](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Een nieuwe ASP.NET web-app maken

1. Open Visual Studio.
1. Een nieuw project maken.
1. Kies *ASP.NET Kernwebtoepassing* en klik op *Volgende*.
1. Geef uw project *een naam aan webfrontend* en klik op *Maken*.
1. Kies deoptie *Webtoepassing (Model-View-Controller)* voor de sjabloon wanneer u daarom wordt gevraagd.
1. Selecteer *bovenin .NET Core* en *ASP.NET Core 2.1.*
1. Klik *op Maken*.

## <a name="connect-your-project-to-your-dev-space"></a>Verbind uw project met uw dev-ruimte

Selecteer **Azure Dev Spaces** in de vervolgkeuzelijst voor startinstellingen zoals hieronder weergegeven.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Selecteer in het dialoogvenster Azure Dev Spaces de optie *Uw abonnement* en *Azure Kubernetes-cluster*. Ruimte *laten* instellen op *standaard* en schakel het selectievakje Openbaar *toegankelijk in.* Klik op *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Dit proces implementeert uw *default* service naar de standaarddev-ruimte met een openbaar toegankelijke URL. Als u een cluster kiest dat niet is geconfigureerd om te werken met Azure Dev Spaces, ziet u een bericht waarin wordt gevraagd of u dit wilt configureren. Klik op *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

De openbare URL voor de *default* service die wordt uitgevoerd in de standaarddevruimte wordt weergegeven in het venster *Uitvoer:*

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

Service 'webfrontend' port 'http' is available at http://default.webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

In het bovenstaande voorbeeld is http://default.webfrontend.1234567890abcdef1234.eus.azds.io/de openbare URL . 

Selecteer **Foutopsporing** **en begin met foutopsporing**. Na een paar seconden wordt uw service gestart en opent Visual Studio een browser met de openbare URL van de service. Als een browser niet automatisch wordt geopend, navigeert u naar de openbare URL van uw service in een browser en communiceert u met de service die in uw dev-ruimte wordt uitgevoerd.

Dit proces heeft mogelijk de toegang van het publiek tot uw service uitgeschakeld. Als u de toegang van het publiek wilt inschakelen, u de [invallende waarde in de *values.yaml bijwerken.*][ingress-update]

## <a name="update-code"></a>Code bijwerken

Als Visual Studio nog steeds is verbonden met uw dev-ruimte, klikt u op de stopknop. Regel 20 `Controllers/HomeController.cs` wijzigen in:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Sla uw wijzigingen op en selecteer **Foutopsporing** **en begin met foutopsporing**. Na een paar seconden wordt uw service gestart en opent Visual Studio een browser met de openbare URL van de service. Als een browser niet automatisch wordt geopend, navigeert u door de openbare URL van uw service in een browser en klikt u op *Over*. Houd er rekening mee dat uw bijgewerkte bericht wordt weergegeven.

In plaats van elke keer dat codebewerkingen worden uitgevoerd, hercompileert en herschikt een nieuwe containerafbeelding, wordt de code binnen de bestaande container stapsgewijs opnieuw gecompileerd om een snellere lus voor bewerken/foutopsporing te bieden.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Breekpunten instellen en gebruiken voor foutopsporing

Als Visual Studio nog steeds is verbonden met uw dev-ruimte, klikt u op de stopknop. Open `Controllers/HomeController.cs` en klik ergens op lijn 20 om je cursor daar te plaatsen. Als u een breakpoint wilt instellen, klikt *u* op *Foutopsporing en* schakelt *u Breekpunt in*. Als u uw service wilt starten in de foutopsporingsmodus in uw dev-ruimte, klikt u op *F5* of klikt u op *Foutopsporing* *en start u Foutopsporing*.

Open uw service in een browser en merk dat er geen bericht wordt weergegeven. Ga terug naar Visual Studio en observeer lijn 20 wordt gemarkeerd. Het breekpunt dat u instelt, heeft de service op lijn 20 onderbroken. Als u de service wilt hervatten, klikt u op *F5* of klikt u op *Foutopsporing* *en vervolgens Doorgaan*. Keer terug naar uw browser en merk dat het bericht nu wordt weergegeven.

Tijdens het uitvoeren van uw service in Kubernetes met een debugger gekoppeld, hebt u volledige toegang tot foutopsporingsinformatie zoals de callstack, lokale variabelen en uitzonderingsinformatie.

Verwijder het breekpunt door uw cursor `Controllers/HomeController.cs` op lijn 20 in en raken *F9*.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-bronnen opschonen

Navigeer naar uw brongroep in de Azure-portal en klik op *Brongroep verwijderen*. U ook de opdracht [az aks delete](/cli/azure/aks#az-aks-delete) gebruiken:

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Werken met meerdere containers en teamontwikkeling](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
