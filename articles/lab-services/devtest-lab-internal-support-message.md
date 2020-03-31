---
title: Een interne ondersteuningsinstructie toevoegen aan een lab in Azure DevTest Labs
description: Meer informatie over het plaatsen van een interne ondersteuningsverklaring naar een lab in Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 591dcec36dc62143901d3b49db24196e84d58c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170355"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Een interne ondersteuningsinstructie toevoegen aan een lab in Azure DevTest Labs

Met Azure DevTest Labs u uw lab aanpassen met een interne ondersteuningsverklaring die gebruikers ondersteuningsinformatie over het lab biedt. U bijvoorbeeld contactgegevens verstrekken, zodat een gebruiker weet hoe hij interne ondersteuning kan bereiken wanneer hij hulp nodig heeft bij het oplossen van problemen of het openen van bronnen in het lab. U ook links naar interne websites of veelgestelde vragen bieden waartoe uw team toegang heeft voordat u contact opneemt met de ondersteuning.

Een interne ondersteuningsverklaring is bedoeld om u labinformatie te laten plaatsen die doorgaans niet te vaak verandert. Als u gebruikers op de hoogte wilt stellen van labinformatie die meer van aard is - zoals recente updates van het labbeleid - [raadpleegt u Berichtaankondiging in een lab](devtest-lab-announcements.md).

U een ondersteuningsinstructie eenvoudig uitschakelen of bewerken nadat deze niet meer van toepassing is.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Stappen om een ondersteuningsinstructie toe te voegen aan een bestaand lab

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer indien nodig **Alle services**en selecteer **Vervolgens DevTest Labs** in de lijst. (Uw lab wordt mogelijk al weergegeven op het dashboard onder **Alle bronnen**).
1. Selecteer in de lijst met labs het lab waarin u een ondersteuningsverklaring wilt toevoegen.  
1. Selecteer **Configuratie en beleid**in het gebied **Overzicht** van het lab.  

    ![Knop Configuratie en beleid](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Selecteer Rechts onder **INSTELLINGEN**de optie **Interne ondersteuning**.

    ![Knop Interne ondersteuning](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Als u een intern ondersteuningsbericht wilt maken voor de gebruikers in dit lab, stelt u Ingeschakeld in **op Ja.**

1. Voer in het veld **Ondersteuningsbericht** de interne ondersteuningsverklaring in die u aan uw labgebruikers wilt presenteren. Het ondersteuningsbericht accepteert Markdown. Wanneer u de berichttekst invoert, u het **gebied Voorbeeld** onder aan het scherm bekijken om te zien hoe het bericht wordt weergegeven aan gebruikers.

    ![Intern ondersteuningsscherm om het bericht te maken.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Selecteer **Opslaan** zodra uw ondersteuningsinstructie klaar is om te posten.

Als u dit ondersteuningsbericht niet meer wilt weergeven aan labgebruikers, gaat u terug naar de **pagina Interne ondersteuning** en stelt u **Ingeschakeld** in op **Nee.**

## <a name="steps-for-users-to-view-the-support-message"></a>Stappen voor gebruikers om het ondersteuningsbericht te bekijken

1. Selecteer in de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)een lab.

1. Selecteer **interne ondersteuning**in het **gebied Overzicht** van het lab.  

    ![Knop Interne ondersteuning](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Als er een ondersteuningsbericht wordt geplaatst, kan de gebruiker het bekijken vanuit het interne ondersteuningsvenster.

    ![Intern ondersteuningsvenster met geplaatst ondersteuningsbericht](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
* Interne ondersteuningsinstructies worden meestal gebruikt om ondersteuningsinformatie te bieden die niet zo vaak verandert. U ook leren hoe u [een aankondiging plaatsen in een lab](devtest-lab-announcements.md) om gebruikers te informeren over tijdelijke wijzigingen of updates van het lab.
* [Stel beleidsregels en schema's](devtest-lab-set-lab-policy.md) in met informatie over hoe u andere beperkingen en conventies in uw abonnement toepassen met behulp van aangepast beleid.