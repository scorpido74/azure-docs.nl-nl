---
title: Private Link - Azure portal - Azure Database for PostgreSQL - Single server Private Link - Azure portal - Azure Database for PostgreSQL - Single server Private Link - Azure portal - Azure Database for PostgreSQL - Single server Private Link
description: 'Meer informatie over het configureren van privékoppeling voor Azure Database voor PostgreSQL: Single server vanuit Azure-portal'
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 72dcf95c8ae8d8da34532fa96e3bf0371f5112fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370913"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-portal"></a>Private Link voor Azure Database voor PostgreSQL maken en beheren met Portal

Een privéeindpunt is de fundamentele bouwsteen voor privékoppelingen in Azure. Hiermee kunnen Azure-resources, zoals Virtuele Machines (VM's), privé communiceren met privékoppelingsbronnen.  In dit artikel leert u hoe u de Azure-portal gebruiken om een VM te maken in een Azure Virtual Network en een Azure Database voor PostgreSQL Single-server met een Azure-privéeindpunt.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database voor PostgreSQL Single-server prijsniveaus voor algemeen gebruik en geheugengeoptimaliseerd ondersteunt.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Een Azure VM maken

In deze sectie maakt u een virtueel netwerk en het subnet om de VM te hosten die wordt gebruikt om toegang te krijgen tot uw Private Link-bron (een PostgreSQL-server in Azure).

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken
In deze sectie maakt u een virtueel netwerk en het subnet om de VM te hosten die wordt gebruikt om toegang te krijgen tot uw Private Link-bron.

1. Selecteer linksboven**Networking** > in het scherm de optie Een**virtueel netwerknetwerk voor** **bronnen** > maken .
2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *MyVirtualNetwork in*. |
    | Adresruimte | Voer *10.1.0.0/16* in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Locatie | Selecteer **Europa - west**.|
    | Subnet - Naam | Voer *mySubnet*in . |
    | Subnet - adresbereik | Voer *10.1.0.0/24* in. |
    |||
3. Laat de rest als standaard en selecteer **Maken**.

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Selecteer linksboven in het scherm in de Azure-portal de optie **Een resource** > **Compute** > **Virtual Machine maken**.

2. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit in de vorige sectie gemaakt.  |
    | **EXEMPLAARDETAILS** |  |
    | Naam van de virtuele machine | Voer *myVm*in . |
    | Regio | Selecteer **Europa - west**. |
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

## <a name="create-an-azure-database-for-postgresql-single-server"></a>Een Azure-database maken voor PostgreSQL Single-server

In deze sectie maakt u een Azure Database voor PostgreSQL-server in Azure. 

1. Selecteer linksboven in het scherm in de Azure-portal de optie Azure Database voor > **brondatabases** > **maken voor PostgreSQL**. **Create a resource**

1. Selecteer in **de optie Azure Database for PostgreSQL-implementatie**de optie Eén **server** en geef deze informatie op:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectdetails** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit in de vorige sectie gemaakt.|
    | **Servergegevens** |  |
    |Servernaam  | Voer *mijnserver*in . Als deze naam wordt aangenomen, maakt u een unieke naam.|
    | Gebruikersnaam beheerder| Voer een beheerdersnaam van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet ten minste 8 tekens lang zijn en voldoen aan de gedefinieerde vereisten. |
    | Locatie | Selecteer een Azure-gebied waar u wilt dat uw PostgreSQL Server zich bevindt. |
    |Versie  | Selecteer de databaseversie van de PostgreSQL-server die vereist is.|
    | Compute + Opslag| Selecteer de prijscategorie die nodig is voor de server op basis van de werkbelasting. |
    |||
 
7. Selecteer **OK**. 
8. Selecteer **Controleren + maken**. U wordt naar de **pagina Controleren + maken geleid,** waar Azure uw configuratie valideert. 
9. Wanneer u het doorgegeven bericht Validatie ziet, selecteert u **Maken**. 
10. Wanneer u het doorgegeven bericht Validatie ziet, selecteert u Maken. 

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

In deze sectie maakt u een PostgreSQL-server en voegt u er een privéeindpunt aan toe. 

1. Selecteer linksboven in het scherm in de Azure-portal de optie **Een private koppeling voor bronnetwerken** > **Networking** > **maken**.
2. Selecteer **Start**in **Private Link Center - Overzicht**over de optie om een **privéverbinding met een service op te bouwen.**

    ![Overzicht van Private Link](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. Voer deze informatie in **Een privéeindpunt maken - Basisbeginselen**in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectdetails** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit in de vorige sectie gemaakt.|
    | **Instantiedetails** |  |
    | Name | Voer *mijnPrivéEindpunt in*. Als deze naam wordt aangenomen, maakt u een unieke naam. |
    |Regio|Selecteer **Europa - west**.|
    |||
5. Selecteer **Volgende: Resource**.
6. Voer in **Een privéeindpunt maken - Resource**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer Verbinding maken met een Azure-bron in mijn map.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **Microsoft.DBforPostgreSQL/servers**. |
    | Resource |MyServer *selecteren*|
    |Subresource target |*PostgresqlServer selecteren*|
    |||
7. Selecteer **Volgende: Configuratie**.
8. Voer **in Een privéeindpunt maken - Configuratie**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**Networking**| |
    | Virtueel netwerk| Selecteer *MyVirtualNetwork*. |
    | Subnet | Selecteer *mySubnet*. |
    |**INTEGRATIE VAN PRIVÉ-DNS**||
    |Integreren met private DNS-zone |Selecteer **Ja**. |
    |Privé-DNS-zone |Selecteer *(Nieuw)privatelink.postgres.database.azure.com* |
    |||

1. Selecteer **Controleren + maken**. U wordt naar de **pagina Controleren + maken geleid,** waar Azure uw configuratie valideert. 
2. Wanneer u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken**. 

    ![Private Link gemaakt](media/concepts-data-access-and-security-private-link/show-postgres-private-link.png)

    > [!NOTE] 
    > De FQDN in de DNS-instelling voor klanten wordt niet opgelost in het geconfigureerde privé-IP-adres. U moet een DNS-zone instellen voor de geconfigureerde FQDN zoals [hier](../dns/dns-operations-recordsets-portal.md)wordt weergegeven.

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

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Toegang tot de PostgreSQL-server privé vanaf de VM

1. Open PowerShell in het extern bureaublad van *myVM.*

2. Voer `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

    Je ontvangt een bericht dat vergelijkbaar is met dit:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Test de private link-verbinding voor de PostgreSQL-server met elke beschikbare client. In het onderstaande voorbeeld heb ik [Azure Data studio](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) gebruikt om de bewerking uit te voeren.

4. Voer in **Nieuwe verbinding**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Servertype| Selecteer **PostgreSQL**.|
    | Servernaam| Selecteer *mydemopostgresserver.privatelink.postgres.database.azure.com* |
    | Gebruikersnaam | Voer de username@servername gebruikersnaam in die wordt opgegeven tijdens het maken van de PostgreSQL-server. |
    |Wachtwoord |Voer een wachtwoord in dat is opgegeven tijdens het maken van de PostgreSQL-server. |
    |SSL|Selecteer **Vereist**.|
    ||

5. Selecteer Verbinden.

6. Blader door databases vanuit het linkermenu.

7. (Optioneel) Informatie maken of query's maken vanaf de postgreSQL-server.

8. Sluit de verbinding met extern bureaublad met myVm.

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u klaar bent met het privéeindpunt, de PostgreSQL-server en de VM, verwijdert u de brongroep en alle bronnen die deze bevat:

1. Typ *myResourceGroup* in het vak **Zoeken** boven aan de portal en selecteer *myResourceGroup* in de zoekresultaten.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ myResourceGroup voor **TYPE DE NAAM VAN DE BRONGROEP** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze how-to hebt u een VM gemaakt op een virtueel netwerk, een Azure Database voor PostgreSQL - Single server en een privéeindpunt voor privétoegang. U hebt vanaf het internet verbinding gemaakt met één VM en veilig gecommuniceerd met de PostgreSQL-server via Private Link. Zie [Wat is privéeindpunt](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)voor Azure voor meer informatie over privéeindpunten.
