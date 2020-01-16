---
title: VM-extensies implementeren met een sjabloon
description: Informatie over het implementeren van extensies voor virtuele machines met Azure Resource Manager-sjablonen
author: mumian
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5379bf3c0a5127e5114ac819bd3e0e2ad12e8d69
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045251"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Zelfstudie: Extensies voor virtuele machines implementeren met Azure Resource Manager-sjablonen

Meer informatie over het gebruik van [extensies voor virtuele Azure-machines](../../virtual-machines/extensions/features-windows.md) voor het uitvoeren van configuratie- en automatiseringstaken na de implementatie op virtuele Azure-machines. Er zijn veel verschillende VM-extensies beschikbaar voor gebruik met Azure-VM's. In deze zelfstudie implementeert u een aangepaste scriptextensie van een Azure Resource Manager-sjabloon om een PowerShell-script uit te voeren op een Windows-VM.  Met het script wordt een webserver op de virtuele machine geïnstalleerd.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een PowerShell-script voorbereiden
> * Een snelstartsjabloon openen
> * De sjabloon bewerken
> * De sjabloon implementeren
> * De implementatie controleren

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio code met de extensie Resource Manager-Hulpprogram Ma's. Zie [Visual Studio code gebruiken om Azure Resource Manager sjablonen te maken](use-vs-code-to-create-template.md).
* Voor een verbeterde beveiliging gebruikt u een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in de Resource Manager-sjabloonimplementatie](./template-tutorial-use-key-vault.md) voor meer informatie. We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="prepare-a-powershell-script"></a>Een PowerShell-script voorbereiden

Een Power shell-script met de volgende inhoud wordt gedeeld vanuit [github](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Als u ervoor kiest het bestand naar uw eigen locatie te publiceren, moet u het element `fileUri` in de sjabloon later in de zelfstudie bijwerken.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Azure-snelstartsjablonen is een opslagplaats voor Resource Manager-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelfstudie wordt gebruikt, heet [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Een eenvoudige Windows-VM implementeren).

1. Selecteer in Visual Studio Code **File** > **Open File**.
1. Plak de volgende URL in het vak **File name**: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. Selecteer **Open** om het bestand te openen.
    In de sjabloon zijn vijf resources gedefinieerd:

   * **Microsoft.Storage/storageAccounts**. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft.Network/publicIPAddresses**. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft.Network/virtualNetworks**. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft.Network/networkInterfaces**. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft.Compute/virtualMachines**. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.

1. Selecteer **File** > **Save As** om een kopie van het bestand op uw lokale computer op te slaan als *azuredeploy.json*.

## <a name="edit-the-template"></a>De sjabloon bewerken

Voeg een VM-extensieresource toe aan de bestaande sjabloon met de volgende inhoud:

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Zie de [extensieverwijzing](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions) voor meer informatie over deze resourcedefinitie. Hier volgen enkele belangrijke elementen:

* **name**: omdat de extensiebron een onderliggende resource van het virtuele-machineobject is, moet de naam het voorvoegsel van de naam van de virtuele machine hebben. Zie [naam en type voor onderliggende resources instellen](child-resource-name-type.md).
* **dependsOn**: Maak de uitbreidings resource nadat u de virtuele machine hebt gemaakt.
* **fileUris**: de locaties waar de script bestanden worden opgeslagen. Als u ervoor kiest om de meegeleverde locaties niet te gebruiken, moet u de waarden bijwerken.
* **commandToExecute**: met deze opdracht wordt het script aangeroepen.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Zie de sectie ' de sjabloon implementeren ' in de [zelf studie: een Azure Resource Manager sjablonen met afhankelijke resources maken](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)voor de implementatie procedure. Het wordt aanbevolen een gegenereerd wachtwoord te gebruiken voor het beheerdersaccount van de virtuele machine. Zie de sectie [Vereisten](#prerequisites) van dit artikel voor meer informatie.

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Selecteer de VM in de Azure-portal.
1. Kopieer in het overzicht van de virtuele machine het IP-adres door **klikken te selecteren om te kopiëren**en plak het vervolgens op een browser tabblad. De welkomst pagina van de standaard Internet Information Services (IIS) wordt geopend:

![De welkomstpagina van Internet Information Services](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de geïmplementeerd Azure-resources niet meer nodig hebt, kunt u deze opschonen door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroepen** in het linkerdeelvenster van de Azure-portal.
2. Typ de naam van de resourcegroep in het vak **Filteren op naam**.
3. Selecteer de naam van de resourcegroep.
    De resourcegroep bevat zes resources.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie gebruikt u een virtuele machine en een VM-extensie. Door de extensie is de IIS-webserver op de virtuele machine geïnstalleerd. Zie dit artikel voor meer informatie over het gebruik van de Azure SQL Database-extensie om een BACPAC-bestand te importeren:

> [!div class="nextstepaction"]
> [SQL-extensies implementeren](./template-tutorial-deploy-sql-extensions-bacpac.md)
