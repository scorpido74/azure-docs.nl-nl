---
title: Een Azure Stream Analytics-taak Azure Resource Manager-sjabloon exporteren
description: In dit artikel wordt beschreven hoe u een Azure Resource Manager-sjabloon exporteert voor uw Azure Stream Analytics-taak.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968953"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Een Azure Stream Analytics-taak Azure Resource Manager-sjabloon exporteren

[Met Azure Resource Manager-sjablonen](../azure-resource-manager/templates/overview.md) u infrastructuur implementeren als code. De sjabloon is een JSON-bestand (JavaScript Object Notation) dat de infrastructuur en configuratie voor uw resources definieert. U geeft de resources op die moeten worden geïmplementeerd en de eigenschappen voor deze resources.

U een Azure Stream Analytics-taak opnieuw implementeren door de sjabloon Azure Resource Manager te exporteren.

## <a name="open-a-job-in-vs-code"></a>Een taak openen in VS-code

Voordat u een sjabloon exporteren, moet u eerst een bestaande Stream Analytics-taak openen in Visual Studio Code. 

Als u een taak wilt exporteren naar een lokaal project, zoekt u de taak die u wilt exporteren in de **Stream Analytics Explorer** in de Azure-portal. Selecteer Openen in **Visual Studio**op de pagina **Query** . Selecteer vervolgens **Visual Studio Code**.

![Vacature Voor Stream Analytics openen in Visual Studio Code](./media/resource-manager-export/open-job-vs-code.png)

Zie de Visual Studio Code snel aan de [slag](quick-create-vs-code.md)voor meer informatie over het gebruik van Visual Studio Code voor het beheren van Stream Analytics-taken.

## <a name="compile-the-script"></a>Het script compileren 

De volgende stap is het compileren van het taakscript naar een Azure Resource Manager-sjabloon. Voordat u het script compileert, moet u ervoor zorgen dat uw taak ten minste één invoer en één uitvoer heeft geconfigureerd. Als er geen invoer of uitvoer is geconfigureerd, moet u eerst de invoer en uitvoer configureren.

1. Navigeer in Visual Studio Code naar het *asaql-bestand van* uw taak.

   ![Transformation.asaql-bestand in Visual Studio Code](./media/resource-manager-export/transformation-asaql.png)

1. Klik met de rechtermuisknop op het bestand *Transformation.asaql* en selecteer **ASA: Script compileren** in het menu.

1. Merk op dat een **map Implementeren** wordt weergegeven in de werkwerkruimte Stream Analytics.

1. Ga naar het *bestand JobTemplate.json,* de azure resourcebeheersjabloon die wordt gebruikt om te implementeren.

## <a name="complete-the-parameters-file"></a>Het parametersbestand voltooien

Vul vervolgens het sjabloonbestand Azure Resource Management in.

1. Open het bestand *JobTemplate.parameters.json* in de map **Implementeren** van uw werkwerkruimte Stream Analytics in Visual Studio Code.

1. Merk op dat de invoer- en uitvoersleutels nietig zijn. Vervang de null-waarden door de werkelijke toegangssleutels voor uw invoer- en uitvoerbronnen.

1. Sla het parametersbestand op.

## <a name="deploy-using-templates"></a>Implementeren met behulp van sjablonen

U bent klaar om uw Azure Stream Analytics-taak te implementeren met behulp van de Azure Resource Manager-sjablonen die u in de vorige sectie hebt gegenereerd.

Voer in een PowerShell-venster de volgende opdracht uit. Zorg ervoor dat u de bestanden *ResourceGroupName,* *TemplateFile*en *TemplateParameterFile* reaplcemet de naam van uw werkelijke resourcegroep en de volledige bestandspaden naar de bestanden *JobTemplate.json* en *JobTemplate.parameters.json* in de **map Implementeren** van uw werkwerkruimte.

Als Azure PowerShell niet is geconfigureerd, voert u de stappen uit in [azure PowerShell-module installeren.](https://docs.microsoft.com/powershell/azure/install-Az-ps)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics-taken lokaal testen met live-invoer met Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Azure Stream Analytics-taken verkennen met Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)