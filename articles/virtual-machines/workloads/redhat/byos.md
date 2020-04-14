---
title: Red Hat Enterprise Linux bring-your-own-subscription Azure images | Microsoft Documenten
description: Meer informatie over afbeeldingen met uw eigen abonnement voor Red Hat Enterprise Linux op Azure.
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
ms.openlocfilehash: 9ab578b4b688c02c9150dfb23fce53fbb82df405
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273168"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux brengt uw eigen abonnement Gold Images in Azure

Red Hat Enterprise Linux (RHEL) afbeeldingen zijn beschikbaar in Azure via een pay-as-you-go of bring-your-own-subscription (BYOS) (Red Hat Gold Image) model. Dit artikel geeft een overzicht van de Red Hat Gold Images in Azure.

>[!NOTE]
> RHEL BYOS Gold Images zijn beschikbaar in Azure Public (commerciële) en Azure Government clouds. Ze zijn niet beschikbaar in Azure China- of Azure Blackforest-clouds.

## <a name="important-points-to-consider"></a>Belangrijke punten om te overwegen

- De Red Hat Gold-afbeeldingen in dit programma zijn rhel-afbeeldingen die klaar zijn voor productie, vergelijkbaar met de RHEL-pay-as-you-go-afbeeldingen in Azure Marketplace.
- De afbeeldingen volgen het huidige beleid beschreven in [Red Hat Enterprise Linux-afbeeldingen op Azure](./redhat-images.md).
- Standaardondersteuningsbeleid is van toepassing op VM's die zijn gemaakt met deze afbeeldingen.
- De VM's van Red Hat Gold Images dragen geen RHEL-kosten in verband met RHEL-pay-as-you-go-afbeeldingen.
- De beelden hebben geen recht. U moet Red Hat Subscription-Manager gebruiken om de VM's te registreren en u te abonneren om updates van Red Hat rechtstreeks te ontvangen.
- Het is momenteel niet mogelijk om dynamisch te schakelen tussen BYOS en pay-as-you-go factureringsmodellen voor Linux-afbeeldingen. Als u het factureringsmodel wilt vervangen, moet u de VM opnieuw implementeren vanuit de desbetreffende afbeelding.

>[!NOTE]
> Generatie 2 RHEL BYOS-afbeeldingen zijn momenteel niet beschikbaar via het marketplace-aanbod. Als u een Generation 2 RHEL BYOS-afbeelding nodig hebt, gaat u naar het Cloud Access-dashboard in Red Hat-abonnementsbeheer. Zie voor meer informatie de [Red Hat-documentatie.](https://access.redhat.com/articles/4847681)

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Vereisten en voorwaarden om toegang te krijgen tot de Red Hat Gold Images

1. Maak kennis met de voorwaarden van het [Red Hat Cloud Access-programma.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) Schakel uw Red Hat-abonnementen in voor Cloud Access bij [Red Hat Subscription-Manager](https://access.redhat.com/management/cloud). U moet de Azure-abonnementen bij de hand hebben die worden geregistreerd voor Cloud Access.

1. Als de Red Hat-abonnementen die u hebt ingeschakeld voor Cloud Access voldoen aan de geschiktheidsvereisten, worden uw Azure-abonnementen automatisch ingeschakeld voor toegang tot Gold Image.

### <a name="expected-time-for-image-access"></a>Verwachte tijd voor toegang tot afbeeldingen

Nadat u de enablementstappen voor Cloud Access hebt voltooid, valideert Red Hat of u in aanmerking komt voor de Red Hat Gold-afbeeldingen. Als de validatie succesvol is, krijgt u binnen drie uur toegang tot de Gold Images.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>De Red Hat Gold-afbeeldingen gebruiken vanuit de Azure-portal

1. Nadat uw Azure-abonnement toegang heeft tot Red Hat Gold Images, u ze vinden in de [Azure-portal.](https://portal.azure.com) Ga naar Een > **resourceweerweer weermaken.** **Create a Resource**

1. Boven aan de pagina zie je dat je privéaanbiedingen hebt.

    ![Privéaanbiedingen op Marktplaats](./media/rhel-byos-privateoffers.png)

1. Selecteer de paarse koppeling of blader omlaag naar de onderkant van de pagina om uw privéaanbiedingen te bekijken.

1. De rest van provisioning in de UI is niet anders dan alle andere bestaande Red Hat afbeelding. Kies uw RHEL-versie en volg de aanwijzingen om uw vm in te richten. Met dit proces u ook de voorwaarden van de afbeelding bij de laatste stap accepteren.

>[!NOTE]
>Deze stappen tot nu toe maken uw Red Hat Gold Image niet mogelijk voor programmatische implementatie. Een extra stap is vereist zoals beschreven in de sectie "Aanvullende informatie".

De rest van dit document richt zich op de CLI-methode voor het inrichten en accepteren van termen op de afbeelding. De Gebruikersinterface en CLI zijn volledig uitwisselbaar wat betreft het eindresultaat (een ingerichte RHEL Gold Image VM).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Gebruik de Red Hat Gold-afbeeldingen uit de Azure CLI

De volgende instructies leiden u door het eerste implementatieproces voor een RHEL VM met behulp van de Azure CLI. Deze instructies gaan ervan uit dat u de [Azure CLI hebt geïnstalleerd.](https://docs.microsoft.com/cli/azure/install-azure-cli)

>[!IMPORTANT]
>Zorg ervoor dat u alle kleine letters in de uitgever, aanbieding, plan en afbeeldingreferenties gebruikt voor alle volgende opdrachten.

1. Controleer of u in uw gewenste abonnement zit.

    ```azurecli
    az account show -o=json
    ```

1. Maak een resourcegroep voor uw Red Hat Gold Image VM.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Accepteer de afbeeldingsvoorwaarden.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Deze voorwaarden moeten eenmaal per *Azure-abonnement worden geaccepteerd, per afbeelding SKU.*

1. (Optioneel) Valideer uw VM-implementatie met de volgende opdracht:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Instel uw VM door dezelfde opdracht uit te `--validate` voeren als in het vorige voorbeeld zonder het argument.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH in uw VM en controleer of u een afbeelding zonder recht hebt. Voer deze stap `sudo yum repolist`uit om deze stap uit te voeren. Voor RHEL 8, gebruik `sudo dnf repolist`. De uitvoer vraagt u om Subscription-Manager te gebruiken om de VM te registreren met Red Hat.

>[!NOTE]
>Op RHEL `dnf` 8, en `yum` zijn uitwisselbaar. Zie voor meer informatie de [RHEL 8-beheergids](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Gebruik de Red Hat Gold Images van PowerShell

Het volgende script is een voorbeeld. Vervang de brongroep, locatie, VM-naam, aanmeldingsgegevens en andere variabelen door de configuratie van uw keuze. Informatie over uitgevers en plannen moet in kleine letters worden verstrekt.

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Versleutel Red Hat Enterprise Linux bring-your-own-subscription Gold Images

Red Hat Enterprise Linux BYOS Gold Images kan worden beveiligd door het gebruik van [Azure Disk Encryption.](../../linux/disk-encryption-overview.md) Het abonnement *moet* worden geregistreerd voordat u versleuteling inschakelen. Zie Hoe u een systeem registreren op [de Red Hat Customer Portal en abonneert op de Red Hat Customer Portal met Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273)voor meer informatie over het registreren van een RHEL BYOS Gold Image. Als je een actief Red Hat-abonnement hebt, kun je ook [de activeringssleutels voor Red Hat Customer Portal](https://access.redhat.com/articles/1378093)maken lezen.

Azure Disk Encryption wordt niet ondersteund op [aangepaste red hat-afbeeldingen.](../../linux/redhat-create-upload-vhd.md) Aanvullende vereisten en vereisten voor Azure Disk Encryption zijn gedocumenteerd in [Azure Disk Encryption voor Linux VM's.](../../linux/disk-encryption-overview.md#additional-vm-requirements)

Zie [Azure Disk Encryption-scenario's voor Linux-VM's](../../linux/disk-encryption-linux.md) en gerelateerde artikelen voor stappen om Azure Disk Encryption toe te passen.

## <a name="additional-information"></a>Aanvullende informatie

- Als u probeert een VM in te richten op een abonnement dat niet is ingeschakeld voor deze aanbieding, krijgt u het volgende bericht:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    Neem in dit geval contact op met Microsoft of Red Hat om uw abonnement in te schakelen.

- Als u een momentopname van een RHEL BYOS-afbeelding wijzigt en probeert die aangepaste afbeelding te publiceren in de [gedeelde afbeeldingsgalerie,](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)moet u plangegevens opgeven die overeenkomt met de oorspronkelijke bron van de momentopname. De opdracht ziet er bijvoorbeeld als volgt uit:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Let op de planparameters in de laatste regel.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) wordt niet ondersteund op aangepaste afbeeldingen.

- Als u automatisering gebruikt om VM's in te richten op de RHEL BYOS-afbeeldingen, moet u planparameters opgeven die vergelijkbaar zijn met wat in de voorbeeldopdrachten is weergegeven. Als u bijvoorbeeld Terraform gebruikt, geeft u de plangegevens op in een [planblok.](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)

## <a name="next-steps"></a>Volgende stappen

- Meer details over Red Hat Cloud Access zijn beschikbaar in de [red hat public cloud documentatie](https://access.redhat.com/public-cloud)
- Zie de [Red Hat Cloud Access-documentatie](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)voor stapsgewijze handleidingen en programmadetails voor Cloud Access.
- Zie [Azure Red Hat Update Infrastructure](./redhat-rhui.md)voor meer informatie over de Red Hat Update Infrastructure .
- Zie de [documentatiepagina](./redhat-images.md)voor meer informatie over alle Red Hat-afbeeldingen in Azure.
- Zie de [levenscycluspagina](https://access.redhat.com/support/policy/updates/errata) van Red Hat Enterprise Linux voor informatie over het ondersteuningsbeleid van Red Hat voor alle versies van RHEL.
- Zie de [Red Hat-documentatie](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)voor aanvullende documentatie over de RHEL Gold Images.
