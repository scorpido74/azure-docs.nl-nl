---
title: Beveiligings beheer van Azure-veilige configuratie
description: Beveiligde configuratie van Azure-beveiligings beheer
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 347a63cc77c565d800328c19d1d543c2c9efafc0
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400089"
---
# <a name="security-control-secure-configuration"></a>Beveiligings beheer: beveiligde configuratie

De beveiligings configuratie van Azure-resources tot stand brengen, implementeren en actief beheren (bijhouden, rapporteren, corrigeren) om te voor komen dat aanvallers gevoelige services en instellingen misbruiken.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7.1 | 5,1 | Klant |

Gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de configuratie van uw Azure-resources te controleren of af te dwingen. U kunt ook ingebouwde Azure Policy definities gebruiken.

Azure Resource Manager heeft ook de mogelijkheid om de sjabloon in JavaScript Object Notation (JSON) te exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw organisatie of deze overschrijden.

U kunt ook aanbevelingen van Azure Security Center gebruiken als een veilige configuratie basislijn voor uw Azure-resources.

- [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Aanbevelingen voor beveiliging: een referentie gids](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7.2 | 5,1 | Klant |

Gebruik Azure Security Center aanbevelingen voor het onderhouden van beveiligings configuraties op alle reken resources.  Daarnaast kunt u aangepaste installatie kopieën van besturings systemen of de configuratie van Azure Automation status gebruiken om de beveiligings configuratie te bepalen van het besturings systeem dat door uw organisatie wordt vereist.

- [Azure Security Center aanbevelingen bewaken](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Aanbevelingen voor beveiliging: een referentie gids](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Overzicht van Azure Automation status configuratie](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Een VHD uploaden en gebruiken om nieuwe Windows-Vm's in azure te maken](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Een virtuele Linux-machine maken op basis van een aangepaste schijf met de Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7.3 | 5.2 | Klant |

Gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.  Daarnaast kunt u Azure Resource Manager sjablonen gebruiken om de beveiligings configuratie van uw Azure-resources te onderhouden die uw organisatie nodig heeft. 

- [Azure Policy effecten begrijpen](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Overzicht van Azure Resource Manager sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7,4 | 5.2 | Gedeeld |

Volg de aanbevelingen van Azure Security Center over het uitvoeren van evaluatie van beveiligings problemen op uw Azure Compute-resources.  Daarnaast kunt u Azure Resource Manager-sjablonen, aangepaste installatie kopieën van besturings systemen of de configuratie van Azure Automation status gebruiken om de beveiligings configuratie van het besturings systeem te onderhouden dat door uw organisatie wordt vereist.   De micro soft-sjablonen voor virtuele machines in combi natie met de Azure Automation desired state Configuration kunnen helpen bij de vergadering en het onderhouden van de beveiligings vereisten. 

Houd er ook rekening mee dat de installatie kopieën van virtuele machines van Azure Marketplace die door micro soft zijn gepubliceerd, worden beheerd en beheerd door micro soft. 

- [Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Een virtuele Azure-machine maken op basis van een Azure Resource Manager sjabloon](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Overzicht van Azure Automation status configuratie](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Een virtuele Windows-machine maken in de Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Informatie over het downloaden van de VM-sjabloon](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Voorbeeld van een script voor het uploaden van een VHD naar Azure om een nieuwe VM te maken](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7.5 | 5.3 | Klant |

Gebruik Azure DevOps om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleids regels, Azure Resource Manager sjablonen en desired state Configuration-scripts. Als u toegang wilt krijgen tot de resources die u beheert in azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps, of Active Directory als dit is geïntegreerd met TFS.

- [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Over machtigingen en groepen in azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7.6 | 5.3 | Klant |

Als u aangepaste installatie kopieën gebruikt, kunt u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de installatie kopieën. Met behulp van een galerie met gedeelde afbeeldingen kunt u uw installatie kopieën delen met verschillende gebruikers, service-principals of AD-groepen binnen uw organisatie.  Voor container installatie kopieën slaat u ze op in Azure Container Registry en maakt u gebruik van Azure RBAC om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de installatie kopieën.  

- [Meer informatie over Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Meer informatie over Azure RBAC voor Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Azure RBAC configureren](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Overzicht van Galerie gedeelde afbeeldingen](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7,7 | 5.4 | Klant |

Definieer en implementeer standaard beveiligings configuraties voor Azure-resources met behulp van Azure Policy. Gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure-resources te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleids definities die betrekking hebben op uw specifieke resources.  Daarnaast kunt u Azure Automation gebruiken om configuratie wijzigingen te implementeren.

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Aliassen gebruiken](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7,8 | 5.4 | Klant |

Azure Automation status configuratie is een configuratie beheer service voor DSC-knoop punten (desired state Configuration) in elke Cloud of on-premises Data Center. U kunt eenvoudig computers onboarden, de declaratieve configuraties toewijzen en rapporten weer geven met de naleving van elke computer die u hebt opgegeven. 

- [Onboarding van machines voor beheer door Azure Automation status configuratie](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7.9 | 5.5 | Klant |

Gebruik Azure Security Center om basislijn scans voor uw Azure-resources uit te voeren.  Gebruik Azure Policy bovendien om een waarschuwing te krijgen en Azure-resource configuraties te controleren.

- [Aanbevelingen herstellen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7.10 | 5.5 | Klant |

Gebruik Azure Security Center voor het uitvoeren van basislijn scans voor OS-en docker-instellingen voor containers.

- [Aanbevelingen van Azure Security Center voor containers](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7.11 | 13,1 | Klant |

Gebruik Managed Service Identity in combi natie met Azure Key Vault om het geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen.

- [Integratie met door Azure beheerde identiteiten](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Een Key Vault maken](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Verifiëren bij Key Vault](../../key-vault/general/authentication.md)

- [Toegangs beleid voor Key Vault toewijzen](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7.12 | 4.1 | Klant |

Gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in azure AD. Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

- [Beheerde identiteiten configureren](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 7,13 | 18,1, 18,7 | Klant |

Referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault. 

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Volgende stappen

- Zie het volgende beveiligings beheer: beveiliging  [tegen schadelijke software](security-control-malware-defense.md)
