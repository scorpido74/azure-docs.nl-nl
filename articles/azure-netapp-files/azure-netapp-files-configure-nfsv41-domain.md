---
title: NfSv4.1-standaarddomein configureren voor Azure NetApp-bestanden | Microsoft Documenten
description: Beschrijft hoe u de NFS-client configureert voor het gebruik van NFSv4.1 met Azure NetApp-bestanden.
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
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906284"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>NFSv4.1-standaarddomein configureren voor Azure NetApp Files

NFSv4 introduceert het concept van een authenticatiedomein. Azure NetApp Files ondersteunt momenteel alleen-gebruikerstoewijzing met alleen rooten van de service naar de NFS-client. Als u de NFSv4.1-functionaliteit wilt gebruiken met Azure NetApp-bestanden, moet u de NFS-client bijwerken.

## <a name="default-behavior-of-usergroup-mapping"></a>Standaardgedrag van gebruikers-/groepstoewijzing

Rootmapping standaard voor `nobody` de gebruiker omdat het NFSv4-domein is ingesteld op `localdomain`. Wanneer u een NFSv4.1-volume van Azure NetApp Files als hoofdmap monteert, ziet u als volgt bestandsmachtigingen:  

![Standaardgedrag van gebruikers-/groepstoewijzing voor NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Zoals het bovenstaande voorbeeld laat `file1` zien, de gebruiker voor moet worden, `root`maar het kaarten aan `nobody` standaard.  In dit artikel ziet `file1` u `root`hoe u de gebruiker instelt op .  

## <a name="steps"></a>Stappen 

1. Bewerk `/etc/idmapd.conf` het bestand op de NFS-client.   
    De opmerking `#Domain` van de regel `#` ongedaan maken (dat `localdomain` wil `defaultv4iddomain.com`zeggen de regel verwijderen) en de waarde wijzigen in . 

    Initiële configuratie: 
    
    ![Initiële configuratie voor NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Bijgewerkte configuratie:
    
    ![Bijgewerkte configuratie voor NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. De huidige NFS-volumes worden ontkoppeld.
3. Werk `/etc/idmapd.conf` het bestand bij.
4. Start `rpcbind` de service opnieuw`service rpcbind restart`op uw host ( ), of gewoon opnieuw opstarten van de host.
5. Monteer de NFS-volumes zoals vereist.   

    Zie [Een volume voor virtuele Windows- of Linux-machines monteren of demonteren.](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 

In het volgende voorbeeld wordt de resulterende gebruiker/groepswijziging weergegeven: 

![Resulterende configuratie voor NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Zoals uit het voorbeeld blijkt, is `nobody` de `root`gebruiker/groep nu gewijzigd van .

## <a name="behavior-of-other-non-root-users-and-groups"></a>Gedrag van andere (niet-root) gebruikers en groepen

Azure NetApp Files ondersteunt lokale gebruikers (gebruikers die lokaal zijn gemaakt op een host) die machtigingen hebben gekoppeld aan bestanden of mappen in NFSv4.1-volumes. De service ondersteunt momenteel echter geen toewijzing van de gebruikers/groepen over meerdere knooppunten. Gebruikers die op één host zijn gemaakt, stellen daarom niet standaard in kaart aan gebruikers die op een andere host zijn gemaakt. 

In het volgende `Host1` voorbeeld heeft drie`testuser01`bestaande `testuser02` `testuser03`testgebruikersaccounts : , 

![Resulterende configuratie voor NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

Houd `Host2`er rekening mee dat de gebruikersaccounts van de testniet zijn gemaakt, maar dat hetzelfde volume op beide hosts is gemonteerd:

![Resulterende configuratie voor NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Volgende stap 

[Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

