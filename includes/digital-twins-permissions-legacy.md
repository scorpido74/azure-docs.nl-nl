---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: fcb4b3d54e1e62a7f197f2f499e2b176bb707fd8
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71843830"
---
1. Open in de [Azure-portal](https://portal.azure.com) **Azure Active Directory** in linkerdeelvenster en open vervolgens het deelvenster **Eigenschappen**. Kopieer de **Map-id** naar een tijdelijk bestand. U gebruikt deze waarde om de voorbeeldtoepassing in de volgende sectie te configureren.

    [![Azure Active Directory Directory-ID](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. Open in de [Azure Portal](https://portal.azure.com) **Azure Active Directory** in het linkerdeel venster en open vervolgens het deel venster **app-registraties (verouderd)** . Selecteer de knop **nieuwe toepassing registreren** .

1. Geef een beschrijvende naam voor deze app-registratie op in het vak **Naam**. Kies voor **Toepassingstype** de optie **Systeemeigen** en voor **Omleidings-URI** `https://microsoft.com`. Selecteer **Maken**.

    [![Deel venster maken](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  Open de geregistreerde app en kopieer de waarde in het veld **Toepassings-id** naar een tijdelijk bestand. Deze waarde identificeert uw Azure Active Directory-app. U gebruikt de toepassings-id om uw voorbeeldtoepassing in de volgende secties te configureren.

    [![Azure Active Directory toepassings-ID](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. Open het deelvenster voor app-registratie. Selecteer **Instellingen** > **Vereiste machtigingen** en ga als volgt te werk:

   a. Selecteer linksboven **Toevoegen** om het deelvenster **API-toegang toevoegen** te openen.

   b. Selecteer **Een API selecteren** en zoek **Azure Digital Twins**. Als de API met uw zoekopdracht niet wordt gevonden, zoekt u in plaats daarvan **Azure Smart Spaces**.

   c. Selecteer de optie **Azure Digital Twins (Azure Smart Spaces-service)** en kies **Selecteren**.

   d. Kies **Machtigingen selecteren**. Schakel het selectievakje **Lees-/schrijftoegang** voor gedelegeerde machtigingen in en klik op **Selecteren**.

   e. Selecteer **Gereed** in het deelvenster **API-toegang toevoegen**.

   f. Selecteer in het deel venster **vereiste machtigingen** de knop **machtigingen verlenen** en accepteer de bevestiging die wordt weer gegeven. Neem contact op met de beheerder als de machtiging voor deze API niet is toegekend.

      [@no__t deel venster machtigingen 1Required](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox)

 
