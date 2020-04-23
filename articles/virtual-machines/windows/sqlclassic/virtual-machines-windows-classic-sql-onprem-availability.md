---
title: On-premises AlwaysOn-beschikbaarheids groepen uitbreiden naar Azure | Microsoft Docs
description: In deze zelf studie wordt gebruikgemaakt van resources die zijn gemaakt met het klassieke implementatie model, en wordt beschreven hoe u de wizard replica toevoegen in SQL Server Management Studio (SSMS) gebruikt om een AlwaysOn-beschikbaarheids groep replica in azure toe te voegen.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 4521c2c112c93e83144cfc84d600208817b2ccac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978050"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>On-premises AlwaysOn-beschikbaarheidsgroepen uitbreiden naar Azure
AlwaysOn-beschikbaarheids groepen bieden een hoge Beschik baarheid voor groepen data base door secundaire replica's toe te voegen. Met deze replica's kunnen data bases mislukken bij een storing. Daarnaast kunnen ze worden gebruikt voor het offloaden van Lees-workloads of back-uptaken.

U kunt on-premises beschikbaarheids groepen uitbreiden naar Microsoft Azure door een of meer virtuele Azure-machines in te richten met SQL Server en ze vervolgens als replica's toe te voegen aan uw on-premises beschikbaarheids groepen.

In deze zelf studie wordt ervan uitgegaan dat u het volgende hebt:

* Een actief Azure-abonnement. U kunt [zich registreren voor een gratis proef versie](https://azure.microsoft.com/pricing/free-trial/).
* Een bestaande AlwaysOn-beschikbaarheids groep on-premises. Zie AlwaysOn [Availability groups](https://msdn.microsoft.com/library/hh510230.aspx)(AlwaysOn-beschikbaarheids groepen) voor meer informatie over beschikbaarheids groepen.
* Connectiviteit tussen het on-premises netwerk en uw virtuele Azure-netwerk. Zie [een site-naar-site-verbinding maken met behulp van de Azure Portal (klassiek)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)voor meer informatie over het maken van dit virtuele netwerk.

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Resource Manager en klassiek](../../../azure-resource-manager/management/deployment-models.md). In dit artikel wordt beschreven hoe u het klassieke implementatie model gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

## <a name="add-azure-replica-wizard"></a>Wizard Azure replica toevoegen
In deze sectie wordt beschreven hoe u de **wizard Azure replica toevoegen** gebruikt om uw oplossing voor AlwaysOn-beschikbaarheids groepen uit te breiden om Azure-replica's te omvatten.

> [!IMPORTANT]
> De **wizard Azure replica toevoegen** ondersteunt alleen virtuele machines die zijn gemaakt met het klassieke implementatie model. Nieuwe VM-implementaties moeten het nieuwere Resource Manager-model gebruiken. Als u Vm's gebruikt met Resource Manager, moet u de secundaire Azure-replica hand matig toevoegen met behulp van Transact-SQL-opdrachten (deze worden hier niet weer gegeven). Deze wizard werkt niet in het Resource Manager-scenario.

1. Vouw in SQL Server Management Studio altijd de**beschikbaarheids groepen** > met **hoge Beschik baarheid** > uit **[naam van uw beschikbaarheids groep]**.
2. Klik met de rechter muisknop op **beschikbaarheids replica's**en klik vervolgens op **replica toevoegen**.
3. Standaard wordt de **wizard replica toevoegen aan beschikbaarheids groep** weer gegeven. Klik op **Volgende**.  Als u de optie **Deze pagina niet opnieuw weer geven** onder aan de pagina hebt geselecteerd tijdens een vorige keer dat deze wizard wordt gestart, wordt dit scherm niet weer gegeven.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. U moet verbinding maken met alle bestaande secundaire replica's. U kunt klikken op **verbinden...** naast elke replica kunt u klikken op **verbinding maken...** onder aan het scherm. Klik na de verificatie op **volgende** om naar het volgende scherm te gaan.
5. Op de pagina **Replica's opgeven** worden meerdere tabbladen bovenaan weer gegeven: **replica's**, **eind punten**, **back-upvoorkeuren**en **listener**. Klik op het tabblad **replica's** op **Azure replica toevoegen...** om de wizard Azure replica toevoegen te starten.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Selecteer een bestaand Azure-beheer certificaat in het lokale Windows-certificaat archief als u er een hebt geïnstalleerd. Selecteer of typ de id van een Azure-abonnement als u er een hebt gebruikt. U kunt klikken op downloaden om een Azure-beheer certificaat te downloaden en te installeren en de lijst met abonnementen te downloaden met behulp van een Azure-account.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. U vult elk veld op de pagina met waarden die worden gebruikt voor het maken van de virtuele Azure-machine (VM) die als host moet fungeren voor de replica.
   
   | Instelling | Beschrijving |
   | --- | --- |
   | **Installatiekopie** |Selecteer de gewenste combi natie van besturings systeem en SQL Server |
   | **VM-grootte** |Selecteer de grootte van de virtuele machine die het beste past bij uw bedrijfs behoeften |
   | **VM-naam** |Geef een unieke naam op voor de nieuwe virtuele machine. De naam moet tussen de 3 en 15 tekens bevatten, mag alleen letters, cijfers en afbreek streepjes bevatten en moet beginnen met een letter en eindigen op een letter of cijfer. |
   | **VM-gebruikers naam** |Geef een gebruikers naam op die het Administrator-account wordt op de VM |
   | **Beheerders wachtwoord voor de VM** |Geef een wacht woord op voor het nieuwe account |
   | **Wacht woord bevestigen** |Bevestig het wacht woord van het nieuwe account |
   | **Virtual Network** |Geef het virtuele netwerk van Azure op dat door de nieuwe virtuele machine moet worden gebruikt. Zie [Virtual Network Overview](../../../virtual-network/virtual-networks-overview.md)voor meer informatie over virtuele netwerken. |
   | **Virtual Network subnet** |Geef het subnet van het virtuele netwerk op dat door de nieuwe virtuele machine moet worden gebruikt |
   | **Domain** |Controleren of de vooraf ingevulde waarde voor het domein juist is |
   | **Domein gebruikers naam** |Geef een account op dat zich in de lokale groep Administrators op de knoop punten van het lokale cluster bevindt |
   | **Wachtwoord** |Geef het wacht woord voor de gebruikers naam van het domein op |
8. Klik op **OK** om de implementatie-instellingen te valideren.
9. De juridische voor waarden worden weer gegeven volgende. Lees en klik op **OK** als u akkoord gaat met deze voor waarden.
10. De pagina **Replica's opgeven** wordt opnieuw weer gegeven. Controleer de instellingen voor de nieuwe Azure-replica op de tabbladen **replica's**, **eind punten**en **back-up** . Wijzig de instellingen om te voldoen aan uw bedrijfs vereisten.  Zie voor meer informatie over de para meters op deze tabbladen de [pagina Replica's opgeven (wizard Nieuwe beschikbaarheids groep/add replica)](https://msdn.microsoft.com/library/hh213088.aspx). Houd er rekening mee dat listeners niet kunnen worden gemaakt met behulp van het tabblad listener voor beschikbaarheids groepen die Azure-replica's bevatten. Als er al een listener is gemaakt voordat de wizard werd gestart, ontvangt u een bericht dat aangeeft dat het niet wordt ondersteund in Azure. We gaan kijken hoe u listeners maakt in het gedeelte **een listener voor een beschikbaarheids groep maken** .
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Klik op **Volgende**.
12. Selecteer de gegevens synchronisatie methode die u wilt gebruiken op de pagina **eerste gegevens synchronisatie selecteren** en klik op **volgende**. Selecteer voor de meeste scenario's **volledige gegevens synchronisatie**. Zie voor meer informatie over de methoden voor gegevens synchronisatie de [optie eerste gegevens synchronisatie selecteren (AlwaysOn wizards voor beschikbaarheids groepen)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Bekijk de resultaten op de pagina **validatie** . Corrigeer openstaande problemen en voer indien nodig de validatie opnieuw uit. Klik op **Volgende**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Controleer de instellingen op de pagina **samen vatting** en klik vervolgens op **volt ooien**.
15. Het inrichtings proces begint. Wanneer de wizard is voltooid, klikt u op **sluiten** om de wizard af te sluiten.

> [!NOTE]
> Met de wizard Azure replica toevoegen maakt u een logboek bestand in Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Dit logboek bestand kan worden gebruikt om problemen met mislukte Azure replica-implementaties op te lossen. Als de wizard geen actie uitvoert, worden alle vorige bewerkingen teruggedraaid, inclusief het verwijderen van de ingerichte VM.
> 
> 

## <a name="create-an-availability-group-listener"></a>Een listener voor een beschikbaarheids groep maken
Nadat de beschikbaarheids groep is gemaakt, maakt u een listener voor clients om verbinding te maken met de replica's. Listeners sturen binnenkomende verbindingen naar de primaire of een alleen-lezen secundaire replica. Zie [een ILB-listener configureren voor AlwaysOn-beschikbaarheids groepen in azure](../classic/ps-sql-int-listener.md)voor meer informatie over listeners.

## <a name="next-steps"></a>Volgende stappen
Naast het gebruik van de **wizard Azure replica toevoegen** om uw AlwaysOn-beschikbaarheids groep uit te breiden naar Azure, kunt u ook enkele SQL Server workloads volledig naar Azure verplaatsen. Zie [een SQL Server virtuele machine inrichten in azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)om aan de slag te gaan.

Zie [SQL Server op azure virtual machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in azure vm's.

