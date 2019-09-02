---
title: Bewaren van gegevens en opslag in Azure-toepassing inzichten | Microsoft Docs
description: Retentie en privacybeleid
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mbullwin
ms.openlocfilehash: df441a55ef4a9a40fe4defcabca5f667eeddbf29
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207287"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Verzameling, retentie en opslag van gegevens in Application Insights

Wanneer u [Azure-toepassing Insights][start] -SDK in uw app installeert, verzendt deze telemetrie over uw app naar de Cloud. Natuurlijk willen bevoegde ontwikkel aars precies weten welke gegevens worden verzonden, wat er gebeurt met de gegevens en hoe ze de controle kunnen blijven houden. Met name kunnen gevoelige gegevens worden verzonden, waar ze worden opgeslagen en hoe veilig het is? 

Eerst het korte antwoord:

* De standaard-telemetrie-modules die "out-of-Box" worden uitgevoerd, verzenden waarschijnlijk geen gevoelige gegevens naar de service. De telemetrie heeft betrekking op metrische gegevens over belasting, prestaties en gebruik, uitzonderings rapporten en andere diagnostische informatie. De belangrijkste gebruikers gegevens die in de diagnostische rapporten worden weer gegeven, zijn Url's. maar uw app mag in geen geval gevoelige gegevens in een URL plaatsen als tekst zonder opmaak.
* U kunt code schrijven die aanvullende aangepaste telemetrie verstuurt om u te helpen bij het gebruik van diagnostische gegevens en bewaking. (Deze uitbreid baarheid is een uitstekende functie van Application Insights.) Het kan per ongeluk zijn om deze code te schrijven, zodat deze persoonlijke en andere gevoelige gegevens bevat. Als uw toepassing met dergelijke gegevens werkt, moet u een grondige beoordelings procedure Toep assen op alle code die u schrijft.
* Tijdens het ontwikkelen en testen van uw app is het eenvoudig om te controleren wat wordt verzonden door de SDK. De gegevens worden weer gegeven in de uitvoer van de Windows-oplossing voor fout opsporing van de IDE en browser. 
* De gegevens worden opgeslagen in [Microsoft Azure](https://azure.com) servers in de Verenigde Staten en Europa. (Maar uw app kan overal worden uitgevoerd.) Azure heeft [sterke beveiligings processen en voldoet aan een breed scala aan nalevings standaarden](https://azure.microsoft.com/support/trust-center/). Alleen u en uw specifieke team hebben toegang tot uw gegevens. Micro soft-mede werkers kunnen beperkte toegang hebben tot alleen onder specifieke beperkte omstandigheden met uw kennis. Het is versleuteld onderweg en in rust.

De rest van dit artikel bevat meer informatie over deze antwoorden. Het is ontworpen om op zichzelf te staan, zodat u deze kunt weer geven aan collega's die geen deel uitmaken van uw directe team.

## <a name="what-is-application-insights"></a>Wat is Application Insights?
[Azure-toepassing Insights][start] is een service van micro soft waarmee u de prestaties en bruikbaarheid van uw Live-toepassing kunt verbeteren. Hiermee wordt uw toepassing gecontroleerd op het moment dat deze wordt uitgevoerd, zowel tijdens het testen als nadat u deze hebt gepubliceerd of geïmplementeerd. Application Insights maakt grafieken en tabellen waarin u bijvoorbeeld kunt zien op welke tijdstippen u de meeste gebruikers krijgt, hoe reageert de app en hoe goed deze wordt bediend door externe services waarvan deze afhankelijk is. Als er zich crashes, fouten of prestatie problemen voordoen, kunt u de gegevens in de telemetrie gedetailleerd doorzoeken om de oorzaak vast te stellen. En de service stuurt u e-mail berichten als er wijzigingen zijn in de beschik baarheid en prestaties van uw app.

Als u deze functionaliteit wilt ontvangen, installeert u een Application Insights SDK in uw toepassing, die deel wordt van de code. Wanneer uw app wordt uitgevoerd, wordt de werking van de SDK bewaakt en wordt telemetrie naar de Application Insights-service verzonden. Dit is een Cloud service die wordt gehost door [Microsoft Azure](https://azure.com). (Maar Application Insights werkt voor elke toepassing, niet alleen de toepassingen die worden gehost in Azure.)

De telemetrie wordt door de Application Insights-service opgeslagen en geanalyseerd. U meldt zich aan bij uw Azure-account en opent de Application Insights resource voor uw toepassing om de analyse te bekijken of te zoeken in de opgeslagen telemetrie. U kunt ook toegang tot de gegevens delen met andere leden van uw team of met de opgegeven Azure-abonnees.

U kunt gegevens exporteren van de Application Insights-service, bijvoorbeeld naar een Data Base of naar externe hulpprogram ma's. U geeft elk hulp programma een speciale sleutel die u van de service aanschaft. De sleutel kan indien nodig worden ingetrokken. 

Application Insights Sdk's zijn beschikbaar voor verschillende toepassings typen: webservices die worden gehost in uw eigen Java EE-of ASP.NET-servers of in azure; webclients, dat wil zeggen de code die wordt uitgevoerd op een webpagina. bureau blad-apps en-services; apparaat-apps zoals Windows Phone, iOS en Android. Ze verzenden telemetrie naar dezelfde service.

## <a name="what-data-does-it-collect"></a>Welke gegevens worden er verzameld?
### <a name="how-is-the-data-is-collected"></a>Hoe worden de gegevens verzameld?
Er zijn drie gegevens bronnen:

* De SDK, die u integreert met uw app [in ontwikkeling](../../azure-monitor/app/asp-net.md) of [tijdens de uitvoering](../../azure-monitor/app/monitor-performance-live-website-now.md). Er zijn verschillende Sdk's voor verschillende toepassings typen. Er is ook een [SDK voor webpagina's](../../azure-monitor/app/javascript.md)die in de browser van de eind gebruiker wordt geladen samen met de pagina.
  
  * Elke SDK heeft een aantal [modules](../../azure-monitor/app/configuration-with-applicationinsights-config.md), die gebruikmaken van verschillende technieken voor het verzamelen van verschillende soorten telemetrie.
  * Als u de SDK in ontwikkeling installeert, kunt u ook de API gebruiken om uw eigen telemetrie en de standaard modules te verzenden. Deze aangepaste telemetrie kan alle gegevens bevatten die u wilt verzenden.
* Op sommige webservers zijn er ook agents die naast de app worden uitgevoerd en het verzenden van telemetrie over CPU, geheugen en netwerk bezetting. Zo kunnen Azure-Vm's, docker-hosts en [Java EE-servers](../../azure-monitor/app/java-agent.md) dergelijke agenten hebben.
* [Beschikbaarheids tests](../../azure-monitor/app/monitor-web-app-availability.md) zijn processen die door micro soft worden uitgevoerd en die op gezette tijden aanvragen verzenden naar uw web-app. De resultaten worden naar de Application Insights-service verzonden.

### <a name="what-kinds-of-data-are-collected"></a>Welke soorten gegevens worden er verzameld?
De belangrijkste categorieën zijn:

* [](../../azure-monitor/app/asp-net.md) Telemetrie van webserver-HTTP-aanvragen.  URI, de tijd die nodig is om de aanvraag, de antwoord code, het IP-adres van de client te verwerken. Sessie-id.
* [](../../azure-monitor/app/javascript.md) Webpagina's-pagina-, gebruikers-en sessie aantallen. Laad tijden van pagina's. Uitzonderingen. Ajax-aanroepen.
* Prestatie meter items-geheugen, CPU, IO, netwerk bezetting.
* Client-en server context: besturings systeem, land instelling, apparaattype, browser, scherm resolutie.
* [Uitzonde ringen](../../azure-monitor/app/asp-net-exceptions.md) en crash- **stack dumps**, build-id, CPU-type. 
* [Afhankelijkheden](../../azure-monitor/app/asp-net-dependencies.md) : aanroepen naar externe services, zoals rest, SQL, Ajax. URI of connection string, duur, geslaagd, opdracht.
* [Beschikbaarheids tests](../../azure-monitor/app/monitor-web-app-availability.md) : duur van testen en stappen, reacties.
* [Traceer logboeken](../../azure-monitor/app/asp-net-trace-logs.md) en [aangepaste](../../azure-monitor/app/api-custom-events-metrics.md) - telemetrie**Alles wat u in uw logboeken of**telemetrie codeeert.

[Meer details](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Hoe kan ik controleren wat er wordt verzameld?
Als u de App ontwikkelt met behulp van Visual Studio, voert u de app uit in de foutopsporingsmodus (F5). De telemetrie wordt weer gegeven in het uitvoer venster. Daar kunt u het kopiëren en opmaken als JSON voor eenvoudige inspectie. 

![](./media/data-retention-privacy/06-vs.png)

Er is ook een meer Lees bare weer gave in het venster Diagnostiek.

Open het venster fout opsporing van uw browser voor webpagina's.

![Druk op F12 en open het tabblad netwerk.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Kan ik code schrijven om de telemetrie te filteren voordat deze wordt verzonden?
Dit zou mogelijk kunnen zijn door een telemetrie- [processor-invoeg toepassing](../../azure-monitor/app/api-filtering-sampling.md)te schrijven.

## <a name="how-long-is-the-data-kept"></a>Hoe lang worden de gegevens bewaard?
Onbewerkte gegevens punten (dat wil zeggen, items die u in analyses kunt opvragen en zoeken in zoek acties) worden Maxi maal 730 dagen bewaard. U kunt [een Bewaar periode](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) van 30, 60, 90, 120, 180, 270, 365, 550 of 730 dagen selecteren. Als u gegevens langer dan 730 dagen wilt bewaren, kunt u doorlopend [exporteren](../../azure-monitor/app/export-telemetry.md) gebruiken om deze naar een opslag account te kopiëren tijdens de gegevens opname. 

Als er meer dan 90 dagen gegevens worden bewaard, worden er extra kosten in rekening gebracht. Meer informatie over Application Insights prijzen vindt u op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/).

Samengevoegde gegevens (dat wil zeggen, aantallen, gemiddelden en andere statistische gegevens die u ziet in metrische Explorer) worden gedurende 90 dagen in een korrel van 1 minuut bewaard.

[Moment opnamen van fout opsporing](../../azure-monitor/app/snapshot-debugger.md) worden vijf tien dagen bewaard. Deze bewaarbeleid is ingesteld op basis van de per toepassing. Als u nodig hebt om deze waarde te verhogen, kunt u een toename van aanvragen door een ondersteuningsaanvraag opent in de Azure-portal.

## <a name="who-can-access-the-data"></a>Wie heeft er toegang tot de gegevens?
De gegevens zijn zichtbaar voor u en, als u een organisatie account hebt, uw team leden. 

Deze kan door u en uw team leden worden geëxporteerd en kunnen worden gekopieerd naar andere locaties en worden door gegeven aan andere personen.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Wat doet micro soft met de informatie die mijn app naar Application Insights stuurt?
Micro soft gebruikt de gegevens alleen om de service voor u te leveren.

## <a name="where-is-the-data-held"></a>Waar bevinden de gegevens zich?
* U kunt de locatie selecteren wanneer u een nieuwe Application Insights resource maakt. Meer informatie over de beschik baarheid van Application Insights per regio [vindt u hier](https://azure.microsoft.com/global-infrastructure/services/?products=all).

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Betekent dit dat mijn app moet worden gehost in de VS, Europa of Zuidoost-Azië?
* Nee. Uw toepassing kan overal worden uitgevoerd, hetzij in uw eigen on-premises hosts of in de Cloud.

## <a name="how-secure-is-my-data"></a>Hoe veilig zijn mijn gegevens?
Application Insights is een Azure-service. Beveiligings beleid wordt beschreven in het [technische document over beveiliging, privacy en naleving van Azure](https://go.microsoft.com/fwlink/?linkid=392408).

De gegevens worden opgeslagen in Microsoft Azure-servers. Voor accounts in azure Portal worden account beperkingen beschreven in het document over [beveiliging, privacy en naleving van Azure](https://go.microsoft.com/fwlink/?linkid=392408).

Toegang tot uw gegevens door mede werkers van micro soft is beperkt. We hebben alleen toegang tot uw gegevens met uw toestemming en als dit nodig is voor de ondersteuning van uw gebruik van Application Insights. 

Gegevens in de samen voeging van alle klanten toepassingen (zoals de gegevens tarieven en de gemiddelde grootte van traceringen) worden gebruikt om Application Insights te verbeteren.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Kan het telemetrie van iemand anders leiden tot mijn Application Insights gegevens?
Ze kunnen extra telemetrie naar uw account verzenden met behulp van de instrumentatie sleutel, die u kunt vinden in de code van uw webpagina's. Met voldoende extra gegevens worden de prestaties en het gebruik van uw app niet correct weer gegeven.

Als u code deelt met andere projecten, vergeet dan niet om uw instrumentatie sleutel te verwijderen.

## <a name="is-the-data-encrypted"></a>Zijn de gegevens versleuteld?
Alle gegevens worden versleuteld in rust en tijdens het verplaatsen tussen data centers.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Worden de gegevens versleuteld tijdens de overdracht van mijn toepassing naar Application Insights servers?
Ja, we gebruiken https om gegevens te verzenden naar de portal vanaf bijna alle Sdk's, inclusief webservers, apparaten en HTTPS-webpagina's. De enige uitzonde ring hierop is gegevens die worden verzonden vanuit webpagina's zonder HTTP-indeling.

## <a name="does-the-sdk-create-temporary-local-storage"></a>Maakt de SDK tijdelijke lokale opslag?

Ja, bepaalde telemetrie-kanalen blijven gegevens lokaal behouden als een eind punt niet kan worden bereikt. Raadpleeg hieronder om te zien welke frameworks en telemetrie-kanalen worden beïnvloed.

Telemetrie-kanalen die gebruikmaken van lokale opslag, maken tijdelijke bestanden in de tijdelijke of APPDATA-directory's die beperkt zijn tot het specifieke account dat uw toepassing uitvoert. Dit kan gebeuren wanneer een eind punt tijdelijk niet beschikbaar is of als u de beperkings limiet bereikt. Zodra dit probleem is opgelost, wordt het verzenden van alle nieuwe en permanente gegevens hervat met het telemetrie-kanaal.

Deze persistente gegevens worden niet lokaal versleuteld. Als dit een probleem is, controleert u de gegevens en beperkt u het verzamelen van persoonlijke gegevens. (Zie [persoonlijke gegevens exporteren en verwijderen](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data) voor meer informatie.)

Als een klant deze map moet configureren met specifieke beveiligings vereisten, kan deze per Framework worden geconfigureerd. Zorg ervoor dat het proces dat uw toepassing uitvoert, schrijf toegang heeft tot deze map, maar zorg er ook voor dat deze map wordt beveiligd om te voor komen dat telemetrie door onbedoelde gebruikers wordt gelezen.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp`wordt gebruikt voor het persistent maken van gegevens. Deze locatie kan niet vanuit de map config worden geconfigureerd en de machtigingen voor toegang tot deze map zijn beperkt tot de specifieke gebruiker met de vereiste referenties. (Zie hier [implementatie](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72) .)

###  <a name="net"></a>.Net

Maakt standaard `ServerTelemetryChannel` gebruik van de map `%localAppData%\Microsoft\ApplicationInsights` lokale app-gegevens van de huidige gebruiker `%TMP%`of de map Temp. (Zie hier [implementatie](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) .)


Via het configuratie bestand:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Via code:

- ServerTelemetryChannel uit het configuratie bestand verwijderen
- Voeg dit fragment toe aan uw configuratie:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Maakt standaard `ServerTelemetryChannel` gebruik van de map `%localAppData%\Microsoft\ApplicationInsights` lokale app-gegevens van de huidige gebruiker `%TMP%`of de map Temp. (Zie hier [implementatie](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) .) In een Linux-omgeving wordt lokale opslag uitgeschakeld, tenzij er een opslagmap is opgegeven.

Het volgende code fragment laat zien hoe u `ServerTelemetryChannel.StorageFolder` kunt instellen `ConfigureServices()` in de methode `Startup.cs` van uw klasse:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Zie [aangepaste configuratie van AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) voor meer informatie. )

### <a name="nodejs"></a>Node.js

Wordt standaard `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` gebruikt voor het persistent maken van gegevens. Machtigingen voor toegang tot deze map zijn beperkt tot de huidige gebruiker en beheerders. (Zie hier [implementatie](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) .)

Het voor voegsel `appInsights-node` van de map kan worden overschreven door de runtime-waarde van `Sender.TEMPDIR_PREFIX` de statische variabele in [Sender. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384)te wijzigen.



## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Hoe kan ik gegevens naar Application Insights verzenden met behulp van TLS 1,2?

Om ervoor te zorgen dat de beveiliging van gegevens die onderweg zijn naar de eind punten van de Application Insights, raden we klanten ten zeerste aan om hun toepassing te configureren voor het gebruik van ten minste Transport Layer Security (TLS) 1,2. Oudere versies van TLS/Secure Sockets Layer (SSL) kwetsbaar zijn gevonden en hoewel ze op dit moment nog steeds werken om toe te staan achterwaartse compatibiliteit, zijn ze onderling **niet aanbevolen**, en de branche is snel veranderende te breken ondersteuning voor deze oudere protocollen. 

De [PCI Security Standards Council heeft onlangs](https://www.pcisecuritystandards.org/) heeft een [deadline van 30 juni 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) om uit te schakelen van oudere versies van TLS/SSL en upgrade voor de protocollen beter kunt beveiligen. Als de ondersteuning voor Azure is verbroken, kunt u, als uw toepassing/clients geen communicatie meer over ten minste TLS 1,2, gegevens verzenden naar Application Insights. De aanpak die u moet nemen om de TLS-ondersteuning van uw toepassing te testen en te valideren, varieert afhankelijk van het besturings systeem/platform en de taal/het Framework dat door uw toepassing wordt gebruikt.

Het wordt niet aanbevolen om uw toepassing expliciet in te stellen op alleen TLS 1,2, tenzij dit absoluut nood zakelijk is, omdat dit de beveiligings functies op platform niveau kan verstoren, zodat u nieuwe beveiligde protocollen automatisch kunt detecteren en gebruiken wanneer deze worden beschikbaar als TLS 1,3. We raden u aan om de code van uw toepassing grondig te controleren en te controleren op hardcoding van specifieke TLS/SSL-versies.

### <a name="platformlanguage-specific-guidance"></a>Specifieke richt lijnen voor platforms en talen

|Platform/taal | Ondersteuning | Meer informatie |
| --- | --- | --- |
| Azure App Services  | Wordt ondersteund. de configuratie is mogelijk vereist. | Ondersteuning werd aangekondigd in april 2018. Lees de aankondiging voor [meer informatie](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)over de configuratie.  |
| Azure function-apps | Wordt ondersteund. de configuratie is mogelijk vereist. | Ondersteuning werd aangekondigd in april 2018. Lees de aankondiging voor [meer informatie](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)over de configuratie. |
|.NET | Ondersteund, de configuratie is afhankelijk van versie. | Raadpleeg [deze instructies](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12)voor gedetailleerde informatie over de configuratie van .net 4,7 en eerdere versies.  |
|Status Monitor | Ondersteund, configuratie vereist | Status monitor is afhankelijk van de[configuratie](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) van de [besturingssysteem configuratie](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + voor de ondersteuning van TLS 1,2.
|Node.js |  In v 10.5.0 is configuratie mogelijk vereist. | Gebruik de [officiële node. js TLS/SSL-documentatie](https://nodejs.org/api/tls.html) voor specifieke configuratie van een toepassing. |
|Java | Ondersteund, JDK-ondersteuning voor TLS 1,2 is toegevoegd in [JDK 6 update 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) en [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 maakt [standaard gebruik van TLS 1,2](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Linux-distributies meestal afhankelijk zijn van [OpenSSL](https://www.openssl.org) voor ondersteuning van TLS 1.2.  | Controleer de [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) om te bevestigen van uw versie van OpenSSL wordt ondersteund.|
| Windows 8.0-10 | Ondersteund en standaard ingeschakeld. | Om te bevestigen dat u nog steeds gebruikt de [standaardinstellingen](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| WindowsServer 2012-2016 | Ondersteund en standaard ingeschakeld. | Om te bevestigen dat u nog steeds gebruikt de [standaardinstellingen](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 en Windows Server 2008 R2 SP1 | Ondersteund, maar niet standaard ingeschakeld. | Zie de [registerinstellingen voor Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) pagina voor meer informatie over het inschakelen.  |
| Windows Server 2008 SP2 | Ondersteuning voor TLS 1.2 is een update vereist. | Zie [Update voor het toevoegen van ondersteuning voor TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) in Windows Server 2008 SP2. |
|Windows Vista | Niet ondersteund. | N/A

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Controleren welke versie van OpenSSL uw Linux-distributie wordt uitgevoerd

Als u wilt controleren welke versie van OpenSSL u hebt geïnstalleerd, opent u de Terminal en voert u het volgende uit:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Een test-TLS 1,2-trans actie uitvoeren op Linux

Voor het uitvoeren van een basis voorbereidende test om te controleren of uw Linux-systeem kan communiceren via TLS 1,2. Open de Terminal en voer de volgende handelingen uit:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Persoons gegevens die zijn opgeslagen in Application Insights

In ons [Application Insights persoonlijke gegevens artikel](../../azure-monitor/platform/personal-data-mgmt.md) wordt dit probleem uitvoerig besproken.

#### <a name="can-my-users-turn-off-application-insights"></a>Kunnen mijn gebruikers Application Insights uitschakelen?
Niet rechtstreeks. We bieden geen switch die uw gebruikers kunnen uitvoeren om Application Insights uit te scha kelen.

U kunt een dergelijke functie echter implementeren in uw toepassing. Alle Sdk's bevatten een API-instelling waarmee telemetrie-verzameling wordt uitgeschakeld. 

## <a name="data-sent-by-application-insights"></a>Gegevens die worden verzonden door Application Insights
De Sdk's verschillen tussen platforms en er zijn verschillende onderdelen die u kunt installeren. (Zie [Application Insights-overzicht][start].) Elk onderdeel verzendt verschillende gegevens.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Klassen van gegevens die in verschillende scenario's worden verzonden

| Uw actie | Verzamelde gegevens klassen (zie volgende tabel) |
| --- | --- |
| [Application Insights SDK toevoegen aan een .NET-webproject][greenbrown] |ServerContext<br/>Afgeleid<br/>Prestatiemeteritems<br/>Aanvragen<br/>**Uitzonderingen**<br/>Sessie<br/>Gebruikers |
| [Status Monitor installeren op IIS][redfield] |Afhankelijkheden<br/>ServerContext<br/>Afgeleid<br/>Prestatiemeteritems |
| [Application Insights SDK toevoegen aan een Java-Web-app][java] |ServerContext<br/>Afgeleid<br/>Aanvraag<br/>Sessie<br/>Gebruikers |
| [Java script-SDK toevoegen aan de webpagina][client] |ClientContext <br/>Afgeleid<br/>Pagina<br/>ClientPerf<br/>Ajax |
| [Standaard eigenschappen definiëren][apiproperties] |**Eigenschappen** voor alle standaard-en aangepaste gebeurtenissen |
| [TrackMetric aanroepen][api] |Numerieke waarden<br/>**Eigenschappen** |
| [Nummer van oproep *][api] |Gebeurtenisnaam<br/>**Eigenschappen** |
| [TrackException aanroepen][api] |**Uitzonderingen**<br/>Stack dump<br/>**Eigenschappen** |
| Er kunnen geen gegevens worden verzameld met de SDK. Bijvoorbeeld: <br/> -geen toegang tot prestatie meter items<br/> -uitzonde ring in de initialisatie functie voor telemetrie |SDK-diagnose |

Voor [sdk's voor andere platforms][platforms]raadpleegt u hun documenten.

#### <a name="the-classes-of-collected-data"></a>De klassen van verzamelde gegevens

| Klasse verzamelde gegevens | Bevat (geen volledige lijst) |
| --- | --- |
| **Eigenschappen** |**Alle gegevens, bepaald door uw code** |
| DeviceContext |Id, IP, land instelling, model apparaat, netwerk, netwerk type, OEM-naam, scherm resolutie, Rolinstantie, rolnaam, apparaattype |
| ClientContext |Besturings systeem, land instelling, taal, netwerk, venster resolutie |
| Sessie |sessie-id |
| ServerContext |Computer naam, land instelling, besturings systeem, apparaat, gebruikers sessie, gebruikers context, bewerking |
| Afgeleid |Geo-locatie van IP-adres, tijds tempel, besturings systeem, browser |
| Metrische gegevens |Naam en waarde van de metriek |
| Events |Gebeurtenis naam en-waarde |
| PageViews |URL en pagina naam of scherm naam |
| Client prestaties |URL/pagina naam, laad tijd browser |
| Ajax |HTTP-aanroepen van de webpagina naar de server |
| Aanvragen |URL, duur, respons code |
| Afhankelijkheden |Type (SQL, HTTP,...), connection string of URI, synchronisatie/async, duur, geslaagd, SQL-instructie (met Status Monitor) |
| **Uitzonderingen** |Type, **bericht**, oproep stacks, bron bestand en regel nummer, thread-id |
| Valt |Proces-id, bovenliggende proces-id, crash-thread-id; toepassings patch, id, build;  type uitzonde ring, adres, reden; verborgen symbolen en registers, binaire begin-en eind adressen, binaire naam en pad, CPU-type |
| Trace |**Bericht** -en ernst niveau |
| Prestatiemeteritems |Processor tijd, beschikbaar geheugen, aanvraag frequentie, uitzonderings frequentie, privé-bytes verwerken, i/o-frequentie, aanvraag duur, lengte van aanvraag wachtrij |
| Beschikbaarheid |Reactie code van webtest, duur van elke test stap, test naam, tijds tempel, geslaagd, reactie tijd, test locatie |
| SDK-diagnose |Bericht of uitzonde ring traceren |

U kunt [een aantal van de gegevens uitschakelen door ApplicationInsights. config te bewerken][config]

> [!NOTE]
> Client-IP wordt gebruikt om geografische locatie af te leiden, maar standaard worden IP-gegevens niet meer opgeslagen en worden alle nullen naar het gekoppelde veld geschreven. Voor meer informatie over het afhandelen van persoonlijke gegevens kunt u dit [artikel](../../azure-monitor/platform/personal-data-mgmt.md#application-data)aanbevelen. Als u IP-adres gegevens moet opslaan in het artikel over de [IP-adres verzameling](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) , wordt u door de opties geleid.

## <a name="credits"></a>Credits
Dit product bevat GeoLite2-gegevens die zijn gemaakt door MaxMind [https://www.maxmind.com](https://www.maxmind.com), die beschikbaar zijn via.



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
