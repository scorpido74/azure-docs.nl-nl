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
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: b2e597ff8fc761b66de6228063c471933a364144
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449643"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Een NFS-client voor Azure NetApp Files configureren

De configuratie van de NFS-client die in dit artikel wordt beschreven, maakt deel uit van de installatie wanneer u [nfsv 4.1 Kerberos Encryption configureert](configure-kerberos-encryption.md) of [een Dual-protocol volume maakt](create-volumes-dual-protocol.md). Er zijn een groot aantal Linux-distributies beschikbaar voor gebruik met Azure NetApp Files. In dit artikel worden configuraties beschreven voor twee van de vaker gebruikte omgevingen: RHEL 8 en Ubuntu 18,04. 

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
In deze sectie wordt de Ubuntu-configuratie voor de NFS-clients beschreven.  

### <a name="if-you-are-using-nfsv41-kerberos-encryption"></a>Als u gebruikmaakt van NFSv 4.1 Kerberos-versleuteling 

1. Pakketten installeren:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Configureer de NTP-client.  
    Ubuntu 18,04 maakt `chrony` standaard gebruik van.  Volg de richt lijnen voor configuratie in [Ubuntu Bionic: using chrony om NTP te configureren](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Word lid van de Active Directory-domein:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

### <a name="if-you-are-using-dual-protocol"></a>Als u gebruikmaakt van Dual Protocol  

1. Voer de volgende opdracht uit om de geïnstalleerde pakketten bij te werken:  
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Voorbeeld:   

    `base dc=hariscus,dc=com` `uri ldap://10.20.0.4:389/`
    `ldap_version 3`
    `rootbinddn cn=admin,cn=Users,dc=hariscus,dc=com`
    `pam_password ad`
 
2. Voer de volgende opdracht uit om de service opnieuw te starten en in te scha kelen:   
    `sudo systemctl restart nscd && sudo systemctl enable nscd`

In het volgende voor beeld wordt een query uitgevoerd voor de AD LDAP-server van de Ubuntu LDAP-client voor een LDAP-gebruiker `ldapu1` :   

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="next-steps"></a>Volgende stappen  

* [Een NFS-volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Een volume met dubbele protocollen maken voor Azure NetApp Files](create-volumes-dual-protocol.md)

