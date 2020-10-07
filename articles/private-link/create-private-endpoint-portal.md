---
title: Quickstart – Privé-eindpunten beheren in Azure
description: In deze quickstart leert u hoe u een privé-eindpunt kunt maken met behulp van de Azure Portal
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: ef6d49c9046ba04bbac40ec9bf555e12d2faa8f6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "84021701"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Quickstart: Een privé-eindpunt maken met behulp van de Azure Portal

Een privé-eindpunt is de fundamentele bouwsteen voor een Private Link in Azure. Het biedt Azure-resources, zoals virtuele machines, de mogelijkheid om Private Link-resources te gebruiken om privé met elkaar communiceren. In deze quickstart leert u hoe u een VM maakt in een Microsoft Azure Virtual Network, een logische SQL-server met een privé-eindpunt in Azure, met behulp van de Azure Portal. Vervolgens hebt u vanuit de VM veilige toegang tot SQL Database.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vm"></a>Een virtuele machine maken
In deze sectie maakt u een virtueel netwerk en het subnet om de VM te hosten die wordt gebruikt om op een veilige manier toegang te krijgen tot uw Private Link-resource (bijvoorbeeld SQL-server in Azure in dit voorbeeld).

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie maakt u een virtueel netwerk en het subnet om de VM te hosten die wordt gebruikt om op een veilige manier toegang te krijgen tot uw Private Link-resource.

In deze sectie moet u de volgende parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | VS - west-centraal    |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Selecteer in de linkerbovenhoek van het scherm in de Azure Portal de optie **Een resource maken** > **Compute** > **Virtuele machine**.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze in de vorige sectie gemaakt.  |
    | **EXEMPLAARDETAILS** |  |
    | Naam van de virtuele machine | Voer *myVm* in. |
    | Regio | Selecteer **WestCentralUS**. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter**. |
    | Grootte | Laat de standaardwaarde **Standard DS1 v2** staan. |
    | **ADMINISTRATOR-ACCOUNT** |  |
    | Gebruikersnaam | Voer een gebruikersnaam naar keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **REGELS VOOR BINNENKOMENDE POORT** |  |
    | Openbare poorten voor inkomend verkeer | Laat de standaardwaarde **Geen** staan. |
    | **GELD BESPAREN** |  |
    | Hebt u al een Windows-licentie? | Laat de standaardwaarde **Nee** staan. |
    |||

1. Selecteer **Volgende: Schijven**.

1. Behoud de standaardinstellingen in **Een virtuele machine maken – schijven** en selecteer **Volgende: Netwerken**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | Laat de standaardwaarde **MyVirtualNetwork** staan.  |
    | Adresruimte | Laat de standaardwaarde **10.1.0.0/24** staan.|
    | Subnet | Laat de standaardwaarde **mySubnet (10.1.0.0/24)** staan.|
    | Openbare IP | Handhaaf de standaardinstelling **(new) myVm-ip**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**.|
    |||


1. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

1. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

## <a name="create-a-logical-sql-server"></a>Een logische SQL-server maken

In deze sectie maakt u een logische SQL-server in Azure. 

1. Selecteer in de linkerbovenhoek van het scherm in de Azure Portal de optie **Een resource maken** > **Databases** > **SQL-database**.

1. In **Een SQL-database maken – Basisprincipes** typt of selecteert u de volgende informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Databasedetails** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze in de vorige sectie gemaakt.|
    | **EXEMPLAARDETAILS** |  |
    | Databasenaam  | Voer *mydatabase* in. Als deze naam al wordt gebruikt, maakt u een unieke naam. |
    |||
5. Selecteer onder **Server** de optie **Nieuwe maken**. 
6. Typ of selecteer in **Nieuwe server** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    |Servernaam  | Voer *myserver* in. Als deze naam al wordt gebruikt, maakt u een unieke naam.|
    | Aanmeldgegevens van serverbeheerder| Voer de naam van de beheerder van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 8 tekens lang zijn en moet voldoen aan de vooraf gedefinieerde vereisten. |
    | Locatie | Selecteer een Azure-regio voor de SQL Server. |
    
7. Selecteer **OK**. 
8. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure. 
9. Als u het bericht Validatie geslaagd ziet, selecteert u **Maken**. 
10. Als u het bericht Validatie geslaagd ziet, selecteert u Maken. 

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

In deze sectie maakt u een SQL-server en voegt u hieraan een privé-eindpunt toe. 

1. Selecteer in de linkerbovenhoek van het scherm in de Azure Portal de optie **Een resource maken** > **Netwerken** > **Private Link-centrum (preview)** .
2. Selecteer in **Private Link-centrum – Overzicht** bij de optie **Een particuliere verbinding met een service maken** de optie **Start**.
1. Typ of selecteer in **Een privé-eindpunt maken – Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze in de vorige sectie gemaakt.|
    | **EXEMPLAARDETAILS** |  |
    | Naam | Voer *myPrivateEndpoint* in. Als deze naam al wordt gebruikt, maakt u een unieke naam. |
    |Regio|Selecteer **WestCentralUS**.|
    |||
5. Selecteer **Volgende: Resource**.
6. Typ of selecteer in **Een privé-eindpunt maken – Resource** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer Verbinding maken met een Azure-resource in mijn directory.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **Microsoft.Sql/servers**. |
    | Resource |Selecteer *myServer*|
    |Stel subresource in |Selecteer *sqlServer*|
    |||
7. Selecteer **Volgende: Configuratie**.
8. Typ of selecteer in **Een privé-eindpunt maken – Configuratie** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    |**NETWERKEN**| |
    | Virtueel netwerk| Selecteer *MyVirtualNetwork*. |
    | Subnet | Selecteer *mySubnet*. |
    |**INTEGRATIE VAN PRIVÉ-DNS**||
    |Integreren met privé-DNS-zone |Selecteer **Ja**. |
    |Privé-DNS-zone |Selecteer *(New)privatelink.database.windows.net* |
    |||

1. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure. 
2. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Verbinding maken met een virtuele machine met behulp van Extern bureaublad (RDP)


Nadat u **myVm** hebt gemaakt, maakt u hiermee als volgt verbinding via internet: 

1. Voer in de zoekbalk van de portal *myVm* in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het *downloaded.rdp*-bestand.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-sql-database-privately-from-the-vm"></a>Privé-toegang tot SQL Database vanuit de VM

1. Open PowerShell in het extern bureaublad van *myVM*.

2. Voer `nslookup myserver.database.windows.net` in. 

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

4. Typ of selecteer in **Verbinding maken met server** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Servertype| Selecteer **Database Engine**.|
    | Servernaam| Selecteer *myserver.database.windows.net* |
    | Gebruikersnaam | Voer in de notatie username@servername de gebruikersnaam in die tijdens het maken van de SQL-server is verstrekt. |
    |Wachtwoord |Voer een wachtwoord in dat u hebt opgegeven tijdens het maken van de SQL-server. |
    |Wachtwoord onthouden|Selecteer **Ja**.|
    |||
1. Selecteer **Verbinden**.
2. Blader in het menu aan de linkerkant door databases.
3. (Optioneel) U kunt mydatabase maken of er een query op uitvoeren.
4. Sluit de externe bureaubladverbinding met *myVm*. 

## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het privé-eindpunt, de SQL-server en de VM, verwijdert u de resourcegroep en alle resources die deze bevat: 
1. Typ *myResourceGroup* in het vak **Zoeken** bovenaan de portal en selecteer *myResourceGroup* in de zoekresultaten. 
2. Selecteer **Resourcegroep verwijderen**. 
3. Typ myResourceGroup voor **TYP DE NAAM VAN DE RESOURCEGROEP** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een VM gemaakt in een virtueel netwerk, een logische SQL-server en een privé-eindpunt voor privé-toegang. U hebt via internet verbinding gemaakt met één virtuele machine en met behulp van Private Link op veilige manier gecommuniceerd met SQL Database. Zie [Wat is een Azure-privé-eindpunt?](private-endpoint-overview.md) voor meer informatie over privé-eindpunten.
