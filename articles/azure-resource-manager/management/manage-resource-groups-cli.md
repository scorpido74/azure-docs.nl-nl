---
title: Resourcegroepen beheren - Azure CLI
description: Gebruik Azure CLI om uw brongroepen te beheren via Azure Resource Manager. Hier ziet u hoe u resourcegroepen maakt, opdook en verwijdert.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 7face572f545153ea92efbdb345bbaabda5dd126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248331"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Azure Resource Manager-brongroepen beheren met Azure CLI

Meer informatie over het gebruik van Azure CLI met [Azure Resource Manager](overview.md) om uw Azure-brongroepen te beheren. Zie [Azure-resources beheren met Azure CLI](manage-resources-cli.md)voor het beheren van Azure-resources.

Andere artikelen over het beheren van resourcegroepen:

- [Azure-brongroepen beheren met behulp van de Azure-portal](manage-resources-portal.md)
- [Azure-brongroepen beheren met Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Wat is een resourcegroep

Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. De resourcegroep kan alle resources voor de oplossing bevatten of enkel de resources die u als groep wilt beheren. U bepaalt hoe resources worden toegewezen aan resourcegroepen op basis van wat voor uw organisatie het meest zinvol is. Voeg over het algemeen resources toe die dezelfde levenscyclus delen aan dezelfde resourcegroep, zodat u ze eenvoudig implementeren, bijwerken en verwijderen als groep.

De resourcegroep slaat metagegevens op over de resources. Dat is de reden waarom u moet aangeven waar die metagegevens moeten worden opgeslagen als u een locatie voor de resourcegroep opgeeft. In verband met nalevingsvereisten moet u er mogelijk voor zorgen dat uw gegevens worden opgeslagen in een bepaalde regio.

De resourcegroep slaat metagegevens op over de resources. Wanneer u een locatie voor de resourcegroep opgeeft, geeft u op waar die metagegevens zijn opgeslagen.

## <a name="create-resource-groups"></a>Resourcegroepen maken

Met het volgende CLI-script wordt een resourcegroep gemaakt en wordt de resourcegroep weergegeven.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Resourcegroepen aanbieden

In het volgende CLI-script worden de brongroepen onder uw abonnement weergegeven.

```azurecli-interactive
az group list
```

Eén resourcegroep op te halen:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Resourcegroepen verwijderen

In het volgende CLI-script wordt een brongroep verwijderd:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Zie het verwijderen van [azure resource manager-bronnen](delete-resource-group.md)voor meer informatie over hoe Azure Resource Manager de verwijdering van resources bestelt.

## <a name="deploy-resources-to-an-existing-resource-group"></a>Resources implementeren voor een bestaande resourcegroep

Zie [Resources implementeren voor een bestaande resourcegroep](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Een resourcegroep en resources implementeren

U een resourcegroep maken en resources naar de groep implementeren met behulp van een resourcemanagersjabloon. Zie [Resourcegroep maken en resources implementeren](../templates/deploy-to-subscription.md#resource-group-and-resources)voor meer informatie.

## <a name="redeploy-when-deployment-fails"></a>Opnieuw implementeren wanneer implementatie mislukt

Deze functie wordt ook wel *Rollback on error genoemd.* Zie [Opnieuw implementeren wanneer de implementatie mislukt](../templates/rollback-on-error.md)voor meer informatie .

## <a name="move-to-another-resource-group-or-subscription"></a>Naar een andere resourcegroep of -abonnement gaan

U de resources in de groep verplaatsen naar een andere resourcegroep. Zie [Resources verplaatsen](manage-resources-cli.md#move-resources)voor meer informatie .

## <a name="lock-resource-groups"></a>Brongroepen vergrendelen

Vergrendeling voorkomt dat andere gebruikers in uw organisatie per ongeluk kritieke bronnen verwijderen of wijzigen, zoals Azure-abonnement, resourcegroep of resource. 

In het volgende script wordt een resourcegroep vergrendeld, zodat de resourcegroep niet kan worden verwijderd.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

In het volgende script worden alle vergrendelingen voor een resourcegroep opgevolgd:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

In het volgende script wordt een vergrendeling verwijderd:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Brongroepen taggen

U tags toepassen op resourcegroepen en resources om uw assets logisch te ordenen. Zie Tags [gebruiken om uw Azure-bronnen te ordenen](tag-resources.md#azure-cli)voor meer informatie.

## <a name="export-resource-groups-to-templates"></a>Brongroepen exporteren naar sjablonen

Nadat u de resourcegroep hebt ingesteld, u de sjabloon Resourcebeheer voor de resourcegroep weergeven. Het exporteren van de sjabloon biedt twee voordelen:

- Automatiseer toekomstige implementaties van de oplossing omdat de sjabloon alle volledige infrastructuur bevat.
- Lees de syntaxis van sjablonen door te kijken naar de Json (JavaScript Object Notatie) die uw oplossing vertegenwoordigt.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

In het script wordt de sjabloon op de console weergegeven.  Kopieer de JSON en sla op als bestand.

De functie exportsjabloon biedt geen ondersteuning voor het exporteren van Azure Data Factory-resources. Zie [Een gegevensfabriek kopiëren of klonen in Azure Data Factory](https://aka.ms/exportTemplateViaAdf)voor meer informatie over hoe u gegevensfabriekresources exporteren.

Als u resources wilt exporteren die zijn gemaakt via een klassiek implementatiemodel, moet u [deze migreren naar het implementatiemodel resourcebeheer](https://aka.ms/migrateclassicresourcetoarm).

Zie Exporteren naar sjabloon met [één en meerdere resources naar een sjabloon in azure portal](../templates/export-template-portal.md)voor meer informatie.

## <a name="manage-access-to-resource-groups"></a>Toegang tot resourcegroepen beheren

[RBAC (Role-based access control)](../../role-based-access-control/overview.md) is de manier waarop u de toegang tot bronnen in Azure beheert. Zie [Toegang beheren met RBAC en Azure CLI](../../role-based-access-control/role-assignments-cli.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie Overzicht azure [resource manager](overview.md)voor azure resource manager.
- Zie [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen](../templates/template-syntax.md)voor de syntaxis van de resourcemanagersjabloon .
- Zie de [stapsgewijze zelfstudies](/azure/azure-resource-manager/)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloonverwijzing](/azure/templates/)voor het weergeven van de sjabloonschema's van Azure Resource Manager .