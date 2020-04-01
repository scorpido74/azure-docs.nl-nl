---
title: Scripts voor sjabloonimplementatie gebruiken | Microsoft Documenten
description: Meer informatie over het gebruik van implementatiescripts in Azure Resource Manager-sjablonen.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 94b351ddb18ca596f47e8ef40cff8229c838d7bd
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239207"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Zelfstudie: Implementatiescripts gebruiken om een zelfondertekend certificaat te maken (Voorbeeld)

Meer informatie over het gebruik van implementatiescripts in ARM-sjablonen (Azure Resource Manage). Implementatiescripts kunnen worden gebruikt om aangepaste stappen uit te voeren die niet kunnen worden uitgevoerd met ARM-sjablonen. Bijvoorbeeld het maken van een zelfondertekend certificaat.  In deze zelfstudie maakt u een sjabloon om een `Microsoft.Resources/deploymentScripts` Azure-sleutelkluis te implementeren en gebruikt u vervolgens een bron in dezelfde sjabloon om een certificaat te maken en vervolgens het certificaat toe te voegen aan de sleutelkluis. Zie [Implementatiescripts gebruiken in ARM-sjablonen](./deployment-script-template.md)voor meer informatie over implementatiescript.

> [!IMPORTANT]
> Twee implementatiescriptbronnen, een opslagaccount en een containerinstantie, worden gemaakt in dezelfde resourcegroep voor het uitvoeren van scripts en het oplossen van problemen. Deze bronnen worden meestal verwijderd door de scriptservice wanneer de scriptuitvoering in een terminalstatus wordt. Er worden kosten in rekening gebracht voor de resources totdat de resources zijn verwijderd. Zie [Bronnen voor implementatiescript opschonen](./deployment-script-template.md#clean-up-deployment-script-resources)voor meer informatie .

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * De sjabloon bewerken
> * De sjabloon implementeren
> * Foutopsporing van het mislukte script
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* ** [Visual Studio Code](https://code.visualstudio.com/) met de extensie Hulpmiddelen voor Resource Manager**. Zie [Visual Studio Code gebruiken om ARM-sjablonen te maken.](./use-vs-code-to-create-template.md)

* **Een door de gebruiker toegewezen beheerde identiteit met de rol van de inzender op abonnementsniveau**. Deze identiteit wordt gebruikt om implementatiescripts uit te voeren. Zie Door [de gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)als u er een wilt maken. U hebt de identiteits-id nodig wanneer u de sjabloon implementeert. Het formaat van de identiteit is:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Gebruik het volgende PowerShell-script om de id op te halen door de naam van de resourcegroep en de identiteitsnaam op te geven.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

In plaats van een sjabloon helemaal opnieuw te maken, opent u een sjabloon in [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/). Azure Quickstart-sjablonen is een opslagplaats voor ARM-sjablonen.

De sjabloon die in deze quickstart wordt gebruikt, wordt [Een Azure Key Vault maken en een geheim genoemd.](https://azure.microsoft.com/resources/templates/101-key-vault-create/) De sjabloon maakt een sleutelkluis en voegt vervolgens een geheim toe aan de sleutelkluis.

1. Selecteer **Bestand**>**openen bestand**in Visual Studio-code .
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Selecteer **Openen** om het bestand te openen.
4. Selecteer **Bestand**>**opslaan als** om het bestand op te slaan als **azuredeploy.json** op uw lokale computer.

## <a name="edit-the-template"></a>De sjabloon bewerken

Breng de volgende wijzigingen aan in de sjabloon:

### <a name="clean-up-the-template-optional"></a>De sjabloon opschonen (optioneel)

De oorspronkelijke sjabloon voegt een geheim toe aan de sleutelkluis.  Verwijder de volgende bron om de zelfstudie te vereenvoudigen:

* **Microsoft.KeyVault/kluizen/geheimen**

Verwijder de volgende twee parameterdefinities:

* **secretName**
* **secretWaarde**

Als u ervoor kiest deze definities niet te verwijderen, moet u de parameterwaarden tijdens de implementatie opgeven.

### <a name="configure-the-key-vault-access-policies"></a>Het toegangsbeleid voor belangrijke kluizen configureren

Het implementatiescript voegt een certificaat toe aan de sleutelkluis. Configureer het toegangsbeleid voor sleutelkluizen om de beheerde identiteit toe te staan:

1. Voeg een parameter toe om de beheerde identiteits-id op te halen:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > De sjabloonextensie ResourceManager van Visual Studio Code kan nog geen implementatiescripts opmaken. Gebruik [SHIFT]+[ALT]+F niet om de resources deploymentScripts op te maken, zoals de volgende.

1. Voeg een parameter toe voor het configureren van het toegangsbeleid voor sleutelkluizen, zodat de beheerde identiteit certificaten kan toevoegen aan de sleutelkluis.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Werk het bestaande toegangsbeleid voor sleutelkluizen bij om:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Er zijn twee beleidsregels gedefinieerd, een voor de aangemelde gebruiker en de andere voor de beheerde identiteit.  De aangemelde gebruiker heeft alleen de *lijsttoestemming* nodig om de implementatie te verifiëren.  Om de zelfstudie te vereenvoudigen, wordt hetzelfde certificaat toegewezen aan zowel de beheerde identiteit als de aangemelde gebruikers.

### <a name="add-the-deployment-script"></a>Het implementatiescript toevoegen

1. Voeg drie parameters toe die door het implementatiescript worden gebruikt.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Een resource deploymentScripts toevoegen:

    > [!NOTE]
    > Omdat de inline-implementatiescripts zijn ingesloten in dubbele aanhalingstekens, moeten de tekenreeksen in de implementatiescripts in plaats daarvan worden ingesloten in afzonderlijke aanhalingstekens. Het escape-personage voor PowerShell is **&#92;. **

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    De `deploymentScripts` resource is afhankelijk van de resource van de sleutelkluis en de bron voor roltoewijzing.  Het heeft deze eigenschappen:

    * **identiteit:** Implementatiescript gebruikt een door de gebruiker toegewezen beheerde identiteit om de scripts uit te voeren.
    * **soort**: Geef het type script op. Momenteel is alleen PowerShell-script ondersteuning.
    * **forceUpdateTag:** bepaal of het implementatiescript moet worden uitgevoerd, zelfs als de scriptbron niet is gewijzigd. Kan huidige tijdstempel of een GUID zijn. Zie [Script meerdere keer uitvoeren voor](./deployment-script-template.md#run-script-more-than-once)meer dan één keer .
    * **azPowerShellVersion:** hiermee geeft u de Azure PowerShell-moduleversie op die moet worden gebruikt. Momenteel ondersteunt implementatiescript versie 2.7.0, 2.8.0 en 3.0.0.
    * **time-out**: Geef de maximaal toegestane uitvoeringstijd van het script op die is opgegeven in de [ISO 8601-notatie](https://en.wikipedia.org/wiki/ISO_8601). Standaardwaarde is **P1D**.
    * **argumenten:** Geef de parameterwaarden op. De waarden worden gescheiden door spaties.
    * **scriptInhoud:** geef de scriptinhoud op. Als u een extern script wilt uitvoeren, gebruikt u in plaats daarvan **primaireScriptURI.** Zie [Extern script gebruiken](./deployment-script-template.md#use-external-scripts)voor meer informatie.
        Het declareren **$DeploymentScriptOutputs** is alleen vereist bij het testen van het script op een lokale machine. Als u de variabele declareert, kan het script worden uitgevoerd op een lokale machine en in een implementatieScript-bron zonder dat u wijzigingen hoeft aan te brengen. De waarde die aan $DeploymentScriptOutputs is toegewezen, is beschikbaar als uitvoer in de implementaties. Zie [Werken met uitvoer uit PowerShell-implementatiescripts](./deployment-script-template.md#work-with-outputs-from-powershell-script) of Werken met uitvoer uit [CLI-implementatiescripts](./deployment-script-template.md#work-with-outputs-from-cli-script)voor meer informatie.
    * **opschoningSvoorkeur**: Geef de voorkeur op wanneer de bronnen voor implementatiescript moeten worden verwijderd.  De standaardwaarde is **Altijd**, wat betekent dat de bronnen voor implementatiescript worden verwijderd ondanks de terminalstatus (Geslaagd, Mislukt, geannuleerd). In deze zelfstudie wordt **OnSuccess** gebruikt, zodat u de kans krijgt om de resultaten van de uitvoering van het script te bekijken.
    * **retentionInterval**: Geef het interval op waarvoor de service de scriptbronnen behoudt nadat deze een terminalstatus heeft bereikt. Bronnen worden verwijderd wanneer deze duur afloopt. De duur is gebaseerd op het ISO 8601-patroon. Deze tutorial maakt gebruik van P1D, wat betekent dat een dag.  Deze eigenschap wordt gebruikt wanneer **cleanupPreference** is ingesteld **op OnExpiration**. Deze eigenschap is momenteel niet ingeschakeld.

    Het implementatiescript heeft drie parameters: de naam van de sleutelkluis, de naam van het certificaat en de onderwerpnaam.  Hiermee wordt een certificaat aanmaken en wordt het certificaat toegevoegd aan de sleutelkluis.

    **$DeploymentScriptOutputs** wordt gebruikt om de uitvoerwaarde op te slaan.  Zie [Werken met uitvoer uit PowerShell-implementatiescripts](./deployment-script-template.md#work-with-outputs-from-powershell-script) of Werken met uitvoer uit [CLI-implementatiescripts](./deployment-script-template.md#work-with-outputs-from-cli-script)voor meer informatie.

    De ingevulde sjabloon is [hier](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)te vinden.

1. Als u het foutopsporingsproces wilt zien, plaatst u een fout in de code door de volgende regel toe te voegen aan het implementatiescript:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    De juiste opdracht is **Write-Output** in plaats van **Write-Output1**.

1. Selecteer **Bestand**>**opslaan** om het bestand op te slaan.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Raadpleeg de sectie [De sjabloon implementeren](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template) in de Visual Studio Code snelstart voor het openen van de cloudshell en het uploaden van het sjabloonbestand naar de shell. En voer vervolgens het volgende PowerShell-script uit:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
$identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$resourceGroupName = "${projectName}rg"
$keyVaultName = "${projectName}kv"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

Write-Host "Press [ENTER] to continue ..."
```

De implementatiescriptservice moet extra implementatiescriptbronnen maken voor scriptuitvoering. De voorbereiding en het opruimproces kunnen tot een minuut in beslag nemen naast de werkelijke uitvoeringstijd van het script.

De implementatie is mislukt vanwege de ongeldige **opdracht, Write-Output1** wordt gebruikt in het script. U krijgt een foutmelding:

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

Het uitvoeringsresultaat voor implementatiescript wordt opgeslagen in de bronnen voor implementatiescript voor het probleemoplossingsdoel.

## <a name="debug-the-failed-script"></a>Foutopsporing van het mislukte script

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Open de resourcegroep. Het is de projectnaam met **rg** toegevoegd. U ziet twee extra resources in de resourcegroep. Deze resources worden *implementatiescriptresources*genoemd.

    ![Scriptbronnen voor het implementeren van resourcebeheer-sjabloon](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Beide bestanden hebben het **azscripts** achtervoegsel. Een daarvan is een opslagaccount en de andere is een containerexemplaar.

    Selecteer **Verborgen typen weergeven** om de bron deploymentScripts weer te geven.

1. Selecteer het opslagaccount **azscripts** met het azscripts-achtervoegsel.
1. Selecteer de tegel **Bestandsshares.** U ziet een **azscripts** map.  De map bevat de uitvoeringsbestanden voor implementatiescripts.
1. Selecteer **azscripts**. U ziet twee mappenL **azscriptinput** en **azscriptoutput**.  De invoermap bevat een PowerShell-scriptbestand voor het systeem en de scriptbestanden voor gebruikersimplementatie. De uitvoermap bevat een **executionresult.json** en het scriptuitvoerbestand. U het foutbericht zien in **executionresult.json**. Het uitvoerbestand is er niet omdat de uitvoering is mislukt.

Verwijder de regel **Write-Output1** en implementeer de sjabloon opnieuw.

Wanneer de tweede implementatie is uitgevoerd, worden de bronnen voor implementatiescripts verwijderd door de scriptservice, omdat de eigenschap **cleanupPreference** is ingesteld op **OnSuccess**.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u implementatiescript in ARM-sjablonen gebruiken. Informatie over het implementeren van Azure-resources op basis van voorwaarden vindt u in:

> [!div class="nextstepaction"]
> [Gebruiksvoorwaarden](./template-tutorial-use-conditions.md)
