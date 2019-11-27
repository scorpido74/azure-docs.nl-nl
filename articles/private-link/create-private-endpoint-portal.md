---
title: 'Quick Start: privé-eind punten beheren in azure'
description: Meer informatie over het maken van een persoonlijk eind punt met behulp van de Azure Portal in deze Snelstartgids
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2cb3c1d798e48017604d72d8a438d6a1071199ea
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419761"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Snelstartgids: een persoonlijk eind punt maken met Azure Portal

Een persoonlijk eind punt is de fundamentele bouw steen voor privé-koppeling in Azure. Hiermee kunnen Azure-resources, zoals Virtual Machines (Vm's), privé communiceren met persoonlijke koppelings bronnen. In deze Quick Start leert u hoe u een virtuele machine kunt maken op een Azure-Virtual Network, een SQL Database-Server met een Azure-persoonlijk eind punt met behulp van Azure Portal. Daarna kunt u veilig toegang krijgen tot de SQL Database-Server vanaf de VM.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


> [!NOTE]
> Een of meer persoonlijke eind punten zijn niet toegestaan in combi natie met Service-eind punten in hetzelfde subnet.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vm"></a>Een virtuele machine maken
In deze sectie maakt u een virtueel netwerk en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron (een SQL-Server in Azure in dit voor beeld).

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken


In deze sectie maakt u een Virtual Network en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron.

1. Selecteer linksboven in het scherm **Een resource maken** > **Netwerken** > **Virtueel netwerk**.
1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *MyVirtualNetwork*in. |
    | Adresruimte | Voer *10.1.0.0/16* in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Locatie | Selecteer **WestCentralUS**.|
    | Subnet - naam | Voer *mySubnet*in. |
    | Subnet - adresbereik | Voer *10.1.0.0/24* in. |
    |||
1. Laat de rest als standaard en selecteer **maken**.


### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een resource maken** > **reken** > **virtuele machine**.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.  |
    | **INSTANTIEDETAILS** |  |
    | Naam van de virtuele machine | Voer *myVm*in. |
    | Regio | Selecteer **WestCentralUS**. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | Installatiekopie | Selecteer **Windows Server 2019 Data Center**. |
    | Grootte | Laat de standaardwaarde **Standard DS1 v2** staan. |
    | **ADMINISTRATOR-ACCOUNT** |  |
    | Gebruikersnaam | Voer een gebruikers naam van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **REGELS VOOR BINNENKOMENDE POORT** |  |
    | Openbare poorten voor inkomend verkeer | Laat de standaardwaarde **Geen** staan. |
    | **GELD BESPAREN** |  |
    | Hebt u al een Windows-licentie? | Laat de standaardwaarde **Nee** staan. |
    |||

1. Selecteer **volgende: schijven**.

1. In **een virtuele machine maken-schijven**, de standaard instellingen behouden en **volgende selecteren: netwerken**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | De standaard **MyVirtualNetwork**behouden.  |
    | Adresruimte | De standaard **10.1.0.0/24**behouden.|
    | Subnet | Behoud de standaard **mySubnet (10.1.0.0/24)** .|
    | Openbare IP | Handhaaf de standaardinstelling **(new) myVm-ip**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**.|
    |||


1. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert.

1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

## <a name="create-a-sql-database-server"></a>Een SQL database-server maken
In deze sectie maakt u een SQL database-server in Azure. 

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een resource** > **data bases** maken > **SQL database**.

1. Voer in **SQL database basis beginselen maken**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Database Details** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.|
    | **INSTANTIEDETAILS** |  |
    | Databasenaam  | Voer *mydatabase*in. Als deze naam wordt gebruikt, maakt u een unieke naam. |
    |||
5. Selecteer in **Server**de optie **nieuwe maken**. 
6. In **nieuwe server**voert u de volgende gegevens in of selecteert u deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |Servernaam  | Voer *mijn server*in. Als deze naam wordt gebruikt, maakt u een unieke naam.|
    | Aanmeldgegevens van serverbeheerder| Voer de naam van de beheerder van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wacht woord moet ten minste acht tekens lang zijn en voldoen aan de gedefinieerde vereisten. |
    | Locatie | Selecteer een Azure-regio waar u wilt dat uw SQL Server zich bevindt. |
    
7. Selecteer **OK**. 
8. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert. 
9. Wanneer u het bericht door gegeven validatie ziet, selecteert u **maken**. 
10. Wanneer u het bericht door gegeven validatie ziet, selecteert u maken. 

## <a name="create-a-private-endpoint"></a>Een persoonlijk eind punt maken

In deze sectie maakt u een SQL Server en voegt u hieraan een persoonlijk eind punt toe. 

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een resource maken** > **netwerk** > **privé koppelings centrum (preview)** .
2. Selecteer **Start**in het **persoonlijke koppelings centrum**op de optie om **een particuliere verbinding met een service te maken**.
1. Voer in **een persoonlijk eind punt maken (preview)-basis beginselen**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Project Details** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.|
    | **INSTANTIEDETAILS** |  |
    | Naam | Voer * myPrivateEndpoint*in. Als deze naam wordt gebruikt, maakt u een unieke naam. |
    |Regio|Selecteer **WestCentralUS**.|
    |||
5. Selecteer **volgende: resource**.
6. Voer in **een persoonlijk eind punt maken-resource**in of Selecteer deze gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer verbinding maken met een Azure-resource in mijn Directory.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **micro soft. SQL/servers**. |
    | Resource |*Mijn server* selecteren|
    |Doel-subresource |*SqlServer* selecteren|
    |||
7. Selecteer **volgende: Configuratie**.
8. Voer in **een persoonlijk eind punt maken (preview)-configuratie**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**INBEL**| |
    | Virtueel netwerk| Selecteer *MyVirtualNetwork*. |
    | Subnet | Selecteer *mySubnet*. |
    |**INTEGRATIE VAN PARTICULIERE DNS**||
    |Integreren met een privé-DNS-zone |Selecteer **Ja**. |
    |Privé-DNS zone |Selecteer *(nieuw) privatelink. data base. Windows. net* |
    |||

1. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert. 
2. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Verbinding maken met een virtuele machine met behulp van Extern bureaublad (RDP)


Nadat u **myVm*hebt gemaakt, kunt u als volgt verbinding maken met het Internet: 

1. Voer in de zoek balk van de portal *myVm*in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. Azure maakt een Remote Desktop Protocol-bestand ( *.rdp*) en downloadt het bestand naar uw computer.

1. Open het bestand gedownloade. rdp *.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>De SQL database-server privé openen vanuit de VM

1. Open Power shell in de Extern bureaublad van *myVM*.

2. Voer `nslookup myserver.database.windows.net`in. 

    U ontvangt een bericht dat er ongeveer als volgt uitziet:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Installeer [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. Typ of Selecteer in **verbinding maken met server**de volgende informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | Servertype| Selecteer **Database Engine**.|
    | Servernaam| *MyServer.database.Windows.net* selecteren |
    | Gebruikersnaam | Voer de gebruikers naam in als username@servername die wordt opgegeven tijdens het maken van de SQL-Server. |
    |Wachtwoord |Voer een wacht woord in dat u hebt opgegeven tijdens het maken van de SQL-Server. |
    |Wacht woord onthouden|Selecteer **Ja**.|
    |||
1. Selecteer **Verbinden**.
2. Bladeren door data bases vanuit het menu links.
3. Eventueel Gegevens uit mydatabase maken of er een query op uitvoeren.
4. Sluit de verbinding met extern bureau blad met *myVm*. 

## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het persoonlijke eind punt, de SQL-Server en de virtuele machine, verwijdert u de resource groep en alle resources die deze bevat: 
1. Geef *myResourceGroup* op in het **zoekvak** boven aan de portal en selecteer *myResourceGroup* in de zoek resultaten. 
2. Selecteer **Resourcegroep verwijderen**. 
3. Voer myResourceGroup in bij **Typ de naam van de resource groep** en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een VM gemaakt op een virtueel netwerk, een SQL database-server en een persoonlijk eind punt voor persoonlijke toegang. U hebt verbinding gemaakt met één virtuele machine via internet en u kunt veilig worden door gegeven aan de SQL database server met behulp van een persoonlijke koppeling. Zie [Wat is Azure private endpoint?](private-endpoint-overview.md)voor meer informatie over privé-eind punten.
