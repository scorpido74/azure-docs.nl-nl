---
title: Een Azure-compatibele VHD maken voor Azure Marketplace
description: Hierin wordt uitgelegd hoe u een VHD maakt voor een aanbieding van een virtuele machine in azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 642c6964aaad8d6e8750fca67efb11eb3feaf19d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147013"
---
# <a name="create-an-azure-compatible-vhd"></a>Een virtuele harde schijf maken die compatibel is met Azure

> [!IMPORTANT]
> Vanaf 13 april 2020 begint het beheer van uw Azure Virtual Machine-aanbiedingen naar het partner centrum. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in de [technische assets van uw virtuele Azure-machines maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) om uw gemigreerde aanbiedingen te beheren.

In dit artikel worden de stappen beschreven die nodig zijn voor het maken van een virtuele harde schijf (VHD) voor een virtuele machine (VM)-aanbieding in de Azure Marketplace.  Het bevat ook aanbevolen procedures voor verschillende aspecten, zoals het gebruik van de Remote Desktop Protocol (RDP), het selecteren van een grootte voor de virtuele machine, het installeren van de meest recente Windows-updates en het generaliseren van de VHD-installatie kopie.  De volgende secties zijn voornamelijk gericht op Vhd's met Windows. Zie [Linux op distributies die zijn goedgekeurd door Azure](../../../virtual-machines/linux/endorsed-distros.md)voor meer informatie over het maken van op Linux gebaseerde virtuele harde schijven. 

> [!WARNING]
> U wordt aangeraden de instructies in dit onderwerp te volgen om Azure te gebruiken voor het maken van een virtuele machine met een vooraf geconfigureerd, goedgekeurd besturings systeem.  Als deze niet compatibel is met uw oplossing, kunt u een on-premises VM maken en configureren met behulp van een goedgekeurd besturings systeem.  U kunt deze vervolgens configureren en voorbereiden voor het uploaden, zoals wordt beschreven in [een Windows-VHD of VHDX voorbereiden om te uploaden naar Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Selecteer een goedgekeurde basis
De VHD van het besturings systeem voor uw VM-installatie kopie moet gebaseerd zijn op een door Azure goedgekeurde basis installatie kopie die Windows Server of SQL Server bevat.
Als u wilt beginnen, maakt u een virtuele machine op basis van een van de volgende installatie kopieën, die zich bevindt op de Microsoft Azure-portal:

-    Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 data center](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Data Center](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-    [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enter prise, Standard, web)
-    [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enter prise, Standard, web)

> [!TIP]
> Als u de huidige Azure Portal of Power shell gebruikt, worden installatie kopieën van Windows Server die zijn gepubliceerd op 8 september 2014 en later goedgekeurd.

Azure biedt ook een aantal goedgekeurde Linux-distributies.  Zie voor een actuele lijst [Linux op distributies die zijn goedgekeurd door Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Een virtuele machine maken in de Azure Portal 

Maak in de micro soft- [Azure Portal](https://ms.portal.azure.com/)de basis installatie kopie met behulp van de volgende stappen.

1. Meld u aan bij de portal met de Microsoft-account voor het Azure-abonnement waarvoor u uw VM-aanbieding wilt publiceren.
2. Maak een nieuwe resource groep en geef de locatie van de **resource groep**, het **abonnement**en de **resource groep**op.  Zie [resource groepen beheren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)voor meer informatie.
3. Klik op **virtuele machines** in de linker menu balk om de pagina Details van virtuele machines weer te geven. 
4. Klik op deze nieuwe pagina op **+ toevoegen** om de Blade **rekender** weer te geven.  Als u het VM-type niet ziet op het eerste scherm, kunt u zoeken naar de naam van de basis-VM, bijvoorbeeld:

    ![Reken blad van nieuwe VM](./media/publishvm_014.png)

5. Nadat u de juiste virtuele installatie kopie hebt geselecteerd, geeft u de volgende waarden op:
   * Voer op de Blade **basis beginselen** een **naam** in voor de virtuele machine, tussen 1-15 alfanumerieke tekens. (In dit voor `DemoVm009`beeld wordt gebruikt.)
   * Voer een **gebruikers naam** en een sterk **wacht woord**in die worden gebruikt voor het maken van een lokaal account op de virtuele machine.  (Dit `adminUser` wordt gebruikt.)  Het wacht woord moet 8-123 tekens lang zijn en aan drie van de vier volgende complexiteits vereisten voldoen: één kleine letter, één hoofd letter, één cijfer en één speciaal teken. Zie [vereisten voor gebruikers naam en wacht woord](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm)voor meer informatie.
   * Selecteer de resource groep die u hebt gemaakt `DemoResourceGroup`(hier).
   * Selecteer een **locatie** van een Azure- `West US`Data Center (hier).
   * Klik op **OK** om deze waarden op te slaan. 

6. Selecteer de grootte van de virtuele machine die u wilt implementeren met behulp van de volgende aanbevelingen:
   * Als u van plan bent om de VHD on-premises te ontwikkelen, is de grootte niet van belang. Overweeg het gebruik van een van de kleinere Vm's.
   * Als u van plan bent de installatie kopie in azure te ontwikkelen, kunt u een van de aanbevolen VM-grootten voor de geselecteerde installatie kopie gebruiken.
   * Voor prijs informatie raadpleegt u de **Aanbevolen prijs categorie** selecteren die wordt weer gegeven op de portal. De drie aanbevolen grootten worden weer gegeven die door de uitgever worden ondersteund. (Dit is de uitgever van micro soft.)

   ![De Blade van de nieuwe VM aanpassen](./media/publishvm_015.png)

7. Stel op de Blade **instellingen** de optie **Managed Disk gebruiken** in op **Nee**.  Hierdoor kunt u de nieuwe VHD hand matig beheren. (Met de Blade **instellingen** kunt u ook andere wijzigingen in de opslag-en netwerk opties wijzigen, bijvoorbeeld het selecteren van **Premium (SSD)** in **schijf type**.)  Klik op **OK** om door te gaan.

    ![De Blade instellingen van de nieuwe VM](./media/publishvm_016.png)

8. Klik op **Samenvatting** om uw keuzes te bekijken. Als u het bericht **Validatie geslaagd** ziet, klikt u op **OK**.

    ![Blade samen vatting van nieuwe VM](./media/publishvm_017.png)

Azure begint met het inrichten van de virtuele machine die u hebt opgegeven.  U kunt de voortgang volgen door op **virtual machines** tabblad aan de linkerkant te klikken.  Nadat deze is gemaakt, wordt de status gewijzigd in **actief**.  Op dat moment kunt u [verbinding maken met de virtuele machine](./cpp-connect-vm.md).


## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt bij het maken van uw nieuwe VHD op basis van Azure, raadpleegt u [algemene problemen tijdens het maken](./cpp-common-vhd-creation-issues.md)van de VHD.  Anders moet u eerst [verbinding maken met de virtuele machines](./cpp-connect-vm.md) die u in azure hebt gemaakt. 
