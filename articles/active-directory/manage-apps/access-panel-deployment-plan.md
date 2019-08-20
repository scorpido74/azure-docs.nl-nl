---
title: Een Azure Active Directory implementatie van het toegangs paneel plannen
description: Richt lijnen voor het implementeren van Azure AD Access Panel-functionaliteit
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
ms.date: 08/16/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 448af7075297c2b18df1eceaeaac34d5da762489
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576522"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Een Azure Active Directory implementatie van het toegangs paneel plannen

Het Azure Active Directory toegangs venster is een webgebaseerde Portal waarmee u de ondersteunings kosten kunt verlagen, de productiviteit en de beveiliging kunt verhogen en de gebruikers frustraties vermindert. Het systeem bevat gedetailleerde rapportages die bijhouden wanneer gebruikers toegang tot het systeem hebben, en waarschuwt beheerders voor misbruik of misbruik.

In het Azure Active Directory toegangs venster kunnen gebruikers het volgende doen:

* Ontdek en toegang tot alle Azure Active Directory verbonden resources van hun bedrijf, zoals toepassingen.
* Vraag toegang aan tot nieuwe apps en groepen of beheer de toegang tot deze resources voor anderen.
* Beheer selfservice voor het opnieuw instellen van wacht woorden en instellingen voor multi-factor Authentication.
* Hun apparaten beheren.

Daarnaast kunnen beheerders het volgende beheren:

* Servicevoorwaarden
* Organisaties
* Toegangsbeoordelingen


## <a name="benefits-of-azure-active-directory-access-panel-integration"></a>Voor delen van de integratie van Azure Active Directory-toegangs paneel

Het toegangs venster van het Microsoft Azure Active Directory (Azure AD) biedt bedrijven op de volgende manieren:

**Biedt intuïtieve gebruikers ervaring**: Het inschakelen van het toegangs venster biedt gebruikers de mogelijkheid om één start paneel te hebben voor al hun Azure Single-Sign op verbonden toepassingen. Als mogelijkheden zoals groeps beheer en self-service voor het opnieuw instellen van wacht woorden worden toegevoegd, hebben gebruikers nog steeds een uniforme Portal nodig om deze instellingen te vinden. De intuïtieve ervaring stelt gebruikers in staat om sneller te werken en productiever te zijn, terwijl ze hun frustratie verminderen.

**Verhoogt de productiviteit**: Voor alle gebruikers toepassingen in het toegangs venster is eenmalige aanmelding (SSO) ingeschakeld. Het inschakelen van eenmalige aanmelding in zakelijke toepassingen en Office 365 biedt een superieure aanmeldings ervaring voor bestaande gebruikers, het verminderen of elimineren van aanvullende aanmeldings prompts. Het toegangs venster maakt selfservice en dynamische lidmaatschap mogelijk en verbetert de algehele beveiliging van uw identiteits systeem door ervoor te zorgen dat de juiste personen de toegang tot de toepassingen beheren. Het toegangs venster fungeert als een samenhangende landings pagina voor een gebruiker om snel bronnen te vinden en werk taken voort te zetten.

**Beheert kosten**: Het inschakelen van het toegangs venster met Azure Active Directory kan Divestment van on-premises infra structuur toestaan. Het vermindert de ondersteunings kosten door gebruikers in staat te stellen een consistente portal te gebruiken om al hun apps te vinden, toegang tot resources te vragen en hun account te beheren.

**Verhoogt de flexibiliteit en beveiliging**: Met het toegangs venster kunt u toegang krijgen tot de beveiliging en flexibiliteit die een Cloud platform biedt. Beheerders kunnen instellingen eenvoudig wijzigen in toepassingen en bronnen en voldoen aan nieuwe beveiligings vereisten zonder dat dit van invloed is op gebruikers.

**Zorgt voor robuuste controle en bijhouden**van het gebruik: Door de controle en het bijhouden van het gebruik voor alle functies van eind gebruikers kunt u zien wanneer gebruikers hun resources gebruiken en ervoor zorgen dat u de beveiliging kunt beoordelen.

### <a name="licensing-considerations"></a>Licentie overwegingen

Het toegangs venster is gratis en er zijn geen licenties nodig voor het gebruik van een basis versie van alle gebruikers. Voor het aantal objecten in uw directory en de functies die u wilt implementeren, zijn mogelijk extra licenties nodig. Veelvoorkomende scenario's voor Azure AD bevatten de volgende beveiligings functies waarvoor licentie vereisten gelden.

* [Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Groepslid maatschap](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Wachtwoord vereiste voor selfservice](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Identiteits beveiliging](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Raadpleeg de [volledige licentie handleiding voor Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Vereisten voor de implementatie van het Azure AD-toegangs venster

De volgende vereisten moeten vóór het begin van dit project zijn voltooid.

* [Application SSO-integratie](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Azure AD-gebruikers-en-groeps infrastructuur](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>De implementatie van het Azure AD-toegangs venster plannen

De volgende tabel geeft een overzicht van de belangrijkste use cases voor een implementatie van het toegangs paneel:

| Onderwerp| Description |
| - | - |
| Toegang| De portal voor het toegangs paneel is toegankelijk vanaf zakelijke en persoonlijke apparaten in het bedrijfs netwerk. |
|Toegang | De portal voor het toegangs paneel is toegankelijk voor zakelijke apparaten buiten het bedrijfs netwerk. |
| Controleren| Gebruiks gegevens worden minstens elke 29 dagen in bedrijfs systemen gedownload. |
| Beheer| De levens cyclus van gebruikers toewijzingen aan Azure AD Connected Applications and Groups wordt gedefinieerd en gecontroleerd. |
| Beveiliging| Toegang tot bronnen wordt geregeld via gebruikers-en groeps toewijzingen. Alleen geautoriseerde gebruikers kunnen toegang tot resources beheren. |
| Prestaties| De tijd lijnen voor het door geven van toegangs toewijzingen worden gedocumenteerd en gecontroleerd. |
| Gebruikers ervaring| Gebruikers zijn op de hoogte van de mogelijkheden van het toegangs venster en hoe ze deze kunnen gebruiken.|
| Gebruikers ervaring| Gebruikers kunnen zelf hun toegang tot toepassingen en groepen beheren.|
| Gebruikers ervaring| Gebruikers kunnen hun accounts beheren. |
| Gebruikers ervaring| Gebruikers zijn op de hoogte van browser compatibiliteit. |
| Ondersteuning| Gebruikers kunnen ondersteuning voor problemen met het toegangs paneel vinden. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Aanbevolen procedures voor het implementeren van het Azure AD-toegangs venster

De functionaliteit van het toegangs venster kan geleidelijk worden ingeschakeld. We raden u aan de volgende volg orde van de implementatie te implementeren:

1. Mijn apps
   * Start programma voor apps
   * Self-service app-beheer
   * Microsoft Office 365-integratie

1. Detectie van self-service app
   * Self-service voor wachtwoord opnieuw instellen
   * Multi-factor Authentication-instellingen
   * Apparaatbeheer
   * Gebruiksvoorwaarden
   * Organisaties beheren

1. Mijn groepen
   * Groepsbeheer via selfservice
1. Toegangsbeoordelingen
   * Toegangs beoordelings beheer

Vanaf mijn apps introduceert gebruikers de portal als een gemeen schappelijke locatie voor toegang tot resources. Het toevoegen van self-service toepassings detectie bouwt voort op de ervaring mijn apps. Mijn groepen en toegangs beoordelingen zijn gebaseerd op de self-service mogelijkheden.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Configuraties voor het Azure AD-toegangs venster plannen

De volgende tabel bevat een overzicht van een aantal belang rijke configuraties van het toegangs venster en de typische waarden die u kunt gebruiken:

| Configuratie| Typische waarden |
| - | - |
| Test groep (en) bepalen| Identificeer de Azure AD-beveiligings groep die moet worden gebruikt en zorg ervoor dat alle leden van de test groep deel uitmaken. |
| Bepaal welke groep of groepen moeten worden ingeschakeld voor productie.| Bepaal welke Azure AD-beveiligings groep (en) of AD-groepen die zijn gesynchroniseerd met Azure AD, moeten worden gebruikt. Zorg ervoor dat alle leden van de pilot deel uitmaken van de groep. |
| Gebruikers toestaan om eenmalige aanmelding te gebruiken voor de typen toepassingen| Federatieve SSO, OAuth, wacht woord-SSO, app proxy |
| Gebruikers toestaan self-service voor wachtwoord herstel te gebruiken| Ja |
| Gebruikers toestaan multi-factor Authentication te gebruiken| Ja |
| Gebruikers toestaan om het groeps beheer van self-service te gebruiken voor de typen groepen| Beveiligings groepen, O365-groepen |
| Gebruikers toestaan self-service app-beheer te gebruiken| Ja |
| Gebruikers toestaan om toegangs beoordelingen te gebruiken| Ja |

### <a name="plan-consent-strategy"></a>Strategie voor het plannen van toestemming

Gebruikers of beheerders moeten toestemming geven voor de gebruiks voorwaarden en het privacybeleid van de toepassing. Indien mogelijk uw bedrijfs regels, adviseren we om toestemming van de beheerder te gebruiken, waarmee gebruikers een betere ervaring kunnen krijgen.

Als u toestemming van de beheerder wilt gebruiken, moet u een globale beheerder van de Tenant zijn en moeten de toepassingen:

* Geregistreerd in uw Tenant of

* Geregistreerd in een andere Azure AD-Tenant en die eerder door ten minste één eind gebruiker is gezonden.

Zie voor meer informatie [configureren hoe eind gebruikers toestemming geven voor een toepassing in azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden benaderen

Wanneer technologie projecten mislukken, worden ze doorgaans door niet-overeenkomende verwachtingen voor impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet [u ervoor zorgen dat u de juiste belanghebbenden gebruikt](../fundamentals/active-directory-deployment-plans.md) en dat de rol van belanghebbenden in het project goed zijn begrepen.

### <a name="plan-communications"></a>Communicatie plannen

Communicatie is van cruciaal belang voor het slagen van een nieuwe service. Laat uw gebruikers proactief weten hoe en wanneer hun ervaring wordt gewijzigd en hoe u zo nodig ondersteuning krijgt.

Hoewel het toegangs paneel doorgaans geen gebruikers problemen maakt, is het belang rijk om voor te bereiden. Maak hand leidingen en een lijst met alle resources voor uw ondersteunings personeel voordat u het start.

#### <a name="communications-templates"></a>Communicatie sjablonen

Micro soft biedt [aanpas bare sjablonen voor e-mail berichten en andere communicatie](https://aka.ms/APTemplates) voor het toegangs venster. U kunt deze assets aanpassen voor gebruik in andere communicatie kanalen die geschikt zijn voor uw bedrijfs cultuur.

## <a name="plan-your-sso-configuration"></a>Uw SSO-configuratie plannen

Wanneer een gebruiker zich aanmeldt bij een toepassing, gaan ze een verificatie proces en moeten ze bewijzen wie ze zijn. Zonder eenmalige aanmelding, een wacht woord dat is opgeslagen bij de toepassing en de gebruiker moet het wacht woord kennen. Wanneer gebruikers referenties voor eenmalige aanmelding (SSO) worden door gegeven aan de toepassing, moeten ze geen wacht woorden voor elke toepassing opnieuw invoeren.

Voor het starten van toepassingen in mijn apps moet eenmalige aanmelding (SSO) zijn ingeschakeld voor de toepassingen.

Azure AD biedt ondersteuning voor drie verschillende manieren om eenmalige aanmelding in te scha kelen [voor toepassingen](what-is-single-sign-on.md):

* **Federatieve eenmalige aanmelding** 
    * Hiermee kan een toepassing omleiden naar Azure AD voor gebruikers verificatie in plaats van een wacht woord op te vragen. 
    * Wordt ondersteund voor toepassingen die gebruikmaken van protocollen zoals SAML 2,0, WS-Federation of OpenID Connect Connect, en is de uitgebreide modus van eenmalige aanmelding.

* **Eenmalige aanmelding op basis van wacht woorden** 
    * Hiermee schakelt u beveiligde wachtwoord opslag en herhaling met een webbrowser of mobiele app. 
    * Maakt gebruik van het bestaande aanmeldings proces van de toepassing, maar een beheerder kan de wacht woorden beheren. De gebruiker is niet verplicht het wacht woord te kennen.

* **Bestaande eenmalige aanmelding** 
    * Hiermee kan Azure AD gebruikmaken van alle bestaande eenmalige aanmelding die voor de toepassing is geconfigureerd.
    * Hiermee kunnen deze toepassingen worden gekoppeld aan de portals van het Office 365-of Azure AD-toegangs venster. 
    * Biedt extra rapportage in azure AD wanneer de toepassingen daar worden geïntroduceerd. 
    * Omvat het gebruik van Azure-toepassing-proxy en de gekoppelde modus voor eenmalige aanmelding.

Meer informatie over het configureren van de SSO-modus van een toepassing: [Eenmalige aanmelding bij toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Voor de beste ervaring met de pagina mijn apps raden we u aan om te beginnen met de integratie van Cloud toepassingen die beschikbaar zijn voor federatieve SSO. Met federatieve SSO kunnen gebruikers een consistente ervaring met één klik hebben in hun app-lanceer oppervlakken en zijn ze in het algemeen beter te verstevigen in configuratie beheer.

Gebruik federatieve SSO met Azure AD (OpenID Connect Connect/SAML) wanneer een toepassing dit ondersteunt, in plaats van SSO op basis van wacht woord en ADFS.

Zie voor meer informatie over het implementeren en configureren van SaaS-toepassingen het [SaaS SSO-implementatie plan](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>De implementatie van de browser uitbreiding van mijn apps plannen

Als SSO-toepassingen op basis van wacht woorden beschikbaar worden gesteld aan eind gebruikers, moeten ze de beveiligde aanmeldings extensie mijn apps installeren om zich aan te melden. De uitbrei ding voert een script uit dat het wacht woord verzendt naar het aanmeld formulier van de toepassing. Gebruikers wordt gevraagd om de uitbrei ding te installeren wanneer ze voor het eerst de SSO-toepassing op basis van een wacht woord starten. Meer informatie over de uitbrei ding vindt u in de documentatie over [het installeren van de browser uitbreiding van het toegangs venster](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Als u SSO-toepassingen op basis van wacht woorden moet integreren, moet u een mechanisme definiëren om de uitbrei ding op schaal te implementeren met [ondersteunde browsers](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Een aantal opties:

* [groepsbeleid voor Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [System Center Configuration Manager (SCCM) voor Internet Explorer](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)

* [Door de gebruiker gestuurde down load en configuratie voor Chrome, Firefox, micro soft Edge of IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Meer informatie: [Het configureren van een eenmalige aanmelding met een wacht woord](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Gebruikers die geen SSO-toepassing op basis van wacht woorden gebruiken, profiteren ook van de uitbrei ding. Deze voor delen zijn onder andere de mogelijkheid om apps te starten vanaf de zoek balk, toegang te verkrijgen tot recent gebruikte toepassingen en een koppeling naar de pagina mijn apps te maken.

Dit is wat de gebruiker te zien krijgt wanneer een SSO-toepassing op basis van een wacht woord voor de eerste keer wordt gestart:

![Scherm afbeelding van browser extensie van mijn apps ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Mobile Access plannen

Een browser die is beveiligd met intune-beleid (micro soft Edge of Intune Managed Browser) is vereist voor mobiele gebruikers die SSO-toepassingen op basis van wacht woorden starten. Met een met beleid beveiligde browser kan de overdracht van het wacht woord dat voor de toepassing is opgeslagen, worden overgedragen. Micro soft Edge of de Managed browser biedt een aantal functies voor het beveiligen van webgegevens. U kunt ook micro soft Edge gebruiken voor zakelijke scenario's op iOS-en Android-apparaten. Micro soft Edge ondersteunt dezelfde beheer scenario's als de Intune Managed Browser en verbetert de ervaring van de eind gebruiker. Meer informatie: [Webtoegang beheren met een door Microsoft Intune-beleid beveiligde browser](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>Uw implementatie van mijn apps plannen

De basis van het toegangs venster is het start programma voor toepassingen van [https://myapps.microsoft.com](https://myapps.microsoft.com/)mijn apps, waarmee gebruikers toegang hebben. Met de pagina's van mijn apps hebben gebruikers één plek om hun werk te starten en naar hun benodigde toepassingen te gaan. Hier vinden gebruikers een lijst van alle toepassingen waarmee ze toegang hebben tot eenmalige aanmelding. 

![Een scherm opname van het deel venster apps](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Dezelfde toepassingen worden weer gegeven in het start programma voor Office 365-apps wanneer gebruikers de Office 365-Portal gebruiken.

Plan de volg orde waarin u toepassingen gaat toevoegen aan het start programma voor mijn apps en of u deze geleidelijk of allemaal tegelijk wilt samen vouwen. U doet dit door een toepassings voorraad te maken met het type verificatie en eventuele bestaande SSO-integratie (single sign-on) voor elke toepassing.

#### <a name="add-applications-to-the-my-apps-panel"></a>Toepassingen toevoegen aan het deel venster mijn apps

Elke toepassing met Azure AD SSO kan worden toegevoegd aan het start programma voor apps. Andere toepassingen worden toegevoegd met behulp van de optie gekoppelde SSO. U kunt een toepassings tegel configureren die wordt gekoppeld aan de URL van uw bestaande webtoepassing. Met gekoppelde SSO kunt u beginnen met het omleiden van gebruikers naar de portal mijn apps zonder dat alle toepassingen moeten worden gemigreerd naar Azure Active Directory SSO. U kunt geleidelijk overschakelen naar door Azure AD SSO geconfigureerde toepassingen zonder de gebruikers ervaring te onderbreken.

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Plannen of mijn apps of een bestaande portal moeten worden gebruikt

Uw gebruikers hebben mogelijk al een andere toepassing of portal dan mijn apps. Als dit het geval is, moet u beslissen of u beide portals wilt ondersteunen of dat u er slechts één wilt gebruiken.

Als er al een bestaande portal wordt gebruikt als uitgangs punt voor gebruikers, kunt u de functionaliteit van mijn apps integreren met behulp van ' Url's voor gebruikers toegang '. De functie Url's van gebruikers toegang is een directe koppeling naar de toepassingen die beschikbaar zijn in de portal mijn apps. Deze Url's kunnen worden inge sloten in een bestaande website. Wanneer een gebruiker op de koppeling klikt, wordt de toepassing gestart vanuit de portal mijn apps.

U vindt de eigenschap voor de gebruikers toegangs-URL in het gedeelte Eigenschappen van de toepassing in de Azure Portal.

![Een scherm opname van het deel venster apps](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>De detectie en toegang van self-service toepassingen plannen

Zodra een kernset van toepassingen op de pagina mijn apps van een gebruiker is geïmplementeerd, wordt aangeraden om selfservice functies voor het beheer van apps in te scha kelen. Met de functie voor het detecteren van self-service apps kunt u:
* Gebruikers zoeken naar nieuwe apps die kunnen worden toegevoegd aan hun apps. 
* Gebruikers om optionele apps toe te voegen die u mogelijk niet weet tijdens de installatie.

Goedkeurings werk stromen zijn beschikbaar voor expliciete goed keuring voor toegang tot toepassingen. Gebruikers die goed keurders zijn, ontvangen meldingen binnen de portal mijn apps wanneer er een aanvraag voor toegang tot de toepassing in behandeling is.

## <a name="plan-self-service-group-membership"></a>Self-service groepslid maatschap plannen 

U kunt gebruikers in staat stellen hun eigen beveiligings groepen of Office 365-groepen te maken en beheren in Azure Active Directory (Azure AD). De eigenaar van de groep kan lidmaatschaps aanvragen goed keuren of weigeren en het beheer van groepslid maatschap delegeren. Self-service groeps beheer functies zijn niet beschikbaar voor beveiligings groepen met e-mail functionaliteit of distributie lijsten.

Als u wilt plannen voor een self-service groepslid maatschap, bepaalt u of alle gebruikers in uw organisatie groepen kunnen maken en beheren, of alleen een subset van gebruikers. Als u een subset van gebruikers hebt, moet u een groep instellen waaraan deze personen worden toegevoegd. Zie [self-service groeps beheer in azure Active Directory instellen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) voor meer informatie over het inschakelen van deze scenario's.

## <a name="plan-reporting-and-auditing"></a>Rapportage en controle plannen

Azure AD biedt [rapporten die technische en zakelijke inzichten bieden](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). Werk samen met de eigen aren van zakelijke en technische toepassingen om te bepalen of u deze rapporten regel matig wilt gebruiken. De onderstaande tabel bevat enkele voor beelden van typische rapportage scenario's.

|   | beheer risico's.| Productiviteit verhogen| Governance en naleving |
|  - |- | - | - |
| Rapporttypen|  Toepassings machtigingen en-gebruik.| Activiteit voor het inrichten van accounts| Controleren wie toegang heeft tot de toepassingen |
| Mogelijke acties| Toegang controleren; machtigingen intrekken| Eventuele inrichtings fouten herstellen| Toegang intrekken |

Azure AD houdt de meeste controle gegevens gedurende 30 dagen. De gegevens zijn beschikbaar via de Azure-beheer portal of-API die u kunt downloaden naar uw analyse systemen.

#### <a name="auditing"></a>Controleren

Audit logboeken voor toegang tot toepassingen zijn 30 dagen beschikbaar. Als de beveiligings controle binnen uw onderneming een langere retentie vereist, moeten de logboeken worden geëxporteerd naar een SIEM-hulp programma (Security Information Event and Management) zoals Splunk of ArcSight.

Voor controle-, rapportage-en nood herstel back-ups documenteert u de vereiste Download frequentie, het doel systeem en wie verantwoordelijk is voor het beheer van elke back-up. U hebt mogelijk geen afzonderlijke back-ups van controles en rapporten nodig. Uw back-up voor herstel na nood gevallen moet een afzonderlijke entiteit zijn.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Toepassingen implementeren in het deel venster mijn apps van gebruikers

Nadat een toepassing is geconfigureerd voor SSO, krijgen groepen toegang toegewezen. Gebruikers in de toegewezen groepen hebben toegang en zien de toepassing in hun apps en het start programma voor de Office 365-app

Zie [gebruikers en groepen toewijzen aan een toepassing in Active Directory](methods-for-assigning-users-and-groups.md).

Als tijdens het testen of implementeren u de groepen wilt toevoegen, maar nog niet toestaat dat de toepassingen in mijn apps worden weer gegeven, raadpleegt u [een toepassing verbergen van gebruikers ervaring in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Microsoft Office 365-toepassingen implementeren in mijn apps

Voor Office 365-toepassingen ontvangen gebruikers een exemplaar van Office op basis van licenties die aan hen zijn toegewezen. Een vereiste voor toegang tot Office-toepassingen is het toewijzen van gebruikers aan de juiste licenties die zijn gekoppeld aan de Office-toepassingen. Wanneer u een gebruiker toewijst aan de licentie, worden automatisch de toepassingen weer geven die zijn gekoppeld aan de licentie op de pagina mijn apps en in het start programma voor de O365-app.

Als u een set Office-toepassingen wilt verbergen voor gebruikers, is er een optie om apps te verbergen in de portal mijn apps, terwijl u toch toegang hebt tot de O365-Portal. Deze instellingen vindt u in het gedeelte gebruikers instellingen van de toepassing. Meer informatie: [Verberg een toepassing van de gebruikers ervaring in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Een scherm afbeelding van het configureren van het verbergen van toepassingen](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Self-service mogelijkheden voor toepassingen implementeren

Met toegang door selfservice toepassingen kunnen gebruikers zelf toegang krijgen tot toepassingen en deze aanvragen. Gebruikers krijgen de vrijheid om toegang te krijgen tot de apps die ze nodig hebben zonder dat ze elke keer een IT-groep hoeven te door lopen om toegang aan te vragen. Wanneer een gebruiker toegang vraagt en automatisch of hand matig wordt goedgekeurd door een eigenaar van een app, worden ze toegevoegd aan een groep aan de back-end. Rapportage is ingeschakeld voor wie toegang heeft aangevraagd, goedgekeurd of verwijderd, en biedt u de controle over het beheer van de toegewezen rollen.

U kunt de goed keuring van aanvragen voor toegang tot een toepassing overdragen aan zakelijke goed keurders. De zakelijke goed keurder kan het wacht woord voor app-toegang direct instellen vanaf de pagina mijn apps van de zakelijke goed keurder.

Meer informatie: [Toegang tot Self-service toepassingen gebruiken](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![Een scherm opname van het configureren van self-service toepassings beheer](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Uw implementatie valideren

Zorg ervoor dat de implementatie van het toegangs venster grondig wordt getest en dat er een rollback-plan aanwezig is.

De volgende tests moeten worden uitgevoerd met apparaten in bedrijfs eigendom en persoonlijke apparaten. Deze test cases moeten ook overeenkomen met uw zakelijke gebruiks voorbeelden. Hieronder volgen enkele gevallen op basis van de zakelijke vereisten voor voor beeld in dit document en voor typische technische scenario's. Voeg anderen specifiek toe aan uw behoeften.

#### <a name="application-sso-access-test-case-examples"></a>Case-voor beelden van Application SSO Access testen:


| Businesscase| Verwacht resultaat |
| - | -|
| Gebruiker meldt zich aan bij de portal mijn apps| De gebruiker kan zich aanmelden en hun toepassingen bekijken |
| Gebruiker start een federatieve SSO-toepassing| De gebruiker wordt automatisch aangemeld bij de toepassing |
| De gebruiker start een SSO-toepassing voor wacht woord voor de eerste keer| De gebruiker moet de uitbrei ding mijn apps installeren |
| Gebruiker start een SSO-toepassing voor een wacht woord een volgende keer| De gebruiker wordt automatisch aangemeld bij de toepassing |
| Gebruiker start een app vanuit de O365-Portal| De gebruiker wordt automatisch aangemeld bij de toepassing |
| Gebruiker start een app vanuit de Managed Browser| De gebruiker wordt automatisch aangemeld bij de toepassing |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Case-voor beelden van de self-service voor toepassingen testen


| Businesscase| Verwacht resultaat |
| - | - |
| Gebruiker kan lidmaatschap van de toepassing beheren| Gebruiker kan leden toevoegen/verwijderen die toegang hebben tot de app |
| Gebruiker kan de toepassing bewerken| Gebruiker kan de beschrijving en referenties voor wacht woord-SSO-toepassingen bewerken |

### <a name="rollback-steps"></a>Stappen voor ongedaan maken

Het is belang rijk om te plannen wat u moet doen voor het geval uw implementatie niet wordt uitgevoerd zoals gepland. Als de SSO-configuratie mislukt tijdens de implementatie, moet u weten hoe u [SSO-problemen oplost](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) en de gevolgen voor uw gebruikers vermindert. In uitzonderlijke omstandigheden moet u mogelijk [SSO terugdraaien](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>Uw implementatie beheren

Micro soft raadt u aan om de minst privileged Role te gebruiken om een vereiste taak uit te voeren binnen Azure Active Directory. Micro soft raadt aan om [de verschillende beschik bare rollen te bekijken](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) en de juiste te kiezen om uw behoeften voor elke persoon voor deze toepassing op te lossen. Sommige rollen moeten mogelijk tijdelijk worden toegepast en worden verwijderd nadat de implementatie is voltooid.

| Persona's| Rollen| Azure AD-rol  |
| - | -| -|
| Help Desk-beheerder| Ondersteuning voor laag 1| Geen |
| Identiteits beheerder| Configureren en fouten opsporen wanneer de problemen invloed hebben op Azure AD| Globale beheerder |
| Toepassings beheerder| Gebruikers verklaring in toepassing, configuratie voor gebruikers met machtigingen| Geen |
| Infrastructuur beheerders| Eigenaar certificaat rollover| Globale beheerder |
| Bedrijfs eigenaar/belanghebbende| Gebruikers verklaring in toepassing, configuratie voor gebruikers met machtigingen| Geen |

Het is raadzaam om [privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) te gebruiken voor het beheren van uw rollen om extra controle, controle en toegangs beoordeling te bieden voor gebruikers met mapmachtigingen.

### <a name="troubleshoot-access-panel-issues"></a>Problemen met het toegangs paneel oplossen

Maak richt lijnen voor probleem oplossing voor uw ondersteunings organisatie met algemene scenario's en wijs naar micro soft-documentatie over hun oplossingen. Het is raadzaam om hand leidingen te maken die ondersteuning bieden voor de lagen die door uw organisatie worden gebruikt.

Zie de onderstaande hand leidingen voor probleem oplossing voor naslag informatie:

[Toepassingen die niet worden weer gegeven](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Onverwachte toepassingen die worden weer gegeven](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[Gebruiker kan zich niet aanmelden bij het toegangs venster](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problemen met het gebruik van self-service toepassings toegang](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problemen met de browser uitbreiding](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Volgende stappen

[Een implementatie van Azure Active Directory multi-factor Authentication plannen](https://aka.ms/deploymentplans/mfa)
