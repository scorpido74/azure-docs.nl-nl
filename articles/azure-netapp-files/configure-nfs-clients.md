---
title: Een NFS-client configureren voor Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u NFS-clients configureert voor gebruik met Azure NetApp Files.
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
ms.date: 08/19/2020
ms.author: b-juche
ms.openlocfilehash: 4a54858f4a09cd62bb555a6df2c12efa2290dd69
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653168"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Een NFS-client voor Azure NetApp Files configureren

Er zijn een groot aantal Linux-distributies beschikbaar voor gebruik met Azure NetApp Files. In dit artikel worden configuraties beschreven voor twee van de vaker gebruikte omgevingen: RHEL 8 en Ubuntu 18,04. De configuratie van de NFS-client die in dit artikel wordt beschreven, maakt deel uit van de installatie wanneer u [nfsv 4.1 Kerberos Encryption configureert](configure-kerberos-encryption.md) of [een Dual-protocol volume maakt](create-volumes-dual-protocol.md).  

Ongeacht de Linux-smaak die u gebruikt, zijn de volgende configuraties vereist:
* Configureer een NTP-client om problemen met tijd verschil te voor komen.
* Configureer DNS-vermeldingen van de Linux-client voor naam omzetting.  
    Deze configuratie omvat de A-record (voorwaarts) en de PTR-record (omgekeerd). 
* Maak voor domein deelname een computer account in het doel Active Directory (dat wordt gemaakt tijdens de realm-koppelings opdracht). 
    > [!NOTE] 
    > De `$SERVICEACCOUNT` variabele die in de onderstaande opdrachten wordt gebruikt, moet een gebruikers account met machtigingen of delegering zijn om een computer account te maken in de doel organisatie-eenheid.
* Schakel de client in om NFS-volumes en andere relevante controle hulpprogramma's te koppelen.

## <a name="rhel-8-configuration"></a>RHEL 8-configuratie 

1. Pakketten installeren:   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. Configureer de NTP-client:  
    RHEL 8 maakt `chrony` standaard gebruik van.  Volg de richt lijnen voor configuratie in [het gebruik van de chrony Suite voor het configureren van NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

3. Word lid van het domein Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Ubuntu-configuratie 

1. Pakketten installeren:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Configureer de NTP-client.  
    Ubuntu 18,04 maakt `chrony` standaard gebruik van.  Volg de richt lijnen voor configuratie in [Ubuntu Bionic: using chrony om NTP te configureren](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Word lid van de Active Directory-domein:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="next-steps"></a>Volgende stappen  

* [Een NFS-volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Een volume met dubbele protocollen maken voor Azure NetApp Files](create-volumes-dual-protocol.md)

