---
title: API's en -hulpprogramma's voor ontwikkelaars - Azure Batch | Microsoft Docs
description: Meer informatie over de API's en hulpprogramma's voor het ontwikkelen van oplossingen met de Azure Batch-service.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 00d2a74946957f690979eec1d3a03a9b766299d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252322"
---
# <a name="overview-of-batch-apis-and-tools"></a>Overzicht van Batch-API's en -hulpprogramma's

Het verwerken van parallelle workloads met Azure Batch gebeurt gewoonlijk via een programma met behulp van een van de Batch-API's. Uw clienttoepassing of -service kan de Batch-API's gebruiken om met de Batch-service te communiceren. Met de Batch-API's kunt u pools van rekenknooppunten maken en beheren, ofwel virtuele machines ofwel cloudservices. Vervolgens kunt u jobs en taken plannen voor uitvoering op deze knooppunten. 

U kunt voor uw organisatie efficiënt grootschalige workloads verwerken of uw klanten een front-endservice aanbieden, zodat zij (op aanvraag of gepland) jobs en taken kunnen uitvoeren op één knooppunt of honderden of zelfs duizenden knooppunten. U kunt Azure Batch ook gebruiken als onderdeel van een grotere werkstroom, beheerd door hulpprogramma's zoals [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Wanneer u gereed bent om met de Batch-API aan de slag te gaan en u te verdiepen in de mogelijkheden ervan, leest u het [Overzicht van de Batch-functies voor ontwikkelaars](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-for-batch-development"></a>Azure-accounts voor Batch-ontwikkeling
Wanneer u Batch-oplossingen ontwikkelt, gebruikt u de volgende accounts in uw Azure-abonnement:

* **Batch-account** - Azure Batch-bronnen, waaronder pools, rekenknooppunten, taken en taken, zijn gekoppeld aan een Azure [Batch-account](batch-api-basics.md#account). Als uw toepassing een aanvraag indient voor de Batch-service, verifieert deze de aanvraag met de Azure Batch-accountnaam, de URL van het account en een toegangssleutel of een Azure Active Directory-token. U kunt [een Batch-account maken](batch-account-create-portal.md) in Azure Portal of via een programma.
* **Storage-account**: Batch bevat ingebouwde ondersteuning voor het werken met bestanden in [Azure Storage][azure_storage]. Vrijwel elk Batch-scenario gebruikt Azure Blob-opslag voor het faseren van de programma's die door de taken worden uitgevoerd en de gegevens die ze verwerken, en voor de opslag van uitvoergegevens die ze genereren. Zie voor opslagaccountopties in Batch het [Overzicht van Batch-functies](batch-api-basics.md#azure-storage-account).

## <a name="batch-service-apis"></a>API’s voor Batch-service

Uw toepassingen en services kunnen direct REST-API-aanroepen verstrekken of een of meer van de volgende clientbibliotheken gebruiken voor het uitvoeren en beheren van uw Azure Batch-workloads.

| API | API-verwijzing | Download | Zelfstudie | Codevoorbeelden | Meer informatie |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[docs.microsoft.com][batch_rest] |N.v.t. |- |- | [Ondersteunde versies](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet (NuGet)][api_net_nuget] |[Zelfstudie](tutorial-parallel-dotnet.md) |[GitHub GitHub][api_sample_net] | [Releaseopmerkingen](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[Zelfstudie](tutorial-parallel-python.md)|[GitHub GitHub][api_sample_python] | [Leesmij](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[Zelfstudie](batch-nodejs-get-started.md) |- | [Leesmij](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[Leesmij][api_sample_java] | [Leesmij](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>API’s voor Batch Management

De Azure Resource Manager-API's voor Batch bieden programmatisch toegang tot Batch-accounts. Met deze API's kunt u Batch-accounts, -quota, -toepassingspakketten en andere -resources programmatisch beheren via de Microsoft.Batch-provider.  

| API | API-verwijzing | Download | Zelfstudie | Codevoorbeelden |
| --- | --- | --- | --- | --- |
| **Batch Management REST** |[docs.microsoft.com][api_rest_mgmt] |N.v.t. |- |[GitHub GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet (NuGet)][api_net_mgmt_nuget] | [Zelfstudie](batch-management-dotnet.md) |[GitHub GitHub][api_sample_net] |
| **Batch Management Python** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Batch Management Node.js** |[docs.microsoft.com][api_nodejs_mgmt] |[npm][api_nodejs_mgmt_npm] |- |- | 
| **Batch Management Java** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>Batch-opdrachtregelprogramma's

Deze opdrachtregelprogramma's bieden dezelfde functionaliteit als de API’s voor de Batch-service en Batch Management: 

* [PowerShell-cmdlets voor Batch][batch_ps]: met de Azure Batch-cmdlets in de [Azure PowerShell](/powershell/azure/overview)-module kunt u Batch-resources beheren met PowerShell.
* [Azure CLI](/cli/azure): de Azure CLI is een platformoverschrijdende hulpmiddelenset die shellopdrachten biedt voor interactie met vele Azure-services, waaronder de Batch-service en Batch Management-service. Zie [Batch-resources beheren met de Azure CLI](batch-cli-get-started.md) voor meer informatie over het gebruik van de Azure CLI met Batch.

## <a name="other-tools-for-application-development"></a>Andere hulpmiddelen voor toepassingsontwikkeling

Hier volgen enkele extra hulpprogramma's die mogelijk nuttig zijn voor het bouwen van uw Batch-toepassingen en -services en het opsporen van fouten daarin:

* [Azure Portal][portal]: u kunt Batch-pools, -taken en -opdrachten in Azure Portal maken, controleren en verwijderen. U kunt de statusinformatie voor deze en andere resources bekijken terwijl u taken uitvoert en zelfs taken downloadt van de rekenknooppunten in uw pools. U kunt bijvoorbeeld de `stderr.txt` van een taak downloaden bij het oplossen van problemen. U kunt ook Remote Desktop (RDP)-bestanden downloaden die u kunt gebruiken om aan te melden om knooppunten te berekenen.
* [Azure Batch Explorer][batch_labs]: Batch Explorer (vroeger BatchLabs genaamd) is een gratis, uitgebreid, zelfstandig clienthulpprogramma voor het maken en bewaken van en opsporen van fouten in Azure Batch-toepassingen. Download een [installatiepakket](https://azure.github.io/BatchExplorer/) voor Mac, Linux of Windows.
* [Azure Batch Shipyard](https://github.com/Azure/batch-shipyard): Batch Shipyard is een hulpmiddel voor het inrichten, uitvoeren en bewaken van batchverwerking op basis van containers en HPC-workloads op Azure Batch.
* [Azure Storage Explorer:][storage_explorer]Hoewel het niet strikt een Azure Batch-tool is, is de Storage Explorer een ander waardevol hulpmiddel om te hebben terwijl u uw Batch-oplossingen ontwikkelt en debugadt.

## <a name="additional-resources"></a>Aanvullende bronnen

- Zie [Logboekgebeurtenissen voor diagnostische evaluatie en bewaking van de Batch-oplossingen](batch-diagnostics.md) voor meer informatie over het vastleggen van gebeurtenissen van uw Batch-toepassing. Zie [Batch-analyses](batch-analytics.md) voor informatie over gebeurtenissen die worden gegenereerd door de Batch-service.
- Zie [Omgevingsvariabelen van Azure Batch-rekenknooppunten](batch-compute-node-environment-variables.md) voor meer informatie over omgevingsvariabelen voor rekenknooppunten.

## <a name="next-steps"></a>Volgende stappen

* Bekijk het [overzicht met Batch-functies voor ontwikkelaars](batch-api-basics.md), essentiële informatie voor iedereen die Batch wil gaan gebruiken. Het artikel bevat meer gedetailleerde informatie over de Batch-serviceresources zoals groepen, knooppunten, jobs en taken, en de vele API-functies die u tijdens het maken van de Batch-toepassing kunt gebruiken.
* Lees [Aan de slag met de Azure Batch-bibliotheek voor .NET](tutorial-parallel-dotnet.md) voor informatie over het gebruik van C# en de Batch .NET-bibliotheek om een eenvoudige workload uit te voeren met een gebruikelijke Batch-werkstroom. Een [Python-versie](tutorial-parallel-python.md) en een [Node.js-zelfstudie ](batch-nodejs-get-started.md) zijn ook beschikbaar.
* Download de [codevoorbeelden op GitHub][github_samples] om te zien hoe C# en Python kunnen samenwerken met Batch om voorbeeldworkloads te plannen en te verwerken.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/az.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchExplorer/
[storage_explorer]: https://storageexplorer.com/
[portal]: https://portal.azure.com
