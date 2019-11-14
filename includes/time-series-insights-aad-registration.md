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
ms.date: 09/24/2019
ms.openlocfilehash: 1ed57e30273c9ba3feb110e0e5acbd3bcc7ffd40
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74062871"
---
1. Selecteer in de [Azure Portal](https://ms.portal.azure.com/) **Azure Active Directory** > **app-registraties** > **nieuwe registratie**.

   [![nieuwe toepassings registratie in Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Uw app wordt hier weer gegeven nadat u deze hebt geregistreerd.

1. Geef de toepassing een naam en selecteer **accounts in deze organisatie Directory alleen** om de **ondersteunde account typen** op te geven die toegang kunnen krijgen tot de API. Kies een geldige URI voor het omleiden van gebruikers naar nadat ze zijn geverifieerd en **Registreer**deze vervolgens.

   [![de toepassing in Azure Active Directory maken](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Belang rijk Azure Active Directory app-gegevens worden weer gegeven op de Blade **overzicht** van de lijst met apps. Selecteer uw app onder **toepassingen in eigendom**en klik vervolgens op **overzicht**.

   [de toepassings-ID ![kopiëren](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Kopieer de **client-id** die moet worden gebruikt in uw client toepassing.

1. De Blade **verificatie** specificeert belang rijke instellingen voor verificatie configuratie. 

    1. **Omleidings-uri's** moeten overeenkomen met het adres dat is opgegeven door de verificatie aanvraag:

        * Voor apps die worden gehost in een lokale ontwikkel omgeving selecteert u **open bare client (mobiele & bureau blad)** . Zorg ervoor dat het **standaard client type** is ingesteld op Ja.
        * Voor apps met één pagina die worden gehost op Azure App Service, selecteert u **Web**.

    1. Schakel de impliciete toekennings stroom in door **id-tokens**te controleren.

   [![een nieuw client geheim maken](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Klik op **Opslaan**.

1. Selecteer **certificaten & geheimen** en vervolgens **Nieuw client geheim** voor het maken van een toepassings wachtwoord dat door de client kan worden gebruikt om de identiteit ervan te bewijzen.

   [![een nieuw client geheim maken](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Uw client geheim wacht woord wordt dan weer gegeven. Kopieer de sleutel naar uw favoriete tekst editor.

   > [!NOTE]
   > U kunt in plaats daarvan een certificaat importeren. Voor een betere beveiliging wordt een certificaat aanbevolen. Als u een certificaat wilt gebruiken, selecteert u **certificaat uploaden**.

1. Koppel uw Azure Active Directory app Azure TIme Series Insights. Selecteer **API-machtigingen** > **een machtiging toe te voegen** > **api's die mijn organisatie gebruikt**. 

    [![een API aan uw Azure Active Directory-app koppelen](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Typ `Azure Time Series Insights` in de zoek balk en selecteer vervolgens `Azure Time Series Insights`.

1. Geef vervolgens de type API-machtiging op die uw app vereist. Standaard worden **gedelegeerde machtigingen** gemarkeerd. Kies een machtigings type en selecteer vervolgens **machtigingen toevoegen**.

    [![Geef het type API-machtiging op dat voor uw app is vereist](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)