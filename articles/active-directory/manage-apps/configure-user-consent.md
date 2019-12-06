---
title: Gebruikers vergunning configureren voor een toepassing-Azure Active Directory
description: Meer informatie over het beheren van de manier waarop gebruikers toestemming geven voor toepassings machtigingen. U kunt de gebruikers ervaring vereenvoudigen door toestemming van de beheerder te verlenen. Deze methoden zijn van toepassing op alle eind gebruikers in uw Azure Active Directory-Tenant (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba3cc27777d13448b0e1114413128b550b0b2f5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842285"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>De manier configureren waarop eind gebruikers toestemming geven voor een toepassing in Azure Active Directory
Meer informatie over het configureren van de manier waarop gebruikers toestemming geven voor toepassings machtigingen. U kunt de gebruikers ervaring vereenvoudigen door toestemming van de beheerder te verlenen. In dit artikel worden de verschillende manieren beschreven waarop u de gebruikers toestemming kunt configureren. De methoden zijn van toepassing op alle eind gebruikers in uw Azure Active Directory-Tenant (Azure AD). 

Zie [Azure Active Directory instemming-Framework](../develop/consent-framework.md)voor meer informatie over het inzenden van toepassingen.

## <a name="prerequisites"></a>Vereisten

Voor het verlenen van een beheerders toestemming moet u zich aanmelden als globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.

Als u de beheerder toestemming wilt geven voor machtigingen voor Microsoft Graph en Azure AD Graph, hebt u globale beheerders rechten nodig. 

Als u de toegang tot toepassingen wilt beperken, moet u de gebruikers toewijzing vereisen en vervolgens gebruikers of groepen toewijzen aan de toepassing.  Zie [methoden voor het toewijzen van gebruikers en groepen](methods-for-assigning-users-and-groups.md)voor meer informatie.

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Toestemming geven aan de beheerder voor zakelijke apps in de Azure Portal

Beheerders toestemming geven voor een bedrijfs-app:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
2. Klik op **alle services** boven aan het navigatie menu aan de linkerkant. De **uitbrei ding Azure Active Directory** wordt geopend.
3. In het zoekvak filteren typt u **' Azure Active Directory '** en selecteert u het **Azure Active Directory** item.
4. Klik in het navigatie menu op **bedrijfs toepassingen**.
5. Selecteer de app om toestemming te geven.
6. Selecteer **machtigingen** en klik vervolgens op **toestemming beheerder verlenen**. U wordt gevraagd om u aan te melden om de toepassing te beheren.
7. Meld u aan met een account dat gemachtigd is om de beheerder toestemming te geven voor de toepassing. 
8. Toestemming geven aan de machtigingen van de toepassing.

Deze optie werkt alleen als de toepassing: 

- Geregistreerd in uw Tenant of
- Geregistreerd in een andere Azure AD-Tenant en door ten minste één eind gebruiker gezonden. Zodra een eind gebruiker heeft ingestemd met een toepassing, vermeldt Azure AD de toepassing onder de **Enter prise-apps** in de Azure Portal.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Toestemming van de beheerder geven bij het registreren van een app in de Azure Portal

Toestemming van de beheerder geven bij het registreren van een app: 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder.
2. Navigeer naar de Blade **app-registraties** .
3. Selecteer de toepassing voor de toestemming.
4. Selecteer **API-machtigingen**.
5. Klik op **toestemming beheerder verlenen**.


## <a name="grant-admin-consent-through-a-url-request"></a>Beheerder toestemming geven via een URL-aanvraag

Toestemming van de beheerder verlenen via een URL-aanvraag:

1. Maak een aanvraag voor *login.microsoftonline.com* met uw app-configuraties en voeg deze toe aan `&prompt=admin_consent`. De URL ziet er als volgt uit: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=admin_consent`
2. Nadat u zich hebt aangemeld met beheerders referenties, is de app toestemming gegeven voor alle gebruikers.


## <a name="force-user-consent-through-a-url-request"></a>Toestemming geven voor de gebruiker via een URL-aanvraag

Als u wilt dat eind gebruikers elke keer dat ze worden geverifieerd, een aanvraag indienen, voegt u `&prompt=consent` toe aan de URL van de verificatie aanvraag.
De URL ziet er als volgt uit: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=consent`

## <a name="next-steps"></a>Volgende stappen

[Apps goed keuren en integreren met AzureAD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Toestemming en machtiging voor AzureAD v 2.0 geconvergeerde apps](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
