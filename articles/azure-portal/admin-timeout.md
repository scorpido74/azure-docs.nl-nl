---
title: Time-out op inactiviteit op directoryniveau instellen voor gebruikers van de Azure-portal | Microsoft Documenten
description: Beheerders kunnen de maximale inactieve tijd afdwingen voordat een sessie is afgemeld. Het time-outbeleid voor inactiviteit is ingesteld op directoryniveau.
services: azure-portal
keywords: instellingen, time-out
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096607"
---
# <a name="set-directory-level-inactivity-timeout"></a>Time-out op inactiviteit op directoryniveau instellen

De instelling voor een time-out voor inactiviteit helpt uw resources te beschermen tegen onbevoegde toegang als gebruikers vergeten hun werkstation te beveiligen. Wanneer een gebruiker een tijdje niet actief is geweest, wordt de Azure-portalsessie automatisch afgemeld. Beheerders in de [rol Globale beheerder](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) kunnen de maximale inactieve tijd afdwingen voordat een sessie wordt afgemeld. De instelling voor een time-out van inactiviteit is van toepassing op directoryniveau. Zie Overzicht van Active [Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)voor meer informatie over mappen.

## <a name="configure-the-inactive-timeout-setting"></a>De instelling voor inactieve time-out configureren

Als u een globale beheerder bent en u een inactieve time-outinstelling wilt afdwingen voor alle gebruikers van de Azure-portal, voert u de volgende stappen uit:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Instellingen** in de algemene paginakoptekst.
3. Selecteer de **time-out van koppelingstekst Mapniveau configureren**.

    ![Schermafbeelding van portalinstellingen met koppelingstekst gemarkeerd](./media/admin-timeout/settings.png)

4. Er wordt een nieuwe pagina geopend. Selecteer op de pagina **Time-out van inactiviteitconfigureren op mapniveau configureren** de optie **Inactieve time-out op adres van de Azure-portal inschakelen voor de** instelling.
5. Voer vervolgens de **uren** en **minuten** in voor de maximale tijd dat een gebruiker niet kan worden inactief voordat zijn sessie automatisch wordt afgemeld.
6. Selecteer **Toepassen**.

    ![Schermafbeelding van pagina om een time-out op inactiviteit op directoryniveau in te stellen](./media/admin-timeout/configure.png)

Als u wilt controleren of het time-outbeleid voor inactiviteit correct is ingesteld, selecteert u **Meldingen** in de algemene paginakoptekst. Controleer of een melding voor succes wordt vermeld.

  ![Schermafbeelding van een succesvol meldingsbericht voor een time-out op inactiviteitsniveau op directoryniveau](./media/admin-timeout/confirmation.png)

De instelling wordt van kracht voor nieuwe sessies. Het is niet onmiddellijk van toepassing op gebruikers die al zijn aangemeld.

> [!NOTE]
> Als een globale beheerder een time-outinstelling op mapniveau heeft geconfigureerd, kunnen gebruikers het beleid overschrijven en hun eigen inactieve afmeldingsduur instellen. De gebruiker moet echter een tijdsinterval kiezen dat kleiner is dan wat de globale beheerder op directoryniveau is ingesteld.
>

## <a name="next-steps"></a>Volgende stappen

* [Uw voorkeuren voor de Azure-portal instellen](set-preferences.md)
* [Gebruikersinstellingen exporteren of verwijderen](azure-portal-export-delete-settings.md)
* [Hoog contrast inschakelen of het thema wijzigen](azure-portal-change-theme-high-contrast.md)
