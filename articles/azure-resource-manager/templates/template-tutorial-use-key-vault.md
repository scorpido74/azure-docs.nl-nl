---
title: Azure Key Vault gebruiken in sjablonen
description: Ontdek hoe u Azure Key Vault gebruikt om veilig parameterwaarden door te geven tijdens het implementeren van Resource Manager-sjablonen
author: mumian
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 527002bee1bec4097cf62155efebae06880bad97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472392"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Zelf studie: Azure Key Vault integreren in de implementatie van de Resource Manager-sjabloon

Meer informatie over het ophalen van geheimen van een Azure-sleutel kluis en het door geven van geheimen als para meters wanneer u Azure Resource Manager implementeert. De parameter waarde wordt nooit weer gegeven, omdat u alleen naar de sleutel kluis-ID verwijst. Zie [Azure Key Vault gebruiken om de waarde van een beveiligde para meter door te geven tijdens de implementatie](./key-vault-parameter.md)voor meer informatie.

U maakt een virtuele machine (VM) in de zelf studie voor de [implementatie volgorde van resources instellen](./template-tutorial-create-templates-with-dependent-resources.md) . U moet de gebruikers naam en het wacht woord voor de VM-beheerder opgeven. In plaats van het wacht woord op te geven, kunt u het wacht woord vooraf opslaan in een Azure-sleutel kluis en vervolgens de sjabloon aanpassen om het wacht woord van de sleutel kluis tijdens de implementatie op te halen.

![Diagram waarin de integratie van een resource manager-sjabloon met een sleutel kluis wordt weer gegeven](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een sleutelkluis voorbereiden
> * Een snelstartsjabloon openen
> * Het parameterbestand bewerken
> * De sjabloon implementeren
> * De implementatie valideren
> * Resources opschonen

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio code met de extensie Resource Manager-Hulpprogram Ma's. Zie [Visual Studio code gebruiken om Azure Resource Manager sjablonen te maken](use-vs-code-to-create-template.md).
* Gebruik een gegenereerd wacht woord voor het beheerders account van de virtuele machine om de beveiliging te verbeteren. Hier volgt een voor beeld voor het genereren van een wacht woord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Controleer of het gegenereerde wacht woord voldoet aan de vereisten voor het VM-wacht woord. Elke Azure-service heeft eigen wachtwoordvereisten. Zie [Wat zijn de wachtwoord vereisten wanneer u een VM maakt?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)voor de vereisten voor het VM-wacht woord.

## <a name="prepare-a-key-vault"></a>Een sleutelkluis voorbereiden

In deze sectie maakt u een sleutel kluis en voegt u er een geheim aan toe, zodat u het geheim kunt ophalen wanneer u de sjabloon implementeert. Er zijn veel manieren om een sleutel kluis te maken. In deze zelf studie gebruikt u Azure PowerShell voor het implementeren van een [Resource Manager-sjabloon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Deze sjabloon doet het volgende:

* Hiermee maakt u een sleutel kluis waarvoor de eigenschap `enabledForTemplateDeployment` is ingeschakeld. Deze eigenschap moet *waar* zijn om het implementatie proces van de sjabloon toegang te geven tot de geheimen die in de sleutel kluis zijn gedefinieerd.
* Hiermee voegt u een geheim toe aan de sleutel kluis. In het geheim wordt het wacht woord van de VM-beheerder opgeslagen.

> [!NOTE]
> Als u de gebruiker die de virtuele-machine sjabloon implementeert en u niet de eigenaar van of een bijdrager aan de sleutel kluis bent, moet de eigenaar of een Inzender u toegang verlenen tot de machtiging *micro soft. sleutel kluis/kluizen/implementeren/actie* voor de sleutel kluis. Zie [Azure Key Vault gebruiken om een beveiligde parameter waarde door te geven tijdens de implementatie](./key-vault-parameter.md)voor meer informatie.

Als u het volgende Azure PowerShell script wilt uitvoeren, selecteert u **proberen het** te openen Azure Cloud shell. Als u het script wilt plakken, klikt u met de rechter muisknop op het deel venster shell en selecteert u vervolgens **Plakken**.

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
> * De naam van de resource groep is de naam van het project, maar met **RG** eraan toegevoegd. Als u de [resources die u in deze zelf studie hebt gemaakt eenvoudiger wilt opschonen](#clean-up-resources), moet u dezelfde project naam en resource groepsnaam gebruiken wanneer u [de volgende sjabloon implementeert](#deploy-the-template).
> * De standaard naam voor het geheim is **vmAdminPassword**. Het is hardcoded in de sjabloon.
> * Als u de sjabloon wilt inschakelen om het geheim op te halen, moet u een toegangs beleid met de naam ' toegang tot Azure Resource Manager voor sjabloon implementatie inschakelen ' inschakelen voor de sleutel kluis. Dit beleid is ingeschakeld in de sjabloon. Zie [sleutel kluizen en geheimen implementeren](./key-vault-parameter.md#deploy-key-vaults-and-secrets)voor meer informatie over het toegangs beleid.

De sjabloon heeft één uitvoer waarde, met de naam *keyVaultId*. Noteer de ID-waarde voor later gebruik wanneer u de virtuele machine implementeert. De indeling van de resource-ID is:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Wanneer u de ID kopieert en plakt, kan deze in meerdere regels worden onderverdeeld. De regels samen voegen en de extra spaties bijsnijden.

Als u de implementatie wilt valideren, voert u de volgende Power shell-opdracht uit in hetzelfde deel venster van de shell om het geheim in Lees bare tekst op te halen. De opdracht werkt alleen in dezelfde shell-sessie, omdat deze gebruikmaakt van de variabele *$keyVaultName*, die in het voor gaande Power shell-script is gedefinieerd.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Nu hebt u een sleutel kluis en een geheim voor bereid. In de volgende secties ziet u hoe u een bestaande sjabloon kunt aanpassen om het geheim op te halen tijdens de implementatie.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Azure-snelstartsjablonen is een opslagplaats voor Resource Manager-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelf studie wordt gebruikt, wordt [een eenvoudige Windows-VM implementeren](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)genoemd.

1. Selecteer in Visual Studio Code **File** > **Open File**.

1. Plak in het vak **Bestands naam** de volgende URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecteer **Openen** om het bestand te openen. Het scenario is hetzelfde als de versie die wordt gebruikt in de [zelf studie: maak Azure Resource Manager sjablonen met afhankelijke resources](./template-tutorial-create-templates-with-dependent-resources.md).
   In de sjabloon zijn vijf resources gedefinieerd:

   * `Microsoft.Storage/storageAccounts`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   Het is handig om een eenvoudige uitleg van de sjabloon te hebben voordat u deze aanpast.

1. Selecteer **bestand** > **Opslaan als**en sla een kopie van het bestand op uw lokale computer op met de naam *azuredeploy. json*.

1. Herhaal stap 1-3 om de volgende URL te openen en sla het bestand op als *azuredeploy. para meters. json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Het parameterbestand bewerken

U hoeft het sjabloonbestand niet te wijzigen.

1. Open *azuredeploy. para meters. json* in Visual Studio code als deze nog niet is geopend.
1. Werk de para meter `adminPassword` bij naar:

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
    > Vervang de waarde voor **id** door de resource-id van de sleutel kluis die u hebt gemaakt in de vorige procedure.

    ![Sleutel kluis en Resource Manager-sjabloon voor de implementatie van de virtuele machine integreren bestand](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Werk de volgende waarden bij:

    * **adminUsername**: de naam van het Administrator-account van de virtuele machine.
    * **dnsLabelPrefix**: Geef de dnsLabelPrefix-waarde een naam.

    Voor voor beelden van namen, zie de vorige afbeelding.

1. Sla de wijzigingen op.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Volg de instructies in [de sjabloon implementeren](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Upload zowel *azuredeploy. json* als *azuredeploy. para meters. json* naar Cloud shell en gebruik vervolgens het volgende Power shell-script om de sjabloon te implementeren:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Wanneer u de sjabloon implementeert, gebruikt u dezelfde resource groep die u in de sleutel kluis hebt gebruikt. Deze aanpak maakt het gemakkelijker om de resources op te schonen, omdat u slechts één resource groep moet verwijderen in plaats van twee.

## <a name="validate-the-deployment"></a>De implementatie valideren

Nadat u de virtuele machine hebt geïmplementeerd, moet u de aanmeldings referenties testen met behulp van het wacht woord dat is opgeslagen in de sleutel kluis.

1. Open de [Azure Portal](https://portal.azure.com).

1. Selecteer **resource groepen** >  **\<*YourResourceGroupName*>**  > **simpleWinVM**.
1. Selecteer bovenaan **verbinding maken** .
1. Selecteer **RDP-bestand downloaden**en volg de instructies om u aan te melden bij de virtuele machine met behulp van het wacht woord dat is opgeslagen in de sleutel kluis.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u uw Azure-resources niet meer nodig hebt, moet u de resources die u hebt geïmplementeerd, opschonen door de resource groep te verwijderen.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een geheim van uw Azure-sleutel kluis opgehaald. Vervolgens hebt u het geheim gebruikt in de implementatie van uw sjabloon. Voor meer informatie over het maken van gekoppelde sjablonen ziet u:

> [!div class="nextstepaction"]
> [Gekoppelde sjablonen maken](./template-tutorial-create-linked-templates.md)
