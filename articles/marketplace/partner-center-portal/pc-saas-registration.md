---
title: Een SaaS-aanvraag registreren | Azure Marketplace
description: Hier wordt uitgelegd hoe u een SaaS-toepassing registreert met behulp van de Azure-portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275710"
---
# <a name="register-a-saas-application"></a>Een SaaS-toepassing registreren

In dit artikel wordt uitgelegd hoe u een SaaS-toepassing registreert met behulp van de Microsoft [Azure-portal.](https://portal.azure.com/)  Na een succesvolle registratie ontvangt u een Azure Active Directory (Azure AD) beveiligingstoken dat u gebruiken om toegang te krijgen tot de SaaS Fulfillment API's.  Zie Wat is verificatie voor meer informatie over Azure [AD?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Service-to-service-verificatiestroom

In het volgende diagram ziet u de abonnementsstroom van een nieuwe klant en wanneer deze API's worden gebruikt:

![SaaS biedt API-flow](./media/saas-offer-publish-api-flow-v1.png)

Azure legt geen beperkingen op aan de verificatie die de SaaS-service aan zijn eindgebruikers blootstelt. Verificatie met de SaaS Fulfillment API's wordt echter uitgevoerd met een Azure AD-beveiligingstoken, meestal verkregen door de SaaS-app te registreren via de Azure-portal. 


## <a name="register-an-azure-ad-secured-app"></a>Een door Azure AD beveiligde app registreren

Elke toepassing die de mogelijkheden van Azure Active Directory wil gebruiken, moet eerst in een Azure Active Directory-tenant worden geregistreerd. Dit registratieproces omvat het geven van Azure AD-gegevens over uw toepassing, zoals de URL waar deze zich bevindt, de URL om antwoorden te verzenden nadat een gebruiker is geverifieerd, de URI die de app identificeert, enzovoort.  Voer de volgende stappen uit om een nieuwe toepassing te registreren via de Azure-portal:

1.  Meld u aan bij [Azure Portal](https://portal.azure.com/).
2.  Als uw account u toegang geeft tot meer dan één account, klikt u op uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure AD-tenant.
3.  Klik in het navigatiedeelvenster aan de linkerkant op de **Azure Active Directory-service,** klik op **App-registraties**en klik op Nieuwe **toepassingsregistratie**.

    ![SaaS AD-app-registraties](./media/saas-offer-app-registration-v1.png)

4.  Voer op de pagina\'Maken de registratiegegevens van uw aanvraag in:
    -   **Naam:** Voer een betekenisvolle toepassingsnaam in
    -   **Toepassingstype**: 
        - Selecteer **Systeemeigen** voor [clienttoepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) die lokaal op een apparaat zijn geïnstalleerd. Deze instelling wordt gebruikt voor openbare [systeemeigen clients](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) met OAuth.
        - Selecteer **Web-app / API** voor [clienttoepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) en [resource/API-toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) die op een beveiligde server zijn geïnstalleerd. Deze instelling wordt gebruikt voor Vertrouwelijke [Webclients van](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) OAuth en op [openbare gebruikersagenten gebaseerde clients.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)
        Dezelfde toepassing kan zowel een client als resource/API beschikbaar maken.
    -   **Aanmeldings-URL:** geef voor web-app-/API-toepassingen de basis-URL van uw app op. Het kan **http://localhost:31544** bijvoorbeeld de URL zijn voor een web-app die op uw lokale machine wordt uitgevoerd. Gebruikers gebruiken deze URL vervolgens om zich aan te melden bij een webclienttoepassing.
    -   **Omleiding URI:** voor native toepassingen moet u de URI die door Azure AD wordt gebruikt, opbieden om tokenreacties terug te sturen. Voer bijvoorbeeld **http://MyFirstAADApp**een waarde in die specifiek is voor uw toepassing.

        ![SaaS AD-app-registraties](./media/saas-offer-app-registration-v1-2.png)

        Bekijk de snelaangeleide instellingen die beschikbaar zijn in het gedeelte Aan *de slag* in de [Azure AD Developers Guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)voor specifieke voorbeelden voor webtoepassingen of native toepassingen.

5.  Klik op **Create** als u klaar bent. Azure AD wijst een unieke *toepassings-id* \'toe aan uw\'toepassing en u wordt naar de hoofdregistratiepagina van uw toepassing geleid. Afhankelijk van of uw toepassing een webtoepassing of systeemeigen toepassing is, worden er verschillende opties geboden om extra mogelijkheden aan uw toepassing toe te voegen.

>[!Note]
>Standaard is de nieuw geregistreerde toepassing zo geconfigureerd dat alleen gebruikers van dezelfde tenant zich bij uw toepassing kunnen aanmelden.


## <a name="using-the-azure-ad-security-token"></a>Het Azure AD-beveiligingstoken gebruiken

Zodra u uw toepassing hebt geregistreerd, u programmatisch een Azure AD-beveiligingstoken aanvragen.  Van de uitgever wordt verwacht dat hij dit token gebruikt en een verzoek indient om het op te lossen.  Bij het gebruik van de verschillende Fulfillment API's bevindt de parameter tokenquery zich in de URL wanneer de gebruiker vanuit Azure naar de SaaS-website wordt doorgestuurd.  Dit token is slechts één uur geldig.  Bovendien moet u de tokenwaarde van de browser decoderen voordat u deze gebruikt.

Zie [Azure Active Directory-toegangstokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)voor meer informatie over deze tokens.


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Een token downloaden op basis van de Azure AD-app

HTTP-methode

`POST`

*Aanvraag-URL*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*URI, parameter*

|  **Parameternaam**  | **Vereist**  | **Beschrijving**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | Tenant-id van de geregistreerde AAD-aanvraag   |
|  |  |  |


*Koptekst aanvragen*

|  **Headernaam**  | **Vereist** |  **Beschrijving**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Inhoudstype dat aan het verzoek is gekoppeld. De standaardwaarde is `application/x-www-form-urlencoded`.  |
|  |  |  |


*Aanvraaginstantie*

| **Naam van eigenschap**   | **Vereist** |  **Beschrijving**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Subsidietype. De standaardwaarde is `client_credentials`.                    |
|  Client_id          | True         |  Client/app-id die is gekoppeld aan de Azure AD-app.                  |
|  client_secret      | True         |  Wachtwoord gekoppeld aan de Azure AD-app.                               |
|  Resource           | True         |  Doelbron waarvoor het token wordt aangevraagd. De standaardwaarde is `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Reactie*

|  **Naam**  | **Type**       |  **Beschrijving**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Aanvraag geslaagd   |
|  |  |  |

*TokenResponse*

Voorbeeldreactietoken:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Volgende stappen

Uw door Azure AD beveiligde app kan nu de [SaaS Fulfillment API Version 2](./pc-saas-fulfillment-api-v2.md)gebruiken.
