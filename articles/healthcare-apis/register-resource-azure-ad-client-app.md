---
title: Een resource-app registreren in azure AD-Azure API voor FHIR
description: Registreer een resource-app (of API) in Azure Active Directory, zodat client toepassingen toegang tot de bron kunnen aanvragen tijdens de verificatie.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 10b951300b8386b057744a980abd5d847b6b6907
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "84871853"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Een resource toepassing registreren in Azure Active Directory

In dit artikel leert u hoe u een resource-of API-toepassing in Azure Active Directory kunt registreren. Een resource toepassing is een Azure Active Directory representatie van de FHIR-Server-API zelf en client toepassingen kunnen tijdens de verificatie toegang tot de bron aanvragen. De resource toepassing staat ook bekend als de *doel groep* in OAuth-jargon.

## <a name="azure-api-for-fhir"></a>Azure-API voor FHIR

Als u de Azure API voor FHIR gebruikt, wordt er automatisch een resource toepassing gemaakt wanneer u de service implementeert. Zolang u de Azure-API voor FHIR in dezelfde Azure Active Directory Tenant gebruikt wanneer u uw toepassing implementeert, kunt u deze hand leiding overs Laan en in plaats daarvan uw Azure API voor FHIR implementeren om aan de slag te gaan.

Als u een andere Azure Active Directory Tenant gebruikt (niet gekoppeld aan uw abonnement), kunt u de Azure API voor de FHIR-resource toepassing importeren in uw Tenant met Power shell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

u kunt ook Azure CLI gebruiken:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>FHIR-server voor Azure

Als u gebruikmaakt van de open source FHIR-server voor Azure, voert u de volgende stappen uit om een resource toepassing te registreren.

### <a name="app-registrations-in-azure-portal"></a>App-registraties in Azure Portal

1. Klik in de [Azure-portal](https://portal.azure.com) in het navigatiepaneel aan de linkerkant op **Azure Active Directory**.

2. Klik in de Blade **Azure Active Directory** op **app-registraties**:

    ![Azure Portal. Nieuwe app-registratie.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klik op de **nieuwe registratie**.

### <a name="add-a-new-application-registration"></a>Een nieuwe toepassings registratie toevoegen

Vul de details in voor de nieuwe toepassing. Er zijn geen specifieke vereisten voor de weergave naam, maar als u deze instelt op de URI van de FHIR-server, is het eenvoudig om het volgende te vinden:

![Nieuwe toepassing registreren](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>Id-URI instellen en bereiken definiëren

Een resource toepassing heeft een id-URI (URI voor de toepassings-ID), die clients kunnen gebruiken bij het aanvragen van toegang tot de bron. Met deze waarde wordt de `aud` claim van het toegangs token gevuld. U kunt deze URI het beste instellen als de URI van uw FHIR-server. Voor SMART on FHIR-apps wordt ervan uitgegaan dat de *doel groep* de URI is van de FHIR-server.

1. Klik op **een API beschikbaar** maken

2. Klik op **instellen** naast de URI van de *toepassings-id*.

3. Voer de id-URI in en klik op **Opslaan**. Een goede id-URI is de URI van uw FHIR-server.

4. Klik op **een bereik toevoegen** en voeg de scopes toe die u voor de API wilt definiëren. U moet ten minste één bereik toevoegen om in de toekomst machtigingen voor uw resource toepassing te verlenen. Als u geen specifieke bereiken hebt die u wilt toevoegen, kunt u user_impersonation als bereik toevoegen.

![Doel groep en bereik](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>Toepassings rollen definiëren

De Azure-API voor FHIR en de OSS FHIR-server voor Azure gebruiken [Azure Active Directory toepassings rollen](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) voor toegangs beheer op basis van rollen. Als u wilt definiëren welke rollen beschikbaar moeten zijn voor uw FHIR-Server-API, opent u het [manifest](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/)van de resource toepassing:

1. Klik op **manifest**:

    ![Toepassings rollen](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. Voeg in de `appRoles` eigenschap de functies toe die u wilt dat gebruikers of toepassingen het volgende hebben:

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een resource toepassing kunt registreren in Azure Active Directory. Implementeer vervolgens de Azure API voor FHIR.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR implementeren](fhir-paas-powershell-quickstart.md)