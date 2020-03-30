---
title: Azure VMware-oplossing per cloudSimple - Azure AD gebruiken als identiteitsbron op Private Cloud
description: Beschrijft hoe u Azure AD toevoegt als identiteitsprovider in uw CloudSimple Private Cloud om gebruikers te verifiëren die cloudsimple openen vanuit Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 674ca8bea110d60557d1e50e7b68c9c3f7a92bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564581"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Azure AD gebruiken als identiteitsprovider voor vCenter op CloudSimple Private Cloud

U uw CloudSimple Private Cloud vCenter zo instellen dat u met Azure Active Directory (Azure AD) verifieert voor uw VMware-beheerders om toegang te krijgen tot vCenter. Nadat de enkele aanmeldingsidentiteitsbron is ingesteld, kan de **cloudowner-gebruiker** gebruikers toevoegen van de identiteitsbron naar vCenter.  

U uw Active Directory-domein- en domeincontrollers op een van de volgende manieren instellen:

* Active Directory-domein- en domeincontrollers die on-premises worden uitgevoerd
* Active Directory-domein- en domeincontrollers die op Azure worden uitgevoerd als virtuele machines in uw Azure-abonnement
* Nieuwe Active Directory-domein- en domeincontrollers die worden uitgevoerd in uw CloudSimple Private Cloud
* Azure Active Directory-service

In deze handleiding worden de taken uitgelegd die nodig zijn om Azure AD in te stellen als identiteitsbron.  Raadpleeg [VCenter-identiteitsbronnen instellen om Active Directory te gebruiken](set-vcenter-identity.md) voor informatie over het gebruik van on-premises Active Directory of Active Directory in Azure.

## <a name="about-azure-ad"></a>Over Azure AD

Azure AD is de Microsoft multi-tenant, cloud gebaseerde directory en identity management service.  Azure AD biedt een schaalbaar, consistent en betrouwbaar verificatiemechanisme voor gebruikers om verschillende services op Azure te verifiëren en te openen.  Het biedt ook veilige LDAP-services voor services van derden om Azure AD te gebruiken als verificatie-/identiteitsbron.  Azure AD combineert kerndirectoryservices, geavanceerd identiteitsbeheer en toepassingstoegangsbeheer, dat kan worden gebruikt om toegang te geven tot uw Private Cloud voor gebruikers die de Private Cloud beheren.

Als u Azure AD wilt gebruiken als identiteitsbron met vCenter, moet u Azure AD- en Azure AD-domeinservices instellen. Volg de volgende instructies:

1. [Azure AD- en Azure AD-domeinservices instellen](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Een identiteitsbron instellen op uw Private Cloud vCenter](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Azure AD- en Azure AD-domeinservices instellen

Voordat u aan de slag gaat, hebt u toegang nodig tot uw Azure-abonnement met global administrator-bevoegdheden.  De volgende stappen geven algemene richtlijnen. Details zijn opgenomen in de Azure-documentatie.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Als u al Azure AD hebt, u deze sectie overslaan.

1. Azure AD instellen voor uw abonnement zoals beschreven in [Azure AD-documentatie](../active-directory/fundamentals/get-started-azure-ad.md).
2. Azure Active Directory Premium inschakelen voor uw abonnement zoals beschreven in [Aanmelden voor Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md).
3. Stel een aangepaste domeinnaam in en verifieer de aangepaste domeinnaam zoals beschreven in [Een aangepaste domeinnaam toevoegen aan Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    1. Stel een DNS-record in op uw domeinregistrar met de informatie op Azure.
    2. Stel de aangepaste domeinnaam in als het primaire domein.

U optioneel andere Azure AD-functies configureren.  Deze zijn niet vereist voor het inschakelen van vCenter-verificatie met Azure AD.

### <a name="azure-ad-domain-services"></a>Azure AD-domeinservices

> [!NOTE]
> Dit is een belangrijke stap voor het inschakelen van Azure AD als identiteitsbron voor vCenter.  Om problemen te voorkomen, moet u ervoor zorgen dat alle stappen correct worden uitgevoerd.

1. Azure AD-domeinservices inschakelen zoals beschreven in [Azure Active Directory-domeinservices inschakelen met behulp van de Azure-portal.](../active-directory-domain-services/active-directory-ds-getting-started.md)
2. Stel het netwerk in dat wordt gebruikt door Azure AD-domeinservices zoals beschreven in [Azure Active Directory Domain Services inschakelen met behulp van de Azure-portal.](../active-directory-domain-services/active-directory-ds-getting-started-network.md)
3. Beheer-groep voor het beheren van Azure AD Domain Services zoals beschreven in [Azure Active Directory Domain Services inschakelen met de Azure-portal.](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md)
4. DNS-instellingen voor uw Azure AD Domain Services bijwerken zoals beschreven in [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-dns.md)inschakelen .  Als u via internet verbinding wilt maken met AD, stelt u de DNS-record in voor het openbare IP-adres van de Azure AD-domeinservices op de domeinnaam.
5. Wachtwoordhashsynchronisatie inschakelen voor gebruikers.  Met deze stap u wachtwoordhashes synchroniseren die vereist zijn voor NT LAN Manager (NTLM) en Kerberos-verificatie naar Azure AD Domain Services. Wanneer u de synchronisatie voor wachtwoordhashes hebt ingesteld, kunnen gebruikers zich bij het beheerde domein aanmelden met hun zakelijke referenties. Zie [Synchronisatie van wachtwoordhash inschakelen voor Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).
    1. Als gebruikers met alleen de cloud aanwezig zijn, moeten ze hun wachtwoord wijzigen met het <a href="http://myapps.microsoft.com/" target="_blank">Azure AD-toegangspaneel</a> om ervoor te zorgen dat wachtwoordhashes worden opgeslagen in de indeling die vereist is door NTLM of Kerberos.  Volg instructies in [Wachtwoordhashsynchronisatie inschakelen voor uw beheerde domein voor alleen cloudgebruikersaccounts.](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)  Deze stap moet worden gedaan voor individuele gebruikers en elke nieuwe gebruiker die is gemaakt in uw Azure AD-map met behulp van de Azure-portal of Azure AD PowerShell-cmdlets. Gebruikers die toegang nodig hebben tot Azure AD-domeinservices, moeten het <a href="http://myapps.microsoft.com/" target="_blank">Azure AD-toegangspaneel</a> gebruiken en toegang krijgen tot hun profiel om het wachtwoord te wijzigen.

        > [!NOTE]
        > Als uw organisatie gebruikersaccounts in de cloud heeft, moeten alle gebruikers die Azure Active Directory Domain Services willen gebruiken, hun wachtwoord wijzigen. Een cloudgebruikersaccount is een account dat is gemaakt in uw Azure AD-directory via de Azure portal of Azure AD PowerShell-cmdlets. Deze gebruikersaccounts zijn niet gesynchroniseerd vanuit een on-premises map.

    2. Als u wachtwoorden synchroniseert vanuit uw on-premises Active directory, voert u de stappen in de [Active Directory-documentatie uit.](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)

6.  Configureer veilige LDAP op uw Azure Active Directory Domain Services zoals beschreven in [Secure LDAP (LDAPS) configureren voor een beheerd Azure AD Domain Services-domein.](../active-directory-domain-services/tutorial-configure-ldaps.md)
    1. Upload een certificaat voor gebruik door beveiligde LDAP zoals beschreven in het Azure-onderwerp [om een certificaat voor beveiligde LDAP te verkrijgen.](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)  CloudSimple raadt aan een ondertekend certificaat te gebruiken dat is uitgegeven door een certificaatautoriteit om ervoor te zorgen dat vCenter het certificaat kan vertrouwen.
    2. Schakel beveiligde LDAP in zoals beschreven [Schakel secure LDAP (LDAPS) in voor een beheerd Azure AD Domain Services-domein.](../active-directory-domain-services/tutorial-configure-ldaps.md)
    3. Sla het openbare deel van het certificaat (zonder de privésleutel) op in .cer-indeling voor gebruik met vCenter tijdens het configureren van de identiteitsbron.
    4. Als internettoegang tot de Azure AD-domeinservices vereist is, schakelt u de optie 'Veilige toegang tot LDAP via internet toestaan' in.
    5. Voeg de binnenkomende beveiligingsregel toe voor de NSG voor Azure AD Domain Services VOOR TCP-poort 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Een identiteitsbron instellen op uw Private Cloud vCenter

1. De bevoegdheden voor uw Private Cloud vCenter [escaleren.](escalate-private-cloud-privileges.md)
2. Verzamel de configuratieparameters die nodig zijn voor het instellen van identiteitsbronnen.

    | **Optie** | **Beschrijving** |
    |------------|-----------------|
    | **Naam** | Naam van de identiteitsbron. |
    | **Basis DN voor gebruikers** | Base onderscheiden naam voor gebruikers.  Voor Azure AD `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` gebruikt `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`u: Voorbeeld: .|
    | **Domeinnaam** | FQDN van het domein, bijvoorbeeld, example.com. Geef geen IP-adres op in dit tekstvak. |
    | **Domeinalias** | *(facultatief)* De naam van het domein NetBIOS. Voeg de NetBIOS-naam van het Active Directory-domein toe als alias van de identiteitsbron als u SSPI-verificaties gebruikt. |
    | **Basis DN voor groepen** | De basis voorname naam voor groepen. Voor Azure AD `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` gebruikt u: Voorbeeld:`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **URL van primaire server** | LDAP-server voor primaire domeincontroller voor het domein.<br><br>Gebruik de `ldaps://hostname:port`indeling . De poort is meestal 636 voor LDAPS-verbindingen. <br><br>Een certificaat dat de vertrouwensrelatie voor het LDAPS-eindpunt van `ldaps://` de Active Directory-server vaststelt, is vereist wanneer u de primaire of secundaire LDAP-URL gebruikt. |
    | **URL van secundaire server** | Adres van een LDAP-server met secundaire domeincontroller die wordt gebruikt voor failover. |
    | **Certificaat kiezen** | Als u LDAPS wilt gebruiken met uw Active Directory LDAP Server of OpenLDAP `ldaps://` Server-identiteitsbron, wordt een knop Certificaat kiezen weergegeven nadat u het TEKSTVAK URL hebt getypt. Een secundaire URL is niet vereist. |
    | **Gebruikersnaam** | ID van een gebruiker in het domein die een minimum aan alleen-lezen toegang tot Base DN heeft voor gebruikers en groepen. |
    | **Wachtwoord** | Wachtwoord van de gebruiker die is opgegeven op gebruikersnaam. |

3. Meld u aan bij uw Private Cloud vCenter nadat de bevoegdheden zijn geëscaleerd.
4. Volg de instructies in [Een identiteitsbron toevoegen aan vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) met de waarden uit de vorige stap om Azure Active Directory in te stellen als identiteitsbron.
5. Voeg gebruikers/groepen van Azure AD toe aan vCenter-groepen zoals beschreven in het [VMware-onderwerp Leden toevoegen aan een vCenter-aanmeldingsgroep](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Nieuwe gebruikers mogen alleen worden toegevoegd aan *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* of *Cloud-Global-VM-Admin-Group*.  Gebruikers die zijn toegevoegd aan de groep *Administrators,* worden automatisch verwijderd.  Alleen serviceaccounts mogen worden toegevoegd aan de groep *Administrators.*

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het machtigingsmodel voor Private Cloud](learn-private-cloud-permissions.md)
