---
title: Toegang tot PaaS-resources beperken-zelf studie-Azure Portal
description: In deze zelfstudie leert u hoe u de toegang tot Azure resources, zoals Azure Storage en Azure SQL Database, met service-eindpunten voor een virtueel netwerk kunt begrenzen en beperken met behulp van de Azure-portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/23/2018
ms.author: kumud
ms.openlocfilehash: 85fc5687b82947ed16bde0c30ca2b947514ba958
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186375"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Zelfstudie: Netwerktoegang tot PaaS-resources beperken met service-eindpunten voor een virtueel netwerk met behulp van de Azure-portal

Met service-eindpunten voor virtuele netwerken kunt u de netwerktoegang tot sommige Azure-servicebronnen beperken tot een subnet van een virtueel netwerk. U kunt ook internettoegang tot de resources verwijderen. Service-eindpunten zorgen voor een rechtstreekse verbinding van uw virtuele netwerk met ondersteunde Azure-services, zodat u de privéadresruimte van uw virtuele netwerk kunt gebruiken voor toegang tot de Azure-services. Verkeer dat bestemd is voor Azure-resources via de service-eindpunten blijft altijd op het Microsoft Azure-backbone-netwerk. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk maken met één subnet
> * Een subnet toevoegen en een service-eindpunt inschakelen
> * Een Azure-resource maken en alleen toegang ertoe toestaan vanaf een subnet
> * Een virtuele machine (VM) implementeren op elk subnet
> * Toegang tot een resource vanaf een subnet bevestigen
> * Bevestigen dat toegang wordt geweigerd aan een resource vanaf een subnet en internet

U kunt deze zelfstudie desgewenst volgen met behulp van de [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) of [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Netwerken** en selecteer vervolgens **Virtueel netwerk**.
3. Voer de volgende gegevens in of selecteer deze, en selecteer **Maken**:

   |Instelling|Waarde|
   |----|----|
   |Naam| myVirtualNetwork |
   |Adresruimte| 10.0.0.0/16|
   |Abonnement| Selecteer uw abonnement|
   |Resourcegroep | Selecteer **Nieuwe maken** en voer *myResourceGroup* in.|
   |Locatie| Selecteer **US - oost** |
   |Subnetnaam| Public|
   |Subnetadresbereik| 10.0.0.0/24|
   |DDoS-bescherming| Basic|
   |Service-eindpunten| Uitgeschakeld|
   |Firewall| Uitgeschakeld|

   ![Basisinformatie over uw virtuele netwerk invoeren](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

## <a name="enable-a-service-endpoint"></a>Een service-eindpunt inschakelen

Service-eindpunten worden ingeschakeld per service, per subnet. Maak een subnet en schakel een service-eindpunt voor het subnet in.

1. Voer in het vak **Resources, services en documenten zoeken** bovenaan de portal *myVirtualNetwork* in. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit.
2. Voeg een subnet toe aan het virtuele netwerk. Selecteer **Subnetten** onder **INSTELLINGEN** en selecteer vervolgens **+ Subnet**, zoals in de volgende afbeelding:

    ![Subnet toevoegen](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Selecteer onder **Subnet toevoegen** de volgende informatie of voer deze in, en selecteer **OK**:

    |Instelling|Waarde|
    |----|----|
    |Naam| Privé |
    |Adresbereik| 10.0.1.0/24|
    |Service-eindpunten| Selecteer **Microsoft.Storage** onder **Services**|

> [!CAUTION]
> Zie [Subnetinstellingen wijzigen](virtual-network-manage-subnet.md#change-subnet-settings) voordat u een servicepunt voor een bestaand subnet met resources inschakelt.

## <a name="restrict-network-access-for-a-subnet"></a>Netwerktoegang voor een subnet beperken

Standaard kunnen alle VM's in een subnet met alle resources communiceren. U kunt de communicatie naar en van alle bronnen in een subnet beperken door een netwerkbeveiligingsgroep te maken en deze aan het subnet te koppelen.

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Netwerken** en selecteer **Netwerkbeveiligingsgroep**.
3. Voer onder **Een netwerkbeveiligingsgroep maken** de volgende informatie in of selecteer deze, en selecteer**Maken**:

    |Instelling|Waarde|
    |----|----|
    |Naam| myNsgPrivate |
    |Abonnement| Selecteer uw abonnement|
    |Resourcegroep | Selecteer **Bestaande gebruiken** en selecteer *myResourceGroup*.|
    |Locatie| Selecteer **US - oost** |

4. Nadat de netwerkbeveiligingsgroep is gemaakt, voert u *myNsgPrivate* in het vak **Resources, services en documenten zoeken** bovenaan de portal in. Wanneer **myNsgPrivate** wordt weergegeven in de zoekresultaten, selecteert u dit.
5. Selecteer onder **INSTELLINGEN** **Uitgaande beveiligingsregels**.
6. Selecteer **+ Toevoegen**.
7. Maak een regel die uitgaande communicatie naar de Azure Storage-service toestaat. Voer de volgende gegevens in of selecteer deze, en selecteer **Toevoegen**:

    |Instelling|Waarde|
    |----|----|
    |Bron| Selecteer **VirtualNetwork** |
    |Poortbereiken van bron| * |
    |Doel | Selecteer **Servicetag**|
    |Doelservicetag | Selecteer **Storage**|
    |Poortbereiken van doel| * |
    |Protocol|Alle|
    |Actie|Toestaan|
    |Prioriteit|100|
    |Naam|Allow-Storage-All|

8. Maak een uitgaande beveiligingsregel die communicatie naar internet weigert. Deze regel overschrijft een standaardregel in alle netwerkbeveiligingsgroepen waarmee uitgaande internetcommunicatie mogelijk is. Voer stap 5-7 opnieuw uit, met behulp van de volgend waarden:

    |Instelling|Waarde|
    |----|----|
    |Bron| Selecteer **VirtualNetwork** |
    |Poortbereiken van bron| * |
    |Doel | Selecteer **Servicetag**|
    |Doelservicetag| Selecteer **Internet**|
    |Poortbereiken van doel| * |
    |Protocol|Alle|
    |Actie|Weigeren|
    |Prioriteit|110|
    |Naam|Deny-Internet-All|

9. Selecteer onder **INSTELLINGEN** **Inkomende beveiligingsregels**.
10. Selecteer **+ Toevoegen**.
11. Maak een inkomende beveiligingsregel waarmee RDP-verkeer (Remote Desktop Protocol) naar het subnet vanaf elke locatie wordt toegestaan. De regel overschrijft een standaardbeveiligingsregel waardoor al het inkomende verkeer van internet wordt geweigerd. Externe bureaublad-verbindingen worden toegestaan voor het subnet zodat de verbinding in een later stadium kan worden getest. Selecteer onder **INSTELLINGEN** de optie **Inkomende beveiligingsregels**, selecteer **+ Toevoegen**, voer de volgende waarden in en selecteer vervolgens **Toevoegen**:

    |Instelling|Waarde|
    |----|----|
    |Bron| Alle |
    |Poortbereiken van bron| * |
    |Doel | Selecteer **VirtualNetwork**|
    |Poortbereiken van doel| 3389 |
    |Protocol|Alle|
    |Actie|Toestaan|
    |Prioriteit|120|
    |Naam|Allow-RDP-All|

12. Selecteer onder **INSTELLINGEN** **Subnetten**.
13. Selecteer **+ Koppelen**
14. Selecteer **Virtueel netwerk** onder **Subnet koppelen** en selecteer vervolgens **myVirtualNetwork** onder **Een virtueel netwerk kiezen**.
15. Selecteer **Privé** onder **Subnet kiezen** en selecteer **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Netwerktoegang tot een resource beperken

De stappen die nodig zijn om netwerktoegang te beperken tot resources die zijn gemaakt met Azure-services waarvoor service-eindpunten zijn ingeschakeld, verschillen per service. Zie de documentatie voor afzonderlijke services voor specifieke stappen voor elke service. De rest van deze zelfstudie bevat stappen voor het beperken van netwerktoegang voor een Azure Storage-account, als voorbeeld.

### <a name="create-a-storage-account"></a>Een opslagaccount maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Storage** en selecteer vervolgens **Opslagaccount - blob, bestand, tabel, wachtrij**.
3. Voer de volgende informatie in of selecteer deze, accepteer voor het overige de standaardwaarden en selecteer **Maken**:

    |Instelling|Waarde|
    |----|----|
    |Naam| Voer een naam die uniek is voor alle Azure locaties, 3 tot 24 tekens lang is en alleen cijfers en kleine letters bevat.|
    |Soort account|StorageV2 (general purpose v2)|
    |Locatie| Selecteer **US - oost** |
    |Replicatie| Lokaal redundante opslag (LRS)|
    |Abonnement| Selecteer uw abonnement|
    |Resourcegroep | Selecteer **Bestaande gebruiken** en selecteer *myResourceGroup*.|

### <a name="create-a-file-share-in-the-storage-account"></a>Een bestandsshare maken in het opslagaccount

1. Nadat het opslagaccount is gemaakt, voert u de naam van het opslagaccount in het vak **Resources, services en documenten zoeken** bovenaan de portal in. Wanneer de naam van uw opslagaccount wordt weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer **Bestanden**, zoals weergegeven in de volgende afbeelding:

   ![Storage-account](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Selecteer  **+ bestandsshare**.
4. Voer *mijn-bestandsshare* in onder **Naam** en selecteer **OK**.
5. Sluit het vak **Bestandsservice**.

### <a name="restrict-network-access-to-a-subnet"></a>Netwerktoegang tot een subnet beperken

Standaard accepteren opslagaccounts netwerkverbindingen van clients in ieder netwerk, inclusief internet. Weiger netwerktoegang van internet en alle andere subnetten in alle virtuele netwerken, behalve het *Privé*-subnet in het virtuele netwerk *myVirtualNetwork*.

1. Selecteer onder **INSTELLINGEN** voor het opslagaccount **Firewalls en virtuele netwerken**.
2. Selecteer **Geselecteerde netwerken**.
3. Selecteer **+Bestaand virtueel netwerk toevoegen**.
4. Selecteer onder **Netwerken toevoegen** de volgende waarden en selecteer **Toevoegen**:

    |Instelling|Waarde|
    |----|----|
    |Abonnement| Selecteer uw abonnement.|
    |Virtuele netwerken|Selecteer **myVirtualNetwork** onder **Virtuele netwerken**|
    |Subnetten| Selecteer **Privé** onder **Subnetten**|

    ![Firewalls en virtuele netwerken](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png)

5. Selecteer **Opslaan**.
6. Sluit het vak **Firewalls en virtuele netwerken**.
7. Kies onder **INSTELLINGEN**  voor het opslagaccount **Toegangssleutels**, zoals in de volgende afbeelding:

      ![Firewalls en virtuele netwerken](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Noteer de **Sleutel**-waarde, want u moet deze in een latere stap handmatig invoeren bij het toewijzen van de bestandsshare aan een stationsletter in een VM.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Implementeer een VM in elk subnet om de netwerktoegang tot een opslagaccount te testen.

### <a name="create-the-first-virtual-machine"></a>De eerste virtuele machine maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Compute** en vervolgens **Windows Server 2016 Datacenter**.
3. Voer de volgende gegevens in of selecteer deze, en selecteer **OK**:

   |Instelling|Waarde|
   |----|----|
   |Naam| myVmPublic|
   |Gebruikersnaam|Voer een gebruikersnaam naar keuze in.|
   |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Abonnement| Selecteer uw abonnement.|
   |Resourcegroep| Selecteer **Bestaande gebruiken** en selecteer **myResourceGroup**.|
   |Locatie| Selecteer **US - oost**.|

   ![Basisinformatie invoeren over een virtuele machine](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Selecteer een grootte voor de virtuele machine en selecteer **Selecteren**.
5. Onder **instellingen** selecteert u **Netwerk** en vervolgens **myVirtualNetwork**. Selecteer vervolgens **Subnet** en selecteer **Openbaar**, zoals in de volgende afbeelding:

   ![Een virtueel netwerk selecteren](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)

6. Onder **Netwerkbeveiligingsgroep** selecteert u **Geavanceerd**. De portal maakt automatisch een netwerkbeveiligingsgroep voor u waarin poort 3389 is toegestaan, die u later moeten openen om verbinding te maken met de virtuele machine. Selecteer **OK** op de pagina **Instellingen**.
7. Selecteer **OK** op de pagina **Overzicht** om de implementatie van de virtuele machine te starten. De implementatie van de VM neemt enkele minuten in beslag, maar u kunt tijdens het maken van de VM doorgaan met de volgende stap.

### <a name="create-the-second-virtual-machine"></a>De tweede virtuele machine maken

Voer nogmaals stap 1-7 uit, maar noem in stap 3 de virtuele machine *myVmPrivate* en selecteer in stap 5 het **Privé**-subnet.

Het implementeren van de VM duurt een paar minuten. Ga niet door met de volgende stap voordat deze is gemaakt en de instellingen ervan in de portal worden geopend.

## <a name="confirm-access-to-storage-account"></a>Toegang tot opslagaccount bevestigen

1. Wanneer de VM *myVmPrivate* is gemaakt, worden de instellingen ervan in Azure geopend. Maak verbinding met de VM door de knop **Verbinding maken** te selecteren, zoals in de volgende afbeelding:

   ![Verbinding maken met een virtuele machine](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. Nadat u de knop **Verbinding maken** hebt geselecteerd, wordt er een RDP-bestand (Remote Desktop Protocol) gemaakt en gedownload op uw computer.  
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM. 
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.
6. Wijs op de VM *myVmPrivate* de Azure-bestandsshare toe aan station Z met behulp van PowerShell. Voordat u de opdrachten die volgen uitvoert, vervangt u `<storage-account-key>` en `<storage-account-name>` door waarden die u hebt opgegeven en opgehaald in [Een opslagaccount maken](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell retourneert uitvoer die er ongeveer zo uitziet als in dit voorbeeld:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   De Azure-bestandsshare is toegewezen aan station Z.

7. Bevestig dat de virtuele machine geen uitgaande verbinding heeft naar internet vanaf een opdrachtprompt:

   ```
   ping bing.com
   ```

   U krijgt geen antwoorden, omdat de netwerkbeveiligingsgroep die is gekoppeld aan het *Privé*-subnet geen uitgaande toegang toestaat naar internet.

8. Sluit de externe bureaubladsessie naar de VM *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bevestigen dat toegang tot opslagaccount wordt geweigerd

1. Voer in het vak *Resources, services en documenten zoeken* bovenaan de portal **myVmPublic** in.
2. Wanneer **myVmPublic** wordt weergegeven in de zoekresultaten, selecteert u deze.
3. Voltooi de stappen 1-6 in [Toegang tot opslagaccount bevestigen](#confirm-access-to-storage-account) voor de VM *myVmPublic*.

   Na enkele ogenblikken ontvangt u een fout `New-PSDrive : Access is denied`. De toegang wordt geweigerd omdat de VM *myVmPublic* is geïmplementeerd in het *Openbare* subnet. Het *Openbare* subnet heeft geen service-eindpunt voor Azure Storage. Het opslagaccount staat alleen netwerktoegang toe vanuit het *Privé*-subnet, niet het *Openbare* subnet.

4. Sluit de externe bureaubladsessie naar de VM *myVmPublic*.

5. Browse vanaf uw computer naar Azure [Portal](https://portal.azure.com).
6. Voer de naam in van het opslagaccount dat u hebt gemaakt in het vak **Resources, services en documenten zoeken**. Wanneer de naam van uw opslagaccount wordt weergegeven in de zoekresultaten, selecteert u deze.
7. Selecteer **Bestanden**.
8. Het foutbericht uit de volgende afbeelding wordt weergegeven:

   ![Fout: Toegang geweigerd](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

   De toegang wordt geweigerd omdat de computer zich niet in het *Privé*-subnet van het virtuele netwerk *MyVirtualNetwork* bevindt.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet langer nodig hebt, verwijdert u de resourcegroep en alle resources die deze bevat:

1. Voer *myResourceGroup* in het vak **Zoeken** bovenaan de portal in. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een service-eindpunt voor een subnet van een virtueel netwerk ingeschakeld. U hebt geleerd dat u service-eindpunten kunt inschakelen voor vanaf meerdere Azure-services geïmplementeerde resources. U hebt een Azure Storage-account gemaakt en netwerktoegang tot het opslagaccount beperkt tot alleen resources binnen een subnet van een virtueel netwerk. Zie voor meer informatie over service-eindpunten [Overzicht voor service-eindpunten](virtual-network-service-endpoints-overview.md) en [Subnetten beheren](virtual-network-manage-subnet.md).

Als u meerdere virtuele netwerken in uw account hebt, kunt u twee virtuele netwerken met elkaar verbinden zodat de resources in beide virtuele netwerken met elkaar kunnen communiceren. Ga verder met de volgende zelfstudie om te leren hoe u virtuele netwerken met elkaar kunt verbinden.

> [!div class="nextstepaction"]
> [Virtuele netwerken verbinden](./tutorial-connect-virtual-networks-portal.md)
