---
title: Een SaaS-toepassing registreren | Azure Marketplace
description: Hierin wordt uitgelegd hoe u een SaaS-toepassing registreert met behulp van de Azure Portal.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: b2e02e42ab63f893574ca5217fd2f36c7481aabd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827943"
---
# <a name="register-a-saas-application"></a>Een SaaS-toepassing registreren

In dit artikel wordt uitgelegd hoe u een SaaS-toepassing registreert met behulp van de micro soft- [Azure Portal](https://portal.azure.com/).  Nadat de registratie is voltooid, ontvangt u een beveiligings token van Azure Active Directory (Azure AD) dat u kunt gebruiken voor toegang tot de SaaS-fulfillment-Api's.  Zie [Wat is verificatie?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios) voor meer informatie over Azure AD.


## <a name="service-to-service-authentication-flow"></a>Service-naar-service-verificatie stroom

In het volgende diagram ziet u de abonnements stroom van een nieuwe klant en wanneer deze Api's worden gebruikt:

![API-stroom voor SaaS-aanbiedingen](./media/saas-offer-publish-api-flow-v1.png)

Azure biedt geen beperkingen voor de verificatie die de SaaS-service voor de eind gebruikers weergeeft. Verificatie met de SaaS-fulfillment-Api's wordt echter uitgevoerd met een Azure AD-beveiligings token, dat meestal wordt verkregen door de SaaS-app te registreren via de Azure Portal. 


## <a name="register-an-azure-ad-secured-app"></a>Een Azure AD-beveiligde app registreren

Elke toepassing die de mogelijkheden van Azure Active Directory wil gebruiken, moet eerst in een Azure Active Directory-tenant worden geregistreerd. Bij dit registratie proces moet u Azure AD-gegevens over uw toepassing geven, zoals de URL waar deze zich bevindt, de URL voor het verzenden van antwoorden nadat een gebruiker is geverifieerd, de URI waarmee de app wordt geïdentificeerd, enzovoort.  Voer de volgende stappen uit om een nieuwe toepassing te registreren met behulp van de Azure Portal:

1.  Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2.  Als uw account u toegang geeft tot meer dan één, klikt u in de rechter bovenhoek op uw account en stelt u uw portal sessie in op de gewenste Azure AD-Tenant.
3.  Klik in het navigatie deel venster aan de linkerkant op de **Azure Active Directory** -service, klik op **app-registraties**en klik vervolgens op **nieuwe toepassing registreren**.

    ![SaaS AD-App-registraties](./media/saas-offer-app-registration-v1.png)

4.  Voer op de pagina maken uw\'de registratie gegevens van uw toepassing in:
    -   **Naam**: Voer een beschrijvende naam voor de toepassing in
    -   **Toepassings type**: 
        - Selecteer **Systeemeigen** voor [clienttoepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) die lokaal op een apparaat zijn geïnstalleerd. Deze instelling wordt gebruikt voor openbare [systeemeigen clients](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) met OAuth.
        - Selecteer **Web-app/API** voor [client toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) en [resource/API-toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) die zijn geïnstalleerd op een beveiligde server. Deze instelling wordt gebruikt voor OAuth vertrouwelijke [webclients](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) en [clients op basis van](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)een open bare gebruiker.
        Dezelfde toepassing kan zowel een client als resource/API beschikbaar maken.
    -   **Aanmeldings-URL**: voor web app/API-toepassingen geeft u de basis-URL van uw app op. **http://localhost:31544** kan bijvoorbeeld de URL zijn voor een web-app die wordt uitgevoerd op uw lokale machine. Gebruikers zouden deze URL vervolgens gebruiken om zich aan te melden bij een webclient-toepassing.
    -   **Omleidings-URI**: voor systeem eigen toepassingen geeft u de URI op die door Azure AD wordt gebruikt om token antwoorden te retour neren. Voer een waarde in die specifiek is voor uw toepassing, bijvoorbeeld **http://MyFirstAADApp** .

        ![SaaS AD-App-registraties](./media/saas-offer-app-registration-v1-2.png)

        Voor specifieke voor beelden voor webtoepassingen of systeem eigen toepassingen raadpleegt u de Quick Start-setups die beschikbaar zijn in de sectie *aan de slag* van de [hand leiding voor ontwikkel AARS van Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Klik op **Create** als u klaar bent. Azure AD wijst een unieke *toepassings-id* toe aan uw toepassing en u\'opnieuw naar de hoofd registratie pagina van de toepassing\'s. Afhankelijk van of uw toepassing een webtoepassing of systeemeigen toepassing is, worden er verschillende opties geboden om extra mogelijkheden aan uw toepassing toe te voegen.

>[!Note]
>De zojuist geregistreerde toepassing is standaard zo geconfigureerd dat alleen gebruikers van dezelfde Tenant zich kunnen aanmelden bij uw toepassing.


## <a name="using-the-azure-ad-security-token"></a>Het Azure AD-beveiligings token gebruiken

Zodra u uw toepassing hebt geregistreerd, kunt u via een programma een Azure AD-beveiligings token aanvragen.  De uitgever wordt verwacht dit token te gebruiken en een aanvraag indienen om deze te kunnen oplossen.  Wanneer u de verschillende fulfillment-Api's gebruikt, bevindt de token query-para meter zich in de URL wanneer de gebruiker wordt omgeleid naar de SaaS-website vanuit Azure.  Dit token is slechts één uur geldig.  Daarnaast moet u de URL decoderen van de token waarde uit de browser voordat u deze gebruikt.

Zie [Azure Active Directory toegangs tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)voor meer informatie over deze tokens.


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Een Token ophalen op basis van de Azure AD-app

HTTP-methode

`POST`

*Aanvraag-URL*

**https://login.microsoftonline.com/ *{tenantId}* /oauth2/token**

*URI-para meter*

|  **Parameternaam**  | **Vereist**  | **Beschrijving**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | Tenant-ID van de geregistreerde AAD-toepassing   |
|  |  |  |


*Aanvraag header*

|  **Header naam**  | **Vereist** |  **Beschrijving**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Het inhouds type dat is gekoppeld aan de aanvraag. De standaard waarde is `application/x-www-form-urlencoded`.  |
|  |  |  |


*Aanvraag tekst*

| **Eigenschapsnaam**   | **Vereist** |  **Beschrijving**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Toekennings type. De standaard waarde is `client_credentials`.                    |
|  Client_id          | True         |  Client/App-ID die is gekoppeld aan de Azure AD-app.                  |
|  client_secret      | True         |  Het wacht woord dat is gekoppeld aan de Azure AD-app.                               |
|  Resource           | True         |  Doel resource waarvoor het token wordt aangevraagd. De standaard waarde is `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Antwoord*

|  **Naam**  | **Type**       |  **Beschrijving**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Aanvraag is voltooid   |
|  |  |  |

*TokenResponse*

Voorbeeld token voor antwoorden:

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

Uw met Azure AD beveiligde app kan nu gebruikmaken van de [SaaS fulfillment API versie 2](./pc-saas-fulfillment-api-v2.md).
