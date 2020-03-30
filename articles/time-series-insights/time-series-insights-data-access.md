---
title: Beveiliging configureren om gegevenstoegang te verlenen - Azure Time Series Insights Preview | Microsoft Documenten
description: Meer informatie over het configureren van beveiligings-, machtigingen- en beheerbeleid voor gegevenstoegang in uw Azure Time Series Insights Preview-omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: 1c8f14bb1bca082a9d887e5d6d88aec213448c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254350"
---
# <a name="grant-data-access-to-an-environment"></a>Gegevenstoegang verlenen tot een omgeving

In dit artikel worden de twee typen toegangsbeleid voor Azure Time Series Insights Preview besproken.

> [!TIP]
> Lees de registratiestappen [Verificatie en autorisatie](time-series-insights-authentication-and-authorization.md) voor Azure Active Directory-app.

## <a name="sign-in-to-time-series-insights"></a>Aanmelden bij Inzichten in de time-serie

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zoek uw Time Series Insights-omgeving. Voer `Time Series` het **vak Zoeken** in. Selecteer **Tijdreeksomgeving** in de zoekresultaten.
1. Selecteer uw Time Series Insights-omgeving in de lijst.

## <a name="grant-data-access"></a>Gegevenstoegang verlenen

Volg deze stappen om gegevenstoegang te verlenen aan een gebruikersprincipal.

1. Selecteer **Beleid voor gegevenstoegang**en selecteer **+ Toevoegen**.

    [![Een beleid voor gegevenstoegang selecteren en toevoegen](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Kies **Gebruiker selecteren**. Zoek naar de gebruikersnaam of het e-mailadres om de gebruiker te vinden die u wilt toevoegen. Selecteer **Selecteren** om de selectie te bevestigen.

    [![Een gebruiker selecteren om toe te voegen](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Kies **Rol selecteren**. Kies de juiste toegangsrol voor de gebruiker:

    * Selecteer **Bijdrager** als u de gebruiker toestemming wilt geven om referentiegegevens te wijzigen en opgeslagen query's en perspectieven te delen met andere gebruikers van de omgeving.

    * Selecteer anders **Reader** om de gebruiker in staat te stellen gegevens in de omgeving op te vragen en persoonlijke, niet gedeelde, query's in de omgeving op te slaan.

   Selecteer **OK** om de rolkeuze te bevestigen.

    [![De geselecteerde rol bevestigen](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Selecteer **OK** op de pagina **Gebruikersrol selecteren.**

    [![Ok selecteren op de pagina Gebruikersrol selecteren](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Controleer of op de pagina **Beleid voor gegevenstoegang** de gebruikers en de rollen voor elke gebruiker worden weergegeven.

    [![De juiste gebruikers en rollen verifiëren](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Gasttoegang bieden vanuit een andere Azure AD-tenant

De `Guest` rol is geen managementrol. Het is een term die wordt gebruikt voor een account dat is uitgenodigd van de ene huurder naar de andere. Nadat het gastaccount is uitgenodigd in de directory van de tenant, kan het hetzelfde toegangscontrolehebben toegepast als elk ander account. U het beheer toegang verlenen tot een Time Series Insights-omgeving met behulp van het IAM-blade (Access Control). Of u toegang verlenen tot de gegevens in de omgeving via het blade gegevenstoegangsbeleid. Lees [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen in de Azure-portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)voor meer informatie over azure active directory-tenantgasttoegang voor Azure Active Directory (Azure AD).

Volg deze stappen om gasttoegang te verlenen tot een Time Series Insights-omgeving aan een Azure AD-gebruiker vanuit een andere tenant.

1. Selecteer **Beleid voor gegevenstoegang**en selecteer **+ Uitnodigen**.

    [![Selecteer Gegevenstoegangspolitie en vervolgens + Uitnodigen](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Voer het e-mailadres in voor de gebruiker die u wilt uitnodigen. Dit e-mailadres moet zijn gekoppeld aan Azure AD. U optioneel een persoonlijk bericht bij de uitnodiging opnemen.

    [![Voer het e-mailadres in om de geselecteerde gebruiker te vinden](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Zoek naar de bevestigingsballon die op het scherm verschijnt.

    [![Zoek naar de bevestigingsballon die wordt weergegeven](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Kies **Gebruiker selecteren**. Zoek naar het e-mailadres van de gastgebruiker die u hebt uitgenodigd om de gebruiker te vinden die u wilt toevoegen. **Selecteer** vervolgens om de selectie te bevestigen.

    [![Selecteer de gebruiker en bevestig de selectie](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Kies **Rol selecteren**. Kies de juiste toegangsrol voor de gastgebruiker:

    * Selecteer **Bijdrager** als u de gebruiker toestemming wilt geven om referentiegegevens te wijzigen en opgeslagen query's en perspectieven te delen met andere gebruikers van de omgeving.

    * Selecteer anders **Reader** om de gebruiker in staat te stellen gegevens in de omgeving op te vragen en persoonlijke, niet gedeelde, query's in de omgeving op te slaan.

   Selecteer **OK** om de rolkeuze te bevestigen.

    [![De rolkeuze bevestigen](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Selecteer **OK** op de pagina **Gebruikersrol selecteren.**

1. Controleer of op de pagina **Beleid voor gegevenstoegang** de gastgebruiker en de rollen voor elke gastgebruiker worden weergegeven.

    [![Controleren of gebruikers en rollen correct zijn toegewezen](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Nu ontvangt de gastgebruiker een uitnodigingse-mail op het hierboven opgegeven e-mailadres. De gastgebruiker selecteert **Aan de slag** om de acceptatie ervan te bevestigen en verbinding te maken met Azure Cloud.

    [![Gast selecteert Aan de slag om te accepteren](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Nadat u Aan de **slag bent geselecteerd,** krijgt de gastgebruiker een machtigingsvak te zien dat is gekoppeld aan de organisatie van de beheerder. Na het verlenen van toestemming door **accepteren te**selecteren, worden ze aangemeld.

    [![Gasten beoordelingen machtigingen en accepteert](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. De beheerder [deelt de URL van de omgeving](time-series-insights-parameterized-urls.md) met zijn/haar gast.

1. Nadat de gastgebruiker is aangemeld bij het e-mailadres dat u hebt gebruikt om hen uit te nodigen en ze de uitnodiging accepteren, worden ze doorverwezen naar de Azure-portal. 

1. De gast heeft nu toegang tot de gedeelde omgeving via de URL van de omgeving die door de beheerder wordt verstrekt. Ze kunnen die URL invoeren in hun webbrowser voor directe toegang.

1. De tenant van de beheerder wordt weergegeven aan de gastgebruiker nadat hij zijn profielpictogram heeft geselecteerd in de rechterbovenhoek van de verkenner van de tijdreeks.

    [![Avatar selectie op insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    Nadat de gastgebruiker de tenant van de beheerder heeft geselecteerd, kan deze de gedeelde Time Series Insights-omgeving selecteren. 
    
    Ze hebben nu alle mogelijkheden in verband met de rol die u hen in **stap 5**.

    [![Gastgebruiker selecteert uw Azure-tenant uit vervolgkeuzelijst](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het toevoegen van een gebeurtenisbron van Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) aan uw Time Series Insights-omgeving.

* Gebeurtenissen [verzenden naar de gebeurtenisbron](./time-series-insights-send-events.md).

* Uw omgeving weergeven [in de time series Insights Preview verkenner](./time-series-insights-update-explorer.md).
