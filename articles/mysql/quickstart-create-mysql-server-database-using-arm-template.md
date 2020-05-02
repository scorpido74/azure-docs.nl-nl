---
title: Een Azure-Data Base voor MySQL maken met een ARM-sjabloon
description: In dit artikel vindt u informatie over het maken van een Azure Database for MySQL server met virtuele netwerk integratie, met behulp van een Azure Resource Manager sjabloon.
services: azure-resource-manager
author: mgblythe
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 04/27/2020
ms.openlocfilehash: 7313d12509859514e41b30c4021f74f25a0e50b9
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629950"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-arm-template"></a>Snelstartgids: een Azure Database for MySQL-server maken met behulp van de ARM-sjabloon

Azure Database for MySQL is een beheerde service waarmee u MySQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze Quick start ziet u hoe u een vooraf gedefinieerde Azure Resource Manager (ARM)-sjabloon kunt gebruiken om een Azure Database for MySQL server te maken met Virtual Network-integratie. U kunt de-server maken met behulp van de Azure Portal, Azure CLI of Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/).
* Als u de code lokaal wilt uitvoeren, [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[CLI](#tab/CLI)

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/).
* Als u de code lokaal wilt uitvoeren, [Azure cli](/cli/azure/).

---

## <a name="create-an-azure-database-for-mysql-server"></a>Een Azure-database voor MySQL-server maken

U maakt een Azure Database voor MySQL-server met een gedefinieerde set reken- en opslagresources. Zie [Azure database for MySQL prijs categorieën](concepts-pricing-tiers.md)voor meer informatie. De server wordt gemaakt binnen een [Azure-resourcegroep](../azure-resource-manager/management/overview.md).

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet/)start-sjablonen.

:::code language="json" source="~/quickstart-templates/101-managed-mysql-with-vnet/azuredeploy.json" range="001-231" highlight="149,162,176,199,213":::

De sjabloon definieert vijf Azure-resources:

* [**Micro soft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Micro soft. Network/virtualNetworks/subnetten**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Micro soft. DBforMySQL/servers**](/azure/templates/microsoft.dbformysql/servers)
* [**Micro soft. DBforMySQL/servers/virtualNetworkRules**](/azure/templates/microsoft.dbformysql/servers/virtualnetworkrules)
* [**Micro soft. DBforMySQL/servers/firewallRules**](/azure/templates/microsoft.dbformysql/servers/firewallrules)

Meer Azure Database for MySQL sjabloon voorbeelden vindt u in de Quick Start- [sjabloon galerie](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformysql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>De sjabloon implementeren

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer de volgende koppeling om de Azure Database for MySQL Server sjabloon te implementeren in de Azure Portal:

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

Op de pagina **Azure database for MySQL implementeren met VNet** :

1. Voor **resource groep**selecteert u **nieuwe maken**, voert u een naam in voor de nieuwe resource groep en selecteert u **OK**.

2. Als u een nieuwe resource groep hebt gemaakt, selecteert u een **locatie** voor de resource groep en de nieuwe server.

3. Voer een **Server naam**, **beheerders aanmelding**en **aanmeldings wachtwoord**voor de beheerder in.

    ![Azure Database for MySQL implementeren met het VNet-venster, de Azure Quick Start-sjabloon Azure Portal](./media/quickstart-create-mysql-server-database-using-arm-template/deploy-azure-database-for-mysql-with-vnet.png)

4. Wijzig desgewenst de andere standaard instellingen:

    * **Abonnement**: het Azure-abonnement dat u wilt gebruiken voor de-server.
    * **SKU-capaciteit**: de capaciteit van de vCore, *die 2* kan zijn (de standaard instelling), *4*, *8*, *16*, *32*of *64*.
    * **SKU-naam**: het voor voegsel van de SKU-laag, de SKU-serie en de SKU-capaciteit, gekoppeld aan onderstrepings tekens, zoals *B_Gen5_1*, *GP_Gen5_2* (de standaard instelling) of *MO_Gen5_32*.
    * **SKU-grootte MB**: de opslag grootte, in mega bytes, van de Azure database for mysql-server (standaard *5120*).
    * **SKU-laag**: de implementatie-laag, *zoals Basic*, *GeneralPurpose* (de standaard instelling) of *MemoryOptimized*.
    * **SKU-familie**: *Gen4* of *GEN5* (de standaard instelling), die de hardware-generatie voor Server implementatie aangeeft.
    * **MySQL-versie**: de versie van de mysql-server die moet worden geïmplementeerd, zoals *5,6* of *5,7* (de standaard instelling).
    * **Bewaar dagen voor back-ups**: de gewenste periode voor geo-redundante back-upbewaaring, in dagen (standaard *7*).
    * **Geo redundante back-up**: *ingeschakeld* of *uitgeschakeld* (de standaard instelling), afhankelijk van de vereisten voor geo-nood herstel (geo-Dr).
    * **Virtual Network naam**: de naam van het virtuele netwerk (standaard *azure_mysql_vnet*).
    * **Subnetnaam**: de naam van het subnet (standaard *azure_mysql_subnet*).
    * **Virtual Network regel naam**: de naam van de regel van het virtuele netwerk waarmee het subnet kan worden (standaard *AllowSubnet*).
    * **Vnet-adres voorvoegsel**: het adres voorvoegsel voor het virtuele netwerk (standaard *10.0.0.0/16*).
    * **Subnetvoorvoegsel**: het adres voorvoegsel voor het subnet (standaard *10.0.0.0/16*).

5. Lees de voor waarden en selecteer **Ik ga akkoord met de bovenstaande voor waarden**.

6. Selecteer **Aankoop**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Gebruik de volgende interactieve code om een nieuwe Azure Database for MySQL server te maken met behulp van de sjabloon. De code vraagt u om de nieuwe server naam, de naam en de locatie van een nieuwe resource groep en een naam en wacht woord voor een beheerders account.

Als u de code in Azure Cloud Shell wilt uitvoeren, selecteert u **deze** in de linkerbovenhoek van een wille keurige code blok.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Gebruik de volgende interactieve code om een nieuwe Azure Database for MySQL server te maken met behulp van de sjabloon. De code vraagt u om de nieuwe server naam, de naam en de locatie van een nieuwe resource groep en een naam en wacht woord voor een beheerders account.

Als u de code in Azure Cloud Shell wilt uitvoeren, selecteert u **deze** in de linkerbovenhoek van een wille keurige code blok.

```azurecli-interactive
echo "Enter a name for the new Azure Database for MySQL server:" &&
read serverName &&
echo "Enter a name for the new resource group where the server will exist:" &&
read resourceGroupName &&
echo "Enter an Azure region (for example, centralus) for the resource group:" &&
read location &&
echo "Enter the Azure Database for MySQL server's administrator account name:" &&
read adminUser &&
echo "Enter the administrator password:" &&
read adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

# <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om een overzicht van de nieuwe Azure Database for MySQL-server weer te geven:

1. Zoek en selecteer **Azure database for MySQL servers**In de [Azure Portal](https://portal.azure.com).

2. Selecteer de nieuwe server in de lijst met data bases. De **overzichts** pagina voor de nieuwe Azure database for mysql-server wordt weer gegeven.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Voer de volgende interactieve code uit om details over uw Azure Database for MySQL-server weer te geven. U moet de naam van de nieuwe server invoeren.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/servers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Voer de volgende interactieve code uit om details over uw Azure Database for MySQL-server weer te geven. U moet de naam en de resource groep van de nieuwe server opgeven.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/servers"
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, verwijdert u de resource groep, waarmee de resources in de resource groep worden verwijderd.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zoek en selecteer in het [Azure Portal](https://portal.azure.com) **resource groepen**.

2. Kies in de lijst resource groep de naam van uw resource groep.

3. Selecteer op de pagina **overzicht** van de resource groep de optie **resource groep verwijderen**.

4. Typ in het bevestigings dialoogvenster de naam van de resource groep en selecteer vervolgens **verwijderen**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Volgende stappen

Voor een stapsgewijze zelf studie die u door het proces van het maken van een sjabloon leidt, raadpleegt u:

> [!div class="nextstepaction"]
> [Zelf studie: uw eerste Azure Resource Manager sjabloon maken en implementeren](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
