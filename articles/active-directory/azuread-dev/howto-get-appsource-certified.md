---
title: AppSource-gecertificeerd voor Azure Active Directory| Microsoft Documenten
description: Meer informatie over het instellen van uw app-appbron-certificering voor Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 3ad4efa3b8126a9b9c6557822f61e3bfff3fe120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154879"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>AppSource-gecertificeerd voor Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Microsoft AppSource](https://appsource.microsoft.com/) is een bestemming voor zakelijke gebruikers om zakelijke SaaS-toepassingen (standalone SaaS en add-on voor bestaande Microsoft SaaS-producten) te ontdekken, uit te proberen en te beheren.

Als u een zelfstandige SaaS-toepassing op AppSource wilt aanbieden, moet uw toepassing één aanmelding accepteren van werkaccounts van elk bedrijf of organisatie met Azure Active Directory (Azure AD). Het aanmeldingsproces moet gebruik maken van de [OpenID Connect-](v1-protocols-openid-connect-code.md) of [OAuth 2.0-protocollen.](v1-protocols-oauth-code.md) SAML-integratie wordt niet geaccepteerd voor AppSource-certificering.

## <a name="guides-and-code-samples"></a>Gidsen en codevoorbeelden

Als u meer wilt weten over het integreren van uw toepassing met Azure AD met Open ID Connect, volgt u onze handleidingen en codevoorbeelden in de handleiding van Azure [Active Directory-ontwikkelaars.](v1-overview.md#get-started "Aan de slag met Azure AD voor ontwikkelaars")

## <a name="multi-tenant-applications"></a>Toepassingen voor meerdere tenants

Een *toepassing met meerdere tenants* is een toepassing die aanmeldingen accepteert van gebruikers van een bedrijf of organisatie die Azure AD hebben zonder dat er een aparte instantie, configuratie of implementatie nodig is. AppSource raadt toepassingen aan multi-tenancy te implementeren om de gratis proefervaring *met één klik* mogelijk te maken.

Voer de volgende stappen uit om multi-tenancy op uw toepassing in te schakelen:
1. Stel `Multi-Tenanted` de `Yes` eigenschap in op de gegevens van uw toepassingsregistratie in de [Azure-portal.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) Standaard worden toepassingen die in de *[Azure-portal](#single-tenant-applications)* zijn gemaakt geconfigureerd als één tenant.
1. Werk uw code bij `common` om aanvragen naar het eindpunt te verzenden. Om dit te doen, `https://login.microsoftonline.com/{yourtenant}` werkt `https://login.microsoftonline.com/common*`u het eindpunt bij van .
1. Voor sommige platforms, zoals ASP .NET, moet u ook uw code bijwerken om meerdere emittenten te accepteren.

Zie Hoe u zich aanmeldt [bij een Azure Active Directory-gebruiker (Azure AD) in Met behulp van het patroon van de multi-tenanttoepassings .](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

### <a name="single-tenant-applications"></a>Toepassingen met één tenant

Een toepassing met één tenant is een toepassing die alleen aanmeldingen accepteert van gebruikers van een gedefinieerde Azure *AD-instantie.* Externe gebruikers (waaronder werk- of schoolaccounts van andere organisaties of persoonlijke accounts) kunnen zich aanmelden bij een toepassing met één tenant nadat ze elke gebruiker als gastaccount hebben toegevoegd aan het Azure AD-exemplaar dat de toepassing is geregistreerd. 

U gebruikers als gastaccounts toevoegen aan Azure AD via de [Azure AD B2B-samenwerking](../b2b/what-is-b2b.md) en u dit [programmatisch](../../active-directory-b2c/code-samples.md)doen. Bij het gebruik van B2B kunnen gebruikers een selfserviceportal maken waarvoor geen uitnodiging nodig is om zich aan te melden. Zie [Selfserviceportal voor Azure AD B2B-samenwerkingsmelding voor](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal)meer informatie.

Toepassingen met één tenant kunnen de *contact-me-ervaring* inschakelen, maar als u de single-click/free-proefervaring wilt inschakelen die AppSource aanbeveelt, schakelt u in plaats daarvan multi-tenancy in op uw toepassing.

## <a name="appsource-trial-experiences"></a>AppSource-proefervaringen

### <a name="free-trial-customer-led-trial-experience"></a>Gratis proefperiode (door de klant geleide proefervaring)

De door de klant geleide proefversie is de ervaring die AppSource aanbeveelt omdat deze een single-click toegang biedt tot uw toepassing. In het volgende voorbeeld ziet u hoe deze ervaring eruit ziet:

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>Gebruiker vindt uw toepassing in AppSource-website</li><li>Selecteert de optie 'Gratis proefversie'</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>AppSource verwijst gebruiker door naar een URL op uw website</li><li>Uw website start het <i>single-sign-on</i> proces automatisch (op paginabelasting)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>Gebruiker wordt doorgestuurd naar de aanmeldingspagina van Microsoft</li><li>Gebruiker biedt referenties om in te loggen</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>Gebruiker geeft toestemming voor uw aanvraag</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Aanmelding is voltooid en de gebruiker wordt doorgestuurd naar uw website</li><li>Gebruiker start de gratis proefperiode</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Neem contact met mij op (partner-geleide proefervaring)

U de partnerproefervaring gebruiken wanneer een handleiding of een langdurige bewerking moet gebeuren om de gebruiker/het bedrijf in te richten- bijvoorbeeld uw toepassing moet virtuele machines, database-exemplaren of bewerkingen inrichten die veel tijd in beslag nemen. In dit geval stuurt AppSource u de contactgegevens van de gebruiker nadat de gebruiker de knop **Proef aanvragen** heeft geselecteerd en een formulier heeft ingevuld. Wanneer u deze informatie ontvangt, dient u de omgeving in en stuurt u de instructies naar de gebruiker over hoe u toegang krijgt tot de proefervaring:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>Gebruiker vindt uw toepassing in appsource-website</li><li>Selecteert de optie 'Contact met mij'</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>Vult een formulier in met contactgegevens</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>U ontvangt gebruikersinformatie</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>Omgeving instellen</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>Contact opnemen met de gebruiker met proefgegevens</td>
        </tr>
        </table><br/><br/>
        <ul><li>U ontvangt de informatie van de gebruiker en het proefexemplaar voor het instellen</li><li>U stuurt de hyperlink om toegang te krijgen tot uw toepassing naar de gebruiker</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>Gebruiker heeft toegang tot uw toepassing en voltooit het single-sign-on proces</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>Gebruiker geeft toestemming voor uw aanvraag</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Aanmelding is voltooid en de gebruiker wordt doorgestuurd naar uw website</li><li>Gebruiker start de gratis proefperiode</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Meer informatie

Zie [deze video](https://aka.ms/trialexperienceforwebapps)voor meer informatie over de appsource-proefervaring. 

## <a name="next-steps"></a>Volgende stappen

- Zie [Verificatiescenario's voor Azure AD voor](https://docs.microsoft.com/azure/active-directory/develop/v1-authentication-scenarios)meer informatie over het bouwen van toepassingen die azure AD-aanmeldingen ondersteunen.
- Zie [AppSource-partnerinformatie](https://appsource.microsoft.com/partners) voor informatie over het aanbieden van uw SaaS-toepassing in AppSource

## <a name="get-support"></a>Ondersteuning krijgen

Voor Azure AD-integratie gebruiken we [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) met de community om ondersteuning te bieden.

We raden je ten zeerste aan om eerst je vragen te stellen over Stack Overflow en door bestaande problemen te bladeren om te zien of iemand je vraag al eerder heeft gesteld. Zorg ervoor dat uw vragen [ `[azure-active-directory]` of `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource)opmerkingen worden getagd met en .

Gebruik de volgende opmerkingensectie om feedback te geven en ons te helpen onze inhoud te verfijnen en vorm te geven.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
