---
title: Azure Cloud Services-apps converteren naar Service Fabric
description: In deze handleiding worden statusloze services voor cloudservices voor web- en werknemersrollen en servicefabric's vergeleken om te helpen migreren van Cloud Services naar Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: caf067f793ca2086bc068907e86a82266627d128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463341"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Handleiding voor het converteren van web- en werknemersrollen naar servicefabric-statusloze services
In dit artikel wordt beschreven hoe u uw web- en werknemersrollen van Cloud Services migreert naar stateloze services van Service Fabric. Dit is het eenvoudigste migratiepad van Cloud Services naar Service Fabric voor toepassingen waarvan de algehele architectuur ongeveer hetzelfde zal blijven.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Cloud Service-project naar Service Fabric-toepassingsproject
 Een Cloud Service-project en een Service Fabric Application-project hebben een vergelijkbare structuur en vertegenwoordigen beide de implementatie-eenheid voor uw toepassing, dat wil zeggen dat ze elk het volledige pakket definiëren dat wordt geïmplementeerd om uw toepassing uit te voeren. Een Cloud Service-project bevat een of meer web- of werknemersrollen. Op dezelfde manier bevat een Service Fabric Application-project een of meer services. 

Het verschil is dat het Cloud Service-project de implementatie van de toepassing koppelt aan een VM-implementatie en er dus VM-configuratie-instellingen in bevat, terwijl het project Service Fabric Application alleen een toepassing definieert die wordt geïmplementeerd op een reeks bestaande VM's in een cluster van ServiceFabric. Het cluster Servicefabric zelf wordt slechts één keer geïmplementeerd, via een Resource Manager-sjabloon of via de Azure-portal, en er kunnen meerdere Service Fabric-toepassingen worden geïmplementeerd.

![Service Fabric en Cloud Services projectvergelijking][3]

## <a name="worker-role-to-stateless-service"></a>Werknemerrol naar staatloze service
Conceptueel vertegenwoordigt een werkfunctie een statusloze werkbelasting, wat betekent dat elk exemplaar van de werkbelasting identiek is en aanvragen op elk gewenst moment naar elke instantie kunnen worden doorgestuurd. Van elk exemplaar wordt niet verwacht dat het vorige verzoek wordt onthouden. Staat dat de werkbelasting werkt op wordt beheerd door een externe status opslag, zoals Azure Table Storage of Azure Cosmos DB. In Service Fabric wordt dit type werkbelasting vertegenwoordigd door een stateless service. De eenvoudigste benadering voor het migreren van een werknemersrol naar servicefabric kan worden gedaan door de functiecode van werknemers om te zetten in een service zonder staat.

![Rol van werknemer naar staatloze service][4]

## <a name="web-role-to-stateless-service"></a>Webrol naar stateloze service
Similar to Worker Role, a Web Role also represents a stateless workload, and so conceptually it too can be mapped to a Service Fabric stateless service. In tegenstelling tot webrollen ondersteunt Service Fabric echter geen IIS. Als u een webtoepassing wilt migreren van een webrol naar een stateloze service, moet u eerst overstappen naar een webframework dat zelf gehost kan worden en niet afhankelijk is van IIS of System.Web, zoals ASP.NET Core 1.

| **Toepassing** | **Ondersteund** | **Migratiepad** |
| --- | --- | --- |
| webformulieren ASP.NET |Nee |Converteren naar ASP.NET Core 1 MVC |
| ASP.NET MVC |Met migratie |Upgraden naar ASP.NET Core 1 MVC |
| ASP.NET Web-API |Met migratie |Gebruik zelfgehoste server of ASP.NET Core 1 |
| ASP.NET Core 1 |Ja |N.v.t. |

## <a name="entry-point-api-and-lifecycle"></a>Entry point API en levenscyclus
Worker Role and Service Fabric-service API's bieden vergelijkbare toegangspunten: 

| **Ingangspunt** | **Rol van werknemer** | **Service Fabric-service** |
| --- | --- | --- |
| Wordt verwerkt |`Run()` |`RunAsync()` |
| VM-start |`OnStart()` |N.v.t. |
| VM-stop |`OnStop()` |N.v.t. |
| Listener openen voor clientaanvragen |N.v.t. |<ul><li> `CreateServiceInstanceListener()`voor staatloze</li><li>`CreateServiceReplicaListener()`voor statige</li></ul> |

### <a name="worker-role"></a>Rol van werknemer
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Service Fabric Stateless Service
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Beide hebben een primaire "Run" override waarin om te beginnen met de verwerking. Service `Run`Fabric-services `Start`combineren `Stop` , en in `RunAsync`één ingangspunt, . Uw service moet `RunAsync` beginnen te werken wanneer `RunAsync` deze wordt gestart en moet stoppen met werken wanneer het CancellationToken van de methode wordt gesignaleerd. 

Er zijn verschillende belangrijke verschillen tussen de levenscyclus en levensduur van Worker Roles en Service Fabric-services:

* **Levenscyclus:** Het grootste verschil is dat een werknemersrol een VM is en dus de levenscyclus ervan is gekoppeld aan de VM, inclusief gebeurtenissen voor wanneer de VM wordt gestart en gestopt. Een Service Fabric-service heeft een levenscyclus die losstaat van de VM-levenscyclus, dus er zijn geen gebeurtenissen opgenomen voor wanneer de host-VM of -machine wordt gestart en gestopt, omdat deze niet gerelateerd zijn.
* **Levensduur:** Een instantie Worker Role `Run` wordt gerecycled als de methode wordt afgesloten. De `RunAsync` methode in een Service Fabric-service kan echter worden uitgevoerd tot voltooiing en de service-instantie blijft overeind. 

Service Fabric biedt een optioneel toegangspunt voor communicatie-instellingen voor services die naar clientverzoeken luisteren. Zowel het RunAsync- als het communicatie-invoerpunt zijn optionele overschrijvingen in Service Fabric-services - uw service kan ervoor kiezen om alleen naar clientaanvragen te luisteren of alleen een verwerkingslus of beide uit te voeren - daarom mag de RunAsync-methode worden afgesloten zonder opnieuw te starten de service-instantie, omdat deze kan blijven luisteren naar clientverzoeken.

## <a name="application-api-and-environment"></a>API en omgeving voor toepassingen
De API voor de Cloud Services-omgeving biedt informatie en functionaliteit voor de huidige VM-instantie en informatie over andere VM-rolexemplaren. Service Fabric biedt informatie met betrekking tot de runtime en sommige informatie over het knooppunt waarop een service momenteel wordt uitgevoerd. 

| **Omgevingstaak** | **Cloudservices** | **Service Fabric** |
| --- | --- | --- |
| Configuratie-instellingen en wijzigingsmelding |`RoleEnvironment` |`CodePackageActivationContext` |
| Lokale opslag |`RoleEnvironment` |`CodePackageActivationContext` |
| Eindpuntgegevens |`RoleInstance` <ul><li>Huidige instantie:`RoleEnvironment.CurrentRoleInstance`</li><li>Andere rollen en instantie:`RoleEnvironment.Roles`</li> |<ul><li>`NodeContext`voor het huidige knooppuntadres</li><li>`FabricClient`en `ServicePartitionResolver` voor serviceeindpuntdetectie</li> |
| Milieu-emulatie |`RoleEnvironment.IsEmulated` |N.v.t. |
| Gebeurtenis gelijktijdige wijziging |`RoleEnvironment` |N.v.t. |

## <a name="configuration-settings"></a>Configuratie-instellingen
Configuratie-instellingen in Cloud Services zijn ingesteld voor een VM-rol en zijn van toepassing op alle exemplaren van die VM-rol. Deze instellingen zijn sleutelwaardeparen die zijn ingesteld in ServiceConfiguration.*.cscfg-bestanden en zijn rechtstreeks toegankelijk via RoleEnvironment. In Service Fabric zijn instellingen afzonderlijk van toepassing op elke service en op elke toepassing, in plaats van op een VM, omdat een VM meerdere services en toepassingen kan hosten. Een dienst bestaat uit drie pakketten:

* **Code:** bevat de service-uitvoerbare bestanden, binaire bestanden, DLL's en andere bestanden die een service moet uitvoeren.
* **Config:** alle configuratiebestanden en -instellingen voor een service.
* **Gegevens:** statische gegevensbestanden die aan de service zijn gekoppeld.

Elk van deze pakketten kan onafhankelijk worden geversioned en geüpgraded. Net als bij Cloud Services kan een config-pakket programmatisch worden benaderd via een API en zijn gebeurtenissen beschikbaar om de service op de hoogte te stellen van een config-pakketwijziging. Een bestand Settings.xml kan worden gebruikt voor configuratie met sleutelwaarde en programmatische toegang, vergelijkbaar met het gedeelte app-instellingen van een App.config-bestand. In tegenstelling tot Cloud Services kan een Config-pakket van Service Fabric echter alle configuratiebestanden in elke indeling bevatten, of het nu XML, JSON, YAML of een aangepaste binaire indeling is. 

### <a name="accessing-configuration"></a>Toegang tot configuratie
#### <a name="cloud-services"></a>Cloudservices
Configuratie-instellingen van ServiceConfiguration.*.cscfg zijn `RoleEnvironment`toegankelijk via . Deze instellingen zijn wereldwijd beschikbaar voor alle rolinstanties in dezelfde Cloud Service-implementatie.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Elke service heeft zijn eigen individuele configuratiepakket. Er is geen ingebouwd mechanisme voor algemene configuratie-instellingen die toegankelijk zijn voor alle toepassingen in een cluster. Wanneer u het speciale configuratiebestand Settings.xml van Service Fabric gebruikt in een configuratiepakket, kunnen waarden in Settings.xml op toepassingsniveau worden overschreven, waardoor configuratie-instellingen op toepassingsniveau mogelijk zijn.

Configuratie-instellingen zijn toegangen binnen elke service-instantie via de `CodePackageActivationContext`service.

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Configuratie-updategebeurtenissen
#### <a name="cloud-services"></a>Cloudservices
De `RoleEnvironment.Changed` gebeurtenis wordt gebruikt om alle rolinstanties op de hoogte te stellen wanneer een wijziging plaatsvindt in de omgeving, zoals een configuratiewijziging. Dit wordt gebruikt om configuratie-updates te gebruiken zonder rolexemplaren te recyclen of een werkproces opnieuw te starten.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Elk van de drie pakkettypen in een service - Code, Config en Gegevens - heeft gebeurtenissen die een service-instantie op de hoogte brengen wanneer een pakket wordt bijgewerkt, toegevoegd of verwijderd. Een service kan meerdere pakketten van elk type bevatten. Een service kan bijvoorbeeld meerdere config-pakketten hebben, elk afzonderlijk geversioneerd en uitbreidbaar. 

Deze gebeurtenissen zijn beschikbaar om wijzigingen in servicepakketten te verwerken zonder de service-instantie opnieuw te starten.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Opstarttaken
Opstarttaken zijn acties die worden uitgevoerd voordat een toepassing wordt gestart. Een opstarttaak wordt meestal gebruikt om installatiescripts uit te voeren met verhoogde bevoegdheden. Zowel Cloud Services als Service Fabric ondersteunen opstarttaken. Het belangrijkste verschil is dat in Cloud Services een opstarttaak is gekoppeld aan een VM omdat deze deel uitmaakt van een rolinstantie, terwijl in Service Fabric een opstarttaak is gekoppeld aan een service die niet is gekoppeld aan een bepaalde VM.

| Service Fabric | Cloudservices |
| --- | --- |
| Configuratielocatie |ServiceDefinition.csdef |
| Bevoegdheden |"beperkt" of "verheven" |
| Sequentiëren |"eenvoudig", "achtergrond", "voorgrond" |

### <a name="cloud-services"></a>Cloudservices
In Cloud Services wordt een opstartpunt per rol geconfigureerd in ServiceDefinition.csdef. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
In ServiceFabric wordt een opstartitem per service geconfigureerd in ServiceManifest.xml:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>Een opmerking over ontwikkelingsomgeving
Zowel Cloud Services als Service Fabric zijn geïntegreerd met Visual Studio met projectsjablonen en ondersteuning voor het opsporen, configureren en implementeren van zowel lokaal als naar Azure. Zowel Cloud Services als Service Fabric bieden ook een lokale runtime-omgeving voor ontwikkeling. Het verschil is dat terwijl de runtime van de Cloud Service-ontwikkeling de Azure-omgeving emuleert waarop deze wordt uitgevoerd, Service Fabric geen emulator gebruikt - het maakt gebruik van de volledige runtime van Service Fabric. De Service Fabric-omgeving die u op uw lokale ontwikkelingsmachine uitvoert, is dezelfde omgeving die in productie wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen
Lees meer over Service Fabric Reliable Services en de fundamentele verschillen tussen cloudservices en servicefabric-toepassingsarchitectuur om te begrijpen hoe u profiteren van de volledige set servicefabric-functies.

* [Aan de slag met Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Conceptuele handleiding voor de verschillen tussen Cloud Services en Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
