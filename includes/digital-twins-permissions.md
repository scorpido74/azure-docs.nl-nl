---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
ms.custom: include file
ms.openlocfilehash: cfe3eb4c0ac1378b7c519b3b34094945612d8508
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029269"
---
>[!NOTE]
>Deze sectie bevat instructies voor de [registratie van Azure AD-apps](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Open in de [Azure Portal](https://portal.azure.com) **Azure Active Directory** in het menu uitbreidbaar links en open vervolgens het **app-registraties** deel venster. 

    [het deel venster Azure Active Directory ![selecteren](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Selecteer de knop **+ nieuwe registratie** .

    [![de knop nieuwe registratie selecteren](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Geef een beschrijvende naam voor deze app-registratie op in het vak **Naam**. 

    1. Voer `https://microsoft.com` in het tekstvak in bij **omleidings-URI (optioneel)** .     

    1. Controleer welke accounts en tenants worden ondersteund door uw Azure Active Directory-app.

    1. Selecteer **Registreren**.

    [deel venster ![maken](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. De Blade **verificatie** specificeert belang rijke instellingen voor verificatie configuratie. 

    1. Voeg **omleidings-uri's** toe en configureer **toegangs tokens** door **+ een platform toe te voegen**.

    1. Selecteer **Ja** om op te geven dat de app een **open bare client**is.

    1. Controleer welke accounts en tenants worden ondersteund door uw Azure Active Directory-app.

    [configuratie-instelling ![open bare client](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. Nadat u het juiste platform hebt geselecteerd, configureert u de **omleidings-uri's** en **toegangs tokens** in het deel venster aan de rechter kant van de gebruikers interface.

    1. **Omleidings-uri's** moeten overeenkomen met het adres dat is opgegeven door de verificatie aanvraag:

        * Voor apps die worden gehost in een lokale ontwikkel omgeving selecteert u **open bare client (mobiele & bureau blad)** . Zorg ervoor dat de **open bare client** is ingesteld op **Ja**.
        * Voor apps met één pagina die worden gehost op Azure App Service, selecteert u **Web**.

    1. Bepaal of een **Afmeldings-URL** geschikt is.

    1. Schakel de impliciete toekennings stroom in door **toegangs tokens** of **id-tokens**te controleren.
                
    [omleidings-Uri's ![configureren](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Klik op **configureren**en vervolgens op **Opslaan**.

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
