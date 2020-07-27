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
ms.topic: how-to
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: 848a5779538f4754ef038a1e88be63c33177bc82
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87169976"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Een SMB-volume maken voor Azure NetApp Files

Azure NetApp Files ondersteunt NFS-en SMBv3-volumes. Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool. In dit artikel wordt beschreven hoe u een SMBv3-volume maakt. Als u een NFS-volume wilt maken, raadpleegt u [een NFS-volume maken voor Azure NetApp files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Voordat u begint 
U dient al een capaciteitspool te hebben ingesteld.   
[Een capaciteits groep instellen](azure-netapp-files-set-up-capacity-pool.md)   
Er moet een subnet zijn gedelegeerd aan Azure NetApp Files.  
[Een subnet delegeren aan Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Vereisten voor Active Directory verbindingen

 U moet Active Directory verbindingen maken voordat u een SMB-volume maakt. De vereisten voor Active Directory verbindingen zijn als volgt: 

* Het beheerders account dat u gebruikt, moet de mogelijkheid hebben om computer accounts te maken in het pad naar de organisatie-eenheid (OE) dat u opgeeft.  

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

* De site topologie voor de doel-Active Directory Domain Services moet voldoen aan de richt lijnen, met name de Azure VNet waar Azure NetApp Files wordt geïmplementeerd.  

    De adres ruimte voor het virtuele netwerk waar Azure NetApp Files wordt geïmplementeerd, moet worden toegevoegd aan een nieuwe of bestaande Active Directory site (waarbij een domein controller bereikbaar is voor Azure NetApp Files). 

* De opgegeven DNS-servers moeten bereikbaar zijn vanaf het [overgedragen subnet](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) van Azure NetApp files.  

    Zie de [richt lijnen voor het Azure NetApp files-netwerk planning](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) voor ondersteunde netwerk topologieën.

    De netwerk beveiligings groepen (Nsg's) en firewalls moeten de juiste geconfigureerde regels hebben om Active Directory-en DNS-verkeers aanvragen toe te staan. 

* Het Azure NetApp Files overgedragen subnet moet alle Active Directory Domain Services (toevoegen) domein controllers in het domein, inclusief alle lokale en externe domein controllers, kunnen bereiken. Anders kan er sprake zijn van een onderbreking van de service.  

    Als u domein controllers hebt die niet bereikbaar zijn voor het Azure NetApp Files overgedragen subnet, kunt u een Active Directory site opgeven tijdens het maken van de Active Directory verbinding.  Azure NetApp Files moet alleen communiceren met domein controllers in de site waar de Azure NetApp Files adres ruimte van het subnet is overgedragen.

    Zie [de site topologie ontwerpen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) over AD-sites en-services. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers Microsoft Management Console (MMC):   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

Zie Azure NetApp Files [SMB Veelgestelde vragen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) over extra AD-informatie. 

## <a name="decide-which-domain-services-to-use"></a>Beslissen welke domein Services u wilt gebruiken 

Azure NetApp Files ondersteunt zowel [Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (toevoegen) als Azure Active Directory Domain Services (AADDS) voor AD-verbindingen.  Voordat u een AD-verbinding maakt, moet u beslissen of u toevoegt of AADDS wilt gebruiken.  

Zie voor meer informatie [zelf beheerde Active Directory Domain Services, Azure Active Directory en beheerde Azure Active Directory Domain Services vergelijken](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

U kunt uw favoriete [Active Directory-sites en-services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) -bereik gebruiken voor Azure NetApp files. Met deze optie schakelt u lees-en schrijf bewerkingen naar Active Directory Domain Services (toevoegen) domein controllers die [toegankelijk zijn via Azure NetApp files](azure-netapp-files-network-topologies.md). Ook wordt voor komen dat de service communiceert met domein controllers die zich niet in de opgegeven Active Directory sites en Services-site. 

Als u wilt zoeken naar de naam van uw site wanneer u toevoegt, kunt u contact opnemen met de beheer groep in uw organisatie die verantwoordelijk is voor Active Directory Domain Services. In het onderstaande voor beeld ziet u de Active Directory-invoeg toepassing sites en services waar de site naam wordt weer gegeven: 

![Sites en services van Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Wanneer u een AD-verbinding voor Azure NetApp Files configureert, geeft u de site naam in bereik op voor het veld **ad-site naam** .

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Zie [Azure AD Domain Services documentatie](https://docs.microsoft.com/azure/active-directory-domain-services/)voor Azure Active Directory Domain Services (AADDS) configuratie en richt lijnen.

Er zijn aanvullende AADDS-overwegingen van toepassing op Azure NetApp Files: 

* Zorg ervoor dat het VNet of subnet waar AADDS wordt geïmplementeerd, zich in dezelfde Azure-regio bevindt als de Azure NetApp Files-implementatie.
* Als u een ander VNet gebruikt in de regio waar Azure NetApp Files is geïmplementeerd, moet u een peering tussen de twee VNets maken.
* Azure NetApp Files ondersteunt `user` en `resource forest` typen.
* Voor het synchronisatie type kunt u `All` of selecteren `Scoped` .   
    Als u selecteert `Scoped` , moet u ervoor zorgen dat de juiste Azure AD-groep is geselecteerd voor toegang tot SMB-shares.  Als u niet zeker weet, kunt u het `All` synchronisatie type gebruiken.
* Het gebruik van de Enter prise-of Premium-SKU is vereist. De standaard-SKU wordt niet ondersteund.

Wanneer u een Active Directory verbinding maakt, moet u rekening houden met de volgende specifieke voor AADDS:

* In het menu AADDS vindt u informatie over de **primaire**DNS-, **secundaire DNS**-en **AD DNS-domein naam** .  
Voor DNS-servers worden er twee IP-adressen gebruikt voor het configureren van de Active Directory-verbinding. 
* Het **pad voor de organisatie-eenheid** is `OU=AADDC Computers` .  
Deze instelling wordt geconfigureerd in de **Active Directory verbindingen** onder **NetApp-account**:

  ![Pad naar de organisatie-eenheid](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **Gebruikers naam** referenties kunnen alle gebruikers zijn die lid zijn van de Azure AD-groep **Azure AD DC-Administrators**.


## <a name="create-an-active-directory-connection"></a>Een Active Directory verbinding maken

1. Klik vanuit uw NetApp-account op **Active Directory verbindingen**en klik vervolgens op **lid worden**.  

    ![Active Directory verbindingen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Geef in het venster lid worden Active Directory de volgende informatie op op basis van de domein services die u wilt gebruiken:  

    Zie [beslissen welke domein Services u moet gebruiken](#decide-which-domain-services-to-use)voor informatie die specifiek is voor de domein services die u gebruikt. 

    * **Primaire DNS**  
        Dit is de DNS-server die is vereist voor de Active Directory domein deelname en SMB-verificatie bewerkingen. 
    * **Secundaire DNS**   
        Dit is de secundaire DNS-server voor het controleren van redundante naam Services. 
    * **AD DNS-domein naam**  
        Dit is de domein naam van de Active Directory Domain Services die u wilt toevoegen.
    * **AD-site naam**  
        Dit is de naam van de site die de detectie van de domein controller beperkt is.
    * **Voor voegsel van SMB-server (computer-account)**  
        Dit is het naam voorvoegsel voor het machine account in Active Directory dat Azure NetApp Files gebruikt voor het maken van nieuwe accounts.

        Als de naamgevings standaard die uw organisatie gebruikt voor bestands servers NAS-01, NAS-02..., NAS-045 is, voert u "NAS" in voor het voor voegsel. 

        Met de service worden zo nodig extra machine accounts in Active Directory gemaakt.

        > [!IMPORTANT] 
        > Als u de naam van het SMB-server voorvoegsel wijzigt nadat u de Active Directory verbinding hebt gemaakt, verstoort het. U moet bestaande SMB-shares opnieuw koppelen na het wijzigen van de naam van het voor voegsel van de SMB-server.

    * **Pad naar de organisatie-eenheid**  
        Dit is het LDAP-pad voor de organisatie-eenheid (OE) waar de computer accounts van de SMB-server worden gemaakt. Dat wil zeggen OU = 2e niveau, OE = eerste niveau. 

        Als u Azure NetApp Files gebruikt met Azure Active Directory Domain Services, is het pad voor de organisatie-eenheid `OU=AADDC Computers` Wanneer u Active Directory configureert voor uw NetApp-account.

     * **Back-upbeleid gebruikers**  
        U kunt aanvullende accounts toevoegen waarvoor verhoogde bevoegdheden zijn vereist voor het computer account dat is gemaakt voor gebruik met Azure NetApp Files. De opgegeven accounts kunnen de NTFS-machtigingen op bestands-of mapniveau wijzigen. U kunt bijvoorbeeld een niet-privileged service account opgeven dat wordt gebruikt voor het migreren van gegevens naar een SMB-bestands share in Azure NetApp Files.  

        > [!IMPORTANT] 
        > Voor het gebruik van de gebruikers functie back-upbeleid is white list vereist. E-mail anffeedback@microsoft.com met uw abonnements-id om deze functie aan te vragen. 

    * Referenties, inclusief uw **gebruikers naam** en **wacht woord**

    ![Active Directory toevoegen](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Klik op **Deelnemen**.  

    De Active Directory verbinding die u hebt gemaakt, wordt weer gegeven.

    ![Active Directory verbindingen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Een SMB-volume toevoegen

1. Klik op de Blade **volumes** op de Blade capaciteits groepen. 

    ![Navigeren naar volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klik op **+ Volume toevoegen** om een volume te maken.  
    Het venster een volume maken wordt weer gegeven.

3. Klik in het venster een volume maken op **maken** en geef informatie op voor de volgende velden:   
    * **Volume naam**      
        Geef de naam op voor het volume dat u wilt maken.   

        Een volume naam moet uniek zijn binnen elke capaciteits groep. De naam moet minstens drie tekens bevatten. U kunt alle alfanumerieke tekens gebruiken.   

        U kunt niet gebruiken `default` als de volume naam.

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
        
        Als u nog geen subnet hebt gedelegeerd, kunt u op de pagina een volume maken klikken op **Nieuw maken** . Geef vervolgens op de pagina Subnet maken de subnetgegevens op, en selecteer **Microsoft.NetApp/volumes** om het subnet te delegeren aan Azure NetApp Files. In elk VNet kan slechts één subnet worden gedelegeerd aan Azure NetApp Files.   
 
        ![ Een volume maken](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Subnet maken](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Als u een bestaand momentopname beleid wilt Toep assen op het volume, klikt u op de **sectie Geavanceerd weer geven** om deze uit te vouwen en selecteert u een beleid voor moment opnamen in de vervolg keuzelijst. 

        Zie [moment opnamen beheren](azure-netapp-files-manage-snapshots.md)voor meer informatie over het maken van een momentopname beleid.

        ![Geavanceerde selectie weer geven](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Klik op **protocol** en voer de volgende informatie uit:  
    * Selecteer **SMB** als het protocol type voor het volume. 
    * Selecteer uw **Active Directory** verbinding in de vervolg keuzelijst.
    * Geef de naam op van het gedeelde volume in **share naam**.

    ![SMB-protocol opgeven](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Klik op **beoordeling + maken** om de details van het volume te controleren.  Klik vervolgens op **maken** om het SMB-volume te maken.

    Het volume dat u hebt gemaakt, wordt weer gegeven op de pagina volumes. 
 
    Een volume neemt het abonnement, de resourcegroep en de locatiekenmerken over van de bijbehorende capaciteitspool. U kunt de implementatiestatus van het volume controleren vanuit het tabblad Meldingen.

## <a name="control-access-to-an-smb-volume"></a>Toegang tot een SMB-volume beheren  

Toegang tot een SMB-volume wordt beheerd via machtigingen.  

### <a name="share-permissions"></a>Share machtigingen  

Een nieuw volume heeft standaard de machtigingen delen **iedereen/volledig beheer** . Leden van de groep domein Administrators kunnen de share machtigingen wijzigen door gebruik te maken van computer beheer op het computer account dat wordt gebruikt voor het Azure NetApp Files volume.

![SMB-koppel pad ](../media/azure-netapp-files/smb-mount-path.png) 
 ![ machtigingen voor delen instellen](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS-bestands-en mapmachtigingen  

U kunt machtigingen voor een bestand of map instellen met behulp van het tabblad **beveiliging** van de eigenschappen van het object in de Windows SMB-client.
 
![Machtigingen voor bestanden en mappen instellen](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Volgende stappen  

* [Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Veelgestelde vragen over SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* Meer informatie over [Integratie van virtuele netwerken voor Azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Een nieuw Active Directory-forest installeren met behulp van Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
