---
title: Sjablonen opnieuw gebruiken voor verschillende Clouds
description: Ontwikkel Azure Resource Manager-sjablonen die consistent worden gewerkt voor verschillende cloudomgevingen. Maak een nieuwe of bestaande sjablonen voor Azure Stack bijwerken.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: 0e4dd67e1686d3b63376138d1be2d1f7df4bb41a
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290645"
---
# <a name="develop-azure-resource-manager-templates-for-cloud-consistency"></a>Azure Resource Manager-sjablonen voor de consistentie van de cloud ontwikkelen

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Een belangrijk voordeel van Azure is consistentie. Infrastructuurontwikkelingsinvesteringen voor één locatie kunnen worden hergebruikt in een andere. Een sjabloon kunt u uw implementaties consistente en herhaalbare datacenteromgevingen, met inbegrip van de globale Azure, Azure soevereine clouds en Azure Stack. Als u wilt gebruiken sjablonen in verschillende clouds, moet u echter rekening houden met cloud-specifieke afhankelijkheden, zoals deze handleiding wordt uitgelegd.

Microsoft biedt een intelligente, bedrijfsklare cloudservices op vele locaties, met inbegrip van:

* Het wereldwijde Azure-platform wordt ondersteund door een groeiend netwerk van door Microsoft beheerde datacenters in regio's over de hele wereld.
* Geïsoleerde onafhankelijke Clouds, zoals Azure Duitsland, Azure Government en Azure China 21Vianet. Onafhankelijke clouds bieden een consistent platform met dezelfde handige functies die globale Azure-klanten toegang tot hebben de meeste.
* Azure Stack, een hybride cloudplatform waarmee u Azure-services leveren vanuit het datacenter van uw organisatie. Ondernemingen kunnen Azure Stack instellen in hun eigen datacenters of Azure-Services van serviceproviders, Azure Stack uitgevoerd in hun faciliteiten (ook bekend als gehoste regio's) gebruiken.

De spil van alle deze clouds biedt Azure Resource Manager een API waarmee een groot aantal gebruikersinterfaces om te communiceren met het Azure-platform. Deze API biedt krachtige mogelijkheden van infrastructuur als code. Elk type resource dat beschikbaar is op het Azure-cloud-platform kan worden geïmplementeerd en geconfigureerd met Azure Resource Manager. U kunt met één enkele sjabloon implementeren en configureren uw volledige toepassing naar een operationele end-status.

![Azure-omgevingen](./media/templates-cloud-consistency/environments.png)

De consistentie van de globale Azure, de onafhankelijke clouds, gehoste clouds en een cloud in uw datacenter kunt u profiteren van Azure Resource Manager. Bij het instellen van de sjabloon op basis van resource-implementatie en configuratie, kunt u uw investeringen in ontwikkeling hergebruiken in deze clouds.

Echter, zelfs als de globale, onafhankelijke, gehoste, en hybride clouds bieden consistente-services, niet alle clouds identiek zijn. Als gevolg hiervan kunt u een sjabloon maken met afhankelijkheden op functies die beschikbaar zijn in een specifieke cloud.

De rest van deze handleiding beschrijft de gebieden te overwegen bij het plannen van het ontwikkelen van nieuwe of het bijwerken bestaande Azure Resource Manager-sjablonen voor Azure Stack. Uw controlelijst moet in het algemeen zijn onder andere de volgende items:

* Controleer of de functies, -eindpunten, services en andere resources in uw sjabloon beschikbaar in de doellocaties voor implementatie zijn.
* Store geneste sjablonen en artefacten configuratie op toegankelijk locaties, ervoor te zorgen dat de toegang tot verschillende clouds.
* Dynamische verwijzingen gebruiken in plaats van hard-coding koppelingen en elementen.
* Zorg ervoor dat de sjabloonparameters die u gebruiken in de doel-clouds werken.
* Controleren of de resource-specifieke eigenschappen beschikbaar zijn de doel-clouds.

Zie voor een inleiding tot Azure Resource Manager-sjablonen, [sjabloonimplementatie](overview.md).

## <a name="ensure-template-functions-work"></a>Zorg ervoor dat sjabloonfuncties werken

De algemene syntaxis van een Resource Manager-sjabloon is JSON. Sjablonen gebruiken een hoofdverzameling van JSON, uitbreiding van de syntaxis bij met expressies en functies. De sjabloon taal processor wordt regelmatig bijgewerkt ter ondersteuning van aanvullende sjabloonfuncties. Zie voor een gedetailleerde uitleg van de beschikbare sjabloonfuncties [Azure Resource Manager-sjabloonfuncties](template-functions.md).

Nieuwe functies van sjablonen die zijn geïntroduceerd in Azure Resource Manager zijn niet onmiddellijk beschikbaar in soevereine clouds of Azure Stack. Voor het implementeren van een sjabloon is, moeten alle functies waarnaar wordt verwezen in de sjabloon die beschikbaar zijn in de doelcloud.

Mogelijkheden van Azure Resource Manager zullen altijd eerst naar globale Azure worden geïntroduceerd. U kunt de volgende PowerShell-script gebruiken om te controleren of geïntroduceerde sjabloonfuncties ook beschikbaar in Azure Stack:

1. Maken van een kloon van de GitHub-opslagplaats: [ https://github.com/marcvaneijk/arm-template-functions ](https://github.com/marcvaneijk/arm-template-functions).

1. Zodra u een lokale kloon van de opslagplaats hebt, maak verbinding naar de bestemming van Azure Resource Manager met PowerShell.

1. Importeer de module psm1 en voer de cmdlet Test-AzureRmTemplateFunctions:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Het script worden meerdere, sjablonen, elk met alleen unieke sjabloonfuncties geminimaliseerd. De uitvoer van het script rapporteert de sjabloonfuncties ondersteunde en niet beschikbaar.

## <a name="working-with-linked-artifacts"></a>Werken met gekoppelde artefacten

Een sjabloon kunt bevatten verwijzingen naar gekoppelde artefacten en bevatten een implementatie-resource die is gekoppeld aan een andere sjabloon. De gekoppelde sjablonen (ook wel geneste sjabloon genoemd) worden door Resource Manager tijdens runtime opgehaald. Een sjabloon kunt u ook verwijzingen naar artefacten voor extensies van virtuele machines (VM) bevatten. Deze artefacten worden opgehaald door de VM-extensie die worden uitgevoerd op de virtuele machine voor de configuratie van de VM-extensie tijdens de sjabloonimplementatie van de.

De volgende secties beschrijven de overwegingen voor de consistentie van de cloud bij het ontwikkelen van sjablonen die artefacten die zich buiten de sjabloon voor de belangrijkste implementatie bevatten.

### <a name="use-nested-templates-across-regions"></a>Gebruik van geneste sjablonen in regio 's

Sjablonen kunnen worden opgedeeld in kleine, herbruikbare sjablonen, die allemaal een bepaald doel en kunnen worden hergebruikt binnen implementatiescenario's. Voor het uitvoeren van een implementatie, moet u één enkele sjabloon bekend als de hoofd- of -master sjabloon opgeven. Het geeft de resources om te implementeren, zoals virtuele netwerken, VM's en web-apps. De belangrijkste sjabloon kan ook een koppeling naar een andere sjabloon, wat betekent dat u kunt sjablonen nesten bevatten. Een geneste sjabloon bevat ook koppelingen naar andere sjablonen. U kunt maximaal vijf niveaus diep genest.

De volgende code toont hoe de templateLink parameter verwijst naar een geneste sjabloon:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure Resource Manager evalueert de belangrijkste sjabloon tijdens runtime en worden opgehaald en evalueert elk geneste sjablonen. Nadat alle geneste sjablonen worden opgehaald, de sjabloon wordt afgevlakt en verdere verwerking wordt gestart.

### <a name="make-linked-templates-accessible-across-clouds"></a>Gekoppelde sjablonen toegankelijk te maken in verschillende clouds

Houd rekening met waar en hoe voor het opslaan van een gekoppelde sjablonen die u gebruiken. Tijdens runtime, Azure Resource Manager worden opgehaald, en daarom vereist directe toegang tot, alle gekoppelde sjablonen. Een normaal worden bij GitHub gebruiken voor het opslaan van de geneste sjablonen. Een GitHub-opslagplaats kan bestanden die toegankelijk openbaar via een URL zijn bevatten. Hoewel deze methode goed voor de openbare cloud en de onafhankelijke clouds werkt, is er mogelijk een Azure Stack-omgeving zich bevindt op een bedrijfsnetwerk bevindt of op een niet-verbonden externe locatie, zonder alle uitgaande internettoegang. In deze gevallen mislukken Azure Resource Manager om op te halen van de geneste sjablonen.

Een betere manier voor cross-cloud-implementaties is voor het opslaan van uw gekoppelde sjablonen in een locatie die toegankelijk is voor de doelcloud. In het ideale geval worden alle implementatie-artefacten onderhouden in en geïmplementeerd vanuit een continue integratie/continue ontwikkelingspijplijn (CI/CD). U kunt ook geneste sjablonen opslaan in een blob storage-container waarin Azure Resource Manager kunt ze wel ophalen.

Omdat de blob-opslag in elke cloud gebruikmaakt van een volledig gekwalificeerde domeinnaam (FQDN) van ander eindpunt, moet u de sjabloon configureren met de locatie van de gekoppelde sjablonen met twee parameters. Parameters kunnen invoer van de gebruiker tijdens de implementatie accepteren. Sjablonen zijn meestal gemaakt en gedeeld door meerdere personen, zodat een best practice is het gebruik van een standaardnaam voor deze parameters. Naamgevingsregels helpen meer herbruikbare sjablonen maken voor regio's, clouds en auteurs.

In de volgende code `_artifactsLocation` wordt gebruikt om te verwijzen naar een enkele locatie, met alle artefacten met betrekking tot implementatie. U ziet dat er een standaardwaarde is opgegeven. Tijdens de implementatie als er geen invoer waarde is opgegeven voor `_artifactsLocation`, de standaardwaarde wordt gebruikt. De `_artifactsLocationSasToken` wordt gebruikt als invoer voor de `sasToken`. De standaardwaarde moet een lege tekenreeks voor scenario's waarbij de `_artifactsLocation` is niet beveiligd, bijvoorbeeld een openbare GitHub-opslagplaats.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

In de sjabloon koppelingen worden gegenereerd door een combinatie van de basis-URI (van de `_artifactsLocation` parameter) met een pad relatief ten opzichte van artefacten en de `_artifactsLocationSasToken`. De volgende code laat zien hoe de koppeling naar de geneste sjabloon met behulp van de sjabloonfunctie uri opgeven:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Met behulp van deze benadering, de standaardwaarde voor de `_artifactsLocation` parameter wordt gebruikt. Als de gekoppelde sjablonen worden opgehaald uit een andere locatie moeten, de parameter invoer kan worden gebruikt tijdens de implementatie voor de onderdrukking van de standaardwaarde, geen wijziging in de sjabloon zelf nodig is.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>_ArtifactsLocation gebruiken in plaats van hardcoderen koppelingen

Naast het wordt gebruikt voor geneste sjablonen, de URL in de `_artifactsLocation` parameter wordt gebruikt als basis voor alle artefacten van een sjabloon voor de implementatie gerelateerde. Aantal VM-extensies bevatten een koppeling naar een script die buiten de sjabloon is opgeslagen. U moet niet hardcoderen we de koppelingen voor deze uitbreidingen. De Custom Script en PowerShell DSC-uitbreidingen kunnen bijvoorbeeld een koppeling naar een externe scripts op GitHub, zoals wordt weergegeven:

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

Hardcoderen de koppelingen aan het script mogelijk wordt voorkomen dat de sjabloon met succes implementeren naar een andere locatie. Tijdens het configureren van de virtuele machine op de VM-agent die wordt uitgevoerd binnen de virtuele machine initieert een download van de scripts die zijn gekoppeld aan de VM-extensie en slaat vervolgens de scripts op de lokale schijf van de virtuele machine. Deze aanpak functies zoals de koppelingen geneste sjabloon die eerder zijn uitgelegd in de sectie 'Met geneste sjablonen in regio's '.

Resource Manager haalt geneste sjablonen tijdens runtime. Voor VM-extensies voor wordt het ophalen van alle externe artefacten uitgevoerd door de VM-agent. Naast de andere initiator van het artefact ophalen is de oplossing in het Sjabloondefinitie van de hetzelfde. Gebruik van de parameter _artifactsLocation met een standaardwaarde van het basispad, waar alle artefacten (met inbegrip van de VM-extensie scripts) worden opgeslagen en de `_artifactsLocationSasToken` parameter voor de invoer voor de sasToken.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Kan de absolute URI zijn die van een artefact, is de voorkeursmethode voor het gebruik van de sjabloonfunctie uri in plaats van de functie concat sjabloon. Deze functionaliteit in de sjabloon wordt vastgelegd koppelingen naar de scripts in de VM-extensie vervangen door de sjabloonfunctie uri, geconfigureerd voor de consistentie van de cloud.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

Met deze methode kunnen alle implementatie-artefacten, met inbegrip van configuratiescripts, worden opgeslagen op dezelfde locatie met de sjabloon zelf. Als u de locatie van alle koppelingen wilt wijzigen, hoeft u alleen een andere basis-URL voor de _artifactsLocation-para meters_op te geven.

## <a name="factor-in-differing-regional-capabilities"></a>Rekening houden verschillende regionale mogelijkheden

Met de flexibele ontwikkeling en een continue stroom van updates en nieuwe services toegevoegd aan Azure, [regio kunnen verschillen](https://azure.microsoft.com/regions/services/) in de beschikbaarheid van services of updates. Na een rigoureuze interne tests, worden nieuwe services of updates met bestaande services meestal voor een kleine publiek van klanten die deel uitmaken van een validatieprogramma geïntroduceerd. Na de validatie van klanten, zijn de services of updates beschikbaar in een subset van de Azure-regio's, worden geïntroduceerd in meer regio's, uitgerold voor onafhankelijke clouds, en mogelijk beschikbaar gesteld voor Azure Stack-klanten.

Weten dat Azure-regio's en clouds in de beschikbare services verschillen kunnen, kunt u sommige proactieve beslissingen over uw sjablonen. Een goede plaats om te beginnen is door te controleren van de beschikbare resourceproviders voor een cloud. Een resourceprovider ziet u de set met resources en bewerkingen die beschikbaar voor een Azure-service zijn.

Een sjabloon wordt geïmplementeerd en geconfigureerd resources. Een brontype wordt geleverd door een resourceprovider. Bijvoorbeeld, biedt de compute resourceprovider (Microsoft.Compute), meerdere resourcetypen, zoals virtuele machines en availabilitySets. Elke resourceprovider biedt een API naar Azure Resource Manager gedefinieerd door een algemene contract, waardoor een consistente en uniforme ervaring van de ontwerper is voor alle resourceproviders. Echter, een resourceprovider die beschikbaar is in de globale Azure mogelijk niet beschikbaar in een onafhankelijke cloud of een Azure Stack-regio.

![Resourceproviders](./media/templates-cloud-consistency/resource-providers.png)

Om te controleren of de resourceproviders die beschikbaar in een bepaalde cloud zijn, kunt u het volgende script uitvoeren in de Azure command line interface ([CLI](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

U kunt ook de volgende PowerShell-cmdlet gebruiken om te zien van de beschikbare resourceproviders:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Controleer of de versie van alle resourcetypen

Een set eigenschappen is gebruikelijk voor alle brontypen, maar elke resource heeft ook een eigen specifieke eigenschappen. Nieuwe functies en verwante eigenschappen worden toegevoegd aan bestaande resourcetypen soms door een nieuwe API-versie. Een resource in een sjabloon is de eigenschap van een eigen API-version - `apiVersion`. Deze versiebeheer zorgt ervoor dat de resourceconfiguratie van een bestaande in een sjabloon wordt niet beïnvloed door wijzigingen op het platform.

Nieuwe API-versies toegevoegd aan bestaande resourcetypen in de globale Azure direct mogelijk niet beschikbaar in alle regio's, onafhankelijke clouds of Azure Stack. U kunt een lijst van de beschikbare resourceproviders, brontypen en API-versies voor een cloud wilt weergeven, Resource Explorer gebruiken in Azure portal. Zoeken naar Resource Explorer in het menu alle Services. Vouw het knooppunt Providers in Resource Explorer te retourneren van alle beschikbare resourceproviders, hun resourcetypen en API-versies in die cloud.

Als u de beschikbare API-versie voor alle resourcetypen in een bepaalde cloud in de Azure CLI, voer het volgende script:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

U kunt ook de volgende PowerShell-cmdlet gebruiken:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Verwijzen naar de resourcelocaties met een parameter

Een sjabloon wordt altijd geïmplementeerd in een resourcegroep die zich in een regio bevinden. Naast de implementatie van zichzelf heeft elke resource in een sjabloon ook een locatie-eigenschap die u gebruikt om op te geven van de regio voor de implementatie. Voor het ontwikkelen van uw sjabloon voor de consistentie van de cloud, moet u een dynamische manier om te verwijzen naar de resourcelocaties, omdat elke Azure Stack kan unieke locatienamen bevatten. Meestal resources worden geïmplementeerd in dezelfde regio als de resourcegroep, maar ter ondersteuning van scenario's zoals de beschikbaarheid van regio-overschrijdende, kan het nuttig om te verdelen van bronnen tussen regio's zijn.

Hoewel u de regionamen hardcoden bij het opgeven van de resource-eigenschappen in een sjabloon, deze benadering biedt geen garantie dat de sjabloon kan worden geïmplementeerd voor andere Azure Stack-omgeving, omdat de naam van het gebied waarschijnlijk niet bestaat er.

Toevoegen om te voldoen aan verschillende regio's, een invoerparameter-locatie in de sjabloon met een standaardwaarde. De standaardwaarde zal worden gebruikt als er geen waarde is opgegeven tijdens de implementatie.

De sjabloonfunctie `[resourceGroup()]` retourneert een object dat de volgende sleutel/waarde-paren bevat:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Door te verwijzen naar de sleutel van de locatie van het object in de standaardwaarde van de invoerparameter, Azure Resource Manager, tijdens runtime, vervangt de `[resourceGroup().location]` sjabloonfunctie met de naam van de locatie van de resourcegroep waarin de sjabloon wordt geïmplementeerd.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Met deze sjabloonfunctie kunt u de sjabloon op elke cloud implementeren zonder dat de regionamen van de van tevoren weet. Bovendien kan een locatie voor een specifieke bron in de sjabloon afwijken van de locatie voor de resourcegroep. In dit geval kunt u deze configureren met behulp van aanvullende invoerparameters die zijn opgegeven voor die specifieke resource, terwijl de andere resources in dezelfde sjabloon nog steeds gebruik van de invoerparameter van de oorspronkelijke locatie.

### <a name="track-versions-using-api-profiles"></a>Versies met behulp van API-profielen bijhouden

Het kan lastig om alle beschikbare resourceproviders en -gerelateerde API-versies die aanwezig in Azure Stack zijn bij te houden. Bijvoorbeeld op het moment van schrijven, de meest recente versie van de API voor **Microsoft.Compute/availabilitySets** in Azure is `2018-04-01`, terwijl de beschikbare API-versie voor Azure en Azure Stack `2016-03-30`. De algemene API-versie voor **Microsoft.Storage/storageAccounts** gedeeld tussen alle locaties voor Azure en Azure Stack is `2016-01-01`, terwijl de meest recente versie van de API in Azure `2018-02-01`.

Om deze reden Resource Manager het concept van profielen voor API geïntroduceerd naar sjablonen. Zonder de profielen van de API, elke resource in een sjabloon is geconfigureerd met een `apiVersion` element dat de API-versie voor deze specifieke resource beschrijft.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

Een API-versie voor profiel fungeert als een alias voor een enkele API-versie per resourcetype voor Azure en Azure Stack. In plaats van op te geven een API-versie voor elke resource in een sjabloon, u alleen de versie van de API-profiel opgeven in een nieuw root-element met de naam `apiProfile` en laat de `apiVersion` -element voor de afzonderlijke resources.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

De API-profiel zorgt ervoor dat de API-versies beschikbaar op locaties, zijn dus u niet hoeft handmatig te controleren of de apiVersions die beschikbaar in een specifieke locatie zijn. Om te controleren of de API-versies waarnaar wordt verwezen door uw API-profiel in een Azure Stack-omgeving aanwezig zijn, bewaart de Azure Stack-operators up-to-date van de oplossing op basis van het beleid voor ondersteuning. Een systeem is meer dan zes maanden verouderd, het als niet-compatibel wordt beschouwd als de omgeving moet worden bijgewerkt.

De API-profiel is een vereist element in een sjabloon niet. Zelfs als u het element toevoegt, deze wordt alleen gebruikt voor de resources voor welke geen `apiVersion` is opgegeven. Dit element kunt u geleidelijk wijzigingen, maar niet vereist geen wijzigingen in bestaande sjablonen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Eindpunt referenties controleren

Resources kunnen verwijzingen naar andere services zijn op het platform. Een openbaar IP-adres kan bijvoorbeeld een openbare DNS-naam toegewezen hebben. De openbare cloud, onafhankelijke clouds en Azure Stack-oplossingen hebben hun eigen unieke eindpunt-naamruimten. Een resource is alleen een voorvoegsel in de meeste gevallen vereist als invoer in de sjabloon. Tijdens runtime voegt Azure Resource Manager de eindpuntwaarde toe. Sommige waarden eindpunt moeten expliciet worden opgegeven in de sjabloon.

> [!NOTE]
> Geen hardcode eindpunt naamruimten voor het ontwikkelen van sjablonen voor de consistentie van de cloud.

De volgende twee voorbeelden zijn algemene eindpunt naamruimten die expliciet worden opgegeven moeten bij het maken van een resource:

* Storage-accounts (blob, wachtrij, tabel en bestand)
* Verbindingsreeksen voor databases en Azure voor Redis-Cache

Eindpunt-naamruimten kan ook worden gebruikt in de uitvoer van een sjabloon als informatie voor de gebruiker wanneer de implementatie is voltooid. Hier volgen algemene voorbeelden:

* Storage-accounts (blob, wachtrij, tabel en bestand)
* Verbindingsreeksen (MySql, SQL Server, systeemonderhoud plaatsvindt, aangepaste, NotificationHub, ServiceBus, Event hub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* Domeinnaamlabel van een openbaar IP-adres
* Cloudservices

In het algemeen niet vastgelegd eindpunten in een sjabloon. De aanbevolen procedure is het gebruik van de sjabloonfunctie verwijzing naar de eindpunten dynamisch ophalen. Bijvoorbeeld, het eindpunt van de meest voorkomende vastgelegd is de naamruimte van het eindpunt voor storage-accounts. Elk opslagaccount heeft een unieke FQDN-naam die is samengesteld door de naam van de storage-account met de eindpunt-naamruimte. Een blob storage-account met de naam mystorageaccount1 resultaten in verschillende FQDN's, afhankelijk van de cloud:

* **mystorageaccount1.BLOB.Core.Windows.NET** wanneer gemaakt op de wereldwijde Azure-cloud.
* **mystorageaccount1.blob.core.chinacloudapi.cn** wanneer deze wordt gemaakt in de Azure China 21vianet-Cloud.

De volgende verwijzing sjabloonfunctie haalt de eindpunt-naamruimte van de storage resourceprovider:

```json
"diskUri":"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Door het vervangen van de waarde vastgelegd van het eindpunt van de storage-account met de `reference` sjabloonfunctie, kunt u dezelfde sjabloon gebruiken om te implementeren voor verschillende omgevingen is zonder dat u wijzigingen aanbrengt aan de eindpunt-verwijzing.

### <a name="refer-to-existing-resources-by-unique-id"></a>Verwijzen naar bestaande resources door unieke ID

U kunt ook verwijzen naar een bestaande resource in dezelfde of een andere resource-groep, en binnen hetzelfde abonnement of een ander abonnement, binnen dezelfde tenant in de cloud. Als u wilt ophalen van de resource-eigenschappen, moet u de unieke id voor de resource zelf. De `resourceId` sjabloonfunctie haalt de unieke ID van een resource, zoals SQL Server als de volgende code wordt getoond:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Vervolgens kunt u de `resourceId` functie binnen de `reference` sjabloonfunctie om op te halen van de eigenschappen van een database. Het geretourneerde object bevat de `fullyQualifiedDomainName` eigenschap die de volledige eindpunt-waarde bevat. Deze waarde wordt opgehaald tijdens runtime en biedt de cloud omgevingsspecifieke endpoint-naamruimte. Voor het definiëren van de verbindingsreeks zonder hardcoderen de eindpunt-naamruimte, kunt u verwijzen naar de eigenschap van het geretourneerde object rechtstreeks in de verbindingsreeks, zoals wordt weergegeven:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Houd rekening met de resource-eigenschappen

Specifieke resources binnen Azure Stack-omgevingen hebben unieke eigenschappen die u rekening in uw sjabloon houden moet.

### <a name="ensure-vm-images-are-available"></a>Zorg ervoor dat de VM-installatiekopieën beschikbaar zijn

Azure biedt een uitgebreide selectie van VM-installatiekopieën. Deze installatiekopieën worden gemaakt en voorbereid voor implementatie door Microsoft en partners. De afbeeldingen, vormen de basis voor virtuele machines op het platform. Echter, een cloud-consistente sjabloon moet verwijzen naar alleen de beschikbare parameters: in het bijzonder, de uitgever, aanbieding en SKU van de VM-installatiekopieën die beschikbaar zijn voor de globale Azure, Azure soevereine clouds of een Azure Stack-oplossing.

Ophalen van een lijst van de beschikbare installatiekopieën voor virtuele machine op een locatie, moet u de volgende Azure CLI-opdracht uitvoeren:

```azurecli-interactive
az vm image list -all
```

U kunt dezelfde lijst met de Azure PowerShell-cmdlet ophalen [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) en geef de locatie die u wilt dat met de `-Location` parameter. Bijvoorbeeld:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Met deze opdracht duurt enkele minuten om te retourneren van alle beschikbare installatiekopieën in de regio West-Europa van de globale Azure-cloud.

Als u deze VM-installatiekopieën beschikbaar met Azure Stack gemaakt, zou de beschikbare opslag worden gebruikt. Voor zelfs de kleinste schaaleenheid, kunt Azure Stack u de installatiekopieën die u wilt toevoegen aan een omgeving selecteren.

Het volgende codevoorbeeld ziet u een consistent benadering om te verwijzen naar de uitgever, aanbieding en SKU-parameters in uw Azure Resource Manager-sjablonen:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Lokale VM-grootten controleren

Voor het ontwikkelen van uw sjabloon voor de consistentie van de cloud, moet u om te controleren of de gewenste VM-grootte is beschikbaar in alle doelomgevingen. VM-grootten zijn een groepering van de prestatiekenmerken en mogelijkheden. Enkele VM-grootten, is afhankelijk van de hardware waarop de virtuele machine wordt uitgevoerd. Bijvoorbeeld, als u een GPU-geoptimaliseerde VM implementeren wilt, moet de hardware die de hypervisor wordt uitgevoerd beschikken over de hardware-GPU's.

Wanneer Microsoft een nieuwe grootte van virtuele machine die bepaalde hardware-afhankelijkheden heeft introduceert, is de VM-grootte meestal beschikbaar gesteld eerst in een kleine subset van regio's in de Azure-cloud. Later, wordt deze beschikbaar gesteld aan andere regio's en clouds. Als u wilt controleren of dat de VM-grootte in elke cloud die u implementeert op bestaat, kunt u de beschikbare grootten met de volgende Azure CLI-opdracht ophalen:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Voor Azure PowerShell gebruiken:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Zie voor een volledige lijst van beschikbare services, [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Controleren op gebruik van Azure Managed Disks in Azure Stack

Managed disks verwerken de opslag voor een Azure-tenant. In plaats van een storage-account expliciet maken en op te geven de URI voor een virtuele harde schijf (VHD), kunt u beheerde schijven impliciet deze acties uitvoert wanneer u een virtuele machine implementeert. Beschikbaarheid van verbeteren beheerde schijven door alle schijven van virtuele machines in dezelfde beschikbaarheidsset in verschillende opslageenheden. Bovendien kunnen bestaande VHD's worden geconverteerd van Standard naar Premium storage met aanzienlijk minder uitvaltijd.

Hoewel beheerde schijven op de roadmap voor Azure Stack zijn, worden ze momenteel niet ondersteund. Totdat ze zijn, kunt u cloud-consistente sjablonen ontwikkelen voor Azure Stack door expliciet op te geven VHD's met behulp van de `vhd` -element in de sjabloon voor de VM-resource, zoals wordt weergegeven:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Daarentegen, om op te geven de configuratie van een beheerde schijf in een sjabloon, verwijdert de `vhd` -element van de configuratie van de schijf.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

De dezelfde wijzigingen ook van toepassing [gegevensschijven](../../virtual-machines/windows/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Controleer of VM-extensies zijn beschikbaar in Azure Stack

Een andere overweging voor consistentie van de cloud is het gebruik van [extensies voor virtuele machines](../../virtual-machines/windows/extensions-features.md) om de resources binnen een virtuele machine te configureren. Niet alle VM-extensies zijn beschikbaar in Azure Stack. Een sjabloon kunt opgeven van de resources die zijn toegewezen aan de VM-extensie, afhankelijkheden en voorwaarden in de sjabloon maken.

Bijvoorbeeld, als u configureren van een virtuele machine waarop Microsoft SQL Server wordt uitgevoerd wilt, kan de VM-extensie SQL Server configureren als onderdeel van de sjabloonimplementatie. Afvragen wat er gebeurt als de sjabloon voor de implementatie ook een toepassingsserver die is geconfigureerd bevat voor het maken van een database op de virtuele machine waarop SQL Server wordt uitgevoerd. Naast het ook gebruik van een VM-extensie voor de toepassingsservers, kunt u de afhankelijkheid van de toepassingsserver configureren bij de geslaagde resultaat van de SQL Server-VM-extensie-resource. Deze aanpak zorgt ervoor dat de virtuele machine met SQL Server is geconfigureerd en beschikbaar wanneer de toepassingsserver wordt geïnstrueerd om de database te maken.

De declaratieve benadering van de sjabloon kunt u de status van de einde van de resources en hun onderlinge relaties, terwijl het platform zorgt dat de logica die nodig zijn voor de afhankelijkheden definiëren.

#### <a name="check-that-vm-extensions-are-available"></a>Controleer of de VM-extensies beschikbaar zijn

Veel soorten VM-extensies bestaan. Bij het ontwikkelen van sjabloon voor de consistentie van de cloud, zorg er dan voor dat alleen de extensies die beschikbaar in alle gebieden van de doelen van de sjabloon zijn gebruiken.

Een lijst van de VM-extensies die beschikbaar voor een bepaalde regio zijn op te halen (in dit voorbeeld `myLocation`), voer de volgende Azure CLI-opdracht:

```azurecli-interactive
az vm extension image list --location myLocation
```

U kunt ook uitvoeren met Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) cmdlet en gebruik `-Location` om op te geven van de locatie van de installatiekopie van de virtuele machine. Bijvoorbeeld:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Zorg ervoor dat er versies beschikbaar zijn

Aangezien VM-extensies eigen Resource Manager-resources zijn, hebben ze hun eigen API-versies. Zoals in de volgende code wordt weergegeven, is het type VM-extensie een geneste resource in de Microsoft.Compute-resourceprovider.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

De API-versie van de virtuele machine-extensie moet aanwezig zijn in alle locaties die u van plan bent om met de sjabloon. De locatie-afhankelijkheid werkt als de resourceprovider API-versie beschikbaar die eerder zijn besproken in de sectie 'Controleren de versie van alle brontypen'.

Als u wilt ophalen van een lijst van de beschikbare API-versies voor de VM-resource-uitbreiding, gebruikt u de [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) cmdlet met de **Microsoft.Compute** resourceprovider, zoals wordt weergegeven:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

U kunt ook VM-extensies in schaalsets voor virtuele machines. Er gelden de locatievoorwaarden van de dezelfde. Voor het ontwikkelen van uw sjabloon voor de consistentie van de cloud, zorg ervoor dat de API-versies zijn beschikbaar in alle locaties die u van plan bent om te implementeren. Als u wilt ophalen van de API-versies van de virtuele machine-extensie voor schaalsets, gebruikt u de dezelfde cmdlet als voordat, maar geef dat de virtuele-machineschaalsets resourcetype zoals wordt weergegeven:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Elke specifieke uitbreiding is ook is samengesteld. Deze versie wordt weergegeven in de `typeHandlerVersion` eigenschap van de VM-extensie. Zorg ervoor dat de versie opgegeven in de `typeHandlerVersion` element van de VM-extensies van de sjabloon zijn beschikbaar in de locaties waar u van plan bent om de sjabloon te implementeren. De volgende code geeft bijvoorbeeld versie 1.7:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

Als u wilt ophalen van een lijst van de beschikbare versies voor een specifieke VM-extensie, gebruikt u de [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) cmdlet. Het volgende voorbeeld wordt de beschikbare versies voor de PowerShell DSC (Desired State Configuration) VM-extensie van **myLocation**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Als u een lijst van uitgevers, gebruikt de [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) opdracht. Gebruiken om aan te vragen type, de [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) opdracht.

## <a name="tips-for-testing-and-automation"></a>Tips voor het testen en automatisering

Er is een uitdaging om bij te houden alle gerelateerde instellingen, mogelijkheden en beperkingen bij het ontwerpen van een sjabloon. De algemene aanpak is het ontwikkelen en testen van sjablonen op basis van een enkele cloud voordat andere locaties worden geselecteerd. Echter, de eerdere tests worden uitgevoerd in het ontwerpproces, minder oplossen van problemen en code voor het herschrijven dat uw ontwikkelteam moet doen. Implementaties die niet voldoen aan vanwege locatie afhankelijkheden kunnen tijdrovend zijn om op te lossen. Daarom is het is raadzaam geautomatiseerd testen zo vroeg mogelijk in de ontwerphandleiding van. Uiteindelijk, minder ontwikkelingstijd en minder bronnen, moet u en uw cloud-consistente artefacten worden nog waardevol.

De volgende afbeelding toont een typisch voorbeeld van een ontwikkelingsproces voor een team met behulp van een geïntegreerde ontwikkelomgeving (IDE). In verschillende stadia in de tijdlijn, worden verschillende testtypen uitgevoerd. Hier twee ontwikkelaars werkt op dezelfde oplossing, maar in dit scenario geldt ook voor een ontwikkelaar van één of een grote team. Elke ontwikkelaar maakt doorgaans een lokale kopie van een centrale opslagplaats, elke service op de lokale kopie werken zonder gevolgen voor de andere die op dezelfde bestanden werkt inschakelen.

![Werkstroom](./media/templates-cloud-consistency/workflow.png)

Houd rekening met de volgende tips voor het testen en automatisering:

* Maak gebruik van hulpprogramma's te testen. Bijvoorbeeld, Visual Studio Code en Visual Studio bevatten IntelliSense en andere functies die u kunnen helpen uw sjablonen valideren.
* Ter verbetering van de kwaliteit van code tijdens het ontwikkelen van op de lokale IDE, moet u statische code-analyse met eenheidstests en integratietests uitvoeren.
* Voor een nog betere ervaring tijdens de ontwikkelingsfase eenheidstests en integratietests moeten alleen waarschuwen wanneer er een probleem is gevonden en ga verder met de tests. Op die manier kunt u de problemen gericht identificeren en prioriteren van de volgorde van de wijzigingen, ook wel implementatie test-driven (TDD) genoemd.
* Er rekening mee dat sommige tests kunnen worden uitgevoerd zonder verbinding naar Azure Resource Manager. Andere, moet zoals het testen van sjabloonimplementatie, Resource Manager om uit te voeren van bepaalde acties die offline kunnen niet worden uitgevoerd.
* Testen van een sjabloon voor de implementatie op basis van de API-validatie is niet gelijk zijn aan een werkelijke implementatie. Zelfs als u een sjabloon vanuit een lokaal bestand implementeren, alle verwijzingen naar geneste sjablonen in de sjabloon door Resource Manager rechtstreeks worden opgehaald en artefacten waarnaar wordt verwezen door de VM-extensies worden opgehaald door de VM-agent die wordt uitgevoerd binnen de geïmplementeerde virtuele machine.

## <a name="next-steps"></a>Volgende stappen

* [Overwegingen met betrekking tot Azure Resource Manager-sjabloon](/azure-stack/user/azure-stack-develop-templates)
* [Aanbevolen procedures voor Azure Resource Manager-sjablonen](template-syntax.md)
