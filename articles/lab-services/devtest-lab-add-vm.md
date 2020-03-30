---
title: Een VM toevoegen aan een lab in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het toevoegen van een virtuele machine aan een lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 22060cc6dd5eb15e81a0c397a7b0255f16780d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284250"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Een VM toevoegen aan een lab in Azure DevTest Labs
Als u [uw eerste VM](tutorial-create-custom-lab.md#add-a-vm-to-the-lab)al hebt gemaakt, hebt u dit waarschijnlijk gedaan vanuit een vooraf geladen [marketplace-afbeelding.](devtest-lab-configure-marketplace-images.md) Als u nu volgende VM's aan uw lab wilt toevoegen, u ook een *basis* kiezen die een [aangepaste afbeelding](devtest-lab-create-template.md) of een [formule](devtest-lab-manage-formulas.md)is. Met deze zelfstudie u de Azure-portal gebruiken om een VM toe te voegen aan een lab in DevTest Labs.

In dit artikel ziet u ook hoe u de artefacten voor een VM in uw lab beheert.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Stappen om een VM toe te voegen aan een lab in Azure DevTest Labs
1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de sectie **DEVOPS.** Als u * (ster) selecteert naast **DevTest Labs** in de sectie **DEVOPS.** Deze actie voegt **DevTest Labs** toe aan het linker navigatiemenu, zodat u er de volgende keer gemakkelijk toegang toe krijgen. Vervolgens u **DevTest Labs** selecteren in het linkernavigatiemenu.

    ![Alle services - selecteer DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Selecteer in de lijst met labs het lab waarin u de VM wilt maken.
2. Selecteer op de **pagina Overzicht** van het lab de optie + **Toevoegen**.

    ![Knop VM toevoegen](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Selecteer op de **basispagina Kiezen een** marktplaatsafbeelding voor de virtuele machine.
1. Ga op het tabblad **Basisinstellingen** van de pagina **Virtuele machine** de volgende acties uit:
    1. Voer een naam in voor de virtuele machinenaam in het tekstvak **Virtuele machinenaam.** Het tekstvak is vooru vooraf gevuld met een unieke automatisch gegenereerde naam. De naam komt overeen met de gebruikersnaam op uw e-mailadres, gevolgd door een uniek 3-cijferig nummer. Deze functie bespaart u de tijd om aan een machinenaam te denken en deze elke keer dat u een machine maakt te typen. U dit automatisch gevulde veld overschrijven met een naam naar keuze als u dat wilt. Als u de automatisch gevulde naam voor de virtuele machine wilt overschrijven, voert u een naam in het tekstvak Voor de naam van de **virtuele machine** in.
    2. Voer een **gebruikersnaam in** die beheerdersbevoegdheden op de virtuele machine krijgt. De **gebruikersnaam** voor de machine is vooraf gevuld met een unieke automatisch gegenereerde naam. De naam komt overeen met de gebruikersnaam in uw e-mailadres. Deze functie bespaart u de tijd om te beslissen over een gebruikersnaam elke keer dat u een nieuwe machine maakt. Nogmaals, u dit automatisch gevulde veld overschrijven met een gebruikersnaam naar keuze als u dat wilt. Als u de automatisch ingevulde waarde voor gebruikersnaam wilt overschrijven, voert u een waarde in het tekstvak **Gebruikersnaam** in. Deze gebruiker krijgt **beheerdersrechten** op de virtuele machine.
    3. Als u de eerste vm in het lab maakt, voert u een **wachtwoord** in voor de gebruiker. Als u dit wachtwoord wilt opslaan als standaardwachtwoord in de Azure-sleutelkluis die aan het lab is gekoppeld, selecteert **u Opslaan als standaardwachtwoord**. Het standaardwachtwoord wordt opgeslagen in de sleutelkluis met de naam: **VmPassword**. Wanneer u volgende VM's in het lab probeert te maken, wordt **VmPassword** automatisch geselecteerd voor het **wachtwoord.** Als u de waarde wilt overschrijven, schakelt u het selectievakje **Een opgeslagen geheim gebruiken** uit en voert u een wachtwoord in.

        ![Een basis kiezen](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        U ook eerst geheimen opslaan in de sleutelkluis en deze vervolgens gebruiken tijdens het maken van een VM in het lab. Zie [Winkelgeheimen in een sleutelkluis voor](devtest-lab-store-secrets-in-key-vault.md)meer informatie. Als u het wachtwoord wilt gebruiken dat is opgeslagen in de sleutelkluis, selecteert u **Een opgeslagen geheim**gebruiken en geeft u een sleutelwaarde op die overeenkomt met uw geheim (wachtwoord).
    4. Selecteer in de sectie **Meer opties** de optie **Grootte wijzigen**. Selecteer een van de vooraf gedefinieerde items die de processorkernen, ram-grootte en de grootte van de harde schijf van de VM opgeven om te maken.
    5. Selecteer **Artefacten toevoegen of verwijderen**. Selecteer en configureer de artefacten die u aan de basisafbeelding wilt toevoegen.
    **Let op:** Als u nieuw bent bij DevTest Labs of artefacten configureert, raadpleegt u de [sectie Een bestaand artefact toevoegen aan een VM](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) en keert u hier terug wanneer u klaar bent.
2. Ga naar het tabblad **Geavanceerde instellingen** bovenaan en ga de volgende acties uitvoeren:
    1. Als u het virtuele netwerk waarin de virtuele machine zich bevindt wilt wijzigen, selecteert u **VNet wijzigen**.
    2. Als u het subnet wilt wijzigen, selecteert u **Subnet wijzigen**.
    3. Geef op of het IP-adres van de virtuele machine **openbaar, privé of gedeeld**is.
    4. Als u de vm automatisch wilt verwijderen, geeft u de **vervaldatum en -tijd**op .
    5. Als u de VM claimable wilt maken door een labgebruiker, selecteert u **Ja** voor **De optie Claimable van deze machine.**
    6. Geef het aantal **exemplaren van VM** op dat u beschikbaar wilt maken voor uw labgebruikers.

        ![Een basis kiezen](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Selecteer **Maken** om de opgegeven VM aan het lab toe te voegen.

   De labpagina geeft de status weer van de creatie van de VM - eerst als **Maken**, dan als **Uitvoeren** nadat de VM is gestart.

    ![Status van het maken van de virtuele machine](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Een bestaand artefact toevoegen aan een vm
Tijdens het maken van een VM u bestaande artefacten toevoegen. Elk lab bevat artefacten uit de Public DevTest Labs Artefact Repository en artefacten die je hebt gemaakt en toegevoegd aan je eigen Artefact Repository.

* Met Azure DevTest *Labs-artefacten* u *acties* opgeven die worden uitgevoerd wanneer de VM is ingericht, zoals het uitvoeren van Windows PowerShell-scripts, het uitvoeren van Bash-opdrachten en het installeren van software.
* Met *artefactparameters* u het artefact aanpassen voor uw specifieke scenario

Als u wilt ontdekken hoe u artefacten maken, raadpleegt u het [artikel, Leer hoe u uw eigen artefacten maakt voor gebruik met DevTest Labs.](devtest-lab-artifact-author.md)

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met labs het lab met de VM waarmee u wilt werken.
1. Selecteer **Mijn virtuele machines**.
1. Selecteer de gewenste VM.
1. Selecteer **Artefacten beheren**.
1. Selecteer **Artefacten toepassen**.
1. Selecteer in het deelvenster **Artefacten toepassen** het artefact dat u aan de vm wilt toevoegen.
1. Voer in het deelvenster **Artefact toevoegen** de vereiste parameterwaarden en eventuele optionele parameters in die u nodig hebt.
1. Selecteer **Toevoegen** om het artefact toe te voegen en ga terug naar het deelvenster **Artefacten toepassen.**
1. Ga door met het toevoegen van artefacten als dat nodig is voor uw VM.
1. Zodra u uw artefacten hebt toegevoegd, u [de volgorde wijzigen waarin de artefacten worden uitgevoerd.](#change-the-order-in-which-artifacts-are-run) U ook teruggaan om een artefact te [bekijken of te wijzigen.](#view-or-modify-an-artifact)
1. Wanneer u klaar bent met het toevoegen van artefacten, selecteert u **Toepassen**

## <a name="change-the-order-in-which-artifacts-are-run"></a>De volgorde wijzigen waarin artefacten worden uitgevoerd
Standaard worden de acties van de artefacten uitgevoerd in de volgorde waarin ze aan de VM worden toegevoegd.
In de volgende stappen wordt uitgelegd hoe u de volgorde wijzigen waarin de artefacten worden uitgevoerd.

1. Selecteer boven aan het deelvenster **Artefacten toepassen** de koppeling met vermelding van het aantal artefacten dat aan de vm is toegevoegd.

    ![Aantal artefacten dat aan VM is toegevoegd](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Sleep en zet de artefacten in het deelvenster **Geselecteerde artefacten** in de gewenste volgorde. **Let op:** Als u problemen hebt met het slepen van het artefact, moet u ervoor zorgen dat u vanaf de linkerkant van het artefact sleept.
1. Selecteer **OK** wanneer u gereed bent.

## <a name="view-or-modify-an-artifact"></a>Een artefact weergeven of wijzigen
In de volgende stappen wordt uitgelegd hoe u de parameters van een artefact weergeven of wijzigen:

1. Selecteer boven aan het deelvenster **Artefacten toepassen** de koppeling met vermelding van het aantal artefacten dat aan de vm is toegevoegd.

    ![Aantal artefacten dat aan VM is toegevoegd](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Selecteer in het deelvenster **Geselecteerde artefacten** het artefact dat u wilt weergeven of bewerken.
1. Breng **in** het deelvenster Artefact toevoegen de benodigde wijzigingen aan en selecteer **OK** om het **deelvenster Artefact toevoegen** te sluiten.
1. Selecteer **OK** om het deelvenster **Geselecteerde artefacten te** sluiten.

## <a name="save-azure-resource-manager-template"></a>Sjabloon Azure Resource Manager opslaan
Een Azure Resource Manager-sjabloon biedt een declaratieve manier om een herhaalbare implementatie te definiëren.
In de volgende stappen wordt uitgelegd hoe u de sjabloon Azure Resource Manager opslaat voor de vm die wordt gemaakt.
Nadat u bent opgeslagen, u de sjabloon Azure Resource Manager gebruiken om [nieuwe VM's te implementeren met Azure PowerShell.](../azure-resource-manager/templates/overview.md)

1. Selecteer Azure **Resource Manager-sjabloon weergeven**in het deelvenster **Virtuele machine** .
2. Selecteer in het **sjabloonvenster Azure Resource Manager weergeven** de sjabloontekst.
3. Kopieer de geselecteerde tekst naar het klembord.
4. Selecteer **OK** om het **deelvenster Sjabloon voor Azure Resource Manager weergeven te**sluiten .
5. Open een teksteditor.
6. Plak de sjabloontekst van het klembord.
7. Sla het bestand op voor later gebruik.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
* Zodra de VM is gemaakt, u verbinding maken met de VM door **Verbinding maken** te selecteren in het deelvenster van de virtuele machine.
* Meer informatie over het [maken van aangepaste artefacten voor uw VM Voor DevTest Labs.](devtest-lab-artifact-author.md)
* Bekijk [de sjabloongalerie voor Azure Resource Manager QuickStart van DevTest Labs.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
