---
title: Azure-beveiligingsbeheer - beveiligde configuratie
description: Beveiligde configuratie met beveiligingsbeheer
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934296"
---
# <a name="security-control-secure-configuration"></a>Beveiligingscontrole: beveiligde configuratie

Stel de beveiligingsconfiguratie van Azure-resources vast, implementeer en beheer deze actief (traceer, rapporteer, corrigeert) om te voorkomen dat aanvallers misbruik maken van kwetsbare services en instellingen.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties instellen voor alle Azure-resources

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.1 | 5.1 | Klant |

Gebruik Azure Policy of Azure Security Center om beveiligingsconfiguraties voor alle Azure-bronnen te onderhouden.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige configuratie van het besturingssysteem instellen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.2 | 5.1 | Klant |

Gebruik azure security &quot;center-aanbeveling Verwerk kwetsbaarheden in beveiligingsconfiguraties op uw virtuele machines&quot; om beveiligingsconfiguraties op alle rekenbronnen te behouden.

Aanbevelingen voor Azure Security Center controleren:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Aanbevelingen voor Azure Security Center herstellen:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-bronconfiguraties behouden

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.3 | 5.2 | Klant |

Gebruik Azure-beleid [weigeren] en [implementeren als deze niet bestaan] om beveiligde instellingen voor uw Azure-bronnen af te dwingen.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Inzicht in Azure-beleidseffecten:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige configuratie van het besturingssysteem behouden

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.4 | 5.2 | Gedeeld |

Afbeeldingen van het basisbesturingssysteem worden beheerd en onderhouden door Microsoft.

U echter beveiligingsinstellingen toepassen die door uw organisatie vereist zijn met Azure Resource Manager-sjablonen en/of gewenste statusconfiguratie.

Een Azure Virtual Machine maken op basis van een Azure Resource Manager-sjabloon:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Inzicht in de gewenste statusconfiguratie voor virtuele azure-machines:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-bronnen veilig opslaan

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7,5 | 5.3 | Klant |

Als u aangepaste Azure-beleidsdefinities gebruikt, gebruikt u Azure DevOps of Azure Repos om uw code veilig op te slaan en te beheren.

Code opslaan in Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos-documentatie:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Afbeeldingen van aangepaste besturingssysteemen veilig opslaan

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.6 | 5.3 | Klant |

Als u aangepaste afbeeldingen gebruikt, gebruikt u RBAC om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de afbeeldingen. Sla ze voor containerafbeeldingen op in Azure Container Registry en maak gebruik van RBAC om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de afbeeldingen.

RBAC in Azure begrijpen:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Begrijpen RBAC voor Container Registry:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

RBAC configureren in Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7: Hulpprogramma's voor systeemconfiguratiebeheer implementeren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.7 | 5.4 | Klant |

Gebruik Azure Policy om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Bovendien ontwikkelt u een proces en pijplijn voor het beheren van beleidsuitzonderingen.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Systeemconfiguratiebeheertools implementeren voor besturingssystemen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.8 | 5.4 | Klant |

Gebruik Azure compute extensions zoals PowerShell Desired State Configuration for Windows compute or Linux Chef Extension for Linux.

Virtuele machine-extensies installeren in Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-services

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.9 | 5.5 | Klant |

Azure Security Center gebruiken om basislijnscans uit te voeren voor uw Azure Resources

Aanbevelingen herstellen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.1 | 5.5 | Klant |

Gebruik Azure Security Center om basislijnscans uit te voeren voor OS- en Docker-instellingen voor containers.

Informatie over containeraanbevelingen van Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren 

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.11 | 13.1 | Klant |

Gebruik Managed Service Identity in combinatie met Azure Key Vault om geheim beheer voor uw cloudtoepassingen te vereenvoudigen en te beveiligen.

Hoe te integreren met Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een key vault maken:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Key Vault-verificatie voorzien van een beheerde identiteit:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.12 | 4.1 | Klant |

Gebruik Beheerde identiteiten om Azure-services een automatisch beheerde identiteit te bieden in Azure AD. Met Managed Identities u zich verifiëren voor elke service die Azure AD-verificatie ondersteunt, inclusief Key Vault, zonder referenties in uw code.

Beheerde identiteiten configureren:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling aan referenties elimineren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.13 | 13.3 | Klant |

Implementeer Credential Scanner om referenties binnen code te identificeren. Credential Scanner stimuleert ook het verplaatsen van ontdekte referenties naar veiligere locaties zoals Azure Key Vault. 

Credential Scanner instellen:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende beveiligingscontrole: [Malware Defense](security-control-malware-defense.md)
