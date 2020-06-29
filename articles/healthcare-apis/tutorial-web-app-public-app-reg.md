---
title: Zelfstudie voor web-apps - Clienttoepassing instellen
description: In deze zelfstudie worden de stappen beschreven voor het registreren van een openbare toepassing ter voorbereiding van de implementatie van een web-app
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "84870451"
---
# <a name="client-application-registration"></a>Clienttoepassingen registreren
In de vorige zelfstudie hebt u de Azure-API for FHIR geïmplementeerd en ingesteld. Nu u Azure API for FHIR hebt ingesteld, gaan we een openbare clienttoepassing registreren. U kunt de volledige instructiegids voor het [registreren van een openbare client-app](register-public-azure-ad-client-app.md) lezen voor meer informatie of om problemen op te lossen, maar voor deze zelfstudie hebben we de belangrijkste stappen op een rij gezet.

1. Navigeren naar Azure Active Directory
1. Selecteer **App-registratie** --> **Nieuwe registratie**
1. Geef uw toepassing een naam en stel de omleidings-URI in op https://www.getpostman.com/oauth2/callback


![Clienttoepassingen registreren](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>Clienttoepassingsinstellingen
Nadat de clienttoepassing is geregistreerd, kopieert u de (client)toepassings-id vanuit de pagina Overzicht. U hebt deze waarde later nodig bij het openen van de client.

![App-id kopiëren](media/tutorial-web-app/app-id.png)

Stel vervolgens de juiste verificatieopties in. Selecteer **Verificatie** aan de linkerkant. Schakel vervolgens de selectievakjes **Toegangstoken** en **Id-token** in. U kunt ook de omleidings-URI instellen als voorbereiding voor wanneer u uw webtoepassing in het vierde deel van deze zelfstudie gaat maken. U doet dit door https://\<WEB-APP-NAAM>. azurewebsites.net toe te voegen aan de lijst met omleidings-URI's. Als u een andere naam kiest tijdens de stap waarin u [uw web-app schrijft](tutorial-web-app-write-web-app.md), moet u terugkeren en deze bijwerken.

![Instellingen voor app-verificatie](media/tutorial-web-app/app-authentication.png)

Nu u de juiste verificatie hebt ingesteld, stelt u de API-machtigingen in. 
1. Selecteer **API-machtigingen** en klik op **Een machtiging toevoegen**
1. Zoek onder **API's waarvan mijn organisatie gebruikmaakt** naar Azure Healthcare API's
1. Selecteer **user_impersonation** en klik op **machtigingen toevoegen**

## <a name="next-steps"></a>Volgende stappen
U hebt nu een openbare clienttoepassing. In de volgende zelfstudie wordt u begeleid bij het testen en verkrijgen van toegang tot deze toepassing via Postman.

>[!div class="nextstepaction"]
>[Clienttoepassing testen in Postman](tutorial-web-app-test-postman.md)
