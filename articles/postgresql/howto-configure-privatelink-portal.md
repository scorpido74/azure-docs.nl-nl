---
title: Persoonlijke koppeling voor de installatie methode van de portal voor Azure Database for PostgreSQL-één server (preview-versie)
description: Meer informatie over het configureren van een persoonlijke koppeling voor Azure Database for PostgreSQL-één server van Azure Portal
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: f7a796408267fda08d765425a3c529895a251782
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281101"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-preview-using-portal"></a>Een persoonlijke koppeling maken en beheren voor Azure Database for PostgreSQL-één server (preview) met behulp van de portal

Een persoonlijk eind punt is de fundamentele bouw steen voor privé-koppeling in Azure. Hiermee kunnen Azure-resources, zoals Virtual Machines (Vm's), privé communiceren met persoonlijke koppelings bronnen.  In dit artikel leert u hoe u de Azure Portal kunt gebruiken om een virtuele machine te maken in een Azure-Virtual Network en een Azure Database for PostgreSQL enkele server met een persoonlijk Azure-eind punt.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database for PostgreSQL één server ondersteunt de prijs categorieën voor Algemeen en geoptimaliseerd voor geheugen.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij de [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Een Azure-VM maken

In deze sectie maakt u een virtueel netwerk en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron (een PostgreSQL-server in Azure).

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken
In deze sectie maakt u een Virtual Network en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron.

1. Selecteer linksboven in het scherm **Een resource maken** > **Netwerken** > **Virtueel netwerk**.
2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *MyVirtualNetwork*in. |
    | Adresruimte | Voer *10.1.0.0/16* in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Locatie | Selecteer **Europa - west**.|
    | Subnet - Naam | Voer *mySubnet*in. |
    | Subnet - adresbereik | Voer *10.1.0.0/24* in. |
    |||
3. Laat de rest als standaard en selecteer **maken**.

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een resource maken** > **reken** > **virtuele machine**.

2. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.  |
    | **EXEMPLAARDETAILS** |  |
    | Naam van de virtuele machine | Voer *myVm*in. |
    | Regio | Selecteer **Europa - west**. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | afbeelding | Selecteer **Windows Server 2019 Data Center**. |
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

## <a name="create-an-azure-database-for-postgresql-single-server"></a>Een Azure Database for PostgreSQL enkele server maken

In deze sectie maakt u een Azure Database for PostgreSQL-server in Azure. 

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een resource** > **data bases** maken > **Azure database for PostgreSQL**.

1. Selecteer in **Azure database for PostgreSQL implementatie optie** **één server** en geef deze informatie op:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Project Details** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.|
    | **Server Details** |  |
    |Servernaam  | Voer *mijn server*in. Als deze naam wordt gebruikt, maakt u een unieke naam.|
    | Gebruikers naam beheerder| Voer de naam van de beheerder van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wacht woord moet ten minste acht tekens lang zijn en voldoen aan de gedefinieerde vereisten. |
    | Locatie | Selecteer een Azure-regio waar u wilt dat uw PostgreSQL-server zich bevindt. |
    |Versie  | Selecteer de database versie van de PostgreSQL-server die vereist is.|
    | Compute + Storage| Selecteer de prijs categorie die nodig is voor de server op basis van de werk belasting. |
    |||
 
7. Selecteer **OK**. 
8. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert. 
9. Wanneer u het bericht door gegeven validatie ziet, selecteert u **maken**. 
10. Wanneer u het bericht door gegeven validatie ziet, selecteert u maken. 

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

In deze sectie maakt u een PostgreSQL-server en voegt u hieraan een persoonlijk eind punt toe. 

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een resource maken** > **netwerk** > **privé koppelings centrum (preview)** .
2. Selecteer **Start**in het **persoonlijke koppelings centrum**op de optie om **een particuliere verbinding met een service te maken**.

    ![Overzicht van persoonlijke koppelingen](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. Voer in **een persoonlijk eind punt maken (preview)-basis beginselen**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Project Details** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.|
    | **Exemplaar Details** |  |
    | Name | Voer *myPrivateEndpoint*in. Als deze naam wordt gebruikt, maakt u een unieke naam. |
    |Regio|Selecteer **Europa - west**.|
    |||
5. Selecteer **volgende: resource**.
6. Voer in **een persoonlijk eind punt maken-resource**in of Selecteer deze gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer verbinding maken met een Azure-resource in mijn Directory.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **micro soft. DBforPostgreSQL/servers**. |
    | Bron |*Mijn server* selecteren|
    |Doel-subresource |*PostgresqlServer* selecteren|
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
    |Privé-DNS zone |Selecteer *(nieuw) privatelink. post gres. data base. Azure. com* |
    |||

1. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert. 
2. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**. 

    ![Privé koppeling gemaakt](media/concepts-data-access-and-security-private-link/show-postgres-private-link.png)

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Verbinding maken met een virtuele machine met behulp van Extern bureaublad (RDP)


Nadat u **myVm**hebt gemaakt, kunt u als volgt verbinding maken met het Internet: 

1. Voer in de zoek balk van de portal *myVm*in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het *gedownloade RDP* -bestand.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>De PostgreSQL-server privé openen vanuit de VM

1. Open Power shell in de Extern bureaublad van *myVM*.

2. Voer `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`in. 

    U ontvangt een bericht dat er ongeveer als volgt uitziet:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the PostgreSQL server using any available client. In the example below I have used [Azure Data studio](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) to do the operation.

4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **PostgreSQL**.|
    | Server name| Select *mydemopostgresserver.privatelink.postgres.database.azure.com* |
    | User name | Enter username as username@servername which is provided during the PostgreSQL server creation. |
    |Password |Enter a password provided during the PostgreSQL server creation. |
    |SSL|Select **Required**.|
    ||

5. Select Connect.

6. Browse databases from left menu.

7. (Optionally) Create or query information from the postgreSQL server.

8. Close the remote desktop connection to myVm.

## Clean up resources
When you're done using the private endpoint, PostgreSQL server, and the VM, delete the resource group and all of the resources it contains:

1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results.
2. Select **Delete resource group**.
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this how-to, you created a VM on a virtual network, an Azure Database for PostgreSQL - Single server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the PostgreSQL server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
