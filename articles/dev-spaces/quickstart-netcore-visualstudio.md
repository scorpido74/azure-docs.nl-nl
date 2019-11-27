---
title: 'Fouten opsporen en herhalen op Kubernetes: Visual Studio & .NET core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Snelle Kubernetes-ontwikkeling met containers en microservices in Azure
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: a151314bef14e302879f4db0f7c0094779bdcfec
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325611"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Quick Start: fouten opsporen en herhalen op Kubernetes: Visual Studio & .NET core-Azure dev Spaces

In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Iteratief code ontwikkelen in containers met Visual Studio.
- Fout opsporing in code die wordt uitgevoerd in uw cluster met Visual Studio.

Met Azure dev Spaces kunt u ook fouten opsporen en herhalen met:
- [Java-en Visual Studio code](quickstart-java.md)
- [Node. js en Visual Studio code](quickstart-nodejs.md)
- [.NET core en Visual Studio code](quickstart-netcore.md)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account aanmaken](https://azure.microsoft.com/free).
- Visual Studio 2019 op Windows met de werk belasting Azure Development geïnstalleerd. U kunt Visual Studio 2017 ook gebruiken in Windows met de werk belasting voor Web Development en [Visual Studio Tools voor Kubernetes](https://aka.ms/get-vsk8stools) geïnstalleerd. Als Visual Studio niet is geïnstalleerd, kunt u het [hier](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)downloaden.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes service-cluster maken

U moet een AKS-cluster in een [ondersteunde regio][supported-regions]maken. Een cluster maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Selecteer *+ een resource maken > Kubernetes-service*. 
1. Voer het _abonnement_, de _resource groep_, de _Kubernetes-cluster naam_, de _regio_, de _Kubernetes-versie_en het voor voegsel van de _DNS-naam_in.

    ![AKS maken in de Azure Portal](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Klik op *Controleren + maken*.
1. Klik op *Create*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure dev Spaces inschakelen op uw AKS-cluster

Navigeer naar uw AKS-cluster in de Azure Portal en klik op *dev Spaces*. Wijzig *dev Spaces* in *Ja* en klik op *Opslaan*.

![Ontwikkel ruimten in de Azure Portal inschakelen](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Een nieuwe ASP.NET-Web-app maken

1. Open Visual Studio.
1. Een nieuw project maken.
1. Kies *ASP.net core webtoepassing* en klik op *volgende*.
1. Geef uw project *webfrontend* een naam en klik op *maken*.
1. Als u hierom wordt gevraagd, kiest u *Web Application (model-view-controller)* voor de sjabloon.
1. Selecteer in de bovenste versie van *.net core* en *ASP.net Core 2,1* .
1. Klik op *Create*.

## <a name="connect-your-project-to-your-dev-space"></a>Verbind uw project met uw ontwikkel ruimte

Selecteer in uw project **Azure dev Spaces** in de vervolg keuzelijst start instellingen, zoals hieronder wordt weer gegeven.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Selecteer uw *abonnement* en *Azure Kubernetes-cluster*in het dialoog venster Azure dev Spaces. Houd de *ruimte* ingesteld op *standaard* en schakel het selectie vakje *openbaar toegankelijk* in. Klik op *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Met dit proces wordt uw service geïmplementeerd naar de *standaard* ontwikkelaars ruimte met een openbaar toegankelijke URL. Als u een cluster kiest dat niet is geconfigureerd om te werken met Azure Dev Spaces, ziet u een bericht waarin wordt gevraagd of u dit wilt configureren. Klik op *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

De open bare URL voor de service die wordt uitgevoerd in de *standaard* -dev-ruimte wordt weer gegeven in het venster *uitvoer* :

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

In het bovenstaande voor beeld is de open bare URL http://default.webfrontend.1234567890abcdef1234.eus.azds.io/. Ga naar de open bare URL van uw service en communiceer met de service die wordt uitgevoerd in uw dev-ruimte.

Dit proces heeft mogelijk open bare toegang tot uw service uitgeschakeld. Als u open bare toegang wilt inschakelen, kunt u de [ingangs waarde bijwerken in de *waarden. yaml*][ingress-update].

## <a name="update-code"></a>Code bijwerken

Als Visual Studio nog steeds is verbonden met uw dev-ruimte, klikt u op de knop stoppen. Wijzig regel 20 in `Controllers/HomeController.cs` in:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Sla uw wijzigingen op en start uw service met **Azure dev Spaces** in de vervolg keuzelijst start instellingen. Open de open bare URL van uw service in een browser en klik op *about*. Houd er rekening mee dat het bijgewerkte bericht wordt weer gegeven.

In plaats van een nieuwe container installatie kopie opnieuw te maken en te implementeren elke keer dat er code bewerkingen worden uitgevoerd, compileert Azure dev Spaces incrementeel code binnen de bestaande container om een snellere bewerking/debug-lus te bieden.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Onderbrekings punten instellen en gebruiken voor fout opsporing

Als Visual Studio nog steeds is verbonden met uw dev-ruimte, klikt u op de knop stoppen. Open `Controllers/HomeController.cs` en klik ergens op regel 20 om de cursor daar te plaatsen. Stel een onderbrekings punt in op *F9* of klik op *debug* en vervolgens op *onderbrekings punt*. Als u de service in de modus voor fout opsporing in uw ruimte voor ontwikkel aars wilt starten, drukt u op *F5* of klikt u op *fout* *opsporing starten*.

Open uw service in een browser en Let op dat er geen bericht wordt weer gegeven. Ga terug naar Visual Studio en houd er rekening mee dat regel 20 is gemarkeerd. Het onderbrekings punt dat u hebt ingesteld, heeft de service op regel 20 onderbroken. Als u de service wilt hervatten, druk op *F5* of klik op *debug* en vervolgens op *door gaan*. Ga terug naar uw browser en u ziet dat het bericht nu wordt weer gegeven.

Tijdens het uitvoeren van uw service in Kubernetes met een fout opsporingsprogramma, hebt u volledige toegang tot fout opsporingsgegevens, zoals de aanroep stack, lokale variabelen en uitzonderings gegevens.

Verwijder het onderbrekings punt door de cursor op regel 20 in `Controllers/HomeController.cs` te plaatsen en op *F9 te drukken*.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

Navigeer naar uw resource groep in de Azure Portal en klik op *resource groep verwijderen*. U kunt ook de opdracht [AZ AKS delete](/cli/azure/aks#az-aks-delete) gebruiken:

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Werken met meerdere containers en teamontwikkeling](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works.md#how-running-your-code-is-configured
[supported-regions]: about.md#supported-regions-and-configurations
