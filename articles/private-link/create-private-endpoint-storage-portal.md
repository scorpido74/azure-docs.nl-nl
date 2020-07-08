---
title: Privé verbinding maken met een opslagaccount met behulp van een privé-eindpunt in Azure
description: Meer informatie over hoe u persoonlijke verbinding kunt maken met een opslag account in azure met behulp van een persoonlijk eind punt.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 1ccbb685ceb406fd7a52edf793b53d9e1c32630b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737322"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Privé verbinding maken met een opslagaccount met behulp van een privé-eindpunt in Azure
Persoonlijk Azure-eind punt is de fundamentele bouw steen voor privé-koppeling in Azure. Hierdoor kunnen Azure-resources, zoals virtuele machines (Vm's), privé communiceren met persoonlijke koppelings bronnen.

In deze Quick Start leert u hoe u een virtuele machine maakt in een virtueel Azure-netwerk, een opslag account met een persoonlijk eind punt met behulp van de Azure Portal. Daarna kunt u veilig toegang krijgen tot het opslag account via de VM.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vm"></a>Een virtuele machine maken
In deze sectie maakt u een virtueel netwerk en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron (een opslag account in dit voor beeld).

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie maakt u een virtueel netwerk en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot de bron van uw privé-koppeling.

In deze sectie moet u de volgende parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | VS - west-centraal      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een**  >  **Compute**  >  **virtuele machine**voor het berekenen van een resource maken.

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
    ||

1. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

1. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

## <a name="create-your-private-endpoint"></a>Uw persoonlijke eind punt maken
In deze sectie maakt u een privé-opslag account met behulp van een persoonlijk eind punt. 

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een bron**  >  **opslag**  >  **account**maken.

1. Voer in **opslag account maken-basis beginselen**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze in de vorige sectie gemaakt.|
    | **EXEMPLAARDETAILS** |  |
    | Naam van opslagaccount  | Voer *mystorageaccount*in. Als deze naam al wordt gebruikt, maakt u een unieke naam. |
    | Regio | Selecteer **WestCentralUS**. |
    | Prestaties| Laat de standaardwaarde **Standard** staan. |
    | Soort account | Behoud de standaard **opslag (algemeen gebruik v2)**. |
    | Replicatie | Selecteer **geo-redundante opslag met lees toegang (RA-GRS)**. |
    |||
  
3. Selecteer **volgende: netwerken**.
4. Selecteer **privé-eind punt**in **een opslag account maken: netwerken**, connectiviteits methode.
5. Selecteer in **een opslag account maken-netwerken de**optie **persoonlijk eind punt toevoegen**. 
6. Voer in **persoonlijk eind punt maken**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze in de vorige sectie gemaakt.|
    |Locatie|Selecteer **WestCentralUS**.|
    |Naam|Voer *myPrivateEndpoint* in.  |
    |Opslag subresource|Verlaat de standaard- **BLOB**. |
    | **NETWERKEN** |  |
    | Virtueel netwerk  | Selecteer *MyVirtualNetwork* in de resource groep *myResourceGroup*. |
    | Subnet | Selecteer *mySubnet*. |
    | **INTEGRATIE VAN PRIVÉ-DNS**|  |
    | Integreren met privé-DNS-zone  | Accepteer de standaard waarde **Ja**. |
    | Privé-DNS-zone  | Wijzig de standaard waarde voor **privatelink.blob.core.Windows.net (nieuw)**. |
    |||
7. Selecteer **OK**. 
8. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure. 
9. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**. 
10. Blader naar de bron van het opslag account die u zojuist hebt gemaakt.
11. Selecteer **toegangs sleutels** in het menu links.
12. Selecteer **kopiëren** op de Connection String voor Key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM *myVm* van Internet:

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

## <a name="access-storage-account-privately-from-the-vm"></a>Opslag account privé openen vanuit de VM

In deze sectie maakt u een particuliere verbinding met het opslag account met behulp van het persoonlijke eind punt.

1. Open PowerShell in het extern bureaublad van *myVM*.
2. Hier `nslookup mystorageaccount.blob.core.windows.net` wordt een bericht weer gegeven dat er ongeveer als volgt uitziet:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Installeer [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Selecteer **opslag accounts** met de rechter muisknop.
5. Selecteer **verbinding maken met een Azure-opslag**.
6. Selecteer **een Connection String gebruiken**.
7. Selecteer **Volgende**.
8. Voer de connection string in door de gegevens die eerder zijn gekopieerd te plakken.
9. Selecteer **Volgende**.
10. Selecteer **Verbinding maken**.
11. Door de BLOB-containers bladeren vanuit mystorageaccount 
12. Eventueel Maak mappen en/of upload bestanden naar *mystorageaccount*. 
13. Sluit de verbinding met extern bureau blad met *myVM*. 

Aanvullende opties voor toegang tot het opslag account:
- Microsoft Azure Storage Explorer is een zelfstandige gratis app van micro soft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux. U kunt de toepassing installeren om de inhoud van het opslag account privé te bekijken. 
 
- Het hulp programma AzCopy is een andere optie voor hoogwaardige, script bare gegevens overdracht voor Azure Storage. Gebruik AzCopy om gegevens over te brengen naar en van blob-, bestands- en tabelopslag. 


## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het persoonlijke eind punt, het opslag account en de virtuele machine, verwijdert u de resource groep en alle resources die deze bevat: 
1. Typ *myResourceGroup* in het vak **Zoeken** bovenaan de portal en selecteer *myResourceGroup* in de zoekresultaten. 
2. Selecteer **Resourcegroep verwijderen**. 
3. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM** en selecteer **Verwijderen**. 

## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u een VM gemaakt op een virtueel netwerk en een opslag account en een persoonlijk eind punt. U hebt verbinding gemaakt met één virtuele machine via internet en veilig door gegeven aan het opslag account met behulp van een persoonlijke koppeling. Zie [Wat is Azure private endpoint?](private-endpoint-overview.md)voor meer informatie over privé-eind punten.
