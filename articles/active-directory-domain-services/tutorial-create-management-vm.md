---
title: Zelf studie-een beheer-VM maken voor Azure Active Directory Domain Services | Microsoft Docs
description: In deze zelf studie leert u hoe u een virtuele Windows-machine maakt en configureert die u gebruikt om Azure Active Directory Domain Services-exemplaar te beheren.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: e7c3ccb553010b84a30ccdad875ea0362112d830
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619094"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Zelfstudie: Een beheer-VM maken om een Azure Active Directory Domain Services beheerd domein te configureren en te beheren

Azure Active Directory Domain Services (AD DS) biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP en Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. U beheert dit beheerde domein met behulp van dezelfde Remote Server Administration Tools (RSAT), net als bij een on-premises Active Directory Domain Services domein. Omdat Azure AD DS een beheerde service is, zijn er enkele beheer taken die u niet kunt uitvoeren, zoals het gebruik van Remote Desktop Protocol (RDP) om verbinding te maken met de domein controllers.

In deze zelf studie leert u hoe u een Windows Server-VM maakt in Azure en de vereiste hulpprogram ma's installeert voor het beheren van een door Azure AD DS beheerd domein.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Meer informatie over de beschik bare beheer taken in een door Azure AD DS beheerd domein
> * Installeer de Active Directory-beheer Programma's op een Windows Server-VM
> * De Active Directory-beheercentrum gebruiken om algemene taken uit te voeren

Als u nog geen abonnement op Azure hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources en bevoegdheden nodig om deze zelf studie te volt ooien:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory Tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
    * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een Azure Active Directory Domain Services beheerd domein ingeschakeld en geconfigureerd in uw Azure AD-Tenant.
    * Als dat nodig is, [maakt en configureert][create-azure-ad-ds-instance]de eerste zelf studie een Azure Active Directory Domain Services-exemplaar.
* Een Windows Server-VM die is gekoppeld aan het beheerde domein van Azure AD DS.
    * Als dat nodig is, maakt de vorige zelf studie [een Windows Server-VM en voegt deze toe aan een beheerd domein][create-join-windows-vm].
* Een gebruikers account dat lid is van de groep *Azure AD DC* -Administrators in uw Azure AD-Tenant.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelf studie maakt en configureert u een beheer-VM met behulp van de Azure Portal. Meld u eerst aan bij de [Azure Portal](https://portal.azure.com)om aan de slag te gaan.

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Beschik bare beheer taken in azure AD DS

Azure AD DS biedt een beheerd domein voor de verbruikte gebruikers, toepassingen en services. Deze aanpak wijzigt enkele van de beschik bare beheer taken die u kunt uitvoeren en de bevoegdheden die u hebt in het beheerde domein. Deze taken en machtigingen kunnen afwijken van wat u met een vaste on-premises Active Directory Domain Services omgeving kunt doen. U kunt ook geen verbinding maken met domein controllers op het met Azure AD DS beheerde domein met behulp van Extern bureaublad.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Beheer taken die u kunt uitvoeren op een beheerd domein van Azure AD DS

Leden van de groep *Aad DC* -Administrators beschikken over bevoegdheden op het door Azure AD DS beheerde domein waarmee ze taken kunnen uitvoeren zoals:

* Computers toevoegen aan het beheerde domein.
* Configureer het ingebouwde groeps beleidsobject (GPO) voor de containers *AADDC computers* en *AADDC gebruikers* in het beheerde domein.
* DNS beheren in het beheerde domein.
* Aangepaste organisatie-eenheden (Ou's) maken en beheren op het beheerde domein.
* Beheerderstoegang verkrijgen tot computers die aan het beheerde domein zijn gekoppeld.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Beheerders bevoegdheden die u niet hebt op een door Azure AD DS beheerd domein

Het beheerde domein van Azure AD DS is vergrendeld, dus u hebt geen bevoegdheden om bepaalde beheer taken uit te voeren op het domein. Enkele van de volgende voor beelden zijn taken die u niet kunt uitvoeren:

* Het schema van het beheerde domein uitbreiden.
* Verbinding maken met domein controllers voor het beheerde domein met behulp van Extern bureaublad.
* Voeg domein controllers toe aan het beheerde domein.
* U hebt geen *domein beheerders* -of *ondernemings Administrator* bevoegdheden voor het beheerde domein.

## <a name="sign-in-to-the-windows-server-vm"></a>Meld u aan bij de Windows Server-VM

In de vorige zelf studie is een Windows Server-VM gemaakt en gekoppeld aan het beheerde domein van Azure AD DS. We gebruiken deze virtuele machine om de beheer hulpprogramma's te installeren. Als dat nodig is, [volgt u de stappen in de zelf studie voor het maken en toevoegen van een Windows Server-VM aan een beheerd domein][create-join-windows-vm].

> [!NOTE]
> In deze zelf studie gebruikt u een virtuele Windows Server-machine in azure die is gekoppeld aan het beheerde domein van Azure AD DS. U kunt ook een Windows-client, zoals Windows 10, gebruiken die is gekoppeld aan het beheerde domein.
>
> Zie [install Remote Server Administration Tools (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) voor meer informatie over het installeren van de beheer Programma's op een Windows-client.

Als u aan de slag wilt gaan, maakt u als volgt verbinding met de virtuele machine van Windows Server:

1. Selecteer in de Azure Portal **resource groepen** aan de linkerkant. Kies de resource groep waar uw VM is gemaakt, zoals *myResourceGroup*en selecteer vervolgens de virtuele machine, zoals *myVM*.
1. Selecteer in het overzichts venster van de virtuele machine **verbinding maken**.

    ![Verbinding maken met een virtuele Windows-machine in de Azure Portal](./media/tutorial-create-management-vm/connect-vm.png)

    U kunt ook [een Azure bastion-host maken en gebruiken (momenteel in Preview)][azure-bastion] om alleen toegang toe te staan via de Azure portal via SSL.

1. Selecteer de optie om het *RDP-bestand te downloaden*. Sla dit RDP-bestand op in de webbrowser.
1. Open het gedownloade RDP-bestand om verbinding met de VM te maken. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.
1. Voer de referenties in van een gebruiker die deel uitmaakt van de groep *Azure AD DC* -Administrators, zoals *contoso\dee*
1. Als tijdens het aanmeldings proces een certificaat waarschuwing wordt weer gegeven, selecteert u **Ja** of **door gaan** met verbinding maken.

## <a name="install-active-directory-administrative-tools"></a>Active Directory-beheer Programma's installeren

Beheerde domeinen van Azure AD DS worden beheerd met dezelfde beheer Programma's als on-premises AD DS omgevingen, zoals de Active Directory-beheercentrum (ADAC) of AD Power shell. Deze hulpprogram ma's kunnen worden geïnstalleerd als onderdeel van de functie Remote Server Administration Tools (RSAT) op Windows Server-en client computers. Leden van de groep *Aad DC* -Administrators kunnen vervolgens Azure AD DS beheerde domeinen op afstand beheren met behulp van deze ad-beheer Programma's van een computer die is toegevoegd aan het beheerde domein.

Voer de volgende stappen uit om de Active Directory-beheer hulpprogramma's te installeren op een virtuele machine die lid is van een domein:

1. **Serverbeheer** moet standaard worden geopend wanneer u zich aanmeldt bij de VM. Als dat niet het geval is, selecteert u **Serverbeheer**in het menu **Start** .
1. Selecteer in het deel venster *dash board* van het **Serverbeheer** -venster **functies en onderdelen toevoegen**.
1. Selecteer op de pagina **voordat u begint** van de *wizard functies en onderdelen toevoegen*de optie **volgende**.
1. Voor het *installatie type*, houdt u de **installatie optie op basis van functie of op basis** van het onderdeel ingeschakeld en selecteert u **volgende**.
1. Kies op de pagina **server selectie** de huidige virtuele machine uit de Server groep, bijvoorbeeld *myvm.contoso.com*, en selecteer vervolgens **volgende**.
1. Klik op de pagina **Server functies** op **volgende**.
1. Vouw op de pagina **functies** het knoop punt **Remote Server Administration Tools** uit en vouw vervolgens het knoop punt **hulpprogram ma's voor functie beheer** uit.

    Kies **AD DS en AD LDS Hulpprogram ma's** functie in de lijst met hulpprogram ma's voor functie beheer en selecteer vervolgens **volgende**.

    ![Installeer de AD DS-en AD LDS-Hulpprogram Ma's op de pagina functies](./media/tutorial-create-management-vm/install-features.png)

1. Selecteer **installeren**op de pagina **bevestiging** . Het kan een paar minuten duren voordat de beheer Programma's zijn geïnstalleerd.
1. Wanneer de installatie van de functie is voltooid, selecteert u **sluiten** om de wizard **functies en onderdelen toevoegen** af te sluiten.

## <a name="use-active-directory-administrative-tools"></a>Active Directory-beheer Programma's gebruiken

Als u de beheer Programma's hebt geïnstalleerd, ziet u hoe u deze kunt gebruiken voor het beheren van het door Azure AD DS beheerde domein. Zorg ervoor dat u bent aangemeld bij de virtuele machine met een gebruikers account dat lid is van de groep *Aad DC* -Administrators.

1. Selecteer **Windows systeem beheer**in het menu **Start** . De AD-beheer Programma's die in de vorige stap zijn geïnstalleerd, worden weer gegeven.

    ![Lijst met beheer Programma's die op de server zijn geïnstalleerd](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Selecteer **Active Directory-beheercentrum**.
1. Als u de door Azure AD DS beheerde domein wilt verkennen, kiest u de domein naam in het linkerdeel venster, zoals *contoso.com*. Twee containers met de naam *AADDC computers* en *AADDC-gebruikers* bevinden zich boven aan de lijst.

    ![Een lijst maken van de beschik bare containers in het beheerde Azure AD DS-domein](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Als u de gebruikers en groepen wilt zien die deel uitmaken van de Azure AD DS beheerde domein, selecteert u de container **AADDC-gebruikers** . De gebruikers accounts en-groepen van uw Azure AD-Tenant worden weer gegeven in deze container.

    In de volgende voorbeeld uitvoer worden een gebruikers account met de naam *contosoadmin* en een groep voor *Aad DC-beheerders* weer gegeven in deze container.

    ![Bekijk de lijst met Azure AD DS-domein gebruikers in de Active Directory-beheercentrum](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Selecteer de container **AADDC computers** als u de computers wilt zien die lid zijn van het Azure AD DS beheerde domein. Er wordt een vermelding voor de huidige virtuele machine weer gegeven, zoals *myVM*. Computer accounts voor alle computers die lid zijn van het Azure AD DS beheerde domein worden opgeslagen in deze *AADDC* -container computers.

Algemene Active Directory-beheercentrum acties, zoals het opnieuw instellen van een wacht woord voor een gebruikers account of het beheren van groepslid maatschap, zijn beschikbaar. U kunt ook de *Active Directory-module voor Windows Power shell*gebruiken, geïnstalleerd als onderdeel van de systeem beheer, voor het beheren van algemene acties in uw door Azure AD DS beheerd domein.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Meer informatie over de beschik bare beheer taken in een door Azure AD DS beheerd domein
> * Installeer de Active Directory-beheer Programma's op een Windows Server-VM
> * De Active Directory-beheercentrum gebruiken om algemene taken uit te voeren

Schakel Secure Lightweight Directory Access Protocol (LDAPS) in om veilig te communiceren met uw Azure AD DS beheerde domein.

> [!div class="nextstepaction"]
> [Secure LDAP configureren voor uw beheerde domein](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
