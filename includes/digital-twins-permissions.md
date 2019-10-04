---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 880c2a811df7b4d41760676d4d6e0153e9384f38
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949765"
---
>[!NOTE]
>Deze sectie bevat instructies voor de [nieuwe Azure AD-App-registratie](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Als u nog steeds een oude systeem eigen app hebt geregistreerd, kunt u deze gebruiken zolang deze wordt ondersteund. Daarnaast kunt u, als de nieuwe manier voor het opnieuw aanmelden van apps niet werkt in uw installatie, proberen een verouderde systeem eigen AAD-app te maken. Lees [uw Azure Digital apparaatdubbels-app registreren met Azure Active Directory verouderd](../articles/digital-twins/how-to-use-legacy-aad.md) voor meer instructies. 

1. Open in het [Azure Portal](https://portal.azure.com) **Azure Active Directory** in het linkerdeel venster en open vervolgens het **app-registraties** deel venster. Selecteer de knop **nieuwe registratie** .

    [![App geregistreerd](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Geef een beschrijvende naam voor deze app-registratie op in het vak **Naam**. Kies in het gedeelte **omleidings-URI (optioneel)** **open bare client (mobiele & bureau blad)** in de vervolg keuzelijst aan de linkerkant en voer `https://microsoft.com` in het tekstvak aan de rechter kant. Selecteer **Registreren**.

    [@no__t deel venster 1Create](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Om ervoor te zorgen dat [de app is geregistreerd als een *systeem eigen app*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), opent u het deel venster **verificatie** voor uw app-registratie en schuift u omlaag in het deel venster. Kies in het gedeelte **standaard client type** de optie **Ja** om **de toepassing te behandelen als een open bare client**. 

    [![Default systeem eigen](./media/digital-twins-permissions/aad-app-default-native.png)](./media/digital-twins-permissions/aad-app-default-native.png#lightbox)

1.  Open het deel venster **overzicht** van de geregistreerde app en kopieer de waarden van de volgende entiteiten naar een tijdelijk bestand. U gebruikt deze waarden om uw voorbeeld toepassing te configureren in de volgende secties.

    - **Toepassings-ID (client)**
    - **Directory-ID (Tenant)**

    [![Azure Active Directory-toepassings-ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Open het deel venster **API-machtigingen** voor de registratie van uw app. Selecteer **een machtigings knop toevoegen** . Selecteer in het deel venster **API-machtigingen voor aanvragen** de api's die **Mijn organisatie gebruikt** tabblad en zoek naar:
    
    1. `Azure Digital Twins`. Selecteer de **Azure Digital apparaatdubbels** -API.

        [![Search-API of Azure Digital Apparaatdubbels](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. U kunt ook zoeken naar `Azure Smart Spaces Service`. Selecteer de **Azure Smart Spaces-service** -API.

        [![Search-API voor Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > De naam en ID van de Azure AD-API die wordt weer gegeven, is afhankelijk van uw Tenant:
    > * Test Tenant-en klant accounts moeten zoeken naar `Azure Digital Twins`.
    > * Andere micro soft-accounts moeten zoeken naar `Azure Smart Spaces Service`.

1. De geselecteerde API wordt weer gegeven als **Azure Digital apparaatdubbels** in het deel venster **API-machtigingen voor aanvragen** . Selecteer de vervolg keuzelijst **lezen (1)** en selecteer vervolgens **lezen. Schrijf** selectie vakje. Selecteer de knop **machtigingen toevoegen** .

    [![API-machtigingen toevoegen](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Afhankelijk van de instellingen van uw organisatie moet u mogelijk extra stappen uitvoeren om beheerders toegang tot deze API te verlenen. Neem contact op met de beheerder voor meer informatie. Zodra de beheerders toegang is goedgekeurd, wordt de kolom **beheerder toestemming vereist** in het deel venster **API-machtigingen** weer gegeven, zoals in het volgende voor uw api's:

    [![API-machtigingen toevoegen](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

