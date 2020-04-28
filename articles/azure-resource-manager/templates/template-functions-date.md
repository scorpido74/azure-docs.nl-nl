---
title: Sjabloon functies-datum
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon om met datums te werken.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 0c31b26361a262a502b2a9e0fb068391846cab4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192294"
---
# <a name="date-functions-for-arm-templates"></a>Datum functies voor ARM-sjablonen

Resource Manager biedt de volgende functies voor het werken met datums in uw Azure Resource Manager ARM-sjablonen:

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Voegt een tijds duur toe aan een basis waarde. ISO 8601-indeling wordt verwacht.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| base | Ja | tekenreeks | De begin datum/tijd-waarde voor de toevoeging. [ISO 8601-time stamp notatie](https://en.wikipedia.org/wiki/ISO_8601)gebruiken. |
| duur | Ja | tekenreeks | De tijd waarde die aan de basis moet worden toegevoegd. Dit kan een negatieve waarde zijn. Gebruik de [ISO 8601-duur notatie](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| formaat | Nee | tekenreeks | De uitvoer indeling voor het resultaat van de datum en tijd. Als u dit niet opgeeft, wordt de indeling van de basis waarde gebruikt. Gebruik [standaard notatie teken reeksen](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of [teken reeksen met aangepaste notaties](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Retourwaarde

De waarde voor datum/tijd die het resultaat is van het toevoegen van de waarde duration aan de basis waarde.

### <a name="examples"></a>Voorbeelden

In de volgende voorbeeld sjabloon ziet u verschillende manieren om tijd waarden toe te voegen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]"
        }
    },
    "variables": {
        "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
        "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
        "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [],
    "outputs": {
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

Wanneer de voor gaande sjabloon wordt geïmplementeerd met een basis tijd `2020-04-07 14:53:14Z`van, is de uitvoer:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| add3Years | Tekenreeks | 4/7/2023 2:53:14 UUR |
| subtract9Days | Tekenreeks | 3/29/2020 2:53:14 UUR |
| add1Hour | Tekenreeks | 4/7/2020 3:53:14 UUR |

In de volgende voorbeeld sjabloon ziet u hoe u de start tijd voor een Automation-schema kunt instellen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "omsAutomationAccountName": {
            "type": "string",
            "defaultValue": "demoAutomation",
            "metadata": {
                "description": "Use an existing Automation account."
            }
        },
        "scheduleName": {
            "type": "string",
            "defaultValue": "demoSchedule1",
            "metadata": {
                "description": "Name of the new schedule."
            }
        },
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]",
            "metadata": {
                "description": "Schedule will start one hour from this time."
            }
        }
    },
    "variables": {
        "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Retourneert de huidige (UTC) datum/tijd-waarde in de opgegeven notatie. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling (yyyyMMddTHHmmssZ) gebruikt. **Deze functie kan alleen worden gebruikt in de standaard waarde voor een para meter.**

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| formaat |Nee |tekenreeks |De gecodeerde URI-waarde die moet worden geconverteerd naar een teken reeks. Gebruik [standaard notatie teken reeksen](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of [teken reeksen met aangepaste notaties](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Opmerkingen

U kunt deze functie alleen gebruiken in een expressie voor de standaard waarde van een para meter. Als u deze functie ergens anders in een sjabloon gebruikt, wordt er een fout geretourneerd. De functie is niet toegestaan in andere onderdelen van de sjabloon omdat deze een andere waarde retourneert telkens wanneer deze wordt aangeroepen. Het implementeren van dezelfde sjabloon met dezelfde para meters zou geen betrouw bare resultaten opleveren.

Als u de optie gebruikt voor het opnieuw [implementeren van een eerdere geslaagde implementatie](rollback-on-error.md), en de eerdere implementatie bevat een para meter die gebruikmaakt van utcNow, wordt de para meter niet opnieuw geëvalueerd. In plaats daarvan wordt de parameter waarde van de eerdere implementatie automatisch opnieuw gebruikt in de rollback-implementatie.

Wees voorzichtig met het opnieuw implementeren van een sjabloon die afhankelijk is van de functie utcNow voor een standaard waarde. Wanneer u opnieuw implementeert en geen waarde opgeeft voor de para meter, wordt de functie opnieuw geëvalueerd. Als u een bestaande resource wilt bijwerken in plaats van een nieuwe te maken, geeft u de parameter waarde van de eerdere implementatie door.

### <a name="return-value"></a>Retourwaarde

De huidige UTC-waarde voor datum/tijd.

### <a name="examples"></a>Voorbeelden

In de volgende voorbeeld sjabloon ziet u verschillende notaties voor de datum/tijd-waarde.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

De uitvoer van het voor gaande voor beeld varieert per implementatie, maar lijkt op:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| utcOutput | tekenreeks | 20190305T175318Z |
| utcShortOutput | tekenreeks | 03/05/2019 |
| utcCustomOutput | tekenreeks | 3 5 |

In het volgende voor beeld ziet u hoe u een waarde uit de functie gebruikt wanneer u een tag-waarde instelt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager sjabloon.
