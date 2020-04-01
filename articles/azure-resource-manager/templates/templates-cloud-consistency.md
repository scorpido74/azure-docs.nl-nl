---
title: Sjablonen opnieuw gebruiken in verschillende clouds
description: Ontwikkel Azure Resource Manager-sjablonen die consistent werken voor verschillende cloudomgevingen. Maak nieuwe of werk bestaande sjablonen voor Azure Stack bij.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: c5095efef5d4bef44993bdd9cd52dbdef17378a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156103"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>ARM-sjablonen ontwikkelen voor cloudconsistentie

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Een belangrijk voordeel van Azure is consistentie. Ontwikkelingsinvesteringen voor de ene locatie zijn herbruikbaar in een andere. Een ARM-sjabloon (Azure Resource Manager) maakt uw implementaties consistent en herhaalbaar in alle omgevingen, waaronder de wereldwijde Azure-, Azure-soevereine clouds en Azure Stack. Als u sjablonen in verschillende clouds wilt hergebruiken, moet u echter rekening houden met cloudspecifieke afhankelijkheden, zoals in deze handleiding wordt uitgelegd.

Microsoft biedt intelligente, bedrijfsklare cloudservices op vele locaties, waaronder:

* Het wereldwijde Azure-platform wordt ondersteund door een groeiend netwerk van door Microsoft beheerde datacenters in regio's over de hele wereld.
* Geïsoleerde soevereine clouds zoals Azure Germany, Azure Government en Azure China 21Vianet. Soevereine clouds bieden een consistent platform met de meeste van dezelfde geweldige functies waarwereldwijde Azure-klanten toegang toe hebben.
* Azure Stack, een hybride cloudplatform waarmee u Azure-services leveren vanuit het datacenter van uw organisatie. Ondernemingen kunnen Azure Stack instellen in hun eigen datacenters of Azure Services van serviceproviders gebruiken, waarbij Azure Stack wordt uitgevoerd in hun faciliteiten (ook wel gehoste regio's genoemd).

In de kern van al deze clouds biedt Azure Resource Manager een API waarmee een breed scala aan gebruikersinterfaces kan communiceren met het Azure-platform. Deze API biedt u krachtige infrastructure-as-code-mogelijkheden. Elk type resource dat beschikbaar is op het Azure-cloudplatform kan worden geïmplementeerd en geconfigureerd met Azure Resource Manager. Met één sjabloon u uw volledige toepassing implementeren en configureren naar een operationele eindstatus.

![Azure-omgevingen](./media/templates-cloud-consistency/environments.png)

De consistentie van wereldwijde Azure, de soevereine clouds, gehoste clouds en een cloud in uw datacenter helpt u te profiteren van Azure Resource Manager. U uw ontwikkelingsinvesteringen in deze clouds opnieuw gebruiken wanneer u op sjablonen gebaseerde bronimplementatie en -configuratie instelt.

Hoewel de wereldwijde, soevereine, gehoste en hybride clouds consistente services bieden, zijn niet alle clouds identiek. Als gevolg hiervan u een sjabloon maken met afhankelijkheden van functies die alleen beschikbaar zijn in een specifieke cloud.

De rest van deze handleiding beschrijft de gebieden waarmee u rekening moet houden bij het plannen om nieuwe OF het bijwerken van bestaande ARM-sjablonen voor Azure Stack te ontwikkelen. In het algemeen moet uw checklist de volgende items bevatten:

* Controleer of de functies, eindpunten, services en andere bronnen in uw sjabloon beschikbaar zijn op de doelimplementatielocaties.
* Sla geneste sjablonen en configuratieartefacten op toegankelijke locaties op, zodat u toegang krijgt in verschillende clouds.
* Gebruik dynamische referenties in plaats van harde codering links en elementen.
* Controleer de sjabloonparameters die u gebruikt voor werk in de doelwolken.
* Controleer of resourcespecifieke eigenschappen beschikbaar zijn voor de doelwolken.

Zie [Sjabloonimplementatie](overview.md)voor een inleiding tot ARM-sjablonen.

## <a name="ensure-template-functions-work"></a>Ervoor zorgen dat sjabloonfuncties werken

De basissyntaxis van een ARM-sjabloon is JSON. Sjablonen maken gebruik van een superset JSON, waarmee de syntaxis wordt uitgebreid met expressies en functies. De sjabloontaalprocessor wordt regelmatig bijgewerkt om extra sjabloonfuncties te ondersteunen. Zie [ARM-sjabloonfuncties](template-functions.md)voor een gedetailleerde uitleg van de beschikbare sjabloonfuncties.

Nieuwe sjabloonfuncties die worden geïntroduceerd in Azure Resource Manager zijn niet onmiddellijk beschikbaar in de soevereine clouds of Azure Stack. Als u een sjabloon wilt implementeren, moeten alle functies waarnaar in de sjabloon wordt verwezen, beschikbaar zijn in de doelcloud.

Azure Resource Manager-mogelijkheden worden altijd eerst geïntroduceerd in het wereldwijde Azure. U het volgende PowerShell-script gebruiken om te controleren of nieuw ingevoerde sjabloonfuncties ook beschikbaar zijn in Azure Stack:

1. Maak een kloon van de GitHub repository: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions).

1. Zodra u een lokale kloon van de opslagplaats hebt, maakt u verbinding met Azure Resource Manager van de bestemming met PowerShell.

1. Importeer de psm1-module en voer de cmdlet Test-AzureRmTemplateFunctions uit:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Het script implementeert meerdere, geminimaliseerde sjablonen, die elk alleen unieke sjabloonfuncties bevatten. De uitvoer van het script rapporteert de ondersteunde en niet-beschikbare sjabloonfuncties.

## <a name="working-with-linked-artifacts"></a>Werken met gekoppelde artefacten

Een sjabloon kan verwijzingen bevatten naar gekoppelde artefacten en een implementatiebron bevatten die naar een andere sjabloon wordt gekoppeld. De gekoppelde sjablonen (ook wel geneste sjabloon genoemd) worden opgehaald door Resource Manager tijdens runtime. Een sjabloon kan ook verwijzingen bevatten naar artefacten voor VM-extensies (virtual machine). Deze artefacten worden opgehaald door de VM-extensie die in de VM wordt uitgevoerd voor de configuratie van de VM-extensie tijdens de sjabloonimplementatie.

In de volgende secties worden overwegingen voor cloudconsistentie beschreven bij het ontwikkelen van sjablonen die artefacten bevatten die buiten de hoofdimplementatiesjabloon vallen.

### <a name="use-nested-templates-across-regions"></a>Geneste sjablonen gebruiken in verschillende regio's

Sjablonen kunnen worden opgesplitst in kleine, herbruikbare sjablonen, die elk een specifiek doel hebben en kunnen worden hergebruikt voor implementatiescenario's. Als u een implementatie wilt uitvoeren, geeft u één sjabloon op die bekend staat als de hoofd- of hoofdsjabloon. Hierin worden de resources opgegeven die moeten worden geïmplementeerd, zoals virtuele netwerken, VM's en web-apps. De hoofdsjabloon kan ook een koppeling naar een andere sjabloon bevatten, wat betekent dat u sjablonen nesten. Op dezelfde manier kan een geneste sjabloon koppelingen naar andere sjablonen bevatten. U nesten tot vijf niveaus diep.

In de volgende code ziet u hoe de parameter templateLink verwijst naar een geneste sjabloon:

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

Azure Resource Manager evalueert de hoofdsjabloon tijdens runtime en haalt elke geneste sjabloon op en evalueert deze. Nadat alle geneste sjablonen zijn opgehaald, wordt de sjabloon afgevlakt en wordt de verdere verwerking gestart.

### <a name="make-linked-templates-accessible-across-clouds"></a>Gekoppelde sjablonen toegankelijk maken voor verschillende clouds

Bedenk waar en hoe u gekoppelde sjablonen opslaat die u gebruikt. Bij runtime haalt Azure Resource Manager op en vereist daarom directe toegang tot alle gekoppelde sjablonen. Een gangbare praktijk is om GitHub te gebruiken om de geneste sjablonen op te slaan. Een GitHub-repository kan bestanden bevatten die openbaar toegankelijk zijn via een URL. Hoewel deze techniek goed werkt voor de openbare cloud en de soevereine clouds, bevindt zich mogelijk een Azure Stack-omgeving op een bedrijfsnetwerk of op een niet-gekoppelde externe locatie, zonder uitgaande internettoegang. In die gevallen kan Azure Resource Manager de geneste sjablonen niet ophalen.

Een betere praktijk voor cross-cloud implementaties is het opslaan van uw gekoppelde sjablonen op een locatie die toegankelijk is voor de doelcloud. Idealiter worden alle implementatieartefacten onderhouden en geïmplementeerd vanuit een CI/CD-pijplijn (continuous integration/continuous development). U ook geneste sjablonen opslaan in een blob-opslagcontainer, waaruit Azure Resource Manager ze kan ophalen.

Aangezien de blob-opslag op elke cloud een ander eindpunt volledig gekwalificeerde domeinnaam (FQDN) gebruikt, configureert u de sjabloon met de locatie van de gekoppelde sjablonen met twee parameters. Parameters kunnen gebruikersinvoer accepteren tijdens de implementatie. Sjablonen worden meestal door meerdere personen geschreven en gedeeld, dus het is een goede gewoonte om een standaardnaam voor deze parameters te gebruiken. Naamgevingsconventies helpen sjablonen beter bruikbaar te maken voor regio's, clouds en auteurs.

In de volgende `_artifactsLocation` code wordt gebruikt om naar één locatie te wijzen, met alle implementatiegerelateerde artefacten. Merk op dat een standaardwaarde wordt opgegeven. Als er bij implementatietijd geen `_artifactsLocation`invoerwaarde is opgegeven, wordt de standaardwaarde gebruikt. Het `_artifactsLocationSasToken` wordt gebruikt als `sasToken`input voor de . De standaardwaarde moet een lege tekenreeks `_artifactsLocation` zijn voor scenario's waarin deze niet is beveiligd, bijvoorbeeld een openbare GitHub-opslagplaats.

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

In de hele sjabloon worden koppelingen gegenereerd door `_artifactsLocation` de basis-URI (van de `_artifactsLocationSasToken`parameter) te combineren met een artefact-relatieve pad en de . In de volgende code ziet u hoe u de koppeling naar de geneste sjabloon opgeeft met de functie uri-sjabloon:

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

Met deze benadering wordt de `_artifactsLocation` standaardwaarde voor de parameter gebruikt. Als de gekoppelde sjablonen van een andere locatie moeten worden opgehaald, kan de parameterinvoer worden gebruikt op implementatietijd om de standaardwaarde te overschrijven: er is geen wijziging in de sjabloon zelf nodig.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Gebruik _artifactsLocation in plaats van hardcoderingskoppelingen

Naast het gebruik voor geneste sjablonen `_artifactsLocation` wordt de URL in de parameter gebruikt als basis voor alle gerelateerde artefacten van een implementatiesjabloon. Sommige VM-extensies bevatten een koppeling naar een script dat buiten de sjabloon is opgeslagen. Voor deze extensies moet u de koppelingen niet hardcoderen. De Custom Script- en PowerShell DSC-extensies kunnen bijvoorbeeld worden gekoppeld aan een extern script op GitHub, zoals weergegeven:

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

Als u de koppelingen naar het script hardcodeert, wordt mogelijk voorkomen dat de sjabloon met succes naar een andere locatie wordt geïmplementeerd. Tijdens de configuratie van de VM-bron initieert de VM-agent die in de VM wordt uitgevoerd een download van alle scripts die zijn gekoppeld in de VM-extensie en slaat vervolgens de scripts op de lokale schijf van de VM op. Deze benadering functioneert zoals de geneste sjabloonkoppelingen die eerder zijn uitgelegd in de sectie 'Geneste sjablonen in verschillende regio's gebruiken'.

Resource Manager haalt geneste sjablonen op tijdens runtime. Voor VM-extensies wordt het ophalen van externe artefacten uitgevoerd door de VM-agent. Naast de verschillende initiator van het ophalen van artefact, is de oplossing in de sjabloondefinitie hetzelfde. Gebruik de parameter _artifactsLocation met een standaardwaarde van het basispad waar alle artefacten `_artifactsLocationSasToken` zijn opgeslagen (inclusief de VM-extensiescripts) en de parameter voor de invoer voor de sasToken.

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

Als u de absolute URI van een artefact wilt construeren, is de voorkeursmethode het gebruik van de functie uri-sjabloon in plaats van de sjabloonfunctie concat. Door hardcoded koppelingen naar de scripts in de VM-extensie te vervangen door de uri-sjabloonfunctie, wordt deze functionaliteit in de sjabloon geconfigureerd voor cloudconsistentie.

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

Met deze aanpak kunnen alle implementatieartefacten, inclusief configuratiescripts, op dezelfde locatie worden opgeslagen met de sjabloon zelf. Als u de locatie van alle koppelingen wilt wijzigen, hoeft u alleen een andere basis-URL op te geven voor de _parameters artefactenLocatie._

## <a name="factor-in-differing-regional-capabilities"></a>Factor in verschillende regionale capaciteiten

Met de flexibele ontwikkeling en continue stroom van updates en nieuwe services die in Azure zijn geïntroduceerd, [kunnen regio's verschillen](https://azure.microsoft.com/regions/services/) in beschikbaarheid van services of updates. Na rigoureuze interne tests worden nieuwe services of updates van bestaande services meestal geïntroduceerd in een klein publiek van klanten die deelnemen aan een validatieprogramma. Na succesvolle klantvalidatie worden de services of updates beschikbaar gesteld binnen een subset van Azure-regio's, vervolgens geïntroduceerd in meer regio's, uitgerold naar de soevereine clouds en mogelijk ook beschikbaar gesteld voor Azure Stack-klanten.

In de wetenschap dat Azure-regio's en clouds kunnen verschillen in hun beschikbare services, u een aantal proactieve beslissingen nemen over uw sjablonen. Een goede plek om te beginnen is door het onderzoeken van de beschikbare resource providers voor een cloud. Een resourceprovider vertelt u de set resources en bewerkingen die beschikbaar zijn voor een Azure-service.

Een sjabloon implementeert en configureert resources. Een resourcetype wordt geleverd door een resourceprovider. De compute resource provider (Microsoft.Compute) biedt bijvoorbeeld meerdere resourcetypen, zoals virtualMachines en availabilitySets. Elke resourceprovider biedt een API voor Azure Resource Manager, gedefinieerd door een gemeenschappelijk contract, waardoor een consistente, uniforme ontwerpervaring mogelijk is voor alle resourceproviders. Een resourceprovider die beschikbaar is in het wereldwijde Azure, is echter mogelijk niet beschikbaar in een soevereine cloud of een Azure Stack-regio.

![Resourceproviders](./media/templates-cloud-consistency/resource-providers.png)

Als u de resourceproviders wilt verifiëren die beschikbaar zijn in een bepaalde cloud, voert u het volgende script uit in de Azure-opdrachtregelinterface[(CLI):](/cli/azure/install-azure-cli)

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

U ook de volgende PowerShell-cmdlet gebruiken om beschikbare resourceproviders te bekijken:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>De versie van alle resourcetypen verifiëren

Een set eigenschappen is gebruikelijk voor alle resourcetypen, maar elke resource heeft ook zijn eigen specifieke eigenschappen. Nieuwe functies en gerelateerde eigenschappen worden soms aan bestaande brontypen toegevoegd via een nieuwe API-versie. Een resource in een sjabloon heeft `apiVersion`een eigen eigenschap API-versie - . Deze versieing zorgt ervoor dat een bestaande resourceconfiguratie in een sjabloon niet wordt beïnvloed door wijzigingen op het platform.

Nieuwe API-versies die zijn geïntroduceerd in bestaande brontypen in het wereldwijde Azure, zijn mogelijk niet onmiddellijk beschikbaar in alle regio's, soevereine clouds of Azure Stack. Als u een lijst wilt weergeven met de beschikbare resourceproviders, resourcetypen en API-versies voor een cloud, u Resource Explorer gebruiken in Azure-portal. Zoeken naar Resource Explorer in het menu Alle services. Vouw het knooppunt Providers uit in Resource Explorer om alle beschikbare resourceproviders, hun resourcetypen en API-versies in die cloud terug te sturen.

Voer het volgende script uit als u de beschikbare API-versie wilt weergeven voor alle resourcetypen in een bepaalde cloud in Azure CLI:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

U ook de volgende PowerShell-cmdlet gebruiken:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Verwijzen naar resourcelocaties met een parameter

Een sjabloon wordt altijd geïmplementeerd in een resourcegroep die zich in een regio bevindt. Naast de implementatie zelf heeft elke resource in een sjabloon ook een locatieeigenschap die u gebruikt om de regio op te geven waarin u moet implementeren. Als u uw sjabloon wilt ontwikkelen voor cloudconsistentie, hebt u een dynamische manier nodig om naar resourcelocaties te verwijzen, omdat elke Azure Stack unieke locatienamen kan bevatten. Meestal worden resources geïmplementeerd in dezelfde regio als de resourcegroep, maar om scenario's zoals de beschikbaarheid van toepassingen in verschillende regio's te ondersteunen, kan het handig zijn om resources over regio's te verspreiden.

Hoewel u de regionamen hardcoderen wanneer u de resourceeigenschappen in een sjabloon opgeeft, garandeert deze benadering niet dat de sjabloon kan worden geïmplementeerd in andere Azure Stack-omgevingen, omdat de regionaam daar waarschijnlijk niet bestaat.

Als u verschillende regio's wilt aanpassen, voegt u een invoerparameterlocatie toe aan de sjabloon met een standaardwaarde. De standaardwaarde wordt gebruikt als er geen waarde is opgegeven tijdens de implementatie.

De sjabloonfunctie `[resourceGroup()]` retourneert een object dat de volgende sleutel-/waardeparen bevat:

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

Door te verwijzen naar de locatiesleutel van het object in de standaardwaarde van de `[resourceGroup().location]` invoerparameter, vervangt Azure Resource Manager bij uitvoering de sjabloonfunctie door de naam van de locatie van de resourcegroep waaraan de sjabloon is geïmplementeerd.

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

Met deze sjabloonfunctie u uw sjabloon in elke cloud implementeren zonder de regionamen van tevoren te kennen. Bovendien kan een locatie voor een specifieke resource in de sjabloon verschillen van de locatie van de resourcegroep. In dit geval u deze configureren met behulp van extra invoerparameters voor die specifieke resource, terwijl de andere resources in dezelfde sjabloon nog steeds de parameter in de oorspronkelijke locatieinvoer gebruiken.

### <a name="track-versions-using-api-profiles"></a>Versies bijhouden met API-profielen

Het kan zeer uitdagend zijn om alle beschikbare resourceproviders en gerelateerde API-versies die aanwezig zijn in Azure Stack bij te houden. Op het moment van schrijven is `2018-04-01`bijvoorbeeld de nieuwste API-versie voor **Microsoft.Compute/availabilitySets** in Azure , `2016-03-30`terwijl de beschikbare API-versie die algemeen is voor Azure en Azure Stack . De gemeenschappelijke API-versie voor **Microsoft.Storage/storageAccounts** die worden `2016-01-01`gedeeld tussen alle Azure- `2018-02-01`en Azure Stack-locaties is , terwijl de nieuwste API-versie in Azure .

Om deze reden introduceerde Resource Manager het concept van API-profielen in sjablonen. Zonder API-profielen is elke resource in `apiVersion` een sjabloon geconfigureerd met een element dat de API-versie voor die specifieke bron beschrijft.

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

Een API-profielversie fungeert als alias voor één API-versie per resourcetype dat algemeen is voor Azure en Azure Stack. In plaats van een API-versie op te geven voor elke resource in een `apiProfile` sjabloon, `apiVersion` geeft u alleen de API-profielversie op in een nieuw hoofdelement dat het element voor de afzonderlijke resources wordt aangeroepen en weglaat.

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

Het API-profiel zorgt ervoor dat de API-versies beschikbaar zijn op verschillende locaties, zodat u de apiVersies die beschikbaar zijn op een specifieke locatie niet handmatig hoeft te verifiëren. Om ervoor te zorgen dat de API-versies waarnaar wordt verwezen door uw API-profiel aanwezig zijn in een Azure Stack-omgeving, moeten de Azure Stack-operators de oplossing up-to-date houden op basis van het ondersteuningsbeleid. Als een systeem meer dan zes maanden verouderd is, wordt het als niet-naleving beschouwd en moet het milieu worden bijgewerkt.

Het API-profiel is geen vereist element in een sjabloon. Zelfs als u het element toevoegt, wordt het `apiVersion` alleen gebruikt voor resources waarvoor geen is opgegeven. Dit element zorgt voor geleidelijke wijzigingen, maar vereist geen wijzigingen in bestaande sjablonen.

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

## <a name="check-endpoint-references"></a>Eindpuntverwijzingen controleren

Bronnen kunnen verwijzingen naar andere services op het platform hebben. Een openbaar IP kan bijvoorbeeld een openbare DNS-naam hebben toegewezen. De public cloud, de soevereine clouds en Azure Stack-oplossingen hebben hun eigen afzonderlijke eindpuntnaamruimten. In de meeste gevallen vereist een resource alleen een voorvoegsel als invoer in de sjabloon. Tijdens runtime voegt Azure Resource Manager de eindpuntwaarde eraan toe. Sommige eindpuntwaarden moeten expliciet in de sjabloon worden opgegeven.

> [!NOTE]
> Als u sjablonen wilt ontwikkelen voor cloudconsistentie, hoeft u geen naamruimten voor eindpunten hardcoderen.

De volgende twee voorbeelden zijn algemene eindpuntnaamruimten die expliciet moeten worden opgegeven bij het maken van een resource:

* Opslagaccounts (blob, wachtrij, tabel en bestand)
* Verbindingstekenreeksen voor databases en Azure Cache voor Redis

Endpoint-naamruimten kunnen ook worden gebruikt in de uitvoer van een sjabloon als informatie voor de gebruiker wanneer de implementatie is voltooid. De volgende voorbeelden zijn:

* Opslagaccounts (blob, wachtrij, tabel en bestand)
* Verbindingstekenreeksen (MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* domainNameLabel van een openbaar IP-adres
* Cloud services

Over het algemeen vermijdt u hardcoded eindpunten in een sjabloon. De beste praktijk is om de functie referentiesjabloon te gebruiken om de eindpunten dynamisch op te halen. Het eindpunt dat het meest hardcoded is, is bijvoorbeeld de naamruimte voor eindpunten voor opslagaccounts. Elk opslagaccount heeft een unieke FQDN die is opgebouwd door de naam van het opslagaccount samen te brengen met de naamruimte voor eindpunten. Een blob-opslagaccount met de naam mystorageaccount1 resulteert in verschillende FQDN's, afhankelijk van de cloud:

* **mystorageaccount1.blob.core.windows.net** wanneer deze is gemaakt in de wereldwijde Azure-cloud.
* **mystorageaccount1.blob.core.chinacloudapi.cn** wanneer deze zijn gemaakt in de Azure China 21Vianet-cloud.

Met de volgende functie referentiesjabloon wordt de naamruimte van het eindpunt opgehaald bij de opslagbronprovider:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Door de hardcoded waarde van het eindpunt `reference` van het opslagaccount te vervangen door de sjabloonfunctie, u dezelfde sjabloon gebruiken om met succes naar verschillende omgevingen te implementeren zonder de eindpuntverwijzing aan te brengen.

### <a name="refer-to-existing-resources-by-unique-id"></a>Verwijzen naar bestaande bronnen door unieke ID

U ook verwijzen naar een bestaande bron uit dezelfde of een andere resourcegroep en binnen hetzelfde abonnement of een ander abonnement, binnen dezelfde tenant in dezelfde cloud. Als u de resourceeigenschappen wilt ophalen, moet u de unieke id voor de bron zelf gebruiken. Met `resourceId` de sjabloonfunctie wordt de unieke id van een bron zoals SQL Server opgehaald, zoals de volgende code wordt weergegeven:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

U de `resourceId` functie `reference` in de sjabloonfunctie vervolgens gebruiken om de eigenschappen van een database op te halen. Het retourobject `fullyQualifiedDomainName` bevat de eigenschap met de volledige eindpuntwaarde. Deze waarde wordt opgehaald tijdens runtime en biedt de naamruimte voor de cloudomgeving. Als u de verbindingstekenreeks wilt definiëren zonder de naamruimte van het eindpunt te hardcoderen, u rechtstreeks in de weergegeven verbindingstekenreeks verwijzen naar de eigenschap van het retourobject:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Resourceeigenschappen overwegen

Specifieke resources binnen Azure Stack-omgevingen hebben unieke eigenschappen die u in uw sjabloon moet overwegen.

### <a name="ensure-vm-images-are-available"></a>Ervoor zorgen dat VM-afbeeldingen beschikbaar zijn

Azure biedt een uitgebreide selectie van VM-afbeeldingen. Deze afbeeldingen worden gemaakt en voorbereid voor implementatie door Microsoft en partners. De beelden vormen de basis voor VM's op het platform. Een sjabloon die in de cloud consistent is, moet echter alleen verwijzen naar beschikbare parameters, met name de uitgever, aanbieding en SKU van de VM-afbeeldingen die beschikbaar zijn voor de wereldwijde Azure-, Azure-soevereine clouds of een Azure Stack-oplossing.

Als u een lijst met beschikbare VM-afbeeldingen op een locatie wilt ophalen, voert u de volgende opdracht Azure CLI uit:

```azurecli-interactive
az vm image list -all
```

U dezelfde lijst ophalen met de Azure PowerShell-cmdlet [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) en de gewenste locatie opgeven met de `-Location` parameter. Bijvoorbeeld:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Deze opdracht duurt een paar minuten om alle beschikbare afbeeldingen in de regio West-Europa van de wereldwijde Azure-cloud terug te sturen.

Als u deze VM-afbeeldingen beschikbaar hebt gesteld voor Azure Stack, wordt alle beschikbare opslagruimte verbruikt. Azure Stack u de afbeeldingen selecteren die u aan een omgeving wilt toevoegen om zelfs de kleinste schaaleenheid te kunnen huisvesten.

In het volgende voorbeeld van de code wordt een consistente benadering weergegeven om te verwijzen naar de parameters voor de uitgever, aanbieding en SKU in uw ARM-sjablonen:

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

### <a name="check-local-vm-sizes"></a>Lokale VM-formaten controleren

Als u uw sjabloon wilt ontwikkelen voor cloudconsistentie, moet u ervoor zorgen dat de gewenste VM-grootte beschikbaar is in alle doelomgevingen. VM-formaten zijn een groepering van prestatiekenmerken en -mogelijkheden. Sommige VM-formaten zijn afhankelijk van de hardware waarop de VM wordt uitgevoerd. Als u bijvoorbeeld een vm met GPU-geoptimaliseerde hardware wilt implementeren, moet de hardware waarop de hypervisor wordt uitgevoerd, de hardware-GPU's hebben.

Wanneer Microsoft een nieuwe grootte van VM introduceert met bepaalde hardwareafhankelijkheden, wordt de VM-grootte meestal eerst beschikbaar gesteld in een kleine subset van regio's in de Azure-cloud. Later wordt het beschikbaar gesteld aan andere regio's en wolken. Als u wilt dat de VM-grootte bestaat in elke cloud waarnaar u implementeert, u de beschikbare grootten ophalen met de volgende opdracht Azure CLI:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Gebruik voor Azure PowerShell:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Zie [Producten die beschikbaar zijn per regio voor](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable)een volledige lijst met beschikbare services.

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Het gebruik van Azure Managed Disks in Azure Stack controleren

Beheerde schijven verwerken de opslag voor een Azure-tenant. In plaats van expliciet een opslagaccount aan te maken en de URI voor een virtuele harde schijf (VHD) op te geven, u beheerde schijven gebruiken om deze acties impliciet uit te voeren wanneer u een VM implementeert. Beheerde schijven verbeteren de beschikbaarheid door alle schijven van VM's in dezelfde beschikbaarheid te plaatsen in verschillende opslageenheden. Bovendien kunnen bestaande VHD's worden omgezet van Standaard naar Premium-opslag met aanzienlijk minder downtime.

Hoewel beheerde schijven op de roadmap voor Azure Stack staan, worden ze momenteel niet ondersteund. Totdat dit het geval is, u cloudconsistente sjablonen voor Azure `vhd` Stack ontwikkelen door vhd's expliciet op te geven met behulp van het element in de sjabloon voor de VM-bron zoals weergegeven:

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

Als u daarentegen een beheerde schijfconfiguratie `vhd` in een sjabloon wilt opgeven, verwijdert u het element uit de schijfconfiguratie.

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

Dezelfde wijzigingen zijn ook van toepassing [op gegevensschijven](../../virtual-machines/windows/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Controleren of VM-extensies beschikbaar zijn in Azure Stack

Een andere overweging voor cloudconsistentie is het gebruik van [virtuele machine-extensies](../../virtual-machines/windows/extensions-features.md) om de bronnen in een VM te configureren. Niet alle VM-extensies zijn beschikbaar in Azure Stack. Een sjabloon kan de resources opgeven die zijn toegewezen aan de VM-extensie, waardoor afhankelijkheden en voorwaarden in de sjabloon worden gemaakt.

Als u bijvoorbeeld een VM met Microsoft SQL Server wilt configureren, kan de VM-extensie SQL Server configureren als onderdeel van de sjabloonimplementatie. Bedenk wat er gebeurt als de implementatiesjabloon ook een toepassingsserver bevat die is geconfigureerd om een database te maken op de VM met SQL Server. Naast het gebruik van een VM-extensie voor de toepassingsservers, u de afhankelijkheid van de toepassingsserver configureren bij de succesvolle terugkeer van de SQL Server VM-extensiebron. Deze benadering zorgt ervoor dat de VM met SQL Server is geconfigureerd en beschikbaar is wanneer de toepassingsserver wordt geïnstrueerd om de database te maken.

Met de declaratieve benadering van de sjabloon u de eindstatus van de resources en hun onderlinge afhankelijkheden definiëren, terwijl het platform zorgt voor de logica die nodig is voor de afhankelijkheden.

#### <a name="check-that-vm-extensions-are-available"></a>Controleren of VM-extensies beschikbaar zijn

Er bestaan veel soorten VM-extensies. Wanneer u een sjabloon ontwikkelt voor cloudconsistentie, moet u alleen de extensies gebruiken die beschikbaar zijn in alle regio's waarop de sjabloon doelen.

Als u een lijst met vm-extensies wilt ophalen die `myLocation`beschikbaar zijn voor een specifieke regio (in dit voorbeeld), voert u de volgende opdracht Azure CLI uit:

```azurecli-interactive
az vm extension image list --location myLocation
```

U ook de cmdlet Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) uitvoeren en gebruiken `-Location` om de locatie van de virtuele machineafbeelding op te geven. Bijvoorbeeld:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Ervoor zorgen dat versies beschikbaar zijn

Aangezien VM-extensies first-party Resource Manager-bronnen zijn, hebben ze hun eigen API-versies. Zoals de volgende code laat zien, is het vm-extensietype een geneste bron in de Microsoft.Compute-bronprovider.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

De API-versie van de VM-extensiebron moet aanwezig zijn op alle locaties die u wilt targeten met uw sjabloon. De locatieafhankelijkheid werkt zoals de beschikbaarheid van api-versie van de resourceprovider die eerder in de sectie 'De versie van alle resourcetypen verifiëren' is besproken.

Als u een lijst met beschikbare API-versies voor de VM-extensiebron wilt ophalen, gebruikt u de cmdlet [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) met de **Microsoft.Compute-bronprovider** zoals weergegeven:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

U vm-extensies ook gebruiken in virtuele machineschaalsets. Dezelfde locatievoorwaarden zijn van toepassing. Als u uw sjabloon wilt ontwikkelen voor cloudconsistentie, controleert u of de API-versies beschikbaar zijn op alle locaties waarnaar u van plan bent te implementeren. Als u de API-versies van de VM-extensiebron voor schaalsets wilt ophalen, gebruikt u dezelfde cmdlet als voorheen, maar geeft u het resourcetype voor virtuele machineschaalsets op zoals weergegeven:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Elke specifieke extensie is ook versie. Deze versie wordt `typeHandlerVersion` weergegeven in de eigenschap van de VM-extensie. Zorg ervoor dat de `typeHandlerVersion` versie die is opgegeven in het element van de VM-extensies van uw sjabloon beschikbaar is op de locaties waar u de sjabloon wilt implementeren. De volgende code geeft bijvoorbeeld versie 1.7 op:

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

Als u een lijst met beschikbare versies voor een specifieke VM-extensie wilt ophalen, gebruikt u de cmdlet [Get-AzureRmVMExtensionImage.](/powershell/module/az.compute/get-azvmextensionimage) In het volgende voorbeeld worden de beschikbare versies voor de POWERShell DSC-vm-extensie (Gewenste statusconfiguratie) opgehaald van **myLocation:**

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Als u een lijst met uitgevers wilt krijgen, gebruikt u de opdracht [Get-AzureRmVmImagePublisher.](/powershell/module/az.compute/get-azvmimagepublisher) Als u tekst wilt aanvragen, gebruikt u de aanbeveling [get-AzureRmVMExtensionImageType.](/powershell/module/az.compute/get-azvmextensionimagetype)

## <a name="tips-for-testing-and-automation"></a>Tips voor testen en automatisering

Het is een uitdaging om alle gerelateerde instellingen, mogelijkheden en beperkingen bij te houden tijdens het ontwerpen van een sjabloon. De gemeenschappelijke aanpak is het ontwikkelen en testen van sjablonen tegen één cloud voordat andere locaties worden getarget. Echter, hoe eerder tests worden uitgevoerd in het ontwerpproces, hoe minder probleemoplossing en code herschrijven van uw ontwikkelteam zal moeten doen. Implementaties die mislukken vanwege locatieafhankelijkheden kunnen tijdrovend zijn om problemen op te lossen. Daarom raden we u aan om zo vroeg mogelijk in de ontwerpcyclus geautomatiseerd te testen. Uiteindelijk hebt u minder ontwikkelingstijd en minder resources nodig en worden uw cloudconsistente artefacten nog waardevoller.

De volgende afbeelding toont een typisch voorbeeld van een ontwikkelingsproces voor een team met behulp van een geïntegreerde ontwikkelomgeving (IDE). In verschillende stadia van de tijdlijn worden verschillende testtypen uitgevoerd. Hier werken twee ontwikkelaars aan dezelfde oplossing, maar dit scenario geldt ook voor een enkele ontwikkelaar of een groot team. Elke ontwikkelaar maakt meestal een lokale kopie van een centrale opslagplaats, zodat elk een van de lokale kopie te werken zonder dat de anderen die kunnen werken aan dezelfde bestanden.

![Werkstroom](./media/templates-cloud-consistency/workflow.png)

Houd rekening met de volgende tips voor testen en automatisering:

* Maak gebruik van testtools. Visual Studio Code en Visual Studio bevatten bijvoorbeeld IntelliSense en andere functies waarmee u uw sjablonen valideren.
* Om de codekwaliteit tijdens de ontwikkeling op de lokale IDE te verbeteren, voert u statische codeanalyse uit met unittests en integratietests.
* Voor een nog betere ervaring tijdens de eerste ontwikkeling, moeten unittests en integratietests alleen waarschuwen wanneer er een probleem wordt gevonden en doorgaan met de tests. Op die manier u de problemen identificeren om de volgorde van de wijzigingen aan te pakken en prioriteren, ook wel testgestuurde implementatie (TDD) genoemd.
* Houd er rekening mee dat sommige tests kunnen worden uitgevoerd zonder dat ze zijn verbonden met Azure Resource Manager. Voor andere acties, zoals het testen van de implementatie van sjablonen, moet Resource Manager bepaalde acties uitvoeren die niet offline kunnen worden uitgevoerd.
* Het testen van een implementatiesjabloon op de validatie-API is niet gelijk aan een daadwerkelijke implementatie. Zelfs als u een sjabloon implementeert uit een lokaal bestand, worden verwijzingen naar geneste sjablonen in de sjabloon rechtstreeks opgehaald door Resource Manager en worden artefacten waarnaar door VM-extensies wordt verwezen opgehaald door de VM-agent die in de geïmplementeerde VM wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

* [Overwegingen van Azure Resource Manager-sjabloon](/azure-stack/user/azure-stack-develop-templates)
* [Aanbevolen procedures voor ARM-sjablonen](template-syntax.md)
