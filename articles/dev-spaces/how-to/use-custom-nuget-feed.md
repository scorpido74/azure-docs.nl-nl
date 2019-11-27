---
title: Een aangepaste NuGet-feed gebruiken
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Gebruik een aangepaste NuGet feed te openen en gebruiken van NuGet-pakketten in een Azure Dev Space.
keywords: Docker, Kubernetes, azure, AKS, Azure Container Service, containers
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325732"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Een aangepaste NuGet-feed gebruiken met Azure dev Spaces

Een NuGet-feed biedt een handige manier om pakket bronnen in een project op te neemt. Azure dev Spaces moeten toegang hebben tot deze feed om afhankelijkheden goed te kunnen installeren in de docker-container.

## <a name="set-up-a-nuget-feed"></a>Een NuGet-feed instellen

Voeg een [pakket verwijzing](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) toe voor uw afhankelijkheid in het `*.csproj` bestand onder het knoop punt `PackageReference`. Bijvoorbeeld:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Maak een [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) -bestand in de projectmap en stel de secties `packageSources` en `packageSourceCredentials` in voor uw NuGet-feed. De sectie `packageSources` bevat de feed-URL, die toegankelijk moet zijn vanuit uw AKS-cluster. De `packageSourceCredentials` zijn de referenties voor toegang tot de feed. Bijvoorbeeld:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Werk uw Dockerfiles bij om het `NuGet.Config` bestand naar de installatie kopie te kopiëren. Bijvoorbeeld:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> In Windows zijn `NuGet.Config`, `Nuget.Config`en `nuget.config` alle werken als geldige bestands namen. In Linux is alleen `NuGet.Config` een geldige bestands naam voor dit bestand. Omdat Azure dev Spaces docker en Linux gebruiken, moet dit bestand de naam `NuGet.Config`hebben. U kunt de naam hand matig aanpassen of door `dotnet restore --configfile nuget.config`uit te voeren.


Als u Git gebruikt, moet u de referenties voor uw NuGet-feed niet in versie beheer hebben. `NuGet.Config` toevoegen aan de `.gitignore` voor uw project, zodat het `NuGet.Config` bestand niet wordt toegevoegd aan versie beheer. Voor Azure dev Spaces is dit bestand vereist tijdens het bouwen van de container installatie kopie, maar standaard zijn de regels die zijn gedefinieerd in `.gitignore` en `.dockerignore` tijdens de synchronisatie. Als u de standaard instelling wilt wijzigen en Azure-ontwikkel ruimten wilt toestaan om het `NuGet.Config` bestand te synchroniseren, werkt u het `azds.yaml` bestand bij:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Als u geen gebruik maakt van Git, kunt u deze stap overs Laan.

De volgende keer dat u `azds up` uitvoert of `F5` klikt in Visual Studio code of Visual Studio, worden met Azure dev Spaces het `NuGet.Config` bestand gesynchroniseerd om pakket afhankelijkheden te installeren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [NuGet en hoe deze werkt](https://docs.microsoft.com/nuget/what-is-nuget).