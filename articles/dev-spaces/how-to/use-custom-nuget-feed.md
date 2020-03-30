---
title: Een aangepaste NuGet-feed gebruiken
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Gebruik een aangepaste NuGet-feed om NuGet-pakketten te openen en te gebruiken in een Azure Dev Space.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325732"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Een aangepaste NuGet-feed gebruiken met Azure Dev Spaces

Een NuGet-feed biedt een handige manier om pakketbronnen in een project op te nemen. Azure Dev Spaces moet toegang krijgen tot deze feed om afhankelijkheden goed te kunnen installeren in de Docker-container.

## <a name="set-up-a-nuget-feed"></a>Een NuGet-feed instellen

Voeg een [pakketverwijzing](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) toe voor `*.csproj` uw `PackageReference` afhankelijkheid in het bestand onder het knooppunt. Bijvoorbeeld:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Maak een [NuGet.Config-bestand](https://docs.microsoft.com/nuget/reference/nuget-config-file) in de `packageSources` `packageSourceCredentials` projectmap en stel de secties in voor je NuGet-feed. De `packageSources` sectie bevat uw feed url, die toegankelijk moet zijn vanuit uw AKS-cluster. Dit `packageSourceCredentials` zijn de referenties voor toegang tot de feed. Bijvoorbeeld:

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

Werk uw Dockerfiles `NuGet.Config` bij om het bestand naar de afbeelding te kopiëren. Bijvoorbeeld:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Op `NuGet.Config`Windows, `Nuget.Config`, `nuget.config` en alle werkt als geldige bestandsnamen. Op Linux `NuGet.Config` is alleen een geldige bestandsnaam voor dit bestand. Aangezien Azure Dev Spaces Docker en Linux `NuGet.Config`gebruikt, moet dit bestand de naam . U de naamgeving handmatig `dotnet restore --configfile nuget.config`herstellen of door het uitvoeren van.


Als u Git gebruikt, moet u niet de referenties voor uw NuGet-feed in versiebeheer hebben. Voeg `NuGet.Config` toe `.gitignore` aan het project `NuGet.Config` voor uw project, zodat het bestand niet wordt toegevoegd aan versiebeheer. Azure Dev Spaces heeft dit bestand nodig tijdens het containerimage-opbouwproces, `.gitignore` maar `.dockerignore` voldoet standaard aan de regels die zijn gedefinieerd in en tijdens synchronisatie. Als u de standaardinstelling wilt wijzigen `NuGet.Config` en Azure `azds.yaml` Dev Spaces het bestand wilt synchroniseren, werkt u het bestand bij:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Als u Git niet gebruikt, u deze stap overslaan.

De volgende keer `azds up` dat `F5` u wordt uitgevoerd of opdezedagen in `NuGet.Config` Visual Studio Code of Visual Studio, synchroniseert Azure Dev Spaces het bestandsgebruik om pakketafhankelijkheden te installeren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [NuGet en hoe het werkt.](https://docs.microsoft.com/nuget/what-is-nuget)