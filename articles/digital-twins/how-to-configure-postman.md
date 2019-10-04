---
title: Postman configureren voor Azure Digital Apparaatdubbels | Microsoft Docs
description: Postman configureren voor Azure Digital Apparaatdubbels.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 14e6a52f86586eaae019d9658c2f813a15fc3474
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949211"
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

Configureer uw Azure Active Directory-app om de OAuth 2,0 impliciet-toekennings stroom te gebruiken.

1. Open het deel venster **API-machtigingen** voor de registratie van uw app. Selecteer **een machtigings knop toevoegen** . Selecteer in het deel venster **API-machtigingen voor aanvragen** de api's die **Mijn organisatie gebruikt** tabblad en zoek naar:
    
    1. `Azure Digital Twins`. Selecteer de **Azure Digital apparaatdubbels** -API.

        [![Search-API of Azure Digital Apparaatdubbels](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png)](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. U kunt ook zoeken naar `Azure Smart Spaces Service`. Selecteer de **Azure Smart Spaces-service** -API.

        [![Search-API voor Azure Smart Spaces](../../includes/media/digital-twins-permissions/aad-app-search-api.png)](../../includes/media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > De naam en ID van de Azure AD-API die wordt weer gegeven, is afhankelijk van uw Tenant:
    > * Test Tenant-en klant accounts moeten zoeken naar `Azure Digital Twins`.
    > * Andere micro soft-accounts moeten zoeken naar `Azure Smart Spaces Service`.

1. De geselecteerde API wordt weer gegeven als **Azure Digital apparaatdubbels** in het deel venster **API-machtigingen voor aanvragen** . Selecteer de vervolg keuzelijst **lezen (1)** en selecteer vervolgens **lezen. Schrijf** selectie vakje. Selecteer de knop **machtigingen toevoegen** .

    [![API-machtigingen toevoegen](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Afhankelijk van de instellingen van uw organisatie moet u mogelijk extra stappen uitvoeren om beheerders toegang tot deze API te verlenen. Neem contact op met de beheerder voor meer informatie. Zodra de beheerders toegang is goedgekeurd, wordt de kolom **beheerder toestemming vereist** in het deel venster **API-machtigingen** weer gegeven, zoals in het volgende voor uw api's:

    [![API-machtigingen toevoegen](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png)](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)


1. Selecteer **manifest** om het toepassings manifest voor uw app te openen. Stel *oauth2AllowImplicitFlow* in op `true`.

    [![Azure Active Directory impliciete stroom](media/how-to-configure-postman/implicit-flow.png)](media/how-to-configure-postman/implicit-flow.png#lightbox)

1. Configureer een **antwoord-URL** naar `https://www.getpostman.com/oauth2/callback`.

    [![Azure Active Directory antwoord-URL](media/how-to-configure-postman/reply-url.png)](media/how-to-configure-postman/reply-url.png#lightbox)

1. Kopieer de **toepassings-id** van uw Azure Active Directory-app en bewaar deze. Dit wordt gebruikt in de volgende stappen.

   [![Azure Active Directory-toepassings-ID](../../includes/media/digital-twins-permissions/aad-app-reg-app-id.png)](../../includes/media//digital-twins-permissions/aad-app-reg-app-id.png#lightbox)


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
    | Toekennings type | `Implicit` |
    | URL voor terugbellen | `https://www.getpostman.com/oauth2/callback` |
    | Verificatie-URL | Gebruik de **autorisatie-URL** uit **stap 2** |
    | Client-id | Gebruik de **toepassings-id** voor de app Azure Active Directory die is gemaakt of opnieuw is gebruikt in de vorige sectie |
    | Scope | Leeg laten |
    | State | Leeg laten |
    | Clientverificatie | `Send as Basic Auth header` |

1. De client moet er nu als volgt uitzien:

    [voor beeld van ![Postman-client](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. Selecteer een **aanvraag token**.

    >[!TIP]
    >Als het fout bericht ' OAuth 2 kan niet worden voltooid ' wordt weer gegeven, gaat u als volgt te werk:
    > * Sluit de Postman en open deze opnieuw en probeer het opnieuw.
  
1. Schuif omlaag en selecteer **token gebruiken**.

## <a name="make-a-multipart-post-request"></a>Een meerdelige POST-aanvraag maken

Na het volt ooien van de vorige stappen, moet u na het uitvoeren van een geverifieerde HTTP meerdelige POST-aanvraag:

1. Voeg op het tabblad **koptekst** een sleutel inhoud van een HTTP-aanvraag header toe **-Type** met waarde `multipart/mixed`.

   [![Content type multi part/Mixed](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Serialisatie van niet-tekst gegevens in bestanden. JSON-gegevens worden opgeslagen als een JSON-bestand.
1. Selecteer op het tabblad **hoofd tekst** `form-data`. 
1. Voeg elk bestand toe door een **sleutel** naam toe te wijzen en `file` te selecteren.
1. Selecteer vervolgens elk bestand via de knop **bestand kiezen** .

   [voor beeld van ![Postman-client](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * De Postman-client vereist niet dat meerdelige segmenten een hand matig toegewezen **inhouds type** of **Content-Disposition**hebben.
   > * U hoeft deze headers niet voor elk onderdeel op te geven.
   > * U moet `multipart/mixed` of een ander toepasselijk **type inhoud** voor de hele aanvraag selecteren.

1. Selecteer ten slotte **verzenden** om uw multi part HTTP POST-aanvraag te verzenden.

## <a name="next-steps"></a>Volgende stappen

- Lees [hoe u Azure Digital apparaatdubbels Management-api's kunt gebruiken](how-to-navigate-apis.md)voor meer informatie over de Api's voor Digital apparaatdubbels-beheer en hoe u deze kunt gebruiken.

- Gebruik meerdelige aanvragen om [blobs toe te voegen aan Azure Digital apparaatdubbels-entiteiten](./how-to-add-blobs.md).

- Lees [verificatie met api's](./security-authenticating-apis.md)voor meer informatie over verificatie met de beheer-api's.
