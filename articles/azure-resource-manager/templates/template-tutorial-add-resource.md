---
title: Zelfstudie - Bron toevoegen aan sjabloon
description: Beschrijft de stappen om uw eerste Azure Resource Manager-sjabloon te maken. U leert over de syntaxis van het sjabloonbestand en hoe u een opslagaccount implementeert.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: dcdbbb325e6589669abe6cf3d25ac5191e29118b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411737"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Zelfstudie: Een resource toevoegen aan uw ARM-sjabloon

In de [vorige zelfstudie](template-tutorial-create-first-template.md)hebt u geleerd hoe u een lege sjabloon maken en deze implementeren. Nu bent u klaar om een echte bron te implementeren. In deze zelfstudie voegt u een opslagaccount toe. Het duurt ongeveer **9 minuten** om deze tutorial te voltooien.

## <a name="prerequisites"></a>Vereisten

We raden u aan de [inleidende zelfstudie over sjablonen in te](template-tutorial-create-first-template.md)vullen, maar dit is niet vereist.

U moet beschikken over Visual Studio Code met de extensie Hulpmiddelen voor ResourceBeheer en Azure PowerShell of Azure CLI. Zie [sjabloongereedschappen voor](template-tutorial-create-first-template.md#get-tools)meer informatie .

## <a name="add-resource"></a>Bron toevoegen

Als u een definitie van een opslagaccount wilt toevoegen aan de bestaande sjabloon, bekijkt u de gemarkeerde JSON in het volgende voorbeeld. In plaats van te proberen delen van de sjabloon te kopiëren, kopieert u het hele bestand en vervangt u de sjabloon door de inhoud ervan.

Vervang **{provide-unique-name}** (inclusief de krullende haakjes) door een unieke naam van het opslagaccount.

> [!IMPORTANT]
> De naam van het opslagaccount moet uniek zijn in Azure. De naam mag alleen kleine letters of cijfers hebben. Het kan niet langer zijn dan 24 tekens. U een naamgevingspatroon proberen, zoals het gebruik van **store1** als voorvoegsel en vervolgens uw initialen en de datum van vandaag toevoegen. De naam die u gebruikt, kan er bijvoorbeeld uitzien als **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Het raden van een unieke naam voor een opslagaccount is niet eenvoudig en werkt niet goed voor het automatiseren van grote implementaties. Later in deze zelfstudiereeks gebruikt u sjabloonfuncties die het gemakkelijker maken om een unieke naam te maken.

## <a name="resource-properties"></a>Resourceeigenschappen

U vraagt zich misschien af hoe u de eigenschappen vinden die u voor elk resourcetype gebruiken. U de [arm-sjabloonverwijzing](/azure/templates/) gebruiken om de resourcetypen te vinden die u wilt implementeren.

Elke resource die u implementeert, heeft ten minste de volgende drie eigenschappen:

- **type**: Type van de resource. Deze waarde is een combinatie van de naamruimte van de resourceprovider en het resourcetype (zoals Microsoft.Storage/storageAccounts).
- **apiVersion:** Versie van de REST API te gebruiken voor het maken van de bron. Elke resourceprovider heeft zijn eigen API-versies gepubliceerd, zodat deze waarde specifiek is voor het type.
- **naam:** Naam van de resource.

De meeste resources hebben ook een **locatieeigenschap,** die de regio instelt waar de resource wordt geïmplementeerd.

De andere eigenschappen verschillen per resourcetype en API-versie. Het is belangrijk om de verbinding tussen de API-versie en de beschikbare eigenschappen te begrijpen, dus laten we meer in detail gaan.

In deze zelfstudie hebt u een opslagaccount aan de sjabloon toegevoegd. U die API-versie zien op [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). U hebt niet alle eigenschappen aan uw sjabloon toegevoegd. Veel van de eigenschappen zijn optioneel. De Microsoft.Storage-bronprovider kan een nieuwe API-versie vrijgeven, maar de versie die u implementeert, hoeft niet te worden gewijzigd. U die versie blijven gebruiken en weten dat de resultaten van uw implementatie consistent zijn.

Als u een oudere API-versie bekijkt, zoals [storageAccounts 2016-05-01,](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)ziet u dat er een kleinere set eigenschappen beschikbaar is.

Als u besluit de API-versie voor een resource te wijzigen, moet u de eigenschappen voor die versie evalueren en uw sjabloon op de juiste manier aanpassen.

## <a name="deploy-template"></a>Sjabloon implementeren

U de sjabloon implementeren om het opslagaccount te maken. Geef uw implementatie een andere naam, zodat u deze gemakkelijk in de geschiedenis vinden.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group)als u de resourcegroep niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de **variabele templateFile** hebt ingesteld op het pad naar het sjabloonbestand, zoals wordt weergegeven in de [eerste zelfstudie.](template-tutorial-create-first-template.md#deploy-template)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI beschikken.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de **foutopsporingsschakelaar** met de opdracht implementatie om de foutopsporingslogboeken weer te geven.  U ook de **verbose-schakelaar** gebruiken om de volledige foutopsporingslogboeken weer te geven.

Twee mogelijke implementatiefouten die u tegenkomen:

- Fout: code=accountnaamongeldig; Message={provide-unique-name} is geen geldige naam van een opslagaccount. De naam van het opslagaccount moet tussen de 3 en 24 tekens lang zijn en alleen getallen en kleine letters gebruiken.

    Vervang in de sjabloon **{provide-unique-name}** door een unieke naam van het opslagaccount.  Zie [Resource toevoegen](#add-resource).

- Fout: code=StorageAccountAlreadytaken; Message=Het opslagaccount met de naam store1abc09092019 is al bezet.

    Probeer in de sjabloon een andere naam van het opslagaccount.

Deze implementatie duurt langer dan de implementatie van uw lege sjabloon omdat het opslagaccount is gemaakt. Het kan ongeveer een minuut duren, maar is meestal sneller.

## <a name="verify-deployment"></a>Implementatie verifiëren

U de implementatie verifiëren door de brongroep te verkennen vanuit de Azure-portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen**in het linkermenu .
1. Selecteer de resourcegroep die u hebt geïmplementeerd.
1. U ziet dat een opslagaccount is geïmplementeerd.
1. Merk op dat het implementatielabel nu zegt: **Implementaties: 2 Geslaagd**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u doorgaat naar de volgende zelfstudie, hoeft u de brongroep niet te verwijderen.

Als u nu stopt, u de resources die u hebt geïmplementeerd, opschonen door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt een eenvoudige sjabloon gemaakt om een Azure-opslagaccount te implementeren.  In de latere zelfstudies leert u hoe u parameters, variabelen, resources en uitvoer toevoegt aan een sjabloon. Deze functies zijn de bouwstenen voor veel complexere sjablonen.

> [!div class="nextstepaction"]
> [Parameters toevoegen](template-tutorial-add-parameters.md)