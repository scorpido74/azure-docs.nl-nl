---
title: Schema maken voor klaslokalen in Azure Lab Services | Microsoft Documenten
description: Meer informatie over het maken van schema's voor klaslabs in Azure Lab Services, zodat VM's in de labs op een bepaald tijdstip worden gestart en afgesloten.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 4887b4359451ca5ce85042b4de42d5376bf4a730
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667766"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Schema's maken en beheren voor klaslabs in Azure Lab Services 
Met schema's u een klaslokaallab zo configureren dat VM's in het lab automatisch op een bepaald tijdstip worden gestart en afgesloten. U een eenmalig schema of een terugkerend schema definiëren. De volgende procedures geven u stappen om schema's voor een klaslokaallab te maken en te beheren: 

> [!IMPORTANT]
> De geplande looptijd van VM's telt niet mee voor het [quotum dat aan een gebruiker is toegewezen.](how-to-configure-student-usage.md#set-quotas-for-users) Het quotum is voor de tijd buiten de planningsuren die een student aan VM's besteedt. 

## <a name="set-a-schedule-for-the-lab"></a>Stel een schema voor het lab
Maak een geplande gebeurtenis voor het lab, zodat VM's in het lab automatisch worden gestart/gestopt op specifieke tijdstippen. Het gebruikersquotum dat u eerder hebt opgegeven, is de extra tijd die buiten deze geplande tijd aan elke gebruiker is toegewezen. 

> [!NOTE]
> Voordat we aan de slag gaan, gaan schema's als een van invloed op virtuele machines in het lab: 
>- Sjabloon virtuele machine is niet opgenomen in schema's. 
>- Alleen toegewezen virtuele machines worden gestart. Dit betekent dat als een machine niet wordt geclaimd door een eindgebruiker (student), de machine niet start op de geplande uren. 
>- Alle virtuele machines (al dan niet geclaimd door een gebruiker) worden gestopt op basis van het labschema. 

1. Ga naar de pagina **Schema's** en selecteer **Geplande gebeurtenis toevoegen** op de werkbalk. 

    ![Knop Planning toevoegen op de pagina Schema's](../media/how-to-create-schedules/add-schedule-button.png)
2. Controleer of **Standaard** is geselecteerd op **het gebeurtenistype**. U selecteert **Start alleen** om alleen de begintijd voor de VM's op te geven. U selecteert **Alleen stoppen** om alleen de stoptijd voor de VM's op te geven. 
7. Selecteer **in** de sectie Herhalen het huidige schema. 

    ![Knop Planning toevoegen op de pagina Schema's](../media/how-to-create-schedules/select-current-schedule.png)
5. Ga in het dialoogvenster **Herhalen** de volgende stappen uit:
    1. Controleer of **elke week** is ingesteld voor het veld **Herhalen.** 
    3. Geef de **begindatum op**.
    4. Geef de **begintijd** op waarop u de VM's wilt starten.
    5. Geef de **stoptijd** op waarop de VM's moeten worden afgesloten. 
    6. Geef de **tijdzone** op voor de opgegeven start- en stoptijden. 
    2. Selecteer de dagen waarop u het schema wilt laten werken. In het volgende voorbeeld wordt maandag-donderdag geselecteerd. 
    8. Selecteer **Opslaan**. 

        ![Herhaalschema instellen](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Voer nu op de pagina **Geplande gebeurtenis toevoegen** voor Notities **(optioneel)** een beschrijving of notities voor het schema in. 
4. Selecteer **Op** de pagina Geplande gebeurtenis toevoegen de optie **Opslaan**. 

    ![Weekschema](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Schema's weergeven in agenda
U de geplande datums en tijden zien die in de agendaweergave zijn gemarkeerd, zoals weergegeven in de volgende afbeelding:

![Schema's in de agendaweergave](../media/how-to-create-schedules/schedules-calendar.png)

Selecteer de knop **Vandaag** in de rechterbovenhoek om over te schakelen naar de huidige datum in de agenda. Selecteer **pijl-links** om over te schakelen naar de vorige week en **pijl-rechts** om over te schakelen naar de volgende week in de agenda. 

## <a name="edit-a-schedule"></a>Een schema bewerken
Wanneer u een gemarkeerd schema in de agenda selecteert, ziet u knoppen om de planning te **bewerken** of **te verwijderen.** 

![Pagina Planning bewerken](../media/how-to-create-schedules/schedule-edit-button.png)

Op de pagina **Geplande gebeurtenis bewerken** u de planning bijwerken en Opslaan **selecteren.** 

## <a name="delete-a-schedule"></a>Een schema verwijderen

1. Als u een planning wilt verwijderen, selecteert u een gemarkeerd schema in de agenda en selecteert u de knop prullenbakpictogram (verwijderen):

    ![Knop Verwijderen op werkbalk](../media/how-to-create-schedules/schedule-delete-button.png)
2. Selecteer in het dialoogvenster **Geplande gebeurtenis verwijderen** de optie **Ja** om de verwijdering te bevestigen. 



## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labaccounts maken en beheren als beheerder](how-to-manage-lab-accounts.md)
- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als labgebruiker toegang tot klaslokalen](how-to-use-classroom-lab.md)
