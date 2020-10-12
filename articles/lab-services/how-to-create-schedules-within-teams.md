---
title: Azure Lab Services planningen maken in teams
description: Meer informatie over het maken van planningen voor Lab-Services in teams.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 5578a2f63a4d6f58998a54560299a4356ed65997
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946669"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Schema's voor Lab-Services maken en beheren in teams

Met planningen kunt u een leslokaal Lab zodanig configureren dat de virtuele machines in het lab automatisch worden gestart en op een opgegeven tijdstip worden afgesloten. U kunt een eenmalig schema of een terugkerende planning definiëren. De volgende procedures geven u stappen voor het maken en beheren van planningen voor een klas Lab: 

Planningen zijn van invloed op virtuele machines van Lab: 

- De virtuele machine van de sjabloon is niet opgenomen in schema's. 
- Alleen toegewezen virtuele machines worden gestart. Dit betekent dat als een computer niet door een eind gebruiker (student) wordt geclaimd, de computer niet kan beginnen met de geplande uren. 
- Alle virtuele machines (of dit wordt geclaimd door een gebruiker of niet), worden gestopt op basis van het lab-schema. 

> [!IMPORTANT]
> De geplande uitvoerings tijd van Vm's telt niet op het quotum dat aan een gebruiker is toegewezen. Het quotum geldt voor de tijd buiten de plannings uren die een student op Vm's doorbrengt. 

## <a name="createeditdelete-a-schedule-for-the-lab"></a>Een planning voor het lab maken/bewerken/verwijderen

Maak een geplande gebeurtenis voor het lab, zodat VM's in het lab op opgegeven momenten automatisch worden gestart/gestopt. Het gebruikers quotum dat u eerder hebt opgegeven, is de extra tijd die wordt toegewezen aan elke gebruiker buiten dit geplande tijdstip. 

1. Ga naar de pagina **schema's** en selecteer een **geplande gebeurtenis toevoegen** op de werk balk (boven aan het venster). 
1. Stel de volgende para meters in voor het schema:
    1. Controleer of **Standaard** is geselecteerd voor het **Gebeurtenistype**. U selecteert **alleen starten** om alleen de begin tijd voor de virtuele machines op te geven. U selecteert **alleen stoppen** om alleen de eind tijd voor de virtuele machines op te geven. 
    1. De **begin datum**opgeven.
    1. Geef de **begin tijd** op waarop u de vm's wilt starten.
    1. Geef de **eind tijd** op waarop de vm's moeten worden afgesloten. 
    1. Geef de **tijd zone** op voor de start-en stop tijden die u hebt opgegeven. 
    1. Selecteer in de sectie **herhalen** **elke week** of **nooit**. 
    1. Voer voor **opmerkingen (optioneel)** een beschrijving of notities in voor de planning. 
1. Klik op **Opslaan**. 

### <a name="view-schedules-in-calendar"></a>Schema's in de agenda weer geven

U kunt de geplande datums en tijden zien die zijn gemarkeerd in de agenda. Selecteer de knop **vandaag** in de rechter bovenhoek om over te scha kelen naar de huidige datum in de agenda. Selecteer **pijl-links** om over te scha kelen naar de vorige week en de **pijl-rechts** om over te scha kelen naar de volgende week in de agenda. 

### <a name="edit-a-schedule"></a>Een planning bewerken

Wanneer u een gemarkeerde planning in de agenda selecteert, ziet u knoppen om de planning te **bewerken** of te **verwijderen** . 

Op de pagina **geplande gebeurtenis bewerken** kunt u het schema bijwerken en vervolgens **Opslaan**selecteren. 

### <a name="delete-a-schedule"></a>Een schema verwijderen

1. Als u een planning wilt verwijderen, selecteert u een gemarkeerde planning in de agenda en selecteert u de knop met het prullenbak pictogram (verwijderen):
1. Selecteer **Ja** in het dialoog venster **geplande gebeurtenis verwijderen** om de verwijdering te bevestigen. 

## <a name="automatic-shutdown-and-disconnect-settings"></a>Instellingen voor automatisch afsluiten en verbreken

Boven aan de pagina ziet u een koppeling naar de instellingen voor automatisch **Afsluiten** .

U kunt verschillende functies voor het automatisch afsluiten van kosten beheer inschakelen om extra kosten te voor komen wanneer de virtuele machines niet actief worden gebruikt. De combi natie van de volgende drie functies voor automatisch afsluiten en verbreken van de meeste gevallen, waarbij gebruikers per ongeluk hun virtuele machines verlaten:
 
- Automatisch de verbinding van gebruikers met virtuele machines die het besturings systeem inactief bewaart.
- Virtuele machines automatisch uitschakelen wanneer gebruikers de verbinding verbreken.
- Virtuele machines die zijn gestart automatisch afsluiten, maar gebruikers maken geen verbinding.

Klik op het *informatie* pictogram naast opties in de instellingen voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen:

- [Overzicht van het gebruik van Azure Lab Services in teams](lab-services-within-teams-overview.md)
- [Aan de slag en een Lab-service Lab maken op basis van teams](how-to-get-started-create-lab-within-teams.md)
- [Gebruikers lijsten van Lab-Services beheren vanuit teams](how-to-manage-user-lists-within-teams.md)
- [Een VM-groep in Lab-Services beheren vanuit teams](how-to-manage-vm-pool-within-teams.md)
- [Toegang tot een virtuele machine (weer gave student) in Lab-services van teams](how-to-access-vm-for-students-within-teams.md)