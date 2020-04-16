---
title: Azure-beveiligingsbeheer - identiteits- en toegangscontrole
description: Identiteit en toegangscontrole azure-beveiliging
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 473fe838f53df0a874a2c793792533e01b7c069a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408537"
---
# <a name="security-control-identity-and-access-control"></a>Beveiligingscontrole: identiteits- en toegangscontrole

Aanbevelingen voor identiteits- en toegangsbeheer zijn gericht op het aanpakken van problemen in verband met identiteitsgebaseerde toegangscontrole, het blokkeren van beheerderstoegang, het waarschuwen voor identiteitsgerelateerde gebeurtenissen, abnormaal accountgedrag en op rollen gebaseerde toegangscontrole.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Inventaris bijhouden van de administratieve rekeningen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3.1 | 4.1 | Klant |

Azure AD heeft ingebouwde rollen die expliciet moeten worden toegewezen en opvraagbaar zijn. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

- [Een maprol in Azure AD met PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

## <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen indien van toepassing

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3,2 | 4.2 | Klant |

Azure AD heeft niet het concept van standaardwachtwoorden. Andere Azure-bronnen waarvoor een wachtwoord vereist is, dwingt een wachtwoord te maken met complexiteitsvereisten en een minimale wachtwoordlengte, die verschilt afhankelijk van de service. U bent verantwoordelijk voor toepassingen van derden en marktplaatsservices die standaardwachtwoorden kunnen gebruiken.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: Gebruik maken van speciale administratieve rekeningen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3,3 | 4.3 | Klant |

Maak standaard operationele procedures rond het gebruik van speciale administratieve accounts. Gebruik Azure Security Center Identity and Access Management om het aantal beheerdersaccounts te controleren.

U ook een Just-In-Time / Just-Enough-Access inschakelen met Azure AD Privileged Identity Management Privileged Roles voor Microsoft Services en Azure Resource Manager. 

- [Meer informatie over privileged identity management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Gebruik eenmalige aanmelding (SSO) met Azure Active Directory

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3.4 | 4.4 | Klant |

Gebruik waar mogelijk Azure Active Directory SSO in plaats van het configureren van afzonderlijke zelfstandige referenties per service. Gebruik aanbevelingen voor azure security center-identiteits- en toegangsbeheer.

- [SSO begrijpen met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Multi-factor authenticatie gebruiken voor alle Azure Active Directory-gebaseerde toegang

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3,5 | 4.5, 11.5, 12.11, 16.3 | Klant |

Azure AD MFA inschakelen en aanbevelingen voor Azure Security Center Identity and Access Management volgen.

- [MFA inschakelen in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Identiteit en toegang controleren binnen Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedicated machines (Privileged Access Workstations) gebruiken voor alle administratieve taken

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Klant |

Gebruik PAWs (geprivilegieerde toegangswerkstations) met MFA geconfigureerd om in te loggen en Azure-bronnen te configureren.

- [Meer informatie over geprivilegieerde werkstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [MFA inschakelen in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwing en waarschuwing over verdachte activiteiten van administratieve accounts

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3.7 | 4.8, 4.9 | Klant |

Gebruik Azure Active Directory-beveiligingsrapporten voor het genereren van logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving plaatsvinden. Gebruik Azure Security Center om identiteits- en toegangsactiviteiten te controleren.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [De identiteit en toegangsactiviteit van gebruikers controleren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources beheren vanaf alleen goedgekeurde locaties

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3.8 | 11.7 | Klant |

Gebruik locaties met voorwaardelijke toegang om alleen specifieke logische groeperingen van IP-adresbereiken of landen/regio's toe te staan.

- [Benoemde locaties configureren in Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

## <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory gebruiken

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Klant |

Gebruik Azure Active Directory (AAD) als het centrale verificatie- en autorisatiesysteem. AAD beschermt gegevens door sterke versleuteling te gebruiken voor gegevens in rust en onderweg. AAD zouten, hashes en slaat ook veilig gebruikersreferenties op.

- [Een AAD-exemplaar maken en configureren](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3.10 | 16.9, 16.10 | Klant |

Azure AD biedt logboeken om verouderde accounts te ontdekken. Gebruik daarnaast Azure Identity Access Reviews om groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden beoordeeld om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

- [Informatie over Azure AD-rapportage](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Azure Identity Access-recensies gebruiken](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Controle op pogingen om toegang te krijgen tot gedeactiveerde referenties

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3.11 | 16.12 | Klant |

U hebt toegang tot logboeklogboeken voor Azure AD-aanmeldingsactiviteit, audit en risicogebeurtenis, waarmee u integreren met een SIEM/Monitoring-hulpprogramma.

U dit proces stroomlijnen door diagnostische instellingen voor Azure Active Directory-gebruikersaccounts te maken en de controlelogboeken en aanmeldingslogboeken naar een Logboekanalysewerkruimte te verzenden. U de gewenste waarschuwingen configureren binnen Log Analytics Workspace.

- [Azure-activiteitslogboeken integreren in Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing over afwijking van het inloggedrag van het account

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3.12 | 16.13 | Klant |

Gebruik azure AD-functies voor risico- en identiteitsbeveiliging om geautomatiseerde antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. U ook gegevens opnemen in Azure Sentinel voor verder onderzoek.

- [Azure AD-riskante aanmeldingen weergeven](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Risicobeleid voor identiteitsbescherming configureren en inschakelen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Azure Sentinel aan boord](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 3.13 | 16 | Klant |

In ondersteuningsscenario's waarin Microsoft toegang nodig heeft tot klantgegevens, biedt Customer Lockbox een interface voor u om verzoeken om toegang tot klantgegevens te controleren en goed te keuren of af te wijzen.

- [Inzicht in klantlockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)


## <a name="next-steps"></a>Volgende stappen

- Zie de volgende beveiligingscontrole: [gegevensbescherming](security-control-data-protection.md)