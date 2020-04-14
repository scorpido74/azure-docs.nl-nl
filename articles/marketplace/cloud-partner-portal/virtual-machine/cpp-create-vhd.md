---
title: Een VHD met Azure-compatibele vhd maken voor azure marketplace
description: Hier wordt uitgelegd hoe u een VHD maakt voor een virtuele machineaanbieding in de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 99d2bc95c1dd837bfc3bcabcead28777b7e6f746
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273933"
---
# <a name="create-an-azure-compatible-vhd"></a>Een VHD met Azure-compatibel maken

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar Partner Center. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Uw technische assets van Azure Virtual Machine maken](https://aka.ms/AzureVMTechAsset) om uw gemigreerde aanbiedingen te beheren.

In dit artikel worden de stappen beschreven die nodig zijn om een virtuele harde schijf (VHD) te maken voor een virtuele machine (VM) in de Azure Marketplace.  Het bevat ook aanbevolen procedures voor verschillende aspecten, zoals het gebruik van het Remote Desktop Protocol (RDP), het selecteren van een grootte voor de VM, het installeren van de nieuwste Windows-updates en het generaliseren van de VHD-afbeelding.  De volgende secties richten zich voornamelijk op windows-gebaseerde VHD's; Zie Linux voor meer informatie over het maken van Op Linux gebaseerde VHD's [op distributies die zijn goedgekeurd door Azure.](../../../virtual-machines/linux/endorsed-distros.md) 

> [!WARNING]
> Het wordt ten zeerste aanbevolen dat u de richtlijnen in dit onderwerp volgt om Azure te gebruiken om een VM te maken met een vooraf geconfigureerd, goedgekeurd besturingssysteem.  Als dit niet compatibel is met uw oplossing, is het mogelijk om een on-premises VM te maken en te configureren met behulp van een goedgekeurd besturingssysteem.  U deze vervolgens configureren en voorbereiden op uploaden zoals beschreven in [Een Windows VHD of VHDX voorbereiden om te uploaden naar Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)


## <a name="select-an-approved-base"></a>Een goedgekeurde basis selecteren
Het besturingssysteem VHD voor uw VM-afbeelding moet gebaseerd zijn op een door Azure goedgekeurde basisafbeelding die Windows Server of SQL Server bevat.
Maak om te beginnen een VM op basis van een van de volgende afbeeldingen die zich op de Microsoft Azure-portal bevinden:

-    Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [Datacenter 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-    [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-    [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Als u de huidige Azure-portal of PowerShell gebruikt, worden Windows Server-afbeeldingen die op 8 september 2014 en hoger zijn gepubliceerd, goedgekeurd.

Azure biedt afwisselend een reeks goedgekeurde Linux-distributies.  Zie Linux voor een huidige lijst [op distributies die zijn goedgekeurd door Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)


## <a name="create-vm-in-the-azure-portal"></a>VM maken in de Azure-portal 

Maak in de Microsoft [Azure-portal](https://ms.portal.azure.com/)de basisafbeelding met de volgende stappen.

1. Meld u aan bij de portal met het Microsoft-account voor het Azure-abonnement dat u wilt publiceren.
2. Maak een nieuwe resourcegroep en geef de naam van uw **resourcegroep,** **abonnement**en **locatie van de resourcegroep op**.  Zie [Resourcegroepen beheren voor](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)meer richtlijnen .
3. Klik op **Virtuele machines** in de linkermenubalk om de pagina Virtuele machines details weer te geven. 
4. Klik op deze nieuwe pagina op **+Toevoegen** om het **Compute-blad** weer te geven.  Als u het VM-type niet op het eerste scherm ziet, u zoeken naar de naam van uw basis-VM, bijvoorbeeld:

    ![Compute blade van nieuwe VM](./media/publishvm_014.png)

5. Nadat u de juiste virtuele afbeelding hebt geselecteerd, geeft u de volgende waarden op:
   * Voer op het **grondblad Basics** een **naam** voor de virtuele machine in tussen 1-15 alfanumerieke tekens. (In dit `DemoVm009`voorbeeld wordt gebruik gebruikt .)
   * Voer een **gebruikersnaam** en een sterk **wachtwoord in,** die worden gebruikt om een lokaal account op de VM te maken.  (Hier `adminUser` wordt gebruikt.)  Het wachtwoord moet 8-123 tekens lang zijn en voldoen aan drie van de vier volgende complexiteitsvereisten: een kleine lettersteken, één hoofdletterteken, één getal en één speciaal teken. Zie [Gebruikersnaam- en wachtwoordvereisten voor](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm)meer informatie.
   * Selecteer de resourcegroep die `DemoResourceGroup`u hebt gemaakt (hier ).
   * Selecteer een Azure-datacenterlocatie (hier). **Location** `West US`
   * Klik op **OK** om deze waarden op te slaan. 

6. Selecteer de grootte van de VM die u wilt implementeren met behulp van de volgende aanbevelingen:
   * Als u van plan bent om de VHD on-premises te ontwikkelen, maakt de grootte niet uit. Overweeg het gebruik van een van de kleinere VM's.
   * Als u van plan bent de afbeelding in Azure te ontwikkelen, u overwegen een van de aanbevolen VM-formaten voor de geselecteerde afbeelding te gebruiken.
   * Raadpleeg voor prijsinformatie de selectie van **aanbevolen prijsniveaus** die op de portal worden weergegeven. Het toont de drie aanbevolen maten die door de uitgever. (Hier is de uitgever Microsoft.)

   ![Grootte blad van nieuwe VM](./media/publishvm_015.png)

7. Stel in het blad **Instellingen** de optie **Beheerde schijf gebruiken** in op **Nee**.  Hierdoor u de nieuwe VHD handmatig beheren. (Met het **pagina-blad Instellingen** u ook andere opslag- en netwerkopties wijzigen, bijvoorbeeld door **Premium (SSD)** te selecteren in **schijftype**.)  Klik op **OK** om door te gaan.

    ![Instellingen blad van nieuwe VM](./media/publishvm_016.png)

8. Klik op **Samenvatting** om uw keuzes te bekijken. Als u het bericht **Validatie geslaagd** ziet, klikt u op **OK**.

    ![Samenvatting mes van nieuwe VM](./media/publishvm_017.png)

Azure begint met het inrichten van de virtuele machine die u hebt opgegeven.  U de voortgang ervan bijhouden door links op het tabblad **Virtuele machines** te klikken.  Nadat deze is gemaakt, wordt de status gewijzigd in **Actief uitvoeren**.  Op dat moment u [verbinding maken met de virtuele machine.](./cpp-connect-vm.md)


## <a name="next-steps"></a>Volgende stappen

Als u problemen hebt ondervonden bij het maken van uw nieuwe VHD op Azure-gebaseerde, raadpleegt u [Veelvoorkomende problemen tijdens het maken van VHD.](./cpp-common-vhd-creation-issues.md)  Anders moet u vervolgens [verbinding maken met de VM's die](./cpp-connect-vm.md) u op Azure hebt gemaakt. 
