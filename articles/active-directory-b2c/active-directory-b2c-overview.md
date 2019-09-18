---
title: Wat is Azure Active Directory B2C? | Microsoft Docs
description: Ontdek hoe u identiteiten kunt maken en beheren, zoals voor registreren en aanmelden, en voor profielbeheer in uw toepassing met behulp van Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ca636079439f811a887d16b627473e7d73930799
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065696"
---
# <a name="what-is-azure-active-directory-b2c"></a>Wat is Azure Active Directory B2C?

Azure Active Directory B2C (Azure AD B2C) is een identiteits beheer service van Business-to-consumer. Met deze service kunt u aanpassen en beheren hoe gebruikers veilig kunnen omgaan met uw web-, bureaublad- en mobiele toepassingen of toepassingen met één pagina. Met Azure AD B2C kunnen gebruikers zich registreren, aanmelden, kunnen ze wachtwoorden herstellen en profielen bewerken. In Azure AD B2C is een formulier geïmplementeerd van de OpenID Connect- en OAuth 2.0-protocollen. De belangrijke sleutel in de implementatie van deze protocollen wordt gevormd door de beveiligingstokens en de bijbehorende claims waarmee u veilige toegang hebt tot resources.

Een *gebruikersbeleving* is een aanvraag waarmee een beleid wordt opgegeven dat het gedrag controleert van de wijze waarop de gebruiker en uw toepassing met Azure AD B2C omgaan. Er zijn twee paden beschikbaar voor het definiëren van gebruikersbelevingen in Azure AD B2C.

Als u een app-ontwikkelaar bent met of zonder ervaring met identiteiten, kunt u desgewenst in de Azure-portal algemene identiteitsgebruikersstromen definiëren. Als u een professional bent op het gebied van identiteiten, een systeemintegrator, een consultant of een in-house identiteitsteam, ervaring hebt met OpenID Connect-stromen en kennis hebt van id-providers en verificatie op basis van claims, dan kunt u kiezen voor aangepaste beleidsregels op basis van XML.

Voordat u een gebruikersbeleving gaat definiëren, dient u een Azure AD B2C-tenant te maken en uw toepassing en API in de tenant te registreren. Als u deze taken hebt uitgevoerd, kunt u beginnen met het definiëren van een gebruikersbeleving met gebruikersstromen of aangepaste beleidsregels. Desgewenst kunt u id-providers toevoegen of wijzigen of de manier waarop de gebruiker de beleving ervaart, aanpassen.

## <a name="protocols-and-tokens"></a>Protocollen en tokens

Azure AD B2C ondersteunt de [OpenID Connect- en OAuth 2.0-protocollen](active-directory-b2c-reference-protocols.md) voor gebruikersbelevingen. In de Azure AD B2C-implementatie van OpenID Connect wordt deze gebruikerservaring via de toepassing gestart door verificatieaanvragen te verzenden naar Azure AD B2C.

Het resultaat van een aanvraag bij Azure AD B2C is een beveiligingstoken, zoals een [id-token of toegangstoken](active-directory-b2c-reference-tokens.md). Met dit beveiligingstoken wordt de identiteit van de gebruiker gedefinieerd. Tokens worden ontvangen van Azure AD B2C-eindpunten, zoals een `/token`- of `/authorize`-eindpunt. Met deze tokens kunt u claims openen waarmee u een identiteit kunt valideren en toegang verlenen tot veilige resources.

## <a name="tenants-and-applications"></a>Tenants en toepassingen

In Azure AD B2C bestaat een *tenant* uit een adreslijst of directory met gebruikers en vertegenwoordigt deze zo uw organisatie. Elke Azure AD B2C-tenant is uniek en staat volledig los van andere Azure AD B2C-tenants. Mogelijk hebt u al een Azure Active Directory-tenant. De Azure AD B2C-tenant is een andere tenant. Een tenant bevat gegevens over de gebruikers die zich hebben geregistreerd om uw toepassing te gebruiken. Denk hierbij aan gegevens zoals wachtwoorden, profielgegevens en machtigingen. Zie [Zelfstudie: Een Azure Active Directory B2C-tenant maken](tutorial-create-tenant.md).

Voordat u uw toepassing configureert voor het gebruik van Azure AD B2C, moet u deze eerst via de Azure-portal in de tenant registreren. Tijdens het registratieproces worden er waarden verzameld en toegewezen aan uw toepassing. Deze waarden bevatten een toepassings-id waarmee de toepassing op unieke wijze wordt gedefinieerd, en een URI waarmee antwoorden naar de toepassing worden teruggeleid.

De interactie van elke toepassing volgt in grote lijnen hetzelfde patroon:

1. De toepassing zorgt ervoor dat de gebruiker een beleid uitvoert.
2. De gebruiker voltooit het beleid volgens de beleidsdefinitie.
3. De toepassing ontvangt een token.
4. De toepassing gebruikt het token om te proberen toegang te krijgen tot een resource.
5. De resource-server valideert het token om te controleren of toegang kan worden verleend.
6. Het token wordt regelmatig vernieuwd door de toepassing.

Als u een webtoepassing wilt registreren, voert u de stappen uit in [Zelfstudie: een toepassing registreren om registratie en aanmelding in te schakelen in Azure AD B2C](tutorial-register-applications.md). U kunt ook [een web-API-toepassing toevoegen aan uw Azure Active Directory B2C-tenant](add-web-application.md) of [een systeemeigen clienttoepassing toevoegen aan uw Azure Active Directory B2C-tenant](add-native-application.md).

## <a name="user-journeys"></a>Gebruikersbelevingen

Het beleid in een gebruikersbeleving kan worden gedefinieerd als een [gebruikersstroom](active-directory-b2c-reference-policies.md) of als [aangepast beleid](active-directory-b2c-overview-custom.md). Vooraf gedefinieerde gebruikersstromen voor de meestvoorkomende identiteitstaken, zoals registratie, aanmelding en het bewerken van profielen, zijn beschikbaar in de Azure-portal.

Met een gebruikersbeleving kunt u gedrag bepalen door de volgende instellingen te configureren:

- Social media-accounts die de gebruiker gebruikt om zich te registreren voor de toepassing
- Gegevens die van de gebruiker worden gevraagd, zoals voornaam of postcode
- Multi-Factor Authentication
- Uiterlijk van pagina's
- Gegevens die worden geretourneerd naar de toepassing

Aangepaste beleidsregels zijn configuratiebestanden die het gedrag van het [Identity Experience Framework](trustframeworkpolicy.md) in de Azure AD B2C-tenant definiëren. Identity Experience Framework is het onderliggende platform dat een vertrouwensrelatie opzet tussen meerdere partijen en de stappen in een gebruikersbeleving uitvoert.

Aangepaste beleidsregels kunnen worden gewijzigd voor het voltooien van allerlei taken. Een aangepast beleid bestaat uit een of meer XML-bestanden die in een hiërarchische keten naar elkaar verwijzen. Er is een [beginnerspakket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) beschikbaar voor aangepaste beleidsregels voor het inschakelen van algemene identiteitstaken.

Er worden zo nodig aangepaste beleidsregels of gebruikersstromen van verschillende typen in de Azure AD B2C-tenant gebruikt.Deze kunnen in toepassingen worden hergebruikt. Deze flexibiliteit zorgt ervoor dat u gebruikersidentiteitservaringen kunt definiëren en aanpassen met geen of minimale wijzigingen van uw code. Beleidsregels worden gebruikt door een speciale queryparameter toe te voegen aan HTTP-verificatieaanvragen. Zie [Aan de slag met aangepaste beleidsregels in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md) als u uw eigen aangepaste beleidsregels wilt maken.

## <a name="identity-providers"></a>Id-providers

Het is wellicht wenselijk dat uw gebruikers zich in uw toepassingen bij verschillende id-providers kunnen aanmelden. Een *id-provider* maakt, onderhoudt en beheert id-gegevens en biedt tegelijkertijd verificatieservices voor toepassingen. Via de Azure-portal kunt u id-providers toevoegen die worden ondersteund door Azure AD B2C.

Gewoonlijk gebruikt u slechts één id-provider in uw toepassing, maar u kunt er eventueel meer toevoegen. Als u een id-provider wilt configureren in uw Azure AD B2C-tenant, maakt u eerst een toepassing op de ontwikkelaarssite van de id-provider en daarna legt u de toepassings-id of client-id vast, evenals het wachtwoord of het clientgeheim van de id-providertoepassing die u maakt. Deze id en wachtwoord worden vervolgens gebruikt om uw toepassing te configureren.

In de volgende artikelen worden de stappen beschreven voor het toevoegen van een aantal veelvoorkomende id-providers aan gebruikersstromen:

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Microsoft-account](active-directory-b2c-setup-msa-app.md)

In de volgende artikelen worden de stappen beschreven voor het toevoegen van een aantal veelvoorkomende id-providers aan aangepaste beleidsregels:
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Microsoft-account](active-directory-b2c-custom-setup-msa-idp.md)

Zie [Zelfstudie: Id-providers toevoegen aan uw toepassingen in Azure Active Directory B2C](tutorial-add-identity-providers.md).


## <a name="page-customization"></a>Pagina-aanpassing

De meeste HTML- en CSS-inhoud die klanten in een gebruikersbeleving krijgen gepresenteerd, kan worden beheerd. Door gebruik te maken van pagina-aanpassing, kunt u het uiterlijk van elke aangepaste beleidsregel of gebruikersstroom aanpassen. U zorgt er ook voor dat er visuele consistentie en merkconsistentie bestaat tussen de toepassing en Azure AD B2C door deze aanpassingsfunctie te gebruiken.

Azure AD B2C voert code uit in de browser van de gebruiker en maakt gebruik van een moderne aanpak die Cross-Origin Resource Sharing (CORS) wordt genoemd. U geeft eerst een URL op in een beleid met aangepaste HTML-inhoud. Azure AD B2C combineert elementen van de gebruikersinterface met de HTML-inhoud die vanaf de URL wordt geladen en presenteert vervolgens de pagina aan de gebruiker.

U verzendt parameters naar Azure AD B2C in een queryreeks. Door de parameter door te geven aan het HTML-eindpunt wordt de pagina-inhoud dynamisch gewijzigd. U wijzigt bijvoorbeeld de achtergrondafbeelding op de registratie- of aanmeldingspagina op basis van een parameter die u doorgeeft vanuit uw webtoepassing of mobiele toepassing.

Als u pagina's in een gebruikersstroom wilt aanpassen, raadpleegt u [Zelfstudie: de interface van de gebruikerservaring in Azure Active Directory B2C aanpassen](tutorial-customize-ui.md). Zie [De gebruikersinterface van uw toepassing met behulp van een aangepaste beleidsregel in Azure Active Directory B2C aanpassen](active-directory-b2c-ui-customization-custom.md) of [De gebruikersinterface met dynamische inhoud configureren met behulp van aangepaste beleidsregels in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md) als u pagina's in een aangepaste beleidsregel wilt aanpassen.

## <a name="developer-resources"></a>Resources voor ontwikkelaars

### <a name="client-applications"></a>Clienttoepassingen

U kunt kiezen uit toepassingen voor onder meer [iOS](active-directory-b2c-devquickstarts-ios.md), [Android](active-directory-b2c-devquickstarts-android.md) en .NET. Met Azure AD B2C kunt u deze acties mogelijk maken en tegelijkertijd de gebruikers-id's beschermen.

Als u een ontwikkelaar van ASP.NET-webtoepassingen bent, kunt u uw toepassing instellen voor het verifiëren van accounts met behulp van de stappen in [Zelfstudie: verificatie inschakelen in een webtoepassing met behulp van Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

Als u een ontwikkelaar van bureaubladtoepassingen bent, kunt u uw toepassing instellen voor het verifiëren van accounts met behulp van de stappen in [Zelfstudie: verificatie inschakelen in een bureaubladtoepassing met behulp van Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

Als u een ontwikkelaar van toepassingen met één pagina bent, kunt u uw toepassing instellen voor het verifiëren van accounts met behulp van de stappen in [Zelfstudie: verificatie inschakelen in een webtoepassing met één pagina met behulp van Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).

### <a name="apis"></a>API's
Als uw client- of webtoepassingen API's moeten kunnen aanroepen, kunt u voor deze resources beveiligde toegang instellen in Azure AD B2C.

Als u een ontwikkelaar van ASP.NET-webtoepassingen bent, kunt u uw toepassing instellen voor het aanroepen van een beveiligde API met behulp van de stappen in [Zelfstudie: toegang verlenen aan een web-API van ASP.NET met behulp van Azure Active Directory B2C](active-directory-b2c-tutorials-web-api.md).

Als u een ontwikkelaar van bureaubladtoepassingen bent, kunt u uw toepassing instellen voor het aanroepen van een beveiligde API met behulp van de stappen in [Zelfstudie: toegang verlenen aan een web-API van Node.js vanuit een bureaublad-app met behulp van Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app-webapi.md).

Als u een ontwikkelaar van toepassingen met één pagina bent, kunt u uw toepassing instellen voor het verifiëren van accounts met behulp van de stappen in [Zelfstudie: toegang verlenen aan een web-API van ASP.NET Core vanuit een toepassing met één pagina met behulp van Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md).

### <a name="javascript"></a>JavaScript

U kunt uw eigen JavaScript-code aan de clientzijde toevoegen aan uw toepassingen in Azure AD B2C. Als u Java script wilt instellen in uw toepassing, definieert u een [pagina-indeling](page-layout.md) en schakelt u [Java script](javascript-samples.md) in uw gebruikers stromen of aangepast beleid in.

### <a name="user-accounts"></a>Gebruikersaccounts

Veel algemene tenantbeheertaken moeten programmatisch worden uitgevoerd. Een goed voorbeeld is gebruikersbeheer. U wilt bijvoorbeeld een bestaand gebruikersarchief naar een Azure AD B2C-tenant migreren. Of u wilt op de achtergrond gebruikersregistratie op uw eigen pagina hosten en gebruikersaccounts maken in uw Azure AD B2C-adreslijst. Voor dit soort taken moet u gebruikersaccounts kunnen maken, lezen, bijwerken en verwijderen. U kunt deze taken uitvoeren met behulp van de [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

## <a name="next-steps"></a>Volgende stappen

Ga verder met de volgende zelfstudie om uw toepassing te configureren voor de registratie- en aanmeldingservaring.

> [!div class="nextstepaction"]
> [Zelfstudie: Een Azure Active Directory B2C-tenant maken](tutorial-create-tenant.md)
