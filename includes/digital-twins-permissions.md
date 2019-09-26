---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 09/24/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 1fc30ea5aa843329b6227bfa564b3d10e2273cd7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310489"
---
>[!NOTE]
>Deze sectie bevat instructies voor de [nieuwe Azure AD-App-registratie](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Als u nog steeds een oude systeem eigen app hebt geregistreerd, kunt u deze gebruiken zolang deze wordt ondersteund. Daarnaast kunt u, als de nieuwe manier voor het opnieuw aanmelden van apps niet werkt in uw installatie, proberen een verouderde systeem eigen AAD-app te maken. Lees [uw Azure Digital apparaatdubbels-app registreren met Azure Active Directory verouderd](../articles/digital-twins/how-to-use-legacy-aad.md) voor meer instructies. 

1. Open in het [Azure Portal](https://portal.azure.com) **Azure Active Directory** in het linkerdeel venster en open vervolgens het **app-registraties** deel venster. Selecteer de knop **nieuwe registratie** .

    [![Geregistreerde app](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Geef een beschrijvende naam voor deze app-registratie op in het vak **Naam**. Kies in het gedeelte **omleidings-URI (optioneel)** **open bare client (mobiele & bureau blad)** in de vervolg keuzelijst aan de linkerkant `https://microsoft.com` en typ in het tekstvak aan de rechter kant. Selecteer **Registreren**.

    [![Deel venster maken](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Om ervoor te zorgen dat [de app is geregistreerd als een *systeem eigen app*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), opent u het deel venster **verificatie** voor uw app-registratie en schuift u omlaag in het deel venster. Kies in het gedeelte **standaard client type** de optie **Ja** om **de toepassing te behandelen als een open bare client**. 

    [![Standaard systeem eigen](./media/digital-twins-permissions/aad-app-default-native.png)](./media/digital-twins-permissions/aad-app-default-native.png#lightbox)

1.  Open het deel venster **overzicht** van de geregistreerde app en kopieer de waarden van de volgende entiteiten naar een tijdelijk bestand. U gebruikt deze waarden om uw voorbeeld toepassing te configureren in de volgende secties.

    - **Toepassings-ID (client)**
    - **Directory-ID (Tenant)**

    [![Azure Active Directory toepassings-ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Open het deel venster **API-machtigingen** voor de registratie van uw app. Selecteer **een machtigings knop toevoegen** . Selecteer in het deel venster **API-machtigingen voor aanvragen** de api's die **Mijn organisatie gebruikt** tabblad en zoek naar:
    
    1. **Azure Digital apparaatdubbels**. Selecteer de **Azure Digital apparaatdubbels** -API.

        [![Zoek-API of Azure Digital Apparaatdubbels](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. U kunt ook zoeken naar **Azure Smart Spaces**. Selecteer de **Azure Smart Spaces-service** -API.

        [![Zoek-API voor Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!NOTE]
    > De exacte naam die wordt weer gegeven wanneer deze wordt doorzocht, kan variëren, afhankelijk van de Azure-Tenant waarvan u deel uitmaakt.

1. De geselecteerde API wordt weer gegeven als **Azure Digital apparaatdubbels** in het deel venster **API-machtigingen voor aanvragen** . Selecteer de vervolg keuzelijst **lezen (1)** en selecteer vervolgens **lezen. Schrijf** selectie vakje. Selecteer de knop **machtigingen toevoegen** .

    [![API-machtigingen toevoegen](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Afhankelijk van de instellingen van uw organisatie moet u mogelijk extra stappen uitvoeren om beheerders toegang tot deze API te verlenen. Neem contact op met de beheerder voor meer informatie. Zodra de beheerders toegang is goedgekeurd, wordt de kolom **beheerder toestemming vereist** in het deel venster **API-machtigingen** weer gegeven, zoals in het volgende voor uw api's:

    [![API-machtigingen toevoegen](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

