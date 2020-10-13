---
title: Aan de slag en een Azure Lab Services Lab maken op basis van teams
description: Meer informatie over hoe u aan de slag kunt gaan en een Azure Lab Services lab kunt maken op basis van teams.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: b585196fe61a09697cfa203aaa33f08afae2b427
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946663"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Aan de slag en een Lab-service Lab maken op basis van teams

In dit artikel wordt beschreven hoe u een Azure Lab Services-app toevoegt aan teams. Vervolgens kunt u een lab maken op basis van teams.

## <a name="add-a-lab-services-app-to-teams"></a>Een Lab Services-app toevoegen aan teams

U kunt Lab-services rechtstreeks toevoegen in uw teams-kanalen, waarna de app beschikbaar is voor iedereen in het team. Volg deze drie stappen:

1. Navigeer naar het teams kanaal waaraan u de app wilt toevoegen en selecteer **+** een tabblad toevoegen door te klikken op '... ' vanaf de bovenkant van het rechter venster. 
1. Zoek **Azure Lab Services** via de opties op het tabblad en voeg deze app toe. 

    > [!NOTE]
    > Alleen team **eigenaren** kunnen Labs maken voor het team.
1. Selecteer een Lab Services-account, dat u wilt gebruiken voor het maken van klassikale Labs in dit team. 

    Azure Lab Services maakt gebruik van eenmalige aanmelding in de [Azure Lab Services-website](https://labs.azure.com) en haalt alle Lab-accounts op waartoe u toegang hebt. 

    De accounts die zich in dezelfde Tenant bevinden als teams en waarvoor u de toegang **eigenaar**, **bijdrager**of **Maker** hebt, worden weer gegeven. 

   ![Welkom bij als](./media/integrate-with-teams/welcome.png) 
1. Klik op **Opslaan** om de app toe te voegen aan teams en het tabblad wordt toegevoegd aan het kanaal. 

    Nu kunt u het tabblad **Azure Lab Services** selecteren vanuit uw kanaal en de laboratoria beheren zoals beschreven in de volgende stap.

    Zodra een lid van een team het tabblad toevoegt, wordt het weer gegeven voor iedereen in het kanaal. Gebruikers die toegang hebben tot de app, krijgen toegang tot eenmalige aanmelding met de referenties die ze gebruiken voor micro soft teams. Gebruikers die geen toegang hebben tot de app, kunnen het tabblad zien in teams, maar worden geblokkeerd totdat u ze machtigingen geeft voor de on-premises app en de Azure Portal gepubliceerde versie van de app.

## <a name="create-a-classroom-lab"></a>Een leslokaallab maken

Nadat het lab-account is geselecteerd, kunnen team eigenaren Labs maken voor het team. Het hele Lab wordt gemaakt en alle taken op het niveau van de test omgeving kunnen worden uitgevoerd binnen teams. Gebruikers hebben de mogelijkheid om meerdere Labs te maken binnen hetzelfde team en de team eigenaar, met de juiste toegang op het niveau van het account Lab, maar alleen de laboratoria die aan het specifieke team zijn gekoppeld.

## <a name="giving-access-to-users-of-the-lab-account"></a>Toegang verlenen aan gebruikers van het lab-account

Het inrichten van toegang tot gebruikers op het niveau van het lab-account moet plaatsvinden in [Azure](https://ms.portal.azure.com/) Portal.

1. Navigeer in het Azure Portal naar uw Azure Lab Services-account. 
1. Selecteer op de pagina **Labaccount** de optie **Toegangsbeheer (IAM)** , selecteer **+ Toevoegen** op de werkbalk en klik vervolgens op **+ Roltoewijzing toevoegen** in de werkbalk. 

    ![Access Control -> Knop Roltoewijzing toevoegen](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Selecteer op de pagina **Roltoewijzing toevoegen** de optie **Labmaker** als **Rol**. Selecteer de gebruiker die u wilt toevoegen aan de rol Labmaker en selecteer **Opslaan**. 

    ![Labmaker toevoegen](./media/tutorial-setup-lab-account/add-lab-creator.png)

### <a name="creating-classroom-labs"></a>Klassikale Labs maken

Het maken van het leslokaal Labs-proces is hetzelfde, ongeacht of u Labs maakt op basis van teams of de [test Services-website](https://labs.azure.com). 

Voor meer informatie over het instellen van dit artikel overzicht Labs maakt u het volgende: [klassikale Labs beheren in Azure Lab Services](how-to-manage-classroom-labs.md).

## <a name="deleting-classroom-labs"></a>Klassikale Labs verwijderen

Een lab dat is gemaakt in teams kan worden verwijderd op de website van de [test services](https://labs.azure.com) door het lab rechtstreeks te verwijderen, zoals wordt beschreven in [klassikale labs beheren in Azure Lab Services](how-to-manage-classroom-labs.md). 

Het verwijderen van het lab wordt ook geactiveerd wanneer het team wordt verwijderd. Als het team waarin het lab is gemaakt wordt verwijderd, wordt het lab automatisch 24 uur na de synchronisatie van de automatische gebruikers lijst geactiveerd. 

Als u het tabblad verwijdert of de app verwijdert, wordt het lab niet verwijderd. Als het tabblad wordt verwijderd, hebben gebruikers in de lijst team lidmaatschap nog steeds toegang tot de virtuele machines op de [website van de test services](https://labs.azure.com) , tenzij het verwijderen van het lab expliciet wordt geactiveerd door het lab op de website te verwijderen of het team te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

Wanneer een lab in teams wordt gemaakt, wordt de gebruikers lijst in de test omgeving automatisch ingevuld en gesynchroniseerd met het team lidmaatschap. Iedereen in het team, met inbegrip van eigen aren, leden en gasten, wordt automatisch toegevoegd aan de gebruikers lijst van de test omgeving. Azure Lab-Services behouden een synchronisatie met het team lidmaatschap en een automatische synchronisatie wordt elke 24 uur geactiveerd. Zie deze artikelen voor meer informatie:

[Gebruikers lijsten van Lab-Services beheren vanuit teams](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Zie ook

Zie ook de volgende artikelen:

- [Overzicht van het gebruik van Azure Lab Services in teams](lab-services-within-teams-overview.md)
- [Een VM-groep in Lab-Services beheren vanuit teams](how-to-manage-vm-pool-within-teams.md)
- [Planningen voor Lab-Services maken op basis van teams](how-to-create-schedules-within-teams.md)
- [Toegang tot een virtuele machine (weer gave student) in Lab-services van teams](how-to-access-vm-for-students-within-teams.md)

