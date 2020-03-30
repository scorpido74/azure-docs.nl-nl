---
title: Een NFS-volume maken voor Azure NetApp-bestanden | Microsoft Documenten
description: Beschrijft hoe u een NFS-volume maakt voor Azure NetApp-bestanden.
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
ms.date: 12/01/2019
ms.author: b-juche
ms.openlocfilehash: 9e8817f802ca1d73ca0f6bfa2b32b1b14b37d7da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274084"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Een NFS-volume maken voor Azure NetApp Files

Azure NetApp Files ondersteunt NFS -volumes (NFSv3 en NFSv4.1) en SMBv3. Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool. In dit artikel ziet u hoe u een NFS-volume maakt. Zie [Een SMB-volume maken voor Azure NetApp-bestanden](azure-netapp-files-create-volumes-smb.md)als u een SMB-volume wilt maken. 

## <a name="before-you-begin"></a>Voordat u begint 
U dient al een capaciteitspool te hebben ingesteld.   
[Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)   
Er moet een subnet zijn gedelegeerd aan Azure NetApp Files.  
[Een subnet delegeren aan Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Overwegingen 

* Beslissen welke NFS-versie moet worden gebruikt  
  NFSv3 kan een breed scala aan use cases verwerken en wordt vaak geïmplementeerd in de meeste bedrijfstoepassingen. U moet valideren welke versie (NFSv3 of NFSv4.1) uw toepassing vereist en uw volume maken met de juiste versie. Als u bijvoorbeeld [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)gebruikt, wordt bestandsvergrendeling met NFSv4.1 aanbevolen via NFSv3. 

* Beveiliging  
  Ondersteuning voor UNIX-modusbits (lezen, schrijven en uitvoeren) is beschikbaar voor NFSv3 en NFSv4.1. Root-level toegang is vereist op de NFS-client om NFS-volumes te monteren.

* Lokale gebruikers-/groep- en LDAP-ondersteuning voor NFSv4.1  
  Momenteel ondersteunt NFSv4.1 alleen roottoegang tot volumes. Zie [NFSv4.1-standaarddomein configureren voor Azure NetApp-bestanden](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Best practice

* U moet ervoor zorgen dat u de juiste montage-instructies voor het volume gebruikt.  Zie [Een volume voor virtuele Windows- of Linux-machines monteren of demonteren.](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

* De NFS-client moet zich in dezelfde VNet- of VNet-peered bevinden als het Azure NetApp-bestandenvolume. Verbinding maken van buiten het VNet wordt ondersteund; het zal echter extra latentie introduceren en de algehele prestaties verlagen.

* U moet ervoor zorgen dat de NFS-client up-to-date is en de nieuwste updates voor het besturingssysteem uitvoert.

## <a name="create-an-nfs-volume"></a>Een NFS-volume maken

1.  Klik op het blad **Volumes** van het blad Capaciteitspools. 

    ![Navigeren naar volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Klik op **+ Volume toevoegen** om een volume te maken.  
    Het venster Een volume maken wordt weergegeven.

3.  Klik in het venster Een volume maken op **Maken** en geef informatie op voor de volgende velden:   
    * **Volumenaam**      
        Geef de naam op voor het volume dat u wilt maken.   

        Een volumenaam moet uniek zijn binnen elke capaciteitsgroep. De naam moet minstens drie tekens bevatten. U alfanumerieke tekens gebruiken.   

        U kunt `default` de volumenaam niet gebruiken.

    * **Capaciteitspool**  
        Geef de capaciteitsgroep op waar het volume moet worden gemaakt.

    * **Quotum**  
        Geef de hoeveelheid logische opslag op die u wilt toewijzen aan het volume.  

        Het veld **Beschikbare quotum** toont hoeveel ongebruikte ruimte er is in de gekozen capaciteitspool, die u kunt gebruiken om een nieuw volume te maken. De grootte van het nieuwe volume mag niet groter zijn dan het beschikbare quotum.  

    * **Virtueel netwerk**  
        Geef het virtuele Azure-netwerk (Vnet) op dat u wilt gebruiken om het volume te benaderen.  

        Het opgegeven VNet moet een subnet bevatten dat is gedelegeerd aan Azure NetApp Files. De Azure NetApp Files-service is alleen toegankelijk vanuit hetzelfde VNet, of vanuit een VNet in dezelfde regio als het volume via VNet-peering. U hebt ook toegang tot het volume vanuit uw on-premises netwerk via Express Route.   

    * **Subnet**  
        Geef het subnet op dat u wilt gebruiken voor het volume.  
        Het opgegeven subnet moet zijn gedelegeerd aan Azure NetApp Files. 
        
        Als u geen subnet hebt gedelegeerd, kunt u klikken op **Nieuwe maken** op de pagina Een volume maken. Geef vervolgens op de pagina Subnet maken de subnetgegevens op, en selecteer **Microsoft.NetApp/volumes** om het subnet te delegeren aan Azure NetApp Files. In elk Vnet kan slechts één subnet worden gedelegeerd aan Azure NetApp-bestanden.   
 
        ![ Een volume maken](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Subnet maken](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klik op **Protocol**en voer de volgende acties uit:  
    * Selecteer **NFS** als protocoltype voor het volume.   
    * Geef het **bestandspad** op dat wordt gebruikt om het exportpad voor het nieuwe volume te maken. Het exportpad wordt gebruikt om het volume te koppelen en benaderen.

        Het bestandspad mag alleen letters, cijfers en afbreekstreepjes ('-') bevatten. Het bestandspad moet tussen de 16 en 40 tekens lang zijn. 

        Het bestandspad moet uniek zijn binnen elk abonnement en elke regio. 

    * Selecteer de NFS-versie **(NFSv3** of **NFSv4.1)** voor het volume.  
    * Configureer [destijds het exportbeleid voor het NFS-volume](azure-netapp-files-configure-export-policy.md).

    ![NFS-protocol opgeven](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Klik **op Controleren + Maken** om de volumedetails te bekijken.  Klik vervolgens op **Maken** om het NFS-volume te maken.

    Het volume dat u hebt gemaakt, wordt weergegeven op de pagina Volumes. 
 
    Een volume neemt het abonnement, de resourcegroep en de locatiekenmerken over van de bijbehorende capaciteitspool. U kunt de implementatiestatus van het volume controleren vanuit het tabblad Meldingen.


## <a name="next-steps"></a>Volgende stappen  

* [NFSv4.1-standaarddomein configureren voor Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Exportbeleid voor een NFS-volume configureren](azure-netapp-files-configure-export-policy.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* Meer informatie over [Integratie van virtuele netwerken voor Azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
