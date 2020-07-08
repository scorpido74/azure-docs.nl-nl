---
title: Een open bare client-app registreren in azure AD-Azure API voor FHIR
description: In dit artikel wordt uitgelegd hoe u een open bare client toepassing registreert bij Azure Active Directory, ter voor bereiding op de implementatie van de FHIR-API in Azure.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 5aa9e5a33dbe66e3ebd787decfa3a520454fc6f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871790"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Een open bare client toepassing registreren in Azure Active Directory

In dit artikel leert u hoe u een open bare toepassing in Azure Active Directory kunt registreren.  

Registraties van client toepassingen zijn Azure Active Directory representaties van toepassingen die de API-machtigingen namens een gebruiker kunnen verifiëren en om deze te vragen. Open bare clients zijn toepassingen zoals mobiele toepassingen en Java script-toepassingen met één pagina waarmee geheimen vertrouwelijk niet kunnen worden bewaard. De procedure is vergelijkbaar met het [registreren van een vertrouwelijke client](register-confidential-azure-ad-client-app.md), maar omdat open bare clients niet kunnen worden vertrouwd voor het opslaan van een toepassings geheim, hoeft er geen te worden toegevoegd.

## <a name="app-registrations-in-azure-portal"></a>App-registraties in Azure Portal

1. Klik in de [Azure-portal](https://portal.azure.com) in het navigatiepaneel aan de linkerkant op **Azure Active Directory**.

2. Op de Blade **Azure Active Directory** klikt u op **app-registraties**:

    ![Azure Portal. Nieuwe app-registratie.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klik op de **nieuwe registratie**.

## <a name="application-registration-overview"></a>Overzicht van toepassings registratie

1. Geef de toepassing een weergave naam.

2. Geef een antwoord-URL op. De antwoord-URL is de locatie van de authenticatie codes worden geretourneerd naar de client toepassing. U kunt later meer antwoord-Url's toevoegen en bestaande bewerken.

    ![Azure Portal. Nieuwe open bare app registreren.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>API-machtigingen

Net als bij de [vertrouwelijke client toepassing](register-confidential-azure-ad-client-app.md)moet u selecteren welke API-machtigingen deze toepassing moet kunnen aanvragen namens gebruikers:

1. Open de **API-machtigingen**.

    Als u de Azure API voor FHIR gebruikt, voegt u een machtiging toe aan de Azure-gezondheids zorg-api's door te zoeken naar Azure-gezondheids zorg Api's onder **api's die mijn organisatie gebruikt** (onderstaande afbeelding).
    
    Als u naar een andere bron toepassing verwijst, selecteert u de [registratie van de FHIR API-resource toepassing](register-resource-azure-ad-client-app.md) die u eerder hebt gemaakt onder **mijn api's**:

    ![Azure Portal. Nieuwe open bare API-machtigingen: Azure API for FHIR-standaard](media/public-client-app/api-permissions.png)


2. Selecteer de machtigingen die u wilt dat de toepassing het volgende kan aanvragen: ![ Azure Portal. App-machtigingen](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>FHIR Server-instantie valideren
Als de toepassing die u in dit artikel hebt geregistreerd en uw FHIR-server zich in dezelfde Azure AD-Tenant bevinden, kunt u door gaan met de volgende stappen.

Als u uw client toepassing configureert in een andere Azure AD-Tenant vanaf uw FHIR-server, moet u de **instantie**bijwerken. In azure API voor FHIR stelt u de instantie in op instellingen-->-verificatie. Stel uw instantie in op **https://login.microsoftonline.com/\<TENANT-ID>** .

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een open bare client toepassing in Azure Active Directory kunt registreren. Test vervolgens de toegang tot uw FHIR-server met behulp van postman.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR openen met Postman](access-fhir-postman-tutorial.md)
