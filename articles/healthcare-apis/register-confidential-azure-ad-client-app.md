---
title: Een vertrouwelijke client-app registreren in azure AD-Azure API voor FHIR
description: Registreer een vertrouwelijke client toepassing in Azure Active Directory die namens de gebruiker verifieert en toegang tot bron toepassingen vraagt.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 6642adf5f51be1f4f54c538624829fe9004d3907
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "84871874"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Een vertrouwelijke client toepassing registreren in Azure Active Directory

In deze zelf studie leert u hoe u een vertrouwelijke client toepassing in Azure Active Directory kunt registreren. 

Registratie van een client toepassing is een Azure Active Directory representatie van een toepassing die kan worden gebruikt om namens een gebruiker te verifiëren en om toegang tot [bron toepassingen](register-resource-azure-ad-client-app.md)aan te vragen. Een vertrouwelijke client toepassing is een toepassing die kan worden vertrouwd voor het bewaren van een geheim en die geheim presenteert bij het aanvragen van toegangs tokens. Voor beelden van vertrouwelijke toepassingen zijn toepassingen aan de server zijde.

Volg de onderstaande stappen om een nieuwe vertrouwelijke toepassing te registreren in de portal.

## <a name="app-registrations-in-azure-portal"></a>App-registraties in Azure Portal

1. Klik in de [Azure-portal](https://portal.azure.com) in het navigatiepaneel aan de linkerkant op **Azure Active Directory**.

2. Klik in de Blade **Azure Active Directory** op **app-registraties**:

    ![Azure Portal. Nieuwe app-registratie.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klik op de **nieuwe registratie**.

## <a name="register-a-new-application"></a>Een nieuwe toepassing registreren

1. Geef de toepassing een weergave naam.

2. Geef een antwoord-URL op. Deze gegevens kunnen later worden gewijzigd, maar als u de antwoord-URL van uw toepassing weet, voert u deze nu in.

    ![Nieuwe registratie van vertrouwelijke client-app.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>API-machtigingen

Volgende API-machtigingen toevoegen:

1. Open de **API-machtigingen**:

    ![Vertrouwelijke client. API-machtigingen](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Klik op **een machtiging toevoegen**

3. Selecteer de juiste resource-API:

    Voor de Azure API voor FHIR (beheerde service) klikt u op **api's mijn organisatie gebruikt** en zoekt u naar "Azure-gezondheids zorg-api's". Voor de open source FHIR-server voor Azure selecteert u de registratie van de [FHIR-API-resource toepassing](register-resource-azure-ad-client-app.md):

    ![Vertrouwelijke client. Mijn Api's](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Selecteer bereiken (machtigingen) die de vertrouwelijke toepassing moet kunnen vragen namens een gebruiker:

    ![Vertrouwelijke client. Gedelegeerde machtigingen](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Toepassingsgeheim

1. Een toepassings geheim maken (client geheim):

    ![Vertrouwelijke client. Toepassings geheim](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Geef een beschrijving en duur van het geheim op.

3. Zodra de app is gegenereerd, wordt deze weer gegeven in de portal. Noteer deze versie en bewaar deze veilig.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een vertrouwelijke client toepassing in Azure Active Directory kunt registreren. U bent nu klaar om de [Azure API voor FHIR](fhir-paas-powershell-quickstart.md)te implementeren.

Nadat u de Azure API voor FHIR hebt geïmplementeerd, kunt u aanvullende beschik bare instellingen bekijken.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR implementeren](fhir-paas-powershell-quickstart.md)