---
title: Persoonlijke koppeling-Azure Portal-Azure Database for PostgreSQL-één server
description: Meer informatie over het configureren van een persoonlijke koppeling voor Azure Database for PostgreSQL-één server van Azure Portal
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 72dcf95c8ae8d8da34532fa96e3bf0371f5112fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79370913"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-portal"></a>Een persoonlijke koppeling maken en beheren voor Azure Database for PostgreSQL-één server met behulp van portal

Een persoonlijk eind punt is de fundamentele bouw steen voor privé-koppeling in Azure. Hiermee kunnen Azure-resources, zoals Virtual Machines (Vm's), privé communiceren met persoonlijke koppelings bronnen.  In dit artikel leert u hoe u de Azure Portal kunt gebruiken om een virtuele machine te maken in een Azure-Virtual Network en een Azure Database for PostgreSQL enkele server met een persoonlijk Azure-eind punt.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database for PostgreSQL één server ondersteunt de prijs categorieën voor Algemeen en geoptimaliseerd voor geheugen.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Een Azure-VM maken

In deze sectie maakt u een virtueel netwerk en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron (een PostgreSQL-server in Azure).

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken
In deze sectie maakt u een Virtual Network en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron.

1. Selecteer in de linkerbovenhoek van het scherm **een resource** > **netwerk** > **virtueel netwerk**maken.
2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *MyVirtualNetwork*in. |
    | Adresruimte | Voer *10.1.0.0/16* in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Locatie | Selecteer **Europa - west**.|
    | Subnet - Naam | Voer *mySubnet*in. |
    | Subnet - adresbereik | Voer *10.1.0.0/24* in. |
    |||
3. Laat de rest als standaard en selecteer **maken**.

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal een**virtuele machine**voor het > **berekenen** > van **een resource maken**.

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
    | Subnet | Behoud de standaard **mySubnet (10.1.0.0/24)**.|
    | Openbare IP | Handhaaf de standaardinstelling **(new) myVm-ip**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**.|
    |||


1. Selecteer **controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert.

1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

## <a name="create-an-azure-database-for-postgresql-single-server"></a>Een Azure Database for PostgreSQL enkele server maken

In deze sectie maakt u een Azure Database for PostgreSQL-server in Azure. 

1. Selecteer in de linkerbovenhoek van het scherm in de Azure Portal **een resource** > **databases** > maken**Azure database for PostgreSQL**.

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
8. Selecteer **controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert. 
9. Wanneer u het bericht door gegeven validatie ziet, selecteert u **maken**. 
10. Wanneer u het bericht door gegeven validatie ziet, selecteert u maken. 

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

In deze sectie maakt u een PostgreSQL-server en voegt u hieraan een persoonlijk eind punt toe. 

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal de optie **een resource** > maken persoonlijke**netwerk** > **koppeling**.
2. Selecteer **Start**in het **persoonlijke koppelings centrum**op de optie om **een particuliere verbinding met een service te maken**.

    ![Overzicht van persoonlijke koppelingen](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. Voer in **een persoonlijk eind punt maken-basis beginselen**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Project Details** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.|
    | **Exemplaar Details** |  |
    | Naam | Voer *myPrivateEndpoint*in. Als deze naam wordt gebruikt, maakt u een unieke naam. |
    |Regio|Selecteer **Europa - west**.|
    |||
5. Selecteer **volgende: resource**.
6. Voer in **een persoonlijk eind punt maken-resource**in of Selecteer deze gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer verbinding maken met een Azure-resource in mijn Directory.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **micro soft. DBforPostgreSQL/servers**. |
    | Resource |*Mijn server* selecteren|
    |Doel-subresource |*PostgresqlServer* selecteren|
    |||
7. Selecteer **volgende: Configuratie**.
8. Voer in **een persoonlijk eind punt maken-configuratie**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**INBEL**| |
    | Virtueel netwerk| Selecteer *MyVirtualNetwork*. |
    | Subnet | Selecteer *mySubnet*. |
    |**INTEGRATIE VAN PARTICULIERE DNS**||
    |Integreren met een privé-DNS-zone |Selecteer **Ja**. |
    |Privé-DNS zone |Selecteer *(nieuw) privatelink. post gres. data base. Azure. com* |
    |||

1. Selecteer **controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert. 
2. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**. 

    ![Privé koppeling gemaakt](media/concepts-data-access-and-security-private-link/show-postgres-private-link.png)

    > [!NOTE] 
    > De FQDN in de DNS-instelling van de klant wordt niet omgezet naar het geconfigureerde particuliere IP-adres. U moet een DNS-zone voor de geconfigureerde FQDN instellen, zoals [hier](../dns/dns-operations-recordsets-portal.md)wordt weer gegeven.

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Verbinding maken met een virtuele machine met behulp van Extern bureaublad (RDP)


Nadat u **myVm**hebt gemaakt, kunt u als volgt verbinding maken met het Internet: 

1. Voer in de zoek balk van de portal *myVm*in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het *gedownloade RDP* -bestand.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **meer opties** > selecteren**een ander account gebruiken**om de referenties op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine.

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
    ```

3. Test de verbinding van de persoonlijke verbinding voor de PostgreSQL-server met behulp van elke beschik bare client. In het onderstaande voor beeld heb ik [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) gebruikt om de bewerking uit te voeren.

4. In **nieuwe verbinding**voert u de volgende gegevens in of selecteert u deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Servertype| Selecteer **postgresql**.|
    | Servernaam| *Mydemopostgresserver.privatelink.postgres.database.Azure.com* selecteren |
    | Gebruikersnaam | Voer de gebruikers username@servername naam in die wordt opgegeven tijdens het maken van de postgresql-server. |
    |Wachtwoord |Voer een wacht woord in dat u hebt opgegeven tijdens het maken van de PostgreSQL-server. |
    |SSL|Selecteer **vereist**.|
    ||

5. Selecteer Verbinden.

6. Bladeren door data bases vanuit het menu links.

7. Eventueel Gegevens van de postgreSQL-server maken of er een query op uitvoeren.

8. Sluit de verbinding met extern bureau blad met myVm.

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u klaar bent met het persoonlijke eind punt, de PostgreSQL-server en de virtuele machine, verwijdert u de resource groep en alle resources die deze bevat:

1. Voer *myResourceGroup* in het **zoekvak** boven aan de portal in en selecteer *myResourceGroup* in de zoek resultaten.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer myResourceGroup in bij **Typ de naam van de resource groep** en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze procedure maakt u een virtuele machine in een virtueel netwerk, een Azure Database for PostgreSQL-één-server en een persoonlijk eind punt voor persoonlijke toegang. U hebt verbinding gemaakt met één virtuele machine via internet en deze wordt veilig door gegeven aan de PostgreSQL-server met behulp van een persoonlijke koppeling. Zie [Wat is Azure private endpoint](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)? voor meer informatie over privé-eind punten.
