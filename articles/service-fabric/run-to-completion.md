---
title: RunToCompletion-semantiek in servicestructuur
description: Beschrijft RunToCompletion semantiek in Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431291"
---
# <a name="runtocompletion"></a>RunToCompletion

Service Fabric begint met versie 7.1 en ondersteunt **RunToCompletion-semantiek** voor containers en [uitvoerbare][guest-executables-introduction-link] toepassingen voor [gasten.][containers-introduction-link] Deze semantiek maakt toepassingen en services mogelijk die een taak voltooien en afsluiten, in tegenstelling tot, altijd toepassingen en services uitvoeren.

Voordat we verder gaan met dit artikel, raden we aan om vertrouwd te raken met het [Service Fabric-toepassingsmodel][application-model-link] en het [Service Fabric-hostingmodel.][hosting-model-link]

> [!NOTE]
> RunToCompletion semantiek worden momenteel niet ondersteund voor services die zijn geschreven met behulp van het [programmeermodel Betrouwbare services.][reliable-services-link]
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion semantiek en -specificatie
RunToCompletion semantiek kan worden opgegeven als **uitvoeringsbeleid** bij [het importeren van het ServiceManifest][application-and-service-manifests-link]. Het opgegeven beleid wordt overgenomen door alle CodePackages die het ServiceManifest omvatten. Het volgende fragment ApplicationManifest.xml geeft een voorbeeld.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** staat de volgende twee kenmerken toe:
* **Type:** **RunToCompletion** is momenteel de enige toegestane waarde voor dit kenmerk.
* **Opnieuw opstarten:** Met dit kenmerk geeft u het herstartbeleid op dat wordt toegepast op CodePackages, bestaande uit het ServicePackage, op fout. Een CodePackage die afsluit met een **niet-nulexitcode** wordt beschouwd als mislukt. Toegestane waarden voor dit kenmerk zijn **OnFailure** en **Nooit** met **OnFailure** als standaard.

Met het herstartbeleid ingesteld **op OnFailure**, als een CodePackage mislukt **(niet-nul exit code)**, wordt het opnieuw gestart, met back-offs tussen herhaalde fouten. Als het herstartbeleid is ingesteld op **Nooit**, als een CodePackage mislukt, wordt de implementatiestatus van het Geïmplementeerdservicepakket gemarkeerd als **Mislukt,** maar mogen andere CodePackages doorgaan met de uitvoering. Als alle CodePackages die de ServicePackage uitvoeren tot een succesvolle voltooiing **(exitcode 0)** worden uitgevoerd, wordt de implementatiestatus van het Geïmplementeerdservicepakket gemarkeerd als **RanToCompletion**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Voorbeeld voltooien met RunToCompletion-semantiek

Laten we eens kijken naar een compleet voorbeeld met behulp van RunToCompletion semantiek.

> [!IMPORTANT]
> In het volgende voorbeeld wordt ervan uitgegaan dat u vertrouwd bent met het maken van [Windows-containertoepassingen met Service Fabric en Docker.][containers-getting-started-link]
>
> In dit voorbeeld wordt verwezen naar mcr.microsoft.com/windows/nanoserver:1809. Windows Server-containers zijn niet compatibel voor alle versies van een hostbesturingssysteem. Zie [Compatibiliteit van windows containerversies voor](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)meer informatie.

Het volgende ServiceManifest.xml beschrijft een ServicePackage bestaande uit twee CodePackages, die containers vertegenwoordigen. *RunToCompletionCodePackage1* registreert alleen een bericht naar **stdout** en exits. *RunToCompletionCodePackage2* pingt het loopback-adres een tijdje en sluit vervolgens af met een exitcode van **0,** **1** of **2**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

De volgende ApplicationManifest.xml beschrijft een toepassing op basis van de hierboven besproken ServiceManifest.xml. Hiermee wordt **runtocompletion-uitvoeringsbeleid** **ExecutionPolicy** voor *WindowsRunToCompletionServicePackage* opgegeven met een herstartbeleid van **OnFailure**. Bij activering van *WindowsRunToCompletionServicePackage*wordt de samenstellende CodePackages gestart. *RunToCompletionCodePackage1* moet bij de eerste activering met succes worden afgesloten. *RunToCompletionCodePackage2* kan echter mislukken **(niet-nul exitcode)**, in welk geval het opnieuw wordt gestart omdat het herstartbeleid **OnFailure**is.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Implementatiestatus van een geïmplementeerdServicepakket opvragen
Implementatiestatus van een GeïmplementeerdServicepakket kan worden opgevraagd vanuit PowerShell met [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] of van C# met [fabricclient-API][fabric-client-link] [GetDeployedServicePackageListAsync(String, Uri, String)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Overwegingen bij het gebruik van RunToCompletion semantiek

De volgende punten moeten worden opgemerkt voor de huidige RunToCompletion-ondersteuning.
* Deze semantiek wordt alleen ondersteund voor [containers][containers-introduction-link] en [gastuitvoerbare][guest-executables-introduction-link] toepassingen.
* Upgradescenario's voor toepassingen met RunToCompletion-semantiek zijn niet toegestaan. Gebruikers moeten dergelijke toepassingen verwijderen en opnieuw maken, indien nodig.
* Failovergebeurtenissen kunnen ervoor zorgen dat CodePackages opnieuw wordt uitgevoerd na succesvolle voltooiing, op hetzelfde knooppunt of op andere knooppunten van het cluster. Voorbeelden van failovergebeurtenissen zijn: knooppunt opnieuw wordt opgestart en servicefabric runtime-upgrades op een knooppunt.

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync

