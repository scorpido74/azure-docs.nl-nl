---
title: Analytics-gegevensbeveiliging Meld | Microsoft Docs
description: Meer informatie over hoe de Log Analytics beschermt uw privacy en uw gegevens worden beveiligd.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2019
ms.openlocfilehash: 0ac169060f7ba0e58aeb3e36e3af1629b6453fc1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667360"
---
# <a name="log-analytics-data-security"></a>Meld u Analytics-gegevensbeveiliging
Dit document is bedoeld om informatie te verschaffen die specifiek is voor Log Analytics, een functie van Azure Monitor, om de informatie over [Vertrouwenscentrum van Azure](../../security/fundamentals/trust-center.md)aan te vullen.  

In dit artikel wordt uitgelegd hoe data door Log Analytics wordt verzameld, verwerkt en beveiligd. Agents kunt u verbinding maken met de webservice, System Center Operations Manager gebruiken voor het verzamelen van operationele gegevens of gegevens ophalen uit Azure diagnostics voor gebruik door Log Analytics. 

De service Log Analytics beheert uw cloud-gebaseerde gegevens veilig met behulp van de volgende methoden:

* scheiding van gegevens
* Bewaartijd van gegevens
* Fysieke beveiliging
* incidentbeheer
* Naleving
* standaardcertificeringen van beveiliging

Neem contact met ons op met eventuele vragen, suggesties of problemen met betrekking tot een van de volgende informatie, waaronder ons beveiligings beleid voor [ondersteunings opties voor Azure](https://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Verzenden van gegevens veilig gebruik TLS 1.2 

Als u wilt controleren of de beveiliging van gegevens die onderweg zijn naar Log Analytics, we raden u aan de agent configureren voor het gebruik van ten minste Transport Layer Security (TLS) 1.2. Oudere versies van TLS/Secure Sockets Layer (SSL) zijn kwetsbaar voor aanvallen en terwijl ze nog steeds werken om achterwaartse compatibiliteit mogelijk te maken, worden ze **niet aanbevolen**en wordt de branche snel verplaatst om ondersteuning voor deze oudere protocollen te annuleren. 

De [PCI Security Standards Council](https://www.pcisecuritystandards.org/) heeft een [deadline ingesteld van 30 juni 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) om oudere versies van TLS/SSL uit te scha kelen en te upgraden naar meer beveiligde protocollen. Nadat Azure ondersteuning voor oudere, komt als uw agents niet kunnen via ten minste communiceren TLS 1.2 u niet mogelijk zou zijn om gegevens te verzenden naar Log Analytics. 

We raden niet expliciet instelling uw agent alleen gebruik van TLS 1.2, tenzij dit absoluut noodzakelijk is, omdat deze functies waarmee u kunt automatisch detecteren en te profiteren van de nieuwere veiliger protocollen zodra deze beschikbaar is, bijvoorbeeld van beveiliging op het platform kunt verbreken Als TLS 1.3. 

### <a name="platform-specific-guidance"></a>Platform-specifieke richtlijnen

|Platform/taal | Ondersteuning | Meer informatie |
| --- | --- | --- |
|Linux | Linux-distributies zijn vaak afhankelijk van [openssl](https://www.openssl.org) voor TLS 1,2-ondersteuning.  | Controleer de [openssl wijzigingen logboek](https://www.openssl.org/news/changelog.html) om te bevestigen dat uw versie van openssl wordt ondersteund.|
| Windows 8.0-10 | Ondersteund en standaard ingeschakeld. | Om te bevestigen dat u nog steeds de [standaard instellingen](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)gebruikt.  |
| WindowsServer 2012-2016 | Ondersteund en standaard ingeschakeld. | Controleren of u nog steeds de [standaard instellingen](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) gebruikt |
| Windows 7 SP1 en Windows Server 2008 R2 SP1 | Ondersteund, maar niet standaard ingeschakeld. | Zie de pagina met [register instellingen voor Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) voor meer informatie over het inschakelen van.  |

## <a name="data-segregation"></a>scheiding van gegevens
Nadat uw gegevens worden opgenomen door de Log Analytics-service, worden de gegevens bewaard logische manier apart op elk onderdeel van de service. Alle gegevens worden gemarkeerd per werkruimte. Deze markering blijft aanwezig gedurende de levenscyclus van de gegevens en deze wordt afgedwongen op elke laag van de service. Uw gegevens worden opgeslagen in een specifieke database in het opslagcluster in de regio die u hebt geselecteerd.

## <a name="data-retention"></a>Bewaartijd van gegevens
Geïndexeerde log search gegevens worden opgeslagen en behouden op basis van uw prijsplan. Zie [log Analytics prijzen](https://azure.microsoft.com/pricing/details/log-analytics/)voor meer informatie.

Als onderdeel van uw [abonnements overeenkomst](https://azure.microsoft.com/support/legal/subscription-agreement/)houdt micro soft uw gegevens volgens de voor waarden van de overeenkomst.  Wanneer gegevens van de klant wordt verwijderd, worden er zijn geen fysieke stations vernietigd.  

De volgende tabel vindt u enkele van de beschikbare oplossingen en bevat voorbeelden van het type van de gegevens die zij verzamelen.

| **Oplossing** | **Gegevenstypen** |
| --- | --- |
| Capaciteit en prestaties |Prestatiegegevens en metagegevens |
| Updatebeheer |Metagegevens en statusinformatie gegevens |
| Logboekbeheer |Gebruiker gedefinieerde gebeurtenislogboeken, Windows-gebeurtenislogboeken en/of IIS-logboeken |
| Tracering wijzigen |Software-inventaris, service voor Windows en Linux-daemon metagegevens en metagegevens van het bestand Windows/Linux |
| SQL- en Active Directory-evaluatie |WMI-gegevens, registergegevens, prestatiegegevens en dynamische Beheerweergave van SQL Server-resultaten weergeven |

De volgende tabel ziet u voorbeelden van gegevenstypen:

| **Gegevenstype** | **Fields** |
| --- | --- |
| Waarschuwing |Ontvang een waarschuwing naam, beschrijving van de waarschuwing, BaseManagedEntityId, probleem-ID, IsMonitorAlert, RuleId, ResolutionState, prioriteit, ernst, categorie, de eigenaar, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuratie |Klant-id, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Gebeurtenis |Gebeurtenis-id, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, getal, categorie, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Opmerking:** Wanneer u gebeurtenissen met aangepaste velden in het Windows-gebeurtenis logboek schrijft, worden deze door Log Analytics verzameld. |
| Metagegevens |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IP-adres, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Adres NetbiosDomainName, LogicalProcessors, DNS-naam, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Prestaties |Objectnaam, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Status |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fysieke beveiliging
De Log Analytics-service wordt beheerd door Microsoft-personeel en alle activiteiten worden vastgelegd en kunnen worden gecontroleerd. Log Analytics wordt gebruikt als een Azure-Service en voldoet aan alle vereisten voor Azure-naleving en beveiliging. U kunt details weer geven over de fysieke beveiliging van Azure-assets op pagina 18 van het [overzicht van Microsoft Azure beveiliging](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fysieke toegangsrechten voor het beveiligen van gebieden worden gewijzigd binnen één werkdag voor iedereen die heeft niet langer verantwoordelijk is voor de Log Analytics-service, inclusief overdracht en beëindiging. Meer informatie over de wereld wijde fysieke infra structuur die [wordt gebruikt voor micro soft-data centers](https://azure.microsoft.com/global-infrastructure/).

## <a name="incident-management"></a>incidentbeheer
Log Analytics heeft een incidentbeheerproces die voor alle Microsoft-services worden aangehouden. Om samen te vatten, we:

* Gebruikt een model met gedeelde verantwoordelijkheid waar een deel van de verantwoordelijkheid van de beveiliging is van Microsoft en een gedeelte behoort tot de klant
* Azure beveiligingsincidenten beheren:
  * Een onderzoek tijdens de detectie van een incident starten
  * Evalueer de impact en urgentie van een incident met een teamlid in aanroep reageren op incidenten. Op basis van gegevens, de evaluatie kan of niet meer escalatie naar het security response team kan leiden.
  * Diagnose van een incident met beveiligingsexperts voor het antwoord aan de technische of forensisch onderzoek uitvoeren, het identificeren van strategieën voor insluiting, voor risicobeperking en tijdelijke oplossing. Als het beveiligingsteam ervan overtuigd is dat gegevens van de klant kan hebben worden blootgesteld aan een onwettig is of niet-gemachtigde persoon, begint de parallelle uitvoering van het proces van de klant Incident melding parallel.  
  * Stabiliseren en herstellen van het incident. Het incident response team maakt u een herstelplan om het probleem te verhelpen. Crisis containment stappen zoals het betrokken systemen in quarantaine plaatsen kunnen zich voordoen onmiddellijk en parallel met diagnose. Mogelijk langere termijn oplossingen worden gepland die zich voordoen nadat de onmiddellijke risico is verstreken.  
  * Het incident sluit en een daarmee uitvoeren. Het incident response team maakt een daarmee die geeft een overzicht van de details van het incident, met de intentie te herzien beleidsregels, procedures en processen om te voorkomen dat een terugkeerpatroon van de gebeurtenis.
* Informeer klanten over beveiligingsincidenten:
  * Het bereik van de betrokken klanten en voor iedereen die is van invloed als een kennisgeving mogelijk gedetailleerde bepalen
  * Maak een aankondiging voor klanten met gedetailleerde voldoende informatie zodat ze kunnen een onderzoek op hun einde uitvoeren en voldoen aan eventuele verplichtingen die zijn aangebracht in hun eindgebruikers tijdens niet ten onrechte meldingen vertragen.
  * Controleer en declareren van het incident, indien nodig.
  * Informeer klanten met een incident melding onredelijk onmiddellijk en in overeenstemming met de juridische of contractuele toezegging. Meldingen van beveiligingsincidenten wordt geleverd aan een of meer van de beheerders van de klant door middel van die Microsoft selecteert, onder andere via e-mail.
* Gedragscode team gereedheid en training:
  * Medewerkers van Microsoft zijn vereist voor het voltooien van de beveiligings- en awareness training, waarmee ze om te bepalen en mogelijke beveiligingsproblemen rapporteren.  
  * Operators werken op de Microsoft Azure-service hebben toevoeging training verplichtingen rond de toegang tot systemen met gevoelige informatie die als host fungeert voor klantgegevens.
  * Medewerkers van Microsoft security response ontvangen speciale training voor hun rollen

Als er verlies van gegevens van de klant optreedt, melden we elke klant binnen een dag. Verlies van gegevens van de klant heeft echter nooit is opgetreden met de service. 

Zie [Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf)(Engelstalig) voor meer informatie over hoe micro soft reageert op beveiligings incidenten.

## <a name="compliance"></a>Naleving
De informatie beveiliging en het beheer programma voor de Log Analytics software ontwikkeling en het service team ondersteunt zijn bedrijfs vereisten en voldoet aan de wetten en voor schriften zoals beschreven in [Microsoft Azure vertrouwens centrum](https://azure.microsoft.com/support/trust-center/) en naleving van het [micro soft vertrouwens centrum](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Hoe Log Analytics wordt tot stand gebracht voor beveiliging, identificeert beveiligingsmechanismen, beheert en bewaakt de risico's worden er ook beschreven. Jaarlijks, we revisie beleidsregels, standaarden en procedures en richtlijnen.

Elk teamlid ontwikkeling ontvangt beveiligingstraining formele aanvraag. Intern, gebruiken we een versiebeheersysteem voor softwareontwikkeling. Elk softwareproject is beveiligd door het systeem voor versiebeheer.

Microsoft heeft een beveiliging en naleving team dat verantwoordelijk is voor en alle services in Microsoft beoordeelt. Information security officers maken van het team en ze niet zijn gekoppeld aan het engineering-teams die Log Analytics ontwikkelt. De security officers hebben hun eigen Managementketen en uitvoering van een onafhankelijke evaluaties van producten en services om ervoor te zorgen voor beveiliging en naleving.

Raad van bestuur van Microsoft is op de hoogte met in een jaarrapport over alle informatie beveiligingsprogramma's bij Microsoft.

De ontwikkeling van Log Analytics-software en service-team werkt actief met de teams Microsoft Legal en naleving en andere IT-partners aan te schaffen verschillende certificeringen.

## <a name="certifications-and-attestations"></a>Certificeringen en verklaringen
Azure Log Analytics voldoet aan de volgende vereisten:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Betaal kaart (PCI-compatibel) Data Security Standard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) door de PCI Security Standards-Raad.
* [Service organization Controls (SOC) 1 type 1 en SOC 2 type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) voldoen aan het beleid
* [HIPAA en Hitech](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) voor bedrijven die een HIPAA-overeenkomst voor bedrijven hebben
* Windows gemeenschappelijke Engineering Criteria
* Microsoft Trustworthy Computing
* Als een Azure-service voldoen de onderdelen die gebruikmaakt van Log Analytics aan vereisten voor naleving van Azure. Meer informatie vindt u op de naleving van het [vertrouwens centrum van micro soft](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> In sommige certificeringen/verklaringen wordt Log Analytics vermeld onder de voormalige naam van *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Cloud computing security-gegevensstroom
Het volgende diagram toont een cloud-beveiligingsarchitectuur als de stroom van gegevens van uw bedrijf en hoe deze is beveiligd, is verplaatst naar de service Log Analytics, uiteindelijk worden gezien door u in Azure portal. Meer informatie over elke stap volgt het diagram.

![Afbeelding van Log Analytics-gegevensverzameling en -beveiliging](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Registreer u voor Log Analytics en gegevens verzamelen
Bij uw organisatie gegevens verzenden naar Log Analytics, configureert u een Windows- of Linux agent wordt uitgevoerd op virtuele machines van Azure of op virtuele of fysieke computers in uw omgeving of andere cloudprovider.  Als u Operations Manager gebruikt, uit de beheergroep configureert u de Operations Manager-agent. Gebruikers (die mogelijk worden u, andere individuele gebruikers of een groep personen) een of meer Log Analytics-werkruimten maken en registreren van agents met behulp van een van de volgende accounts:

* [Organisatie-ID](../../active-directory/fundamentals/sign-up-organization.md)
* [Micro soft-account: Outlook, Office Live, MSN](https://account.microsoft.com/account)

Een Log Analytics-werkruimte is waaruit gegevens worden verzameld, samengevoegd, geanalyseerd en gepresenteerd. Een werkruimte wordt voornamelijk gebruikt als een manier om gegevens te partitioneren en elke werkruimte is uniek. Bijvoorbeeld, als u wilt uw productiegegevens met één werkruimte worden beheerd en uw testgegevens die worden beheerd met een andere werkruimte. Werkruimten helpen ook een beheerder gebruikerstoegang tot de gegevens. Elke werkruimte kunnen meerdere gebruikersaccounts die zijn gekoppeld aan deze, en elk gebruikersaccount dat toegang tot meerdere Log Analytics-werkruimten. U maken werkruimten op basis van datacentrumregio.

Voor Operations Manager maakt de Operations Manager-beheergroep verbinding met een Log Analytics-service. U configureert welke agent beheerde systemen in de beheergroep zijn toegestaan voor het verzamelen en verzenden van gegevens naar de service. Gegevens van deze oplossingen zijn, afhankelijk van de oplossing die u hebt ingeschakeld, hetzij rechtstreeks vanuit een Operations Manager-beheerserver wordt verzonden naar de Log Analytics-service, of vanwege de hoeveelheid gegevens die zijn verzameld door het systeem door agents beheerde rechtstreeks vanuit worden verzonden de agent met de service. Voor systemen die niet worden bewaakt door Operations Manager, verbindt elk veilig met de service Log Analytics rechtstreeks.

Alle communicatie tussen verbonden systemen en de Log Analytics-service is versleuteld. Het TLS (HTTPS)-protocol wordt gebruikt voor versleuteling.  Het Microsoft SDL-proces wordt gevolgd om ervoor te zorgen dat log Analytics is bijgewerkt met de meest recente ontwikkelingen in de cryptografische protocollen.

Elk type van de agent verzamelt gegevens voor Log Analytics. Het type van de gegevens die worden verzameld, is afhankelijk van de soorten oplossingen die worden gebruikt. U kunt een samen vatting van gegevens verzameling weer geven op [log Analytics oplossingen toevoegen vanuit de Oplossingengalerie](../../azure-monitor/insights/solutions.md). Meer gedetailleerde informatie van de verzameling is daarnaast beschikbaar voor de meeste oplossingen. Een oplossing is een bundel van vooraf gedefinieerde weergaven, log zoekquery's, regels voor het verzamelen van gegevens en Verwerkingslogica voor. Alleen beheerders kunnen Log Analytics gebruiken voor het importeren van een oplossing. Nadat de oplossing is geïmporteerd, wordt deze verplaatst naar de Operations Manager-beheerservers (indien gebruikt), en vervolgens naar alle agents die u hebt gekozen. Daarna wordt verzamelen de agents de gegevens.

## <a name="2-send-data-from-agents"></a>2. gegevens verzenden van agents
U alle typen van de agent zich bij een registratie-sleutel en een beveiligde verbinding tot stand is gebracht tussen de agent en de Log Analytics-service met behulp van verificatie op basis van certificaten en SSL met poort 443. Log Analytics maakt gebruik van een geheime store te genereren en beheren van sleutels. Persoonlijke sleutels om de 90 dagen worden gedraaid en worden opgeslagen in Azure en worden beheerd door de Azure-bewerkingen die strikt van wetten en voorschriften procedures volgen.

De beheergroep geregistreerd bij een Log Analytics-werkruimte maakt met Operations Manager, een beveiligde HTTPS-verbinding met een Operations Manager-beheerserver.

Voor Windows of Linux-agents worden uitgevoerd op Azure virtual machines, wordt een alleen-lezen opslagsleutel gebruikt om te lezen van diagnostische gebeurtenissen in Azure-tabellen.  

Met elke agent die rapporteert aan een beheergroep van Operations Manager die is geïntegreerd met Log Analytics, als de beheerserver kan niet communiceren met de service voor een bepaalde reden, de verzamelde gegevens lokaal opgeslagen in een tijdelijke cache op de management de server.   Ze proberen te verzenden van de gegevens om de acht minuten gedurende twee uur.  Voor gegevens die de beheerserver omzeilt en rechtstreeks naar Log Analytics wordt verzonden, is het gedrag consistent met de Windows-agent.  

De Windows- of management server-agent in de cache opgeslagen gegevens wordt beveiligd door de referentie-archief van het besturingssysteem. Als de service niet kan van de gegevens na twee uur verwerken, wordt de gegevens in de agents een wachtrij. Als de wachtrij vol is, start de agent verwijderen van gegevenstypen, beginnend met de prestatiegegevens. De agent de wachtrijlimiet is een registersleutel, zodat u wijzigen kunt, indien nodig. Verzamelde gegevens worden gecomprimeerd en verzonden naar de service, overslaan van de databases van Operations Manager management groep, zodat deze geen een elke belasting door toegevoegd aan deze. Nadat de verzamelde gegevens wordt verzonden, wordt deze verwijderd uit de cache.

Zoals hierboven beschreven, wordt de gegevens van de beheerserver of rechtstreeks verbonden agents via SSL verzonden naar Microsoft Azure-datacenters. U kunt eventueel ExpressRoute gebruiken voor extra beveiliging voor de gegevens. ExpressRoute is een manier om rechtstreeks verbinding maken met Azure vanuit uw bestaande WAN-netwerk, zoals een multiprotocol label switching (MPLS) VPN dat wordt geleverd door een netwerkserviceprovider. Zie [ExpressRoute](https://azure.microsoft.com/services/expressroute/)voor meer informatie.

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. de Log Analytics-service ontvangt en verwerkt gegevens
De Log Analytics-service zorgt ervoor dat binnenkomende gegevens van een vertrouwde bron is door het valideren van certificaten en de integriteit van gegevens met Azure-verificatie. De niet-verwerkte onbewerkte gegevens wordt vervolgens opgeslagen in een Azure Event Hub in de regio die de gegevens uiteindelijk worden opgeslagen in rust. Het type van de gegevens die zijn opgeslagen, is afhankelijk van de soorten oplossingen die zijn geïmporteerd en gebruikt om gegevens te verzamelen. Vervolgens wordt de met Log Analytics service processen de onbewerkte gegevens en neemt deze in de database.

De bewaarperiode van de verzamelde gegevens opgeslagen in de database, is afhankelijk van de geselecteerde prijsstelling. Voor de *gratis* laag zijn verzamelde gegevens zeven dagen beschikbaar. Verzamelde gegevens voor de laag *betaald* zijn standaard 31 dagen beschikbaar, maar kunnen worden verlengd tot 730 dagen. Gegevens worden opgeslagen versleuteld in rust in Azure storage om te controleren of de vertrouwelijkheid van gegevens, en de gegevens worden gerepliceerd binnen de regio met lokaal redundante opslag (LRS). De laatste twee weken van gegevens worden ook opgeslagen in de cache op SSD-basis en deze cache is versleuteld.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. gebruik Log Analytics om toegang te krijgen tot de gegevens
Voor toegang tot uw Log Analytics-werkruimte moet u zich aanmeldt bij de Azure-portal met behulp van de organisatie-account of een Microsoft-account dat u eerder hebt ingesteld. Al het verkeer tussen de portal en de Log Analytics-service worden verzonden via een beveiligde HTTPS-kanaal. Wanneer u de portal, een sessie-ID is gegenereerd op de gebruiker-client (webbrowser) en gegevens worden opgeslagen in een lokale cache totdat de sessie wordt beëindigd. Wanneer is afgesloten, wordt de cache verwijderd. Client-side-cookies niet persoonlijk identificeerbare informatie bevatten, worden niet automatisch verwijderd. Sessiecookies HTTPOnly zijn gemarkeerd en worden beveiligd. Na een vooraf bepaald niet-actieve periode, is de Azure portal-sessie beëindigd.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het verzamelen van gegevens met Log Analytics voor uw virtuele Azure-machines na de [Snelstartgids van Azure VM](../../azure-monitor/learn/quick-collect-azurevm.md).  

*  Als u gegevens wilt verzamelen van fysieke of virtuele Windows-of Linux-computers in uw omgeving, raadpleegt u de [Quick start voor Linux-computers](../../azure-monitor/learn/quick-collect-linux-computer.md) of [Quick start voor Windows-computers](../../azure-monitor/learn/quick-collect-windows-computer.md)

