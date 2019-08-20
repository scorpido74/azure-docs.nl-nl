---
title: 'Azure Active Directory Domain Services: Een Ubuntu-VM toevoegen aan een beheerd domein | Microsoft Docs'
description: Een Ubuntu Linux virtuele machine toevoegen aan Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: c782629d422eb8846b209fed7ab6b5a5c015de25
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612290"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Een virtuele Ubuntu-machine in azure toevoegen aan een beheerd domein
In dit artikel wordt beschreven hoe u een Ubuntu Linux virtuele machine kunt koppelen aan een Azure AD Domain Services beheerd domein.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voordat u begint
Voor het uitvoeren van de taken die in dit artikel worden vermeld, hebt u het volgende nodig:  
1. Een geldig **Azure-abonnement**.
2. Een **Azure AD-Directory** : gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
3. **Azure AD Domain Services** moet zijn ingeschakeld voor de Azure AD-adres lijst. Als u dit nog niet hebt gedaan, volgt u alle taken die in de aan de slag- [hand leiding](tutorial-create-instance.md)worden beschreven.
4. Zorg ervoor dat u de IP-adressen van het beheerde domein hebt geconfigureerd als de DNS-servers voor het virtuele netwerk. Zie [DNS-instellingen bijwerken voor het virtuele Azure-netwerk](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) voor meer informatie.
5. Voer de stappen uit die nodig zijn om [wacht woorden te synchroniseren met uw Azure AD Domain Services beheerde domein](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Een Ubuntu Linux virtuele machine inrichten
Gebruik een van de volgende methoden om een Ubuntu Linux virtuele machine in Azure in te richten:
* [Azure-portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure-CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implementeer de virtuele machine in **hetzelfde virtuele netwerk waarin u Azure AD Domain Services hebt ingeschakeld**.
> * Kies een **ander subnet** dan de naam waarin u Azure AD Domain Services hebt ingeschakeld.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Extern verbinding maken met de virtuele machine van Ubuntu Linux
De virtuele Ubuntu-machine is ingericht in Azure. De volgende taak is om extern verbinding te maken met de virtuele machine met het lokale beheerders account dat is gemaakt tijdens het inrichten van de VM.

Volg de instructies in het artikel [hoe u zich kunt aanmelden bij een virtuele machine waarop Linux wordt uitgevoerd](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Het hosts-bestand op de virtuele Linux-machine configureren
Bewerk in uw SSH-terminal het bestand/etc/hosts-bestand en werk het IP-adres en de hostnaam van uw computer bij.

```console
sudo vi /etc/hosts
```

Voer in het bestand hosts de volgende waarde in:

```console
127.0.0.1 contoso-ubuntu.contoso.com contoso-ubuntu
```

Hier is ' contoso.com ' de DNS-domein naam van uw beheerde domein. Contoso-Ubuntu is de hostnaam van de virtuele Ubuntu-machine die u aan het beheerde domein toevoegt.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Vereiste pakketten installeren op de virtuele Linux-machine
Installeer vervolgens pakketten die vereist zijn voor domein deelname op de virtuele machine. Voer de volgende stappen uit:

1.  Typ in de SSH-terminal de volgende opdracht om de pakket lijsten te downloaden uit de opslag plaatsen. Met deze opdracht worden de pakket lijsten bijgewerkt om informatie te krijgen over de nieuwste versies van pakketten en hun afhankelijkheden.

    ```console
    sudo apt-get update
    ```

2. Typ de volgende opdracht om de vereiste pakketten te installeren.

    ```console
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Tijdens de installatie van Kerberos wordt een roze scherm weer gegeven. De installatie van het pakket ' krb5-gebruiker ' vraagt naar de realm-naam (in alle hoofd letters). De installatie schrijft de secties [realm] en [domain_realm] in/etc/krb5.conf.

    > [!TIP]
    > Als de naam van uw beheerde domein contoso.com is, voert u contoso.COM in als de realm. Houd er rekening mee dat de realm-naam in hoofd letters moet worden opgegeven.


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Configureer de NTP-instellingen (Network Time Protocol) op de virtuele Linux-machine
De datum en tijd van uw Ubuntu-VM moeten worden gesynchroniseerd met het beheerde domein. Voeg de NTP-hostnaam van uw beheerde domein toe aan het/etc/ntp.conf-bestand.

```console
sudo vi /etc/ntp.conf
```

Voer in het bestand ntp. conf de volgende waarde in en sla het bestand op:

```console
server contoso.com
```

Hier is ' contoso.com ' de DNS-domein naam van uw beheerde domein.

Synchroniseer nu de datum en tijd van de Ubuntu-VM met de NTP-server en start vervolgens de NTP-service:

```console
sudo systemctl stop ntp
sudo ntpdate contoso.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>De virtuele Linux-machine toevoegen aan het beheerde domein
Nu de vereiste pakketten zijn geïnstalleerd op de virtuele Linux-machine, moet de volgende taak de virtuele machine toevoegen aan het beheerde domein.

1. Ontdek het door AAD Domain Services beheerde domein. Typ de volgende opdracht in uw SSH-terminal:

    ```console
    sudo realm discover contoso.COM
    ```

   > [!NOTE]
   > **Problemen oplossen:** Als de *realm detecteert* dat uw beheerde domein niet kan worden gevonden:
   >   * Zorg ervoor dat het domein bereikbaar is vanaf de virtuele machine (probeer ping).
   >   * Controleer of de virtuele machine inderdaad is geïmplementeerd in hetzelfde virtuele netwerk waarin het beheerde domein beschikbaar is.
   >   * Controleer of u de DNS-server instellingen voor het virtuele netwerk hebt bijgewerkt zodat deze verwijzen naar de domein controllers van het beheerde domein.

2. Initialiseer Kerberos. Typ de volgende opdracht in uw SSH-terminal:

    > [!TIP]
    > * Zorg ervoor dat u een gebruiker opgeeft die deel uitmaakt van de groep AAD DC Administrators. Voeg, indien nodig, [een gebruikers account toe aan een groep in azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)
    > * Geef de domein naam in hoofd letters op, anders kinit mislukt.
    >

    ```console
    kinit bob@contoso.COM
    ```

3. Voeg de computer toe aan het domein. Typ de volgende opdracht in uw SSH-terminal:

    > [!TIP]
    > Gebruik hetzelfde gebruikers account dat u in de vorige stap hebt opgegeven (' kinit ').
    >
    > Als uw virtuele machine geen lid kan worden van het domein, moet u ervoor zorgen dat uitgaande Kerberos-verkeer op TCP + UDP-poort 464 naar het subnet van het virtuele netwerk voor uw door Azure AD DS beheerd domein is toegestaan voor de netwerk beveiligings groep van de virtuele machine.

    ```console
    sudo realm join --verbose contoso.COM -U 'bob@contoso.COM' --install=/
    ```

U moet een bericht ontvangen (de computer is geregistreerd bij realm) wanneer de computer is toegevoegd aan het beheerde domein.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>De SSSD-configuratie bijwerken en de service opnieuw starten
1. Typ de volgende opdracht in uw SSH-terminal. Open het bestand SSSD. conf en breng de volgende wijziging aan
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

2. Commentaar bij de regel **use_fully_qualified_names = True** en sla het bestand op.
    
    ```console
    # use_fully_qualified_names = True
    ```

3. Start de SSSD-service opnieuw.
    
    ```console
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Automatisch maken van basis mappen configureren
Als u het automatisch maken van de basismap wilt inschakelen na het aanmelden van gebruikers, typt u de volgende opdrachten in de PuTTy-terminal:

```console
sudo vi /etc/pam.d/common-session
```

Voeg de volgende regel in dit bestand onder de regel ' sessie optioneel pam_sss. so ' toe en sla deze op:

```console
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Domein deelname verifiëren
Controleer of de computer is toegevoegd aan het beheerde domein. Maak verbinding met het domein dat is gekoppeld aan Ubuntu VM met een andere SSH-verbinding. Gebruik een domein gebruikers account en controleer of het gebruikers account correct is opgelost.

1. Typ in de SSH-terminal de volgende opdracht om verbinding te maken met het domein dat is gekoppeld aan de virtuele Ubuntu-machine via SSH. Gebruik een domein account dat tot het beheerde domein behoort (bijvoorbeeld 'bob@contoso.COM' in dit geval).
    
    ```console
    ssh -l bob@contoso.COM contoso-ubuntu.contoso.com
    ```

2. Typ in de SSH-terminal de volgende opdracht om te controleren of de basismap juist is geïnitialiseerd.
    
    ```console
    pwd
    ```

3. Typ in de SSH-terminal de volgende opdracht om te controleren of de groepslid maatschappen correct worden omgezet.
    
    ```console
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>De groep ' AAD DC Administrators ' sudo privileges verlenen
U kunt leden van de groep ' AAD DC Administrators ' beheerders bevoegdheden verlenen op de Ubuntu-VM. Het sudo-bestand bevindt zich op/etc/sudoers. De leden van AD-groepen die zijn toegevoegd in sudo kunnen sudo uitvoeren.

1. Zorg ervoor dat u bent aangemeld met de bevoegdheden van uw SSH-terminal. U kunt het lokale beheerders account dat u hebt opgegeven tijdens het maken van de virtuele machine gebruiken. Voer de volgende opdracht uit:
    
    ```console
    sudo vi /etc/sudoers
    ```

2. Voeg de volgende vermelding toe aan het/etc/sudoers-bestand en sla het op:
    
    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. U kunt zich nu aanmelden als lid van de groep AAD DC Administrators en moet beschikken over beheerders bevoegdheden voor de virtuele machine.


## <a name="troubleshooting-domain-join"></a>Problemen met domein deelname oplossen
Raadpleeg het artikel [problemen oplossen van domein toevoegen](join-windows-vm.md#troubleshoot-domain-join-issues) .


## <a name="related-content"></a>Gerelateerde inhoud
* [Azure AD Domain Services aan de slag-hand leiding](tutorial-create-instance.md)
* [Een virtuele machine met Windows Server toevoegen aan een door Azure AD Domain Services beheerd domein](active-directory-ds-admin-guide-join-windows-vm.md)
* [Aanmelden bij een virtuele machine met Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
