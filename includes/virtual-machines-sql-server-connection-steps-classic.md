---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 57f238a8f91df1271e91894b88a7f02118b1f123
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175979"
---
### <a name="determine-the-dns-name-of-the-virtual-machine"></a>De DNS-naam van de virtuele machine achterhalen
Als u vanaf een andere computer verbinding wilt maken met de database-engine van SQL Server, moet u de DNS-naam (Domain Name System) van de virtuele machine weten. (Dit is de naam waaraan de virtuele machine op internet wordt herkend. U kunt ook het IP-adres gebruiken, maar dit kan veranderen wanneer Azure resources verplaatst wegens redundantie of onderhoud. De DNS-naam blijft hetzelfde omdat deze kan worden omgeleid naar een nieuw IP-adres.)  

1. Selecteer **Virtuele machines (klassiek)** in Azure Portal (of vanuit de vorige stap).
2. Selecteer uw SQL-VM.
3. Selecteer op de blade **Virtuele machine** de **DNS-naam** van de virtuele machine.
   
    ![DNS-naam](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Verbinding maken met de Database-engine vanaf een andere computer
1. Open SQL Server Management Studio op een computer die is verbonden met internet.
2. In het dialoogvenster **Verbinding maken met server** of **Verbinding maken met database-engine** typt u in het vak **Servernaam** de DNS-naam van de virtuele machine (zoals bepaald in de vorige taak) en het poortnummer van een openbaar eindpunt. Gebruik hiervoor de notatie *DNSnaam,poortnummer*, bijvoorbeeld **mysqlvm.cloudapp.net,57500**.
   
    ![Verbinding maken met behulp van SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    Als u het eerder gekozen poortnummer van het openbare eindpunt niet meer weet, kunt u dit terugvinden in het gedeelte **Eindpunten** van de blade **Virtuele machine**.
   
    ![Openbare poort](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. Kies in het vak **Verificatie** **SQL Server-verificatie**.
4. Typ in het vak **Aanmelding** de naam van een aanmelding die u eerder hebt gemaakt.
5. Typ in het vak **Wachtwoord** het wachtwoord van de aanmelding die u eerder hebt gemaakt.
6. Klik op **Verbinden**.

