---
title: 'Quickstart: Eenmalige aanmelding (SSO) instellen voor een toepassing in uw Azure Active Directory-tenant (Azure AD)'
description: In deze quickstart wordt het proces voor het instellen van eenmalige aanmelding (SSO) voor een toepassing in uw Azure Active Directory-tenant (Azure AD) besproken.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: f90edf525a8deb35376d6637090e7b424b2fd658
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86038987"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Eenmalige aanmelding (SSO) instellen voor een toepassing in uw Azure Active Directory-tenant (Azure AD)

Ga aan de slag met vereenvoudigde gebruikersaanmeldingen door eenmalige aanmelding (SSO) in te stellen voor een toepassing die u hebt toegevoegd aan uw Azure AD-tenant. Nadat u SSO hebt ingesteld, kunnen uw gebruikers zich aanmelden bij een toepassing met behulp van hun Azure AD-referenties. SSO is opgenomen in de gratis versie van Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u SSO wilt instellen voor een toepassing die u hebt toegevoegd aan uw Azure AD-tenant, hebt u het volgende nodig:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een van de volgende rollen: Globale beheerder, Cloudtoepassingsbeheerder, Toepassingsbeheerder of eigenaar van de service-principal.
- Een toepassing die ondersteuning biedt voor eenmalige aanmelding en die al vooraf is geconfigureerd en toegevoegd aan de Azure AD-galerie. De meeste apps kunnen Azure AD gebruiken voor eenmalige aanmelding. De apps in de Azure AD-galerie zijn vooraf geconfigureerd. Als uw app niet wordt vermeld, of een aangepaste ontwikkelde app is, kunt u deze nog steeds gebruiken met Azure AD. Bekijk de zelfstudies en andere documentatie in de inhoudsopgave. Deze quickstart is gericht op apps die vooraf zijn geconfigureerd voor SSO en die zijn toegevoegd aan de Azure AD-galerie door de app-ontwikkelaars.
- (Optioneel: Voltooiing van [Uw apps weergeven](view-applications-portal.md)).
- (Optioneel: Voltooiing van [Een app toevoegen](add-application-portal.md)).
- (Optioneel: Voltooiing van [Een app configureren](add-application-portal-configure.md)).


>[!IMPORTANT]
>U kunt het beste een niet-productieomgeving gebruiken om de stappen in deze quickstart te testen.


## <a name="enable-single-sign-on-for-an-app"></a>Eenmalige aanmelding inschakelen voor een app

Wanneer u klaar bent met het toevoegen van een toepassing aan uw Azure AD-tenant, wordt er onmiddellijk een overzichtspagina weergegeven. Als u een toepassing configureert die al is toegevoegd, kijkt u naar de eerste quickstart, waarin u leert hoe u de toepassingen weergeeft die aan uw tenant zijn toegevoegd. 

Eenmalige aanmelding voor een toepassing instellen:

1. Selecteer **Ondernemingstoepassingen** in Azure AD en zoek en selecteer vervolgens de toepassing die u wilt instellen voor eenmalige aanmelding.
2. Selecteer **Eenmalige aanmelding** in de sectie Beheren om het deelvenster Eigenschappen te openen voor bewerken.
    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Toont de configuratiepagina voor eenmalige aanmelding in de Azure AD-portal.":::
3. Selecteer SAML om de SSO-configuratiepagina te openen. In dit voorbeeld is GitHub de toepassing die wordt geconfigureerd voor eenmalige aanmelding. Nadat GitHub is ingesteld, kunnen uw gebruikers zich aanmelden bij GitHub met behulp van hun referenties van onze Azure AD-tenant.
    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Toont de configuratiepagina voor eenmalige aanmelding op GitHub.":::
4. Het proces voor het configureren van een toepassing om Azure AD te gebruiken voor SSO op basis van SAML is afhankelijk van de toepassing. U ziet een koppeling naar de richtlijnen voor GitHub. U kunt gidsen voor andere apps vinden op: https://docs.microsoft.com/azure/active-directory/saas-apps/
5. Volg de handleiding om SSO in te stellen voor de toepassing. Veel toepassingen hebben specifieke abonnementsvereisten voor SSO-functionaliteit. GitHub vereist bijvoorbeeld een Enterprise-abonnement.
    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Toont de optie voor eenmalige aanmelding in het Enterprise-abonnement op de pagina met GitHub-prijzen.":::


## <a name="next-steps"></a>Volgende stappen

- [Een app verwijderen](delete-application-portal.md)
