---
title: 'Quick Start: Azure NetApp Files instellen en een NFS-volume maken | Microsoft Docs'
description: 'Quick Start: hier wordt beschreven hoe u Azure NetApp Files snel instelt en een volume maakt.'
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 9/11/2019
ms.author: b-juche
ms.openlocfilehash: d7bc07ddce605838cf7aa966c6c94b85dad6b58c
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212214"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Quickstart: Azure NetApp Files instellen en een NFS-volume maken 

In dit artikel wordt beschreven hoe u Azure NetApp Files snel kunt instellen en een volume kunt maken. 

In deze Quick start gaat u de volgende items instellen:

- Registratie voor de resource provider Azure NetApp Files en NetApp
- Een NetApp-account
- Een capaciteits pool
- Een NFS-volume voor Azure NetApp Files

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint 

> [!IMPORTANT] 
> U moet toegang krijgen tot de Azure NetApp Files-service.  Als u toegang tot de service wilt aanvragen, raadpleegt u de [pagina Azure NetApp files Waitlist-verzen ding](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u).  U moet wachten op een officieel bevestigings bericht van het Azure NetApp Files team voordat u doorgaat. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registreren voor de resource provider Azure NetApp Files en NetApp

> [!NOTE]
> Dit kan enige tijd duren.
>

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Voor registratie tappen met behulp van Portal opent u een Cloud Shell-sessie zoals hierboven aangegeven en volgt u deze stappen in azure CLI:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Voor dit procedure-artikel is de Azure PowerShell AZ versie 2.6.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om uw huidige versie te vinden. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u wilt, kunt u in plaats daarvan Cloud Shell-console in een Power shell-sessie gebruiken.

1. Geef in een Power shell-opdracht prompt (of Power shell Cloud Shell-sessie) het abonnement op dat is white list voor Azure NetApp Files:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. De Azure-resource provider registreren:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

---

## <a name="create-a-netapp-account"></a>Een NetApp-account maken

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Voer in het zoekvak van de Azure Portal **Azure NetApp files** in en selecteer vervolgens **Azure NetApp files** in de lijst die wordt weer gegeven.

      ![Azure NetApp Files selecteren](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Klik op **+ Toevoegen** om een nieuw NetApp-account te maken.

     ![Nieuw NetApp-account maken](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Geef in het venster nieuwe NetApp-account de volgende informatie op: 
   1. Voer **MyAccount1** in als de account naam. 
   2. Selecteer uw abonnement.
   3. Selecteer **nieuwe maken** om een nieuwe resource groep te maken. Voer **myRG1** in als naam van de resource groep. Klik op **OK**. 
   4. Selecteer de locatie van uw account.  

      ![Venster nieuwe NetApp-account](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Venster Resource groep](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Klik op **maken** om uw nieuwe NetApp-account te maken.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definieer enkele variabelen zodat we deze in de rest van de voor beelden kunnen raadplegen:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ``` 

    > [!NOTE]
    > Raadpleeg de [beschik bare producten per regio](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=netapp&regions=all) voor een lijst met ondersteunde regio's.
    > Gebruik voor het verkrijgen van de regio naam die wordt ondersteund door onze opdracht regel Programma's`Get-AzLocation | select Location`
    >

1. Maak een nieuwe resource groep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Azure NetApp Files-account maken met de opdracht [New-AzNetAppFilesAccount](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount) :
   
    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Definieer enkele variabelen zodat we deze in de rest van de voor beelden kunnen raadplegen:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ``` 

    > [!NOTE]
    > Raadpleeg de [beschik bare producten per regio](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=netapp&regions=all) voor een lijst met ondersteunde regio's.
    > Gebruik voor het verkrijgen van de regio naam die wordt ondersteund door onze opdracht regel Programma's`az account list-locations -query "[].{Region:name}" --out table`
    >

2. Maak een nieuwe resource groep met behulp van de opdracht [AZ Group Create](/cli/azure/group#az-group-create) :

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Maak Azure NetApp Files account met de opdracht [AZ netappfiles account create](/cli/azure/netappfiles/account#az-netappfiles-account-create) :
   
    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```
---

## <a name="set-up-a-capacity-pool"></a>Een capaciteitspool instellen

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Selecteer uw NetApp-account (**MyAccount1**) op de blade Azure NetApp files beheer.

    ![NetApp-account selecteren](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. Klik op de Blade Azure NetApp Files beheer van uw NetApp-account op **capaciteits groepen**.

    ![Klik op capaciteits Pools](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Klik op **+ groepen toevoegen**. 

    ![Klik op groepen toevoegen](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Geef informatie op voor de capaciteits groep: 
    1. Voer **mypool1** in als de naam van de groep.
    2. Selecteer **Premium** voor het service niveau. 
    3. Geef **4 (Tib)** op als Pool grootte. 

5. Klik op **OK**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Enkele nieuwe variabelen definiëren voor toekomstige referentie

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Een nieuwe capaciteits groep maken met behulp van de [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Enkele nieuwe variabelen definiëren voor toekomstige referentie

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Een nieuwe capaciteits groep maken met behulp van de [AZ netappfiles pool Create](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) 

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

## <a name="create-nfs-volume-for-azure-netapp-files"></a>NFS-volume maken voor Azure NetApp Files

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Klik op **volumes**op de blade Azure NetApp files beheer van uw NetApp-account.

    ![Klik op volumes](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Klik op **Volume toevoegen**.

    ![Klik op volumes toevoegen](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. Geef in het venster een volume maken informatie op voor het volume: 
   1. Voer **myvol1** in als de naam van het volume. 
   2. Selecteer uw capaciteits pool (**mypool1**).
   3. Gebruik de standaard waarde voor quotum. 
   4. Klik onder virtueel netwerk op **nieuwe maken** om een nieuw virtueel Azure-netwerk (Vnet) te maken.  Vul vervolgens de volgende gegevens in:
       * Voer **myvnet1** in als de Vnet-naam.
       * Geef een adres ruimte voor uw instelling op, bijvoorbeeld 10.7.0.0/16
       * Voer **myANFsubnet** in als de naam van het subnet.
       * Geef het adres bereik van het subnet op, bijvoorbeeld 10.7.0.0/24. Houd er rekening mee dat u het toegewezen subnet niet met andere resources kunt delen.
       * Selecteer **micro soft. NetApp/volumes** voor subnet delegering.
       * Klik op **OK** om het Vnet te maken.
   5. Selecteer in subnet de zojuist gemaakte Vnet (**myvnet1**) als het subnet van de gemachtigde.

      ![Een volume venster maken](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Het venster virtueel netwerk maken](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Klik op **protocol**en selecteer vervolgens **NFS** als protocol type voor het volume.   

    Voer **myfilepath1** in als het bestandspad dat wordt gebruikt voor het maken van het exportpad voor het volume. 

    ![NFS-protocol voor Quick Start opgeven](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Klik op **Controleren + maken**.

    ![Venster controleren en maken](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

6. Controleer de informatie voor het volume en klik vervolgens op **maken**.  
    Het gemaakte volume wordt weer gegeven op de Blade volumes.

    ![Volume gemaakt](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Maak een subnet delegering naar ' micro soft. NetApp/volumes ' met de opdracht [New-AzDelegation](/powershell/module/az.network/new-azdelegation) .

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Maak een subnet-configuratie met behulp van de opdracht [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) .

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Maak het virtuele netwerk met behulp van de opdracht [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) .
    
    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Maak het volume met behulp van de opdracht [New-AzNetAppFilesVolume](/powershell/module/az.netappfiles/new-aznetappfilesvolume) .
   
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

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Een aantal variabelen definiëren voor later gebruik.
    
    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Maak een virtueel netwerk zonder subnet met behulp van de opdracht [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create) .
    
    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Maak een gedelegeerd subnet met behulp van [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) opdracht.

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Maak het volume met behulp van de opdracht [AZ netappfiles volume Create](/cli/azure/netappfiles/volume#az-netappfiles-volume-create) .
   
    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

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
        --creation-token $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

---

## <a name="clean-up-resources"></a>Resources opschonen

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Wanneer u klaar bent en u wilt, kunt u de resource groep verwijderen. De actie voor het verwijderen van een resource groep is onomkeerbaar.  

> [!IMPORTANT]
> Alle resources in de resource groepen worden permanent verwijderd en kunnen niet ongedaan worden gemaakt. 

1. Voer in het zoekvak van de Azure Portal **Azure NetApp files** in en selecteer vervolgens **Azure NetApp files** in de lijst die wordt weer gegeven.

2. Klik in de lijst met abonnementen op de resource groep (myRG1) die u wilt verwijderen. 

    ![Navigeren naar resource groepen](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Klik op de pagina resource groep op **resource groep verwijderen**.

    ![Resourcegroep verwijderen](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Een venster wordt geopend en toont een waarschuwing over de resources die worden verwijderd met de resourcegroep.

4. Voer de naam van de resource groep (myRG1) in om te bevestigen dat u de resource groep en alle resources daarin permanent wilt verwijderen en klik vervolgens op **verwijderen**.

    ![Resourcegroep verwijderen](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Wanneer u klaar bent en u wilt, kunt u de resource groep verwijderen. De actie voor het verwijderen van een resource groep is onomkeerbaar.  

> [!IMPORTANT]
> Alle resources in de resource groepen worden permanent verwijderd en kunnen niet ongedaan worden gemaakt.

1. Verwijder de resource groep met behulp van de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .
   
    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

Wanneer u klaar bent en u wilt, kunt u de resource groep verwijderen. De actie voor het verwijderen van een resource groep is onomkeerbaar.  

> [!IMPORTANT]
> Alle resources in de resource groepen worden permanent verwijderd en kunnen niet ongedaan worden gemaakt.

1. Verwijder de resource groep met de opdracht [AZ Group delete](/cli/azure/group#az-group-delete) .
   
    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```
---

## <a name="next-steps"></a>Volgende stappen  

> [!div class="nextstepaction"]
> [Volumes beheren met Azure NetApp Files](azure-netapp-files-manage-volumes.md)  
