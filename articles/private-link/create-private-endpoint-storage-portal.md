---
title: Privé verbinding maken met een opslag account met behulp van een persoonlijk Azure-eind punt
description: Meer informatie over hoe u persoonlijke verbinding kunt maken met een opslag account in azure met behulp van een persoonlijk eind punt.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: bb1913d77616869c889c464a41e8166b3a88b03c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028865"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Privé verbinding maken met een opslag account met behulp van een persoonlijk Azure-eind punt
Persoonlijk Azure-eind punt is de fundamentele bouw steen voor privé-koppeling in Azure. Hierdoor kunnen Azure-resources, zoals virtuele machines (Vm's), privé communiceren met persoonlijke koppelings bronnen.

In deze Quick Start leert u hoe u een virtuele machine maakt in een virtueel Azure-netwerk, een opslag account met een persoonlijk eind punt met behulp van de Azure Portal. Daarna kunt u veilig toegang krijgen tot het opslag account via de VM.

> [!NOTE]
> Een of meer persoonlijke eind punten zijn niet toegestaan in combi natie met Service-eind punten in hetzelfde subnet.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vm"></a>Een VM maken
In deze sectie maakt u een virtueel netwerk en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron (een opslag account in dit voor beeld).

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot de bron van uw privé-koppeling.

1. Selecteer linksboven in het scherm **Een resource maken** > **Netwerken** > **Virtueel netwerk**.
1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *MyVirtualNetwork*in. |
    | Adresruimte | Voer *10.1.0.0/16* in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Locatie | Selecteer **WestCentralUS**.|
    | Subnet - Naam | Voer *mySubnet*in. |
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
    | **EXEMPLAARDETAILS** |  |
    | Naam van de virtuele machine | Voer *myVm*in. |
    | Regio | Selecteer **WestCentralUS**. |
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
    ||

1. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert.

1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

## <a name="create-your-private-endpoint"></a>Uw persoonlijke eind punt maken
In deze sectie maakt u een privé-opslag account met behulp van een persoonlijk eind punt. 

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een resource maken** > **opslag** > **opslag account**.

1. Voer in **opslag account maken-basis beginselen**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.|
    | **EXEMPLAARDETAILS** |  |
    | Naam van opslagaccount  | Voer *mystorageaccount*in. Als deze naam wordt gebruikt, maakt u een unieke naam. |
    | Regio | Selecteer **WestCentralUS**. |
    | Prestaties| Laat de standaardwaarde **Standard** staan. |
    | Soort account | Behoud de standaard **opslag (algemeen gebruik v2)** . |
    | Replicatie | Selecteer **geo-redundante opslag met lees toegang (RA-GRS)** . |
    |||
  
3. Selecteer **volgende: netwerken**.
4. Selecteer **privé-eind punt**in **een opslag account maken: netwerken**, connectiviteits methode.
5. Selecteer in **een opslag account maken-netwerken de**optie **persoonlijk eind punt toevoegen**. 
6. Voer in **persoonlijk eind punt maken**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.|
    |Locatie|Selecteer **WestCentralUS**.|
    |Name|Voer *myPrivateEndpoint*in.  |
    |Opslag subresource|Verlaat de standaard- **BLOB**. |
    | **INBEL** |  |
    | Virtueel netwerk  | Selecteer *MyVirtualNetwork* in de resource groep *myResourceGroup*. |
    | Subnet | Selecteer *mySubnet*. |
    | **INTEGRATIE VAN PARTICULIERE DNS**|  |
    | Integreren met een privé-DNS-zone  | Accepteer de standaard waarde **Ja**. |
    | Privé-DNS-zone  | Wijzig de standaard waarde voor **privatelink.blob.core.Windows.net (nieuw)** . |
    |||
7. Selecteer **OK**. 
8. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert. 
9. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**. 
10. Blader naar de bron van het opslag account die u zojuist hebt gemaakt.
11. Selecteer **toegangs sleutels** in het menu links.
12. Selecteer **kopiëren** op de Connection String voor Key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM- *myVm* van het Internet:

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

## <a name="access-storage-account-privately-from-the-vm"></a>Opslag account privé openen vanuit de VM

In deze sectie maakt u een particuliere verbinding met het opslag account met behulp van het persoonlijke eind punt.

1. Open Power shell in de Extern bureaublad van *myVM*.
2. Voer `nslookup mystorageaccount.blob.core.windows.net` u een bericht ontvangt dat er ongeveer als volgt uitziet:
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
7. Selecteer **Next**.
8. Voer de connection string in door de gegevens die eerder zijn gekopieerd te plakken.
9. Selecteer **Next**.
10. Selecteer **Verbinden**.
11. Door de BLOB-containers bladeren vanuit mystorageaccount 
12. Eventueel Maak mappen en/of upload bestanden naar *mystorageaccount*. 
13. Sluit de verbinding met extern bureau blad met *myVM*. 

Aanvullende opties voor toegang tot het opslag account:
- Microsoft Azure Storage Explorer is een zelfstandige gratis app van micro soft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux. U kunt de toepassing installeren om de inhoud van het opslag account privé te bekijken. 
 
- Het hulp programma AzCopy is een andere optie voor hoogwaardige, script bare gegevens overdracht voor Azure Storage. Gebruik AzCopy om gegevens over te brengen naar en van blob-, bestands- en tabelopslag. 


## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het persoonlijke eind punt, het opslag account en de virtuele machine, verwijdert u de resource groep en alle resources die deze bevat: 
1. Voer *myResourceGroup* in het **zoekvak** boven aan de portal in en selecteer *myResourceGroup* in de zoek resultaten. 
2. Selecteer **Resourcegroep verwijderen**. 
3. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM** en selecteer **Verwijderen**. 

## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u een VM gemaakt op een virtueel netwerk en een opslag account en een persoonlijk eind punt. U hebt verbinding gemaakt met één virtuele machine via internet en veilig door gegeven aan het opslag account met behulp van een persoonlijke koppeling. Zie [Wat is Azure private endpoint?](private-endpoint-overview.md)voor meer informatie over privé-eind punten.
