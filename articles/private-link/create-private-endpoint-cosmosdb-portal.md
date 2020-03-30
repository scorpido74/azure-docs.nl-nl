---
title: Verbinding maken met een Azure Cosmos-account met Azure Private Link
description: Meer informatie over hoe u het Azure Cosmos-account veilig openen vanaf een VM door een privéeindpunt te maken.
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252603"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Privé verbinding maken met een Azure Cosmos-account met Azure Private Link

Azure Private Endpoint is de fundamentele bouwsteen voor Private Link in Azure. Hiermee kunnen Azure-resources, zoals virtuele machines (VM's), privé communiceren met Private Link-bronnen.

In dit artikel leert u hoe u een VM maakt op een virtueel Azure-netwerk en een Azure Cosmos-account met een privéeindpunt met behulp van de Azure-portal. Vervolgens u veilig toegang krijgen tot het Azure Cosmos-account van de VM.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal.](https://portal.azure.com)

## <a name="create-a-vm"></a>Een virtuele machine maken

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie maakt u een virtueel netwerk en het subnet om de VM te hosten die wordt gebruikt om toegang te krijgen tot uw Private Link-bron (een Azure Cosmos-account in dit voorbeeld).

In deze sectie moet u de volgende parameters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resourcegroepnaam>**  | myResourceGroup|
| **\<>van de naam van het virtuele netwerk** | myVirtualNetwork         |
| **\<regionaam>**          | VS - west-centraal     |
| **\<IPv4-adresruimte>**   | 10.1.0.0\16          |
| **\<subnetnaam>**          | mySubnet        |
| **\<subnet-adresbereik>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>De virtuele machine maken

1. Selecteer linksboven in het scherm in de Azure-portal de optie **Een resource** > **Compute** > **Virtual-machine maken**.

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
    ||

1. Selecteer **Controleren + maken**. U wordt naar de **pagina Controleren + maken geleid,** waar Azure uw configuratie valideert.

1. Wanneer u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken**.

## <a name="create-an-azure-cosmos-account"></a>Een Azure Cosmos-account maken

Maak een [Azure Cosmos SQL API-account](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Voor de eenvoud u het Azure Cosmos-account maken in dezelfde regio als de andere bronnen (dat is 'WestCentralUS').

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Een privéeindpunt maken voor uw Azure Cosmos-account

Maak een privékoppeling voor uw Azure Cosmos-account zoals beschreven in de privékoppeling maken met behulp van het gedeelte [Azure-portal](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) van het gekoppelde artikel.

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM *myVm* via internet:

1. Voer *myVm*in in de zoekbalk van het portaal.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het gedownloade *RDP*-bestand.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikersnaam en het wachtwoord in dat u hebt opgegeven bij het maken van de vm.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > selecteren**Gebruik een ander account**om de referenties op te geven die u hebt ingevoerd toen u de vm hebt gemaakt.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Privé toegang krijgen tot het Azure Cosmos-account vanaf de VM

In deze sectie maakt u privé verbinding met het Azure Cosmos-account met behulp van het privéeindpunt. 

1. Als u het IP-adres en de DNS-toewijzing wilt `c:\Windows\System32\Drivers\etc\hosts` opnemen, meldt u zich aan bij *myVM,* opent u het bestand en neemt u de DNS-informatie van vorige stap op in de volgende indeling:

   [Privé-IP-adres] [Accounteindpunt].documents.azure.com

   **Voorbeeld:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. Installeer [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)in het extern bureaublad van *myVM*.

1. Selecteer **Cosmos DB-accounts (Voorbeeld)** met de rechtermuisknop.

1. Selecteer **Verbinding maken met Cosmos DB**.

1. Selecteer **API**.

1. Voer de verbindingstekenreeks in door de eerder gekopieerde gegevens te plakken.

1. Selecteer **Volgende**.

1. Selecteer **Verbinden**.

1. Blader door de Azure Cosmos-databases en -containers vanuit *mijncosmosaccount*.

1. (Optioneel) nieuwe items toevoegen aan *mycosmosaccount*.

1. Sluit de verbinding met extern bureaublad met *myVM*.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het privéeindpunt, het Azure Cosmos-account en de VM, verwijdert u de brongroep en alle bronnen die deze bevat: 

1. Typ *myResourceGroup* in het vak **Zoeken** boven aan de portal en selecteer *myResourceGroup* in de zoekresultaten.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een VM gemaakt voor een virtueel netwerk, een Azure Cosmos-account en een privéeindpunt. U hebt vanaf internet verbinding gemaakt met de VM en veilig gecommuniceerd met het Azure Cosmos-account via Private Link.

* Zie [Wat is Azure Private Endpoint voor](private-endpoint-overview.md)meer informatie over privéeindpunt?

* Zie Azure [Private Link with Azure Cosmos DB-artikel](../cosmos-db/how-to-configure-private-endpoints.md) voor meer informatie over beperking van privéeindpunt bij gebruik met Azure Cosmos DB.
