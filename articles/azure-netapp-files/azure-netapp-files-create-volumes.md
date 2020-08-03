---
title: Een NFS-volume maken voor Azure NetApp Files | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een NFS-volume maakt in Azure NetApp Files. Meer informatie over overwegingen, zoals welke versie moet worden gebruikt en aanbevolen procedures.
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
ms.openlocfilehash: 2e1e6ad6625586e882551521111057a2a20f0fff
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513040"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Een NFS-volume maken voor Azure NetApp Files

Azure NetApp Files ondersteunt NFS-(NFSv3-en NFSv 4.1)-en SMBv3-volumes. Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool. In dit artikel wordt beschreven hoe u een NFS-volume maakt. Als u een SMB-volume wilt maken, raadpleegt u [een SMB-volume maken voor Azure NetApp files](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Voordat u begint 
U dient al een capaciteitspool te hebben ingesteld.   
[Een capaciteits groep instellen](azure-netapp-files-set-up-capacity-pool.md)   
Er moet een subnet zijn gedelegeerd aan Azure NetApp Files.  
[Een subnet delegeren aan Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Overwegingen 

* Bepalen welke NFS-versie moet worden gebruikt  
  NFSv3 kan een groot aantal use-cases verwerken en wordt meestal geïmplementeerd in de meeste bedrijfs toepassingen. U dient te controleren welke versie (NFSv3 of NFSv 4.1) uw toepassing vereist en maakt u het volume met behulp van de juiste versie. Als u bijvoorbeeld [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)gebruikt, wordt het vergren delen van bestanden met nfsv 4.1 aanbevolen voor NFSv3. 

* Beveiliging  
  Ondersteuning voor UNIX-modus bits (lezen, schrijven en uitvoeren) is beschikbaar voor NFSv3 en NFSv 4.1. Toegang op hoofd niveau is vereist op de NFS-client om NFS-volumes te koppelen.

* Lokale gebruiker/groep en LDAP-ondersteuning voor NFSv 4.1  
  Op dit moment ondersteunt NFSv 4.1 alleen toegang tot de hoofdmap voor volumes. Zie [nfsv 4.1 standaard domein configureren voor Azure NetApp files](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Best practice

* Zorg ervoor dat u de juiste koppelings instructies voor het volume gebruikt.  Zie [een volume koppelen of ontkoppelen voor virtuele Windows-of Linux-machines](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* De NFS-client moet zich in hetzelfde VNet of een gelijkwaardige VNet bezien als het Azure NetApp Files volume. Het maken van een verbinding van buiten het VNet wordt ondersteund; Er wordt echter extra latentie geïntroduceerd en de algehele prestaties worden gereduceerd.

* Zorg ervoor dat de NFS-client up-to-date is en dat de meest recente updates voor het besturings systeem worden uitgevoerd.

## <a name="create-an-nfs-volume"></a>Een NFS-volume maken

1.  Klik op de Blade **volumes** op de Blade capaciteits groepen. 

    ![Navigeren naar volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Klik op **+ Volume toevoegen** om een volume te maken.  
    Het venster een volume maken wordt weer gegeven.

3.  Klik in het venster een volume maken op **maken** en geef informatie op voor de volgende velden:   
    * **Volume naam**      
        Geef de naam op voor het volume dat u wilt maken.   

        Een volume naam moet uniek zijn binnen elke capaciteits groep. De naam moet minstens drie tekens bevatten. U kunt alle alfanumerieke tekens gebruiken.   

        U kunt niet gebruiken `default` als de volume naam.

    * **Capaciteits pool**  
        Geef de capaciteits pool op waar u het volume wilt maken.

    * **Quota**  
        Geef de hoeveelheid logische opslag op die u wilt toewijzen aan het volume.  

        Het veld **Beschikbare quotum** toont hoeveel ongebruikte ruimte er is in de gekozen capaciteitspool, die u kunt gebruiken om een nieuw volume te maken. De grootte van het nieuwe volume mag niet groter zijn dan het beschikbare quotum.  

    * **Virtueel netwerk**  
        Geef het virtuele Azure-netwerk (VNet) op van waaruit u toegang wilt krijgen tot het volume.  

        Het opgegeven VNet moet een subnet bevatten dat is gedelegeerd aan Azure NetApp Files. De Azure NetApp Files-service is alleen toegankelijk vanuit hetzelfde VNet, of vanuit een VNet in dezelfde regio als het volume via VNet-peering. U kunt het volume ook openen vanuit uw on-premises netwerk via een snelle route.   

    * **Subnet**  
        Geef het subnet op dat u wilt gebruiken voor het volume.  
        Het opgegeven subnet moet zijn gedelegeerd aan Azure NetApp Files. 
        
        Als u geen subnet hebt gedelegeerd, kunt u klikken op **Nieuwe maken** op de pagina Een volume maken. Geef vervolgens op de pagina Subnet maken de subnetgegevens op, en selecteer **Microsoft.NetApp/volumes** om het subnet te delegeren aan Azure NetApp Files. In elk Vnet kan slechts één subnet worden gedelegeerd aan Azure NetApp Files.   
 
        ![ Een volume maken](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Subnet maken](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Als u een bestaand momentopname beleid wilt Toep assen op het volume, klikt u op de **sectie Geavanceerd weer geven** om deze uit te vouwen en selecteert u een beleid voor moment opnamen in de vervolg keuzelijst. 

        Zie [moment opnamen beheren](azure-netapp-files-manage-snapshots.md)voor meer informatie over het maken van een momentopname beleid.

        ![Geavanceerde selectie weer geven](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Klik op **Protocol** en voer de volgende acties uit:  
    * Selecteer **NFS** als protocoltype voor het volume.   
    * Geef het **bestandspad op dat wordt** gebruikt voor het maken van het exportpad voor het nieuwe volume. Het exportpad wordt gebruikt om het volume te koppelen en benaderen.

        Het bestandspad mag alleen letters, cijfers en afbreekstreepjes ('-') bevatten. Het bestandspad moet tussen de 16 en 40 tekens lang zijn. 

        Het bestandspad moet uniek zijn binnen elk abonnement en elke regio. 

    * Selecteer de NFS-versie (**NFSv3** of**NFSv4.1**) voor het volume.  
    * Optioneel kunt [u het export beleid configureren voor het NFS-volume](azure-netapp-files-configure-export-policy.md).

    ![NFS-protocol opgeven](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Klik op **beoordeling + maken** om de details van het volume te controleren.  Klik vervolgens op **maken** om het NFS-volume te maken.

    Het volume dat u hebt gemaakt, wordt weer gegeven op de pagina volumes. 
 
    Een volume neemt het abonnement, de resourcegroep en de locatiekenmerken over van de bijbehorende capaciteitspool. U kunt de implementatiestatus van het volume controleren vanuit het tabblad Meldingen.


## <a name="next-steps"></a>Volgende stappen  

* [NFSv4.1-standaarddomein configureren voor Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Exportbeleid voor een NFS-volume configureren](azure-netapp-files-configure-export-policy.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* Meer informatie over [Integratie van virtuele netwerken voor Azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
