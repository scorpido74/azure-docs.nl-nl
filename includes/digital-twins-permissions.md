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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77029269"
---
>[!NOTE]
>Deze sectie bevat instructies voor de [registratie van Azure AD-apps](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Open in de [Azure Portal](https://portal.azure.com) **Azure Active Directory** in het menu uitbreidbaar links en open vervolgens het **app-registraties** deel venster. 

    [![Het deel venster Azure Active Directory selecteren](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Selecteer de knop **+ nieuwe registratie** .

    [![Selecteer de knop nieuwe registratie](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Geef een beschrijvende naam voor deze app-registratie op in het vak **Naam**. 

    1. Typ `https://microsoft.com` in het tekstvak onder **omleidings-URI (optioneel)** .     

    1. Controleer welke accounts en tenants worden ondersteund door uw Azure Active Directory-app.

    1. Selecteer **Registreren**.

    [![Deelvenster maken](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. De Blade **verificatie** specificeert belang rijke instellingen voor verificatie configuratie. 

    1. Voeg **omleidings-uri's** toe en configureer **toegangs tokens** door **+ een platform toe te voegen**.

    1. Selecteer **Ja** om op te geven dat de app een **open bare client**is.

    1. Controleer welke accounts en tenants worden ondersteund door uw Azure Active Directory-app.

    [![Configuratie-instelling voor de open bare client](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. Nadat u het juiste platform hebt geselecteerd, configureert u de **omleidings-uri's** en **toegangs tokens** in het deel venster aan de rechter kant van de gebruikers interface.

    1. **Omleidings-uri's** moeten overeenkomen met het adres dat is opgegeven door de verificatie aanvraag:

        * Voor apps die worden gehost in een lokale ontwikkel omgeving selecteert u **open bare client (mobiele & bureau blad)**. Zorg ervoor dat de **open bare client** is ingesteld op **Ja**.
        * Voor apps met één pagina die worden gehost op Azure App Service, selecteert u **Web**.

    1. Bepaal of een **Afmeldings-URL** geschikt is.

    1. Schakel de impliciete toekennings stroom in door **toegangs tokens** of **id-tokens**te controleren.
                
    [![Omleidings-Uri's configureren](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Klik op **configureren**en vervolgens op **Opslaan**.

1.  Open het deel venster **overzicht** van de geregistreerde app en kopieer de waarden van de volgende entiteiten naar een tijdelijk bestand. U gebruikt deze waarden om uw voorbeeld toepassing te configureren in de volgende secties.

    - **(Client-)id van de app**
    - **(Tenant-)id van de map**

    [![Azure Active Directory toepassings-ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Open het deel venster **API-machtigingen** voor de registratie van uw app. Selecteer **+ een machtigings knop toevoegen** . Selecteer in het deel venster **API-machtigingen voor aanvragen** de **api's mijn organisatie tabblad gebruikt** en zoek vervolgens een van de volgende opties:
    
    1. `Azure Digital Twins`. Selecteer de **Azure Digital apparaatdubbels** -API.

        [![Zoek-API of Azure Digital Apparaatdubbels](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. U kunt ook zoeken naar `Azure Smart Spaces Service`. Selecteer de **Azure Smart Spaces-service** -API.

        [![Zoek-API voor Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > De naam en ID van de Azure AD-API die wordt weer gegeven, is afhankelijk van uw Tenant:
    > * Test Tenant-en klant accounts moeten zoeken `Azure Digital Twins`naar.
    > * Andere micro soft-accounts moeten `Azure Smart Spaces Service`zoeken.

1. Een van de API'S wordt weer gegeven als **Azure Digital apparaatdubbels** in het deel venster **API-machtigingen voor aanvragen** nadat het is geselecteerd. Selecteer de vervolg keuzelijst **lezen** en schakel vervolgens het selectie vakje **lezen. schrijven** in. Selecteer de knop **machtigingen toevoegen** .

    [![API-machtigingen toevoegen](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Afhankelijk van de instellingen van uw organisatie moet u mogelijk extra stappen uitvoeren om beheerders toegang tot deze API te verlenen. Neem contact op met de beheerder voor meer informatie. Zodra de beheerders toegang is goedgekeurd, worden in het deel venster API- **machtigingen** uw machtigingen weer gegeven in de kolom **Administrator toestemming vereist** . 

    [![Goed keuring van beheerders toestemming](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Controleer of **Azure Digital apparaatdubbels** wordt weer gegeven.
