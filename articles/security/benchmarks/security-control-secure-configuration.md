---
title: Azure-beveiligingsbeheer - beveiligde configuratie
description: Beveiligde configuratie van Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 981e13e772ee21e0e4a680e6d07b4ad4892a7cee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417057"
---
# <a name="security-control-secure-configuration"></a>Beveiligingscontrole: beveiligde configuratie

Stel de beveiligingsconfiguratie van Azure-resources vast, implementeer en beheer deze actief (traceer, rapporteer, corrigeert) om te voorkomen dat aanvallers misbruik maken van kwetsbare services en instellingen.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties instellen voor alle Azure-resources

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.1 | 5.1 | Klant |

Gebruik Azure Policy-aliassen om aangepaste beleidsregels te maken om de configuratie van uw Azure-bronnen te controleren of af te dwingen. U ook ingebouwde Azure-beleidsdefinities gebruiken.

Azure Resource Manager heeft ook de mogelijkheid om de sjabloon te exporteren in JavaScript Object Notatie (JSON), die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan / hoger zijn dan de beveiligingsvereisten voor uw organisatie.

U ook aanbevelingen van Azure Security Center gebruiken als een veilige configuratiebasislijn voor uw Azure-bronnen.

- [Beschikbare Azure-beleidsaliassen weergeven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Zelfstudie: Beleid maken en beheren om naleving af te dwingen](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Export met één en meerdere resources naar een sjabloon in Azure-portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Beveiligingsaanbevelingen - een naslaggids](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige configuratie van het besturingssysteem instellen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.2 | 5.1 | Klant |

Gebruik aanbevelingen voor Azure Security Center om beveiligingsconfiguraties op alle compute resources te behouden.  Bovendien u aangepaste besturingssysteemafbeeldingen of Azure Automation State-configuratie gebruiken om de beveiligingsconfiguratie vast te stellen van het besturingssysteem dat door uw organisatie wordt vereist.

- [Azure Security Center-aanbevelingen controleren](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Beveiligingsaanbevelingen - een naslaggids](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Overzicht van configuratie van Azure Automation State](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Upload een VHD en gebruik deze om nieuwe Windows VM's in Azure te maken](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Een Linux-vm maken op basis van een aangepaste schijf met de Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-bronconfiguraties behouden

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.3 | 5.2 | Klant |

Gebruik Azure-beleid [weigeren] en [implementeren als deze niet bestaan] om beveiligde instellingen voor uw Azure-bronnen af te dwingen.  Bovendien u Azure Resource Manager-sjablonen gebruiken om de beveiligingsconfiguratie te behouden van uw Azure-resources die vereist zijn voor uw organisatie. 

- [Azure-beleidseffecten begrijpen](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Overzicht van Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige configuratie van het besturingssysteem behouden

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.4 | 5.2 | Gedeeld |

Volg aanbevelingen van Azure Security Center voor het uitvoeren van kwetsbaarheidsbeoordelingen op uw Azure-rekenbronnen.  Bovendien u Azure Resource Manager-sjablonen, aangepaste besturingssysteemafbeeldingen of Azure Automation State-configuratie gebruiken om de beveiligingsconfiguratie te behouden van het besturingssysteem dat door uw organisatie wordt vereist.   De virtuele machinesjablonen van Microsoft in combinatie met de gewenste azure-configuratie van automatisering kunnen helpen bij het voldoen aan en onderhouden van de beveiligingsvereisten. 

Houd er ook rekening mee dat Azure Marketplace Virtual Machine Images gepubliceerd door Microsoft worden beheerd en onderhouden door Microsoft. 

- [Aanbevelingen voor azure security center-kwetsbaarheidsbeoordeling implementeren](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Een Azure Virtual Machine maken op basis van een Azure Resource Manager-sjabloon](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Overzicht van configuratie van Azure Automation State](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Een virtuele Windows-machine maken in de Azure-portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Informatie over het downloaden van de VM-sjabloon](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Voorbeeld van een script voor het uploaden van een VHD naar Azure om een nieuwe VM te maken](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-bronnen veilig opslaan

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7,5 | 5.3 | Klant |

Gebruik Azure DevOps om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleidsregels, Azure Resource Manager-sjablonen en gewenste statusconfiguratiescripts. Als u toegang wilt krijgen tot de resources die u beheert in Azure DevOps, u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligingsgroepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Active Directory als deze zijn geïntegreerd met TFS.

- [Code opslaan in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informatie over machtigingen en groepen in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Afbeeldingen van aangepaste besturingssysteemen veilig opslaan

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.6 | 5.3 | Klant |

Als u aangepaste afbeeldingen gebruikt, gebruikt u op rollen gebaseerd toegangscontrolebeheer (RBAC) om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de afbeeldingen. Met behulp van een gedeelde afbeeldingsgalerie u uw afbeeldingen delen met verschillende gebruikers, serviceprincipals of AD-groepen binnen uw organisatie.  Sla ze voor containerafbeeldingen op in Azure Container Registry en maak gebruik van RBAC om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de afbeeldingen.  

- [RBAC begrijpen in Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Begrijpen RBAC voor Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [RBAC configureren in Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Overzicht van gedeelde afbeeldingengalerij](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.7 | 5.4 | Klant |

Standaardbeveiligingsconfiguraties voor Azure-resources definiëren en implementeren met Azure Policy. Gebruik Azure Policy-aliassen om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure-bronnen te controleren of af te dwingen. U ook gebruik maken van ingebouwde beleidsdefinities met betrekking tot uw specifieke resources.  Bovendien u Azure Automation gebruiken om configuratiewijzigingen te implementeren.

- [Azure-beleid configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Aliassen gebruiken](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Configuratiebeheertools implementeren voor besturingssystemen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.8 | 5.4 | Klant |

Azure Automation State Configuration is a configuration management service for Desired State Configuration (DSC) nodes in any cloud or on-premises datacenter. U eenvoudig machines aan boord nemen, ze declaratieve configuraties toewijzen en rapporten weergeven die de naleving van de gewenste status van elke machine weergeven. 

- [Onboarding-machines voor beheer door Azure Automation State Configuration](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.9 | 5.5 | Klant |

Gebruik Azure Security Center om basislijnscans uit te voeren voor uw Azure Resources.  Gebruik bovendien Azure Policy om Azure-bronconfiguraties te waarschuwen en te controleren.

- [Aanbevelingen herstellen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.10 | 5.5 | Klant |

Gebruik Azure Security Center om basislijnscans uit te voeren voor OS- en Docker-instellingen voor containers.

- [Aanbevelingen van Azure Security Center voor containers](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.11 | 13.1 | Klant |

Gebruik Managed Service Identity in combinatie met Azure Key Vault om geheim beheer voor uw cloudtoepassingen te vereenvoudigen en te beveiligen.

- [Integreren met Azure Managed Identities](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Een key vault maken](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Key Vault-verificatie voorzien van een beheerde identiteit](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.12 | 4.1 | Klant |

Gebruik Beheerde identiteiten om Azure-services een automatisch beheerde identiteit te bieden in Azure AD. Met Managed Identities u zich verifiëren voor elke service die Azure AD-verificatie ondersteunt, inclusief Key Vault, zonder referenties in uw code.

- [Beheerde identiteiten configureren](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling aan referenties elimineren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 7.13 | 18.1, 18.7 | Klant |

Implementeer Credential Scanner om referenties binnen code te identificeren. Credential Scanner stimuleert ook het verplaatsen van ontdekte referenties naar veiligere locaties zoals Azure Key Vault. 

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Volgende stappen

- Bekijk de volgende beveiligingscontrole: [Malware Defense](security-control-malware-defense.md)