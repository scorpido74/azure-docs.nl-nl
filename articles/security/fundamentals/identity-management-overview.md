---
title: Azure-beveiligingsfuncties die helpen bij identiteitsbeheer | Microsoft Documenten
description: " In dit artikel vindt u een overzicht van de belangrijkste Azure-beveiligingsfuncties die helpen bij identiteitsbeheer. Microsoft-oplossingen voor identiteits- en toegangsbeheer helpen IT de toegang tot toepassingen en resources in het bedrijfsdatacenter en in de cloud te beschermen, waardoor extra validatieniveaus mogelijk zijn, zoals multi-factorauthenticatie en voorwaardelijke toegang Beleid. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: f61b6193a0d2082296a17128b41d7220f9b7e05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565890"
---
# <a name="azure-identity-management-security-overview"></a>Overzicht van azure-identiteitsbeheerbeveiliging

 Identity management is het proces van het authenticeren en autoriseren van [beveiligingsprincipes.](/windows/security/identity-protection/access-control/security-principals) Het gaat ook om het controleren van informatie over deze opdrachtgevers (identiteiten). Beveiligingsprincipals (identiteiten) kunnen diensten, toepassingen, gebruikers, groepen, enz. Microsoft-oplossingen voor identiteits- en toegangsbeheer helpen IT de toegang tot toepassingen en bronnen in het bedrijfsdatacenter en in de cloud te beschermen. Deze beveiliging maakt extra validatieniveaus mogelijk, zoals multifactorauthenticatie en beleid voor voorwaardelijke toegang. Door verdachte activiteiten te controleren via geavanceerde beveiligingsrapportage, controle en waarschuwingen, u potentiële beveiligingsproblemen beperken. [Azure Active Directory Premium](/azure/active-directory/active-directory-editions) biedt single sign-on (SSO) aan duizenden cloudsoftware as a service (SaaS) apps en toegang tot webapps die u on-premises uitvoert.
 
Door gebruik te maken van de beveiligingsvoordelen van Azure Active Directory (Azure AD), u:

* Maak en beheer één enkele identiteit voor elke gebruiker in uw hybride onderneming, zodat gebruikers, groepen en apparaten gesynchroniseerd blijven. 
* Geef SSO toegang tot uw applicaties, waaronder duizenden vooraf geïntegreerde SaaS-apps.
* Toegangsbeveiliging tot toepassingen inschakelen door Multi-Factor Authentication op basis van regels af te dwingen voor zowel on-premises toepassingen als cloudtoepassingen.
* Informeer veilige externe toegang tot on-premises webtoepassingen via Azure AD Application Proxy.

Het doel van dit artikel is om een overzicht te geven van de belangrijkste Azure-beveiligingsfuncties die helpen bij identiteitsbeheer. We bieden ook links naar artikelen die details van elke functie geven, zodat u meer weten.  

Het artikel richt zich op de volgende kernmogelijkheden voor Azure Identity Management:

* Eenmalige aanmelding
* Omgekeerde proxy
* Multi-Factor Authentication
* Op rollen gebaseerdtoegangscontrole (RBAC)
* Beveiligingsbewaking, waarschuwingen en op machine learning gebaseerde rapporten
* Beheer van de identiteit en toegang van de consument
* Apparaatregistratie
* Privileged Identity Management
* Identiteitsbeveiliging
* Hybride identiteitsbeheer/Azure AD-verbinding
* Azure AD-toegangsbeoordelingen

## <a name="single-sign-on"></a>Eenmalige aanmelding

SSO betekent toegang hebben tot alle toepassingen en bronnen die u nodig hebt om zaken te doen, door u slechts één keer aan te melden met één gebruikersaccount. Eenmaal aangemeld, hebt u toegang tot alle toepassingen die u nodig hebt zonder dat u een tweede keer een wachtwoord hoeft te verifiëren (bijvoorbeeld een wachtwoord moet typen).

Veel organisaties vertrouwen op SaaS-toepassingen zoals Office 365, Box en Salesforce voor de productiviteit van gebruikers. In het verleden moesten IT-medewerkers gebruikersaccounts individueel maken en bijwerken in elke SaaS-toepassing en gebruikers moesten een wachtwoord onthouden voor elke SaaS-toepassing.

Azure AD breidt on-premises Active Directory-omgevingen uit naar de cloud, zodat gebruikers hun primaire organisatieaccount kunnen gebruiken om zich niet alleen aan te melden bij hun met een domein verbonden apparaten en bedrijfsbronnen, maar ook voor alle web- en SaaS-toepassingen die ze nodig hebben voor hun baan.

Niet alleen hoeven gebruikers niet meerdere sets gebruikersnamen en wachtwoorden te beheren, u ook automatisch toegang tot toepassingen in- of de-provisionen, op basis van hun organisatiegroepen en hun werknemersstatus. Azure AD introduceert besturingselementen voor beveiligings- en toegangsbeheer waarmee u de toegang van gebruikers in SaaS-toepassingen centraal beheren.

Meer informatie:

* [Overzicht van eenmalige aanmelding](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Azure Active Directory-enkele aanmelding integreren met SaaS-apps](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md)

## <a name="reverse-proxy"></a>Omgekeerde proxy

Met Azure AD Application Proxy u on-premises toepassingen publiceren, zoals [SharePoint-sites,](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)en [IIS-gebaseerde](https://www.iis.net/)apps binnen uw privénetwerk en biedt u veilige toegang tot gebruikers buiten uw netwerk. Application Proxy biedt externe toegang en SSO voor vele soorten on-premises webtoepassingen met de duizenden SaaS-toepassingen die Azure AD ondersteunt. Werknemers kunnen zich vanuit huis aanmelden bij uw apps op hun eigen apparaten en verifiëren via deze proxy in de cloud.

Meer informatie:

* [Azure AD-toepassingsproxy inschakelen](/azure/active-directory/manage-apps/application-proxy-enable)
* [Toepassingen publiceren met Azure AD-toepassingsproxy](/azure/active-directory/active-directory-application-proxy-publish)
* [Eenmalige aanmelding met toepassingsproxy](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Werken met voorwaardelijke toegang](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication is een verificatiemethode die het gebruik van meer dan één verificatiemethode vereist en een kritieke tweede beveiligingslaag toevoegt aan aanmeldingen en transacties van gebruikers. Multi-Factor Authentication helpt de toegang tot gegevens en toepassingen te waarborgen en tegelijkertijd te voldoen aan de vraag van gebruikers naar een eenvoudig aanmeldingsproces. Het levert sterke verificatie via een reeks verificatieopties: telefoongesprekken, sms-berichten of mobiele app-meldingen of verificatiecodes en OAuth-tokens van derden.

Meer informatie:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Wat is Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Hoe Multi-Factor Authentication werkt](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>RBAC

Op rollen gebaseerd toegangsbeheer is een machtigingssysteem dat is gebouwd op Azure Resource Manager dat een geavanceerd toegangsbeheer van resources in Azure biedt. Met RBAC u gedetailleerd het toegangsniveau bepalen dat gebruikers hebben. U bijvoorbeeld een gebruiker beperken om alleen virtuele netwerken te beheren en een andere gebruiker om alle resources in een resourcegroep te beheren. Azure bevat diverse ingebouwde rollen die u kunt gebruiken. Hier volgen vier fundamentele ingebouwde rollen. De eerste drie zijn op alle resourcetypen van toepassing.

Meer informatie:

* [Wat is toegangsbeheer op basis van rollen (RBAC)?](/azure/role-based-access-control/overview)
* [Ingebouwde rollen voor Azure-resources](/azure/role-based-access-control/built-in-roles)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Beveiligingsbewaking, waarschuwingen en op machine learning gebaseerde rapporten

Beveiligingsbewaking, waarschuwingen en op machine learning gebaseerde rapporten die inconsistente toegangspatronen identificeren, kunnen u helpen uw bedrijf te beschermen. U Azure AD-toegangs- en gebruiksrapporten gebruiken om inzicht te krijgen in de integriteit en beveiliging van de directory van uw organisatie. Met deze informatie kan een directorybeheerder beter bepalen waar mogelijke beveiligingsrisico's kunnen liggen, zodat ze adequaat kunnen plannen om die risico's te beperken.

In de Azure-portal vallen rapporten in de volgende categorieën:

* **Anomalierapporten**: Bevatten aanmeldingsgebeurtenissen die we afwijkend vonden. Ons doel is om u bewust te maken van dergelijke activiteiten en u in staat stellen om te bepalen of een gebeurtenis verdacht is.
* **Geïntegreerde toepassingsrapporten:** geef inzicht in hoe cloudtoepassingen in uw organisatie worden gebruikt. Azure AD biedt integratie met duizenden cloudtoepassingen.
* **Foutrapporten:** geef fouten aan die kunnen optreden wanneer u accounts indient voor externe toepassingen.
* **Gebruikersspecifieke rapporten**: Weergave van aanmeldingsactiviteitsgegevens van apparaten voor een specifieke gebruiker.
* **Activiteitslogboeken:** Bevat een record van alle gecontroleerde gebeurtenissen in de afgelopen 24 uur, afgelopen 7 dagen of laatste 30 dagen, en wijzigingen in groepsactiviteiten en wachtwoordreset en registratieactiviteit.

Meer informatie:

* [Uw toegangs- en gebruiksrapporten weergeven](/azure/active-directory/active-directory-view-access-usage-reports)
* [Aan de slag met Azure Active Directory-rapportage](/azure/active-directory/active-directory-reporting-getting-started)
* [Azure Active Directory-rapportagehandleiding](/azure/active-directory/active-directory-reporting-guide)

## <a name="consumer-identity-and-access-management"></a>Beheer van de identiteit en toegang van de consument

Azure AD B2C is een zeer beschikbare, wereldwijde, identiteitsbeheerservice voor toepassingen die gericht zijn op consumenten die worden geschaald naar honderden miljoenen identiteiten. De service kan worden geïntegreerd in zowel mobiele als webplatforms. Uw consumenten kunnen zich aanmelden bij al uw toepassingen door middel van aanpasbare ervaringen door gebruik te maken van hun bestaande sociale accounts of door het maken van nieuwe referenties.

In het verleden, applicatie-ontwikkelaars die wilden aanmelden klanten en meld ze in bij hun toepassingen zou hebben geschreven hun eigen code. Bovendien gebruikten ze on-premises databases of systemen om gebruikersnamen en wachtwoorden op te slaan. Azure AD B2C biedt uw organisatie een betere manier om identiteitsbeheer van consumenten te integreren in toepassingen met behulp van een veilig, op standaarden gebaseerd platform en een grote reeks uitbreidbaar beleid.

Wanneer u Azure AD B2C gebruikt, kunnen uw consumenten zich aanmelden voor uw toepassingen met behulp van hun bestaande sociale accounts (Facebook, Google, Amazon, LinkedIn) of door nieuwe referenties (e-mailadres en wachtwoord, of gebruikersnaam en wachtwoord) te maken.

Meer informatie:

* [Wat is Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C Preview: consumenten registreren en aanmelden bij uw toepassingen](../../active-directory-b2c/overview.md)
* [Azure Active Directory B2C Preview: typen toepassingen](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Apparaatregistratie

Azure AD-apparaatregistratie is de basis voor scenario's [voor voorwaardelijke toegang op](/azure/active-directory/active-directory-conditional-access-device-registration-overview) basis van apparaten. Wanneer een apparaat is geregistreerd, biedt Azure AD-apparaatregistratie het apparaat een identiteit die het gebruikt om het apparaat te verifiëren wanneer een gebruiker zich aanmeldt. Het geverifieerde apparaat en de kenmerken van het apparaat kunnen vervolgens worden gebruikt om beleid voor voorwaardelijke toegang af te dwingen voor toepassingen die worden gehost in de cloud en on-premises.

In combinatie met een oplossing voor het beheer van mobiele apparaten, zoals Intune, worden de apparaatkenmerken in Azure AD bijgewerkt met aanvullende informatie over het apparaat. U vervolgens regels voor voorwaardelijke toegang maken die de toegang vanaf apparaten afdwingen om te voldoen aan uw normen voor beveiliging en naleving.

Meer informatie:

* [Aan de slag met Azure AD-apparaatregistratie](/azure/active-directory/active-directory-conditional-access-device-registration-overview)
* [Automatische apparaatregistratie bij Azure AD voor Windows-apparaten die zijn verbonden aan het domein](/azure/active-directory/active-directory-conditional-access-automatic-device-registration)
* [Automatische registratie van Windows-apparaten die zijn verbonden met Azure AD instellen](/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Met Azure AD Privileged Identity Management u uw bevoorrechte identiteiten en toegang tot bronnen in Azure AD en andere Online Services van Microsoft, zoals Office 365 en Microsoft Intune, beheren, beheren en bewaken.

Gebruikers moeten soms bevoorrechte bewerkingen uitvoeren in Azure- of Office 365-bronnen of in andere SaaS-apps. Deze behoefte betekent vaak dat organisaties gebruikers permanent bevoorrechte toegang moeten geven in Azure AD. Dergelijke toegang is een groeiend beveiligingsrisico voor door de cloud gehoste resources, omdat organisaties niet voldoende kunnen controleren wat de gebruikers doen met hun beheerdersrechten. Bovendien, als een gebruikersaccount met bevoorrechte toegang is aangetast, kan die ene inbreuk van invloed zijn op de algehele cloudbeveiliging van de organisatie. Azure AD Privileged Identity Management helpt dit risico te beperken.

Met Azure AD Privileged Identity Management u het als nog niet doen:

* Bekijk welke gebruikers Azure AD-beheerders zijn.
* Schakel on-demand, just-in-time (JIT) administratieve toegang in tot Microsoft-services zoals Office 365 en Intune.
* Rapporten over beheerderstoegangsgeschiedenis en wijzigingen in beheerderstoewijzingen optezijn.
* Ontvang meldingen over toegang tot een bevoorrechte rol.

Meer informatie:

* [Wat is Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Azure AD-maprollen toewijzen in PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identiteitsbeveiliging

Azure AD Identity Protection is een beveiligingsservice die een geconsolideerd overzicht biedt van risicodetecties en potentiële kwetsbaarheden die van invloed zijn op de identiteit van uw organisatie. Identiteitsbeveiliging maakt gebruik van bestaande Azure AD-mogelijkheden voor anomaliedetectie, die beschikbaar zijn via Azure AD-anomalous Activity-rapporten. Identity Protection introduceert ook nieuwe typen risicodetectie die afwijkingen in realtime kunnen detecteren.

Meer informatie:

* [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview)
* [Kanaal 9: Azure AD en Identity Show: Preview voor identiteitsbescherming](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hybride identiteitsbeheer/Azure AD-verbinding

De identiteitsoplossingen van Microsoft omvatten mogelijkheden voor zowel on-premises als in de cloud. Er wordt één gebruikersidentiteit gemaakt voor verificatie en autorisatie bij alle resources, ongeacht hun locatie. We noemen dit hybride identiteit. Azure AD Connect is het programma van Microsoft dat is ontworpen om te voldoen aan uw doelstellingen voor een hybride identiteit. Hiermee kunt u uw gebruikers een algemene identiteit bieden voor Office 365, Azure en SaaS toepassingen die zijn geïntegreerd met Azure AD. Deze biedt de volgende functies:

* Synchronisatie
* AD FS en federatie-integratie
* Verificatie doorgeven
* Statuscontrole

Meer informatie:

* [Hybride identiteitswhitepaper](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Azure AD-teamblog](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Azure AD-toegangsbeoordelingen

Met Azure AD-toegangsbeoordelingen (Azure Active Directory) kunnen organisaties op efficiënte wijze groepslidmaatschappen, de toegang tot bedrijfstoepassingen en de toewijzing van bevoorrechte rollen beheren.

Meer informatie:

* [Azure AD-toegangsbeoordelingen](../../active-directory/governance/access-reviews-overview.md)
* [Gebruikerstoegang beheren met Azure AD-toegangsbeoordelingen](../../active-directory/governance/access-reviews-overview.md)
