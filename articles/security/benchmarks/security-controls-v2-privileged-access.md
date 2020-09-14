---
title: Azure Security Bench Mark v2-privileged Access
description: Uitgebreide toegang voor Azure Security Bench Mark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059234"
---
# <a name="security-control-privileged-access"></a>Beveiligings beheer: Privileged Access

Uitgebreide toegang heeft betrekking op besturings elementen voor het beveiligen van bevoegde toegang tot uw Azure-Tenant en-resources. Dit omvat een reeks besturings elementen voor het beveiligen van uw beheer model, beheerders accounts en geprivilegieerde toegangs werk stations tegen opzettelijke en onbedoelde Risico's.

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1: de algemene beheerders beveiligen en beperken

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

Gebruikers die zijn toegewezen aan de rol van globale beheerder kunnen elke beheer instelling in uw Azure AD-organisatie lezen en wijzigen. Beperk het aantal accounts van uw globale Azure-beheerder tot niet meer dan twee voor elk abonnement. De meest essentiële ingebouwde rollen in azure AD zijn globale beheerder en de beheerder van de bevoegde rol als gebruikers die aan deze twee rollen zijn toegewezen, kunnen beheerders rollen overdragen:
- Beheerder van globale beheerder/bedrijf: gebruikers met deze rol hebben toegang tot alle beheer functies in azure AD, evenals services die gebruikmaken van Azure AD-identiteiten.

- Beheerder van geprivilegieerde rol: gebruikers met deze rol kunnen roltoewijzingen beheren in azure AD, en in Azure AD Privileged Identity Management (PIM). Daarnaast kunt u met deze rol alle aspecten van PIM en administratieve eenheden beheren.

Opmerking: u kunt andere essentiële rollen hebben die moeten worden onderhevig aan de hand van aangepaste rollen waaraan bepaalde privileged permissions zijn toegewezen. Het is ook mogelijk dat u vergelijk bare besturings elementen wilt Toep assen op het beheerders account van essentiële bedrijfs activa.  

U kunt met behulp van Azure AD Privileged Identity Management (PIM) just-in-time-toegang verlenen tot Azure-resources en Azure AD. JIT verleent tijdelijke machtigingen voor het uitvoeren van geprivilegieerde taken alleen wanneer gebruikers deze nodig hebben. PIM kan ook beveiligings waarschuwingen genereren wanneer er verdachte of onveilige activiteiten in uw Azure AD-organisatie zijn.

Opmerking: sommige Azure-Services zoals Azure SQL ondersteunen lokale gebruikers verificatie naast de Azure AD-verificatie. Dit type lokale gebruikers verificatie wordt niet beheerd via Azure AD. U moet deze gebruikers afzonderlijk beheren.

- [Machtigingen voor beheerdersrol in azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Aangepaste Azure-rollen](../../role-based-access-control/custom-roles.md)

- [Beveiligings waarschuwingen van Azure Privileged Identity Management gebruiken](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Een directory-rol verkrijgen in azure AD met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Azure Security Center gebruiken om identiteit en toegang te bewaken](../../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Identiteit en sleutels](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2: gebruikers toegang regel matig controleren en afstemmen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-2 | 4,1, 16,9, 16,10 | AC-2 |

Gebruikers accounts en toegangs rechten regel matig controleren om ervoor te zorgen dat de gebruikers accounts en hun toegang geldig zijn. 

Gebruik Azure AD Identity and Access revisies om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen te beheren. Azure AD Reporting kan logboeken bieden waarmee u verlopen accounts kunt detecteren. U kunt ook Azure AD Privileged Identity Management gebruiken om een werk stroom voor het lezen van rapporten te maken om het controle proces te vergemakkelijken.

Voor gebruikers met beheerders rechten op de Azure-service en het werkbelasting niveau moet een frequentere gebruikers-en toegangs beoordeling worden uitgevoerd. U kunt ook Azure Privileged Identity Management-beveiligings waarschuwingen gebruiken om te detecteren wanneer er beheerders accounts worden gemaakt en om beheerders accounts te vinden die verouderd of onjuist zijn geconfigureerd. 

Opmerking: sommige Azure-Services, zoals Azure SQL, ondersteunen lokale gebruikers die niet worden beheerd via Azure AD. U moet deze gebruikers afzonderlijk beheren.

- [Een directory-rol verkrijgen in azure AD met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Leden van een directory-rol in azure AD ophalen met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Azure Security Center gebruiken om identiteit en toegang te bewaken](../../security-center/security-center-identity-access.md)

- [Beveiligings waarschuwingen van Azure Privileged Identity Management gebruiken](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Beheer verantwoordelijkheden in teams uitlijnen](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [Meer informatie over Azure AD-rapportage](/azure/active-directory/reports-monitoring/)

- [Azure AD-identiteits-en toegangs beoordelingen gebruiken](../../active-directory/governance/access-reviews-overview.md)

- [Privileged Identity Management-de toegang tot Azure AD-rollen controleren](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Identiteits-en toegangs controle Azure Security Center](../../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Identiteit en sleutels](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Beveiligingsnalevingsbeheer](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3: een nood toegangs account instellen in azure AD

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-3 | 12,3 | AC-2 |

Als u wilt voor komen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een account voor toegang voor nood gevallen in voor toegang wanneer normale beheerders accounts niet kunnen worden gebruikt. Accounts voor toegang in nood gevallen zijn meestal zeer goed beschermd en ze mogen niet worden toegewezen aan specifieke personen. Accounts voor toegang in nood gevallen zijn beperkt tot scenario's met nood gevallen of ' afbreek glazen ', waarbij normale beheerders accounts niet kunnen worden gebruikt.

Zorg ervoor dat de referenties (zoals wacht woord, certificaat of Smart Card) voor accounts voor toegang in nood gevallen beveiligd blijven en alleen bekend zijn bij personen die alleen in een nood geval mogen gebruiken.

- [Accounts voor nood toegang beheren in azure AD](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Identiteit en sleutels](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Beveiligingsnalevingsbeheer](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Security Operations Center (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4: de werk stroom voor identiteits-en toegangs aanvragen van Azure automatiseren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-4 | N.v.t. | AC-2, AC-5, PM-10 |

Gebruik Azure AD-functies voor rechten beheer om werk stromen voor Azure-toegangs aanvragen te automatiseren, met inbegrip van toegangs toewijzingen, beoordelingen en verval datums. Het is ook mogelijk om twee of meerdere fasen goed te keuren.  

- [Wat is het beheer van rechten van Azure AD](../../active-directory/governance/entitlement-management-overview.md)

- [Toegangs aanvraag en goedkeurings proces instellen](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Identiteit en sleutels](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Beveiligingsnalevingsbeheer](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5: gebruik Maxi maal beveiligde computers voor beheer taken

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-5 | 4,6, 11,6, 12,12 | AC-2, SC-7 |

Beveiligde, geïsoleerde werk stations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkel aars en essentiële service operators. Gebruik zeer beveiligde werk stations van gebruikers en/of Azure Bastion voor beheer taken:
- Gebruik Azure Active Directory, micro soft Defender Advanced Threat Protection (ATP) en/of Microsoft Intune voor het implementeren van een beveiligd en beheerd gebruikers werkstation voor beheer taken. De beveiligde werk stations kunnen centraal worden beheerd voor het afdwingen van beveiligde configuratie, waaronder sterke authenticatie, software-en hardware-basis lijnen, beperkte logische en netwerk toegang. 

- Gebruik de Azure Bastion-functie voor een veilig pad om toegang te krijgen tot uw virtuele machines via RDP of SSH. Azure Bastion is een volledig beheerde PaaS-service die per virtueel netwerk kan worden ingericht zonder dat er een nieuw virtueel netwerk wordt gemaakt. 

- [Meer informatie over veilige, door Azure beheerde werk stations](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Een beveiligd, door Azure beheerd werk station implementeren](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Azure bastion-host gebruiken](../../bastion/bastion-create-host-portal.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Security Operations Center (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Identiteit en sleutels](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6: machtigingen toewijzen aan resources met behulp van Azure RBAC

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-6 | 14,6 | AC-2, AC-3 |

Met op rollen gebaseerd toegangs beheer (RBAC) van Azure kunt u machtigingen voor toegang tot Azure-bronnen beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er zijn vooraf gedefinieerde ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via hulpprogram ma's als Azure CLI, Azure PowerShell of de Azure Portal. 

- [Wat is Azure Role-based Access Control (Azure RBAC)](../../role-based-access-control/overview.md)

- [RBAC configureren in azure](../../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Beveiligingsnalevingsbeheer](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Identiteit en sleutels](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7: goedkeurings proces kiezen voor micro soft ondersteuning

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-7 | 16 | AC-2, AC-3, AC-4 |

In ondersteunings scenario's waarin micro soft toegang heeft tot klant gegevens, biedt Klanten-lockbox u de mogelijkheid om elke aanvraag voor de toegang tot gegevens van klanten expliciet te controleren en goed te keuren of af te wijzen.

- [Klanten-lockbox begrijpen](../fundamentals/customer-lockbox-overview.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Beveiligingsnalevingsbeheer](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Identiteit en sleutels](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

