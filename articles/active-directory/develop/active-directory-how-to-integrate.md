---
title: Integreren met Azure Active Directory
description: Ontdek de voor delen van het integreren van uw toepassing met Azure Active Directory en ontvang resources voor functies zoals vereenvoudigde aanmelding, identiteits beheer, multi-factor Authentication en toegangs beheer.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 4eaca7350ac006a2fcf2eed8d3eb0cb8ef274841
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885698"
---
# <a name="integrating-with-azure-active-directory"></a>Integreren met Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

In dit artikel vindt u informatie over de voor delen van het integreren van uw toepassing met Azure Active Directory (Azure AD) en het ophalen van resources voor integratie. Azure AD biedt organisaties een identiteits beheer op ondernemings niveau voor Cloud toepassingen. Azure AD-integratie biedt uw gebruikers een gestroomlijnde aanmeldings ervaring en helpt uw toepassing te voldoen aan het IT-beleid.

## <a name="how-to-integrate"></a>Het integreren van

Er zijn verschillende manieren waarop uw toepassing kan worden geïntegreerd met Azure AD. Profiteer van zoveel of slechts enkele van deze scenario's als geschikt voor uw toepassing.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Ondersteuning voor Azure AD als een manier om u aan te melden bij uw toepassing

**Verminder de wrijving van het probleem en verminder de ondersteunings kosten.** Als u Azure AD gebruikt om u aan te melden bij uw toepassing, hebben uw gebruikers niet langer een naam en wacht woord om te onthouden. Als ontwikkelaar hebt u nog een minder wacht woord om op te slaan en te beveiligen. Het is niet mogelijk om verg eten wacht woorden opnieuw in te voeren. Dit kan alleen aanzienlijk zijn. Met Azure AD-bevoegdheden meldt u zich aan voor een aantal van de populairste Cloud toepassingen van de wereld, waaronder Office 365 en Microsoft Azure. Met honderden miljoenen gebruikers van miljoenen organisaties is uw gebruiker al aangemeld bij Azure AD. Meer informatie over het [toevoegen van ondersteuning voor Azure AD-aanmelding](authentication-scenarios.md).

**Vereenvoudig de registratie voor uw toepassing.**  Wanneer u zich aanmeldt voor uw toepassing, kan Azure AD essentiële informatie over een gebruiker verzenden, zodat u uw aanmeldings formulier vooraf kunt invullen of het volledig moet elimineren. Gebruikers kunnen zich aanmelden voor uw toepassing met behulp van hun Azure AD-account via een vertrouwde instemming-ervaring die vergelijkbaar is met die van sociale media en mobiele toepassingen. Elke gebruiker kan zich registreren en aanmelden bij een toepassing die is geïntegreerd met Azure AD zonder dat hiervoor een rol is vereist. Meer informatie over [het aanmelden van uw toepassing voor aanmelding bij een Azure ad-account](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Bladeren naar gebruikers, het inrichten van gebruikers beheren en de toegang tot uw toepassing controleren

**Blader naar gebruikers in de Directory.**  Gebruik de Microsoft Graph-API om gebruikers te helpen bij het zoeken en zoeken naar andere personen in hun organisatie bij het uitnodigen van anderen of het verlenen van toegang, in plaats van dat ze e-mail adressen moeten typen. Gebruikers kunnen bladeren door gebruik te maken van een vertrouwde Adresboek stijl interface, inclusief het weer geven van de details van de organisatie hiërarchie. Meer informatie over de [Microsoft Graph-API](https://docs.microsoft.com/graph/overview).

**Active Directory groepen en distributie lijsten opnieuw gebruiken die uw klant al beheert.**  Azure AD bevat de groepen die uw klant al gebruikt voor het distribueren van e-mail en het beheren van de toegang. Gebruik de Microsoft Graph-API om deze groepen opnieuw te gebruiken in plaats van uw klant te verplichten om een afzonderlijke set groepen in uw toepassing te maken en te beheren. Groeps informatie kan ook worden verzonden naar uw toepassing in aanmeldings tokens. Meer informatie over de [Microsoft Graph-API](https://docs.microsoft.com/graph/overview).

**Gebruik Azure AD om te bepalen wie toegang heeft tot uw toepassing.**  Beheerders en eigen aren van toepassingen in azure AD kunnen toegang tot toepassingen toewijzen aan specifieke gebruikers en groepen. Met de Microsoft Graph-API kunt u deze lijst lezen en gebruiken om het inrichten en het ongedaan maken van de inrichting van resources en toegang in uw toepassing te beheren.

**Gebruik Azure AD voor functies op basis van Access Control.**  Beheerders en eigen aren van toepassingen kunnen gebruikers en groepen toewijzen aan rollen die u definieert wanneer u uw toepassing registreert in azure AD. De functie gegevens worden naar uw toepassing verzonden in aanmeldings tokens en kunnen ook worden gelezen met behulp van de Microsoft Graph-API. Meer informatie over het [gebruik van Azure AD voor autorisatie](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Toegang krijgen tot het profiel van de gebruiker, de agenda, het e-mail adres, de contact personen, bestanden en meer

**Azure AD is de autorisatie server voor Office 365 en andere micro soft Business Services.**  Als u Azure AD ondersteunt om u aan te melden bij uw toepassing of ondersteuning te bieden voor het koppelen van uw huidige gebruikers accounts aan Azure AD-gebruikers accounts met behulp van OAuth 2,0, kunt u lees-en schrijf toegang aanvragen voor het profiel van een gebruiker, agenda, e-mail, contact personen, bestanden en andere informatie. U kunt eenvoudig gebeurtenissen naar de agenda van de gebruiker schrijven en bestanden in OneDrive lezen of schrijven. Meer informatie over [het verkrijgen van toegang tot de Office 365-api's](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promoot uw toepassing in Azure en Office 365 marketplaces

**Promoot uw toepassing tot miljoenen organisaties die al gebruikmaken van Azure AD.**  Gebruikers die deze markt plaatsen zoeken en bezoeken, maken al gebruik van een of meer Cloud Services, waardoor ze gekwalificeerde Cloud service-klanten zijn. Meer informatie over het promoten van uw toepassing in [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Wanneer gebruikers zich aanmelden voor uw toepassing, wordt deze weer gegeven in het Azure AD-toegangs venster en het start programma voor Office 365-apps.**  Gebruikers kunnen snel en eenvoudig terugkeren naar uw toepassing, waardoor de gebruikers betrokkenheid wordt verbeterd. Meer informatie over het [Azure AD-toegangs venster](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Beveiligde apparaat-naar-service-en service-to-service-communicatie

**Het gebruik van Azure AD voor het Identity Management van services en apparaten vermindert de code die u moet schrijven en maakt het mogelijk om de toegang te beheren.**  Services en apparaten kunnen tokens van Azure AD ophalen met behulp van OAuth en deze tokens gebruiken om toegang te krijgen tot Web-Api's. Met Azure AD kunt u voor komen dat complexe verificatie code wordt geschreven. Omdat de identiteiten van de services en apparaten zijn opgeslagen in azure AD, kan deze sleutels en intrekken op één plek beheren, in plaats van dat ze afzonderlijk in uw toepassing hoeven te worden uitgevoerd.

## <a name="benefits-of-integration"></a>Voor delen van integratie

De integratie met Azure AD is gebaseerd op voor delen waarvoor u geen extra code hoeft te schrijven.

### <a name="integration-with-enterprise-identity-management"></a>Integratie met Enter prise Identity Management

**Help uw toepassing te voldoen aan het IT-beleid.**  Organisaties integreren hun bedrijfsidentiteits beheersysteem met Azure AD, dus wanneer een persoon een organisatie verlaat, wordt de toegang tot uw toepassing automatisch verbroken zonder dat er extra stappen hoeven te worden ondernomen. Hiermee kan worden beheerd wie toegang heeft tot uw toepassing en bepalen welk toegangs beleid vereist is, bijvoorbeeld multi-factor Authentication, waardoor code niet meer hoeft te worden geschreven om te voldoen aan complexe bedrijfs beleidsregels. Azure AD biedt beheerders een gedetailleerd controle logboek van wie zich heeft aangemeld bij uw toepassing, zodat het gebruik kan worden bijgehouden.

**Azure AD breidt Active Directory uit naar de Cloud, zodat uw toepassing kan worden geïntegreerd met AD.**  Veel organisaties over de hele wereld gebruiken Active Directory als hun belangrijkste aanmeld-en identiteits beheersysteem en vereisen dat hun toepassingen werken met AD. Integreren met Azure AD integreert uw app met Active Directory.

### <a name="advanced-security-features"></a>Geavanceerde beveiligings functies

**Multi-factor Authentication.**  Azure AD biedt systeem eigen multi-factor Authentication. IT-beheerders kunnen multi-factor Authentication vereisen voor toegang tot uw toepassing, zodat u deze ondersteuning niet zelf hoeft te coderen. Meer informatie over [multi-factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Afwijkende aanmeldings detectie.**  Azure AD verwerkt meer dan een miljard aanmeldingen per dag, terwijl u machine learning algoritmen gebruikt om verdachte activiteiten te detecteren en IT-beheerders op de hoogte te stellen van mogelijke problemen. Door de aanmelding van Azure AD te ondersteunen, haalt uw toepassing het voor deel van deze beveiliging op. Meer informatie over het [weer geven van Azure Active Directory Access-rapport](../active-directory-view-access-usage-reports.md).

**Voorwaardelijke toegang.**  Naast multi-factor Authentication kunnen beheerders specifieke voor waarden vereisen voordat gebruikers zich kunnen aanmelden bij uw toepassing. Voor waarden die kunnen worden ingesteld, zijn onder andere het IP-adres bereik van client apparaten, lidmaatschap van de opgegeven groepen en de status van het apparaat dat wordt gebruikt voor toegang. Meer informatie over [voorwaardelijke toegang van Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Eenvoudige ontwikkeling

**Industrie standaard protocollen.**  Micro soft hecht veel belang aan de ondersteuning van industrie normen. Het micro soft Identity-platform biedt ondersteuning voor de industrie standaard OAuth 2,0 en OpenID Connect Connect 1,0-protocollen. Meer informatie over [verificatie protocollen voor micro soft-identiteits platform](active-directory-v2-protocols.md).

**Open-source-bibliotheken.**  Micro soft biedt volledig ondersteunde open source-bibliotheken voor populaire talen en platforms om de ontwikkeling te versnellen. De bron code wordt in licentie gegeven onder Apache 2,0 en u kunt de projecten splitsen en er een bijdrage aan leveren. Meer informatie over [micro soft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Wereld wijde aanwezigheid en hoge Beschik baarheid

**Azure AD is geïmplementeerd in data centers over de hele wereld en wordt beheerd en gecontroleerd rond de klok.**  Azure AD is het identiteits beheersysteem voor Microsoft Azure en Office 365 en wordt geïmplementeerd in 28 data centers over de hele wereld. Directory gegevens worden gegarandeerd gerepliceerd naar ten minste drie data centers. Globale load balancers zorgen ervoor dat gebruikers toegang krijgen tot het dichtstbijzijnde exemplaar van Azure AD dat hun gegevens bevat, en aanvragen automatisch opnieuw routeren naar andere Data Centers als er een probleem wordt gedetecteerd.

## <a name="next-steps"></a>Volgende stappen

[Aan de slag](v2-overview.md#getting-started)met het schrijven van code.

[Gebruikers aanmelden met het micro soft Identity-platform](authentication-scenarios.md)

