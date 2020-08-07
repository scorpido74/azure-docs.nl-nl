---
title: Globale parameters
description: Algemene para meters instellen voor elk van uw Azure Data Factory omgevingen
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/05/2020
ms.openlocfilehash: 052f502ed27db9ade0fd2916f91d6922c52a5a98
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854268"
---
# <a name="global-parameters-in-azure-data-factory"></a>Globale para meters in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Globale para meters zijn constanten over een data factory die kunnen worden gebruikt door een pijp lijn in een wille keurige expressie. Ze zijn handig wanneer u meerdere pijp lijnen met identieke parameter namen en-waarden hebt. Bij het promo veren van een data factory met behulp van het doorlopende integratie-en implementatie proces (CI/CD), kunt u deze para meters in elke omgeving overschrijven. 

## <a name="creating-global-parameters"></a>Globale para meters maken

Als u een globale para meter wilt maken, gaat u naar het tabblad *globale para meters* in de sectie *beheren* . Selecteer **Nieuw** om het maken van de kant-NAV te openen.

![Algemene para meters maken](media/author-global-parameters/create-global-parameter-1.png)

Voer in de zijde-NAV een naam in, selecteer een gegevens type en geef de waarde van de para meter op.

![Algemene para meters maken](media/author-global-parameters/create-global-parameter-2.png)

Nadat u een globale para meter hebt gemaakt, kunt u deze bewerken door te klikken op de naam van de para meter. Als u meerdere para meters tegelijk wilt wijzigen, selecteert u **Alles bewerken**.

![Algemene para meters maken](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>Globale para meters gebruiken in een pijp lijn

Globale para meters kunnen worden gebruikt in elke [pijplijn expressie](control-flow-expression-language-functions.md). Als een pijp lijn verwijst naar een andere bron, zoals een gegevensset of gegevens stroom, kunt u de globale parameter waarde door geven via de para meters van die resource. Er wordt naar globale para meters verwezen als `pipeline().globalParameters.<parameterName>` .

![Globale para meters gebruiken](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a>Algemene para meters in CI/CD

Globale para meters hebben een uniek CI/CD-proces ten opzichte van andere entiteiten in Azure Data Factory. Wanneer u een fabriek publiceert of een ARM-sjabloon met globale para meters exporteert, wordt een map met de naam *globalParameters* gemaakt met een bestand met de naam *your-factory-name_GlobalParameters.jsop*. Dit bestand is een JSON-object dat elk globaal parameter type en waarde bevat in de gepubliceerde Factory.

![Global-para meters publiceren](media/author-global-parameters/global-parameters-adf-publish.png)

Als u implementeert in een nieuwe omgeving, zoals TEST of PROD, is het raadzaam om een kopie te maken van dit bestand met globale para meters en de juiste waarden voor de specifieke omgeving te overschrijven. Wanneer u het oorspronkelijke bestand met globale para meters opnieuw publiceert, wordt overschreven, maar wordt de kopie voor de andere omgeving niet meer gewijzigd.

Als u bijvoorbeeld een fabriek met de naam ' ADF-DEV ' hebt en een algemene para meter van het type teken reeks met de naam ' omgeving ' met de waarde ' dev ', worden er bij het publiceren van een bestand met de naam *ADF-DEV_GlobalParameters.jsop* gegenereerd. Als u implementeert in een test-Factory met de naam ' ADF_TEST ', maakt u een kopie van het JSON-bestand (bijvoorbeeld de naam ADF-TEST_GlobalParameters.jsop) en vervangt u de parameter waarden door de omgevings waarden. De para meter Environment kan nu een waarde ' test ' hebben. 

![Globale para meters implementeren](media/author-global-parameters/powershell-task.png)

Gebruik het onderstaande Power shell-script voor het promo veren van globale para meters voor andere omgevingen. Voeg een Azure PowerShell DevOps-taak toe vóór uw ARM-Sjabloonimlementatie. In de taak DevOps moet u de locatie van het nieuwe parameter bestand, de doel resource groep en het doel data factory opgeven.

> [!NOTE]
> Voor het implementeren van algemene para meters met behulp van Power shell, moet u ten minste versie 4.4.0 van de module AZ gebruiken.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [doorlopende integratie-en implementatie proces](continuous-integration-deployment.md) van Azure Data Factory
* Meer informatie over het gebruik van de [controle stroom expressie taal](control-flow-expression-language-functions.md)