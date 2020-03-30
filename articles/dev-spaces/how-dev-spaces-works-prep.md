---
title: Hoe het voorbereiden van een project voor Azure Dev Spaces werkt
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beschrijft hoe het voorbereiden van uw project met Azure Dev Spaces werkt
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241632"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Hoe het voorbereiden van een project voor Azure Dev Spaces werkt

Azure Dev Spaces biedt u meerdere manieren om Kubernetes-toepassingen snel te herhalen en te debuggen en samen te werken met uw team op een AKS-cluster (Azure Kubernetes Service). Dev Spaces kan Dockerfiles en Helm-diagrammen genereren voor uw project. Dev Spaces maakt en gebruikt ook een configuratiebestand voor het implementeren, uitvoeren en debuggen van uw Kubernetes-toepassingen in AKS. Al deze bestanden bevinden zich met de code van uw toepassing en kunnen worden toegevoegd aan uw versiebeheersysteem.

In dit artikel wordt beschreven wat er gebeurt en bereidt u uw project voor op het uitvoeren in AKS met Dev Spaces.

## <a name="prepare-your-code"></a>Uw code voorbereiden

Om uw toepassing in een v-ruimte uit te voeren, moet deze worden gecontaineriseerd en moet u definiëren hoe deze moet worden geïmplementeerd in Kubernetes. Om uw toepassing te containeriseren, hebt u een Dockerfile nodig. Als u wilt definiëren hoe uw toepassing wordt geïmplementeerd in Kubernetes, hebt u een [Helm-diagram](https://docs.helm.sh/)nodig. Om te helpen bij het maken van zowel de Dockerfile- `prep` als het Helm-diagram voor uw toepassing, bieden de client-side tools de opdracht:

```cmd
azds prep --enable-ingress
```

De `prep` opdracht bekijkt de bestanden in uw project en probeert de dockerfile- en helmdiagram te maken voor het uitvoeren van uw toepassing in Kubernetes. Momenteel genereert `prep` de opdracht een dockerfile- en helmdiagram met de volgende talen:

* Java
* Node.js
* .NET Core

U *moet* `prep` de opdracht uitvoeren vanuit een map met broncode. Als `prep` u de opdracht uitvoert vanuit de juiste map, kan de client-side tooling de taal identificeren en een geschikt Dockerbestand maken om uw toepassing te containeriseren. U de `prep` opdracht ook uitvoeren vanuit een map met een *pom.xml-bestand* voor Java-projecten.

Als u `prep` de opdracht uitvoert vanuit de map die geen broncode bevat, genereert de tooling aan de clientzijde geen Dockerfile. Er wordt ook een foutmelding weergegeven: *Dockerfile kan niet worden gegenereerd vanwege niet-ondersteunde taal.* Deze fout treedt ook op als de client-side tooling het projecttype niet herkent.

Wanneer u `prep` de opdracht uitvoert, u `--enable-ingress` de vlag opgeven. Deze vlag vertelt de controller om een internet-toegankelijk eindpunt voor deze service te maken. Als u deze vlag niet opgeeft, is de service alleen toegankelijk vanuit het cluster of met behulp van de localhosttunnel die is gemaakt door de client-side tooling. U dit gedrag inschakelen `prep` of uitschakelen nadat u de opdracht hebt uitgevoerd door het gegenereerde Helmdiagram bij te werken.

De `prep` opdracht vervangt geen bestaande Dockerfiles of Helm-diagrammen die u in uw project hebt. Als een bestaand Dockerfile- of Helm-diagram dezelfde naamgevingsconventie gebruikt als de bestanden die door de `prep` opdracht worden gegenereerd, slaat de `prep` opdracht het genereren van deze bestanden over. Anders genereert `prep` de opdracht zijn eigen Dockerfile- of Helm-diagram samen met de bestaande bestanden.

> [!IMPORTANT]
> Azure Dev Spaces gebruikt de dockerfile- en helmdiagram voor uw project om uw code te bouwen en uit te voeren, maar u deze bestanden wijzigen als u de manier waarop het project wordt gebouwd en uitgevoerd wilt wijzigen.

De `prep` opdracht genereert `azds.yaml` ook een bestand aan de basis van uw project. Azure Dev Spaces gebruikt dit bestand voor het bouwen, installeren, configureren en uitvoeren van uw toepassing. In dit configuratiebestand wordt de locatie van uw Dockerfile- en Helm-diagram weergegeven en wordt ook extra configuratie weergegeven bovenop deze artefacten.

Hier is een voorbeeld azds.yaml bestand gemaakt met behulp van [.NET Core voorbeeld toepassing:](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

Het `azds.yaml` bestand dat `prep` door de opdracht wordt gegenereerd, is bedoeld om te werken voor een eenvoudig, enkel projectontwikkelingsscenario. Als uw specifieke project de complexiteit heeft vergroot, moet `prep` u dit bestand mogelijk bijwerken nadat de opdracht is uitgevoerd. Uw project kan bijvoorbeeld een aantal wijzigingen in uw bouw- of startproces vereisen op basis van uw ontwikkelings- of foutopsporingsbehoeften. U ook meerdere toepassingen in uw project hebben, waarvoor meerdere bouwprocessen of een andere build-inhoud nodig zijn.

## <a name="next-steps"></a>Volgende stappen

Zie Hoe het uitvoeren van uw code [met Azure Dev Spaces werkt][how-it-works-up]voor meer informatie over het uitvoeren van uw code in uw dev-ruimte.

Zie de volgende snelstarts om aan de slag te gaan met Azure Dev Spaces om uw project voor Azure Dev Space voor te bereiden:

* [Snel herhalen en debuggen met Visual Studio Code en Java][quickstart-java]
* [Snel herhalen en debuggen met Visual Studio Code en .NET][quickstart-netcore]
* [Snel herhalen en debuggen met Visual Studio Code en Node.js][quickstart-node]
* [Snel herhalen en debuggen met Visual Studio en .NET Core][quickstart-vs]
* [De CLI gebruiken om een toepassing op Kubernetes te ontwikkelen][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md