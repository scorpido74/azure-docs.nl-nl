---
title: VM-extensies implementeren met sjabloon
description: Informatie over het implementeren van extensies voor virtuele machines met Azure Resource Manager-sjablonen
author: mumian
ms.date: 04/16/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 280b4a9775346c719e82d1fef4162fa6ea666798
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616878"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Zelfstudie: Virtuele machine-extensies implementeren met ARM-sjablonen

Meer informatie over het gebruik van [extensies voor virtuele Azure-machines](../../virtual-machines/extensions/features-windows.md) voor het uitvoeren van configuratie- en automatiseringstaken na de implementatie op virtuele Azure-machines. Er zijn veel verschillende VM-extensies beschikbaar voor gebruik met Azure-VM's. In deze zelfstudie implementeert u een Aangepaste Script-extensie vanuit een ARM-sjabloon (Azure Resource Manager) om een PowerShell-script op een Windows-vm uit te voeren.  Met het script wordt een webserver op de virtuele machine geïnstalleerd.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een PowerShell-script voorbereiden
> * Een snelstartsjabloon openen
> * De sjabloon bewerken
> * De sjabloon implementeren

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Visual Studio Code gebruiken om ARM-sjablonen te maken.](use-vs-code-to-create-template.md)
* Voor een verbeterde beveiliging gebruikt u een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in ARM-sjabloonimplementatie](./template-tutorial-use-key-vault.md). We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="prepare-a-powershell-script"></a>Een PowerShell-script voorbereiden

U inline PowerShell-script of een scriptbestand gebruiken.  In deze zelfstudie ziet u hoe u een scriptbestand gebruikt. Een PowerShell-script met de volgende inhoud wordt gedeeld vanuit [GitHub:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1)

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Als u ervoor kiest het bestand naar `fileUri` uw eigen locatie te publiceren, werkt u het element in de sjabloon later in de zelfstudie bij.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Azure Quickstart-sjablonen is een opslagplaats voor ARM-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelfstudie wordt gebruikt, heet [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Een eenvoudige Windows-VM implementeren).

1. Selecteer **Bestand** > **openen bestand**in Visual Studio-code .
1. Plak de volgende URL in het vak **File name**: 

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecteer **Open** om het bestand te openen.
    In de sjabloon zijn vijf resources gedefinieerd:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

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

* **name**: omdat de extensiebron een onderliggende resource van het virtuele-machineobject is, moet de naam het voorvoegsel van de naam van de virtuele machine hebben. Zie [Naam en type instellen voor onderliggende resources](child-resource-name-type.md).
* **dependsOn:** Maak de extensiebron nadat u de virtuele machine hebt gemaakt.
* **fileUris**: De locaties waar de scriptbestanden worden opgeslagen. Als u ervoor kiest om de meegeleverde locaties niet te gebruiken, moet u de waarden bijwerken.
* **commandToExecute:** Deze opdracht roept het script aan.

Als u inline script wilt gebruiken, verwijdert u **bestandUris**en werkt **u de opdracht Uitvoeren** bij naar:

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

Dit inline script werkt ook de iisstart.html-inhoud bij.

U moet ook de HTTP-poort openen, zodat u toegang hebt tot de webserver.

1. Zoek **beveiligingsregels** in de sjabloon.
1. Voeg de volgende regel toe naast **standaard-toestaan-3389**.

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

Zie voor de implementatieprocedure het gedeelte 'De sjabloon implementeren' van [Zelfstudie: ARM-sjablonen maken met afhankelijke resources](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Het wordt aanbevolen een gegenereerd wachtwoord te gebruiken voor het beheerdersaccount van de virtuele machine. Zie de sectie [Vereisten](#prerequisites) van dit artikel voor meer informatie.

Voer vanuit de Cloud Shell de volgende opdracht uit om het openbare IP-adres van de VM op te halen:

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
