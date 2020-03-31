---
title: Een service uitvoeren onder systeem- en lokale beveiligingsaccounts
description: Meer informatie over het uitvoeren van een Service Fabric-toepassing onder systeem- en lokale beveiligingsaccounts.  Maak beveiligingsprincipals en pas het Run-As-beleid toe om uw services veilig uit te voeren.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610115"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Een service uitvoeren als een lokaal gebruikersaccount of lokaal systeemaccount
Door Azure Service Fabric te gebruiken, u toepassingen beveiligen die in het cluster worden uitgevoerd onder verschillende gebruikersaccounts. Standaard worden Service Fabric-toepassingen uitgevoerd onder het account waarop het Fabric.exe-proces wordt uitgevoerd. Service Fabric biedt ook de mogelijkheid om toepassingen uit te voeren onder een lokale gebruiker of systeemaccount. Ondersteunde lokale systeemaccounttypen zijn **LocalUser,** **NetworkService,** **LocalService**en **LocalSystem**.  Als u Service Fabric uitvoert op een windows-zelfstandige cluster, u een service uitvoeren onder [Active Directory-domeinaccounts](service-fabric-run-service-as-ad-user-or-group.md) of [door groepen beheerde serviceaccounts](service-fabric-run-service-as-gmsa.md).

In het toepassingsmanifest definieert u de gebruikersaccounts die nodig zijn om services uit te voeren of resources te beveiligen in de sectie **Principals.** U ook gebruikersgroepen definiëren en maken, zodat een of meer gebruikers samen kunnen worden beheerd. Dit is handig wanneer er meerdere gebruikers zijn voor verschillende serviceinvoerpunten en ze gemeenschappelijke bevoegdheden nodig hebben die beschikbaar zijn op groepsniveau.  De gebruikers worden vervolgens verwezen in een RunAs-beleid, dat wordt toegepast op een specifieke service of alle services in de toepassing. 

Standaard wordt het Runas-beleid toegepast op het hoofdinvoerpunt.  U ook een RunAs-beleid toepassen op het instelpunt voor instellen, als u [bepaalde installatiebewerkingen met hoge bevoegdheden onder een systeemaccount](service-fabric-run-script-at-service-startup.md)of zowel hoofd- als instellingstoegangspunten moet uitvoeren.  

> [!NOTE] 
> Als u een RunAs-beleid toepast op een service en het servicemanifest eindpuntbronnen declareert met het HTTP-protocol, moet u een **SecurityAccessPolicy**opgeven.  Zie [Een beveiligingstoegangsbeleid toewijzen voor HTTP- en HTTPS-eindpunten voor](service-fabric-assign-policy-to-endpoint.md)meer informatie. 
>

## <a name="run-a-service-as-a-local-user"></a>Een service uitvoeren als lokale gebruiker
U een lokale gebruiker maken die kan worden gebruikt om een service binnen de toepassing te beveiligen. Wanneer een **LocalUser-accounttype** is opgegeven in het sectie principals van het toepassingsmanifest, maakt Service Fabric lokale gebruikersaccounts op machines waar de toepassing wordt geïmplementeerd. Standaard hebben deze accounts niet dezelfde namen als die welke zijn opgegeven in het toepassingsmanifest (bijvoorbeeld *Customer3* in het volgende voorbeeld van het toepassingsmanifest). In plaats daarvan worden ze dynamisch gegenereerd en hebben ze willekeurige wachtwoorden.

Geef in de sectie **RunAsPolicy** voor een **ServiceManifestImport**het gebruikersaccount op in de sectie **Principals** om het servicecodepakket uit te voeren.  In het volgende voorbeeld ziet u hoe u een lokale gebruiker maakt en een RunAs-beleid toepast op het belangrijkste toegangspunt:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Een lokale gebruikersgroep maken
U gebruikersgroepen maken en een of meer gebruikers aan de groep toevoegen. Dit is handig als er meerdere gebruikers zijn voor verschillende serviceinvoerpunten en ze bepaalde gemeenschappelijke bevoegdheden moeten hebben die beschikbaar zijn op groepsniveau. In het volgende toepassingsmanifestvoorbeeld wordt een lokale groep met de naam *LocalAdminGroup* weergegeven met beheerdersbevoegdheden. Twee gebruikers, *Customer1* en *Customer2,* zijn lid van deze lokale groep. In de sectie **ServiceManifestImport** wordt een RunAs-beleid toegepast om het *Stateful1Pkg-codepakket* als *Klant 2*uit te voeren.  Een ander RunAs-beleid wordt toegepast om het *Web1Pkg-codepakket* als *Klant 1*uit te voeren.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Een standaardbeleid toepassen op alle servicecodepakketten
U gebruikt de sectie **DefaultRunAsPolicy** om een standaardgebruikersaccount op te geven voor alle codepakketten waarvoor geen specifiek **RunAsPolicy** is gedefinieerd. Als de meeste codepakketten die zijn opgegeven in het servicemanifest dat door een toepassing wordt gebruikt, onder dezelfde gebruiker moeten worden uitgevoerd, kan de toepassing gewoon een standaard RunAs-beleid definiëren met dat gebruikersaccount. In het volgende voorbeeld wordt aangegeven dat als een codepakket geen **RunAsPolicy** heeft opgegeven, het codepakket moet worden uitgevoerd onder de **MyDefaultAccount-gebruiker** die is opgegeven in de sectie Principals.  Ondersteunde accounttypen zijn LocalUser, NetworkService, LocalSystem en LocalService.  Als u een lokale gebruiker of service gebruikt, geeft u ook de accountnaam en het wachtwoord op.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Een codepakket lokaal debuggen met behulp van consoleomleiding
Af en toe is het handig voor foutopsporingsdoeleinden om de console-uitvoer van een lopende service te zien. U een omleidingsbeleid voor de console instellen op het invoerpunt in het servicemanifest, waarmee de uitvoer naar een bestand wordt geschreven. De bestandsuitvoer wordt geschreven naar de toepassingsmap die **u aanroept** op het clusterknooppunt waar de toepassing is geïmplementeerd en uitgevoerd. 

> [!WARNING]
> Gebruik nooit het omleidingsbeleid voor de console in een toepassing die in productie wordt geïmplementeerd, omdat dit van invloed kan zijn op de failover van de toepassing. *Gebruik* dit alleen voor lokale ontwikkelings- en debuggingdoeleinden.  
> 
> 

In het volgende voorbeeld van het servicemanifest ziet u de omleiding van de console inschakelen met een fileretentioncount-waarde:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Volgende stappen
* [Het toepassingsmodel begrijpen](service-fabric-application-model.md)
* [Resources opgeven in een servicemanifest](service-fabric-service-manifest-resources.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
