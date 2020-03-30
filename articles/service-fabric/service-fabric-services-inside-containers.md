---
title: Uw Azure Service Fabric-services containeriseren op Windows
description: Meer informatie over het containeriseren van uw Service Fabric Reliable Services en Reliable Actors-services op Windows.
ms.topic: conceptual
ms.date: 5/23/2018
ms.author: anmola
ms.openlocfilehash: 9fe5980c13f655f8f30cc42771971a5015460420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466181"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Uw Reliable Services en Reliable Actors van Service Fabric in Windows in een container plaatsen

Service Fabric ondersteunt containerizing Service Fabric microservices (Reliable Services, and Reliable Actor based services). Zie voor meer informatie [de containers van de servicestof](service-fabric-containers-overview.md).

Dit document biedt richtlijnen om uw service in een Windows-container te laten werken.

> [!NOTE]
> Momenteel werkt deze functie alleen voor Windows. Als u containers wilt uitvoeren, moet het cluster worden uitgevoerd op Windows Server 2016 met containers.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Stappen om uw Service Fabric-toepassing te containeriseren

1. Open uw Service Fabric-toepassing in Visual Studio.

2. Voeg klasse [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) toe aan uw project. De code in deze klasse is een helper om de runtimebinaries van Service Fabric in uw toepassing correct te laden wanneer u in een container wordt uitgevoerd.

3. Voor elk codepakket dat u wilt containeriseren, initialiseren van de lader op het programma entry point. Voeg de statische constructor in het volgende codefragment toe aan het itembestand van het programma.

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. Bouw en [verpak](service-fabric-package-apps.md#Package-App) uw project. Als u een pakket wilt maken en maken, klikt u met de rechtermuisknop op het toepassingsproject in Solution Explorer en kiest u de opdracht **Pakket.**

5. Voer voor elk codepakket dat u moet containeriseren het PowerShell-script [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1)uit. Het gebruik is als volgt:

    Volledig .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      Het script maakt een map met Docker-artefacten op $dockerPackageOutputDirectoryPath. Wijzig het gegenereerde `expose` Dockerfile in alle poorten, voer installatiescripts uit en ga zo maar door. gebaseerd op uw behoeften.

6. Vervolgens moet je je Docker containerpakket [bouwen](service-fabric-get-started-containers.md#Build-Containers) en naar je repository [duwen.](service-fabric-get-started-containers.md#Push-Containers)

7. Wijzig de ApplicationManifest.xml en ServiceManifest.xml om uw containerafbeelding, repository-informatie, registerverificatie en port-to-host-toewijzing toe te voegen. Zie [Een containertoepassing voor Azure Service Fabric maken](service-fabric-get-started-containers.md)voor het wijzigen van de manifesten. De definitie van het codepakket in het servicemanifest moet worden vervangen door de bijbehorende containerafbeelding. Zorg ervoor dat u het EntryPoint wijzigt in een ContainerHost-type.

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. Voeg de port-to-host-toewijzing toe voor uw replicator- en serviceeindpunt. Aangezien beide poorten tijdens runtime door Service Fabric zijn toegewezen, is de ContainerPort ingesteld op nul om de toegewezen poort te gebruiken voor toewijzing.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Zie [Isolatiemodus configureren]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode)voor het configureren van de containerisolatiemodus . Windows ondersteunt twee isolatiemodi voor containers: proces en Hyper-V. In de volgende fragmenten ziet u hoe de isolatiemodus is opgegeven in het manifestbestand van de toepassing.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

> [!NOTE] 
> Standaard hebben Service Fabric-toepassingen toegang tot de runtime van Service Fabric, in de vorm van een eindpunt dat toepassingsspecifieke aanvragen accepteert. Overweeg deze toegang uit te schakelen wanneer de toepassing niet-vertrouwde code host. Zie aanbevolen procedures [voor beveiliging in Service Fabric](service-fabric-best-practices-security.md#platform-isolation)voor meer informatie. Als u de toegang tot de runtime van de ServiceFabric wilt uitschakelen, voegt u de volgende instelling toe in het gedeelte Beleid van het toepassingsmanifest dat overeenkomt met het geïmporteerde servicemanifest als volgt:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Als u deze toepassing wilt testen, moet u deze implementeren in een cluster waarop versie 5.7 of hoger wordt uitgevoerd. Voor runtime-versies 6.1 of lager moet u de clusterinstellingen bewerken en bijwerken om deze voorbeeldfunctie in te schakelen. Volg de stappen in dit [artikel](service-fabric-cluster-fabric-settings.md) om de volgende instelling toe te voegen.
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. Implementeer [vervolgens](service-fabric-deploy-remove-applications.md) het bewerkte toepassingspakket in dit cluster.

U moet nu een containerized Service Fabric-toepassing hebben waarop uw cluster wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het uitvoeren van [containers in Service Fabric](service-fabric-get-started-containers.md).
* Meer informatie over de [levenscyclus](service-fabric-application-lifecycle.md) van de Service Fabric-toepassing.
