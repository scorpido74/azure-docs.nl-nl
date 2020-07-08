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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76984539"
---
1. Selecteer in de [Azure Portal](https://ms.portal.azure.com/) **Azure Active Directory**  >  **app-registraties**  >  **nieuwe registratie**.

   [![Nieuwe toepassings registratie in Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Uw app wordt hier weer gegeven nadat u deze hebt geregistreerd.

1. Geef de toepassing een naam en selecteer **accounts in deze organisatie Directory alleen** om de **ondersteunde account typen** op te geven die toegang kunnen krijgen tot de API. Kies een geldige URI voor het omleiden van gebruikers naar nadat ze zijn geverifieerd en **Registreer**deze vervolgens.

   [![De toepassing maken in Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Belang rijk Azure Active Directory app-gegevens worden weer gegeven op de Blade **overzicht** van de lijst met apps. Selecteer uw app onder **toepassingen in eigendom**en klik vervolgens op **overzicht**.

   [![De toepassings-ID kopiëren](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Kopieer de **client-id** die moet worden gebruikt in uw client toepassing.

1. De Blade **verificatie** specificeert belang rijke instellingen voor verificatie configuratie. 

    1. Voeg **omleidings-uri's** toe en configureer **toegangs tokens** door **+ een platform toe te voegen**.

    1. Bepaal of de app een **open bare client** is of niet door **Ja** of **Nee**te selecteren.

    1. Controleer welke accounts en tenants worden ondersteund.

    [![Impliciete toekenning configureren](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Nadat u het juiste platform hebt geselecteerd, configureert u de **omleidings-uri's** en **toegangs tokens** in het deel venster aan de rechter kant van de gebruikers interface.

    1. **Omleidings-uri's** moeten overeenkomen met het adres dat is opgegeven door de verificatie aanvraag:

        * Voor apps die worden gehost in een lokale ontwikkel omgeving selecteert u **open bare client (mobiele & bureau blad)**. Zorg ervoor dat de **open bare client** is ingesteld op **Ja**.
        * Voor apps met één pagina die worden gehost op Azure App Service, selecteert u **Web**.

    1. Bepaal of een **Afmeldings-URL** geschikt is.

    1. Schakel de impliciete toekennings stroom in door **toegangs tokens** of **id-tokens**te controleren.

    [![Omleidings-Uri's maken](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Klik op **configureren**en vervolgens op **Opslaan**.

1. Selecteer **certificaten & geheimen** en vervolgens **Nieuw client geheim** voor het maken van een toepassings wachtwoord dat door uw client-app kan worden gebruikt om de identiteit ervan te bewijzen.

   [![Een nieuw client geheim maken](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Uw client geheim wacht woord wordt dan weer gegeven. Kopieer de sleutel naar uw favoriete tekst editor.

   > [!NOTE]
   > U kunt in plaats daarvan een certificaat importeren. Voor een betere beveiliging wordt een certificaat aanbevolen. Als u een certificaat wilt gebruiken, selecteert u **certificaat uploaden**.

1. Koppel uw Azure Active Directory app Azure TIme Series Insights. Selecteer **API-machtigingen**  >  **een machtigings**  >  **-api's toevoegen die mijn organisatie gebruikt**. 

    [![Een API koppelen aan uw Azure Active Directory-app](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Typ `Azure Time Series Insights` in de zoek balk en selecteer `Azure Time Series Insights` .

1. Geef vervolgens de type API-machtiging op die uw app vereist. Standaard worden **gedelegeerde machtigingen** gemarkeerd. Kies een machtigings type en selecteer vervolgens **machtigingen toevoegen**.

    [![Geef het type API-machtiging op dat voor uw app is vereist](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
