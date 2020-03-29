---
title: Verplichte artefacten opgeven voor uw Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het opgeven van verplichte artefacten die moeten worden geïnstalleerd voordat u door de gebruiker geselecteerde artefacten op virtuele machines (VM's) in het lab installeert.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 090236ec3647c7c3e38eb862780a615f854e952b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60562140"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Verplichte artefacten voor uw lab opgeven in Azure DevTest Labs
Als eigenaar van een lab u verplichte artefacten opgeven die worden toegepast op elke machine die in het lab is gemaakt. Stelt u zich een scenario voor waarin u wilt dat elke machine in uw lab wordt aangesloten op uw bedrijfsnetwerk. In dit geval moet elke labgebruiker een domeinjoinartefact toevoegen tijdens het maken van virtuele machines om ervoor te zorgen dat zijn of haar machine is verbonden met het bedrijfsdomein. Met andere woorden, lab gebruikers zouden in wezen opnieuw een machine te maken in het geval ze vergeten om verplichte artefacten toe te passen op hun machine. Als eigenaar van een lab, maak je het domein join artefact als een verplicht artefact in uw lab. Deze stap zorgt ervoor dat elke machine is aangesloten op het bedrijfsnetwerk en bespaart de tijd en moeite voor uw labgebruikers.
 
Andere verplichte artefacten kunnen een gemeenschappelijk hulpmiddel bevatten dat uw team gebruikt, of een platformgerelateerd beveiligingspakket dat elke machine standaard moet hebben, enz. Kortom, elke gemeenschappelijke software die elke machine in uw lab moet hebben wordt een verplicht artefact. Als u een aangepaste afbeelding maakt van een machine waarop verplichte artefacten zijn toegepast en vervolgens een nieuwe machine uit die afbeelding maakt, worden de verplichte artefacten opnieuw op de machine toegepast tijdens het maken. Dit gedrag betekent ook dat, hoewel de aangepaste afbeelding oud is, elke keer dat u er een machine van maakt, de meest bijgewerkte versie van verplichte artefacten erop wordt toegepast tijdens de creatiestroom. 
 
Alleen artefacten die geen parameters hebben, worden als verplichte artefacten ondersteund. Uw labgebruiker hoeft geen extra parameters in te voeren tijdens het maken van het lab en maakt het proces van het maken van vm's eenvoudig. 

## <a name="specify-mandatory-artifacts"></a>Verplichte artefacten opgeven
U verplichte artefacten voor Windows- en Linux-machines afzonderlijk selecteren. U deze artefacten ook opnieuw ordenen, afhankelijk van de volgorde waarin u wilt dat ze worden toegepast. 

1. Selecteer op de startpagina van uw lab **Configuratie en beleid** onder **INSTELLINGEN**. 
3. Selecteer **Verplichte artefacten** onder **EXTERNE RESOURCES**. 
4. Selecteer **Bewerken** in de **sectie Windows** of de sectie **Linux.** In dit voorbeeld wordt de optie **Windows** gebruikt. 

    ![Pagina Verplichte artefacten - Knop Bewerken](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Selecteer een artefact. In dit voorbeeld wordt de optie **7-Zip** gebruikt. 
5. Selecteer **op** de pagina Artefact toevoegen de optie **Toevoegen**. 

    ![Verplichte artefacten pagina - Voeg 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Als u een ander artefact wilt toevoegen, selecteert u het artikel en selecteert u **Toevoegen**. In dit voorbeeld wordt **Chrome** toegevoegd als het tweede verplichte artefact.

    ![Pagina Verplichte artefacten - Chrome toevoegen](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Op de pagina **Verplichte artefacten** ziet u een bericht waarin het aantal geselecteerde artefacten wordt opgegeven. Als u op het bericht klikt, ziet u de artefacten die u hebt geselecteerd. Selecteer **Opslaan** om op te slaan. 

    ![Verplichte artefacten pagina - Artefacten opslaan](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Herhaal de stappen om verplichte artefacten voor Linux VM's op te geven. 
    
    ![Verplichte artefacten pagina - Windows en Linux artefacten](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Als u een artefact uit de lijst wilt **verwijderen,** selecteert u **... (ellips)** aan het einde van de rij en selecteer **Verwijderen**. 
10. Als u artefacten in de lijst wilt **ordenen,** zweeft u met de muis over het artefact, selecteert u **... (ellips)** die wordt weergegeven aan het begin van de rij en sleept het item naar de nieuwe positie. 
11. Als u verplichte artefacten in het lab wilt opslaan, selecteert u **Opslaan**. 

    ![Verplichte artefacten pagina - Artefacten opslaan in het lab](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Sluit de pagina **Configuratie en beleid** (selecteer **X** in de rechterbovenhoek) om terug te gaan naar de startpagina voor uw lab.  

## <a name="delete-a-mandatory-artifact"></a>Een verplicht artefact verwijderen
Ga als volgt te werk om een verplicht artefact uit een lab te verwijderen: 

1. Selecteer **Configuratie en beleid** onder **INSTELLINGEN**. 
2. Selecteer **Verplichte artefacten** onder **EXTERNE RESOURCES**. 
3. Selecteer **Bewerken** in de **sectie Windows** of de sectie **Linux.** In dit voorbeeld wordt de optie **Windows** gebruikt. 
4. Selecteer het bericht met het aantal verplichte artefacten bovenaan. 

    ![Pagina Verplichte artefacten - Selecteer het bericht](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Selecteer op de pagina **Geselecteerde artefacten** de optie **... (ellips)** als het artefact moet worden verwijderd en selecteer **Verwijderen**. 
    
    ![Verplichte artefacten pagina - Artefact verwijderen](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Selecteer **OK** om de pagina **Geselecteerde artefacten te** sluiten. 
7. Selecteer **Opslaan** op de pagina **Verplichte artefacten.**
8. Herhaal stappen voor **Linux-afbeeldingen** indien nodig. 
9. Selecteer **Opslaan** om alle wijzigingen in het lab op te slaan. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Verplichte artefacten weergeven bij het maken van een VM
Nu u als labgebruiker de lijst met verplichte artefacten bekijken tijdens het maken van een VM in het lab. U verplichte artefacten die door de eigenaar van het lab in het lab zijn ingesteld, niet bewerken of verwijderen.

1. Selecteer **Overzicht** in het menu op de startpagina van uw lab.
2. Als u een vm aan het lab wilt toevoegen, selecteert u **+ Toevoegen**. 
3. Selecteer een **basisafbeelding**. In dit voorbeeld wordt windows **server gebruikt, versie 1709**.
4. U ziet een bericht voor **artefacten** met het aantal geselecteerde verplichte artefacten. 
5. Selecteer **Artefacten**. 
6. Controleer of u de **verplichte artefacten** ziet die u hebt opgegeven in de configuratie en het beleid van het lab. 

    ![Een VM maken - verplichte artefacten](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [toevoegen van een Git-artefactrepository aan een lab.](devtest-lab-add-artifact-repo.md)

