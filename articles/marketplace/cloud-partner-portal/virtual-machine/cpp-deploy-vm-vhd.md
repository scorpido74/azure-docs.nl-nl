---
title: Een VM implementeren van uw VHD's voor de Azure Marketplace
description: Hier wordt uitgelegd hoe u een VM registreert vanaf een VHD die door Azure is geïmplementeerd.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: f13e4066137e0d76612040d9f6e5ff3d0aa399c8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273899"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Een VM implementeren van uw VHD's

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar Partner Center. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Uw technische assets van Azure Virtual Machine maken](https://aka.ms/AzureVMTechAsset) om uw gemigreerde aanbiedingen te beheren.

In dit gedeelte wordt uitgelegd hoe u een virtuele machine (VM) implementeert vanaf een door Azure geïmplementeerde virtuele harde schijf (VHD).  Het bevat de benodigde hulpprogramma's en hoe u deze gebruiken om een vm-afbeelding van de gebruiker te maken en deze vervolgens te implementeren in Azure met PowerShell-scripts.

Nadat u uw virtuele harde schijven (VHD's) hebt geüpload, het algemene besturingssysteem VHD en zero of more datadisk VHD's, u ze registreren als een vm-afbeelding van de gebruiker. Dan kun je dat beeld testen. Omdat uw besturingssysteem VHD gegeneraliseerd is, u de VM niet rechtstreeks implementeren door de VHD-URL op te geven.

Zie de volgende blogberichten voor meer informatie over VM-afbeeldingen:

- [VM-afbeelding](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell 'How To'](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Voorwaarde: installeer de benodigde tools

Als u dit nog niet hebt gedaan, installeert u Azure PowerShell en de Azure CLI met de volgende instructies:

- [Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Implementatiestappen

U gebruikt de volgende stappen om een VM-afbeelding van de gebruiker te maken en te implementeren:

1. Maak de VM-afbeelding van de gebruiker, wat betekent dat de afbeelding wordt vastgelegd en generaliseren. 
2. Maak certificaten en sla ze op in een nieuwe Azure Key Vault. Er is een certificaat vereist voor het tot stand brengen van een veilige WinRM-verbinding met de VM.  Er worden een Azure Resource Manager-sjabloon en een Azure PowerShell-script meegeleverd. 
3. Implementeer de VM vanuit een vm-afbeelding van de gebruiker met behulp van de meegeleverde sjabloon en script.

Nadat uw VM is geïmplementeerd, bent u klaar om [uw VM-afbeelding](./cpp-certify-vm.md)te certificeren.

1. Klik **op Nieuw** en zoek naar **sjabloonimplementatie**en selecteer **Vervolgens Uw eigen sjabloon maken in editor**.  <br/>
   ![VHD-implementatiesjabloon bouwen in Azure-portal](./media/publishvm_021.png)

1. Kopieer en plak deze [JSON-sjabloon](./cpp-deploy-json-template.md) in de editor en klik op **Opslaan**. <br/>
   ![VHD-implementatiesjabloon opslaan in Azure-portal](./media/publishvm_022.png)

1. Geef de parameterwaarden op voor de weergegeven aangepaste eigenschapspagina's. **Custom deployment**

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parameter**              |   **Beschrijving**                                                            |
   |  -------------              |   ---------------                                                            |
   | Gebruikersnaam accountnaam   | Naam van het opslagaccount waar de gegeneraliseerde VHD zich bevindt                    |
   | Naam van gebruikersopslagcontainer | Containernaam waar de gegeneraliseerde VHD zich bevindt                          |
   | DNS-naam voor openbaar IP      | Openbare IP DNS-naam. De DNS-naam is van de VM, u definieert dit in de Azure Portal, zodra de aanbieding is geïmplementeerd.  |
   | Gebruikersnaam beheerder             | Gebruikersnaam van het beheerdersaccount voor nieuwe vm                                  |
   | Beheerderswachtwoord              | Het wachtwoord van het beheerdersaccount voor nieuwe vm                                  |
   | Het type besturingssysteem                     | VM-besturingssysteem: `Windows` \|`Linux`                                    |
   | Abonnements-id             | Id van het geselecteerde abonnement                                      |
   | Locatie                    | Geografische locatie van de implementatie                                        |
   | VM-grootte                     | [Azure VM-grootte](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), bijvoorbeeld`Standard_A2` |
   | Naam van openbaar IP-adres      | Naam van uw openbare IP-adres                                               |
   | VM-naam                     | Naam van de nieuwe VM                                                           |
   | Naam virtueel netwerk        | Naam van het virtuele netwerk dat door de VM wordt gebruikt                                   |
   | NIC-naam                    | Naam van de netwerkinterfacekaart waarop het virtuele netwerk wordt uitgevoerd               |
   | VHD-URL                     | Url van OS Disk VHD voltooien                                                     |
   |  |  |
            
1. Nadat u deze waarden hebt opgedaan, klikt u op **Kopen**. 

Azure begint met de implementatie: het maakt een nieuwe VM met de opgegeven onbeheerde VHD in het opgegeven opslagaccountpad.  U de voortgang in de Azure-portal bijhouden door te klikken op **Virtuele machines** aan de linkerkant van de portal.  Wanneer de vm is gemaakt, verandert `Starting` `Running`de status van . 


### <a name="deploy-a-vm-from-powershell"></a>Een VM implementeren vanuit PowerShell

Als u een grote VM wilt implementeren van de zojuist gemaakte gegeneraliseerde VM-afbeelding, gebruikt u de volgende cmdlets.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Volgende stappen

Vervolgens maakt u [een VM-afbeelding](cpp-create-user-image.md) voor uw gebruiker voor uw oplossing.

