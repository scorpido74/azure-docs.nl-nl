---
title: Ontwikkelaarsaccounts autoriseren met OAuth 2.0 in API-beheer
titleSuffix: Azure API Management
description: Meer informatie over het autoriseren van gebruikers met OAuth 2.0 in API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 90c890925378c30ce5688d2713990b4b2cdd20c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430675"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Ontwikkelaarsaccounts autoriseren met OAuth 2.0 in Azure API Management

Veel API's ondersteunen [OAuth 2.0](https://oauth.net/2/) om de API te beveiligen en ervoor te zorgen dat alleen geldige gebruikers toegang hebben, en ze hebben alleen toegang tot bronnen waarop ze recht hebben. Om de interactieve Developer Console van Azure API Management met dergelijke API's te gebruiken, u met de service-instantie uw service-instantie configureren om te werken met uw API met OAuth 2.0.

> [!IMPORTANT]
> OAuth 2.0 autorisatie is nog niet beschikbaar in de interactieve console van de nieuwe ontwikkelaar portal.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Vereisten

In deze handleiding ziet u hoe u uw API Management-serviceinstantie configureert om OAuth 2.0-autorisatie voor ontwikkelaarsaccounts te gebruiken, maar u niet hoe u een OAuth 2.0-provider configureert. De configuratie voor elke OAuth 2.0-provider is anders, hoewel de stappen vergelijkbaar zijn en de vereiste informatie die wordt gebruikt bij het configureren van OAuth 2.0 in uw API Management-service-instantie hetzelfde zijn. In dit onderwerp worden voorbeelden weergegeven die Azure Active Directory gebruiken als OAuth 2.0-provider.

> [!NOTE]
> Zie het voorbeeld [webapp-graphAPI-DotNet][WebApp-GraphAPI-DotNet] voor meer informatie over het configureren van OAuth 2.0 met Azure Active Directory.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>Een OAuth 2.0-autorisatieserver configureren in API-beheer

> [!NOTE]
> Zie [Een API Management-serviceinstantie maken][Create an API Management service instance]als u nog geen API Management-serviceinstantie hebt gemaakt.

1. Klik op het tabblad OAuth 2.0 in het menu aan de linkerkant en klik op **+Toevoegen**.

    ![Menu OAuth 2.0](./media/api-management-howto-oauth2/oauth-01.png)

2. Voer een naam en een optionele beschrijving in de velden **Naam** en **beschrijving** in.

    > [!NOTE]
    > Deze velden worden gebruikt om de OAuth 2.0-autorisatieserver te identificeren binnen het huidige API Management-serviceexemplaar en hun waarden komen niet van de OAuth 2.0-server.

3. Voer de URL van de **pagina Clientregistratie in**. Op deze pagina kunnen gebruikers hun accounts maken en beheren en is deze afhankelijk van de gebruikte OAuth 2.0-provider. De **URL van de clientregistratiepagina** verwijst naar de pagina die gebruikers kunnen gebruiken om hun eigen accounts te maken en te configureren voor OAuth 2.0-providers die gebruikersbeheer van accounts ondersteunen. Sommige organisaties configureren of gebruiken deze functionaliteit niet, zelfs niet als de OAuth 2.0-provider deze ondersteunt. Als uw OAuth 2.0-provider geen gebruikersbeheer van accounts heeft geconfigureerd, voert u hier een url `https://placeholder.contoso.com`van tijdelijke aanduiding in, zoals de URL van uw bedrijf, of een URL zoals .

    ![OAuth 2.0 nieuwe server](./media/api-management-howto-oauth2/oauth-02.png)

4. Het volgende gedeelte van het formulier bevat de **instellingen autorisatieverlening**, **URL van het eindpunt autorisatie**en de **methodeinstellingen autorisatieaanvraag.**

    Geef de **typen autorisatieverlening** op door de gewenste typen te controleren. **Autorisatiecode** is standaard opgegeven.

    Voer de **URL van het eindpunt autorisatie in**. Voor Azure Active Directory is deze URL vergelijkbaar `<tenant_id>` met de volgende URL, waarbij deze wordt vervangen door de id van uw Azure AD-tenant.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    De **methode Autorisatieaanvraag** geeft aan hoe de autorisatieaanvraag naar de OAuth 2.0-server wordt verzonden. Standaard is **GET** geselecteerd.

5. Vervolgens moeten **tokeneindpunt-URL,** **Clientverificatiemethoden,** **toegangstokenverzendmethode** en **standaardbereik** worden opgegeven.

    ![OAuth 2.0 nieuwe server](./media/api-management-howto-oauth2/oauth-03.png)

    Voor een Azure Active Directory OAuth 2.0-server heeft de **URL tokeneindpunt** de volgende indeling, waarbij `<TenantID>` de indeling van `yourapp.onmicrosoft.com`.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    De standaardinstelling voor **clientverificatiemethoden** is **Basic**en **de methode voor het verzenden van Access-tokens** is de **koptekst Autorisatie**. Deze waarden zijn geconfigureerd in dit gedeelte van het formulier, samen met het **standaardbereik**.

6. De sectie **Clientreferenties** bevat de **client-id** en **het clientgeheim,** die worden verkregen tijdens het aanmaken en configuratieproces van uw OAuth 2.0-server. Zodra de **client-id** en **het clientgeheim** zijn opgegeven, wordt de **redirect_uri** voor de **autorisatiecode** gegenereerd. Deze URI wordt gebruikt om de antwoord-URL in uw OAuth 2.0-serverconfiguratie te configureren.

    ![OAuth 2.0 nieuwe server](./media/api-management-howto-oauth2/oauth-04.png)

    Als **type autorisatieverlening** is ingesteld op **het wachtwoord van de broneigenaar,** wordt de sectie **Wachtwoordreferenties voor broneigenaren** gebruikt om deze referenties op te geven. anders u het leeg laten.

    Zodra het formulier is voltooid, klikt u op **Maken** om de configuratie van de API-beheerOAuth 2.0-autorisatieserver op te slaan. Zodra de serverconfiguratie is opgeslagen, u API's configureren om deze configuratie te gebruiken, zoals in de volgende sectie wordt weergegeven.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>Een API configureren om de gebruikersautorisatie van OAuth 2.0 te gebruiken

1. Klik op **API's** in het menu **API-beheer** aan de linkerkant.

    ![OAuth 2.0 API's](./media/api-management-howto-oauth2/oauth-05.png)

2. Klik op de naam van de gewenste API en klik op **Instellingen**. Schuif naar de sectie **Beveiliging** en schakel het selectievakje in voor **OAuth 2.0**.

    ![OAuth 2.0-instellingen](./media/api-management-howto-oauth2/oauth-06.png)

3. Selecteer de gewenste **autorisatieserver** in de vervolgkeuzelijst en klik op **Opslaan**.

    ![OAuth 2.0-instellingen](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>Legacy developer portal - test de OAuth 2.0 gebruikersautorisatie

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Zodra u uw OAuth 2.0-autorisatieserver hebt geconfigureerd en uw API hebt geconfigureerd om die server te gebruiken, u deze testen door naar de ontwikkelaarsportal te gaan en een API aan te roepen. Klik op **Ontwikkelaarsportal (legacy)** in het bovenste menu van de pagina Overzicht van het Azure API **Management-exemplaar.**

Klik op **API's** in het bovenste menu en selecteer **Echo API**.

![Echo-API][api-management-apis-echo-api]

> [!NOTE]
> Als er slechts één API is geconfigureerd of zichtbaar is voor uw account, gaat u wanneer u op API's klikt rechtstreeks naar de bewerkingen voor die API.

Selecteer de bewerking **GET Resource,** klik op **Console openen**en selecteer **vervolgens Autorisatiecode** in de vervolgkeuzelijst.

![Console openen][api-management-open-console]

Wanneer **autorisatiecode** is geselecteerd, wordt een pop-upvenster weergegeven met de aanmeldingsvorm van de OAuth 2.0-provider. In dit voorbeeld wordt het aanmeldingsformulier geleverd door Azure Active Directory.

> [!NOTE]
> Als u pop-ups hebt uitgeschakeld, wordt u gevraagd deze in te schakelen door de browser. Nadat u deze hebt ingeschakeld, selecteert u **autorisatiecode** opnieuw en wordt het aanmeldingsformulier weergegeven.

![Aanmelden][api-management-oauth2-signin]

Zodra u zich hebt aangemeld, worden de `Authorization : Bearer` **kopteksten Aanvragen** gevuld met een koptekst waarmee de aanvraag is geautoriseerd.

![Kopteksttoken aanvragen][api-management-request-header-token]

Op dit punt u de gewenste waarden voor de resterende parameters configureren en de aanvraag indienen.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende video- en bijbehorende [artikel](api-management-howto-protect-backend-with-aad.md)voor meer informatie over het gebruik van OAuth 2.0 en API Management.

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

