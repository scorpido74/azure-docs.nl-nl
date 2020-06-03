---
title: Uw Azure Portal voor keuren instellen | Microsoft Docs
description: U kunt Azure Portal standaard instellingen wijzigen om te voldoen aan uw eigen voor keuren. De instellingen omvatten een inactieve sessietime-out, de standaard weergave, de menu modus, het contrast, het thema, de meldingen en de taal-en regionale notaties
services: azure-portal
keywords: instellingen, time-out, taal, regionale
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: a3f51c356f4476782bb830b2702a8fe87c79235c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84308061"
---
# <a name="set-your-azure-portal-preferences"></a>Uw voorkeuren voor de Azure-portal instellen

U kunt de standaard instellingen van de Azure Portal wijzigen om te voldoen aan uw eigen voor keuren. Elk van de onderstaande instellingen kan worden gewijzigd:

* [Time-out voor inactieve sessie](#change-the-idle-duration-for-inactive-sign-out)
* [Standaard weergave](#choose-your-default-view)
* [Menu modus van de portal](#choose-a-portal-menu-mode)
* [Thema kleur en hoog contrast](#choose-a-theme)
* [Pop-upmeldingen](#enable-or-disable-pop-up-notifications)
* [Indeling voor taal en regio](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Algemene Portal instellingen wijzigen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **instellingen** in de koptekst van de globale pagina.

    ![Scherm opname van globale paginakop tekst pictogrammen met gemarkeerde instellingen](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>De duur van niet-actieve afmelding wijzigen

Met de instelling time-out voor inactiviteit kunt u resources beveiligen tegen onbevoegde toegang als u uw werk station wilt beveiligen. Nadat u een tijdje niet langer inactief bent, wordt u automatisch afgemeld bij uw Azure Portal-sessie.

Selecteer de vervolg keuzelijst onder **aanmelden wanneer**deze niet actief is. Kies de duur waarna uw Azure Portal-sessie wordt afgemeld als u niet actief bent.

   ![Scherm opname van Portal instellingen met inactieve time-outinstellingen gemarkeerd](./media/set-preferences/inactive-signout-user.png)

De wijziging wordt automatisch opgeslagen. Als u inactief bent, wordt uw Azure Portal-sessie afgemeld na de periode die u hebt ingesteld.

Deze instelling kan ook worden gemaakt door een beheerder op het niveau van de Directory om een maximale niet-actieve tijd af te dwingen. Als een beheerder een time-outinstelling op mapniveau heeft gemaakt, kunt u nog steeds uw eigen inactieve aanmeldings duur instellen. Kies een tijd instelling die kleiner is dan wat is ingesteld op het niveau van de Directory.

Als uw beheerder een beleid voor time-out bij inactiviteit heeft ingeschakeld, schakelt u het selectie vakje **de Directory-time-out voor inactiviteit negeren** in. Stel een tijds interval in dat kleiner is dan de beleids instelling.

   ![Scherm opname van Portal instellingen met onderdrukking van de beleids instelling voor de time-out voor inactiviteit gemarkeerd](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Als u een beheerder bent en u wilt een inactieve time-outinstelling afdwingen voor alle gebruikers van de Azure Portal, Zie [de time-out voor inactiviteit op mapniveau instellen voor gebruikers van de Azure Portal](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Kies uw standaard weergave 

U kunt de pagina die standaard wordt geopend, wijzigen wanneer u zich aanmeldt bij de Azure Portal.

   ![Scherm opname met Azure Portal instellingen met standaard weergave gemarkeerd](./media/set-preferences/default-view.png)

De standaard weergave-instelling bepaalt welke Azure Portal weer gave wordt weer gegeven wanneer u zich aanmeldt. U kunt ervoor kiezen om Azure Home standaard te openen of de dashboard weergave.

* **Start pagina** kan niet worden aangepast.  Het bevat snelkoppelingen naar populaire Azure-Services en geeft een lijst van de resources die u het laatst hebt gebruikt. We bieden u ook nuttige koppelingen naar resources zoals Microsoft Learn en het Azure-schema.
* Dash boards kunnen worden aangepast om een werk ruimte te maken die alleen voor u is ontworpen. U kunt bijvoorbeeld een dash board maken dat is gericht op project, taak of rol. Als u **dash board**selecteert, gaat uw standaard weergave naar het meest recent gebruikte dash board.

### <a name="choose-a-portal-menu-mode"></a>Kies een portal menu modus

De standaard modus voor het menu Portal bepaalt hoeveel ruimte het menu van de portal op de pagina inneemt.

* Wanneer het menu van de portal in de **flyout** wordt weer gegeven, wordt het verborgen tot u het nodig hebt. Selecteer het menu pictogram om het menu te openen of te sluiten.
* Als u de modus **gedokt** kiest voor het menu Portal, is deze altijd zichtbaar. U kunt het menu samen vouwen om meer werk ruimte beschikbaar te maken. 

### <a name="choose-a-theme"></a>Een thema kiezen

Het thema dat u kiest, is van invloed op de achtergrond-en lettertype kleuren die in de Azure Portal worden weer gegeven. U kunt kiezen uit een van de vier vooraf ingestelde kleuren Thema's. Selecteer elke miniatuur om het thema te vinden dat het beste bij u past.

   ![Scherm opname van Azure Portal instellingen met Thema's gemarkeerd](./media/set-preferences/theme.png)

U kunt in plaats daarvan een van de Thema's met een hoog contrast kiezen. Met de instellingen voor hoog contrast kan de Azure Portal gemakkelijker te lezen zijn voor mensen die een visuele uitzondelijke waardevermindering hebben en alle andere thema selecties overschrijven. Zie voor meer informatie [inschakelen hoog contrast of thema wijzigen](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Pop-upmeldingen in-of uitschakelen

Meldingen zijn systeem berichten die betrekking hebben op uw huidige sessie. Ze bieden informatie zoals het huidige tegoed, wanneer de resources die u zojuist hebt gemaakt beschikbaar zijn of uw laatste actie bevestigen, bijvoorbeeld. Wanneer pop-upmeldingen zijn ingeschakeld, worden de berichten kort weer gegeven in de bovenste hoek van het scherm. 

Als u pop-upmeldingen wilt in-of uitschakelen, schakelt u het selectie vakje **pop-upmeldingen inschakelen** in of uit.

   ![Scherm opname van Azure Portal instellingen met pop-upmeldingen gemarkeerd](./media/set-preferences/popup-notifications.png)

Als u alle meldingen wilt lezen die tijdens de huidige sessie zijn ontvangen, selecteert u **meldingen** in de globale koptekst.

   ![Scherm opname van Azure Portal globale koptekst met gemarkeerde meldingen](./media/set-preferences/read-notifications.png)

Als u meldingen van eerdere sessies wilt lezen, zoekt u naar gebeurtenissen in het activiteiten logboek. Meer informatie vindt u in [Azure activiteiten logboek gebeurtenissen weer geven en ophalen](/azure/azure-monitor/platform/activity-log-view).

### <a name="settings-under-useful-links"></a>Instellingen onder handige koppelingen

Als u wijzigingen hebt aangebracht in de Azure Portal instellingen en deze wilt negeren, selecteert u **standaard instellingen herstellen**. Wijzigingen die u in de Portal instellingen hebt aangebracht, gaan verloren. Deze optie is niet van invloed op aanpassingen van het dash board.

Zie [gebruikers instellingen exporteren of verwijderen](azure-portal-export-delete-settings.md)voor meer informatie over het **exporteren van alle instellingen** of het **verwijderen van alle instellingen en persoonlijke Dash boards**.

## <a name="change-language-and-regional-settings"></a>Taal-en land instellingen wijzigen

Er zijn twee instellingen die bepalen hoe de tekst in de Azure Portal wordt weer gegeven. De **taal** instelling bepaalt de taal die u ziet voor tekst in de Azure Portal. **Regionale notatie** bepaalt de manier waarop datums, tijden, getallen en valuta worden weer gegeven.

Als u de taal wilt wijzigen die wordt gebruikt in de Azure Portal, gebruikt u de vervolg keuzelijst om te selecteren in de lijst met beschik bare talen.

De regionale indelings selectie wordt gewijzigd zodat land instellingen alleen worden weer gegeven voor de geselecteerde taal. Als u deze automatische selectie wilt wijzigen, gebruikt u de vervolg keuzelijst om de gewenste regionale indeling te kiezen.

Als u bijvoorbeeld Engels als taal selecteert en vervolgens Verenigde Staten selecteert als regionale indeling, wordt valuta weer gegeven in Amerikaanse dollars. Als u Engels als taal selecteert en vervolgens Europa selecteert als regionale indeling, wordt valuta weer gegeven in euro.

Selecteer **Toep assen** om uw instellingen voor taal en regionale indeling bij te werken.

   ![Scherm opname van instellingen voor taal en land indeling](./media/set-preferences/language.png)

>[!NOTE]
>Deze taal-en land instellingen zijn alleen van invloed op de Azure Portal. In een nieuw tabblad of venster worden de taal instellingen van de browser gebruikt om te bepalen welke taal moet worden weer gegeven.
>

## <a name="next-steps"></a>Volgende stappen

* [Aangepaste dashboards maken en delen](azure-portal-dashboards.md)
* [Videoserie met instructies voor de Azure-portal](azure-portal-video-series.md)
