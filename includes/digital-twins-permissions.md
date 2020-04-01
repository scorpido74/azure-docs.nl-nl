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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77029269"
---
>[!NOTE]
>In deze sectie vindt u instructies voor [de registratie van Azure AD-app.This](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)section provides instructions for Azure AD app registration .

1. Open Azure Active **Directory** in de [Azure-portal](https://portal.azure.com)in het uitvouwbare linkermenu en open vervolgens het deelvenster **App-registraties.** 

    [![Het deelvenster Azure Active Directory selecteren](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Selecteer de knop **+ Nieuwe registratie.**

    [![De knop Nieuwe registratie selecteren](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Geef een beschrijvende naam voor deze app-registratie op in het vak **Naam**. 

    1. Voer onder de sectie URI `https://microsoft.com` **(optioneel)** omleiden in het tekstvak.     

    1. Controleer welke accounts en tenants worden ondersteund door uw Azure Active Directory-app.

    1. Selecteer **Registreren**.

    [![Deelvenster maken](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Het **verificatieblad** geeft belangrijke verificatieconfiguratie-instellingen op. 

    1. Voeg **omleidings-URI's** toe en configureer **Toegangstokens** door **een platform toe te**voegen .

    1. Selecteer **Ja** om op te geven dat de app een **openbare client**is.

    1. Controleer welke accounts en tenants worden ondersteund door uw Azure Active Directory-app.

    [![Instelling voor openbare clientconfiguratie](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. Nadat u het juiste platform hebt geselecteerd, configureert u uw **OMLEIDINGs-URI's** en **Toegangstokens** in het zijpaneel rechts van de gebruikersinterface.

    1. **Omleidings-URI's** moeten overeenkomen met het adres dat door de verificatieaanvraag wordt opgegeven:

        * Selecteer **Openbare client (mobiel & desktop)** voor apps die worden gehost in een lokale ontwikkelingsomgeving. Zorg ervoor dat **u de openbare client instelt** op **Ja.**
        * Selecteer **Web**voor apps met één pagina die worden gehost op Azure App Service .

    1. Bepaal of een **aanmeldings-URL** geschikt is.

    1. Schakel de impliciete subsidiestroom in door **Access-tokens** of **ID-tokens te**controleren.
                
    [![Omleidings-URI's configureren](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Klik **op Configureren**en sla **op.**

1.  Open het **deelvenster Overzicht** van uw geregistreerde app en kopieer de waarden van de volgende entiteiten naar een tijdelijk bestand. U gebruikt deze waarden om uw voorbeeldtoepassing in de volgende secties te configureren.

    - **(Client-)id van de app**
    - **(Tenant-)id van de map**

    [![Azure Active Directory-toepassings-id](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Open het deelvenster **API-machtigingen** voor uw app-registratie. Selecteer **+ Een machtigingsknop toevoegen.** Selecteer in het venster **API-machtigingen aanvragen** het tabblad **API's dat mijn organisatie gebruikt** en zoek vervolgens naar een van de volgende opties:
    
    1. `Azure Digital Twins`. Selecteer de **Azure Digital Twins** API.

        [![Zoek-API of Azure Digital Twins](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. U ook `Azure Smart Spaces Service`zoeken naar. Selecteer de **Azure Smart Spaces Service** API.

        [![Zoeken api voor Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > De naam en id van de Azure AD-API die worden weergegeven, is afhankelijk van uw tenant:
    > * Test tenant en klantaccounts `Azure Digital Twins`moeten zoeken naar .
    > * Andere Microsoft-accounts `Azure Smart Spaces Service`moeten zoeken naar .

1. Beide API's worden weergegeven als **Azure Digital Twins** in hetzelfde deelvenster voor **api-machtigingen voor aanvragen** nadat deze is geselecteerd. Schakel de vervolgkeuzelijst **Lezen** in en schakel het selectievakje **Lezen.schrijven** in. Selecteer de knop **Machtigingen toevoegen.**

    [![API-machtigingen toevoegen](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Afhankelijk van de instellingen van uw organisatie moet u mogelijk aanvullende stappen ondernemen om beheerders toegang te verlenen tot deze API. Neem contact op met uw beheerder voor meer informatie. Zodra de beheerderstoegang is goedgekeurd, worden in de kolom Vereist toestemming **voor beheerders** in het deelvenster **API-machtigingen** uw machtigingen weergegeven. 

    [![Goedkeuring van toestemming voor beheerders](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Controleer of **Azure Digital Twins** wordt weergegeven.
