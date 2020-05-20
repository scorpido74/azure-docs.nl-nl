---
title: Overzicht van verificatie van Azure Automation-account
description: Dit artikel bevat een overzicht van Azure Automation beveiligings-en verificatie methoden voor Automation-accounts.
keywords: automation-beveiliging, veilige automation; automation-verificatie
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: c9e3c2b15b45eb15b40782a5d0ecfe1e736fc013
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683519"
---
# <a name="azure-automation-account-authentication-overview"></a>Overzicht van verificatie van Azure Automation-account

Met Azure Automation kunt u taken automatiseren voor bronnen in Azure, on-premises en bij andere cloudproviders zoals Amazon Web Services (AWS). Voor een runbook om de vereiste acties uit te voeren, moet het een machtiging hebben om veilig toegang te krijgen tot de resources met de mini maal vereiste rechten binnen het abonnement.

Wanneer u Azure Automation voor het eerst start, moet u ten minste één Automation-account maken. Met Automation-accounts kunt u uw Automation-resources (runbooks, assets, configuraties) isoleren van de resources in andere Automation-accounts. U kunt Automation-accounts gebruiken om resources onder te verdelen in afzonderlijke logische omgevingen. U kunt bijvoorbeeld één account maken voor ontwikkeling, één voor productie, en één voor uw on-premises omgeving. Een Azure Automation-account verschilt van uw Microsoft-account of de accounts die zijn gemaakt in uw Azure-abonnement.

De Automation-resources voor elk Automation-account zijn gekoppeld aan één Azure-regio, maar Automation-accounts kunnen alle resources in uw abonnement beheren. De voornaamste reden om Automation-accounts in verschillende regio's te maken zou zijn als u beleid hebt waardoor gegevens en resources moeten worden geïsoleerd in een specifieke regio.

Alle taken die u uitvoert met behulp van Azure Resource Manager en de Azure-cmdlets in Azure Automation, moeten worden geverifieerd bij Azure met behulp van Azure Active Directory (Azure AD)-verificatie op basis van de identiteits referentie. Uitvoeren als-accounts in Azure Automation bieden verificatie voor het beheren van resources in azure met behulp van de Azure-cmdlets. Wanneer u een uitvoeren als-account maakt, wordt er een nieuwe Service-Principal-gebruiker gemaakt in azure AD en wordt de rol Inzender toegewezen aan deze gebruiker op het abonnements niveau. Voor runbooks die gebruikmaken van Hybrid Runbook Workers op Azure virtual machines, kunt u met behulp van [Runbook-verificatie met beheerde identiteiten](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) in plaats van uitvoeren als-accounts te verifiëren bij uw Azure-resources.

De service-principal voor een uitvoeren als-account heeft geen machtigingen om Azure AD standaard te lezen. Als u machtigingen wilt toevoegen om Azure AD te lezen of te beheren, moet u de machtigingen voor de Service-Principal verlenen onder **API-machtigingen**. Zie [machtigingen toevoegen voor toegang tot Web-api's](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)voor meer informatie.

Op rollen gebaseerd toegangsbeheer is beschikbaar in Azure Resource Manager voor het toekennen van toegestane acties aan een Azure AD-gebruikersaccount en Uitvoeren als-account, en om die service-principal te verifiëren. Lees het artikel [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie die u helpt bij het ontwikkelen van een model voor het beheren van machtigingen in Automation.  

Runbooks die worden uitgevoerd op een Hybrid Runbook Worker in uw Data Center of op computer services in andere Cloud omgevingen, zoals AWS, kunnen niet dezelfde methode gebruiken die doorgaans wordt gebruikt voor runbooks die worden geverifieerd bij Azure-resources. Dit is omdat deze resources buiten Azure worden uitgevoerd en er daarom voor deze resources eigen beveiligingsreferenties moeten worden gedefinieerd in Automation, zodat ze kunnen worden geverifieerd voor resources waartoe ze lokaal toegang hebben. Zie [Runbooks verifiëren voor Hybrid Runbook Workers](automation-hrw-run-runbooks.md)voor meer informatie over runbook-verificatie met runbook Workers. 

## <a name="next-steps"></a>Volgende stappen

* [Een Automation-account maken vanuit de Azure Portal](automation-create-standalone-account.md).
* [Maak een Automation-account met Azure Resource Manager sjabloon](automation-create-account-template.md).
* [Verifiëren met Amazon Web Services (AWS)](automation-config-aws-account.md).
