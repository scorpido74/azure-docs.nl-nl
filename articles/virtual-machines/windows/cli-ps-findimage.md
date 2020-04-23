---
title: Windows VM-afbeeldingen selecteren in Azure
description: Gebruik Azure PowerShell om de uitgever, aanbieding, SKU en versie voor Marketplace VM-afbeeldingen te bepalen.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 46a2badbbe957f6a8a6af7f5a40633ea24cadcd4
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083362"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Windows-VM-installatiekopieën zoeken in de Azure Marketplace met Azure PowerShell

In dit artikel wordt beschreven hoe u Azure PowerShell gebruiken om VM-afbeeldingen in de Azure Marketplace te vinden. U vervolgens een Marketplace-afbeelding opgeven wanneer u een VM programmatisch maakt met PowerShell-sjablonen, Resource Manager-sjablonen of andere hulpprogramma's.

U ook door beschikbare afbeeldingen en aanbiedingen bladeren met de Azure Marketplace-storefront, de [Azure-portal](https://portal.azure.com)of de [Azure CLI.](../linux/cli-ps-findimage.md) [Azure Marketplace](https://azuremarketplace.microsoft.com/) 

 

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabel met veelgebruikte Windows-afbeeldingen

In deze tabel ziet u een subset van beschikbare Skus voor de aangegeven uitgevers en aanbiedingen.

| Uitgever | Aanbieding | Sku |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |Datacenter 2019 |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-Core |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-met-Containers |
| MicrosoftWindowsServer |WindowsServer |Datacenter 2016 |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-met-containers |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftSharePoint |MicrosoftSharePointServer |sp2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Navigeren door de afbeeldingen

Een manier om een afbeelding op een locatie te vinden, is door de cmdlets [Get-AzVMImagePublisher,](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher) [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer)en [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) in volgorde uit te voeren:

1. Geef de uitgevers van installatiekopieën weer.
2. Geef de aanbiedingen voor een bepaalde uitgever weer.
3. Geef de SKU's voor een bepaalde aanbieding weer.

Voer vervolgens voor een geselecteerde SKU [Get-AzVMImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) uit om de te implementeren versies weer te geven.

1. Vermeld de uitgevers:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Vul de door u gekozen uitgevernaam in en vermeld de aanbiedingen:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Vul de door u gekozen aanbiedingsnaam in en vermeld de SKU's:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Vul de door u gekozen SKU-naam in en download de afbeeldingsversie:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
In de uitvoer `Get-AzVMImage` van de opdracht u een versieafbeelding selecteren om een nieuwe virtuele machine te implementeren.

In het volgende voorbeeld ziet u de volledige reeks opdrachten en de uitvoer ervan:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Gedeeltelijke uitvoer:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

Voor de *MicrosoftWindowsServer-uitgever:*

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Uitvoer:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Voor de *WindowsServer-aanbieding:*

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Gedeeltelijke uitvoer:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Dan, voor de *2019-Datacenter* SKU:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Nu u de geselecteerde uitgever, aanbieding, SKU en versie combineren tot een URN (waarden gescheiden door :). Geef deze URN `--image` door met de parameter wanneer u een VM maakt met de [nieuw-AzVM-cmdlet.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) U optioneel het versienummer in de URN vervangen door laatste om de nieuwste versie van de afbeelding te krijgen.

Als u een VM implementeert met een resourcemanagersjabloon, stelt u `imageReference` de afbeeldingsparameters afzonderlijk in de eigenschappen in. Zie de [sjabloonverwijzing](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Eigenschappen van plan weergeven

Voer de cmdlet uit om `Get-AzVMImage` de aankoopplangegevens van een afbeelding weer te geven. Als `PurchasePlan` de eigenschap in `null`de uitvoer niet is, heeft de afbeelding termen die u moet accepteren voordat de programmatische implementatie wordt uitgevoerd.  

De afbeelding *van het Windows Server 2016-datacenter* heeft `PurchasePlan` bijvoorbeeld `null`geen aanvullende voorwaarden, dus de informatie is:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Uitvoer:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

In het onderstaande voorbeeld ziet u een vergelijkbare opdracht voor de afbeelding `PurchasePlan` Data `name`Science `product`Virtual `publisher`Machine *- Windows 2016,* die de volgende eigenschappen heeft: , en . Sommige afbeeldingen hebben `promotion code` ook een eigenschap. Als u deze afbeelding wilt implementeren, raadpleegt u de volgende secties om de voorwaarden te accepteren en programmatische implementatie in te schakelen.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Uitvoer:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>De gebruiksvoorwaarden accepteren

Als u de licentievoorwaarden wilt bekijken, gebruikt u de cmdlet [Get-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms) en geeft u de parameters van het aankoopplan door. De uitvoer biedt een koppeling naar de voorwaarden voor de Marketplace-afbeelding en geeft aan of u de voorwaarden eerder hebt geaccepteerd. Zorg ervoor dat u alle kleine letters in de parameterwaarden gebruikt.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Uitvoer:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Gebruik de cmdlet [Set-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) om de voorwaarden te accepteren of af te wijzen. U hoeft slechts één keer per abonnement voor de afbeelding voorwaarden te accepteren. Zorg ervoor dat u alle kleine letters in de parameterwaarden gebruikt. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Uitvoer:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Implementeren met behulp van parameters van het aankoopplan

Nadat u de voorwaarden voor een afbeelding hebt geaccepteerd, u een virtuele machine in dat abonnement implementeren. Zoals in het volgende fragment wordt weergegeven, gebruikt u de cmdlet [Set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) om de informatie over het Marketplace-abonnement voor het VM-object in te stellen. Zie de voorbeelden van [PowerShell-scripten](powershell-samples.md)voor een volledig script om netwerkinstellingen voor de VM te maken en de implementatie te voltooien.

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "19.01.14"

$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Vervolgens geeft u de VM-configuratie samen met `New-AzVM` netwerkconfiguratieobjecten door aan de cmdlet.

## <a name="next-steps"></a>Volgende stappen

Zie Een virtuele machine `New-AzVM` maken [met PowerShell](quick-create-powershell.md)als u snel een virtuele machine wilt maken met de cmdlet door gebruik te maken van basisafbeeldingsinformatie.


Zie een PowerShell-scriptvoorbeeld om [een volledig geconfigureerde virtuele machine](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)te maken.


