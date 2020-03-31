---
title: 'Snelstart: Azure NetApp-bestanden en NFS-volume instellen'
description: Snelstart - Beschrijft hoe u Snel Azure NetApp-bestanden instelt en een volume maakt.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 12/01/2019
ms.openlocfilehash: fc7f13fb7ffe1667aaeaa4a3cc1916c6049a98c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75551638"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Snelstart: Azure NetApp-bestanden instellen en een NFS-volume maken 

In dit artikel ziet u hoe u Snel Azure NetApp-bestanden instelt en een volume maakt. 

In deze snelstart stelt u de volgende items in:

- Registratie voor Azure NetApp-bestanden en NetApp Resource Provider
- Een NetApp-account
- Een capaciteitspool
- Een NFS-volume voor Azure NetApp-bestanden

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint 

> [!IMPORTANT] 
> U moet toegang krijgen tot de Azure NetApp Files-service.  Zie de [opdewachtlijstindieningspagina](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u)van Azure NetApp Files om toegang tot de service te vragen.  U moet wachten op een officiële bevestigingse-mail van het Azure NetApp Files-team voordat u verdergaat. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registreren voor Azure NetApp-bestanden en NetApp Resource Provider

> [!NOTE]
> Dit kan enige tijd duren.
>

# <a name="portal"></a>[Portal](#tab/azure-portal)

Open een Cloud Shell-sessie zoals hierboven aangegeven voor registratiestappen met Portal en volg de volgende Azure CLI-stappen:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Voor dit artikel voor how-to's is de Azure PowerShell-module Az-versie 2.6.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om uw huidige versie te vinden. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u dat liever hebt, u in plaats daarvan cloudshell-console gebruiken in een PowerShell-sessie.

1. Geef in een PowerShell-opdrachtprompt (of PowerShell Cloud Shell-sessie) het abonnement op dat op de witte lijst staat voor Azure NetApp-bestanden:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Registreer de Azure Resource Provider:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

---

## <a name="create-a-netapp-account"></a>Een NetApp-account maken

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Voer in het zoekvak van de Azure-portal **Azure NetApp-bestanden** in en selecteer **vervolgens Azure NetApp-bestanden** in de lijst die wordt weergegeven.

      ![Azure NetApp-bestanden selecteren](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Klik op **+ Toevoegen** om een nieuw NetApp-account te maken.

     ![Nieuw NetApp-account maken](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Geef in het venster Nieuwe NetApp-account de volgende gegevens op: 
   1. Voer **mijn account1** in voor de accountnaam. 
   2. Selecteer uw abonnement.
   3. Selecteer **Nieuw maken** om een nieuwe resourcegroep te maken. Voer **myRG1** in voor de naam van de resourcegroep. Klik op **OK**. 
   4. Selecteer uw accountlocatie.  

      ![Nieuw venster NetApp-account](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Venster Resourcegroep](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Klik **op Maken** om uw nieuwe NetApp-account te maken.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Definieer enkele variabelen, zodat we er in de rest van de voorbeelden naar kunnen verwijzen:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ``` 

    > [!NOTE]
    > Raadpleeg [producten die per regio beschikbaar zijn](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) voor een lijst met ondersteunde regio's.
    > Als u de regionaam wilt verkrijgen die wordt ondersteund door onze opdrachtregelgereedschappen, gebruikt u`Get-AzLocation | select Location`
    >

1. Maak een nieuwe resourcegroep met de opdracht [Nieuw-AzResourceGroep:](/powershell/module/az.resources/new-azresourcegroup)

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Azure NetApp Files-account maken met de opdracht [Nieuw-AzNetAppBestandenAccount:](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount)
   
    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Definieer enkele variabelen, zodat we er in de rest van de voorbeelden naar kunnen verwijzen:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ``` 

    > [!NOTE]
    > Raadpleeg [producten die per regio beschikbaar zijn](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) voor een lijst met ondersteunde regio's.
    > Als u de regionaam wilt verkrijgen die wordt ondersteund door onze opdrachtregelgereedschappen, gebruikt u`az account list-locations -query "[].{Region:name}" --out table`
    >

2. Maak een nieuwe resourcegroep met de opdracht [Az-groep maken:](/cli/azure/group#az-group-create)

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Maak Azure NetApp Files-account met de opdracht [Az Netappfiles-account maken:](/cli/azure/netappfiles/account#az-netappfiles-account-create)
   
    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```
---

## <a name="set-up-a-capacity-pool"></a>Een capaciteitspool instellen

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in het beheerblad azure NetApp-bestanden uw NetApp-account **(myaccount1).**

    ![NetApp-account selecteren](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. Klik in het beheerblad azure NetApp-bestanden van uw NetApp-account op **Capaciteitsgroepen**.

    ![Klik op capaciteitsgroepen](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Klik **op + Pools toevoegen**. 

    ![Klik op Groepen toevoegen](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Informatie verstrekken voor de capaciteitsgroep: 
    1. Voer **mypool1** in als de naam van het zwembad.
    2. Selecteer **Premium** voor het serviceniveau. 
    3. Geef **4 (TiB)** op als de grootte van het zwembad. 

5. Klik op **OK**.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Enkele nieuwe variabelen definiëren voor toekomstige referentie

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Maak een nieuwe capaciteitspool met de [Nieuwe-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Enkele nieuwe variabelen definiëren voor toekomstige referentie

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Maak een nieuwe capaciteitspool met behulp van de [az netappfiles pool create](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) 

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>NFS-volume maken voor Azure NetApp-bestanden

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Klik in het beheerblad azure NetApp-bestanden van uw NetApp-account op **Volumes**.

    ![Klik op Volumes](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Klik op **Volume toevoegen**.

    ![Klik op Volumes toevoegen](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. Geef in het venster Een volume maken informatie op over het volume: 
   1. Voer **myvol1** in als de volumenaam. 
   2. Selecteer uw capaciteitspool **(mypool1).**
   3. Gebruik de standaardwaarde voor quota. 
   4. Klik onder virtueel netwerk op **Nieuw maken** om een nieuw Virtueel Azure-netwerk (Vnet) te maken.  Vul vervolgens de volgende informatie in:
       * Voer **myvnet1** in als de Vnet-naam.
       * Geef een adresruimte op voor uw instelling, bijvoorbeeld 10.7.0.0/16
       * Voer **myANFsubnet** in als subnetnaam.
       * Geef bijvoorbeeld het subnetadresbereik op: 10.7.0.0/24. U het speciale subnet niet delen met andere bronnen.
       * Selecteer **Microsoft.NetApp/volumes** voor subnetdelegatie.
       * Klik op **OK** om het Vnet te maken.
   5. Selecteer in subnet het nieuw gemaakte Vnet (**myvnet1**) als het subnet van de gemachtigde.

      ![Een volumevenster maken](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Virtueel netwerkvenster maken](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Klik op **Protocol**en voer de volgende acties uit: 
    * Selecteer **NFS** als protocoltype voor het volume.  
    * Voer **myfilepath1** in als het bestandspad dat wordt gebruikt om het exportpad voor het volume te maken.  
    * Selecteer de NFS-versie **(NFSv3** of **NFSv4.1)** voor het volume.  
      Zie [overwegingen](azure-netapp-files-create-volumes.md#considerations) en [aanbevolen procedures](azure-netapp-files-create-volumes.md#best-practice) over NFS-versies. 
      
  ![NFS-protocol opgeven voor snel start](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Klik op **Controleren + maken**.

    ![Venster controleren en maken](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

6. Controleer de informatie voor het volume en klik op **Maken**.  
    Het gemaakte volume wordt weergegeven in het blad Volumes.

    ![Volume gemaakt](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Maak een subnetdelegatie naar 'Microsoft.NetApp/volumes' met de opdracht [Nieuw-AzDelegatie.](/powershell/module/az.network/new-azdelegation)

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Maak een subnetconfiguratie met de opdracht [Nieuw-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Maak het virtuele netwerk met de opdracht [Nieuw-AzVirtualNetwork.](/powershell/module/az.network/new-azvirtualnetwork)
    
    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Maak het volume met de opdracht [Nieuw-AzNetAppBestandenVolume.](/powershell/module/az.netappfiles/new-aznetappfilesvolume)
   
    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Een aantal variabelen definiëren voor later gebruik.
    
    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Maak virtueel netwerk zonder subnet met behulp van de [opdracht vnet vnet van het AZ-netwerk.](/cli/azure/network/vnet#az-network-vnet-create)
    
    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Maak een gedelegeerd subnet met behulp van [de opdracht VNET-subnet van AZ-netwerk.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Maak het volume met behulp van de [opdracht az netappfiles volume maken.](/cli/azure/netappfiles/volume#az-netappfiles-volume-create)
   
    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

---

## <a name="clean-up-resources"></a>Resources opschonen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wanneer u klaar bent en als u dat wilt, u de brongroep verwijderen. De actie van het verwijderen van een resourcegroep is onomkeerbaar.  

> [!IMPORTANT]
> Alle bronnen binnen de resourcegroepen worden permanent verwijderd en kunnen niet ongedaan worden gemaakt. 

1. Voer in het zoekvak van de Azure-portal **Azure NetApp-bestanden** in en selecteer **vervolgens Azure NetApp-bestanden** in de lijst die wordt weergegeven.

2. Klik in de lijst met abonnementen op de resourcegroep (myRG1) die u wilt verwijderen. 

    ![Navigeren naar resourcegroepen](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Klik op de pagina resourcegroep op **Resourcegroep verwijderen**.

    ![Resourcegroep verwijderen](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Een venster wordt geopend en toont een waarschuwing over de resources die worden verwijderd met de resourcegroep.

4. Voer de naam van de resourcegroep (myRG1) in om te bevestigen dat u de brongroep en alle bronnen erin permanent wilt verwijderen en klik op **Verwijderen**.

    ![Resourcegroep verwijderen](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Wanneer u klaar bent en als u dat wilt, u de brongroep verwijderen. De actie van het verwijderen van een resourcegroep is onomkeerbaar.  

> [!IMPORTANT]
> Alle bronnen binnen de resourcegroepen worden permanent verwijderd en kunnen niet ongedaan worden gemaakt.

1. Verwijder de brongroep met de opdracht [Verwijderen-AzResourceGroep.](/powershell/module/az.resources/remove-azresourcegroup)
   
    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Wanneer u klaar bent en als u dat wilt, u de brongroep verwijderen. De actie van het verwijderen van een resourcegroep is onomkeerbaar.  

> [!IMPORTANT]
> Alle bronnen binnen de resourcegroepen worden permanent verwijderd en kunnen niet ongedaan worden gemaakt.

1. Verwijder de brongroep met de opdracht Verwijderen van de [AZ-groep.](/cli/azure/group#az-group-delete)
   
    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```
---

## <a name="next-steps"></a>Volgende stappen  

> [!div class="nextstepaction"]
> [Volumes beheren met Azure NetApp-bestanden](azure-netapp-files-manage-volumes.md)  
