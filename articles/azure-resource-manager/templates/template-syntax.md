---
title: Sjabloonstructuur en syntaxis
description: Beschrijft de structuur en eigenschappen van Azure Resource Manager-sjablonen met behulp van declaratieve JSON-syntaxis.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 60d800eb5251fb3454ba60a67bd109261c6ff9d4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687871"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>De structuur en syntaxis van ARM-sjablonen begrijpen

In dit artikel wordt de structuur van een ARM-sjabloon (Azure Resource Manager) beschreven. Het presenteert de verschillende secties van een sjabloon en de eigenschappen die beschikbaar zijn in deze secties.

Dit artikel is bedoeld voor gebruikers die enige bekendheid hebben met ARM-sjablonen. Het biedt gedetailleerde informatie over de structuur van de sjabloon. Zie [Zelfstudie: Uw eerste Azure Resource Manager-sjabloon maken en implementeren](template-tutorial-create-first-template.md)voor een stapsgewijze zelfstudie die u door het proces van het maken van een sjabloon leidt.

## <a name="template-format"></a>Sjabloonindeling

In de eenvoudigste structuur heeft een sjabloon de volgende elementen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Elementnaam | Vereist | Beschrijving |
|:--- |:--- |:--- |
| $schema |Ja |Locatie van het JSON-schemabestand dat de versie van de sjabloontaal beschrijft. Het versienummer dat u gebruikt, is afhankelijk van het bereik van de implementatie en uw JSON-editor.<br><br>Als u [VS-code gebruikt met de extensie Azure Resource Manager-hulpprogramma's,](use-vs-code-to-create-template.md)gebruikt u de nieuwste versie voor implementaties van resourcegroepen:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Andere editors (waaronder Visual Studio) kunnen dit schema mogelijk niet verwerken. Voor deze redacteuren, gebruik:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Gebruik voor abonnementsimplementaties:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>Gebruik voor implementaties van beheergroepen:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Gebruik voor tenant-implementaties het:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| inhoudVersie |Ja |Versie van de sjabloon (zoals 1.0.0.0). U elke waarde voor dit element bieden. Gebruik deze waarde om belangrijke wijzigingen in uw sjabloon vast te leggen. Bij het implementeren van resources met behulp van de sjabloon kan deze waarde worden gebruikt om ervoor te zorgen dat de juiste sjabloon wordt gebruikt. |
| apiProfiel |Nee | Een API-versie die dient als een verzameling API-versies voor resourcetypen. Gebruik deze waarde om te voorkomen dat API-versies voor elke resource in de sjabloon moeten worden opgegeven. Wanneer u een API-profielversie opgeeft en geen API-versie opgeeft voor het brontype, gebruikt Resource Manager de API-versie voor dat brontype dat in het profiel is gedefinieerd.<br><br>De eigenschap API-profiel is vooral handig bij het implementeren van een sjabloon in verschillende omgevingen, zoals Azure Stack en global Azure. Gebruik de API-profielversie om ervoor te zorgen dat uw sjabloon automatisch versies gebruikt die in beide omgevingen worden ondersteund. Zie [API-profiel](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)voor een lijst met de huidige API-profielversies en de API-versies van resources die in het profiel zijn gedefinieerd.<br><br>Zie [Versies bijhouden met API-profielen](templates-cloud-consistency.md#track-versions-using-api-profiles)voor meer informatie. |
| [Parameters](#parameters) |Nee |Waarden die worden geleverd wanneer de implementatie wordt uitgevoerd om de implementatie van resources aan te passen. |
| [Variabelen](#variables) |Nee |Waarden die worden gebruikt als JSON-fragmenten in de sjabloon om sjabloontaaluitdrukkingen te vereenvoudigen. |
| [Functies](#functions) |Nee |Door de gebruiker gedefinieerde functies die beschikbaar zijn in de sjabloon. |
| [Middelen](#resources) |Ja |Resourcetypen die worden geïmplementeerd of bijgewerkt in een resourcegroep of -abonnement. |
| [Uitgangen](#outputs) |Nee |Waarden die worden geretourneerd na implementatie. |

Elk element heeft eigenschappen die u instellen. In dit artikel worden de secties van de sjabloon gedetailleerder beschreven.

## <a name="parameters"></a>Parameters

In de sectie parameters van de sjabloon geeft u op welke waarden u invoeren bij het implementeren van de resources. U bent beperkt tot 256 parameters in een sjabloon. U het aantal parameters verminderen door objecten te gebruiken die meerdere eigenschappen bevatten.

De beschikbare eigenschappen voor een parameter zijn:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>"
    }
  }
}
```

| Elementnaam | Vereist | Beschrijving |
|:--- |:--- |:--- |
| parameternaam |Ja |Naam van de parameter. Het moet een geldige JavaScript-id zijn. |
| type |Ja |Type van de parameterwaarde. De toegestane typen en waarden zijn **tekenreeks**, **securestring**, **int,** **bool,** **object**, **secureObject**en **array**. Zie [Gegevenstypen](#data-types). |
| Standaardwaarde |Nee |Standaardwaarde voor de parameter, als er geen waarde is opgegeven voor de parameter. |
| toegestaanWaarden |Nee |Array met toegestane waarden voor de parameter om ervoor te zorgen dat de juiste waarde wordt opgegeven. |
| minWaarde |Nee |De minimumwaarde voor inttypeparameters, deze waarde is inclusief. |
| maxWaarde |Nee |De maximale waarde voor int type parameters, deze waarde is inclusief. |
| minLengte |Nee |De minimale lengte voor tekenreeks-, beveiligde tekenreeks- en arraytypeparameters, deze waarde is inclusief. |
| Maxlength |Nee |De maximale lengte voor tekenreeks-, beveiligde tekenreeks- en arraytypeparameters, deze waarde is inclusief. |
| description |Nee |Beschrijving van de parameter die via de portal aan gebruikers wordt weergegeven. Zie [Opmerkingen in sjablonen](#comments)voor meer informatie. |

Zie Parameters in Azure Resource [Manager-sjablonen](template-parameters.md)voor voorbeelden van het gebruik van parameters .

### <a name="data-types"></a>Gegevenstypen

Voor gehele getallen die als inlineparameters worden doorgegeven, kan het bereik van waarden worden beperkt door het SDK- of opdrachtregelgereedschap dat u gebruikt voor implementatie. Wanneer u bijvoorbeeld PowerShell gebruikt om een sjabloon te implementeren, kunnen gehele typen variëren van -2147483648 tot 2147483647. Als u deze beperking wilt voorkomen, geeft u grote gehele getallen op in een [parameterbestand](parameter-files.md). Resourcetypen passen hun eigen limieten toe voor gehele eigenschappen.

Wanneer u booleaanse en gehele waarden in uw sjabloon opgeeft, u de waarde niet omringen met aanhalingstekens. Tekenreekswaarden starten en beëindigen met dubbele aanhalingstekens.

Objecten beginnen met een linker brace en eindigen met een rechter brace. Arrays beginnen met een linkerbeugel en eindigen met een rechterbeugel.

Beveiligde tekenreeksen en beveiligde objecten kunnen niet worden gelezen na het implementeren van resources.

Zie [Parametertype-indelingen](parameter-files.md#parameter-type-formats)voor voorbeelden van opmaakgegevenstypen .

## <a name="variables"></a>Variabelen

In de sectie Variabelen construeert u waarden die in uw sjabloon kunnen worden gebruikt. U hoeft geen variabelen te definiëren, maar ze vereenvoudigen uw sjabloon vaak door complexe expressies te verminderen.

In het volgende voorbeeld worden de beschikbare opties weergegeven voor het definiëren van een variabele:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": {
    <variable-complex-type-value>
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Zie Variabele `copy` [iteratie](copy-variables.md)voor informatie over het gebruik van verschillende waarden voor een variabele.

Zie Variabelen in de [sjabloon Azure Resource Manager](template-variables.md)voor voorbeelden van het gebruik van variabelen.

## <a name="functions"></a>Functions

Binnen uw sjabloon u uw eigen functies maken. Deze functies zijn beschikbaar voor gebruik in uw sjabloon. Doorgaans definieert u ingewikkelde expressies die u niet in de hele sjabloon wilt herhalen. U maakt de door de gebruiker gedefinieerde functies uit expressies en [functies](template-functions.md) die worden ondersteund in sjablonen.

Bij het definiëren van een gebruikersfunctie zijn er enkele beperkingen:

* De functie heeft geen toegang tot variabelen.
* De functie kan alleen parameters gebruiken die in de functie zijn gedefinieerd. Wanneer u de [functie parameters](template-functions-deployment.md#parameters) binnen een door de gebruiker gedefinieerde functie gebruikt, bent u beperkt tot de parameters voor die functie.
* De functie kan geen andere door de gebruiker gedefinieerde functies aanroepen.
* De functie kan de [referentiefunctie](template-functions-resource.md#reference)niet gebruiken.
* Parameters voor de functie kunnen geen standaardwaarden hebben.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Elementnaam | Vereist | Beschrijving |
|:--- |:--- |:--- |
| naamruimte |Ja |Naamruimte voor de aangepaste functies. Gebruiken om te voorkomen dat naamgeving conflicten met sjabloonfuncties ingaat. |
| functienaam |Ja |Naam van de aangepaste functie. Combineer bij het aanroepen van de functie de functienaam met de naamruimte. Als u bijvoorbeeld een functie met de naam uniqueName `"[contoso.uniqueName()]"`in de naamruimtecontoso wilt aanroepen, gebruikt u . |
| parameternaam |Nee |Naam van de parameter die binnen de aangepaste functie moet worden gebruikt. |
| parameterwaarde |Nee |Type van de parameterwaarde. De toegestane typen en waarden zijn **tekenreeks**, **securestring**, **int,** **bool,** **object**, **secureObject**en **array**. |
| output-type |Ja |Type van de uitvoerwaarde. Uitvoerwaarden ondersteunen dezelfde typen als functieinvoerparameters. |
| output-waarde |Ja |Sjabloontaalexpressie die wordt geëvalueerd en geretourneerd uit de functie. |

Zie Door [gebruikers gedefinieerde functies in azure resource manager-sjabloon](template-user-defined-functions.md)voor voorbeelden van het gebruik van aangepaste functies.

## <a name="resources"></a>Resources

In de sectie Resources definieert u de resources die zijn geïmplementeerd of bijgewerkt.

U definieert resources met de volgende structuur:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Elementnaam | Vereist | Beschrijving |
|:--- |:--- |:--- |
| Voorwaarde | Nee | Booleaanse waarde die aangeeft of de resource tijdens deze implementatie wordt ingericht. Wanneer `true`wordt de resource gemaakt tijdens de implementatie. Wanneer `false`wordt de bron overgeslagen voor deze implementatie. Zie [voorwaarde](conditional-resource-deployment.md). |
| type |Ja |Type van de bron. Deze waarde is een combinatie van de naamruimte van de resourceprovider en het resourcetype (zoals **Microsoft.Storage/storageAccounts).** Zie [sjabloonverwijzing](/azure/templates/)om beschikbare waarden te bepalen. Voor een onderliggende bron is de indeling van het type afhankelijk van of deze is genest binnen de bovenliggende resource of buiten de bovenliggende resource is gedefinieerd. Zie [Naam en type instellen voor onderliggende resources](child-resource-name-type.md). |
| apiVersion |Ja |Versie van de REST API te gebruiken voor het maken van de bron. Zie [sjabloonverwijzing](/azure/templates/)om beschikbare waarden te bepalen. |
| name |Ja |De naam van de resource. De naam moet voldoen aan de uri-componentbeperkingen die zijn gedefinieerd in RFC3986. Azure-services die de bronnaam blootstellen aan externe partijen, valideren de naam om te controleren of het geen poging is om een andere identiteit te vervalsen. Voor een onderliggende bron is de indeling van de naam afhankelijk van of deze is genest binnen de bovenliggende resource of buiten de bovenliggende resource is gedefinieerd. Zie [Naam en type instellen voor onderliggende resources](child-resource-name-type.md). |
| Opmerkingen |Nee |Uw notities voor het documenteren van de bronnen in uw sjabloon. Zie [Opmerkingen in sjablonen](template-syntax.md#comments)voor meer informatie. |
| location |Varieert |Ondersteunde geolocaties van de geleverde bron. U een van de beschikbare locaties selecteren, maar meestal is het zinvol om er een te kiezen die dicht bij uw gebruikers staat. Meestal is het ook zinvol om resources te plaatsen die met elkaar communiceren in dezelfde regio. Voor de meeste resourcetypen is een locatie vereist, maar voor sommige typen (zoals een roltoewijzing) is geen locatie vereist. Zie [Resourcelocatie instellen](resource-location.md). |
| dependsOn |Nee |Resources die moeten worden geïmplementeerd voordat deze bron wordt geïmplementeerd. Resource Manager evalueert de afhankelijkheden tussen resources en implementeert deze in de juiste volgorde. Wanneer resources niet afhankelijk van elkaar zijn, worden ze parallel geïmplementeerd. De waarde kan een door komma's gescheiden lijst zijn met unieke resourcenamen of unieke resources-id's. Vermeld alleen bronnen die in deze sjabloon zijn geïmplementeerd. Er moeten al resources bestaan die niet in deze sjabloon zijn gedefinieerd. Voorkom het toevoegen van onnodige afhankelijkheden omdat ze uw implementatie kunnen vertragen en cirkelvormige afhankelijkheden kunnen maken. Zie [Afhankelijkheden definiëren in Azure Resource Manager-sjablonen](define-resource-dependency.md)voor richtlijnen voor het instellen van afhankelijkheden. |
| tags |Nee |Tags die zijn gekoppeld aan de resource. Tags toepassen om bronnen in uw abonnement logisch te ordenen. |
| sku | Nee | Met sommige resources kunnen waarden die de SKU definiëren, worden geïmplementeerd. U bijvoorbeeld het type redundantie voor een opslagaccount opgeven. |
| Soort | Nee | Sommige resources staan een waarde toe die het type resource definieert dat u implementeert. U bijvoorbeeld het type Cosmos DB opgeven dat u wilt maken. |
| Kopiëren |Nee |Als er meer dan één exemplaar nodig is, moet het aantal resources worden gemaakt. De standaardmodus is parallel. Geef de seriële modus op wanneer u niet wilt dat alle resources tegelijkertijd worden geïmplementeerd. Zie [Meerdere exemplaren van resources maken in Azure Resource Manager](copy-resources.md)voor meer informatie. |
| plannen | Nee | Sommige resources staan waarden toe die het plan definiëren om te implementeren. U bijvoorbeeld de marketplace-afbeelding voor een virtuele machine opgeven. |
| properties |Nee |Resourcespecifieke configuratie-instellingen. De waarden voor de eigenschappen zijn dezelfde als de waarden die u opgeeft in de aanvraaginstantie voor de REST API-bewerking (PUT-methode) om de resource te maken. U ook een kopieerarray opgeven om meerdere exemplaren van een eigenschap te maken. Zie [sjabloonverwijzing](/azure/templates/)om beschikbare waarden te bepalen. |
| resources |Nee |Onderliggende bronnen die afhankelijk zijn van de resource die wordt gedefinieerd. Geef alleen resourcetypen op die zijn toegestaan volgens het schema van de bovenliggende resource. Afhankelijkheid van de bovenliggende resource wordt niet geïmpliceerd. U moet die afhankelijkheid expliciet definiëren. Zie [Naam en type instellen voor onderliggende resources](child-resource-name-type.md). |

## <a name="outputs"></a>Uitvoer

In de sectie Uitvoer geeft u waarden op die zijn geretourneerd uit implementatie. Doorgaans retourneert u waarden uit resources die zijn geïmplementeerd.

In het volgende voorbeeld ziet u de structuur van een uitvoerdefinitie:

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| Elementnaam | Vereist | Beschrijving |
|:--- |:--- |:--- |
| uitvoernaam |Ja |Naam van de uitvoerwaarde. Het moet een geldige JavaScript-id zijn. |
| Voorwaarde |Nee | Booleaanse waarde die aangeeft of deze uitvoerwaarde wordt geretourneerd. Wanneer `true`, de waarde is opgenomen in de uitvoer voor de implementatie. Wanneer `false`wordt de uitvoerwaarde overgeslagen voor deze implementatie. Wanneer deze niet is `true`opgegeven, is de standaardwaarde . |
| type |Ja |Type van de uitvoerwaarde. Uitvoerwaarden ondersteunen dezelfde typen als sjablooninvoerparameters. Als u **securestring** opgeeft voor het uitvoertype, wordt de waarde niet weergegeven in de implementatiegeschiedenis en kan deze niet worden opgehaald uit een andere sjabloon. Als u een geheime waarde in meer dan één sjabloon wilt gebruiken, slaat u het geheim op in een sleutelkluis en verwijst u naar het geheim in het parameterbestand. Zie [Azure Key Vault gebruiken om veilige parameterwaarde door te geven tijdens de implementatie](key-vault-parameter.md)voor meer informatie. |
| waarde |Nee |Sjabloontaalexpressie die wordt geëvalueerd en geretourneerd als uitvoerwaarde. Geef **waarde** op of **kopieer**. |
| Kopiëren |Nee | Wordt gebruikt om meer dan één waarde voor een uitvoer terug te geven. Geef **waarde** of **kopie op**. Zie [Uitvoeriteratie in Azure Resource Manager-sjablonen](copy-outputs.md)voor meer informatie. |

Zie Uitvoer in de [sjabloon Azure Resource Manager](template-outputs.md)voor voorbeelden van het gebruik van uitvoer.

<a id="comments" />

## <a name="comments-and-metadata"></a>Opmerkingen en metagegevens

Je hebt een paar opties voor het toevoegen van opmerkingen en metagegevens aan je sjabloon.

### <a name="comments"></a>Opmerkingen

Voor inline-opmerkingen `//` u `/* ... */` een van beide gebruiken of deze syntaxis werkt niet met alle hulpprogramma's. U de portalsjablooneditor niet gebruiken om aan sjablonen te werken met inline-opmerkingen. Als u deze stijl van commentaar toevoegt, moet u ervoor zorgen dat de hulpprogramma's die u gebruikt ondersteuning inline JSON-opmerkingen gebruiken.

> [!NOTE]
> Als u sjablonen met opmerkingen wilt implementeren met Azure CLI met versie `--handle-extended-json-format` 2.3.0 of ouder, moet u de switch gebruiken.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

In Visual Studio Code kan de [azure resource manager tools-extensie](use-vs-code-to-create-template.md#install-resource-manager-tools-extension) automatisch de sjabloon Resource Manager detecteren en de taalmodus dienovereenkomstig wijzigen. Als u **Azure Resource Manager-sjabloon** ziet in de rechterbenedenhoek van VS-code, u de inline-opmerkingen gebruiken. De inline-opmerkingen worden niet langer gemarkeerd als ongeldig.

![Sjabloonmodus visual studiocode Azure Resource Manager](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metagegevens

U `metadata` een object bijna overal in uw sjabloon toevoegen. Resource Manager negeert het object, maar uw JSON-editor kan u waarschuwen dat de eigenschap niet geldig is. Definieer in het object de eigenschappen die u nodig hebt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Voeg **voor**parameters `metadata` een `description` object met een eigenschap toe.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Bij het implementeren van de sjabloon via de portal wordt de tekst die u in de beschrijving opgeeft, automatisch gebruikt als tip voor die parameter.

![Parametertip weergeven](./media/template-syntax/show-parameter-tip.png)

Voeg **resources**voor resources `comments` een element of een metagegevensobject toe. In het volgende voorbeeld wordt zowel een opmerkingenelement als een metagegevensobject weergegeven.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Voeg **voor uitvoer**een metagegevensobject toe aan de uitvoerwaarde.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

U een metagegevensobject niet toevoegen aan door de gebruiker gedefinieerde functies.

## <a name="multi-line-strings"></a>Tekenreeksen met meerdere regels

U een tekenreeks in meerdere regels opsplitsen. Zie bijvoorbeeld de locatieeigenschap en een van de opmerkingen in het volgende JSON-voorbeeld.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Als u sjablonen met tekenreeksen met meerdere regels wilt implementeren met Azure CLI `--handle-extended-json-format` met versie 2.3.0 of ouder, moet u de switch gebruiken.

## <a name="next-steps"></a>Volgende stappen

* Zie de [Azure-snelstartsjablonen](https://azure.microsoft.com/documentation/templates/) voor volledige sjablonen voor verschillende soorten oplossingen.
* Zie [Azure Resource Manager Template-functies](template-functions.md)voor meer informatie over de functies die u vanuit een sjabloon gebruiken.
* Zie [Gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md)als u tijdens de implementatie meerdere sjablonen wilt combineren.
* Zie Aanbevolen procedures voor [Azure Resource Manager-sjabloon voor](template-best-practices.md)aanbevelingen over het maken van sjablonen.
* Zie [Azure Resource Manager-sjablonen ontwikkelen voor cloudconsistentie voor](templates-cloud-consistency.md)aanbevelingen voor het maken van Resource Manager-sjablonen die u gebruiken voor alle Azure-omgevingen en Azure Stack.
