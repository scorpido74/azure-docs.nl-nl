---
title: Een NFSv3-en SMB-volume (Dual-Protocol) maken voor Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u een volume maakt dat gebruikmaakt van het dubbele Protocol van NFSv3 en SMB met ondersteuning voor LDAP-gebruikers toewijzing.
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
ms.date: 09/24/2020
ms.author: b-juche
ms.openlocfilehash: 972f9b1ac96ca180aa6eaeead7cde51b60ec0e93
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278482"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Een NFSv3-en SMB-volume (Dual-Protocol) maken voor Azure NetApp Files

Azure NetApp Files biedt ondersteuning voor het maken van volumes met behulp van NFS (NFSv3 en NFSv 4.1), SMBv3 of het dubbele protocol. In dit artikel wordt beschreven hoe u een volume maakt dat gebruikmaakt van het dubbele Protocol van NFSv3 en SMB met ondersteuning voor LDAP-gebruikers toewijzing.  


## <a name="before-you-begin"></a>Voordat u begint 

* U dient al een capaciteitspool te hebben ingesteld.  
    Zie [een capaciteits pool instellen](azure-netapp-files-set-up-capacity-pool.md).   
* Er moet een subnet zijn gedelegeerd aan Azure NetApp Files.  
    Zie [een subnet delegeren aan Azure NetApp files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Overwegingen

* Zorg ervoor dat u voldoet aan de [vereisten voor Active Directory verbindingen](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Maak een zone voor reverse lookup op de DNS-server en voeg vervolgens een PTR-record (pointer) van de AD-hostcomputer toe aan de zone voor reverse lookup. Als dat niet het geval is, mislukt het maken van het volume met twee protocollen.
* Zorg ervoor dat de NFS-client up-to-date is en de meest recente updates voor het besturings systeem uitvoert.

## <a name="create-a-dual-protocol-volume"></a>Een volume met dubbele protocollen maken

1.  Klik op de Blade **volumes** op de Blade capaciteits groepen. Klik op **+ Volume toevoegen** om een volume te maken. 

    ![Navigeren naar volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Klik in het venster een volume maken op **maken**en geef informatie op over de volgende velden op het tabblad basis beginselen:   
    * **Volume naam**      
        Geef de naam op voor het volume dat u wilt maken.   

        Een volume naam moet uniek zijn binnen elke capaciteits groep. De naam moet minstens drie tekens bevatten. U kunt alle alfanumerieke tekens gebruiken.   

        U kunt niet gebruiken `default` of `bin` als de naam van het volume.

    * **Capaciteits pool**  
        Geef de capaciteits pool op waar u het volume wilt maken.

    * **Quota**  
        Geef de hoeveelheid logische opslag op die u wilt toewijzen aan het volume.  

        Het veld **Beschikbare quotum** toont hoeveel ongebruikte ruimte er is in de gekozen capaciteitspool, die u kunt gebruiken om een nieuw volume te maken. De grootte van het nieuwe volume mag niet groter zijn dan het beschikbare quotum.  

    * **Door Voer (MiB/S)**   
        Als het volume is gemaakt in een hand matige QoS-capaciteits groep, geeft u de door Voer op voor het volume.   

        Als het volume wordt gemaakt in een automatische QoS-capaciteits groep, is de waarde die wordt weer gegeven in dit veld (quotum x-door Voer van service niveau).   

    * **Virtueel netwerk**  
        Geef het virtuele Azure-netwerk (VNet) op van waaruit u toegang wilt krijgen tot het volume.  

        Het opgegeven VNet moet een subnet bevatten dat is gedelegeerd aan Azure NetApp Files. De Azure NetApp Files-service is alleen toegankelijk vanuit hetzelfde VNet, of vanuit een VNet in dezelfde regio als het volume via VNet-peering. U kunt het volume ook openen vanuit uw on-premises netwerk via een snelle route.   

    * **Subnet**  
        Geef het subnet op dat u wilt gebruiken voor het volume.  
        Het opgegeven subnet moet zijn gedelegeerd aan Azure NetApp Files. 
        
        Als u geen subnet hebt gedelegeerd, kunt u klikken op **Nieuwe maken** op de pagina Een volume maken. Geef vervolgens op de pagina Subnet maken de subnetgegevens op, en selecteer **Microsoft.NetApp/volumes** om het subnet te delegeren aan Azure NetApp Files. In elk Vnet kan slechts één subnet worden gedelegeerd aan Azure NetApp Files.   
 
        ![ Een volume maken](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Subnet maken](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Als u een bestaand momentopname beleid wilt Toep assen op het volume, klikt u op de **sectie Geavanceerd weer geven** om deze uit te vouwen, geeft u op of u het pad naar de moment opname wilt verbergen en selecteert u een momentopname beleid in de vervolg keuzelijst. 

        Zie voor meer informatie over het maken van een momentopname beleid [beleid voor moment opnamen beheren](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Geavanceerde selectie weer geven](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Klik op **Protocol** en voer de volgende acties uit:  
    * Selecteer **Dual-Protocol (NFSv3 en SMB)** als protocol type voor het volume.   

    * Selecteer de **Active Directory** verbinding in de vervolg keuzelijst.  
    Het Active Directory dat u gebruikt, moet een server basis-CA-certificaat hebben. 

    * Geef het **pad** naar het volume voor het volume op.   
    Dit pad naar het volume is de naam van het gedeelde volume. De naam moet beginnen met een alfabetisch teken en moet uniek zijn binnen elk abonnement en elke regio.  

    * Geef de te gebruiken **beveiligings stijl** op: NTFS (standaard) of UNIX.

    * Optioneel kunt [u het export beleid voor het volume configureren](azure-netapp-files-configure-export-policy.md).

    ![Dual-protocol opgeven](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Klik op **beoordeling + maken** om de details van het volume te controleren. Klik vervolgens op **maken** om het volume te maken.

    Het volume dat u hebt gemaakt, wordt weer gegeven op de pagina volumes. 
 
    Een volume neemt het abonnement, de resourcegroep en de locatiekenmerken over van de bijbehorende capaciteitspool. U kunt de implementatiestatus van het volume controleren vanuit het tabblad Meldingen.

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>Openbaar basis certificaat voor Active Directory certificerings instantie uploaden  

1.  Volg [de installatie van de certificerings instantie](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) die u wilt installeren en configureren certificerings instantie toevoegen. 

2.  Volg [certificaten weer geven met de MMC-module](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) om de MMC-module en het hulp programma certificaat beheer te gebruiken.  
    Gebruik de module certificaat beheer om het basis-of verlenings certificaat voor het lokale apparaat te zoeken. U moet de opdrachten van de module certificaat beheer uitvoeren vanuit een van de volgende instellingen:  
    * Een Windows-client die lid is van het domein en waarop het basis certificaat is geïnstalleerd 
    * Een andere computer in het domein met het basis certificaat  

3. Het basis certificaat exporteren.  
    Zorg ervoor dat het certificaat wordt geëxporteerd in de base-64 Encoded X. 509 (. CER)-indeling: 

    ![Wizard Certificaat exporteren](../media/azure-netapp-files/certificate-export-wizard.png)

4. Ga naar het NetApp-account van het Dual-protocol volume, klik op **Active Directory verbindingen**en upload het basis-CA-certificaat met behulp van het venster **lid worden Active Directory** :  

    ![Server basis-CA-certificaat](../media/azure-netapp-files/server-root-ca-certificate.png)

    Zorg ervoor dat de naam van de certificerings instantie kan worden omgezet door DNS. Deze naam is het veld "verleend door" of "verlener" in het certificaat:  

    ![Certificaat gegevens](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>LDAP POSIX-kenmerken beheren

U kunt POSIX-kenmerken zoals UID, basismap en andere waarden beheren met de Active Directory MMC-module gebruikers en computers.  In het volgende voor beeld wordt de Active Directory kenmerk editor weer gegeven:  

![Active Directory kenmerk editor](../media/azure-netapp-files/active-directory-attribute-editor.png) 


## <a name="configure-the-nfs-client"></a>De NFS-client configureren 

Volg de instructies in [een NFS-client configureren voor Azure NetApp files](configure-nfs-clients.md) om de NFS-client te configureren.  

## <a name="next-steps"></a>Volgende stappen  

* [Veelgestelde vragen over dual-Protocol](azure-netapp-files-faqs.md#dual-protocol-faqs)
* [Een NFS-client voor Azure NetApp Files configureren](configure-nfs-clients.md) 
