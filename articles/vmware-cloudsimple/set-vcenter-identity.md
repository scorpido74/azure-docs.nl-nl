---
title: 'Azure VMware-oplossingen (AVS): vCenter-identiteits bronnen instellen op de AVS-privécloud'
description: Hierin wordt beschreven hoe u de automatische AVS-Cloud-vCenter instelt om te verifiëren met Active Directory voor VMware-beheerders om toegang te krijgen tot vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad4a7b2bc67b7d50d9e9a5f8337a09dbe77366ea
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014212"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>VCenter-identiteits bronnen instellen voor het gebruik van Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Over VMware vCenter-identiteits bronnen

VMware vCenter ondersteunt verschillende identiteits bronnen voor verificatie van gebruikers die toegang hebben tot vCenter. Uw AVS Private Cloud vCenter kan worden ingesteld om te verifiëren met Active Directory voor uw VMware-beheerders om toegang te krijgen tot vCenter. Wanneer de installatie is voltooid, kan de gebruiker van **cloudowner** gebruikers van de identiteits bron toevoegen aan vCenter. 

U kunt uw Active Directory domein-en domein controllers op een van de volgende manieren instellen:

* Active Directory domein-en domein controllers die on-premises worden uitgevoerd
* Active Directory domein en domein controllers die op Azure worden uitgevoerd als virtuele machines in uw Azure-abonnement
* Nieuwe Active Directory domein-en domein controllers die worden uitgevoerd in uw AVS-Privécloud
* Azure Active Directory-service

In deze hand leiding worden de taken beschreven voor het instellen van Active Directory domein en domein controllers die on-premises of als virtuele machines in uw abonnementen worden uitgevoerd. Als u Azure AD wilt gebruiken als de identiteits bron, raadpleegt u [Azure AD gebruiken als een id-provider voor vCenter in de privécloud van de AVS](azure-ad.md) voor gedetailleerde instructies voor het instellen van de identiteits bron.

Voordat u [een identiteits bron toevoegt](#add-an-identity-source-on-vcenter), moet [u uw vCenter-bevoegdheden tijdelijk escaleren](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Nieuwe gebruikers moeten alleen worden toegevoegd aan de *Cloud-eigenaar-groep*, *Cloud-Global-cluster-admin groep*, *Cloud-Global-Storage-admin-Group*, Cloud-Global: *Network-Administrator-* Group of Cloud-Global:- *beheer groep*.  Gebruikers die zijn toegevoegd aan de groep *Administrators* , worden automatisch verwijderd.  Alleen service accounts moeten worden toegevoegd aan de groep *Administrators* en service accounts moeten worden gebruikt om u aan te melden bij de vSphere-webgebruikersinterface.   


## <a name="identity-source-options"></a>Opties voor identiteits bron

* [On-premises Active Directory toevoegen als identiteits bron met eenmalige aanmelding](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Nieuwe Active Directory instellen op een AVS-Privécloud](#set-up-new-active-directory-on-an-avs-private-cloud)
* [Active Directory instellen op Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>On-premises Active Directory toevoegen als identiteits bron met eenmalige aanmelding

U hebt het volgende nodig om uw on-premises Active Directory in te stellen als id-bron voor eenmalige aanmelding:

* [Site-naar-site-VPN-verbinding](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) van uw on-premises Data Center naar de privécloud van uw AVS.
* IP-adres van on-premises DNS-server is toegevoegd aan vCenter-en platform Services controller (PSC).

Gebruik de informatie in de volgende tabel bij het instellen van uw Active Directory domein.

| **Optie** | **Beschrijving** |
|------------|-----------------|
| **Naam** | De naam van de identiteits bron. |
| **Basis-DN voor gebruikers** | Basis-DN-naam voor gebruikers. |
| **Domeinnaam** | FDQN van het domein, bijvoorbeeld example.com. Geef geen IP-adres op in dit tekstvak. |
| **Domein alias** | De NetBIOS-naam van het domein. Voeg de NetBIOS-naam van het Active Directory domein als alias van de identiteits bron toe als u SSPI-verificaties gebruikt. |
| **Basis-DN voor groepen** | De DN-basis naam voor groepen. |
| **URL van primaire server** | LDAP-server van de primaire domein controller voor het domein.<br><br>Gebruik de indeling `ldap://hostname:port` of `ldaps://hostname:port`. De poort is doorgaans 389 voor LDAP-verbindingen en 636 voor LDAPS-verbindingen. Voor Active Directory implementaties van meerdere domein controllers is de poort doorgaans 3268 voor LDAP en 3269 voor LDAPS.<br><br>Een certificaat dat een vertrouwens relatie voor het LDAPS-eind punt van de Active Directory server tot stand brengt, is vereist wanneer u `ldaps://` in de primaire of secundaire LDAP-URL gebruikt. |
| **URL van secundaire server** | Adres van de LDAP-server van de secundaire domein controller die wordt gebruikt voor failover. |
| **Certificaat kiezen** | Als u LDAPS wilt gebruiken met uw Active Directory LDAP-server of OpenLDAP-server identiteits bron, wordt een knop certificaat kiezen weer gegeven nadat u `ldaps://` in het tekstvak URL hebt getypt. Een secundaire URL is niet vereist. |
| **Gebruikersnaam** | ID van een gebruiker in het domein met mini maal alleen-lezen toegang tot de basis-DN voor gebruikers en groepen. |
| **Wachtwoord** | Het wacht woord van de gebruiker die is opgegeven door de gebruikers naam. |

Wanneer u de gegevens in de vorige tabel hebt, kunt u uw on-premises Active Directory toevoegen als id-bron voor eenmalige aanmelding op vCenter.

> [!TIP]
> U vindt meer informatie over identiteits bronnen voor eenmalige aanmelding op de [documentatie pagina van VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-an-avs-private-cloud"></a>Nieuwe Active Directory instellen op een AVS-Privécloud

U kunt een nieuw Active Directory domein instellen in de Privécloud van uw AVS en dit als een identiteits bron gebruiken voor eenmalige aanmelding. Het Active Directory domein kan deel uitmaken van een bestaand Active Directory forest of kunnen worden ingesteld als een onafhankelijk forest.

### <a name="new-active-directory-forest-and-domain"></a>Nieuw Active Directory forest en domein

Als u een nieuw Active Directory-forest en-domein wilt instellen, hebt u het volgende nodig:

* Een of meer virtuele machines met micro soft Windows Server om te gebruiken als domein controllers voor het nieuwe Active Directory-forest en-domein.
* Een of meer virtuele machines waarop de DNS-service wordt uitgevoerd voor naam omzetting.

Zie [een nieuwe Windows Server 2012 Active Directory-forest installeren](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) voor gedetailleerde stappen.

> [!TIP]
> Voor een hoge Beschik baarheid van services raden we u aan om meerdere domein controllers en DNS-servers in te stellen.

Nadat u het Active Directory-forest en-domein hebt ingesteld, kunt u [een id-bron toevoegen aan vCenter](#add-an-identity-source-on-vcenter) voor uw nieuwe Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Nieuw Active Directory domein in een bestaand Active Directory-forest

Als u een nieuw Active Directory domein wilt instellen in een bestaand Active Directory forest, hebt u het volgende nodig:

* Site-naar-site-VPN-verbinding met de locatie van uw Active Directory-forest.
* DNS-server om de naam van uw bestaande Active Directory-forest op te lossen.

Zie [een nieuwe Windows Server 2012-Active Directory onderliggende of structuur domein installeren](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) voor gedetailleerde stappen.

Nadat u het Active Directory domein hebt ingesteld, kunt u [een id-bron toevoegen aan vCenter](#add-an-identity-source-on-vcenter) voor uw nieuwe Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Active Directory instellen op Azure

Active Directory op Azure wordt uitgevoerd, is vergelijkbaar met Active Directory die on-premises worden uitgevoerd. Als u Active Directory die op Azure wordt uitgevoerd als id-bron voor eenmalige aanmelding op vCenter wilt instellen, moet de vCenter-Server en de PSC netwerk verbinding hebben met de Azure-Virtual Network waar Active Directory Services worden uitgevoerd. U kunt deze verbinding tot stand brengen met behulp van [azure Virtual Network verbinding met behulp van ExpressRoute](azure-expressroute-connection.md) van het virtuele Azure-netwerk waar Active Directory Services worden uitgevoerd voor de AVS-privécloud.

Nadat de netwerk verbinding tot stand is gebracht, volgt u de stappen in [on-premises Active Directory toevoegen als id-bron voor eenmalige aanmelding](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) om deze als een identiteits bron toe te voegen. 

## <a name="add-an-identity-source-on-vcenter"></a>Een identiteits bron toevoegen aan vCenter

1. [Bevoegdheden](escalate-private-cloud-privileges.md) voor de privécloud van uw AVS escaleren.

2. Meld u aan bij de vCenter voor de Privécloud van uw AVS.

3. Selecteer **Home >-beheer**.

    ![Beheer](media/OnPremAD01.png)

4. Selecteer de **configuratie voor eenmalige aanmelding >** .

    ![Eenmalige aanmelding](media/OnPremAD02.png)

5. Open het tabblad **identiteits bronnen** en klik op **+** om een nieuwe id-bron toe te voegen.

    ![Identiteits bronnen](media/OnPremAD03.png)

6. Selecteer **Active Directory als een LDAP-server** en klik op **volgende**.

    ![Active Directory](media/OnPremAD04.png)

7. Geef de identiteits bron parameters voor uw omgeving op en klik op **volgende**.

    ![Active Directory](media/OnPremAD05.png)

8. Controleer de instellingen en klik op **volt ooien**.
