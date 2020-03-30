---
title: Uw Azure-portalvoorkeuren instellen | Microsoft Documenten
description: U de standaardinstellingen van azure portalen wijzigen om aan uw eigen voorkeuren te voldoen. Instellingen zijn onder andere inactieve sessietime-out, standaardweergave, menumodus, contrast, thema, meldingen en taal- en regionale indelingen
services: azure-portal
keywords: instellingen, time-out, taal, regionale
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310676"
---
# <a name="set-your-azure-portal-preferences"></a>Uw voorkeuren voor de Azure-portal instellen

U de standaardinstellingen van de Azure-portal wijzigen om aan uw eigen voorkeuren te voldoen. Elk van de onderstaande instellingen kan worden gewijzigd:

* [Time-out voor inactieve sessies](#change-the-idle-duration-for-inactive-sign-out)
* [Standaardweergave](#choose-your-default-view)
* [Menumodus portal](#choose-a-portal-menu-mode)
* [Thema Kleur en hoog contrast](#choose-a-theme)
* [Pop-upmeldingen](#enable-or-disable-pop-up-notifications)
* [Indeling voor taal en regio](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Algemene portalinstellingen wijzigen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Instellingen** in de algemene paginakoptekst.

    ![Schermafbeelding van algemene paginakoptekstpictogrammen met gemarkeerde instellingen](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>De niet-actieve duur voor inactieve afmelding wijzigen

De instelling voor een time-out voor inactiviteit helpt resources te beschermen tegen ongeautoriseerde toegang als u vergeet uw werkstation te beveiligen. Nadat u een tijdje niet actief bent geweest, wordt u automatisch afgemeld bij uw Azure-portalsessie.

Selecteer de vervolgkeuzelijst onder **Mij afmelden wanneer u inactief bent**. Kies de duur waarna uw Azure-portalsessie is afgemeld als u niet actief bent.

   ![Schermafbeelding van portalinstellingen met inactieve time-out-instellingen gemarkeerd](./media/set-preferences/inactive-signout-user.png)

De wijziging wordt automatisch opgeslagen. Als u niet actief bent, wordt uw Azure-portalsessie afmelden na de intestellen duur.

Deze instelling kan ook worden gemaakt door een beheerder op directoryniveau om een maximale inactieve tijd af te dwingen. Als een beheerder een time-outinstelling op mapniveau heeft gemaakt, u nog steeds uw eigen inactieve afmeldingsduur instellen. Kies een tijdinstelling die kleiner is dan wat is ingesteld op directoryniveau.

Als uw beheerder een time-outbeleid voor inactiviteit heeft ingeschakeld, schakelt u **het selectievakje Time-outvan de map inactiviteit overschrijven** in. Stel een tijdsinterval in dat kleiner is dan de beleidsinstelling.

   ![Schermafbeelding van portalinstellingen met de instelling voor time-out van mapinactiviteit overschrijven](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Zie Time-out op inactiviteit instellen [voor gebruikers van de Azure-portal](admin-timeout.md) als u een inactieve time-outinstelling wilt afdwingen voor alle gebruikers van de Azure-portal.
>

### <a name="choose-your-default-view"></a>Kies uw standaardweergave 

U de pagina wijzigen die standaard wordt geopend wanneer u zich aanmeldt bij de Azure-portal.

   ![Schermafbeelding van Azure-portalinstellingen met de standaardweergave gemarkeerd](./media/set-preferences/default-view.png)

De standaardweergaveinstelling bepaalt welke Azure-portalweergave wordt weergegeven wanneer u zich aanmeldt. U ervoor kiezen om Azure Home standaard te openen of de dashboardweergave.

* **Thuis** kan niet worden aangepast.  Er worden snelkoppelingen naar populaire Azure-services weergegeven en de bronnen die u het meest recent hebt gebruikt. We bieden u ook nuttige koppelingen naar bronnen zoals Microsoft Learn en de Azure-roadmap.
* Dashboards kunnen worden aangepast om een werkruimte te maken die speciaal voor u is ontworpen. U bijvoorbeeld een dashboard maken dat project-, taak- of rolgericht is. Als u **Dashboard**selecteert, gaat uw standaardweergave naar uw meest recent gebruikte dashboard.

### <a name="choose-a-portal-menu-mode"></a>Kies een menumodus voor portalen

De standaardmodus voor het portalmenu bepaalt hoeveel ruimte het portalmenu op de pagina inneemt.

* Wanneer het portalmenu in de **flyout-modus** staat, wordt het verborgen totdat u het nodig hebt. Selecteer het menupictogram om het menu te openen of te sluiten.
* Als u **de dockmodus** voor het portalmenu kiest, is deze altijd zichtbaar. U het menu samenvouwen om meer werkruimte te bieden. 

### <a name="choose-a-theme"></a>Een thema kiezen

Het thema dat u kiest, is van invloed op de achtergrond- en lettertypekleuren die worden weergegeven in de Azure-portal. U kiezen uit een van de vier vooraf ingestelde kleurthema's. Selecteer elke miniatuur om het thema te vinden dat het beste bij u past.

   ![Schermafbeelding van Azure-portalinstellingen met gemarkeerde thema's](./media/set-preferences/theme.png)

U in plaats daarvan een van de thema's met een hoog contrast kiezen. De instellingen met een hoog contrast maken de Azure-portal beter leesbaar voor gebruikers met een visuele beperking en overschrijven alle andere themaselecties. Zie [Hoog contrast inschakelen of thema wijzigen voor](azure-portal-change-theme-high-contrast.md)meer informatie.

### <a name="enable-or-disable-pop-up-notifications"></a>Pop-upmeldingen in- of uitschakelen

Meldingen zijn systeemberichten die gerelateerd zijn aan uw huidige sessie. Ze bieden informatie zoals uw huidige tegoed, wanneer resources die u zojuist hebt gemaakt beschikbaar komen, of bijvoorbeeld uw laatste actie bevestigen. Wanneer pop-upmeldingen zijn ingeschakeld, worden de berichten kort weergegeven in de bovenhoek van het scherm. 

Als u pop-upmeldingen wilt in- of uitschakelen, schakelt u het **selectievakje Pop-upmeldingen inschakelen in** of uit.

   ![Schermafbeelding van Azure-portalinstellingen met pop-upmeldingen gemarkeerd](./media/set-preferences/popup-notifications.png)

Als u alle meldingen wilt lezen die tijdens uw huidige sessie zijn ontvangen, selecteert u **Meldingen** in de algemene koptekst.

   ![Schermafbeelding van de globale koptekst van azure-portalen met gemarkeerde meldingen](./media/set-preferences/read-notifications.png)

Als u meldingen van eerdere sessies wilt lezen, zoekt u naar gebeurtenissen in het logboek Activiteit. Lees Azure [Activity Log-gebeurtenissen voor](/azure/azure-monitor/platform/activity-log-view)meer informatie.

### <a name="settings-under-useful-links"></a>Instellingen onder nuttige koppelingen

Als u wijzigingen hebt aangebracht in de instellingen van de Azure-portal en deze wilt verwijderen, selecteert u **Standaardinstellingen herstellen**. Wijzigingen die u hebt aangebracht in de portalinstellingen gaan verloren. Deze optie heeft geen invloed op dashboardaanpassingen.

Zie Gebruikersinstellingen exporteren of verwijderen voor meer informatie over **Alle instellingen exporteren** of **Alle instellingen en privédashboards** [verwijderen.](azure-portal-export-delete-settings.md)

## <a name="change-language-and-regional-settings"></a>Taal- en regionale instellingen wijzigen

Er zijn twee instellingen die bepalen hoe de tekst in de Azure-portal wordt weergegeven. Met **de instelling Taal** bepaalt u de taal die u ziet voor tekst in de Azure-portal. **De regionale indeling** bepaalt de manier waarop datums, tijd, getallen en valuta worden weergegeven.

Als u de taal wilt wijzigen die in de Azure-portal wordt gebruikt, gebruikt u de vervolgkeuzelijst om te kiezen uit de lijst met beschikbare talen.

De selectie van de regionale indeling wijzigt om regionale opties weer te geven voor alleen de taal die u hebt geselecteerd. Als u die automatische selectie wilt wijzigen, gebruikt u de vervolgkeuzelijst om de gewenste regionale indeling te kiezen.

Als u bijvoorbeeld Engels als taal selecteert en vervolgens Verenigde Staten selecteert als regionale indeling, wordt valuta weergegeven in Amerikaanse dollars. Als u Engels als taal selecteert en vervolgens Europa als regionale indeling selecteert, wordt valuta weergegeven in euro's.

Selecteer **Toepassen** om uw taal- en instellingen voor regionale indeling bij te werken.

   ![Schermafbeelding van taal- en instellingen voor regionale indeling](./media/set-preferences/language.png)

>[!NOTE]
>Deze taal- en regionale instellingen zijn alleen van invloed op de Azure-portal. Documentatiekoppelingen die in een nieuw tabblad of venster worden geopend, gebruiken de taalinstellingen van uw browser om de taal te bepalen die moet worden weergegeven.
>

## <a name="next-steps"></a>Volgende stappen

* [Aangepaste dashboards maken en delen](azure-portal-dashboards.md)
* [Videoserie met instructies voor de Azure-portal](azure-portal-video-series.md)
