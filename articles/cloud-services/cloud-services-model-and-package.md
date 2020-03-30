---
title: Wat is een Cloud Service-model en -pakket | Microsoft Documenten
description: Beschrijft het cloudservicemodel (.csdef, .cscfg) en pakket (.cspkg) in Azure
services: cloud-services
author: tanmaygore
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 32603f4ab33e020245861e5dc66d2ade545fa627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247486"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Wat is het Cloud Service-model en hoe kan ik het verpakken?
Er wordt een cloudservice gemaakt uit drie componenten, de servicedefinitie *(.csdef),* de serviceconfig *(.cscfg)* en een servicepakket *(.cspkg)*. Zowel de **ServiceDefinition.csdef-** als **ServiceConfig.cscfg-bestanden** zijn op XML gebaseerd en beschrijven de structuur van de cloudservice en hoe deze is geconfigureerd; gezamenlijk het model genoemd. Het **ServicePackage.cspkg** is een zip-bestand dat wordt gegenereerd uit de **ServiceDefinition.csdef** en bevat onder andere alle vereiste afhankelijke afhankelijkheden op basis van binaire bestanden. Azure maakt een cloudservice van zowel **servicepakket.cspkg** als **serviceconfig.cscfg**.

Zodra de cloudservice in Azure wordt uitgevoerd, u deze opnieuw configureren via het **Bestand ServiceConfig.cscfg,** maar u de definitie niet wijzigen.

## <a name="what-would-you-like-to-know-more-about"></a>Waar wil je meer over weten?
* Ik wil meer weten over de [ServiceDefinition.csdef](#csdef) en [ServiceConfig.cscfg](#cscfg) bestanden.
* Ik weet al over dat, geef me [een aantal voorbeelden](#next-steps) over wat ik kan configureren.
* Ik wil de [ServicePackage.cspkg](#cspkg)maken.
* Ik gebruik Visual Studio en ik wil ...
  * [Een cloudservice maken][vs_create]
  * [Een bestaande cloudservice opnieuw configureren][vs_reconfigure]
  * [Een Cloud Service-project implementeren][vs_deploy]
  * [Extern bureaublad in een cloudservice-exemplaar][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
Het bestand **ServiceDefinition.csdef** geeft de instellingen op die door Azure worden gebruikt om een cloudservice te configureren. Het [Azure Service Definition Schema (.csdef File)](/previous-versions/azure/reference/ee758711(v=azure.100)) biedt de toegestane indeling voor een servicedefinitiebestand. In het volgende voorbeeld worden de instellingen weergegeven die kunnen worden gedefinieerd voor de web- en werknemersrollen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

U verwijzen naar het [Service Definition Schema](/previous-versions/azure/reference/ee758711(v=azure.100)) voor een beter begrip van het XML-schema dat hier wordt gebruikt, maar hier is een snelle uitleg van enkele van de elementen:

**Sites**  
Bevat de definities voor websites of webapplicaties die worden gehost in IIS7.

**Invoereindpunten**  
Bevat de definities voor eindpunten die worden gebruikt om contact op te nemen met de cloudservice.

**Interne eindpunten**  
Bevat de definities voor eindpunten die worden gebruikt door rolinstanties om met elkaar te communiceren.

**Configuratie-instellingen**  
Bevat de instellingsdefinities voor functies van een specifieke rol.

**Certificaten**  
Bevat de definities voor certificaten die nodig zijn voor een rol. In het vorige codevoorbeeld wordt een certificaat weergegeven dat wordt gebruikt voor de configuratie van Azure Connect.

**LocalResources (LocalResources)**  
Bevat de definities voor lokale opslagbronnen. Een lokale opslagbron is een gereserveerde map op het bestandssysteem van de virtuele machine waarin een instantie van een rol wordt uitgevoerd.

**Invoer**  
Bevat de definities voor geïmporteerde modules. In het vorige codevoorbeeld worden de modules voor Verbinding met extern bureaublad en Azure Connect weergegeven.

**Opstarten**  
Bevat taken die worden uitgevoerd wanneer de rol wordt gestart. De taken worden gedefinieerd in een .cmd of uitvoerbaar bestand.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
De configuratie van de instellingen voor uw cloudservice wordt bepaald door de waarden in het **bestand ServiceConfiguration.cscfg.** U geeft het aantal exemplaren op dat u voor elke rol in dit bestand wilt implementeren. De waarden voor de configuratie-instellingen die u hebt gedefinieerd in het servicedefinitiebestand, worden aan het serviceconfiguratiebestand toegevoegd. De duimafdrukken voor beheercertificaten die zijn gekoppeld aan de cloudservice, worden ook aan het bestand toegevoegd. Het [Azure Service Configuration Schema (.cscfg File)](/previous-versions/azure/reference/ee758710(v=azure.100)) biedt de toegestane indeling voor een serviceconfiguratiebestand.

Het serviceconfiguratiebestand is niet verpakt met de toepassing, maar wordt als afzonderlijk bestand naar Azure geüpload en wordt gebruikt om de cloudservice te configureren. U een nieuw serviceconfiguratiebestand uploaden zonder uw cloudservice opnieuw te implementeren. De configuratiewaarden voor de cloudservice kunnen worden gewijzigd terwijl de cloudservice wordt uitgevoerd. In het volgende voorbeeld worden de configuratie-instellingen weergegeven die kunnen worden gedefinieerd voor de web- en werknemersrollen:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

U verwijzen naar het [serviceconfiguratieschema](/previous-versions/azure/reference/ee758710(v=azure.100)) voor een beter begrip van het XML-schema dat hier wordt gebruikt, maar hier is een snelle uitleg van de elementen:

**exemplaren**  
Hiermee configureert u het aantal lopende instanties voor de rol. Om te voorkomen dat uw cloudservice mogelijk niet meer beschikbaar is tijdens upgrades, wordt aanbevolen om meer dan één exemplaar van uw webgerichte rollen te implementeren. Door meer dan één exemplaar te implementeren, houdt u zich aan de richtlijnen in de [SLA (Azure Compute Service Level Agreement),](https://azure.microsoft.com/support/legal/sla/)die 99,95% externe connectiviteit garandeert voor naar internet gerichte rollen wanneer twee of meer rolexemplaren worden geïmplementeerd voor een service.

**Configuratie-instellingen**  
Hiermee configureert u de instellingen voor de lopende instanties voor een rol. De naam `<Setting>` van de elementen moet overeenkomen met de instellingsdefinities in het servicedefinitiebestand.

**Certificaten**  
Hiermee configureert u de certificaten die door de service worden gebruikt. In het vorige codevoorbeeld ziet u hoe u het certificaat voor de RemoteAccess-module definieert. De waarde van het *kenmerk duimafdruk* moet worden ingesteld op de duimafdruk van het te gebruiken certificaat.

<p/>

> [!NOTE]
> De duimafdruk voor het certificaat kan met behulp van een teksteditor aan het configuratiebestand worden toegevoegd. De waarde kan ook worden toegevoegd op het tabblad **Certificaten** van de pagina **Eigenschappen** van de rol in Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Poorten definiëren voor rolexemplaren
Azure staat slechts één toegangspunt toe tot een webrol. Dit betekent dat al het verkeer plaatsvindt via één IP-adres. U uw websites configureren om een poort te delen door de hostheader te configureren om het verzoek naar de juiste locatie te leiden. U uw toepassingen ook configureren om te luisteren naar bekende poorten op het IP-adres.

In het volgende voorbeeld ziet u de configuratie voor een webrol met een website en webtoepassing. De website is geconfigureerd als de standaardinvoerlocatie op poort 80 en de webtoepassingen zijn geconfigureerd om aanvragen te ontvangen van een alternatieve hostheader die "mail.mysite.cloudapp.net" wordt genoemd.

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>De configuratie van een rol wijzigen
U de configuratie van uw cloudservice bijwerken terwijl deze wordt uitgevoerd in Azure, zonder de service offline te halen. Als u configuratiegegevens wilt wijzigen, u een nieuw configuratiebestand uploaden of het configuratiebestand bewerken en toepassen op uw hardloopservice. De volgende wijzigingen kunnen worden aangebracht in de configuratie van een service:

* **De waarden van configuratie-instellingen wijzigen**  
  Wanneer een configuratie-instelling verandert, kan een rolinstantie ervoor kiezen om de wijziging toe te passen terwijl de instantie online is, of om de instantie op een elegante manier te recyclen en de wijziging toe te passen terwijl de instantie offline is.
* **De servicetopologie van rolinstanties wijzigen**  
  Wijzigingen in de topologie hebben geen invloed op lopende instanties, behalve wanneer een instantie wordt verwijderd. Alle overige instanties hoeven over het algemeen niet te worden gerecycled; U er echter voor kiezen om rolinstanties te recyclen als reactie op een topologiewijziging.
* **De duimafdruk van het certificaat wijzigen**  
  U een certificaat alleen bijwerken als een rolinstantie offline is. Als een certificaat wordt toegevoegd, verwijderd of gewijzigd terwijl een rolinstantie online is, neemt Azure de instantie op sierlijke wijze offline om het certificaat bij te werken en weer online te brengen nadat de wijziging is voltooid.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Configuratiewijzigingen verwerken met serviceruntimegebeurtenissen
De [Azure Runtime-bibliotheek](/previous-versions/azure/reference/mt419365(v=azure.100)) bevat de naamruimte [Microsoft.WindowsAzure.ServiceRuntime,](/previous-versions/azure/reference/ee741722(v=azure.100)) die klassen biedt voor interactie met de Azure-omgeving vanuit een rol. In de klasse [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) worden de volgende gebeurtenissen gedefinieerd die voor en na een configuratiewijziging worden verhoogd:

* **[Gebeurtenis wijzigen](/previous-versions/azure/reference/ee758134(v=azure.100))**  
  Dit gebeurt voordat de configuratiewijziging wordt toegepast op een opgegeven instantie van een rol, waardoor u de kans krijgt om de rolinstanties zo nodig te uitschakelen.
* **[Gewijzigde](/previous-versions/azure/reference/ee758129(v=azure.100)) gebeurtenis**  
  Treedt op nadat de configuratiewijziging is toegepast op een opgegeven instantie van een rol.

> [!NOTE]
> Omdat certificaatwijzigingen de exemplaren van een rol altijd offline halen, verhogen ze de gebeurtenissen RoleEnvironment.Changing of RoleEnvironment.Changed niet.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
> [!NOTE]
> De maximale pakketgrootte die kan worden geïmplementeerd is 600MB

Als u een toepassing als cloudservice in Azure wilt implementeren, moet u de toepassing eerst in de juiste indeling verpakken. U het **opdrachtregelgereedschap CSPack** (geïnstalleerd met de [Azure SDK)](https://azure.microsoft.com/downloads/)gebruiken om het pakketbestand te maken als alternatief voor Visual Studio.

**CSPack** gebruikt de inhoud van het servicedefinitiebestand en het serviceconfiguratiebestand om de inhoud van het pakket te definiëren. **CSPack** genereert een toepassingspakketbestand (.cspkg) dat u naar Azure uploaden met behulp van de [Azure-portal.](cloud-services-how-to-create-deploy-portal.md#create-and-deploy) Standaard heeft het pakket `[ServiceDefinitionFileName].cspkg`de naam , maar u `/out` kunt een andere naam opgeven met behulp van de optie **CSPack**.

**CSPack** bevindt zich op  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (op windows) is beschikbaar door de snelkoppeling **Microsoft Azure Opdrachtprompt** uit te voeren die met de SDK is geïnstalleerd.  
> 
> Voer het CSPack.exe-programma zelf uit om documentatie te bekijken over alle mogelijke switches en opdrachten.
> 
> 

<p />

> [!TIP]
> Voer uw cloudservice lokaal uit in de **Microsoft Azure Compute Emulator**, gebruik de optie **/copyonly.** Met deze optie worden de binaire bestanden voor de toepassing gekopieerd naar een mapindeling van waaruit ze kunnen worden uitgevoerd in de compute emulator.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Voorbeeldopdracht voor het verpakken van een cloudservice
In het volgende voorbeeld wordt een toepassingspakket gemaakt dat de informatie voor een webrol bevat. De opdracht geeft het te gebruiken servicedefinitiebestand op, de map waar binaire bestanden kunnen worden gevonden en de naam van het pakketbestand.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Als de toepassing zowel een webrol als een werknemersrol bevat, wordt de volgende opdracht gebruikt:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Wanneer de variabelen als volgt worden gedefinieerd:

| Variabele | Waarde |
| --- | --- |
| \[DirectoryName\] |De submap onder de hoofdprojectmap die het .csdef-bestand van het Azure-project bevat. |
| \[ServiceDefinitie\] |De naam van het servicedefinitiebestand. Standaard heeft dit bestand de naam ServiceDefinition.csdef. |
| \[UitvoerBestandsnaam\] |De naam voor het gegenereerde pakketbestand. Dit is meestal ingesteld op de naam van de toepassing. Als er geen bestandsnaam is opgegeven, \[wordt\]het toepassingspakket gemaakt als ApplicationName .cspkg. |
| \[RoleName\] |De naam van de rol zoals gedefinieerd in het servicedefinitiebestand. |
| \[RoleBinariesDirectory] |De locatie van de binaire bestanden voor de rol. |
| \[Virtueel pad\] |De fysieke mappen voor elk virtueel pad gedefinieerd in de sectie Sites van de servicedefinitie. |
| \[PhysicalPath\] |De fysieke mappen van de inhoud voor elk virtueel pad dat is gedefinieerd in het siteknooppunt van de servicedefinitie. |
| \[Naam voor taakvergadering\] |De naam van het binaire bestand voor de rol. |

## <a name="next-steps"></a>Volgende stappen
Ik ben het creëren van een cloud service pakket en ik wil ...

* [Extern bureaublad instellen voor een cloudservice-exemplaar][remotedesktop]
* [Een Cloud Service-project implementeren][deploy]

Ik gebruik Visual Studio en ik wil ...

* [Een nieuwe cloudservice maken][vs_create]
* [Een bestaande cloudservice opnieuw configureren][vs_reconfigure]
* [Een Cloud Service-project implementeren][vs_deploy]
* [Extern bureaublad instellen voor een cloudservice-exemplaar][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md



