---
title: Azure Files-volumestuurprogramma voor ServiceFabric
description: Service Fabric ondersteunt het gebruik van Azure Files om back-upvolumes uit uw container te maken.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: 514a0cb12359d58e38ebc30ae12cdb277757f2b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750036"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Azure Files-volumestuurprogramma voor ServiceFabric

Het volumestuurprogramma voor Azure Files is een [Docker-volumeplug-in](https://docs.docker.com/engine/extend/plugins_volume/) die [op Azure Files](/azure/storage/files/storage-files-introduction) gebaseerde volumes biedt voor Docker-containers. Het is verpakt als een Service Fabric-toepassing die kan worden geïmplementeerd in een Cluster Service Fabric om volumes te bieden voor andere Service Fabric-containertoepassingen binnen het cluster.

> [!NOTE]
> Versie 6.5.661.9590 van de Azure Files-volumeplug-in is vrijgegeven voor algemene beschikbaarheid.
>

## <a name="prerequisites"></a>Vereisten
* De Windows-versie van de Azure Files-volumeplug-in werkt alleen op [Windows Server-versie 1709,](/windows-server/get-started/whats-new-in-windows-server-1709) [Windows 10 versie 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) of hoger.

* De Linux-versie van de Azure Files-volumeplug-in werkt op alle versies van het besturingssysteem die worden ondersteund door Service Fabric.

* De plug-in azure files-volumewerkt alleen op Service Fabric-versie 6.2 en nieuwer.

* Volg de instructies in de [Azure Files-documentatie](/azure/storage/files/storage-how-to-create-file-share) om een bestandsshare te maken voor de containertoepassing Service Fabric die als volume kan worden gebruikt.

* U hebt Powershell nodig [met de Service Fabric-module](/azure/service-fabric/service-fabric-get-started) of [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) geïnstalleerd.

* Als u Hyper-V-containers gebruikt, moeten de volgende fragmenten worden toegevoegd in de sectie ClusterManifest (lokaal cluster) of fabricSettings in uw Azure Resource Manager-sjabloon (Azure-cluster) of ClusterConfig.json (standalone cluster).

In het Clustermanifest moet het volgende worden toegevoegd in de sectie Hosting. In dit voorbeeld is de volumenaam **sfazurefile** en de poort die wordt beluisterd op het cluster **19100**. Vervang ze door de juiste waarden voor uw cluster.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

In de sectie fabricSettings in uw Azure Resource Manager-sjabloon (voor Azure-implementaties) of ClusterConfig.json (voor zelfstandige implementaties) moet het volgende fragment worden toegevoegd. Vervang nogmaals de volumenaam en poortwaarden door die van u.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Een voorbeeldtoepassing implementeren met het volumestuurprogramma voor Azure-bestanden van Service Fabric

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Azure Resource Manager gebruiken via het meegeleverde Powershell-script (aanbevolen)

Als uw cluster is gebaseerd in Azure, raden we u aan toepassingen te implementeren met behulp van het Azure Resource Manager-toepassingsbronmodel voor gebruiksgemak en om te helpen bij het beheren van infrastructuur als code. Met deze aanpak hoeft u de app-versie voor het volumestuurprogramma voor Azure Files niet meer bij te houden. Het stelt u ook in staat om afzonderlijke Azure Resource Manager-sjablonen te onderhouden voor elk ondersteund besturingssysteem. Het script gaat ervan uit dat u de nieuwste versie van de Azure Files-toepassing implementeert en parameters neemt voor het type besturingssysteem, de clusterabonnements-id en de brongroep. U het script downloaden van de [Service Fabric download site.](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip) Houd er rekening mee dat hiermee automatisch de ListenPort, de poort waarop de Azure Files-volumeplug-in luistert naar aanvragen van de Docker-daemon, wordt ingesteld op 19100. U deze wijzigen door parameter listenPort toe te voegen. Zorg ervoor dat de poort niet in strijd is met een andere poort die het cluster of uw toepassingen gebruikt.
 

De opdracht implementatie van Azure Resource Manager voor Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

De opdracht implementatie van Azure Resource Manager voor Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Zodra u het script hebt uitgevoerd, u naar de [sectie uw toepassing configureren.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Handmatige implementatie voor zelfstandige clusters

De Service Fabric-applicatie die de volumes voor uw containers biedt, kan worden gedownload van de [downloadsite servicefabric.](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip) De toepassing kan worden geïmplementeerd in het cluster via [PowerShell,](./service-fabric-deploy-remove-applications.md) [CLI](./service-fabric-application-lifecycle-sfctl.md) of [FabricClient API's](./service-fabric-deploy-remove-applications-fabricclient.md).

1. Wijzig met de opdrachtregel de map in de hoofdmap van het gedownloade toepassingspakket.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Kopieer vervolgens het toepassingspakket naar het afbeeldingsarchief met de juiste waarden voor [ApplicationPackagePath] en [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Het toepassingstype registreren

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Maak de toepassing, met veel aandacht voor de parameterwaarde **listenport-toepassing.** Deze waarde is de poort waarop de Azure Files-volumeplug-in luistert naar aanvragen van de Docker-daemon. Zorg ervoor dat de poort die aan de toepassing wordt geleverd overeenkomt met de VolumePluginPorts in het clustermanifest en niet in strijd is met een andere poort die het cluster of uw toepassingen gebruikt.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter biedt geen ondersteuning voor het toewijzen van SMB-bevestigingen aan containers ([Dit wordt alleen ondersteund op Windows Server-versie 1709](/virtualization/windowscontainers/manage-containers/container-storage)). Deze beperking voorkomt netwerkvolumetoewijzing en Azure Files-volumestuurprogramma's op versies ouder dan 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>De toepassing implementeren op een lokaal ontwikkelingscluster
Volg stappen 1-3 van [boven.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 Het aantal standaardservice-instanties voor de plug-intoepassing Azure Files is -1, wat betekent dat er een instantie van de service is geïmplementeerd voor elk knooppunt in het cluster. Wanneer u echter de plug-in-toepassing Azure Files-volume in een lokaal ontwikkelingscluster implementeert, moet het aantal service-instanties worden opgegeven als 1. Dit kan via **InstanceCount** de instancecount-toepassingsparameter. Daarom is de opdracht voor het maken van de plug-in-toepassing Azure Files op een lokaal ontwikkelingscluster:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Uw toepassingen configureren om het volume te gebruiken
In het volgende fragment ziet u hoe een op Azure Files gebaseerd volume kan worden opgegeven in het toepassingsmanifestbestand van uw toepassing. Het specifieke element van belang is het **volumelabel:**

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

De naam van het stuurprogramma voor de azure-inhoudsplug-in **Azure-bestanden is sfazurefile**. Deze waarde is ingesteld voor het kenmerk **Stuurprogramma** van het element **Volumetag** in het toepassingsmanifest.

In de **volumetag** in het bovenstaande fragment heeft de azure-bestandenvolumeplug-in de volgende kenmerken vereist:
- **Bron** - Dit is de naam van het volume. De gebruiker kan elke naam voor zijn volume kiezen.
- **Bestemming** - Dit kenmerk is de locatie waaraan het volume is toegewezen in de lopende container. Uw bestemming kan dus geen locatie zijn die al in uw container aanwezig is

Zoals weergegeven in de **elementen DriverOption** in het bovenstaande fragment, ondersteunt de volumeplug-in Azure Files de volgende stuurprogrammaopties:
- **shareName** - Naam van de Azure Files-bestandsshare die het volume voor de container biedt.
- **storageAccountName** - Naam van het Azure-opslagaccount dat de bestandsshare van Azure Files bevat.
- **storageAccountKey** - Toegangssleutel voor het Azure-opslagaccount dat de bestandsshare van Azure Files bevat.
- **storageAccountFQDN** - Domeinnaam die is gekoppeld aan het opslagaccount. Als storageAccountFQDN niet is opgegeven, wordt de domeinnaam gevormd met behulp van het standaardachtervoegsel(.file.core.windows.net) met de storageAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Uw eigen volume of logboekregistratiestuurprogramma gebruiken
Service Fabric maakt het ook mogelijk om uw eigen aangepaste [volume](https://docs.docker.com/engine/extend/plugins_volume/) of [logging](https://docs.docker.com/engine/admin/logging/overview/) drivers. Als het Docker-volume/logboekregistratiestuurprogramma niet op het cluster is geïnstalleerd, u het handmatig installeren met behulp van de RDP/SSH-protocollen. U de installatie met deze protocollen uitvoeren via een [opstartscript voor virtuele machines schaalset](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) of een [SetupEntryPoint-script.](/azure/service-fabric/service-fabric-application-model)

Een voorbeeld van het script dat het [docker-volumestuurprogramma voor Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) wilt installeren, is als volgt:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

In uw toepassingen moet u in uw toepassingen, om het volume of het logboekstuurprogramma dat u hebt geïnstalleerd, de juiste waarden in de **elementen Volume** en **LogConfig** onder **ContainerHostPolicies** in uw toepassingsmanifest opgeven.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

Bij het opgeven van een volumeplug-in maakt Service Fabric automatisch het volume met behulp van de opgegeven parameters. De **brontag** voor het element **Volume** is de naam van het volume en de **tag Stuurprogramma** geeft de invoegtoepassing volumestuurprogramma aan. De **tag Doel** is de locatie waaraan de **bron** is toegewezen in de lopende container. Uw bestemming kan dus geen locatie zijn die al in uw container aanwezig is. Opties kunnen als volgt worden opgegeven met de tag **DriverOption:**

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Toepassingsparameters worden ondersteund voor volumes zoals weergegeven in het `MyStorageVar` vorige manifestfragment (zoek naar een voorbeeldgebruik).

Als een Docker-logboekstuurprogramma is opgegeven, moet u agents (of containers) implementeren om de logboeken in het cluster te verwerken. De **DriverOption-tag** kan worden gebruikt om opties voor het logboekstuurprogramma op te geven.

## <a name="next-steps"></a>Volgende stappen
* Ga naar de [containermonsters van Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) om containermonsters te bekijken, inclusief de volumedriver.
* Als u containers wilt implementeren in een cluster servicestructuur, verwijst u het artikel [Een container implementeren op Servicefabric](service-fabric-deploy-container.md)
