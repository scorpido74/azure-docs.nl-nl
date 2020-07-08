---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 8b919608dfc562d8db77619d5215a6828a53a4aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175977"
---
1. Zoek naar **Configuration Manager** terwijl u via Extern bureaublad bent verbonden met de virtuele machine:

    ![SSCM openen](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. Breid in het consolevenster van SQL Server Configuration Manager het gedeelte **SQL Server-netwerkconfiguratie** uit.

1. Klik in het console venster op **protocollen voor MSSQLSERVER** (de naam van het standaard exemplaar). Klik in het detail venster met de rechter muisknop op **TCP** en klik op inschakelen als deze **optie** nog niet is ingeschakeld.

    ![TCP inschakelen](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. Klik in het consolevenster op **SQL Server-services**. Klik in het detail venster met de rechter muisknop op **SQL Server (*exemplaar naam*)** (het standaard exemplaar is **SQL Server (MSSQLSERVER)**) en klik vervolgens op **opnieuw opstarten**om het exemplaar van SQL Server te stoppen en opnieuw op te starten.

    ![Database-engine opnieuw starten](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Sluit SQL Server Configuration Manager.

Zie [Enable or Disable a Server Network Protocol](https://msdn.microsoft.com/library/ms191294.aspx) (Een servernetwerkprotocol in- of uitschakelen) voor meer informatie over het inschakelen van protocollen voor de database-engine van SQL Server.
