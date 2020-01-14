---
title: Postman-Azure Digital Apparaatdubbels configureren | Microsoft Docs
description: Meer informatie over het configureren en gebruiken van Postman voor het testen van de Azure Digital Apparaatdubbels-Api's.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: 3d0220f23c8098222b93473dc6c7aa7a4f2dd791
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933446"
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

    [API-machtigingen voor Azure Digital Apparaatdubbels ![toevoegen](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Afhankelijk van de instellingen van uw organisatie moet u mogelijk extra stappen uitvoeren om beheerders toegang tot deze API te verlenen. Neem contact op met de beheerder voor meer informatie. Zodra de beheerders toegang is goedgekeurd, wordt de kolom **beheerder toestemming vereist** in het deel venster **API-machtigingen** weer gegeven, zoals in het volgende voor uw api's:

    [goed keuring van beheerders toestemming ![configureren](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png)](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

1. Configureer een tweede **omleidings-URI** naar `https://www.getpostman.com/oauth2/callback`.

    [![een nieuwe postman omleidings-URI configureren](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Als u er zeker van wilt zijn dat [de app is geregistreerd als een **open bare client**](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), opent u het deel venster **verificatie** voor uw app-registratie en schuift u omlaag in het deel venster. Kies in het gedeelte **standaard client type** de optie **Ja** voor **behandelen van toepassing als een open bare client**en druk op **Opslaan**.

    Controleer **toegangs tokens** om de instelling **Oauth2AllowImplicitFlow** in uw manifest. json in te scha kelen.

    [configuratie-instelling ![open bare client](../../includes/media/digital-twins-permissions/aad-configure-public-client.png)](../../includes/media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. Kopieer de **toepassings-id** van uw Azure Active Directory-app en bewaar deze. Dit wordt gebruikt in de volgende stappen.

   [Azure Active Directory toepassings-ID ![](../../includes/media/digital-twins-permissions/aad-app-reg-app-id.png)](../../includes/media//digital-twins-permissions/aad-app-reg-app-id.png#lightbox)


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

    | Veld  | Waarde |
    |---------|---------|
    | Toekenningstype | `Implicit` |
    | Callback-URL | `https://www.getpostman.com/oauth2/callback` |
    | Auth.-URL | Gebruik de **autorisatie-URL** uit **stap 2** |
    | Client-id | Gebruik de **toepassings-id** voor de app Azure Active Directory die is gemaakt of opnieuw is gebruikt in de vorige sectie |
    | Scope | Leeg laten |
    | Staat | Leeg laten |
    | Clientauthenticatie | `Send as Basic Auth header` |

1. De client moet er nu als volgt uitzien:

    [![postman client token-voor beeld](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Selecteer **Token aanvragen**.
  
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
