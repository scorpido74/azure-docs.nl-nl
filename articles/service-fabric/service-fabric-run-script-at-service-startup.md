---
title: Een script uitvoeren wanneer een Azure Service Fabric-service wordt gestart
description: Meer informatie over het configureren van een beleid voor een ingangspunt voor het instellen van een Service Fabric-serviceservice en het uitvoeren van een script bij het opstarten van de service.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464284"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Het opstartscript van een service uitvoeren als lokale gebruiker of systeemaccount
Voordat een servicefabricservice uitvoerbaar wordt opgestart, kan het nodig zijn om bepaalde configuratie- of installatiewerkzaamheden uit te voeren.  Bijvoorbeeld het configureren van omgevingsvariabelen. U een script opgeven dat moet worden uitgevoerd voordat de uitvoerbare service wordt opgestart in het servicemanifest voor de service. Door een RunAs-beleid voor het ingangspunt voor service-instellingen te configureren, u wijzigen onder welk account de uitvoerbare installatie wordt uitgevoerd.  Met een apart instelpunt voor het instellen u een configuratie met hoge bevoegdheden voor een korte periode uitvoeren, zodat de uitvoerbare servicehost niet gedurende langere tijd met hoge bevoegdheden hoeft te worden uitgevoerd.

Het instelpunt **(SetupEntryPoint** in het [servicemanifest)](service-fabric-application-and-service-manifests.md)is een geprivilegieerd toegangspunt dat standaard wordt uitgevoerd met dezelfde referenties als Service Fabric (meestal het *NetworkService-account)* vóór een ander toegangspunt. De uitvoerbare die door **EntryPoint** is opgegeven, is meestal de langlopende servicehost. De **uitvoerbare EntryPoint** wordt uitgevoerd nadat de **uitvoerbare setupEntryPoint** is afgesloten. Het resulterende proces wordt gecontroleerd en opnieuw gestart en begint opnieuw met **SetupEntryPoint** als het ooit wordt beëindigd of vastloopt. 

## <a name="configure-the-service-setup-entry-point"></a>Het toegangspunt voor service-instellingen configureren
Het volgende is een eenvoudig servicemanifestvoorbeeld voor een statusloze service die een setupscript *MySetup.bat* opgeeft in de service **SetupEntryPoint**.  **Argumenten** worden gebruikt om argumenten door te geven aan het script wanneer het wordt uitgevoerd.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Het beleid configureren voor een toegangspunt voor het instellen van een serviceinstelling
Standaard wordt het uitvoerpunt voor service-instellingen uitgevoerd onder dezelfde referenties als Service Fabric (meestal het *NetworkService-account).*  In het toepassingsmanifest u de beveiligingsmachtigingen wijzigen om het opstartscript uit te voeren onder een lokaal systeemaccount of een beheerdersaccount.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Het beleid configureren met behulp van een lokaal systeemaccount
In het volgende toepassingsmanifestvoorbeeld ziet u hoe u het ingangspunt voor service-instellingen configureert dat wordt uitgevoerd onder het gebruikersbeheerdersaccount (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

Maak eerst een sectie **Principals** met een gebruikersnaam, zoals SetupAdminUser. Het gebruikersaccount SetupAdminUser is lid van de systeemgroep Administrators.

Configureer vervolgens onder de sectie **ServiceManifestImport** een beleid om deze principal toe te passen op **SetupEntryPoint**. Dit beleid vertelt Service Fabric dat wanneer het **Bestand MySetup.bat** wordt uitgevoerd, het moet worden uitgevoerd als SetupAdminUser (met beheerdersbevoegdheden). Aangezien u *geen* beleid hebt toegepast op het hoofdinvoerpunt, wordt de code in **MyServiceHost.exe** uitgevoerd onder het **systeemNetworkService-account.** Dit is het standaardaccount dat alle serviceinvoerpunten worden uitgevoerd als.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Het beleid configureren met behulp van lokale systeemaccounts
Vaak is het beter om het opstartscript uit te voeren met een lokaal systeemaccount in plaats van een beheerdersaccount. Het uitvoeren van het runas-beleid als lid van de groep Administrators werkt doorgaans niet goed omdat computers standaard gebruikerstoegangsbeheer (UAC) hebben ingeschakeld. In dergelijke gevallen is de aanbeveling om setupEntryPoint uit te voeren als LocalSystem, in plaats van als een lokale gebruiker die is toegevoegd aan de groep Administrators. In het volgende voorbeeld wordt de instelling van setupEntryPoint ingesteld als LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> Voor Linux-clusters, als u een service of het instellingsinvoerpunt als **hoofdwilt**uitvoeren, u het **AccountType** opgeven als **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Een script uitvoeren vanaf het ingangspunt voor instellen
Voeg nu een opstartscript toe aan het project dat moet worden uitgevoerd onder beheerdersbevoegdheden. 

Klik in Visual Studio met de rechtermuisknop op het serviceproject en voeg een nieuw bestand toe met de naam *MySetup.bat*.

Zorg er vervolgens voor dat het *bestand MySetup.bat* is opgenomen in het servicepakket. Standaard is dat niet het zo. Selecteer het bestand, klik met de rechtermuisknop om het contextmenu op te halen en kies **Eigenschappen**. Controleer in het dialoogvenster Eigenschappen of **Kopiëren naar uitvoermap** is ingesteld op **Kopiëren als nieuwer**. Zie de volgende schermafbeelding.

![Visual Studio CopyToOutput voor setupEntryPoint-batchbestand][image1]

Bewerk nu het *bestand MySetup.bat* en voeg de volgende opdrachten toe, stel een variabele systeemomgeving in en voer een tekstbestand uit:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Bouw en implementeer de oplossing vervolgens in een lokaal ontwikkelingscluster. Nadat de service is gestart, zoals weergegeven in [Service Fabric Explorer,](service-fabric-visualizing-your-cluster.md)u zien dat het bestand MySetup.bat op twee manieren is geslaagd. Open een PowerShell-opdrachtprompt en -typ:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Let vervolgens op de naam van het knooppunt waar de service is geïmplementeerd en gestart in [Service Fabric Explorer.](service-fabric-visualizing-your-cluster.md) Bijvoorbeeld knooppunt 2. Navigeer vervolgens naar de werkmap voor toepassingsinstantie om het out.txt-bestand te vinden met de waarde van **TestVariable.** Als deze service bijvoorbeeld is geïmplementeerd op Knooppunt 2, u naar dit pad gaan voor het **MyApplicationType:**

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>PowerShell-opdrachten uitvoeren vanaf een instelpunt
Als u PowerShell wilt uitvoeren vanaf het **SetupEntryPoint-punt,** u **PowerShell.exe** uitvoeren in een batchbestand dat naar een PowerShell-bestand verwijst. Voeg eerst een PowerShell-bestand toe aan het serviceproject, bijvoorbeeld **MySetup.ps1**. Vergeet niet de eigenschap *Kopiëren als nieuwere* eigenschap in te stellen, zodat het bestand ook in het servicepakket is opgenomen. In het volgende voorbeeld wordt een voorbeeldbatchbestand weergegeven waarmee een PowerShell-bestand met de naam MySetup.ps1 wordt gestart, waarmee een systeemomgevingsvariabele met de naam **TestVariable wordt**ingesteld.

MySetup.bat om een PowerShell-bestand te starten:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

Voeg in het PowerShell-bestand het volgende toe om een systeemomgevingsvariabele in te stellen:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Wanneer het batchbestand wordt uitgevoerd, wordt standaard gekeken naar de toepassingsmap die **werk** voor bestanden wordt genoemd. In dit geval, wanneer MySetup.bat wordt uitgevoerd, willen we dat dit het Bestand MySetup.ps1 in dezelfde map vindt, de map met **toepassingscode.** Als u deze map wilt wijzigen, stelt u de werkmap in:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Een opstartscript lokaal debuggen met behulp van consoleomleiding
Af en toe is het handig voor foutopsporingsdoeleinden om de console-uitvoer te zien van het uitvoeren van een setupscript. U een omleidingsbeleid voor de console instellen op het instelpunt voor de instelling in het servicemanifest, waarmee de uitvoer naar een bestand wordt geschreven. De bestandsuitvoer wordt geschreven naar de toepassingsmap die **u aanroept** op het clusterknooppunt waar de toepassing is geïmplementeerd en uitgevoerd. 

> [!WARNING]
> Gebruik nooit het omleidingsbeleid voor de console in een toepassing die in productie wordt geïmplementeerd, omdat dit van invloed kan zijn op de failover van de toepassing. *Gebruik* dit alleen voor lokale ontwikkelings- en debuggingdoeleinden.  
> 
> 

In het volgende voorbeeld van het servicemanifest ziet u de omleiding van de console in met een fileretentioncount-waarde:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Als u nu het bestand MySetup.ps1 wijzigt om een **Opdracht Echo** te schrijven, wordt hiermee naar het uitvoerbestand geschreven voor foutopsporingsdoeleinden:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Nadat u uw script hebt gedebugd, verwijdert u onmiddellijk dit omleidingsbeleid van deze console.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over applicatie- en servicebeveiliging](service-fabric-application-and-service-security.md)
* [Het toepassingsmodel begrijpen](service-fabric-application-model.md)
* [Resources opgeven in een servicemanifest](service-fabric-service-manifest-resources.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
