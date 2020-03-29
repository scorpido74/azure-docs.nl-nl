---
title: Runtime azure SSIS-integratie implementeren met PowerShell
description: Met dit PowerShell-script wordt een runtime voor Azure-SSIS-integratie gemaakt waarmee SSIS-pakketten in de cloud kunnen worden uitgevoerd.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: sawinark
author: swinarko
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: c6d9a9299fa25013a440ee6ac45f5eae407225b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929809"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell-script - Runtime azure-SSIS-integratie implementeren

Met dit voorbeeld maakt PowerShell-script een runtime voor Azure-SSIS-integratie waarmee uw SSIS-pakketten in Azure kunnen worden uitgevoerd.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Nadat u het voorbeeldscript hebt uitgevoerd, u de volgende opdracht gebruiken om de resourcegroep en alle bijbehorende resources te verwijderen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Voer de volgende opdracht uit om het gegevensfabriek uit de resourcegroep te verwijderen: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Een gegevensfactory maken. |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Hiermee maakt u een runtime voor Azure-SSIS-integratie waarmee SSIS-pakketten in de cloud kunnen worden uitgevoerd |
| [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Hiermee wordt de runtime van Azure-SSIS-integratie gestart. |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Hier vindt u informatie over de runtime van Azure-SSIS-integratie. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Factory PowerShell-scriptvoorbeelden zijn te vinden in de [PowerShell-voorbeelden](../samples-powershell.md)van Azure Data Factory.
