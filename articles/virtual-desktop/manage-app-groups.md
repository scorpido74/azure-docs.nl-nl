---
title: App-groepen voor het Windows Virtual Desktop-portal beheren - Azure
description: Ontdek hoe u app-groepen voor Windows Virtual Desktop kunt beheren met het Azure-portal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5b0269b80cb879923f9f00861494915b6abe103e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283398"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Zelfstudie: App-groepen beheren met het Azure-portal

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/manage-app-groups-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

De standaard app-groep die is aangemaakt voor een nieuwe Windows Virtual Desktop-hostgroep publiceert ook het volledige bureaublad. Daarnaast kunt u een of meer RemoteApp-toepassingsgroepen aanmaken voor de hostgroep. Volg deze zelfstudie om een RemoteApp-app-groep te maken en individuele apps voor het menu Start te publiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een RemoteApp-groep maken.
> * Toegang verlenen tot RemoteApp-programma's.

## <a name="create-a-remoteapp-group"></a>Een RemoteApp-groep maken

Als u al een hostgroep en virtuele machines van de sessiehost heeft gemaakt met het Azure-portal of PowerShell, dan kunt u als volgt toepassingsgroepen toevoegen vanuit het Azure-portal:

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2.  Zoek en selecteer **Windows Virtual Desktop**.

3.  Selecteer **Toepassingsgroepen** in het menu aan de linkerkant van de pagina en selecteer vervolgens **+ Toevoegen**.

4. Selecteer in het tabblad **Basis** de abonnementsgroep en de resourcegroep waarvoor u de app-groep wilt maken. U kunt er ook voor kiezen om een nieuwe resourcegroep te maken in plaats van er een bestaande te selecteren.

5. Selecteer de hostgroep die gekoppeld zal worden aan de toepassingsgroep in de vervolgkeuzelijst naast **Hostgroep**.

    >[!NOTE]
    >U moet de hostgroep selecteren die aan de toepassingsgroep is gekoppeld. App-groepen bevatten apps of bureaubladen die aangeleverd worden vanuit een sessiehost, en sessiehosts maken deel uit van hostgroepen. De app-groep moet gekoppeld zijn met een hostgroep tijdens het aanmaken.

    > [!div class="mx-imgBorder"]
    > ![Een schermopname van het tabblad Basis in het Azure-portal.](media/basics-tab.png)

6. Als u toepassingsgroepen wilt toevoegen aan uw hostgroep, selecteer dan **Hostgroepen** in het menu aan de linkerkant van het scherm.
   
    Selecteer vervolgens de naam van de hostgroep waaraan u toepassingsgroepen wilt toevoegen.
   
    Selecteer vervolgens **Toepassingsgroepen** in het menu aan de linkerkant van het scherm en selecteer vervolgens **+ Toevoegen**.

    Selecteer ten slotte de abonnementsgroep en de resourcegroep waarin u de app-groep wilt maken. Selecteer de naam van een bestaande resourcegroep uit het vervolgkeuzemenu of selecteer **Nieuwe maken** om er een nieuwe te maken.

      >[!NOTE]
      >Wanneer u toepassingsgroepen aan uw hostgroep toevoegt, is de hostgroep die hoort bij de toepassingsgroep al geselecteerd, omdat u van daaruit bent genavigeerd.
      > 
      > [!div class="mx-imgBorder"]
      >![Een schermopname van het tabblad Basis waarin de hostgroep is geselecteerd.](media/host-pool-selected.png)

7. Selecteer **RemoteApp** als type voor de toepassingsgroep en voer vervolgens een naam in voor uw RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![Een schermopname van de velden van het type voor de toepassingsgroep. 'RemoteApp' is gemarkeerd.](media/remoteapp-button.png)

8.  Selecteer het tabblad **Toewijzingen**.

9.  Als u afzonderlijke gebruikers of gebruikersgroepen wilt publiceren naar de app-groep, selecteer dan **+Azure AD-gebruikers of -gebruikersgroepen toevoegen**.

10.  Selecteer het aantal gebruikers waaraan u de apps wilt toevoegen. U kunt één of meerdere gebruikers en gebruikersgroepen selecteren.

     > [!div class="mx-imgBorder"]
     > ![Een schermopname van het menu gebruikersselectie.](media/select-users.png)

11.  Kies **Selecteren**.

12.  Selecteer het tabblad **Toepassingen** en selecteer vervolgens **+Toepassingen toevoegen**.

13.  Een toepassing toevoegen vanuit het menu Start: 

      - Ga naar **Toepassingsbron** en selecteer **menu Start** in het vervolgkeuzemenu. Ga vervolgens naar **Toepassing** en kies de toepassing in het vervolgkeuzemenu.

     > [!div class="mx-imgBorder"]
     > ![Een schermopname van het scherm toepassing toevoegen waarin het menu Start is geselecteerd.](media/add-app-start.png)

      - Voer bij **Weergavenaam** de naam voor de toepassing in die de gebruikers te zien krijgen op hun client.

      - Laat de andere opties ongewijzigd en selecteer **Opslaan**.

14. Een toepassing toevoegen vanaf een specifiek bestandspad:

      - Ga naar **Toepassingsbron** en selecteer **Bestandspad** in het vervolgkeuzemenu.

      - Voer het pad naar de toepassing in bij de sessiehost, geregistreerd bij de gekoppelde hostgroep.

      - Voer de toepassingsgegevens in bij de velden **Toepassingsnaam**, **Weergavenaam**, **Pad naar pictogram** en **Pictogramindex**.

      - Selecteer **Opslaan**.

     > [!div class="mx-imgBorder"]
     > ![Een schermopname van de pagina toepassing toevoegen waarop het bestandspad is geselecteerd.](media/add-app-file.png)

     Herhaal dit proces voor elke toepassing die u aan de toepassingsgroep wilt toevoegen.

15.  Selecteer vervolgens het tabblad **Werkruimte**.

16.  Als u de app-groep wilt registreren voor een werkruimte, ga dan naar **Toepassingsgroep registreren** en selecteer **Ja**. Als u de app-groep liever op een later tijdstip wilt registreren, selecteer dan **Nee**.

17.  Als u **Ja**selecteert, dan kunt u een bestaande werkruimte selecteren om uw app-groep bij te registreren.
       
       >[!NOTE]
       >U kunt de app-groep enkel registreren bij werkruimten die zijn aangemaakt op dezelfde locatie als de hostgroep. Daarnaast: Als u eerder een andere app-groep van dezelfde hostgroep als uw nieuwe app-groep heeft geregistreerd bij een werkruimte, dan wordt deze geselecteerd en kunt u deze niet bewerken. Alle app-groepen uit een hostgroep moeten bij dezelfde werkruimte geregistreerd worden.

     > [!div class="mx-imgBorder"]
     > ![Een schermopname van de pagina toepassingsgroep registreren voor een bestaande werkruimte. De hostgroep is vooraf geselecteerd.](media/register-existing.png)

18. Als u tags wilt maken om uw werkruimte gemakkelijk te organiseren, selecteert u het tabblad **Tags** en voert u de namen voor uw tags in.

19. Als u klaar bent, selecteert u het tabblad **Beoordelen en maken**.

20. Wacht even tot het validatieproces voltooid is. Wanneer dit voltooid is, selecteert u **Maken** om uw app-groep te implementeren.

Het implementatieproces doet het volgende voor u:

- Maak de RemoteApp-app-groep aan.
- Voeg uw geselecteerde apps toe aan de app-groep.
- Publiceer de app-groep naar gebruikers en gebruikersgroepen die u heeft geselecteerd.
- Registreer de app-groep als u dit wilt.
- Maak een koppeling aan naar een Azure Resource Manager-sjabloon op basis van uw configuratie, die u kunt downloaden en opslaan voor later.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een app-groep kunt maken, deze kunt vullen met RemoteApp-programma's en gebruikers kunt toewijzen aan de app-groep. Bekijk de volgende zelfstudie om te leren hoe u een validatiehostgroep kunt maken. U kunt een validatiehostgroep gebruiken om service-updates te controleren voor u ze implementeert naar uw productieomgeving.

> [!div class="nextstepaction"]
> [Een hostpool voor het valideren van service-updates maken](./create-validation-host-pool.md)
