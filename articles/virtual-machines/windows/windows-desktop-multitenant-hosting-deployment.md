---
title: Windows 10 implementeren op Azure met Multitenant-hostingrechten
description: Meer informatie over hoe u uw Windows Software Assurance-voordelen maximaliseren om on-premises licenties naar Azure te brengen
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 9ff8cc64266375a2d439763b222870843136f67a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101491"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Windows 10 implementeren op Azure met Multitenant-hostingrechten 
Voor klanten met Windows 10 Enterprise E3/E5 per gebruiker of Windows Virtual Desktop Access per gebruiker (gebruikersabonnementslicenties of invoegtoepassing gebruikersabonnementslicenties) u met Multitenant Hosting Rechten voor Windows 10 uw Windows 10-licenties naar de cloud brengen en Windows 10 Virtual Machines op Azure uitvoeren zonder te betalen voor een andere licentie. Zie [Multitenant Hosting voor Windows 10 voor](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)meer informatie.

> [!NOTE]
> In dit artikel ziet u de licentievoordelen voor Windows 10 Pro Desktop-afbeeldingen implementeren op Azure Marketplace.
> - Voor Windows 7-, 8.1-, 10 Enterprise-afbeeldingen (x64) op Azure Marketplace voor MSDN-abonnementen verwijzen weu naar [Windows-client in Azure voor dev/testscenario's](client-images.md)
> - Voor licentievoordelen voor Windows Server verwijzen we je naar [Azure Hybrid-gebruiksvoordelen voor Windows Server-afbeeldingen.](hybrid-use-benefit-licensing.md)
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Windows 10-afbeelding implementeren vanuit Azure Marketplace 
Voor powershell-, CLI- en Azure Resource Manager-sjabloonimplementaties is de Windows 10-afbeelding te vinden met de volgende naam van de uitgever, aanbieding, sku.

| OS  |      PublisherName      |  Aanbieding | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Windows 10 VHD uploaden naar Azure
Als u een gegeneraliseerde Windows 10 VHD uploadt, moet u er rekening mee houden dat Windows 10 standaard geen ingebouwd beheerdersaccount heeft ingeschakeld. Als u het ingebouwde beheerdersaccount wilt inschakelen, neemt u de volgende opdracht op als onderdeel van de extensie Aangepast script.

```powershell
Net user <username> /active:yes
```

Het volgende powershell-fragment is om alle beheerdersaccounts als actief te markeren, inclusief de ingebouwde beheerder. Dit voorbeeld is handig als de gebruikersnaam van de ingebouwde beheerder onbekend is.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Voor meer informatie: 
* [VHD uploaden naar Azure](upload-generalized-managed.md)
* [Een Windows VHD voorbereiden om te uploaden naar Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Windows 10 implementeren met multitenant-hostingrechten
Zorg ervoor dat u [de nieuwste Azure PowerShell](/powershell/azure/overview)hebt geïnstalleerd en geconfigureerd. Zodra u uw VHD hebt voorbereid, uploadt u `Add-AzVhd` de VHD naar uw Azure Storage-account met de cmdlet als volgt:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Implementeren met Azure Resource Manager-sjabloonimplementatie** Binnen uw Resource Manager-sjablonen `licenseType` kan een extra parameter worden opgegeven. U meer lezen over [het ontwerpen van Azure Resource Manager-sjablonen.](../../resource-group-authoring-templates.md) Zodra u uw VHD hebt geüpload naar Azure, bewerkt u de sjabloon Resource Manager om het licentietype op te nemen als onderdeel van de compute provider en uw sjabloon als normaal te implementeren:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Implementeren via PowerShell** Wanneer u uw Windows Server VM via PowerShell `-LicenseType`implementeert, hebt u een extra parameter voor . Zodra u uw VHD naar Azure hebt geüpload, maakt u een VM met het `New-AzVM` licentietype als volgt:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Controleer of uw VM gebruikmaakt van het licentievoordeel
Zodra u uw VM hebt geïmplementeerd via de implementatiemethode PowerShell of `Get-AzVM` Resource Manager, controleert u het licentietype als volgt:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

De uitvoer is vergelijkbaar met het volgende voorbeeld voor Windows 10 met het juiste licentietype:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Deze uitvoer staat in contrast met de volgende VM die is geïmplementeerd zonder Azure Hybrid Use Benefit-licentieverlening, zoals een VM die rechtstreeks vanuit de Azure Gallery wordt geïmplementeerd:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Aanvullende informatie over deelname aan Azure AD
>[!NOTE]
>Azure voorziet alle Windows VM's met een ingebouwd beheerdersaccount, dat niet kan worden gebruikt om lid te worden van AAD. *Instellingen > account > Access Work of School >+Connect* werken bijvoorbeeld niet. U moet een tweede beheerdersaccount maken en aanmelden om handmatig lid te worden van Azure AD. U Azure AD ook configureren met een inrichtingspakket, gebruik de koppeling is de sectie *Volgende stappen* voor meer informatie.
>
>

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [het configureren van VDA voor Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Meer informatie over [Multitenant Hosting voor Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


