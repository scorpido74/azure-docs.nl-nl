---
title: API-connectors toevoegen aan self-service-aanmeld stromen-Azure AD
description: Een web-API configureren voor gebruik in een gebruikers stroom.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6238e89b3941668f831f3128bb0e723a4097e48
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027509"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Een API-connector toevoegen aan een gebruikers stroom

Als u een [API-connector](api-connectors-overview.md)wilt gebruiken, maakt u eerst de API-connector en schakelt u deze in in een gebruikers stroom.

## <a name="create-an-api-connector"></a>Een API-connector maken

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com/).
2. Onder **Azure-Services**selecteert u **Azure Active Directory**.
3. Selecteer in het linkermenu **externe identiteiten**.
4. Selecteer **alle API-connectors (preview)** en selecteer vervolgens **nieuwe API-connector**.

   ![Een nieuwe API-connector toevoegen](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Geef een weergave naam op voor de aanroep. **Controleer bijvoorbeeld goedkeurings status**.
6. Geef de **eind punt-URL** voor de API-aanroep op.
7. Geef de verificatie gegevens op voor de API.

   - Momenteel wordt alleen basis verificatie ondersteund. Als u een API zonder basis verificatie voor ontwikkelings doeleinden wilt gebruiken, voert u een dummy **gebruikers naam** en **wacht woord** in die door uw API kunnen worden genegeerd. Voor gebruik met een Azure-functie met een API-sleutel kunt u de code als een query parameter in de **eind punt-URL** toevoegen (bijvoorbeeld https []() ://contoso.azurewebsites.net/API/endpoint<b>? code = 0123456789</b>).

   ![Een nieuwe API-connector toevoegen](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. Selecteer de claims die u naar de API wilt verzenden.
9. Selecteer claims die u van de API wilt ontvangen.

   ![Claims voor API-connector instellen](./media/self-service-sign-up-add-api-connector/api-connector-claims.png)

10. Selecteer **Opslaan**.

## <a name="enable-the-api-connector-in-a-user-flow"></a>De API-connector inschakelen in een gebruikers stroom

Volg deze stappen om een API-connector toe te voegen aan een self-service voor het registreren van een gebruikers stroom.

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com/).
2. Onder **Azure-Services**selecteert u **Azure Active Directory**.
3. Selecteer in het linkermenu **externe identiteiten**.
4. Selecteer **gebruikers stromen (preview)** en selecteer vervolgens de gebruikers stroom waaraan u de API-connector wilt toevoegen.
5. Selecteer **API-connectors**en selecteer vervolgens de API-eind punten die u wilt aanroepen met de volgende stappen in de gebruikers stroom:

   - **Nadat u zich hebt aangemeld met een id-provider**
   - **Voordat u de gebruiker maakt**

   ![Api's toevoegen aan de gebruikers stroom](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Selecteer **Opslaan**.

Meer informatie over [waar u een API-connector in een gebruikers stroom kunt inschakelen](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow).

## <a name="request-sent-to-the-api"></a>De aanvraag is verzonden naar de API

Een API-connector resultatenset als een HTTP POST-aanvraag, waarbij geselecteerde claims als sleutel-waardeparen worden verzonden in een JSON-hoofd tekst. Het antwoord moet ook de HTTP-header hebben `Content-Type: application/json` . Kenmerken worden op dezelfde manier geserialiseerd als Microsoft Graph gebruikers kenmerken. <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>Voorbeeldaanvraag

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "postalCode": "33971",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

De **gebruikers interface land instellingen (' ui_locales ')** wordt standaard in alle aanvragen verzonden. Het biedt de land instellingen van een gebruiker en kan worden gebruikt door de API om internationale reacties te retour neren. Deze wordt niet weer gegeven in het configuratie venster van de API.

Als een claim voor verzenden geen waarde heeft op het moment dat het API-eind punt wordt aangeroepen, wordt de claim niet verzonden naar de API.

Aangepaste kenmerken kunnen worden gemaakt voor de gebruiker met behulp van de indeling **extension_ \<extensions-app-id> _AttributeName** . Uw API moet verwachten dat er claims in dezelfde geserialiseerde indeling worden ontvangen. Uw API kan claims retour neren met of zonder de `<extensions-app-id>` . Zie voor meer informatie over aangepaste kenmerken [aangepaste kenmerken definiëren voor Self-service aanmeldingen](user-flow-add-custom-attributes.md).

> [!TIP] 
> [**identiteiten (' Identities ')**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) en het **e-mail adres (' e-mail ')** kunnen worden gebruikt om een gebruiker te identificeren voordat ze een account in uw Tenant hebben. De claim ' Identities ' wordt verzonden wanneer een gebruiker wordt geverifieerd met een Google-of Facebook-en e-mail bericht wordt altijd verzonden.

## <a name="expected-response-types-from-the-web-api"></a>Verwachte antwoord typen van de Web-API

Wanneer de Web-API tijdens een gebruikers stroom een HTTP-aanvraag van Azure AD ontvangt, kan deze de volgende antwoorden retour neren:

- [Vervolg reactie](#continuation-response)
- [Antwoord blok keren](#blocking-response)
- [Validatie-fout bericht](#validation-error-response)

### <a name="continuation-response"></a>Vervolg reactie

Een vervolg antwoord geeft aan dat de gebruikers stroom moet door gaan met de volgende stap. In een vervolg reactie kan de API claims retour neren.

Als een claim wordt geretourneerd door de API en is geselecteerd als een **claim om te ontvangen**, doet de claim het volgende:

- Vooraf ingevulde invoer velden op de pagina kenmerk verzameling als de API-connector wordt aangeroepen voordat de pagina wordt weer gegeven.
- Overschrijft elke waarde die al aan de claim is toegewezen.
- Hiermee wijst u een waarde toe aan de claim als deze eerder null is.

#### <a name="example-of-a-continuation-response"></a>Voor beeld van een vervolg reactie

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parameter                                          | Type              | Vereist | Beschrijving                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| versie                                            | Tekenreeks            | Ja      | De versie van de API.                                                                                                                                                                                                                                                                |
| actie                                             | Tekenreeks            | Ja      | Waarde moet zijn `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | Waarden kunnen worden opgeslagen in de map als ze zijn geselecteerd als een **claim om te ontvangen** in de API-connector configuratie en **gebruikers kenmerken** voor een gebruikers stroom. Waarden kunnen worden geretourneerd in het token als deze zijn geselecteerd als een **toepassings claim**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | De geretourneerde claim hoeft niet te bevatten `_<extensions-app-id>_` . Waarden worden opgeslagen in de map als deze zijn geselecteerd als een **claim om te ontvangen** in de API-connector configuratie en het **gebruikers kenmerk** voor een gebruikers stroom. Aangepaste kenmerken kunnen niet terug worden verzonden in het token. |

### <a name="blocking-response"></a>Antwoord blok keren

Een blokkerende reactie sluit de gebruikers stroom af. Het kan worden uitgegeven door de API om de voortzetting van de gebruikers stroom te stoppen door een blok pagina voor de gebruiker weer te geven. Op de pagina blok keren wordt de weer gegeven `userMessage` van de API.

Voor beeld van het blokkerende antwoord:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Parameter   | Type   | Vereist | Beschrijving                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| versie     | Tekenreeks | Ja      | De versie van de API.                                                    |
| actie      | Tekenreeks | Ja      | Waarde moet`ShowBlockPage`                                              |
| userMessage | Tekenreeks | Ja      | Bericht dat wordt weergegeven aan de gebruiker.                                            |
| code        | Tekenreeks | No       | Foutcode. Kan worden gebruikt voor fout opsporing. Niet weer gegeven voor de gebruiker. |

#### <a name="end-user-experience-with-a-blocking-response"></a>Eind gebruikers ervaring met een blokkerend antwoord

![Voor beeld blok pagina](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>Validatie-fout bericht

Een API-aanroep die wordt aangeroepen nadat een pagina met kenmerk verzamelingen een validatie fout antwoord retourneert. Wanneer u dit doet, blijft de gebruikers stroom op de pagina kenmerk verzameling en `userMessage` wordt de gebruiker weer gegeven. De gebruiker kan vervolgens het formulier bewerken en opnieuw verzenden. Dit type antwoord kan worden gebruikt voor invoer validatie.

#### <a name="example-of-a-validation-error-response"></a>Voor beeld van een validatie-fout bericht

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Parameter   | Type    | Vereist | Beschrijving                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| versie     | Tekenreeks  | Ja      | De versie van de API.                                                    |
| actie      | Tekenreeks  | Ja      | Waarde moet zijn `ValidationError` .                                           |
| status      | Geheel getal | Yes      | Dit moet een waarde zijn `400` voor een ValidationError-antwoord.                        |
| userMessage | Tekenreeks  | Ja      | Bericht dat wordt weergegeven aan de gebruiker.                                            |
| code        | Tekenreeks  | No       | Foutcode. Kan worden gebruikt voor fout opsporing. Niet weer gegeven voor de gebruiker. |

#### <a name="end-user-experience-with-a-validation-error-response"></a>Eind gebruikers ervaring met validatie-fout bericht

![Voorbeeld validatie pagina](./media/api-connectors-overview/validation-error-postal-code.png)

### <a name="integration-with-azure-functions"></a>Integratie met Azure Functions
U kunt een HTTP-trigger in Azure Functions een eenvoudige manier gebruiken om een API te maken voor gebruik met de API-connector. U gebruikt de functie Azure om [bijvoorbeeld](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)validatie logica uit te voeren en aanmeldings pogingen te beperken tot specifieke domeinen. U kunt ook andere web-Api's, gebruikers archieven en andere Cloud Services aanroepen en aanroepen.

## <a name="next-steps"></a>Volgende stappen

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Meer informatie over het [toevoegen van een aangepaste goedkeurings werk stroom aan self-service registratie](self-service-sign-up-add-approvals.md)
- Ga aan de slag met onze voor [beelden van Azure function Quick](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)start.
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
