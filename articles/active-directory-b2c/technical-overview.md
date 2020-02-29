---
title: Technisch en functie overzicht-Azure Active Directory B2C
description: Een diep gaande Inleiding tot de functies en technologieën in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1aed1b0a13482bc932024a9d2a131ec5f1d948ba
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186893"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Technisch en feature overzicht van Azure Active Directory B2C

Dit artikel bevat een extra uitgebreide inleiding tot het [Azure Active Directory B2C](overview.md)van de service. Dit zijn de belangrijkste resources waarmee u samenwerkt in de service, de functies ervan en hoe u hiermee een volledig aangepaste identiteits ervaring kunt bieden voor uw klanten in uw toepassingen.

## <a name="azure-ad-b2c-tenant"></a>Azure AD B2C Tenant

In Azure Active Directory B2C (Azure AD B2C) vertegenwoordigt een *Tenant* uw organisatie en is deze een map met gebruikers. Elke Azure AD B2C-tenant is uniek en staat volledig los van andere Azure AD B2C-tenants. Een Azure AD B2C-Tenant is anders dan een Azure Active Directory Tenant, die u mogelijk al hebt.

De primaire resources waarmee u samenwerkt in een Azure AD B2C-Tenant zijn:

* **Directory** : de *Directory* is de locatie waar Azure AD B2C de referenties en profiel gegevens van uw gebruikers opslaat, evenals uw toepassings registraties.
* **Toepassings registraties** : u kunt uw web-, mobiele en systeem eigen toepassingen registreren met Azure AD B2C om identiteits beheer in te scha kelen. Ook de Api's die u met Azure AD B2C wilt beveiligen.
* **Gebruikers stromen** en **aangepaste beleids regels** : de ingebouwde (gebruikers stromen) en volledig aanpas bare identiteits ervaring (aangepaste beleids regels) voor uw toepassingen.
  * Gebruik *gebruikers stromen* voor snelle configuratie en activering van algemene identiteits taken, zoals aanmelden, aanmelden en profiel bewerking.
  * Gebruik *aangepaste beleids regels* om gebruikers ervaringen niet alleen in te scha kelen voor algemene identiteits taken, maar ook voor het maken van ondersteuning voor complexe identiteits werk stromen die uniek zijn voor uw organisatie, klanten, werk nemers, partners en burgers.
* **Id-providers** -Federatie-instellingen voor:
  * Aanbieders van *sociale* identiteiten, zoals Facebook, LinkedIn of Twitter, die u wilt ondersteunen in uw toepassingen.
  * *Externe* id-providers die ondersteuning bieden voor standaard identiteits protocollen zoals OAuth 2,0, OpenID Connect Connect en meer.
  * *Lokale* accounts waarmee gebruikers zich kunnen registreren en zich kunnen aanmelden met een gebruikers naam (of een e-mail adres of een andere id) en een wacht woord.
* **Sleutels** -versleutelings sleutels voor het ondertekenen en valideren van tokens toevoegen en beheren.

Een Azure AD B2C Tenant is de eerste resource die u moet maken om aan de slag te gaan met Azure AD B2C. Meer informatie over hoe u in [de zelf studie een Azure Active Directory B2C-Tenant maakt](tutorial-create-tenant.md).

## <a name="accounts-in-azure-ad-b2c"></a>Accounts in Azure AD B2C

Azure AD B2C definieert verschillende typen gebruikers accounts. Azure Active Directory, Azure Active Directory B2B en Azure Active Directory B2C deze account typen delen.

* **Werk account** : gebruikers met een werk account kunnen resources in een Tenant beheren en met een rol beheerder kunnen ook tenants beheren. Gebruikers met een werk account kunnen nieuwe consumenten accounts maken, wacht woorden opnieuw instellen, accounts blok keren/deblokkeren, en machtigingen instellen of een account toewijzen aan een beveiligings groep.
* **Gast account** : externe gebruikers die u als gast uitnodigen voor uw Tenant. Een typisch scenario voor het uitnodigen van een gast gebruiker aan uw Azure AD B2C-Tenant is het delen van beheer verantwoordelijkheden.
* **Consument account** -consumenten accounts zijn de accounts die zijn gemaakt in uw Azure AD B2C Directory wanneer gebruikers de inschrijvings traject van de gebruiker in een toepassing die u in uw Tenant hebt geregistreerd, kunt volt ooien.

![pagina Azure AD B2C gebruikers beheer in de Azure Portal](media/technical-overview/portal-01-users.png)<br/>*Afbeelding: gebruikers lijst binnen een Azure AD B2C-Tenant in de Azure Portal*

### <a name="consumer-accounts"></a>Consumenten accounts

Met een *consumenten* account kunnen gebruikers zich aanmelden bij de toepassingen die u hebt beveiligd met Azure AD B2C. Gebruikers met consumenten accounts kunnen echter geen toegang krijgen tot Azure-resources, bijvoorbeeld de Azure Portal.

Een consument account kan worden gekoppeld aan deze identiteits typen:

* **Lokale** identiteit, waarbij de gebruikers naam en het wacht woord lokaal zijn opgeslagen in de Azure AD B2C Directory. We verwijzen vaak naar deze identiteiten als ' lokale accounts '.
* **Sociale** of **Enter prise** -identiteiten, waarbij de identiteit van de gebruiker wordt beheerd door een federatieve id-provider zoals Facebook, micro soft, ADFS of Sales Force.

Een gebruiker met een consumenten account kan zich aanmelden met meerdere identiteiten, bijvoorbeeld gebruikers naam, e-mail adres, werk nemer-ID, overheids-ID en anderen. Eén account kan meerdere identiteiten, zowel lokaal als sociaal, hebben.

identiteiten van gebruikers accounts ![](media/technical-overview/identities.png)<br/>*Afbeelding: één consument account met meerdere identiteiten in Azure AD B2C*

Met Azure AD B2C kunt u algemene kenmerken van profielen voor consumenten accounts beheren, zoals de weergave naam, de achternaam, de voor waarde, de plaats en andere. U kunt ook het Azure AD-schema uitbreiden om aanvullende informatie over uw gebruikers op te slaan. Bijvoorbeeld hun land of locatie, voorkeurs taal en voor keuren, zoals of ze zich willen abonneren op een nieuws brief of multi-factor Authentication inschakelen.

Meer informatie over de typen gebruikers accounts in Azure AD B2C vindt u in [overzicht van gebruikers account in azure Active Directory B2C](user-overview.md).

## <a name="external-identity-providers"></a>Externe id-providers

U kunt Azure AD B2C configureren zodat gebruikers zich kunnen aanmelden bij uw toepassing met referenties van externe Social of ENTER prise Identity providers (IdP). Azure AD B2C ondersteunt externe ID-providers, zoals Facebook, Microsoft-account, Google, Twitter en enige ID-provider die ondersteuning biedt voor OAuth 1,0, OAuth 2,0, OpenID Connect Connect, SAML of WS-Federation-protocollen.

![Externe id-providers](media/technical-overview/external-idps.png)

Met een externe ID-provider Federatie kunt u uw consumenten de mogelijkheid bieden om zich aan te melden met hun bestaande sociale of zakelijke accounts, zonder dat u een nieuw account hoeft te maken voor uw toepassing.

Op de pagina registreren of aanmelden bevat Azure AD B2C een lijst met externe ID-providers die de gebruiker kan kiezen om zich aan te melden. Zodra ze een van de externe ID-providers hebben geselecteerd, worden ze (omgeleid) naar de website van de geselecteerde provider geleid om het aanmeldings proces te volt ooien. Nadat de gebruiker zich heeft aangemeld, keert deze terug naar Azure AD B2C voor verificatie van het account in uw toepassing.

![Mobiel aanmeldings voorbeeld met een sociaal account (Facebook)](media/technical-overview/external-idp.png)

Zie [zelf studie: id-providers toevoegen aan uw toepassingen in azure Active Directory B2C](tutorial-add-identity-providers.md)voor informatie over het toevoegen van id-providers in azure AD B2C.

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Identiteits ervaring: gebruikers stromen of aangepast beleid

Het Extensible Policy Framework van Azure AD B2C is de kern sterkte. Het beleid beschrijft de identiteits ervaringen van uw gebruikers, zoals registreren, aanmelden en profiel bewerking.

In Azure AD B2C zijn er twee primaire paden die u kunt nemen om deze identiteits ervaring te bieden: gebruikers stromen en aangepaste beleids regels.

* **Gebruikers stromen** zijn vooraf gedefinieerde, ingebouwde, Configureer bare beleids regels die we bieden, zodat u binnen enkele minuten registratie kunt maken, aanmelden en beleid voor het bewerken van processen.

* **Aangepaste beleids regels** bieden u de mogelijkheid om uw eigen gebruikers trajecten te maken voor scenario's met complexe identiteits ervaring.

Gebruikers stromen en aangepaste beleids regels worden aangedreven door het *Framework voor identiteits ervaring*, de Azure AD B2C's Policy Orchestration-engine.

### <a name="user-flow"></a>Gebruikersstroom

Om u te helpen snel de meest voorkomende identiteits taken in te stellen, bevat de Azure Portal verschillende vooraf gedefinieerde en configureer bare beleids regels die *gebruikers stromen*worden genoemd.

U kunt instellingen voor de gebruikers stroom configureren, zoals deze, om het gedrag van identiteits ervaring in uw toepassingen te beheren:

* Account typen die worden gebruikt voor aanmelding, zoals sociale accounts zoals een Facebook of lokale accounts die gebruikmaken van een e-mail adres en wacht woord voor aanmelding
* Kenmerken die moeten worden verzameld van de consument, zoals de voor naam, de post code of het land van locatie
* Azure-Multi-Factor Authentication (MFA)
* Aanpassing van de gebruikers interface
* Een set claims in een token die door de toepassing wordt ontvangen nadat de gebruiker de gebruikers stroom heeft voltooid
* Sessiebeheer
* ... en nog veel meer.

De meest voorkomende identiteits scenario's voor het meren deel van mobiele, Web-en single-page-toepassingen kunnen worden gedefinieerd en effectief worden geïmplementeerd met gebruikers stromen. U wordt aangeraden de ingebouwde gebruikers stromen te gebruiken tenzij u complexe gebruikers traject scenario's hebt waarvoor de volledige flexibiliteit van aangepast beleid is vereist.

Meer informatie over gebruikers stromen in [gebruikers stromen in azure Active Directory B2C](user-flow-overview.md).

### <a name="custom-policy"></a>Aangepast beleid

Aangepaste beleids regels ontgrendelen de toegang tot de volledige kracht van de IEF-Orchestration-Engine (Identity experience Framework). Met aangepaste beleids regels kunt u IEF gebruiken om bijna elke authenticatie, gebruikers registratie of profiel bewerkings ervaring te bouwen die u kunt Voorst Ellen.

Het Framework voor identiteits ervaring biedt u de mogelijkheid om gebruikers ritten te maken met elke combi natie van stappen. Bijvoorbeeld:

* Communiceren met andere id-providers
* Oplossingen voor de eerste en externe multi-factor Authentication (MFA)
* Gebruikers invoer verzamelen
* Integreren met externe systemen met behulp van REST API communicatie

Elke dergelijke gebruikers traject wordt gedefinieerd door een beleid en u kunt zoveel of zoveel beleids regels maken als u nodig hebt om de beste gebruikers ervaring voor uw organisatie in te scha kelen.

![Diagram van een voor beeld van een complexe gebruikers traject dat door IEF wordt ingeschakeld](media/technical-overview/custom-policy.png)

Een aangepast beleid wordt gedefinieerd door verschillende XML-bestanden die naar elkaar in een hiërarchische keten verwijzen. De XML-elementen definiëren het claim schema, claim transformaties, inhouds definities, claim providers, technische profielen, de Orchestrator-stappen voor gebruikers traject en andere aspecten van de identiteits ervaring.

De krachtige flexibiliteit van aangepast beleid is het meest geschikt voor wanneer u complexe identiteits scenario's wilt maken. Ontwikkel aars die aangepaste beleids regels configureren, moeten de vertrouwde relaties in zorgvuldige Details definiëren om eind punten van meta gegevens, exacte claim uitwisselings definities op te geven en geheimen, sleutels en certificaten te configureren die nodig zijn voor elke id-provider.

Meer informatie over aangepast beleid in het [aangepaste beleid in azure Active Directory B2C](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protocollen en tokens

Azure AD B2C ondersteunt de [OpenID Connect- en OAuth 2.0-protocollen](protocols-overview.md) voor gebruikersbelevingen. In de Azure AD B2C-implementatie van OpenID Connect wordt deze gebruikerservaring via de toepassing gestart door verificatieaanvragen te verzenden naar Azure AD B2C.

Het resultaat van een aanvraag bij Azure AD B2C is een beveiligingstoken, zoals een [id-token of toegangstoken](tokens-overview.md). Met dit beveiligingstoken wordt de identiteit van de gebruiker gedefinieerd. Tokens worden ontvangen van Azure AD B2C-eind punten, zoals het `/token`-of `/authorize`-eind punt. Met deze tokens kunt u toegang krijgen tot claims die kunnen worden gebruikt voor het valideren van een identiteit en toegang verlenen tot beveiligde bronnen.

Voor externe identiteiten ondersteunt Azure AD B2C Federatie met een OAuth 1,0-, OAuth 2,0-, OpenID Connect Connect-, SAML-en WS-ID-provider.

![Diagram van OIDC-client-app-federeren met een IdP op basis van SAML](media/technical-overview/protocols.png)

In het voor gaande diagram ziet u hoe Azure AD B2C kunnen communiceren met behulp van verschillende protocollen in dezelfde verificatie stroom:

1. De Relying Party toepassing initieert een autorisatie aanvraag naar Azure AD B2C met behulp van OpenID Connect Connect.
1. Wanneer een gebruiker van de toepassing meldt om zich aan te melden met een externe ID-provider die het SAML-protocol gebruikt, roept Azure AD B2C het SAML-protocol aan om te communiceren met die id-provider.
1. Nadat de gebruiker de aanmeldings bewerking met de externe ID-provider heeft voltooid, Azure AD B2C het token naar de Relying Party toepassing terugstuurt met behulp van OpenID Connect Connect.

## <a name="application-integration"></a>Integratie van toepassingen

Wanneer een gebruiker zich wil aanmelden bij uw toepassing, of het nu gaat om een web-, mobiel-, desktop-of single-page-toepassing (SPA), initieert de toepassing een autorisatie aanvraag naar een gebruikers stroom of een aangepast eind punt voor beleid. De gebruikers stroom of het aangepaste beleid definieert en bepaalt de gebruikers ervaring. Wanneer ze een gebruikers stroom volt ooien, bijvoorbeeld de *registratie-of aanmeldings* stroom, wordt door Azure AD B2C een token gegenereerd en wordt de gebruiker omgeleid naar uw toepassing.

![Mobiele app met pijlen die stroom tussen Azure AD B2C aanmeldings pagina weer geven](media/technical-overview/app-integration.png)

Meerdere toepassingen kunnen dezelfde gebruikers stroom of aangepast beleid gebruiken. Eén toepassing kan meerdere gebruikers stromen of aangepaste beleids regels gebruiken.

Als u zich bijvoorbeeld wilt aanmelden bij een toepassing, gebruikt de toepassing de flow *registreren of aanmelden* bij de gebruiker. Nadat de gebruiker zich heeft aangemeld, kunnen ze hun profiel bewerken, zodat de toepassing een andere autorisatie aanvraag initieert, met behulp van de gebruikers stroom voor het bewerken van het *profiel* .

## <a name="seamless-user-experiences"></a>Naadloze gebruikers ervaring

In Azure AD B2C kunt u de identiteits ervaringen van uw gebruikers bevestigen, zodat de pagina's die ze weer geven naadloos samen werken met het uiterlijk van uw merk. U krijgt bijna volledige controle over de HTML-en CSS-inhoud die aan uw gebruikers wordt gepresenteerd wanneer ze door de identiteits ritten van uw toepassing gaan. Met deze flexibiliteit kunt u merk-en visuele consistentie tussen uw toepassing en Azure AD B2C behouden.

![Scherm afbeeldingen van een op het merk aangepaste aanmeldings pagina](media/technical-overview/seamless-ux.png)

Zie informatie over het aanpassen van de [gebruikers interface in azure Active Directory B2C](customize-ui-overview.md)voor meer informatie over UI-aanpassing.

## <a name="localization"></a>Lokalisatie

Met de aanpassing van de taal in Azure AD B2C kunt u verschillende talen aanpassen aan de behoeften van uw klanten. Micro soft biedt de vertalingen voor 36 talen, maar u kunt ook uw eigen vertalingen voor elke taal opgeven. Zelfs als uw ervaring slechts voor één taal wordt gegeven, kunt u tekst op de pagina's aanpassen.

![Drie aanmeldings pagina's met gebruikers interface-tekst in verschillende talen](media/technical-overview/localization.png)

Bekijk hoe lokalisatie werkt in de [taal aanpassing in azure Active Directory B2C](user-flow-language-customization.md).

## <a name="add-your-own-business-logic"></a>Uw eigen bedrijfslogica toevoegen

Als u kiest voor het gebruik van aangepast beleid, kunt u integreren met een REST API in een gebruikers traject om uw eigen bedrijfs logica toe te voegen aan de reis. Azure AD B2C kunt bijvoorbeeld gegevens uitwisselen met een REST-service voor:

* Aangepaste gebruikers vriendelijke fout berichten weer geven.
* Valideer de gebruikers invoer om te voor komen dat ongeldige gegevens in uw gebruikers lijst worden bewaard. U kunt bijvoorbeeld de gegevens wijzigen die door de gebruiker worden ingevoerd, zoals de voor naam van een hoofd letter als ze in alle kleine letters worden ingevoerd.
* Verrijkende gebruikers gegevens door verder te integreren met uw zakelijke line-of-Business-toepassing.
* Met behulp van doorlopende aanroepen kunt u push meldingen verzenden, zakelijke data bases bijwerken, een gebruikers migratie proces uitvoeren, machtigingen beheren, data bases controleren en nog veel meer.

Loyaliteits Programma's zijn een ander scenario dat is ingeschakeld door Azure AD B2C's-ondersteuning voor het aanroepen van REST-Api's. Uw service kan bijvoorbeeld het e-mail adres van de gebruiker ontvangen, een query uitvoeren op de klanten database en vervolgens het loyaliteits nummer van de gebruiker retour neren om Azure AD B2C. De geretourneerde gegevens kunnen worden opgeslagen in het adreslijst account van de gebruiker in Azure AD B2C en vervolgens worden geëvalueerd in volgende stappen in het beleid of in het toegangs token worden opgenomen.

![Line-of-business-integratie in een mobiele toepassing](media/technical-overview/lob-integration.png)

U kunt een REST API-aanroep toevoegen aan elke stap in de gebruikers reis die is gedefinieerd door een aangepast beleid. U kunt bijvoorbeeld een REST API aanroepen:

* Tijdens het aanmelden, net voordat Azure AD B2C de referenties valideert
* Direct na het aanmelden
* Voordat Azure AD B2C een nieuw account in de Directory maakt
* Nadat Azure AD B2C een nieuw account in de Directory hebt gemaakt
* Voordat Azure AD B2C een toegangs token uitgeeft

Als u wilt zien hoe u aangepaste beleids regels gebruikt voor de integratie van de REST API in Azure AD B2C, raadpleegt u [rest API claims-uitwisseling integreren in uw Azure AD B2C gebruikers traject](rest-api-claims-exchange-dotnet.md).

## <a name="protect-customer-identities"></a>Klant identiteiten beveiligen

Azure AD B2C voldoet aan de beveiligings-, privacy-en andere verplichtingen die zijn beschreven in het [vertrouwens centrum van Microsoft Azure](https://www.microsoft.com/trustcenter/cloudservices/azure).

Sessies worden gemodelleerd als versleutelde gegevens, waarbij de ontsleutelingssleutel alleen bekend is bij de Azure AD B2C beveiligings token service. Er wordt een sterke versleutelings algoritme, AES-192, gebruikt. Alle communicatie paden worden beveiligd met TLS voor vertrouwelijkheid en integriteit. Onze beveiligings token service maakt gebruik van een Extended validatie-certificaat (EV) voor TLS. Over het algemeen beperkt de beveiligings token service XSS-aanvallen (cross-site scripting) door niet-vertrouwde invoer niet te renderen.

![Diagram van beveiligde gegevens onderweg en op rest](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Toegang tot gebruikers gegevens

Azure AD B2C-tenants hebben een groot aantal kenmerken met bedrijfs Azure Active Directory-tenants die worden gebruikt voor werk nemers en partners. Gedeelde aspecten omvatten mechanismen voor het weer geven van beheerders rollen, het toewijzen van rollen en controle activiteiten.

U kunt rollen toewijzen om te bepalen wie bepaalde beheer acties in Azure AD B2C kan uitvoeren, waaronder:

* Alle aspecten van gebruikers stromen maken en beheren
* Het kenmerk schema maken en beheren dat beschikbaar is voor alle gebruikers stromen
* Id-providers configureren voor gebruik in directe Federatie
* Beleids regels voor vertrouwens relaties maken en beheren in het Framework voor identiteits ervaring (aangepast beleid)
* Geheimen voor Federatie en versleuteling beheren in het Framework voor identiteits ervaring (aangepast beleid)

Zie [beheerders rollen in azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over Azure AD-functies, waaronder ondersteuning voor Azure AD B2C-beheerdersrol.

### <a name="multi-factor-authentication-mfa"></a>Meervoudige verificatie (MFA)

Azure AD B2C multi-factor Authentication (MFA) helpt u bij het beveiligen van de toegang tot gegevens en toepassingen terwijl u eenvoud hebt voor uw gebruikers. Het biedt extra beveiliging door een tweede vorm van verificatie te vereisen en biedt sterke verificatie door een bereik aan gebruiks vriendelijke verificatie methoden te bieden. Uw gebruikers worden mogelijk niet voor MFA aangevraagd op basis van de configuratie beslissingen die u als beheerder kunt nemen.

Lees hoe u MFA inschakelt in gebruikers stromen in [multi-factor Authentication inschakelen in azure Active Directory B2C](custom-policy-multi-factor-authentication.md).

### <a name="smart-account-lockout"></a>Smart account vergren delen

Azure AD B2C maakt gebruik van een geavanceerde strategie om accounts te vergren delen op basis van het IP-adres van de aanvraag, de ingevoerde wacht woorden en verschillende andere factoren om te voor komen dat het wacht woord wordt geraden. De duur van de vergren deling wordt automatisch verhoogd op basis van het risico en het aantal pogingen.

![Account slimme vergren deling](media/technical-overview/smart-lockout1.png)

Zie [bedreigingen voor resources en gegevens beheren in azure Active Directory B2C](threat-management.md)voor meer informatie over het beheren van instellingen voor wachtwoord beveiliging.

### <a name="password-complexity"></a>Wachtwoordcomplexiteit

Tijdens de registratie of het opnieuw instellen van het wacht woord moeten uw gebruikers een wacht woord opgeven dat voldoet aan complexiteits regels. Azure AD B2C dwingt standaard een sterk wachtwoord beleid af. Azure AD B2C biedt ook configuratie opties voor het opgeven van de complexiteits vereisten van de wacht woorden die uw klanten gebruiken.

U kunt vereisten voor wachtwoord complexiteit configureren in zowel [gebruikers stromen](user-flow-password-complexity.md) als [aangepaste beleids regels](custom-policy-password-complexity.md).

## <a name="auditing-and-logs"></a>Controle en Logboeken

Azure AD B2C verzendt audit logboeken met activiteit gegevens over de resources, uitgegeven tokens en beheerders toegang. U kunt deze controle Logboeken gebruiken om de platform activiteiten te begrijpen en problemen op te sporen. Vermeldingen in het controle logboek zijn binnenkort beschikbaar nadat de activiteit die de gebeurtenis heeft gegenereerd zich voordoet.

In een audit logboek, dat beschikbaar is voor uw Azure AD B2C Tenant of voor een bepaalde gebruiker, kunt u informatie vinden, zoals:

* Activiteiten met betrekking tot de autorisatie van een gebruiker om toegang te krijgen tot B2C-bronnen (bijvoorbeeld een beheerder die toegang heeft tot een lijst met B2C-beleid)
* Activiteiten met betrekking tot adreslijst kenmerken die zijn opgehaald wanneer een beheerder zich aanmeldt met de Azure Portal
* Maken, lezen, bijwerken en verwijderen (ruwe) bewerkingen op B2C-toepassingen
* RUWE bewerkingen op sleutels die zijn opgeslagen in een B2C-sleutel container
* RUWE bewerkingen op B2C-bronnen (bijvoorbeeld beleids regels en identiteits providers)
* Validatie van gebruikers referenties en het uitgeven van tokens

![Controle logboek voor afzonderlijke gebruikers wordt weer gegeven in de Azure Portal](media/technical-overview/audit-log.png)

Zie [toegang tot Azure AD B2C audit logboeken](view-audit-logs.md)voor meer informatie over audit Logboeken.

### <a name="usage-insights"></a>Inzicht in het gebruik

Met Azure AD B2C kunt u ontdekken wanneer mensen zich registreren of aanmelden bij uw web-app, waar uw gebruikers zich bevinden en welke browsers en besturings systemen ze gebruiken. Door Azure-toepassing inzichten te integreren in Azure AD B2C met behulp van aangepast beleid, kunt u inzicht krijgen in de manier waarop gebruikers zich registreren, zich aanmelden, hun wacht woord opnieuw instellen of hun profiel bewerken. Met deze kennis kunt u gegevensgestuurde beslissingen nemen voor uw aanstaande ontwikkel cycli.

Meer informatie over usage Analytics vindt u in het [bijhouden van gebruikers gedrag in azure Active Directory B2C met behulp van Application Insights](analytics-with-application-insights.md).

## <a name="next-steps"></a>Volgende stappen

Nu u meer inzicht hebt in de functies en technische aspecten van Azure Active Directory B2C, kunt u aan de slag met de service door een B2C-Tenant te maken:

> [!div class="nextstepaction"]
> [Zelf studie: een Azure Active Directory B2C Tenant maken >](tutorial-create-tenant.md)