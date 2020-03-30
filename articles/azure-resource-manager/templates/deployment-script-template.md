---
title: Implementatiescripts gebruiken in sjablonen | Microsoft Documenten
description: implementatiescripts gebruiken in Azure Resource Manager-sjablonen.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: jgao
ms.openlocfilehash: 7ff91545b1b7ab1920f437e0c3a5410270efaac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153247"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Implementatiescripts gebruiken in sjablonen (Voorbeeld)

Meer informatie over het gebruik van implementatiescripts in Azure Resource-sjablonen. Met een nieuw `Microsoft.Resources/deploymentScripts`resourcetype genaamd kunnen gebruikers implementatiescripts uitvoeren in sjabloonimplementaties en uitvoeringsresultaten bekijken. Deze scripts kunnen worden gebruikt voor het uitvoeren van aangepaste stappen, zoals:

- gebruikers toevoegen aan een map
- een app-registratie maken
- gegevensvlakbewerkingen uitvoeren, bijvoorbeeld blobs kopiëren of zaaddatabase
- een licentiesleutel opzoeken en valideren
- een zelfondertekend certificaat maken
- een object maken in Azure AD
- IP-adresblokken opzoeken van het aangepaste systeem

De voordelen van implementatiescript:

- Gemakkelijk te coderen, te gebruiken en te debuggen. U implementatiescripts ontwikkelen in uw favoriete ontwikkelomgevingen. De scripts kunnen worden ingesloten in sjablonen of in externe scriptbestanden.
- U de scripttaal en het platform opgeven. Momenteel worden Azure PowerShell- en Azure CLI-implementatiescripts op de Linux-omgeving ondersteund.
- Geef het opgeven van de identiteiten toe die worden gebruikt om de scripts uit te voeren. Momenteel wordt alleen [de door azure toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) ondersteund.
- Hiermee u opdrachtregelargumenten doorgeven aan het script.
- Kan scriptuitvoer opgeven en deze doorgeven aan de implementatie.

> [!IMPORTANT]
> Twee implementatiescriptbronnen, een opslagaccount en een containerinstantie, worden gemaakt in dezelfde resourcegroep voor het uitvoeren van scripts en het oplossen van problemen. Deze resources worden meestal verwijderd door de scriptservice wanneer de implementatiescriptuitvoering in een terminalstatus wordt uitgevoerd. Er worden kosten in rekening gebracht voor de resources totdat de resources zijn verwijderd. Zie [Scriptbronnen voor het opschonen](#clean-up-deployment-script-resources)van implementatiescripts voor meer informatie .

## <a name="prerequisites"></a>Vereisten

- **Een door de gebruiker toegewezen beheerde identiteit met de rol van de bijdrager aan de doelgroep.** Deze identiteit wordt gebruikt om implementatiescripts uit te voeren. Als u bewerkingen buiten de resourcegroep wilt uitvoeren, moet u extra machtigingen verlenen. Wijs de identiteit bijvoorbeeld toe aan het abonnementsniveau als u een nieuwe resourcegroep wilt maken.

  > [!NOTE]
  > De implementatiescriptengine maakt een opslagaccount en een containerinstantie op de achtergrond.  Een door de gebruiker toegewezen beheerde identiteit met de rol van de inzender op abonnementsniveau is vereist als het abonnement het Azure-opslagaccount (Microsoft.Storage) en Azure container instance (Microsoft.ContainerInstance) resource (Microsoft.ContainerInstance) niet heeft geregistreerd Providers.

  Zie Een door [de gebruiker toegewezen beheerde identiteit maken met behulp van de Azure-portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)of [met Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)of met Azure [PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). U hebt de identiteits-id nodig wanneer u de sjabloon implementeert. Het formaat van de identiteit is:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Gebruik het volgende CLI- of PowerShell-script om de id te krijgen door de naam van de brongroep en de identiteitsnaam op te geven.

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[Powershell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** of **Azure CLI**. Zie [hier](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)voor een lijst met ondersteunde Azure PowerShell-versies ; zie [hier](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)voor een lijst met ondersteunde Azure CLI-versies.

    >[!IMPORTANT]
    > Implementatiescript maakt gebruik van de beschikbare CLI-afbeeldingen van Microsoft Container Registry (MCR). Het duurt ongeveer een maand om een CLI-afbeelding te certificeren voor implementatiescript. Gebruik de CLI-versies die binnen 30 dagen zijn uitgebracht niet. Zie [Azure CLI-releasenotes](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest)voor het vinden van de releasedatums voor de afbeeldingen. Als een niet-ondersteunde versie wordt gebruikt, worden in het foutbericht de ondersteunde versies weergegeven.

    U hebt deze versies niet nodig voor het implementeren van sjablonen. Maar deze versies zijn nodig voor het testen van implementatiescripts lokaal. Zie [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). U een vooraf geconfigureerde Docker-afbeelding gebruiken.  Zie [Ontwikkelomgeving configureren](#configure-development-environment).

## <a name="sample-templates"></a>Voorbeeldsjablonen

De volgende json is een voorbeeld.  Het laatste sjabloonschema is [hier](/azure/templates/microsoft.resources/deploymentscripts)te vinden.

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Het voorbeeld is voor demonstratiedoeleinden.  **scriptInhoud** en **primaireScriptUris** kunnen niet naast elkaar bestaan in een sjabloon.

Details van de waarde van onroerend goed:

- **Identiteit:** de implementatiescriptservice gebruikt een door de gebruiker toegewezen beheerde identiteit om de scripts uit te voeren. Momenteel wordt alleen door de gebruiker toegewezen beheerde identiteit ondersteund.
- **soort**: Geef het type script op. Momenteel worden Azure PowerShell- en Azure CLI-scripts ondersteund. De waarden zijn **AzurePowerShell** en **AzureCLI**.
- **forceUpdateTag:** Als u deze waarde wijzigt tussen sjabloonimplementaties, wordt het implementatiescript opnieuw uitgevoerd. Gebruik de functie newGuid() of utcNow() die moet worden ingesteld als de standaardwaarde van een parameter. Zie [Script meerdere keer uitvoeren voor](#run-script-more-than-once)meer dan één keer .
- **azPowerShellVersion**/**azCliVersion**: Geef de te gebruiken moduleversie op. Zie Voorwaarden voor een lijst met ondersteunde [PowerShell-](#prerequisites)en CLI-versies.
- **argumenten:** Geef de parameterwaarden op. De waarden worden gescheiden door spaties.
- **scriptInhoud:** geef de scriptinhoud op. Als u een extern `primaryScriptUri` script wilt uitvoeren, gebruikt u in plaats daarvan. Zie [Inline script gebruiken](#use-inline-scripts) en Extern script [gebruiken](#use-external-scripts)voor voorbeelden .
- **primaryScriptUri:** Geef een openbaar toegankelijke url op voor het primaire implementatiescript met ondersteunde bestandsextensies.
- **supportingScriptUris:** Geef een array met openbaar toegankelijke Url's `ScriptContent` op `PrimaryScriptUri`voor ondersteunende bestanden die in een van beide of .
- **time-out**: Geef de maximaal toegestane uitvoeringstijd van het script op die is opgegeven in de [ISO 8601-notatie](https://en.wikipedia.org/wiki/ISO_8601). Standaardwaarde is **P1D**.
- **opschoningVoorkeur**. Geef de voorkeur op voor het opschonen van implementatiebronnen wanneer de scriptuitvoering in een terminalstatus wordt uitgevoerd. Standaardinstelling is **Altijd**, wat betekent dat de resources worden verwijderd ondanks de terminalstatus (Geslaagd, Mislukt, Geannuleerd). Zie [Bronnen voor implementatiescript opschonen](#clean-up-deployment-script-resources)voor meer informatie .
- **retentionInterval**: Geef het interval op waarvoor de service de implementatiescriptbronnen behoudt nadat de implementatiescriptuitvoering een terminalstatus heeft bereikt. De bronnen voor implementatiescripts worden verwijderd wanneer deze duur afloopt. De duur is gebaseerd op het [ISO 8601-patroon](https://en.wikipedia.org/wiki/ISO_8601). De standaardwaarde is **P1D,** wat zeven dagen betekent. Deze eigenschap wordt gebruikt wanneer cleanupPreference is ingesteld *op OnExpiration*. De eigenschap *OnExpiration* is momenteel niet ingeschakeld. Zie [Bronnen voor implementatiescript opschonen](#clean-up-deployment-script-resources)voor meer informatie .

### <a name="additional-samples"></a>Aanvullende monsters

- [een certificaat maken en toewijzen aan een sleutelkluis](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [een door de gebruiker toegewezen beheerde identiteit maken en toewijzen aan een resourcegroep en een implementatiescript uitvoeren](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> Het wordt aanbevolen om vooraf een door de gebruiker toegewezen identiteit te maken en machtigingen toe te kennen. U aanmeldings- en machtigingsgerelateerde fouten krijgen als u de identiteit maakt en machtigingen verleent in dezelfde sjabloon waarin implementatiescripts worden uitgevoerd. Het duurt enige tijd voordat de machtigingen effectief worden.

## <a name="use-inline-scripts"></a>Inline-scripts gebruiken

In de volgende sjabloon is `Microsoft.Resources/deploymentScripts` één resource gedefinieerd met het type. Het gemarkeerde gedeelte is het inline script.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Omdat de inline-implementatiescripts zijn ingesloten in dubbele aanhalingstekens, moeten de tekenreeksen in de implementatiescripts in plaats daarvan worden ingesloten in afzonderlijke aanhalingstekens. Het escape-personage voor PowerShell is **&#92;. ** U ook overwegen om tekenreekssubstitutie te gebruiken zoals deze wordt weergegeven in het vorige JSON-voorbeeld. Zie de standaardwaarde van de parameter naam.

Het script neemt één parameter en voert de parameterwaarde uit. **DeploymentScriptOutputs** wordt gebruikt voor het opslaan van uitvoer.  In de sectie uitvoer geeft de **waarderegel** aan hoe u toegang krijgt tot de opgeslagen waarden. `Write-Output`wordt gebruikt voor het debuggen doel. Zie [Foutopsporingsimplementatiescripts](#debug-deployment-scripts)voor meer informatie over het openen van het uitvoerbestand.  Zie [Voorbeeldsjablonen voor](#sample-templates)de eigenschapsbeschrijvingen .

Als u het script wilt uitvoeren, selecteert **u Try it** to open the Cloud Shell en plakt u de volgende code in het shell-deelvenster.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

De uitvoer ziet er als volgt uit:

![Script voor het implementeren van resourcemanager-sjabloonen met hello World-uitvoer](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Externe scripts gebruiken

Naast inline scripts u ook externe scriptbestanden gebruiken. Alleen primaire PowerShell-scripts met de **ps1-bestandsextensie** worden ondersteund. Voor CLI-scripts kunnen de primaire scripts extensies hebben (of zonder extensie), zolang de scripts geldige bashscripts zijn. Als u externe scriptbestanden `primaryScriptUri`wilt gebruiken, vervangt u door `scriptContent` . Bijvoorbeeld:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Als u een voorbeeld wilt zien, selecteert u [hier](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

De externe scriptbestanden moeten toegankelijk zijn.  Zie [Zelfstudie: Beheer artefacten in Azure Resource Manager-sjabloonimplementaties](./template-tutorial-secure-artifacts.md)om uw scriptbestanden te beveiligen die zijn opgeslagen in Azure-opslagaccounts.

## <a name="use-supporting-scripts"></a>Ondersteunende scripts gebruiken

U ingewikkelde logica scheiden in een of meer ondersteunende scriptbestanden. Met `supportingScriptURI` de eigenschap u indien nodig een array van URI's aan de ondersteunende scriptbestanden verstrekken:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Ondersteunende scriptbestanden kunnen worden aangeroepen vanuit zowel inline scripts als primaire scriptbestanden. Ondersteunende scriptbestanden hebben geen beperkingen op de bestandsextensie.

De ondersteunende bestanden worden tijdens de runtime gekopieerd naar azscripts/azscriptinput. Gebruik relatief pad om te verwijzen naar de ondersteunende bestanden van inline scripts en primaire scriptbestanden.

## <a name="work-with-outputs-from-powershell-script"></a>Werken met uitvoer uit PowerShell-script

In de volgende sjabloon ziet u hoe u waarden doorgeeft tussen twee resources voor implementatieScripts:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

In de eerste resource definieert u een variabele die **$DeploymentScriptOutputs**wordt genoemd en gebruikt u deze om de uitvoerwaarden op te slaan. Gebruik het volgende om toegang te krijgen tot de uitvoerwaarde vanuit een andere bron binnen de sjabloon:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Werken met uitvoer uit CLI-script

Anders dan het PowerShell-implementatiescript stelt CLI/bash-ondersteuning een veelvoorkomende variabele niet bloot om scriptuitvoer op te slaan, in plaats daarvan is er een omgevingsvariabele die **AZ_SCRIPTS_OUTPUT_PATH** wordt genoemd die de locatie opslaat waar het scriptuitvoerbestand zich bevindt. Als een implementatiescript wordt uitgevoerd vanuit een Resource Manager-sjabloon, wordt deze omgevingsvariabele automatisch voor u ingesteld door de bash-shell.

Implementatiescriptuitvoer moet worden opgeslagen op de locatie AZ_SCRIPTS_OUTPUT_PATH en de uitvoer moet een geldig JSON-tekenreeksobject zijn. De inhoud van het bestand moet worden opgeslagen als een sleutelwaardepaar. Een array met tekenreeksen wordt bijvoorbeeld opgeslagen als { "MyResult": [ "foo", "bar"] }.  Het opslaan van alleen de arrayresultaten, bijvoorbeeld [ "foo", "bar" ], is ongeldig.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/) wordt gebruikt in het vorige monster. Het wordt geleverd met de container beelden. Zie [Ontwikkelomgeving configureren](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Implementatiescripts ontwikkelen

### <a name="handle-non-terminating-errors"></a>Niet-beëindigende fouten verwerken

U bepalen hoe PowerShell reageert op niet-beëindigende fouten met behulp van de [**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) variabele in uw implementatiescript. De implementatiescriptengine stelt de waarde niet in/wijzigt deze niet.  Ondanks de waarde die u voor $ErrorActionPreference instelt, stelt het implementatiescript de status voor het inrichten van resources in op *Mislukt* wanneer het script een fout ondervindt.

### <a name="pass-secured-strings-to-deployment-script"></a>Beveiligde tekenreeksen doorgeven aan implementatiescript

Door omgevingsvariabelen in uw containerinstanties in te stellen, kunt u dynamische configuratie mogelijk maken van de toepassing die of het script dat door de container wordt uitgevoerd. Implementatiescript verwerkt niet-beveiligde en beveiligde omgevingsvariabelen op dezelfde manier als Azure Container Instance. Zie [Omgevingsvariabelen instellen in containerinstanties](../../container-instances/container-instances-environment-variables.md#secure-values)voor meer informatie.

## <a name="debug-deployment-scripts"></a>Foutopsporingsimplementatiescripts

De scriptservice maakt een [opslagaccount](../../storage/common/storage-account-overview.md) en een [containerinstantie](../../container-instances/container-instances-overview.md) voor scriptuitvoering. Beide resources **azscripts** hebben het azscripts-achtervoegsel in de resourcenamen.

![Bronbeheersjabloontoewijzingsscriptnamen](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Het gebruikersscript, de uitvoeringsresultaten en het stdout-bestand worden opgeslagen in de bestanden van het opslagaccount. Er is een map genaamd **azscripts**. In de map zijn er nog twee mappen voor de invoer en de uitvoerbestanden: **azscriptinput** en **azscriptoutput**.

De uitvoermap bevat een **executionresult.json** en het scriptuitvoerbestand. U het foutbericht scriptuitvoering zien in **executionresult.json**. Het uitvoerbestand wordt alleen gemaakt wanneer het script is uitgevoerd. De invoermap bevat een PowerShell-scriptbestand voor het systeem en de scriptbestanden voor gebruikersimplementatie. U het scriptbestand voor gebruikersimplementatie vervangen door een herzien bestand en het implementatiescript opnieuw uitvoeren vanuit de Azure-containerinstantie.

U de implementatiegegevens van de implementatievan implementatieop basis van de brongroep en het abonnementsniveau opvragen met rest-API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

In het volgende voorbeeld wordt [ARMClient gebruikt:](https://github.com/projectkudu/ARMClient)

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

De uitvoer is vergelijkbaar met:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

De uitvoer toont de implementatiestatus en de implementatiescriptbron-id's.

De volgende REST-API retourneert het logboek:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Het werkt alleen voordat de bronnen voor implementatiescript worden verwijderd.

Als u de bron deploymentScripts in de portal wilt bekijken, selecteert **u Verborgen typen weergeven:**

![Script voor het implementeren van resourcebeheer-sjabloon, verborgen typen, portal weergeven](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Scriptbronnen voor implementatieopschonen

Implementatiescript maakt een opslagaccount en een containerinstantie die worden gebruikt voor het uitvoeren van implementatiescripts en het opslaan van foutopsporingsgegevens. Deze twee resources worden gemaakt in dezelfde resourcegroep als de ingerichte resources en worden door de scriptservice verwijderd wanneer het script verloopt. U de levenscyclus van deze middelen controleren.  Totdat ze worden verwijderd, wordt u gefactureerd voor beide bronnen. Zie [Containerinstances-prijzen](https://azure.microsoft.com/pricing/details/container-instances/) en [Azure Storage-prijzen](https://azure.microsoft.com/pricing/details/storage/)voor de prijsinformatie voor de prijsinformatie .

De levenscyclus van deze resources wordt gecontroleerd door de volgende eigenschappen in de sjabloon:

- **cleanupVoorkeur**: De voorkeur voor het opschonen wanneer de scriptuitvoering in een terminalstatus wordt.  De ondersteunde waarden zijn:

  - **Altijd:** Verwijder de resources zodra de uitvoering van het script in een terminalstatus wordt uitgevoerd. Aangezien de implementatieScripts-bron mogelijk nog steeds aanwezig is nadat de resources zijn opgeschoond, kopieert het systeemscript de resultaten van de scriptuitvoering, bijvoorbeeld stdout, uitvoer, retourwaarde, enz.
  - **OnSuccess**: Verwijder de resources alleen wanneer de uitvoering van het script is voltooid. U nog steeds toegang krijgen tot de bronnen om de foutopsporingsgegevens te vinden.
  - **OnExpir:** Verwijder de resources alleen wanneer de instelling **retentionInterval** is verlopen. Deze eigenschap is momenteel uitgeschakeld.

- **retentionInterval**: Geef het tijdsinterval op dat een scriptbron wordt behouden en waarna deze wordt verlopen en verwijderd.

> [!NOTE]
> Het wordt afgeraden om de bronnen voor implementatiescripts voor andere doeleinden te gebruiken.

## <a name="run-script-more-than-once"></a>Script meer dan één keer uitvoeren

Implementatiescriptuitvoering is een idempotente bewerking. Als geen van de resources-eigenschappen van deploymentScripts (inclusief het inlinescript) wordt gewijzigd, wordt het script niet uitgevoerd wanneer u de sjabloon opnieuw implementeert. De implementatiescriptservice vergelijkt de resourcenamen in de sjabloon met de bestaande resources in dezelfde resourcegroep. Er zijn twee opties als u hetzelfde implementatiescript meerdere keren wilt uitvoeren:

- Wijzig de naam van uw bron deploymentScripts. Gebruik bijvoorbeeld de [sjabloonfunctie utcNow](./template-functions-string.md#utcnow) als resourcenaam of als onderdeel van de resourcenaam. Als u de resourcenaam wijzigt, wordt een nieuwe bron voor deploymentScripts gemaakt. Het is goed voor het bijhouden van een geschiedenis van script uitvoering.

    > [!NOTE]
    > De utcNow-functie kan alleen worden gebruikt in de standaardwaarde voor een parameter.

- Geef een andere `forceUpdateTag` waarde op in de eigenschap template.  Gebruik utcNow bijvoorbeeld als waarde.

> [!NOTE]
> Schrijf de implementatiescripts die idempotent zijn. Dit zorgt ervoor dat als ze per ongeluk opnieuw worden uitgevoerd, dit geen systeemwijzigingen zal veroorzaken. Als het implementatiescript bijvoorbeeld wordt gebruikt om een Azure-bron te maken, controleert u of de bron niet bestaat voordat deze wordt gemaakt, zodat het script slaagt of u de bron niet opnieuw maakt.

## <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

U een vooraf geconfigureerde dockercontainerafbeelding gebruiken als de ontwikkelomgeving voor implementatiescript. In de volgende procedure ziet u hoe u de dockerafbeelding in Windows configureert. Voor Linux en Mac vindt u de informatie op het internet.

1. Installeer [Docker Desktop](https://www.docker.com/products/docker-desktop) op uw ontwikkelcomputer.
1. Docker-bureaublad openen.
1. Selecteer het pictogram Docker-bureaublad op taakbalken en selecteer **Vervolgens Instellingen**.
1. Selecteer **Gedeelde stations,** selecteer een lokaal station dat u beschikbaar wilt hebben voor uw containers en selecteer **Vervolgens Toepassen**

    ![Scriptdockerstation voor scriptdocker van resourcebeheer-sjabloon](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Voer uw windows-referenties in bij de prompt.
1. Open een terminalvenster, Command Prompt of Windows PowerShell (Gebruik geen PowerShell ISE).
1. De containerafbeelding van het implementatiescript naar de lokale computer trekken:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    In het voorbeeld wordt versie PowerShell 2.7.0 gebruikt.

    Een CLI-afbeelding uit een Microsoft Container Registry (MCR) halen:

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    In dit voorbeeld wordt versie CLI 2.0.80 gebruikt. Implementatiescript maakt gebruik van de standaard CLI-containers die [hier](https://hub.docker.com/_/microsoft-azure-cli)worden gevonden.

1. Voer de dockerafbeelding lokaal uit.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Vervang ** &lt;de>en** ** &lt;hostmapnaam>** vervangen door een bestaande map op het gedeelde station.  De map wordt toegewezen aan de **map /data** in de container. Als voorbeeld, om D:\docker in kaart te brengen:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-het** betekent dat het containerbeeld in leven moet worden gehouden.

    Een CLI-voorbeeld:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Selecteer **Delen** wanneer u een melding krijgt.
1. De volgende schermafbeelding laat zien hoe u een PowerShell-script uitvoert, aangezien u een helloworld.ps1-bestand hebt in de map d:\docker.

    ![Scriptdockd voor het implementeren van resourcebeheer-sjabloon](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Nadat het script is getest, u het gebruiken als implementatiescript.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u implementatiescripts gebruiken. Ga als lid van een zelfstudie voor implementatiescript:

> [!div class="nextstepaction"]
> [Zelfstudie: Implementatiescripts gebruiken in Azure Resource Manager-sjablonen](./template-tutorial-deployment-script.md)