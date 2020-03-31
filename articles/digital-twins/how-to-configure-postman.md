---
title: Postbode configureren - Azure Digital Twins | Microsoft Documenten
description: Meer informatie over het configureren en gebruiken van Postman om de Azure Digital Twins API's te testen.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297161"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Postbode configureren voor Azure Digital Twins

In dit artikel wordt beschreven hoe u de Postman REST-client configureert om te communiceren met en de Azure Digital Twins Management API's te testen. In het bijzonder beschrijft het:

* Een Azure Active Directory-toepassing configureren om de impliciete subsidiestroom van OAuth 2.0 te gebruiken.
* De Postman REST-client gebruiken om HTTP-aanvragen voor tokendragende producten te maken voor uw beheer-API's.
* Hoe postman te gebruiken om meerdelige POST-verzoeken te doen aan uw Management API's.

## <a name="postman-summary"></a>Postman samenvatting

Ga aan de slag met Azure Digital Twins met behulp van een REST-clienttool zoals [Postman](https://www.getpostman.com/) om uw lokale testomgeving voor te bereiden. De Postman-client helpt om snel complexe HTTP-aanvragen te maken. Download de desktopversie van de Postman-client door naar [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) is een REST-testtool die belangrijke HTTP-aanvraagfunctionaliteiten in een nuttige gui op desktop en plug-in lokaliseert.

Via de Postman-client kunnen ontwikkelaars van oplossingen het soort HTTP-aanvraag opgeven (*POST*, *GET*, *UPDATE*, *PATCH*en *Delete),* API-eindpunt om aan te roepen en het gebruik van TLS. Postman ondersteunt ook het toevoegen van HTTP-aanvraagkoppen, parameters, formuliergegevens en -instanties.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Azure Active Directory configureren om de impliciete subsidiestroom van OAuth 2.0 te gebruiken

1. Volg de stappen in [de Quickstart](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) om een Azure Active Directory-toepassing te maken en te configureren. U ook een bestaande app-registratie opnieuw gebruiken.

    [![Een nieuwe postman redirect URI configureren](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Voeg nu een **OmleidingsURI** toe aan `https://www.getpostman.com/oauth2/callback`.

1. Schakel het selectievakje Impliciete > **toekenningstoegangstokens** in om de impliciete subsidiestroom van OAuth 2.0 te kunnen gebruiken. **Implicit grant** Selecteer **Configureren**en vervolgens **Opslaan**.

1. Kopieer de **client-id** van uw Azure Active Directory-app.

## <a name="obtain-an-oauth-20-token"></a>Een OAuth 2.0-token verkrijgen

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Postman instellen en configureren om een Azure Active Directory-token te verkrijgen. Vervolgens moet u een geverifieerd HTTP-verzoek indienen bij Azure Digital Twins met behulp van het verkregen token:

1. Controleer of de **URL van autorisatie** juist is. Het moet het formaat:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name  | Vervangen door | Voorbeeld |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | De naam van uw huurder of organisatie. Gebruik de mensvriendelijke naam in plaats van de alfanumerieke **tenant-id** van uw Azure Active Directory-appregistratie. | `microsoft` |

1. Ga naar [www.getpostman.com](https://www.getpostman.com/) om de app te downloaden.

1. We willen get verzoek te maken. Selecteer het tabblad **Autorisatie,** selecteer OAuth 2.0 en selecteer **Vervolgens Nieuw toegangstoken opbrengen**.

    | Veld  | Waarde |
    |---------|---------|
    | Toekenningstype | `Implicit` |
    | Callback-URL | `https://www.getpostman.com/oauth2/callback` |
    | Auth.-URL | De **URL van autorisatie** gebruiken vanaf **stap 1** |
    | Client-id | De **toepassings-id gebruiken** voor de Azure Active Directory-app die is gemaakt of hergebruikt uit de vorige sectie |
    | Bereik | Leeg laten |
    | Status | Leeg laten |
    | Clientverificatie | `Send as Basic Auth header` |

1. De client moet nu worden weergegeven als:

    [![Voorbeeld van postmanclienttoken](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Selecteer **Token aanvragen**.
  
1. Schuif omlaag en selecteer **Token gebruiken**.

## <a name="make-a-multipart-post-request"></a>Een meerdelige POST-aanvraag indienen

Configureer Na het voltooien van de vorige stappen de postman om een geverifieerd HTTP-berichtverzoek voor meerdere delen in te dienen:

1. Voeg onder het tabblad **Kopteksten** een HTTP-aanvraagkoptoets **Inhoudstype** met waarde `multipart/mixed`toe.

   [![Inhoudstype opgeven, meerdelige/gemengde](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Niet-tekstgegevens serialiseren in bestanden. JSON-gegevens worden opgeslagen als een JSON-bestand.
1. Selecteer **Body** onder het `form-data`tabblad Hoofd de optie . 
1. Voeg elk bestand toe door een `File` **sleutelnaam** toe te kennen en .
1. Selecteer vervolgens elk bestand via de knop **Bestand kiezen.**

   [![Postman client vorm lichaam voorbeeld](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * De Postman-client vereist niet dat meerdelige segmenten een handmatig toegewezen **Inhoudstype** of **Inhoudsbeschikking**hebben.
   > * U hoeft deze kopteksten niet voor elk onderdeel op te geven.
   > * U moet `multipart/mixed` voor het hele verzoek een ander **geschikt inhoudstype** selecteren of een ander geschikt inhoudstype selecteren.

1. Tot slot selecteert u **Verzenden** om uw meerdelige HTTP POST-aanvraag in te dienen. Een statuscode `200` `201` van of geeft een geslaagde aanvraag aan. Het juiste antwoordbericht wordt weergegeven in de clientinterface.

1. Valideer uw HTTP POST-aanvraaggegevens door het API-eindpunt aan te roepen: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Volgende stappen

- Lees Hoe u [Azure Digital Twins-beheerAPI's gebruiken](how-to-navigate-apis.md)voor meer informatie over de API's voor beheer van Digital Twins en hoe u deze gebruiken.

- Gebruik meerdelige aanvragen om [blobs toe te voegen aan de entiteiten van Azure Digital Twins.](./how-to-add-blobs.md)

- Lees [Authenticeren met API's](./security-authenticating-apis.md)voor meer informatie over het verifiëren met de beheer-API's.
