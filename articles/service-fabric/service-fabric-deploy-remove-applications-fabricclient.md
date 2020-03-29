---
title: Azure Service Fabric-implementatie met FabricClient
description: Gebruik de FabricClient API's om toepassingen in Service Fabric te implementeren en te verwijderen.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 25b874d1be8ab50d8076ff8fe9423c8cc0187512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376967"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Toepassingen implementeren en verwijderen met FabricClient
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [Powershell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric-CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient-API's](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Zodra een [toepassingstype is verpakt,][10]is het klaar voor implementatie in een Azure Service Fabric-cluster. Implementatie omvat de volgende drie stappen:

1. Het toepassingspakket uploaden naar het afbeeldingsarchief
2. Het toepassingstype registreren
3. Het toepassingspakket uit het afbeeldingsarchief verwijderen
4. De toepassingsinstantie maken

Nadat u een toepassing hebt geïmplementeerd en een instantie in het cluster hebt uitgevoerd, u de toepassingsinstantie en het toepassingstype verwijderen. Verwijder een toepassing volledig uit het cluster door de volgende stappen te volgen:

1. De lopende toepassingsinstantie verwijderen (of verwijderen)
2. Het toepassingstype uitschrijven als u het niet meer nodig hebt

Als u Visual Studio gebruikt voor het implementeren en debuggen van toepassingen op uw lokale ontwikkelingscluster, worden alle voorgaande stappen automatisch afgehandeld via een PowerShell-script.  Dit script is te vinden in de map *Scripts* van het toepassingsproject. In dit artikel vindt u achtergrondinformatie over wat dat script doet, zodat u dezelfde bewerkingen buiten Visual Studio uitvoeren. 
 
## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster
Maak verbinding met het cluster door een [FabricClient-exemplaar](/dotnet/api/system.fabric.fabricclient) te maken voordat u een van de codevoorbeelden in dit artikel uitvoert. Zie Verbinding maken met een beveiligd cluster voor een lokaal ontwikkelingscluster of een extern cluster of cluster dat is beveiligd met Azure Active Directory-, X509-certificaten of Windows Active Directory voor voorbeelden van verbinding maken met [een cluster voor lokale](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis)ontwikkeling of een extern cluster of cluster dat is beveiligd met Azure Active Directory-, X509-certificaten of Windows Active Directory. Voer het volgende voorbeeld uit om verbinding te maken met het cluster lokale ontwikkeling:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Het toepassingspakket uploaden
Stel dat u een toepassing met de naam *MyApplication* in Visual Studio bouwt en verpakt. Standaard is de naam van het toepassingstype in applicationmanifest.xml "MyApplicationType".  Het toepassingspakket, dat het benodigde applicatiemanifest, servicemanifesten en code/config/datapakketten bevat, bevindt zich in *C:\Gebruikers\&&gt;lt;gebruikersnaam \Documents\Visual Studio 2019\Projects\MyApplication\MyApplication\pkg\Debug*.

Als u het toepassingspakket uploadt, wordt het op een locatie geplaatst die toegankelijk is voor de interne Onderdelen van Service Fabric. Service Fabric verifieert het aanvraagpakket tijdens de registratie van het aanvraagpakket. Als u het toepassingspakket echter lokaal wilt verifiëren (dat wil zeggen voordat u het uploadt), gebruikt u de cmdlet [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)

De [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API uploadt het toepassingspakket naar het clusterimagearchief. 

Als het toepassingspakket groot is en/of veel bestanden heeft, u [het comprimeren](service-fabric-package-apps.md#compress-a-package) en kopiëren naar de afbeeldingswinkel met PowerShell. De compressie vermindert de grootte en het aantal bestanden.

Zie [De verbindingstekenreeks voor afbeeldingsarchiefen begrijpen](service-fabric-image-store-connection-string.md) voor aanvullende informatie over de verbindingstekenreeks voor afbeeldingsopslag en afbeeldingsarchief.

## <a name="register-the-application-package"></a>Het aanvraagpakket registreren
Het toepassingstype en de versie die in het toepassingsmanifest zijn aangegeven, worden beschikbaar voor gebruik wanneer het toepassingspakket wordt geregistreerd. Het systeem leest het pakket dat in de vorige stap is geüpload, verifieert het pakket, verwerkt de inhoud van het pakket en kopieert het verwerkte pakket naar een interne systeemlocatie.  

De [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API registreert het toepassingstype in het cluster en maakt het beschikbaar voor implementatie.

De [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API bevat informatie over alle met succes geregistreerde toepassingstypen. U deze API gebruiken om te bepalen wanneer de registratie is uitgevoerd.

## <a name="remove-an-application-package-from-the-image-store"></a>Een toepassingspakket verwijderen uit het afbeeldingsarchief
Het wordt aanbevolen om het aanvraagpakket te verwijderen nadat de toepassing is geregistreerd.  Als u toepassingspakketten uit de afbeeldingswinkel verwijderde, worden systeembronnen vrijgemaakt.  Het bijhouden van ongebruikte toepassingspakketten verbruikt schijfopslag en leidt tot problemen met de prestaties van toepassingen. Verwijder het toepassingspakket uit het afbeeldingsarchief met de [API RemoveApplicationPackage.](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage)

## <a name="create-an-application-instance"></a>Een toepassingsinstantie maken
U een toepassing instantiëren van elk toepassingstype dat is geregistreerd met behulp van de [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API. De naam van elke toepassing moet beginnen met het *"fabric:"-schema* en moet uniek zijn voor elke toepassingsinstantie (binnen een cluster). Er worden ook standaardservices gemaakt die zijn gedefinieerd in het toepassingsmanifest van het doeltoepassingstype.

Voor een bepaalde versie van een geregistreerd toepassingstype kunnen meerdere toepassingsexemplaren worden gemaakt. Elke toepassingsinstantie wordt geïsoleerd uitgevoerd, met een eigen werkmap en een reeks processen.

Als u wilt zien welke benoemde toepassingen en services in het cluster worden uitgevoerd, voert u de [API's GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) en [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) uit.

## <a name="create-a-service-instance"></a>Een service-instantie maken
U een service van een servicetype instantiëren met de [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API.  Als de service wordt gedeclareerd als een standaardservice in het toepassingsmanifest, wordt de service geinstantieerd wanneer de toepassing wordt geinstantieerd.  Als u de [CreateServiceAsync-API](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) aanroept voor een service die al is geinstantieerd, wordt een uitzondering van het type FabricException weergegeven. De uitzondering bevat een foutcode met een waarde van FabricErrorCode.ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Een service-instantie verwijderen
Wanneer een service-exemplaar niet meer nodig is, u deze verwijderen uit de lopende toepassingsinstantie door de [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API aan te roepen.  

> [!WARNING]
> Deze bewerking kan niet worden teruggedraaid en de servicestatus kan niet worden hersteld.

## <a name="remove-an-application-instance"></a>Een toepassingsinstantie verwijderen
Wanneer een toepassingsinstantie niet meer nodig is, u deze permanent op naam verwijderen met behulp van de [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) verwijdert automatisch alle services die tot de toepassing behoren, waardoor alle servicestatus permanent wordt verwijderd.

> [!WARNING]
> Deze bewerking kan niet worden teruggedraaid en de toepassingsstatus kan niet worden hersteld.

## <a name="unregister-an-application-type"></a>Een toepassingstype uitschrijven
Wanneer een bepaalde versie van een toepassingstype niet meer nodig is, moet u die specifieke versie van het toepassingstype uitschrijven met behulp van de API [Unregister-ServiceFabricApplicationType.](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) Als u niet-gebruikte versies van toepassingstypen uitschrijft, wordt opslagruimte vrijgegeven die door het afbeeldingsarchief wordt gebruikt. Een versie van een toepassingstype kan worden niet geregistreerd zolang er geen toepassingen zijn geinstantieerd ten opzichte van die versie van het toepassingstype. Het toepassingstype kan ook geen in behandeling zijnde toepassingsupgrades hebben die verwijzen naar die versie van het toepassingstype.

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage vraagt om een ImageStoreConnectionString
De SERVICE Fabric SDK-omgeving moet al de juiste standaardinstellingen hebben ingesteld. Maar indien nodig moet de ImageStoreConnectionString voor alle opdrachten overeenkomen met de waarde die het cluster ServiceFabric gebruikt. U de ImageStoreConnectionString vinden in het clustermanifest, opgehaald met de opdrachten [Get-ServiceClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) en Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

De **get-ImageStoreConnectionStringFromClusterManifest-cmdlet,** die deel uitmaakt van de SDK PowerShell-module servicestructuur, wordt gebruikt om de verbindingstekenreeks voor de afbeeldingswinkel te krijgen.  Voer het als u de SDK-module wilt importeren:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


De ImageStoreConnectionString is te vinden in het clustermanifest:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Zie [De verbindingstekenreeks voor afbeeldingsarchiefen begrijpen](service-fabric-image-store-connection-string.md) voor aanvullende informatie over de verbindingstekenreeks voor afbeeldingsopslag en afbeeldingsarchief.

### <a name="deploy-large-application-package"></a>Groot toepassingspakket implementeren
Probleem: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API-tijden voor een groot toepassingspakket (volgorde van GB).
Proberen:
- Geef een grotere time-out op `timeout` voor de methode [CopyApplicationPackage,](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) met parameter. Standaard is de time-out 30 minuten.
- Controleer de netwerkverbinding tussen uw bronmachine en cluster. Als de verbinding traag is, u overwegen een machine met een betere netwerkverbinding te gebruiken.
Als de clientmachine zich in een andere regio dan het cluster bevindt, u overwegen een clientmachine te gebruiken in een dichtere of dezelfde regio als het cluster.
- Controleer of u externe beperking raakt. Wanneer het afbeeldingsarchief bijvoorbeeld is geconfigureerd om azure-opslag te gebruiken, kan het uploaden worden beperkt.

Probleem: Uploadpakket voltooid, maar [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API heeft een keer uit. Proberen:
- [Comprimeer het pakket](service-fabric-package-apps.md#compress-a-package) voordat u kopieert naar het afbeeldingsarchief.
De compressie vermindert de grootte en het aantal bestanden, wat op zijn beurt de hoeveelheid verkeer en werk vermindert die Service Fabric moet uitvoeren. De uploadbewerking kan trager zijn (vooral als u de compressietijd opneemt), maar het registreren en uitschrijven van het toepassingstype is sneller.
- Geef een grotere time-out op `timeout` voor [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API met parameter.

### <a name="deploy-application-package-with-many-files"></a>Toepassingspakket implementeren met veel bestanden
Probleem: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) heeft een tijd voor een aanvraagpakket met veel bestanden (order van duizenden).
Proberen:
- [Comprimeer het pakket](service-fabric-package-apps.md#compress-a-package) voordat u kopieert naar het afbeeldingsarchief. De compressie vermindert het aantal bestanden.
- Geef een grotere time-out op `timeout` voor [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) met parameter.

## <a name="code-example"></a>Voorbeeld van code
In het volgende voorbeeld wordt een toepassingspakket naar het afbeeldingsarchief gekopieerd en wordt het toepassingstype opgenomen. Vervolgens maakt het voorbeeld een toepassingsinstantie en maakt u een service-instantie. Ten slotte verwijdert het voorbeeld de toepassingsinstantie, ontziet u het toepassingstype en verwijdert u het toepassingspakket uit het afbeeldingsarchief.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Volgende stappen
[Upgrade van servicefabric-toepassingen](service-fabric-application-upgrade.md)

[Service Fabric health introduction](service-fabric-health-introduction.md)

[Een servicefabric-service diagnosticeren en oplossen](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Een toepassing modelleren in Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
