---
title: Sjabloonfuncties - datum
description: Beschrijft de functies die u moet gebruiken in een Azure Resource Manager-sjabloon om met datums te werken.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 75234a142b9f2fbe61c337bfeb378b47534bac79
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986276"
---
# <a name="date-functions-for-arm-templates"></a>Datumfuncties voor ARM-sjablonen

Resourcebeheer biedt de volgende functies voor het werken met datums in uw Azure Resource Manager-sjablonen (ARM):Resource Manager provides the following functions for working with dates in your Azure Resource Manager (ARM) templates:

* [dateTimeAdd dateTimeAdd dateTimeAdd dateTime](#datetimeadd)
* [utcNu](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd dateTimeAdd dateTimeAdd dateTime

`dateTimeAdd(base, duration, [format])`

Hiermee voegt u een tijdsduur toe aan een basisdatumwaarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| base | Ja | tekenreeks | De begindatumwaarde voor de toevoeging. Gebruik [de tijdstempelindeling ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| duur | Ja | tekenreeks | De tijdswaarde die aan de basis moet worden toegevoegd. Het kan een negatieve waarde zijn. Gebruik [de duurnotatie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| formaat | Nee | tekenreeks | De uitvoernotatie voor het datumresultaat. Indien dit niet is voorzien, wordt de notatie van de basiswaarde gebruikt. Gebruik [tekenreeksen voor standaardindelingen](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of [tekenreeksen voor aangepaste indelingen](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Retourwaarde

De datumtijdwaarde die voortvloeit uit het toevoegen van de duurwaarde aan de basiswaarde.

### <a name="examples"></a>Voorbeelden

In de volgende voorbeeldsjabloon worden verschillende manieren weergegeven om tijdwaarden toe te voegen.

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

Wanneer de voorgaande sjabloon wordt geïmplementeerd `2020-04-07 14:53:14Z`met een basistijd van , is de uitvoer:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| add3Years | Tekenreeks | 4/7/2023 14:53:14 |
| subtract9Days | Tekenreeks | 3/29/2020 14:53:14 |
| toevoegen1Uur | Tekenreeks | 4/7/2020 15:53:14 |

In de volgende voorbeeldsjabloon ziet u hoe u de begintijd voor een automatiseringsschema instelt.

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

## <a name="utcnow"></a>utcNu

`utcNow(format)`

Geeft als resultaat de huidige datumtijdwaarde (UTC) in de opgegeven notatie. Als er geen formaat wordt geleverd, wordt het ISO 8601 -formaat (yyyyMddTHHmmssZ) gebruikt. **Deze functie kan alleen worden gebruikt in de standaardwaarde voor een parameter.**

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| formaat |Nee |tekenreeks |De URI-gecodeerde waarde om te converteren naar een tekenreeks. Gebruik [tekenreeksen voor standaardindelingen](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of [tekenreeksen voor aangepaste indelingen](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Opmerkingen

U deze functie alleen gebruiken in een expressie voor de standaardwaarde van een parameter. Als u deze functie ergens anders in een sjabloon gebruikt, wordt een fout geretourneerd. De functie is niet toegestaan in andere delen van de sjabloon omdat deze elke keer dat deze wordt aangeroepen een andere waarde retourneert. Het implementeren van dezelfde sjabloon met dezelfde parameters zou niet betrouwbaar dezelfde resultaten opleveren.

Als u de optie gebruikt [om een eerdere succesvolle implementatie opnieuw te implementeren](rollback-on-error.md)en de eerdere implementatie een parameter bevat die utcNow gebruikt, wordt de parameter niet opnieuw geëvalueerd. In plaats daarvan wordt de parameterwaarde van de eerdere implementatie automatisch opnieuw gebruikt in de rollback-implementatie.

Zorg ervoor dat u een sjabloon opnieuw implementeert die is gebaseerd op de utcNow-functie voor een standaardwaarde. Wanneer u de parameter opnieuw implementeert en geen waarde voor de parameter geeft, wordt de functie opnieuw geëvalueerd. Als u een bestaande resource wilt bijwerken in plaats van een nieuwe resource wilt maken, geeft u de parameterwaarde door van de eerdere implementatie.

### <a name="return-value"></a>Retourwaarde

De huidige UTC-datumwaarde.

### <a name="examples"></a>Voorbeelden

In de volgende voorbeeldsjabloon worden verschillende indelingen voor de datumtijdwaarde weergegeven.

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

De uitvoer van het voorgaande voorbeeld varieert voor elke implementatie, maar is vergelijkbaar met:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| utcOutput | tekenreeks | 20190305T175318Z |
| utcShortOutput | tekenreeks | 03/05/2019 |
| utcCustomOutput | tekenreeks | 3 5 |

In het volgende voorbeeld ziet u hoe u een waarde van de functie gebruikt bij het instellen van een tagwaarde.

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