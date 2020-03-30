---
title: B2B-gebruikers toegang verlenen tot uw on-premises apps - Azure AD
description: Laat zien hoe u Cloud B2B-gebruikers toegang geeft tot on-premises apps met Azure AD B2B-samenwerking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 098f464b6af5f10866403e1cd1549d571d883ac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272802"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>B2B-gebruikers in Azure AD toegang verlenen tot uw on-premises toepassingen

Als organisatie die B2B-samenwerkingsmogelijkheden (Azure Active Directory) gebruikt om gastgebruikers van partnerorganisaties uit te nodigen voor uw Azure AD, u deze B2B-gebruikers nu toegang bieden tot on-premises apps. Deze on-premises apps kunnen SAML-gebaseerde verificatie of Integrated Windows Authentication (IWA) gebruiken met Kerberos constrained delegation (KCD).

## <a name="access-to-saml-apps"></a>Toegang tot SAML-apps

Als uw on-premises app SAML-gebaseerde verificatie gebruikt, u deze apps eenvoudig beschikbaar maken voor uw Azure AD B2B-samenwerkingsgebruikers via de Azure-portal.

U moet beide van de volgende handelingen doen:

- Integreer de SAML-app met behulp van de sjabloon voor niet-galerietoepassingen, zoals beschreven in [Het configureren van eenmalige aanmelding voor toepassingen die zich niet in de Azure Active Directory-toepassingsgalerie bevinden.](../manage-apps/configure-single-sign-on-non-gallery-applications.md) Let op wat u gebruikt voor de **URL-waarde aanmelding.**
-  Gebruik Azure AD-toepassingsproxy om de on-premises app te publiceren, waarbij **Azure Active Directory** is geconfigureerd als verificatiebron. Zie Toepassingen [publiceren met Azure AD-toepassingsproxy](../manage-apps/application-proxy-publish-azure-portal.md)voor instructies. 

   Wanneer u de instelling **Interne url configureert,** gebruikt u de aanmeldings-URL die u hebt opgegeven in de niet-galerietoepassingssjabloon. Op deze manier hebben gebruikers toegang tot de app van buiten de organisatiegrens. Application Proxy voert de SAML single sign-on uit voor de on-premises app.
 
   ![Toont interne URL en verificatie van on-premises app-instellingen](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Toegang tot IWA- en KCD-apps

Als u B2B-gebruikers toegang wilt bieden tot on-premises toepassingen die zijn beveiligd met geïntegreerde Windows-verificatie en beperkte delegering met Kerberos,, hebt u de volgende onderdelen nodig:

- **Verificatie via Azure AD Application Proxy**. B2B-gebruikers moeten zich kunnen verifiëren voor de on-premises toepassing. Hiervoor moet u de on-premises app publiceren via de Azure AD Application Proxy. Zie [Aan de slag met Application Proxy en installeer de connector](../manage-apps/application-proxy-enable.md) en Publicatie toepassingen met Azure AD Application [Proxy](../manage-apps/application-proxy-publish-azure-portal.md)voor meer informatie.
- **Autorisatie via een B2B-gebruikersobject in de on-premises directory**. De toepassing moet in staat zijn om gebruikerstoegangscontroles uit te voeren en toegang te verlenen tot de juiste bronnen. IWA en KCD vereisen dat een gebruikersobject in de on-premises Windows Server Active Directory deze autorisatie voltooit. Zoals beschreven in [Hoe eenmalige aanmelding met KCD werkt,](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works)heeft Application Proxy dit gebruikersobject nodig om zich voor te doen als de gebruiker en een Kerberos-token voor de app te krijgen. 

   Voor het B2B-gebruikersscenario zijn er twee methoden beschikbaar die u gebruiken om de gastgebruikersobjecten te maken die vereist zijn voor autorisatie in de on-premises map:

   - Microsoft Identity Manager (MIM) en de MIM-beheeragent voor Microsoft Graph. 
   - [Een PowerShell-script](#create-b2b-guest-user-objects-through-a-script-preview). Het gebruik van het script is een meer lichtgewicht oplossing die geen MIM vereist. 

Het volgende diagram biedt een overzicht op hoog niveau van hoe Azure AD Application Proxy en het genereren van het B2B-gebruikersobject in de on-premises directory samenwerken om B2B-gebruikers toegang te geven tot uw on-premises IWA- en KCD-apps. De genummerde stappen worden in detail beschreven onder het diagram.

![Diagram van MIM- en B2B-scriptoplossingen](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Een gebruiker van een partnerorganisatie (de Fabrikam huurder) wordt uitgenodigd bij de Contoso huurder.
2.  Er wordt een gastgebruikersobject gemaakt in de Contoso-tenant (bijvoorbeeld een gebruikersobject met@contoso.onmicrosoft.comeen UPN van guest_fabrikam.com#EXT# ).
3.  De Fabrikam-gast wordt geïmporteerd uit Contoso via MIM of via het B2B PowerShell-script.
4.  Een representatie of "voetafdruk" van het Fabrikam-gastgebruikersobject (Guest#EXT#) wordt gemaakt in de on-premises directory, Contoso.com, via MIM of via het B2B PowerShell-script.
5.  De gastgebruiker heeft toegang tot de on-premises applicatie, app.contoso.com.
6.  De verificatieaanvraag is geautoriseerd via Application Proxy, met behulp van Kerberos beperkte delegatie. 
7.  Omdat het gastgebruikersobject lokaal bestaat, is de verificatie geslaagd.

### <a name="lifecycle-management-policies"></a>Beleid voor levenscyclusbeheer

U de on-premises B2B-gebruikersobjecten beheren via het levenscyclusbeheerbeleid. Bijvoorbeeld:

- U MFA-beleid (multi-factor authentication) instellen voor de gastgebruiker, zodat MFA wordt gebruikt tijdens application proxy-verificatie. Zie [Voorwaardelijke toegang voor Gebruikers van B2B-samenwerkingsverbanden voor](conditional-access.md)meer informatie.
- Alle sponsoring, toegangsbeoordelingen, accountverificaties, enz. Als de cloudgebruiker bijvoorbeeld wordt verwijderd via uw levenscyclusbeheerbeleid, wordt de on-premises gebruiker ook verwijderd door MIM Sync of via Azure AD Connect-synchronisatie. Zie [Gasttoegang beheren met Azure AD-toegangsbeoordelingen](../governance/manage-guest-access-with-access-reviews.md)voor meer informatie .

### <a name="create-b2b-guest-user-objects-through-mim"></a>B2B-gastgebruikersobjecten maken via MIM

Zie [Azure AD business-to-business (B2B) samenwerking met Microsoft Identity Manager (MIM) 2016 SP1 met Azure Application Proxy](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario)voor informatie over het gebruik van MIM 2016 Service Pack 1 en de MIM-beheeragent voor Microsoft Graph om de gastgebruikersobjecten in de on-premises directory te maken.

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>B2B-gastgebruikersobjecten maken via een script (Voorbeeld)

Er is een PowerShell-voorbeeldscript beschikbaar dat u als uitgangspunt gebruiken om de gastgebruikersobjecten in uw on-premises Active Directory te maken.

U het script en het Readme-bestand downloaden vanuit het [Downloadcentrum.](https://www.microsoft.com/download/details.aspx?id=51495) Kies het **Script en Readme om Azure AD B2B-gebruikers op prem.zip-bestand te trekken.**

Voordat u het script gebruikt, moet u de vereisten en belangrijke overwegingen in het bijbehorende Readme-bestand bekijken. Begrijp ook dat het script alleen beschikbaar wordt gesteld als voorbeeld. Uw ontwikkelingsteam of partner moet het script aanpassen en bekijken voordat u het uitvoert.

## <a name="license-considerations"></a>Licentieoverwegingen

Zorg ervoor dat u over de juiste Client Access-licenties (CAL's) beschikt voor externe gastgebruikers die toegang hebben tot on-premises apps. Zie het gedeelte 'Externe connectors' van [clienttoegangslicenties en beheerlicenties](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx)voor meer informatie. Raadpleeg uw Microsoft-vertegenwoordiger of lokale reseller over uw specifieke licentiebehoeften.

## <a name="next-steps"></a>Volgende stappen

- [Azure Active Directory B2B-samenwerking voor hybride organisaties](hybrid-organizations.md)

- Zie [Uw on-premises mappen integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor een overzicht van Azure AD Connect.

