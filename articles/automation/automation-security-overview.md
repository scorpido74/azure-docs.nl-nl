---
title: Overzicht van verificatie van Azure Automation-account
description: Dit artikel bevat een overzicht van de verificatie van Azure Automation-account.
keywords: automation-beveiliging, veilige automation; automation-verificatie
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: f78161590c897e6337e8694acb9f8a6b34258246
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743805"
---
# <a name="automation-account-authentication-overview"></a>Overzicht van Automation-accountverificatie

Met Azure Automation kunt u taken automatiseren voor bronnen in Azure, on-premises en bij andere cloudproviders zoals Amazon Web Services (AWS). U kunt runbooks gebruiken om uw taken te automatiseren, of een Hybrid Runbook Worker als u niet-Azure-taken wilt beheren. Beide omgevingen hebben machtigingen nodig om veilig toegang te krijgen tot de resources met de minimale rechten die zijn vereist binnen het Azure-abonnement.

In dit artikel worden verificatie scenario's behandeld die door Azure Automation worden ondersteund en wordt uitgelegd hoe u aan de slag kunt gaan op basis van de omgeving of omgevingen die u wilt beheren.

## <a name="automation-account"></a>Automation-account 

Wanneer u Azure Automation voor het eerst start, moet u ten minste één Automation-account maken. Met Automation-accounts kunt u uw Automation-resources, runbooks, assets, configuraties, van de resources van andere accounts isoleren. U kunt Automation-accounts gebruiken om resources onder te verdelen in afzonderlijke logische omgevingen. U kunt bijvoorbeeld één account maken voor ontwikkeling, één voor productie, en één voor uw on-premises omgeving. Een Azure Automation-account verschilt van uw Microsoft-account of de accounts die zijn gemaakt in uw Azure-abonnement. Zie [een Automation-account maken](automation-quickstart-create-account.md)voor een inleiding tot het maken van een Automation-account.

## <a name="automation-resources"></a>Automation-resources

De Automation-resources voor elk Automation-account zijn gekoppeld aan één Azure-regio, maar het account kan alle resources in uw Azure-abonnement beheren. De belangrijkste reden voor het maken van Automation-accounts in verschillende regio's is als u beleids regels hebt waarvoor gegevens en resources moeten worden geïsoleerd in een bepaalde regio.

Alle taken die u maakt met behulp van Azure Resource Manager en de Power shell-cmdlets in Azure Automation moeten worden geverifieerd bij Azure met behulp van Azure Active Directory (Azure AD)-verificatie op basis van de identiteit van de organisatie. 

## <a name="run-as-account"></a>Run As-account

Uitvoeren als-accounts in Azure Automation bieden verificatie voor het beheer van Azure-resources met behulp van Power shell-cmdlets. Wanneer u een uitvoeren als-account maakt, wordt er een nieuwe Service-Principal-gebruiker gemaakt in azure AD en wordt de rol Inzender toegewezen aan deze gebruiker op het abonnements niveau. Voor runbooks die Hybrid Runbook Workers op virtuele machines van Azure gebruiken, kunt u [Runbook-verificatie met beheerde identiteiten](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) gebruiken in plaats van run as-accounts om uw Azure-resources te verifiëren.

## <a name="service-principal-for-run-as-account"></a>Service-Principal voor uitvoeren als-account

De service-principal voor een uitvoeren als-account heeft geen machtigingen om Azure AD standaard te lezen. Als u machtigingen wilt toevoegen om Azure AD te lezen of te beheren, moet u de machtigingen voor de Service-Principal verlenen onder **API-machtigingen**. Zie [machtigingen toevoegen voor toegang tot Web-api's](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)voor meer informatie.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Toegangs beheer op basis van rollen is beschikbaar met Azure Resource Manager om toegestane acties te verlenen aan een Azure AD-gebruikers account en een uitvoeren als-account en de service-principal te verifiëren. Lees het artikel [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie die u helpt bij het ontwikkelen van een model voor het beheren van machtigingen in Automation.  

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook-verificatie met Hybrid Runbook Worker 

Runbooks die worden uitgevoerd op een Hybrid Runbook Worker in uw Data Center of voor computer services in andere Cloud omgevingen, zoals AWS, kunnen niet dezelfde methode gebruiken die doorgaans wordt gebruikt voor runbooks die worden geverifieerd bij Azure-resources. Dit is omdat deze resources buiten Azure worden uitgevoerd en er daarom voor deze resources eigen beveiligingsreferenties moeten worden gedefinieerd in Automation, zodat ze kunnen worden geverifieerd voor resources waartoe ze lokaal toegang hebben. Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over runbook-verificatie met runbook-werk rollen. 

## <a name="next-steps"></a>Volgende stappen

* [Een Automation-account maken vanuit Azure Portal](automation-create-standalone-account.md)
* [Een Automation-account maken met Azure Resource Manager sjabloon](automation-create-account-template.md)
* [Verifiëren met Amazon Web Services (AWS)](automation-config-aws-account.md)