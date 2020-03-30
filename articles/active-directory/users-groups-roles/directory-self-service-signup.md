---
title: Selfservice-aanmelding voor gebruikers die zijn geverifieerd via e-mail - Azure AD | Microsoft Documenten
description: Zelfservice-aanmelding gebruiken in een Azure AD-tenant (Azure Directory)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 953837e22cdd3ba8a54d702eac61461739db82d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74027640"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Wat is selfservice-aanmelding voor Azure Active Directory?

In dit artikel wordt uitgelegd hoe u zelfservice-aanmelding gebruiken om een organisatie te vullen in Azure Active Directory (Azure AD). Zie [Een niet-beheerde map](domains-admin-takeover.md)overnemen als beheerder als u een domeinnaam wilt overnemen van een niet-beheerde Azure AD-organisatie.

## <a name="why-use-self-service-sign-up"></a>Waarom gebruik maken van self-service sign-up?
* Breng klanten sneller naar services die ze willen
* E-mailaanbiedingen voor een service maken
* Maak op e-mail gebaseerde aanmeldingsstromen waarmee gebruikers snel identiteiten kunnen maken met behulp van hun gemakkelijk te onthouden e-mailaliassen
* Een door selfservice gemaakte Azure AD-map kan worden omgezet in een beheerde map die kan worden gebruikt voor andere services

## <a name="terms-and-definitions"></a>Voorwaarden en definities
* **Self-service aanmelden:** Dit is de methode waarmee een gebruiker zich aanmeldt voor een cloudservice en een identiteit automatisch voor hen heeft gemaakt in Azure AD op basis van hun e-maildomein.
* **Onbeheerde Azure AD-map:** dit is de map waar die identiteit wordt gemaakt. Een niet-beheerde map is een map die geen globale beheerder heeft.
* **Door e-mail geverifieerde gebruiker:** dit is een type gebruikersaccount in Azure AD. Een gebruiker die een identiteit automatisch heeft gemaakt nadat hij zich heeft aangemeld voor een selfserviceaanbieding, staat bekend als een door e-mail geverifieerde gebruiker. Een door e-mail geverifieerde gebruiker is een regelmatig lid van een map die is gelabeld met creationmethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Hoe beheer ik selfservice-instellingen?
Beheerders hebben vandaag twee selfservice-besturingselementen. Zij kunnen bepalen of:

* Gebruikers kunnen lid worden van de directory via e-mail
* Gebruikers kunnen zichzelf in licentie geven voor toepassingen en services

### <a name="how-can-i-control-these-capabilities"></a>Hoe kan ik deze mogelijkheden beheren?
Een beheerder kan deze mogelijkheden configureren met behulp van de volgende Azure AD-parameters voor set-MsolCompanySettings:

* **AllowEmailVerifiedUsers** bepaalt of een gebruiker een directory kan maken of lid kunnen worden. Als u deze parameter instelt op $false, kan geen gebruiker die door e-mail is geverifieerd, lid worden van de map.
* **AllowAdHocSubscriptions** bepaalt de mogelijkheid voor gebruikers om selfservice-aanmelding uit te voeren. Als u deze parameter instelt op $false, kan geen enkele gebruiker zich aanmelden voor selfservice.
  
AllowEmailVerifiedUsers en AllowAdHocSubscriptions zijn instellingen voor een hele map die kunnen worden toegepast op een beheerde of onbeheerde map. Hier is een voorbeeld waar:

* U beheert een map met een geverifieerd domein, zoals contoso.com
* U gebruikt B2B-samenwerking vanuit een andere map omuserdoesnotexist@contoso.comeen gebruiker uit te nodigen die nog niet bestaat ( ) in de thuismap van contoso.com
* De startpagina heeft de AllowEmailVerifiedUsers ingeschakeld

Als de voorgaande voorwaarden waar zijn, wordt een lidgebruiker gemaakt in de thuismap en wordt een B2B-gastgebruiker gemaakt in de uitnodigende map.

Aanmeldingen voor de proef Van Flow en PowerApps worden niet beheerd door de instelling **AllowAdHocSubscriptions.** Raadpleeg voor meer informatie de volgende artikelen:

* [Hoe voorkom ik dat mijn bestaande gebruikers Power BI gaan gebruiken?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Vragen en antwoorden over Flow in uw organisatie](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hoe werken de besturingselementen samen?
Deze twee parameters kunnen worden gebruikt in combinatie om preciezere controle over self-service sign-up te definiëren. Met de volgende opdracht kunnen gebruikers bijvoorbeeld zelfservice-aanmelding uitvoeren, maar alleen als deze gebruikers al een account hebben in Azure AD (met andere woorden, gebruikers die een e-mailgeverifieerd account nodig hebben om eerst te worden gemaakt, kunnen zich niet aanmelden voor selfservice):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

In het volgende stroomdiagram worden de verschillende combinaties voor deze parameters en de daaruit voortvloeiende voorwaarden voor de aanmelding van de map en de selfservice uitgelegd.

![stroomdiagram van selfservice-aanmeldingsbesturingselementen](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Zie [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)voor meer informatie en voorbeelden over het gebruik van deze parameters.

## <a name="next-steps"></a>Volgende stappen

* [Een aangepaste domeinnaam toevoegen aan Azure AD](../fundamentals/add-custom-domain.md)
* [Azure PowerShell installeren en configureren](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure-cmdlet-naslaginformatie](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Uw werk- of schoolaccount sluiten in een niet-beheerde map](users-close-account.md)
