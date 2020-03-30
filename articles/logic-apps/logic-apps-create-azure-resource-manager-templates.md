---
title: Logische app-sjablonen maken voor implementatie
description: Meer informatie over het maken van Azure Resource Manager-sjablonen voor het automatiseren van implementatie in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972637"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Azure Resource Manager-sjablonen maken om de implementatie voor Azure Logic Apps te automatiseren

In dit artikel wordt beschreven hoe u een [Azure Resource Manager-sjabloon](../azure-resource-manager/management/overview.md) voor uw logische app maken om het maken en implementeren van uw logische app te automatiseren. Zie [Overzicht: Implementatie automatiseren voor logische apps met Azure Resource Manager-sjablonen](logic-apps-azure-resource-manager-templates-overview.md)voor een overzicht voor een sjabloon met de definitie van uw werkstroom en andere resources die nodig zijn voor implementatie.

Azure Logic Apps biedt een [vooraf gebouwde sjabloon voor Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) van de logische app die u opnieuw gebruiken, niet alleen voor het maken van logische apps, maar ook om de resources en parameters te definiëren die u moet gebruiken voor implementatie. U deze sjabloon gebruiken voor uw eigen bedrijfsscenario's of de sjabloon aanpassen om aan uw vereisten te voldoen.

> [!IMPORTANT]
> Zorg ervoor dat verbindingen in uw sjabloon dezelfde Azure-brongroep en -locatie gebruiken als uw logische app.

Zie de volgende onderwerpen voor meer informatie over Azure Resource Manager-sjablonen:

* [Sjabloonstructuur en syntaxis van Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager-sjablonen maken](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager-sjablonen voor consistentie van de cloud ontwikkelen](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Sjablonen maken met Visual Studio

Gebruik Visual Studio (gratis community-editie of meer) en de Azure Logic Apps Tools voor Visual Studio voor de eenvoudigste manier om geldige logische app-sjablonen te maken die meestal klaar zijn voor implementatie. U vervolgens [uw logische app maken in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) of een bestaande logische app van de [Azure-portal zoeken en downloaden naar Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md)

Als u uw logica-app downloadt, krijgt u een sjabloon met de definities voor uw logische app en andere bronnen, zoals verbindingen. De sjabloon *parameteriseert*ook parameters of definieert parameters voor de waarden die worden gebruikt voor het implementeren van uw logische app en andere bronnen. U de waarden voor deze parameters opgeven in een afzonderlijk parametersbestand. Op die manier u deze waarden gemakkelijker wijzigen op basis van uw implementatiebehoeften. Zie deze onderwerpen voor meer informatie:

* [Logische apps maken met Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Logische apps beheren met Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Sjablonen maken met Azure PowerShell

U Resource Manager-sjablonen maken met Azure PowerShell met de [LogicAppTemplate-module](https://github.com/jeffhollan/LogicAppTemplateCreator). Deze open-source module evalueert eerst uw logische app en alle verbindingen die de logische app gebruikt. De module genereert vervolgens sjabloonbronnen met de nodige parameters voor implementatie.

Stel dat u een logische app hebt die een bericht ontvangt vanuit een Azure Service Bus-wachtrij en gegevens uploadt naar een Azure SQL-database. De module behoudt alle orchestration-logica en parameteriseert de SQL- en Service Bus-verbindingstekenreeksen, zodat u deze waarden opgeven en wijzigen op basis van uw implementatiebehoeften.

In deze voorbeelden wordt uitgelegd hoe u logische apps maakt en implementeert met Azure Resource Manager-sjablonen, Azure Pipelines in Azure DevOps en Azure PowerShell:

* [Voorbeeld: Verbinding maken met Azure Service Bus-wachtrijen vanuit Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voorbeeld: Verbinding maken met Azure Storage-accounts vanuit Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voorbeeld: een functie-app-actie instellen voor Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voorbeeld: Verbinding maken met een integratieaccount van Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>PowerShell-modules installeren

1. Als u dit nog niet hebt gedaan, installeert u [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

1. Voer de opdracht uit voor de eenvoudigste manier om de LogicAppTemplate-module in de [PowerShell-galerie](https://www.powershellgallery.com/packages/LogicAppTemplate)te installeren:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   Voer de opdracht uit om bij te werken naar de nieuwste versie:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

Of als u handmatig wilt installeren, volgt u de stappen in GitHub voor [Logic App Template Creator.](https://github.com/jeffhollan/LogicAppTemplateCreator)

### <a name="install-azure-resource-manager-client"></a>Azure Resource Manager-client installeren

Als u de LogicAppTemplate-module wilt gebruiken voor een Azure-tenant en een token voor abonnementstoegang, installeert u het [azure resource manager-clienthulpprogramma](https://github.com/projectkudu/ARMClient), een eenvoudig opdrachtregelhulpprogramma dat de Azure Resource Manager API aanroept.

Wanneer u `Get-LogicAppTemplate` de opdracht met dit gereedschap uitvoert, krijgt de opdracht eerst een toegangstoken via het ARMClient-gereedschap, wordt het token naar het PowerShell-script geleid en wordt de sjabloon als JSON-bestand gemaakt. Zie dit [artikel over het azure resource manager-clienthulpprogramma](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)voor meer informatie over het hulpprogramma voor azure resourcebeheer .

### <a name="generate-template-with-powershell"></a>Sjabloon genereren met PowerShell

Voer de opdracht PowerShell uit om uw sjabloon te genereren na het installeren van de LogicAppTemplate-module en [Azure CLI:](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Als u de aanbeveling voor piping in een token wilt volgen `$SubscriptionId` vanuit het Azure Resource [Manager-clienthulpprogramma,](https://github.com/projectkudu/ARMClient)voert u deze opdracht in plaats daarvan uit waar uw Azure-abonnements-id is:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Na extractie u vervolgens een parametersbestand van uw sjabloon maken door deze opdracht uit te voeren:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Voer de opdracht uit voor extractie met Azure Key Vault-verwijzingen (alleen statisch)

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parameters | Vereist | Beschrijving |
|------------|----------|-------------|
| Sjabloonbestand | Ja | Het bestandspad naar uw sjabloonbestand |
| KeyVault | Nee | Een enum dat beschrijft hoe om te gaan met mogelijke sleutelkluiswaarden. De standaardwaarde is `None`. |
||||

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Sjablonen voor logische app gebruiken](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
