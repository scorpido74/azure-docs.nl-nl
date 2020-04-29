---
title: Een CentOS-VM toevoegen aan Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het configureren en toevoegen van een virtuele CentOS Linux-machine aan een Azure AD Domain Services beheerd domein.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655237"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Een virtuele CentOS Linux-machine toevoegen aan een door Azure AD Domain Services beheerd domein

Als u wilt dat gebruikers zich met één set referenties aanmelden bij virtuele machines (Vm's) in azure, kunt u Vm's toevoegen aan een door Azure Active Directory Domain Services (AD DS) beheerd domein. Wanneer u een virtuele machine koppelt aan een door Azure AD DS beheerd domein, kunnen gebruikers accounts en referenties van het domein worden gebruikt voor het aanmelden en beheren van servers. Groepslid maatschappen van het door Azure AD DS beheerde domein worden ook toegepast om u de toegang tot bestanden of services op de virtuele machine te kunnen beheren.

In dit artikel wordt beschreven hoe u een CentOS Linux-VM kunt koppelen aan een door Azure AD DS beheerd domein.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources en bevoegdheden nodig om deze zelf studie te volt ooien:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory Tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
    * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een Azure Active Directory Domain Services beheerd domein ingeschakeld en geconfigureerd in uw Azure AD-Tenant.
    * Als dat nodig is, [maakt en configureert][create-azure-ad-ds-instance]de eerste zelf studie een Azure Active Directory Domain Services-exemplaar.
* Een gebruikers account dat deel uitmaakt van het door Azure AD DS beheerde domein.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>Maken en verbinding maken met een CentOS Linux-VM

Als u een bestaande virtuele machine met CentOS linux in azure hebt, kunt u er verbinding mee maken via SSH. Ga vervolgens verder met de volgende stap om te beginnen met het [configureren van de virtuele machine](#configure-the-hosts-file).

Als u een virtuele machine met CentOS Linux wilt maken of een test-VM wilt maken voor gebruik met dit artikel, kunt u een van de volgende methoden gebruiken:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure-CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Wanneer u de virtuele machine maakt, moet u aandacht best Eden aan de instellingen voor virtueel netwerk om ervoor te zorgen dat de virtuele machine kan communiceren met het door Azure AD DS beheerde domein:

* Implementeer de virtuele machine in dezelfde of een peered virtueel netwerk waarin u Azure AD Domain Services hebt ingeschakeld.
* Implementeer de virtuele machine in een ander subnet dan uw Azure AD Domain Services-exemplaar.

Wanneer de VM is geïmplementeerd, volgt u de stappen om via SSH verbinding te maken met de VM.

## <a name="configure-the-hosts-file"></a>Het hosts-bestand configureren

Om ervoor te zorgen dat de hostnaam van de virtuele machine correct is geconfigureerd voor het beheerde domein, bewerkt u het bestand *bestand/etc/hosts* en stelt u de hostnaam in:

```console
sudo vi /etc/hosts
```

Werk in het bestand *hosts* het *localhost* -adres bij. In het volgende voorbeeld:

* *aaddscontoso.com* is de DNS-domein naam van uw door Azure AD DS beheerde domein.
* *CentOS* is de hostnaam van de CENTOS-VM die u aan het beheerde domein toevoegt.

Werk deze namen bij met uw eigen waarden:

```console
127.0.0.1 centos.aaddscontoso.com centos
```

Als u klaar bent, slaat u het *hosts* -bestand `:wq` op en sluit u het af met de opdracht van de editor.

## <a name="install-required-packages"></a>De vereiste pakketten installeren

De VM moet enkele extra pakketten hebben om de virtuele machine toe te voegen aan het door Azure AD DS beheerde domein. Als u deze pakketten wilt installeren en configureren, moet u de hulpprogram ma's voor domein `yum`deelname bijwerken en installeren met:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Virtuele machine toevoegen aan het beheerde domein

Nu de vereiste pakketten zijn geïnstalleerd op de VM, voegt u de virtuele machine toe aan het beheerde domein van Azure AD DS.

1. Gebruik de `realm discover` opdracht om het beheerde domein van Azure AD DS te detecteren. In het volgende voor beeld wordt de realm- *AADDSCONTOSO.com*gedetecteerd. Geef in alle hoofd letters uw eigen Azure AD DS beheerde domein naam op:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Als de `realm discover` opdracht uw door Azure AD DS beheerde domein niet kan vinden, raadpleegt u de volgende stappen voor probleem oplossing:

    * Zorg ervoor dat het domein bereikbaar is vanaf de VM. Probeer `ping aaddscontoso.com` te zien of er een positief antwoord wordt geretourneerd.
    * Controleer of de virtuele machine is geïmplementeerd op hetzelfde of een peered virtueel netwerk waarin het beheerde domein van Azure AD DS beschikbaar is.
    * Controleer of de DNS-server instellingen voor het virtuele netwerk zijn bijgewerkt zodat ze verwijzen naar de domein controllers van het door Azure AD DS beheerde domein.

1. Initialiseer nu Kerberos met behulp `kinit` van de opdracht. Geef een gebruiker op die deel uitmaakt van het door Azure AD DS beheerde domein. Voeg, indien nodig, [een gebruikers account toe aan een groep in azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    De Azure AD DS Managed domain name moet in alle hoofd letters worden ingevoerd. In het volgende voor beeld wordt het account `contosoadmin@aaddscontoso.com` met de naam gebruikt voor het initialiseren van Kerberos. Voer uw eigen gebruikers account in die deel uitmaakt van het door Azure AD DS beheerde domein:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Voeg ten slotte de computer toe aan het beheerde domein van Azure AD DS `realm join` met behulp van de opdracht. Gebruik hetzelfde gebruikers account dat deel uitmaakt van het Azure AD DS beheerde domein dat u in de vorige `kinit` opdracht hebt opgegeven, zoals: `contosoadmin@AADDSCONTOSO.COM`

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Het kan even duren voordat de virtuele machine wordt toegevoegd aan het beheerde domein van Azure AD DS. In de volgende voorbeeld uitvoer ziet u dat de virtuele machine is toegevoegd aan het door Azure AD DS beheerde domein:

```output
Successfully enrolled machine in realm
```

Als uw virtuele machine het proces voor domein deelname niet kan volt ooien, moet u ervoor zorgen dat door de netwerk beveiligings groep van de virtuele machine uitgaand Kerberos-verkeer op TCP + UDP-poort 464 naar het subnet van het virtuele netwerk voor uw Azure AD DS beheerde domein is toegestaan.

## <a name="allow-password-authentication-for-ssh"></a>Wachtwoord verificatie voor SSH toestaan

Standaard kunnen gebruikers zich alleen aanmelden bij een VM met behulp van open bare SSH-sleutel verificatie. Verificatie op basis van wacht woorden mislukt. Wanneer u de virtuele machine koppelt aan een door Azure AD DS beheerd domein, moeten die domein accounts gebruikmaken van verificatie op basis van wacht woorden. Werk de SSH-configuratie als volgt bij om verificatie op basis van wacht woorden toe te staan.

1. Open het *sshd_conf* -bestand met een editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Werk de regel voor *PasswordAuthentication* bij naar *Ja*:

    ```console
    PasswordAuthentication yes
    ```

    Als u klaar bent, slaat u het *sshd_conf* bestand op `:wq` en sluit u het af met de opdracht van de editor.

1. Als u de wijzigingen wilt Toep assen en gebruikers wilt laten aanmelden met een wacht woord, start u de SSH-service opnieuw:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>De groep ' AAD DC Administrators ' sudo privileges verlenen

Als u leden van de groep *Aad DC-Administrators* wilt toewijzen aan de CENTOS-VM, voegt u een vermelding toe aan de */etc/sudoers*. Eenmaal toegevoegd, kunnen leden van de groep *Aad DC-Administrators* de `sudo` opdracht gebruiken op de CentOS-VM.

1. Open het *sudo* -bestand om het te bewerken:

    ```console
    sudo visudo
    ```

1. Voeg de volgende vermelding toe aan het einde van het */etc/sudoers* -bestand. De groep *Aad DC-Administrators* bevat witruimte in de naam, dus neem het escape-teken van de back slash op in de groeps naam. Voeg uw eigen domein naam toe, zoals *aaddscontoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Als u klaar bent, slaat u de editor op `:wq` en sluit u deze met de opdracht van de editor.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Aanmelden bij de virtuele machine met behulp van een domein account

Start een nieuwe SSH-verbinding met een domein gebruikers account om te controleren of de virtuele machine is toegevoegd aan het beheerde Azure AD DS-domein. Bevestig dat er een basismap is gemaakt en dat groepslid maatschap van het domein wordt toegepast.

1. Maak een nieuwe SSH-verbinding vanuit uw-console. Gebruik een domein account dat deel uitmaakt van het beheerde domein `ssh -l` met behulp van `contosoadmin@aaddscontoso.com` de opdracht, zoals en voer vervolgens het adres van uw virtuele machine in, bijvoorbeeld *CentOS.aaddscontoso.com*. Als u de Azure Cloud Shell gebruikt, gebruikt u het open bare IP-adres van de virtuele machine in plaats van de interne DNS-naam.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
    ```

1. Wanneer u verbinding hebt gemaakt met de virtuele machine, controleert u of de basismap correct is geïnitialiseerd:

    ```console
    pwd
    ```

    U moet zich in de */Home* -map met uw eigen map bekomen die overeenkomt met het gebruikers account.

1. Controleer nu of de groepslid maatschappen correct worden omgezet:

    ```console
    id
    ```

    U moet uw groepslid maatschappen van het door Azure AD DS beheerde domein zien.

1. Als u zich bij de virtuele machine hebt aangemeld als lid van de groep *Aad DC-Administrators* , controleert u of u `sudo` de opdracht kunt gebruiken:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt met het verbinden van de virtuele machine met de Azure AD DS beheerde domein of als u zich aanmeldt met een domein account, raadpleegt u problemen [met domein deelname oplossen](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
