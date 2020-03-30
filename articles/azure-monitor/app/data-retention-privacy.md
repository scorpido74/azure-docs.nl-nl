---
title: Gegevensbewaring en -opslag in Azure Application Insights | Microsoft Documenten
description: Verklaring voor retentie en privacybeleid
ms.topic: conceptual
ms.date: 09/29/2019
ms.openlocfilehash: 30878eecf795c85713b9f09b8325b326416022b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275995"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Gegevensverzameling, -bewaring en -opslag in Application Insights

Wanneer u [Azure Application Insights][start] SDK in uw app installeert, wordt telemetrie over uw app naar de cloud verzenden. Uiteraard willen verantwoordelijke ontwikkelaars precies weten welke gegevens worden verzonden, wat er met de gegevens gebeurt en hoe ze de controle erover kunnen houden. In het bijzonder, kunnen gevoelige gegevens worden verzonden, waar wordt opgeslagen, en hoe veilig is het? 

Ten eerste, het korte antwoord:

* De standaard telemetriemodules die "out of the box" draaien, verzenden waarschijnlijk geen gevoelige gegevens naar de service. De telemetrie houdt zich bezig met belasting-, prestatie- en gebruiksstatistieken, uitzonderingsrapporten en andere diagnostische gegevens. De belangrijkste gebruikersgegevens die zichtbaar zijn in de diagnostische rapporten zijn URL's; maar uw app mag in geen geval gevoelige gegevens in platte tekst in een URL plaatsen.
* U code schrijven die extra aangepaste telemetrie verzendt om u te helpen bij het diagnosticeren en controleren van het gebruik. (Deze uitbreidbaarheid is een geweldige functie van Application Insights.) Het zou per ongeluk mogelijk zijn om deze code te schrijven, zodat deze persoonlijke en andere gevoelige gegevens bevat. Als uw toepassing met dergelijke gegevens werkt, moet u een grondig beoordelingsproces toepassen op alle code die u schrijft.
* Tijdens het ontwikkelen en testen van uw app, is het eenvoudig om te inspecteren wat er wordt verzonden door de SDK. De gegevens worden weergegeven in de debuggen uitvoervensters van de IDE en de browser. 
* De gegevens worden bewaard in [Microsoft Azure-servers](https://azure.com) in de VS of Europa. (Maar uw app kan overal worden uitgevoerd.) Azure heeft [sterke beveiligingsprocessen en voldoet aan een breed scala aan nalevingsnormen.](https://azure.microsoft.com/support/trust-center/) Alleen u en uw aangewezen team hebben toegang tot uw gegevens. Microsoft-medewerkers kunnen er alleen onder specifieke beperkte omstandigheden toegang toe hebben onder specifieke beperkte omstandigheden met uw medeweten. Het is versleuteld tijdens het transport en in rust.
*   Bekijk de verzamelde gegevens, omdat dit gegevens kan bevatten die in sommige omstandigheden zijn toegestaan, maar andere niet.  Een goed voorbeeld hiervan is apparaatnaam. De naam van het apparaat van een server heeft geen privacy-impact en is nuttig, maar een apparaatnaam van een telefoon of laptop kan een privacy-impact hebben en minder nuttig zijn. Een SDK die voornamelijk is ontwikkeld om servers te targeten, zou standaard de naam van het apparaat verzamelen en dit moet mogelijk worden overschreven in zowel normale gebeurtenissen als uitzonderingen.

De rest van dit artikel gaat dieper in op deze antwoorden. Het is ontworpen om op zichzelf te staan, zodat je het laten zien aan collega's die geen deel uitmaken van je directe team.

## <a name="what-is-application-insights"></a>Wat is Application Insights?
[Azure Application Insights][start] is een service van Microsoft die u helpt de prestaties en bruikbaarheid van uw live-toepassing te verbeteren. Het controleert uw toepassing de hele tijd dat deze wordt uitgevoerd, zowel tijdens het testen als nadat u deze hebt gepubliceerd of geïmplementeerd. Application Insights maakt grafieken en tabellen die u bijvoorbeeld laten zien op welke momenten van de dag u de meeste gebruikers krijgt, hoe responsief de app is en hoe goed deze wordt bediend door externe services waarvan deze afhankelijk is. Als er crashes, storingen of prestatieproblemen zijn, u de telemetriegegevens in detail doorzoeken om de oorzaak vast te stellen. En de service stuurt u e-mails als er wijzigingen zijn in de beschikbaarheid en prestaties van uw app.

Om deze functionaliteit te krijgen, installeert u een Application Insights SDK in uw toepassing, die onderdeel wordt van de code. Wanneer uw app wordt uitgevoerd, controleert de SDK de werking ervan en stuurt telemetrie naar de Application Insights-service. Dit is een cloudservice die wordt gehost door [Microsoft Azure.](https://azure.com) (Maar Application Insights werkt voor alle toepassingen, niet alleen toepassingen die worden gehost in Azure.)

De Application Insights-service slaat de telemetrie op en analyseert deze. Als u de analyse wilt bekijken of zoeken via de opgeslagen telemetrie, meldt u zich aan bij uw Azure-account en opent u de application Insights-bron voor uw toepassing. U ook toegang tot de gegevens delen met andere leden van uw team of met opgegeven Azure-abonnees.

U gegevens laten exporteren vanuit de Application Insights-service, bijvoorbeeld naar een database of naar externe hulpprogramma's. U voorziet elke tool van een speciale sleutel die u van de service krijgt. De sleutel kan indien nodig worden ingetrokken. 

Application Insights SDKs zijn beschikbaar voor een reeks toepassingstypen: webservices die worden gehost in uw eigen Java EE- of ASP.NET-servers of in Azure; webclients - dat wil zeggen, de code die wordt uitgevoerd op een webpagina; bureaublad-apps en -services; apparaat-apps zoals Windows Phone, iOS en Android. Ze sturen allemaal telemetrie naar dezelfde dienst.

## <a name="what-data-does-it-collect"></a>Welke gegevens verzamelt het?
Er zijn drie gegevensbronnen:

* De SDK, die u integreert met uw app [in ontwikkeling](../../azure-monitor/app/asp-net.md) of [tijdens de looptijd.](../../azure-monitor/app/monitor-performance-live-website-now.md) Er zijn verschillende SDK's voor verschillende toepassingstypen. Er is ook een [SDK voor webpagina's,](../../azure-monitor/app/javascript.md)die laadt in de browser van de eindgebruiker, samen met de pagina.
  
  * Elke SDK heeft een aantal [modules,](../../azure-monitor/app/configuration-with-applicationinsights-config.md)die verschillende technieken gebruiken om verschillende soorten telemetrie te verzamelen.
  * Als u de SDK in ontwikkeling installeert, u de API gebruiken om uw eigen telemetrie te verzenden, naast de standaardmodules. Deze aangepaste telemetrie kan alle gegevens bevatten die u wilt verzenden.
* In sommige webservers zijn er ook agenten die naast de app draaien en telemetrie over CPU, geheugen en netwerkbezetting verzenden. Azure VM's, Docker-hosts en [Java EE-servers](../../azure-monitor/app/java-agent.md) kunnen bijvoorbeeld dergelijke agents hebben.
* [Beschikbaarheidstests](../../azure-monitor/app/monitor-web-app-availability.md) zijn processen die door Microsoft worden uitgevoerd en die regelmatig aanvragen naar uw web-app verzenden. De resultaten worden verzonden naar de Application Insights-service.

### <a name="what-kinds-of-data-are-collected"></a>Welke soorten gegevens worden verzameld?
De belangrijkste categorieën zijn:

* [Telemetrie van de webserver](../../azure-monitor/app/asp-net.md) - HTTP-aanvragen.  Uri, tijd die nodig is om de aanvraag, antwoordcode, client IP-adres te verwerken. `Session id`.
* [Webpagina's](../../azure-monitor/app/javascript.md) - Aantal pagina's, gebruikers en sessies. Laadtijden van pagina's. Uitzonderingen. Ajax belt.
* Prestatiemeteritems - Geheugen, CPU, IO, Netwerkbezetting.
* Client- en servercontext - OS, locale, apparaattype, browser, schermresolutie.
* [Uitzonderingen](../../azure-monitor/app/asp-net-exceptions.md) en crashes - `build id` **stack dumps**, , CPU-type. 
* [Afhankelijkheden](../../azure-monitor/app/asp-net-dependencies.md) - oproepen naar externe services zoals REST, SQL, AJAX. URI of verbindingstekenreeks, duur, succes, opdracht.
* [Beschikbaarheidstests](../../azure-monitor/app/monitor-web-app-availability.md) - duur van de test en stappen, reacties.
* [Traceer logboeken](../../azure-monitor/app/asp-net-trace-logs.md) en [aangepaste telemetrie](../../azure-monitor/app/api-custom-events-metrics.md) - **alles wat u codeert in uw logboeken of telemetrie.**

[Meer details](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Hoe kan ik controleren wat er wordt verzameld?
Als u de app ontwikkelt met Visual Studio, voert u de app uit in de foutopsporingsmodus (F5). De telemetrie wordt weergegeven in het venster Uitvoer. Vanaf daar u het kopiëren en opmaken als JSON voor eenvoudige inspectie. 

![](./media/data-retention-privacy/06-vs.png)

Er is ook een meer leesbare weergave in het venster Diagnostische gegevens.

Open het foutopsporingsvenster van uw browser voor webpagina's.

![Druk op F12 en open het tabblad Netwerk.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Kan ik code schrijven om de telemetrie te filteren voordat deze wordt verzonden?
Dit zou mogelijk zijn door het schrijven van een [telemetrie processor plugin](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Hoe lang worden de gegevens bewaard?
Ruwe gegevenspunten (dat wil zeggen items die u opvragen in Analytics en inspecteren in Search) worden maximaal 730 dagen bewaard. U [een bewaarduur van](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) 30, 60, 90, 120, 180, 270, 365, 550 of 730 dagen selecteren. Als u gegevens langer dan 730 dagen moet bewaren, u [Continuous Export](../../azure-monitor/app/export-telemetry.md) gebruiken om deze tijdens het innemen van gegevens naar een opslagaccount te kopiëren. 

Gegevens die langer dan 90 dagen worden bewaard, worden extra kosten in rekening gebracht. Meer informatie over de prijzen van Application Insights vindt u op de [prijspagina van Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Geaggregeerde gegevens (dat wil zeggen, tellingen, gemiddelden en andere statistische gegevens die u ziet in Metric Explorer) worden gedurende 90 dagen op een korrel van 1 minuut bewaard.

[Foutopsporingsmomentopnamen](../../azure-monitor/app/snapshot-debugger.md) worden 15 dagen bewaard. Dit bewaarbeleid wordt per toepassing vastgesteld. Als u deze waarde wilt verhogen, u een verhoging aanvragen door een ondersteuningsaanvraag te openen in de Azure-portal.

## <a name="who-can-access-the-data"></a>Wie heeft er toegang tot de gegevens?
De gegevens zijn zichtbaar voor u en, als u een organisatieaccount hebt, uw teamleden. 

Het kan worden geëxporteerd door u en uw teamleden en kan worden gekopieerd naar andere locaties en doorgegeven aan andere mensen.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Wat doet Microsoft met de informatie die mijn app naar Application Insights stuurt?
Microsoft gebruikt de gegevens alleen om de service aan u te leveren.

## <a name="where-is-the-data-held"></a>Waar worden de gegevens bewaard?
* U de locatie selecteren wanneer u een nieuwe Application Insights-bron maakt. Hier vindt u meer informatie over de beschikbaarheid van Application Insights per [regio.](https://azure.microsoft.com/global-infrastructure/services/?products=all)

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Betekent dit dat mijn app moet worden gehost in de VS, Europa of Zuidoost-Azië?
* Nee. Uw toepassing kan overal worden uitgevoerd, in uw eigen on-premises hosts of in de cloud.

## <a name="how-secure-is-my-data"></a>Hoe veilig zijn mijn gegevens?
Application Insights is een Azure-service. Beveiligingsbeleid wordt beschreven in de [whitepaper Azure Security, Privacy en Compliance.](https://go.microsoft.com/fwlink/?linkid=392408)

De gegevens worden opgeslagen in Microsoft Azure-servers. Voor accounts in de Azure-portal worden accountbeperkingen beschreven in het [document Azure Security, Privacy en Compliance.](https://go.microsoft.com/fwlink/?linkid=392408)

De toegang van Microsoft-personeel tot uw gegevens is beperkt. Wij hebben alleen toegang tot uw gegevens met uw toestemming en als dit nodig is om uw gebruik van Application Insights te ondersteunen. 

Gegevens in totaal voor alle toepassingen van onze klanten (zoals gegevenssnelheden en gemiddelde grootte van traces) worden gebruikt om Application Insights te verbeteren.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Kan de telemetrie van iemand anders mijn Application Insights-gegevens verstoren?
Ze kunnen extra telemetrie naar uw account sturen met behulp van de instrumentatiesleutel, die kan worden gevonden in de code van uw webpagina's. Met voldoende extra gegevens geven uw statistieken de prestaties en het gebruik van uw app niet correct weer.

Als u code deelt met andere projecten, vergeet dan niet om uw instrumentatiesleutel te verwijderen.

## <a name="is-the-data-encrypted"></a>Zijn de gegevens versleuteld?
Alle gegevens worden in rust versleuteld en verplaatst zich tussen datacenters.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Worden de gegevens versleuteld tijdens het transport van mijn toepassing naar Application Insights-servers?
Ja, we gebruiken https om gegevens naar de portal te verzenden vanaf bijna alle SDK's, inclusief webservers, apparaten en HTTPS-webpagina's. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>Maakt de SDK tijdelijke lokale opslag?

Ja, bepaalde telemetriekanalen blijven lokaal gegevens bevatten als een eindpunt niet kan worden bereikt. Bekijk hieronder welke frameworks en telemetriekanalen worden beïnvloed.

Telemetriekanalen die gebruik maken van lokale opslag maken tijdelijke bestanden in de TEMP- of APPDATA-mappen, die beperkt zijn tot het specifieke account waarop uw toepassing wordt uitgevoerd. Dit kan gebeuren wanneer een eindpunt tijdelijk niet beschikbaar was of wanneer u de beperkingslimiet hebt bereikt. Zodra dit probleem is opgelost, wordt het telemetriekanaal hervat met het verzenden van alle nieuwe en aanhoudende gegevens.

Deze aanhoudende gegevens worden niet lokaal versleuteld. Als dit een probleem is, bekijkt u de gegevens en beperkt u het verzamelen van privégegevens. (Zie [Privégegevens exporteren en verwijderen](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data)voor meer informatie .)

Als een klant deze map moet configureren met specifieke beveiligingsvereisten, kan deze per framework worden geconfigureerd. Zorg ervoor dat het proces waarin uw toepassing wordt uitgevoerd, schrijftoegang heeft tot deze map, maar zorg er ook voor dat deze map is beveiligd om te voorkomen dat telemetrie wordt gelezen door onbedoelde gebruikers.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp`wordt gebruikt voor aanhoudende gegevens. Deze locatie is niet configureerbaar vanuit de config-map en de machtigingen voor toegang tot deze map zijn beperkt tot de specifieke gebruiker met vereiste referenties. (Zie [implementatie](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72)voor meer informatie .)

###  <a name="net"></a>.Net

`ServerTelemetryChannel` Standaard gebruikt u de lokale app-gegevensmap `%localAppData%\Microsoft\ApplicationInsights` of tijdelijke tijdelijke map `%TMP%`van de huidige gebruiker. (Zie [implementatie](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) hier.)


Via configuratiebestand:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Via code:

- ServerTelemetryChannel verwijderen uit configuratiebestand
- Voeg dit fragment toe aan uw configuratie:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore (NetCore)

`ServerTelemetryChannel` Standaard gebruikt u de lokale app-gegevensmap `%localAppData%\Microsoft\ApplicationInsights` of tijdelijke tijdelijke map `%TMP%`van de huidige gebruiker. (Zie [implementatie](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) hier.) In een Linux-omgeving wordt lokale opslag uitgeschakeld, tenzij een opslagmap is opgegeven.

In het volgende codefragment `ServerTelemetryChannel.StorageFolder` ziet `ConfigureServices()` u `Startup.cs` hoe u deze instelt in de methode van uw klasse:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Zie [Aangepaste configuratie van AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration)voor meer informatie .)

### <a name="nodejs"></a>Node.js

Standaard `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` wordt gebruikt voor het aanhouden van gegevens. Machtigingen voor toegang tot deze map zijn beperkt tot de huidige gebruiker en beheerders. (Zie [implementatie](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) hier.)

Het voorvoegsel van de `appInsights-node` map kan worden overschreven `Sender.TEMPDIR_PREFIX` door de runtime-waarde van de statische variabele in [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384)te wijzigen.

### <a name="javascript-browser"></a>JavaScript (browser)

[HTML5-sessieopslag](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) wordt gebruikt om gegevens voort te zetten. Er worden twee afzonderlijke `AI_buffer` `AI_sent_buffer`buffers gebruikt: en . Telemetrie die wordt gebatched en `AI_buffer`wacht om te worden verzonden, wordt opgeslagen in . Telemetrie die net is `AI_sent_buffer` verzonden, wordt geplaatst totdat de opnameserver reageert dat deze met succes is ontvangen. Wanneer telemetrie is ontvangen, wordt deze uit alle buffers verwijderd. Bij tijdelijke fouten (bijvoorbeeld verliest een gebruiker netwerkconnectiviteit), `AI_buffer` blijft telemetrie binnen totdat deze is ontvangen of de opnameserver reageert dat de telemetrie ongeldig is (slecht schema of te oud, bijvoorbeeld).

Telemetriebuffers kunnen worden [`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31) uitgeschakeld `false`door in te stellen op . Wanneer sessieopslag is uitgeschakeld, wordt een lokale array in plaats daarvan gebruikt als permanente opslag. Omdat de JavaScript SDK op een clientapparaat wordt uitgevoerd, heeft de gebruiker toegang tot deze opslaglocatie via de ontwikkelaarstools van zijn browser.

### <a name="opencensus-python"></a>OpenCensus Python

OpenCensus Python SDK gebruikt standaard `%username%/.opencensus/.azure/`de huidige gebruikersmap . Machtigingen voor toegang tot deze map zijn beperkt tot de huidige gebruiker en beheerders. (Zie [implementatie](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) hier.) De map met uw uitgebreide gegevens wordt vernoemd naar het Python-bestand dat de telemetrie heeft gegenereerd.

U de locatie van uw opslagbestand wijzigen door de parameter in de `storage_path` constructor van de exporteur die u gebruikt in te voeren.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Hoe stuur ik gegevens naar Application Insights met TLS 1.2?

Om de beveiliging van gegevens die onderweg zijn naar de Application Insights-eindpunten te verzekeren, raden we klanten ten zeerste aan om hun toepassing te configureren om ten minste TLS(1.2) te gebruiken. Oudere versies van TLS/Secure Sockets Layer (SSL) zijn kwetsbaar bevonden en hoewel ze momenteel nog werken om achterwaartse compatibiliteit mogelijk te maken, worden ze **niet aanbevolen**en de industrie gaat snel over om de ondersteuning voor deze oudere protocollen op te geven. 

De [PCI Security Standards Council](https://www.pcisecuritystandards.org/) heeft een deadline van [30 juni 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) ingesteld om oudere versies van TLS/SSL uit te schakelen en te upgraden naar veiligere protocollen. Als Azure de legacy-ondersteuning heeft laten vallen, u gegevens niet meer verzenden naar Application Insights als uw toepassing/clients niet meer via TLS 1.2 kunnen communiceren. De aanpak die u volgt om de TLS-ondersteuning van uw toepassing te testen en te valideren, is afhankelijk van het besturingssysteem/platform en de taal/framework die uw toepassing gebruikt.

We raden u niet aan uw toepassing expliciet in te stellen om TLS 1.2 alleen te gebruiken, tenzij dit noodzakelijk is, omdat dit beveiligingsfuncties op platformniveau kan verbreken waarmee u automatisch nieuwere, veiligere protocollen detecteren en gebruiken wanneer deze beschikbaar zijn, zoals TLS 1.3. We raden u aan een grondige audit van de code van uw toepassing uit te voeren om te controleren op harde codering van specifieke TLS/SSL-versies.

### <a name="platformlanguage-specific-guidance"></a>Platform/Taalspecifieke richtlijnen

|Platform/Taal | Ondersteuning | Meer informatie |
| --- | --- | --- |
| Azure App Services  | Ondersteund, configuratie kan nodig zijn. | Ondersteuning werd aangekondigd in april 2018. Lees de aankondiging voor [configuratiedetails.](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)  |
| Azure Function-apps | Ondersteund, configuratie kan nodig zijn. | Ondersteuning werd aangekondigd in april 2018. Lees de aankondiging voor [configuratiedetails.](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/) |
|.NET | Ondersteund, configuratie varieert per versie. | Raadpleeg [deze instructies](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12)voor gedetailleerde configuratiegegevens voor .NET 4.7 en eerdere versies.  |
|Status Monitor | Ondersteund, configuratie vereist | Statusmonitor is afhankelijk van [OS Configuration](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [.NET Configuration](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) ter ondersteuning van TLS 1.2.
|Node.js |  Ondersteund in v10.5.0 kan configuratie vereist zijn. | Gebruik de [officiële Node.js TLS/SSL-documentatie](https://nodejs.org/api/tls.html) voor elke toepassingsspecifieke configuratie. |
|Java | Ondersteund, JDK-ondersteuning voor TLS 1.2 werd toegevoegd in [JDK 6 update 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) en [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 maakt standaard gebruik van [TLS 1.2.](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default)  |
|Linux | Linux-distributies zijn meestal afhankelijk van [OpenSSL](https://www.openssl.org) voor TLS 1.2-ondersteuning.  | Controleer de [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) om te bevestigen dat uw versie van OpenSSL wordt ondersteund.|
| Windows 8.0 - 10 | Standaard ondersteund en ingeschakeld. | Om te bevestigen dat u nog steeds de [standaardinstellingen gebruikt.](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)  |
| Windows Server 2012 - 2016 | Standaard ondersteund en ingeschakeld. | Om te bevestigen dat u nog steeds de [standaardinstellingen gebruikt](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 en Windows Server 2008 R2 SP1 | Ondersteund, maar standaard niet ingeschakeld. | Zie de [registerinstellingenpagina (Transport Layer Security)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) voor meer informatie over het inschakelen.  |
| Windows Server 2008 SP2 | Ondersteuning voor TLS 1.2 vereist een update. | Zie [Bijwerken om ondersteuning voor TLS 1.2 toe te voegen](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) in Windows Server 2008 SP2. |
|Windows Vista | Niet ondersteund. | N.v.t.

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Controleer welke versie van OpenSSL uw Linux-distributie wordt uitgevoerd

Als u wilt controleren welke versie van OpenSSL u hebt geïnstalleerd, opent u de terminal en voert u het uitvoeren uit:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Een test TLS 1.2-transactie uitvoeren op Linux

Als u een voorlopige test wilt uitvoeren om te zien of uw Linux-systeem via TLS 1.2 kan communiceren, opent u de terminal en draait u:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Persoonsgegevens opgeslagen in Application Insights

Ons [artikel over persoonsgegevens van Application Insights](../../azure-monitor/platform/personal-data-mgmt.md) bespreekt dit onderwerp diepgaand.

#### <a name="can-my-users-turn-off-application-insights"></a>Kunnen mijn gebruikers Application Insights uitschakelen?
Niet direct. We bieden geen schakelaar die uw gebruikers kunnen gebruiken om Application Insights uit te schakelen.

U een dergelijke functie echter implementeren in uw toepassing. Alle SDK's bevatten een API-instelling die telemetrieverzameling uitschakelt. 

## <a name="data-sent-by-application-insights"></a>Gegevens verzonden door Application Insights
De SDK's variëren tussen platforms en er zijn verschillende onderdelen die u installeren. (Raadpleeg [Application Insights - overzicht][start].) Elk onderdeel verzendt verschillende gegevens.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Klassen van gegevens die in verschillende scenario's worden verzonden

| Uw actie | Verzamelde gegevensklassen (zie volgende tabel) |
| --- | --- |
| [Application Insights SDK toevoegen aan een .NET-webproject][greenbrown] |ServerContext<br/>Afgeleid<br/>Prestatiemeteritems<br/>Aanvragen<br/>**Uitzonderingen**<br/>Sessie<br/>gebruikers |
| [Statusmonitor installeren op IIS][redfield] |Afhankelijkheden<br/>ServerContext<br/>Afgeleid<br/>Prestatiemeteritems |
| [Application Insights SDK toevoegen aan een Java-web-app][java] |ServerContext<br/>Afgeleid<br/>Aanvraag<br/>Sessie<br/>gebruikers |
| [JavaScript SDK toevoegen aan webpagina][client] |ClientContext <br/>Afgeleid<br/>Pagina<br/>ClientPerf<br/>Ajax |
| [Standaardeigenschappen definiëren][apiproperties] |**Eigenschappen** op alle standaard- en aangepaste gebeurtenissen |
| [Trackmetric bellen][api] |Numerieke waarden<br/>**Eigenschappen** |
| [Oproeptrack*][api] |Gebeurtenisnaam<br/>**Eigenschappen** |
| [TrackException bellen][api] |**Uitzonderingen**<br/>Stapeldump<br/>**Eigenschappen** |
| SDK kan geen gegevens verzamelen. Bijvoorbeeld: <br/> - heeft geen toegang tot perf-tellers<br/> - uitzondering in telemetrieinitialisator |SDK diagnostiek |

Voor [SDKs voor andere platforms,][platforms]zie hun documenten.

#### <a name="the-classes-of-collected-data"></a>De klassen van verzamelde gegevens

| Klasse Verzamelde gegevens | Inclusief (geen volledige lijst) |
| --- | --- |
| **Eigenschappen** |**Alle gegevens - bepaald door uw code** |
| DeviceContext |`Id`, IP, Locale, Apparaatmodel, netwerk, netwerktype, OEM-naam, schermresolutie, Rolinstantie, Rolnaam, Apparaattype |
| ClientContext |OS, landstreek, taal, netwerk, vensterresolutie |
| Sessie |`session id` |
| ServerContext |Machinenaam, landstreek, besturingssysteem, apparaat, gebruikerssessie, gebruikerscontext, bewerking |
| Afgeleid |geolocatie vanaf IP-adres, tijdstempel, OS, browser |
| Metrische gegevens |Metrische naam en waarde |
| Gebeurtenissen |Naam en waarde van gebeurtenis |
| Paginaweergaves |URL en paginanaam of schermnaam |
| Client perf |URL/paginanaam, laadtijd van de browser |
| Ajax |HTTP-oproepen van webpagina naar server |
| Aanvragen |URL, duur, antwoordcode |
| Afhankelijkheden |Type(SQL, HTTP, ...), verbindingstekenreeks of URI, synchronisatie/async, duur, succes, SQL-instructie (met statusmonitor) |
| **Uitzonderingen** |Tekst, **bericht,** oproepstapels, bronbestand, regelnummer,`thread id` |
| Crashes |`Process id`, `parent process id`, `crash thread id`; toepassingspatch,, `id`bouwen;  uitzonderingstype, adres, reden; versluierde symbolen en registers, binaire begin- en eindadressen, binaire naam en pad, cpu-type |
| Tracering |**Bericht** en ernstniveau |
| Prestatiemeteritems |Processortijd, beschikbaar geheugen, aanvraagsnelheid, uitzonderingspercentage, privébytes verwerken, IO-snelheid, aanvraagduur, wachtrijlengte aanvragen |
| Beschikbaarheid |Webtestresponscode, duur van elke teststap, testnaam, tijdstempel, succes, responstijd, testlocatie |
| SDK diagnostiek |Traceerbericht of uitzondering |

U [een deel van de gegevens uitschakelen door ApplicationInsights.config te bewerken][config]

> [!NOTE]
> Client-IP wordt gebruikt om geografische locatie af te leiden, maar standaard worden IP-gegevens niet meer opgeslagen en worden alle nullen naar het bijbehorende veld geschreven. Om meer te weten te komen over de verwerking van persoonsgegevens raden we dit [artikel](../../azure-monitor/platform/personal-data-mgmt.md#application-data)aan. Als u IP-adresgegevens moet opslaan, zal ons [IP-adresverzamelingsartikel](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) u door uw opties heen brengen.

## <a name="credits"></a>Tegoeden
Dit product bevat GeoLite2-gegevens die [https://www.maxmind.com](https://www.maxmind.com)zijn gemaakt door MaxMind, beschikbaar vanaf .



<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiproperties]: ../../azure-monitor/app/api-custom-events-metrics.md#properties
[client]: ../../azure-monitor/app/javascript.md
[config]: ../../azure-monitor/app/configuration-with-applicationinsights-config.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[java]: ../../azure-monitor/app/java-get-started.md
[platforms]: ../../azure-monitor/app/platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
