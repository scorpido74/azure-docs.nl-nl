---
title: Azure Key Vault gebruiken in sjablonen
description: Ontdek hoe u Azure Key Vault gebruikt om veilig parameterwaarden door te geven tijdens het implementeren van Resource Manager-sjablonen
author: mumian
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: a305914c5c870543e16c515880955693c2634044
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239181"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Zelfstudie: Azure Key Vault integreren in de implementatie van uw ARM-sjabloon

Meer informatie over het ophalen van geheimen uit een Azure-sleutelkluis en het doorgeven van de geheimen als parameters wanneer u een ARM-sjabloon (Azure Resource Manager) implementeert. De parameterwaarde wordt nooit weergegeven, omdat u alleen verwijst naar de sleutelkluis-ID. Zie [Azure Key Vault gebruiken om veilige parameterwaarde door te geven tijdens de implementatie](./key-vault-parameter.md)voor meer informatie.

In de zelfstudie [Van de toewijzingsvolgorde voor resources](./template-tutorial-create-templates-with-dependent-resources.md) instellen maakt u een virtuele machine (VM). U moet de gebruikersnaam en het wachtwoord van de VM-beheerder opgeven. In plaats van het wachtwoord op te geven, u het wachtwoord vooraf opslaan in een Azure-sleutelkluis en vervolgens de sjabloon aanpassen om het wachtwoord tijdens de implementatie uit de sleutelkluis op te halen.

![Diagram met de integratie van een Resource Manager-sjabloon met een sleutelkluis](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een sleutelkluis voorbereiden
> * Een snelstartsjabloon openen
> * Het parameterbestand bewerken
> * De sjabloon implementeren
> * De implementatie valideren
> * Resources opschonen

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Visual Studio Code gebruiken om ARM-sjablonen te maken.](use-vs-code-to-create-template.md)
* Gebruik een gegenereerd wachtwoord voor het VM-beheerdersaccount om de veiligheid te verhogen. Hier is een voorbeeld voor het genereren van een wachtwoord:

    ```console
    openssl rand -base64 32
    ```
    Controleer of het gegenereerde wachtwoord voldoet aan de vereisten voor VM-wachtwoorden. Elke Azure-service heeft eigen wachtwoordvereisten. Zie Wat zijn de wachtwoordvereisten voor de vm-wachtwoordvereisten [wanneer u een vm maakt?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Een sleutelkluis voorbereiden

In deze sectie maakt u een sleutelkluis en voegt u er een geheim aan toe, zodat u het geheim ophalen wanneer u uw sjabloon implementeert. Er zijn vele manieren om een sleutelkluis te maken. In deze zelfstudie gebruikt u Azure PowerShell om een [ARM-sjabloon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json)te implementeren. Deze sjabloon doet het volgende:

* Hiermee maakt u `enabledForTemplateDeployment` een sleutelkluis met de eigenschap ingeschakeld. Deze eigenschap moet *waar* zijn voordat het implementatieproces van de sjabloon toegang heeft tot de geheimen die zijn gedefinieerd in de sleutelkluis.
* Hiermee voegt u een geheim toe aan de sleutelkluis. Het geheim slaat het wachtwoord van de VM-beheerder op.

> [!NOTE]
> Als de gebruiker die de sjabloon voor virtuele machines implementeert, moet de eigenaar of een inzender u toegang verlenen tot de *microsoft.KeyVault/vaults/deploy/action-toestemming* voor de sleutelkluis als u niet de eigenaar van of een bijdrager aan de sleutelkluis bent. Zie [Azure Key Vault gebruiken om een veilige parameterwaarde tijdens de implementatie door te geven](./key-vault-parameter.md)voor meer informatie.

Als u het volgende Azure PowerShell-script wilt uitvoeren, selecteert **u Proberen** azure cloud shell te openen. Als u het script wilt plakken, klikt u met de rechtermuisknop op het deelvenster shell en selecteert u **Plakken**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

> [!IMPORTANT]
> * De naam van de resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd. Gebruik dezelfde projectnaam en de naam van de brongroep wanneer u [de volgende sjabloon implementeert,](#deploy-the-template)om het gemakkelijker te maken om de resources op te [schonen die u in deze zelfstudie hebt gemaakt.](#clean-up-resources)
> * De standaardnaam voor het geheim is **vmAdminPassword**. Het is hardgecodeerd in de sjabloon.
> * Als u de sjabloon wilt inschakelen om het geheim op te halen, moet u voor de sleutelkluis een toegangsbeleid inschakelen met de naam 'Toegang tot Azure Resource Manager inschakelen voor sjabloonimplementatie'. Dit beleid is ingeschakeld in de sjabloon. Zie [Sleutelkluizen en geheimen implementeren](./key-vault-parameter.md#deploy-key-vaults-and-secrets)voor meer informatie over het toegangsbeleid.

De sjabloon heeft één uitvoerwaarde, *keyVaultId*genaamd. Noteer de ID-waarde voor later gebruik, wanneer u de virtuele machine implementeert. De resource-id-indeling is:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Wanneer u de id kopieert en plakt, kan deze in meerdere regels worden opgesplitst. Voeg de lijnen samen en knip de extra spaties bij.

Als u de implementatie wilt valideren, voert u de volgende PowerShell-opdracht uit in hetzelfde shellvenster om het geheim in duidelijke tekst op te halen. De opdracht werkt alleen in dezelfde shellsessie, omdat deze de variabele *$keyVaultName*gebruikt, die is gedefinieerd in het vorige PowerShell-script.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Nu heb je een sleutelkluis en een geheim voorbereid. In de volgende secties ziet u hoe u een bestaande sjabloon aanpassen om het geheim tijdens de implementatie op te halen.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Azure Quickstart-sjablonen is een opslagplaats voor ARM-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelfstudie wordt gebruikt, wordt [Een eenvoudige Windows-VM implementeren](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)genoemd.

1. Selecteer **Bestand** > **openen bestand**in Visual Studio-code .

1. Plak de volgende URL in het vak **File name**: 

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecteer **Openen** om het bestand te openen. Het scenario is hetzelfde als het scenario dat wordt gebruikt in [Zelfstudie: ARM-sjablonen maken met afhankelijke resources.](./template-tutorial-create-templates-with-dependent-resources.md)
   In de sjabloon zijn vijf resources gedefinieerd:

   * `Microsoft.Storage/storageAccounts`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   Het is handig om een basiskennis van de sjabloon te hebben voordat u deze aanpast.

1. Selecteer **Bestand** > **opslaan als**, en sla vervolgens een kopie van het bestand op uw lokale computer op met de naam *azuredeploy.json*.

1. Herhaal stap 1-3 om de volgende URL te openen en sla het bestand op als *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Het parameterbestand bewerken

U hoeft het sjabloonbestand niet te wijzigen.

1. Open *azuredeploy.parameters.json* in Visual Studio Code als deze nog niet is geopend.
1. Werk `adminPassword` de parameter bij naar:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Vervang de waarde voor **id** door de resource-id van de sleutelkluis die u in de vorige procedure hebt gemaakt.

    ![Bestand sleutelkluis en Resource Manager-sjabloon sjabloon-implementatieparameters integreren](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Werk de volgende waarden bij:

    * **adminUsername**: De naam van het account voor virtuele machinebeheerder.
    * **dnsLabelPrefix**: Geef de waarde van dnsLabelPrefix een naam.

    Zie voor voorbeelden van namen de voorgaande afbeelding.

1. Sla de wijzigingen op.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Volg de instructies in [De sjabloon implementeren](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Upload zowel *azuredeploy.json* als *azuredeploy.parameters.json* naar Cloud Shell en gebruik vervolgens het volgende PowerShell-script om de sjabloon te implementeren:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Wanneer u de sjabloon implementeert, gebruikt u dezelfde resourcegroep die u in het sleutelkluishebt gebruikt. Deze aanpak maakt het gemakkelijker voor u om de resources op te schonen, omdat u slechts één resourcegroep hoeft te verwijderen in plaats van twee.

## <a name="validate-the-deployment"></a>De implementatie valideren

Nadat u de virtuele machine hebt geïmplementeerd, test u de aanmeldingsreferenties met het wachtwoord dat is opgeslagen in de sleutelkluis.

1. Open de [Azure Portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** > **\<*YourResourceGroupName*>** > **simpleWinVM**.
1. Selecteer **verbinding** maken aan de bovenkant.
1. Selecteer **RDP-bestand downloaden**en volg de instructies om u aan te melden bij de virtuele machine met behulp van het wachtwoord dat is opgeslagen in de sleutelkluis.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u uw Azure-resources niet meer nodig hebt, ruimt u de resources op die u hebt geïmplementeerd door de brongroep te verwijderen.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een geheim uit uw Azure-sleutelkluis opgehaald. Vervolgens gebruikte u het geheim in uw sjabloonimplementatie. Voor meer informatie over het maken van gekoppelde sjablonen ziet u:

> [!div class="nextstepaction"]
> [Gekoppelde sjablonen maken](./template-tutorial-create-linked-templates.md)
