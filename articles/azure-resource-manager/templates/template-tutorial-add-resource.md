---
title: Zelf studie-resource aan sjabloon toevoegen
description: Hierin worden de stappen beschreven voor het maken van uw eerste Azure Resource Manager sjabloon. Meer informatie over de syntaxis van het sjabloon bestand en het implementeren van een opslag account.
author: mumian
ms.date: 02/24/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e533b70ba713b8c52a29cfbcc1cd8ccb99b8543f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370692"
---
# <a name="tutorial-add-a-resource-to-your-resource-manager-template"></a>Zelf studie: een resource toevoegen aan uw Resource Manager-sjabloon

In de [vorige zelf studie](template-tutorial-create-first-template.md)hebt u geleerd hoe u een lege sjabloon maakt en deze implementeert. U bent nu klaar om een werkelijke resource te implementeren. In deze zelf studie voegt u een opslag account toe. Het duurt ongeveer **9 minuten** om deze zelf studie te volt ooien.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden de [inleidende zelf studie over sjablonen](template-tutorial-create-first-template.md)te volt ooien, maar dit is niet vereist.

U moet Visual Studio code hebben met de uitbrei ding Resource Manager tools en een Azure PowerShell of Azure CLI. Zie voor meer informatie [sjabloon hulpprogramma's](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Resource toevoegen

Als u een opslag account definitie wilt toevoegen aan de bestaande sjabloon, bekijkt u de gemarkeerde JSON in het volgende voor beeld. In plaats van een poging om secties van de sjabloon te kopiëren, kopieert u het hele bestand en vervangt u uw sjabloon door de inhoud ervan.

Vervang **{Geef unieke naam}** door een unieke naam voor het opslag account.

> [!IMPORTANT]
> De naam van het opslagaccount moet uniek zijn in Azure. De naam mag alleen kleine letters of cijfers bevatten. De waarde mag niet langer zijn dan 24 tekens. U kunt een naamgevings patroon gebruiken zoals het gebruik van **store1** als voor voegsel en vervolgens uw initialen en de datum van vandaag toevoegen. De naam die u gebruikt, kan er bijvoorbeeld uitzien als **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Het raden van een unieke naam voor een opslag account is niet eenvoudig en werkt niet goed voor het automatiseren van grote implementaties. Verderop in deze reeks zelf studies gebruikt u sjabloon functies waarmee u gemakkelijker een unieke naam kunt maken.

## <a name="resource-properties"></a>Resource-eigenschappen

U vraagt zich misschien af hoe u de eigenschappen voor elk resource type kunt vinden. U kunt de [Resource Manager-sjabloon verwijzing](/azure/templates/) gebruiken om de resource typen te vinden die u wilt implementeren.

Elke resource die u implementeert, heeft ten minste de volgende drie eigenschappen:

- **type**: het type van de resource. Deze waarde is een combi natie van de naam ruimte van de resource provider en het resource type (zoals micro soft. Storage/Storage accounts).
- **apiVersion**: de versie van de rest API die moet worden gebruikt voor het maken van de resource. Elke resource provider heeft zijn eigen API-versies gepubliceerd, waardoor deze waarde specifiek is voor het type.
- **naam**: naam van de resource.

De meeste resources hebben ook een **locatie** -eigenschap, die de regio instelt waarin de resource wordt geïmplementeerd.

De overige eigenschappen variëren per bron type en API-versie. Het is belang rijk om inzicht te krijgen in de verbinding tussen de API-versie en de beschik bare eigenschappen, dus we gaan meer details door.

In deze zelf studie hebt u een opslag account aan de sjabloon toegevoegd. U kunt deze API-versie zien op [storage accounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). U ziet dat u niet alle eigenschappen aan uw sjabloon hebt toegevoegd. Veel van de eigenschappen zijn optioneel. De resource provider micro soft. Storage kan een nieuwe API-versie vrijgeven, maar de versie die u implementeert, hoeft niet te worden gewijzigd. U kunt deze versie blijven gebruiken en er zeker van zijn dat de resultaten van uw implementatie consistent zijn.

Als u een oudere API-versie bekijkt, zoals [storage accounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), ziet u dat er een kleiner aantal eigenschappen beschikbaar is.

Als u besluit de API-versie voor een resource te wijzigen, moet u de eigenschappen voor die versie evalueren en de sjabloon op de juiste wijze aanpassen.

## <a name="deploy-template"></a>Sjabloon implementeren

U kunt de sjabloon implementeren om het opslag account te maken. Geef uw implementatie een andere naam zodat u deze eenvoudig kunt vinden in de geschiedenis.

Als u de resource groep nog niet hebt gemaakt, raadpleegt u [resource groep maken](template-tutorial-create-first-template.md#create-resource-group). In het voor beeld wordt ervan uitgegaan dat u de **templateFile** -variabele hebt ingesteld op het pad naar het sjabloon bestand, zoals wordt weer gegeven in de [eerste zelf studie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Twee mogelijke implementatie fouten die kunnen optreden:

- Fout: code = AccountNameInvalid; Bericht = {Geef unieke naam} is geen geldige naam voor een opslag account. De naam van het opslag account moet tussen 3 en 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.

    Vervang **{Geef unieke naam}** in de sjabloon door een unieke naam voor het opslag account.  Zie [resource toevoegen](#add-resource).

- Fout: code = StorageAccountAlreadyTaken; Bericht = het opslag account met de naam store1abc09092019 wordt al gebruikt.

    Probeer in de sjabloon een andere naam voor het opslag account.

Deze implementatie duurt langer dan de implementatie van de lege sjabloon omdat het opslag account is gemaakt. Het kan ongeveer een minuut duren, maar is doorgaans sneller.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resource groep te verkennen van de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **resource groepen**in het menu links.
1. Selecteer de resource groep die u hebt geïmplementeerd.
1. U ziet dat er een opslag account is geïmplementeerd.
1. U ziet dat het label implementatie nu de volgende tekst bevat: **implementaties: 2 geslaagd**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelf studie, hoeft u de resource groep niet te verwijderen.

Als u nu stopt, wilt u misschien de resources opschonen die u hebt geïmplementeerd door de resource groep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt een eenvoudige sjabloon gemaakt voor het implementeren van een Azure Storage-account.  In de volgende zelf studies leert u hoe u para meters, variabelen, resources en uitvoer kunt toevoegen aan een sjabloon. Deze functies zijn de bouw stenen voor veel complexere sjablonen.

> [!div class="nextstepaction"]
> [Para meters toevoegen](template-tutorial-add-parameters.md)