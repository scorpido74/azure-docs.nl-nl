---
title: Privé verbinding maken met een opslagaccount met behulp van een privé-eindpunt in Azure
description: Meer informatie over hoe u privé verbinding maken met een opslagaccount in Azure met behulp van een privéeindpunt.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 111e6e2f80c3460f363c496b7b32befdca16250d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115112"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Privé verbinding maken met een opslagaccount met behulp van een privé-eindpunt in Azure
Azure Private Endpoint is de fundamentele bouwsteen voor Private Link in Azure. Hiermee kunnen Azure-resources, zoals virtuele machines (VM's), privé communiceren met Private Link-bronnen.

In deze Quickstart leert u hoe u een VM maakt op een virtueel Azure-netwerk, een opslagaccount met een privéeindpunt met behulp van de Azure-portal. Vervolgens u veilig toegang krijgen tot het opslagaccount van de VM.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vm"></a>Een virtuele machine maken
In deze sectie maakt u een virtueel netwerk en het subnet om de VM te hosten die wordt gebruikt om toegang te krijgen tot uw Private Link Resource (een opslagaccount in dit voorbeeld).

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie maakt u een virtueel netwerk en het subnet om de VM te hosten die wordt gebruikt om toegang te krijgen tot uw Private Link-bron.

In deze sectie moet u de volgende parameters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resourcegroepnaam>**  | myResourceGroup |
| **\<>van de naam van het virtuele netwerk** | myVirtualNetwork          |
| **\<regionaam>**          | VS - west-centraal      |
| **\<IPv4-adresruimte>**   | 10.1.0.0\16          |
| **\<subnetnaam>**          | mySubnet        |
| **\<subnet-adresbereik>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Virtuele machine maken

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

## <a name="create-your-private-endpoint"></a>Uw privéeindpunt maken
In deze sectie maakt u een privé-opslagaccount aan met behulp van een privéeindpunt. 

1. Selecteer linksboven in het scherm in de Azure-portal de optie > Een > **bronopslagopslagaccount** **maken**.**Storage**

1. Voer deze gegevens in **Opslagaccount maken - Basisbeginselen**in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit in de vorige sectie gemaakt.|
    | **EXEMPLAARDETAILS** |  |
    | Naam van opslagaccount  | Voer *mijn opslagaccount in*. Als deze naam wordt aangenomen, maakt u een unieke naam. |
    | Regio | Selecteer **WestCentralUS**. |
    | Prestaties| Laat de standaardwaarde **Standard** staan. |
    | Soort account | Laat de **standaardopslag (v2 voor algemeen gebruik)**. |
    | Replicatie | Selecteer **Georedundant Storage (RA-GRS) voor toegang tot toegang.** |
    |||
  
3. Selecteer **Volgende: Netwerken**.
4. Selecteer **privéeindpunt**in **Een opslagaccount maken - Netwerken**, connectiviteitsmethode .
5. Selecteer **Privéeindpunt toevoegen**in **Een opslagaccount maken - Netwerken**. 
6. Voer in **Privéeindpunt maken**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit in de vorige sectie gemaakt.|
    |Locatie|Selecteer **WestCentralUS**.|
    |Name|Voer *mijnPrivéEindpunt in*.  |
    |Subresource voor opslag|De **standaardblob verlaten**. |
    | **Networking** |  |
    | Virtueel netwerk  | Selecteer *MyVirtualNetwork* uit resourcegroep *myResourceGroup*. |
    | Subnet | Selecteer *mySubnet*. |
    | **INTEGRATIE VAN PRIVÉ-DNS**|  |
    | Integreren met private DNS-zone  | Laat de standaard **ja .** |
    | Privé-DNS-zone  | Laat de standaard **(Nieuw) privatelink.blob.core.windows.net**. |
    |||
7. Selecteer **OK**. 
8. Selecteer **Controleren + maken**. U wordt naar de **pagina Controleren + maken geleid,** waar Azure uw configuratie valideert. 
9. Wanneer u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken**. 
10. Blader naar de opslagaccountbron die u zojuist hebt gemaakt.
11. Selecteer **Toegangstoetsen** in het linkerinhoudsmenu.
12. Selecteer **Kopiëren** op de verbindingstekenreeks voor toets1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM *myVm* via internet:

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

## <a name="access-storage-account-privately-from-the-vm"></a>Privé toegang tot opslagaccount vanaf de VM

In deze sectie maakt u privé verbinding met het opslagaccount via het Privéeindpunt.

1. Open PowerShell in het extern bureaublad van *myVM.*
2. Enter `nslookup mystorageaccount.blob.core.windows.net` U ontvangt een bericht dat vergelijkbaar is met dit bericht:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Installeer [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Selecteer **Opslagaccounts** met de rechtermuisknop.
5. Selecteer **Verbinding maken met een azure-opslag**.
6. Selecteer **Een verbindingstekenreeks gebruiken**.
7. Selecteer **Volgende**.
8. Voer de verbindingstekenreeks in door de eerder gekopieerde gegevens te plakken.
9. Selecteer **Volgende**.
10. Selecteer **Verbinden**.
11. Blader door de Blob-containers vanaf mijn opslagaccount 
12. (Optioneel) Mappen maken en/of bestanden uploaden naar *mystorageaccount*. 
13. Sluit de verbinding met extern bureaublad met *myVM*. 

Aanvullende opties voor toegang tot het opslagaccount:
- Microsoft Azure Storage Explorer is een zelfstandige gratis app van Microsoft waarmee u visueel werken met Azure-opslaggegevens op Windows, macOS en Linux. U de toepassing installeren om privé door de inhoud van het opslagaccount te bladeren. 
 
- Het AzCopy-hulpprogramma is een andere optie voor krachtige scriptbare gegevensoverdracht voor Azure-opslag. Gebruik AzCopy om gegevens over te brengen naar en van blob-, bestands- en tabelopslag. 


## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het privéeindpunt, het opslagaccount en de vm, verwijdert u de brongroep en alle bronnen die deze bevat: 
1. Typ *myResourceGroup* in het vak **Zoeken** boven aan de portal en selecteer *myResourceGroup* in de zoekresultaten. 
2. Selecteer **Resourcegroep verwijderen**. 
3. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM** en selecteer **Verwijderen**. 

## <a name="next-steps"></a>Volgende stappen
In deze Quickstart hebt u een VM gemaakt op een virtueel netwerk- en opslagaccount en een privéeindpunt. U hebt vanaf het internet verbinding gemaakt met één VM en veilig gecommuniceerd met het opslagaccount via Private Link. Zie [Wat is Azure Private Endpoint voor](private-endpoint-overview.md)meer informatie over privéeindpunt?
