---
title: Deelnemen aan een CentOS VM voor Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over het configureren en aansluiten van een virtuele centoslinuxmachine van een beheerd Azure AD Domain Services-domein.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: d08552dcae51c897f2419d94e5e61e857247f09a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655237"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Word lid van een virtuele centos linux-machine naar een beheerd Azure AD Domain Services-domein

Als u gebruikers wilt laten inloggen op virtuele machines (VM's) in Azure met één set referenties, u VM's toevoegen aan een beheerd beheerd domein van Azure Active Directory Domain Services (AD DS). Wanneer u lid wordt van een VM in een door Azure AD DS beheerd domein, kunnen gebruikersaccounts en referenties uit het domein worden gebruikt om u aan te melden en servers te beheren. Groepslidmaatschappen uit het beheerde Azure AD DS-domein worden ook toegepast om u toegang te geven tot bestanden of services op de VM.

In dit artikel ziet u hoe u lid worden van een CentOS Linux VM in een door Azure AD DS beheerd domein.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Indien nodig maakt [en configureert][create-azure-ad-ds-instance]de eerste zelfstudie een Azure Active Directory Domain Services-exemplaar .
* Een gebruikersaccount dat deel uitmaakt van het door Azure AD DS beheerde domein.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>Maak en maak verbinding met een CentOS Linux VM

Als u een bestaande CentOS Linux VM in Azure hebt, maakt u verbinding met deze met SSH en gaat u verder naar de volgende stap om [te beginnen met het configureren van de VM.](#configure-the-hosts-file)

Als u een CentOS Linux VM wilt maken of een test-VM wilt maken voor gebruik met dit artikel, u een van de volgende methoden gebruiken:

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
* *centos* is de hostnaam van uw CentOS VM die u aansluit bij het beheerde domein.

Werk deze namen bij met uw eigen waarden:

```console
127.0.0.1 centos.aaddscontoso.com centos
```

Sla het *hosts-bestand* op en `:wq` sluit deze af met de opdracht van de editor.

## <a name="install-required-packages"></a>De vereiste pakketten installeren

De VM heeft een aantal extra pakketten nodig om de VM toe te voegen aan het beheerde Azure AD DS-domein. Als u deze pakketten wilt installeren en configureren, `yum`werkt u de hulpprogramma's voor domeinverbinding bij en installeert u deze met :

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Lid worden van VM naar het beheerde domein

Nu de vereiste pakketten op de VM zijn geïnstalleerd, wordt u lid van de VM naar het beheerde Azure AD DS-domein.

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
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Het duurt even voordat u lid wordt van de VM naar het beheerde Azure AD DS-domein. In de volgende voorbeelduitvoerwordt weergegeven dat de VM is verbonden met het beheerde Azure AD DS-domein:

```output
Successfully enrolled machine in realm
```

Als uw VM het domeinjoinproces niet kan voltooien, controleert u of de netwerkbeveiligingsgroep van de VM uitgaand Kerberos-verkeer op TCP + UDP-poort 464 toestaat naar het subnet van het virtuele netwerk voor uw door Azure AD DS beheerde domein.

## <a name="allow-password-authentication-for-ssh"></a>Wachtwoordverificatie voor SSH toestaan

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
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>De sudo-sudo-rechten voor de groep 'AAD DC Administrators' verlenen

Als u leden van de aad *dc-beheerdersgroep* beheerdersrechten wilt verlenen op de CentOS-vm, voegt u een vermelding toe aan de */etc/sudoers*. Na de toegevoegde opdracht kunnen leden `sudo` van de groep *AAD DC-beheerders* de opdracht op de CentOS-vm gebruiken.

1. Open het *bestand sudoers* om te bewerken:

    ```console
    sudo visudo
    ```

1. Voeg de volgende vermelding toe aan het bestand einde van */etc/sudoers.* De groep *AAD DC-beheerders* bevat witruimte in de naam, dus neem het backslash-escape-teken op in de groepsnaam. Voeg uw eigen domeinnaam toe, zoals *aaddscontoso.com:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Sla de editor op en `:wq` verlaat deze met de opdracht van de editor.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Aanmelden bij de VM met een domeinaccount

Als u wilt controleren of de VM is verbonden met het beheerde Azure AD DS-domein, start u een nieuwe SSH-verbinding met een domeingebruikersaccount. Controleer of er een thuismap is gemaakt en dat groepslidmaatschap van het domein wordt toegepast.

1. Maak een nieuwe SSH-verbinding vanaf uw console. Gebruik een domeinaccount dat tot het `ssh -l` beheerde `contosoadmin@aaddscontoso.com` domein behoort met behulp van de opdracht, zoals en voer vervolgens het adres van uw vm in, zoals *centos.aaddscontoso.com.* Als u de Azure Cloud Shell gebruikt, gebruikt u het openbare IP-adres van de vm in plaats van de interne DNS-naam.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
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
    sudo yum update
    ```

## <a name="next-steps"></a>Volgende stappen

Zie Problemen met het koppelen van [domeinen oplossen](join-windows-vm.md#troubleshoot-domain-join-issues)als u problemen hebt met het koppelen van de vm met het beheerde Azure AD DS-domein of als u zich aanmeldt met een domeinaccount.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
