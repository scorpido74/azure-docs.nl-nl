---
title: Ontwikkelaarsaccounts autoriseren met Azure Active Directory B2C
titleSuffix: Azure API Management
description: Meer informatie over het autoriseren van gebruikers met Azure Active Directory B2C in API-beheer.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b8215cd852d54283bfc6bd47e77d7d63ee4e2582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475490"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Ontwikkelaarsaccounts autoriseren met behulp van Azure Active Directory B2C in Azure API Management

## <a name="overview"></a>Overzicht

Azure Active Directory B2C is een cloud-oplossing voor identiteitsbeheer voor web- en mobiele toepassingen die gericht zijn op consumenten. U het gebruiken om de toegang tot uw ontwikkelaarsportal te beheren. In deze handleiding ziet u de configuratie die vereist is in uw API Management-service om te integreren met Azure Active Directory B2C. Zie [Ontwikkelaarsaccounts autoriseren met Azure Active Directory]voor informatie over het inschakelen van toegang tot de ontwikkelaarsportal met behulp van klassieke Azure Active Directory.

> [!NOTE]
> Als u de stappen in deze handleiding wilt uitvoeren, moet u eerst een Azure Active Directory B2C-tenant hebben om een toepassing in te maken. U moet ook aanmeldings- en aanmeldingsbeleid klaar hebben staan. Zie azure [Active Directory B2C-overzicht]voor meer informatie .

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Ontwikkelaarsaccounts autoriseren met Azure Active Directory B2C

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en zoekt uw API-beheerexemplaar om aan de slag te gaan.

   > [!NOTE]
   > Zie [Een API Management-serviceinstantie maken][Create an API Management service instance] in de zelfstudie Aan de slag met Azure API Management als u nog geen API Management-serviceinstantie hebt [gemaakt.][Get started with Azure API Management]

1. Onder **Identiteiten**. Klik bovenaan **op +Toevoegen.**

   Het deelvenster **Identiteitsprovider toevoegen** wordt aan de rechterkant weergegeven. Kies **Azure Active Directory B2C**.
    
   ![AAD B2C toevoegen als identiteitsprovider][api-management-howto-add-b2c-identity-provider]

1. Kopieer de **URL omleiden**.

   ![AAD B2C-identiteitsprovider omleiden URL][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. Ga in een nieuw tabblad naar uw Azure Active Directory B2C-tenant in de Azure-portal en open het **blade Toepassingen.**

   ![Een nieuwe aanvraag registreren 1][api-management-howto-aad-b2c-portal-menu]

1. Klik **op** de knop Toevoegen om een nieuwe Azure Active Directory B2C-toepassing te maken.

   ![Een nieuwe aanvraag registreren 2][api-management-howto-aad-b2c-add-button]

1. Voer in het **nieuwe toepassingsblad** een naam in voor de toepassing. Kies **Ja** onder **Web App/Web API**en kies **Ja** onder Impliciete **stroom toestaan**. Plak vervolgens de **URL omleiden** die in stap 3 is gekopieerd in het tekstvak **URL beantwoorden.**

   ![Een nieuwe aanvraag registreren 3][api-management-howto-aad-b2c-app-details]

1. Als u de nieuwe ontwikkelaarsportal gebruikt (niet de verouderde ontwikkelaarsportal), neemt u de **voornaam,** **achternaam**en **object-id van de gebruiker** op in de toepassingsclaims.

    ![Toepassingsclaims](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Klik op de knop **Maken**. Wanneer de toepassing wordt gemaakt, wordt deze weergegeven in het **mes Toepassingen.** Klik op de naam van de toepassing om de details te bekijken.

   ![Een nieuwe aanvraag registreren 4][api-management-howto-aad-b2c-app-created]

1. Kopieer de **toepassings-id** naar het klembord vanuit het blad **Eigenschappen.**

   ![Toepassings-id 1][api-management-howto-aad-b2c-app-id]

1. Ga terug naar het deelvenster API-beheer Voeg het deelvenster **Identiteitsprovider toe** en plak de id in het tekstvak **Client-id.**
    
1.  Ga terug naar de b2c-appregistratie, klik op de knop **Toetsen** en klik vervolgens op **Toets genereren**. Klik **op Opslaan** om de configuratie op te slaan en de **app-toets**weer te geven. Kopieer de sleutel naar het klembord.

    ![App-toets 1][api-management-howto-aad-b2c-app-key]

1.  Ga terug naar het deelvenster API-beheer Voeg het deelvenster **Identiteitsprovider toe** en plak de sleutel in het tekstvak **Clientgeheim.**
    
1.  Geef de domeinnaam op van de Azure Active Directory B2C-tenant in **de tenant Signin.**

1.  Met het veld **Autoriteit** u de te gebruiken URL van azure AD B2C-aanmelding beheren. Stel de waarde in **op<your_b2c_tenant_name>.b2clogin.com**.

1. Geef het **aanmeldingsbeleid** en **aanmeldingsbeleid** op in het beleid voor B2C-tenant. Optioneel u ook het **beleid voor profielbewerking** en **het beleid voor het opnieuw instellen**van wachtwoorden opgeven.

1. Nadat u de gewenste configuratie hebt opgegeven, klikt u op **Opslaan**.

    Nadat de wijzigingen zijn opgeslagen, kunnen ontwikkelaars nieuwe accounts maken en zich aanmelden bij de ontwikkelaarsportal met Azure Active Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Ontwikkelaarsportal - Azure AD B2C-accountverificatie toevoegen

In de developer portal is aanmelden met AAD B2C mogelijk met de **aanmeldingsknop: OAuth** widget. De widget is al opgenomen op de aanmeldingspagina van de standaardinhoud van de ontwikkelaarsportal.

Hoewel een nieuw account automatisch wordt gemaakt wanneer een nieuwe gebruiker zich aanmeldt bij AAD B2C, u overwegen om dezelfde widget toe te voegen aan de aanmeldingspagina.

Het **aanmeldingsformulier: de** widget OAuth vertegenwoordigt een formulier dat wordt gebruikt voor het aanmelden bij OAuth.

> [!IMPORTANT]
> U moet [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de AAD-wijzigingen van kracht te laten worden.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Verouderde ontwikkelaarsportal - aanmelden bij Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Als u zich wilt aanmelden voor een ontwikkelaarsaccount met Azure Active Directory B2C, opent u een nieuw browservenster en gaat u naar de ontwikkelaarsportal. Klik op de knop **Aanmelden.**

   ![Ontwikkelaarsportal 1][api-management-howto-aad-b2c-dev-portal]

2. Kies ervoor om je aan te melden bij **Azure Active Directory B2C**.

   ![Ontwikkelaarsportal 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. U wordt doorgestuurd naar het aanmeldingsbeleid dat u in de vorige sectie hebt geconfigureerd. Kies ervoor om je aan te melden via je e-mailadres of een van je bestaande sociale accounts.

   > [!NOTE]
   > Als Azure Active Directory B2C de enige optie is die is ingeschakeld op het tabblad **Identiteiten** in de uitgeversportal, wordt u rechtstreeks doorgestuurd naar het aanmeldingsbeleid.

   ![ontwikkelaarsportal][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Wanneer de aanmelding is voltooid, wordt u omgeleid naar de ontwikkelaarsportal. U bent nu aangemeld bij de ontwikkelaarsportal voor uw API Management-serviceexemplaar.

    ![Registratie voltooid][api-management-registration-complete]

## <a name="next-steps"></a>Volgende stappen

*  [Overzicht van Azure Active Directory B2C]
*  [Azure Active Directory B2C: uitbreidbaar beleidskader]
*  [Een Microsoft-account gebruiken als identiteitsprovider in Azure Active Directory B2C]
*  [Een Google-account gebruiken als identiteitsprovider in Azure Active Directory B2C]
*  [Een LinkedIn-account gebruiken als identiteitsprovider in Azure Active Directory B2C]
*  [Een Facebook-account gebruiken als identiteitsprovider in Azure Active Directory B2C]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Overzicht van Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Ontwikkelaarsaccounts autoriseren met Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: uitbreidbaar beleidskader]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Een Microsoft-account gebruiken als identiteitsprovider in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Een Google-account gebruiken als identiteitsprovider in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Een Facebook-account gebruiken als identiteitsprovider in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Een LinkedIn-account gebruiken als identiteitsprovider in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
