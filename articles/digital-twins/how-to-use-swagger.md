---
title: Naslag informatie over Swagger gebruiken-Azure Digital Apparaatdubbels | Microsoft Docs
description: Meer informatie over het gebruik van Azure Digital Apparaatdubbels Swagger naslag documentatie.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023290"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Naslag documentatie voor Azure Digital Apparaatdubbels Swagger

Elk ingericht Azure Digital Apparaatdubbels-exemplaar bevat een eigen automatisch gegenereerde Swagger-referentie documentatie.

[Swagger](https://swagger.io/), of [OpenAPI](https://www.openapis.org/), bedeelt complexe API-informatie in een interactieve en taal-neutraal verwijzings bron. Swagger biedt essentiële referentie materiaal over welke JSON-nettoladingen, HTTP-methoden en specifieke eind punten moeten worden gebruikt om bewerkingen uit te voeren op een API.

## <a name="swagger-summary"></a>Swagger-samen vatting

Swagger biedt een interactieve samen vatting van uw API, waaronder:

* Informatie over API en object model.
* REST API eind punten die de vereiste nettoladingen voor aanvragen, headers, para meters, context paden en HTTP-methoden opgeven.
* Testen van API-functionaliteiten.
* Voorbeeld reactie gegevens die worden gebruikt om HTTP-antwoorden te valideren en te bevestigen.
* Informatie over de fout code.

Swagger is een handig hulp middel om te helpen bij het ontwikkelen en testen van aanroepen naar de Azure Digital Apparaatdubbels Management-Api's.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Naslagmateriaal

Het automatisch gegenereerde Swagger-referentie materiaal bevat een kort overzicht van belang rijke concepten, beschik bare Management API-eind punten en een beschrijving van elk object model om te helpen bij het ontwikkelen en testen.

Een beknopt overzicht bevat een beschrijving van de API.

[overzicht van informatie over ![Swagger en API](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Beheer-API-object modellen worden ook weer gegeven.

[![Swagger-modellen onder in de Swagger-gebruikers interface](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

U kunt elk weer gegeven object model selecteren voor een gedetailleerd overzicht van belang rijke kenmerken.

[![Swagger-modellen uitgebreid om de inhoud van modellen te lezen](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

De gegenereerde Swagger-object modellen zijn handig om alle beschik bare [objecten en api's](./concepts-objectmodel-spatialgraph.md)van Azure Digital apparaatdubbels te lezen. Ontwikkel aars kunnen deze resource gebruiken wanneer ze oplossingen bouwen op Azure Digital Apparaatdubbels.

## <a name="endpoint-summary"></a>Eind punt overzicht

Swagger biedt ook een uitgebreid overzicht van alle eind punten die de beheer-Api's vormen.

Elk eind punt in de lijst bevat ook de vereiste aanvraag informatie, zoals:

* Vereiste para meters.
* Vereiste parameter gegevens typen.
* HTTP-methode voor toegang tot de bron.

[![Swagger-eind punten weer gegeven in de Swagger-gebruikers interface](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Selecteer elke resource om de extra inhoud weer te geven voor een gedetailleerder overzicht.

## <a name="use-swagger-to-test-endpoints"></a>Swagger gebruiken om eind punten te testen

Een van de krachtige functies Swagger biedt de mogelijkheid om een API-eind punt rechtstreeks te testen via de gebruikers interface van de documentatie.

Wanneer u een bepaald eind punt selecteert, wordt de knop **try-out** weer gegeven.

[knop ![Swagger try-out](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Vouw deze sectie uit om invoer velden weer te geven voor elke vereiste en optionele para meter. Voer de juiste waarden in en selecteer **uitvoeren**.

[voor beeld van het resultaat van ![Swagger](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Nadat u de test hebt uitgevoerd, kunt u de antwoord gegevens valideren.

## <a name="swagger-response-data"></a>Reactie gegevens van Swagger

Elk eind punt in de lijst bevat ook gegevens over de antwoord tekst om uw ontwikkeling en tests te valideren. Deze voor beelden bevatten de status codes en JSON voor geslaagde HTTP-aanvragen.

[voor beeld van JSON-antwoord ![Swagger](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

De voor beelden bevatten ook fout codes voor het oplossen van fouten of het verbeteren van mislukte testen.

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2,0-autorisatie

> [!NOTE]
> * De gebruikers-principal die de Azure Digital Apparaatdubbels-resource heeft gemaakt, heeft een rol voor ruimte-beheerdersrol en kan extra roltoewijzingen maken voor andere gebruikers. Deze gebruikers en hun rollen kunnen worden gemachtigd om de Api's aan te roepen.

1. Volg de stappen in [de Quick](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) start om een Azure Active Directory-toepassing te maken en te configureren. U kunt ook een bestaande app-registratie hergebruiken.

1. Voeg de volgende **omleidings-URI** toe aan de registratie van uw Azure Active Directory-app:

    [URL voor Swagger-omleiding ![registreren in AAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Name  | Vervangen door | Voorbeeld |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Uw management REST API-documentatie-URL gevonden in de portal  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Schakel het selectie vakje impliciete **toegangs tokens** voor **granting** > in om toe te staan dat de OAuth 2,0 impliciete toekennings stroom moet worden gebruikt. Selecteer **configureren**en vervolgens **Opslaan**.

1. Kopieer de **client-id** van uw Azure Active Directory-app.

Na het volt ooien van de Azure Active Directory registratie:

1. Selecteer de knop **autoriseren** op uw Swagger-pagina.

    [![de knop voor Swagger-autorisatie selecteren](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Plak de toepassings-ID in het veld **client_id** .

    [client_id veld voor Swagger ![](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. U wordt vervolgens doorgestuurd naar het volgende geslaagde modale.

    [![Swagger-omleiding modaal](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Lees de [officiële documentatie](https://swagger.io/docs/specification/authentication/oauth2/)voor meer informatie over het interactief testen van aanvragen die worden beveiligd door OAuth 2,0.

## <a name="next-steps"></a>Volgende stappen

- Lees informatie over [Azure Digital apparaatdubbels-object modellen](./concepts-objectmodel-spatialgraph.md)voor meer informatie over Azure Digital apparaatdubbels-object modellen en de ruimtelijke Intelligence-grafiek.

- Lees [verificatie met api's](./security-authenticating-apis.md)voor meer informatie over het verifiëren met uw beheer-API.
