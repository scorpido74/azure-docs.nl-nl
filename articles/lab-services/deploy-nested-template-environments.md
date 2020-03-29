---
title: Geneste sjabloonomgevingen implementeren in Azure DevTest Labs
description: Meer informatie over het implementeren van geneste Azure Resource Manager-sjablonen om omgevingen te bieden met Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168825"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Geneste Azure Resource Manager-sjablonen implementeren voor testomgevingen
Met een geneste implementatie u andere Azure Resource Manager-sjablonen uitvoeren vanuit een hoofdsjabloon ResourceBeheer. Hiermee u uw implementatie ontleden in een reeks gerichte en doelgerichte sjablonen. Het biedt voordelen op het gebied van testen, hergebruik en leesbaarheid. Het artikel [Het gebruik van gekoppelde sjablonen bij het implementeren van Azure-resources](../azure-resource-manager/templates/linked-templates.md) biedt een goed overzicht van deze oplossing met verschillende codevoorbeelden. In dit artikel vindt u een voorbeeld dat specifiek is voor Azure DevTest Labs. 

## <a name="key-parameters"></a>Belangrijkste parameters
Hoewel u uw eigen Resource Manager-sjabloon helemaal opnieuw maken, raden we u aan het [Azure Resource Group-project](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) in Visual Studio te gebruiken, waardoor het eenvoudig is om sjablonen te ontwikkelen en te debuggen. Wanneer u een geneste implementatiebron toevoegt aan azuredeploy.json, voegt Visual Studio verschillende items toe om de sjabloon flexibeler te maken. Deze items omvatten de submap met de secundaire sjabloon en parameters bestand, variabele namen in de belangrijkste sjabloon bestand, en twee parameters voor de opslaglocatie voor de nieuwe bestanden. De **_artifactsLocation** en **_artifactsLocationSasToken** zijn de belangrijkste parameters die de DevTest Labs gebruikt. 

Zie [MultiVM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md)als u niet bekend bent met hoe de DevTest Labs met omgevingen werken. Uw sjablonen worden opgeslagen in de repository gekoppeld aan het lab in DevTest Labs. Wanneer u een nieuwe omgeving met deze sjablonen maakt, worden de bestanden verplaatst naar een Azure Storage-container in het lab. Om de geneste bestanden te kunnen identificeren en kopiëren, identificeert DevTest Labs de _artifactsLocation- en _artifactsLocationSasToken parameters en kopieert de submappen tot aan de opslagcontainer. Vervolgens worden de locatie en het SaS-token (Shared Access Signature) automatisch ingevoegd in parameters. 

## <a name="nested-deployment-example"></a>Voorbeeld van geneste implementatie
Hier is een eenvoudig voorbeeld van een geneste implementatie:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

De map in de opslagplaats met deze `nestedtemplates` sjabloon heeft een submap met de bestanden **NestOne.json** en **NestOne.parameters.json**. In de **azuredeploy.json**wordt URI voor de sjabloon gebouwd met behulp van de artefactenlocatie, geneste sjabloonmap, geneste sjabloonbestandsnaam. Op dezelfde manier wordt URI voor de parameters gebouwd met behulp van de artefactenlocatie, geneste sjabloonmap en parameterbestand voor de geneste sjabloon. 

Hier is het beeld van dezelfde projectstructuur in Visual Studio: 

![Projectstructuur in Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

U extra mappen toevoegen in de primaire map, maar niet dieper dan één niveau. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over omgevingen: 

- [Multi-VM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md)
- [Openbare omgevingen configureren en gebruiken in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Een omgeving verbinden met het virtuele netwerk van uw lab in Azure DevTest Labs](connect-environment-lab-virtual-network.md)