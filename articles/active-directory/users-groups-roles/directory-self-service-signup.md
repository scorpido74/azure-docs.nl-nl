---
title: Selfservice registratie voor door e-mail geverifieerde gebruikers-Azure AD | Microsoft Docs
description: Aanmelden via self-service gebruiken in een Azure Active Directory-Tenant (Azure AD)
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74027640"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Wat is een self-service registratie voor Azure Active Directory?

In dit artikel wordt uitgelegd hoe u Self-Service-aanmelding kunt gebruiken om een organisatie te vullen in Azure Active Directory (Azure AD). Als u een domein naam wilt overnemen uit een niet-beheerde Azure AD-organisatie, raadpleegt u een niet- [beheerde Directory als Administrator overnemen](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Waarom gebruik maken van self-service registratie?
* Klanten sneller op de gewenste services ontvangen
* Op e-mail gebaseerde aanbiedingen voor een service maken
* Op e-mail gebaseerde aanmeldings stromen maken waarmee gebruikers snel identiteiten kunnen maken met behulp van hun gemakkelijk te onthouden werk-e-mail aliassen
* Een door selfservice gemaakte Azure AD-Directory kan worden omgezet in een beheerde map die kan worden gebruikt voor andere services

## <a name="terms-and-definitions"></a>Voorwaarden en definities
* **Aanmelden voor selfservice**: dit is de methode waarmee een gebruiker zich aanmeldt voor een Cloud service en die automatisch een identiteit heeft gemaakt in azure AD op basis van hun e-mail domein.
* Niet- **beheerde Azure AD-adres lijst**: dit is de map waarin de identiteit is gemaakt. Een onbeheerde map is een map zonder globale beheerder.
* Door **e-mail geverifieerde gebruiker**: dit is een type gebruikers account in azure AD. Een gebruiker met een identiteit die automatisch is gemaakt nadat u zich hebt aangemeld voor een self-service aanbieding, wordt een door e-mail geverifieerde gebruiker genoemd. Een door e-mail geverifieerde gebruiker is een standaard lid van een map die is gelabeld met creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Instellingen voor Self-service voor Hoe kan ik besturings element?
Beheerders hebben vandaag nog twee selfservice besturings elementen. Ze kunnen bepalen of:

* Gebruikers kunnen lid worden van de map via e-mail
* Gebruikers kunnen zichzelf een licentie voor toepassingen en services

### <a name="how-can-i-control-these-capabilities"></a>Hoe kan ik deze mogelijkheden beheren?
Een beheerder kan deze mogelijkheden configureren met de volgende para meters van de Azure AD-cmdlet Set-MsolCompanySettings:

* **AllowEmailVerifiedUsers** bepaalt of een gebruiker een map kan maken of toevoegen. Als u die para meter instelt op $false, kan geen door e-mail geverifieerde gebruiker lid worden van de map.
* Met **AllowAdHocSubscriptions** bepaalt u de mogelijkheid voor gebruikers om selfservice registratie uit te voeren. Als u die para meter instelt op $false, kan geen enkele gebruiker aanmelden met selfservice service uitvoeren.
  
AllowEmailVerifiedUsers en AllowAdHocSubscriptions zijn instellingen voor de hele directory die kunnen worden toegepast op een beheerde of onbeheerde map. Hier volgt een voor beeld waarin:

* U beheert een directory met een geverifieerd domein, zoals contoso.com
* U maakt gebruik van B2B-samen werking vanuit een andere Directory om een gebruiker die nog nietuserdoesnotexist@contoso.combestaat () uit te nodigen in de hoofdmap van contoso.com
* De AllowEmailVerifiedUsers is ingeschakeld op de basismap

Als aan de voor waarden wordt voldaan, wordt een lid van de gebruiker in de basismap gemaakt en wordt een B2B-gast gebruiker gemaakt in de uitgenodigde map.

De aanmeldingen voor de proef versie van flow en PowerApps worden niet beheerd door de **AllowAdHocSubscriptions** -instelling. Raadpleeg voor meer informatie de volgende artikelen:

* [Hoe voorkom ik dat mijn bestaande gebruikers Power BI gaan gebruiken?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Vragen en antwoorden over Flow in uw organisatie](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hoe werken de besturings elementen samen?
Deze twee para meters kunnen worden gebruikt in combi natie met het definiëren van nauw keurigere controle over self-service registratie. Met de volgende opdracht kunnen gebruikers zich bijvoorbeeld aanmelden voor selfservice registratie, maar alleen als deze gebruikers al een account hebben in azure AD (met andere woorden, gebruikers die een e-mail account moeten maken dat eerst moet worden gemaakt, kan niet zelf een aanmelding uitvoeren):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

In het volgende diagram ziet u de verschillende combi Naties voor deze para meters en de resulterende voor waarden voor de Directory en self-service registratie.

![stroom diagram van self-service registratie besturings elementen](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Zie [set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)voor meer informatie en voor beelden van het gebruik van deze para meters.

## <a name="next-steps"></a>Volgende stappen

* [Een aangepaste domein naam toevoegen aan Azure AD](../fundamentals/add-custom-domain.md)
* [Azure PowerShell installeren en configureren](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure-cmdlet-naslaginformatie](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Uw werk-of school account sluiten in een niet-beheerde map](users-close-account.md)
