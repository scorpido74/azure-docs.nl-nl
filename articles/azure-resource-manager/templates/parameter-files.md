---
title: Parameterbestand maken
description: Parameterbestand maken voor het doorgeven van waarden tijdens de implementatie van een Azure Resource Manager-sjabloon
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: a1a1f703594f8eaa572ea38ecef88b4cd6ba5a4b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682896"
---
# <a name="create-resource-manager-parameter-file"></a>Parameterbestand Resourcebeheer maken

In plaats van parameters door te geven als inlinewaarden in uw script, u het gemakkelijker vinden om een JSON-bestand te gebruiken dat de parameterwaarden bevat. In dit artikel ziet u hoe u het parameterbestand maakt.

## <a name="parameter-file"></a>Parameterbestand

Het parameterbestand heeft de volgende indeling:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Merk op dat de parameterwaarden worden opgeslagen als platte tekst in het parameterbestand. Deze benadering werkt voor waarden die niet gevoelig zijn, zoals het opgeven van de SKU voor een resource. Het werkt niet voor gevoelige waarden, zoals wachtwoorden. Als u een gevoelige waarde als parameter moet doorgeven, slaat u de waarde op in een sleutelkluis en verwijst u naar de sleutelkluis in uw parameterbestand. De gevoelige waarde wordt veilig opgehaald tijdens de implementatie.

Het volgende parameterbestand bevat een waarde met effen tekst en een waarde die is opgeslagen in een sleutelkluis.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Zie Azure Key Vault gebruiken om [veilige parameterwaarde tijdens de implementatie door te geven voor](key-vault-parameter.md)meer informatie over het gebruik van waarden uit een sleutelkluis.

## <a name="define-parameter-values"></a>Parameterwaarden definiëren

Als u wilt weten hoe u de parameterwaarden definiëren, opent u de sjabloon die u implementeert. Bekijk het gedeelte parameters van de sjabloon. In het volgende voorbeeld worden de parameters van een sjabloon weergegeven.

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

Het eerste detail dat opvalt is de naam van elke parameter. De waarden in uw parameterbestand moeten overeenkomen met de namen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Let op het type parameter. De waarden in het parameterbestand moeten dezelfde typen hebben. Voor deze sjabloon u beide parameters als tekenreeksen opgeven.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Zoek vervolgens naar een standaardwaarde. Als een parameter een standaardwaarde heeft, u een waarde opgeven, maar dat hoeft niet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Tot slot, kijk naar de toegestane waarden en eventuele beperkingen zoals maximale lengte. Ze vertellen u het bereik van waarden die u bieden voor de parameter.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

## <a name="parameter-type-formats"></a>Parametertype-indelingen

In het volgende voorbeeld worden de opaties van verschillende parametertypen weergegeven.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="file-name"></a>Bestandsnaam

De algemene conventie voor het benoemen van het parameterbestand is het toevoegen van **.parameters** aan de sjabloonnaam. Als uw sjabloon bijvoorbeeld **azuredeploy.json**wordt genoemd, krijgt uw parameterbestand de naam **azuredeploy.parameters.json**. Met deze naamgevingsconventie u de verbinding tussen de sjabloon en de parameters zien.

Als u wilt implementeren in verschillende omgevingen, maakt u meer dan één parameterbestand. Voeg bij het benoemen van het parameterbestand een manier toe om het gebruik ervan te identificeren. Gebruik bijvoorbeeld **azuredeploy.parameters-dev.json** en **azuredeploy.parameters-prod.json**


## <a name="parameter-precedence"></a>Parametervoorrang

U inlineparameters en een lokaal parameterbestand gebruiken in dezelfde implementatiebewerking. U bijvoorbeeld bepaalde waarden opgeven in het lokale parameterbestand en tijdens de implementatie andere waarden toevoegen. Als u waarden opgeeft voor een parameter in zowel het lokale parameterbestand als inline, heeft de inlinewaarde voorrang.

Wanneer u echter een extern parameterbestand gebruikt, u geen andere waarden doorgeven, noch inline als vanuit een lokaal bestand. Alle inline parameters worden genegeerd. Geef alle parameterwaarden op in het externe bestand.

## <a name="parameter-name-conflicts"></a>Parameternaamconflicten

Als uw sjabloon een parameter bevat met dezelfde naam als een van de parameters in de opdracht PowerShell, presenteert PowerShell de parameter van uw sjabloon met het postfix **FromTemplate**. Een parameter met de naam **ResourceGroupName** in uw sjabloon komt bijvoorbeeld in conflict met de parameter **ResourceGroupName** in de cmdlet [Nieuw-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) U wordt gevraagd een waarde op te geven voor **ResourceGroupNameFromTemplate**. U deze verwarring voorkomen door parameternamen te gebruiken die niet worden gebruikt voor implementatieopdrachten.

## <a name="next-steps"></a>Volgende stappen

- Zie [Parameters in Azure Resource Manager-sjablonen](template-parameters.md)voor meer informatie over het definiëren van parameters in uw sjabloon.
- Zie Azure Key Vault gebruiken om [veilige parameterwaarde tijdens de implementatie door te geven voor](key-vault-parameter.md)meer informatie over het gebruik van waarden uit een sleutelkluis.
- Zie [Parameters in Azure Resource Manager-sjablonen](template-parameters.md)voor meer informatie over parameters.
