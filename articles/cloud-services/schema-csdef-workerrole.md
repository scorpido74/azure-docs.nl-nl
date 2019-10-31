---
title: Azure Cloud Services def. WorkerRole-schema | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 04/14/2015
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 41cd46bc-c479-43fa-96e5-d6c83e4e6d89
caps.latest.revision: 55
author: georgewallace
ms.author: gwallace
manager: gwallace
ms.openlocfilehash: 4ecad6ffc1b57feb16583fd95525a456f6e315a5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162097"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>WorkerRole-schema voor Azure Cloud Services definition
De rol Azure Worker is een rol die nuttig is voor algemene ontwikkeling en kan achtergrond verwerking uitvoeren voor een webrole.

De standaard extensie voor het service definitie bestand is. csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Basis-service definitie schema voor een worker-rol.
De basis indeling van het service definitie bestand met een werk rollen is als volgt.

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
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
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]"/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>">
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
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
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schema-elementen
Het service definitie bestand bevat deze elementen, zoals beschreven in de volgende secties in dit onderwerp:

[WorkerRole](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[Instelling](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Eind punten](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certificaten](#Certificates)

[Certificaat](#Certificate)

[Rusland](#Imports)

[Importeren](#Import)

[Gezamenlijke](#Runtime)

[Omgeving](#Environment)

[Toegangs](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Variabeletype](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Opstarten](#Startup)

[Taak](#Task)

[Gehele](#Contents)

[Inhoud](#Content)

[Source Directory](#SourceDirectory)

##  <a name="WorkerRole"></a>WorkerRole
Het `WorkerRole`-element beschrijft een rol die nuttig is voor algemene ontwikkeling en kan achtergrond verwerking uitvoeren voor een webrole. Een service kan nul of meer werk rollen bevatten.

In de volgende tabel worden de kenmerken van het element `WorkerRole` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|name|string|Vereist. De naam voor de worker-rol. De naam van de rol moet uniek zijn.|
|enableNativeCodeExecution|booleaans|Optioneel. De standaard waarde is `true`; uitvoering van systeem eigen code en volledig vertrouwen zijn standaard ingeschakeld. Stel dit kenmerk in op `false` om het uitvoeren van systeem eigen code voor de werknemersrol uit te scha kelen en in plaats daarvan Azure gedeeltelijk vertrouwen te gebruiken.|
|vmsize|string|Optioneel. Stel deze waarde in om de grootte te wijzigen van de virtuele machine die aan deze rol is toegewezen. De standaard waarde is `Small`. Zie [grootten van virtuele machines voor Cloud Services](cloud-services-sizes-specs.md)voor een lijst met mogelijke grootten voor virtuele machines en de bijbehorende kenmerken.|

##  <a name="ConfigurationSettings"></a>ConfigurationSettings
Het element `ConfigurationSettings` beschrijft de verzameling configuratie-instellingen voor een werk rollen. Dit element is de bovenliggende elementen van het `Setting`-element.

##  <a name="Setting"></a>Stelt
Het `Setting`-element beschrijft een naam en een waardepaar waarmee een configuratie-instelling voor een exemplaar van een rol wordt opgegeven.

In de volgende tabel worden de kenmerken van het element `Setting` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|name|string|Vereist. Een unieke naam voor de configuratie-instelling.|

De configuratie-instellingen voor een rol zijn naam-en waardeparen die in het service definitie bestand zijn gedeclareerd en in het service configuratie bestand zijn ingesteld.

##  <a name="LocalResources"></a>LocalResources
Het `LocalResources`-element beschrijft de verzameling van lokale opslag resources voor een werk rollen. Dit element is de bovenliggende elementen van het `LocalStorage`-element.

##  <a name="LocalStorage"></a>LocalStorage
Het `LocalStorage`-element identificeert een lokale opslag resource die bestandssysteem ruimte biedt voor de service tijdens runtime. Een rol kan nul of meer lokale opslag resources definiëren.

> [!NOTE]
>  Het `LocalStorage`-element kan worden weer gegeven als een onderliggend item van het `WorkerRole`-element ter ondersteuning van compatibiliteit met eerdere versies van de Azure SDK.

In de volgende tabel worden de kenmerken van het element `LocalStorage` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|name|string|Vereist. Een unieke naam voor het lokale archief.|
|cleanOnRoleRecycle|booleaans|Optioneel. Hiermee wordt aangegeven of het lokale archief moet worden gereinigd wanneer de rol opnieuw wordt gestart. De standaard waarde is `true`.|
|Sizeinmb kleiner|int|Optioneel. De gewenste hoeveelheid opslag ruimte die voor het lokale archief moet worden toegewezen, in MB. Als dat niet is opgegeven, is de standaard toegewezen opslag ruimte 100 MB. De minimale hoeveelheid opslag ruimte die kan worden toegewezen, is 1 MB.<br /><br /> De maximale grootte van de lokale resources is afhankelijk van de grootte van de virtuele machine. Zie [grootten van virtuele machines voor Cloud Services](cloud-services-sizes-specs.md)voor meer informatie.|

De naam van de map die is toegewezen aan de lokale opslag Resource komt overeen met de waarde die is gegeven voor het naam kenmerk.

##  <a name="Endpoints"></a>Eind punten
Het element `Endpoints` beschrijft de verzameling invoer-eind punten (extern), intern en exemplaar voor een rol. Dit element is de bovenliggende elementen van de `InputEndpoint`-, `InternalEndpoint`-en `InstanceInputEndpoint`-onderdelen.

De invoer en interne eind punten worden afzonderlijk toegewezen. Een service kan in totaal 25 invoer-, interne en exemplaar invoer-eind punten bevatten die kunnen worden toegewezen in de 25 rollen die in een service zijn toegestaan. Als u bijvoorbeeld vijf rollen hebt, kunt u vijf invoer eindpunten per rol toewijzen, of u kunt 25 invoer eindpunten toewijzen aan één rol, maar u kunt ook één invoer eindpunt toewijzen aan 25 rollen.

> [!NOTE]
>  Voor elke geïmplementeerde rol is één exemplaar per rol vereist. De standaard inrichting voor een abonnement is beperkt tot 20 kernen en is daarom beperkt tot 20 exemplaren van een rol. Als uw toepassing meer exemplaren vereist dan wordt geleverd door de standaard inrichting, Zie [facturering, abonnements beheer en quotum ondersteuning](https://azure.microsoft.com/support/options/) voor meer informatie over het verhogen van uw quotum.

##  <a name="InputEndpoint"></a>InputEndpoint
Het `InputEndpoint`-element beschrijft een extern eind punt naar een werknemersrol.

U kunt meerdere eind punten definiëren die een combi natie zijn van HTTP-, HTTPS-, UDP-en TCP-eind punten. U kunt elk poort nummer opgeven dat u voor een invoer eindpunt kiest, maar de opgegeven poort nummers voor elke rol in de service moeten uniek zijn. Als u bijvoorbeeld opgeeft dat een rol gebruikmaakt van poort 80 voor HTTP en poort 443 voor HTTPS, kunt u opgeven dat een tweede rol poort 8080 gebruikt voor HTTP en poort 8043 voor HTTPS.

In de volgende tabel worden de kenmerken van het element `InputEndpoint` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|name|string|Vereist. Een unieke naam voor het externe eind punt.|
|Protocolsubstatus|string|Vereist. Het transport protocol voor het externe eind punt. Voor een werk rollen zijn mogelijke waarden `HTTP`, `HTTPS`, `UDP`of `TCP`.|
|poort|int|Vereist. De poort voor het externe eind punt. U kunt een wille keurig poort nummer opgeven, maar de poort nummers die voor elke rol in de service zijn opgegeven, moeten uniek zijn.<br /><br /> Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1,7 of hoger).|
|certificaat|string|Vereist voor een HTTPS-eind punt. De naam van een certificaat dat is gedefinieerd door een `Certificate`-element.|
|localPort|int|Optioneel. Hiermee geeft u een poort op die wordt gebruikt voor interne verbindingen op het eind punt. Het kenmerk `localPort` wijst de externe poort van het eind punt toe aan een interne poort van een rol. Dit is handig in scenario's waarbij een rol moet communiceren met een intern onderdeel op een poort die afwijkt van de functie die extern wordt weer gegeven.<br /><br /> Als dat niet is opgegeven, is de waarde van `localPort` hetzelfde als het kenmerk `port`. Stel de waarde van `localPort` in op ' * ' om automatisch een niet-toegewezen poort toe te wijzen die kan worden gedetecteerd met behulp van de runtime-API.<br /><br /> Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1,7 of hoger).<br /><br /> Het kenmerk `localPort` is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.|
|ignoreRoleInstanceStatus|booleaans|Optioneel. Wanneer de waarde van dit kenmerk is ingesteld op `true`, wordt de status van een service genegeerd en wordt het eind punt niet verwijderd door de load balancer. Stel deze waarde in op `true` nuttig is voor het opsporen van fouten in de actieve instanties van een service. De standaard waarde is `false`. **Opmerking:** Een eind punt kan nog steeds verkeer ontvangen, zelfs wanneer de rol niet de status gereed heeft.|
|loadBalancerProbe|string|Optioneel. De naam van de load balancer-test die is gekoppeld aan het invoer eindpunt. Zie [LoadBalancerProbe-schema](schema-csdef-loadbalancerprobe.md)voor meer informatie.|

##  <a name="InternalEndpoint"></a>InternalEndpoint
Het `InternalEndpoint`-element beschrijft een intern eind punt voor een werk rollen. Een intern eind punt is alleen beschikbaar voor andere rolinstanties die in de service worden uitgevoerd. het is niet beschikbaar voor clients buiten de service. Een worker-rol kan Maxi maal vijf HTTP-, UDP-of TCP-eind punten bevatten.

In de volgende tabel worden de kenmerken van het element `InternalEndpoint` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|name|string|Vereist. Een unieke naam voor het interne eind punt.|
|Protocolsubstatus|string|Vereist. Het transport protocol voor het interne eind punt. Mogelijke waarden zijn `HTTP`, `TCP`, `UDP`of `ANY`.<br /><br /> Een waarde van `ANY` geeft aan dat elk protocol een wille keurige poort is toegestaan.|
|poort|int|Optioneel. De poort die wordt gebruikt voor verbindingen met interne taak verdeling op het eind punt. Een eind punt met taak verdeling gebruikt twee poorten. De poort die wordt gebruikt voor het open bare IP-adres en de poort die wordt gebruikt op het privé-IP-adres. Deze zijn meestal ingesteld op hetzelfde, maar u kunt ervoor kiezen om andere poorten te gebruiken.<br /><br /> Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1,7 of hoger).<br /><br /> Het kenmerk `Port` is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.|

##  <a name="InstanceInputEndpoint"></a>InstanceInputEndpoint
Het `InstanceInputEndpoint`-element beschrijft een invoer eindpunt van een exemplaar aan een werk rollen. Een invoer eindpunt van een instantie wordt gekoppeld aan een specifieke rolinstantie door het gebruik van poort door te sturen in de load balancer. Elk invoer eindpunt van de instantie wordt toegewezen aan een specifieke poort uit een bereik van mogelijke poorten. Dit element is de bovenliggende elementen van het `AllocatePublicPortFrom`-element.

Het `InstanceInputEndpoint`-element is alleen beschikbaar via de Azure SDK-versie 1,7 of hoger.

In de volgende tabel worden de kenmerken van het element `InstanceInputEndpoint` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|name|string|Vereist. Een unieke naam voor het eind punt.|
|localPort|int|Vereist. Hiermee geeft u de interne poort op waarnaar alle rolinstanties worden geluisterd om binnenkomend verkeer te ontvangen dat vanuit het load balancer wordt doorgestuurd. Mogelijke waarden variëren tussen 1 en 65535, inclusief.|
|Protocolsubstatus|string|Vereist. Het transport protocol voor het interne eind punt. Mogelijke waarden zijn `udp` en `tcp`. Gebruik `tcp` voor verkeer op basis van http/https.|

##  <a name="AllocatePublicPortFrom"></a>AllocatePublicPortFrom
Het `AllocatePublicPortFrom`-element beschrijft het open bare poort bereik dat door externe klanten kan worden gebruikt voor toegang tot elk invoer eindpunt van het exemplaar. Het open bare (VIP) poort nummer wordt toegewezen vanuit dit bereik en toegewezen aan elk eind punt van de afzonderlijke Role-instantie tijdens de Tenant implementatie en-update. Dit element is de bovenliggende elementen van het `FixedPortRange`-element.

Het `AllocatePublicPortFrom`-element is alleen beschikbaar via de Azure SDK-versie 1,7 of hoger.

##  <a name="FixedPort"></a>FixedPort
Het element `FixedPort` specificeert de poort voor het interne eind punt, waarmee verbindingen met gelijke taak verdeling op het eind punt worden ingeschakeld.

Het `FixedPort`-element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het element `FixedPort` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|poort|int|Vereist. De poort voor het interne eind punt. Dit heeft hetzelfde effect als het instellen van de `FixedPortRange` min en maximum voor dezelfde poort.<br /><br /> Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1,7 of hoger).|

##  <a name="FixedPortRange"></a>FixedPortRange
Het `FixedPortRange`-element geeft het bereik aan van poorten die zijn toegewezen aan het interne eind punt of het invoer eindpunt van het exemplaar en stelt de poort in die wordt gebruikt voor verbindingen met gelijke taak verdeling op het eind punt.

> [!NOTE]
>  Het `FixedPortRange`-element werkt anders, afhankelijk van het element waarin het zich bevindt. Wanneer het element `FixedPortRange` zich in het `InternalEndpoint`-element bevindt, worden alle poorten op de load balancer geopend binnen het bereik van de minimum-en maximum kenmerken voor alle virtuele machines waarop de functie wordt uitgevoerd. Wanneer het element `FixedPortRange` zich in het `InstanceInputEndpoint` element bevindt, wordt er slechts één poort geopend binnen het bereik van de minimum-en maximum kenmerken van elke virtuele machine waarop de functie wordt uitgevoerd.

Het `FixedPortRange`-element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het element `FixedPortRange` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|min.|int|Vereist. De minimale poort in het bereik. Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1,7 of hoger).|
|aantal|string|Vereist. De maximum poort in het bereik. Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1,7 of hoger).|

##  <a name="Certificates"></a>Bewijzen
Het `Certificates`-element beschrijft de verzameling certificaten voor een worker-rol. Dit element is de bovenliggende elementen van het `Certificate`-element. Een rol kan bestaan uit een wille keurig aantal gekoppelde certificaten. Zie [het service definitie bestand met een certificaat wijzigen](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files)voor meer informatie over het gebruik van het onderdeel certificaten.

##  <a name="Certificate"></a>Certificaat
Het element `Certificate` beschrijft een certificaat dat is gekoppeld aan een worker-rol.

In de volgende tabel worden de kenmerken van het element `Certificate` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|name|string|Vereist. Een naam voor dit certificaat, dat wordt gebruikt om ernaar te verwijzen wanneer het is gekoppeld aan een HTTPS-`InputEndpoint` element.|
|storeLocation|string|Vereist. De locatie van het certificaat archief waar dit certificaat op de lokale computer kan worden gevonden. Mogelijke waarden zijn `CurrentUser` en `LocalMachine`.|
|storeName|string|Vereist. De naam van het certificaat archief waar dit certificaat zich bevindt op de lokale computer. Mogelijke waarden zijn de ingebouwde archief namen `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`of een aangepaste archief naam. Als een aangepaste archief naam is opgegeven, wordt het archief automatisch gemaakt.|
|permissionLevel|string|Optioneel. Hiermee geeft u de toegangs machtigingen op die aan de rollen processen worden gegeven. Geef `elevated` machtiging op als u wilt dat alleen verhoogde processen toegang hebben tot de persoonlijke sleutel. met `limitedOrElevated` machtiging kunnen alle rollen processen toegang krijgen tot de persoonlijke sleutel. Mogelijke waarden zijn `limitedOrElevated` en `elevated`. De standaard waarde is `limitedOrElevated`.|

##  <a name="Imports"></a>Rusland
Het `Imports`-element beschrijft een verzameling import modules voor een werk rollen die onderdelen toevoegen aan het gast besturingssysteem. Dit element is de bovenliggende elementen van het `Import`-element. Dit element is optioneel en een rol kan slechts één runtime blok hebben.

Het `Imports`-element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

##  <a name="Import"></a>Wederinvoer
Het `Import`-element geeft een module op die aan het gast besturingssysteem moet worden toegevoegd.

Het `Import`-element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het element `Import` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|moduleName|string|Vereist. De naam van de module die u wilt importeren. Geldige import modules zijn:<br /><br /> -RemoteAccess<br />- RemoteForwarder<br />-Diagnostische gegevens<br /><br /> Met de modules RemoteAccess en RemoteForwarder kunt u uw rolinstantie voor extern bureau blad-verbindingen configureren. Zie [verbinding met extern bureaublad inschakelen](cloud-services-role-enable-remote-desktop-new-portal.md)voor meer informatie.<br /><br /> Met de module diagnostiek kunt u Diagnostische gegevens verzamelen voor een rolinstantie|

##  <a name="Runtime"></a>Gezamenlijke
Het `Runtime`-element beschrijft een verzameling instellingen voor een omgevings variabele voor een werk rollen die de runtime omgeving van het Azure-hostproces beheren. Dit element is de bovenliggende elementen van het `Environment`-element. Dit element is optioneel en een rol kan slechts één runtime blok hebben.

Het `Runtime`-element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het element `Runtime` beschreven:

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|executionContext|string|Optioneel. Hiermee geeft u de context waarin het Role proces wordt gestart. De standaard context is `limited`.<br /><br /> -   `limited`: het proces wordt gestart zonder beheerders bevoegdheden.<br />-   `elevated`: het proces wordt gestart met Administrator bevoegdheden.|

##  <a name="Environment"></a>Variabelen
Het `Environment`-element beschrijft een verzameling instellingen voor een omgevings variabele voor een worker-rol. Dit element is de bovenliggende elementen van het `Variable`-element. Er is mogelijk een aantal omgevings variabelen ingesteld voor een rol.

##  <a name="Variable"></a>Variabeletype
Het `Variable`-element geeft een omgevings variabele op die in het gast besturingssysteem moet worden ingesteld.

Het `Variable`-element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het element `Variable` beschreven:

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|name|string|Vereist. De naam van de omgevings variabele die moet worden ingesteld.|
|waarde|string|Optioneel. De waarde die moet worden ingesteld voor de omgevings variabele. U moet een value-kenmerk of een `RoleInstanceValue`-element bevatten.|

##  <a name="RoleInstanceValue"></a>RoleInstanceValue
Het element `RoleInstanceValue` geeft het xPath op waaruit de waarde van de variabele moet worden opgehaald.

In de volgende tabel worden de kenmerken van het element `RoleInstanceValue` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|XPath|string|Optioneel. Locatiepad van de implementatie-instellingen voor het exemplaar. Zie [Configuration Varia bles with XPath](cloud-services-role-config-xpath.md)(Engelstalig) voor meer informatie.<br /><br /> U moet een value-kenmerk of een `RoleInstanceValue`-element bevatten.|

##  <a name="EntryPoint"></a>Toegangs
Het `EntryPoint`-element geeft u het toegangs punt voor een rol op. Dit element is het bovenliggende onderdeel van de `NetFxEntryPoint` elementen. Met deze elementen kunt u een andere toepassing dan de standaard WaWorkerHost. exe opgeven om te fungeren als het ingangs punt van de functie.

Het `EntryPoint`-element is alleen beschikbaar via de Azure SDK-versie 1,5 of hoger.

##  <a name="NetFxEntryPoint"></a>NetFxEntryPoint
Het `NetFxEntryPoint`-element geeft het programma op dat moet worden uitgevoerd voor een rol.

> [!NOTE]
>  Het `NetFxEntryPoint`-element is alleen beschikbaar via de Azure SDK-versie 1,5 of hoger.

In de volgende tabel worden de kenmerken van het element `NetFxEntryPoint` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|AssemblyName|string|Vereist. Het pad en de bestands naam van de assembly met het toegangs punt. Het pad is relatief ten opzichte van de map **\\%ROLEROOT%\Approot** ( **\\%ROLEROOT%\Approot** niet opgeven in `commandLine`, wordt ervan uitgegaan dat dit wordt gebruikt). **% ROLEROOT%** is een omgevings variabele die wordt onderhouden door Azure en vertegenwoordigt de locatie van de hoofdmap voor uw rol. De map **\\%ROLEROOT%\Approot** vertegenwoordigt de toepassingsmap voor uw rol.|
|targetFrameworkVersion|string|Vereist. De versie van .NET Framework waarop de assembly is gebouwd. Bijvoorbeeld `targetFrameworkVersion="v4.0"`.|

##  <a name="ProgramEntryPoint"></a>ProgramEntryPoint
Het `ProgramEntryPoint`-element geeft het programma op dat moet worden uitgevoerd voor een rol. Met het element `ProgramEntryPoint` kunt u een invoer punt voor een programma opgeven dat niet is gebaseerd op een .NET-assembly.

> [!NOTE]
>  Het `ProgramEntryPoint`-element is alleen beschikbaar via de Azure SDK-versie 1,5 of hoger.

In de volgende tabel worden de kenmerken van het element `ProgramEntryPoint` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|commandLine|string|Vereist. Het pad, de bestands naam en eventuele opdracht regel argumenten van het programma dat moet worden uitgevoerd. Het pad is relatief ten opzichte van de map **%ROLEROOT%\Approot** ( **%ROLEROOT%\Approot** niet opgeven bij commandline, wordt aangenomen). **% ROLEROOT%** is een omgevings variabele die wordt onderhouden door Azure en vertegenwoordigt de locatie van de hoofdmap voor uw rol. De map **%ROLEROOT%\Approot** vertegenwoordigt de toepassingsmap voor uw rol.<br /><br /> Als het programma wordt beëindigd, wordt de rol gerecycled en wordt het programma doorgaans ingesteld om te worden uitgevoerd, in plaats van een programma dat zojuist wordt gestart en een eindige taak uitvoert.|
|setReadyOnProcessStart|booleaans|Vereist. Hiermee geeft u op of de rolinstantie wacht totdat het opdracht regel programma een signaal start. Deze waarde moet op dit moment worden ingesteld op `true`. Het instellen van de waarde op `false` is gereserveerd voor toekomstig gebruik.|

##  <a name="Startup"></a>Opstarten
Het `Startup`-element beschrijft een verzameling taken die worden uitgevoerd wanneer de rol wordt gestart. Dit element kan de bovenliggende elementen van het `Variable`-element zijn. Zie [opstart taken configureren](cloud-services-startup-tasks.md)voor meer informatie over het gebruik van de functie opstart taken. Dit element is optioneel en een rol kan slechts één opstart blok hebben.

In de volgende tabel wordt het kenmerk van het element `Startup` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|priority|int|Alleen voor intern gebruik.|

##  <a name="Task"></a>Takenreeksuitvoeringsengine
Het `Task`-element geeft de opstart taak op die wordt uitgevoerd wanneer de rol wordt gestart. Opstart taken kunnen worden gebruikt om taken uit te voeren die de rol voorbereiden voor het uitvoeren van dergelijke installatie software componenten of het uitvoeren van andere toepassingen. Taken worden uitgevoerd in de volg orde waarin ze worden weer gegeven in het `Startup`-element blok.

Het `Task`-element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het element `Task` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|commandLine|string|Vereist. Een script, zoals een CMD-bestand, dat de opdrachten bevat die moeten worden uitgevoerd. Opstart opdrachten en batch bestanden moeten worden opgeslagen in ANSI-indeling. Bestands indelingen die een byte volgorde markering instellen aan het begin van het bestand, worden niet goed verwerkt.|
|executionContext|string|Hiermee geeft u de context waarin het script wordt uitgevoerd.<br /><br /> -   `limited` [standaard]: Voer uit met dezelfde bevoegdheden als voor de rol die het proces host.<br />-   `elevated`: Voer uit met Administrator bevoegdheden.|
|taskType|string|Hiermee geeft u het uitvoerings gedrag van de opdracht op.<br /><br /> -   `simple` [standaard]: er wordt gewacht tot de taak wordt afgesloten voordat alle andere taken worden gestart.<br />-   `background`: het systeem wacht niet tot de taak is afgesloten.<br />-   `foreground` – vergelijkbaar met achtergrond, behalve als de rol niet opnieuw wordt gestart totdat alle voorgrond taken worden afgesloten.|

##  <a name="Contents"></a>Gehele
Het element `Contents` beschrijft het verzamelen van inhoud voor een werk rollen. Dit element is de bovenliggende elementen van het `Content`-element.

Het `Contents`-element is alleen beschikbaar via de Azure SDK-versie 1,5 of hoger.

##  <a name="Content"></a>Inhoudbeheer
Het element `Content` definieert de bron locatie van de inhoud die moet worden gekopieerd naar de virtuele Azure-machine en het doelpad waarnaar deze wordt gekopieerd.

Het `Content`-element is alleen beschikbaar via de Azure SDK-versie 1,5 of hoger.

In de volgende tabel worden de kenmerken van het element `Content` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|Beoogde|string|Vereist. De locatie op de virtuele Azure-machine waarop de inhoud is geplaatst. Deze locatie is relatief ten opzichte van de map **%ROLEROOT%\Approot**.|

Dit element is het bovenliggende element van het element `SourceDirectory`.

##  <a name="SourceDirectory"></a>Source Directory
Het `SourceDirectory`-element definieert de lokale map waaruit inhoud wordt gekopieerd. Gebruik dit element om de lokale inhoud op te geven die moet worden gekopieerd naar de virtuele machine van Azure.

Het `SourceDirectory`-element is alleen beschikbaar via de Azure SDK-versie 1,5 of hoger.

In de volgende tabel worden de kenmerken van het element `SourceDirectory` beschreven.

| Kenmerk | Type | Beschrijving |
| --------- | ---- | ----------- |
|programmapad|string|Vereist. Relatief of absoluut pad van een lokale map waarvan de inhoud wordt gekopieerd naar de virtuele machine van Azure. De uitbrei ding van omgevings variabelen in het mappad wordt ondersteund.|

## <a name="see-also"></a>Zie ook
[Schema voor Cloud service (klassiek)](schema-csdef-file.md)