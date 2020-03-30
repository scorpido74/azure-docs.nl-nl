---
title: Vier stappen naar een sterke identiteitsbasis - Azure AD
description: Dit onderwerp beschrijft vier stappen hybride identiteit klanten kunnen nemen om een sterke identiteit stichting op te bouwen.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74206782"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Vier stappen naar een sterke identiteitsbasis met Azure Active Directory

Het beheren van toegang tot apps en gegevens kan niet langer afhankelijk zijn van de traditionele grensstrategieën voor netwerkbeveiliging, zoals perimeternetwerken en firewalls, vanwege de snelle verplaatsing van apps naar de cloud. Nu moeten organisaties vertrouwen op hun identiteitsoplossing om te bepalen wie en wat toegang heeft tot de apps en gegevens van de organisatie. Meer organisaties staan werknemers toe om hun eigen apparaten mee te nemen naar het werk en hun apparaten te gebruiken vanaf elke plek waar ze verbinding kunnen maken met internet. Ervoor zorgen dat deze apparaten compatibel en veilig zijn, is een belangrijke overweging geworden in de identiteitsoplossing die een organisatie kiest om te implementeren. In de huidige digitale werkplek [is identiteit het primaire controlevlak](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) van elke organisatie die naar de cloud verhuist.

Bij de toepassing van een azure active directory -hybride identiteitsoplossing (Azure Ad) krijgen organisaties toegang tot premiumfuncties die productiviteit ontsluiten door automatisering, delegeren, selfservice en single sign-on-mogelijkheden. Het stelt uw werknemers in staat om toegang te krijgen tot bedrijfsbronnen, waar ze ook hun werk moeten doen, terwijl uw IT-team die toegang kan regelen door ervoor te zorgen dat de juiste mensen de juiste toegang hebben tot de juiste middelen om een veilige productiviteit te garanderen.

Op basis van onze learnings helpt deze checklist met best practices u om snel aanbevolen acties te implementeren om een *sterke* identiteitsbasis in uw organisatie op te bouwen:

* Maak eenvoudig verbinding met apps
* Stel automatisch één identiteit vast voor elke gebruiker
* Geef uw gebruikers een veilige staat van kracht
* Operationaliseer uw inzichten

## <a name="step-1---connect-to-apps-easily"></a>Stap 1 - Eenvoudig verbinding maken met apps

Door uw apps te verbinden met Azure AD, u de productiviteit en beveiliging van eindgebruikers verbeteren door eenmalige aanmelding (SSO) in te schakelen en gebruikersvoorzieningen uit te brengen. Door uw apps op één plaats, Azure AD, te beheren, u de administratieve overhead minimaliseren en één controlepunt voor uw beveiligings- en nalevingsbeleid bereiken.

In dit gedeelte worden uw opties voor het beheren van gebruikerstoegang tot apps, het inschakelen van veilige externe toegang tot interne apps en de voordelen van het migreren van uw apps naar Azure AD, weergegeven.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Apps naadloos beschikbaar maken voor uw gebruikers

Azure AD stelt beheerders in staat [om toepassingen toe](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) te voegen aan de galerie met Enterprise-toepassingen in de [Azure-portal.](https://portal.azure.com/) Als u toepassingen toevoegt aan de galerie met Enterprise-toepassingen, u toepassingen eenvoudiger configureren om Azure AD als uw identiteitsprovider te gebruiken. Hiermee u ook de toegang van gebruikers tot de toepassing beheren met beleid voor voorwaardelijke toegang en eenmalige aanmelding (SSO) configureren voor toepassingen, zodat gebruikers hun wachtwoorden niet herhaaldelijk hoeven in te voeren en automatisch worden aangemeld bij zowel on-premises als cloudgebaseerde toepassingen.

Zodra toepassingen zijn toegevoegd aan de Azure AD-galerie, kunnen gebruikers apps zien die aan hen zijn toegewezen en naar behoefte andere apps zoeken en aanvragen. Azure AD biedt [verschillende methoden](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) voor gebruikers om toegang te krijgen tot hun apps:

* Access-paneel/Mijn apps
* Startprogramma voor de Office 365-app
* Directe aanmelding bij federatieve apps
* Directe aanmeldingskoppelingen

Zie Stap 3 voor meer informatie over gebruikerstoegang tot apps: Uw gebruikers in dit artikel **versterken.**

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Apps migreren van Active Directory Federation Services naar Azure AD

Als u een enkele aanmeldingsconfiguratie migreert van Active Directory Federation Services (ADFS) naar Azure AD, u extra mogelijkheden op beveiliging, een consistentere beheerbaarheid en samenwerking mogelijk maken. Voor optimale resultaten raden we u aan uw apps te migreren van AD FS naar Azure AD. Het meenemen van uw toepassingsverificatie en -autorisatie naar Azure AD biedt u de volgende voordelen:

* Kosten beheren
* Risico's beheren
* Verhoging van de productiviteit
* Naleving en governance aanpakken

Zie De whitepaper [Uw toepassingen migreren naar Azure Active Directory](https://aka.ms/migrateapps/whitepaper) voor meer informatie.

### <a name="enable-secure-remote-access-to-apps"></a>Beveiligde externe toegang tot apps inschakelen

[Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) biedt een eenvoudige oplossing voor organisaties om on-premises apps naar de cloud te publiceren voor externe gebruikers die op een veilige manier toegang tot interne apps nodig hebben. Na één aanmelding bij Azure AD hebben gebruikers toegang tot zowel cloud- als on-premises toepassingen via externe URL's of een interne toepassingsportal.

Azure AD-toepassingsproxy biedt de volgende voordelen:

* Azure AD uitbreiden naar on-premises resources
  * Beveiliging en beveiliging op cloudschaal
  * Functies zoals voorwaardelijke toegang en multi-factor authenticatie die eenvoudig in te schakelen zijn
* Geen componenten in het perimeternetwerk zoals VPN en traditionele reverse proxy-oplossingen
* Geen inkomende verbindingen vereist
* SSO (Single sign-on) voor apparaten, resources en apps in de cloud en on-premises
* Stelt eindgebruikers in staat om altijd en overal productief te zijn

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Ontdek Shadow IT met Microsoft Cloud App Security

In moderne ondernemingen zijn IT-afdelingen vaak niet op de hoogte van alle cloudtoepassingen die door de gebruikers worden gebruikt om hun werk te doen. Wanneer IT-beheerders wordt gevraagd hoeveel cloud-apps ze denken dat hun werknemers gebruiken, zeggen ze gemiddeld 30 of 40. In werkelijkheid is het gemiddelde meer dan 1.000 afzonderlijke apps die worden gebruikt door werknemers in uw organisatie. 80% van de werknemers gebruikt niet-gesanctioneerde apps die niemand heeft beoordeeld en mogelijk niet voldoet aan uw beveiligings- en nalevingsbeleid.

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) kan u helpen bij het identificeren van nuttige apps die populair zijn bij gebruikers die IT kan sanctioneren en toevoegen aan de galerij met Enterprise-toepassingen, zodat gebruikers kunnen profiteren van mogelijkheden zoals SSO en Voorwaardelijke toegang.

<em>"**Cloud App Security** helpt ons ervoor te zorgen dat onze mensen onze cloud- en SaaS-applicaties goed gebruiken, op een manier die het fundamentele beveiligingsbeleid ondersteunt dat Accenture helpt beschermen."</em> --- [John Blasi, Managing Director, Information Security, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Naast het detecteren van schaduw-IT kan MCAS ook het risiconiveau van apps bepalen, ongeautoriseerde toegang tot bedrijfsgegevens, mogelijk gegevenslekken en andere beveiligingsrisico's die inherent zijn aan de toepassingen voorkomen.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Stap 2 - Stel automatisch één identiteit vast voor elke gebruiker

Als u on-premises en cloudmappen samenbrengt in een hybride azure-identiteitsoplossing voor AD, u uw bestaande on-premises Active Directory-investering opnieuw gebruiken door uw bestaande identiteiten in de cloud in te richten. De oplossing synchroniseert on-premises identiteiten met Azure AD, terwijl IT de on-premises Active Directory blijft draaien met bestaande governance-oplossingen als de primaire bron van waarheid voor identiteiten. De hybride identiteitsoplossing van Microsoft azure AD omvat on-premises en cloudgebaseerde mogelijkheden, waardoor een algemene gebruikersidentiteit wordt voorverificatie en autorisatie voor alle resources, ongeacht hun locatie.

De integratie van uw on-premises mappen met Azure AD maakt uw gebruikers productiever en voorkomt dat gebruikers meerdere accounts in apps en services kunnen gebruiken door een gemeenschappelijke identiteit te bieden voor toegang tot zowel cloud- als on-premises bronnen. Het gebruik van meerdere accounts is een pijnpunt voor eindgebruikers en IT. Vanuit het oogpunt van de eindgebruiker betekent het hebben van meerdere accounts dat u meerdere wachtwoorden moet onthouden. Om dit te voorkomen, veel gebruikers hergebruiken hetzelfde wachtwoord voor elk account, dat is slecht vanuit een beveiligingsperspectief. Vanuit IT-perspectief leidt hergebruik vaak tot meer wachtwoordresets en helpdeskkosten, samen met de klachten van de eindgebruiker.

Azure AD Connect is het hulpprogramma dat wordt gebruikt om uw on-premises identiteiten te synchroniseren met Azure AD, dat vervolgens kan worden gebruikt om toegang te krijgen tot cloudtoepassingen. Zodra de identiteiten zich in Azure AD bevinden, kunnen ze worden ingericht voor SaaS-toepassingen zoals Salesforce of Concur.

In deze sectie zetten we aanbevelingen op een rij voor het bieden van hoge beschikbaarheid, moderne authenticatie voor de cloud en het verminderen van uw on-premises voetafdruk.

> [!NOTE]
> Zie Azure AD Connect Sync als u meer wilt weten over Azure AD [Connect?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Een faseringsserver instellen voor Azure AD Connect en deze up-to-date houden

Azure AD Connect speelt een belangrijke rol in het inrichtingsproces. Als de synchronisatieserver om welke reden dan ook offline gaat, worden wijzigingen in on-premises niet bijgewerkt in de cloud en veroorzaken ze toegangsproblemen voor gebruikers. Het is belangrijk om een failoverstrategie te definiëren waarmee beheerders de synchronisatie snel kunnen hervatten nadat de synchronisatieserver offline is gegaan.

Als u een hoge beschikbaarheid wilt bieden in het geval dat uw primaire Azure AD Connect-server offline gaat, wordt aanbevolen een afzonderlijke [faseringsserver](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) voor Azure AD Connect te implementeren. Door een server te implementeren, kan de beheerder de staging-server "promoten" tot productie via een eenvoudige configuratieswitch. Als u een stand-byserver hebt geconfigureerd in de faseringsmodus, u ook nieuwe configuratiewijzigingen testen en implementeren en een nieuwe server introduceren als de oude wordt buiten gebruik gesteld.

> [!TIP]
> Azure AD Connect wordt regelmatig bijgewerkt. Daarom wordt het ten zeerste aanbevolen om de staging-server actueel te houden om te profiteren van de prestatieverbeteringen, bugfixes en nieuwe mogelijkheden die elke nieuwe versie biedt.

### <a name="enable-cloud-authentication"></a>Cloudverificatie inschakelen

Organisaties met on-premises Active Directory moeten hun directory uitbreiden naar Azure AD met Azure AD Connect en de juiste verificatiemethode configureren. [Het kiezen van de juiste verificatiemethode](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) voor uw organisatie is de eerste stap in uw reis om apps naar de cloud te verplaatsen. Het is een essentieel onderdeel omdat het de toegang tot alle cloudgegevens en -bronnen regelt.

De eenvoudigste en aanbevolen methode voor het inschakelen van cloudverificatie voor on-premises directoryobjecten in Azure AD is het inschakelen [van Password Hash Synchronization](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS). Als alternatief kunnen sommige organisaties overwegen [om Pass-through Authentication](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) in te schakelen.

Of u nu phs of PTA kiest, vergeet niet om [Seamless Single Sign-on](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) in te schakelen om gebruikers toegang te geven tot cloud-apps zonder voortdurend hun gebruikersnaam en wachtwoord in de app in te voeren wanneer ze Windows 7- en 8-apparaten in uw bedrijfsnetwerk gebruiken. Zonder eenmalige aanmelding moeten gebruikers toepassingsspecifieke wachtwoorden onthouden en zich aanmelden bij elke toepassing. Op dezelfde manier moeten IT-medewerkers gebruikersaccounts maken en bijwerken voor elke toepassing, zoals Office 365, Box en Salesforce. Gebruikers moeten hun wachtwoorden onthouden en de tijd besteden om zich bij elke toepassing aan te melden. Het bieden van een gestandaardiseerd single sign-on mechanisme voor de hele onderneming is cruciaal voor de beste gebruikerservaring, vermindering van risico's, vermogen om te rapporteren en governance.

Voor organisaties die ad FS of een andere on-premises verificatieprovider al gebruiken, kan de overstap naar Azure AD als uw identiteitsprovider de complexiteit verminderen en de beschikbaarheid verbeteren. Tenzij u specifieke use cases hebt voor het gebruik van federatie, raden we u aan te migreren van federatieve verificatie naar PHS en Seamless SSO of PTA en Seamless SSO om te genieten van de voordelen van een verminderde on-premises footprint en de flexibiliteit die de cloud biedt met verbeterde gebruikerservaringen. Zie [Migreren van federatie naar wachtwoordhashsynchronisatie voor Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)meer informatie.

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Automatische deprovisioning van accounts inschakelen

Het inschakelen van geautomatiseerde inrichting en deprovisioning voor uw toepassingen is de beste strategie voor het regelen van de levenscyclus van identiteiten in meerdere systemen. Azure AD ondersteunt [geautomatiseerde, op beleid gebaseerde inrichting en deprovisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) van gebruikersaccounts voor een verscheidenheid aan populaire SaaS-toepassingen, zoals ServiceNow en Salesforce, en andere die het [SCIM 2.0-protocol](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)implementeren. In tegenstelling tot traditionele inrichtingsoplossingen, waarvoor aangepaste code of handmatige uploaden van CSV-bestanden vereist is, wordt de inrichtingsservice gehost in de cloud en beschikt het over vooraf geïntegreerde connectors die kunnen worden ingesteld en beheerd met behulp van de Azure-portal. Een belangrijk voordeel van automatische deprovisioning is dat het helpt uw organisatie te beveiligen door de identiteit van gebruikers direct te verwijderen uit belangrijke SaaS-apps wanneer ze de organisatie verlaten.

Zie [Gebruikersinrichting en deprovisioning voor gebruikersvoorziening automatiseren voor SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)voor meer informatie over het inrichten van automatische gebruikersaccounts en hoe deze werkt.

## <a name="step-3---empower-your-users-securely"></a>Stap 3 - Uw gebruikers veilig mondiger maken

In de huidige digitale werkplek is het belangrijk om beveiliging in evenwicht te brengen met productiviteit. Eindgebruikers dringen echter vaak terug op beveiligingsmaatregelen die hun productiviteit en toegang tot cloud-apps vertragen. Om dit op te pakken, biedt Azure AD selfservicemogelijkheden waarmee gebruikers productief kunnen blijven en tegelijkertijd de administratieve overhead minimaliseren.

In deze sectie worden aanbevelingen weergegeven voor het verwijderen van frictie uit uw organisatie door uw gebruikers mondiger te maken terwijl ze waakzaam blijven.

### <a name="enable-self-service-password-reset-for-all-users"></a>Selfservice wachtwoordreset inschakelen voor alle gebruikers

Azure's [self-service password reset](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) biedt een eenvoudige manier voor IT-beheerders om gebruikers in staat te stellen hun wachtwoorden of accounts te resetten en te ontgrendelen zonder tussenkomst van de beheerder. Het systeem biedt gedetailleerde rapporten zodat u kunt volgen wanneer gebruikers het systeem openen. U ontvangt ook meldingen om u te waarschuwen over misbruik.

Azure AD ontgrendelt standaard accounts wanneer het een wachtwoordreset uitvoert. Wanneer u echter on-premises Azure AD [Connect-integratie inschakelt,](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)hebt u ook de mogelijkheid om deze twee bewerkingen te scheiden, zodat gebruikers hun account kunnen ontgrendelen zonder het wachtwoord opnieuw in te hoeven stellen.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Zorg ervoor dat alle gebruikers zijn geregistreerd voor MFA en SSPR

Azure biedt rapporten die door u en uw organisatie kunnen worden gebruikt om ervoor te zorgen dat gebruikers zijn geregistreerd voor MFA en SSPR. Gebruikers die zich nog niet hebben geregistreerd, moeten mogelijk worden opgeleid over het proces.

Het [MFA-aanmeldingsrapport](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) bevat informatie over MFA-gebruik en geeft u inzicht in hoe MFA werkt in uw organisatie. Toegang hebben tot aanmeldingsactiviteiten (en audits en risicodetecties) voor Azure AD is cruciaal voor het oplossen van problemen, gebruiksanalyses en forensisch onderzoek.

Op dezelfde manier kan het [selfservice wachtwoordbeheerrapport](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) worden gebruikt om te bepalen wie zich heeft (of niet) heeft geregistreerd voor SSPR.

### <a name="self-service-app-management"></a>Beheer van selfservice-apps

Voordat uw gebruikers zelf toepassingen kunnen ontdekken vanuit hun toegangspaneel, moet u [selfservice-toepassingen toegang](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) bieden tot toepassingen die u gebruikers zelf wilt laten ontdekken en toegang tot aanvragen. Selfservice-applicatietoegang is een geweldige manier om gebruikers in staat te stellen toepassingen zelf te ontdekken en de bedrijfsgroep optioneel toegang tot die toepassingen te laten goedkeuren. U de bedrijfsgroep toestaan de referenties te beheren die aan deze gebruikers zijn toegewezen voor toepassingen met een enkel wachtwoord met [één melding vanaf](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) hun toegangspanelen.

### <a name="self-service-group-management"></a>Groepsbeheer via selfservice

Het toewijzen van gebruikers aan toepassingen kan het beste in kaart worden gebracht bij het gebruik van groepen, omdat ze een grote flexibiliteit en het vermogen om op schaal te beheren mogelijk maken:

* Op basis van kenmerken met behulp van dynamisch groepslidmaatschap
* Delegeren naar app-eigenaren

Azure AD biedt de mogelijkheid om toegang tot bronnen te beheren met behulp van beveiligingsgroepen en Office 365-groepen. Deze groepen kunnen worden beheerd door een groepseigenaar die lidmaatschapsaanvragen kan goedkeuren of weigeren en het beheer van het groepslidmaatschap kan delegeren. Deze functie wordt [zelfservicegroepsbeheer genoemd](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)en bespaart tijd doorgroepseigenaren die geen beheerrol toegewezen krijgen, toe te staan groepen te maken en te beheren zonder dat u hoeft te vertrouwen op beheerders om hun aanvragen af te handelen.

## <a name="step-4---operationalize-your-insights"></a>Stap 4 - Operationaliseer uw inzichten

Het controleren en registreren van beveiligingsgerelateerde gebeurtenissen en gerelateerde waarschuwingen zijn essentiële onderdelen van een efficiënte strategie om ervoor te zorgen dat gebruikers productief blijven en uw organisatie veilig is. Beveiligingslogboeken en rapporten kunnen helpen bij het beantwoorden van vragen zoals:

* Gebruik je waar je voor betaalt?
* Is er iets verdachts of kwaadaardigs gebeurt in mijn tenant?
* Wie is er getroffen tijdens een beveiligingsincident?

Beveiligingslogboeken en -rapporten bieden u een elektronisch overzicht van verdachte activiteiten en helpen u patronen te detecteren die kunnen duiden op een poging tot of succesvolle externe penetratie van het netwerk en interne aanvallen. U controle gebruiken om de activiteiten van gebruikers te controleren, naleving van de regelgeving te documenteren, forensische analyses te doen en meer. Waarschuwingen bieden meldingen van beveiligingsgebeurtenissen.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Minste bevoorrechte beheerdersrollen toewijzen voor bewerkingen

Als u nadenkt over uw aanpak van de operaties, zijn er een paar niveaus van administratie te overwegen. Het eerste niveau legt de administratielast op uw globale beheerder(s). Altijd gebruik maken van de wereldwijde beheerder rol, kan geschikt zijn voor kleinere bedrijven. Maar voor grotere organisaties met helpdeskpersoneel en beheerders die verantwoordelijk zijn voor specifieke taken, kan het toewijzen van de rol van globale beheerder een beveiligingsrisico zijn, omdat het die personen de mogelijkheid biedt om taken te beheren die hoger zijn dan wat ze moeten kunnen doen.

In dit geval moet u rekening houden met het volgende niveau van administratie. Met Azure AD u eindgebruikers aanwijzen als 'beperkte beheerders' die taken kunnen beheren in minder bevoorrechte rollen. U uw helpdeskpersoneel bijvoorbeeld de rol [van beveiligingslezer](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) toewijzen om hen de mogelijkheid te bieden beveiligingsfuncties te beheren met alleen-lezen toegang. Of misschien is het zinvol om de [verificatiebeheerderrol](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) toe te wijzen aan individuen om hen de mogelijkheid te geven om niet-wachtwoordreferenties opnieuw in te stellen of Azure Service Health te lezen en te configureren.

Zie [Beheerdersrolmachtigingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)voor meer informatie.

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Hybride componenten bewaken (Azure AD Connect-synchronisatie, AD FS) met Azure AD Connect-status

Azure AD Connect en AD FS zijn kritieke onderdelen die het levenscyclusbeheer en de verificatie mogelijk kunnen doorbreken en uiteindelijk tot uitval kunnen leiden. Daarom moet u Azure AD Connect-status implementeren voor het bewaken en rapporteren van deze onderdelen.

Lees [ad fs.v. Monitor AD FS voor](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)meer informatie met Azure AD Connect Health .

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Azure Monitor gebruiken om gegevenslogboeken voor analyses te verzamelen

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) is een uniforme monitoringportal voor alle Azure AD-logboeken, die diepgaande inzichten, geavanceerde analyses en slimme machine learning biedt. Met Azure Monitor u statistieken en logboeken binnen de portal en via API's gebruiken om meer inzicht te krijgen in de status en prestaties van uw resources. Het maakt een enkele ruitervaring binnen het portaal mogelijk en maakt een breed scala aan productintegraties mogelijk via API's en gegevensexportopties die traditionele SIEM-systemen van derden ondersteunen. Azure Monitor biedt u ook de mogelijkheid om waarschuwingsregels te configureren om een melding te ontvangen of om geautomatiseerde acties uit te voeren voor problemen die van invloed zijn op uw resources.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Maak aangepaste dashboards voor uw leiderschap en uw dagelijkse

Organisaties die geen SIEM-oplossing hebben, kunnen het [Power BI-inhoudspakket](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) voor Azure AD downloaden. Het Power BI-inhoudspakket bevat vooraf gebouwde rapporten om u te helpen begrijpen hoe uw gebruikers Azure AD-functies gebruiken en gebruiken, zodat u inzicht krijgen in alle activiteiten in uw directory. U ook uw eigen [aangepaste dashboard](https://docs.microsoft.com/power-bi/service-dashboards) maken en delen met uw leiderschapsteam om verslag uit te brengen over dagelijkse activiteiten. Dashboards zijn een geweldige manier om uw bedrijf te controleren en al uw belangrijkste statistieken in één oogopslag te bekijken. De visualisaties op een dashboard kunnen afkomstig zijn uit een of meer onderliggende gegevenssets en rapporten. Een dashboard combineert on-premises gegevens en gegevens in de cloud om zo een geconsolideerde weergave te bieden van uw gegevens, ongeacht waar deze zich bevinden.

![Op power BI aangepast dashboard](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Inzicht in uw ondersteuningsoproepstuurprogramma's

Wanneer u een hybride identiteitsoplossing implementeert zoals beschreven in dit artikel, moet u uiteindelijk een vermindering van uw ondersteuningsoproepen opmerken. Veelvoorkomende problemen zoals vergeten wachtwoorden en accountuitsluitingen worden beperkt door het implementeren van Azure's selfservice wachtwoordreset, terwijl gebruikers selfservice-toepassingstoegang kunnen inschakelen en toegang tot toepassingen kunnen aanvragen zonder te vertrouwen op uw IT-personeel.

Als u geen vermindering van het aantal ondersteuningsoproepen waarneemt, raden we u aan uw ondersteuningsoproepstuurprogramma's te analyseren in een poging om te bevestigen of de toegang tot SSPR of selfservice-toepassingen correct is geconfigureerd of als er andere nieuwe problemen zijn die systematisch kunnen worden Gericht.

*"In ons digitale transformatietraject hadden we een betrouwbare leverancier van identiteits- en toegangsbeheer nodig om een naadloze maar veilige integratie tussen ons, partners en cloudserviceproviders te vergemakkelijken, voor een effectief ecosysteem; Azure AD was de beste optie die ons de benodigde mogelijkheden en zichtbaarheid bood die ons in staat stelden om risico's te detecteren en erop te reageren."* --- [Yazan Almasri, Global Information Security Director, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Houd uw gebruik van apps in de gaten om inzichten te genereren

Naast het ontdekken van Shadow IT, kan het monitoren van app-gebruik in uw hele organisatie met behulp van [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) uw organisatie helpen om de belofte van cloudtoepassingen ten volle te benutten. Het kan u helpen de controle over uw assets te behouden door een betere zichtbaarheid in de activiteit en de bescherming van kritieke gegevens in verschillende cloudtoepassingen te verbeteren. Door het gebruik van apps in uw organisatie te controleren met BEHULP van MCAS, u de volgende vragen beantwoorden:

* In welke niet-goedgekeurde apps gebruiken werknemers om gegevens op te slaan?
* Waar en wanneer worden gevoelige gegevens opgeslagen in de cloud?
* Wie heeft toegang tot gevoelige gegevens in de cloud?

*"Met Cloud App Security kunnen we snel afwijkingen opsporen en actie ondernemen."* --- [Eric LePenske, Senior Manager Informatiebeveiliging, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Samenvatting

Er zijn veel aspecten aan het implementeren van een hybride identiteitsoplossing, maar deze checklist in vier stappen helpt u snel een identiteitsinfrastructuur te realiseren waarmee gebruikers productiever en veiliger kunnen zijn.

* Maak eenvoudig verbinding met apps
* Stel automatisch één identiteit vast voor elke gebruiker
* Geef uw gebruikers een veilige staat van kracht
* Operationaliseer uw inzichten

We hopen dat dit document een nuttig stappenplan is om een sterke identiteitsbasis voor uw organisatie op te zetten.

## <a name="identity-checklist"></a>Identiteitschecklist

We raden u aan de volgende checklist ter referentie af te drukken wanneer u uw reis naar een meer solide identiteitsbasis in uw organisatie begint.

### <a name="today"></a>Today

|Klaar?|Item|
|:-|:-|
||Pilot Self-Service Password Reset (SSPR) voor een groep|
||Hybride componenten bewaken met Azure AD Connect-status|
||Minste bevoorrechte beheerdersrollen toewijzen voor bewerking|
||Ontdek Shadow IT met Microsoft Cloud App Security|
||Azure Monitor gebruiken om gegevenslogboeken te verzamelen voor analyse|

### <a name="next-two-weeks"></a>Komende twee weken

|Klaar?|Item|
|:-|:-|
||Een app beschikbaar maken voor uw gebruikers|
||Azure AD-inrichting voor een SaaS-app naar keuze pilot|
||Een faseringsserver instellen voor Azure AD Connect en deze up-to-date houden|
||Apps migreren van ADFS naar Azure AD|
||Maak aangepaste dashboards voor uw leiderschap en uw dagelijkse|

### <a name="next-month"></a>Volgende maand

|Klaar?|Item|
|:-|:-|
||Houd uw gebruik van apps in de gaten om inzichten te genereren|
||Pilot veilige toegang op afstand tot apps|
||Zorg ervoor dat alle gebruikers zijn geregistreerd voor MFA en SSPR|
||Cloudverificatie inschakelen|

### <a name="next-three-months"></a>Volgende drie maanden

|Klaar?|Item|
|:-|:-|
||Selfservice-appbeheer inschakelen|
||Selfservicegroepsbeheer inschakelen|
||Houd uw gebruik van apps in de gaten om inzichten te genereren|
||Inzicht in uw ondersteuningsoproepstuurprogramma's|

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe u uw veilige houding vergroten met behulp van de mogelijkheden van Azure Active Directory en deze checklist voor vijf stappen - [Vijf stappen om uw identiteitsinfrastructuur te beveiligen.](https://aka.ms/securitysteps)

Ontdek hoe de identiteitsfuncties in Azure AD u kunnen helpen uw overgang naar cloudbeheer te versnellen door de oplossingen en mogelijkheden te bieden waarmee organisaties snel meer van hun identiteitsbeheer kunnen overnemen en verplaatsen van traditionele on-premises systemen naar Azure AD - [Hoe Azure AD Cloud-beheer levert voor on-premises workloads.](https://aka.ms/cloudgoverned)
