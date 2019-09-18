---
title: Privé verbinding maken met een opslag account met behulp van een persoonlijk Azure-eind punt
description: Meer informatie over hoe u persoonlijke verbinding kunt maken met een opslag account in azure met behulp van een persoonlijk eind punt.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 93ca3c8c4b7a4da0387d1302b301cf677b689281
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71074979"
---
# <a name="connect-privately-to-a-storage-account-using-an-azure-private-endpoint"></a>Privé verbinding maken met een opslag account met behulp van een persoonlijk Azure-eind punt
Persoonlijk Azure-eind punt is de fundamentele bouw steen voor privé-koppeling in Azure. Hierdoor kunnen Azure-resources, zoals virtuele machines (Vm's), privé communiceren met persoonlijke koppelings bronnen.

In deze Quick Start leert u hoe u een virtuele machine maakt in een virtueel Azure-netwerk, een opslag account met een persoonlijk eind punt met behulp van de Azure Portal. Daarna kunt u veilig toegang krijgen tot het opslag account via de VM.


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vm"></a>Een virtuele machine maken
In deze sectie maakt u een virtueel netwerk en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron (een opslag account in dit voor beeld).

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot de bron van uw privé-koppeling.

1. Selecteer linksboven in het scherm **Een resource maken** > **Netwerken** > **Virtueel netwerk**.
1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *MyVirtualNetwork*in. |
    | Adresruimte | Voer *10.1.0.0/16* in. |
    | Subscription | Selecteer uw abonnement.|
    | Resource group | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Location | Selecteer **WestCentralUS**.|
    | Subnet - Naam | Voer *mySubnet*in. |
    | Subnet - adresbereik | Voer *10.1.0.0/24* in. |
    |||
1. Laat de rest als standaard en selecteer **maken**.


### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal een**virtuele machine**voor het**berekenen** > van **een resource** > maken.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Value |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Subscription | Selecteer uw abonnement. |
    | Resource group | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.  |
    | **EXEMPLAARDETAILS** |  |
    | Naam van de virtuele machine | Voer *myVm*in. |
    | Regio | Selecteer **WestCentralUS**. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | Image | Selecteer **Windows Server 2019 Data Center**. |
    | Size | Laat de standaardwaarde **Standard DS1 v2** staan. |
    | **ADMINISTRATOR-ACCOUNT** |  |
    | Gebruikersnaam | Voer een gebruikers naam van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **REGELS VOOR BINNENKOMENDE POORT** |  |
    | Openbare poorten voor inkomend verkeer | Laat de standaardwaarde **Geen** staan. |
    | **GELD BESPAREN** |  |
    | Hebt u al een Windows-licentie? | Laat de standaardwaarde **Nee** staan. |
    |||

1. Selecteer **Volgende: Schijven**.

1. In **een virtuele machine maken-schijven**, de standaard instellingen behouden en **volgende selecteren: Netwerken**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | De standaard **MyVirtualNetwork**behouden.  |
    | Adresruimte | De standaard **10.1.0.0/24**behouden.|
    | Subnet | Behoud de standaard **mySubnet (10.1.0.0/24)** .|
    | Openbaar IP | Handhaaf de standaardinstelling **(new) myVm-ip**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**.|
    ||

1. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert.

1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

## <a name="create-your-private-endpoint"></a>Uw persoonlijke eind punt maken
In deze sectie maakt u een privé-opslag account met behulp van een persoonlijk eind punt. 

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een bron** > **opslag** > **account**maken.

1. Voer in **opslag account maken-basis beginselen**de volgende gegevens in of Selecteer deze:

    | Instelling | Value |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Subscription | Selecteer uw abonnement. |
    | Resource group | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.|
    | **EXEMPLAARDETAILS** |  |
    | Naam van opslagaccount  | Voer *mystorageaccount*in. Als deze naam wordt gebruikt, maakt u een unieke naam. |
    | Regio | Selecteer **WestCentralUS**. |
    | Prestaties| Laat de standaardwaarde **Standard** staan. |
    | Soort account | Behoud de standaard **opslag (algemeen gebruik v2)** . |
    | Replicatie | Selecteer **geo-redundante opslag met lees toegang (RA-GRS)** . |
    |||
  
3. Selecteer **volgende: Netwerken**.
4. Selecteer **privé-eind punt**in **een opslag account maken: netwerken**, connectiviteits methode.
5. Selecteer in **een opslag account maken-netwerken de**optie **persoonlijk eind punt toevoegen**. 
6. Voer in **persoonlijk eind punt maken**de volgende gegevens in of Selecteer deze:

    | Instelling | Value |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Subscription | Selecteer uw abonnement. |
    | Resource group | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.|
    |Location|Selecteer **WestCentralUS**.|
    |Name|Voer *myPrivateEndpoint*in.  |
    |Opslag subresource|Verlaat de standaard- **BLOB**. |
    | **INBEL** |  |
    | Virtueel netwerk  | Selecteer *MyVirtualNetwork* in de resource groep *myResourceGroup*. |
    | Subnet | Selecteer *mySubnet*. |
    | **INTEGRATIE VAN PARTICULIERE DNS**|  |
    | Integreren met een privé-DNS-zone  | Accepteer de standaard waarde **Ja**. |
    | Privé-DNS zone  | Wijzig de standaard waarde  ** voor privatelink.blob.core.Windows.net (nieuw)** . |
    |||
7. Selecteer **OK**. 
8. Selecteer **controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert. 
9. Wanneer u het bericht **door gegeven** validatie ziet, selecteert u **maken**. 
10. Blader naar de bron van het opslag account die u zojuist hebt gemaakt.
11. Selecteer **toegangs sleutels** in het menu links.
12. Selecteer **kopiëren** op de Connection String voor Key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM- *myVm* van het Internet:

1. Voer in de zoek balk van de portal *myVm*in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het bestand gedownloade. rdp *.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-storage-account-privately-from-the-vm"></a>Opslag account privé openen vanuit de VM

In deze sectie maakt u een particuliere verbinding met het opslag account met behulp van het persoonlijke eind punt.

> [!IMPORTANT]
> De DNS-configuratie voor opslag moet hand matig worden aangepast aan het hosts-bestand om de FQDN van het specifieke account op te slaan. Wijzig het volgende bestand met behulp van beheerders machtigingen voor Windows: c:\Windows\System32\Drivers\etc\hosts of Linux bestand/etc/hosts Neem de DNS-gegevens voor het account uit de vorige stap op in de volgende indeling [privé IP-adres] myaccount.blob.core.windows.net

1. Open Power shell in de Extern bureaublad van *myVM*.
2. Hier `nslookup mystorageaccount.blob.core.windows.net` wordt een bericht weer gegeven dat er ongeveer als volgt uitziet:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
