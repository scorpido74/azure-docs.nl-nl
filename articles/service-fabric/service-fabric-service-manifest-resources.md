---
title: Eindpunten van servicefabric-service opgeven
description: Eindpuntbronnen beschrijven in een servicemanifest, inclusief het instellen van HTTPS-eindpunten
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 88e71d15829e68bde635f5b4d40224b8fa914f40
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417595"
---
# <a name="specify-resources-in-a-service-manifest"></a>Resources opgeven in een servicemanifest
## <a name="overview"></a>Overzicht
Met het servicemanifest kunnen resources die door de service worden gebruikt, worden gedeclareerd of gewijzigd zonder de gecompileerde code te wijzigen. Service Fabric ondersteunt de configuratie van eindpuntresources voor de service. De toegang tot de resources die zijn opgegeven in het servicemanifest kan worden beheerd via de SecurityGroup in het toepassingsmanifest. Met de verklaring van resources kunnen deze resources worden gewijzigd tijdens de implementatie, wat betekent dat de service geen nieuw configuratiemechanisme hoeft in te voeren. De schemadefinitie voor het bestand ServiceManifest.xml is geïnstalleerd met de Service Fabric SDK en de hulpprogramma's voor *C:\Program Files\Microsoft SDKs\Service Fabric\schema's\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Eindpunten
Wanneer een eindpuntbron is gedefinieerd in het servicemanifest, wijst Service Fabric poorten toe uit het gereserveerde toepassingspoortbereik wanneer een poort niet expliciet is opgegeven. Kijk bijvoorbeeld naar het eindpunt *ServiceEndpoint1* dat is opgegeven in het manifestfragment dat na deze alinea wordt verstrekt. Bovendien kunnen services ook een specifieke poort in een resource aanvragen. Servicereplica's die op verschillende clusterknooppunten worden uitgevoerd, kunnen verschillende poortnummers toegewezen krijgen, terwijl replica's van een service die op hetzelfde knooppunt wordt uitgevoerd, de poort delen. De servicereplica's kunnen deze poorten vervolgens gebruiken als dat nodig is voor replicatie en het luisteren naar clientaanvragen.

Bij het activeren van een service die een https-eindpunt opgeeft, stelt Service Fabric de toegangscontrole invoer voor de poort in, bindt het opgegeven servercertificaat aan de poort en verleent het ook de identiteit die de service uitvoert als machtigingen voor de privésleutel van het certificaat. De activeringsstroom wordt aangeroepen telkens wanneer Service Fabric wordt gestart of wanneer de certificaatverklaring van de toepassing wordt gewijzigd via een upgrade. Het eindpuntcertificaat wordt ook gecontroleerd op wijzigingen/verlengingen en machtigingen worden indien nodig periodiek opnieuw toegepast.

Na beëindiging van de service zal Service Fabric de toegangscontrole-vermelding voor eindpunten opschonen en de certificaatbinding verwijderen. Alle machtigingen die worden toegepast op de privésleutel van het certificaat worden echter niet opgeschoond.

> [!WARNING] 
> Statische poorten mogen volgens het ontwerp niet overlappen met het bereik van de toepassingspoort dat is opgegeven in het clustermanifest. Als u een statische poort opgeeft, wijst u deze toe buiten het bereik van de toepassingspoort, anders leidt dit tot poortconflicten. Met release 6.5CU2 geven we een **gezondheidswaarschuwing** wanneer we een dergelijk conflict detecteren, maar laten we de implementatie synchroon laten doorgaan met het verzonden 6.5-gedrag. We kunnen echter voorkomen dat de implementatie van de toepassing wordt verwijderd van de volgende grote releases.
>
> Met release 7.0 geven we een **gezondheidswaarschuwing** wanneer we het gebruik van het toepassingspoortbereik detecteren dat verder gaat dan HostingConfig::ApplicationPortExhaustThresholdPercentage (standaard 80%).
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Als er meerdere codepakketten in één servicepakket zijn, moet er ook naar het codepakket worden verwezen in de sectie **Eindpunten.**  Als **ServiceEndpoint2a** en **ServiceEndpoint2b** bijvoorbeeld eindpunten zijn van hetzelfde servicepakket dat verwijst naar verschillende codepakketten, wordt het codepakket dat overeenkomt met elk eindpunt als volgt verduidelijkt:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Raadpleeg [Stateful Reliable Services configureren](service-fabric-reliable-services-configuration.md) om meer te lezen over het verwijzen naar eindpunten uit het bestand met config-pakketinstellingen (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Voorbeeld: een HTTP-eindpunt voor uw service opgeven
In het volgende servicemanifest wordt één TCP-eindpuntbron &lt;en&gt; twee HTTP-eindpuntresources gedefinieerd in het element Resources.

HTTP-eindpunten worden automatisch ACL'd van Service Fabric.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Voorbeeld: een HTTPS-eindpunt voor uw service opgeven
Het HTTPS-protocol biedt serververificatie en wordt ook gebruikt voor het versleutelen van client-servercommunicatie. Als u HTTPS wilt inschakelen op uw Service Fabric-service, geeft u het protocol op in het gedeelte *Resources -> Eindpunten -> eindpunt* van het servicemanifest, zoals eerder wordt weergegeven voor het eindpunt *ServiceEndpoint3*.

> [!NOTE]
> Het protocol van een service kan niet worden gewijzigd tijdens de upgrade van de toepassing. Als het tijdens de upgrade wordt gewijzigd, is het een baanbrekende wijziging.
> 

> [!WARNING] 
> Gebruik bij het gebruik van HTTPS niet dezelfde poort en hetzelfde certificaat voor verschillende service-exemplaren (onafhankelijk van de toepassing) die naar hetzelfde knooppunt worden geïmplementeerd. Als u twee verschillende services met dezelfde poort in verschillende toepassingsinstanties upgradet, wordt een upgradefout uitgevoerd. Zie [Meerdere toepassingen upgraden met HTTPS-eindpunten ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints)voor meer informatie.
>

Hier is een voorbeeld ApplicationManifest dat de configuratie aantoont die nodig is voor een HTTPS-eindpunt. Het server/eindpuntcertificaat kan worden aangegeven met duimafdruk of algemene naam en er moet een waarde worden opgegeven. De EndpointRef is een verwijzing naar EndpointResource in ServiceManifest en waarvan het protocol moet zijn ingesteld op het 'https'-protocol. U meer dan één EndpointCertificate toevoegen.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Voor Linux-clusters wordt de **MY-winkel** standaard ingesteld op de map **/var/lib/sfcerts.**


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Overschrijding van eindpunten in ServiceManifest.xml

Voeg in het ApplicationManifest een sectie ResourceOverrides toe, die een broer of zus is van de sectie ConfigOverrides. In deze sectie u de override voor de sectie Eindpunten opgeven in de sectie resources die is opgegeven in het servicemanifest. Overheersende eindpunten worden ondersteund in runtime 5.7.217/SDK 2.7.217 en hoger.

Als u EndPoint in ServiceManifest wilt overschrijven met ApplicationParameters, wijzigt u het ApplicationManifest als volgt:

Voeg in de sectie ServiceManifestImport een nieuwe sectie 'ResourceOverrides' toe.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Voeg in de onderstaande parameters toe:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Tijdens het implementeren van de toepassing u deze waarden doorgeven als ApplicationParameters.  Bijvoorbeeld:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Opmerking: Als de waarden voor de toepassingsparameters leeg zijn, gaan we terug naar de standaardwaarde in het ServiceManifest voor het bijbehorende EndPointName.

Bijvoorbeeld:

Als u in het ServiceManifest hebt opgegeven

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

En de waarde Port1 en Protocol1 voor toepassingsparameters is nietig of leeg. De haven wordt nog steeds bepaald door ServiceFabric. En het protocol zal tcp.

Stel dat u een verkeerde waarde opgeeft. Net als bij Port hebt u een tekenreekswaarde 'Foo' opgegeven in plaats van een int.  Opdracht Nieuw-ServiceFabricApplication mislukt met een fout: de parameter overschrijven met de naam 'ServiceEndpoint1' kenmerk 'Port1' in sectie 'ResourceOverrides' is ongeldig. De opgegeven waarde is 'Foo' en vereist is 'int'.
