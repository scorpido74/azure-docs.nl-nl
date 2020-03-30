---
title: Labaccounts beheren in Azure Lab Services | Microsoft Documenten
description: Meer informatie over het maken van een labaccount, het weergeven van alle labaccounts of het verwijderen van een labaccount in een Azure-abonnement.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: c82b5d02ab3928eb0472f2a047cdca2238bf0b63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284289"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Labaccounts beheren in Azure Lab Services 
In Azure Lab Services is een labaccount een container voor beheerde labtypen, zoals klaslabs. Een beheerder stelt een labaccount in met Azure Lab Services en biedt toegang tot labeigenaren die labs in het account kunnen maken. In dit artikel wordt beschreven hoe u een labaccount maakt, alle labaccounts bekijkt of een labaccount verwijdert.

## <a name="create-a-lab-account"></a>Een lab-account maken
In de volgende stappen ziet u hoe u Azure Portal kunt gebruiken om een lab te maken met Azure Lab Services. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Selecteer **Lab-accounts** in de sectie **DevOps.** Selecteer de ster (`*`) naast de **Lab-accounts** om er een toe te voegen aan de sectie **FAVORIETEN** in het menu links. Vanaf de volgende keer selecteert u **Lab-accounts** onder **Favorieten**.

    ![Alle Services -> Lab-accounts](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecteer op de pagina **Lab-accounts** de optie **Toevoegen** op de werkbalk of **Lab-account maken** op de pagina. 

    ![Selecteer de Toevoegen op de pagina Lab-accounts](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Ga op het tabblad **Basisbeginselen** van de pagina **Een labaccount maken** de volgende acties uit: 
    1. Voer een naam in bij **lab-accountnaam**. 
    2. Selecteer het **Azure-abonnement** waarin u het lab-account wilt maken.
    3. Selecteer voor **Resourcegroep** de optie **Nieuwe maken** en voer een naam in voor de resourcegroep.
    4. Selecteer voor **Locatie** een locatie/regio waarin het lab-account moet worden gemaakt.
    5. Voor het veld **Labmaker toestaan om lablocatie te kiezen** geeft u op of u wilt dat labmakers een locatie voor het lab kunnen selecteren. De optie is standaard uitgeschakeld. Wanneer deze is uitgeschakeld, kunnen labmakers geen locatie opgeven voor het lab dat ze maken. De labs worden gemaakt in de dichtstbijzijnde geografische locatie in het labaccount. Wanneer deze is ingeschakeld, kan de labmaker een locatie selecteren wanneer hij een lab maakt. Zie [De maker van het lab toestaan om locatie voor het lab te kiezen voor](allow-lab-creator-pick-lab-location.md)meer informatie. 

        ![Lab-account maken -> Basics](../media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Selecteer **Volgende: Geavanceerd** onder aan de pagina om naar het tabblad **Geavanceerd** te navigeren en voer de volgende stappen uit: 
    1. Selecteer een bestaande **gedeelde afbeeldingsgalerie** of maak er een. U de sjabloon-vm opslaan in de gedeelde afbeeldingsgalerie om deze door anderen opnieuw te kunnen gebruiken. Zie [Een gedeelde afbeeldingsgalerie gebruiken in Azure Lab Services](how-to-use-shared-image-gallery.md)voor gedetailleerde informatie over gedeelde afbeeldingsgalerieën.
    2. Geef op of u **virtuele Windows-machines automatisch** wilt afsluiten wanneer gebruikers de verbinding met deze apparaten verbreken. Geef op hoe lang de virtuele machines moeten wachten tot de gebruiker opnieuw verbinding maakt voordat deze automatisch wordt afgesloten. 
    3. Selecteer voor **virtuele peer-netwerk**een virtueel peer-netwerk (VNet) voor het labnetwerk. Labs die in dit account zijn gemaakt, zijn verbonden met het geselecteerde VNet en hebben toegang tot de bronnen in het geselecteerde VNet. Zie Het [virtuele netwerk van uw lab verbinden met een virtueel netwerk van een peer](how-to-connect-peer-virtual-network.md)voor meer informatie.    
    8. Geef een **adresbereik** op voor VM's in het lab. Het adresbereik moet zich bevinden in de cidr-notatie (classless inter-domain routing) (voorbeeld: 10.20.0.0/23). Virtuele machines in het lab worden gemaakt in dit adresbereik. Zie [Een adresbereik voor VM's in het lab opgeven voor](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab) meer informatie  

        ![Lab-account maken -> Geavanceerd](../media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Selecteer **Volgende: tags** onder aan de pagina om over te schakelen naar het tabblad **Labels.** Voeg tags toe die u aan het labaccount wilt koppelen. Tags zijn naam-/waardeparen waarmee u resources categoriseren en geconsolideerde facturering weergeven door dezelfde tag toe te passen op meerdere bronnen en resourcegroepen. Zie [Tags gebruiken om uw Azure-bronnen te ordenen](../../azure-resource-manager/management/tag-resources.md)voor meer informatie.

    ![Lab-account maken -> Tags](../media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Selecteer **Controleren + maken** onder aan deze pagina om over te schakelen naar het tabblad Controleren + **maken.** 
4. Bekijk de overzichtsinformatie op deze pagina en selecteer **Maken**. 

    ![Lab-account maken -> Tags](../media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Wacht tot de implementatie is voltooid, vouw **Volgende stappen**uit en selecteer Ga **naar resource** zoals weergegeven in de volgende afbeelding: 

    U ook het **belpictogram** op de werkbalk **(Meldingen)** selecteren, bevestigen dat de implementatie is geslaagd en vervolgens **Ga naar resource selecteren.** 

    U kunt ook **Vernieuwen** selecteren op de **Lab-accounts**-pagina en het lab-account selecteren dat u hebt gemaakt. 

    ![Venster Een lab-account maken](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. U ziet de volgende pagina **lab-account**:

    ![Pagina lab-account](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Lab-accounts bekijken
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle bronnen** in het menu. 
3. Selecteer **Lab-accounts** voor het **type**. 
    U ook filteren op abonnement, resourcegroep, locaties en tags. 

    ![Alle bronnen > Lab-accounts](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Labs in het labaccount weergeven en beheren

1. Selecteer op de pagina **Lab-account** **alle labs** in het linkermenu.

    ![Labs in de account](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. U ziet een **lijst met labs** in het account met de volgende informatie: 
    1. Naam van het lab.
    2. De datum waarop het lab is gemaakt. 
    3. E-mailadres van de gebruiker die het lab heeft gemaakt. 
    4. Maximaal aantal gebruikers toegestaan in het lab. 
    5. Status van het lab. 
    6. Roltoewijzingen. 

## <a name="delete-a-lab-in-the-lab-account"></a>Een lab in het labaccount verwijderen
Volg instructies in de vorige sectie om een lijst van de labs in het lab account te zien.

1. Selecteer **... (ellips)** en selecteer **Verwijderen**. 

    ![Een lab verwijderen - knop](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Selecteer **Ja** in het waarschuwingsbericht. 

    ![Verwijdering van het lab bevestigen](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Een labaccount verwijderen
Volg instructies uit de vorige sectie waarin labaccounts in een lijst worden weergegeven. Gebruik de volgende instructies om een labaccount te verwijderen: 

1. Selecteer het **labaccount** dat u wilt verwijderen. 
2. Selecteer **Verwijderen op** de werkbalk. 

    ![Knop Lab-accounts -> verwijderen](../media/how-to-manage-lab-accounts/delete-button.png)
1. Typ **Ja** voor bevestiging.
1. Selecteer **Verwijderen**. 

    ![Lab-account verwijderen - bevestiging](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> U ook de Az.LabServices PowerShell-module (preview) gebruiken om labaccounts te beheren. Zie voor meer informatie de [startpagina van Az.LabServices op GitHub.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Toestaan dat de labmaker de lablocatie kiest](allow-lab-creator-pick-lab-location.md)
- [Verbind het netwerk van uw lab met een virtueel netwerk van collega's](how-to-connect-peer-virtual-network.md)
- [Een gedeelde afbeeldingsgalerie aan een lab koppelen](how-to-attach-detach-shared-image-gallery.md)
- [Een gebruiker toevoegen als eigenaar van een lab](how-to-add-user-lab-owner.md)
- [Firewall-instellingen voor een lab weergeven](how-to-configure-firewall-settings.md)
- [Andere instellingen voor een lab configureren](how-to-configure-lab-accounts.md)