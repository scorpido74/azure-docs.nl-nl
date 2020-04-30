---
title: Inleiding tot verificatie in Azure Automation
description: Dit artikel geeft een overzicht van Automation-beveiliging en de diverse verificatiemethoden die beschikbaar zijn voor Automation-accounts in Azure Automation.
keywords: automation-beveiliging, veilige automation; automation-verificatie
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 109bb6dd29ea9c4239e0abcfc668f1185f7e9783
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82114527"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Inleiding tot verificatie in Azure Automation

Met Azure Automation kunt u taken automatiseren voor bronnen in Azure, on-premises en bij andere cloudproviders zoals Amazon Web Services (AWS). Om een runbook in staat te stellen de vereiste acties uit te voeren, moet het machtigingen hebben om veilig toegang te krijgen tot de resources met de minimale rechten die vereist zijn binnen het abonnement.

Dit artikel heeft betrekking op de verschillende verificatie scenario's die worden ondersteund door Azure Automation en hoe u aan de slag kunt gaan op basis van de omgeving of omgevingen die u wilt beheren.  

## <a name="automation-account-overview"></a>Overzicht van Automation-account

Wanneer u Azure Automation voor het eerst start, moet u ten minste één Automation-account maken. Met Automation-accounts kunt u uw Automation-resources (runbooks, assets, configuraties) isoleren van de resources in andere Automation-accounts. U kunt Automation-accounts gebruiken om resources onder te verdelen in afzonderlijke logische omgevingen. U kunt bijvoorbeeld één account maken voor ontwikkeling, één voor productie, en één voor uw on-premises omgeving. Een Azure Automation-account verschilt van uw Microsoft-account of de accounts die zijn gemaakt in uw Azure-abonnement.

De Automation-resources voor elk Automation-account zijn gekoppeld aan één Azure-regio, maar Automation-accounts kunnen alle resources in uw abonnement beheren. De voornaamste reden om Automation-accounts in verschillende regio's te maken zou zijn als u beleid hebt waardoor gegevens en resources moeten worden geïsoleerd in een specifieke regio.

Alle taken die u uitvoert op resources met behulp van Azure Resource Manager en de Azure-cmdlets in Azure Automation moeten worden geverifieerd bij Azure met behulp van verificatie op basis van organisatie-identiteitreferenties van Azure Active Directory. Uitvoeren als-accounts in Azure Automation bieden verificatie voor het beheren van resources in azure met behulp van de Azure-cmdlets. Wanneer u een uitvoeren als-account maakt, wordt er een nieuwe Service-Principal-gebruiker in Azure Active Directory (AD) gemaakt en wordt de rol Inzender toegewezen aan deze gebruiker op het abonnements niveau. Voor runbooks die gebruikmaken van Hybrid Runbook Workers op Azure virtual machines, kunt u [beheerde identiteiten voor Azure-resources](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) gebruiken in plaats van run as-accounts om te verifiëren bij uw Azure-resources.

De service-principal voor een uitvoeren als-account heeft geen machtigingen om Azure AD standaard te lezen. Als u machtigingen wilt toevoegen om Azure AD te lezen of te beheren, moet u de machtigingen voor de Service-Principal verlenen onder **API-machtigingen**. Zie [machtigingen toevoegen voor toegang tot Web-api's](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)voor meer informatie.

Op rollen gebaseerd toegangsbeheer is beschikbaar in Azure Resource Manager voor het toekennen van toegestane acties aan een Azure AD-gebruikersaccount en Uitvoeren als-account, en om die service-principal te verifiëren. Lees het artikel [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie die u helpt bij het ontwikkelen van een model voor het beheren van machtigingen in Automation.  

Runbooks die worden uitgevoerd op een Hybrid Runbook Worker in uw Data Center of op computer services in andere Cloud omgevingen, zoals AWS, kunnen niet dezelfde methode gebruiken die doorgaans wordt gebruikt voor runbooks die worden geverifieerd bij Azure-resources. Dit is omdat deze resources buiten Azure worden uitgevoerd en er daarom voor deze resources eigen beveiligingsreferenties moeten worden gedefinieerd in Automation, zodat ze kunnen worden geverifieerd voor resources waartoe ze lokaal toegang hebben. Zie [Runbooks verifiëren voor Hybrid Runbook Workers](automation-hrw-run-runbooks.md)voor meer informatie over runbook-verificatie met runbook Workers. 

## <a name="next-steps"></a>Volgende stappen

* [Een Automation-account maken vanuit de Azure Portal](automation-create-standalone-account.md).

* [Maak een Automation-account met Azure Resource Manager sjabloon](automation-create-account-template.md).

* [Verifiëren met Amazon Web Services (AWS)](automation-config-aws-account.md).
