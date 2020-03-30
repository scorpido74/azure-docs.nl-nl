---
title: bestand opnemen
description: bestand opnemen
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 5be6e7937a6e1f710b8e2576a9058963413fb6c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76984539"
---
1. Selecteer in de [Azure-portal](https://ms.portal.azure.com/)de optie **Azure Active Directory** > **App-registraties** > **Nieuwe registratie**.

   [![Nieuwe toepassingsregistratie in Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Uw app wordt hier vermeld nadat u deze hebt geregistreerd.

1. Geef de toepassing een naam en selecteer **Accounts in deze organisatiemap alleen** om de ondersteunde **accounttypen** op te geven die toegang hebben tot de API. Kies een geldige URI om gebruikers om te leiden naar nadat ze zich hebben geverifieerd en **Registreer .**

   [![De toepassing maken in Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Belangrijke Azure Active Directory-app-informatie wordt weergegeven in het **overzichtsblad van** de lijst van de app. Selecteer uw app onder **Bezeten toepassingen**en vervolgens **Overzicht**.

   [![De toepassings-id kopiëren](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Kopieer uw **applicatie-id (client)** om te gebruiken in uw clienttoepassing.

1. Het **verificatieblad** geeft belangrijke verificatieconfiguratie-instellingen op. 

    1. Voeg **omleidings-URI's** toe en configureer **Toegangstokens** door **een platform toe te**voegen .

    1. Bepaal of de app een **openbare client** is of niet door **Ja** of **Nee**te selecteren.

    1. Controleer welke accounts en tenants worden ondersteund.

    [![Impliciete subsidie configureren](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Nadat u het juiste platform hebt geselecteerd, configureert u uw **OMLEIDINGs-URI's** en **Toegangstokens** in het zijpaneel rechts van de gebruikersinterface.

    1. **Omleidings-URI's** moeten overeenkomen met het adres dat door de verificatieaanvraag wordt opgegeven:

        * Selecteer **Openbare client (mobiel & desktop)** voor apps die worden gehost in een lokale ontwikkelingsomgeving. Zorg ervoor dat **u de openbare client instelt** op **Ja.**
        * Selecteer **Web**voor apps met één pagina die worden gehost op Azure App Service .

    1. Bepaal of een **aanmeldings-URL** geschikt is.

    1. Schakel de impliciete subsidiestroom in door **Access-tokens** of **ID-tokens te**controleren.

    [![Omleidings-URI's maken](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Klik **op Configureren**en sla **op.**

1. Selecteer **Certificaten & geheimen** dan Nieuw **clientgeheim** om een toepassingswachtwoord te maken dat uw client-app kan gebruiken om zijn identiteit te bewijzen.

   [![Een nieuw klantgeheim maken](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Uw client geheim wachtwoord zal dan worden weergegeven. Kopieer de sleutel naar uw favoriete teksteditor.

   > [!NOTE]
   > U in plaats daarvan een certificaat importeren. Voor verbeterde beveiliging wordt een certificaat aanbevolen. Als u een certificaat wilt gebruiken, selecteert u **Certificaat uploaden**.

1. Uw Azure Active Directory-app Azure TIme-serie Insights koppelen. **API-machtigingen** > **toevoegen Voeg een machtigings-API's** > toe die mijn organisatie**gebruikt**. 

    [![Een API koppelen aan uw Azure Active Directory-app](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Typ `Azure Time Series Insights` in de zoekbalk en selecteer `Azure Time Series Insights`.

1. Geef vervolgens de vriendelijke API-machtiging op die uw app nodig heeft. **Gedelegeerde machtigingen** worden standaard gemarkeerd. Kies vervolgens een **machtigingstype**en selecteer Machtigingen toevoegen .

    [![Het soort API-machtiging opgeven dat uw app nodig heeft](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
