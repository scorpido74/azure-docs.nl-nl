---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/23/2020
ms.custom: include file
ms.openlocfilehash: a1576e4a97af5de0b936c662de636aae542a19b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748922"
---
>[!NOTE]
>Deze sectie bevat instructies voor de [registratie van Azure AD-apps](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Open in de [Azure Portal](https://portal.azure.com) **Azure Active Directory** in het menu uitbreidbaar links en open vervolgens het **app-registraties** deel venster. 

    [het deel venster Azure Active Directory ![selecteren](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Selecteer de knop **+ nieuwe registratie** .

    [![de knop nieuwe registratie selecteren](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Geef een beschrijvende naam voor deze app-registratie op in het vak **Naam**. Kies in de vervolg keuzelijst **URI omleiden (optioneel)** de optie **open bare client/systeem eigen (mobiele & bureau blad)** en voer `https://microsoft.com` in het tekstvak aan de rechter kant in. Selecteer **Registreren**.

    [deel venster ![maken](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Als u er zeker van wilt zijn dat [de app is geregistreerd als een **open bare client**](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), opent u het deel venster **verificatie** voor uw app-registratie en schuift u omlaag in het deel venster. Kies in het gedeelte **standaard client type** de optie **Ja** voor **behandelen van toepassing als een open bare client**en druk op **Opslaan**.

    1. **Omleidings-uri's** moeten overeenkomen met het adres dat is opgegeven door de verificatie aanvraag:

        * Voor apps die worden gehost in een lokale ontwikkel omgeving selecteert u **open bare client (mobiele & bureau blad)** . Zorg ervoor dat het **standaard client type** is ingesteld op Ja.
        * Voor apps met één pagina die worden gehost op Azure App Service, selecteert u **Web**.

        Selecteer **open bare client (mobiele & bureau blad)** en voer `http://localhost:8080/`in.

        [omleidings-Uri's ![configureren](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    1. Controleer **toegangs tokens** voor het configureren van de **oauth2AllowImplicitFlow** -instelling voor het `true` in de JSON van de resource **manifest** .

        [configuratie-instelling ![open bare client](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1.  Open het deel venster **overzicht** van de geregistreerde app en kopieer de waarden van de volgende entiteiten naar een tijdelijk bestand. U gebruikt deze waarden om uw voorbeeld toepassing te configureren in de volgende secties.

    - **Toepassings-ID (client)**
    - **Directory-ID (Tenant)**

    [Azure Active Directory toepassings-ID ![](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Open het deel venster **API-machtigingen** voor de registratie van uw app. Selecteer **+ een machtigings knop toevoegen** . Selecteer in het deel venster **API-machtigingen voor aanvragen** de **api's mijn organisatie tabblad gebruikt** en zoek vervolgens een van de volgende opties:
    
    1. `Azure Digital Twins`. Selecteer de **Azure Digital apparaatdubbels** -API.

        [![Search-API of Azure Digital Apparaatdubbels](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. U kunt ook zoeken naar `Azure Smart Spaces Service`. Selecteer de **Azure Smart Spaces-service** -API.

        [![Search-API voor Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > De naam en ID van de Azure AD-API die wordt weer gegeven, is afhankelijk van uw Tenant:
    > * Test Tenant-en klant accounts moeten zoeken naar `Azure Digital Twins`.
    > * Andere micro soft-accounts moeten zoeken naar `Azure Smart Spaces Service`.

1. Een van de API'S wordt weer gegeven als **Azure Digital apparaatdubbels** in het deel venster **API-machtigingen voor aanvragen** nadat het is geselecteerd. Selecteer de vervolg keuzelijst **lezen** en schakel vervolgens het selectie vakje **lezen. schrijven** in. Selecteer de knop **machtigingen toevoegen** .

    [![API-machtigingen toevoegen](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Afhankelijk van de instellingen van uw organisatie moet u mogelijk extra stappen uitvoeren om beheerders toegang tot deze API te verlenen. Neem contact op met de beheerder voor meer informatie. Zodra de beheerders toegang is goedgekeurd, worden in het deel venster API- **machtigingen** uw machtigingen weer gegeven in de kolom **Administrator toestemming vereist** . 

    [goed keuring van ![-beheerder](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Controleer of **Azure Digital apparaatdubbels** wordt weer gegeven.
