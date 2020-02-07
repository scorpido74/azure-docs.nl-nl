---
title: Een SMB-volume maken voor Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u een SMB-volume maakt voor Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: b-juche
ms.openlocfilehash: c65da771dd483b3a79785d4bec2b89cbeefca5c4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049890"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Een SMB-volume maken voor Azure NetApp Files

Azure NetApp Files ondersteunt NFS-en SMBv3-volumes. Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool. In dit artikel wordt beschreven hoe u een SMBv3-volume maakt. Als u een NFS-volume wilt maken, raadpleegt u [een NFS-volume maken voor Azure NetApp files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Voordat u begint 
U dient al een capaciteitspool te hebben ingesteld.   
[Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)   
Er moet een subnet zijn gedelegeerd aan Azure NetApp Files.  
[Een subnet delegeren aan Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Vereisten voor Active Directory verbindingen

 U moet Active Directory verbindingen maken voordat u een SMB-volume maakt. De vereisten voor Active Directory verbindingen zijn als volgt: 

* Het beheerders account dat u gebruikt, moet in staat zijn om computer accounts te maken in het pad van de organisatie-eenheid (OE) dat u opgeeft.  

* De juiste poorten moeten geopend zijn op de betreffende Windows Active Directory (AD)-server.  
    De vereiste poorten zijn als volgt: 

    |     Service           |     Poort     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    AD-webservices    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N.v.t.       |    ECHO antwoord    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS-naam       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    W32Time            |    123       |    UDP           |

* De site topologie voor de doel-Active Directory Domain Services moet voldoen aan de aanbevolen procedures, met name de Azure VNet waar Azure NetApp Files wordt geïmplementeerd.  

    De adres ruimte voor het virtuele netwerk waar Azure NetApp Files wordt geïmplementeerd, moet worden toegevoegd aan een nieuwe of bestaande Active Directory site (waarbij een domein controller bereikbaar is voor Azure NetApp Files). 

* De opgegeven DNS-servers moeten bereikbaar zijn vanaf het [overgedragen subnet](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) van Azure NetApp files.  

    Zie de [richt lijnen voor het Azure NetApp files-netwerk planning](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) voor ondersteunde netwerk topologieën.

    De netwerk beveiligings groepen (Nsg's) en firewalls moeten de juiste geconfigureerde regels hebben om Active Directory-en DNS-verkeers aanvragen toe te staan. 

* Het Azure NetApp Files overgedragen subnet moet alle Active Directory Domain Services (toevoegen) domein controllers in het domein, inclusief alle lokale en externe domein controllers, kunnen bereiken. Anders kan er sprake zijn van een onderbreking van de service.  

    Als u domein controllers hebt die niet bereikbaar zijn via het Azure NetApp Files gedelegeerde subnet, kunt u een ondersteunings aanvraag voor Azure verzenden om de scope te wijzigen van **Algemeen** (standaard) naar **site**.  Azure NetApp Files moet alleen communiceren met domein controllers in de site waar de Azure NetApp Files adres ruimte van het geplaatste subnet zich bevindt.

    Zie [de site topologie ontwerpen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) over AD-sites en-services. 
    
Zie Azure NetApp Files [SMB Veelgestelde vragen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) over extra AD-informatie. 

## <a name="create-an-active-directory-connection"></a>Een Active Directory verbinding maken

1. Klik vanuit uw NetApp-account op **Active Directory verbindingen**en klik vervolgens op **lid worden**.  

    ![Active Directory verbindingen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Geef in het venster lid worden Active Directory de volgende informatie op:

    * **Primaire DNS**  
        Dit is de DNS-server die is vereist voor de Active Directory domein deelname en SMB-verificatie bewerkingen. 
    * **Secundaire DNS-**    
        Dit is de secundaire DNS-server voor het controleren van redundante naam Services. 
    * **Domeinen**  
        Dit is de domein naam van de Active Directory Domain Services die u wilt toevoegen.
    * **Voor voegsel van SMB-server (computer-account)**  
        Dit is het naam voorvoegsel voor het machine account in Active Directory dat Azure NetApp Files gebruikt voor het maken van nieuwe accounts.

        Als de naamgevings standaard die uw organisatie gebruikt voor bestands servers NAS-01, NAS-02..., NAS-045 is, voert u "NAS" in voor het voor voegsel. 

        Met de service worden zo nodig extra machine accounts in Active Directory gemaakt.

    * **Pad naar de organisatie-eenheid**  
        Dit is het LDAP-pad voor de organisatie-eenheid (OE) waar de computer accounts van de SMB-server worden gemaakt. Dat wil zeggen OU = 2e niveau, OE = eerste niveau. 

        Als u Azure NetApp Files gebruikt met Azure Active Directory Domain Services, wordt het pad van de organisatie-eenheid `OU=AADDC Computers` wanneer u Active Directory configureert voor uw NetApp-account.
        
    * Referenties, inclusief uw **gebruikers naam** en **wacht woord**

    ![Active Directory toevoegen](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Klik op **Deelnemen**.  

    De Active Directory verbinding die u hebt gemaakt, wordt weer gegeven.

    ![Active Directory verbindingen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> U kunt de velden gebruikers naam en wacht woord bewerken nadat u de Active Directory verbinding hebt opgeslagen. Er kunnen geen andere waarden worden bewerkt na het opslaan van de verbinding. Als u andere waarden moet wijzigen, moet u eerst alle geïmplementeerde SMB-volumes verwijderen en vervolgens de Active Directory verbinding verwijderen en opnieuw maken.

## <a name="add-an-smb-volume"></a>Een SMB-volume toevoegen

1. Klik op de Blade **volumes** op de Blade capaciteits groepen. 

    ![Navigeren naar volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klik op **+ Volume toevoegen** om een volume te maken.  
    Het venster een volume maken wordt weer gegeven.

3. Klik in het venster een volume maken op **maken** en geef informatie op voor de volgende velden:   
    * **Volume naam**      
        Geef de naam op voor het volume dat u wilt maken.   

        Een volume naam moet uniek zijn binnen elke capaciteits groep. De naam moet minstens drie tekens bevatten. U kunt alle alfanumerieke tekens gebruiken.   

        U kunt `default` niet als de naam van het volume gebruiken.

    * **Capaciteits pool**  
        Geef de capaciteits pool op waar u het volume wilt maken.

    * **Quotum**  
        Geef de hoeveelheid logische opslag op die u wilt toewijzen aan het volume.  

        Het veld **Beschikbare quotum** toont hoeveel ongebruikte ruimte er is in de gekozen capaciteitspool, die u kunt gebruiken om een nieuw volume te maken. De grootte van het nieuwe volume mag niet groter zijn dan het beschikbare quotum.  

    * **Virtueel netwerk**  
        Geef het virtuele Azure-netwerk (VNet) op van waaruit u toegang wilt krijgen tot het volume.  

        Het VNet dat u opgeeft, moet een subnet hebben gedelegeerd aan Azure NetApp Files. De Azure NetApp Files-service kan alleen worden geopend vanuit hetzelfde VNet of vanuit een VNet dat zich in dezelfde regio bevindt als het volume via VNet-peering. U kunt het volume ook openen vanuit uw on-premises netwerk via een snelle route.   

    * **Subnet**  
        Geef het subnet op dat u wilt gebruiken voor het volume.  
        Het opgegeven subnet moet zijn gedelegeerd aan Azure NetApp Files. 
        
        Als u geen subnet hebt gedelegeerd, kunt u klikken op **Nieuwe maken** op de pagina Een volume maken. Geef vervolgens op de pagina Subnet maken de subnetgegevens op, en selecteer **Microsoft.NetApp/volumes** om het subnet te delegeren aan Azure NetApp Files. In elk VNet kan slechts één subnet worden gedelegeerd aan Azure NetApp Files.   
 
        ![Een volume maken](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Subnet maken](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klik op **protocol** en voer de volgende informatie uit:  
    * Selecteer **SMB** als het protocol type voor het volume. 
    * Selecteer uw **Active Directory** verbinding in de vervolg keuzelijst.
    * Geef de naam op van het gedeelde volume in **share naam**.

    ![SMB-protocol opgeven](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Klik op **beoordeling + maken** om de details van het volume te controleren.  Klik vervolgens op **maken** om het SMB-volume te maken.

    Het volume dat u hebt gemaakt, wordt weer gegeven op de pagina volumes. 
 
    Een volume neemt het abonnement, de resourcegroep en de locatiekenmerken over van de bijbehorende capaciteitspool. U kunt de implementatiestatus van het volume controleren vanuit het tabblad Meldingen.

## <a name="next-steps"></a>Volgende stappen  

* [Een volume koppelen of ontkoppelen voor virtuele Windows-of Linux-machines](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Veelgestelde vragen over SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* Meer informatie over [Integratie van virtuele netwerken voor Azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Een nieuw Active Directory-forest installeren met behulp van Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
