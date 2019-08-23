---
title: Postman configureren voor Azure Digital Apparaatdubbels | Microsoft Docs
description: Postman configureren voor Azure Digital Apparaatdubbels.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: v-adgera
ms.openlocfilehash: a39663adedfdb9c00c4429f65ec1bd27286cb136
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904298"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Postman configureren voor Azure Digital Apparaatdubbels

In dit artikel wordt beschreven hoe u de Postman REST-client kunt configureren om te communiceren met en de Azure Digital Apparaatdubbels Management-Api's te testen. In het bijzonder wordt beschreven:

* Een Azure Active Directory-toepassing configureren voor het gebruik van de impliciete OAuth 2,0-toekennings stroom.
* Het gebruik van de Postman REST-client om token-HTTP-aanvragen te maken voor uw beheer-Api's.
* Postman gebruiken om meerdelige POST-aanvragen te maken voor uw beheer-Api's.

## <a name="postman-summary"></a>Overzicht van postman

Ga aan de slag met Azure Digital Apparaatdubbels door gebruik te maken van een [](https://www.getpostman.com/) rest-client hulpprogramma, zoals Postman, om uw lokale test omgeving voor te bereiden. De Postman-client helpt snel complexe HTTP-aanvragen te maken. Down load de desktop versie van de Postman-client door naar [www.getpostman.com/apps](https://www.getpostman.com/apps)te gaan.

[Postman](https://www.getpostman.com/) is een hulp programma voor rest testen dat de belangrijkste functies voor HTTP-aanvragen in een handige, op bureau blad gebaseerde gebruikers interface zoekt.

Met de Postman-client kunnen ontwikkel aars van oplossingen het type HTTP-aanvraag (*post*, *Get*, *Update*, *patch*en *Delete*), het API-eind punt voor het aanroepen en het gebruik van SSL opgeven. Postman biedt ook ondersteuning voor het toevoegen van HTTP-aanvraag headers, para meters, formulier gegevens en instanties.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Azure Active Directory configureren voor het gebruik van de OAuth 2,0 impliciete toekennings stroom

Configureer uw Azure Active Directory-app om de OAuth 2,0 impliciet-toekennings stroom te gebruiken.

1. Volg de stappen in [onze Snelstartgids](./quickstart-view-occupancy-dotnet.md) om een Azure AD-toepassing te maken. U kunt ook een [systeem eigen app maken met behulp van de oude Aad](./how-to-use-legacy-aad.md)-Blade.

1. Selecteer **een machtiging toevoegen**onder **API-machtigingen**. Vervolgens maakt **Azure Digital apparaatdubbels** onder **api's mijn organisatie gebruik**van. Als de API met uw zoekopdracht niet wordt gevonden, zoekt u in plaats daarvan **Azure Smart Spaces**. Selecteer vervolgens **gedelegeerde machtigingen**, **Lees** > **lezen. schrijven**en **machtigingen toevoegen**.

    [![API-registraties toevoegen Azure Active Directory app](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Selecteer **manifest** om het toepassings manifest voor uw app te openen. Stel *oauth2AllowImplicitFlow* in `true`op.

    [![Azure Active Directory impliciete stroom](media/how-to-configure-postman/implicit-flow.png)](media/how-to-configure-postman/implicit-flow.png#lightbox)

1. Configureer een **antwoord** -URL `https://www.getpostman.com/oauth2/callback`naar.

    [![Azure Active Directory antwoord-URL](media/how-to-configure-postman/reply-url.png)](media/how-to-configure-postman/reply-url.png#lightbox)

1. Kopieer de **toepassings-id** van uw Azure Active Directory-app en bewaar deze. Dit wordt gebruikt in de volgende stappen.

## <a name="obtain-an-oauth-20-token"></a>Een OAuth 2,0-token verkrijgen

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Postman instellen en configureren om een Azure Active Directory token te verkrijgen. Vervolgens maakt u een geverifieerde HTTP-aanvraag voor Azure Digital Apparaatdubbels met behulp van het verkregen token:

1. Ga naar [www.getpostman.com](https://www.getpostman.com/) om de app te downloaden.
1. Controleer of uw **autorisatie-URL** juist is. De notatie moet de volgende indeling hebben:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name  | Vervangen door | Voorbeeld |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | De naam van uw Tenant of organisatie | `microsoft` |

1. Selecteer het tabblad **autorisatie** , selecteer **OAuth 2,0**en selecteer vervolgens **nieuw toegangs Token ophalen**.

    | Veld  | Value |
    |---------|---------|
    | Toekenningstype | `Implicit` |
    | URL voor terugbellen | `https://www.getpostman.com/oauth2/callback` |
    | Verificatie-URL | Gebruik de **autorisatie-URL** uit stap 2 |
    | Client-id | Gebruik de **toepassings-id** voor de app Azure Active Directory die is gemaakt of opnieuw is gebruikt in de vorige sectie |
    | Scope | Leeg laten |
    | State | Leeg laten |
    | Clientverificatie | `Send as Basic Auth header` |

1. De client moet er nu als volgt uitzien:

    [![Voor beeld van Postman-client](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. Selecteer een **aanvraag token**.

    >[!TIP]
    >Als het fout bericht ' OAuth 2 kan niet worden voltooid ' wordt weer gegeven, gaat u als volgt te werk:
    > * Sluit de Postman en open deze opnieuw en probeer het opnieuw.
  
1. Schuif omlaag en selecteer **token gebruiken**.

<div id="multi"></div>

## <a name="make-a-multipart-post-request"></a>Een meerdelige POST-aanvraag maken

Na het volt ooien van de vorige stappen, moet u na het uitvoeren van een geverifieerde HTTP meerdelige POST-aanvraag:

1. Voeg op het tabblad **koptekst** een sleutel inhoud van een HTTP-aanvraag header toe **-type** met waarde `multipart/mixed`.

   [![Meerdelige/gemengd inhouds type](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Serialisatie van niet-tekst gegevens in bestanden. JSON-gegevens worden opgeslagen als een JSON-bestand.
1. Voeg op het tabblad **hoofd tekst** elk bestand toe door een **sleutel** naam toe te `file` wijzen `text`en te selecteren.
1. Selecteer vervolgens elk bestand via de knop **bestand kiezen** .

   [![Voor beeld van Postman-client](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * De Postman-client vereist niet dat meerdelige segmenten een hand matig toegewezen **inhouds type** of **Content-Disposition**hebben.
   > * U hoeft deze headers niet voor elk onderdeel op te geven.
   > * U moet of `multipart/mixed` een ander toepasselijk **type inhoud** voor de hele aanvraag selecteren.

1. Selecteer ten slotte **verzenden** om uw multi part HTTP POST-aanvraag te verzenden.

## <a name="next-steps"></a>Volgende stappen

- Lees [hoe u Azure Digital apparaatdubbels Management-api's kunt gebruiken](how-to-navigate-apis.md)voor meer informatie over de Api's voor Digital apparaatdubbels-beheer en hoe u deze kunt gebruiken.

- Gebruik meerdelige aanvragen om [blobs toe te voegen aan Azure Digital apparaatdubbels-entiteiten](./how-to-add-blobs.md).

- Lees [verificatie met api's](./security-authenticating-apis.md)voor meer informatie over verificatie met de beheer-api's.
