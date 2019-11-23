---
title: Quickstart - Manage Private Endpoints in Azure
description: Learn how to create a Private Endpoint using the Azure portal in this Quickstart
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2cb3c1d798e48017604d72d8a438d6a1071199ea
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419761"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Quickstart: Create a Private Endpoint using Azure portal

A Private Endpoint is the fundamental building block for private link in Azure. It enables Azure resources, like Virtual Machines (VMs), to communicate privately with private link resources. In this Quickstart, you will learn how to create a VM on an Azure Virtual Network, a  SQL Database Server with an Azure private endpoint using the Azure Portal. Then, you can securely access the SQL Database Server from the VM.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


> [!NOTE]
> Private endpoint(s) are not permitted in conjunction with service endpoints in the same subnet!

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vm"></a>Een VM maken
In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link resource (a SQL server in Azure in this example).

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken


In this section, you will create a Virtual Network and the subnet to host the VM that is used to access your Private Link resource.

1. Selecteer linksboven in het scherm **Een resource maken** > **Netwerken** > **Virtueel netwerk**.
1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Enter *MyVirtualNetwork*. |
    | Adresruimte | Voer *10.1.0.0/16* in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Locatie | Select **WestCentralUS**.|
    | Subnet - naam | Enter *mySubnet*. |
    | Subnet - adresbereik | Voer *10.1.0.0/24* in. |
    |||
1. Leave the rest as default and select **Create**.


### <a name="create-virtual-machine"></a>Virtuele machine maken

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual Machine**.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. You created this in the previous section.  |
    | **INSTANTIEDETAILS** |  |
    | Naam van de virtuele machine | Enter *myVm*. |
    | Regio | Select **WestCentralUS**. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | afbeelding | Select **Windows Server 2019 Datacenter**. |
    | Grootte | Laat de standaardwaarde **Standard DS1 v2** staan. |
    | **ADMINISTRATOR-ACCOUNT** |  |
    | Gebruikersnaam | Enter a username of your choosing. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **REGELS VOOR BINNENKOMENDE POORT** |  |
    | Openbare poorten voor inkomend verkeer | Laat de standaardwaarde **Geen** staan. |
    | **GELD BESPAREN** |  |
    | Hebt u al een Windows-licentie? | Laat de standaardwaarde **Nee** staan. |
    |||

1. Select **Next: Disks**.

1. In **Create a virtual machine - Disks**, leave the defaults and select **Next: Networking**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | Leave the default **MyVirtualNetwork**.  |
    | Adresruimte | Leave the default **10.1.0.0/24**.|
    | Subnet | Leave the default **mySubnet (10.1.0.0/24)** .|
    | Openbare IP | Handhaaf de standaardinstelling **(new) myVm-ip**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**.|
    |||


1. Selecteer **Controleren + maken**. You're taken to the **Review + create** page where Azure validates your configuration.

1. When you see the **Validation passed** message, select **Create**.

## <a name="create-a-sql-database-server"></a>Create a SQL database server
In this section, you will create a SQL database server in Azure. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Databases** > **SQL database**.

1. In **Create SQL database - Basics**, enter or select this information:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Database details** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. You created this in the previous section.|
    | **INSTANTIEDETAILS** |  |
    | Databasenaam  | Enter *mydatabase*. If this name is taken, create a unique name. |
    |||
5. In **Server**, select **Create new**. 
6. In **New server**, enter or select this information:

    | Instelling | Waarde |
    | ------- | ----- |
    |Servernaam  | Enter *myserver*. If this name is taken, create a unique name.|
    | Aanmeldgegevens van serverbeheerder| Enter an administrator name of your choosing. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. The password must be at least 8 characters long and meet the defined requirements. |
    | Locatie | Select an Azure region where you want to want your SQL Server to reside. |
    
7. Selecteer **OK**. 
8. Selecteer **Controleren + maken**. You're taken to the **Review + create** page where Azure validates your configuration. 
9. When you see the Validation passed message, select **Create**. 
10. When you see the Validation passed message, select Create. 

## <a name="create-a-private-endpoint"></a>Create a private endpoint

In this section, you will create a SQL server and add a private endpoint to it. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Networking** > **Private Link Center (Preview)** .
2. In **Private Link Center - Overview**, on the option to **Build a private connection to a service**, select **Start**.
1. In **Create a private endpoint (Preview) - Basics**, enter or select this information:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Project details** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. You created this in the previous section.|
    | **INSTANTIEDETAILS** |  |
    | Naam | Enter * myPrivateEndpoint*. If this name is taken, create a unique name. |
    |Regio|Select **WestCentralUS**.|
    |||
5. Select **Next: Resource**.
6. In **Create a private endpoint - Resource**, enter or select this information:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Select connect to an Azure resource in my directory.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Select **Microsoft.Sql/servers**. |
    | Bron |Select *myServer*|
    |Target sub-resource |Select *sqlServer*|
    |||
7. Select **Next: Configuration**.
8. In **Create a private endpoint (Preview) - Configuration**, enter or select this information:

    | Instelling | Waarde |
    | ------- | ----- |
    |**NETWORKING**| |
    | Virtueel netwerk| Select *MyVirtualNetwork*. |
    | Subnet | Select *mySubnet*. |
    |**PRIVATE DNS INTEGRATION**||
    |Integrate with private DNS zone |Selecteer **Ja**. |
    |Private DNS Zone |Select *(New)privatelink.database.windows.net* |
    |||

1. Selecteer **Controleren + maken**. You're taken to the **Review + create** page where Azure validates your configuration. 
2. When you see the **Validation passed** message, select **Create**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Connect to a VM using Remote Desktop (RDP)


After you've created **myVm*, connect to it from the internet as follows: 

1. In the portal's search bar, enter *myVm*.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. Azure maakt een Remote Desktop Protocol-bestand ( *.rdp*) en downloadt het bestand naar uw computer.

1. Open the downloaded.rdp* file.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Enter the username and password you specified when creating the VM.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Access the SQL database server privately from the VM

1. In the Remote Desktop of *myVM*, open PowerShell.

2. Enter `nslookup myserver.database.windows.net`. 

    You'll receive a message similar to this:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Install [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. In **Connect to server**, enter or select this information:

    | Instelling | Waarde |
    | ------- | ----- |
    | Servertype| Selecteer **Database Engine**.|
    | Servernaam| Select *myserver.database.windows.net* |
    | Gebruikersnaam | Enter username as username@servername which is provided during the SQL server creation. |
    |Wachtwoord |Enter a password provided during the SQL server creation. |
    |Remember password|Selecteer **Ja**.|
    |||
1. Selecteer **Verbinden**.
2. Browse databases from left menu.
3. (Optionally) Create or query information from mydatabase.
4. Close the remote desktop connection to *myVm*. 

## <a name="clean-up-resources"></a>Resources opschonen 
When you're done using the private endpoint, SQL server, and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Selecteer **Resourcegroep verwijderen**. 
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## <a name="next-steps"></a>Volgende stappen

In this quickstart, you created a VM on a virtual network, a SQL database server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the SQL database server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint?](private-endpoint-overview.md).
