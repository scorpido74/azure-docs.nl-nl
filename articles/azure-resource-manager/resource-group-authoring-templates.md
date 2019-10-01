---
title: Structuur en syntaxis van Azure Resource Manager-sjabloon | Microsoft Docs
description: Hierin worden de structuur en eigenschappen van Azure Resource Manager sjablonen beschreven met declaratieve JSON-syntaxis.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: tomfitz
ms.openlocfilehash: b6d479935bc9e4bd731b93d3e027644b9ca4dbe0
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694978"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen

In dit artikel wordt de structuur van een Azure Resource Manager sjabloon beschreven. Het biedt de verschillende secties van een sjabloon en de eigenschappen die beschikbaar zijn in deze secties.

Dit artikel is bedoeld voor gebruikers die bekend zijn met Resource Manager-sjablonen. Het bevat gedetailleerde informatie over de structuur van de sjabloon. Zie [Azure Resource Manager sjablonen](template-deployment-overview.md)als u een sjabloon wilt maken.

## <a name="template-format"></a>Sjabloon indeling

In de eenvoudigste structuur heeft een sjabloon de volgende elementen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| De naam van element | Vereist | Description |
|:--- |:--- |:--- |
| $schema |Ja |Locatie van het JSON-schema bestand waarin de versie van de sjabloon taal wordt beschreven.<br><br> Voor implementaties van resource groepen gebruikt u:`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Gebruik voor implementaties van abonnementen:`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Ja |De versie van de sjabloon (bijvoorbeeld 1.0.0.0). U kunt een wille keurige waarde voor dit element opgeven. Gebruik deze waarde als u belang rijke wijzigingen in uw sjabloon wilt documenteren. Wanneer u resources implementeert met behulp van de sjabloon, kan deze waarde worden gebruikt om ervoor te zorgen dat de juiste sjabloon wordt gebruikt. |
| apiProfile |Nee | Een API-versie die fungeert als een verzameling van API-versies voor resource typen. Gebruik deze waarde om te voor komen dat u API-versies opgeeft voor elke resource in de sjabloon. Wanneer u een API-profiel versie opgeeft en geen API-versie voor het resource type opgeeft, gebruikt Resource Manager de API-versie voor het bron type dat in het profiel is gedefinieerd.<br><br>De API-profiel eigenschap is vooral handig bij het implementeren van een sjabloon in verschillende omgevingen, zoals Azure Stack en wereld wijd Azure. Gebruik de versie van het API-profiel om ervoor te zorgen dat uw sjabloon automatisch versies gebruikt die in beide omgevingen worden ondersteund. Zie [API-profiel](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)voor een lijst met de huidige API-profiel versies en de bronnen-API-versies die in het profiel zijn gedefinieerd.<br><br>Zie [versies bijhouden met API-profielen](templates-cloud-consistency.md#track-versions-using-api-profiles)voor meer informatie. |
| [parameters](#parameters) |Nee |Waarden die worden gegeven bij het uitvoeren van de implementatie om de resource-implementatie aan te passen. |
| [variabelen](#variables) |Nee |Waarden die worden gebruikt als JSON-fragmenten in de sjabloon voor het vereenvoudigen van sjabloon taal expressies. |
| [vervullen](#functions) |Nee |Door de gebruiker gedefinieerde functies die beschikbaar zijn in de sjabloon. |
| [resources](#resources) |Ja |Resource typen die worden geïmplementeerd of bijgewerkt in een resource groep of-abonnement. |
| [uitvoer](#outputs) |Nee |Waarden die worden geretourneerd na de implementatie. |

Elk element heeft eigenschappen die u kunt instellen. In dit artikel worden de secties van de sjabloon uitvoeriger beschreven.

## <a name="parameters"></a>Parameters

In het gedeelte para meters van de sjabloon geeft u op welke waarden u kunt invoeren bij het implementeren van de resources. U bent beperkt tot 256 para meters in een sjabloon. U kunt het aantal para meters verminderen door objecten te gebruiken die meerdere eigenschappen bevatten.

De beschik bare eigenschappen voor een para meter zijn:

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

| De naam van element | Vereist | Description |
|:--- |:--- |:--- |
| para meter-naam |Ja |De naam van de para meter. Moet een geldige JavaScript-id. |
| type |Ja |Type parameter waarde. De toegestane typen en waarden zijn **String**, **securestring**, **int**, **BOOL**, **object**, **secureObject**en **array**. Zie [gegevens typen](#data-types). |
| defaultValue |Nee |De standaard waarde voor de para meter, als er geen waarde wordt gegeven voor de para meter. |
| allowedValues |Nee |Matrix van toegestane waarden voor de para meter om ervoor te zorgen dat de juiste waarde wordt gegeven. |
| minValue |Nee |De minimum waarde voor de para meters van het type int, deze waarde is inclusief. |
| maxValue |Nee |De maximum waarde voor de para meters van het type int, deze waarde is inclusief. |
| minLength |Nee |De minimale lengte voor de para meters String, Secure string en array type is inclusief. |
| Lengte |Nee |De maximale lengte van de para meters voor teken reeksen, beveiligde teken reeksen en matrix type is inclusief. |
| description |Nee |Beschrijving van de para meter die wordt weer gegeven voor gebruikers via de portal. Zie [opmerkingen in sjablonen](#comments)voor meer informatie. |

Zie [para meters in azure Resource Manager Templates](template-parameters.md)voor voor beelden van het gebruik van para meters.

### <a name="data-types"></a>Gegevenstypen

Voor gehele getallen die als inline-para meters worden door gegeven, kan het bereik van waarden worden beperkt door de SDK of het opdracht regel programma dat u voor implementatie gebruikt. Als u bijvoorbeeld Power shell gebruikt voor het implementeren van een sjabloon, kunnen integerwaarden variëren van-2147483648 tot 2147483647. Als u deze beperking wilt vermijden, geeft u grote waarden voor geheel getal op in een [parameter bestand](resource-manager-parameter-files.md). De resource typen hebben hun eigen limieten voor eigenschappen van gehele getallen.

Wanneer u Boole-waarden en integerwaarden in uw sjabloon opgeeft, plaatst u de waarde niet tussen aanhalings tekens. Begin-en eind teken reeks waarden met dubbele aanhalings tekens.

Objecten beginnen met een accolade links en eindigen met een haakje sluiten. Matrices beginnen met een haakje openen en eindigen met een haakje sluiten.

Beveiligde teken reeksen en beveiligde objecten kunnen niet worden gelezen na het implementeren van de resource.

Zie [notaties van het type para meter](resource-manager-parameter-files.md#parameter-type-formats)voor voor beelden van het opmaken van gegevens typen.

## <a name="variables"></a>Variabelen

In de sectie variabelen kunt u waarden maken die in de hele sjabloon kunnen worden gebruikt. U hoeft geen variabelen te definiëren, maar ze vereenvoudigen uw sjabloon vaak door complexe expressies te reduceren.

In het volgende voor beeld ziet u de beschik bare opties voor het definiëren van een variabele:

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

Zie voor meer informatie `copy` over het gebruik van om verschillende waarden voor een variabele te maken de [variabele iteratie](resource-group-create-multiple.md#variable-iteration).

Zie [variabelen in azure Resource Manager sjabloon](template-variables.md)voor voor beelden van het gebruik van variabelen.

## <a name="functions"></a>Functies

U kunt binnen uw sjabloon uw eigen functies maken. Deze functies zijn beschikbaar voor gebruik in uw sjabloon. Normaal gesp roken definieert u gecompliceerde expressies die u niet wilt herhalen in uw sjabloon. U maakt de door de gebruiker gedefinieerde functies op basis van expressies en [functies](resource-group-template-functions.md) die in sjablonen worden ondersteund.

Bij het definiëren van een gebruikers functie gelden enkele beperkingen:

* De functie heeft geen toegang tot variabelen.
* De functie kan alleen para meters gebruiken die in de functie zijn gedefinieerd. Wanneer u de [functie para meters](resource-group-template-functions-deployment.md#parameters) in een door de gebruiker gedefinieerde functie gebruikt, bent u beperkt tot de para meters voor die functie.
* De functie kan geen andere door de gebruiker gedefinieerde functies aanroepen.
* De functie kan de [functie Reference](resource-group-template-functions-resource.md#reference)niet gebruiken.
* Para meters voor de functie kunnen geen standaard waarden hebben.

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

| De naam van element | Vereist | Description |
|:--- |:--- |:--- |
| naamruimte |Ja |Naam ruimte voor de aangepaste functies. Gebruik om naam conflicten te voor komen met sjabloon functies. |
| functie naam |Ja |De naam van de aangepaste functie. Wanneer u de functie aanroept, moet u de naam van de functie combi neren met de naam ruimte. Als u bijvoorbeeld een functie met de naam UniqueName in de naam ruimte Contoso wilt aanroepen, gebruikt u `"[contoso.uniqueName()]"`. |
| para meter-naam |Nee |De naam van de para meter die moet worden gebruikt binnen de aangepaste functie. |
| para meter-waarde |Nee |Type parameter waarde. De toegestane typen en waarden zijn **String**, **securestring**, **int**, **BOOL**, **object**, **secureObject**en **array**. |
| uitvoer type |Ja |Type van de uitvoerwaarde. Uitvoer waarden ondersteunen dezelfde typen als de invoer parameters van de functie. |
| uitvoer waarde |Ja |De taal expressie voor de sjabloon die wordt geëvalueerd en geretourneerd door de functie. |

Zie door de [gebruiker gedefinieerde functies in azure Resource Manager sjabloon](template-user-defined-functions.md)voor voor beelden van het gebruik van aangepaste functies.

## <a name="resources"></a>Resources

In de sectie Resources definieert u de resources die worden geïmplementeerd of bijgewerkt.

U definieert resources met de volgende structuur:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
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
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| De naam van element | Vereist | Description |
|:--- |:--- |:--- |
| condition | Nee | Een Booleaanse waarde die aangeeft of de resource wordt ingericht tijdens deze implementatie. Wanneer `true`wordt de bron gemaakt tijdens de implementatie. Wanneer `false`wordt de resource overgeslagen voor deze implementatie. Zie [voor waarde](conditional-resource-deployment.md). |
| apiVersion |Ja |De versie van de REST API die moet worden gebruikt voor het maken van de resource. Zie [Naslag informatie over sjablonen](/azure/templates/)om beschik bare waarden te bepalen. |
| type |Ja |Het type van de resource. Deze waarde is een combi natie van de naam ruimte van de resource provider en het resource type (zoals **micro soft. Storage/Storage accounts**). Zie [Naslag informatie over sjablonen](/azure/templates/)om beschik bare waarden te bepalen. Voor een onderliggende resource is de notatie van het type afhankelijk van of het is genest binnen de bovenliggende resource of buiten de bovenliggende resource is gedefinieerd. Zie [naam en type voor onderliggende resources instellen](child-resource-name-type.md). |
| name |Ja |Naam van de resource. De naam moet volgen op de URI-onderdeel beperkingen die zijn gedefinieerd in RFC3986. Azure-Services die de resource naam beschikbaar maken voor externe partijen, valideren de naam om ervoor te zorgen dat het geen poging is om een andere identiteit te vervalsen. Voor een onderliggende resource is de notatie van de naam afhankelijk van of deze is genest binnen de bovenliggende resource of buiten de bovenliggende resource is gedefinieerd. Zie [naam en type voor onderliggende resources instellen](child-resource-name-type.md). |
| location |Varieert |Ondersteunde geo-locaties van de gegeven bron. U kunt een van de beschik bare locaties selecteren, maar normaal gesp roken is het een goed idee om er een te kiezen die zich dicht bij uw gebruikers bevindt. Doorgaans is het zinvol om resources te plaatsen die met elkaar in dezelfde regio communiceren. Voor de meeste resource typen is een locatie vereist, maar sommige typen (zoals een roltoewijzing) vereisen geen locatie. Zie [resource locatie instellen](resource-location.md). |
| tags |Nee |Tags die zijn gekoppeld aan de resource. Pas Tags toe om de resources in uw abonnement logisch te organiseren. |
| Aantekeningen |Nee |Uw notities voor het documenteren van de resources in uw sjabloon. Zie [opmerkingen in sjablonen](resource-group-authoring-templates.md#comments)voor meer informatie. |
| kopiëren |Nee |Als er meer dan één exemplaar nodig is, wordt het aantal resources dat moet worden gemaakt. De standaard modus is parallel. Geef de seriële modus op als u niet wilt dat alle of de resources op hetzelfde moment worden geïmplementeerd. Zie [meerdere exemplaren van resources maken in azure Resource Manager](resource-group-create-multiple.md)voor meer informatie. |
| dependsOn |Nee |Resources die moeten worden geïmplementeerd voordat deze resource wordt geïmplementeerd. Resource Manager evalueert de afhankelijkheden tussen resources en implementeert deze in de juiste volg orde. Wanneer resources niet afhankelijk zijn van elkaar, worden ze parallel geïmplementeerd. De waarde kan een door komma's gescheiden lijst zijn van een resource naam of unieke id van een resource. Alleen een lijst met resources die in deze sjabloon zijn geïmplementeerd. Resources die niet in deze sjabloon zijn gedefinieerd, moeten al bestaan. Vermijd overbodige afhankelijkheden omdat deze uw implementatie kunnen vertragen en circulaire afhankelijkheden kan maken. Zie [afhankelijkheden definiëren in azure Resource Manager sjablonen](resource-group-define-dependencies.md)voor hulp bij het instellen van afhankelijkheden. |
| properties |Nee |Resource-specifieke configuratie-instellingen. De waarden voor de eigenschappen zijn gelijk aan de waarden die u opgeeft in de hoofd tekst van de aanvraag voor de REST API bewerking (PUT-methode) om de resource te maken. U kunt ook een Kopieer matrix opgeven om meerdere exemplaren van een eigenschap te maken. Zie [Naslag informatie over sjablonen](/azure/templates/)om beschik bare waarden te bepalen. |
| sku | Nee | Sommige resources staan waarden toe die de te implementeren SKU definiëren. U kunt bijvoorbeeld het type redundantie voor een opslag account opgeven. |
| soort | Nee | Bij sommige resources is een waarde toegestaan die het type resource definieert dat u implementeert. U kunt bijvoorbeeld het type Cosmos DB opgeven dat u wilt maken. |
| plan | Nee | In sommige resources zijn waarden toegestaan waarmee het plan wordt gedefinieerd dat moet worden geïmplementeerd. U kunt bijvoorbeeld de Marketplace-installatie kopie voor een virtuele machine opgeven. | 
| bronnen |Nee |Onderliggende bronnen die afhankelijk zijn van de resource die wordt gedefinieerd. Alleen resource typen opgeven die zijn toegestaan voor het schema van de bovenliggende resource. De afhankelijkheid van de bovenliggende resource is niet geïmpliceerd. Deze afhankelijkheid moet expliciet worden gedefinieerd. Zie [naam en type voor onderliggende resources instellen](child-resource-name-type.md). |

## <a name="outputs"></a>outputs

In de sectie uitvoer geeft u waarden die zijn geretourneerd na de implementatie. Normaal gesp roken retourneert u waarden van resources die zijn geïmplementeerd.

Het volgende voorbeeld ziet u de structuur van de uitvoerdefinitie van een:

```json
"outputs": {
  "<output-name>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| De naam van element | Vereist | Description |
|:--- |:--- |:--- |
| uitvoer naam |Ja |De naam van de uitvoerwaarde. Moet een geldige JavaScript-id. |
| condition |Nee | Booleaanse waarde die aangeeft of deze uitvoer waarde wordt geretourneerd. Wanneer `true`is de waarde opgenomen in de uitvoer voor de implementatie. Wanneer `false`wordt de uitvoer waarde overgeslagen voor deze implementatie. Wanneer deze niet is opgegeven, is `true`de standaard waarde. |
| type |Ja |Type van de uitvoerwaarde. Uitvoerwaarden ondersteuning van de dezelfde typen als sjabloon invoerparameters die zijn opgegeven. Als u **securestring** opgeeft voor het uitvoer type, wordt de waarde niet weer gegeven in de implementatie geschiedenis en kan deze niet worden opgehaald uit een andere sjabloon. Als u een geheime waarde in meer dan één sjabloon wilt gebruiken, slaat u het geheim op in een Key Vault en verwijst u naar het geheim in het parameter bestand. Zie [Azure Key Vault gebruiken om de waarde van een beveiligde para meter door te geven tijdens de implementatie](resource-manager-keyvault-parameter.md)voor meer informatie. |
| value |Ja |De sjabloontaalexpressie dat wordt geëvalueerd en geretourneerd als de uitvoerwaarde. |

Zie [uitvoer in azure Resource Manager sjabloon](template-outputs.md)voor voor beelden van het gebruik van uitvoer.

<a id="comments" />

## <a name="comments-and-metadata"></a>Opmerkingen en meta gegevens

U hebt een aantal opties voor het toevoegen van opmerkingen en meta gegevens aan uw sjabloon.

U kunt een `metadata` object bijna overal in uw sjabloon toevoegen. De Resource Manager negeert het object, maar uw JSON-editor kan u waarschuwen dat de eigenschap niet geldig is. In het object definieert u de eigenschappen die u nodig hebt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Voor **para meters**voegt `metadata` u een object `description` toe met een eigenschap.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Wanneer u de sjabloon via de portal implementeert, wordt de tekst die u in de beschrijving opgeeft automatisch gebruikt als tip voor die para meter.

![Parameter tip weer geven](./media/resource-group-authoring-templates/show-parameter-tip.png)

Voor **resources**voegt u een `comments` -element of een meta gegevens object toe. In het volgende voor beeld ziet u een comments-element en een meta gegevens object.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
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

Voeg voor **uitvoer**een meta gegevens object toe aan de uitvoer waarde.

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

U kunt geen meta gegevens object toevoegen aan door de gebruiker gedefinieerde functies.

Voor inline opmerkingen kunt u gebruiken `//` , maar deze syntaxis is niet geschikt voor alle hulpprogram ma's. U kunt Azure CLI niet gebruiken om de sjabloon te implementeren met inline opmerkingen. En u kunt de Portal sjabloon editor niet gebruiken voor het werken met sjablonen met inline opmerkingen. Als u deze stijl van een opmerking toevoegt, moet u ervoor zorgen dat de hulpprogram ma's die u gebruikt ondersteuning bieden voor inline JSON-opmerkingen.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

In VS code kunt u de taal modus instellen op JSON met opmerkingen. De inline opmerkingen worden niet meer als ongeldig gemarkeerd. De modus wijzigen:

1. De selectie van de taal modus openen (CTRL + K M)

1. Selecteer **JSON met opmerkingen**.

   ![Taal modus selecteren](./media/resource-group-authoring-templates/select-json-comments.png)

## <a name="next-steps"></a>Volgende stappen

* Zie de [Azure-snelstartsjablonen](https://azure.microsoft.com/documentation/templates/) voor volledige sjablonen voor verschillende soorten oplossingen.
* Zie voor meer informatie over de functies die u uit in een sjabloon gebruiken kunt [Azure Resource Manager-sjabloonfuncties](resource-group-template-functions.md).
* Zie [gekoppelde sjablonen gebruiken met Azure Resource Manager](resource-group-linked-templates.md)voor het combi neren van verschillende sjablonen tijdens de implementatie.
* Zie voor aanbevelingen voor het maken van sjablonen [Azure Resource Manager best practices](template-best-practices.md)voor sjablonen.
* Zie [Azure Resource Manager sjablonen voor Cloud consistentie ontwikkelen](templates-cloud-consistency.md)voor aanbevelingen voor het maken van Resource Manager-sjablonen die u kunt gebruiken in alle Azure-omgevingen en Azure stack.
