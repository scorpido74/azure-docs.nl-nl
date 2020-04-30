---
title: Een VM implementeren vanaf uw Vhd's voor Azure Marketplace
description: Hierin wordt uitgelegd hoe u een virtuele machine registreert vanuit een door Azure geïmplementeerde VHD.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: b02fda545ac135735186885d7db597885bf6cc21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147972"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Een VM implementeren vanaf uw Vhd's

> [!IMPORTANT]
> Vanaf 13 april 2020 begint het beheer van uw Azure Virtual Machine-aanbiedingen naar het partner centrum. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in de [technische assets van uw virtuele Azure-machines maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) om uw gemigreerde aanbiedingen te beheren.

In deze sectie wordt uitgelegd hoe u een virtuele machine (VM) implementeert vanuit een door Azure geïmplementeerde virtuele harde schijf (VHD).  De lijst bevat de vereiste hulpprogram ma's en hoe u deze kunt gebruiken om een VM-installatie kopie van een gebruiker te maken en deze vervolgens te implementeren in azure met behulp van Power shell-scripts.

Nadat u de virtuele harde schijven (Vhd's), de gegeneraliseerde VHD van het besturings systeem en nul of meer gegevens schijf-Vhd's hebt geüpload, kunt u ze registreren als een VM-installatie kopie van een gebruiker. Vervolgens kunt u de installatie kopie testen. Omdat de VHD van het besturings systeem generaliseert, kunt u de virtuele machine niet rechtstreeks implementeren door de URL van de VHD op te geven.

Voor meer informatie over VM-installatie kopieën raadpleegt u de volgende blog berichten:

- [VM-installatie kopie](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM-installatie kopie Power shell ' How to '](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Voor waarde: Installeer de benodigde hulpprogram ma's

Als u dit nog niet hebt gedaan, installeert u Azure PowerShell en de Azure CLI met behulp van de volgende instructies:

- [Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Implementatiestappen

U gaat als volgt te werk om een VM-installatie kopie van een gebruiker te maken en te implementeren:

1. Maak de VM-installatie kopie van de gebruiker, waarmee de installatie kopie kan worden vastgelegd en gegeneraliseerd. 
2. Certificaten maken en opslaan in een nieuwe Azure Key Vault. Er is een certificaat vereist voor het tot stand brengen van een beveiligde WinRM-verbinding met de virtuele machine.  Er worden een Azure Resource Manager sjabloon en een Azure PowerShell script gegeven. 
3. Implementeer de virtuele machine op basis van een VM-installatie kopie van een gebruiker met behulp van de meegeleverde sjabloon en het opgegeven script.

Nadat uw virtuele machine is geïmplementeerd, bent u klaar om [uw VM-installatie kopie te certificeren](./cpp-certify-vm.md).

1. Klik op **Nieuw** en zoek naar **sjabloon implementatie**en selecteer vervolgens **uw eigen sjabloon bouwen in de editor**.  <br/>
   ![VHD-implementatie sjabloon maken in Azure Portal](./media/publishvm_021.png)

1. Kopieer en plak deze [JSON-sjabloon](./cpp-deploy-json-template.md) in de editor en klik op **Opslaan**. <br/>
   ![VHD-implementatie sjabloon opslaan in Azure Portal](./media/publishvm_022.png)

1. Geef de parameter waarden op voor de weer gegeven eigenschappen pagina's met **aangepaste implementatie** .

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Bepaalde**              |   **Beschrijving**                                                            |
   |  -------------              |   ---------------                                                            |
   | Account naam voor gebruikers opslag   | De naam van het opslag account waarin de gegeneraliseerde VHD zich bevindt                    |
   | Container naam voor gebruikers opslag | Container naam waar de gegeneraliseerde VHD zich bevindt                          |
   | DNS-naam voor openbaar IP-adres      | De naam van het open bare IP-adres. De DNS-naam is van de virtuele machine. u definieert deze in azure Portal zodra de aanbieding is geïmplementeerd.  |
   | Gebruikers naam voor de beheerder             | Gebruikers naam van beheerders account voor nieuwe VM                                  |
   | Beheerderswachtwoord              | Wacht woord van beheerders account voor nieuwe VM                                  |
   | Het type besturingssysteem                     | VM-besturings systeem `Windows` \| :`Linux`                                    |
   | Abonnements-id             | Id van het geselecteerde abonnement                                      |
   | Locatie                    | Geografische locatie van de implementatie                                        |
   | VM-grootte                     | [Azure VM-grootte](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), bijvoorbeeld`Standard_A2` |
   | Naam van openbaar IP-adres      | Naam van uw open bare IP-adres                                               |
   | VM-naam                     | De naam van de nieuwe virtuele machine                                                           |
   | Virtual Network naam        | De naam van het virtuele netwerk dat wordt gebruikt door de virtuele machine                                   |
   | NIC-naam                    | De naam van de netwerk interface kaart met het virtuele netwerk               |
   | URL VOOR VHD                     | Volledige VHD-URL van de besturingssysteem schijf                                                     |
   |  |  |
            
1. Nadat u deze waarden hebt opgegeven, klikt u op **kopen**. 

Azure begint met de implementatie: er wordt een nieuwe virtuele machine gemaakt met de opgegeven onbeheerde VHD in het opgegeven pad van het opslag account.  U kunt de voortgang van de Azure Portal volgen door op **virtual machines** aan de linkerkant van de portal te klikken.  Wanneer de virtuele machine is gemaakt, wordt de status gewijzigd van `Starting` in `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Een VM implementeren vanuit Power shell

Gebruik de volgende cmdlets om een grote virtuele machine te implementeren vanuit de gegeneraliseerde VM-installatie kopie die u zojuist hebt gemaakt.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Volgende stappen

Vervolgens [maakt u een VM-installatie kopie](cpp-create-user-image.md) van de gebruiker voor uw oplossing.

