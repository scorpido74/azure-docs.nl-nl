---
title: Hoe u een project voorbereidt voor Azure dev Spaces werkt
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Hierin wordt beschreven hoe u uw project voorbereidt met Azure dev Spaces werkt
keywords: azds. yaml, Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241632"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Hoe u een project voorbereidt voor Azure dev Spaces werkt

Met Azure dev Spaces kunt u op verschillende manieren snel en probleem oplossing voor Kubernetes-toepassingen en samen werken met uw team op een AKS-cluster (Azure Kubernetes service). Ontwikkel ruimten kunnen Dockerfiles-en helm-grafieken voor uw project genereren. Dev Spaces maakt en gebruikt ook een configuratie bestand voor het implementeren, uitvoeren en opsporen van fouten in uw Kubernetes-toepassingen in AKS. Al deze bestanden bevinden zich in de code van uw toepassing en kunnen worden toegevoegd aan uw versie beheersysteem.

In dit artikel wordt beschreven wat u kunt doen om uw project voor te bereiden op het uitvoeren van AKS met dev Spaces.

## <a name="prepare-your-code"></a>Uw code voorbereiden

Als u uw toepassing wilt uitvoeren in een dev-ruimte, moet deze worden containerd en moet u definiëren hoe deze moet worden geïmplementeerd in Kubernetes. Als u uw toepassing wilt container plaatsen, hebt u een Dockerfile nodig. Als u wilt definiëren hoe uw toepassing wordt geïmplementeerd naar Kubernetes, hebt u een [helm-grafiek](https://docs.helm.sh/)nodig. Om u te helpen bij het maken van de Dockerfile-en helm-grafiek voor uw toepassing, biedt de `prep` client-side tools de volgende opdracht:

```cmd
azds prep --enable-ingress
```

De `prep` opdracht gaat naar de bestanden in uw project en probeert de Dockerfile-en helm-grafiek te maken voor het uitvoeren van uw toepassing in Kubernetes. Op dit moment `prep` wordt met de opdracht een Dockerfile-en helm-grafiek gegenereerd met de volgende talen:

* Java
* Node.js
* .NET Core

U *moet* de opdracht `prep` uitvoeren vanuit een map met de bron code. Door de `prep` opdracht uit te voeren vanuit de juiste map, kan het programma aan de client zijde de taal identificeren en een geschikte Dockerfile maken om uw toepassing te container plaatsen. U kunt de `prep` opdracht ook uitvoeren vanuit een map die een *pom. XML-* bestand voor Java-projecten bevat.

Als u de `prep` opdracht uitvoert vanuit een map die geen bron code bevat, wordt door het hulp programma aan de client zijde geen Dockerfile gegenereerd. Er wordt ook een fout weer gegeven met de melding: *Dockerfile kan niet worden gegenereerd als gevolg*van een niet-ondersteunde taal. Deze fout treedt ook op als het project type niet wordt herkend door het programma aan de client zijde.

Wanneer u de `prep` opdracht uitvoert, hebt u de optie om de `--enable-ingress` vlag op te geven. Met deze markering wordt aan de controller aangegeven dat er een eind punt voor Internet toegang moet worden gemaakt voor deze service. Als u deze vlag niet opgeeft, is de service alleen toegankelijk vanuit het cluster of met behulp van de localhost-tunnel die is gemaakt door het programma aan de client zijde. U kunt dit gedrag in-of uitschakelen nadat de `prep` opdracht is uitgevoerd door de gegenereerde helm-grafiek bij te werken.

Met `prep` de opdracht worden geen bestaande Dockerfiles-of helm-grafieken vervangen in uw project. Als een bestaande Dockerfile-of helm-grafiek dezelfde naam conventie gebruikt als de bestanden die door `prep` de opdracht worden `prep` gegenereerd, slaat de opdracht deze bestanden over. Anders wordt met `prep` de opdracht een eigen Dockerfile-of helm-grafiek gegenereerd naast de bestaande bestanden.

> [!IMPORTANT]
> Azure dev Spaces maakt gebruik van de Dockerfile-en helm-grafiek voor uw project om uw code te bouwen en uit te voeren, maar u kunt deze bestanden aanpassen als u wilt wijzigen hoe het project wordt gemaakt en uitgevoerd.

Met `prep` deze opdracht wordt ook een `azds.yaml` bestand gegenereerd in de hoofdmap van uw project. Azure dev Spaces gebruiken dit bestand om uw toepassing te bouwen, te installeren, te configureren en uit te voeren. Dit configuratie bestand bevat een lijst met de locatie van uw Dockerfile-en helm-grafiek en biedt ook aanvullende configuratie boven deze artefacten.

Hier volgt een voor beeld van een azds. yaml-bestand dat is gemaakt met behulp van [.net core-voorbeeld toepassing](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

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

Het `azds.yaml` bestand dat door de `prep` opdracht wordt gegenereerd, is bedoeld om te werken met een eenvoudig scenario voor het ontwikkelen van één project. Als uw specifieke project meer complexiteit heeft, moet u dit bestand mogelijk bijwerken nadat u de `prep` opdracht hebt uitgevoerd. Het is bijvoorbeeld mogelijk dat uw project enkele wijzigingen in uw bouw-of start proces vereist op basis van uw ontwikkelings-of fout opsporings behoeften. Het is ook mogelijk dat er meerdere toepassingen in uw project zijn, waarvoor meerdere bouw processen of een andere build-inhoud zijn vereist.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het uitvoeren van uw code in uw ontwikkelings ruimte raadpleegt u [hoe u uw code uitvoert met Azure dev Spaces][how-it-works-up].

Raadpleeg de volgende Quick starts om aan de slag te gaan met Azure dev Spaces om uw project voor te bereiden voor Azure-ontwikkel ruimte:

* [Snel herhalen en fouten opsporen met Visual Studio code en Java][quickstart-java]
* [Snel herhalen en fouten opsporen met Visual Studio code en .NET][quickstart-netcore]
* [Snel herhalen en fouten opsporen met Visual Studio code en node. js][quickstart-node]
* [Snel herhalen en fouten opsporen met Visual Studio en .NET core][quickstart-vs]
* [De CLI gebruiken om een toepassing te ontwikkelen op Kubernetes][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md