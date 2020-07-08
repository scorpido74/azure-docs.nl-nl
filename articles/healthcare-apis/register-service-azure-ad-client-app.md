---
title: Een service-app registreren in azure AD-Azure API voor FHIR
description: Meer informatie over het registreren van een service-client toepassing in Azure Active Directory die kan worden gebruikt voor het verifiëren en verkrijgen van tokens.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 83514ea13ca51e6980a1b4b60d4555974db5a240
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871846"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Een service-client toepassing registreren in Azure Active Directory

In dit artikel leert u hoe u een service-client toepassing registreert in Azure Active Directory. Registraties van client toepassingen zijn Azure Active Directory representaties van toepassingen die kunnen worden gebruikt voor het verifiëren en verkrijgen van tokens. Een service-client is bedoeld om te worden gebruikt door een toepassing om een toegangs token te verkrijgen zonder interactieve verificatie van een gebruiker. Het heeft bepaalde toepassings machtigingen en gebruikt een toepassings geheim (wacht woord) bij het verkrijgen van toegangs tokens.

Volg de onderstaande stappen om een nieuwe serviceclient te maken.

## <a name="app-registrations-in-azure-portal"></a>App-registraties in Azure Portal

1. Klik in de [Azure-portal](https://portal.azure.com) in het navigatiepaneel aan de linkerkant op **Azure Active Directory**.

2. Klik in de Blade **Azure Active Directory** op **app-registraties**:

    ![Azure Portal. Nieuwe app-registratie.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klik op **Nieuwe registratie**.

## <a name="service-client-application-details"></a>Details van service-client toepassing

* De service-client heeft een weergave naam nodig en u kunt ook een antwoord-URL opgeven, maar deze wordt meestal niet gebruikt.

    ![Azure Portal. Nieuwe registratie van service-client-app.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>API-machtigingen

U moet de service-client toepassings rollen verlenen. 

1. Open de **API-machtigingen** en selecteer de registratie van de [FHIR API-bron toepassing](register-resource-azure-ad-client-app.md). Als u de Azure API voor FHIR gebruikt, voegt u een machtiging toe aan de Azure-gezondheids zorg Api's door te zoeken naar Azure-gezondheids zorg Api's onder **api's mijn organisatie gebruikt**.

    ![Azure Portal. API-machtigingen voor service-client](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Selecteer de toepassings rollen die u hebt uit de toepassingen die zijn gedefinieerd in de resource toepassing:

    ![Azure Portal. Machtigingen voor de service-client toepassing](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Toestemming verlenen aan de toepassing. Als u niet de vereiste machtigingen hebt, raadpleegt u de beheerder van uw Azure Active Directory:

    ![Azure Portal. Toestemming van client beheerder voor service](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Toepassingsgeheim

De service-client vereist een geheim (wacht woord) dat u gebruikt bij het verkrijgen van tokens.

1. Klik op **certificaten &amp; geheimen**

2. Klik op **Nieuw clientgeheim**

    ![Azure Portal. Client geheim van de service](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Geef een duur van het geheim op.

4. Zodra de app is gegenereerd, wordt deze slechts eenmaal weer gegeven in de portal. Noteer deze versie en bewaar deze veilig.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een service-client toepassing registreert in Azure Active Directory. Implementeer vervolgens een FHIR-API in Azure.
 
>[!div class="nextstepaction"]
>[Open source FHIR-server implementeren](fhir-oss-powershell-quickstart.md)