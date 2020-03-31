---
title: Voorbeelden van voorbeelden van het manifest van azure Service Fabric-containertoepassingsmanifest
description: Meer informatie over het configureren van toepassings- en servicemanifestinstellingen voor een servicefabric-toepassing met meerdere containers.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258393"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Voorbeelden van toepassings- en servicemanifesten voor toepassingen met meerdere containers
Hieronder volgen voorbeelden van de applicatie- en servicemanifesten voor een multicontainer Service Fabric-toepassing. Het doel van deze voorbeelden is om te laten zien welke instellingen beschikbaar zijn en hoe ze te gebruiken. Deze toepassings- en servicemanifesten zijn gebaseerd op de [manifesten van Windows Server 2016 Container Sample.](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows)

De volgende functies worden weergegeven:

|Manifest|Functies|
|---|---|
|[Toepassingsmanifest](#application-manifest)| [omgevingsvariabelen overschrijven,](service-fabric-get-started-containers.md#configure-and-set-environment-variables) [containerpoort-naar-hosttoewijzing configureren,](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery) [containerregisterverificatie configureren,](service-fabric-get-started-containers.md#configure-container-repository-authentication) [resourcebeheer](service-fabric-resource-governance.md), [isolatiemodus instellen](service-fabric-get-started-containers.md#configure-isolation-mode), [build-specifieke containerafbeeldingen van het besturingssysteem opgeven](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[FrontEndService-servicemanifest](#frontendservice-service-manifest)| [omgevingsvariabelen instellen](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [een eindpunt configureren,](service-fabric-get-started-containers.md#configure-communication)opdrachten doorgeven aan de container, [een certificaat importeren in een container](service-fabric-securing-containers.md)| 
|[BackEndService-servicemanifest](#backendservice-service-manifest)|[omgevingsvariabelen instellen,](service-fabric-get-started-containers.md#configure-and-set-environment-variables) [een eindpunt configureren,](service-fabric-get-started-containers.md#configure-communication) [volumestuurprogramma configureren](service-fabric-containers-volume-logging-drivers.md)| 

Zie [Manifestelementen voor toepassingen,](#application-manifest-elements) [manifestelementen frontEndService-service](#frontendservice-service-manifest-elements)en [BackEndService-servicemanifestelementen](#backendservice-service-manifest-elements) voor meer informatie over specifieke XML-elementen.

## <a name="application-manifest"></a>Manifest van de toepassing

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>FrontEndService-servicemanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>BackEndService-servicemanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="application-manifest-elements"></a>Manifestelementen van toepassing
### <a name="applicationmanifest-element"></a>Toepassingsmanifestelement
Declaratively beschrijft het toepassingstype en de versie. Naar een of meer servicemanifesten van de samenstellende diensten wordt verwezen naar het samenstellen van een toepassingstype. Configuratie-instellingen van de samenstellende services kunnen worden overschreven met behulp van parameterinstellingen. Standaardservices, servicesjablonen, principals, beleidsregels, diagnose-instellingen en certificaten kunnen ook worden gedeclareerd op toepassingsniveau. Zie [ApplicationManifest Element voor](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType) meer informatie

### <a name="parameters-element"></a>Parameterselement
Hiermee verklaart u de parameters die in dit toepassingsmanifest worden gebruikt. De waarde van deze parameters kan worden opgegeven wanneer de toepassing is geinstantieerd en kan worden gebruikt om de instellingen van de toepassing of serviceconfiguratie te overschrijven. Zie [Parameterselement](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType) voor meer informatie

### <a name="parameter-element"></a>Parameterelement
Een toepassingsparameter die in dit manifest moet worden gebruikt. De parameterwaarde kan worden gewijzigd tijdens de instantiatie van toepassingen of, als er geen waarde wordt opgegeven, wordt de standaardwaarde gebruikt. Zie [Parameterelement voor](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement) meer informatie

### <a name="servicemanifestimport-element"></a>ServiceManifestImport Element
Hiermee importeert u een servicemanifest dat is gemaakt door de serviceontwikkelaar. Voor elke samenstellende dienst in de toepassing moet een servicemanifest worden ingevoerd. Configuratieoverschrijvingen en -beleid kunnen worden gedeclareerd voor het servicemanifest. Zie [ServiceManifestImport Element voor](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType) meer informatie

### <a name="servicemanifestref-element"></a>ServiceManifestRef Element
Hiermee wordt het gemanifesteerde dienst op basis van verwijzing ingevoerd. Momenteel moet het servicemanifestbestand (ServiceManifest.xml) aanwezig zijn in het buildpakket. Zie [ServiceManifestRef Element voor](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement) meer informatie

### <a name="policies-element"></a>Beleidselement
Beschrijft beleid (end-point binding, pakketdelen, run-as- en beveiligingstoegang) die moet worden toegepast op het geïmporteerde servicemanifest. Zie [Beleidselement voor](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement) meer informatie

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
Hiermee definieert u het resourcegovernancebeleid dat wordt toegepast op het niveau van het gehele servicepakket. Zie [ServicePackageResourceGovernancePolicyPolicy Element voor](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType) meer informatie

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy Element
Hiermee geeft u resourcelimieten op voor een codepakket. Zie [ResourceGovernancePolicy Element voor](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement) meer informatie

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies Element
Hiermee geeft u beleid op voor het activeren van containerhosts. Zie [ContainerHostPolicies Element](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement) voor meer informatie

### <a name="repositorycredentials-element"></a>RepositoryCredentials Element
Referenties voor containerimagerepository om afbeeldingen uit te halen. Zie [RepositoryCredentials Element](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType) voor meer informatie

### <a name="portbinding-element"></a>Portbinding-element
Hiermee geeft u op welke eindpuntbron moet worden gekoppeld aan de blootgestelde containerpoort. Zie [Portbinding Element voor](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType) meer informatie

### <a name="volume-element"></a>Volume-element
Hiermee geeft u het volume op dat aan de container moet worden gebonden. Zie [Volumeelement voor](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType) meer informatie

### <a name="driveroption-element"></a>DriverOption Element
Driver opties worden doorgegeven aan de bestuurder. Zie [DriverOption Element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType) voor meer informatie

### <a name="imageoverrides-element"></a>Afbeelding Overschrijft element
Windows Server-containers zijn mogelijk niet compatibel voor verschillende versies van het besturingssysteem.  U meerdere OS-afbeeldingen per container opgeven en deze taggen met de buildversies van het besturingssysteem. Download de buildversie van het besturingssysteem door 'winver' uit te voeren bij een Windows-opdrachtprompt. Als het onderliggende besturingssysteem versie 16299 (Windows Server-versie 1709) is gebouwd, kiest Service Fabric de containerafbeelding met os="16299". Er wordt van uitgegaan dat een niet-gecodeerde containerafbeelding in alle versies van het besturingssysteem werkt en de afbeelding overschrijft die is opgegeven in het servicemanifest. Zie [Afbeeldingselement Overschrijft voor](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType) meer informatie

### <a name="image-element"></a>Afbeeldingselement
Containerafbeelding die overeenkomt met het versienummer van OS build dat moet worden gestart. Als het kenmerk Os niet is opgegeven, wordt ervan uitgegaan dat de containerafbeelding werkt in alle versies van het besturingssysteem en wordt de afbeelding die is opgegeven in het servicemanifest overschreven. Zie [Afbeeldingselement voor](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType) meer informatie

### <a name="environmentoverrides-element"></a>Element Omgevingoverschrijft
 Zie [Element Omgevingoverschrijft voor](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement) meer informatie

### <a name="environmentvariable-element"></a>Element Omgevingsveranderlijk
Omgevingsvariabele. Zie [EnvironmentVariable Element voor](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType) meer informatie

### <a name="certificateref-element"></a>CertificateRef-element
Hiermee geeft u informatie op over een X509-certificaat dat moet worden blootgesteld aan de containeromgeving. Het certificaat moet worden geïnstalleerd in de LocalMachine-winkel van alle clusterknooppunten.
Wanneer de toepassing wordt gestart, leest de runtime het certificaat en genereert een PFX-bestand en wachtwoord (op Windows) of een PEM-bestand (op Linux).
Het PFX-bestand en wachtwoord zijn toegankelijk in de container met behulp van de Certificates_ServicePackageName_CodePackageName_CertName_PFX en Certificates_ServicePackageName_CodePackageName_CertName_Password omgevingsvariabelen. Het PEM-bestand is toegankelijk in de container met behulp van de Certificates_ServicePackageName_CodePackageName_CertName_PEM en Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey omgevingsvariabelen. Zie [CertificateRef Element voor](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType) meer informatie

### <a name="defaultservices-element"></a>DefaultServices-element
Hiermee declareert u service-exemplaren die automatisch worden gemaakt wanneer een toepassing wordt geinstantieerd tegen dit toepassingstype. Zie [DefaultServices-element voor](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType) meer informatie

### <a name="service-element"></a>Service-element
Hiermee verklaart u dat een service automatisch moet worden gemaakt wanneer de toepassing wordt geinstantieerd. Zie [Service-element voor](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType) meer informatie

### <a name="statelessservice-element"></a>StatelessService-element
Hiermee definieert u een statusloze service. Zie [StatelessService Element voor](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement) meer informatie


## <a name="frontendservice-service-manifest-elements"></a>FrontEndService-servicemanifestelementen
### <a name="servicemanifest-element"></a>ServiceManifest Element
Declaratief beschrijft het servicetype en de versie. Het bevat de onafhankelijk te upgraden code, configuratie en gegevenspakketten die samen een servicepakket samenstellen om een of meer servicetypen te ondersteunen. Resources, diagnostische instellingen en servicemetagegevens, zoals servicetype, statuseigenschappen en load-balancing metrics, worden ook opgegeven. Zie [ServiceManifest Element voor](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType) meer informatie

### <a name="servicetypes-element"></a>ServiceTypes Element
Hiermee definieert u welke servicetypen worden ondersteund door een CodePackage in dit manifest. Wanneer een service wordt geinstantieerd ten opzichte van een van deze servicetypen, worden alle codepakketten die in dit manifest zijn aangegeven, geactiveerd door hun toegangspunten uit te voeren. Servicetypen worden aangegeven op manifestniveau en niet op het codepakketniveau. Zie [Element ServiceTypes voor](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType) meer informatie

### <a name="statelessservicetype-element"></a>StatelessServiceType-element
Beschrijft een servicetype zonder staat. Zie [StatelessServiceType Element voor](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType) meer informatie

### <a name="codepackage-element"></a>CodePakket-element
Beschrijft een codepakket dat een gedefinieerd servicetype ondersteunt. Wanneer een service wordt geinstantieerd ten opzichte van een van deze servicetypen, worden alle codepakketten die in dit manifest zijn aangegeven, geactiveerd door hun toegangspunten uit te voeren. De resulterende processen worden verwacht dat de ondersteunde servicetypen tijdens uitvoering registreren. Wanneer er meerdere codepakketten zijn, worden ze allemaal geactiveerd wanneer het systeem zoekt naar een van de opgegeven servicetypen. Zie [CodePackage Element voor](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement) meer informatie

### <a name="entrypoint-element"></a>EntryPoint-element
De uitvoerbare die door EntryPoint is opgegeven, is meestal de langlopende servicehost. De aanwezigheid van een apart instelpunt voorkomt dat de servicehost langere tijd moet worden uitgevoerd met hoge bevoegdheden. De uitvoerbare die door EntryPoint is opgegeven, wordt uitgevoerd nadat SetupEntryPoint is afgesloten. Het resulterende proces wordt gecontroleerd en opnieuw gestart (opnieuw beginnen met SetupEntryPoint) als het ooit wordt beëindigd of vastloopt. Zie [EntryPoint-element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType) voor meer informatie

### <a name="containerhost-element"></a>ContainerHost-element
 Zie [ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType) voor meer informatie

### <a name="imagename-element"></a>Afbeeldingsnaamelement
De repo en [https://hub.docker.com](https://hub.docker.com) afbeelding op of Azure Container Registry. Zie [ImageName Element voor](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType) meer informatie

### <a name="environmentvariables-element"></a>Element Omgevingsvariabelen
Geef omgevingsvariabelen door aan uw container of exe.  Zie [EnvironmentVariables Element voor](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType) meer informatie

### <a name="environmentvariable-element"></a>Element Omgevingsveranderlijk
Omgevingsvariabele. Zie [EnvironmentVariable Element voor](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType) meer informatie

### <a name="configpackage-element"></a>ConfigPackage Element
Hiermee verklaart u een map, genoemd naar het kenmerk Naam, die een bestand Settings.xml bevat. Dit bestand bevat secties met door de gebruiker gedefinieerde instellingen voor sleutelsleutels die het proces tijdens de looptijd kan teruglezen. Tijdens een upgrade, als alleen de ConfigPackage-versie is gewijzigd, wordt het lopende proces niet opnieuw gestart. In plaats daarvan waarschuwt een callback het proces dat de configuratie-instellingen zijn gewijzigd, zodat ze dynamisch kunnen worden herladen. Zie [ConfigPackage Element voor](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement) meer informatie

### <a name="datapackage-element"></a>Element DataPakket
Hiermee verklaart u een map, genoemd door het kenmerk Naam, die statische gegevensbestanden bevat. Service Fabric zal alle EXEs en DLLHOSTs recyclen die in de host- en ondersteuningspakketten zijn opgegeven wanneer een van de gegevenspakketten in het servicemanifest wordt bijgewerkt. Zie [DataPackage Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement) voor meer informatie

### <a name="resources-element"></a>Elementen resources
Beschrijft de resources die door deze service worden gebruikt, die kunnen worden gedeclareerd zonder de gecompileerde code te wijzigen en gewijzigd wanneer de service wordt geïmplementeerd. De toegang tot deze bronnen wordt beheerd via de secties Principals and Policies van het toepassingsmanifest. Zie [Resources Element voor](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType) meer informatie

### <a name="endpoints-element"></a>Element Eindpunten
Hiermee definieert u eindpunten voor de service. Zie [Eindpuntenelement voor](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType) meer informatie

### <a name="endpoint-element"></a>Eindpuntelement
Zie [Eindpuntelement voor](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement) meer informatie


## <a name="backendservice-service-manifest-elements"></a>BackEndService-servicemanifestelementen
### <a name="servicemanifest-element"></a>ServiceManifest Element
Declaratief beschrijft het servicetype en de versie. Het bevat de onafhankelijk te upgraden code, configuratie en gegevenspakketten die samen een servicepakket samenstellen om een of meer servicetypen te ondersteunen. Resources, diagnostische instellingen en servicemetagegevens, zoals servicetype, statuseigenschappen en load-balancing metrics, worden ook opgegeven. Zie [ServiceManifest Element voor](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType) meer informatie

### <a name="servicetypes-element"></a>ServiceTypes Element
Hiermee definieert u welke servicetypen worden ondersteund door een CodePackage in dit manifest. Wanneer een service wordt geinstantieerd ten opzichte van een van deze servicetypen, worden alle codepakketten die in dit manifest zijn aangegeven, geactiveerd door hun toegangspunten uit te voeren. Servicetypen worden aangegeven op manifestniveau en niet op het codepakketniveau. Zie [Element ServiceTypes voor](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType) meer informatie

### <a name="statelessservicetype-element"></a>StatelessServiceType-element
Beschrijft een servicetype zonder staat. Zie [StatelessServiceType Element voor](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType) meer informatie

### <a name="codepackage-element"></a>CodePakket-element
Beschrijft een codepakket dat een gedefinieerd servicetype ondersteunt. Wanneer een service wordt geinstantieerd ten opzichte van een van deze servicetypen, worden alle codepakketten die in dit manifest zijn aangegeven, geactiveerd door hun toegangspunten uit te voeren. De resulterende processen worden verwacht dat de ondersteunde servicetypen tijdens uitvoering registreren. Wanneer er meerdere codepakketten zijn, worden ze allemaal geactiveerd wanneer het systeem zoekt naar een van de opgegeven servicetypen. Zie [CodePackage Element voor](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement) meer informatie

### <a name="entrypoint-element"></a>EntryPoint-element
De uitvoerbare die door EntryPoint is opgegeven, is meestal de langlopende servicehost. De aanwezigheid van een apart instelpunt voorkomt dat de servicehost langere tijd moet worden uitgevoerd met hoge bevoegdheden. De uitvoerbare die door EntryPoint is opgegeven, wordt uitgevoerd nadat SetupEntryPoint is afgesloten. Het resulterende proces wordt gecontroleerd en opnieuw gestart (opnieuw beginnen met SetupEntryPoint) als het ooit wordt beëindigd of vastloopt. Zie [EntryPoint-element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType) voor meer informatie

### <a name="containerhost-element"></a>ContainerHost-element
Zie [ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType) voor meer informatie

### <a name="imagename-element"></a>Afbeeldingsnaamelement
De repo en [https://hub.docker.com](https://hub.docker.com) afbeelding op of Azure Container Registry. Zie [ImageName Element voor](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType) meer informatie

### <a name="commands-element"></a>Opdrachten Element
Geef een komma afgebakende lijst met opdrachten door aan de container. Zie [Opdrachtenelement voor](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType) meer informatie

### <a name="environmentvariables-element"></a>Element Omgevingsvariabelen
Geef omgevingsvariabelen door aan uw container of exe.  Zie [EnvironmentVariables Element voor](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType) meer informatie

### <a name="environmentvariable-element"></a>Element Omgevingsveranderlijk
Omgevingsvariabele. Zie [EnvironmentVariable Element voor](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType) meer informatie

### <a name="configpackage-element"></a>ConfigPackage Element
Hiermee verklaart u een map, genoemd naar het kenmerk Naam, die een bestand Settings.xml bevat. Dit bestand bevat secties met door de gebruiker gedefinieerde instellingen voor sleutelsleutels die het proces tijdens de looptijd kan teruglezen. Tijdens een upgrade, als alleen de ConfigPackage-versie is gewijzigd, wordt het lopende proces niet opnieuw gestart. In plaats daarvan waarschuwt een callback het proces dat de configuratie-instellingen zijn gewijzigd, zodat ze dynamisch kunnen worden herladen. Zie [ConfigPackage Element voor](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement) meer informatie

### <a name="resources-element"></a>Elementen resources
Beschrijft de resources die door deze service worden gebruikt, die kunnen worden gedeclareerd zonder de gecompileerde code te wijzigen en gewijzigd wanneer de service wordt geïmplementeerd. De toegang tot deze bronnen wordt beheerd via de secties Principals and Policies van het toepassingsmanifest. Zie [Resources Element voor](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType) meer informatie

### <a name="endpoints-element"></a>Element Eindpunten
Hiermee definieert u eindpunten voor de service. Zie [Eindpuntenelement voor](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType) meer informatie

### <a name="endpoint-element"></a>Eindpuntelement
 Zie [Eindpuntelement voor](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement) meer informatie

