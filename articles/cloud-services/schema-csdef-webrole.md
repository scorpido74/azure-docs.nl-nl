---
title: Azure Cloud Services Def. WebRole-schema | Microsoft Documenten
description: Azure-webrol is aangepast voor webtoepassingsprogrammering ter ondersteuning van ASP.NET, PHP, WCF en FastCGI. Meer informatie over elementen van servicedefinitie van een webrol.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 85368e4e-a0db-4c02-8dbc-8e2928fa6091
caps.latest.revision: 60
author: tgore03
ms.author: tagore
ms.openlocfilehash: 4368bb38a280461fdd77348de60a0e5793ee9582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535677"
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Webrole-schema voor Azure Cloud Services-definitie
De Azure-webrol is een rol die is aangepast voor het programmeren van webtoepassingen zoals ondersteund door IIS 7, zoals ASP.NET, PHP, Windows Communication Foundation en FastCGI.

De standaardextensie voor het servicedefinitiebestand is .csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Basisservicedefinitieschema voor een webrol  
De basisindeling van een servicedefinitiebestand met een webrol is als volgt.

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
    <LocalResources>  
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    </LocalResources>  
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    <Runtime executionContext="[limited|elevated]">  
      <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
      </Environment>  
      <EntryPoint>  
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>  
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
        <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
        </Environment>  
      </Task>  
    </Startup>  
    <Contents>  
      <Content destination="<destination-folder-name>" >  
        <SourceDirectory path="<local-source-directory>" />  
      </Content>  
    </Contents>  
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Schema-elementen  
Het servicedefinitiebestand bevat deze elementen, die in de volgende secties in dit onderwerp in detail worden beschreven:  

[WebRole](#WebRole)

[Configuratie-instellingen](#ConfigurationSettings)

[Instelling](#Setting)

[LocalResources (LocalResources)](#LocalResources)

[LokaalOpslag](#LocalStorage)

[Eindpunten](#Endpoints)

[Intern eindpunt](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[PublicPort toewijzen van](#AllocatePublicPortFrom)

[FixedPort (FixedPort)](#FixedPort)

[FixedPortRange FixedPortRange FixedPortRange FixedPort](#FixedPortRange)

[Certificaten](#Certificates)

[Certificaat](#Certificate)

[Invoer](#Imports)

[Importeren](#Import)

[Runtime](#Runtime)

[Omgeving](#Environment)

[Variabele](#Variable)

[RoleInstanceWaarde](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Sites](#Sites)

[Site](#Site)

[Virtuele toepassing](#VirtualApplication)

[Virtuele toepassing](#VirtualApplication)

[Bindingen](#Bindings)

[Binding](#Binding)

[Opstarten](#Startup)

[Taak](#Task)

[Inhoud](#Contents)

[Inhoud](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="webrole"></a><a name="WebRole"></a>WebRole  
Het `WebRole` element beschrijft een rol die is aangepast voor het programmeren van webtoepassingen, zoals ondersteund door IIS 7 en ASP.NET. Een service kan nul of meer webrollen bevatten.

In de volgende tabel worden `WebRole` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|name|tekenreeks|Vereist. De naam voor de webrol. De naam van de rol moet uniek zijn.|  
|enableNativeCodeExecution|booleaans|Optioneel. De standaardwaarde `true`is ; native code execution en full trust zijn standaard ingeschakeld. Stel dit `false` kenmerk in om de uitvoering van native code voor de webrol uit te schakelen en in plaats daarvan gedeeltelijke vertrouwensrelatie in Azure te gebruiken.|  
|vmsize|tekenreeks|Optioneel. Stel deze waarde in om de grootte van de virtuele machine die aan de rol is toegewezen, te wijzigen. De standaardwaarde is `Small`. Zie [Virtuele machineformaten voor Cloud Services voor](cloud-services-sizes-specs.md)meer informatie.|  

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a>Configuratie-instellingen  
Het `ConfigurationSettings` element beschrijft het verzamelen van configuratie-instellingen voor een webrol. Dit element is de `Setting` bovenliggende element.

##  <a name="setting"></a><a name="Setting"></a>Instelling  
Het `Setting` element beschrijft een naam- en waardepaar dat een configuratie-instelling voor een instantie van een rol opgeeft.

In de volgende tabel worden `Setting` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|name|tekenreeks|Vereist. Een unieke naam voor de configuratie-instelling.|  

De configuratie-instellingen voor een rol zijn naam- en waardeparen die zijn gedeclareerd in het servicedefinitiebestand en zijn ingesteld in het serviceconfiguratiebestand.

##  <a name="localresources"></a><a name="LocalResources"></a>LocalResources (LocalResources)  
Het `LocalResources` element beschrijft het verzamelen van lokale opslagbronnen voor een webrol. Dit element is de `LocalStorage` bovenliggende element.

##  <a name="localstorage"></a><a name="LocalStorage"></a>LokaalOpslag  
Het `LocalStorage` element identificeert een lokale opslagbron die ruimte biedt aan het bestandssysteem voor de service tijdens runtime. Een rol kan nul of meer lokale opslagbronnen definiëren.

> [!NOTE]
>  Het `LocalStorage` element kan worden weergegeven `WebRole` als een onderliggend element van het element ter ondersteuning van compatibiliteit met eerdere versies van de Azure SDK.

In de volgende tabel worden `LocalStorage` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|name|tekenreeks|Vereist. Een unieke naam voor de lokale winkel.|  
|cleanOnRoleRecycle|booleaans|Optioneel. Geeft aan of de lokale winkel moet worden schoongemaakt wanneer de rol opnieuw wordt opgestart. Standaardwaarde `true`is .|  
|sizeInMb|int|Optioneel. De gewenste hoeveelheid opslagruimte toe te wijzen voor de lokale winkel, in MB. Als dit niet is opgegeven, is de toegewezen standaardopslagruimte 100 MB. De minimale hoeveelheid opslagruimte die kan worden toegewezen is 1 MB.<br /><br /> De maximale grootte van de lokale resources is afhankelijk van de grootte van de virtuele machine. Zie [Virtuele machineformaten voor Cloud Services voor](cloud-services-sizes-specs.md)meer informatie.|  
  
De naam van de map die is toegewezen aan de lokale opslagbron komt overeen met de waarde die is opgegeven voor het kenmerk naam.

##  <a name="endpoints"></a><a name="Endpoints"></a>Eindpunten  
Het `Endpoints` element beschrijft het verzamelen van invoer (externe), interne en instantieinvoereindpunten voor een rol. Dit element is de `InputEndpoint` `InternalEndpoint`bovenliggende `InstanceInputEndpoint` en elementen.

Invoer- en interne eindpunten worden afzonderlijk toegewezen. Een service kan in totaal 25 invoer-, interne en instantieinvoereindpunten hebben die kunnen worden toegewezen aan de 25 rollen die in een service zijn toegestaan. Als u bijvoorbeeld 5 rollen hebt, u 5 invoereindpunten per rol toewijzen of u 25 invoereindpunten toewijzen aan één rol of u elk 1 ingangseindpunt toewijzen aan 25 rollen.

> [!NOTE]
>  Voor elke geïmplementeerde rol is één instantie per rol vereist. De standaardvoorziening voor een abonnement is beperkt tot 20 cores en is dus beperkt tot 20 exemplaren van een rol. Als uw toepassing meer exemplaren vereist dan wordt verstrekt door de standaardinrichting zie [Facturering, Abonnementsbeheer en Quotaondersteuning](https://azure.microsoft.com/support/options/) voor meer informatie over het verhogen van uw quotum.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a>Invoereindpunt  
Het `InputEndpoint` element beschrijft een extern eindpunt van een webrol.

U meerdere eindpunten definiëren die een combinatie zijn van HTTP-, HTTPS-, UDP- en TCP-eindpunten. U elk poortnummer opgeven dat u kiest voor een invoereindpunt, maar de poortnummers die voor elke rol in de service zijn opgegeven, moeten uniek zijn. Als u bijvoorbeeld opgeeft dat een webrol poort 80 voor HTTP en poort 443 voor HTTPS gebruikt, u vervolgens opgeven dat een tweede webrol poort 8080 voor HTTP en poort 8043 voor HTTPS gebruikt.

In de volgende tabel worden `InputEndpoint` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|name|tekenreeks|Vereist. Een unieke naam voor het externe eindpunt.|  
|Protocol|tekenreeks|Vereist. Het transportprotocol voor het externe eindpunt. Voor een webrol zijn `HTTP` `HTTPS`mogelijke `UDP`waarden `TCP`, , of .|  
|poort|int|Vereist. De poort voor het externe eindpunt. U elk poortnummer opgeven dat u kiest, maar de poortnummers die voor elke rol in de service zijn opgegeven, moeten uniek zijn.<br /><br /> Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1.7 of hoger).|  
|certificaat|tekenreeks|Vereist voor een HTTPS-eindpunt. De naam van een certificaat `Certificate` dat door een element is gedefinieerd.|  
|localPort|int|Optioneel. Hiermee geeft u een poort op die wordt gebruikt voor interne verbindingen op het eindpunt. Het `localPort` kenmerk brengt de externe poort op het eindpunt in kaart met een interne poort in een rol. Dit is handig in scenario's waarin een rol moet communiceren met een interne component op een poort die verschilt van de poort die extern wordt blootgesteld.<br /><br /> Als dit niet is `localPort` opgegeven, is `port` de waarde van het kenmerk gelijk aan het kenmerk. Stel de `localPort` waarde in op '*' om automatisch een niet-toegewezen poort toe te wijzen die detecteerbaar is met behulp van de runtime-API.<br /><br /> Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1.7 of hoger).<br /><br /> Het `localPort` kenmerk is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.|  
|ignoreRoleInstanceStatus|booleaans|Optioneel. Wanneer de waarde van dit `true`kenmerk is ingesteld op , wordt de status van een service genegeerd en wordt het eindpunt niet verwijderd door de load balancer. Deze waarde `true` instellen op nuttig voor het opsporen van drukke exemplaren van een service. De standaardwaarde is `false`. **Let op:**  Een eindpunt kan nog steeds verkeer ontvangen, zelfs als de rol niet in de status Klaar is.|  
|loadBalancerProbe|tekenreeks|Optioneel. De naam van de lastensonde die is gekoppeld aan het invoereindpunt. Zie [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md)voor meer informatie .|  

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a>Intern eindpunt  
Het `InternalEndpoint` element beschrijft een intern eindpunt voor een webrol. Een intern eindpunt is alleen beschikbaar voor andere rolexemplaren die binnen de service worden uitgevoerd. het is niet beschikbaar voor klanten buiten de service. Webrollen die het `Sites` element niet bevatten, kunnen slechts één intern HTTP-, UDP- of TCP-intern eindpunt hebben.

In de volgende tabel worden `InternalEndpoint` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|name|tekenreeks|Vereist. Een unieke naam voor het interne eindpunt.|  
|Protocol|tekenreeks|Vereist. Het transportprotocol voor het interne eindpunt. Mogelijke waarden `HTTP` `TCP`zijn `UDP`, `ANY`, of .<br /><br /> Een waarde `ANY` van geeft aan dat elk protocol, elke poort is toegestaan.|  
|poort|int|Optioneel. De poort die wordt gebruikt voor interne belastingsverbindingen op het eindpunt. Een laadpunt voor een gebalanceerd eindpunt voor laden gebruikt twee poorten. De poort die wordt gebruikt voor het openbare IP-adres en de poort die wordt gebruikt op het privé-IP-adres. Meestal zijn dit deze zijn ingesteld op hetzelfde, maar u ervoor kiezen om verschillende poorten te gebruiken.<br /><br /> Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1.7 of hoger).<br /><br /> Het `Port` kenmerk is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.|  

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a>InstanceInputEndpoint  
Het `InstanceInputEndpoint` element beschrijft een eindpunt voor instantieinvoer in een webrol. Een instantie-invoereindpunt is gekoppeld aan een specifieke rolinstantie door poortforwarding te gebruiken in de load balancer. Elk eindpunt voor instantieinvoer wordt toegewezen aan een specifieke poort vanuit een reeks mogelijke poorten. Dit element is de `AllocatePublicPortFrom` bovenliggende element.

Het `InstanceInputEndpoint` element is alleen beschikbaar met de Azure SDK-versie 1.7 of hoger.

In de volgende tabel worden `InstanceInputEndpoint` de kenmerken van het element beschreven.
  
| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|name|tekenreeks|Vereist. Een unieke naam voor het eindpunt.|  
|localPort|int|Vereist. Hiermee geeft u de interne poort op waarnaar alle rolinstanties luisteren om binnenkomend verkeer dat wordt doorgestuurd van de load balancer te ontvangen. Mogelijke waarden variëren tussen 1 en 65535, inclusief.|  
|Protocol|tekenreeks|Vereist. Het transportprotocol voor het interne eindpunt. Mogelijke waarden zijn `udp` en `tcp`. Gebruik `tcp` voor http/https gebaseerd verkeer.|  
  
##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a>PublicPort toewijzen van  
Het `AllocatePublicPortFrom` element beschrijft het openbare poortbereik dat door externe klanten kan worden gebruikt om toegang te krijgen tot elk eindpunt van de instantieinvoer. Het openbare (VIP)-poortnummer wordt vanuit dit bereik toegewezen en toegewezen aan elk eindpunt van afzonderlijke rolinstanties tijdens de implementatie en update van de tenant. Dit element is de `FixedPortRange` bovenliggende element.

Het `AllocatePublicPortFrom` element is alleen beschikbaar met de Azure SDK-versie 1.7 of hoger.

##  <a name="fixedport"></a><a name="FixedPort"></a>FixedPort (FixedPort)  
Het `FixedPort` element geeft de poort voor het interne eindpunt aan, waardoor load balanced connections op het eindpunt mogelijk zijn.

Het `FixedPort` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

In de volgende tabel worden `FixedPort` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|poort|int|Vereist. De poort voor het interne eindpunt. Dit heeft hetzelfde effect `FixedPortRange` als het instellen van de min en max op dezelfde poort.<br /><br /> Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1.7 of hoger).|  

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a>FixedPortRange FixedPortRange FixedPortRange FixedPort  
Het `FixedPortRange` element geeft het bereik aan van poorten die zijn toegewezen aan het eindpunt van het interne eindpunt of instantieinvoer en stelt de poort in die wordt gebruikt voor laadverbindingen op het eindpunt.

> [!NOTE]
>  Het `FixedPortRange` element werkt anders, afhankelijk van het element waarin het zich bevindt. Wanneer `FixedPortRange` het element `InternalEndpoint` zich in het element bevindt, worden alle poorten op de load balancer geopend binnen het bereik van de min- en max-kenmerken voor alle virtuele machines waarop de rol wordt uitgevoerd. Wanneer `FixedPortRange` het element `InstanceInputEndpoint` zich in het element bevindt, wordt slechts één poort geopend binnen het bereik van de min- en max-kenmerken op elke virtuele machine waarop de rol wordt uitgevoerd.

Het `FixedPortRange` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

In de volgende tabel worden `FixedPortRange` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|min.|int|Vereist. De minimale poort in het bereik. Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1.7 of hoger).|  
|Max|tekenreeks|Vereist. De maximale poort in het bereik. Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1.7 of hoger).|  

##  <a name="certificates"></a><a name="Certificates"></a>Certificaten  
Het `Certificates` element beschrijft het verzamelen van certificaten voor een webrol. Dit element is de `Certificate` bovenliggende element. Een rol kan een willekeurig aantal bijbehorende certificaten hebben. Zie [Het bestand Servicedefinitie wijzigen met een certificaat](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files)voor meer informatie over het gebruik van het certificaatelement.

##  <a name="certificate"></a><a name="Certificate"></a>Certificaat  
Het `Certificate` element beschrijft een certificaat dat is gekoppeld aan een webrol.

In de volgende tabel worden `Certificate` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|name|tekenreeks|Vereist. Een naam voor dit certificaat, die wordt gebruikt om ernaar `InputEndpoint` te verwijzen wanneer het is gekoppeld aan een HTTPS-element.|  
|storeLocatie|tekenreeks|Vereist. De locatie van het certificaatarchief waar dit certificaat op de lokale machine te vinden is. Mogelijke waarden `CurrentUser` `LocalMachine`zijn en .|  
|storeNaam|tekenreeks|Vereist. De naam van het certificaatarchief waar dit certificaat zich op de lokale machine bevindt. Mogelijke waarden zijn de ingebouwde `Root` `CA`winkelnamen `Disallowed` `My` `TrustedPeople`, `TrustedPublisher` `AuthRoot`, `AddressBook`, `Trust`, , , , of een aangepaste winkelnaam. Als een aangepaste winkelnaam is opgegeven, wordt de winkel automatisch gemaakt.|  
|machtigingNiveau|tekenreeks|Optioneel. Hiermee geeft u de toegangsmachtigingen op die aan de rolprocessen worden gegeven. Als u alleen verhoogde processen wilt hebben om toegang `elevated` te krijgen tot de privésleutel, geeft u toestemming op. `limitedOrElevated`met toestemming kunnen alle rolprocessen toegang krijgen tot de privésleutel. Mogelijke waarden zijn `limitedOrElevated` en `elevated`. De standaardwaarde is `limitedOrElevated`.|  

##  <a name="imports"></a><a name="Imports"></a>Invoer  
Het `Imports` element beschrijft een verzameling importmodules voor een webrol die componenten toevoegt aan het gastbesturingssysteem. Dit element is de `Import` bovenliggende element. Dit element is optioneel en een rol kan slechts één importblok hebben. 

Het `Imports` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

##  <a name="import"></a><a name="Import"></a>Importeren  
Het `Import` element geeft een module op die aan het gastbesturingssysteem moet worden toegevoegd.

Het `Import` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

In de volgende tabel worden `Import` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|moduleNaam|tekenreeks|Vereist. De naam van de module om te importeren. Geldige importmodules zijn:<br /><br /> - RemoteAccess<br />- RemoteForwarder<br />- Diagnostiek<br /><br /> Met de modules RemoteAccess en RemoteForwarder u uw rolinstantie configureren voor externe bureaubladverbindingen. Zie Verbinding [met extern bureaublad inschakelen](cloud-services-role-enable-remote-desktop-new-portal.md)voor meer informatie .<br /><br /> Met de module Diagnostische gegevens u diagnostische gegevens verzamelen voor een rolexemplaar.|  

##  <a name="runtime"></a><a name="Runtime"></a>Runtime  
Het `Runtime` element beschrijft een verzameling omgevingsvariabele instellingen voor een webrol die de runtime-omgeving van het Azure-hostproces beheert. Dit element is de `Environment` bovenliggende element. Dit element is optioneel en een rol kan slechts één runtimeblok hebben.

Het `Runtime` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

In de volgende tabel worden `Runtime` de kenmerken van het element beschreven:  

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|uitvoeringContext|tekenreeks|Optioneel. Hiermee geeft u de context op waarin het rolproces wordt gestart. De standaardcontext `limited`is .<br /><br /> -   `limited`– Het proces wordt gestart zonder beheerdersbevoegdheden.<br />-   `elevated`– Het proces wordt gestart met beheerdersbevoegdheden.|  

##  <a name="environment"></a><a name="Environment"></a>Milieu  
Het `Environment` element beschrijft een verzameling omgevingsvariabele instellingen voor een webrol. Dit element is de `Variable` bovenliggende element. Een rol kan een willekeurig aantal omgevingsvariabelen hebben ingesteld.

##  <a name="variable"></a><a name="Variable"></a>Variabele  
Het `Variable` element geeft een omgevingsvariabele op die u in de gastbediening wilt instellen.

Het `Variable` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

In de volgende tabel worden `Variable` de kenmerken van het element beschreven:  

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|name|tekenreeks|Vereist. De naam van de omgevingsvariabele die moet worden ingesteld.|  
|waarde|tekenreeks|Optioneel. De waarde die moet worden ingesteld voor de omgevingsvariabele. U moet een waardekenmerk `RoleInstanceValue` of een element opnemen.|  

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a>RoleInstanceWaarde  
Het `RoleInstanceValue` element geeft het xPath op waaruit de waarde van de variabele moet worden opgehaald.

In de volgende tabel worden `RoleInstanceValue` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|Xpath|tekenreeks|Optioneel. Locatiepad van implementatie-instellingen voor de instantie. Zie [Configuratievariabelen met XPath voor](cloud-services-role-config-xpath.md)meer informatie.<br /><br /> U moet een waardekenmerk `RoleInstanceValue` of een element opnemen.|  

##  <a name="entrypoint"></a><a name="EntryPoint"></a>EntryPoint  
Het `EntryPoint` element geeft het ingangspunt voor een rol aan. Dit element is de `NetFxEntryPoint` bovenliggende elementen. Met deze elementen u een andere toepassing opgeven dan de standaardWaWorkerHost.exe om als het invoerpunt voor rollen op te treden.

Het `EntryPoint` element is alleen beschikbaar met de Azure SDK-versie 1.5 of hoger.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a>NetFxEntryPoint  
Het `NetFxEntryPoint` element geeft het programma aan dat moet worden uitgevoerd voor een rol.

> [!NOTE]
>  Het `NetFxEntryPoint` element is alleen beschikbaar met de Azure SDK-versie 1.5 of hoger.

In de volgende tabel worden `NetFxEntryPoint` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|montageNaam|tekenreeks|Vereist. Het pad en de bestandsnaam van de assemblage die het ingangspunt bevatten. Het pad is relatief ten opzichte van de map `commandLine` ** \\%ROLEROOT%\Approot** (geef geen ** \\%ROLEROOT%\Approot** op in , wordt aangenomen). **%ROLEROOT%** is een omgevingsvariabele die wordt onderhouden door Azure en vertegenwoordigt de locatie van de hoofdmap voor uw rol. De map ** \\%ROLEROOT%\Approot** vertegenwoordigt de toepassingsmap voor uw rol.<br /><br /> Voor HWC-rollen is het pad altijd relatief ten opzichte van de ** \\map %ROLEROOT%\Approot\bin.**<br /><br /> Voor volledige IIS- en IIS Express-webrollen ** \\wordt** gezocht als de verzameling niet kan worden gevonden ten opzichte ** \\van %ROLEROOT%\Approot-**<br /><br /> Deze terugval gedrag voor volledige IIS is niet een aanbevolen best practice en misschien verwijderd in toekomstige versies.|  
|targetFrameworkVersion targetFrameworkVersion|tekenreeks|Vereist. De versie van het .NET framework waarop de assemblage is gebouwd. Bijvoorbeeld `targetFrameworkVersion="v4.0"`.|  

##  <a name="sites"></a><a name="Sites"></a>Sites  
Het `Sites` element beschrijft een verzameling websites en webtoepassingen die worden gehost in een webrol. Dit element is de `Site` bovenliggende element. Als u geen `Sites` element opgeeft, wordt uw webrol gehost als een verouderde webrol en u slechts één website ontvangen in uw webrol. Dit element is optioneel en een rol kan slechts één sitesblok hebben.

Het `Sites` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

##  <a name="site"></a><a name="Site"></a>Site  
Het `Site` element geeft een website of webtoepassing op die deel uitmaakt van de webrol.

Het `Site` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

In de volgende tabel worden `Site` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|name|tekenreeks|Vereist. Naam van de website of applicatie.|  
|physicalDirectory (physicalDirectory)|tekenreeks|De locatie van de inhoudsmap voor de siteroot. De locatie kan worden opgegeven als een absoluut pad of ten opzichte van de .csdef-locatie.|  

##  <a name="virtualapplication"></a><a name="VirtualApplication"></a>Virtuele toepassing  
Het `VirtualApplication` element definieert een toepassing in Internet Information Services (IIS) 7 is een groepering van bestanden die inhoud levert of services levert via protocollen, zoals HTTP. Wanneer u een toepassing maakt in IIS 7, wordt het pad van de toepassing onderdeel van de URL van de site.

Het `VirtualApplication` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

In de volgende tabel worden `VirtualApplication` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|name|tekenreeks|Vereist. Hiermee geeft u een naam op om de virtuele toepassing te identificeren.|  
|physicalDirectory (physicalDirectory)|tekenreeks|Vereist. Hiermee geeft u het pad op op de ontwikkelingsmachine dat de virtuele toepassing bevat. In de compute emulator is IIS geconfigureerd om inhoud van deze locatie op te halen. Bij het implementeren naar azure wordt de inhoud van de fysieke map samen met de rest van de service verpakt. Wanneer het servicepakket wordt geïmplementeerd in Azure, is IIS geconfigureerd met de locatie van de uitgepakte inhoud.|  

##  <a name="virtualdirectory"></a><a name="VirtualDirectory"></a>Virtuele map  
Het `VirtualDirectory` element geeft een mapnaam op (ook wel pad genoemd) die u opgeeft in IIS en wordt toegewezen aan een fysieke map op een lokale of externe server.

Het `VirtualDirectory` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

In de volgende tabel worden `VirtualDirectory` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|name|tekenreeks|Vereist. Hiermee geeft u een naam op om de virtuele map te identificeren.|  
|waarde|physicalDirectory (physicalDirectory)|Vereist. Hiermee geeft u het pad op op de ontwikkelingsmachine dat de inhoud van de website of virtuele map bevat. In de compute emulator is IIS geconfigureerd om inhoud van deze locatie op te halen. Bij het implementeren naar azure wordt de inhoud van de fysieke map samen met de rest van de service verpakt. Wanneer het servicepakket wordt geïmplementeerd in Azure, is IIS geconfigureerd met de locatie van de uitgepakte inhoud.|  

##  <a name="bindings"></a><a name="Bindings"></a>Bindings  
Het `Bindings` element beschrijft een verzameling bindingen voor een website. Het is het bovenliggende element van het `Binding` element. Het element is `Site` vereist voor elk element. Zie Communicatie inschakelen voor rolinstanties voor meer informatie over het configureren van [eindpunten.](cloud-services-enable-communication-role-instances.md)

Het `Bindings` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

##  <a name="binding"></a><a name="Binding"></a>Bindend  
Het `Binding` element geeft configuratiegegevens op die nodig zijn voor verzoeken om te communiceren met een website of webtoepassing.

Het `Binding` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|name|tekenreeks|Vereist. Hiermee geeft u een naam op om de binding te identificeren.|  
|endpointNaam|tekenreeks|Vereist. Hiermee geeft u de eindpuntnaam op waaraan u moet binden.|  
|hostHeader|tekenreeks|Optioneel. Hiermee geeft u een hostnaam op waarmee u meerdere sites, met verschillende hostnamen, hosten op één combinatie van IP-adres/poortnummer.|  

##  <a name="startup"></a><a name="Startup"></a>Opstarten  
Het `Startup` element beschrijft een verzameling taken die worden uitgevoerd wanneer de rol wordt gestart. Dit element kan de `Variable` bovenliggende partij van het element zijn. Zie [Opstarttaken configureren](cloud-services-startup-tasks.md)voor meer informatie over het gebruik van de taakopstarttaken. Dit element is optioneel en een rol kan slechts één opstartblok hebben.

In de volgende tabel wordt `Startup` het kenmerk van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|priority|int|Alleen voor intern gebruik.|  

##  <a name="task"></a><a name="Task"></a>Taak  
Het `Task` element geeft opstarttaak op die plaatsvindt wanneer de rol wordt gestart. Opstarttaken kunnen worden gebruikt om taken uit te voeren die de rol voorbereiden om dergelijke softwarecomponenten te installeren of andere toepassingen uit te voeren. Taken worden uitgevoerd in de volgorde `Startup` waarin ze worden weergegeven in het elementblok.

Het `Task` element is alleen beschikbaar met de Azure SDK-versie 1.3 of hoger.

In de volgende tabel worden `Task` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|Commandline|tekenreeks|Vereist. Een script, zoals een CMD-bestand, met de opdrachten die moeten worden uitgevoerd. Opstartopdracht- en batchbestanden moeten worden opgeslagen in ansi-indeling. Bestandsindelingen die aan het begin van het bestand een markering voor byte-order instellen, worden niet goed verwerkt.|  
|uitvoeringContext|tekenreeks|Hiermee geeft u de context op waarin het script wordt uitgevoerd.<br /><br /> -   `limited`[Standaard] – Uitvoeren met dezelfde bevoegdheden als de rol die het proces host.<br />-   `elevated`– Uitvoeren met beheerdersbevoegdheden.|  
|taskType|tekenreeks|Hiermee geeft u het uitvoeringsgedrag van de opdracht op.<br /><br /> -   `simple`[Standaard] – Systeem wacht tot de taak wordt afgesloten voordat andere taken worden gestart.<br />-   `background`– Systeem wacht niet tot de taak wordt afgesloten.<br />-   `foreground`– Net als op de achtergrond wordt de rol niet opnieuw gestart totdat alle voorgrondtaken worden afgesloten.|  

##  <a name="contents"></a><a name="Contents"></a>Inhoud  
Het `Contents` element beschrijft de verzameling inhoud voor een webrol. Dit element is de `Content` bovenliggende element.

Het `Contents` element is alleen beschikbaar met de Azure SDK-versie 1.5 of hoger.

##  <a name="content"></a><a name="Content"></a>Inhoud  
Het `Content` element definieert de bronlocatie van inhoud die moet worden gekopieerd naar de virtuele Azure-machine en het doelpad waarnaar deze wordt gekopieerd.

Het `Content` element is alleen beschikbaar met de Azure SDK-versie 1.5 of hoger.

In de volgende tabel worden `Content` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|doel|tekenreeks|Vereist. Locatie op de virtuele Azure-machine waarop de inhoud wordt geplaatst. Deze locatie is relatief ten opzichte van de map **%ROLEROOT%\Approot**.|  

Dit element is het `SourceDirectory` bovenliggende element van het element.

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a>SourceDirectory  
Het `SourceDirectory` element definieert de lokale map waaruit inhoud wordt gekopieerd. Gebruik dit element om de lokale inhoud op te geven die naar de virtuele Azure-machine moet worden gekopieerd.

Het `SourceDirectory` element is alleen beschikbaar met de Azure SDK-versie 1.5 of hoger.

In de volgende tabel worden `SourceDirectory` de kenmerken van het element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|path|tekenreeks|Vereist. Relatief of absoluut pad van een lokale map waarvan de inhoud wordt gekopieerd naar de virtuele Azure-machine. Uitbreiding van omgevingsvariabelen in het mappad wordt ondersteund.|  
  
## <a name="see-also"></a>Zie ook
[Cloud Service (klassiek) definitieschema](schema-csdef-file.md)




