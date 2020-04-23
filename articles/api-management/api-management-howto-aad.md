---
title: Ontwikkelaarsaccounts autoriseren met Azure Active Directory
titleSuffix: Azure API Management
description: Meer informatie over het autoriseren van gebruikers met Azure Active Directory in API-beheer.
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
ms.openlocfilehash: 41f9f267880d199d2e221453eea5c3584ce96881
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868392"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Ontwikkelaarsaccounts autoriseren met behulp van Azure Active Directory in Azure API Management

In dit artikel ziet u hoe u toegang tot de ontwikkelaarsportal inschakelt voor gebruikers vanuit Azure Active Directory (Azure AD). In deze handleiding ziet u ook hoe u groepen Azure AD-gebruikers beheert door externe groepen toe te voegen die de gebruikers bevatten.

## <a name="prerequisites"></a>Vereisten

- Lees de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
- Een Azure API Management-exemplaar importeren en publiceren. Zie [Importeren en publiceren](import-and-publish.md)voor meer informatie .

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Ontwikkelaarsaccounts autoriseren met Azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
2. Selecteer ![pijl](./media/api-management-howto-aad/arrow.png).
3. Typ **api** in het zoekvak.
4. Selecteer **API-beheerservices**.
5. Selecteer uw service-exemplaar van API Management.
6. Selecteer onder **Ontwikkelaarsportal**de optie **Identiteiten**.
7. Selecteer **+Toevoegen** bovenaan.

    Het deelvenster **Identiteitsprovider toevoegen** wordt aan de rechterkant weergegeven.
8. Selecteer Azure **Active Directory** **onder Providertype**.

    Besturingselementen waarmee u andere noodzakelijke gegevens invoeren, worden in het deelvenster weergegeven. De besturingselementen omvatten **Client ID** en **Client secret**. (U krijgt informatie over deze besturingselementen later in het artikel.)
9. Noteer de inhoud van **de URL van Redirect**.
    
   ![Stappen voor het toevoegen van een identiteitsprovider in de Azure-portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Open in uw browser een ander tabblad. 
11. Navigeer naar de [Azure-portal - App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) om een app te registreren in Active Directory.
12. Selecteer **App-registraties** onder **Beheren**.
13. Selecteer **Nieuwe registratie**. Stel **op** de pagina Een aanvraag registreren de waarden als volgt in:
    
    * **Stel Naam** in op een betekenisvolle naam. bijvoorbeeld *ontwikkelaarsportal*
    * Stel **ondersteunde accounttypen alleen** in op Accounts in deze **organisatiemap**. 
    * Stel **URI omleiden** in op de waarde die u hebt gekregen vanaf stap 9. 
    * Kies **Registreren**. 

14.  Nadat de aanvraag is geregistreerd, kopieert u de **id van de toepassing (client)** van de pagina **Overzicht.** 
15. Ga terug naar uw API-beheerexemplaar. Plak in het venster **Identiteitsprovider toevoegen** de waarde **van de id-toepassing (client)** in het vak **Client-id.**
16. Schakel terug naar de Azure AD-configuratie, Selecteer **certificaten & geheimen** onder **Beheren**. Selecteer de knop **Nieuw clientgeheim**. Voer een waarde in voor **Beschrijving**, selecteer een optie voor **Verloopt** en kies **Toevoegen**. Kopieer de geheime waarde van de client voordat u de pagina verlaat. U hebt deze nodig in de volgende stap. 
17. Selecteer **onder Beheren** **verificatie** en selecteer **vervolgens ID-tokens** onder **Impliciete toekenning**
18. Ga terug naar uw API-beheerexemplaar en plak het geheim in het geheime vak **Client.**

    > [!IMPORTANT]
    > Zorg ervoor dat u het geheim van de **client bijwerkt** voordat de sleutel verloopt. 
    >  
    >

19. Het venster **Identiteitsprovider toevoegen** bevat ook het tekstvak **Toegestane tenants.** Geef daar de domeinen op van de Azure AD-exemplaren waaraan u toegang wilt verlenen tot de API's van het api-beheerserviceexemplaar. U meerdere domeinen scheiden met nieuwe regels, spaties of komma's.

    > [!NOTE]
    > U meerdere domeinen opgeven in de sectie **Toegestane tenants.** Voordat een gebruiker zich kan aanmelden vanuit een ander domein dan het oorspronkelijke domein waar de toepassing is geregistreerd, moet een globale beheerder van het andere domein toestemming verlenen voor de toepassing om toegang te krijgen tot directorygegevens. Om toestemming te verlenen, moet de globale beheerder: a. Ga `https://<URL of your developer portal>/aadadminconsent` naar (bijvoorbeeld. https://contoso.portal.azure-api.net/aadadminconsent)
    > b. Typ de domeinnaam van de Azure AD-tenant waartoe ze toegang willen geven.
    > c. Selecteer **Indienen**. 

20.  Nadat u de gewenste configuratie hebt opgegeven, selecteert u **Toevoegen**.

Nadat de wijzigingen zijn opgeslagen, kunnen gebruikers in de opgegeven Azure AD-instantie zich aanmelden bij de ontwikkelaarsportal door de stappen in Aanmelden bij de ontwikkelaarsportal te volgen [met behulp van een Azure AD-account](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Een externe Azure AD-groep toevoegen

Nadat u toegang hebt ingeschakeld voor gebruikers in een Azure AD-tenant, u Azure AD-groepen toevoegen aan API-beheer. Als gevolg hiervan u de zichtbaarheid van het product beheren met Azure AD-groepen.

Als u een externe Azure AD-groep wilt toevoegen aan APIM, moet u eerst de vorige sectie voltooien. Bovendien moet de toepassing die u hebt geregistreerd, `Directory.Read.All` toegang krijgen tot de Microsoft Graph API met toestemming door de volgende stappen te volgen: 

1. Ga terug naar uw app-registratie die in de vorige sectie is gemaakt.
2. Selecteer **API-machtigingen**en klik op **+Een machtiging toevoegen**. 
3. Selecteer in het deelvenster **API-machtigingen aanvragen** het tabblad **Microsoft-API's** en selecteer vervolgens de tegel **Microsoft Graph.** Selecteer **Toepassingsmachtigingen**, zoek naar **Directory**en selecteer vervolgens de machtiging **Directory.Read.All.** 
4. Klik **op Machtigingen onder** aan het deelvenster toevoegen en klik vervolgens op **Beheerderstoestemming verlenen voor {tenantname}** zodat u toegang verleent aan alle gebruikers in deze map. 

U nu externe Azure AD-groepen toevoegen via het tabblad **Groepen** van uw API-beheerexemplaar.

1. Selecteer de tab **Groepen**.
2. Selecteer de knop **AAD-groep toevoegen.**
    ![Knop 'AAD-groep toevoegen'](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Selecteer de groep die u wilt toevoegen.
4. Druk op de knop **Selecteren.**

Nadat u een externe Azure AD-groep hebt toegevoegd, u de eigenschappen ervan controleren en configureren. Selecteer de naam van de groep op het tabblad **Groepen.** Vanaf hier u **de naam-** en **beschrijvingsgegevens** voor de groep bewerken.
 
Gebruikers van het geconfigureerde Azure AD-exemplaar kunnen zich nu aanmelden bij de ontwikkelaarsportal. Ze kunnen alle groepen bekijken en zich abonneren waarvoor ze zichtbaar zijn.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/>Ontwikkelaarsportal - Azure AD-accountverificatie toevoegen

In de developer portal is aanmelden met AAD mogelijk met de **aanmeldingsknop: OAuth** widget. De widget is al opgenomen op de aanmeldingspagina van de standaardinhoud van de ontwikkelaarsportal.

Hoewel een nieuw account automatisch wordt gemaakt wanneer een nieuwe gebruiker zich aanmeldt bij AAD, u overwegen om dezelfde widget toe te voegen aan de aanmeldingspagina.

Het **aanmeldingsformulier: de** widget OAuth vertegenwoordigt een formulier dat wordt gebruikt voor het aanmelden bij OAuth.

> [!IMPORTANT]
> U moet [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de AAD-wijzigingen van kracht te laten worden.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Verouderde ontwikkelaarsportal - aanmelden bij Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

U zich aanmelden bij de ontwikkelaarsportal met een Azure AD-account dat u in de vorige secties hebt geconfigureerd:

1. Open een nieuw browservenster met de aanmeldings-URL in de configuratie van de Active Directory-toepassings en selecteer **Azure Active Directory**.

   ![Aanmeldingspagina][api-management-dev-portal-signin]

1. Voer de referenties in van een van de gebruikers in Azure AD en selecteer **Aanmelden**.

   ![Aanmelden met gebruikersnaam en wachtwoord][api-management-aad-signin]

1. U wordt mogelijk gevraagd een registratieformulier te gebruiken als er aanvullende informatie nodig is. Vul het inschrijfformulier in en selecteer **Aanmelden**.

   ![Knop Aanmelden op het registratieformulier][api-management-complete-registration]

Uw gebruiker is nu aangemeld bij de ontwikkelaarsportal voor uw API Management-serviceinstantie.

![Ontwikkelaarsportal nadat de registratie is voltooid][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

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

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
