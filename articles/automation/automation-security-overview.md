---
title: Overzicht van verificatie van Azure Automation-account
description: Dit artikel bevat een overzicht van de verificatie van Azure Automation-account.
keywords: automation-beveiliging, veilige automation; automation-verificatie
services: automation
ms.subservice: process-automation
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: bcb5f61c93bd4c3ff7c0f81ae808807f7deb71df
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766096"
---
# <a name="automation-account-authentication-overview"></a>Overzicht van Automation-accountverificatie

Met Azure Automation kunt u taken automatiseren voor bronnen in Azure, on-premises en bij andere cloudproviders zoals Amazon Web Services (AWS). U kunt runbooks gebruiken om uw taken te automatiseren, of een Hybrid Runbook Worker als u zakelijke of operationele processen hebt voor het beheren van buiten Azure. Voor het werken in een van deze omgevingen zijn machtigingen vereist om veilig toegang te krijgen tot de resources met de mini maal vereiste rechten.

In dit artikel worden verificatie scenario's behandeld die door Azure Automation worden ondersteund en wordt uitgelegd hoe u aan de slag kunt gaan op basis van de omgeving of omgevingen die u wilt beheren.

## <a name="automation-account"></a>Automation-account

Wanneer u Azure Automation voor het eerst start, moet u ten minste één Automation-account maken. Met Automation-accounts kunt u uw Automation-resources, runbooks, assets, configuraties, van de resources van andere accounts isoleren. U kunt Automation-accounts gebruiken om resources onder te verdelen in afzonderlijke logische omgevingen. U kunt bijvoorbeeld één account maken voor ontwikkeling, één voor productie, en één voor uw on-premises omgeving. Een Azure Automation-account verschilt van uw Microsoft-account of de accounts die zijn gemaakt in uw Azure-abonnement. Zie [een Automation-account maken](automation-quickstart-create-account.md)voor een inleiding tot het maken van een Automation-account.

## <a name="automation-resources"></a>Automation-resources

De Automation-resources voor elk Automation-account zijn gekoppeld aan één Azure-regio, maar het account kan alle resources in uw Azure-abonnement beheren. De belangrijkste reden voor het maken van Automation-accounts in verschillende regio's is als u beleids regels hebt waarvoor gegevens en resources moeten worden geïsoleerd in een bepaalde regio.

Alle taken die u maakt met behulp van Azure Resource Manager en de Power shell-cmdlets in Azure Automation moeten worden geverifieerd bij Azure met behulp van Azure Active Directory (Azure AD)-verificatie op basis van de identiteit van de organisatie.

## <a name="run-as-accounts"></a>Uitvoeren als-accounts

Uitvoeren als-accounts in Azure Automation bieden verificatie voor het beheren van Azure Resource Manager resources of resources die zijn geïmplementeerd in het klassieke implementatie model. Er zijn twee typen uitvoeren als-accounts in Azure Automation:

* Azure Run As-account
* Klassiek uitvoeren als-account van Azure

Zie [Resource Manager en klassieke implementatie](../azure-resource-manager/management/deployment-models.md)voor meer informatie over deze twee implementatie modellen.

>[!NOTE]
>Azure Cloud Solution Provider-abonnementen (CSP) ondersteunen alleen het Azure Resource Manager model. Niet-Azure Resource Manager services zijn niet beschikbaar in het programma. Wanneer u een CSP-abonnement gebruikt, wordt het klassieke uitvoeren als-account van Azure niet gemaakt, maar wordt het uitvoeren als-account van Azure gemaakt. Zie [beschik bare Services in CSP-abonnementen](/azure/cloud-solution-provider/overview/azure-csp-available-services)voor meer informatie over CSP-abonnementen.

### <a name="run-as-account"></a>Run As-account

Met het uitvoeren als-account van Azure beheert u Azure-resources op basis van de Azure Resource Manager Deployment and Management-service voor Azure.

Wanneer u een uitvoeren als-account maakt, worden de volgende taken uitgevoerd:

* Hiermee maakt u een Azure AD-toepassing met een zelfondertekend certificaat, maakt u een Service-Principal-account voor de toepassing in azure AD en wijst u de rol [Inzender](../role-based-access-control/built-in-roles.md#contributor) toe voor het account in uw huidige abonnement. U kunt de certificaat instelling wijzigen in eigenaar of een andere rol. Zie [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie.

* Hiermee maakt u een Automation-certificaat Asset `AzureRunAsCertificate` met de naam in het opgegeven Automation-account. De certificaat Asset bevat de persoonlijke sleutel van het certificaat dat door de Azure AD-toepassing wordt gebruikt.

* Hiermee wordt een Automation-verbindings element gemaakt `AzureRunAsConnection` met de naam in het opgegeven Automation-account. Het verbindings element bevat de toepassings-ID, Tenant-ID, abonnements-ID en vinger afdruk van het certificaat.

### <a name="azure-classic-run-as-account"></a>Klassieke Azure Uitvoeren als-account

Het klassieke uitvoeren als-account van Azure beheert de klassieke Azure-resources op basis van het klassieke implementatie model. U moet een mede beheerder zijn van het abonnement om dit type run as-account te maken of te vernieuwen.

Wanneer u een klassiek uitvoeren als-account voor Azure maakt, worden de volgende taken uitgevoerd.

* Hiermee maakt u een beheer certificaat in het abonnement.

* Hiermee maakt u een Automation-certificaat Asset `AzureClassicRunAsCertificate` met de naam in het opgegeven Automation-account. Het certificaatasset bevat de persoonlijke sleutel van het certificaat die wordt gebruikt door het beheercertificaat.

* Hiermee wordt een Automation-verbindings element gemaakt `AzureClassicRunAsConnection` met de naam in het opgegeven Automation-account. Het verbindings element bevat de naam van het abonnement, de abonnements-ID en de naam van de certificaat Asset.

>[!NOTE]
>Het klassieke uitvoeren als-account van Azure wordt niet standaard op hetzelfde moment gemaakt als u een Automation-account maakt. Dit account wordt afzonderlijk gemaakt volgens de stappen die worden beschreven in het artikel [uitvoeren als-account beheren](manage-runas-account.md#create-a-run-as-account-in-azure-portal) .

## <a name="service-principal-for-run-as-account"></a>Service-Principal voor uitvoeren als-account

De service-principal voor een uitvoeren als-account heeft geen machtigingen om Azure AD standaard te lezen. Als u machtigingen wilt toevoegen om Azure AD te lezen of te beheren, moet u de machtigingen voor de Service-Principal verlenen onder **API-machtigingen**. Zie [machtigingen toevoegen voor toegang tot uw web-API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)voor meer informatie.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Toegangs beheer op basis van rollen is beschikbaar met Azure Resource Manager om toegestane acties te verlenen aan een Azure AD-gebruikers account en een uitvoeren als-account en de service-principal te verifiëren. Lees het artikel [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie die u helpt bij het ontwikkelen van een model voor het beheren van machtigingen in Automation.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook-verificatie met Hybrid Runbook Worker

Runbooks die worden uitgevoerd op een Hybrid Runbook Worker in uw Data Center of voor computer services in andere Cloud omgevingen, zoals AWS, kunnen niet dezelfde methode gebruiken die doorgaans wordt gebruikt voor runbooks die worden geverifieerd bij Azure-resources. Dit is omdat deze resources buiten Azure worden uitgevoerd en er daarom voor deze resources eigen beveiligingsreferenties moeten worden gedefinieerd in Automation, zodat ze kunnen worden geverifieerd voor resources waartoe ze lokaal toegang hebben. Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over runbook-verificatie met runbook-werk rollen.

Voor runbooks die Hybrid Runbook Workers op virtuele machines van Azure gebruiken, kunt u [Runbook-verificatie met beheerde identiteiten](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) gebruiken in plaats van run as-accounts om uw Azure-resources te verifiëren.

## <a name="next-steps"></a>Volgende stappen

* Zie [een zelfstandig Azure Automation account maken](automation-create-standalone-account.md)als u een Automation-account wilt maken op basis van de Azure Portal.
* Als u uw account liever met behulp van een sjabloon wilt maken, raadpleegt u [een Automation-account maken met behulp van een Azure Resource Manager sjabloon](quickstart-create-automation-account-template.md).
* Zie [Runbooks verifiëren met Amazon Web Services](automation-config-aws-account.md)voor verificatie met behulp van Amazon Web Services.