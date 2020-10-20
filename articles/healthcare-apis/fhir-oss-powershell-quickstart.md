---
title: 'PowerShell: FHIR-server voor Azure implementeren - Azure API for FHIR'
description: In deze quickstart wordt uitgelegd hoe u de Microsoft Open Source FHIR-server implementeert met PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: c6c1a7e21f0a1554c67c7f1860a2bd3382c941f5
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91817954"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Quickstart: Een Open Source FHIR-server implementeren met behulp van PowerShell

In deze quickstart leest u hoe u de Open Source Microsoft FHIR-server voor Azure implementeert met PowerShell.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Kies een naam voor de resourcegroep die de ingerichte resources bevat en maak deze:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>De FHIR-serversjabloon implementeren

De [GitHub-opslagplaats](https://github.com/Microsoft/fhir-server) van de Microsoft FHIR-server voor Azure bevat een sjabloon waarmee alle benodigde resources worden geïmplementeerd. Het implementatieproces duurt enkele minuten. Tijdens dit proces worden de vereiste Azure-resources gemaakt en geconfigureerd. Implementeer deze met:

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

> [!NOTE]
> Als u nog niet bent aangemeld, voert u eerst de volgende opdrachten uit.

```azurepowershell-interactive
Connect-AzAccount
get-azsubscription
Set-AzContext -SubscriptionId yoursubscriptionid
```

Als u een bestaande resourcegroep wilt gebruiken, wijzigt u $rg-waarden in de regel voor de $rg-variabeledefinitie en de Azure-opdrachtregel voor de ARM-sjabloonimplementatie, zoals wordt weergegeven in de code.

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = "MyExistingResourceGroupName"
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>Controleren of de FHIR-server draait

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

Het duurt ongeveer een minuut voordat de server de eerste keer reageert.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijder dan de resourcegroep met de volgende stappen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de Microsoft Open Source FHIR-server voor Azure geïmplementeerd in uw abonnement. Als u wilt weten hoe u de FHIR-API kunt openen met behulp van Postman, gaat u verder met de zelfstudie Postman.
 
>[!div class="nextstepaction"]
>[Toegang tot FHIR-API met Postman](access-fhir-postman-tutorial.md)
