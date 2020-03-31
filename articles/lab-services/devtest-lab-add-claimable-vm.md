---
title: Em's maken en beheren in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het toevoegen van een claimbare virtuele machine aan een lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 13d642597fdf5d0eae6c6fd4f0cab16181f033c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270795"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Eisbare VM's maken en beheren in Azure DevTest Labs
U voegt een claimbare VM toe aan een lab op een vergelijkbare manier als hoe u [een standaard-VM toevoegt](devtest-lab-add-vm.md) , van een *basis* die een [aangepaste afbeelding,](devtest-lab-create-template.md) [formule](devtest-lab-manage-formulas.md)of [Marketplace-afbeelding](devtest-lab-configure-marketplace-images.md)is. Deze zelfstudie leidt u door het gebruik van de Azure-portal om een claimbare VM toe te voegen aan een lab in DevTest Labs en toont de processen die een gebruiker volgt om de VM te claimen en te claimen.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Stappen om een claimbare VM toe te voegen aan een lab in Azure DevTest Labs
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
3. Selecteer **Maken** om de opgegeven VM aan het lab toe te voegen.

   De labpagina geeft de status weer van de creatie van de VM - eerst als **Maken**, dan als **Uitvoeren** nadat de VM is gestart.

> [!NOTE]
> Als u labVM's implementeert via [Azure Resource Manager-sjablonen,](devtest-lab-create-environment-from-arm.md)u claimbare VM's maken door de eigenschap **allowClaim** in te stellen op true in de sectie eigenschappen.


## <a name="using-a-claimable-vm"></a>Een claimbare VM gebruiken

Een gebruiker kan elke VM claimen uit de lijst van "Claimable virtual machines" door een van de volgende stappen uit te voeren:

* Klik in de lijst met 'Claimable virtual machines' onder aan het deelvenster 'Overzicht' met de rechtermuisknop op een van de VM's in de lijst en kies **Claimmachine.**

  ![Vraag een specifieke claimbare VM aan.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Kies boven aan het deelvenster Overzicht de optie **Claim een .** Een willekeurige virtuele machine wordt toegewezen uit de lijst van claimbare VM's.

  ![Vraag elke claimbare VM aan.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Nadat een gebruiker een VM claimt, start DevTest Labs de machine en verplaatst deze naar de lijst van de laboratoriumgebruiker van "Mijn virtuele machines". Dit betekent dat de labgebruiker nu eigenaarbevoegdheden op deze machine heeft. De tijd die nodig is voor deze stap kan variëren, afhankelijk van de opstarttijden en alle andere aangepaste acties die tijdens de claimgebeurtenis worden uitgevoerd. Eenmaal geclaimd, is de machine niet meer beschikbaar in de claimable pool.  

## <a name="unclaim-a-vm"></a>Een VM ongedaan maken

Wanneer een gebruiker klaar is met een geclaimde VM en deze beschikbaar wil maken voor iemand anders, kan deze de geclaimde VM terugsturen naar de lijst met claimbare virtuele machines door een van de volgende stappen uit te voeren:

- Klik in de lijst van "Mijn virtuele machines" met de rechtermuisknop op een van de VM's in de lijst – of selecteer de ellips (...) – en kies **Claim en Eis ontzeggen**.

  ![Claim een VM niet op de lijst van VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Selecteer in de lijst 'Mijn virtuele machines' een VM om het beheervenster te openen en selecteer Claim en boven menubalk **opheffen.**

  ![De claim van een vm in het beheervenster van de VM ongedaan maken.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Wanneer een gebruiker een VM niet claimt, hebben ze geen eigenaarmachtigingen meer voor die specifieke lab-vm en kan deze worden geclaimd door een andere labgebruiker in de staat dat deze is opnieuw bevestigd aan de pool. 

### <a name="transferring-the-data-disk"></a>De gegevensschijf overbrengen
Als aan een claimbare VM een gegevensschijf is gekoppeld en een gebruiker deze niet claimt, blijft de gegevensschijf bij de VM. Wanneer een andere gebruiker vervolgens beweert dat VM, die nieuwe gebruiker beweert dat de gegevensschijf en de VM.

Dit staat bekend als "het overbrengen van de gegevensschijf". De gegevensschijf wordt vervolgens beschikbaar in de lijst van mijn **gegevensvan** de nieuwe gebruiker die deze kan beheren.

![Gegevensschijven ongedaan maken.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Volgende stappen
* Zodra deze is gemaakt, u verbinding maken met de virtuele machine door **Verbinding maken** in het beheervenster te selecteren.
* Bekijk [de sjabloongalerie Voor Azure Resource Manager van DevTest Labs.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
