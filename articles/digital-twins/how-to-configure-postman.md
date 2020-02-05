---
title: Postman-Azure Digital Apparaatdubbels configureren | Microsoft Docs
description: Meer informatie over het configureren en gebruiken van Postman voor het testen van de Azure Digital Apparaatdubbels-Api's.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 377639d7a88478308709743ab842db71028686ed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023307"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Postman configureren voor Azure Digital Apparaatdubbels

In dit artikel wordt beschreven hoe u de Postman REST-client kunt configureren om te communiceren met en de Azure Digital Apparaatdubbels Management-Api's te testen. In het bijzonder wordt beschreven:

* Een Azure Active Directory-toepassing configureren voor het gebruik van de impliciete OAuth 2,0-toekennings stroom.
* Het gebruik van de Postman REST-client om token-HTTP-aanvragen te maken voor uw beheer-Api's.
* Postman gebruiken om meerdelige POST-aanvragen te maken voor uw beheer-Api's.

## <a name="postman-summary"></a>Overzicht van postman

Ga aan de slag met Azure Digital Apparaatdubbels door gebruik te maken van een REST-client hulpprogramma, zoals [postman](https://www.getpostman.com/) , om uw lokale test omgeving voor te bereiden. De Postman-client helpt snel complexe HTTP-aanvragen te maken. Down load de desktop versie van de Postman-client door naar [www.getpostman.com/apps](https://www.getpostman.com/apps)te gaan.

[Postman](https://www.getpostman.com/) is een hulp programma voor rest testen dat de belangrijkste functies voor HTTP-aanvragen in een handige, op bureau blad gebaseerde gebruikers interface zoekt.

Met de Postman-client kunnen ontwikkel aars van oplossingen het type HTTP-aanvraag (*post*, *Get*, *Update*, *patch*en *Delete*), het API-eind punt voor het aanroepen en het gebruik van SSL opgeven. Postman biedt ook ondersteuning voor het toevoegen van HTTP-aanvraag headers, para meters, formulier gegevens en instanties.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Azure Active Directory configureren voor het gebruik van de OAuth 2,0 impliciete toekennings stroom

1. Volg de stappen in [de Quick](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) start om een Azure Active Directory-toepassing te maken en te configureren. U kunt ook een bestaande app-registratie hergebruiken.

    [![een nieuwe postman omleidings-URI configureren](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Voeg nu een **omleidings-URI** toe aan `https://www.getpostman.com/oauth2/callback`.

1. Schakel het selectie vakje impliciete **toegangs tokens** voor **granting** > in om toe te staan dat de OAuth 2,0 impliciete toekennings stroom moet worden gebruikt. Selecteer **configureren**en vervolgens **Opslaan**.

1. Kopieer de **client-id** van uw Azure Active Directory-app.

## <a name="obtain-an-oauth-20-token"></a>Een OAuth 2,0-token verkrijgen

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Postman instellen en configureren om een Azure Active Directory token te verkrijgen. Vervolgens maakt u een geverifieerde HTTP-aanvraag voor Azure Digital Apparaatdubbels met behulp van het verkregen token:

1. Controleer of uw **autorisatie-URL** juist is. De notatie moet de volgende indeling hebben:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name  | Vervangen door | Voorbeeld |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | De naam van uw Tenant of organisatie. Gebruik de mensen vriendelijke naam in plaats van de alfanumerieke **Tenant-id** van uw Azure Active Directory app-registratie. | `microsoft` |

1. Ga naar [www.getpostman.com](https://www.getpostman.com/) om de app te downloaden.

1. We willen een GET-aanvraag maken. Selecteer het tabblad **autorisatie** , selecteer OAuth 2,0 en selecteer vervolgens **nieuw toegangs Token ophalen**.

    | Veld  | Waarde |
    |---------|---------|
    | Toekennings type | `Implicit` |
    | URL voor terugbellen | `https://www.getpostman.com/oauth2/callback` |
    | Verificatie-URL | Gebruik de **autorisatie-URL** uit **stap 1** |
    | Client-id | Gebruik de **toepassings-id** voor de app Azure Active Directory die is gemaakt of opnieuw is gebruikt in de vorige sectie |
    | Scope | Leeg laten |
    | Staat | Leeg laten |
    | Client verificatie | `Send as Basic Auth header` |

1. De client moet er nu als volgt uitzien:

    [![postman client token-voor beeld](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Selecteer een **aanvraag token**.
  
1. Schuif omlaag en selecteer **token gebruiken**.

## <a name="make-a-multipart-post-request"></a>Een meerdelige POST-aanvraag maken

Na het volt ooien van de vorige stappen, moet u na het uitvoeren van een geverifieerde HTTP meerdelige POST-aanvraag:

1. Voeg op het tabblad **headers** het **inhouds type http-** aanvraag header toe met waarde `multipart/mixed`.

   [![type multi part/Mixed opgeven](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serialisatie van niet-tekst gegevens in bestanden. JSON-gegevens worden opgeslagen als een JSON-bestand.
1. Selecteer `form-data`op het tabblad **hoofd tekst** . 
1. Voeg elk bestand toe door een **sleutel** naam toe te wijzen en `File`te selecteren.
1. Selecteer vervolgens elk bestand via de knop **bestand kiezen** .

   [![postman-client voor beeld van hoofd tekst](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * De Postman-client vereist niet dat meerdelige segmenten een hand matig toegewezen **inhouds type** of **Content-Disposition**hebben.
   > * U hoeft deze headers niet voor elk onderdeel op te geven.
   > * U moet `multipart/mixed` of een ander toepasselijk **type inhoud** voor de hele aanvraag selecteren.

1. Selecteer ten slotte **verzenden** om uw multi part HTTP POST-aanvraag te verzenden. Een status code van `200` of `201` geeft een geslaagde aanvraag aan. Het juiste antwoord bericht wordt weer gegeven in de client interface.

1. Valideer uw HTTP POST-aanvraag gegevens door het API-eind punt aan te roepen: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Volgende stappen

- Lees [hoe u Azure Digital apparaatdubbels Management-api's kunt gebruiken](how-to-navigate-apis.md)voor meer informatie over de Api's voor Digital apparaatdubbels-beheer en hoe u deze kunt gebruiken.

- Gebruik meerdelige aanvragen om [blobs toe te voegen aan Azure Digital apparaatdubbels-entiteiten](./how-to-add-blobs.md).

- Lees [verificatie met api's](./security-authenticating-apis.md)voor meer informatie over verificatie met de beheer-api's.
