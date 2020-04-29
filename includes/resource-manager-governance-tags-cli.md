---
title: bestand opnemen
description: bestand opnemen
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c1259584e91461865b0c7e7bbbd6aced1781827b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67708454"
---
Gebruik de opdracht [az group update](/cli/azure/group) om twee tags toe te voegen aan een resourcegroep:

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Stel dat u een derde tag wilt toevoegen. Voer de opdracht opnieuw uit met de nieuwe tag. Deze wordt toegevoegd aan de bestaande tags.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Resources nemen geen tags over van de resourcegroep. Op dit moment heeft uw resourcegroep drie tags, maar hebben de resources geen tags. Gebruik het volgende script als u alle tags van een resourcegroep op de bijbehorende resources wilt toepassen en daarbij ook de bestaande tags voor de resources wilt behouden:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Get the tags for this resource
  jsonrtag=$(az resource show --id $resid --query tags)
  
  # Reformat from JSON to space-delimited and equals sign
  rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
  
  # Reapply the updated tags to this resource
  az resource tag --tags $t$rt --id $resid
done
```

U kunt ook tags van de resourcegroep toepassen op de resources zonder de bestaande tags te houden:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Apply tags from resource group to this resource
  az resource tag --tags $t --id $resid
done
```

Gebruik een JSON-tekenreeks om meerdere waarden met elkaar te combineren in een enkele tag.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Als u alle tags van een resourcegroep wilt verwijderen, gebruikt u:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
