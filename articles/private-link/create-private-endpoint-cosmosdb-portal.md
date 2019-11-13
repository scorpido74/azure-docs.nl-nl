---
title: Verbinding maken met een Azure Cosmos-account met een persoonlijke Azure-koppeling
description: Meer informatie over hoe u veilig toegang kunt krijgen tot het Azure Cosmos-account via een virtuele machine door een persoonlijk eind punt te maken.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sngun
ms.openlocfilehash: 32595e561736b5f22f109d0caff1f3990300d2bc
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007333"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Privé verbinding maken met een Azure Cosmos-account met behulp van een persoonlijke Azure-koppeling

Persoonlijk Azure-eind punt is de fundamentele bouw steen voor privé-koppeling in Azure. Hierdoor kunnen Azure-resources, zoals virtuele machines (Vm's), privé communiceren met persoonlijke koppelings bronnen.

In dit artikel leert u hoe u een virtuele machine maakt in een virtueel Azure-netwerk en een Azure Cosmos-account met een persoonlijk eind punt met behulp van de Azure Portal. Daarna kunt u veilig toegang krijgen tot het Azure Cosmos-account via de VM.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure Portal.](https://portal.azure.com)

## <a name="create-a-vm"></a>Een virtuele machine maken

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron (een Azure Cosmos-account in dit voor beeld).

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

### <a name="create-the-virtual-machine"></a>De virtuele machine maken

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
    | Wachtwoord | Voer een wacht woord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wacht woord opnieuw in. |
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
    ||

1. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert.

1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

## <a name="create-an-azure-cosmos-account"></a>Een Azure Cosmos-account maken

Maak een [Azure Cosmos SQL API-account](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Ter vereenvoudiging kunt u het Azure Cosmos-account maken in dezelfde regio als de andere resources (dat wil zeggen ' WestCentralUS ').

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Een persoonlijk eind punt maken voor uw Azure Cosmos-account

Maak een persoonlijke koppeling voor uw Azure Cosmos-account zoals wordt beschreven in de [koppeling een persoonlijke verbinding maken met behulp van de sectie Azure Portal](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) van het gekoppelde artikel.

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM- *myVm* van het Internet:

1. Voer in de zoek balk van de portal *myVm*in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. Azure maakt een Remote Desktop Protocol-bestand ( *.rdp*) en downloadt het bestand naar uw computer.

1. Open het gedownloade *RDP*-bestand.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Het Azure Cosmos-account privé openen vanuit de VM

In deze sectie maakt u een particuliere verbinding met het Azure Cosmos-account met behulp van het persoonlijke eind punt. 

> [!IMPORTANT]
> De DNS-configuratie voor het Azure Cosmos-account moet hand matig worden aangepast aan het hosts-bestand om de FQDN van het specifieke account op te kunnen bevatten. In productie scenario's configureert u de DNS-server voor het gebruik van de privé-IP-adressen. Voor het demo doel kunt u echter beheerders machtigingen gebruiken op de VM en het `c:\Windows\System32\Drivers\etc\hosts` bestand (in Windows) of `/etc/hosts` bestand (op Linux) zodanig wijzigen dat het IP-adres en DNS-toewijzing worden meegenomen.

1. Als u het IP-adres en de DNS-toewijzing wilt gebruiken, meldt u zich aan bij de *myVM*van de virtuele machine, opent u het `c:\Windows\System32\Drivers\etc\hosts` bestand en neemt u de DNS-informatie uit de vorige stap op in de volgende indeling:

   [Privé IP-adres] [Account eindpunt]. Documents. Azure. com

   **Voorbeeld:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. Installeer [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)In de Extern bureaublad van *myVM*.

1. Selecteer **Cosmos DB accounts (preview)** met de rechter muisknop.

1. Selecteer **verbinding maken met Cosmos DB**.

1. Selecteer **API**.

1. Voer de connection string in door de gegevens die eerder zijn gekopieerd te plakken.

1. Selecteer **Volgende**.

1. Selecteer **Verbinden**.

1. Blader door de Azure Cosmos-data bases en-containers via *mycosmosaccount*.

1. (Optioneel) Voeg nieuwe items toe aan *mycosmosaccount*.

1. Sluit de verbinding met extern bureau blad met *myVM*.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het persoonlijke eind punt, het Azure Cosmos-account en de virtuele machine, verwijdert u de resource groep en alle resources die deze bevat: 

1. Geef *myResourceGroup* op in het **zoekvak** boven aan de portal en selecteer *myResourceGroup* in de zoek resultaten.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer *myResourceGroup* in om **de naam van de resource groep te typen** en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een VM gemaakt in een virtueel netwerk, een Azure Cosmos-account en een persoonlijk eind punt. U hebt verbinding gemaakt met de virtuele machine via internet en veilig door gegeven aan het Azure Cosmos-account met behulp van een persoonlijke koppeling.

* Zie [Wat is Azure private endpoint?](private-endpoint-overview.md)voor meer informatie over privé-eind punten.

* Zie voor meer informatie over het beperken van een persoonlijk eind punt bij gebruik met Azure Cosmos DB [Azure private link met Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) artikel.