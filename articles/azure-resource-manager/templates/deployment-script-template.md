---
title: Implementatie scripts gebruiken in sjablonen | Microsoft Docs
description: gebruik implementatie scripts in Azure Resource Manager sjablonen.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: jgao
ms.openlocfilehash: 24a0891b57f67bfb78cf3699bddbcf8d345ee679
ms.sourcegitcommit: a3c6efa4d4a48e9b07ecc3f52a552078d39e5732
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83708003"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Implementatie scripts gebruiken in sjablonen (preview-versie)

Meer informatie over het gebruik van implementatie scripts in azure-resource sjablonen. Met een nieuw resource type `Microsoft.Resources/deploymentScripts` , kunnen gebruikers implementatie scripts uitvoeren in sjabloon implementaties en uitvoerings resultaten controleren. Deze scripts kunnen worden gebruikt voor het uitvoeren van aangepaste stappen zoals:

- gebruikers toevoegen aan een map
- bewerkingen voor gegevens vlak uitvoeren, bijvoorbeeld blobs of een Seed-data base kopiëren
- een licentie sleutel opzoeken en valideren
- een zelfondertekend certificaat maken
- een object maken in azure AD
- IP-adres blokken zoeken op basis van aangepast systeem

De voor delen van het implementatie script:

- Eenvoudig te coderen, gebruiken en fouten opsporen. U kunt implementatie scripts ontwikkelen in uw favoriete ontwikkel omgevingen. De scripts kunnen worden inge sloten in sjablonen of in externe script bestanden.
- U kunt de script taal en het platform opgeven. Momenteel worden Azure PowerShell-en Azure CLI-implementatie scripts in de Linux-omgeving ondersteund.
- Toestaan dat de identiteiten worden opgegeven die worden gebruikt voor het uitvoeren van de scripts. Op dit moment wordt alleen een door de [gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) ondersteund.
- Het door geven van opdracht regel argumenten naar het script toestaan.
- Kan script uitvoer opgeven en deze weer door geven aan de implementatie.

De bron van het implementatie script is alleen beschikbaar in de regio's waar Azure container instance beschikbaar is.  Zie de [Beschik baarheid van resources voor Azure container instances in azure-regio's](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Een opslag account en een container exemplaar zijn nodig voor het uitvoeren van scripts en het oplossen van problemen. U hebt de opties om een bestaand opslag account op te geven, anders wordt het opslag account samen met het container exemplaar automatisch gemaakt door de script service. De twee automatisch gemaakte resources worden doorgaans verwijderd door de script service wanneer de uitvoering van het implementatie script wordt uitgevoerd in een Terminal status. Er worden kosten in rekening gebracht voor de resources totdat de resources zijn verwijderd. Zie voor meer informatie het [opschonen van implementatie script bronnen](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Vereisten

- **Een door de gebruiker toegewezen beheerde identiteit met de rol van de mede werker van de doel resource groep**. Deze identiteit wordt gebruikt om implementatie scripts uit te voeren. Als u bewerkingen buiten de resource groep wilt uitvoeren, moet u extra machtigingen verlenen. Wijs de identiteit bijvoorbeeld toe aan het abonnements niveau als u een nieuwe resource groep wilt maken.

  > [!NOTE]
  > De script service maakt een opslag account (tenzij u een bestaand opslag account opgeeft) en een container exemplaar op de achtergrond.  Een door de gebruiker toegewezen beheerde identiteit met de rol van de Inzender op het abonnements niveau is vereist als het abonnement de Azure Storage-account (micro soft. Storage) en de resource providers van het Azure-container exemplaar (micro soft. ContainerInstance) niet heeft geregistreerd.

  Als u een identiteit wilt maken, raadpleegt u [een door de gebruiker toegewezen beheerde identiteit maken met behulp van de Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), of met behulp van [Azure cli](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)of met [behulp van Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). U hebt de identiteits-ID nodig wanneer u de sjabloon implementeert. De indeling van de identiteit is:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Gebruik het volgende CLI-of Power shell-script om de ID op te halen door de naam van de resource groep en de naam van de identiteit op te geven.

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** of **Azure cli**. Zie [hier](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)voor een lijst met ondersteunde versies van Azure PowerShell. Zie [hier](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)voor een lijst met ondersteunde versies van Azure cli.

    >[!IMPORTANT]
    > Het implementatie script maakt gebruik van de beschik bare CLI-installatie kopieën van micro soft Container Registry (MCR). Het duurt ongeveer één maand om een CLI-installatie kopie te certificeren voor het implementatie script. Gebruik de CLI-versies die binnen 30 dagen zijn uitgebracht. Zie opmerkingen bij de [release van Azure cli](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest)om de release datums voor de installatie kopieën te vinden. Als er een niet-ondersteunde versie wordt gebruikt, wordt het fout bericht weer gegeven met de ondersteunde versies.

    U hebt deze versies niet nodig voor het implementeren van sjablonen. Deze versies zijn echter nodig om implementatie scripts lokaal te testen. Zie [de Azure PowerShell-module installeren](/powershell/azure/install-az-ps). U kunt een vooraf geconfigureerde docker-installatie kopie gebruiken.  Zie [ontwikkel omgeving configureren](#configure-development-environment).

## <a name="sample-templates"></a>Voorbeeldsjablonen

De volgende JSON is een voor beeld.  Het meest recente sjabloon schema kunt u [hier](/azure/templates/microsoft.resources/deploymentscripts)vinden.

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
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
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
> Het voor beeld is voor demonstratie doeleinden.  **scriptContent** en **primaryScriptUris** kunnen niet worden gecombineerd in een sjabloon.

Details van eigenschaps waarde:

- **Identiteit**: de implementatie script service gebruikt een door de gebruiker toegewezen beheerde identiteit voor het uitvoeren van de scripts. Op dit moment wordt alleen door de gebruiker toegewezen beheerde identiteit ondersteund.
- **soort**: Geef het type script op. Momenteel worden Azure PowerShell-en Azure CLI-scripts ondersteund. De waarden zijn **AzurePowerShell** en **AzureCLI**.
- **updatetag**: als u deze waarde wijzigt tussen de implementaties van een sjabloon, wordt het implementatie script opnieuw uitgevoerd. Gebruik de functie newGuid () of utcNow () die moet worden ingesteld als de defaultValue van een para meter. Zie [script meer dan één keer uitvoeren](#run-script-more-than-once)voor meer informatie.
- **containerSettings**: Geef de instellingen op om Azure container instance aan te passen.  **containerGroupName** is voor het opgeven van de naam van de container groep.  Als u niets opgeeft, wordt de groeps naam automatisch gegenereerd.
- **storageAccountSettings**: Geef de instellingen op om een bestaand opslag account te gebruiken. Als u niets opgeeft, wordt er automatisch een opslag account gemaakt. Zie [een bestaand opslag account gebruiken](#use-an-existing-storage-account).
- **azPowerShellVersion** / **azCliVersion**: Geef de module versie op die moet worden gebruikt. Zie [vereisten](#prerequisites)voor een lijst met ondersteunde Power shell-en CLI-versies.
- **argumenten**: Geef de parameter waarden op. De waarden worden gescheiden door spaties.
- **omgevings variabelen**: Geef de omgevings variabelen op die moeten worden door gegeven aan het script. Zie [implementatie scripts ontwikkelen](#develop-deployment-scripts)voor meer informatie.
- **scriptContent**: Geef de script inhoud op. Als u een extern script wilt uitvoeren, gebruikt u `primaryScriptUri` in plaats daarvan. Zie [inline-script gebruiken](#use-inline-scripts) en [extern script gebruiken](#use-external-scripts)voor voor beelden.
- **primaryScriptUri**: Geef een openbaar toegankelijke URL op voor het primaire implementatie script met ondersteunde bestands extensies.
- **supportingScriptUris**: Geef een matrix met openbaar toegankelijke url's op voor de ondersteunende bestanden die worden genoemd in `ScriptContent` of `PrimaryScriptUri` .
- **time-out**: Geef de maximale toegestane uitvoerings tijd voor het script op dat is opgegeven in de [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601). De standaard waarde is **P1D**.
- **cleanupPreference**. Geef de voor keur op voor het opschonen van implementatie resources wanneer de uitvoering van het script wordt uitgevoerd in een Terminal status. De standaard instelling is **altijd**, wat betekent dat de resources worden verwijderd ondanks de status van de Terminal (geslaagd, mislukt, geannuleerd). Zie [implementatie script bronnen opschonen](#clean-up-deployment-script-resources)voor meer informatie.
- **retentionInterval**: Geef het interval op waarvoor de service de implementatie script bronnen behoudt nadat de uitvoering van het implementatie script een Terminal status heeft bereikt. De resources van het implementatie script worden verwijderd wanneer deze duur verloopt. De duur is gebaseerd op het [ISO 8601-patroon](https://en.wikipedia.org/wiki/ISO_8601). De standaard waarde is **P1D**. Dit betekent dat er zeven dagen zijn. Deze eigenschap wordt gebruikt wanneer cleanupPreference is ingesteld op *OnExpiration*. De eigenschap *OnExpiration* is momenteel niet ingeschakeld. Zie [implementatie script bronnen opschonen](#clean-up-deployment-script-resources)voor meer informatie.

### <a name="additional-samples"></a>Aanvullende voor beelden

- [een certificaat maken en toewijzen aan een sleutel kluis](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [een door de gebruiker toegewezen beheerde identiteit maken en toewijzen aan een resource groep en een implementatie script uitvoeren](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> U kunt het beste een door de gebruiker toegewezen identiteit maken en vooraf machtigingen verlenen. U kunt zich aanmelden en aan machtigingen gerelateerde fouten krijgen als u de identiteit maakt en machtigingen verleent in dezelfde sjabloon waar u implementatie scripts uitvoert. Het duurt enige tijd voordat de machtigingen van kracht worden.

## <a name="use-inline-scripts"></a>Inline-scripts gebruiken

Voor de volgende sjabloon is één resource gedefinieerd met het `Microsoft.Resources/deploymentScripts` type. Het gemarkeerde deel is het inline-script.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Omdat de inline-implementatie scripts tussen dubbele aanhalings tekens staan, moeten de teken reeksen in de implementatie scripts in plaats daarvan tussen enkele aanhalings tekens worden geplaatst. Het escape-teken voor Power shell is **&#92;**. U kunt ook overwegen om de teken reeks vervanging te gebruiken zoals deze wordt weer gegeven in het vorige JSON-voor beeld. Zie de standaard waarde van de para meter name.

Het script neemt één para meter en de waarde van de para meter. **DeploymentScriptOutputs** wordt gebruikt voor het opslaan van uitvoer.  In de sectie outputs ziet u hoe u met de regel **waarde** toegang krijgt tot de opgeslagen waarden. `Write-Output`wordt gebruikt voor fout opsporing. Zie [debug Deployment scripts](#debug-deployment-scripts)(Engelstalig) voor meer informatie over het openen van het uitvoer bestand.  Zie [voorbeeld sjablonen](#sample-templates)voor de beschrijving van de eigenschap.

Als u het script wilt uitvoeren, selecteert u **proberen** de Cloud shell te openen en plakt u de volgende code in het deel venster shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

De uitvoer ziet er als volgt uit:

![Implementatie script van Resource Manager-sjabloon Hello wereld-uitvoer](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Externe scripts gebruiken

Naast inline-scripts kunt u ook externe script bestanden gebruiken. Alleen primaire Power shell-scripts met de bestands extensie **PS1** worden ondersteund. Voor CLI-scripts kunnen de primaire scripts alle uitbrei dingen (of zonder een uitbrei ding) hebben, zolang de scripts geldige bash-scripts zijn. Als u externe script bestanden wilt gebruiken, vervangt u door `scriptContent` `primaryScriptUri` . Bijvoorbeeld:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Selecteer [hier](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)om een voor beeld te bekijken.

De externe script bestanden moeten toegankelijk zijn.  Zie [Private arm-sjabloon implementeren met SAS-token](./secure-template-with-sas-token.md)voor het beveiligen van uw script bestanden die zijn opgeslagen in azure Storage-accounts.

U bent verantwoordelijk voor het garanderen van de integriteit van de scripts waarnaar wordt verwezen door het implementatie script, ofwel **PrimaryScriptUri** of **SupportingScriptUris**.  Alleen verwijzen naar scripts die u vertrouwt.

## <a name="use-supporting-scripts"></a>Ondersteunende scripts gebruiken

U kunt complexe logica van elkaar scheiden in een of meer ondersteunende script bestanden. `supportingScriptURI`Met de eigenschap kunt u indien nodig een matrix met uri's naar de ondersteunende script bestanden opgeven:

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

Ondersteunende script bestanden kunnen worden aangeroepen vanuit inline scripts en primaire script bestanden. Ondersteunende script bestanden hebben geen beperkingen voor de bestands extensie.

De ondersteunende bestanden worden tijdens de runtime gekopieerd naar azscripts/azscriptinput. Gebruik relatief pad om te verwijzen naar de ondersteunende bestanden van inline-scripts en primaire script bestanden.

## <a name="work-with-outputs-from-powershell-script"></a>Werken met uitvoer van Power shell-script

De volgende sjabloon laat zien hoe waarden worden door gegeven tussen twee deploymentScripts-resources:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

In de eerste resource definieert u een variabele met de naam **$DeploymentScriptOutputs**en gebruikt u deze om de uitvoer waarden op te slaan. Als u de uitvoer waarde van een andere resource in de sjabloon wilt openen, gebruikt u:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Werken met uitvoer van CLI-script

Met de CLI/bash-ondersteuning van het Power shell-implementatie script wordt niet een gemeen schappelijke variabele voor het opslaan van script uitvoer weer gegeven, maar er is een omgevings variabele met de naam **AZ_SCRIPTS_OUTPUT_PATH** waarin de locatie wordt opgeslagen waar het bestand met script uitvoer zich bevindt. Als een implementatie script wordt uitgevoerd vanuit een resource manager-sjabloon, wordt deze omgevings variabele automatisch voor u ingesteld door de bash-shell.

Uitvoer van het implementatie script moet worden opgeslagen op de AZ_SCRIPTS_OUTPUT_PATH locatie en de uitvoer moet een geldig JSON-teken reeks object zijn. De inhoud van het bestand moet worden opgeslagen als een sleutel/waarde-paar. Een matrix met teken reeksen wordt bijvoorbeeld opgeslagen als {"MyResult": ["foo", "Bar"]}.  Alleen de matrix resultaten worden opgeslagen, bijvoorbeeld [' foo ', ' bar '], is ongeldig.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[JQ](https://stedolan.github.io/jq/) wordt in het vorige voor beeld gebruikt. Het wordt geleverd met de container installatie kopieën. Zie [ontwikkel omgeving configureren](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Implementatie scripts ontwikkelen

### <a name="handle-non-terminating-errors"></a>Niet-afsluit fouten verwerken

U kunt bepalen hoe Power shell reageert op niet-afsluit fouten met behulp van de [**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) variabele in uw implementatie script. De script service kan de waarde niet instellen of wijzigen.  Ondanks de waarde die u hebt ingesteld voor $ErrorActionPreference, stelt het implementatie script de inrichtings status van de resource in op *mislukt* als er een fout optreedt in het script.

### <a name="pass-secured-strings-to-deployment-script"></a>Beveiligde teken reeksen door geven aan implementatie script

Als omgevings variabelen (EnvironmentVariable) in uw container instanties worden ingesteld, kunt u een dynamische configuratie opgeven van de toepassing of het script dat door de container wordt uitgevoerd. Met het implementatie script worden niet-beveiligde en beveiligde omgevings variabelen op dezelfde manier verwerkt als Azure container instance. Zie [omgevings variabelen instellen in container instanties](../../container-instances/container-instances-environment-variables.md#secure-values)voor meer informatie.

De Maxi maal toegestane grootte voor omgevings variabelen is 64 kB.

## <a name="debug-deployment-scripts"></a>Fout opsporing voor implementatie scripts

De script service maakt een [opslag account](../../storage/common/storage-account-overview.md) (tenzij u een bestaand opslag account opgeeft) en een [container exemplaar](../../container-instances/container-instances-overview.md) voor het uitvoeren van een script. Als deze resources automatisch worden gemaakt door de script service, hebben beide bronnen het **azscripts** -achtervoegsel in de resource namen.

![Bron namen van Resource Manager-sjabloon implementatie script](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Het gebruikers script, de uitvoerings resultaten en het stdout-bestand worden opgeslagen in de bestands shares van het opslag account. Er bevindt zich een map met de naam **azscripts**. In de map zijn er twee mappen voor de invoer en de uitvoer bestanden: **azscriptinput** en **azscriptoutput**.

De uitvoermap bevat een **executionresult. json** en het script uitvoer bestand. U kunt het fout bericht voor het uitvoeren van scripts bekijken in **executionresult. json**. Het uitvoer bestand wordt alleen gemaakt wanneer het script is uitgevoerd. De map invoer bevat een script bestand voor het systeem-Power shell en de script bestanden voor de gebruikers implementatie. U kunt het script bestand voor de gebruikers implementatie vervangen door een herziene versie en het implementatie script opnieuw uitvoeren vanuit het Azure container-exemplaar.

U kunt de informatie over de implementatie script bron implementeren op het niveau van de resource groep en het abonnements niveau door gebruik te maken van REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

In het volgende voor beeld wordt [ARMClient](https://github.com/projectkudu/ARMClient)gebruikt:

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

De uitvoer is vergelijkbaar met:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

In de uitvoer ziet u de implementatie status en de resource-Id's van het implementatie script.

De volgende REST API retourneert het logboek:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Het werkt alleen als de implementatie script bronnen worden verwijderd.

Als u de deploymentScripts-resource in de portal wilt bekijken, selecteert u **verborgen typen weer geven**:

![Implementatie script van Resource Manager-sjabloon, verborgen typen weer geven, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="use-an-existing-storage-account"></a>Een bestaand opslag account gebruiken

Een opslag account en een container exemplaar zijn nodig voor het uitvoeren van scripts en het oplossen van problemen. U hebt de opties om een bestaand opslag account op te geven, anders wordt het opslag account samen met het container exemplaar automatisch gemaakt door de script service. De vereisten voor het gebruik van een bestaand opslag account:

- De volgende typen opslag accounts worden ondersteund: v2-, algemeen-en FileStorage-accounts voor algemeen gebruik. Alleen FileStorage ondersteunt Premium SKU. Zie [typen opslag accounts](../../storage/common/storage-account-overview.md)voor meer informatie.
- De firewall regels van het opslag account worden nog niet ondersteund. Raadpleeg [Firewalls en virtuele netwerken voor Azure Storage configureren](../../storage/common/storage-network-security.md) voor meer informatie.
- De door de gebruiker toegewezen beheerde identiteit van het implementatie script moet machtigingen hebben voor het beheren van het opslag account, waaronder lezen, maken, bestands shares verwijderen.

Als u een bestaand opslag account wilt opgeven, voegt u de volgende JSON toe aan het eigenschaps element van `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName**: Geef de naam van het opslag account op.
- **storageAccountKey "**: Geef een van de sleutels voor het opslag account op. U kunt de [`listKeys()`](./template-functions-resource.md#listkeys) functie gebruiken om de sleutel op te halen. Bijvoorbeeld:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Zie [voorbeeld sjablonen](#sample-templates) voor een volledige `Microsoft.Resources/deploymentScripts` definitie-voor beeld.

Wanneer een bestaand opslag account wordt gebruikt, maakt de script service een bestands share met een unieke naam. Zie [implementatie script bronnen opschonen](#clean-up-deployment-script-resources) voor de manier waarop de script service de bestands share opschoont.

## <a name="clean-up-deployment-script-resources"></a>Implementatie script resources opschonen

Een opslag account en een container exemplaar zijn nodig voor het uitvoeren van scripts en het oplossen van problemen. U hebt de mogelijkheid om een bestaand opslag account op te geven. anders wordt een opslag account samen met een container exemplaar automatisch door de script service gemaakt. De twee automatisch gemaakte resources worden verwijderd door de script service wanneer de uitvoering van het implementatie script wordt uitgevoerd in een Terminal status. Er worden kosten in rekening gebracht voor de resources totdat de resources zijn verwijderd. Zie [container instances prijzen](https://azure.microsoft.com/pricing/details/container-instances/) en [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor de prijs informatie.

De levens cyclus van deze resources wordt bepaald door de volgende eigenschappen in de sjabloon:

- **cleanupPreference**: de voor keur opschonen wanneer het script wordt uitgevoerd in een Terminal status. De ondersteunde waarden zijn:

  - **Altijd**: de automatisch gemaakte resources verwijderen wanneer de uitvoering van het script in een Terminal status krijgt. Als er een bestaand opslag account wordt gebruikt, wordt de bestands share die is gemaakt in het opslag account door de script service verwijderd. Omdat de deploymentScripts-resource mogelijk nog steeds aanwezig is nadat de resources zijn opgeruimd, blijven de script Services de resultaten van het script uitvoeren, bijvoorbeeld stdout, outputs, retour waarde enzovoort voordat de resources worden verwijderd.
  - **OnSuccess**: Verwijder de automatisch gemaakte resources alleen wanneer de uitvoering van het script is geslaagd. Als er een bestaand opslag account wordt gebruikt, wordt de bestands share alleen door de script service verwijderd wanneer de uitvoering van het script is geslaagd. U hebt nog steeds toegang tot de resources om de informatie over de fout opsporing te vinden.
  - **OnExpiration**: de automatische resources worden alleen verwijderd wanneer de instelling voor **retentionInterval** is verlopen. Als er een bestaand opslag account wordt gebruikt, wordt de bestands share door de script service verwijderd, maar blijft het opslag account behouden.

- **retentionInterval**: Geef het tijds interval op dat een script bron moet worden bewaard en waarna deze wordt verwijderd.

> [!NOTE]
> Het is niet raadzaam om het opslag account en het container exemplaar dat door de script service wordt gegenereerd voor andere doel einden te gebruiken. De twee resources kunnen worden verwijderd, afhankelijk van de levens cyclus van het script.

## <a name="run-script-more-than-once"></a>Script meer dan één keer uitvoeren

Uitvoering van het implementatie script is een idempotent-bewerking. Als geen van de deploymentScripts-resource-eigenschappen (met inbegrip van het inline-script) wordt gewijzigd, wordt het script niet uitgevoerd wanneer u de sjabloon opnieuw implementeert. De implementatie script service vergelijkt de resource namen in de sjabloon met de bestaande resources in dezelfde resource groep. Er zijn twee opties als u hetzelfde implementatie script meerdere keren wilt uitvoeren:

- Wijzig de naam van uw deploymentScripts-resource. Gebruik bijvoorbeeld de sjabloon [utcNow](./template-functions-date.md#utcnow) als de naam van de resource of als onderdeel van de resource naam. Als u de resource naam wijzigt, wordt er een nieuwe deploymentScripts-resource gemaakt. Het is handig om een geschiedenis van het uitvoeren van scripts te bewaren.

    > [!NOTE]
    > De functie utcNow kan alleen worden gebruikt in de standaard waarde voor een para meter.

- Geef een andere waarde op in de `forceUpdateTag` sjabloon eigenschap.  Gebruik bijvoorbeeld utcNow als waarde.

> [!NOTE]
> Schrijf de implementatie scripts die idempotent zijn. Dit zorgt ervoor dat als ze per ongeluk worden uitgevoerd, er geen systeem wijzigingen optreden. Als het implementatie script bijvoorbeeld wordt gebruikt om een Azure-resource te maken, moet u controleren of de resource niet bestaat voordat u deze maakt. het script slaagt dus of u maakt de resource niet opnieuw.

## <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

U kunt een vooraf geconfigureerde docker-container installatie kopie gebruiken als uw ontwikkel omgeving voor implementatie scripts. De volgende procedure laat zien hoe u de docker-installatie kopie kunt configureren in Windows. Voor Linux en Mac vindt u de informatie op internet.

1. Installeer [docker Desktop](https://www.docker.com/products/docker-desktop) op uw ontwikkel computer.
1. Open docker Desktop.
1. Selecteer het pictogram docker bureau blad op de taak balken en selecteer vervolgens **instellingen**.
1. Selecteer **gedeelde stations**, selecteer een lokaal station dat u beschikbaar wilt maken voor uw containers en selecteer vervolgens **Toep assen** .

    ![Docker-station voor implementatie script van Resource Manager-sjabloon](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Voer uw Windows-referenties in als u hierom wordt gevraagd.
1. Open een Terminal venster, een opdracht prompt of Windows Power shell (gebruik Power shell ISE niet).
1. Haal de installatie kopie van de implementatie script container op de lokale computer op:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    In het voor beeld wordt gebruikgemaakt van versie Power Shell 2.7.0.

    Een CLI-installatie kopie ophalen uit een micro soft Container Registry (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    In dit voor beeld wordt gebruikgemaakt van versie CLI 2.0.80. Het implementatie script maakt gebruik van de standaard installatie kopieën voor CLI-containers die [hier](https://hub.docker.com/_/microsoft-azure-cli)worden gevonden.

1. Voer de docker-installatie kopie lokaal uit.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Vervang de stationsletter van de ** &lt; host>** en de naam van de ** &lt; Host Directory>** door een bestaande map op het gedeelde station.  De map wordt toegewezen aan de map **/Data** in de container. Voor voor beelden, om D:\docker toe te wijzen:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-het** betekent dat de container installatie kopie actief blijft.

    Een CLI-voor beeld:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Selecteer **delen** als u een prompt krijgt.
1. In de volgende scherm afbeelding ziet u hoe u een Power shell-script uitvoert, aan de hand van een bestand HelloWorld. ps1 in de map d:\docker.

    ![Docker-opdracht voor implementatie script van Resource Manager-sjabloon](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Nadat het script is getest, kunt u het gebruiken als een implementatie script.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u implementatie scripts gebruikt. Een zelf studie over het implementatie script door lopen:

> [!div class="nextstepaction"]
> [Zelf studie: implementatie scripts gebruiken in Azure Resource Manager sjablonen](./template-tutorial-deployment-script.md)