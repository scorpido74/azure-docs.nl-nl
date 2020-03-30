---
title: On-premises always on availability-groepen uitbreiden naar Azure | Microsoft Documenten
description: In deze zelfstudie worden resources gebruikt die zijn gemaakt met het klassieke implementatiemodel en wordt beschreven hoe u de wizard Replica toevoegen in SQL Server Management Studio (SSMS) gebruiken om een replica voor beschikbaarheid always on beschikbaarheid toe te voegen in Azure.
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
Always On Availability Groepen bieden een hoge beschikbaarheid voor groepen database door secundaire replica's toe te voegen. Deze replica's kunnen mislukken over databases in het geval van een storing. Daarnaast kunnen ze worden gebruikt om leesworkloads of back-uptaken te ontladen.

U on-premises beschikbaarheidsgroepen uitbreiden naar Microsoft Azure door een of meer Azure VM's in te richten op SQL Server en deze vervolgens toe te voegen als replica's aan uw on-premises beschikbaarheidsgroepen.

In deze zelfstudie wordt ervan uitgegaan dat u het volgende hebt:

* Een actief Azure-abonnement. U [zich aanmelden voor een gratis proefperiode.](https://azure.microsoft.com/pricing/free-trial/)
* Een bestaande Always On Availability Group on-premises. Zie Beschikbaarheidsgroepen altijd op beschikbaarheid voor meer informatie over [beschikbaarheidsgroepen.](https://msdn.microsoft.com/library/hh510230.aspx)
* Connectiviteit tussen het on-premises netwerk en uw virtuele Azure-netwerk. Zie [Een site-to-site-verbinding maken met de Azure-portal (klassiek)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)voor meer informatie over het maken van dit virtuele netwerk.

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en Classic.](../../../azure-resource-manager/management/deployment-models.md) In dit artikel wordt het implementatiemodel Classic gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

## <a name="add-azure-replica-wizard"></a>Wizard Azure Replica toevoegen
In deze sectie ziet u hoe u de **wizard Azure Replica toevoegen** gebruiken om de oplossing Always On Availability Group uit te breiden met Azure-replica's.

> [!IMPORTANT]
> De **wizard Azure replica toevoegen** ondersteunt alleen virtuele machines die zijn gemaakt met het klassieke implementatiemodel. Nieuwe VM-implementaties moeten het nieuwere Resource Manager-model gebruiken. Als u VM's met Resource Manager gebruikt, moet u de secundaire Azure-replica handmatig toevoegen met transact-SQL-opdrachten (hier niet weergegeven). Deze wizard werkt niet in het resourcebeheerscenario.

1. Vanuit SQL Server Management Studio u **always on high availability** > **groups** > **[Name of your Availability Group]** uitbreiden.
2. Klik met de rechtermuisknop op **Replica's voor beschikbaarheid**en klik vervolgens op **Replica toevoegen**.
3. Standaard wordt de **wizard Replica toevoegen aan beschikbaarheidsgroep** weergegeven. Klik op **Volgende**.  Als u de optie **Deze pagina niet opnieuw weergeven** onder aan de pagina hebt geselecteerd tijdens een eerdere lancering van deze wizard, wordt dit scherm niet weergegeven.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. U moet verbinding maken met alle bestaande secundaire replica's. U klikken op **Connect...** naast elke replica of u klikken **Op Alle verbinden ...** aan de onderkant van het scherm. Klik na verificatie op **Volgende** om door te gaan naar het volgende scherm.
5. Op de pagina **Replica's opgeven** worden meerdere tabbladen bovenaan weergegeven: **replica's,** **eindpunten,** **back-upvoorkeuren**en **listener**. Klik op het tabblad **Replica's** op **Azure Replica toevoegen...** om de wizard Azure replica toevoegen te starten.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Selecteer een bestaand Azure Management Certificate in het lokale Windows-certificaatarchief als u er al eerder een hebt geïnstalleerd. Selecteer of voer de id van een Azure-abonnement in als u er al eerder een hebt gebruikt. U op Downloaden klikken om een Azure Management Certificate te downloaden en te installeren en de lijst met abonnementen te downloaden met een Azure-account.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. U vult elk veld op de pagina met waarden die worden gebruikt om de Azure Virtual Machine (VM) te maken die de replica host.
   
   | Instelling | Beschrijving |
   | --- | --- |
   | **Afbeelding** |Selecteer de gewenste combinatie van OS en SQL Server |
   | **VM-grootte** |Selecteer de grootte van VM die het beste past bij uw bedrijfsbehoeften |
   | **VM-naam** |Geef een unieke naam op voor de nieuwe virtuele machine. De naam moet tussen de 3 en 15 tekens bevatten, mag alleen letters, cijfers en koppeltekens bevatten en moet beginnen met een letter en eindigen met een letter of getal. |
   | **VM-gebruikersnaam** |Een gebruikersnaam opgeven die het beheerdersaccount op de VM wordt |
   | **VM-beheerderswachtwoord** |Een wachtwoord opgeven voor het nieuwe account |
   | **Wachtwoord bevestigen** |Het wachtwoord van het nieuwe account bevestigen |
   | **Virtueel netwerk** |Geef het virtuele Azure-netwerk op dat de nieuwe virtuele machine moet gebruiken. Zie Overzicht van virtueel [netwerk voor](../../../virtual-network/virtual-networks-overview.md)meer informatie over virtuele netwerken. |
   | **Subnet voor virtueel netwerk** |Specify the virtual network subnet that the new VM should use |
   | **Domain** |Controleren of de vooraf ingevulde waarde voor het domein juist is |
   | **Domeinnaam domein** |Een account opgeven dat zich in de groep lokale beheerders op de lokale clusterknooppunten bevindt |
   | **Wachtwoord** |Het wachtwoord voor de domeinnaam opgeven |
8. Klik op **OK** om de implementatie-instellingen te valideren.
9. De volgende wettelijke voorwaarden worden weergegeven. Lees en klik op **OK** als u akkoord gaat met deze voorwaarden.
10. De pagina **Replica's opgeven** wordt opnieuw weergegeven. Controleer de instellingen voor de nieuwe Azure-replica op de tabbladen **Replica's,** **Eindpunten**en **Back-upvoorkeuren.** Wijzig instellingen om aan uw bedrijfsvereisten te voldoen.  Zie [Replicapagina opgeven (wizard Nieuwe beschikbaarheidsgroep/replica toevoegen) voor](https://msdn.microsoft.com/library/hh213088.aspx)meer informatie over de parameters op deze tabbladen. Houd er rekening mee dat listeners niet kunnen worden gemaakt met het tabblad Listener voor beschikbaarheidsgroepen die Azure-replica's bevatten. Als er bovendien al een listener is gemaakt voordat de wizard wordt gestart, ontvangt u een bericht dat deze niet wordt ondersteund in Azure. We bekijken hoe we luisteraars kunnen maken in de sectie **Een beschikbaarheidsgroeplistener maken.**
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Klik op **Volgende**.
12. Selecteer de methode voor gegevenssynchronisatie die u wilt gebruiken op de pagina **Initiële gegevenssynchronisatie selecteren** en klik op **Volgende**. Selecteer voor de meeste scenario's **Volledige gegevenssynchronisatie**. Zie [Pagina Voor initiële gegevenssynchronisatieselecteren (Wizards Altijd op beschikbaarheidgroep) voor](https://msdn.microsoft.com/library/hh231021.aspx)meer informatie over gegevenssynchronisatiemethoden.
13. Bekijk de resultaten op de **pagina Validatie.** Corrigeer openstaande problemen en voer de validatie indien nodig opnieuw uit. Klik op **Volgende**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Controleer de instellingen op de **pagina Overzicht** en klik op **Voltooien**.
15. Het inrichtingsproces begint. Wanneer de wizard is voltooid, klikt u op **Sluiten** om de wizard af te sluiten.

> [!NOTE]
> Met de wizard Azure Replica toevoegen wordt een logboekbestand gemaakt in Gebruikers\Gebruikersnaam\AppData\Lokaal\SQL Server\AddReplicaWizard. Dit logboekbestand kan worden gebruikt om mislukte Azure-replica-implementaties op te lossen. Als de wizard geen actie uitvoert, worden alle eerdere bewerkingen teruggedraaid, inclusief het verwijderen van de ingerichte vm.
> 
> 

## <a name="create-an-availability-group-listener"></a>Een authenticiteitsgroeplistener maken
Nadat de beschikbaarheidsgroep is gemaakt, moet u een listener maken voor clients om verbinding te maken met de replica's. Listeners sturen binnenkomende verbindingen naar de primaire of een alleen-lezen secundaire replica. Zie [Een ILB-listener configureren voor Always On Availability Groups in Azure](../classic/ps-sql-int-listener.md)voor meer informatie over luisteraars.

## <a name="next-steps"></a>Volgende stappen
Naast het gebruik van de **wizard Azure Replica toevoegen** om uw groep voor beschikbaarheid always on uit te breiden naar Azure, u sommige SQL Server-workloads ook volledig naar Azure verplaatsen. Zie [Een SQL Server Virtual Machine inrichten op Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)om aan de slag te gaan.

Zie [SQL Server op Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in Azure VM's.

