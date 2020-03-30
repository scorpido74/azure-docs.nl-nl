---
title: Referentie-Swagger-documentatie gebruiken - Azure Digital Twins | Microsoft Documenten
description: Inzicht in het gebruik van Azure Digital Twins Swagger-referentiedocumentatie.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023290"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Azure Digital Twins Swagger referentiedocumentatie

Elk ingerichte Azure Digital Twins-exemplaar bevat zijn eigen automatisch gegenereerde Swagger-referentiedocumentatie.

[Swagger](https://swagger.io/), of [OpenAPI,](https://www.openapis.org/)verenigt complexe API-informatie in een interactieve en taal-agnostische referentiebron. Swagger biedt kritisch referentiemateriaal waarover JSON-payloads, HTTP-methoden en specifieke eindpunten moeten worden gebruikt om bewerkingen uit te voeren tegen een API.

## <a name="swagger-summary"></a>Swagger samenvatting

Swagger biedt een interactieve samenvatting van uw API, waaronder:

* API- en objectmodelgegevens.
* REST API-eindpunten die de vereiste aanvraagpayloads, kopteksten, parameters, contextpaden en HTTP-methoden opgeven.
* Testen van API-functionaliteiten.
* Voorbeeldinformatie die wordt gebruikt om HTTP-antwoorden te valideren en te bevestigen.
* Foutcode-informatie.

Swagger is een handig hulpmiddel om te helpen bij het ontwikkelen en testen van oproepen naar de Azure Digital Twins Management API's.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Referentiemateriaal

Het automatisch gegenereerde Swagger-referentiemateriaal biedt een snel overzicht van belangrijke concepten, beschikbare Management API-eindpunten en een beschrijving van elk objectmodel om de ontwikkeling en het testen te ondersteunen.

Een beknopte samenvatting beschrijft de API.

[![Swagger samenvatting en API overzichtinformatie](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Beheer API-objectmodellen worden ook vermeld.

[![Swagger modellen vermeld aan de onderkant van Swagger UI](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

U elk opgenomen objectmodel selecteren voor een gedetailleerder overzicht van belangrijke kenmerken.

[![Swagger modellen uitgebreid om de inhoud van modellen te lezen](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

De gegenereerde Swagger-objectmodellen zijn handig om alle beschikbare Azure Digital [Twins-objecten en API's](./concepts-objectmodel-spatialgraph.md)te lezen. Ontwikkelaars kunnen deze bron gebruiken wanneer ze oplossingen bouwen op Azure Digital Twins.

## <a name="endpoint-summary"></a>Eindpuntoverzicht

Swagger biedt ook een grondig overzicht van alle eindpunten die de Management API's opstellen.

Elk vermeld eindpunt bevat ook de vereiste aanvraaggegevens, zoals:

* Vereiste parameters.
* Vereiste parametergegevenstypen.
* HTTP-methode om toegang te krijgen tot de bron.

[![Swagger eindpunten weergegeven in Swagger UI](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Selecteer elke resource om de extra inhoud weer te geven voor een gedetailleerder overzicht.

## <a name="use-swagger-to-test-endpoints"></a>Swagger gebruiken om eindpunten te testen

Een van de krachtige functionaliteiten die Swagger biedt, is de mogelijkheid om een API-eindpunt rechtstreeks te testen via de documentatie-gebruikersinterface.

Nadat u een specifiek eindpunt hebt geselecteerd, wordt de knop **Try it out** weergegeven.

[![Swagger Probeer het uit knop](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Vouw die sectie uit om invoervelden weer te geven voor elke vereiste en optionele parameter. Voer de juiste waarden in en selecteer **Uitvoeren**.

[![Swagger Probeer het uit resultaat voorbeeld](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Nadat u de test hebt uitgevoerd, u de responsgegevens valideren.

## <a name="swagger-response-data"></a>Swagger responsgegevens

Elk vermeld eindpunt bevat ook gegevens over de responsbody om uw ontwikkeling en tests te valideren. Deze voorbeelden zijn de statuscodes en JSON voor succesvolle HTTP-aanvragen.

[![Voorbeeld van Swagger JSON response](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

De voorbeelden bevatten ook foutcodes om foutopsporing of verbetering van falende tests te helpen.

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 autorisatie

> [!NOTE]
> * De gebruikersprincipal die de Azure Digital Twins-bron heeft gemaakt, heeft een roltoewijzing voor ruimtebeheerders en kan extra roltoewijzingen maken voor andere gebruikers. Deze gebruikers en hun rollen kunnen worden gemachtigd om de API's te bellen.

1. Volg de stappen in [de Quickstart](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) om een Azure Active Directory-toepassing te maken en te configureren. U ook een bestaande app-registratie opnieuw gebruiken.

1. Voeg de volgende **URI omleiden** toe aan de registratie van uw Azure Active Directory-app:

    [![Swagger redirect url registreren in AAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Name  | Vervangen door | Voorbeeld |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Uw URL van de BeheerREST API-documentatie in de portal  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Schakel het selectievakje Impliciete > **toekenningstoegangstokens** in om de impliciete subsidiestroom van OAuth 2.0 te kunnen gebruiken. **Implicit grant** Selecteer **Configureren**en vervolgens **Opslaan**.

1. Kopieer de **client-id** van uw Azure Active Directory-app.

Na het voltooien van de Azure Active Directory-registratie:

1. Selecteer de knop **Autoriseren** op uw braniepagina.

    [![De knop Swagger autoriseren selecteren](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Plak de toepassings-id in het **client_id** veld.

    [![Swagger client_id gebied](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. U wordt dan doorgestuurd naar het volgende succes modal.

    [![Swagger redirect modal](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Voor meer informatie over interactief testen van aanvragen beschermd door OAuth 2.0, lees de [officiële documentatie](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Volgende stappen

- Lees [De objectmodellen van Begrijpen Azure Digital Twins](./concepts-objectmodel-spatialgraph.md)voor meer informatie over Azure Digital Twins-objectmodellen .

- Lees [Authenticeren met API's](./security-authenticating-apis.md)voor meer informatie over hoe u zich verifiëren met uw Beheer-API.
