---
title: Deelnemen aan een CoreOS VM voor Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over het configureren en aansluiten van een virtuele CoreOS-machine bij een beheerd Azure AD Domain Services-domein.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: b97b542d11e405bab00519c68d2365dada6b6c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298867"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Een virtuele CoreOS-machine aansluiten bij een beheerd Azure AD Domain Services-domein

Als u gebruikers wilt laten inloggen op virtuele machines (VM's) in Azure met één set referenties, u VM's toevoegen aan een beheerd beheerd domein van Azure Active Directory Domain Services (AD DS). Wanneer u lid wordt van een VM in een door Azure AD DS beheerd domein, kunnen gebruikersaccounts en referenties uit het domein worden gebruikt om u aan te melden en servers te beheren. Groepslidmaatschappen uit het beheerde Azure AD DS-domein worden ook toegepast om u toegang te geven tot bestanden of services op de VM.

In dit artikel ziet u hoe u lid worden van een CoreOS VM in een door Azure AD DS beheerd domein.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Indien nodig maakt [en configureert][create-azure-ad-ds-instance]de eerste zelfstudie een Azure Active Directory Domain Services-exemplaar .
* Een gebruikersaccount dat deel uitmaakt van het door Azure AD DS beheerde domein.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Maak en maak verbinding met een CoreOS Linux VM

Als u een bestaande CoreOS Linux VM in Azure hebt, maakt u verbinding met deze met SSH en gaat u verder naar de volgende stap om [te beginnen met het configureren van de VM.](#configure-the-hosts-file)

Als u een CoreOS Linux VM wilt maken of een test-VM wilt maken voor gebruik met dit artikel, u een van de volgende methoden gebruiken:

* [Azure-portal](../virtual-machines/linux/quick-create-portal.md)
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
* *coreos* is de hostnaam van uw CoreOS VM die u aansluit bij het beheerde domein.

Werk deze namen bij met uw eigen waarden:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

Sla het *hosts-bestand* op en `:wq` sluit deze af met de opdracht van de editor.

## <a name="configure-the-sssd-service"></a>De SSSD-service configureren

Werk de */etc/sssd/sssd.conf* SSSD configuratie bij.

```console
sudo vi /etc/sssd/sssd.conf
```

Geef uw eigen Azure AD DS-beheerde domeinnaam op voor de volgende parameters:

* *domeinen* in ALLE HOOFDLETTERS
* *[domein/AADDS]* wanneer AADDS zich in ALLE HOOFDLETTERs bevindt
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* in ALLE HOOFDLETTERs

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Deelnemen aan de VM naar het beheerde domein

Nu het SSSD-configuratiebestand is bijgewerkt, wordt de virtuele machine nu samengevoegd met het beheerde domein.

1. Gebruik eerst `adcli info` de opdracht om te controleren of u informatie over het beheerde Azure AD DS-domein zien. In het volgende voorbeeld wordt informatie voor het domein *AADDSCONTOSO.COM.* Geef uw eigen Azure AD DS-beheerde domeinnaam op in ALLE HOOFDLETTERS:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Als `adcli info` de opdracht uw door Azure AD DS beheerde domein niet kan vinden, controleert u de volgende stappen voor het oplossen van problemen:

    * Zorg ervoor dat het domein bereikbaar is via de VM. Probeer `ping aaddscontoso.com` te zien of een positief antwoord wordt geretourneerd.
    * Controleer of de VM is geïmplementeerd op hetzelfde of een virtueel netwerk met peered waarin het beheerde Azure AD DS-beheerde domein beschikbaar is.
    * Controleer of de DNS-serverinstellingen voor het virtuele netwerk zijn bijgewerkt om te wijzen op de domeincontrollers van het beheerde Azure AD DS-domein.

1. Sluit je nu met de `adcli join` opdracht aan bij de VM naar het door Azure AD DS beheerde domein. Geef een gebruiker op die deel uitmaakt van het beheerde Azure AD DS-domein. Voeg indien nodig [een gebruikersaccount toe aan een groep in Azure AD.](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)

    Nogmaals, de door Azure AD DS beheerde domeinnaam moet in ALLE HOOFDLETTERS worden ingevoerd. In het volgende voorbeeld `contosoadmin@aaddscontoso.com` wordt het account met de naam gebruikt om Kerberos te initialiseren. Voer uw eigen gebruikersaccount in dat deel uitmaakt van het beheerde Azure AD DS-domein.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    De `adcli join` opdracht geeft geen informatie terug wanneer de VM is verbonden met het beheerde Azure AD DS-domein.

1. Als u de domein-joinconfiguratie wilt toepassen, start u de SSSD-service:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Aanmelden bij de VM met een domeinaccount

Als u wilt controleren of de VM is verbonden met het beheerde Azure AD DS-domein, start u een nieuwe SSH-verbinding met een domeingebruikersaccount. Controleer of er een thuismap is gemaakt en dat groepslidmaatschap van het domein wordt toegepast.

1. Maak een nieuwe SSH-verbinding vanaf uw console. Gebruik een domeinaccount dat tot het `ssh -l` beheerde `contosoadmin@aaddscontoso.com` domein behoort met behulp van de opdracht, zoals en voer vervolgens het adres van uw vm in, zoals *coreos.aaddscontoso.com.* Als u de Azure Cloud Shell gebruikt, gebruikt u het openbare IP-adres van de vm in plaats van de interne DNS-naam.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Controleer nu of de groepslidmaatschappen correct worden opgelost:

    ```console
    id
    ```

    U moet uw groepslidmaatschappen zien vanuit het beheerde Azure AD DS-domein.

## <a name="next-steps"></a>Volgende stappen

Zie Problemen met het koppelen van [domeinen oplossen](join-windows-vm.md#troubleshoot-domain-join-issues)als u problemen hebt met het koppelen van de vm met het beheerde Azure AD DS-domein of als u zich aanmeldt met een domeinaccount.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
