---
title: Een SMB-volume maken voor Azure NetApp-bestanden | Microsoft Documenten
description: Beschrijft hoe u een SMB-volume maakt voor Azure NetApp-bestanden.
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
ms.date: 03/13/2020
ms.author: b-juche
ms.openlocfilehash: b2000c3fd3d64793f797e997d8f3c10eaed5d7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409580"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Een SMB-volume maken voor Azure NetApp Files

Azure NetApp Files ondersteunt NFS- en SMBv3-volumes. Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool. In dit artikel ziet u hoe u een SMBv3-volume maakt. Zie [Een NFS-volume maken voor Azure NetApp-bestanden](azure-netapp-files-create-volumes.md)als u een NFS-volume wilt maken. 

## <a name="before-you-begin"></a>Voordat u begint 
U dient al een capaciteitspool te hebben ingesteld.   
[Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)   
Er moet een subnet zijn gedelegeerd aan Azure NetApp Files.  
[Een subnet delegeren aan Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Vereisten voor Active Directory-verbindingen

 U moet Active Directory-verbindingen maken voordat u een SMB-volume maakt. De vereisten voor Active Directory-verbindingen zijn als volgt: 

* Het beheerdersaccount dat u gebruikt, moet de mogelijkheid hebben om machineaccounts te maken in het organisatie-eenheidspad (OU) dat u opgeeft.  

* De juiste poorten moeten zijn geopend op de toepasselijke Windows Active Directory -server (AD).  
    De vereiste poorten zijn als volgt: 

    |     Service           |     Poort     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    AD-webservices    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N.v.t.       |    Echo-antwoord    |
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
    |    W32time            |    123       |    UDP           |

* De sitetopologie voor de beoogde Active Directory Domain Services moet voldoen aan best practices, met name het Azure VNet waar Azure NetApp Files wordt geïmplementeerd.  

    De adresruimte voor het virtuele netwerk waar Azure NetApp-bestanden wordt geïmplementeerd, moet worden toegevoegd aan een nieuwe of bestaande Active Directory-site (waar een domeincontroller bereikbaar is voor Azure NetApp Files). 

* De opgegeven DNS-servers moeten bereikbaar zijn via het [gedelegeerde subnet](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) van Azure NetApp-bestanden.  

    Zie [Richtlijnen voor azure NetApp Files-netwerkplanning](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) voor ondersteunde netwerktopologieën.

    De Network Security Groups (NSGs) en firewalls moeten op de juiste wijze geconfigureerde regels hebben om Active Directory- en DNS-verkeersaanvragen mogelijk te maken. 

* Het gedelegeerde subnet azure NetApp-bestanden moet alle Active Directory Domain Services -domeincontrollers in het domein kunnen bereiken, inclusief alle lokale en externe domeincontrollers. Anders kan serviceonderbreking optreden.  

    Als u domeincontrollers hebt die onbereikbaar zijn door het gedelegeerde subnet van Azure NetApp Files, u een Active Directory-site opgeven tijdens het maken van de Active Directory-verbinding.  Azure NetApp Files hoeft alleen te communiceren met domeincontrollers op de site waar de gedelegeerde subnetadresruimte van Azure NetApp Files zich bevindt.

    Zie [Het ontwerpen van de sitetopologie](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) over AD-sites en -services. 
    
Zie [veelgestelde vragen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) over Azure NetApp Files SMB over aanvullende AD-informatie. 

## <a name="decide-which-domain-services-to-use"></a>Beslissen welke domeinservices moeten worden gebruikt 

Azure NetApp Files ondersteunt zowel [Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) als Azure Active Directory Domain Services (AADDS) voor AD-verbindingen.  Voordat u een AD-verbinding maakt, moet u beslissen of u ADDS of AADDS wilt gebruiken.  

Zie [Zelfbeheerde Active Directory Domain Services, Azure Active Directory en beheerde Azure Active Directory Domain Services vergelijken](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)voor meer informatie. 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

U de gewenste [Active Directory-sites en -services-scope](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) gebruiken voor Azure NetApp-bestanden. Met deze optie u lezen en schrijven naar Active Directory Domain Services -domeincontrollers (ADDS) die toegankelijk zijn [voor Azure NetApp-bestanden.](azure-netapp-files-network-topologies.md) Het voorkomt ook dat de service communiceert met domeincontrollers die zich niet in de opgegeven Active Directory-sites en -services-site bevinden. 

Als u uw sitenaam wilt vinden wanneer u ADDS gebruikt, u contact opnemen met de beheergroep in uw organisatie die verantwoordelijk is voor Active Directory Domain Services. In het onderstaande voorbeeld ziet u de plug-in Active Directory Sites and Services waar de sitenaam wordt weergegeven: 

![Sites en services van Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

Wanneer u een AD-verbinding configureert voor Azure NetApp-bestanden, geeft u de sitenaam op in het bereik van het veld **AD-sitenaam.**

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Zie configuratie en richtlijnen van [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/)voor aadds (Azure Active Directory Domain Services) .

Aanvullende AADDS-overwegingen zijn van toepassing op Azure NetApp-bestanden: 

* Controleer of het VNet of subnet waar AADDS wordt geïmplementeerd zich in dezelfde Azure-regio bevindt als de Azure NetApp-bestanden-implementatie.
* Als u een andere VNet gebruikt in het gebied waar Azure NetApp-bestanden worden geïmplementeerd, moet u een peering maken tussen de twee VNets.
* Ondersteuning `user` en `resource forest` typen van Azure NetApp Files.
* Voor synchronisatietype u `All` selecteren `Scoped`of.   
    Als u `Scoped`de juiste Azure AD-groep selecteert, wordt de juiste Azure AD-groep geselecteerd voor toegang tot SMB-shares.  Als u onzeker bent, `All` kunt u het synchronisatietype gebruiken.
* Gebruik van de Enterprise of Premium SKU is vereist. De Standaard SKU wordt niet ondersteund.

Wanneer u een Active Directory-verbinding maakt, moet u de volgende specificaties voor AADDS noteren:

* U informatie voor **Primaire DNS,** **Secundaire DNS**en **AD DNS-domeinnaam** vinden in het menu AADDS.  
Voor DNS-servers worden twee IP-adressen gebruikt voor het configureren van de Active Directory-verbinding. 
* Het pad van `OU=AADDC Computers`de **organisatie-eenheid** is .  
Deze instelling is geconfigureerd in de **Active Directory Connections** onder **NetApp-account:**

  ![Pad van de organisatie-eenheid](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **Gebruikersreferenties** kunnen elke gebruiker zijn die lid is van de Azure AD-groep **Azure AD DC Administrators**.


## <a name="create-an-active-directory-connection"></a>Een Active Directory-verbinding maken

1. Klik in uw NetApp-account op **Active Directory-verbindingen**en klik vervolgens op **Deelnemen**.  

    ![Active Directory-verbindingen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Geef in het venster Join Active Directory de volgende gegevens op op basis van de domeinservices die u wilt gebruiken:  

    Zie [Beslissen welke domeinservices u wilt gebruiken](#decide-which-domain-services-to-use)voor informatie die specifiek is voor de domeinservices die u gebruikt. 

    * **Primaire DNS**  
        Dit is de DNS die vereist is voor de join- en SMB-verificatiebewerkingen van Active Directory-domein. 
    * **Secundaire DNS**   
        Dit is de secundaire DNS-server voor het waarborgen van redundante naamservices. 
    * **AD DNS-domeinnaam**  
        Dit is de domeinnaam van uw Active Directory Domain Services waaraan u wilt deelnemen.
    * **AD-sitenaam**  
        Dit is de sitenaam waartoe de detectie van de domeincontroller beperkt blijft.
    * **Voorvoegsel van smb-server (computeraccount)**  
        Dit is het naamgevingsvoorvoegsel voor het machineaccount in Active Directory dat Azure NetApp-bestanden gebruiken voor het maken van nieuwe accounts.

        Als de naamgevingsstandaard die uw organisatie bijvoorbeeld gebruikt voor bestandsservers NAS-01, NAS-02..., NAS-045, wordt ingevoerd voor het voorvoegsel. 

        De service maakt indien nodig extra machineaccounts in Active Directory.

    * **Pad van de organisatie-eenheid**  
        Dit is het LDAP-pad voor de organisatie-eenheid (OU) waar SMB-servermachineaccounts worden gemaakt. Dat wil zeggen, OU = tweede niveau, OU = eerste niveau. 

        Als u Azure NetApp-bestanden gebruikt met Azure Active Directory `OU=AADDC Computers` Domain Services, wordt het pad van de organisatie-eenheid geopend wanneer u Active Directory configureert voor uw NetApp-account.
        
    * Referenties, inclusief **gebruikersnaam** en **wachtwoord**

    ![Lid worden van Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Klik op **Deelnemen**.  

    De Active Directory-verbinding die u hebt gemaakt, wordt weergegeven.

    ![Active Directory-verbindingen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> U de gebruikersnaam- en wachtwoordvelden bewerken nadat u de Active Directory-verbinding hebt opgeslagen. Na het opslaan van de verbinding kunnen geen andere waarden worden bewerkt. Als u andere waarden wilt wijzigen, moet u eerst geïmplementeerde SMB-volumes verwijderen en vervolgens de Active Directory-verbinding verwijderen en opnieuw maken.

## <a name="add-an-smb-volume"></a>Een SMB-volume toevoegen

1. Klik op het blad **Volumes** van het blad Capaciteitspools. 

    ![Navigeren naar volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klik op **+ Volume toevoegen** om een volume te maken.  
    Het venster Een volume maken wordt weergegeven.

3. Klik in het venster Een volume maken op **Maken** en geef informatie op voor de volgende velden:   
    * **Volumenaam**      
        Geef de naam op voor het volume dat u wilt maken.   

        Een volumenaam moet uniek zijn binnen elke capaciteitsgroep. De naam moet minstens drie tekens bevatten. U alfanumerieke tekens gebruiken.   

        U niet `default` als volumenaam gebruiken.

    * **Capaciteitspool**  
        Geef de capaciteitsgroep op waar het volume moet worden gemaakt.

    * **Quotum**  
        Geef de hoeveelheid logische opslag op die u wilt toewijzen aan het volume.  

        Het veld **Beschikbare quotum** toont hoeveel ongebruikte ruimte er is in de gekozen capaciteitspool, die u kunt gebruiken om een nieuw volume te maken. De grootte van het nieuwe volume mag niet groter zijn dan het beschikbare quotum.  

    * **Virtueel netwerk**  
        Geef het Virtual Network (VNet) van Azure op van waaruit u toegang wilt krijgen tot het volume.  

        Het VNet dat u opgeeft, moet een subnet hebben dat is gedelegeerd aan Azure NetApp-bestanden. De Azure NetApp Files-service is alleen toegankelijk vanaf hetzelfde VNet of vanaf een VNet dat zich in dezelfde regio bevindt als het volume via VNet-peering. U hebt ook toegang tot het volume vanuit uw on-premises netwerk via Express Route.   

    * **Subnet**  
        Geef het subnet op dat u wilt gebruiken voor het volume.  
        Het opgegeven subnet moet zijn gedelegeerd aan Azure NetApp Files. 
        
        Als u een subnet niet hebt gedelegeerd, u op **Nieuw maken** klikken op de pagina Een volume maken. Geef vervolgens op de pagina Subnet maken de subnetgegevens op, en selecteer **Microsoft.NetApp/volumes** om het subnet te delegeren aan Azure NetApp Files. In elke VNet kan slechts één subnet worden gedelegeerd aan Azure NetApp-bestanden.   
 
        ![ Een volume maken](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Subnet maken](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klik **op Protocol** en vul de volgende informatie in:  
    * Selecteer **SMB** als protocoltype voor het volume. 
    * Selecteer uw **Active Directory-verbinding** in de vervolgkeuzelijst.
    * Geef de naam op van het gedeelde volume in **De naam Delen**.

    ![SMB-protocol opgeven](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Klik **op Controleren + Maken** om de volumedetails te bekijken.  Klik vervolgens op **Maken** om het SMB-volume te maken.

    Het volume dat u hebt gemaakt, wordt weergegeven op de pagina Volumes. 
 
    Een volume neemt het abonnement, de resourcegroep en de locatiekenmerken over van de bijbehorende capaciteitspool. U kunt de implementatiestatus van het volume controleren vanuit het tabblad Meldingen.

## <a name="next-steps"></a>Volgende stappen  

* [Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Veelgestelde vragen aan het MKB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* Meer informatie over [Integratie van virtuele netwerken voor Azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Een nieuw Active Directory-forest installeren met Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
