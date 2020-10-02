---
title: Een service-app registreren in azure AD-Azure API voor FHIR
description: Meer informatie over het registreren van een service-client toepassing in Azure Active Directory.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 19d6b0ebfa2570b04c3a9dda3fe69428aa0eed75
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629284"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Een service-client toepassing registreren in Azure Active Directory

In dit artikel leert u hoe u een service-client toepassing registreert in Azure Active Directory. Registraties van client toepassingen zijn Azure Active Directory representaties van toepassingen die kunnen worden gebruikt voor het verifiëren en verkrijgen van tokens. Een service-client is bedoeld om te worden gebruikt door een toepassing om een toegangs token te verkrijgen zonder interactieve verificatie van een gebruiker. Het heeft bepaalde toepassings machtigingen en gebruikt een toepassings geheim (wacht woord) bij het verkrijgen van toegangs tokens.

Volg deze stappen om een nieuwe service-client te maken.

## <a name="app-registrations-in-azure-portal"></a>App-registraties in Azure Portal

1. Ga in het [Azure Portal](https://portal.azure.com)naar **Azure Active Directory**.

2. Selecteer **App-registraties**.

    ![Azure Portal. Nieuwe app-registratie.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Selecteer **Nieuwe registratie**.

4. Geef de service-client een weergave naam. Service-client toepassingen gebruiken doorgaans geen antwoord-URL.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure Portal. Nieuwe registratie van service-client-app.":::

5. Selecteer **Registreren**.

## <a name="api-permissions"></a>API-machtigingen

Nu u uw toepassing hebt geregistreerd, moet u selecteren welke API-machtigingen deze toepassing moet kunnen aanvragen namens gebruikers:

1. Selecteer **API-machtigingen**.
1. Selecteer **een machtiging toevoegen**.

    Als u de Azure API voor FHIR gebruikt, voegt u een machtiging toe aan de Azure-gezondheids zorg Api's door te zoeken naar **Azure-gezondheids zorg api's** onder **api's mijn organisatie gebruikt**. 

    Als u naar een andere bron toepassing verwijst, selecteert u de [registratie van de FHIR API-resource toepassing](register-resource-azure-ad-client-app.md) die u eerder hebt gemaakt onder **mijn api's**.

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="Azure Portal. Nieuwe registratie van service-client-app." lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. Selecteer bereiken (machtigingen) die de vertrouwelijke toepassing moet kunnen vragen namens een gebruiker:

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="Azure Portal. Nieuwe registratie van service-client-app.":::

1. Toestemming verlenen aan de toepassing. Als u niet de vereiste machtigingen hebt, raadpleegt u de beheerder van uw Azure Active Directory:

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="Azure Portal. Nieuwe registratie van service-client-app.":::

## <a name="application-secret"></a>Toepassingsgeheim

De service-client heeft een geheim (wacht woord) nodig om een token te verkrijgen.

1. Selecteer **Certificaten en geheimen**.
2. Selecteer **Nieuw clientgeheim**.

    ![Azure Portal. Client geheim van de service](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Geef een beschrijving en duur van het geheim op (ofwel 1 jaar, 2 jaar of nooit).

4. Zodra het geheim is gegenereerd, wordt het maar één keer weer gegeven in de portal. Noteer deze versie en bewaar deze veilig.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een service-client toepassing registreert in Azure Active Directory. Vervolgens vindt u meer informatie over de aanvullende instellingen voor de Azure-API voor FHIR.
 
>[!div class="nextstepaction"]
>[Aanvullende instellingen](azure-api-for-fhir-additional-settings.md)