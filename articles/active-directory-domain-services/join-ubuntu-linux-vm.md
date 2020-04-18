---
title: Deelnemen aan een Ubuntu-vm voor Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over het configureren en aansluiten van een virtuele Ubuntu Linux-machine bij een beheerd Azure AD Domain Services-domein.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 74af841b777494744c72ed219bacd3b3835d41ac
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617554"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Word lid van een virtuele ubuntu Linux-machine naar een beheerd Azure AD Domain Services-domein

Als u gebruikers wilt laten inloggen op virtuele machines (VM's) in Azure met één set referenties, u VM's toevoegen aan een beheerd beheerd domein van Azure Active Directory Domain Services (AD DS). Wanneer u lid wordt van een VM in een door Azure AD DS beheerd domein, kunnen gebruikersaccounts en referenties uit het domein worden gebruikt om u aan te melden en servers te beheren. Groepslidmaatschappen uit het beheerde Azure AD DS-domein worden ook toegepast om u toegang te geven tot bestanden of services op de VM.

In dit artikel ziet u hoe u een Ubuntu Linux VM deelnemen aan een door Azure AD DS beheerd domein.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Indien nodig maakt [en configureert][create-azure-ad-ds-instance]de eerste zelfstudie een Azure Active Directory Domain Services-exemplaar .
* Een gebruikersaccount dat deel uitmaakt van het door Azure AD DS beheerde domein.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Maak en maak verbinding met een Ubuntu Linux VM

Als u een bestaande Ubuntu Linux VM in Azure hebt, maakt u verbinding met deze met SSH en gaat u verder naar de volgende stap om [te beginnen met het configureren van de VM.](#configure-the-hosts-file)

Als u een Ubuntu Linux VM wilt maken of een test-VM wilt maken voor gebruik met dit artikel, u een van de volgende methoden gebruiken:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure-CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Wanneer u de VM maakt, moet u opletten dat de virtuele netwerkinstellingen kunnen communiceren met het beheerde Azure AD DS-domein:

* Implementeer de VM in hetzelfde of een virtueel netwerk met peered waarin u Azure AD Domain Services hebt ingeschakeld.
* Implementeer de VM in een ander subnet dan uw exemplaar Azure AD Domain Services.

Zodra de VM is geïmplementeerd, volgt u de stappen om verbinding te maken met de VM met Behulp van SSH.

## <a name="configure-the-hosts-file"></a>Het hosts-bestand configureren

Als u ervoor wilt zorgen dat de VM-hostnaam correct is geconfigureerd voor het beheerde domein, bewerkt u het *bestand /etc/hosts* en stelt u de hostnaam in:

```console
sudo vi /etc/hosts
```

Werk in het *hosts-bestand* het *localhost-adres* bij. In het volgende voorbeeld:

* *aaddscontoso.com* is de DNS-domeinnaam van uw door Azure AD DS beheerde domein.
* *ubuntu* is de hostnaam van uw Ubuntu VM die u aansluit bij het beheerde domein.

Werk deze namen bij met uw eigen waarden:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

Sla het *hosts-bestand* op en `:wq` sluit deze af met de opdracht van de editor.

## <a name="install-required-packages"></a>De vereiste pakketten installeren

De VM heeft een aantal extra pakketten nodig om de VM toe te voegen aan het beheerde Azure AD DS-domein. Als u deze pakketten wilt installeren en configureren, werkt u de hulpprogramma's voor domeinverbinding bij en installeert u deze`apt-get`

Tijdens de Kerberos-installatie vraagt het *krb5-gebruikerspakket* om de naam van het rijk in ALLE HOOFDLETTERS. Als de naam van uw door Azure AD DS beheerde domein bijvoorbeeld *aaddscontoso.com*is, voert u *AADDSCONTOSO.COM* in als realm. De installatie `[realm]` schrijft `[domain_realm]` de en secties in */etc/krb5.conf* configuratiebestand. Zorg ervoor dat u de realm een ALLE HOOFDLETTERs opgeeft:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Network Time Protocol (NTP) configureren

Om domeincommunicatie correct te laten werken, moeten de datum en tijd van uw Ubuntu VM worden gesynchroniseerd met het door Azure AD DS beheerde domein. Voeg de NTP-hostnaam van uw Azure AD DS-beheerde domein toe aan het bestand */etc/ntp.conf.*

1. Open het *ntp.conf-bestand* met een editor:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. Maak in het *ntp.conf-bestand* een regel om de DNS-naam van uw Azure AD DS-beheerde domein toe te voegen. In het volgende voorbeeld wordt een vermelding voor *aaddscontoso.com* toegevoegd. Gebruik uw eigen DNS-naam:

    ```console
    server aaddscontoso.com
    ```

    Sla het *ntp.conf-bestand* op en `:wq` sluit deze af met de opdracht van de editor.

1. Om ervoor te zorgen dat de VM wordt gesynchroniseerd met het door Azure AD DS beheerde domein, zijn de volgende stappen nodig:

    * De NTP-server stoppen
    * De datum en tijd van het beheerde domein bijwerken
    * De NTP-service starten

    Voer de volgende opdrachten uit om deze stappen uit te voeren. Gebruik uw eigen DNS-naam met de `ntpdate` opdracht:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Lid worden van VM naar het beheerde domein

Nu de vereiste pakketten zijn geïnstalleerd op de VM en NTP is geconfigureerd, u lid worden van de VM naar het beheerde Azure AD DS-domein.

1. Gebruik `realm discover` de opdracht om het beheerde Azure AD DS-domein te ontdekken. In het volgende voorbeeld wordt het *AADDSCONTOSO.COM*ontdekt. Geef uw eigen Azure AD DS-beheerde domeinnaam op in ALLE HOOFDLETTERS:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Als `realm discover` de opdracht uw door Azure AD DS beheerde domein niet kan vinden, controleert u de volgende stappen voor het oplossen van problemen:

    * Zorg ervoor dat het domein bereikbaar is via de VM. Probeer `ping aaddscontoso.com` te zien of een positief antwoord wordt geretourneerd.
    * Controleer of de VM is geïmplementeerd op hetzelfde of een virtueel netwerk met peered waarin het beheerde Azure AD DS-beheerde domein beschikbaar is.
    * Controleer of de DNS-serverinstellingen voor het virtuele netwerk zijn bijgewerkt om te wijzen op de domeincontrollers van het beheerde Azure AD DS-domein.

1. Nu kerberos initialiseren `kinit` met behulp van de opdracht. Geef een gebruiker op die deel uitmaakt van het beheerde Azure AD DS-domein. Voeg indien nodig [een gebruikersaccount toe aan een groep in Azure AD.](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)

    Nogmaals, de door Azure AD DS beheerde domeinnaam moet in ALLE HOOFDLETTERS worden ingevoerd. In het volgende voorbeeld `contosoadmin@aaddscontoso.com` wordt het account met de naam gebruikt om Kerberos te initialiseren. Voer uw eigen gebruikersaccount in dat deel uitmaakt van het beheerde Azure AD DS-domein:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Sluit u ten slotte met de `realm join` opdracht aan bij het door Azure AD DS beheerde domein. Gebruik hetzelfde gebruikersaccount dat deel uitmaakt van het door Azure AD `kinit` DS beheerde domein dat u in de vorige opdracht hebt opgegeven, zoals `contosoadmin@AADDSCONTOSO.COM`:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

Het duurt even voordat u lid wordt van de VM naar het beheerde Azure AD DS-domein. In de volgende voorbeelduitvoerwordt weergegeven dat de VM is verbonden met het beheerde Azure AD DS-domein:

```output
Successfully enrolled machine in realm
```

Als uw VM het domeinjoinproces niet kan voltooien, controleert u of de netwerkbeveiligingsgroep van de VM uitgaand Kerberos-verkeer op TCP + UDP-poort 464 toestaat naar het subnet van het virtuele netwerk voor uw door Azure AD DS beheerde domein.

Als u de fout hebt ontvangen *die niet is opgegeven, is gss-fout.  Kleine code kan meer informatie opleveren (Server niet gevonden in Kerberos database),* open het bestand `[libdefaults]` */etc/krb5.conf* en voeg de volgende code toe in sectie en probeer het opnieuw:

```console
rdns=false
```

## <a name="update-the-sssd-configuration"></a>De SSSD-configuratie bijwerken

Een van de pakketten geïnstalleerd in een vorige stap was voor System Security Services Daemon (SSSD). Wanneer een gebruiker zich probeert aan te melden bij een vm met domeinreferenties, stuurt SSSD het verzoek door naar een verificatieprovider. In dit scenario gebruikt SSSD Azure AD DS om de aanvraag te verifiëren.

1. Open het *bestand ssd.conf* met een editor:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Reageer uit de lijn voor *use_fully_qualified_names* als volgt:

    ```console
    # use_fully_qualified_names = True
    ```

    Wanneer u klaar bent, slaat u het `:wq` *bestand sssd.conf op* en sluit u deze af met de opdracht van de editor.

1. Als u de wijziging wilt toepassen, start u de SSSD-service opnieuw:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>Gebruikersaccount- en groepsinstellingen configureren

Als de VM is gekoppeld aan het beheerde Azure AD DS-domein en geconfigureerd voor verificatie, zijn er een aantal gebruikersconfiguratieopties die moeten worden voltooid. Deze configuratiewijzigingen omvatten het toestaan van verificatie op basis van wachtwoorden en het automatisch maken van thuismappen op de lokale VM wanneer domeingebruikers zich voor het eerst aanmelden.

### <a name="allow-password-authentication-for-ssh"></a>Wachtwoordverificatie voor SSH toestaan

Standaard kunnen gebruikers zich alleen aanmelden bij een vm met ssh-verificatie op basis van openbare sleutels. Verificatie op basis van wachtwoorden mislukt. Wanneer u lid wordt van de VM naar een door Azure AD DS beheerd domein, moeten deze domeinaccounts verificatie op basis van wachtwoorden gebruiken. Werk de SSH-configuratie bij om verificatie op basis van wachtwoorden als volgt toe te staan.

1. Open het *sshd_conf* bestand met een editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Werk de regel voor *PasswordAuthentication* bij naar *ja:*

    ```console
    PasswordAuthentication yes
    ```

    Sla het *sshd_conf-bestand* op en `:wq` sluit deze af met de opdracht van de editor.

1. Als u de wijzigingen wilt toepassen en gebruikers wilt laten inloggen met een wachtwoord, start u de SSH-service opnieuw:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Automatische aanmaaklijst maken configureren

Voer de volgende stappen uit om de automatische aanmaaklijst van de thuismap in te schakelen wanneer een gebruiker zich voor het eerst aanmeldt:

1. Open het *bestand /etc/pam.d/common-session* in een editor:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Voeg de volgende regel toe `session optional pam_sss.so`in dit bestand onder de regel:

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    Sla het *bestand met algemene sessies* op `:wq` en sluit deze af met de opdracht van de editor.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>De sudo-sudo-rechten voor de groep 'AAD DC Administrators' verlenen

Als u leden van de aad *dc-beheerdersgroep* beheerdersrechten op de Ubuntu-VM wilt toekennen, voegt u een vermelding toe aan de */etc/sudoers*. Eenmaal toegevoegd, kunnen leden van de `sudo` groep AAD DC *Administrators* de opdracht op de Ubuntu VM gebruiken.

1. Open het *bestand sudoers* om te bewerken:

    ```console
    sudo visudo
    ```

1. Voeg de volgende vermelding toe aan het bestand einde van */etc/sudoers:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    Sla de editor op en `Ctrl-X` sluit deze af met de opdracht.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Aanmelden bij de VM met een domeinaccount

Als u wilt controleren of de VM is verbonden met het beheerde Azure AD DS-domein, start u een nieuwe SSH-verbinding met een domeingebruikersaccount. Controleer of er een thuismap is gemaakt en dat groepslidmaatschap van het domein wordt toegepast.

1. Maak een nieuwe SSH-verbinding vanaf uw console. Gebruik een domeinaccount dat tot het `ssh -l` beheerde `contosoadmin@aaddscontoso.com` domein behoort met behulp van de opdracht, zoals en voer vervolgens het adres van uw vm in, zoals *ubuntu.aaddscontoso.com.* Als u de Azure Cloud Shell gebruikt, gebruikt u het openbare IP-adres van de vm in plaats van de interne DNS-naam.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
    ```

1. Wanneer u verbinding hebt gemaakt met de vm, controleert u of de startmap correct is geïnitialiseerd:

    ```console
    pwd
    ```

    U moet zich in de */home* directory bevinden met uw eigen directory die overeenkomt met het gebruikersaccount.

1. Controleer nu of de groepslidmaatschappen correct worden opgelost:

    ```console
    id
    ```

    U moet uw groepslidmaatschappen zien vanuit het beheerde Azure AD DS-domein.

1. Als u zich als lid van de groep *AAD DC-beheerders* bij de `sudo` VM hebt aangemeld, controleert u of u de opdracht correct gebruiken:

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>Volgende stappen

Zie Problemen met het koppelen van [domeinen oplossen](join-windows-vm.md#troubleshoot-domain-join-issues)als u problemen hebt met het koppelen van de vm met het beheerde Azure AD DS-domein of als u zich aanmeldt met een domeinaccount.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
