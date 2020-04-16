---
title: Initializer CodePackages in Service Fabric
description: Beschrijft Initializer CodePackages in Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430628"
---
# <a name="initializer-codepackages"></a>Initializer CodePackages

Service Fabric begint met versie 7.1 en ondersteunt **Initializer CodePackages** voor [containers][containers-introduction-link] en [gastuitvoerbare][guest-executables-introduction-link] toepassingen. Initializer CodePackages bieden de mogelijkheid om initialisaties uit te voeren op de ServicePackage-scope voordat andere CodePackages met de uitvoering beginnen. Hun relatie met een ServicePackage is analoog aan wat een [SetupEntryPoint][setup-entry-point-link] is voor een CodePackage.

Voordat we verder gaan met dit artikel, raden we aan om vertrouwd te raken met het [Service Fabric-toepassingsmodel][application-model-link] en het [Service Fabric-hostingmodel.][hosting-model-link]

> [!NOTE]
> Initializer CodePackages worden momenteel niet ondersteund voor services die zijn geschreven met behulp van het [programmeermodel Betrouwbare Services.][reliable-services-link]
 
## <a name="semantics"></a>Semantiek

Een Initializer CodePackage wordt verwacht dat deze tot **een succesvolle voltooiing wordt uitgevoerd (exitcode 0).** Een mislukte Initializer CodePackage wordt opnieuw gestart totdat het is voltooid. Meerdere Initializer CodePackages zijn toegestaan en worden uitgevoerd tot **succesvolle voltooiing**, **opeenvolgend**, **in een bepaalde volgorde** voordat andere CodePackages in het ServicePackage beginnen met de uitvoering.

## <a name="specifying-initializer-codepackages"></a>Initializer CodePackages opgeven
U een CodePackage markeren als initialisator door het kenmerk **Initializer** in te stellen **op true** in het ServiceManifest. Wanneer er meerdere Initializer CodePackages zijn, kan de uitvoeringsvolgorde worden opgegeven via het kenmerk **ExecOrder.** **ExecOrder** moet een niet-negatief geheel getal zijn en is alleen geldig voor Initializer CodePackages. Initializer CodePackages met lagere waarden van **ExecOrder** worden als eerste uitgevoerd. Als **ExecOrder** niet is opgegeven voor een Initializer CodePackage, wordt een standaardwaarde van 0 aangenomen. De relatieve uitvoeringsvolgorde van Initializer CodePackages met dezelfde waarde van **ExecOrder** is niet gespecificeerd.

In het volgende fragment ServiceManifest worden drie CodePackages beschreven waarvan er twee zijn gemarkeerd als Initializers. Wanneer dit ServicePackage wordt geactiveerd, wordt *InitCodePackage0* eerst uitgevoerd omdat het de laagste waarde van **ExecOrder**heeft. Bij succesvolle voltooiing (exit code 0) van *InitCodePackage0*wordt *InitCodePackage1* uitgevoerd. Ten slotte wordt *workloadcodepakket* uitgevoerd na een succesvolle voltooiing van *InitCodePackage1.*

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Voorbeeld voltooien met Initializer CodePackages

Laten we eens kijken naar een compleet voorbeeld met Initializer CodePackages.

> [!IMPORTANT]
> In het volgende voorbeeld wordt ervan uitgegaan dat u vertrouwd bent met het maken van [Windows-containertoepassingen met Service Fabric en Docker.][containers-getting-started-link]
>
> In dit voorbeeld wordt verwezen naar mcr.microsoft.com/windows/nanoserver:1809. Windows Server-containers zijn niet compatibel voor alle versies van een hostbesturingssysteem. Zie [Compatibiliteit van windows containerversies voor](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)meer informatie.

Het volgende ServiceManifest.xml bouwt voort op het eerder beschreven artikel ServiceManifest. *InitCodePackage0,* *InitCodePackage1* en *WorkloadCodePackage* zijn CodePackages die containers vertegenwoordigen. Bij activering wordt *InitCodePackage0* als eerste uitgevoerd. Het registreert een bericht naar een bestand en sluit af. Vervolgens wordt *InitCodePackage1* uitgevoerd, waarbij ook een bericht wordt opgeslagen in een bestand en wordt afgesloten. Ten slotte begint het *WorkloadCodePackage* met de uitvoering. Het registreert ook een bericht naar een bestand, deuitvoer van de inhoud van het bestand naar **stdout** en vervolgens pings voor altijd.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

De volgende ApplicationManifest.xml beschrijft een toepassing op basis van de hierboven besproken ServiceManifest.xml. Houd er rekening mee dat het dezelfde **volumehouder** voor alle containers aangeeft, d.w.z. **C:\WorkspaceOnHost** is gemonteerd op **C:\WorkspaceOnContainer** op alle drie de containers. Het netto-effect is dat alle containers naar hetzelfde logboekbestand schrijven in de volgorde waarin ze worden geactiveerd.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
Zodra het ServicePackage is geactiveerd, moet de inhoud van **C:\WorkspaceOnHost\log.txt** de volgende zijn.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor gerelateerde informatie.

* [Service Fabric en containers.][containers-introduction-link]
* [Service Fabric en gast executables.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

