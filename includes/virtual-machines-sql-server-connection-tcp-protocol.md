---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 8b919608dfc562d8db77619d5215a6828a53a4aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175977"
---
1. Zoek naar **Configuration Manager** terwijl u via Extern bureaublad bent verbonden met de virtuele machine:

    ![SSCM openen](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. Breid in het consolevenster van SQL Server Configuration Manager het gedeelte **SQL Server-netwerkconfiguratie** uit.

1. Klik in het consoledeelvenster op **Protocollen voor MSSQLSERVER** (de standaardinstantienaam.) Klik in het detailvenster met de rechtermuisknop op **TCP** en klik op **Inschakelen** als dit nog niet is ingeschakeld.

    ![TCP inschakelen](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. Klik in het consolevenster op **SQL Server-services**. Klik in het detailvenster met de rechtermuisknop op **SQL Server *(instantienaam*)** (de standaardinstantie is SQL Server **(MSSQLSERVER)** en klik vervolgens op **Opnieuw starten**om de instantie van SQL Server te stoppen en opnieuw te starten.

    ![Database-engine opnieuw starten](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Sluit SQL Server Configuration Manager.

Zie [Enable or Disable a Server Network Protocol](https://msdn.microsoft.com/library/ms191294.aspx) (Een servernetwerkprotocol in- of uitschakelen) voor meer informatie over het inschakelen van protocollen voor de database-engine van SQL Server.
