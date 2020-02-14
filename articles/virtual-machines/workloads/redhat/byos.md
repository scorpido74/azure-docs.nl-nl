---
title: Red Hat Enterprise Linux uw eigen abonnement met Azure-installatie kopieën maken | Microsoft Docs
description: Meer informatie over het opnemen van uw eigen abonnements installatie kopieën voor Red Hat Enterprise Linux op Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: a54e1c9cbc9a80139d71a89f86ac51ecf5ce9902
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190501"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux uw gouden installatie kopieën met uw eigen abonnement in azure

Red Hat Enterprise Linux-installatie kopieën (RHEL) zijn beschikbaar in azure via een PAYG-model (betalen per gebruik) of uw eigen abonnement (Red Hat Gold image). Dit document bevat een overzicht van de Red Hat Gold-installatie kopieën in Azure.

## <a name="important-points-to-consider"></a>Belang rijke punten om rekening mee te houden

- De Red Hat Gold-installatie kopieën die in dit programma worden meegeleverd, zijn RHEL-installatie kopieën die geschikt zijn voor RHEL PAYG in de Azure Gallery/Marketplace.

- De afbeeldingen volgen onze huidige beleids regels die worden beschreven in [Red Hat Enterprise Linux installatie kopieën op Azure](./redhat-images.md)

- Standaard ondersteunings beleid is van toepassing op Vm's die zijn gemaakt op basis van deze installatie kopieën

- De Vm's die zijn ingericht op basis van Red Hat Gold-installatie kopieën, bevatten geen RHEL-kosten voor RHEL PAYG-installatie kopieën

- De installatie kopieën hebben geen recht, dus moet u abonnement-manager gebruiken om de Vm's te registreren en te abonneren om updates van Red Hat direct te ontvangen

- Het is momenteel niet mogelijk om dynamisch te scha kelen tussen BYOS-en PAYG-facturerings modellen voor Linux-installatie kopieën. Het opnieuw implementeren van de VM vanaf de betreffende installatie kopie is vereist om te scha kelen tussen het facturerings model

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Vereisten en voor waarden voor toegang tot de Red Hat Gold-installatie kopieën

1. Krijg kennis met de voor waarden van het [Red Hat Cloud Access-programma](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) en schakel uw Red Hat-abonnementen in voor Cloud toegang in [Red Hat Subscription Manager](https://access.redhat.com/management/cloud). U moet beschikken over de voor handen zijn van de Azure-abonnementen die u wilt registreren voor Cloud toegang.

1. Als u Red Hat-abonnementen hebt ingeschakeld voor Cloud toegang die voldoet aan de juiste geschiktheids vereisten, worden uw Azure-abonnement (en) automatisch ingeschakeld voor Gold image-toegang.

### <a name="expected-time-for-image-access"></a>Verwachte tijd voor toegang tot de installatie kopie

Bij het volt ooien van de activerings stappen voor Cloud toegang, wordt met Red Hat uw geschiktheid voor de Red Hat Gold-installatie kopieën gevalideerd. Als de validatie is geslaagd, ontvangt u binnen drie uur toegang tot de gouden installatie kopieën.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Gebruik de Red Hat Gold-installatie kopieën van de Azure Portal

1. Als uw Azure-abonnement toegang krijgt tot Red Hat Gold-installatie kopieën, kunt u deze vinden in de [Azure Portal](https://portal.azure.com) door te navigeren om **een resource te maken** en **alle weer te geven**.

1. Boven aan de pagina ziet u dat u persoonlijke aanbiedingen hebt.

    ![Privé aanbiedingen voor Marketplace](./media/rhel-byos-privateoffers.png)

1. U kunt klikken op de paarse koppeling of omlaag schuiven naar de onderkant van de pagina om uw persoonlijke aanbiedingen te bekijken.

1. De rest van het inrichten in de gebruikers interface is niet anders dan andere bestaande Red Hat-installatie kopieën. Kies uw RHEL-versie en volg de aanwijzingen om uw VM in te richten. Met dit proces kunt u ook de voor waarden van de afbeelding in de laatste stap accepteren.

>[!NOTE]
>Met deze stappen wordt de installatie kopie van Red Hat Gold niet voor een programmatische implementatie ingeschakeld. hiervoor is een extra stap vereist, zoals beschreven in de sectie ' aanvullende informatie ' hieronder.

De rest van dit document richt zich op de CLI-methode om de voor waarden van de installatie kopie in te richten en te accepteren. De gebruikers interface en CLI zijn volledig verwisselbaar, voor zover het eind resultaat (een ingerichte RHEL Gold image-VM) betreft.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Red Hat Gold-installatie kopieën van de Azure CLI gebruiken
De volgende reeks instructies begeleiden u bij het eerste implementatie proces voor een RHEL-VM met behulp van de Azure CLI. Bij deze instructies wordt ervan uitgegaan dat u de [Azure cli hebt geïnstalleerd](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Zorg ervoor dat u alle kleine letters in de Publisher-, aanbiedings-, plan-en afbeeldings verwijzingen gebruikt voor de volgende opdrachten:

1. Controleer of u zich in het gewenste abonnement bevindt:
    ```azurecli
    az account show -o=json
    ```

1. Een resource groep maken voor uw virtuele machine met Red Hat Gold image:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. De voor waarden van de installatie kopie accepteren:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Deze voor waarden moeten eenmaal worden geaccepteerd *per Azure-abonnement, per afbeeldings-SKU*.

1. Beschrijving Valideer uw VM-implementatie met de volgende opdracht:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Richt uw VM in door dezelfde opdracht uit te voeren als hierboven zonder het argument `--validate`:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH in uw virtuele machine en controleer of u een onrechtse installatie kopie hebt. U doet dit door `sudo yum repolist` uit te voeren (voor RHEL 8 use `sudo dnf repolist`). Met de uitvoer wordt u gevraagd om abonnement-manager te gebruiken voor het registreren van de virtuele machine met Red Hat.

>[!NOTE]
>Op RHEL 8 `dnf` en `yum` zijn uitwisselbaar. meer informatie hierover vindt u in de [RHEL 8-beheerders handleiding](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).


## <a name="use-the-red-hat-gold-images-from-powershell"></a>Red Hat Gold-installatie kopieën uit Power shell gebruiken
Hier volgt een voor beeld van een script. U moet de resource groep, de locatie, de VM-naam, de aanmeldings gegevens en andere variabelen vervangen door de configuratie van uw keuze. De uitgever en de plan informatie moeten kleine letters zijn.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Red Hat Enterprise Linux your-eigen abonnementen van Gold-installatie kopieën versleutelen

Red Hat Enterprise Linux uw installatie kopieën van uw eigen abonnementen kunnen worden beveiligd met behulp van [Azure Disk Encryption](../../linux/disk-encryption-overview.md). Het abonnement **moet** echter worden geregistreerd voordat versleuteling kan worden ingeschakeld.  Meer informatie over het registreren van een RHEL BYOS Gold-installatie kopie is beschikbaar op de Red Hat-site. Zie [een systeem registreren en abonneren op de Red Hat-klanten Portal met behulp van Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273); Als u een actief Red Hat-abonnement hebt, kunt u ook de [activerings sleutels voor Red Hat-klanten portal maken](https://access.redhat.com/articles/1378093)lezen.

Azure Disk Encryption wordt niet ondersteund op [aangepaste Red Hat-installatie kopieën](../../linux/redhat-create-upload-vhd.md). Aanvullende ADE-vereisten en-vereisten worden beschreven in [Azure Disk Encryption voor Linux-vm's](../../linux/disk-encryption-overview.md#additional-vm-requirements).

De stappen voor het Toep assen van Azure Disk Encryption zijn beschikbaar in [Azure Disk Encryption scenario's voor Linux-vm's](../../linux/disk-encryption-linux.md) en gerelateerde artikelen.

## <a name="additional-information"></a>Aanvullende informatie

- Als u probeert een virtuele machine in te richten op een abonnement dat niet is ingeschakeld voor deze aanbieding, wordt de volgende fout weer geven:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    Neem in dit geval contact op met micro soft of Red Hat om uw abonnement in te scha kelen.

- Als u een moment opname van een RHEL BYOS-installatie kopie wijzigt en probeert die aangepaste installatie kopie te publiceren naar de [Galerie met gedeelde afbeeldingen](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries), moet u plan gegevens opgeven die overeenkomen met de oorspronkelijke bron van de moment opname. De opdracht kan er bijvoorbeeld als volgt uitzien:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```
    Let op de plan parameters op de laatste regel hierboven.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) wordt niet ondersteund op aangepaste installatie kopieën.

- Als u Automation gebruikt om Vm's in te richten vanuit de RHEL BYOS-installatie kopieën, moet u plan parameters opgeven die vergelijkbaar zijn met wat hierboven is weer gegeven. Als u bijvoorbeeld terraform gebruikt, geeft u de plan gegevens op in een [plan blok](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Volgende stappen
- Stapsgewijze hand leidingen en programma Details voor Cloud toegang zijn beschikbaar in de [Red Hat Cloud Access-documentatie.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)
- Meer informatie over de [Azure Red Hat-update-infra structuur](./redhat-rhui.md).
- Ga naar de [documentatie pagina](./redhat-images.md)voor meer informatie over alle Red Hat-afbeeldingen in Azure.
- Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
- Meer documentatie over de RHEL-gouden installatie kopieën vindt u in de [documentatie van Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
