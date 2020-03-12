---
title: Time-out voor inactiviteit op Directory niveau instellen voor gebruikers van de Azure Portal | Microsoft Docs
description: Beheerders kunnen het maximum aantal niet-actieve tijd afdwingen voordat een sessie wordt afgemeld. Het beleid voor time-out van inactiviteit is ingesteld op het niveau van de Directory.
services: azure-portal
keywords: instellingen, time-out
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096607"
---
# <a name="set-directory-level-inactivity-timeout"></a>Time-out voor inactiviteit op Directory niveau instellen

De instelling time-out voor inactiviteit helpt u bij het beveiligen van uw resources tegen onbevoegde toegang als gebruikers verg eten hun werk station te beveiligen. Wanneer een gebruiker enige tijd inactief is, wordt de Azure Portal-sessie automatisch afgemeld. Beheerders in de [rol globale beheerder](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) kunnen het maximum aantal niet-actieve tijd afdwingen voordat een sessie wordt afgemeld. De instelling time-out voor inactiviteit is van toepassing op het niveau van de Directory. Zie [Active Directory Domain Services Overview](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)voor meer informatie over directory's.

## <a name="configure-the-inactive-timeout-setting"></a>De instelling voor de inactieve time-out configureren

Als u een globale beheerder bent en u een instelling voor een inactieve time-out wilt afdwingen voor alle gebruikers van de Azure Portal, voert u de volgende stappen uit:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **instellingen** in de koptekst van de globale pagina.
3. Selecteer de koppeling tekst **configureren time-out**mapniveau.

    ![Scherm opname van de Portal instellingen met een koppeling tekst gemarkeerd](./media/admin-timeout/settings.png)

4. Er wordt een nieuwe pagina geopend. Selecteer op de pagina mapniveau op **Directory niveau configureren** de optie time-out voor inactiviteit op mapniveau **inschakelen voor de Azure Portal** om de instelling in te scha kelen.
5. Voer vervolgens de **uren** en **minuten** in voor de maximale tijds duur dat een gebruiker inactief mag zijn voordat de sessie automatisch wordt afgemeld.
6. Selecteer **Toepassen**.

    ![Scherm afbeelding van de pagina voor het instellen van de time-out voor inactiviteit op Directory niveau](./media/admin-timeout/configure.png)

Als u wilt controleren of het beleid voor time-out van inactiviteit juist is ingesteld, selecteert u **meldingen** in de koptekst van de globale pagina. Controleer of er een melding over geslaagde meldingen wordt weer gegeven.

  ![Scherm opname van bericht met geslaagde melding voor time-out voor inactiviteit op Directory niveau](./media/admin-timeout/confirmation.png)

De instelling wordt van kracht voor nieuwe sessies. Deze functie wordt niet onmiddellijk toegepast op alle gebruikers die al zijn aangemeld.

> [!NOTE]
> Als een globale beheerder een time-outinstelling op mapniveau heeft geconfigureerd, kunnen gebruikers het beleid overschrijven en hun eigen inactieve afmeldings periode instellen. De gebruiker moet echter een tijds interval kiezen dat lager is dan wat is ingesteld op mapniveau door de globale beheerder.
>

## <a name="next-steps"></a>Volgende stappen

* [Uw Azure Portal voor keuren instellen](set-preferences.md)
* [Gebruikersinstellingen exporteren of verwijderen](azure-portal-export-delete-settings.md)
* [Hoog contrast inschakelen of het thema wijzigen](azure-portal-change-theme-high-contrast.md)
