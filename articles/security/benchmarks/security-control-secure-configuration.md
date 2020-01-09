---
title: Beveiligings beheer van Azure-veilige configuratie
description: Veilige configuratie van beveiligings beheer
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 08458777d8a8c5c74ac20a63245135ffaf0a5c04
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564112"
---
# <a name="security-control-secure-configuration"></a>Beveiligings beheer: beveiligde configuratie

De beveiligings configuratie van Azure-resources tot stand brengen, implementeren en actief beheren (bijhouden, rapporteren, corrigeren) om te voor komen dat aanvallers gevoelige services en instellingen misbruiken.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7.1 | 5.1 | Klant |

Gebruik Azure Policy of Azure Security Center om beveiligings configuraties voor alle Azure-resources te onderhouden.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7.2 | 5.1 | Klant |

Gebruik Azure Security Center aanbeveling om beveiligings configuraties op uw Virtual Machines&quot; te herstellen &quot;problemen op te lossen voor het onderhouden van de beveiliging van alle reken resources.

Azure Security Center aanbevelingen bewaken:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Azure Security Center aanbevelingen oplossen:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7.3 | 5.2 | Klant |

Gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Informatie over Azure Policy effecten:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7.4 | 5.2 | Gedeeld |

Basis installatie kopieën van het besturings systeem worden beheerd en beheerd door micro soft.

U kunt echter beveiligings instellingen Toep assen die uw organisatie nodig heeft met Azure Resource Manager sjablonen en/of desired state Configuration.

Een virtuele Azure-machine maken op basis van een Azure Resource Manager sjabloon:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

De gewenste status configuratie voor Azure Virtual Machines begrijpen:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7.5 | 5.3 | Klant |

Als u aangepaste definities van Azure-beleid gebruikt, kunt u Azure Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

Code opslaan in azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentatie voor Azure opslag plaatsen:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7.6 | 5.3 | Klant |

Als u aangepaste installatie kopieën gebruikt, moet u RBAC gebruiken om ervoor te zorgen dat alleen gemachtigde gebruikers toegang kunnen krijgen tot de installatie kopieën. Voor container installatie kopieën slaat u ze op in Azure Container Registry en maakt u gebruik van RBAC om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de installatie kopieën.

Meer informatie over RBAC in Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Meer informatie over RBAC voor Container Registry:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

RBAC configureren in Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7.7 | 5.4 | Klant |

Gebruik Azure Policy om systeem configuraties te signasen, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7.8 | 5.4 | Klant |

Gebruik Azure Compute Extensions zoals de gewenste status configuratie van Power shell voor de Windows Compute-of Linux chef-extensie voor Linux.

Extensies voor virtuele machines installeren in Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7.9 | 5.5 | Klant |

Azure Security Center gebruiken om basislijn scans uit te voeren voor uw Azure-resources

Aanbevelingen herstellen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7.1 | 5.5 | Klant |

Gebruik Azure Security Center voor het uitvoeren van basislijn scans voor OS-en docker-instellingen voor containers.

Meer informatie over Azure Security Center container aanbevelingen:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren 

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7.11 | 13,1 | Klant |

Gebruik Managed Service Identity in combi natie met Azure Key Vault om het geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen.

Integratie met door Azure beheerde identiteiten: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een Key Vault maken: https://docs.microsoft.com/azure/key-vault/quick-create-portal

Key Vault verificatie bieden met een beheerde identiteit: https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7,12 | 4.1 | Klant |

Gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in azure AD. Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

Beheerde identiteiten configureren: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 7,13 | 13,3 | Klant |

Referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault. 

Referentie scanner instellen: https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Volgende stappen

Zie het volgende beveiligings beheer: beveiliging [tegen schadelijke software](security-control-malware-defense.md)
