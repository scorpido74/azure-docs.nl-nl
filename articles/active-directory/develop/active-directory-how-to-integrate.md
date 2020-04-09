---
title: Integreren met Azure Active Directory
description: Ontdek de voordelen van het integreren van uw toepassing met Azure Active Directory en krijg resources voor functies zoals vereenvoudigde aanmelding, identiteitsbeheer, meervoudige verificatie en toegangscontrole.
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885698"
---
# <a name="integrating-with-azure-active-directory"></a>Integreren met Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

In dit artikel vindt u meer informatie over de voordelen van het integreren van uw toepassing met Azure Active Directory (Azure AD) en bronnen voor integratie. Azure AD biedt organisaties bedrijfswaardig identiteitsbeheer voor cloudtoepassingen. Azure AD-integratie biedt uw gebruikers een gestroomlijnde aanmeldingservaring en helpt uw toepassing te voldoen aan het IT-beleid.

## <a name="how-to-integrate"></a>Het integreren van

Er zijn verschillende manieren waarop uw toepassing kan worden geïntegreerd met Azure AD. Profiteer van zo veel of zo weinig van deze scenario's als geschikt is voor uw toepassing.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Azure AD ondersteunen als een manier om u aan te melden bij uw toepassing

**Verminder de wrijving tussen aanmelding en verlaag de ondersteuningskosten.** Als u Azure AD gebruikt om zich aan te melden bij uw toepassing, hebben uw gebruikers niet meer één naam en wachtwoord om te onthouden. Als ontwikkelaar heb je één wachtwoord minder om op te slaan en te beschermen. Het niet hoeven omgaan met vergeten wachtwoordresets kan alleen al een aanzienlijke besparing zijn. Azure AD-bevoegdheden aanmelden voor enkele van 's werelds meest populaire cloudtoepassingen, waaronder Office 365 en Microsoft Azure. Met honderden miljoenen gebruikers van miljoenen organisaties is de kans groot dat uw gebruiker al is aangemeld bij Azure AD. Meer informatie over [het toevoegen van ondersteuning voor Azure AD-aanmelding](authentication-scenarios.md).

**Vereenvoudig het aanmelden voor uw aanvraag.**  Tijdens het aanmelden voor uw toepassing kan Azure AD essentiële informatie over een gebruiker verzenden, zodat u uw aanmeldingsformulier vooraf invullen of volledig elimineren. Gebruikers kunnen zich aanmelden voor uw toepassing met behulp van hun Azure AD-account via een vertrouwde toestemmingservaring die vergelijkbaar is met die in sociale media en mobiele toepassingen. Elke gebruiker kan zich aanmelden en zich aanmelden bij een toepassing die is geïntegreerd met Azure AD zonder dat er IT-betrokkenheid nodig is. Meer informatie over [het aanmelden van uw toepassing voor het aanmelden van Azure AD-account](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Zoeken naar gebruikers, gebruikersinrichting beheren en de toegang tot uw toepassing beheren

**Blader naar gebruikers in de directory.**  Gebruik de Microsoft Graph API om gebruikers te helpen zoeken en zoeken naar andere mensen in hun organisatie wanneer ze anderen uitnodigen of toegang verlenen, in plaats van te eisen dat ze e-mailadressen typen. Gebruikers kunnen bladeren met behulp van een bekende adresboekstijl interface, inclusief het bekijken van de details van de organisatorische hiërarchie. Meer informatie over de [Microsoft Graph API](https://docs.microsoft.com/graph/overview).

**Gebruik Active Directory-groepen en distributielijsten die uw klant al beheert.**  Azure AD bevat de groepen die uw klant al gebruikt voor e-maildistributie en beheer van toegang. Gebruik deze groepen opnieuw met de Microsoft Graph API in plaats van dat uw klant een afzonderlijke set groepen in uw toepassing moet maken en beheren. Groepsinformatie kan ook worden verzonden naar uw aanvraag in sign in tokens. Meer informatie over de [Microsoft Graph API](https://docs.microsoft.com/graph/overview).

**Gebruik Azure AD om te bepalen wie toegang heeft tot uw toepassing.**  Beheerders en toepassingseigenaren in Azure AD kunnen toegang tot toepassingen toewijzen aan specifieke gebruikers en groepen. Met de Microsoft Graph API u deze lijst lezen en gebruiken om de inrichting en devoorziening van resources en toegang binnen uw toepassing te beheren.

**Gebruik Azure AD voor toegangsbeheer op basis van rollen.**  Beheerders en toepassingseigenaren kunnen gebruikers en groepen toewijzen aan rollen die u definieert wanneer u uw toepassing registreert in Azure AD. Rolgegevens worden verzonden naar uw inlogtokens voor aanmelding smeren en kunnen ook worden gelezen met behulp van de Microsoft Graph API. Meer informatie over [het gebruik van Azure AD voor autorisatie](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Krijg toegang tot het profiel, de agenda, de e-mail, de contactpersonen, bestanden en meer van de gebruiker

**Azure AD is de autorisatieserver voor Office 365 en andere Microsoft Business-services.**  Als u Azure AD ondersteunt voor aanmelden bij uw toepassing of ondersteuning bij het koppelen van uw huidige gebruikersaccounts aan Azure AD-gebruikersaccounts met OAuth 2.0, u lees- en schrijftoegang aanvragen voor het profiel, de agenda, de agenda, e-mail, contactpersonen, bestanden en andere informatie van een gebruiker. U naadloos gebeurtenissen naar de agenda van de gebruiker schrijven en bestanden lezen of schrijven naar hun OneDrive. Meer informatie over [toegang tot de Office 365-API's](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Uw toepassing promoten in de Azure- en Office 365-marktplaatsen

**Promoot uw toepassing voor de miljoenen organisaties die Azure AD al gebruiken.**  Gebruikers die deze marktplaatsen doorzoeken en doorbladeren, maken al gebruik van een of meer cloudservices, waardoor ze gekwalificeerde cloudserviceklanten zijn. Meer informatie over het promoten van uw toepassing in [de Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Wanneer gebruikers zich aanmelden voor uw toepassing, wordt deze weergegeven in het Azure AD-toegangspaneel en het startprogramma voor Office 365-apps.**  Gebruikers kunnen later snel en eenvoudig terugkeren naar uw toepassing, waardoor de betrokkenheid van gebruikers wordt verbeterd. Meer informatie over het [Azure AD-toegangspaneel](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Veilige service-tot-service en service-to-service communicatie

**Het gebruik van Azure AD voor identiteitsbeheer van services en apparaten vermindert de code die u moet schrijven en stelt IT in staat om toegang te beheren.**  Services en apparaten kunnen tokens van Azure AD krijgen met OAuth en deze tokens gebruiken om toegang te krijgen tot web-API's. Met Azure AD u voorkomen dat u complexe verificatiecode schrijft. Aangezien de identiteiten van de services en apparaten zijn opgeslagen in Azure AD, kan IT sleutels en intrekking op één plaats beheren in plaats van dit afzonderlijk in uw toepassing te moeten doen.

## <a name="benefits-of-integration"></a>Voordelen van integratie

Integratie met Azure AD brengt voordelen met zich mee waarvoor u geen extra code hoeft te schrijven.

### <a name="integration-with-enterprise-identity-management"></a>Integratie met enterprise identity management

**Help uw toepassing te voldoen aan het IT-beleid.**  Organisaties integreren hun enterprise identity management systemen met Azure AD, dus wanneer een persoon een organisatie verlaat, verliezen ze automatisch de toegang tot uw toepassing zonder dat IT extra stappen hoeft te ondernemen. IT kan beheren wie toegang heeft tot uw toepassing en bepalen welk toegangsbeleid vereist is - bijvoorbeeld multi-factor authenticatie - waardoor u geen code hoeft te schrijven om te voldoen aan complexe bedrijfsbeleidsregels. Azure AD biedt beheerders een gedetailleerd controlelogboek van wie zich bij uw toepassing heeft aangemeld, zodat IT het gebruik kan bijhouden.

**Azure AD breidt Active Directory uit naar de cloud, zodat uw toepassing kan worden geïntegreerd met AD.**  Veel organisaties over de hele wereld gebruiken Active Directory als hun belangrijkste aanmeldings- en identiteitsbeheersysteem en vereisen dat hun toepassingen met AD werken. Integratie met Azure AD integreert uw app met Active Directory.

### <a name="advanced-security-features"></a>Geavanceerde beveiligingsfuncties

**Multi-factor authenticatie.**  Azure AD biedt native multi-factor authenticatie. IT-beheerders kunnen multi-factor authenticatie vereisen om toegang te krijgen tot uw toepassing, zodat u deze ondersteuning niet zelf hoeft te coderen. Meer informatie over [multifactorverificatie](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Afwijkende tekendetectie.**  Azure AD verwerkt meer dan een miljard aanmeldingen per dag, terwijl het gebruik van machine learning-algoritmen om verdachte activiteiten te detecteren en IT-beheerders op de hoogte te stellen van mogelijke problemen. Door azure AD-aanmelding te ondersteunen, krijgt uw toepassing het voordeel van deze bescherming. Meer informatie over [het weergeven van Azure Active Directory-toegangsrapport](../active-directory-view-access-usage-reports.md).

**Voorwaardelijke toegang.**  Naast multi-factor authenticatie kunnen beheerders eisen dat aan specifieke voorwaarden wordt voldaan voordat gebruikers zich kunnen aanmelden bij uw toepassing. Voorwaarden die kunnen worden ingesteld, zijn het IP-adresbereik van clientapparaten, het lidmaatschap in bepaalde groepen en de status van het apparaat dat wordt gebruikt voor toegang. Meer informatie over [voorwaardelijke toegang van Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Eenvoudige ontwikkeling

**Industrie standaard protocollen.**  Microsoft zet zich in voor het ondersteunen van industriestandaarden. Het Microsoft-identiteitsplatform ondersteunt de industriestandaard OAuth 2.0- en OpenID Connect 1.0-protocollen. Meer informatie over verificatieprotocollen voor [microsoft-identiteitsplatforms](active-directory-v2-protocols.md).

**Open source-bibliotheken.**  Microsoft biedt volledig ondersteunde open source-bibliotheken voor populaire talen en platforms om de ontwikkeling te versnellen. De broncode is gelicentieerd onder Apache 2.0, en je bent vrij om vork en terug te dragen aan de projecten. Meer informatie over [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Wereldwijde aanwezigheid en hoge beschikbaarheid

**Azure AD wordt wereldwijd geïmplementeerd in datacenters en wordt 24 uur per dag beheerd en bewaakt.**  Azure AD is het identiteitsbeheersysteem voor Microsoft Azure en Office 365 en wordt geïmplementeerd in 28 datacenters over de hele wereld. Directorygegevens worden gegarandeerd gerepliceerd naar ten minste drie datacenters. Globale load balancers zorgen ervoor dat gebruikers toegang krijgen tot de dichtstbijzijnde kopie van Azure AD met hun gegevens en automatisch aanvragen omleiden naar andere datacenters als er een probleem wordt gedetecteerd.

## <a name="next-steps"></a>Volgende stappen

[Aan de slag met het schrijven van code](v2-overview.md#getting-started).

[Gebruikers aanmelden met het Microsoft-identiteitsplatform](authentication-scenarios.md)

