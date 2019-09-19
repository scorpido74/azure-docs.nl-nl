---
title: 'Zelfstudie: Een door SQL Database beheerd exemplaar toevoegen aan een failovergroep'
description: Meer informatie over het configureren van een failovergroep voor uw Azure SQL Database Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 06/27/2019
ms.openlocfilehash: 04802b8b25ca21cc0099874e5a9ea69748868f6e
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103215"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Zelfstudie: Een door SQL Database beheerd exemplaar toevoegen aan een failovergroep

Een door SQL Database beheerd exemplaar toevoegen aan een failovergroep. In dit artikel leert u hoe u:

> [!div class="checklist"]
> - Een primair beheerd exemplaar maken
> - Maak een secundair beheerd exemplaar als onderdeel van een [failovergroep](sql-database-auto-failover-group.md). 
> - Failover testen

  > [!NOTE]
  > - Wanneer u deze zelf studie doorloopt, moet u ervoor zorgen dat u uw resources configureert met de [vereisten voor het instellen van failover-groepen voor een beheerd exemplaar](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Het maken van een beheerd exemplaar kan een aanzienlijke hoeveelheid tijd in beslag nemen. Als gevolg hiervan kan het enkele uren duren voordat deze zelf studie is voltooid. Zie [Managed instance Management Operations](sql-database-managed-instance.md#managed-instance-management-operations)(Engelstalig) voor meer informatie over het inrichten van tijden. 


## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien: 

- Een Azure-abonnement, [een gratis account maken](https://azure.microsoft.com/free/) als u er nog geen hebt. 


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1-resource groep en primair beheerd exemplaar maken
In deze stap maakt u de resource groep en het primaire beheerde exemplaar voor uw failover-groep met behulp van de Azure Portal. 

1. Selecteer **Azure SQL** in het linkermenu van de Azure Portal. Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie over de verschillende data bases weer geven door details weer geven te selecteren op de tegel data bases.
1. Selecteer **maken** in de tegel **SQL Managed instances** . 

    ![Beheerd exemplaar selecteren](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Op het tabblad **basis beginselen** op de pagina **Azure SQL database beheerde instantie maken**
    1. Selecteer uw **abonnement** in de vervolg keuzelijst onder **Project Details**en kies vervolgens nieuwe resource groep **maken** . Typ een naam voor de resource groep, zoals `myResourceGroup`. 
    1. Geef onder **Details van beheerd exemplaar**de naam op van uw beheerde exemplaar en de regio waar u uw beheerde exemplaar wilt implementeren. De **berekening en opslag** op standaard waarden behouden. 
    1. Geef onder **Administrator-account**een beheerders aanmelding op, zoals `azureuser`en een complex beheerders wachtwoord. 

    ![Primaire MI maken](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Laat de overige instellingen op de standaard waarden staan en selecteer **controleren + maken** om de instellingen van het beheerde exemplaar te controleren. 
1. Selecteer **maken** om uw primaire beheerde instantie te maken. 


## <a name="2---create-a-virtual-network"></a>2: een virtueel netwerk maken
In deze stap gaat u een virtueel netwerk maken voor het secundaire beheerde exemplaar. Deze stap is nodig omdat het subnet van de primaire en secundaire beheerde instanties niet-overlappende adresbereiken heeft. 

Voer de volgende stappen uit om het subnet-bereik van het primaire virtuele netwerk te controleren:
1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de resource groep en selecteer het virtuele netwerk voor uw primaire exemplaar. 
1. Selecteer **subnetten** onder **instellingen** en noteer het **adres bereik**. Het adres bereik van het subnet van het virtuele netwerk voor het secundaire beheerde exemplaar kan dit niet overlappen. 


   ![Primair subnet](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Voer de volgende stappen uit om een virtueel netwerk te maken:

1. Selecteer in de [Azure Portal](https://portal.azure.com) **een resource maken** en zoek naar *virtueel netwerk*. 
1. Selecteer de **Virtual Network** -optie die door micro soft is gepubliceerd en selecteer vervolgens **maken** op de volgende pagina. 
1. Vul de vereiste velden in om het virtuele netwerk voor uw secundaire beheerde exemplaar te configureren en selecteer vervolgens **maken**. 

   In de volgende tabel ziet u de waarden die nodig zijn voor het secundaire virtuele netwerk:

    | **Veld** | Value |
    | --- | --- |
    | **Name** |  De naam van het virtuele netwerk dat moet worden gebruikt door het secundaire beheerde exemplaar, zoals `vnet-sql-mi-secondary`. |
    | **Adresruimte** | De adres ruimte voor het virtuele netwerk, zoals `10.128.0.0/16`. | 
    | **Abonnement** | Het abonnement waar uw primaire beheerde instantie en resource groep zich bevinden. |
    | **Regio** | De locatie waar u uw secundaire beheerde exemplaar gaat implementeren. |
    | **Subnet** | De naam voor het subnet. `default`is standaard voor u bestemd. |
    | **Adresbereik**| Het adres bereik voor uw subnet. Dit moet verschillen van het adres bereik van het subnet dat wordt gebruikt door het virtuele netwerk van uw primaire beheerde instantie `10.128.0.0/24`, zoals.  |
    | &nbsp; | &nbsp; |

    ![Secundaire waarden van virtueel netwerk](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3-een secundair beheerd exemplaar maken
In deze stap maakt u een tweede beheerde instantie in de Azure Portal, waarmee ook de netwerken tussen de twee beheerde instanties worden geconfigureerd. 

Uw tweede beheerde exemplaar moet:
- Leeg zijn. 
- Hebben een ander subnet en IP-bereik dan het primaire beheerde exemplaar. 

Voer de volgende stappen uit om uw secundaire beheerde exemplaar te maken: 

1. Selecteer **Azure SQL** in het linkermenu van de Azure Portal. Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie over de verschillende data bases weer geven door details weer geven te selecteren op de tegel data bases.
1. Selecteer **maken** in de tegel **SQL Managed instances** . 

    ![Beheerd exemplaar selecteren](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Vul de vereiste velden in op het tabblad **basis principes** van de pagina **Azure SQL database beheerde instantie maken** om uw secundaire beheerde exemplaar te configureren. 

   In de volgende tabel ziet u de waarden die nodig zijn voor het secundaire beheerde exemplaar:
 
    | **Veld** | Value |
    | --- | --- |
    | **Abonnement** |  Het abonnement waar uw primaire beheerde instantie zich bevindt. |
    | **Resourcegroep**| De resource groep waar uw primaire beheerde instantie zich bevindt. |
    | **Naam van het beheerde exemplaar** | De naam van het nieuwe, secundaire beheerde exemplaar, zoals`sql-mi-secondary`  | 
    | **Regio**| De locatie voor uw secundaire beheerde exemplaar.  |
    | **Beheerdersaanmeldgegevens voor het beheerde exemplaar** | De aanmelding die u wilt gebruiken voor uw nieuwe, secundaire beheerde exemplaar, zoals `azureuser`. |
    | **Wachtwoord** | Een complex wacht woord dat wordt gebruikt door de beheerders aanmelding voor het nieuwe secundaire beheerde exemplaar.  |
    | &nbsp; | &nbsp; |

1. Selecteer op het tabblad **netwerken** , voor de **Virtual Network**, het virtuele netwerk dat u hebt gemaakt voor het secundaire beheerde exemplaar in de vervolg keuzelijst.

   ![Secundaire MI-netwerken](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Klik op het tabblad **extra instellingen** voor **geo-replicatie**op **Ja** als u een _secundaire failover wilt gebruiken_. Selecteer de primaire beheerde instantie in de vervolg keuzelijst. 
    1. Zorg ervoor dat de sortering en tijd zone overeenkomen met die van het primaire beheerde exemplaar. Het primaire beheerde exemplaar dat in deze zelf studie wordt gemaakt `SQL_Latin1_General_CP1_CI_AS` `(UTC) Coordinated Universal Time` , gebruikt de standaard waarde voor sortering en tijd zone. 

   ![Secundaire MI-netwerken](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Selecteer **controleren + maken** om de instellingen voor uw secundaire beheerde exemplaar te controleren. 
1. Selecteer **maken** om uw secundaire beheerde instantie te maken. 


## <a name="4---create-primary-virtual-network-gateway"></a>4-een primaire virtuele netwerk gateway maken 

Voor twee beheerde instanties om deel te nemen aan een failovergroep moet er een gateway zijn geconfigureerd tussen de virtuele netwerken van de twee beheerde instanties om netwerk communicatie toe te staan. U kunt de gateway voor het primaire beheerde exemplaar maken met behulp van de Azure Portal:

1. Ga in het [Azure Portal](https://portal.azure.com)naar de resource groep en selecteer de **virtuele netwerk** resource voor uw primaire beheerde exemplaar. 
1. Selecteer **subnetten** onder **instellingen** en selecteer vervolgens om een nieuw **Gateway-subnet**toe te voegen. De standaard waarden behouden. 

   ![Gateway toevoegen voor primair beheerde exemplaar](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Nadat de subnet gateway is gemaakt, selecteert u **een resource maken** in het navigatie deel venster links en `Virtual network gateway` typt u in het zoekvak. Selecteer de bron van de **virtuele netwerk gateway** die is gepubliceerd door **micro soft**. 

   ![Een nieuwe gateway voor het virtuele netwerk maken](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Vul de vereiste velden in om de gateway te configureren voor uw primaire beheerde exemplaar. 

   In de volgende tabel ziet u de waarden die nodig zijn voor de gateway voor het primaire beheerde exemplaar:
 
    | **Veld** | Value |
    | --- | --- |
    | **Abonnement** |  Het abonnement waar uw primaire beheerde instantie zich bevindt. |
    | **Name** | De naam van de gateway van uw virtuele netwerk, `primary-mi-gateway`zoals. | 
    | **Regio** | De regio waar uw secundaire beheerde instantie zich bevindt. |
    | **Gateway type** | Selecteer **VPN**. |
    | **VPN-type** | **Op route gebaseerd** selecteren |
    | **SKU**| De standaard waarde `VpnGw1`van wijzigen. |
    | **Location**| De locatie waar uw primaire beheerde instantie en primair virtuele netwerk zich bevindt.   |
    | **Virtueel netwerk**| Selecteer het virtuele netwerk dat is gemaakt in sectie 2, zoals `vnet-sql-mi-primary`. |
    | **Openbaar IP-adres**| Selecteer **Nieuw maken**. |
    | **Naam van openbaar IP-adres**| Voer een naam in voor uw IP-adres, `primary-gateway-IP`zoals. |
    | &nbsp; | &nbsp; |
1. Wijzig de andere waarden als standaard en selecteer vervolgens **controleren + maken** om de instellingen voor de gateway van uw virtuele netwerk te controleren.

   ![Primaire gateway-instellingen](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selecteer **maken** om de nieuwe virtuele netwerk gateway te maken. 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5-secundaire virtuele netwerk gateway configureren 

Herhaal de stappen in de vorige sectie om het subnet van het virtuele netwerk en de gateway voor het secundaire beheerde exemplaar te maken. Vul de vereiste velden in om de gateway te configureren voor uw secundaire beheerde exemplaar. 

   In de volgende tabel ziet u de waarden die nodig zijn voor de gateway voor het secundaire beheerde exemplaar:

   | **Veld** | Value |
   | --- | --- |
   | **Abonnement** |  Het abonnement waar uw secundaire beheerde instantie zich bevindt. |
   | **Name** | De naam van de gateway van uw virtuele netwerk, `secondary-mi-gateway`zoals. | 
   | **Regio** | De regio waar uw secundaire beheerde instantie zich bevindt. |
   | **Gateway type** | Selecteer **VPN**. |
   | **VPN-type** | **Op route gebaseerd** selecteren |
   | **SKU**| De standaard waarde `VpnGw1`van wijzigen. |
   | **Location**| De locatie waar uw secundaire beheerde instantie en secundaire virtuele netwerk zich bevindt.   |
   | **Virtueel netwerk**| Selecteer het virtuele netwerk dat is gemaakt in sectie 2, zoals `vnet-sql-mi-secondary`. |
   | **Openbaar IP-adres**| Selecteer **Nieuw maken**. |
   | **Naam van openbaar IP-adres**| Voer een naam in voor uw IP-adres, `secondary-gateway-IP`zoals. |
   | &nbsp; | &nbsp; |

   ![Instellingen van secundaire gateway](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6-de gateways verbinden
In deze stap maakt u een verbinding tussen gateways. Er moet een verbinding tot stand worden gebracht tussen de primaire en de secundaire gateway en vervolgens moet er een afzonderlijke verbinding tot stand worden gebracht tussen de secundaire en de primaire gateway. Zorg ervoor dat u dezelfde **gedeelde sleutel** gebruikt bij het configureren van de connectiviteit tussen beide gateways. 

Voer de volgende stappen uit om connectiviteit te configureren:

1. Navigeer naar uw resource groep in de [Azure Portal](https://portal.azure.com) en selecteer de primaire gateway die u hebt gemaakt in stap 4. 
1. Selecteer **verbindingen** onder **instellingen** en selecteer vervolgens **toevoegen** om een nieuwe verbinding te maken. 

   ![Verbinding toevoegen aan primaire gateway](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Voer een naam in voor de verbinding, bijvoorbeeld `Primary-connection`, en typ een waarde voor de `mi1mi2psk` **gedeelde sleutel** , zoals. 
1. Selecteer de **tweede virtuele netwerk gateway** en selecteer vervolgens de gateway voor het secundaire beheerde exemplaar, zoals `secondary-mi-gateway`. 

   ![Primaire naar secundaire verbinding maken](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Selecteer **OK** om uw nieuwe primaire-naar-secundaire gateway verbinding toe te voegen.
1. Herhaal deze stappen om een verbinding te maken van de gateway van het secundaire beheerde exemplaar naar de gateway van het primaire beheerde exemplaar. 

   ![Secundaire verbinding maken](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7-een failovergroep maken
In deze stap maakt u de failovergroep en voegt u beide beheerde exemplaren toe aan de groep. 

1. Selecteer **Azure SQL** in het linkermenu van de [Azure Portal](https://portal.azure.com). Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer de primaire beheerde instantie die u in de eerste sectie hebt gemaakt, `sql-mi-primary`zoals. 
1. Ga onder **instellingen**naar **failover-groepen voor instanties** en kies vervolgens **groep toevoegen** om de pagina **failover-groep voor instanties** te openen. 

   ![Een failovergroep toevoegen](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Typ op de pagina **failover-groep voor exemplaar** de naam van de failovergroep, bijvoorbeeld `failovergrouptutorial` en kies vervolgens het secundaire beheerde `sql-mi-secondary` exemplaar, bijvoorbeeld in de vervolg keuzelijst. Selecteer **maken** om uw failovergroep te maken. 

   ![Failovergroep maken](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Zodra de implementatie van de failovergroep is voltooid, wordt u teruggeleid naar de pagina **failover-groep** . 

## <a name="8---test-failover"></a>8-testfailover
In deze stap wordt uw failover-groep overschreven naar de secundaire server en wordt er een failback uitgevoerd met behulp van de Azure Portal. 

1. Navigeer naar uw beheerde instantie binnen de [Azure Portal](https://portal.azure.com) en selecteer **failover-groepen voor instanties** onder instellingen. 
1. Controleer welk beheerde exemplaar het primaire is en welk Managed instance het secundaire exemplaar is. 
1. Selecteer **failover** en selecteer vervolgens **Ja** in de waarschuwing over TDS-sessies die worden losgekoppeld. 

   ![Failover-groep](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Controleer welk beheerd-exemplaar het primaire is en welk exemplaar het secundaire is. Als failover is geslaagd, moeten de twee exemplaren geschakelde rollen hebben. 

   ![Beheerde instanties hebben geswitcheerde rollen na een failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Selecteer **failover** eenmaal opnieuw om de primaire instantie weer naar de primaire rol te laten mislukken. 


## <a name="clean-up-resources"></a>Resources opschonen
U kunt resources opschonen door eerst het beheerde exemplaar te verwijderen, vervolgens het virtuele cluster, vervolgens de resterende resources en ten slotte de resource groep. 

1. Navigeer naar uw resource groep in de [Azure Portal](https://portal.azure.com). 
1. Selecteer het beheerde exemplaar en selecteer vervolgens **verwijderen**. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **verwijderen**. Dit proces kan enige tijd in beslag nemen en totdat het is voltooid, kunt u het *virtuele cluster* of andere afhankelijke resources niet verwijderen. Controleer het verwijderen op het tabblad activiteit om te bevestigen dat uw beheerde exemplaar is verwijderd. 
1. Wanneer het beheerde exemplaar is verwijderd, verwijdert u het *virtuele cluster* door het te selecteren in de resource groep en vervolgens **verwijderen**te kiezen. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **verwijderen**. 
1. Alle resterende resources verwijderen. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **verwijderen**. 
1. Verwijder de resource groep door **resource groep verwijderen**te selecteren, typ de naam van de resource groep `myResourceGroup`en selecteer vervolgens **verwijderen**. 

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een failovergroep geconfigureerd tussen twee beheerde exemplaren. U hebt geleerd hoe u:

> [!div class="checklist"]
> - Een primair beheerd exemplaar maken
> - Maak een secundair beheerd exemplaar als onderdeel van een [failovergroep](sql-database-auto-failover-group.md). 
> - Failover testen

Ga naar de volgende Snelstartgids over hoe u verbinding maakt met uw beheerde exemplaar en hoe u een Data Base kunt herstellen naar uw beheerde exemplaar: 

> [!div class="nextstepaction"]
> [Verbinding maken met uw beheerde exemplaar](sql-database-managed-instance-configure-vm.md)
> [een Data Base herstellen naar een beheerd exemplaar](sql-database-managed-instance-get-started-restore.md)


