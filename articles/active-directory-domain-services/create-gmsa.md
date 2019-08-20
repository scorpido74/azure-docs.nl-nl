---
title: 'Azure Active Directory Domain Services: Een beheerd service account voor een groep maken | Microsoft Docs'
description: Meer informatie over het maken van een beheerd service account voor een groep (gMSA) voor gebruik met Azure Active Directory Domain Services beheerde domeinen
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: 3742aed7ff39e0a2f6bdf353fb9f261176027422
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612960"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Een door een groep beheerd service account (gMSA) maken op een Azure AD Domain Services beheerd domein
In dit artikel wordt beschreven hoe u beheerde service accounts maakt in een Azure AD Domain Services beheerd domein.

## <a name="managed-service-accounts"></a>Beheerde service accounts
Een zelfstandig beheerd service account (sMSA) is een beheerd domein account waarvan het wacht woord automatisch wordt beheerd. Het vereenvoudigt het beheer van Service Principal Name (SPN) en maakt gedelegeerd beheer mogelijk voor andere beheerders. Dit type beheerde service account (MSA) is geïntroduceerd in Windows Server 2008 R2 en Windows 7.

Het door de groep beheerde service account (gMSA) biedt dezelfde voor delen voor veel servers in het domein. Alle exemplaren van een service die op een server farm wordt gehost, moeten dezelfde service-principal gebruiken voor wederzijdse verificatie protocollen om te kunnen werken. Wanneer een gMSA wordt gebruikt als Service-Principal, beheert het Windows-besturings systeem het account wachtwoord in plaats van te vertrouwen op de beheerder.

**Meer informatie:**
- [Overzicht van door groepen beheerde service accounts](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Aan de slag met door groepen beheerde service accounts](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Service accounts gebruiken in azure AD Domain Services
Azure AD Domain Services beheerde domeinen zijn vergrendeld en worden beheerd door micro soft. Er zijn enkele belang rijke aandachtspunten bij het gebruik van service accounts met Azure AD Domain Services.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Service accounts maken binnen aangepaste organisatie-eenheden (OE) op het beheerde domein
U kunt geen service account maken in de ingebouwde organisatie-eenheden ' AADDC gebruikers ' of ' AADDC computers '. [Maak een aangepaste OE](create-ou.md) in uw beheerde domein en maak vervolgens service accounts binnen die aangepaste organisatie-eenheid.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>De hoofd sleutel voor Key Distribution Services (KDS) is al vooraf gemaakt
De basis sleutel van de Key Distribution Services (KDS) wordt vooraf gemaakt op een Azure AD Domain Services beheerd domein. U hoeft geen KDS-basis sleutel te maken en u hebt hiervoor geen bevoegdheden. U kunt de basis sleutel KDS niet weer geven in het beheerde domein.

## <a name="sample---create-a-gmsa-using-powershell"></a>Voor beeld: een gMSA maken met Power shell
In het volgende voor beeld ziet u hoe u een aangepaste organisatie-eenheid maakt met behulp van Power shell. U kunt vervolgens een gMSA maken binnen die organisatie-eenheid met ```-Path``` behulp van de para meter om de OE op te geven.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=contoso,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso.com  `
-Path "OU=MYNEWOU,DC=contoso,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso.com/contoso.com, `
http/WebFarmSvc.contoso.com/contoso,  `
http/WebFarmSvc/contoso.com, http/WebFarmSvc/contoso  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**Documentatie voor Power shell-cmdlets:**
- [Cmdlet New-ADOrganizationalUnit](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [Cmdlet New-ADServiceAccount](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Volgende stappen
- [Een aangepaste organisatie-eenheid maken in een beheerd domein](create-ou.md)
- [Overzicht van door groepen beheerde service accounts](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Aan de slag met door groepen beheerde service accounts](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
