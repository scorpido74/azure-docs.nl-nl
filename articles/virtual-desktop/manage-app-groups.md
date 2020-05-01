---
title: App-groepen voor virtuele Windows-bureau blad-portal beheren-Azure
description: Het beheren van Windows-app-groepen voor virtueel bureau blad met de Azure Portal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f072ed8a758173645c886cabf0b20f9e123cbbab
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612126"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Zelf studie: app-groepen beheren met de Azure Portal

>[!IMPORTANT]
>Deze inhoud is van toepassing op de lente 2020-update met Azure Resource Manager virtueel-bureaublad objecten van Windows. Raadpleeg [dit artikel](./virtual-desktop-fall-2019/manage-app-groups-2019.md)als u de versie van het Windows-bureau blad van Virtual Desktop 2019 zonder Azure Resource Manager objecten gebruikt.
>
> De Windows Virtual Desktop lente 2020-update is momenteel beschikbaar als open bare preview. Deze preview-versie is beschikbaar zonder service level agreement. het wordt niet aangeraden deze te gebruiken voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. 
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

De standaard app-groep die is gemaakt voor een nieuwe Windows Virtual Desktop-hostgroep publiceert ook het volledige bureau blad. Daarnaast kunt u een of meer RemoteApp-toepassings groepen maken voor de hostgroep. Volg deze zelf studie voor het maken van een RemoteApp-app-groep en het publiceren van afzonderlijke apps in het start menu.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een RemoteApp-groep maken.
> * Toegang verlenen aan RemoteApp-program ma's.

## <a name="create-a-remoteapp-group"></a>Een RemoteApp-groep maken

Als u al een hostgroep en host-Vm's hebt gemaakt met behulp van de Azure Portal of Power shell, kunt u toepassings groepen toevoegen vanuit de Azure Portal met het volgende proces:

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2.  Zoek en selecteer **Windows virtueel bureau blad**.

3.  Selecteer **toepassings groepen** in het menu aan de linkerkant van de pagina en selecteer vervolgens **+ toevoegen**.

4. Selecteer op het tabblad **basis beginselen** de abonnements groep en de resource groep waarvoor u de app-groep wilt maken. U kunt er ook voor kiezen om een nieuwe resource groep te maken in plaats van een bestaande te selecteren.

5. Selecteer de hostgroep die wordt gekoppeld aan de toepassings groep in de vervolg keuzelijst naast **hostgroep**.

    >[!NOTE]
    >U moet de hostgroep selecteren die aan de toepassings groep is gekoppeld. App-groepen hebben apps of Bureau bladen die afkomstig zijn van een sessie-host en sessies van hosts deel uitmaken van hostgroepen. De app-groep moet worden gekoppeld aan een hostgroep tijdens het maken.

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van het tabblad basis beginselen in het Azure Portal.](media/basics-tab.png)

6. Als u toepassings groepen wilt toevoegen aan uw hostgroep, selecteert u **hostgroepen** in het menu aan de linkerkant van het scherm.
   
    Selecteer vervolgens de naam van de hostgroep waaraan u toepassings groepen wilt toevoegen.
   
    Daarna selecteert u **toepassings groepen** in het menu aan de linkerkant van het scherm en selecteert u vervolgens **+ toevoegen**.

    Selecteer ten slotte de abonnements groep en de resource groep waarin u de app-groep wilt maken. U kunt de naam van een bestaande resource groep in de vervolg keuzelijst selecteren of **nieuwe maken** selecteren om een nieuwe te maken.

      >[!NOTE]
      >Wanneer u toepassings groepen aan uw hostgroep toevoegt, is de hostgroep die aan de toepassings groep is gekoppeld, al geselecteerd, omdat u deze hebt genavigeerd.
      > 
      > [!div class="mx-imgBorder"]
      >![Een scherm opname van het tabblad basis beginselen waarvoor de hostgroep is geselecteerd.](media/host-pool-selected.png)

7. Selecteer **RemoteApp** onder Type toepassings groep en voer een naam in voor uw RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![Een scherm opname van de velden van het type toepassings groep. ' RemoteApp ' is gemarkeerd.](media/remoteapp-button.png)

8.  Selecteer het tabblad **toewijzingen** .

9.  Als u afzonderlijke gebruikers of gebruikers groepen wilt publiceren naar de app-groep, selecteert u **+ Azure AD-gebruikers of-gebruikers groepen toevoegen**.

10.  Selecteer het aantal gebruikers waaraan u de apps wilt toevoegen. U kunt één of meerdere gebruikers en gebruikers groepen selecteren.

     > [!div class="mx-imgBorder"]
     > ![Een scherm opname van het menu voor gebruikers selectie.](media/select-users.png)

11.  Kies **Selecteren**.

12.  Selecteer het tabblad **toepassingen** en selecteer vervolgens **+ toepassingen toevoegen**.

13.  Een toepassing toevoegen vanuit het menu Start: 

      - Ga naar **toepassings bron** en selecteer **menu Start** in de vervolg keuzelijst. Ga vervolgens naar **toepassing** en kies de toepassing in de vervolg keuzelijst.

     > [!div class="mx-imgBorder"]
     > ![Een scherm opname van het scherm toepassing toevoegen met het menu Start geselecteerd.](media/add-app-start.png)

      - In **weergave naam**voert u de naam in voor de toepassing die wordt weer gegeven aan de gebruiker op de client.

      - Wijzig de andere opties niet, en selecteer **Opslaan**.

14. Een toepassing toevoegen vanuit een specifiek bestandspad:

      - Ga naar **toepassings bron** **en selecteer bestandspad** in de vervolg keuzelijst.

      - Geef het pad op naar de toepassing op de sessiehost, geregistreerd bij de bijbehorende hostgroep.

      - Voer de details van de toepassing in de veld naam van de **toepassing**, de **weergave naam**, het **pad naar het pictogram**en de **pictogram index** in.

      - Selecteer **Opslaan**.

     > [!div class="mx-imgBorder"]
     > ![Een scherm afbeelding van de pagina toepassing toevoegen waarvoor het bestandspad is geselecteerd.](media/add-app-file.png)

     Herhaal dit proces voor elke toepassing die u aan de toepassings groep wilt toevoegen.

15.  Selecteer vervolgens het tabblad **werk ruimte** .

16.  Als u de app-groep wilt registreren voor een werk ruimte, gaat u naar **toepassings groep registreren** en selecteert u **Ja**. Als u de app-groep liever op een later tijdstip wilt registreren, selecteert u **Nee**.

17.  Als u **Ja**selecteert, kunt u een bestaande werk ruimte selecteren om uw app-groep te registreren bij.
       
       >[!NOTE]
       >U kunt de app-groep alleen registreren op werk ruimten die zijn gemaakt op dezelfde locatie als de hostgroep. Ook. Als u eerder een andere app-groep hebt geregistreerd in dezelfde hostgroep als uw nieuwe app-groep voor een werk ruimte, wordt deze geselecteerd en kunt u deze niet bewerken. Alle app-groepen uit een hostgroep moeten worden geregistreerd in dezelfde werk ruimte.

     > [!div class="mx-imgBorder"]
     > ![Een scherm afbeelding van de pagina Toepassings groep registreren voor een bestaande werk ruimte. De hostgroep is voorgeselecteerd.](media/register-existing.png)

18. Als u labels wilt maken om uw werk ruimte gemakkelijk in te delen, selecteert u het tabblad **Tags** en voert u de namen van uw tags in.

19. Wanneer u klaar bent, selecteert u het tabblad **controleren + maken** .

20. Wacht tot het validatie proces is voltooid. Wanneer u klaar bent, selecteert u **maken** om uw app-groep te implementeren.

Het implementatie proces heeft de volgende dingen voor u:

- De RemoteApp-app-groep maken.
- Voeg uw geselecteerde apps toe aan de app-groep.
- Publiceer de app-groep die is gepubliceerd naar gebruikers en gebruikers groepen die u hebt geselecteerd.
- Registreer de app-groep als u dit hebt gedaan.
- Maak een koppeling naar een Azure Resource Manager sjabloon op basis van uw configuratie die u kunt downloaden en opslaan voor later.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een app-groep kunt maken, deze kunt vullen met RemoteApp-program ma's en gebruikers kunt toewijzen aan de app-groep. Zie de volgende zelf studie voor meer informatie over het maken van een groep met validatie-hosts. U kunt een groep validatie-host gebruiken om service-updates te controleren voordat u deze naar uw productie omgeving doorvoert.

> [!div class="nextstepaction"]
> [Een hostpool voor het valideren van service-updates maken](./create-validation-host-pool.md)
