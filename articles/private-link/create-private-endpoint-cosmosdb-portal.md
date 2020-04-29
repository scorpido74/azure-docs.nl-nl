---
title: Verbinding maken met een Azure Cosmos-account met een persoonlijke Azure-koppeling
description: Meer informatie over hoe u veilig toegang kunt krijgen tot het Azure Cosmos-account via een virtuele machine door een persoonlijk eind punt te maken.
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78252603"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Privé verbinding maken met een Azure Cosmos-account met behulp van een persoonlijke Azure-koppeling

Persoonlijk Azure-eind punt is de fundamentele bouw steen voor privé-koppeling in Azure. Hierdoor kunnen Azure-resources, zoals virtuele machines (Vm's), privé communiceren met persoonlijke koppelings bronnen.

In dit artikel leert u hoe u een virtuele machine maakt in een virtueel Azure-netwerk en een Azure Cosmos-account met een persoonlijk eind punt met behulp van de Azure Portal. Daarna kunt u veilig toegang krijgen tot het Azure Cosmos-account via de VM.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure Portal.](https://portal.azure.com)

## <a name="create-a-vm"></a>Een virtuele machine maken

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en para meters

In deze sectie maakt u een virtueel netwerk en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron (een Azure Cosmos-account in dit voor beeld).

In deze sectie moet u de volgende para meters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup|
| **\<de naam van het virtuele netwerk>** | myVirtualNetwork         |
| **\<regio-naam>**          | VS - west-centraal     |
| **\<IPv4-adres ruimte>**   | 10.1.0.0 \ 16          |
| **\<>van subnet naam**          | mySubnet        |
| **\<>van het subnet-adres bereik** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>De virtuele machine maken

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal een**virtuele machine**voor het > **berekenen** > van **een resource maken**.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.  |
    | **EXEMPLAARDETAILS** |  |
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
    | Subnet | Behoud de standaard **mySubnet (10.1.0.0/24)**.|
    | Openbare IP | Handhaaf de standaardinstelling **(new) myVm-ip**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**.|
    ||

1. Selecteer **controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert.

1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

## <a name="create-an-azure-cosmos-account"></a>Een Azure Cosmos-account maken

Maak een [Azure Cosmos SQL API-account](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Ter vereenvoudiging kunt u het Azure Cosmos-account maken in dezelfde regio als de andere resources (dat wil zeggen ' WestCentralUS ').

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Een persoonlijk eind punt maken voor uw Azure Cosmos-account

Maak een persoonlijke koppeling voor uw Azure Cosmos-account zoals wordt beschreven in de [koppeling een persoonlijke verbinding maken met behulp van de sectie Azure Portal](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) van het gekoppelde artikel.

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM- *myVm* van het Internet:

1. Voer in de zoek balk van de portal *myVm*in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het gedownloade *RDP*-bestand.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **meer opties** > selecteren**een ander account gebruiken**om de referenties op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Het Azure Cosmos-account privé openen vanuit de VM

In deze sectie maakt u een particuliere verbinding met het Azure Cosmos-account met behulp van het persoonlijke eind punt. 

1. Als u het IP-adres en de DNS-toewijzing wilt gebruiken, *myVM*meldt u zich `c:\Windows\System32\Drivers\etc\hosts` aan bij de myVM van de virtuele machine, opent u het bestand en neemt u de DNS-informatie uit de vorige stap op in de volgende indeling:

   [Privé IP-adres] [Account eindpunt]. Documents. Azure. com

   **Hierbij**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. Installeer [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)In de Extern bureaublad van *myVM*.

1. Selecteer **Cosmos DB accounts (preview)** met de rechter muisknop.

1. Selecteer **verbinding maken met Cosmos DB**.

1. Selecteer **API**.

1. Voer de connection string in door de gegevens die eerder zijn gekopieerd te plakken.

1. Selecteer **Next**.

1. Selecteer **Verbinden**.

1. Blader door de Azure Cosmos-data bases en-containers via *mycosmosaccount*.

1. (Optioneel) Voeg nieuwe items toe aan *mycosmosaccount*.

1. Sluit de verbinding met extern bureau blad met *myVM*.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het persoonlijke eind punt, het Azure Cosmos-account en de virtuele machine, verwijdert u de resource groep en alle resources die deze bevat: 

1. Voer *myResourceGroup* in het **zoekvak** boven aan de portal in en selecteer *myResourceGroup* in de zoek resultaten.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een VM gemaakt in een virtueel netwerk, een Azure Cosmos-account en een persoonlijk eind punt. U hebt verbinding gemaakt met de virtuele machine via internet en veilig door gegeven aan het Azure Cosmos-account met behulp van een persoonlijke koppeling.

* Zie [Wat is Azure private endpoint?](private-endpoint-overview.md)voor meer informatie over privé-eind punten.

* Zie voor meer informatie over het beperken van een persoonlijk eind punt bij gebruik met Azure Cosmos DB [Azure private link met Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) artikel.
