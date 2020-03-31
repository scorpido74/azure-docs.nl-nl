---
title: Een Azure Active Directory Access Panel-implementatie plannen
description: Richtlijnen voor het implementeren van het Azure Active Directory Access Panel
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d8b6c6d40aa81bf56baed59f90417f2147fa56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897071"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Een Azure Active Directory Access Panel-implementatie plannen

Het Azure Active Directory (Azure AD) Access Panel is een webportal die helpt de ondersteuningskosten te verlagen, de productiviteit en beveiliging te verhogen en de frustratie van gebruikers te verminderen. Het systeem bevat gedetailleerde rapportage die bijhoudt wanneer u toegang krijgt tot het systeem en waarschuwt beheerders van misbruik of misbruik.

Met het Azure AD Access-paneel u het nieuwe:

* Ontdek en krijg toegang tot alle Azure AD-verbonden bronnen van hun bedrijf, zoals toepassingen
* Toegang tot nieuwe apps en groepen aanvragen
* Toegang tot deze bronnen beheren voor anderen
* Zelfservicewachtwoordresets en Azure Multi-Factor Authentication-instellingen beheren
* Hun apparaten beheren

Het stelt beheerders ook in staat om te beheren:

* Servicevoorwaarden
* Organisaties
* Toegangsbeoordelingen


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Voordelen van Azure AD Access Panel-integratie

Het Azure AD Access Panel biedt voordelen voor bedrijven op de volgende manieren:

**Intuïtieve gebruikerservaring:** het Access Panel biedt u één platform voor al uw azure single sign-on (SSO)-toepassingen. U hebt een uniforme portal om bestaande instellingen en nieuwe mogelijkheden te vinden, zoals groepsbeheer en het opnieuw instellen van selfservicewachtwoorden, terwijl deze worden toegevoegd. De intuïtieve ervaring stelt gebruikers in staat om sneller te werken en productiever te zijn, terwijl ze hun frustratie verminderen.

**Verhoogt de productiviteit:** alle gebruikerstoepassingen in het Access Panel hebben SSO ingeschakeld. Door SSO in te schakelen voor bedrijfstoepassingen en Office 365 ontstaat een superieure aanmeldingservaring door extra aanmeldingsprompts te verminderen of te elimineren. Het Access Panel maakt gebruik van selfservice en dynamisch lidmaatschap en verbetert de algehele beveiliging van uw identiteitssysteem. Het doet dit door ervoor te zorgen dat de juiste mensen de toegang tot de applicaties beheren. Het toegangspaneel fungeert als een samenhangende bestemmingspagina om snel bronnen te vinden en werktaken voort te zetten.

**Beheert kosten:** Het inschakelen van het toegangspaneel met Azure AD kan helpen bij het desinvestering van on-premises infrastructuren. Het verlaagt de ondersteuningskosten door u een consistente portal te bieden om al uw apps te vinden, toegang tot bronnen aan te vragen en accounts te beheren.

**Verhoogt de flexibiliteit en beveiliging:** het Access Panel geeft u toegang tot de beveiliging en flexibiliteit die een cloudplatform biedt. Beheerders kunnen eenvoudig instellingen wijzigen in toepassingen en resources en kunnen voldoen aan nieuwe beveiligingsvereisten zonder dat dit gevolgen heeft voor gebruikers.

**Maakt robuuste controle- en gebruikstracking mogelijk:** controle en gebruikstracking voor alle gebruikersmogelijkheden laten u weten wanneer gebruikers hun resources gebruiken en zorgt ervoor dat u de beveiliging beoordelen.

### <a name="licensing-considerations"></a>Licentieoverwegingen

Het Toegangspaneel is gratis en vereist geen licenties om op een basisniveau te gebruiken. Het aantal objecten in uw map en de extra functies die u wilt implementeren, kan echter extra licenties vereisen. Enkele veelvoorkomende Azure AD-scenario's met licentievereisten bevatten de volgende beveiligingsfuncties:

* [Azure-verificatie met meerdere factoren](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Groepslidmaatschap](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Self-service voor wachtwoord opnieuw instellen](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory-identiteitsbeveiliging](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Bekijk de [volledige licentiehandleiding voor Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Vereisten voor het implementeren van het Azure AD Access Panel

Voer de volgende vereisten in voordat u met dit project begint:

* [Applicatie-SSO integreren](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Azure AD-gebruikers- en groepsinfrastructuur beheren](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>De implementatie van het Azure AD Access Panel plannen

In de volgende tabel worden de belangrijkste use cases voor een implementatie van een access panel beschreven:

| Onderwerp| Beschrijving |
| - | - |
| Toegang| Access Panel portal is toegankelijk vanaf zakelijke en persoonlijke apparaten binnen het bedrijfsnetwerk. |
|Toegang | Access Panel portal is toegankelijk vanaf zakelijke apparaten buiten het bedrijfsnetwerk. |
| Controleren| Gebruiksgegevens worden minstens om de 29 dagen gedownload naar bedrijfssystemen. |
| Beheer| De levenscyclus van de gebruikerstoewijzingen naar azure AD-gerelateerde toepassingen en groepen wordt gedefinieerd en gecontroleerd. |
| Beveiliging| De toegang tot bronnen wordt beheerd via gebruikers- en groepstoewijzingen. Alleen geautoriseerde gebruikers kunnen toegang tot bronnen beheren. |
| Prestaties| Toegangstoewijzingstijdlijnen worden gedocumenteerd en gecontroleerd. |
| Gebruikerservaring| Gebruikers zijn zich bewust van de mogelijkheden van het Toegangspaneel en hoe ze deze kunnen gebruiken.|
| Gebruikerservaring| Gebruikers kunnen hun toegang tot toepassingen en groepen beheren.|
| Gebruikerservaring| Gebruikers kunnen hun accounts beheren. |
| Gebruikerservaring| Gebruikers zijn zich bewust van de compatibiliteit van de browser. |
| Ondersteuning| Gebruikers kunnen ondersteuning vinden voor problemen met het Access Panel. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Aanbevolen procedures voor het implementeren van het Azure AD Access Panel

De functionaliteit van het Toegangspaneel kan geleidelijk worden ingeschakeld. We raden de volgende volgorde van implementatie aan:

1. Mijn apps
   * Startprogramma voor apps
   * Beheer van selfservice-apps
   * Microsoft Office 365-integratie

1. Zelfbediening app-detectie
   * Self-service voor wachtwoord opnieuw instellen
   * Instellingen voor meervoudige verificatie
   * Apparaatbeheer
   * Gebruiksvoorwaarden
   * Organisaties beheren

1. Mijn groepen
   * Groepsbeheer via selfservice
1. Toegangsbeoordelingen
   * Beheer van toegangsbeoordeling

Beginnen met Mijn apps introduceert gebruikers naar de portal als een gemeenschappelijke plaats voor toegang tot bronnen. De toevoeging van selfservice applicatie detectie bouwt voort op de My Apps ervaring. Mijn groepen en toegangsbeoordelingen bouwen voort op de selfservicemogelijkheden.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Configuraties plannen voor het Azure AD Access-paneel

In de volgende tabel worden een aantal belangrijke configuraties van het Access Panel en de typische waarden vermeld die u mogelijk gebruikt:

| Configuratie| Typische waarden |
| - | - |
| De pilootgroepen bepalen| Identificeer de Azure AD-beveiligingsgroep die moet worden gebruikt en zorg ervoor dat alle pilootleden deel uitmaken van de groep. |
| Bepaal de groep of groepen die moeten worden ingeschakeld voor productie.| Identificeer de Azure AD-beveiligingsgroepen of de Active Directory-groepen die zijn gesynchroniseerd met Azure AD, die moeten worden gebruikt. Zorg ervoor dat alle pilootleden deel uitmaken van de groep. |
| Gebruikers toestaan SSO te gebruiken voor bepaalde typen toepassingen| Federatieve SSO, OAuth, Wachtwoord SSO, App Proxy |
| Gebruikers toestaan om selfservice wachtwoord opnieuw instellen te gebruiken | Ja |
| Gebruikers toestaan multifactorauthenticatie te gebruiken| Ja |
| Gebruikers toestaan om selfservicegroepsbeheer te gebruiken voor bepaalde typen groepen| Beveiligingsgroepen, Office 365-groepen |
| Gebruikers toestaan selfservice-appbeheer te gebruiken| Ja |
| Gebruikers toestaan toegangsbeoordelingen te gebruiken| Ja |

### <a name="plan-consent-strategy"></a>Toestemmingsstrategie plannen

Gebruikers of beheerders moeten instemmen met de gebruiksvoorwaarden en het privacybeleid van een toepassing. Indien mogelijk, gezien uw bedrijfsregels, gebruik administrator toestemming, die gebruikers een betere ervaring geeft.

Als u beheerderstoestemming wilt gebruiken, moet u een globale beheerder van de organisatie zijn en moeten de toepassingen:

* Geregistreerd in uw organisatie

* Geregistreerd in een andere Azure AD-organisatie en eerder goedgekeurd door ten minste één gebruiker

Zie [De manier waarop eindgebruikers toestemming geven voor een toepassing in Azure Active Directory configureren](configure-user-consent.md)voor meer informatie.

### <a name="engage-the-right-stakeholders"></a>Betrek de juiste stakeholders

Wanneer technologieprojecten mislukken, doen ze dit meestal vanwege de onafwijkende verwachtingen over impact, resultaten en verantwoordelijkheden. Om deze valkuilen te vermijden, [moet u ervoor zorgen dat u de juiste belanghebbenden indienst gaat](../fundamentals/active-directory-deployment-plans.md) en dat de rollen van belanghebbenden in het project goed worden begrepen.

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is cruciaal voor het succes van elke nieuwe dienst. Informeer uw gebruikers proactief hoe en wanneer hun ervaring zal veranderen en hoe ze ondersteuning kunnen krijgen als dat nodig is.

Hoewel het Access-paneel doorgaans geen gebruikersproblemen creëert, is het belangrijk om zich voor te bereiden. Maak gidsen en een lijst met alle bronnen voor uw ondersteuningspersoneel voordat u wordt gestart.

#### <a name="communications-templates"></a>Communicatiesjablonen

Microsoft biedt [aanpasbare sjablonen voor e-mails en andere communicatie](https://aka.ms/APTemplates) voor het Toegangspaneel. U deze activa aanpassen voor gebruik in andere communicatiekanalen, indien van toepassing op uw bedrijfscultuur.

## <a name="plan-your-sso-configuration"></a>Uw SSO-configuratie plannen

Wanneer een gebruiker zich aanmeldt bij een toepassing, doorlopen ze een verificatieproces en moeten ze bewijzen wie ze zijn. Zonder SSO wordt een wachtwoord opgeslagen in de toepassing en moet de gebruiker dit wachtwoord kennen. Met SSO worden de referenties van gebruikers doorgegeven aan de toepassing, zodat ze geen wachtwoorden voor elke toepassing opnieuw hoeven in te voeren.

Om toepassingen in Mijn apps te starten, moet SSO zijn ingeschakeld.

Azure AD ondersteunt drie verschillende manieren om [eenmalige aanmelding smaken toepassingen in](what-is-single-sign-on.md)te schakelen:

* **Federatieve enkele aanmelding** 
    * Hiermee stelt u een toepassing in om door te verwijzen naar Azure AD voor gebruikersverificatie, in plaats van om een wachtwoord te vragen. 
    * Wordt ondersteund voor toepassingen die protocollen gebruiken, zoals SAML 2.0, WS-Federation of OpenID Connect, en is de rijkste modus van single sign-on.

* **Single sign-on op basis van wachtwoord** 
    * Hiermee u veilige opslag en herhaling van het wachtwoord van toepassingen mogelijk maken met behulp van een webbrowserextensie of mobiele app. 
    * Maakt gebruik van het bestaande aanmeldingsproces van de toepassing, maar stelt een beheerder in staat om de wachtwoorden te beheren. De gebruiker hoeft het wachtwoord niet te kennen.

* **Bestaande enkele aanmelding** 
    * Hiermee kan Azure AD profiteren van bestaande enkele aanmeldingdie is geconfigureerd voor de toepassing.
    * Hiermee kunnen deze toepassingen worden gekoppeld aan de portals voor office 365 of Azure AD Access Panel. 
    * Hiermee u extra rapportage in Azure AD indienen wanneer de toepassingen daar worden gestart. 
    * Inclusief het gebruik van Azure Application Proxy en de gekoppelde enkele aanmeldingsmodus.

Lees hier hoe u de SSO-modus van een toepassing configureert: [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Voor de beste ervaring met de pagina Mijn apps, begin met de integratie van cloudtoepassingen die beschikbaar zijn voor federated SSO. Federated SSO stelt gebruikers in staat om een consistente one-click ervaring over hun app lancering oppervlakken hebben en heeft de neiging om meer robuust in configuratiecontrole.

Gebruik Federated SSO met Azure AD (OpenID Connect/SAML) wanneer een toepassing dit ondersteunt, in plaats van op een wachtwoord gebaseerde SSO en ADFS.

Zie het [SaaS SSO-implementatieplan](https://aka.ms/deploymentplans/sso)voor meer informatie over het implementeren en configureren van uw SaaS-toepassingen.

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Plannen om de browserextensie Mijn apps te implementeren

Wanneer gebruikers zich aanmelden bij sso-toepassingen op basis van wachtwoorden, moeten ze de inlogextensie Voor beveiliging van Mijn apps installeren en gebruiken. De extensie voert een script uit dat het wachtwoord doorstuurt naar het aanmeldingsformulier van de toepassing. Gebruikers wordt gevraagd om de extensie te installeren wanneer ze voor het eerst de op wachtwoorden gebaseerde SSO-toepassing starten. Meer informatie over de extensie vindt u in deze documentatie over [het installeren van de Access Panel browserextensie.](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)

Als u op wachtwoorden gebaseerde SSO-toepassingen moet integreren, moet u een mechanisme definiëren om de extensie op schaal te implementeren met [ondersteunde browsers.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) Een aantal opties:

* [Groepsbeleid voor Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Configuratiebeheer voor Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Door de gebruiker gestuurde download en configuratie voor Chrome, Firefox, Microsoft Edge of IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Meer informatie: [Wachtwoordeenmalige aanmelding configureren](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Gebruikers die geen gebruik maken van op een wachtwoord gebaseerde SSO-toepassingen profiteren ook van de extensie. Deze voordelen omvatten de mogelijkheid om elke app te starten vanaf de zoekbalk, het vinden van toegang tot recent gebruikte toepassingen en het hebben van een link naar de pagina Mijn apps.

Dit is wat de gebruiker ziet bij het starten van een op een wachtwoord gebaseerde SSO-toepassing voor de eerste keer:

![Schermafbeelding van het installatiescherm van de browserextensie Mijn apps ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Plannen voor mobiele toegang

Een browser die is beveiligd met intune-beleid (Microsoft Edge of Intune Managed Browser) is noodzakelijk voor mobiele gebruikers die op wachtwoorden gebaseerde SSO-toepassingen starten. Met een browser die door het beleid is beveiligd, kan het wachtwoord dat voor de toepassing is opgeslagen, worden overgedragen. Microsoft Edge of de beheerde browser biedt een reeks functies voor webgegevensbescherming. U Microsoft Edge ook gebruiken voor bedrijfsscenario's op iOS- en Android-apparaten. Microsoft Edge ondersteunt dezelfde beheerscenario's als de Intune Managed Browser en verbetert de gebruikerservaring. Meer informatie: [Webtoegang beheren met een browser die door Microsoft Intune-beleid is beveiligd.](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>Uw implementatie van Mijn apps plannen

De basis van het Access Panel is de applicatie [https://myapps.microsoft.com](https://myapps.microsoft.com/)launcher My Apps, die gebruikers toegang hebben op . De My Apps-pagina's geven gebruikers één plek om hun werk te starten en hun noodzakelijke toepassingen te vinden. Hier vinden gebruikers een lijst met alle toepassingen waartoe ze eenmalig toegang hebben. 

![Een schermafbeelding van het deelvenster Apps](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Dezelfde toepassingen worden weergegeven in het startprogramma voor Office 365-apps wanneer gebruikers de Office 365-portal gebruiken.

Plan de volgorde waarin u toepassingen toevoegt aan de start-installatie Mijn apps en bepaal of u ze geleidelijk of allemaal tegelijk uitrolt. Maak hiervoor een toepassingsvoorraad met het type verificatie en eventuele bestaande SSO-integraties voor elke toepassing.

#### <a name="add-applications-to-the-my-apps-panel"></a>Toepassingen toevoegen aan het deelvenster Mijn apps

Elke Azure AD SSO-toepassing kan worden toegevoegd aan het startprogramma Mijn apps. Andere toepassingen worden toegevoegd met behulp van de Linked SSO-optie. U een toepassingstegel configureren die verwijst naar de URL van uw bestaande webtoepassing. Met Linked SSO u gebruikers naar de My Apps-portal leiden zonder alle toepassingen te migreren naar Azure AD SSO. U geleidelijk overgaan naar azure AD SSO-geconfigureerde toepassingen zonder de gebruikerservaring te verstoren.

#### <a name="use-my-apps-collections"></a>Mijn apps-verzamelingen gebruiken

Standaard worden alle toepassingen samen op één pagina weergegeven. Maar u verzamelingen gebruiken om gerelateerde toepassingen samen te groeperen en op een apart tabblad te presenteren, waardoor ze gemakkelijker te vinden zijn. U bijvoorbeeld verzamelingen gebruiken om logische groeperingen van toepassingen te maken voor specifieke taken, taken, projecten, enzovoort. Zie Mijn [apps-verzamelingen gebruiken om gebruikerstoegangspanelen aan te passen](access-panel-collections.md)voor meer informatie. 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Plannen of mijn apps of een bestaande portal moeten worden gebruikt

Uw gebruikers hebben mogelijk al een andere toepassing of portal dan Mijn apps. Als dat het zo is, besluit u of u beide portalen wilt ondersteunen of er slechts één wilt gebruiken.

Als een bestaande portal al wordt gebruikt als startpunt voor gebruikers, u de functionaliteit Van Mijn apps integreren met behulp van URL's van gebruikerstoegang. URL's voor gebruikerstoegang fungeren als directe koppelingen naar de toepassingen die beschikbaar zijn in de mijn apps-portal. Deze URL's kunnen worden ingesloten in een bestaande website. Wanneer een gebruiker de koppeling selecteert, wordt de toepassing geopend vanuit de mijn apps-portal.

U de eigenschap URL van de gebruikerstoegang vinden in **het** eigenschappengebied van de toepassing in de Azure-portal.

![Een schermafbeelding van het deelvenster Apps](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Detectie en toegang van selfservicetoepassingen plannen

Zodra een kernset toepassingen is geïmplementeerd op de Mijn Apps-pagina van een gebruiker, moet u functies voor zelfservice-appbeheer inschakelen. Zelfbediening app detectie stelt gebruikers in staat om:

* Vind nieuwe apps om toe te voegen aan hun Mijn Apps. 
* Voeg optionele apps toe waarvan ze mogelijk niet weten dat ze ze nodig hebben tijdens de installatie.

Goedkeuringswerkstromen zijn beschikbaar voor expliciete goedkeuring voor toegang tot toepassingen. Gebruikers die fiatteurs zijn, ontvangen meldingen binnen de mijn apps-portal wanneer er een aanvraag voor toegang tot de toepassing is ingediend.

## <a name="plan-self-service-group-membership"></a>Zelfbedieningsgroep lidmaatschap plannen 

U gebruikers inschakelen hun eigen beveiligingsgroepen of Office 365-groepen te maken en te beheren in Azure AD. De eigenaar van de groep kan lidmaatschapsaanvragen goedkeuren of weigeren en het beheer van het groepslidmaatschap delegeren. Zelfbeheerfuncties voor groepen zijn niet beschikbaar voor beveiligingsgroepen of distributielijsten met e-mail.

Als u wilt plannen voor het lidmaatschap van zelfbedieningsgroepen, bepaalt u of u alle gebruikers in uw organisatie toestaat groepen te maken en te beheren of alleen een subset van gebruikers. Als u een subset van gebruikers toestaat, moet u een groep instellen waaraan deze personen zijn toegevoegd. Zie [Zelfservicegroepsbeheer instellen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) voor meer informatie over het inschakelen van deze scenario's.

## <a name="plan-reporting-and-auditing"></a>Planrapportage en auditing

Azure AD biedt [rapporten met technische en zakelijke inzichten.](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/) Werk samen met eigenaren van uw bedrijf en technische toepassingen om eigenaar te worden van deze rapporten en deze regelmatig te consumeren. In de volgende tabel vindt u enkele voorbeelden van typische rapportagescenario's.

|   | beheer risico's.| Verhoog de productiviteit| Governance en naleving |
|  - |- | - | - |
| Rapporttypen|  Toepassingsmachtigingen en -gebruik| Rekeningbepalingsactiviteit| Controleren wie toegang heeft tot de toepassingen |
| Mogelijke acties| Toegang tot de controle; machtigingen intrekken| Eventuele indikfouten herstellen| Toegang intrekken |

Azure AD bewaart de meeste controlegegevens gedurende 30 dagen. De gegevens zijn beschikbaar via Azure Admin Portal of API die u downloaden naar uw analysesystemen.

#### <a name="auditing"></a>Controleren

Controlelogboeken voor toegang tot toepassingen zijn 30 dagen beschikbaar. Als beveiligingscontrole binnen uw onderneming een langere bewaring vereist, moeten de logboeken worden geëxporteerd naar een SIEM-tool (Security Information Event and Management), zoals Splunk of ArcSight.

Voor back-ups van controle, rapportage en noodherstel documenteert u de vereiste frequentie van downloaden, wat het doelsysteem is en wie verantwoordelijk is voor het beheer van elke back-up. U hebt mogelijk geen afzonderlijke back-ups voor controle en rapportage nodig. Uw back-up voor noodherstel moet een afzonderlijke entiteit zijn.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Toepassingen implementeren in het deelvenster Mijn apps van gebruikers

Nadat een toepassing is geconfigureerd voor SSO, krijgen groepen toegang toegewezen. Gebruikers in de toegewezen groepen hebben toegang en ze zien de toepassing in hun Mijn apps en het startprogramma voor Office 365-apps.

Zie [Gebruikers en groepen toewijzen aan een toepassing in Active Directory](methods-for-assigning-users-and-groups.md).

Zie Een toepassing verbergen voor de [gebruikerservaring in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)als u tijdens het testen of implementeren de groepen wilt toevoegen, maar nog niet toestaat dat de toepassingen worden weergegeven in Mijn apps.

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Microsoft Office 365-toepassingen implementeren in Mijn apps

Voor Office 365-toepassingen ontvangen gebruikers een kopie van Office op basis van licenties die aan hen zijn toegewezen. Een voorwaarde voor toegang tot Office-toepassingen is dat gebruikers de juiste licenties toegewezen krijgen die zijn gekoppeld aan de Office-toepassingen. Wanneer u een gebruiker een licentie toewijst, ziet deze automatisch de toepassingen die zijn gekoppeld aan de licentie op de pagina Mijn apps en in het startprogramma voor Office 365-apps.

Als u een set Office-toepassingen wilt verbergen voor gebruikers, is er een optie om apps te verbergen voor de mijn apps-portal, terwijl u nog steeds toegang hebt vanuit de Office 365-portal. Zoek deze instellingen in het gedeelte Gebruikersinstellingen van de toepassing. Meer informatie: [Een toepassing verbergen voor de gebruikerservaring in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Een schermafbeelding van het configureren van het verbergen van toepassingen](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Selfservice-mogelijkheden voor toepassingen implementeren

Self-service applicatie toegang stelt gebruikers in staat om zelf te ontdekken en toegang tot toepassingen te vragen. Gebruikers hebben de vrijheid om toegang te krijgen tot de apps die ze nodig hebben zonder telkens via een IT-groep om toegang te vragen. Wanneer een gebruiker toegang aanvraagt en wordt goedgekeurd, automatisch of handmatig door een app-eigenaar, worden deze toegevoegd aan een groep aan de achterkant. Rapportage is ingeschakeld voor wie toegang heeft aangevraagd, goedgekeurd of verwijderd, en het geeft u controle over het beheren van de toegewezen rollen.

U de goedkeuring van aanvragen voor toegang tot toepassingen delegeren aan zakelijke fiatteurs. De zakelijke goedkeurder kan de app-toegangswachtwoorden instellen vanaf de pagina Mijn apps van de zakelijke goedkeurder.

Meer informatie: [Toegang tot selfservicetoepassingen gebruiken](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).

![Een screenshot van het configureren van selfservicetoepassingsbeheer](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Uw implementatie valideren

Zorg ervoor dat uw implementatie van het Access Panel grondig is getest en dat er een terugdraaiplan is.

De volgende tests moeten worden uitgevoerd met zowel apparaten die eigendom zijn van het bedrijf als met persoonlijke apparaten. Deze testcases moeten ook uw zakelijke use cases weerspiegelen. Hieronder volgen enkele gevallen op basis van de zakelijke vereisten in dit document en op typische technische scenario's. Voeg anderen toe die specifiek zijn voor uw behoeften.

#### <a name="application-sso-access-test-case-examples"></a>Voorbeelden van testcasevan toepassing SSO-toegang:


| Business case| Verwacht resultaat |
| - | -|
| Gebruiker meldt zich aan bij de mijn apps-portal| Gebruiker kan zich aanmelden en hun toepassingen bekijken |
| Gebruiker lanceert een gefedereerde SSO-applicatie| Gebruiker wordt automatisch aangemeld bij de toepassing |
| Gebruiker lanceert voor het eerst een Wachtwoord SSO-toepassing| Gebruiker moet de extensie Mijn apps installeren |
| Gebruiker lanceert een wachtwoord SSO-toepassing een volgende keer| Gebruiker wordt automatisch aangemeld bij de toepassing |
| Gebruiker start een app vanuit Office 365 Portal| Gebruiker wordt automatisch aangemeld bij de toepassing |
| Gebruiker start een app vanuit de beheerde browser| Gebruiker wordt automatisch aangemeld bij de toepassing |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Testvoorbeelden voor selfservicemogelijkheden voor toepassingen


| Business case| Verwacht resultaat |
| - | - |
| Gebruiker kan lidmaatschap van de toepassing beheren| Gebruiker kan leden toevoegen/verwijderen die toegang hebben tot de app |
| Gebruiker kan de toepassing bewerken| De gebruiker kan de beschrijving en referenties van de toepassing voor SSO-toepassingen met wachtwoord bewerken |

### <a name="rollback-steps"></a>Terugdraaien, stappen

Het is belangrijk om te plannen wat te doen als uw implementatie niet verloopt zoals gepland. Als de SSO-configuratie tijdens de implementatie mislukt, moet u begrijpen hoe [u SSO-problemen oplossen](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) en de impact voor uw gebruikers verminderen. In extreme omstandigheden moet u sso mogelijk [terugdraaien.](../manage-apps/plan-sso-deployment.md)


## <a name="manage-your-implementation"></a>Uw implementatie beheren

U moet de minst bevoorrechte rol gebruiken om een vereiste taak in Azure Active Directory uit te voeren. [Bekijk de verschillende rollen die beschikbaar zijn](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) en kies de juiste om uw behoeften voor elke persona voor deze toepassing op te lossen. Sommige rollen moeten mogelijk tijdelijk worden toegepast en verwijderd nadat de implementatie is voltooid.

| Persona's| Rollen| Azure AD-rol  |
| - | -| -|
| Helpdeskbeheerder| Ondersteuning voor Tier 1| Geen |
| Identiteitsbeheerder| Configureren en debuggen wanneer problemen van invloed zijn op Azure AD| Globale beheerder |
| Toepassingsbeheerder| Gebruikersattest in toepassing, configuratie op gebruikers met machtigingen| Geen |
| Infrastructuurbeheerders| Cert rollover eigenaar| Globale beheerder |
| Bedrijfseigenaar/stakeholder| Gebruikersattest in toepassing, configuratie op gebruikers met machtigingen| Geen |

U [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) gebruiken om uw rollen te beheren om gebruikers met directorymachtigingen extra controle, controle en toegangscontrole te bieden.

### <a name="troubleshoot-access-panel-issues"></a>Problemen met het toegangspaneel oplossen

Maak handleidingen voor probleemoplossing voor uw ondersteuningsorganisatie met veelvoorkomende scenario's, die wijzen op Microsoft-documentatie in hun resoluties. U hulplijnen maken die ondersteuning opsplitsen in de lagen die door uw organisatie worden gebruikt.

Zie deze handleidingen voor het oplossen van problemen voor referentie:

[Toepassingen die niet worden weergegeven](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Onverwachte toepassingen die worden weergegeven](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[Gebruiker kan zich niet aanmelden bij het Toegangspaneel](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problemen met selfservice-toegang tot toepassingen](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problemen met de browserextensie](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Volgende stappen

[Een implementatie van Azure Multi-Factor Authentication plannen](https://aka.ms/deploymentplans/mfa)
