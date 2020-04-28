---
title: VM-extensies implementeren met een sjabloon
description: Informatie over het implementeren van extensies voor virtuele machines met Azure Resource Manager-sjablonen
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 06d948b44064f029e00a2ef089077e9b55246545
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184959"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Zelf studie: extensies voor virtuele machines implementeren met ARM-sjablonen

Meer informatie over het gebruik van [extensies voor virtuele Azure-machines](../../virtual-machines/extensions/features-windows.md) voor het uitvoeren van configuratie- en automatiseringstaken na de implementatie op virtuele Azure-machines. Er zijn veel verschillende VM-extensies beschikbaar voor gebruik met Azure-VM's. In deze zelf studie implementeert u een aangepaste script extensie van een Azure Resource Manager ARM-sjabloon om een Power shell-script uit te voeren op een Windows-VM.  Met het script wordt een webserver op de virtuele machine geïnstalleerd.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een PowerShell-script voorbereiden
> * Een snelstartsjabloon openen
> * De sjabloon bewerken
> * De sjabloon implementeren

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Visual Studio code gebruiken om arm-sjablonen te maken](use-vs-code-to-create-template.md).
* Voor een verbeterde beveiliging gebruikt u een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [zelf studie: Azure Key Vault integreren in arm-sjabloon implementatie](./template-tutorial-use-key-vault.md)voor meer informatie. We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="prepare-a-powershell-script"></a>Een PowerShell-script voorbereiden

U kunt inline power shell-script of een script bestand gebruiken.  In deze zelf studie ziet u hoe u een script bestand gebruikt. Een Power shell-script met de volgende inhoud wordt gedeeld vanuit [github](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Als u ervoor kiest om het bestand op uw eigen locatie te publiceren, `fileUri` werkt u het element in de sjabloon later in de zelf studie bij.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Quick Start-sjablonen van Azure is een opslag plaats voor ARM-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelfstudie wordt gebruikt, heet [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Een eenvoudige Windows-VM implementeren).

1. Selecteer **bestand** > **openen**in Visual Studio code.
1. Plak de volgende URL in het vak **File name**: 

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecteer **Open** om het bestand te openen.
    In de sjabloon zijn vijf resources gedefinieerd:

   * [**Micro soft. Storage/Storage accounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Micro soft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Micro soft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Micro soft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Micro soft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Micro soft. Compute/informatie**](/azure/templates/microsoft.compute/virtualmachines).

     Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.

1. Selecteer **File** > **Save As** om een kopie van het bestand op uw lokale computer op te slaan als *azuredeploy.json*.

## <a name="edit-the-template"></a>De sjabloon bewerken

Voeg een VM-extensieresource toe aan de bestaande sjabloon met de volgende inhoud:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2019-12-01",
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

Als u inline script wilt gebruiken, verwijdert u **fileUris**en werkt u **commandToExecute** bij naar:

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

Met dit inline script wordt ook de inhoud van iisstart. html bijgewerkt.

U moet ook de HTTP-poort openen zodat u toegang hebt tot de webserver.

1. Zoek **securityRules** in de sjabloon.
1. Voeg de volgende regel toe naast **default-allow-3389**.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>De sjabloon implementeren

Zie de sectie ' de sjabloon implementeren ' in de [zelf studie: arm-sjablonen maken met afhankelijke resources](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)voor de implementatie procedure. Het wordt aanbevolen een gegenereerd wachtwoord te gebruiken voor het beheerdersaccount van de virtuele machine. Zie de sectie [Vereisten](#prerequisites) van dit artikel voor meer informatie.

Voer vanuit het Cloud Shell de volgende opdracht uit om het open bare IP-adres van de virtuele machine op te halen:

```azurepowershell
(Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName).IpAddress
```

Plak het IP-adres in een webbrowser. De gebruikelijke welkomstpagina van Internet Information Services (IIS) wordt geopend:

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
