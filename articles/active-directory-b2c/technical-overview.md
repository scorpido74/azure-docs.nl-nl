---
title: Overzicht van technische functies en functies - Azure Active Directory B2C
description: Een diepgaande introductie van de functies en technologieën in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d3d6b33211f6f247d9f30c0f162b388085faabe6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332533"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Technisch en functieoverzicht van Azure Active Directory B2C

Dit artikel is een aanvulling op [Over Azure Active Directory B2C](overview.md)en biedt een meer diepgaande inleiding tot de service. Hier besproken worden de primaire bronnen waarmee u werkt in de service, de functies en hoe deze u in staat stellen om uw klanten een volledig aangepaste identiteitservaring te bieden in uw toepassingen.

## <a name="azure-ad-b2c-tenant"></a>Azure AD B2C-tenant

In Azure Active Directory B2C (Azure AD B2C) vertegenwoordigt een *tenant* uw organisatie en is het een gebruikersmap. Elke Azure AD B2C-tenant is uniek en staat volledig los van andere Azure AD B2C-tenants. Een Azure AD B2C-tenant is anders dan een Azure Active Directory-tenant, die u mogelijk al hebt.

De primaire resources waarmee u werkt in een Azure AD B2C-tenant zijn:

* **Map** : de *map* is de plaats waar Azure AD B2C de referenties en profielgegevens van uw gebruikers opslaat, evenals uw toepassingsregistraties.
* **Toepassingsregistraties** : u registreert uw web-, mobiele en native toepassingen bij Azure AD B2C om identiteitsbeheer in te schakelen. Ook eventuele API's die u wilt beveiligen met Azure AD B2C.
* **Gebruikersstromen** en **aangepast beleid** - De ingebouwde (gebruikersstromen) en volledig aanpasbare (aangepaste beleidsregels) identiteitservaringen voor uw toepassingen.
  * Gebruik *gebruikersstromen* voor een snelle configuratie en het inschakelen van algemene identiteitstaken, zoals aanmelden, aanmelden en profielbewerking.
  * Gebruik *aangepaste beleidsregels* om gebruikerservaringen niet alleen in te schakelen voor de algemene identiteitstaken, maar ook voor het maken van ondersteuning voor complexe identiteitsworkflows die uniek zijn voor uw organisatie, klanten, werknemers, partners en burgers.
* **Identiteitsproviders** - Federatie-instellingen voor:
  * *Aanbieders* van sociale identiteit zoals Facebook, LinkedIn of Twitter die u wilt ondersteunen in uw toepassingen.
  * *Externe* identiteitsproviders die standaard identiteitsprotocollen zoals OAuth 2.0, OpenID Connect en meer ondersteunen.
  * *Lokale* accounts waarmee gebruikers zich kunnen aanmelden en zich kunnen aanmelden met een gebruikersnaam (of e-mailadres of ander identiteitsbewijs) en een wachtwoord.
* **Sleutels** - Versleutelingssleutels toevoegen en beheren voor het ondertekenen en valideren van tokens.

Een Azure AD B2C-tenant is de eerste bron die u moet maken om aan de slag te gaan met Azure AD B2C. Meer informatie over hoe in [Zelfstudie: Een Azure Active Directory B2C-tenant maken](tutorial-create-tenant.md).

## <a name="accounts-in-azure-ad-b2c"></a>Accounts in Azure AD B2C

Azure AD B2C definieert verschillende typen gebruikersaccounts. Azure Active Directory, Azure Active Directory B2B en Azure Active Directory B2C delen deze accounttypen.

* **Werkaccount** - Gebruikers met werkaccounts kunnen resources in een tenant beheren en met een beheerdersrol kunnen ze ook tenants beheren. Gebruikers met werkaccounts kunnen nieuwe consumentenaccounts maken, wachtwoorden opnieuw instellen, accounts blokkeren/deblokkeren en machtigingen instellen of een account toewijzen aan een beveiligingsgroep.
* **Gastaccount** - Externe gebruikers die u uitnodigt aan uw huurder als gasten. Een typisch scenario voor het uitnodigen van een gastgebruiker voor uw Azure AD B2C-tenant is het delen van beheerverantwoordelijkheden.
* **Consumentenaccount** : consumentenaccounts zijn de accounts die zijn gemaakt in uw Azure AD B2C-map wanneer gebruikers het gebruikerstraject voor aanmelding voltooien in een toepassing die u in uw tenant hebt geregistreerd.

![Azure AD B2C-gebruikersbeheerpagina in de Azure-portal](media/technical-overview/portal-01-users.png)<br/>*Figuur: gebruikersmap binnen een Azure AD B2C-tenant in de Azure-portal*

### <a name="consumer-accounts"></a>Consumentenrekeningen

Met een *consumentenaccount* kunnen gebruikers zich aanmelden bij de toepassingen die u hebt beveiligd met Azure AD B2C. Gebruikers met consumentenaccounts hebben echter geen toegang tot Azure-bronnen, bijvoorbeeld de Azure-portal.

Aan deze identiteitstypen kan een consumentenaccount worden gekoppeld:

* **Lokale** identiteit, met de gebruikersnaam en het wachtwoord lokaal opgeslagen in de Azure AD B2C-map. We noemen deze identiteiten vaak 'lokale accounts'.
* **Sociale** of **bedrijfsidentiteiten,** waarbij de identiteit van de gebruiker wordt beheerd door een federatieve identiteitsprovider zoals Facebook, Microsoft, ADFS of Salesforce.

Een gebruiker met een consumentenaccount kan zich aanmelden met meerdere identiteiten, bijvoorbeeld gebruikersnaam, e-mail, werknemers-ID, overheids-ID en anderen. Eén account kan meerdere identiteiten hebben, zowel lokaal als sociaal.

![Identiteiten van consumentenaccount](media/technical-overview/identities.png)<br/>*Afbeelding: één consumentenaccount met meerdere identiteiten in Azure AD B2C*

Met Azure AD B2C u algemene kenmerken van gebruikersaccountprofielen beheren, zoals weergavenaam, achternaam, voornaam, plaats en anderen. U het Azure AD-schema ook uitbreiden om aanvullende informatie over uw gebruikers op te slaan. Bijvoorbeeld, hun land of residentie, voorkeurstaal, en voorkeuren zoals of ze willen abonneren op een nieuwsbrief of multi-factor authenticatie inschakelen.

Meer informatie over de gebruikersaccounttypen in Azure AD B2C in [Overzicht van gebruikersaccounts in Azure Active Directory B2C](user-overview.md).

## <a name="external-identity-providers"></a>Externe id-providers

U Azure AD B2C zo configureren dat gebruikers zich bij uw toepassing kunnen aanmelden met referenties van externe sociale of zakelijke identiteitsproviders (IdP). Azure AD B2C ondersteunt externe identiteitsproviders zoals Facebook, Microsoft-account, Google, Twitter en elke identiteitsprovider die OAuth 1.0, OAuth 2.0, OpenID Connect, SAML of WS-Federation-protocollen ondersteunt.

![Externe id-providers](media/technical-overview/external-idps.png)

Met de externe identiteitsproviderfederatie u uw consumenten de mogelijkheid bieden om in te loggen met hun bestaande sociale of zakelijke accounts, zonder dat u een nieuw account hoeft aan te maken alleen voor uw toepassing.

Op de aanmeldings- of aanmeldingspagina presenteert Azure AD B2C een lijst met externe identiteitsproviders die de gebruiker kan kiezen voor aanmelding. Zodra ze een van de externe identiteitsproviders hebben geselecteerd, worden ze (doorgestuurd) naar de website van de geselecteerde provider geleid om het aanmeldingsproces te voltooien. Nadat de gebruiker zich heeft aanmeldt, wordt deze teruggestuurd naar Azure AD B2C voor verificatie van het account in uw toepassing.

![Voorbeeld van mobiel aanmelden met een sociaal account (Facebook)](media/technical-overview/external-idp.png)

Zie [Zelfstudie: Identiteitsproviders toevoegen aan uw toepassingen in Azure Active Directory B2C](tutorial-add-identity-providers.md)voor meer informatie over het toevoegen van identiteitsproviders in Azure AD B2C.

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Identiteitservaringen: gebruikersstromen of aangepaste beleidsregels

Het uitbreidbare beleidsframework van Azure AD B2C is de kernkracht. Beleid beschrijft de identiteitservaringen van uw gebruikers, zoals aanmelden, aanmelden en het bewerken van profielen.

In Azure AD B2C zijn er twee primaire paden die u volgen om deze identiteitservaringen te bieden: gebruikersstromen en aangepaste beleidsregels.

* **Gebruikersstromen** zijn vooraf gedefinieerde, ingebouwde, configureerbare beleidsregels die we bieden, zodat u binnen enkele minuten aanmeldings-, aanmeldings- en beleidsbewerkingservaringen maken.

* **Met aangepast beleid** u uw eigen gebruikersreizen maken voor complexe scenario's voor identiteitservaring.

Zowel gebruikersstromen als aangepaste beleidsregels worden aangedreven door het *Identity Experience Framework*, de beleidsorchestration-engine van Azure AD B2C.

### <a name="user-flow"></a>Gebruikersstroom

Om u te helpen snel de meest voorkomende identiteitstaken in te stellen, bevat de Azure-portal verschillende vooraf gedefinieerde en configureerbare beleidsregels genaamd *gebruikersstromen.*

U instellingen voor gebruikersstromen zoals deze configureren om het gedrag van identiteitservaringen in uw toepassingen te beheren:

* Accounttypen die worden gebruikt voor aanmelding, zoals sociale accounts zoals een Facebook of lokale accounts die een e-mailadres en wachtwoord gebruiken voor aanmelding
* Kenmerken die bij de consument moeten worden verzameld, zoals voornaam, postcode of land van verblijf
* Azure Multi-Factor Authentication (MFA)
* Aanpassing van de gebruikersinterface
* Set claims in een token dat uw toepassing ontvangt nadat de gebruiker de gebruikersstroom heeft voltooid
* Sessiebeheer
* ... en meer.

De meest voorkomende identiteitsscenario's voor de meeste mobiele, web- en toepassingen met één pagina kunnen effectief worden gedefinieerd en geïmplementeerd met gebruikersstromen. We raden u aan de ingebouwde gebruikersstromen te gebruiken, tenzij u complexe gebruikersreisscenario's hebt die de volledige flexibiliteit van het aangepaste beleid vereisen.

Meer informatie over gebruikersstromen in [gebruikersstromen in Azure Active Directory B2C](user-flow-overview.md).

### <a name="custom-policy"></a>Aangepast beleid

Aangepaste beleidsregels ontgrendelen toegang tot de volledige kracht van de Identity Experience Framework (IEF) orchestration engine. Met aangepaste beleidsregels u IEF gebruiken om bijna elke verificatie-, gebruikersregistratie- of profielbewerkingservaring te bouwen die u zich voorstellen.

Het Identity Experience Framework geeft u de mogelijkheid om gebruikersreizen te maken met elke combinatie van stappen. Bijvoorbeeld:

* Federatie met andere identiteitsaanbieders
* Uitdagingen voor multifactorauthenticatie (MFA) van eerste en derde partij
* Alle gebruikersinvoer verzamelen
* Integreren met externe systemen met REST API-communicatie

Elk van deze gebruikersreizen wordt gedefinieerd door een beleid en u zo veel of zo weinig beleidsregels maken als u nodig hebt om de beste gebruikerservaring voor uw organisatie in te schakelen.

![Diagram met een voorbeeld van een complexe gebruikersreis ingeschakeld door IEF](media/technical-overview/custom-policy.png)

Een aangepast beleid wordt gedefinieerd door verschillende XML-bestanden die naar elkaar verwijzen in een hiërarchische keten. De XML-elementen definiëren het claimschema, claimstransformaties, inhoudsdefinities, claimsproviders, technische profielen, stappen voor het orkestratie van gebruikersreizen en andere aspecten van de identiteitservaring.

De krachtige flexibiliteit van aangepaste beleidsregels is het meest geschikt voor wanneer u complexe identiteitsscenario's moet maken. Ontwikkelaars die aangepaste beleidsregels configureren, moeten de vertrouwde relaties zorgvuldig definiëren om metagegevenseindpunten, exacte definities van claimsuitwisseling en geheimen, sleutels en certificaten configureren als dat nodig is voor elke identiteitsprovider.

Meer informatie over aangepaste beleidsregels in [Aangepast beleid in Azure Active Directory B2C](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protocollen en tokens

Azure AD B2C ondersteunt de [OpenID Connect- en OAuth 2.0-protocollen](protocols-overview.md) voor gebruikersbelevingen. In de Azure AD B2C-implementatie van OpenID Connect wordt deze gebruikerservaring via de toepassing gestart door verificatieaanvragen te verzenden naar Azure AD B2C.

Het resultaat van een aanvraag bij Azure AD B2C is een beveiligingstoken, zoals een [id-token of toegangstoken](tokens-overview.md). Met dit beveiligingstoken wordt de identiteit van de gebruiker gedefinieerd. Tokens worden ontvangen van Azure AD B2C-eindpunten zoals het `/token` of `/authorize` eindpunt. Met deze tokens hebt u toegang tot claims die kunnen worden gebruikt om een identiteit te valideren en toegang te verlenen tot beveiligde bronnen.

Voor externe identiteiten ondersteunt Azure AD B2C federatie met elke OAuth 1.0, OAuth 2.0, OpenID Connect, SAML en WS-Fed identiteitsprovider.

![Diagram van oidc-gebaseerde client-app die wordt gefederend met een SAML-gebaseerde IdP](media/technical-overview/protocols.png)

Het vorige diagram laat zien hoe Azure AD B2C kan communiceren met behulp van verschillende protocollen binnen dezelfde verificatiestroom:

1. De toepassing relying party initieert een autorisatieaanvraag voor Azure AD B2C met OpenID Connect.
1. Wanneer een gebruiker van de toepassing ervoor kiest zich aan te melden met een externe identiteitsprovider die het SAML-protocol gebruikt, roept Azure AD B2C het SAML-protocol op om met die identiteitsprovider te communiceren.
1. Nadat de gebruiker de aanmeldingsbewerking met de externe identiteitsprovider heeft voltooid, retourneert Azure AD B2C het token vervolgens naar de toepassing van de relying party met OpenID Connect.

## <a name="application-integration"></a>Integratie van toepassingen

Wanneer een gebruiker zich wil aanmelden bij uw toepassing, of het nu gaat om een web-, mobiele, desktop- of single-page toepassing (SPA), initieert de toepassing een autorisatieaanvraag voor een door de gebruiker opgegeven eindpunt. De gebruikersstroom of het aangepaste beleid definieert en bepaalt de ervaring van de gebruiker. Wanneer ze een gebruikersstroom voltooien, bijvoorbeeld de *aanmeldings- of aanmeldingsstroom,* genereert Azure AD B2C een token en leidt de gebruiker vervolgens terug naar uw toepassing.

![Mobiele app met pijlen met stroom tussen aanmeldingspagina van Azure AD B2C](media/technical-overview/app-integration.png)

Meerdere toepassingen kunnen dezelfde gebruikersstroom of het aangepaste beleid gebruiken. Eén toepassing kan meerdere gebruikersstromen of aangepaste beleidsregels gebruiken.

Als u zich bijvoorbeeld wilt aanmelden bij een toepassing, gebruikt de toepassing de *aanmelding s- of aanmeldingsstroom.* Nadat de gebruiker zich heeft aangemeld, wil hij mogelijk zijn profiel bewerken, zodat de toepassing een andere autorisatieaanvraag initieert, dit keer met behulp van de *gebruikersstroom voor profielbewerking.*

## <a name="seamless-user-experiences"></a>Naadloze gebruikerservaringen

In Azure AD B2C u de identiteitservaringen van uw gebruikers zo maken dat de pagina's die ze weergeven naadloos aansluiten bij de look en feel van uw merk. U krijgt bijna volledige controle over de HTML- en CSS-inhoud die aan uw gebruikers wordt gepresenteerd wanneer ze de identiteitsreizen van uw toepassing doorlopen. Met deze flexibiliteit u merk- en visuele consistentie tussen uw toepassing en Azure AD B2C behouden.

![Schermafbeeldingen van aanmeldingspagina voor aanmelding op basis van het merk](media/technical-overview/seamless-ux.png)

Zie Over aanpassing van [de gebruikersinterface in Azure Active Directory B2C](customize-ui-overview.md)voor informatie over aanpassing van de gebruikersinterface .

## <a name="localization"></a>Lokalisatie

Met het aanpassen van talen in Azure AD B2C u verschillende talen aanpassen die aan de behoeften van uw klant voldoen. Microsoft biedt de vertalingen voor 36 talen, maar u ook uw eigen vertalingen voor elke taal. Zelfs als uw ervaring slechts voor één taal is bedoeld, u elke tekst op de pagina's aanpassen.

![Drie aanmeldingspagina's met ui-tekst in verschillende talen](media/technical-overview/localization.png)

Bekijk hoe lokalisatie werkt in [Taalaanpassing in Azure Active Directory B2C](user-flow-language-customization.md).

## <a name="add-your-own-business-logic"></a>Uw eigen bedrijfslogica toevoegen

Als u ervoor kiest om aangepaste beleidsregels te gebruiken, u integreren met een RESTful API in een gebruikersreis om uw eigen bedrijfslogica aan het traject toe te voegen. Azure AD B2C kan bijvoorbeeld gegevens uitwisselen met een RESTful-service om:

* Aangepaste gebruiksvriendelijke foutberichten weergeven.
* Gebruikersinvoer valideren om te voorkomen dat misvormde gegevens in uw gebruikersmap blijven bestaan. U bijvoorbeeld de door de gebruiker ingevoerde gegevens wijzigen, zoals het kapitaliseren van de voornaam als deze in alle kleine letters zijn ingevoerd.
* Verrijk gebruikersgegevens door verder te integreren met uw bedrijfslijn-of-business applicatie.
* Met RESTful-oproepen u pushmeldingen verzenden, bedrijfsdatabases bijwerken, een gebruikersmigratieproces uitvoeren, machtigingen beheren, databases controleren en meer.

Loyaliteitsprogramma's zijn een ander scenario dat is ingeschakeld door de ondersteuning van Azure AD B2C voor het aanroepen van REST-API's. Uw RESTful-service kan bijvoorbeeld het e-mailadres van een gebruiker ontvangen, uw klantendatabase opvragen en vervolgens het loyaliteitsnummer van de gebruiker terugsturen naar Azure AD B2C. De retourgegevens kunnen worden opgeslagen in het directoryaccount van de gebruiker in Azure AD B2C, vervolgens verder worden geëvalueerd in volgende stappen in het beleid of worden opgenomen in het toegangstoken.

![Line-of-business integratie in een mobiele applicatie](media/technical-overview/lob-integration.png)

U een REST API-aanroep toevoegen bij elke stap in het gebruikerstraject dat wordt gedefinieerd door een aangepast beleid. U bijvoorbeeld een REST API aanroepen:

* Tijdens het aanmelden, net voordat Azure AD B2C de referenties valideert
* Direct na het aanmelden
* Voordat Azure AD B2C een nieuw account in de map maakt
* Nadat Azure AD B2C een nieuw account in de map heeft gemaakt
* Voordat Azure AD B2C een toegangstoken uitgeeft

Zie [Rest API-claims uitwisselingen integreren in uw aangepaste Azure AD B2C-beleid](custom-policy-rest-api-intro.md)voor het gebruik van aangepaste beleidsregels voor RESTful API-integratie in Azure AD B2C.

## <a name="protect-customer-identities"></a>Klantidentiteiten beschermen

Azure AD B2C voldoet aan de beveiligings-, privacy- en andere toezeggingen die zijn beschreven in het [Microsoft Azure Trust Center.](https://www.microsoft.com/trustcenter/cloudservices/azure)

Sessies worden gemodelleerd als versleutelde gegevens, waarbij de decryptiesleutel alleen bekend is bij de Azure AD B2C Security Token Service. Een sterk encryptie-algoritme, AES-192, wordt gebruikt. Alle communicatiepaden zijn beveiligd met TLS voor vertrouwelijkheid en integriteit. Onze Security Token Service maakt gebruik van een Extended Validation (EV) certificaat voor TLS. In het algemeen vermindert de Beveiligingstokenservice XSS-aanvallen (cross-site scripting) door geen niet-vertrouwde invoer weer te geven.

![Diagram van beveiligde gegevens tijdens doorvoer en in rust](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Toegang tot gebruikersgegevens

Azure AD B2C-tenants delen veel kenmerken met enterprise Azure Active Directory-tenants die worden gebruikt voor werknemers en partners. Gedeelde aspecten omvatten mechanismen voor het bekijken van administratieve rollen, het toewijzen van rollen en controleactiviteiten.

U rollen toewijzen om te bepalen wie bepaalde beheeracties kan uitvoeren in Azure AD B2C, waaronder:

* Alle aspecten van gebruikersstromen maken en beheren
* Het kenmerkschema maken en beheren dat beschikbaar is voor alle gebruikersstromen
* Identiteitsproviders configureren voor gebruik in directe federatie
* Beleid voor vertrouwenskaders maken en beheren in het Identity Experience Framework (aangepaste beleidsregels)
* Geheimen voor federatie en versleuteling beheren in het Identity Experience Framework (aangepast beleid)

Zie [Beheerdersrolmachtigingen in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over Azure AD-rollen, waaronder ondersteuning voor Azure AD B2C-beheerrol.

### <a name="multi-factor-authentication-mfa"></a>Meervoudige verificatie (MFA)

Azure AD B2C multi-factor authentication (MFA) helpt de toegang tot gegevens en toepassingen te waarborgen en tegelijkertijd de eenvoud voor uw gebruikers te behouden. Het biedt extra beveiliging door een tweede vorm van authenticatie te vereisen en levert sterke verificatie door een reeks gebruiksvriendelijke verificatiemethoden aan te bieden. Uw gebruikers kunnen al dan niet worden uitgedaagd voor MFA op basis van configuratiebeslissingen die u als beheerder nemen.

Bekijk hoe u MFA in gebruikersstromen inschakelt in [Multi-factor authenticatie inschakelen in Azure Active Directory B2C](custom-policy-multi-factor-authentication.md).

### <a name="smart-account-lockout"></a>Slimme accountuitsluiting

Om te voorkomen dat brute-force wachtwoord raden pogingen, Azure AD B2C maakt gebruik van een geavanceerde strategie om accounts te vergrendelen op basis van het IP van het verzoek, de ingevoerde wachtwoorden, en een aantal andere factoren. De duur van de uitsluiting wordt automatisch verhoogd op basis van risico en het aantal pogingen.

![Account slimme lock-out](media/technical-overview/smart-lockout1.png)

Zie Bedreigingen voor resources en [gegevens beheren in Azure Active Directory B2C](threat-management.md)voor meer informatie over het beheren van instellingen voor wachtwoordbeveiliging.

### <a name="password-complexity"></a>Wachtwoordcomplexiteit

Tijdens het opnieuw instellen of met een wachtwoord moeten uw gebruikers een wachtwoord opgeven dat voldoet aan de complexiteitsregels. Azure AD B2C dwingt standaard een sterk wachtwoordbeleid af. Azure AD B2C biedt ook configuratieopties voor het opgeven van de complexiteitsvereisten van de wachtwoorden die uw klanten gebruiken.

U vereisten voor wachtwoordcomplexiteit configureren in zowel [gebruikersstromen](user-flow-password-complexity.md) als [aangepaste beleidsregels.](custom-policy-password-complexity.md)

## <a name="auditing-and-logs"></a>Controle en logboeken

Azure AD B2C zendt controlelogboeken uit met activiteitsgegevens over de resources, uitgegeven tokens en beheerderstoegang. U deze controlelogboeken gebruiken om inzicht te krijgen in de activiteit van het platform en problemen te diagnosticeren. Controlelogboekvermeldingen zijn beschikbaar kort na de activiteit die de gebeurtenis heeft gegenereerd.

In een controlelogboek, dat beschikbaar is voor uw Azure AD B2C-tenant of voor een bepaalde gebruiker, vindt u informatie zoals:

* Activiteiten met betrekking tot de autorisatie van een gebruiker om toegang te krijgen tot B2C-bronnen (bijvoorbeeld een beheerder die toegang heeft tot een lijst met B2C-beleidsregels)
* Activiteiten met betrekking tot directorykenmerken die zijn opgehaald wanneer een beheerder zich aanmeldt met de Azure-portal
* Crud-bewerkingen voor B2C-toepassingen maken, lezen, bijwerken en verwijderen
* CRUD-bewerkingen op sleutels die zijn opgeslagen in een B2C-sleutelcontainer
* CRUD-bewerkingen op B2C-resources (bijvoorbeeld beleids- en identiteitsproviders)
* Validatie van gebruikersreferenties en tokenuitgifte

![Individueel controlelogboek van gebruikers dat wordt weergegeven in de Azure-portal](media/technical-overview/audit-log.png)

Zie Toegang tot Azure [AD B2C-controlelogboeken](view-audit-logs.md)voor meer informatie over controlelogboeken.

### <a name="usage-insights"></a>Inzicht in gebruik

Met Azure AD B2C u ontdekken wanneer mensen zich aanmelden of zich aanmelden bij uw web-app, waar uw gebruikers zich bevinden en welke browsers en besturingssystemen ze gebruiken. Door Azure Application Insights te integreren in Azure AD B2C met behulp van aangepast beleid, u inzicht krijgen in hoe mensen zich aanmelden, zich aanmelden, hun wachtwoord opnieuw instellen of hun profiel bewerken. Met deze kennis u datagestuurde beslissingen nemen voor uw komende ontwikkelingscycli.

Meer informatie over gebruiksanalyses in [Gebruikersgedrag bijhouden in Azure Active Directory B2C met behulp van Application Insights.](analytics-with-application-insights.md)

## <a name="next-steps"></a>Volgende stappen

Nu u meer inzicht hebt in de functies en technische aspecten van Azure Active Directory B2C, gaat u aan de slag met de service door een B2C-tenant te maken:

> [!div class="nextstepaction"]
> [Zelfstudie: een Azure Active Directory B2C-tenant >maken](tutorial-create-tenant.md)