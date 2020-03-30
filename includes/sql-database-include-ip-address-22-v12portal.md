---
title: Firewallregels op serverniveau
description: Firewallregels op serverniveau
keywords: sql-verbinding, verbindingstekenreeks
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176114"
---
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer Alle **services**in de lijst aan de linkerkant .

3. Scroll en selecteer **SQL-servers**.

    ![Uw Azure SQL Database-server zoeken in de portal][b21-FindServerInPortal]
5. Typ in het tekstvak filter de naam van uw server. Uw rij wordt weergegeven.

6. Selecteer de rij voor uw server. Er wordt een blade voor uw server weergegeven.

7. Selecteer **Instellingen**op uw serverblad .

8. Selecteer **Firewall**.

    ![Selecteer Instellingen > Firewall][b31-SettingsFirewallNavig]
9. Selecteer **Client-IP toevoegen**. Typ een naam voor uw nieuwe regel in het eerste tekstvak.

10. Typ de lage en hoge IP-adreswaarden voor het bereik dat u wilt inschakelen.

    * Het kan handig zijn om de lage waarde einde met **0,0** en de hoge waarde einde met **.255**.

11. Selecteer **Opslaan**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
