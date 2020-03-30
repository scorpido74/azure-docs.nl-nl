---
title: Azure VMware-oplossing voor CloudSimple - VCenter-identiteitsbronnen instellen op Private Cloud
description: Beschrijft hoe u uw Private Cloud vCenter instelt om te verifiëren met Active Directory voor VMware-beheerders om toegang te krijgen tot vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5355e43ca6ac075e76a76ceb51be135cf4b62b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564020"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>VCenter-identiteitsbronnen instellen om Active Directory te gebruiken

## <a name="about-vmware-vcenter-identity-sources"></a>Informatie over VMware vCenter-identiteitsbronnen

VMware vCenter ondersteunt verschillende identiteitsbronnen voor verificatie van gebruikers die toegang hebben tot vCenter.  Uw CloudSimple Private Cloud vCenter kan worden ingesteld om te verifiëren met Active Directory voor uw VMware-beheerders om toegang te krijgen tot vCenter. Wanneer de installatie is voltooid, kan de **cloudowner-gebruiker** gebruikers toevoegen van de identiteitsbron naar vCenter.  

U uw Active Directory-domein- en domeincontrollers op een van de volgende manieren instellen:

* Active Directory-domein- en domeincontrollers die on-premises worden uitgevoerd
* Active Directory-domein- en domeincontrollers die op Azure worden uitgevoerd als virtuele machines in uw Azure-abonnement
* Nieuwe Active Directory-domein- en domeincontrollers die worden uitgevoerd in uw Private Cloud
* Azure Active Directory-service

In deze handleiding worden de taken uitgelegd voor het instellen van Active Directory-domein- en domeincontrollers die on-premises of als virtuele machines in uw abonnementen worden uitgevoerd.  Als u Azure AD als identiteitsbron wilt gebruiken, raadpleegt u [Azure AD gebruiken als identiteitsprovider voor vCenter op CloudSimple Private Cloud](azure-ad.md) voor gedetailleerde instructies bij het instellen van de identiteitsbron.

Voordat [u een identiteitsbron toevoegt,](#add-an-identity-source-on-vcenter) [worden uw vCenter-bevoegdheden tijdelijk geëscaleerd.](escalate-private-cloud-privileges.md)

> [!CAUTION]
> Nieuwe gebruikers mogen alleen worden toegevoegd aan *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* of *Cloud-Global-VM-Admin-Group*.  Gebruikers die zijn toegevoegd aan de groep *Administrators,* worden automatisch verwijderd.  Alleen serviceaccounts mogen worden toegevoegd aan de groep *Administrators* en serviceaccounts mogen niet worden gebruikt om zich aan te melden bij de vSphere-webgebruikersinterface.   


## <a name="identity-source-options"></a>Opties voor identiteitsbron

* [On-premises Active Directory toevoegen als één aanmeldingsbron](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Nieuwe Active Directory instellen op een privécloud](#set-up-new-active-directory-on-a-private-cloud)
* [Active Directory instellen in Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>On-premises Active Directory toevoegen als één aanmeldingsbron

Als u uw on-premises Active Directory wilt instellen als één aanmeldingsbron, moet u het andere doen:

* [Site-to-Site VPN-verbinding](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) van uw on-premises datacenter naar uw Private Cloud.
* On-premises DNS-server-IP toegevoegd aan vCenter en Platform Services Controller (PSC).

Gebruik de gegevens in de volgende tabel bij het instellen van uw Active Directory-domein.

| **Optie** | **Beschrijving** |
|------------|-----------------|
| **Naam** | Naam van de identiteitsbron. |
| **Basis DN voor gebruikers** | Base onderscheiden naam voor gebruikers. |
| **Domeinnaam** | FQDN van het domein, bijvoorbeeld, example.com. Geef geen IP-adres op in dit tekstvak. |
| **Domeinalias** | De naam van het domein NetBIOS. Voeg de NetBIOS-naam van het Active Directory-domein toe als alias van de identiteitsbron als u SSPI-verificaties gebruikt. |
| **Basis DN voor groepen** | De basis voorname naam voor groepen. |
| **URL van primaire server** | LDAP-server voor primaire domeincontroller voor het domein.<br><br>Gebruik de `ldap://hostname:port`  `ldaps://hostname:port`notatie of . De poort is meestal 389 voor LDAP-verbindingen en 636 voor LDAPS-verbindingen. Voor implementaties met active directory-controllers voor meerdere domeinen is de poort doorgaans 3268 voor LDAP en 3269 voor LDAPS.<br><br>Een certificaat dat de vertrouwensrelatie voor het LDAPS-eindpunt van `ldaps://` de Active Directory-server vaststelt, is vereist wanneer u de primaire of secundaire LDAP-URL gebruikt. |
| **URL van secundaire server** | Adres van een LDAP-server met secundaire domeincontroller die wordt gebruikt voor failover. |
| **Certificaat kiezen** | Als u LDAPS wilt gebruiken met uw Active Directory LDAP Server of OpenLDAP `ldaps://` Server-identiteitsbron, wordt een knop Certificaat kiezen weergegeven nadat u het TEKSTVAK URL hebt getypt. Een secundaire URL is niet vereist. |
| **Gebruikersnaam** | ID van een gebruiker in het domein die een minimum aan alleen-lezen toegang tot Base DN heeft voor gebruikers en groepen. |
| **Wachtwoord** | Wachtwoord van de gebruiker die is opgegeven op gebruikersnaam. |

Wanneer u de informatie in de vorige tabel hebt, u uw on-premises Active Directory toevoegen als een enkele aanmeldingsbron op vCenter.

> [!TIP]
> Meer informatie over interne aanmeldingsbronnen vindt u op de [documentatiepagina van VMware.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Nieuwe Active Directory instellen in een private cloud

U een nieuw Active Directory-domein instellen op uw private cloud en het gebruiken als identiteitsbron voor Single Sign-On.  Het Active Directory-domein kan deel uitmaken van een bestaand Active Directory-forest of kan worden ingesteld als een onafhankelijk forest.

### <a name="new-active-directory-forest-and-domain"></a>Nieuw Active Directory-forest en -domein

Als u een nieuw Active Directory-forest en -domein wilt instellen, moet u het volgende doen:

* Een of meer virtuele machines met Microsoft Windows Server gebruiken als domeincontrollers voor het nieuwe Active Directory-forest en -domein.
* Een of meer virtuele machines met DNS-service voor naamomzetting.

Zie [Een nieuw Active Directory-forest van Windows Server 2012 installeren](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) voor gedetailleerde stappen.

> [!TIP]
> Voor een hoge beschikbaarheid van services raden we aan meerdere domeincontrollers en DNS-servers in te stellen.

Nadat u het Active Directory-forest en -domein hebt ingesteld, u [een identiteitsbron toevoegen aan vCenter](#add-an-identity-source-on-vcenter) voor uw nieuwe Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Nieuw Active Directory-domein in een bestaand Active Directory-forest

Als u een nieuw Active Directory-domein wilt instellen in een bestaand Active Directory-forest, moet u het volgende doen:

* Vpn-verbinding van site naar site met uw Active Directory-forestlocatie.
* DNS Server om de naam van uw bestaande Active Directory-forest op te lossen.

Zie [Een nieuw Active Directory-kinder- of boomdomein van Windows Server 2012 installeren](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) voor gedetailleerde stappen.

Nadat u het Active Directory-domein hebt ingesteld, u [een identiteitsbron toevoegen aan vCenter](#add-an-identity-source-on-vcenter) voor uw nieuwe Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Active Directory instellen in Azure

Active Directory die wordt uitgevoerd op Azure is vergelijkbaar met Active Directory dat on-premises wordt uitgevoerd.  Als u Active Directory wilt instellen die op Azure wordt uitgevoerd als een enkele aanmeldingsbron op vCenter, moeten de vCenter-server en PSC netwerkconnectiviteit hebben met het Azure Virtual Network waar Active Directory-services worden uitgevoerd.  U deze connectiviteit tot stand brengen met [Azure Virtual Network Connection via ExpressRoute](azure-expressroute-connection.md) vanuit het Virtuele Azure-netwerk waar Active Directory Services wordt uitgevoerd naar CloudSimple Private Cloud.

Volg de stappen in Active Directory [toevoegen on-premises als één aanmeldingsbron](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) om deze toe te voegen als identiteitsbron.  

## <a name="add-an-identity-source-on-vcenter"></a>Een identiteitsbron toevoegen aan vCenter

1. De bevoegdheden op uw privécloud [escaleren.](escalate-private-cloud-privileges.md)

2. Meld u aan bij het vCenter voor uw Private Cloud.

3. Selecteer **>-beheer**van Thuis .

    ![Beheer](media/OnPremAD01.png)

4. Selecteer **Eén aanmelding op > configuratie**.

    ![Eenmalige aanmelding](media/OnPremAD02.png)

5. Open het tabblad **Identiteitsbronnen** en klik **+** om een nieuwe identiteitsbron toe te voegen.

    ![Identiteitsbronnen](media/OnPremAD03.png)

6. Selecteer **Active Directory als LDAP-server** en klik op **Volgende**.

    ![Active Directory](media/OnPremAD04.png)

7. Geef de parameters van de identiteitsbron voor uw omgeving op en klik op **Volgende**.

    ![Active Directory](media/OnPremAD05.png)

8. Controleer de instellingen en klik op **Voltooien**.
