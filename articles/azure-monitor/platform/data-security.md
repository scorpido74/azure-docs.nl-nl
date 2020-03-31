---
title: Beveiliging van log Analytics-gegevens | Microsoft Documenten
description: Meer informatie over hoe Log Analytics uw privacy beschermt en uw gegevens beveiligt.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2019
ms.openlocfilehash: 63d8d8d3701a9adca4bd01e6e061877f5d0bd245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333362"
---
# <a name="log-analytics-data-security"></a>Gegevensbeveiliging van Log Analytics
Dit document is bedoeld om informatie te verstrekken die specifiek is voor Log Analytics, een functie van Azure Monitor, om de informatie in [Azure Trust Center](../../security/fundamentals/trust-center.md)aan te vullen.  

In dit artikel wordt uitgelegd hoe data door Log Analytics wordt verzameld, verwerkt en beveiligd. U agents gebruiken om verbinding te maken met de webservice, System Center Operations Manager te gebruiken om operationele gegevens te verzamelen of gegevens op te halen uit Azure-diagnoses voor gebruik door Log Analytics. 

De Log Analytics-service beheert uw cloudgegevens veilig met behulp van de volgende methoden:

* Gegevensscheiding
* Bewaartijd van gegevens
* Fysieke beveiliging
* Incidentbeheer
* Naleving
* Certificeringen voor veiligheidsnormen

Neem contact met ons op met vragen, suggesties of problemen met betrekking tot een van de volgende informatie, waaronder ons beveiligingsbeleid bij [Azure-ondersteuningsopties.](https://azure.microsoft.com/support/options/)

## <a name="sending-data-securely-using-tls-12"></a>Gegevens veilig verzenden met TLS 1.2 

Om de beveiliging van gegevens die onderweg zijn naar Log Analytics te verzekeren, raden we u ten zeerste aan om de agent te configureren om ten minste TLS(1.2) (Transport Layer Security) te gebruiken. Oudere versies van TLS/Secure Sockets Layer (SSL) zijn kwetsbaar bevonden en hoewel ze momenteel nog werken om achterwaartse compatibiliteit mogelijk te maken, worden ze **niet aanbevolen**en de industrie gaat snel over om de ondersteuning voor deze oudere protocollen op te geven. 

De [PCI Security Standards Council](https://www.pcisecuritystandards.org/) heeft een deadline van [30 juni 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) ingesteld om oudere versies van TLS/SSL uit te schakelen en te upgraden naar veiligere protocollen. Zodra Azure de legacy-ondersteuning heeft laten vallen, u gegevens niet meer verzenden naar Log Analytics als uw medewerkers niet meer via TLS 1.2 kunnen communiceren. 

We raden u niet aan uw agent expliciet in te stellen tls 1.2 alleen te gebruiken, tenzij dit absoluut noodzakelijk is, omdat het beveiligingsfuncties op platformniveau kan breken waarmee u automatisch nieuwere, veiligere protocollen detecteren en gebruiken wanneer deze beschikbaar zijn, zoals als TLS 1.3. 

### <a name="platform-specific-guidance"></a>Platformspecifieke richtlijnen

|Platform/Taal | Ondersteuning | Meer informatie |
| --- | --- | --- |
|Linux | Linux-distributies zijn meestal afhankelijk van [OpenSSL](https://www.openssl.org) voor TLS 1.2-ondersteuning.  | Controleer de [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) om te bevestigen dat uw versie van OpenSSL wordt ondersteund.|
| Windows 8.0 - 10 | Standaard ondersteund en ingeschakeld. | Om te bevestigen dat u nog steeds de [standaardinstellingen gebruikt.](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)  |
| Windows Server 2012 - 2016 | Standaard ondersteund en ingeschakeld. | Om te bevestigen dat u nog steeds de [standaardinstellingen gebruikt](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 en Windows Server 2008 R2 SP1 | Ondersteund, maar standaard niet ingeschakeld. | Zie de [registerinstellingenpagina (Transport Layer Security)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) voor meer informatie over het inschakelen.  |

## <a name="data-segregation"></a>Gegevensscheiding
Nadat uw gegevens zijn ingenomen door de Log Analytics-service, worden de gegevens logisch gescheiden gehouden voor elk onderdeel in de service. Alle gegevens worden per werkruimte getagd. Deze markering blijft aanwezig gedurende de levenscyclus van de gegevens en deze wordt afgedwongen op elke laag van de service. Uw gegevens worden opgeslagen in een speciale database in het opslagcluster in de regio die u hebt geselecteerd.

## <a name="data-retention"></a>Bewaartijd van gegevens
Geïndexeerde logzoekgegevens worden opgeslagen en bewaard volgens uw prijsplan. Zie [Log Analytics Pricing](https://azure.microsoft.com/pricing/details/log-analytics/)voor meer informatie.

Als onderdeel van uw [abonnementsovereenkomst](https://azure.microsoft.com/support/legal/subscription-agreement/)bewaart Microsoft uw gegevens volgens de voorwaarden van de overeenkomst.  Wanneer klantgegevens worden verwijderd, worden er geen fysieke schijven vernietigd.  

In de volgende tabel worden enkele beschikbare oplossingen weergegeven en worden voorbeelden gegeven van het type gegevens dat ze verzamelen.

| **Oplossing** | **Gegevenstypen** |
| --- | --- |
| Capaciteit en prestaties |Prestatiegegevens en metagegevens |
| Updatebeheer |Metagegevens en staatsgegevens |
| Logboekbeheer |Door de gebruiker gedefinieerde gebeurtenislogboeken, Windows-gebeurtenislogboeken en/of IIS-logboeken |
| Tracering wijzigen |Softwareinventaris, Windows-service en Linux-daemonmetagegevens en Windows/Linux-bestandsmetagegevens |
| SQL- en Active Directory-beoordeling |WMI-gegevens, registergegevens, prestatiegegevens en sql server dynamische beheerweergaveresultaten |

In de volgende tabel ziet u voorbeelden van gegevenstypen:

| **Gegevenstype** | **Fields** |
| --- | --- |
| Waarschuwing |Waarschuwingsnaam, waarschuwingsbeschrijving, basemanagedentityid, probleem-ID, IsMonitorAlert, Ruleid, ResolutionState, Prioriteit, Ernst, Categorie, Eigenaar, Opgelostdoor, timeraised, timeadded, LastModified, LastModifiedby, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuratie |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Gebeurtenis |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Let op:** Wanneer u gebeurtenissen met aangepaste velden in het Windows-gebeurtenislogboek schrijft, verzamelt Log Analytics deze. |
| Metagegevens |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Adres, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetinminutefromGreenwichTime |
| Prestaties |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Status |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fysieke beveiliging
De Log Analytics-service wordt beheerd door Microsoft-personeel en alle activiteiten worden geregistreerd en kunnen worden gecontroleerd. Log Analytics wordt beheerd als azure-service en voldoet aan alle azure-vereisten voor naleving en beveiliging. U details over de fysieke beveiliging van Azure-assets weergeven op pagina 18 van het [Microsoft Azure Security Overview.](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf) Fysieke toegangsrechten voor beveiligde gebieden worden binnen één werkdag gewijzigd voor iedereen die niet langer verantwoordelijk is voor de Log Analytics-service, inclusief overdracht en beëindiging. U lezen over de wereldwijde fysieke infrastructuur die we gebruiken bij [Microsoft Datacenters.](https://azure.microsoft.com/global-infrastructure/)

## <a name="incident-management"></a>Incidentbeheer
Log Analytics heeft een incidentmanagementproces waar alle Microsoft-services zich aan houden. Om samen te vatten, we:

* Gebruik een model voor gedeelde verantwoordelijkheid waarbij een deel van de beveiligingsverantwoordelijkheid bij Microsoft hoort en een deel van de klant is
* Azure-beveiligingsincidenten beheren:
  * Start een onderzoek na detectie van een incident
  * Beoordeel de impact en ernst van een incident door een incidentmedewerker op afroep. Op basis van bewijs kan de beoordeling al dan niet leiden tot verdere escalatie naar het security response team.
  * Diagnose van een incident door security response experts om het technische of forensisch onderzoek uit te voeren, containment, mitigatie en tijdelijke oplossing strategieën te identificeren. Als het beveiligingsteam van mening is dat klantgegevens mogelijk zijn blootgesteld aan een onwettigof ongeautoriseerd individu, begint de parallelle uitvoering van het proces voor incidentmeldingen van klanten parallel.  
  * Stabiliseer en herstel van het incident. Het incidentresponseteam maakt een herstelplan om het probleem te beperken. Crisisbeheersingsstappen zoals quarantining beïnvloede systemen kunnen onmiddellijk en parallel met de diagnose optreden. Er kunnen beperkingen op langere termijn worden gepland die optreden nadat het onmiddellijke risico is verstreken.  
  * Sluit het incident en voer een autopsie uit. Het incident response team maakt een post-mortem die de details van het incident schetst, met de bedoeling om beleid, procedures en processen te herzien om herhaling van de gebeurtenis te voorkomen.
* Stel klanten op de hoogte van beveiligingsincidenten:
  * Bepaal de omvang van de getroffen klanten en om iedereen die wordt beïnvloed zo gedetailleerd mogelijk een bericht te geven
  * Maak een kennisgeving om klanten gedetailleerde voldoende informatie te verstrekken, zodat ze een onderzoek aan hun kant kunnen uitvoeren en alle toezeggingen kunnen nakomen die ze aan hun eindgebruikers hebben gedaan, terwijl ze het meldingsproces niet onnodig vertragen.
  * Bevestig en verklaar het incident, indien nodig.
  * Breng klanten hiervan op de hoogte met een incidentmelding zonder onredelijke vertraging en in overeenstemming met een wettelijke of contractuele verbintenis. Meldingen van beveiligingsincidenten worden op alle mogelijke manieren die Microsoft selecteert, ook via e-mail, aan een of meer beheerders van een klant geleverd.
* Voer teamgereedheid en training uit:
  * Microsoft-personeel is verplicht om beveiligings- en bewustzijnstraining te voltooien, waardoor ze verdachte beveiligingsproblemen kunnen identificeren en rapporteren.  
  * Operators die werken aan de Microsoft Azure-service hebben trainingsverplichtingen met betrekking tot hun toegang tot gevoelige systemen die klantgegevens hosten.
  * Microsoft security response personeel ontvangt gespecialiseerde training voor hun rol

Als er klantgegevens verloren gaan, stellen we elke klant binnen één dag op de hoogte. Het verlies van klantgegevens heeft echter nooit plaatsgevonden bij de service. 

Zie Microsoft Azure Security Response in [de cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf)voor meer informatie over hoe Microsoft reageert op beveiligingsincidenten.

## <a name="compliance"></a>Naleving
Het informatiebeveiligings- en governanceprogramma van het Log Analytics-softwareontwikkelings- en serviceteam ondersteunt de bedrijfsvereisten en houdt zich aan wet- en regelgeving zoals beschreven in [het Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) en Microsoft Trust Center [Compliance.](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx) Hoe Log Analytics beveiligingsvereisten vaststelt, beveiligingscontroles identificeert, risico's beheert en controleert, worden daar ook beschreven. Jaarlijks beoordelen we politie, normen, procedures en richtlijnen.

Elk lid van het ontwikkelingsteam krijgt formele training voor applicatiebeveiliging. Intern gebruiken we een versiebeheersysteem voor softwareontwikkeling. Elk softwareproject wordt beschermd door het versiebeheersysteem.

Microsoft heeft een beveiligings- en complianceteam dat alle services in Microsoft overziet en beoordeelt. Information security officers vormen het team en ze zijn niet gekoppeld aan de engineering teams die Log Analytics ontwikkelt. De security officers hebben hun eigen managementketen en voeren onafhankelijke beoordelingen uit van producten en diensten om de veiligheid en naleving te waarborgen.

De raad van bestuur van Microsoft wordt op de hoogte gebracht door een jaarverslag over alle informatiebeveiligingsprogramma's bij Microsoft.

Het softwareontwikkel- en serviceteam van Log Analytics werkt actief samen met de Microsoft Legal and Compliance-teams en andere branchepartners om verschillende certificeringen te verkrijgen.

## <a name="certifications-and-attestations"></a>Certificeringen en verklaringen
Azure Log Analytics voldoet aan de volgende vereisten:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Pci DSS (PCI DSS) (Pci-beveiligingsstandaarden)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) voor betaalkaartindustrie (PCI-compatibel).
* [Service Organization Controls (SOC) 1 Type 1 en SOC 2 Type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) compliant
* [HIPAA en HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) voor bedrijven met een HIPAA Business Associate Agreement
* Windows Common Engineering Criteria
* Microsoft Betrouwbaar Computergebruik
* Als Azure-service voldoen de onderdelen die Door Log Analytics worden gebruikt aan de nalevingsvereisten van Azure. U meer lezen bij [Microsoft Trust Center Compliance](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> In sommige certificeringen/attestations wordt Log Analytics vermeld onder de vroegere naam *Operational Insights.*
>
>

## <a name="cloud-computing-security-data-flow"></a>Cloud computing-gegevensstroom
In het volgende diagram wordt een cloudbeveiligingsarchitectuur weergegeven als de informatiestroom van uw bedrijf en hoe deze wordt beveiligd, zoals wordt verplaatst naar de Log Analytics-service, die u uiteindelijk in de Azure-portal ziet. Meer informatie over elke stap volgt het diagram.

![Afbeelding van het verzamelen en beveiliging van Log Analytics-gegevens](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Meld u aan voor Log Analytics en verzamel gegevens
Als uw organisatie gegevens naar Log Analytics wilt verzenden, configureert u een Windows- of Linux-agent die wordt uitgevoerd op virtuele Azure-machines of op virtuele of fysieke computers in uw omgeving of andere cloudprovider.  Als u Operations Manager gebruikt, configureert u vanuit de beheergroep de operations manager-agent. Gebruikers (u, andere individuele gebruikers of een groep personen) maken een of meer Log Analytics-werkruimten en registreren agents met behulp van een van de volgende accounts:

* [Organisatie-ID](../../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft-account - Outlook, Office Live, MSN](https://account.microsoft.com/account)

Een log analytics-werkruimte is de plaats waar gegevens worden verzameld, samengevoegd, geanalyseerd en gepresenteerd. Een werkruimte wordt voornamelijk gebruikt als een middel om gegevens te partitioneren en elke werkruimte is uniek. U wilt bijvoorbeeld dat uw productiegegevens worden beheerd met één werkruimte en uw testgegevens worden beheerd met een andere werkruimte. Werkruimten helpen een beheerder ook om de gebruiker toegang te geven tot de gegevens. Aan elke werkruimte kunnen meerdere gebruikersaccounts zijn gekoppeld en elk gebruikersaccount heeft toegang tot meerdere Log Analytics-werkruimten. U maakt werkruimten op basis van het datacentergebied.

Voor Operations Manager legt de beheergroep Operations Manager een verbinding met de Log Analytics-service. Vervolgens configureert u welke door de agent beheerde systemen in de beheergroep gegevens mogen verzamelen en verzenden naar de service. Afhankelijk van de oplossing die u hebt ingeschakeld, worden gegevens van deze oplossingen rechtstreeks van een Operations Manager-beheerserver naar de Log Analytics-service verzonden, of vanwege de hoeveelheid gegevens die door het door de agent beheerde systeem worden verzameld, rechtstreeks vanuit de agent aan de dienst. Voor systemen die niet door Operations Manager worden gecontroleerd, wordt elk afzonderlijk rechtstreeks verbonden met de Log Analytics-service.

Alle communicatie tussen verbonden systemen en de Log Analytics-service is versleuteld. Het TLS-protocol (HTTPS) wordt gebruikt voor versleuteling.  Het Microsoft SDL-proces wordt gevolgd om ervoor te zorgen dat Log Analytics up-to-date is met de meest recente ontwikkelingen in cryptografische protocollen.

Elk type agent verzamelt gegevens voor Log Analytics. Het type gegevens dat wordt verzameld, is afhankelijk van de gebruikte soorten oplossingen. U een overzicht van het verzamelen van gegevens bekijken bij [Oplossingen toevoegen aan Logboekanalyse vanuit de Solutions Gallery.](../../azure-monitor/insights/solutions.md) Daarnaast is meer gedetailleerde verzamelinginformatie beschikbaar voor de meeste oplossingen. Een oplossing is een bundel van vooraf gedefinieerde weergaven, logboekzoekopdrachten, regels voor het verzamelen van gegevens en verwerkingslogica. Alleen beheerders kunnen Log Analytics gebruiken om een oplossing te importeren. Nadat de oplossing is geïmporteerd, wordt deze verplaatst naar de Operations Manager-beheerservers (indien gebruikt) en vervolgens naar alle agents die u hebt gekozen. Daarna verzamelen de agenten de gegevens.

## <a name="2-send-data-from-agents"></a>2. Gegevens verzenden van agenten
U registreert alle agenttypen met een inschrijvingssleutel en er wordt een beveiligde verbinding tot stand gebracht tussen de agent en de Log Analytics-service met behulp van verificatie op basis van certificaten en TLS met poort 443. Log Analytics gebruikt een geheime winkel om sleutels te genereren en te onderhouden. Privésleutels worden elke 90 dagen gedraaid en worden opgeslagen in Azure en worden beheerd door de Azure-bewerkingen die strikte regelgevings- en nalevingspraktijken volgen.

Met Operations Manager zorgt de beheergroep die is geregistreerd bij een Log Analytics-werkruimte voor een veilige HTTPS-verbinding met een Operations Manager-beheerserver.

Voor Windows- of Linux-agents die op virtuele Azure-machines worden uitgevoerd, wordt een alleen-lezen opslagsleutel gebruikt om diagnostische gebeurtenissen in Azure-tabellen te lezen.  

Als een agent rapporteert aan een Operations Manager-beheergroep die is geïntegreerd met Log Analytics, als de beheerserver om welke reden dan ook niet met de service kan communiceren, worden de verzamelde gegevens lokaal opgeslagen in een tijdelijke cache op het beheer Server.   Ze proberen de gegevens om de acht minuten twee uur opnieuw te verzenden.  Voor gegevens die de beheerserver omzeilen en rechtstreeks naar Log Analytics worden verzonden, is het gedrag consistent met de Windows-agent.  

De gegevens van de Windows- of beheerserveragent in de cache worden beschermd door het referentiearchief van het besturingssysteem. Als de service de gegevens na twee uur niet kan verwerken, zullen de agents de gegevens in de wachtrij plaatsen. Als de wachtrij vol raakt, begint de agent gegevenstypen te laten vallen, te beginnen met prestatiegegevens. De limiet voor de wachtrij van de agent is een registersleutel, zodat u deze indien nodig wijzigen. Verzamelde gegevens worden gecomprimeerd en naar de service verzonden, waardoor de databases van de operations manager-beheergroep worden omzeild, zodat er geen belasting aan wordt toegevoegd. Nadat de verzamelde gegevens zijn verzonden, wordt deze uit de cache verwijderd.

Zoals hierboven beschreven, worden gegevens van de beheerserver of direct verbonden agents via TLS verzonden naar Microsoft Azure-datacenters. Optioneel u ExpressRoute gebruiken om extra beveiliging voor de gegevens te bieden. ExpressRoute is een manier om rechtstreeks verbinding te maken met Azure vanuit uw bestaande WAN-netwerk, zoals een MPLS-vpn (Multi-protocol label switching) die wordt geleverd door een netwerkserviceprovider. Zie [ExpressRoute](https://azure.microsoft.com/services/expressroute/)voor meer informatie.

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. De Log Analytics-service ontvangt en verwerkt gegevens
De Log Analytics-service zorgt ervoor dat binnenkomende gegevens afkomstig zijn van een vertrouwde bron door certificaten en de gegevensintegriteit te valideren met Azure-verificatie. De onverwerkte ruwe gegevens worden vervolgens opgeslagen in een Azure Event Hub in de regio, waarna de gegevens uiteindelijk in rust worden opgeslagen. Het type gegevens dat wordt opgeslagen, is afhankelijk van de soorten oplossingen die zijn geïmporteerd en gebruikt om gegevens te verzamelen. Vervolgens verwerkt de Log Analytics-service de ruwe gegevens en neemt deze in de database op.

De bewaarperiode van verzamelde gegevens die in de database zijn opgeslagen, is afhankelijk van het geselecteerde prijsplan. Voor de *gratis* laag zijn verzamelde gegevens zeven dagen beschikbaar. Voor de laag *Betaald* zijn verzamelde gegevens standaard 31 dagen beschikbaar, maar kunnen ze worden verlengd tot 730 dagen. Gegevens worden versleuteld opgeslagen in Azure-opslag, om de vertrouwelijkheid van gegevens te garanderen en de gegevens worden gerepliceerd binnen de lokale regio met behulp van lokaal redundante opslag (LRS). De laatste twee weken van de gegevens worden ook opgeslagen in SSD-gebaseerde cache en deze cache is versleuteld.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Log Analytics gebruiken om toegang te krijgen tot de gegevens
Als u toegang wilt krijgen tot uw Log Analytics-werkruimte, meldt u zich aan bij de Azure-portal met het organisatieaccount of het Microsoft-account dat u eerder hebt ingesteld. Al het verkeer tussen de portal en de Log Analytics-service wordt verzonden via een beveiligd HTTPS-kanaal. Bij gebruik van de portal wordt een sessie-id gegenereerd op de gebruikersclient (webbrowser) en worden gegevens opgeslagen in een lokale cache totdat de sessie is beëindigd. Wanneer de cache wordt beëindigd, wordt deze verwijderd. Cookies aan de clientzijde, die geen persoonlijk identificeerbare informatie bevatten, worden niet automatisch verwijderd. Sessiecookies zijn gemarkeerd als HTTPOnly en zijn beveiligd. Na een vooraf bepaalde idle-periode wordt de Azure-portalsessie beëindigd.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het verzamelen van gegevens met Log Analytics voor uw Azure VM's na de [quickstart van Azure VM.](../../azure-monitor/learn/quick-collect-azurevm.md)  

*  Als u gegevens wilt verzamelen van fysieke of virtuele Windows- of Linux-computers in uw omgeving, raadpleegt u de [Quickstart voor Linux-computers](../../azure-monitor/learn/quick-collect-linux-computer.md) of [Quickstart voor Windows-computers](../../azure-monitor/learn/quick-collect-windows-computer.md)

