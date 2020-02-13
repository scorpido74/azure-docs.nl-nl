---
title: Een Azure Active Directory-tenant maken | Microsoft Docs
description: Informatie over het maken van een Azure Active Directory-tenant voor het registreren en maken van toepassingen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/05/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 74747e1bc5247e2a95a180b7361eafe8ca795bc2
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160703"
---
# <a name="quickstart-set-up-a-tenant"></a>Snelstartgids: een Tenant instellen

Met het Microsoft Identity Platform kunnen ontwikkelaars apps bouwen voor een breed scala van aangepaste Microsoft 365-omgevingen en -identiteiten. Om aan de slag te gaan met het Microsoft Identity Platform hebt u toegang nodig tot een omgeving, ook wel een Azure AD-tenant genoemd, waarmee u apps kunt registreren en beheren. Daarnaast hebt u toegang nodig tot Microsoft 365-gegevens en moet u aangepaste beperkingen voor voorwaardelijke toegang en de tenant implementeren.

Een tenant vertegenwoordigt een organisatie. Een tenant is een toegewezen exemplaar van Azure AD dat een organisatie of app-ontwikkelaar ontvangt wanneer deze een relatie start met Microsoft, door zich bijvoorbeeld aan te melden voor Azure, Microsoft Intune of Microsoft 365.

Elke Azure AD-tenant is verschillend en gescheiden van andere Azure AD-tenants. Elke tenant heeft een eigen weergave van werk- en schoolidentiteiten, consumentidentiteiten (als het een Azure AD B2C-tenant betreft) en app-registraties. Met een app-registratie binnen uw tenant kunt u verificatie toestaan vanaf accounts alleen binnen uw tenants of alle tenants.

## <a name="determining-environment-type"></a>Omgevingstype bepalen

Er zijn twee soorten omgevingen die u kunt maken. Welke u nodig hebt is uitsluitend gebaseerd op de typen gebruikers die u moet verifiëren met uw app.

* Werk en school (Azure AD-accounts) of Microsoft-accounts (zoals outlook.com en live.com)
* Socialemedia-accounts en lokale accounts (Azure AD B2C)

De snelstart is verdeeld in twee scenario’s, afhankelijk van het type app dat u bouwt. Als u meer hulp nodig hebt bij het bepalen van een ID-type, raadpleegt u [Over het Microsoft Identity Platform](about-microsoft-identity-platform.md).

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Werk- en schoolaccounts of persoonlijke Microsoft-accounts

### <a name="use-an-existing-tenant"></a>Een bestaande tenant gebruiken

Veel ontwikkelaars hebben al tenants via services of abonnementen die zijn gekoppeld aan Azure AD-tenants, zoals via Microsoft 365- of Azure-abonnementen.

1. Om te controleren of u al een tenant hebt, meld u zich aan bij [Azure Portal](https://portal.azure.com) met het account dat u wilt gebruiken voor het beheren van uw toepassing.
1. Kijk in de rechterbovenhoek. Als u een tenant hebt, wordt u automatisch aangemeld en ziet u de naam van de tenant direct onder de accountnaam.
   * Houd de muisaanwijzer op uw accountnaam rechtsboven in Azure Portal om uw naam, e-mailadres, directory en tenant-id (een GUID), en uw domein te zien.
   * Als uw account is gekoppeld aan meerdere tenants, kunt u de accountnaam selecteren om een menu te openen waarmee u kunt schakelen tussen de tenants. Elke tenant heeft zijn eigen tenant-id.

> [!TIP]
> Als u de tenant-id wilt zoeken, kunt u:
> * De muisaanwijzer op uw accountnaam houden om de directory/tenant-ID te zien, of
> * **Azure Active Directory > Eigenschappen > Directory-ID** selecteren in Azure Portal.

Als er geen bestaande tenant is gekoppeld aan uw account, ziet u een GUID onder de accountnaam. U moet dan eerst een nieuwe tenant maken voordat u bewerkingen kunt uitvoeren zoals het registreren van apps. Daarvoor volgt u de stappen in het volgende gedeelte.

### <a name="create-a-new-azure-ad-tenant"></a>Een nieuwe Azure AD-tenant maken

Als u nog geen Azure AD-Tenant hebt of een nieuwe wilt maken voor ontwikkeling, raadpleegt u de [Snelstartgids](../fundamentals/active-directory-access-create-new-tenant.md) of volgt u de [ervaring](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)voor het maken van mappen. U moet de volgende informatie opgeven voor het maken van de nieuwe tenant:

- **Naam van de organisatie**
- **Eerste domein**: dit wordt onderdeel van *. onmicrosoft.com. U kunt het domein later nog aanpassen.
- **Land of regio**

> [!NOTE]
> Gebruik alfanumerieke tekens als u de tenant een naam geeft. Speciale tekens zijn niet toegestaan. De naam mag maximaal 256 tekens lang zijn.

## <a name="social-and-local-accounts"></a>Socialemedia-accounts en lokale accounts

Om te beginnen met het bouwen van apps waarmee socialemedia-accounts en lokale accounts zich aanmelden, moet u een Azure AD B2C-tenant maken. Daarvoor volgt u de stappen in [een Azure AD B2C-tenant maken](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Volgende stappen

* Probeer een snelstart voor het schrijven van code en begin met het verifiëren van gebruikers. 
* Ga voor meer gedetailleerde codevoorbeelden naar het gedeelte **Zelfstudies** van de documentatie.
* Wilt u uw app in de cloud implementeren? Lees dan [containers implementeren naar Azure](https://docs.microsoft.com/azure/index#pivot=products&panel=containers). 
