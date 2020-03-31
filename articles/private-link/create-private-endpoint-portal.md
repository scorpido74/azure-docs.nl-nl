---
title: Snelstart - Privéeindpunten beheren in Azure
description: Meer informatie over het maken van een privéeindpunt met de Azure-portal in deze Quickstart
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 485eb14938fc7e490ea2d68c9090cdfdbf01cc8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78252564"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Snelstart: een privéeindpunt maken met Azure-portal

Een privéeindpunt is de fundamentele bouwsteen voor privékoppelingen in Azure. Hiermee kunnen Azure-resources, zoals Virtuele Machines (VM's), privé communiceren met privékoppelingsbronnen. In deze Quickstart leert u hoe u een VM maakt op een Azure Virtual Network, een SQL Database Server met een Azure-privéeindpunt met behulp van de Azure-portal. Vervolgens u veilig toegang krijgen tot de SQL Database Server van de VM.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vm"></a>Een virtuele machine maken
In deze sectie maakt u een virtueel netwerk en het subnet om de VM te hosten die wordt gebruikt om toegang te krijgen tot uw Private Link-bron (in dit voorbeeld een SQL-server in Azure).

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie maakt u een virtueel netwerk en het subnet om de VM te hosten die wordt gebruikt om toegang te krijgen tot uw Private Link-bron.

In deze sectie moet u de volgende parameters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resourcegroepnaam>**  | myResourceGroup |
| **\<>van de naam van het virtuele netwerk** | myVirtualNetwork          |
| **\<regionaam>**          | VS - west-centraal    |
| **\<IPv4-adresruimte>**   | 10.1.0.0\16          |
| **\<subnetnaam>**          | mySubnet        |
| **\<subnet-adresbereik>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Selecteer linksboven in het scherm in de Azure-portal de optie **Een resource** > **Compute** > **Virtual Machine maken**.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit in de vorige sectie gemaakt.  |
    | **EXEMPLAARDETAILS** |  |
    | Naam van de virtuele machine | Voer *myVm*in . |
    | Regio | Selecteer **WestCentralUS**. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | Installatiekopie | Selecteer **Windows Server 2019-datacenter**. |
    | Grootte | Laat de standaardwaarde **Standard DS1 v2** staan. |
    | **ADMINISTRATOR-ACCOUNT** |  |
    | Gebruikersnaam | Voer een gebruikersnaam van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **REGELS VOOR BINNENKOMENDE POORT** |  |
    | Openbare poorten voor inkomend verkeer | Laat de standaardwaarde **Geen** staan. |
    | **GELD BESPAREN** |  |
    | Hebt u al een Windows-licentie? | Laat de standaardwaarde **Nee** staan. |
    |||

1. Selecteer **Volgende: Schijven**.

1. In **Een virtuele machine maken - Schijven**laat u de standaardinstellingen achter en selecteer **Volgende: Netwerken**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | Laat de standaard **MyVirtualNetwork**.  |
    | Adresruimte | Laat de **standaardwaarde 10.1.0.0/24 .**|
    | Subnet | Laat het standaard **mySubnet (10.1.0.0/24)** achter .|
    | Openbare IP | Handhaaf de standaardinstelling **(new) myVm-ip**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**.|
    |||


1. Selecteer **Controleren + maken**. U wordt naar de **pagina Controleren + maken geleid,** waar Azure uw configuratie valideert.

1. Wanneer u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken**.

## <a name="create-a-sql-database-server"></a>Een SQL-databaseserver maken
In deze sectie maakt u een SQL-databaseserver in Azure. 

1. Selecteer linksboven in het scherm in de Azure-portal de optie Een**SQL-database** **voor resourcedatabases** > **Databases** > maken .

1. Voer deze informatie in **SQL-database maken - Basisbeginselen**in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Databasedetails** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit in de vorige sectie gemaakt.|
    | **EXEMPLAARDETAILS** |  |
    | Databasenaam  | Voer *mijndatabase*in . Als deze naam wordt aangenomen, maakt u een unieke naam. |
    |||
5. Selecteer **in Server**de optie Nieuw **maken**. 
6. Voer **in Nieuwe server**deze informatie in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |Servernaam  | Voer *mijnserver*in . Als deze naam wordt aangenomen, maakt u een unieke naam.|
    | Aanmeldgegevens van serverbeheerder| Voer een beheerdersnaam van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet ten minste 8 tekens lang zijn en voldoen aan de gedefinieerde vereisten. |
    | Locatie | Selecteer een Azure-gebied waar u wilt dat uw SQL Server zich bevindt. |
    
7. Selecteer **OK**. 
8. Selecteer **Controleren + maken**. U wordt naar de **pagina Controleren + maken geleid,** waar Azure uw configuratie valideert. 
9. Wanneer u het doorgegeven bericht Validatie ziet, selecteert u **Maken**. 
10. Wanneer u het doorgegeven bericht Validatie ziet, selecteert u Maken. 

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

In deze sectie maakt u een SQL-server en voegt u er een privéeindpunt aan toe. 

1. Selecteer linksboven in het scherm in de Azure-portal de optie **Een private** > **koppelingscentrum** > voor netwerken**maken (Voorbeeld)**.
2. Selecteer **Start**in **Private Link Center - Overzicht**over de optie om een **privéverbinding met een service op te bouwen.**
1. Voer **in Een privéeindpunt maken (Voorbeeld) - Basisbeginselen**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectdetails** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit in de vorige sectie gemaakt.|
    | **EXEMPLAARDETAILS** |  |
    | Name | Voer *mijnPrivéEindpunt in*. Als deze naam wordt aangenomen, maakt u een unieke naam. |
    |Regio|Selecteer **WestCentralUS**.|
    |||
5. Selecteer **Volgende: Resource**.
6. Voer in **Een privéeindpunt maken - Resource**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer Verbinding maken met een Azure-bron in mijn map.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **Microsoft.Sql/servers**. |
    | Resource |MyServer *selecteren*|
    |Subresource target |SqlServer *selecteren*|
    |||
7. Selecteer **Volgende: Configuratie**.
8. Voer **in Een privéeindpunt maken (Voorbeeld) - Configuratie**in of selecteer deze informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    |**Networking**| |
    | Virtueel netwerk| Selecteer *MyVirtualNetwork*. |
    | Subnet | Selecteer *mySubnet*. |
    |**INTEGRATIE VAN PRIVÉ-DNS**||
    |Integreren met private DNS-zone |Selecteer **Ja**. |
    |Privé-DNS-zone |Selecteer *(Nieuw)privatelink.database.windows.net* |
    |||

1. Selecteer **Controleren + maken**. U wordt naar de **pagina Controleren + maken geleid,** waar Azure uw configuratie valideert. 
2. Wanneer u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Verbinding maken met een virtuele machine met Extern bureaublad (RDP)


Nadat u **myVm**hebt gemaakt, u er als volgt verbinding mee maken via het internet: 

1. Voer *myVm*in in de zoekbalk van het portaal.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het *bestand downloaded.rdp.*

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikersnaam en het wachtwoord in dat u hebt opgegeven bij het maken van de vm.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > selecteren**Gebruik een ander account**om de referenties op te geven die u hebt ingevoerd toen u de vm hebt gemaakt.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Toegang tot de SQL-databaseserver privé vanaf de VM

1. Open PowerShell in het extern bureaublad van *myVM.*

2. Voer `nslookup myserver.database.windows.net` in. 

    Je ontvangt een bericht dat vergelijkbaar is met dit:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. SQL [Server Management Studio installeren](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. Voer in **Verbinding maken met de server**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Servertype| Selecteer **Database Engine**.|
    | Servernaam| Selecteer *myserver.database.windows.net* |
    | Gebruikersnaam | Voer de username@servername gebruikersnaam in zoals die wordt opgegeven tijdens het maken van de SQL-server. |
    |Wachtwoord |Voer een wachtwoord in dat is opgegeven tijdens het maken van de SQL-server. |
    |Wachtwoord onthouden|Selecteer **Ja**.|
    |||
1. Selecteer **Verbinden**.
2. Blader door databases vanuit het linkermenu.
3. (Optioneel) Informatie uit mijn database maken of opvragen.
4. Sluit de verbinding met extern bureaublad met *myVm*. 

## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het privéeindpunt SQL-server en de VM, verwijdert u de brongroep en alle bronnen die deze bevat: 
1. Typ *myResourceGroup* in het vak **Zoeken** boven aan de portal en selecteer *myResourceGroup* in de zoekresultaten. 
2. Selecteer **Resourcegroep verwijderen**. 
3. Voer myResourceGroup in voor **TYP DE RESOURCEGROEPNAAM** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een VM gemaakt op een virtueel netwerk, een SQL-databaseserver en een privéeindpunt voor privétoegang. U hebt vanaf het internet verbinding gemaakt met één VM en veilig gecommuniceerd met de SQL-databaseserver via Private Link. Zie [Wat is privéeindpunt voor Azure?](private-endpoint-overview.md)
