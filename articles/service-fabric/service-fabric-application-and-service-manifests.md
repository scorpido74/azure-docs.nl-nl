---
title: Azure Service Fabric-apps en -services beschrijven
description: Beschrijft hoe manifesten worden gebruikt om Service Fabric-toepassingen en -services te beschrijven.
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: 6014ef6a9b6ec810aafd5e5be96223b8ed92d576
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349969"
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric-applicatie- en servicemanifesten
In dit artikel wordt beschreven hoe Service Fabric-toepassingen en -services worden gedefinieerd en geversioneerd met behulp van de bestanden ApplicationManifest.xml en ServiceManifest.xml.  Zie [voorbeelden van toepassings- en servicemanifesten](service-fabric-manifest-examples.md)voor meer gedetailleerde voorbeelden.  Het XML-schema voor deze manifestbestanden is gedocumenteerd in [de schemadocumentatie van ServiceFabricServiceModel.xsd.](service-fabric-service-model-schema.md)

> [!WARNING]
> Het manifestXML-bestandsschema dwingt de juiste volgorde van onderliggende elementen af.  Open als gedeeltelijke tijdelijke oplossing 'C:\Program Files\Microsoft SDKs\Service Fabric\schema's\ServiceFabricServiceModel.xsd' in Visual Studio tijdens het ontwerpen of wijzigen van een van de manifesten van de Service Fabric. Dit zal u toelaten om het bestellen van kind elementen te controleren en biedt intelli-zin.

## <a name="describe-a-service-in-servicemanifestxml"></a>Een service beschrijven in ServiceManifest.xml
Het servicemanifest definieert declaratief het servicetype en de versie. Het geeft servicemetagegevens op, zoals servicetype, statuseigenschappen, load-balancing metrics, service binaries en configuratiebestanden.  Anders gezegd, het beschrijft de code, configuratie en gegevenspakketten die een servicepakket samenstellen om een of meer servicetypen te ondersteunen. Een servicemanifest kan meerdere code-, configuratie- en gegevenspakketten bevatten, die onafhankelijk van elkaar kunnen worden geversioneerd. Hier is een service manifest voor de ASP.NET Core web front-end service van de [Stemming voorbeeld applicatie](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (en hier zijn enkele meer gedetailleerde [voorbeelden):](service-fabric-manifest-examples.md)

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**Versiekenmerken** zijn ongestructureerde tekenreeksen en worden niet door het systeem ontleed. Versiekenmerken worden gebruikt om elk onderdeel te gebruiken voor upgrades.

**ServiceTypes** geeft in dit manifest aan welke servicetypen door **CodePackages** worden ondersteund. Wanneer een service wordt geinstantieerd ten opzichte van een van deze servicetypen, worden alle codepakketten die in dit manifest zijn aangegeven, geactiveerd door hun toegangspunten uit te voeren. De resulterende processen worden verwacht dat de ondersteunde servicetypen tijdens uitvoering registreren. Servicetypen worden aangegeven op manifestniveau en niet op het codepakketniveau. Dus wanneer er meerdere codepakketten zijn, worden ze allemaal geactiveerd wanneer het systeem op zoek is naar een van de opgegeven servicetypen.

De uitvoerbare die door EntryPoint is **opgegeven,** is meestal de langlopende servicehost. **SetupEntryPoint** is een geprivilegieerd toegangspunt dat wordt uitgevoerd met dezelfde referenties als Service Fabric (meestal het *LocalSystem-account)* vóór een ander toegangspunt.  De aanwezigheid van een apart instelpunt voorkomt dat de servicehost langere tijd moet worden uitgevoerd met hoge bevoegdheden. De uitvoerbare die door **EntryPoint** is opgegeven, wordt uitgevoerd nadat **SetupEntryPoint** is afgesloten. Als het proces ooit wordt beëindigd of vastloopt, wordt het resulterende proces gecontroleerd en opnieuw gestart (opnieuw beginnen met **SetupEntryPoint).**  

Typische scenario's voor het gebruik van **SetupEntryPoint** zijn wanneer u een uitvoerbare uitvoert voordat de service wordt gestart of wanneer u een bewerking uitvoert met verhoogde bevoegdheden. Bijvoorbeeld:

* Het instellen en initialiseren van omgevingsvariabelen die de service uitvoerbaar heeft. Dit is niet beperkt tot alleen executables geschreven via de Service Fabric programmeermodellen. NPM.exe heeft bijvoorbeeld een aantal omgevingsvariabelen nodig die zijn geconfigureerd voor het implementeren van een node.js-toepassing.
* Toegangsbeheer instellen door beveiligingscertificaten te installeren.

Zie Het [beleid configureren voor een ingangspunt voor het instellen van een serviceinstelling](service-fabric-application-runas-security.md) voor meer informatie over het configureren van het SetupEntryPoint.

**Omgevingsvariabelen** (niet ingesteld in het voorgaande voorbeeld) bevat een lijst met omgevingsvariabelen die zijn ingesteld voor dit codepakket. Omgevingsvariabelen kunnen worden overschreven `ApplicationManifest.xml` in de om verschillende waarden voor verschillende service-instanties te bieden. 

**DataPackage** (niet ingesteld in het voorgaande voorbeeld) declareert een map, vernoemd naar het kenmerk **Naam,** die willekeurige statische gegevens bevat die tijdens uitvoering door het proces moeten worden verbruikt.

**ConfigPackage** declareert een map, genoemd naar het kenmerk **Naam,** met een *bestand Settings.xml.* Het instellingenbestand bevat secties met door de gebruiker gedefinieerde sleutelsleutelpaarinstellingen die het proces tijdens de looptijd terugleest. Tijdens een upgrade, als alleen de **ConfigPackage-versie** **version** is gewijzigd, wordt het lopende proces niet opnieuw gestart. In plaats daarvan waarschuwt een callback het proces dat de configuratie-instellingen zijn gewijzigd, zodat ze dynamisch kunnen worden herladen. Hier is een voorbeeld van Het bestand *Instellingen.xml:*

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Een Service Fabric Service **Endpoint** is een voorbeeld van een Service Fabric Resource. Een Service Fabric Resource kan worden gedeclareerd/gewijzigd zonder de gecompileerde code te wijzigen. Toegang tot de servicefabricbronnen die zijn opgegeven in het servicemanifest, kan worden beheerd via de **SecurityGroup** in het toepassingsmanifest. Wanneer een Endpoint Resource is gedefinieerd in het servicemanifest, wijst Service Fabric poorten toe uit het gereserveerde toepassingspoortbereik wanneer een poort niet expliciet is opgegeven. Lees meer over [het opgeven of overschrijven van eindpuntbronnen](service-fabric-service-manifest-resources.md).

 
> [!WARNING]
> Statische poorten mogen volgens het ontwerp niet overlappen met het bereik van de toepassingspoort dat is opgegeven in het clustermanifest. Als u een statische poort opgeeft, wijst u deze toe buiten het bereik van de toepassingspoort, anders leidt dit tot poortconflicten. Met release 6.5CU2 geven we een **gezondheidswaarschuwing** wanneer we een dergelijk conflict detecteren, maar laten we de implementatie synchroon laten doorgaan met het verzonden 6.5-gedrag. We kunnen echter voorkomen dat de implementatie van de toepassing wordt verwijderd van de volgende grote releases.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Een toepassing beschrijven in ApplicationManifest.xml
Het toepassingsmanifest beschrijft declaratief het toepassingstype en de versie. Het specificeert metagegevens voor servicesamenstelling, zoals stabiele namen, partitieschema, instantietelling/replicatiefactor, beveiligings-/isolatiebeleid, plaatsingsbeperkingen, configuratieoverschrijvingen en samenstellende servicetypen. Ook de load-balancing domeinen waarin de applicatie is geplaatst worden beschreven.

Een toepassingsmanifest beschrijft dus elementen op toepassingsniveau en verwijst naar een of meer servicemanifesten om een toepassingstype samen te stellen. Hier is de aanvraag manifest voor de [stemming steekproef aanvraag](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (en hier zijn enkele meer gedetailleerde [voorbeelden):](service-fabric-manifest-examples.md)

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Net als servicemanifesten zijn **versiekenmerken** ongestructureerde tekenreeksen en worden ze niet ontleed door het systeem. Versiekenmerken worden ook gebruikt om elk onderdeel te gebruiken voor upgrades.

**Parameters** definieert de parameters die in het hele toepassingsmanifest worden gebruikt. De waarden van deze parameters kunnen worden opgegeven wanneer de toepassing is geinstantieerd en kan overschrijven toepassing of service configuratie-instellingen.  De standaardparameterwaarde wordt gebruikt als de waarde niet wordt gewijzigd tijdens de instantiatie van toepassingen. Zie [Toepassingsparameters voor meerdere omgevingen beheren voor](service-fabric-manage-multiple-environment-app-configuration.md)meer informatie over het onderhouden van verschillende toepassings- en serviceparameters voor afzonderlijke omgevingen.

**ServiceManifestImport** bevat verwijzingen naar servicemanifesten die dit toepassingstype vormen. Een toepassingsmanifest kan meerdere servicemanifesten bevatten, elk kan onafhankelijk van elkaar worden geversioneerd. Geïmporteerde servicemanifesten bepalen welke servicetypen geldig zijn binnen dit toepassingstype. Binnen de ServiceManifestImport overschrijf u configuratiewaarden in Instellingen.xml en omgevingsvariabelen in ServiceManifest.xml-bestanden. **Beleid** (niet ingesteld in het voorgaande voorbeeld) voor end-point binding, beveiliging en toegang, en het delen van pakketten kan worden ingesteld op geïmporteerde servicemanifesten.  Zie [Beveiligingsbeleid configureren voor uw toepassing voor](service-fabric-application-runas-security.md)meer informatie.

**DefaultServices** declareert service-exemplaren die automatisch worden gemaakt wanneer een toepassing wordt geinstantieerd tegen dit toepassingstype. Standaardservices zijn slechts een gemak en gedragen zich in elk opzicht als normale services nadat ze zijn gemaakt. Ze worden bijgewerkt samen met alle andere services in de toepassingsinstantie en kunnen ook worden verwijderd. Een toepassingsmanifest kan meerdere standaardservices bevatten.

**Certificaten** (niet ingesteld in het voorgaande voorbeeld) declareert de certificaten die worden gebruikt om [HTTPS-eindpunten in](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) te stellen of [geheimen te versleutelen in het toepassingsmanifest](service-fabric-application-secret-management.md).

**Plaatsingsbeperkingen** zijn de instructies die bepalen waar services moeten worden uitgevoerd. Deze instructies zijn gekoppeld aan afzonderlijke services die u selecteert voor een of meer knooppunteigenschappen. Zie [Plaatsingsbeperkingen en syntaxis van de eigenschap knooppunt voor](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#placement-constraints-and-node-property-syntax) meer informatie

**Beleidsregels** (niet ingesteld in het voorgaande voorbeeld) beschrijft de runtime van de logboekverzameling, [standaardrun-as-](service-fabric-application-runas-security.md) [als-status](service-fabric-health-introduction.md#health-policies)en [beveiligingstoegang](service-fabric-application-runas-security.md) die op toepassingsniveau moet worden ingesteld, inclusief of de service(s) toegang hebben tot de runtime van de ServiceFabric.

> [!NOTE] 
> Standaard hebben Service Fabric-toepassingen toegang tot de runtime van Service Fabric, in de vorm van een eindpunt dat toepassingsspecifieke aanvragen accepteert, en omgevingsvariabelen die verwijzen naar bestandspaden op de host die Fabric- en toepassingsspecifieke bestanden bevatten . Overweeg deze toegang uit te schakelen wanneer de toepassing niet-vertrouwde code host (d.w.z. code waarvan de herkomst onbekend is of waarvan de eigenaar van de toepassing weet dat deze niet veilig is om uit te voeren). Zie aanbevolen procedures [voor beveiliging in Service Fabric](service-fabric-best-practices-security.md#platform-isolation)voor meer informatie. 
>

**Principals** (niet in het voorgaande voorbeeld) beschrijven de beveiligingsprincipals (gebruikers of groepen) die nodig zijn om services uit te [voeren en serviceresources te beveiligen.](service-fabric-application-runas-security.md)  Opdrachtgevers worden verwezen in de secties **Beleid.**





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Volgende stappen
- [Verpak een toepassing](service-fabric-package-apps.md) en maak deze klaar voor implementatie.
- [Toepassingen implementeren en verwijderen](service-fabric-deploy-remove-applications.md).
- [Parameters en omgevingsvariabelen configureren voor verschillende toepassingsinstanties](service-fabric-manage-multiple-environment-app-configuration.md).
- [Beveiligingsbeleid configureren voor uw toepassing](service-fabric-application-runas-security.md).
- [HTTPS-eindpunten instellen](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Geheimen versleutelen in het toepassingsmanifest](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



