---
title: Verificatiesessiebeheer configureren - Azure Active Directory
description: De configuratie van de Azure AD-verificatiesessie aanpassen, inclusief de gebruikersaanmeldingsfrequentie en de persistentie van de browsersessie.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9c0c88064c00c97de7dc58a500910e81c04eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263281"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Verificatiesessiebeheer configureren met voorwaardelijke toegang

In complexe implementaties hebben organisaties mogelijk de noodzaak om verificatiesessies te beperken. Sommige scenario's zijn mogelijk:

* Toegang tot resources vanaf een niet-beheerd of gedeeld apparaat
* Toegang tot gevoelige informatie van een extern netwerk
* Gebruikers met grote impact
* Kritieke bedrijfstoepassingen

Met de besturingselementen voor voorwaardelijke toegang u beleidsregels maken die specifieke use cases binnen uw organisatie targeten zonder dat dit gevolgen heeft voor alle gebruikers.

Voordat we in details duiken over het configureren van het beleid, laten we de standaardconfiguratie bekijken.

## <a name="user-sign-in-frequency"></a>Aanmeldingsfrequentie voor gebruikers

Aanmeldingsfrequentie definieert de periode voordat een gebruiker wordt gevraagd zich opnieuw aan te melden wanneer hij toegang probeert te krijgen tot een bron.

De standaardconfiguratie azure active directory (Azure AD) voor de frequentie van gebruikersaanmelding is een rollend venster van 90 dagen. Vragen gebruikers om referenties lijkt vaak een verstandig ding om te doen, maar het kan averechts werken: gebruikers die zijn opgeleid om hun geloofsbrieven in te voeren zonder na te denken kan onbedoeld leveren ze aan een kwaadaardige referentie prompt.

Het klinkt misschien alarmerend om een gebruiker niet te vragen om zich weer aan te melden, in werkelijkheid zal elke schending van het IT-beleid de sessie intrekken. Enkele voorbeelden zijn (maar zijn niet beperkt tot) een wachtwoordwijziging, een apparaat dat niet compatibel is of een account uitschakelen. U ook de sessies van gebruikers expliciet [intrekken met PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). De standaardconfiguratie van Azure AD komt neer op "vraag gebruikers niet om hun referenties op te geven als de beveiligingshouding van hun sessies niet is gewijzigd".

De inlogfrequentieinstelling werkt met apps die OAUTH2- of OIDC-protocollen hebben geïmplementeerd volgens de normen. De meeste Microsoft-native apps voor Windows, Mac en Mobile, waaronder de volgende webtoepassingen, voldoen aan de instelling.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- O365-beheerportal
- Exchange Online
- SharePoint en OneDrive
- Teams-webclient
- Dynamics CRM Online
- Azure Portal

### <a name="user-sign-in-frequency-and-device-identities"></a>Gebruikersaanmeldingsfrequentie en apparaatidentiteiten

Als Azure AD is toegetreden, hybride Azure AD-apparaten zijn toegetreden of azure AD-geregistreerde apparaten, wanneer een gebruiker zijn apparaat of deze interactie op interactieve wijze ontgrendelt, voldoet deze gebeurtenis ook aan het frequentiebeleid voor aanmeldingsfrequentie. In de volgende 2 voorbeelden is de aanmeldingsfrequentie van de gebruiker ingesteld op 1 uur:

Voorbeeld 1:

- Om 00:00 uur meldt een gebruiker zich aan bij zijn Windows 10 Azure AD-apparaat en begint hij te werken aan een document dat is opgeslagen in SharePoint Online.
- De gebruiker blijft een uur lang aan hetzelfde document op zijn apparaat werken.
- Om 01:00 uur wordt de gebruiker gevraagd zich opnieuw aan te melden op basis van de aanmeldingsfrequentievereiste in het beleid voor voorwaardelijke toegang dat is geconfigureerd door de beheerder.

Voorbeeld 2:

- Om 00:00 uur meldt een gebruiker zich aan bij zijn Windows 10 Azure AD-apparaat en begint hij te werken aan een document dat is opgeslagen in SharePoint Online.
- Om 00:30 staat de gebruiker op en neemt een pauze door zijn apparaat te vergrendelen.
- Om 00:45, de gebruiker keert terug van hun pauze en ontgrendelt het apparaat.
- Om 01:45 wordt de gebruiker gevraagd om zich opnieuw aan te melden op basis van de aanmeldingsfrequentievereiste in het beleid voor voorwaardelijke toegang dat is geconfigureerd door de beheerder sinds de laatste aanmelding plaatsvond om 00:45 uur.

## <a name="persistence-of-browsing-sessions"></a>Volharding van browsersessies

Een permanente browsersessie stelt gebruikers in staat om aangemeld te blijven na het sluiten en heropenen van hun browservenster.

Met de Azure AD-standaard voor het laten duren van browsersessies kunnen gebruikers op persoonlijke apparaten kiezen of ze de sessie willen voortduren door de instelling 'Aangemeld blijven? prompt na succesvolle verificatie. Als browserpersistentie is geconfigureerd in AD FS met behulp van de richtlijnen in het artikel [AD FS Single Sign-On Settings,](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)zullen we ons aan dat beleid houden en de Azure AD-sessie ook voortduren. U ook configureren of gebruikers in uw tenant de 'Aangemeld blijven' te zien krijgen. door de juiste instelling in het deelvenster bedrijfsbranding in azure-portal te wijzigen met behulp van de richtlijnen in het artikel [Uw aanmeldingspagina van Azure AD aanpassen](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Verificatiesessiebesturingselementen configureren

Voorwaardelijke toegang is een Azure AD Premium-mogelijkheid en vereist een premiumlicentie. Zie Wat is voorwaardelijke toegang in [Azure Active Directory als](overview.md#license-requirements) u meer wilt weten over voorwaardelijke toegang?

> [!WARNING]
> Als u de [configureerbare tokenlevensduurfunctie](../develop/active-directory-configurable-token-lifetimes.md) gebruikt die momenteel in openbare preview wordt gebruikt, bieden we geen ondersteuning voor het maken van twee verschillende beleidsregels voor dezelfde gebruikers- of app-combinatie: een met deze functie en een andere met configureerbare tokenlevensduurfunctie. Microsoft is van plan om de configureerbare tokenlevensduurfunctie op 1 mei 2020 in te trekken en deze te vervangen door de functie Voorwaardelijke toegangsverificatiesessiebeheer.  

### <a name="policy-1-sign-in-frequency-control"></a>Beleid 1: Aanmeldingsfrequentiebeheer

1. Nieuw beleid maken
1. Kies alle vereiste voorwaarden voor de omgeving van de klant, inclusief de doelcloud-apps.

   > [!NOTE]
   > Het wordt aanbevolen om een promptfrequentie voor gelijke verificatie in te stellen voor belangrijke Microsoft Office-apps zoals Exchange Online en SharePoint Online voor de beste gebruikerservaring.

1. Ga naar de**sessie** **Toegangsbesturingselementen** > en klik op **Aanmeldingsfrequentie**
1. Voer de vereiste waarde van dagen en uren in het eerste tekstvak in
1. Een waarde van **uren** of **dagen** selecteren in vervolgkeuzelijst
1. Uw beleid opslaan

![Voorwaardelijke toegangsbeleid geconfigureerd voor aanmeldingsfrequentie](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Op Azure AD-geregistreerde Windows-apparaten wordt aanmelden bij het apparaat beschouwd als een prompt. Als u bijvoorbeeld de aanmeldingsfrequentie hebt geconfigureerd tot 24 uur voor Office-apps, voldoen gebruikers op door Azure AD-apparaten geregistreerde Windows-apparaten aan het frequentiebeleid Aanmelden door zich aan te melden bij het apparaat en worden ze niet opnieuw gevraagd bij het openen van Office-apps.

Als u verschillende aanmeldingsfrequentie hebt geconfigureerd voor verschillende web-apps die in dezelfde browsersessie worden uitgevoerd, wordt het strengste beleid toegepast op beide apps omdat alle apps die in dezelfde browsersessie worden uitgevoerd, één sessietoken delen.

### <a name="policy-2-persistent-browser-session"></a>Beleid 2: Permanente browsersessie

1. Nieuw beleid maken
1. Kies alle vereiste voorwaarden.

   > [!NOTE]
   > Houd er rekening mee dat dit besturingselement vereist om "Alle Cloud-apps" als voorwaarde te kiezen. Browsersessie persistentie wordt gecontroleerd door verificatie sessie token. Alle tabbladen in een browsersessie delen één sessietoken en daarom moeten ze allemaal de persistentiestatus delen.

1. Ga naar De**Sessie** **Toegangsbesturingselementen** > en klik op **Permanente browsersessie**
1. Een waarde selecteren in vervolgkeuzelijst
1. U opslaan in beleid

![Beleid voor voorwaardelijke toegang geconfigureerd voor permanente browser](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Permanente browsersessieconfiguratie in Azure AD Conditional Access overschrijft de 'Aangemeld blijven?' instelling in het deelvenster Bedrijfsbranding in de Azure-portal voor dezelfde gebruiker als u beide beleidsregels hebt geconfigureerd.

## <a name="validation"></a>Validatie

Gebruik het gereedschap Wat als om een aanmelding van de gebruiker op de doeltoepassing en andere voorwaarden te simuleren op basis van hoe u uw beleid hebt geconfigureerd. De besturingselementen voor verificatiesessiebeheer worden weergegeven in het resultaat van het hulpprogramma.

![Voorwaardelijke toegang wat als de resultaten van het hulpprogramma](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Implementatie van beleid

Om ervoor te zorgen dat uw beleid werkt zoals verwacht, is de aanbevolen best practice om het te testen voordat u het uitrolt in productie. Gebruik in het ideale idealiter een testtenant om te controleren of uw nieuwe beleid werkt zoals bedoeld. Zie het artikel [Aanbevolen procedures voor voorwaardelijke toegang in Azure Active Directory voor](best-practices.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie het artikel Mfa vereisen voor specifieke apps [met Voorwaardelijke toegang met Azure Active Directory Conditional Access](app-based-mfa.md)als u wilt weten hoe u een beleid voor voorwaardelijke toegang configureren.
* Zie het artikel Aanbevolen procedures voor voorwaardelijke toegang [in Azure Active Directory](best-practices.md)als u klaar bent om beleid voor voorwaardelijke toegang voor uw omgeving te configureren.
