---
title: Azure Security Bench Mark v2-privileged Access
description: Uitgebreide toegang voor Azure Security Bench Mark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 68fdff9444286a7f304c3a3361ad33a02e87a282
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758367"
---
# <a name="security-control-v2-privileged-access"></a>Beveiligings controle v2: Privileged Access

Uitgebreide toegang heeft betrekking op besturings elementen voor het beveiligen van bevoegde toegang tot uw Azure-Tenant en-resources. Dit omvat een reeks besturings elementen voor het beveiligen van uw beheer model, beheerders accounts en geprivilegieerde toegangs werk stations tegen opzettelijke en onbedoelde Risico's.

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: gebruikers met hoge bevoegdheden beveiligen en beperken

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

Beperk het aantal gebruikers accounts met zeer privileged en beveilig deze accounts op een hoger niveau. De meest essentiële ingebouwde rollen in azure AD zijn globale beheerder en de beheerder van de geprivilegieerde rol, omdat gebruikers die aan deze twee rollen zijn toegewezen, beheerders rollen kunnen delegeren. Met deze bevoegdheden kunnen gebruikers direct of indirect elke resource in uw Azure-omgeving lezen en wijzigen:

- Beheerder van globale beheerder/bedrijf: gebruikers met deze rol hebben toegang tot alle beheer functies in azure AD, evenals services die gebruikmaken van Azure AD-identiteiten.

- Beheerder van geprivilegieerde rol: gebruikers met deze rol kunnen roltoewijzingen beheren in azure AD, en in Azure AD Privileged Identity Management (PIM). Daarnaast kunt u met deze rol alle aspecten van PIM en administratieve eenheden beheren.

Opmerking: u kunt andere essentiële rollen hebben die moeten worden onderhevig aan de hand van aangepaste rollen waaraan bepaalde privileged permissions zijn toegewezen. Het is ook mogelijk dat u vergelijk bare besturings elementen wilt Toep assen op het beheerders account van essentiële bedrijfs activa.  

U kunt met behulp van Azure AD Privileged Identity Management (PIM) just-in-time-toegang verlenen tot Azure-resources en Azure AD. JIT verleent tijdelijke machtigingen voor het uitvoeren van geprivilegieerde taken alleen wanneer gebruikers deze nodig hebben. PIM kan ook beveiligings waarschuwingen genereren wanneer er verdachte of onveilige activiteiten in uw Azure AD-organisatie zijn.

- [Machtigingen voor beheerdersrol in azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Beveiligings waarschuwingen van Azure Privileged Identity Management gebruiken](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: beheer toegang beperken tot essentiële bedrijfs systemen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-2 | 13,2, 2,10 | AC-2, SC-3, SC-7 |

Isoleer de toegang tot bedrijfskritische systemen door te beperken welke accounts bevoegde toegang krijgen tot de abonnementen en beheer groepen waarin ze zich bevinden. Zorg ervoor dat u ook de toegang tot de beheer-, identiteits-en beveiligings systemen beperkt die beheerders toegang hebben tot uw bedrijfs kritieke assets, zoals Active Directory-domein controllers (Dc's), beveiligings hulpprogramma's en Systeembeheer Programma's met agents die zijn geïnstalleerd op essentiële bedrijfs systemen. Aanvallers die deze beheer-en beveiligings systemen misbruiken, kunnen ze onmiddellijk weaponize om bedrijfs kritieke activa te beschadigen. 

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen. 

Zorg ervoor dat u afzonderlijke bevoorrechte accounts toewijst die verschillend zijn van de standaard gebruikers accounts die worden gebruikt voor e-mail, surfen en productiviteits taken.

- [Azure-onderdelen en referentie model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Toegang tot beheer groepen](../../governance/management-groups/overview.md#management-group-access)

- [Beheerders van Azure-abonnementen](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: gebruikers toegang regel matig controleren en afstemmen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-3 | 4,1, 16,9, 16,10 | AC-2 |

Controleer gebruikers accounts en toegangs toewijzingen regel matig om te controleren of de accounts en hun toegangs niveau geldig zijn. U kunt Azure AD-toegangs beoordelingen gebruiken om groepslid maatschappen, de toegang tot bedrijfs toepassingen en roltoewijzingen te controleren. Azure AD Reporting kan logboeken bieden waarmee u verlopen accounts kunt detecteren. U kunt Azure AD Privileged Identity Management ook gebruiken om een werk stroom voor een toegangs beoordeling te maken waarmee het controle proces wordt vergemakkelijkt.
Daarnaast kan Azure Privileged Identity Management worden geconfigureerd om te waarschuwen wanneer er een uitzonderlijk groot aantal beheerders accounts worden gemaakt en om beheerders accounts te identificeren die verouderd of onjuist zijn geconfigureerd. 

Opmerking: sommige Azure-Services ondersteunen lokale gebruikers en rollen die niet worden beheerd via Azure AD. U moet deze gebruikers afzonderlijk beheren.

- [Een toegangs beoordeling van Azure-resource rollen maken in Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Azure AD-identiteits-en toegangs beoordelingen gebruiken](../../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Emergency Access instellen in azure AD

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-4 | 16 | AC-2, CP-2 |

Als u wilt voor komen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een account voor toegang voor nood gevallen in voor toegang wanneer normale beheerders accounts niet kunnen worden gebruikt. Accounts voor toegang in nood gevallen zijn meestal zeer goed beschermd en ze mogen niet worden toegewezen aan specifieke personen. Accounts voor toegang in nood gevallen zijn beperkt tot scenario's met nood gevallen of ' afbreek glazen ', waarbij normale beheerders accounts niet kunnen worden gebruikt.
Zorg ervoor dat de referenties (zoals wacht woord, certificaat of Smart Card) voor accounts voor toegang in nood gevallen beveiligd blijven en alleen bekend zijn bij personen die alleen in een nood geval mogen gebruiken.

- [Accounts voor nood toegang beheren in azure AD](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Beveiligings bewerkingen (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5: beheer van rechten automatiseren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-5 | 16 | AC-2, AC-5, PM-10 |

Gebruik Azure AD-functies voor rechten beheer om werk stromen voor toegangs aanvragen te automatiseren, met inbegrip van toegangs toewijzingen, beoordelingen en verval datums. Het is ook mogelijk om twee of meerdere fasen goed te keuren.
- [Wat zijn Azure AD-toegangs beoordelingen](../../active-directory/governance/access-reviews-overview.md) 

- [Wat is het beheer van rechten van Azure AD](../../active-directory/governance/entitlement-management-overview.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6: bevoorrechte toegang gebruiken werk stations

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-6 | 4,6, 11,6, 12,12 | AC-2, SC-3, SC-7 |

Beveiligde, geïsoleerde werk stations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkel aars en essentiële service operators. Gebruik zeer beveiligde werk stations van gebruikers en/of Azure Bastion voor beheer taken. Gebruik Azure Active Directory, micro soft Defender Advanced Threat Protection (ATP) en/of Microsoft Intune voor het implementeren van een beveiligd en beheerd gebruikers werkstation voor beheer taken. De beveiligde werk stations kunnen centraal worden beheerd voor het afdwingen van beveiligde configuratie, inclusief sterke authenticatie, software-en hardware-basis lijnen en beperkte logische en netwerk toegang. 

- [Meer informatie over privileged Access workstations](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Een privileged Access-werk station implementeren](../../active-directory/devices/howto-azure-managed-workstation.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Beveiligings bewerkingen (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Volg gewoon voldoende beheer (minimale bevoegdheids methode)

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-7 | 14,6 | AC-2, AC-3, SC-3 |

Met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) kunt u toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeps service-principals en beheerde identiteiten. Er zijn vooraf gedefinieerde ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via hulpprogram ma's als Azure CLI, Azure PowerShell en de Azure Portal. De bevoegdheden die u toewijst aan resources via Azure RBAC, moeten altijd beperkt zijn tot wat is vereist voor de rollen. Beperkte bevoegdheden vormen een aanvulling op de just-in-time (JIT) benadering van Azure AD Privileged Identity Management (PIM), en deze bevoegdheden moeten regel matig worden gecontroleerd.
Gebruik ingebouwde rollen om machtigingen toe te wijzen en alleen aangepaste rollen te maken wanneer dat nodig is. 

- [Wat is Azure Role-based Access Control (Azure RBAC)](../../role-based-access-control/overview.md)

- [Azure RBAC configureren](../../role-based-access-control/role-assignments-portal.md)

- [Azure AD-identiteits-en toegangs beoordelingen gebruiken](../../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: goedkeurings proces kiezen voor micro soft-ondersteuning 

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| PA-8 | 16 | AC-2, AC-3, AC-4 |

In ondersteunings scenario's waarin micro soft toegang moet krijgen tot klant gegevens, biedt Klanten-lockbox u de mogelijkheid om elke aanvraag voor gegevens toegang van een klant expliciet te controleren en goed te keuren of af te wijzen.

- [Klanten-lockbox begrijpen](../fundamentals/customer-lockbox-overview.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

