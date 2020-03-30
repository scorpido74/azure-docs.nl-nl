---
title: Geavanceerde bedreigingsdetectie in Azure | Microsoft Documenten
description: Meer informatie over Azure AD-identiteitsbeveiliging en de mogelijkheden ervan.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 3c1c385a87fc302d180729ec2e4bcd1c4a315f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981470"
---
# <a name="azure-advanced-threat-detection"></a>Geavanceerde bedreigingsdetectie van Azure

Azure biedt ingebouwde geavanceerde functionaliteit voor bedreigingsdetectie via services zoals Azure Active Directory (Azure AD), Azure Monitor-logboeken en Azure Security Center. Deze verzameling beveiligingsservices en -mogelijkheden biedt een eenvoudige en snelle manier om te begrijpen wat er binnen uw Azure-implementaties gebeurt.

Azure biedt een breed scala aan opties om beveiliging te configureren en aan te passen aan de vereisten van uw app-implementaties. In dit artikel wordt besproken hoe aan deze vereisten kan worden voldaan.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) is een [Azure Active Directory Premium P2-editiefunctie](../../active-directory/active-directory-whatis.md) die een overzicht biedt van de risicodetecties en potentiële kwetsbaarheden die van invloed kunnen zijn op de identiteit van uw organisatie. Identity Protection maakt gebruik van bestaande Azure AD-mogelijkheden voor anomaliedetectie die beschikbaar zijn via [Azure AD-afwijkende activiteitsrapporten](../../active-directory/active-directory-reporting-azure-portal.md)en introduceert nieuwe typen risicodetectie die realtime afwijkingen kunnen detecteren.

![Azure AD Identity Protection-diagram](./media/threat-detection/azure-threat-detection-fig1.png)

Identity Protection maakt gebruik van adaptieve machine learning-algoritmen en heuristiek om afwijkingen en risicodetecties te detecteren die erop kunnen wijzen dat een identiteit is aangetast. Met behulp van deze gegevens genereert Identity Protection rapporten en waarschuwingen, zodat u deze risicodetecties onderzoeken en passende herstel- of mitigatiemaatregelen nemen.

Azure Active Directory Identity Protection is meer dan een monitoring- en rapportagetool. Op basis van risicodetecties berekent Identity Protection een gebruikersrisiconiveau voor elke gebruiker, zodat u op risico's gebaseerd beleid configureren om automatisch de identiteit van uw organisatie te beschermen.

Deze op risico's gebaseerde beleidsregels kunnen, naast andere [conditional access-besturingselementen](../../active-directory/active-directory-conditional-access-azure-portal.md) die worden geleverd door Azure Active Directory en [EMS,](../../active-directory/active-directory-conditional-access-azure-portal.md)automatisch adaptieve herstelacties blokkeren of aanbieden, waaronder wachtwoordresets en multi-factor authenticatiehandhaving.

### <a name="identity-protection-capabilities"></a>Mogelijkheden voor identiteitsbescherming

Azure Active Directory Identity Protection is meer dan een monitoring- en rapportagetool. Om de identiteit van uw organisatie te beschermen, u op risico's gebaseerdbeleid configureren dat automatisch reageert op gedetecteerde problemen wanneer een bepaald risiconiveau is bereikt. Deze beleidsregels kunnen, naast andere conditional access-besturingselementen van Azure Active Directory en EMS, automatisch adaptieve herstelacties blokkeren of initiëren, waaronder wachtwoordresets en handhaving van meervoudige verificatie.

Voorbeelden van enkele manieren waarop Azure Identity Protection uw accounts en identiteiten kan beveiligen, zijn:

[Detecteren van risicodetecties en risicovolle accounts](../../active-directory/identity-protection/overview.md)
-   Detecteer zes typen risicodetectie met behulp van machine learning en heuristische regels.
-   Bereken de risiconiveaus van gebruikers.
-   Geef aangepaste aanbevelingen om de algehele beveiligingshouding te verbeteren door kwetsbaarheden te benadrukken.

[Risicodetecties onderzoeken](../../active-directory/identity-protection/overview.md)
-   Stuur meldingen voor risicodetecties.
-   Onderzoek risicodetecties aan de hand van relevante en contextuele informatie.
-   Basiswerkstromen bieden om onderzoeken bij te houden.
-   Geef eenvoudige toegang tot herstelacties, zoals het opnieuw instellen van wachtwoorden.

[Op risico's gebaseerd beleid voor voorwaardelijke toegang](../../active-directory/identity-protection/overview.md)
-   Verminder risicovolle aanmeldingen door aanmeldingen te blokkeren of uitdagingen voor meervoudige verificatie te vereisen.
-   Riskante gebruikersaccounts blokkeren of beveiligen.
-   Gebruikers vereisen dat ze zich registreren voor meervoudige verificatie.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Met [Azure Active Directory Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md)u de toegang binnen uw organisatie beheren, beheren en bewaken. Deze functie omvat toegang tot bronnen in Azure AD en andere Online-services van Microsoft, zoals Office 365 of Microsoft Intune.

![Azure AD Privileged Identity Management-diagram](./media/threat-detection/azure-threat-detection-fig2.png)

PIM helpt u:

-   Ontvang waarschuwingen en rapporten over Azure AD-beheerders en just-in-time (JIT)-beheertoegang tot onlineservices van Microsoft, zoals Office 365 en Intune.

-   Rapporten over beheerderstoegangsgeschiedenis en wijzigingen in beheerderstoewijzingen optezijn.

-   Ontvang meldingen over toegang tot een bevoorrechte rol.

## <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

[Azure Monitor-logboeken](../../azure-monitor/index.yml) is een op Microsoft cloud gebaseerde IT-beheeroplossing waarmee u uw on-premises en cloudinfrastructuur beheren en beschermen. Omdat Azure Monitor-logboeken worden geïmplementeerd als een cloudservice, u deze snel up and running hebben met minimale investeringen in infrastructuurservices. Nieuwe beveiligingsfuncties worden automatisch geleverd, wat voortdurende onderhouds- en upgradekosten bespaart.

Azure Monitor-logboeken kunnen niet alleen waardevolle services bieden, maar ook worden geïntegreerd met System Center-componenten, zoals [System Center Operations Manager,](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/)om uw bestaande investeringen in beveiligingsbeheer uit te breiden naar de cloud. System Center- en Azure Monitor-logboeken kunnen samenwerken om een volledige hybride beheerervaring te bieden.

### <a name="holistic-security-and-compliance-posture"></a>Holistische beveiligings- en nalevingshouding

Het [log Analytics Security and Audit-dashboard](../../security-center/security-center-intro.md) biedt een uitgebreid overzicht van de IT-beveiligingshouding van uw organisatie, met ingebouwde zoekopdrachten voor opmerkelijke problemen die uw aandacht vereisen. Het dashboard Beveiliging en controle is het startscherm voor alles wat met beveiliging te maken heeft in Azure Monitor-logboeken. Het zorgt voor hoogwaardig inzicht in de beveiligingsstatus van uw computers. U ook alle gebeurtenissen van de afgelopen 24 uur, 7 dagen of een ander aangepast tijdsbestek bekijken.

Azure Monitor-logboeken helpen u snel en eenvoudig inzicht te krijgen in de algemene beveiligingshouding van elke omgeving, allemaal binnen de context van IT-bewerkingen, inclusief software-updatebeoordeling, antimalwarebeoordeling en configuratiebasislijnen. Beveiligingslogboekgegevens zijn gemakkelijk toegankelijk om de beveiligings- en compliance-auditprocessen te stroomlijnen.

![Het dashboard Beveiliging en audit van Log Analytics](./media/threat-detection/azure-threat-detection-fig3.jpg)

Het dashboard Beveiliging en audit van Log Analytics is ingedeeld in vier belangrijke categorieën:

-   **Beveiligingsdomeinen:** hiermee u beveiligingsrecords in de loop van de tijd verder verkennen; toegang tot malwarebeoordelingen; updatebeoordelingen; netwerkbeveiliging, identiteit en toegangsinformatie te bekijken; computers met beveiligingsgebeurtenissen weergeven; en krijg snel toegang tot het Azure Security Center-dashboard.

-   **Opmerkelijke problemen:** Hiermee u snel het aantal actieve problemen en de ernst van de problemen identificeren.

-   **Detecties (Preview):** hiermee u aanvalspatronen identificeren door beveiligingswaarschuwingen weer te geven wanneer deze zich voordoen tegen uw resources.

-   **Threat Intelligence:** Hiermee u aanvalspatronen identificeren door het totale aantal servers met uitgaand kwaadaardig IP-verkeer, het type kwaadaardige bedreigingen en een kaart van de IP-locaties weer te geven.

-   **Veelvoorkomende beveiligingsquery's:** geeft de meest voorkomende beveiligingsquery's weer die u gebruiken om uw omgeving te bewaken. Wanneer u een query selecteert, wordt in het deelvenster Zoeken de resultaten voor die query geopend en weergegeven.

### <a name="insight-and-analytics"></a>Inzicht en analyse
In het midden van [Azure Monitor-logboeken](../../log-analytics/log-analytics-queries.md) bevindt zich de opslagplaats, die wordt gehost door Azure.

![Inzicht en analysediagram](./media/threat-detection/azure-threat-detection-fig4.png)

U verzamelt gegevens in de opslagplaats van verbonden bronnen door gegevensbronnen te configureren en oplossingen toe te voegen aan uw abonnement.

![Het azure-monitorlogboekendashboard](./media/threat-detection/azure-threat-detection-fig5.png)

Gegevensbronnen en -oplossingen maken elk afzonderlijke recordtypen met hun eigen set eigenschappen, maar u ze nog steeds samen analyseren in query's naar de opslagplaats. U dezelfde tools en methoden gebruiken om te werken met een verscheidenheid aan gegevens die door verschillende bronnen worden verzameld.


Het grootste deel van uw interactie met Azure Monitor-logboeken verloopt via de Azure-portal, die in elke browser wordt uitgevoerd en u toegang biedt tot configuratie-instellingen en meerdere hulpprogramma's om verzamelde gegevens te analyseren en te reageren. Vanuit de portal u het:
* [Log zoekopdrachten](../../log-analytics/log-analytics-queries.md) waar u query's te bouwen om verzamelde gegevens te analyseren.
* [Dashboards](../../azure-monitor/learn/tutorial-logs-dashboards.md), die u aanpassen met grafische weergaven van uw meest waardevolle zoekopdrachten.
* [oplossingen](../../monitoring/monitoring-solutions.md), die extra functionaliteit en analyse tools bieden.

![Analysetools](./media/threat-detection/azure-threat-detection-fig6.png)

Oplossingen voegen functionaliteit toe aan Azure Monitor-logboeken. Ze draaien voornamelijk in de cloud en bieden analyse van gegevens die zijn verzameld in de log analytics repository. Oplossingen kunnen ook nieuwe recordtypen definiëren die kunnen worden geanalyseerd met logboekzoekopdrachten of met behulp van een extra gebruikersinterface die de oplossing biedt in het dashboard voor logboekanalyse.

Het dashboard Beveiliging en Audit is een voorbeeld van dit soort oplossingen.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatisering en besturing: Waarschuwing op beveiligingsconfiguratiedriften

Azure Automation automatiseert administratieve processen met runbooks die zijn gebaseerd op PowerShell en worden uitgevoerd in de cloud. Runbooks kunnen ook worden uitgevoerd op een server in uw lokale datacentrum om lokale resources te beheren. Azure Automation biedt configuratiebeheer met PowerShell Desired State Configuration (DSC).

![Azure Automation-diagram](./media/threat-detection/azure-threat-detection-fig7.png)

U DSC-resources maken en beheren die in Azure worden gehost en deze toepassen op cloud- en on-premises systemen. Door dit te doen, u hun configuratie definiëren en automatisch afdwingen of rapporten op drift krijgen om ervoor te zorgen dat beveiligingsconfiguraties binnen het beleid blijven.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center helpt uw Azure-bronnen te beschermen. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen. Binnen de service u de richtlijnen definiëren tegen zowel uw Azure-abonnementen als [resourcegroepen](../../azure-resource-manager/management/manage-resources-portal.md) voor een grotere granulariteit.

![Azure Security Center-diagram](./media/threat-detection/azure-threat-detection-fig8.png)

Beveiligingsonderzoekers van Microsoft zijn voortdurend op zoek naar bedreigingen. Ze hebben toegang tot een uitgebreide set telemetrie, afkomstig van de wereldwijde aanwezigheid van Microsoft in de cloud en on-premises. Deze verrijkende en uitgebreide verzameling gegevenssets stelt Microsoft in staat om nieuwe aanvalspatronen en -trends te ontdekken in on-premises producten voor consumenten en ondernemingen, evenals in haar online services.

Zo kan Security Center snel updaten van de detectie-algoritmen als aanvallers release nieuwe en steeds geavanceerdere exploits. Deze aanpak helpt u gelijke tred te houden met een snel bewegende bedreigingsomgeving.

![Bedreigingsdetectie van beveiligingscentrum](./media/threat-detection/azure-threat-detection-fig9.jpg)

Het detecteren van bedreigingen van Security Center werkt volgens het automatisch verzamelen van beveiligingsgegevens van uw Azure-resources, het netwerk en verbonden partneroplossingen. Het analyseert deze informatie, correleren van informatie uit meerdere bronnen, om bedreigingen te identificeren.

Beveiligingswaarschuwingen krijgen in Security Center een prioriteit, evenals aanbevelingen voor het oplossen van de bedreiging.

Security Center maakt gebruik van geavanceerde beveiligingsanalyses die veel verder gaan dan op handtekeningen gebaseerde benaderingen. Doorbraken in big data- en [machine learning-technologieën](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) worden gebruikt om gebeurtenissen in de hele cloudstructuur te evalueren. Geavanceerde analyses kunnen bedreigingen detecteren die onmogelijk te identificeren zijn door middel van handmatige benaderingen en het voorspellen van de evolutie van aanvallen. Deze beveiligingsanalysetypen worden behandeld in de volgende secties.

### <a name="threat-intelligence"></a>Informatie over bedreigingen

Microsoft heeft toegang tot een enorme hoeveelheid wereldwijde threat intelligence.

Telemetrie komt uit meerdere bronnen, zoals Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, de Microsoft Digital Crimes Unit (DCU) en Microsoft Security Response Center (MSRC).

![Bevindingen van threat intelligence](./media/threat-detection/azure-threat-detection-fig10.jpg)

Onderzoekers ontvangen ook informatie over bedreigingsinformatie die wordt gedeeld tussen grote cloudserviceproviders en ze abonneren zich op threat intelligence-feeds van derden. Azure Security Center kan deze informatie gebruiken om u te waarschuwen over bedreigingen van bekende beveiligingsrisico’s. Voorbeelden zijn:

-   **Gebruik makenvan de kracht van machine learning:** Azure Security Center heeft toegang tot een enorme hoeveelheid gegevens over cloudnetwerkactiviteiten, die kunnen worden gebruikt om bedreigingen te detecteren die zijn gericht op uw Azure-implementaties.

-   **Brute force detectie**: Machine learning wordt gebruikt om een historisch patroon van remote access pogingen te creëren, waardoor het brute force-aanvallen kan detecteren tegen Secure Shell (SSH), Remote Desktop Protocol (RDP) en SQL-poorten.

-   **Uitgaande DDoS- en botnetdetectie:** Een gemeenschappelijk doel van aanvallen die zich richten op cloudresources, is het gebruik van de rekenkracht van deze resources om andere aanvallen uit te voeren.

-   **Nieuwe behavioral analytics servers en VM's:** Nadat een server of virtuele machine is gecompromitteerd, aanvallers maken gebruik van een breed scala aan technieken om kwaadaardige code uit te voeren op dat systeem, terwijl het vermijden van detectie, zorgen voor persistentie, en het voorkomen van beveiligingscontroles.

-   **Azure SQL Database Threat Detection:** Threat detection for Azure SQL Database, which identifies anomous database activities that indicate unusual and potentially harmful attempts to access or exploit databases Azure AZURE SQL Database, which identifies anomalous database activities that indicate unusual and potentially harmful attempts to access or exploit databases Azure Azure

### <a name="behavioral-analytics"></a>Gedragsanalyse

Gedragsanalyse is een techniek waarbij gegevens worden geanalyseerd en vergeleken met een verzameling bekende patronen. Deze patronen zijn echter geen eenvoudige handtekeningen. Ze worden vastgesteld aan de hand van complexe machine learning-algoritmen die worden toegepast op grote gegevenssets.

![Bevindingen over gedragsanalyse](./media/threat-detection/azure-threat-detection-fig11.jpg)

De patronen worden ook bepaald door zorgvuldige analyse van kwaadaardig gedrag door deskundige analisten. Azure Security Center kan gedragsanalyses gebruiken om gecompromitteerde bronnen te identificeren op basis van analyse van logboeken van virtuele machines, logboeken van virtuele netwerkapparaten, fabriclogs, crashdumps en andere bronnen.

Bovendien zijn patronen gecorreleerd met andere signalen om te controleren op ondersteunend bewijs van een wijdverspreide campagne. Aan de hand van dit verband kan beter worden vastgesteld welke gebeurtenissen samenhangen met de opgestelde indicatoren van inbreuk.

Voorbeelden zijn:
-   **Uitvoering van verdachte processen**: de aanvallers maken gebruik van verschillende methoden om schadelijke software uit te voeren zonder te worden gedetecteerd. Een aanvaller kan malware bijvoorbeeld dezelfde namen geven als legitieme systeembestanden, maar deze bestanden op een alternatieve locatie plaatsen, een naam gebruiken die vergelijkbaar is met die van een goedaardig bestand of de werkelijke extensie van het bestand maskeren. Security Center-modellen verwerken gedrag en bewaken procesuitvoeringen om uitschieters zoals deze te detecteren.

-   **Verborgen malware en exploitatiepogingen**: Geavanceerde malware kan traditionele antimalwareproducten ontwijken door nooit naar schijf te schrijven of softwarecomponenten te versleutelen die op schijf zijn opgeslagen. Echter, dergelijke malware kan worden gedetecteerd met behulp van geheugenanalyse, omdat de malware sporen in het geheugen moet achterlaten om te functioneren. Wanneer software vastloopt, bevat een crashdump een gedeelte van het geheugen ten tijde van de crash. Door het geheugen in de crashdump te analyseren, kan Azure Security Center technieken detecteren die worden gebruikt om kwetsbaarheden in software te misbruiken, toegang te krijgen tot vertrouwelijke gegevens en heimelijk te blijven bestaan in een gecompromitteerde machine zonder de prestaties van uw Machine.

-   **Laterale beweging en interne verkenning**: Om in een gecompromitteerd netwerk voort te blijven en waardevolle gegevens te lokaliseren en te oogsten, proberen aanvallers vaak lateraal van de gecompromitteerde machine naar anderen binnen hetzelfde netwerk te gaan. Security Center controleert proces- en inlogactiviteiten om pogingen te ontdekken om de positie van een aanvaller binnen het netwerk uit te breiden, zoals uitvoering van opdrachten op afstand, netwerkonderzoek en accountopsomming.

-   **Kwaadaardige PowerShell scripts:** PowerShell kan worden gebruikt door aanvallers om kwaadaardige code uit te voeren op doel virtuele machines voor verschillende doeleinden. Security Center inspecteert PowerShell-activiteit op tekenen van verdachte activiteiten.

-   **Uitgaande aanvallen**: aanvallers richten zich vaak op cloudresources met het doel deze resources te gebruiken voor het uitvoeren van nieuwe aanvallen. Gecompromitteerde virtuele machines, bijvoorbeeld, kunnen worden gebruikt om brute force-aanvallen op andere virtuele machines te lanceren, spam te verzenden of open poorten en andere apparaten op het internet te scannen. Door machine learning toe te passen op netwerkverkeer kan Security Center het detecteren wanneer uitgaande netwerkcommunicatie groter is dan de norm. Wanneer spam wordt gedetecteerd, correleert Security Center ook ongebruikelijk e-mailverkeer met informatie van Office 365 om te bepalen of de e-mail waarschijnlijk snode is of het resultaat van een legitieme e-mailcampagne.

### <a name="anomaly-detection"></a>Afwijkingsdetectie

Azure Security Center maakt ook gebruik van afwijkingsdetectie om bedreigingen te identificeren. In tegenstelling tot gedragsanalyses (die afhankelijk zijn van bekende patronen die zijn afgeleid van grote gegevenssets) is afwijkingsdetectie meer "gepersonaliseerd" en richt het zich op basislijnen die specifiek voor uw implementaties zijn. Machine learning wordt toegepast om de normale activiteit voor uw implementaties te bepalen en vervolgens worden regels gegenereerd om uitschietervoorwaarden te definiëren die een beveiligingsgebeurtenis kunnen vertegenwoordigen. Hier volgt een voorbeeld:

-   **Inkomende RDP / SSH brute force-aanvallen**: Uw implementaties kunnen drukke virtuele machines hebben met veel aanmeldingen per dag en andere virtuele machines die weinig of geen aanmeldingen hebben. Azure Security Center kan de inlogactiviteit van de basislijn voor deze virtuele machines bepalen en machine learning gebruiken om rond de normale inlogactiviteiten te definiëren. Als er een discrepantie is met de basislijn die is gedefinieerd voor aanmeldingsgerelateerde kenmerken, kan een waarschuwing worden gegenereerd. Ook hier weer wordt door machine learning bepaald wat een aanzienlijk verschil is.

### <a name="continuous-threat-intelligence-monitoring"></a>Doorlopende controle van informatie over bedreigingen

Azure Security Center werkt met beveiligingsonderzoeks- en datascience-teams over de hele wereld die continu controleren op veranderingen in het bedreigingslandschap. Dit omvat de volgende initiatieven:

-   **Monitoring van bedreigingsinformatie**: Threat intelligence omvat mechanismen, indicatoren, implicaties en bruikbare adviezen over bestaande of opkomende bedreigingen. Deze informatie wordt gedeeld in de beveiligingscommunity en Microsoft controleert voortdurend bedreigingsinformatiefeeds van interne en externe bronnen.

-   **Signaaldelen:** Inzichten van beveiligingsteams in het brede Microsoft-portfolio van cloud- en on-premises services, servers en clienteindpuntapparaten worden gedeeld en geanalyseerd.

-   **Microsoft-beveiligingsspecialisten**: voortdurende betrokkenheid bij teams binnen Microsoft die werken op gespecialiseerde beveiligingsgebieden, zoals forensisch onderzoek en detectie van webaanvallen.

-   **Detectietuning**: Algoritmen worden uitgevoerd tegen echte klantgegevenssets en beveiligingsonderzoekers werken samen met klanten om de resultaten te valideren. Echte en fout-positieven worden gebruikt voor het verfijnen van machine learning-algoritmen.

Deze gecombineerde inspanningen culmineren in nieuwe en verbeterde detecties, waar u direct van profiteren. Er is geen actie voor jou om te ondernemen.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Geavanceerde functies voor bedreigingsdetectie: andere Azure-services

### <a name="virtual-machines-microsoft-antimalware"></a>Virtuele machines: Microsoft antimalware

[Microsoft-antimalware](antimalware.md) voor Azure is een oplossing voor toepassingen en tenantomgevingen, ontworpen om op de achtergrond te worden uitgevoerd zonder menselijke tussenkomst. U beveiliging implementeren op basis van de behoeften van uw toepassingsworkloads, met een basisveilige of geavanceerde aangepaste configuratie, inclusief antimalwarebewaking. Azure-antimalware is een beveiligingsoptie voor virtuele Azure-machines die automatisch wordt geïnstalleerd op alle virtuele Azure PaaS-machines.

#### <a name="microsoft-antimalware-core-features"></a>Microsoft antimalware kernfuncties

Dit zijn de functies van Azure die Microsoft-antimalware voor uw toepassingen implementeren en inschakelen:

-   **Realtime beveiliging:** controleert activiteiten in cloudservices en op virtuele machines om de uitvoering van malware te detecteren en te blokkeren.

-   **Gepland scannen**: Voert periodiek gericht scannen uit om malware te detecteren, inclusief actief uitvoeren van programma's.

-   **Malware herstel:** Automatisch werkt op gedetecteerde malware, zoals het verwijderen of quarantining kwaadaardige bestanden en het opruimen van kwaadaardige register vermeldingen.

-   **Handtekeningupdates:** installeert automatisch de nieuwste beveiligingshandtekeningen (virusdefinities) om ervoor te zorgen dat de beveiliging up-to-date is op een vooraf bepaalde frequentie.

-   **Antimalware Engine updates:** Werkt automatisch de Microsoft Antimalware Engine bij.

-   **Antimalware platform updates:** Werkt automatisch het Microsoft antimalware platform bij.

-   **Actieve beveiliging:** rapporteert telemetriemetagegevens over gedetecteerde bedreigingen en verdachte bronnen aan Microsoft Azure om een snelle reactie op het evoluerende dreigingslandschap te garanderen, waardoor real-time synchrone handtekening kan worden geleverd via het actieve beveiligingssysteem van Microsoft.

-   **Rapportage van voorbeelden:** levert en rapporteert voorbeelden aan de Microsoft-antimalwareservice om de service te verfijnen en probleemoplossing mogelijk te maken.

-   **Uitsluitingen:** Hiermee kunnen toepassings- en servicebeheerders bepaalde bestanden, processen en stations configureren voor uitsluiting van bescherming en scannen om prestaties en andere redenen.

-   **Antimalware-gebeurtenisverzameling**: registreert de status van de antimalwareservice, verdachte activiteiten en herstelacties die zijn uitgevoerd in het gebeurtenislogboek van het besturingssysteem en verzamelt deze in het Azure-opslagaccount van de klant.

### <a name="azure-sql-database-threat-detection"></a>Detectie van Azure SQL-database-dreiging

[Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) is een nieuwe beveiligingsfunctie die is ingebouwd in de Azure SQL Database-service. Azure SQL Database Threat Detection werkt de klok rond om afwijkende databaseactiviteiten te leren, te profileren en te detecteren en identificeert potentiële bedreigingen voor de database.

Beveiligingsbeambten of andere aangewezen beheerders kunnen onmiddellijk een melding krijgen over verdachte databaseactiviteiten wanneer deze zich voordoen. Elke melding bevat details over de verdachte activiteit en beveelt aan hoe u de dreiging verder onderzoeken en beperken.

Op dit moment detecteert Azure SQL Database Threat Detection potentiële kwetsbaarheden en SQL-injectieaanvallen en afwijkende databasetoegangspatronen.

Na ontvangst van een e-mailmelding voor bedreigingsdetectie kunnen gebruikers navigeren en de relevante controlerecords bekijken via een diepe koppeling in de e-mail. De koppeling opent een auditviewer of een vooraf geconfigureerde controle-Excel-sjabloon die de relevante controlerecords rond de tijd van de verdachte gebeurtenis weergeeft, volgens het volgende:

-   Controleer de opslag voor de database/server met de afwijkende databaseactiviteiten.

-   Relevante controleopslagtabel die op het moment van de gebeurtenis werd gebruikt om het controlelogboek te schrijven.

-   Controlerecords van het uur direct na het voorval.

-   Controlerecords met een vergelijkbare gebeurtenis-id op het moment van de gebeurtenis (optioneel voor sommige detectoren).

SQL Database-bedreigingsdetectoren maken gebruik van een van de volgende detectiemethoden:

-   **Deterministische detectie:** detecteert verdachte patronen (regels gebaseerd) in de SQL-clientquery's die overeenkomen met bekende aanvallen. Deze methodologie heeft een hoge detectie en lage vals-positieve, maar beperkte dekking omdat het valt binnen de categorie van "atoomdetecties."

-   **Gedragsdetectie**: Detecteert afwijkende activiteit, dat is abnormaal gedrag in de database dat niet werd gezien tijdens de meest recente 30 dagen. Voorbeelden van SQL-clientafwijkende activiteit kunnen een piek zijn van mislukte aanmeldingen of query's, een hoog volume van gegevens die worden geëxtraheerd, ongebruikelijke canonieke query's of onbekende IP-adressen die worden gebruikt om toegang te krijgen tot de database.

### <a name="application-gateway-web-application-firewall"></a>Application Gateway Web Application Firewall

[Web Application Firewall (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) is een functie van [Azure Application Gateway](../../application-gateway/application-gateway-web-application-firewall-overview.md) die bescherming biedt aan webtoepassingen die een toepassingsgateway gebruiken voor [standaardbeheerfuncties voor toepassingsbeheer.](https://kemptechnologies.com/in/application-delivery-controllers) Web Application Firewall doet dit door ze te beschermen tegen de meeste van de [Open Web Application Security Project (OWASP) top 10 gemeenschappelijke web kwetsbaarheden](https://www.owasp.org/index.php/Top_10_2010-Main).

![Application Gateway Web Application Firewall-diagram](./media/threat-detection/azure-threat-detection-fig13.png)

Bescherming omvat:

-   SQL-injectiebeveiliging.

-   Beveiliging van scripts over de site.

-   Common Web Attacks Protection, zoals command injection, HTTP request smuggling, HTTP response splitting en remote file inclusion attack.

-   Bescherming tegen http-protocolschendingen.

-   Bescherming tegen http-protocolafwijkingen, zoals ontbrekende hostuser-agent en accepteren headers.

-   Preventie tegen bots, crawlers en scanners.

-   Detectie van veelvoorkomende toepassingsverkeerde configuraties (dat wil zeggen Apache, IIS, enzovoort).

Het configureren van WAF op uw toepassingsgateway biedt de volgende voordelen:

-   Beschermt uw webapplicatie tegen kwetsbaarheden en aanvallen op het web zonder wijziging van de back-endcode.

-   Beschermt meerdere webapplicaties tegelijk achter een toepassingsgateway. Een applicatiegateway ondersteunt het hosten van maximaal 20 websites.

-   Controleert webapplicaties tegen aanvallen met behulp van realtime rapporten die worden gegenereerd door WAF-logboeken van de toepassingsgateway.

-   Helpt te voldoen aan de nalevingsvereisten. Voor bepaalde nalevingscontroles moeten alle op internet gerichte eindpunten worden beschermd door een WAF-oplossing.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>API voor anomaliedetectie: gebouwd met Azure Machine Learning

De Api voor anomaliedetectie is een API die handig is voor het detecteren van verschillende afwijkende patronen in uw tijdreeksgegevens. De API kent een anomaliescore toe aan elk gegevenspunt in de tijdreeks, dat kan worden gebruikt voor het genereren van waarschuwingen, het bewaken via dashboards of het verbinden met uw ticketingsystemen.

De [API voor anomaliedetectie](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) kan de volgende typen afwijkingen op tijdreeksgegevens detecteren:

-   **Pieken en dips:** Wanneer u het aantal inlogfouten bij een service of het aantal kassa's op een e-commercesite controleert, kunnen ongebruikelijke pieken of dips duiden op beveiligingsaanvallen of serviceonderbrekingen.

-   **Positieve en negatieve trends:** Wanneer u het geheugengebruik in computers controleert, duidt het verkleinen van de vrije geheugengrootte op een mogelijk geheugenlek. Voor bewaking van de wachtrijvoor servicewachtrijkan een aanhoudende opwaartse trend duiden op een onderliggend softwareprobleem.

-   **Niveauwijzigingen en wijzigingen in dynamisch waardenbereik:** Niveauwijzigingen in latencies van een service na een service-upgrade of lagere niveaus van uitzonderingen na een upgrade kunnen interessant zijn om te controleren.

De op machine learning gebaseerde API maakt:

-   **Flexibele en robuuste detectie:** met de anomaliedetectiemodellen kunnen gebruikers gevoeligheidsinstellingen configureren en afwijkingen tussen seizoens- en niet-seizoensgebonden gegevenssets detecteren. Gebruikers kunnen het anomaliedetectiemodel aanpassen om de detectie-API minder of gevoeliger te maken op basis van hun behoeften. Dit zou betekenen dat de minder of meer zichtbare afwijkingen in gegevens met en zonder seizoenspatronen moeten worden opgespoord.

-   **Schaalbare en tijdige detectie**: De traditionele manier van monitoren met huidige drempels die door de domeinkennis van experts zijn ingesteld, zijn kostbaar en niet schaalbaar voor miljoenen dynamisch veranderende gegevenssets. De anomaliedetectiemodellen in deze API worden geleerd en modellen worden automatisch afgestemd op zowel historische als realtime gegevens.

-   **Proactieve en bruikbare detectie:** Langzame trend- en niveauveranderingsdetectie kan worden toegepast voor vroegtijdige anomaliedetectie. De vroege abnormale signalen die worden gedetecteerd kan worden gebruikt om de mens direct te onderzoeken en te handelen op de probleemgebieden. Bovendien kunnen root cause analyse modellen en waarschuwingstools worden ontwikkeld bovenop deze anomalie-detectie API-service.

De API voor anomaliedetectie is een effectieve en efficiënte oplossing voor een breed scala aan scenario's, zoals servicestatus en KPI-monitoring, IoT, prestatiebewaking en netwerkverkeersbewaking. Hier volgen enkele populaire scenario's waarin deze API nuttig kan zijn:

- IT-afdelingen hebben tools nodig om gebeurtenissen, foutcode, gebruikslogboek en prestaties (CPU, geheugen, enzovoort) tijdig bij te houden.

-   Online commercesites willen klantactiviteiten, paginaweergaven, klikken, enzovoort bijhouden.

-   Nutsbedrijven willen het verbruik van water, gas, elektriciteit en andere hulpbronnen bijhouden.

-   Facilitaire of gebouwbeheerdiensten willen temperatuur, vocht, verkeer, enzovoort, in de gaten houden.

-   IoT/fabrikanten willen sensorgegevens in tijdreeksen gebruiken om de werkstroom, kwaliteit, enzovoort, te monitoren.

-   Serviceproviders, zoals callcenters, moeten de trend van de servicevraag, het incidentvolume, de lengte van de wachtrij, enzovoort, controleren.

-   Business analytics-groepen willen de abnormale beweging van bedrijfsKPI's (zoals verkoopvolume, klantsentimentof prijs) in realtime monitoren.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) is een essentieel onderdeel van de Microsoft Cloud Security stack. Het is een uitgebreide oplossing die uw organisatie kan helpen om optimaal te profiteren van de belofte van cloudtoepassingen. Het houdt u in controle, door middel van verbeterde zichtbaarheid in de activiteit. Het verhoogt ook de beveiliging van essentiële gegevens in cloudtoepassingen.

Met hulpprogramma's om schaduw-IT te onthullen, risico's te beoordelen, beleid af te dwingen, activiteiten te onderzoeken en bedreigingen te stoppen, kan uw organisatie veiliger overstappen naar de cloud terwijl de controle over essentiële gegevens wordt behouden.

| | |
|---|---|
| Ontdekken | Onthul Shadow IT met Cloud App Security. Krijg meer inzicht door apps, activiteiten, gebruikers, gegevens en bestanden in uw cloudomgeving te detecteren. Detecteer apps van derden die zijn verbonden met uw cloud.|
|Onderzoeken | Onderzoek uw cloud-apps met forensische hulpprogramma's voor de cloud om riskante apps, specifieke gebruikers en bestanden in uw netwerk diepgaand te onderzoeken. Vind patronen in de gegevens die vanuit de cloud worden verzameld. U kunt rapporten genereren om uw cloud te bewaken. |
| Beheer | Beperk risico's door beleidsregels en waarschuwingen in te stellen om maximale controle over het netwerkverkeer in de cloud te behalen. Gebruik Cloud App Security om uw gebruikers te migreren naar veilige, goedgekeurde alternatieven voor de cloud-app. |
| Beschermen | Gebruik Cloud App Security om toepassingen te sanctioneren of te verbieden, preventie van gegevensverlies af te dwingen, machtigingen te beheren en te delen, en aangepaste rapporten en waarschuwingen te genereren. |
| Beheer | Beperk risico's door beleidsregels en waarschuwingen in te stellen om maximale controle over het netwerkverkeer in de cloud te behalen. Gebruik Cloud App Security om uw gebruikers te migreren naar veilige, goedgekeurde alternatieven voor de cloud-app. |
| | |


![Cloud App-beveiligingsdiagram](./media/threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integreert zichtbaarheid met uw cloud door:

-   Cloud Discovery gebruiken om uw cloudomgeving en de cloud-apps die uw organisatie gebruikt in kaart te brengen en te identificeren.

-   Apps in uw cloud bestraffen en verbieden.

-   Gebruik eenvoudig te implementeren app-connectors die gebruikmaken van api's van provider, voor zichtbaarheid en beheer van apps waarmee u verbinding maakt.

-   U helpen om continu te controleren door beleid in te stellen en vervolgens voortdurend te verfijnen.

Bij het verzamelen van gegevens uit deze bronnen voert Cloud App Security geavanceerde analyses uit. U wordt direct op de hoogte gesteld van afwijkende activiteiten en u krijgt meer inzicht in uw cloudomgeving. U kunt een beleid configureren in Cloud App Security en dit vervolgens gebruiken om alles in uw cloudomgeving te beveiligen.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Geavanceerde geavanceerde bedreigingsdetectiemogelijkheden van derden via de Azure Marketplace

### <a name="web-application-firewall"></a>Web Application Firewall

Web Application Firewall inspecteert binnenkomend webverkeer en blokkeert SQL-injecties, cross-site scripting, malware-uploads, ddos-aanvallen en andere aanvallen die zijn gericht op uw webtoepassingen. Het inspecteert ook de reacties van de back-end webservers voor preventie van gegevensverlies (DLP). De geïntegreerde toegangscontrole-engine stelt beheerders in staat om gedetailleerd toegangscontrolebeleid voor verificatie, autorisatie en boekhouding (AAA) te maken, waardoor organisaties een sterke verificatie en gebruikerscontrole hebben.

Web Application Firewall biedt de volgende voordelen:

-   Detecteert en blokkeert SQL-injecties, Cross-Site Scripting, malware-uploads, ddos van toepassingen of andere aanvallen op uw toepassing.

-   Verificatie en toegangscontrole.

-   Scant uitgaand verkeer om gevoelige gegevens te detecteren en kan de informatie maskeren of blokkeren om uit te lekken.

-   Versnelt de levering van inhoud van webtoepassingen met behulp van mogelijkheden zoals caching, compressie en andere verkeersoptimalisaties.

Zie [Barracuda WAF, Brocade virtual web application firewall (vWAF), Imperva SecureSphere en de ThreatSTOP IP firewall](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf)voor voorbeelden van firewalls voor webtoepassingen die beschikbaar zijn in de Azure Marketplace.

## <a name="next-steps"></a>Volgende stappen

- [Reageren op de bedreigingen van vandaag:](../../security-center/security-center-alerts-overview.md#respond-threats)helpt bij het identificeren van actieve bedreigingen die zich richten op uw Azure-bronnen en biedt de inzichten die u nodig hebt om snel te reageren.

- [Azure SQL Database Threat Detection:](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)helpt bij het aanpakken van uw zorgen over potentiële bedreigingen voor uw databases.
