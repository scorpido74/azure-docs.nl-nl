---
title: Aanbevolen procedures voor sjablonen
description: Beschrijft aanbevolen benaderingen voor het maken van Azure Resource Manager-sjablonen. Biedt suggesties om veelvoorkomende problemen bij het gebruik van sjablonen te voorkomen.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 870636d6457d842c89f261c2537644c17a335294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156409"
---
# <a name="arm-template-best-practices"></a>Aanbevolen procedures voor ARM-sjabloon

In dit artikel vindt u aanbevelingen voor het samenstellen van uw Azure Resource Manager-sjabloon (ARM). Met deze aanbevelingen u veelvoorkomende problemen voorkomen wanneer u een ARM-sjabloon gebruikt om een oplossing te implementeren.

Zie [Azure enterprise schavot: Prescriptief abonnementsbeheer](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)voor aanbevelingen over het beheren van uw Azure-abonnementen.

Zie [Azure Resource Manager-sjablonen ontwikkelen voor cloudconsistentie voor](templates-cloud-consistency.md)aanbevelingen over het bouwen van sjablonen die in alle Azure-cloudomgevingen werken.

## <a name="template-limits"></a>Sjabloonlimieten

Beperk de grootte van uw sjabloon tot 4 MB en elk parameterbestand tot 64 KB. De limiet van 4 MB is van toepassing op de uiteindelijke status van de sjabloon nadat deze is uitgebreid met iteratieve brondefinities en waarden voor variabelen en parameters. 

Je bent ook beperkt tot:

* 256 parameters
* 256 variabelen
* 800 resources (inclusief aantal exemplaren)
* 64 uitvoerwaarden
* 24.576 tekens in een sjabloonexpressie

U bepaalde sjabloonlimieten overschrijden met een geneste sjabloon. Zie [Gekoppelde sjablonen gebruiken bij het implementeren van Azure-resources](linked-templates.md)voor meer informatie. Als u het aantal parameters, variabelen of uitvoer wilt verminderen, u verschillende waarden in een object combineren. Zie [Objecten als parameters voor](/azure/architecture/building-blocks/extending-templates/objects-as-parameters)meer informatie .

## <a name="resource-group"></a>Resourcegroep

Wanneer u resources implementeert in een resourcegroep, worden metagegevens van de resourcegroep opgeslagen in de bronnen. De metagegevens worden opgeslagen op de locatie van de resourcegroep.

Als het gebied van de resourcegroep tijdelijk niet beschikbaar is, u de bronnen in de brongroep niet bijwerken omdat de metagegevens niet beschikbaar zijn. De resources in andere regio's werken nog steeds zoals verwacht, maar u ze niet bijwerken. Als u risico's wilt minimaliseren, zoekt u uw resourcegroep en resources in dezelfde regio.

## <a name="parameters"></a>Parameters

De informatie in deze sectie kan handig zijn wanneer u met [parameters](template-parameters.md)werkt.

### <a name="general-recommendations-for-parameters"></a>Algemene aanbevelingen voor parameters

* Minimaliseer uw gebruik van parameters. Gebruik in plaats daarvan variabelen of letterlijke waarden voor eigenschappen die tijdens de implementatie niet hoeven te worden opgegeven.

* Gebruik camel case voor parameternamen.

* Gebruik parameters voor instellingen die variëren afhankelijk van de omgeving, zoals SKU, grootte of capaciteit.

* Gebruik parameters voor resourcenamen die u wilt opgeven voor eenvoudige identificatie.

* Geef een beschrijving van elke parameter in de metagegevens:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Definieer standaardwaarden voor parameters die niet gevoelig zijn. Door een standaardwaarde op te geven, is het eenvoudiger om de sjabloon te implementeren en zien gebruikers van uw sjabloon een voorbeeld van een geschikte waarde. Elke standaardwaarde voor een parameter moet geldig zijn voor alle gebruikers in de standaardimplementatieconfiguratie. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Als u een optionele parameter wilt opgeven, gebruikt u geen lege tekenreeks als standaardwaarde. Gebruik in plaats daarvan een letterlijke waarde of een taalexpressie om een waarde te construeren.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Gebruik geen parameter voor de API-versie voor een resourcetype. Broneigenschappen en -waarden kunnen variëren per versienummer. IntelliSense in een codeeditor kan het juiste schema niet bepalen wanneer de API-versie is ingesteld op een parameter. In plaats daarvan, hard-code de API-versie in de sjabloon.

* Gebruik `allowedValues` spaarzaam. Gebruik het alleen als u ervoor moet zorgen dat bepaalde waarden niet zijn opgenomen in de toegestane opties. Als u `allowedValues` te breed gebruikt, u geldige implementaties blokkeren door uw lijst niet up-to-date te houden.

* Wanneer een parameternaam in uw sjabloon overeenkomt met een parameter in de powershell-implementatieopdracht, lost Resource Manager dit naamgevingsconflict op door het postfix **FromTemplate** toe te voegen aan de parameter template. Als u bijvoorbeeld een parameter met de naam **ResourceGroupName** in uw sjabloon opneemt, is deze in strijd met de parameter **ResourceGroupName** in de cmdlet [Nieuw-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Tijdens de implementatie wordt u gevraagd een waarde te geven voor **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Beveiligingsaanbevelingen voor parameters

* Gebruik altijd parameters voor gebruikersnamen en wachtwoorden (of geheimen).

* Gebruik `securestring` voor alle wachtwoorden en geheimen. Als u gevoelige gegevens doorgeeft in `secureObject` een JSON-object, gebruikt u het type. Sjabloonparameters met beveiligde tekenreeks- of beveiligde objecttypen kunnen niet worden gelezen na het implementeren van resources. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Geef geen standaardwaarden op voor gebruikersnamen, wachtwoorden of `secureString` waarde waarvoor een type vereist is.

* Geef geen standaardwaarden op voor eigenschappen die het aanvalsoppervlak van de toepassing vergroten.

### <a name="location-recommendations-for-parameters"></a>Locatieaanbevelingen voor parameters

* Gebruik een parameter om de locatie voor resources `resourceGroup().location`op te geven en stel de standaardwaarde in op . Als u een locatieparameter opgeeft, kunnen gebruikers van de sjabloon een locatie opgeven waarnaar ze toestemming hebben om te implementeren.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Geef niet `allowedValues` op voor de locatieparameter. De locaties die u opgeeft, zijn mogelijk niet in alle clouds beschikbaar.

* Gebruik de locatieparameterwaarde voor resources die zich waarschijnlijk op dezelfde locatie bevinden. Deze aanpak minimaliseert het aantal keren dat gebruikers worden gevraagd om locatiegegevens te verstrekken.

* Voor resources die niet op alle locaties beschikbaar zijn, gebruikt u een afzonderlijke parameter of geeft u een letterlijke locatiewaarde op.

## <a name="variables"></a>Variabelen

De volgende informatie kan handig zijn wanneer u met [variabelen](template-variables.md)werkt:

* Gebruik camel case voor variabele namen.

* Gebruik variabelen voor waarden die u meerdere keer in een sjabloon moet gebruiken. Als een waarde slechts één keer wordt gebruikt, maakt een hardgecodeerde waarde uw sjabloon gemakkelijker te lezen.

* Gebruik variabelen voor waarden die u maakt uit een complexe rangschikking van sjabloonfuncties. Uw sjabloon is gemakkelijker te lezen wanneer de complexe expressie alleen in variabelen wordt weergegeven.

* Gebruik geen variabelen voor `apiVersion` een resource. De API-versie bepaalt het schema van de bron. Vaak u de versie niet wijzigen zonder de eigenschappen voor de resource te wijzigen.

* U de [referentiefunctie](template-functions-resource.md#reference) niet gebruiken in de sectie **variabelen** van de sjabloon. De **referentiefunctie** ontleent de waarde ervan aan de runtime-status van de resource. Variabelen worden echter opgelost tijdens het initiële ontseren van de sjabloon. Bouw waarden die de **referentiefunctie** rechtstreeks in de sectie **resources** of **outputs** van de sjabloon nodig hebben.

* Voeg variabelen op voor resourcenamen die uniek moeten zijn.

* Gebruik een [kopielus in variabelen](copy-variables.md) om een herhaald patroon van JSON-objecten te maken.

* Ongebruikte variabelen verwijderen.

## <a name="resource-dependencies"></a>Bronafhankelijkheden

Gebruik de volgende richtlijnen bij het bepalen van de [volgende afhankelijkheden:](define-resource-dependency.md)

* Gebruik de **referentiefunctie** en geef de naam van de resource door om een impliciete afhankelijkheid in te stellen tussen resources die een eigenschap moeten delen. Voeg geen expliciet `dependsOn` element toe wanneer u al een impliciete afhankelijkheid hebt gedefinieerd. Deze aanpak vermindert het risico op onnodige afhankelijkheden.

* Stel een onderliggende bron in als afhankelijk van de bovenliggende resource.

* Resources met het [voorwaardeelement](conditional-resource-deployment.md) dat is ingesteld op false, worden automatisch uit de afhankelijkheidsvolgorde verwijderd. Stel de afhankelijkheden in alsof de resource altijd is geïmplementeerd.

* Laat afhankelijkheden trapsgewijs worden zonder ze expliciet in te stellen. Uw virtuele machine is bijvoorbeeld afhankelijk van een virtuele netwerkinterface en de virtuele netwerkinterface is afhankelijk van een virtueel netwerk en openbare IP-adressen. Daarom wordt de virtuele machine geïmplementeerd na alle drie de resources, maar stel de virtuele machine niet expliciet in als afhankelijk van alle drie de resources. Deze benadering verduidelijkt de afhankelijkheidsvolgorde en maakt het gemakkelijker om de sjabloon later te wijzigen.

* Als een waarde kan worden bepaald voordat de implementatie wordt geïmplementeerd, probeert u de resource zonder afhankelijkheid te implementeren. Als een configuratiewaarde bijvoorbeeld de naam van een andere resource nodig heeft, hebt u mogelijk geen afhankelijkheid nodig. Deze richtlijnen werken niet altijd omdat sommige resources het bestaan van de andere resource controleren. Als u een fout ontvangt, voegt u een afhankelijkheid toe.

## <a name="resources"></a>Resources

De volgende informatie kan handig zijn wanneer u met [bronnen](template-syntax.md#resources)werkt:

* Als u andere bijdragers inzicht wilt geven in het doel van de resource, geeft u **opmerkingen** op voor elke resource in de sjabloon:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-06-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Als u een *openbaar eindpunt* in uw sjabloon gebruikt (zoals een openbaar eindpunt voor Azure *Blob-opslag),* hoeft u de naamruimte niet hard te coderen. Gebruik de **referentiefunctie** om de naamruimte dynamisch op te halen. U deze benadering gebruiken om de sjabloon te implementeren in verschillende openbare naamruimteomgevingen zonder het eindpunt in de sjabloon handmatig te wijzigen. Stel de API-versie in op dezelfde versie die u gebruikt voor het opslagaccount in uw sjabloon:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Als het opslagaccount wordt geïmplementeerd in dezelfde sjabloon die u maakt en de naam van het opslagaccount niet wordt gedeeld met een andere bron in de sjabloon, hoeft u de naamruimte van de provider of de apiVersion niet op te geven wanneer u naar de bron verwijst. In het volgende voorbeeld wordt de vereenvoudigde syntaxis weergegeven:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
       }
   }
   ```
     
   U ook verwijzen naar een bestaand opslagaccount dat zich in een andere brongroep bevindt:

   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```

* Wijs openbare IP-adressen alleen toe aan een virtuele machine wanneer een toepassing dit vereist. Als u verbinding wilt maken met een virtuele machine (VM) voor foutopsporing of voor beheer- of beheerdoeleinden, gebruikt u inkomende NAT-regels, een virtuele netwerkgateway of een jumpbox.
   
     Zie voor meer informatie over het verbinden met virtuele machines:
   
   * [VM's uitvoeren voor een N-tier-architectuur in Azure](../../guidance/guidance-compute-n-tier-vm.md)
   * [WinRM-toegang instellen voor VM's in Azure Resource Manager](../../virtual-machines/windows/winrm.md)
   * [Externe toegang tot uw vm toestaan met behulp van de Azure-portal](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Externe toegang tot uw vm toestaan met PowerShell](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Externe toegang tot uw Linux-VM toestaan met Azure CLI](../../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* De eigenschap **domainNameLabel** voor openbare IP-adressen moet uniek zijn. De **waarde domainNameLabel** moet tussen de 3 en 63 tekens lang `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`zijn en de regels volgen die door deze reguliere expressie zijn opgegeven: . Omdat de functie **uniqueString** een tekenreeks genereert die 13 tekens lang is, is de parameter **dnsPrefixString** beperkt tot 50 tekens:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Wanneer u een wachtwoord toevoegt aan een aangepaste scriptextensie, gebruikt u de eigenschap **commandToExecute** in de eigenschap **protectedSettings:**
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Gebruik de eigenschap **protectedSettings** van de desbetreffende extensies om ervoor te zorgen dat geheimen worden versleuteld wanneer ze worden doorgegeven als parameters aan VM's en extensies.
   > 
   > 

## <a name="next-steps"></a>Volgende stappen

* Zie [De structuur en syntaxis van ARM-sjablonen begrijpen](template-syntax.md)voor informatie over de structuur van het sjabloonbestand .
* Zie [ARM-sjablonen ontwikkelen voor cloudconsistentie voor](templates-cloud-consistency.md)aanbevelingen over het bouwen van sjablonen die in alle Azure-cloudomgevingen werken.
