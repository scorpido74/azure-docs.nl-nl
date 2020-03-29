---
title: Een azure Active Directory-implementatie met één aanmelding plannen
description: Gids om u te helpen bij het plannen, implementeren en beheren van SSO in uw organisatie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92496fa572c5c1cae4588f82ac61c18de3024045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512824"
---
# <a name="plan-a-single-sign-on-deployment"></a>Een implementatie van eenmalige aanmelding plannen

Eenmalige aanmelding (SSO) betekent toegang tot alle toepassingen en bronnen die een gebruiker nodig heeft door zich slechts eenmaal aan te melden met één gebruikersaccount. Met SSO hebben gebruikers toegang tot alle benodigde toepassingen zonder dat ze een tweede keer hoeven te verifiëren.

## <a name="benefits-of-sso"></a>Voordelen van SSO

Eenmalige aanmelding (SSO) voegt beveiliging en gemak toe wanneer gebruikers zich aanmelden bij toepassingen in Azure Active Directory (Azure AD). 

Veel organisaties vertrouwen op software as a service (SaaS)-toepassingen, zoals Office 365, Box en Salesforce, voor de productiviteit van eindgebruikers. In het verleden moesten IT-medewerkers gebruikersaccounts in elke SaaS-toepassing individueel maken en bijwerken, en gebruikers moesten voor elk afzonderlijk een wachtwoord onthouden.

De Azure Marketplace heeft meer dan 3000 toepassingen met vooraf geïntegreerde SSO-verbindingen, waardoor het eenvoudig is om ze in uw tenant te integreren.

## <a name="licensing"></a>Licentieverlening

- **Azure AD-licenties** - SSO voor vooraf geïntegreerde SaaS-toepassingen is gratis. Het aantal objecten in uw directory en de functies die u wilt implementeren, vereist echter mogelijk extra licenties. Zie [Azure Active Directory-prijzen](https://azure.microsoft.com/pricing/details/active-directory/)voor een volledige lijst met licentievereisten.
- **Toepassingslicenties** - U hebt de juiste licenties voor uw SaaS-toepassingen nodig om aan uw bedrijfsbehoeften te voldoen. Werk samen met de eigenaar van de toepassing om te bepalen of de gebruikers die aan de toepassing zijn toegewezen, over de juiste licenties beschikken voor hun rollen binnen de toepassing. Als Azure AD de automatische inrichting beheert op basis van rollen, moeten de rollen die in Azure AD zijn toegewezen, worden afgestemd op het aantal licenties dat in de toepassing is. Oneigenlijk aantal licenties in de toepassing kan leiden tot fouten tijdens het inrichten/bijwerken van een gebruiker.

## <a name="plan-your-sso-team"></a>Plan uw SSO-team

- **Schakel de juiste belanghebbenden in** - Wanneer technologieprojecten mislukken, is dit meestal te wijten aan de onjuiste verwachtingen over impact, resultaten en verantwoordelijkheden. Om deze valkuilen te vermijden, [moet u ervoor zorgen dat u de juiste belanghebbenden indienst teert](https://aka.ms/deploymentplans) en dat belanghebbenden hun rol begrijpen.
- **Plan communicatie** - Communicatie is cruciaal voor het succes van elke nieuwe dienst. Communiceer proactief met uw gebruikers over hoe hun ervaring zal veranderen, wanneer deze zal veranderen en hoe ze ondersteuning kunnen krijgen als ze problemen ervaren. Bekijk de opties voor [hoe eindgebruikers toegang krijgen tot hun SSO-toepassingen](end-user-experiences.md)en maak uw communicatie zo dat ze overeenkomen met uw selectie. 

## <a name="plan-your-sso-protocol"></a>Uw SSO-protocol plannen

Een SSO-implementatie op basis van federatieprotocollen verbetert de beveiliging, betrouwbaarheid en ervaringen van eindgebruikers en is eenvoudiger te implementeren. Veel toepassingen zijn vooraf geïntegreerd in Azure AD met [stapsgewijze handleidingen beschikbaar.](../saas-apps/tutorial-list.md) U ze vinden op onze [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/) Gedetailleerde informatie over elke SSO-methode is te vinden in het artikel [Single sign-on to applications in Azure Active Directory](what-is-single-sign-on.md).

Er zijn twee primaire manieren waarop u uw gebruikers inschakelen om zich eenmalig aan te melden bij uw apps:

- **Met gefedereerd enkel aanmelding** Azure AD verifieert de gebruiker naar de toepassing met behulp van hun Azure AD-account. Deze methode wordt ondersteund voor toepassingen die protocollen zoals SAML 2.0, WS-Federation of OpenID Connect ondersteunen en is de rijkste modus van eenmalige aanmelding. We raden u aan Federated SSO te gebruiken met Azure AD wanneer een toepassing dit ondersteunt, in plaats van op wachtwoorden gebaseerde SSO en ADFS.

- **Met wachtwoord-gebaseerde single sign-on** gebruikers aanmelden bij de toepassing met een gebruikersnaam en wachtwoord de eerste keer, ze toegang tot het. Na de eerste aanmelding levert Azure AD de gebruikersnaam en het wachtwoord aan de toepassing. Met een enkele aanmelding op basis van wachtwoorden met wachtwoord u veilige opslag en herhaling van het wachtwoord van toepassingen mogelijk maken met behulp van een webbrowserextensie of mobiele app. Deze optie maakt gebruik van het bestaande aanmeldingsproces dat door de toepassing wordt geleverd, stelt een beheerder in staat om de wachtwoorden te beheren en vereist niet dat de gebruiker het wachtwoord kent.

### <a name="considerations-for-federation-based-sso"></a>Overwegingen voor federatiegebaseerde SSO

- **OpenID Connect en OAuth** gebruiken - Als de toepassing waarmee u verbinding maakt deze ondersteunt, gebruikt u de OIDC/OAuth 2.0-methode om uw SSO in die toepassing in te schakelen. Deze methode vereist minder configuratie en maakt een rijkere gebruikerservaring mogelijk. Zie [OAuth 2.0,](../develop/v2-oauth2-auth-code-flow.md) [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)en [azure Active Directory-ontwikkelaarshandleiding](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)voor meer informatie.
- **Endpoint-configuraties voor SAML-gebaseerde SSO** - Als u SAML gebruikt, hebben uw ontwikkelaars specifieke informatie nodig voordat ze de toepassing configureren. Zie [De basisSAML-configuratie bewerken](configure-single-sign-on-non-gallery-applications.md)voor meer informatie.
- **Certificaatbeheer voor SAML-gebaseerde SSO** - Wanneer u Federated SSO inschakelt voor uw toepassing, maakt Azure AD een certificaat dat standaard drie jaar geldig is. U indien nodig de vervaldatum voor dat certificaat aanpassen. Zorg ervoor dat u processen hebt om certificaten te vernieuwen voordat ze verlopen. Zie [Azure AD-beheercertificaten voor](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)meer informatie .

### <a name="considerations-for-password-based-sso"></a>Overwegingen voor op wachtwoorden gebaseerde SSO

Voor het gebruik van Azure AD voor sso op basis van wachtwoorden moet een browserextensie worden geïmplementeerd die de referenties veilig ophaalt en de aanmeldingsformulieren invult. Definieer een mechanisme om de extensie op schaal te implementeren met [ondersteunde browsers.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) Een aantal opties:

- [Groepsbeleid voor Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Configuratiebeheer voor Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Door de gebruiker gestuurde download en configuratie voor Chrome, Firefox, Microsoft Edge of IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Zie [Wachtwoord eenmalig aanmelden configureren voor](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)meer informatie.

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Metagegevens van inlogformulieren vastleggen voor toepassingen die zich niet in de galerie bevinden

Microsoft ondersteunt het vastleggen van metagegevens in een webtoepassing voor het openen van wachtwoorden (het vastleggen van de gebruikersnaam- en wachtwoordvelden). Navigeer naar de inlog-URL tijdens het configureren van de toepassing om de metagegevens van formulieren vast te leggen. Vraag de eigenaar van de toepassing naar de exacte login-URL. Deze informatie wordt gebruikt tijdens het aanmeldingsproces en geeft Azure AD-referenties in kaart aan de toepassing tijdens het aanmelden.

Zie [Wat is toepassingstoegang en SSO met Azure AD?](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Aanwijzingen dat metagegevens in formulieren moeten worden heroverd

Wanneer toepassingen hun HTML-indeling wijzigen, moet u mogelijk de metagegevens opnieuw vastleggen om de wijzigingen aan te passen. Veelvoorkomende symptomen die aangeven dat de HTML-indeling is gewijzigd, zijn:

- Gebruikers melden dat het klikken op de toepassing "vastloopt" in de login pagina
- Gebruikers melden dat de gebruikersnaam of het wachtwoord niet is ingevuld

#### <a name="shared-accounts"></a>Gedeelde accounts

Vanuit het aanmeldingsperspectief verschillen toepassingen met gedeelde accounts niet van een galerijtoepassing die wachtwoord SSO voor individuele gebruikers gebruikt. Er zijn echter enkele aanvullende stappen vereist bij het plannen en configureren van een toepassing die bedoeld is om gedeelde accounts te gebruiken:

1. Werk samen met zakelijke gebruikers van toepassingen om het volgende te documenteren:
   1. Set gebruikers in de organisatie die de toepassing zal gebruiken
   1. Bestaande set referenties in de toepassing die is gekoppeld aan de set gebruikers 
1. Maak voor elke combinatie van gebruikerssets en referenties een beveiligingsgroep in de cloud of on-premises op basis van uw vereisten.
1. De gedeelde referenties opnieuw instellen. Zodra de app is geïmplementeerd in Azure AD, hebben personen het wachtwoord van het gedeelde account niet meer nodig. Aangezien Azure AD het wachtwoord opslaat, u overwegen het wachtwoord in te stellen als zeer lang en complex. 
1. Configureer automatische rollover van het wachtwoord als de toepassing dit ondersteunt. Op die manier weet zelfs de beheerder die de eerste installatie heeft gedaan het wachtwoord van het gedeelde account niet. 

## <a name="plan-your-authentication-method"></a>Uw verificatiemethode plannen

Het kiezen van de juiste verificatiemethode is een cruciale eerste beslissing bij het opzetten van een hybride azure-identiteitsoplossing voor AD. Implementeer de verificatiemethode die is geconfigureerd met Azure AD Connect, waarin gebruikers ook in de cloud worden voorzien.

Als u een verificatiemethode wilt kiezen, moet u rekening houden met de tijd, bestaande infrastructuur, complexiteit en kosten van het implementeren van uw keuze. Deze factoren zijn verschillend voor elke organisatie en kunnen in de loop van de tijd veranderen. U moet degene kiezen die het meest overeenkomt met uw specifieke scenario. Zie [De juiste verificatiemethode kiezen voor uw hybride azure directory-identiteitsoplossing](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)voor meer informatie.

## <a name="plan-your-security-and-governance"></a>Plan uw beveiliging en governance 

Identiteit is de nieuwe primaire spil voor beveiligingsaandacht en investeringen, omdat netwerkperimeters steeds poreuzer en minder effectief zijn geworden met de explosie van BYOD-apparaten en cloudtoepassingen. 

### <a name="plan-access-reviews"></a>Beoordelingen van toegang plannen

[Met Access Reviews](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) kunnen organisaties groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt beheren. U moet van plan zijn om de toegang van de gebruiker regelmatig te controleren om ervoor te zorgen dat alleen de juiste mensen toegang hebben.

Enkele van de belangrijkste onderwerpen om te plannen tijdens het instellen van toegangsbeoordelingen zijn:

1. Het identificeren van een cadans voor toegangsbeoordelingen die passen bij uw bedrijfsbehoefte. Dit kan zo frequent zijn als eenmaal per week, maandelijks, jaarlijks, of als een on-demand oefening.

1. Maak groepen die de revisoren van de app-toegangsrapporten vertegenwoordigen. U moet ervoor zorgen dat belanghebbenden die het meest vertrouwd zijn met de app en de doelgebruikers en use cases deelnemen aan uw toegangsbeoordelingen

1. Het invullen van een toegangscontrole omvat het wegnemen van toegangsmachtigingen voor apps voor gebruikers die geen toegang meer nodig hebben. Plan voor het afhandelen van potentiële ondersteuningsverzoeken van geweigerde gebruikers. Een verwijderde gebruiker blijft 30 dagen verwijderd in Azure AD gedurende welke tijd deze indien nodig door een beheerder kan worden hersteld. Na dertig dagen wordt die gebruiker definitief verwijderd. Met de Azure Active Directory-portal kan een globale beheerder een onlangs verwijderde gebruiker expliciet permanent verwijderen voordat die periode is bereikt.

### <a name="plan-auditing"></a>Plan auditing

Azure AD biedt [rapporten met technische en zakelijke inzichten.](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/) 

Er zijn zowel beveiligings- als activiteitenrapporten beschikbaar. Beveiligingsrapporten tonen gebruikers die zijn gemarkeerd voor risico's en riskante aanmeldingen. Activiteitsrapporten helpen u het gedrag van gebruikers in uw organisatie te begrijpen door aanmeldingsactiviteiten te beschrijven en controlepaden van alle aanmeldingen te bieden. U rapporten gebruiken om risico's te beheren, de productiviteit te verhogen en de naleving te controleren.

| Rapporttype | Toegangscontrole | Beveiligingsrapporten | Aanmeldingsrapport |
|-------------|---------------|------------------|----------------|
| Gebruiken om te bekijken | Toepassingsmachtigingen en -gebruik. | Mogelijk gecompromitteerde accounts | Wie heeft toegang tot de applicaties |
| Mogelijke acties | Toegang tot de controle; machtigingen intrekken | Toegang intrekken; beveiliging van de force instellen | Toegang intrekken |

Azure AD bewaart de meeste controlegegevens gedurende 30 dagen en stelt de gegevens beschikbaar via de Azure-beheerportal of via een API die u downloaden naar uw analysesystemen.

### <a name="consider-using-microsoft-cloud-application-security"></a>Overweeg microsoft cloudtoepassingsbeveiliging te gebruiken

Microsoft Cloud App Security (MCAS) is een CASB-oplossing (Cloud Access Security Broker). Het geeft u inzicht in uw cloud-apps en -services, biedt geavanceerde analyses om cyberdreigingen te identificeren en te bestrijden en stelt u in staat om te bepalen hoe uw gegevens worden verplaatst.

Door MCAS te implementeren, u:

- Gebruik Cloud Discovery om uw cloudomgeving en de cloud-apps die uw organisatie gebruikt in kaart te brengen en te identificeren.
- Apps in uw cloud bestraffen en niet-sanctioneren
- Gebruik eenvoudig te implementeren app-connectors die gebruikmaken van provider-API's, voor zichtbaarheid en beheer van apps waarmee u verbinding maakt
- Gebruik voorwaardelijke toegangs-app-controlebeveiliging om realtime inzicht en controle te krijgen over toegang en activiteiten binnen uw cloud-apps
- Helpt u continue controle te hebben door beleid in te stellen en vervolgens voortdurend te verfijnen.

McAS-sessiebeheer (Microsoft Cloud Application Security) is beschikbaar voor elke browser op elk belangrijk platform op elk besturingssysteem. Mobiele apps en desktop-apps kunnen ook worden geblokkeerd of toegestaan. Door native te integreren met Azure AD, kunnen alle apps die zijn geconfigureerd met SAML of Open ID Connect-apps met één aanmelding in Azure AD worden ondersteund, inclusief [verschillende aanbevolen apps.](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

Zie het overzicht Beveiliging [van Microsoft Cloud App voor](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)meer informatie over MCAS. MCAS is een abonnementsservice op basis van gebruikers. U licentiegegevens bekijken in het [MCAS-licentiegegevensblad.](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)

### <a name="use-conditional-access"></a>Voorwaardelijke toegang gebruiken

Met Voorwaardelijke toegang u op criteria gebaseerde toegangscontrolebeslissingen voor uw cloud-apps automatiseren.

Beleid voor voorwaardelijke toegang wordt afgedwongen nadat de eerste-factorverificatie is voltooid. Voorwaardelijke toegang is daarom niet bedoeld als een eerste lijnverdediging voor scenario's zoals dos-aanvallen (denial-of-service), maar kan signalen van deze gebeurtenissen gebruiken om de toegang te bepalen. Bijvoorbeeld het aanmeldingsrisiconiveau, de locatie van de aanvraag, enzovoort, kunnen worden gebruikt. Zie [het overzicht](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) en het [implementatieplan](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)voor meer informatie over voorwaardelijke toegang.

## <a name="azure-sso-technical-requirements"></a>Technische azure SSO-vereisten

In de volgende sectie worden de vereisten beschreven voor het configureren van uw specifieke toepassing, waaronder de benodigde omgeving(en), eindpunten, claimtoewijzing, vereiste kenmerken, certificaten en gebruikte protocollen. U hebt deze informatie nodig om SSO te configureren in de [Azure AD-portal.](https://portal.azure.com/)

### <a name="authentication-mechanism-details"></a>Details van het verificatiemechanisme

Voor alle vooraf geïntegreerde SaaS-apps biedt Microsoft een zelfstudie en hebt u deze informatie niet nodig. Als de toepassing zich niet in onze marktplaats / galerij van toepassingen bevindt, moet u mogelijk de volgende gegevens verzamelen:

- **Huidige identiteitsprovider die de toepassing gebruikt voor SSO indien van toepassing** - Bijvoorbeeld: AD FS, PingFederate, Okta
- **Protocollen ondersteund door de doeltoepassing** - Bijvoorbeeld SAML 2.0, OpenID Connect, OAuth, Forms-Based Auth, WS-Fed, WS-Trust
- **Protocol dat is geconfigureerd met Azure AD** - BIJVOORBEELD SAML 2.0 of 1.1, OpenID Connect, OAuth, Forms-Based, WS-Fed

### <a name="attribute-requirements"></a>Attribuutvereisten

Er is een vooraf geconfigureerde set kenmerken en kenmerktoewijzingen tussen Azure AD-gebruikersobjecten en de gebruikersobjecten van elke SaaS-app. Sommige apps beheren andere typen objecten, zoals groepen. Plan de toewijzing van gebruikerskenmerken van Azure AD naar uw toepassing en [pas de standaardtoewijzingen voor kenmerken aan](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) uw bedrijfsbehoeften aan.

### <a name="certificate-requirements"></a>Certificaatvereisten

Het certificaat voor de toepassing moet up-to-date zijn, of er is een risico dat gebruikers geen toegang hebben tot de toepassing. De meeste SaaS applicatie certificaten zijn goed voor 36 maanden. U wijzigt de certificaatduur in het toepassingsblad. Zorg ervoor dat u de vervaldatum documenteert en weet hoe u uw certificaatverlenging gaat beheren. 

Er zijn twee manieren om uw certificaten te beheren. 

- **Automatische rollover van certificaten** - Microsoft ondersteunt [het ondertekenen van sleutelrollover in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Hoewel dit onze voorkeursmethode is voor het beheren van certificaten, ondersteunt niet alle ISV's dit scenario.

- **Handmatig bijwerken** - Elke toepassing heeft zijn eigen certificaat dat verloopt op basis van hoe het is gedefinieerd. Maak een nieuw certificaat voordat het certificaat van de toepassing verloopt en stuurt u het naar de ISV. Deze informatie kan worden opgehaald uit de federatie metadata. [Lees hier meer over federatiemetadata.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>SSO implementeren

Gebruik de volgende fasen om uw oplossing in uw organisatie te plannen en te implementeren:

### <a name="user-configuration-for-sso"></a>Gebruikersconfiguratie voor SSO

- **Uw testgebruikers identificeren**

   Neem contact op met de eigenaar van de app en vraag hen om een minimum van drie testgebruikers binnen de toepassing te maken. Controleer of de informatie die u gebruikt als primaire id correct wordt ingevuld en overeenkomt met een kenmerk dat beschikbaar is in Azure AD. In de meeste gevallen zal dit in kaart brengen met de "NameID" voor SAML-gebaseerde toepassingen. Voor JWT-tokens is het de 'preferred_username'.
   
   Maak de gebruiker in Azure AD handmatig als een cloudgebruiker of synchroniseer de gebruiker vanuit on-premises met de Azure AD Connect-synchronisatieengine. Zorg ervoor dat de informatie overeenkomt met de claims die naar de toepassing worden verzonden.

- **Eenmalige aanmelding configureren**

   Zoek de SSO-zelfstudie voor uw toepassing op en open de stappen [van](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)de zelfstudie om uw SaaS-toepassing met succes te configureren.

   Zie [Aangepaste toepassingsdocumentatie](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)als u uw toepassing niet vinden. Hiermee u een toepassing toevoegen die zich niet in de Azure AD-galerie bevindt.

   Optioneel u claims die zijn uitgegeven in het SAML-token voor de bedrijfstoepassing gebruiken met behulp van de [richtlijnendocumentatie van Microsoft.](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Zorg ervoor dat u in kaart brengt met wat u verwacht te ontvangen in de SAML-respons voor uw toepassing. Als u problemen ondervindt tijdens de configuratie, gebruikt u onze richtlijnen voor [het opsporen van SSO-integratie.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

Aangepaste toepassingsonboarding is een azure AD Premium P1- of P2-licentiefunctie.

### <a name="provide-sso-change-communications-to-end-users"></a>SSO-wijzigingscommunicatie bieden aan eindgebruikers

Implementeer uw communicatieplan. Zorg ervoor dat u uw eindgebruikers laat weten dat er een verandering komt, wanneer deze is aangekomen, wat u nu moet doen en hoe u hulp zoeken.

### <a name="verify-end-user-scenarios-for-sso"></a>Scenario's voor eindgebruikers voor SSO verifiëren

U de volgende testcases gebruiken om tests uit te voeren op bedrijfs- en persoonlijke apparaten om ervoor te zorgen dat uw SSO-configuraties werken zoals verwacht. De onderstaande scenario's gaan ervan uit dat een gebruiker navigeert naar een app-URL en gaat door een verificatiestroom geïnitieerd door de serviceprovider (SP-geïnitieerde auth flow).

| Scenario | Verwacht resultaat op SP-geïnitieerde auth flow door gebruiker |
|----------|---------------------------------------------------|
| Inloggen op toepassing met IE, terwijl op corpnet. | Geïntegreerde Windows-verificatie (IWA) vindt plaats zonder extra aanwijzingen. |
| Inloggen op de toepassing met IE, terwijl off corpnet met nieuwe login poging. | Op formulieren gebaseerde prompt op AD FS Server. Gebruiker logt met succes in en browservraagt voor MFA. |
| Inloggen op de toepassing met IE, terwijl off corpnet met een huidige sessie en heeft nog nooit uitgevoerd MFA. | Gebruiker ontvangt geen prompt voor de eerste factor. Gebruiker ontvangt prompt voor MFA. |
| Inloggen op de toepassing met IE, terwijl off corpnet met een huidige sessie en heeft al uitgevoerd MFA in deze sessie. | Gebruiker ontvangt geen prompt voor de eerste factor. De gebruiker ontvangt geen MFA. Gebruikers-SSO's in toepassing. |
| Inloggen op toepassing met Chrome / Firefox / Safari, terwijl off corpnet met een huidige sessie en heeft al mfa uitgevoerd in deze sessie. | Gebruiker ontvangt geen prompt voor de eerste factor. De gebruiker ontvangt geen MFA. Gebruiker SSO's in toepassing. |
| Inloggen op toepassing met Chrome / Firefox / Safari, terwijl off corpnet met nieuwe login poging. | Op formulieren gebaseerde prompt op AD FS Server. Gebruiker logt met succes in en browservraagt voor MFA. |
| Log in bij toepassing met Chrome/Firefox terwijl u zich in een bedrijfsnetwerk bevindt met een huidige sessie. | Gebruiker ontvangt geen prompt voor de eerste factor. De gebruiker ontvangt geen MFA. Gebruiker SSO's in toepassing. |
| Inloggen op applicatie met applicatie mobiele app met een nieuwe inlogpoging. | Op formulieren gebaseerde prompt op AD FS Server. Gebruiker logt met succes in en ADAL-clientvraagt voor MFA. |
| Onbevoegde gebruiker probeert in te loggen op de toepassing met login URL. | Op formulieren gebaseerde prompt op AD FS Server. Gebruiker niet in te loggen met de eerste factor. |
| Geautoriseerde gebruiker probeert in te loggen, maar voert een onjuist wachtwoord in. | Gebruiker navigeert naar de URL van de toepassing en ontvangt een fout met een slechte gebruikersnaam/wachtwoord. |
| Geautoriseerde gebruiker klikt op link in een e-mail en is al geverifieerd. | Gebruiker klikt op URL en is aangemeld bij de toepassing zonder extra aanwijzingen. |
| Geautoriseerde gebruiker klikt op link in een e-mail en is nog niet geverifieerd. | Gebruiker klikt op URL en wordt gevraagd om te verifiëren met de eerste factor. |
| Geautoriseerde gebruiker logt in bij toepassing met applicatie mobiele app (SP-geïnitieerd) met een nieuwe inlogpoging. | Op formulieren gebaseerde prompt op AD FS Server. Gebruiker logt met succes in en ADAL-clientvraagt voor MFA. |
| Onbevoegde gebruiker probeert in te loggen op de toepassing met login URL (SP-geïnitieerd). | Op formulieren gebaseerde prompt op AD FS Server. Gebruiker niet in te loggen met de eerste factor. |
| Geautoriseerde gebruiker probeert in te loggen, maar voert een onjuist wachtwoord in.| Gebruiker navigeert naar de URL van de toepassing en ontvangt een fout met een slechte gebruikersnaam/wachtwoord. |
| Geautoriseerde gebruiker logt uit en logt vervolgens opnieuw in. | Als de aanmeldings-URL is geconfigureerd, wordt de gebruiker uitgelogd bij alle services en wordt gevraagd om te verifiëren. |
| Geautoriseerde gebruiker logt uit en logt vervolgens opnieuw in. | Als de aanmeldings-URL niet is geconfigureerd, wordt de gebruiker automatisch opnieuw aangemeld met behulp van bestaande token van de bestaande Azure AD-browsersessie. |
| Geautoriseerde gebruiker klikt op link in een e-mail en is al geverifieerd. | Gebruiker klikt op URL en is aangemeld bij de toepassing zonder extra aanwijzingen. |
| Geautoriseerde gebruiker klikt op link in een e-mail en is nog niet geverifieerd. | Gebruiker klikt op URL en wordt gevraagd om te verifiëren met de eerste factor. |

## <a name="manage-sso"></a>SSO beheren

In dit gedeelte worden de vereisten en aanbevelingen beschreven om SSO succesvol te beheren.

### <a name="required-administrative-roles"></a>Vereiste administratieve rollen

Gebruik de rol altijd met de minste machtigingen die beschikbaar zijn om de vereiste taak in Azure Active Directory uit te voeren. Microsoft raadt aan [de verschillende rollen die beschikbaar zijn](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) te bekijken en de juiste te kiezen om uw behoeften voor elke persona voor deze toepassing op te lossen. Sommige rollen moeten mogelijk tijdelijk worden toegepast en verwijderd nadat de implementatie is voltooid.

| Persona| Rollen | Azure AD-rol (indien nodig) |
|--------|-------|-----------------------------|
| Helpdeskbeheerder | Ondersteuning voor Tier 1 | Geen |
| Identiteitsbeheerder | Configureren en debuggen wanneer problemen van invloed zijn op Azure AD | Globale beheerder |
| Toepassingsbeheerder | Gebruikersattest in toepassing, configuratie op gebruikers met machtigingen | Geen |
| Infrastructuurbeheerders | Cert rollover eigenaar | Globale beheerder |
| Bedrijfseigenaar/stakeholder | Gebruikersattest in toepassing, configuratie op gebruikers met machtigingen | Geen |

We raden u aan [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) te gebruiken om uw rollen te beheren om extra controle, controle en toegangscontrole te bieden voor gebruikers met directorymachtigingen.

### <a name="sso-certificate-lifecycle-management"></a>SSO-certificaatlevenscyclusbeheer

Het is belangrijk om de juiste rollen en e-maildistributielijsten te identificeren die belast zijn met het beheren van de levenscyclus van het ondertekeningscertificaat tussen Azure AD en de toepassing die wordt geconfigureerd met eenmalige aanmelding. Hier zijn enkele van de belangrijkste rollen die we aanbevelen met in plaats:

- Eigenaar voor het bijwerken van gebruikerseigenschappen in toepassing
- Ondersteuning voor eigenaar op afroep voor ondersteuning voor het breken/repareren van toepassingen
- Lijst met e-maildistributie voor certificaatgerelateerde wijzigingsmeldingen

De maximale levensduur van een certificaat is drie jaar. We raden u aan een proces vast te stellen over hoe u een certificaatwijziging tussen Azure AD en uw toepassing gaat verwerken. Dit kan helpen voorkomen of minimaliseren van een storing als gevolg van een certificaat verlopen of force certificaat rollover.

### <a name="rollback-process"></a>Terugdraaiproces

Nadat u de testtests hebt voltooid, is het tijd om met uw toepassing in productie te gaan. Overstappen naar productie betekent dat u uw geplande en geteste configuraties in uw productietenant implementeert en uitrolt naar uw gebruikers. Het is echter belangrijk om te plannen wat te doen als uw implementatie niet verloopt zoals gepland. Als de SSO-configuratie tijdens de implementatie mislukt, moet u begrijpen hoe u eventuele uitval beperken en de impact voor uw gebruikers beperken.

De beschikbaarheid van authenticatiemethoden binnen de toepassing bepaalt uw beste strategie. Zorg er altijd voor dat u gedetailleerde documentatie hebt voor app-eigenaren over hoe u precies teruggaan naar de oorspronkelijke inlogconfiguratiestatus voor het geval uw implementatie problemen ondervindt.

- **Als uw app meerdere identiteitsproviders ondersteunt**, bijvoorbeeld LDAP en AD FS en Ping, verwijdert u de bestaande SSO-configuratie niet tijdens de implementatie. Schakel het in plaats daarvan uit tijdens de migratie voor het geval u deze later terug moet schakelen. 

- **Als uw app geen ondersteuning biedt voor meerdere IdP's,** maar gebruikers in staat stelt om in te loggen met behulp van op formulieren gebaseerde verificatie (gebruikersnaam/wachtwoord), moet u ervoor zorgen dat gebruikers terug kunnen vallen op deze aanpak in het geval de nieuwe SSO-configuratie-implementatie mislukt.

### <a name="access-management"></a>Toegangsbeheer

We raden u aan een geschaalde benadering te kiezen bij het beheren van toegang tot bronnen. Veelvoorkomende benaderingen zijn het gebruik van on-premises groepen door te synchroniseren via Azure AD Connect, [dynamische groepen in Azure AD te maken op basis van gebruikerskenmerken](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)of [zelfservicegroepen](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) te maken in Azure AD dat wordt beheerd door een broneigenaar.

### <a name="monitor-security"></a>Beveiliging controleren

We raden u aan een regelmatige cadans in te stellen waarin u de verschillende aspecten van de beveiliging van de SaaS-app bekijkt en eventuele corrigerende acties uitvoert die nodig zijn.

### <a name="troubleshooting"></a>Problemen oplossen

In de volgende koppelingen worden scenario's voor probleemoplossing weergeven. U een specifieke handleiding maken voor uw ondersteuningsmedewerkers waarin deze scenario's en de stappen zijn opgenomen om deze te herstellen.

#### <a name="consent-issues"></a>Toestemmingsproblemen

- [Onverwachte toestemmingsfout](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Fout bij toestemming gebruiker](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Aanmeldingsproblemen

- [Problemen met het aanmelden vanuit een aangepaste portal](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problemen met aanmelden vanaf toegangsvenster](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Fout bij de aanmeldingspagina van toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Probleem bij het aanmelden bij een Microsoft-toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>SSO-problemen voor toepassingen die worden vermeld in de Azure Application Gallery

- [Probleem met wachtwoord SSO voor toepassingen die worden vermeld in de Azure Application Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Probleem met gefedereerde SSO voor toepassingen die worden vermeld in de Azure Application Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>SSO-problemen voor toepassingen die niet worden vermeld in de Azure Application Gallery

- [Probleem met wachtwoord SSO voor toepassingen DIE NIET worden vermeld in de Azure Application Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Probleem met gefedereerde SSO voor toepassingen die niet worden vermeld in de Azure Application Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Volgende stappen

[Fouten opsporen van op SAML gebaseerde SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Claimmapping voor apps via PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Claims aanpassen die zijn uitgegeven in SAML-token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Enkel aanmeldingsSAML-protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[EnkeluitlogSAML-protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (voor externe gebruikers zoals partners en leveranciers)

[Voorwaardelijke toegang voor Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure-identiteitsbeveiliging](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Toegang via eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Zelfstudie voor toepassing SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
