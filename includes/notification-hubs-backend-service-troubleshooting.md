---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 343fef941870b6deaad777fac9b9d258d454aa2b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095293"
---
#### <a name="no-response-from-the-backend-service"></a>Geen antwoord van de back-end-service

Wanneer u lokaal test, moet u ervoor zorgen dat de back-end-service wordt uitgevoerd en dat u de juiste poort gebruikt.

Controleer of de service wordt uitgevoerd en is gedistribueerd en is gestart zonder fouten als u wilt testen op de **Azure API-app**.

Controleer of u het basisadres juist hebt opgegeven in **[Postman](https://www.postman.com/downloads)** of in de configuratie van de mobiele app bij het testen via de client. Het basisadres moet indicatief `https://<api_name>.azurewebsites.net/` of `https://localhost:5001/` zijn bij het lokaal testen.

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>Er worden geen meldingen ontvangen op Android na het starten of stoppen van een foutopsporingssessie op te sporen

Zorg ervoor dat u zich opnieuw registreert nadat u een foutopsporingssessie hebt gestart of gestopt. Het foutopsporingsprogramma zorgt ervoor dat er een nieuw **Firebase**-token wordt gegenereerd. De installatie van de Notification Hub moet ook worden bijgewerkt.

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>Een 401-status code ontvangen van de back-end-service

Controleer of u de **apikey**-aanvraagheader hebt ingesteld en dat deze waarde overeenkomt met de naam die u voor de back-end-service hebt geconfigureerd.

Als deze fout optreedt bij het lokaal testen, moet u ervoor zorgen dat de sleutelwaarde die u in de clientconfiguratie hebt gedefinieerd, overeenkomt met de instellingswaarde **Authentication:ApiKey** van de gebruikers die door de [API](#create-the-api-app) wordt gebruikt.

Als u test met een **API-app**, moet u ervoor zorgen dat de sleutelwaarde in het clientconfiguratiebestand overeenkomt met de toepassingsinstelling **Authentication:ApiKey** die u gebruikt in de [API-app](#create-the-api-app).

> [!NOTE]
> Als u deze instelling hebt gemaakt of gewijzigd nadat u de back-end-service hebt geïmplementeerd, moet u de service opnieuw starten om deze van kracht te laten worden.

Als u ervoor hebt gekozen om de sectie [Clients met een API-sleutel verifiëren](#authenticate-clients-using-an-api-key-optional) niet te voltooien, moet u ervoor zorgen dat u het kenmerk **Autoriseren** niet hebt toegepast op de klasse **NotificationsController**.

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>Een 404-status code ontvangen van de back-end-service

Controleer of de methode voor het eindpunt en de HTTP-aanvraag juist is. De eindpunten moeten bijvoorbeeld indicatief zijn:

- **[PUT]** `https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[DELETE]** `https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[POST]** `https://<api_name>.azurewebsites.net/api/notifications/requests`

Of bij lokaal testen:

- **[PUT]** `https://localhost:5001/api/notifications/installations`
- **[DELETE]** `https://localhost:5001/api/notifications/installations/<installation_id>`
- **[POST]** `https://localhost:5001/api/notifications/requests`

Wanneer u het basisadres opgeeft in de client-app, moet u ervoor zorgen dat het eindigt met een `/`. Het basisadres moet indicatief `https://<api_name>.azurewebsites.net/` of `https://localhost:5001/` zijn bij het lokaal testen.

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>Kan niet registreren en er wordt een foutbericht over de Notification Hub weergegeven

Controleer of het testapparaat netwerkverbinding heeft. Bepaal vervolgens de statuscode van het HTTP-antwoord door een onderbrekingspunt in te stellen voor het controleren van de eigenschapswaarde van de **StatusCode** in de **HttpResponse**.

Bekijk de vorige suggesties voor probleem oplossen, indien van toepassing op basis van de statuscode.

Stel een onderbrekingspunt in op de regels die deze specifieke statuscodes retourneren voor de respectieve API. Probeer vervolgens de back-end-service aan te roepen wanneer u lokaal fouten opspoort.

Controleer of de back-end-service werkt zoals verwacht via **[Postman](https://www.postman.com/downloads)** met de juiste payload. Gebruik de werkelijke payload die is gemaakt door de clientcode voor het desbetreffende platform.

Bekijk de platformspecifieke configuratiesecties om te controleren of er geen stappen zijn gemist. Controleer of de juiste waarden worden omgezet voor variabelen `installation id` en `token` voor het betreffende platform.

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>Kan geen id omzetten voor het foutbericht van het apparaat die wordt weergegeven

Bekijk de platformspecifieke configuratiesecties om te controleren of er geen stappen zijn gemist.
