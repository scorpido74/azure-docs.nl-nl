---
title: 'Azure Active Directory Domain Services: Een CentOS-VM toevoegen aan een beheerd domein | Microsoft Docs'
description: Een virtuele CentOS Linux-machine toevoegen aan Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: f28933623100ed18320df37741c7c1e82ccffa9f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612846"
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>Een virtuele CentOS Linux-machine toevoegen aan een beheerd domein
In dit artikel wordt beschreven hoe u een virtuele machine met CentOS linux in azure kunt koppelen aan een Azure AD Domain Services beheerd domein.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voordat u begint
Voor het uitvoeren van de taken die in dit artikel worden vermeld, hebt u het volgende nodig:
1. Een geldig **Azure-abonnement**.
2. Een **Azure AD-Directory** : gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
3. **Azure AD Domain Services** moet zijn ingeschakeld voor de Azure AD-adres lijst. Als u dit nog niet hebt gedaan, volgt u alle taken die in de aan de slag- [hand leiding](tutorial-create-instance.md)worden beschreven.
4. Zorg ervoor dat u de IP-adressen van het beheerde domein hebt geconfigureerd als de DNS-servers voor het virtuele netwerk. Zie [DNS-instellingen bijwerken voor het virtuele Azure-netwerk](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) voor meer informatie.
5. Voer de stappen uit die nodig zijn om [wacht woorden te synchroniseren met uw Azure AD Domain Services beheerde domein](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-a-centos-linux-virtual-machine"></a>Een virtuele machine met CentOS Linux inrichten
Gebruik een van de volgende methoden om een virtuele CentOS-machine in Azure in te richten:
* [Azure-portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure-CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implementeer de virtuele machine in **hetzelfde virtuele netwerk waarin u Azure AD Domain Services hebt ingeschakeld**.
> * Kies een **ander subnet** dan de naam waarin u Azure AD Domain Services hebt ingeschakeld.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Op afstand verbinding maken met de zojuist ingerichte virtuele Linux-machine
De virtuele CentOS-machine is ingericht in Azure. De volgende taak is om extern verbinding te maken met de virtuele machine met het lokale beheerders account dat is gemaakt tijdens het inrichten van de VM.

Volg de instructies in het artikel [hoe u zich kunt aanmelden bij een virtuele machine waarop Linux wordt uitgevoerd](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Het hosts-bestand op de virtuele Linux-machine configureren
Bewerk in uw SSH-terminal het bestand/etc/hosts-bestand en werk het IP-adres en de hostnaam van uw computer bij.

```console
sudo vi /etc/hosts
```

Voer in het bestand hosts de volgende waarde in:

```console
127.0.0.1 contoso-centos.contoso.com contoso-centos
```

Hier is ' contoso.com ' de DNS-domein naam van uw beheerde domein. Contoso-CentOS is de hostnaam van de virtuele CentOS-machine die u aan het beheerde domein toevoegt.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Vereiste pakketten installeren op de virtuele Linux-machine
Installeer vervolgens pakketten die vereist zijn voor domein deelname op de virtuele machine. Typ in de SSH-terminal de volgende opdracht om de vereiste pakketten te installeren:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>De virtuele Linux-machine toevoegen aan het beheerde domein
Nu de vereiste pakketten zijn geïnstalleerd op de virtuele Linux-machine, moet de volgende taak de virtuele machine toevoegen aan het beheerde domein.

1. Ontdek het door AAD Domain Services beheerde domein. Typ de volgende opdracht in uw SSH-terminal:

    ```console
    sudo realm discover contoso.COM
    ```

   > [!NOTE]
   > **Problemen oplossen:** Als de *realm detecteert* dat uw beheerde domein niet kan worden gevonden:  
   >    * Zorg ervoor dat het domein bereikbaar is vanaf de virtuele machine (probeer ping).  
   >    * Controleer of de virtuele machine inderdaad is geïmplementeerd in hetzelfde virtuele netwerk waarin het beheerde domein beschikbaar is.
   >    * Controleer of u de DNS-server instellingen voor het virtuele netwerk hebt bijgewerkt zodat deze verwijzen naar de domein controllers van het beheerde domein.  

2. Initialiseer Kerberos. Typ de volgende opdracht in uw SSH-terminal:

    > [!TIP]
    > * Geef een gebruiker op die lid is van de groep AAD DC Administrators. Voeg, indien nodig, [een gebruikers account toe aan een groep in azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)
    > * Geef de domein naam in hoofd letters op, anders kinit mislukt.

    ```console
    kinit bob@contoso.COM
    ```

3. Voeg de computer toe aan het domein. Typ de volgende opdracht in uw SSH-terminal:

    > [!TIP]
    > Gebruik hetzelfde gebruikers account dat u in de vorige stap hebt opgegeven (' kinit ').
    >
    > Als uw virtuele machine geen lid kan worden van het domein, moet u ervoor zorgen dat uitgaande Kerberos-verkeer op TCP + UDP-poort 464 naar het subnet van het virtuele netwerk voor uw door Azure AD DS beheerd domein is toegestaan voor de netwerk beveiligings groep van de virtuele machine.

    ```console
    sudo realm join --verbose contoso.COM -U 'bob@contoso.COM'
    ```

U moet een bericht ontvangen (de computer is geregistreerd bij realm) wanneer de computer is toegevoegd aan het beheerde domein.


## <a name="verify-domain-join"></a>Domein deelname verifiëren
Controleer of de computer is toegevoegd aan het beheerde domein. Maak verbinding met het domein dat is gekoppeld aan CentOS VM met een andere SSH-verbinding. Gebruik een domein gebruikers account en controleer of het gebruikers account correct is opgelost.

1. Typ in de SSH-terminal de volgende opdracht om verbinding te maken met het domein dat is gekoppeld aan de virtuele CentOS-machine via SSH. Gebruik een domein account dat tot het beheerde domein behoort (bijvoorbeeld 'bob@contoso.COM' in dit geval).
    
    ```console
    ssh -l bob@contoso.COM contoso-centos.contoso.com
    ```

2. Typ in de SSH-terminal de volgende opdracht om te controleren of de basismap juist is geïnitialiseerd.
   
    ```console
    pwd
    ```

3. Typ in de SSH-terminal de volgende opdracht om te controleren of de groepslid maatschappen correct worden omgezet.
    
    ```console
    id
    ```


## <a name="troubleshooting-domain-join"></a>Problemen met domein deelname oplossen
Raadpleeg het artikel [problemen oplossen van domein toevoegen](join-windows-vm.md#troubleshoot-domain-join-issues) .

## <a name="related-content"></a>Gerelateerde inhoud
* [Azure AD Domain Services aan de slag-hand leiding](tutorial-create-instance.md)
* [Een virtuele machine met Windows Server toevoegen aan een door Azure AD Domain Services beheerd domein](active-directory-ds-admin-guide-join-windows-vm.md)
* [Aanmelden bij een virtuele machine met Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Kerberos installeren](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows Integration Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
