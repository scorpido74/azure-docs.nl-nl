---
title: 'Azure Active Directory Domain Services: Lid worden van een CoreOS Linux-VM | Microsoft Docs'
description: Een virtuele CoreOS Linux-machine toevoegen aan Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: c1f3d1ec7bb9e9f449cea3f9aa36ca8f80348c6e
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612810"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Een virtuele CoreOS Linux-machine toevoegen aan een beheerd domein
In dit artikel wordt beschreven hoe u een virtuele machine met CoreOS Linux in azure kunt koppelen aan een Azure AD Domain Services beheerd domein.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voordat u begint
Voor het uitvoeren van de taken die in dit artikel worden vermeld, hebt u het volgende nodig:
1. Een geldig **Azure-abonnement**.
2. Een **Azure AD-Directory** : gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
3. **Azure AD Domain Services** moet zijn ingeschakeld voor de Azure AD-adres lijst. Als u dit nog niet hebt gedaan, volgt u alle taken die in de aan de slag- [hand leiding](tutorial-create-instance.md)worden beschreven.
4. Zorg ervoor dat u de IP-adressen van het beheerde domein hebt geconfigureerd als de DNS-servers voor het virtuele netwerk. Zie [DNS-instellingen bijwerken voor het virtuele Azure-netwerk](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) voor meer informatie.
5. Voer de stappen uit die nodig zijn om [wacht woorden te synchroniseren met uw Azure AD Domain Services beheerde domein](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Een virtuele machine met CoreOS Linux inrichten
Gebruik een van de volgende methoden om een virtuele CoreOS-machine in Azure in te richten:
* [Azure-portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure-CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

In dit artikel wordt de installatie kopie van de virtuele machine **CoreOS Linux (stabiele)** in azure gebruikt.

> [!IMPORTANT]
> * Implementeer de virtuele machine in **hetzelfde virtuele netwerk waarin u Azure AD Domain Services hebt ingeschakeld**.
> * Kies een **ander subnet** dan de naam waarin u Azure AD Domain Services hebt ingeschakeld.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Op afstand verbinding maken met de zojuist ingerichte virtuele Linux-machine
De virtuele CoreOS-machine is ingericht in Azure. De volgende taak is om extern verbinding te maken met de virtuele machine met het lokale beheerders account dat is gemaakt tijdens het inrichten van de VM.

Volg de instructies in het artikel [hoe u zich kunt aanmelden bij een virtuele machine waarop Linux wordt uitgevoerd](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Het hosts-bestand op de virtuele Linux-machine configureren
Bewerk in uw SSH-terminal het bestand/etc/hosts-bestand en werk het IP-adres en de hostnaam van uw computer bij.

```console
sudo vi /etc/hosts
```

Voer in het bestand hosts de volgende waarde in:

```console
127.0.0.1 contoso-coreos.contoso.com contoso-coreos
```

Hier is ' contoso.com ' de DNS-domein naam van uw beheerde domein. Contoso-coreos is de hostnaam van de virtuele CoreOS-machine die u aan het beheerde domein toevoegt.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>De SSSD-service configureren op de virtuele Linux-machine
Werk vervolgens uw SSSD-configuratie bestand in ('/etc/SSSD/SSSD.conf '), zodat dit overeenkomt met het volgende voor beeld:

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = contoso.COM

[domain/contoso.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://contoso.com
ldap_search_base = dc=contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = contoso.com
krb5_realm = contoso.COM
```

Vervang contoso. COM ' door de DNS-domein naam van uw beheerde domein. Zorg ervoor dat u de domein naam in hoofd letters in het bestand conf opgeeft.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>De virtuele Linux-machine toevoegen aan het beheerde domein
Nu de vereiste pakketten zijn geïnstalleerd op de virtuele Linux-machine, moet de volgende taak de virtuele machine toevoegen aan het beheerde domein.

```console
sudo adcli join -D contoso.COM -U bob@contoso.COM -K /etc/krb5.keytab -H contoso-coreos.contoso.com -N coreos
```


> [!NOTE]
> **Problemen oplossen:** Als *adcli* uw beheerde domein niet kan vinden:
>   * Zorg ervoor dat het domein bereikbaar is vanaf de virtuele machine (probeer ping).
>   * Controleer of de virtuele machine inderdaad is geïmplementeerd in hetzelfde virtuele netwerk waarin het beheerde domein beschikbaar is.
>   * Controleer of u de DNS-server instellingen voor het virtuele netwerk hebt bijgewerkt zodat deze verwijzen naar de domein controllers van het beheerde domein.

Start de SSSD-service. Typ de volgende opdracht in uw SSH-terminal:
  
```console
sudo systemctl start sssd.service
```


## <a name="verify-domain-join"></a>Domein deelname verifiëren
Controleer of de computer is toegevoegd aan het beheerde domein. Maak verbinding met het domein dat is gekoppeld aan CoreOS VM met een andere SSH-verbinding. Gebruik een domein gebruikers account en controleer of het gebruikers account correct is opgelost.

1. Typ in de SSH-terminal de volgende opdracht om verbinding te maken met het domein dat is gekoppeld aan de virtuele CoreOS-machine via SSH. Gebruik een domein account dat tot het beheerde domein behoort (bijvoorbeeld 'bob@contoso.COM' in dit geval).
    
    ```console
    ssh -l bob@contoso.COM contoso-coreos.contoso.com
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
