---
title: Zelfstudie - Een beheer-VM maken voor Azure Active Directory Domain Services | Microsoft Documenten
description: In deze zelfstudie leert u hoe u een virtuele Windows-machine maakt en configureert die u gebruikt om het exemplaar van Azure Active Directory Domain Services te beheren.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 09fcf88c6dfe90380f387c6d72c751634f5b1606
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475733"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Zelfstudie: Een beheer-vm maken om een beheerd Azure Directory Domain Services-beheerd domein te configureren en te beheren

Azure Active Directory Domain Services (AD DS) biedt beheerde domeinservices zoals domeinjoin, groepsbeleid, LDAP en Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. U beheert dit beheerde domein met dezelfde RSAT (Remote Server Administration Tools) als met een on-premises Active Directory Domain Services-domein. Aangezien Azure AD DS een beheerde service is, zijn er enkele beheertaken die u niet uitvoeren, zoals het gebruik van extern bureaublad-protocol (RDP) om verbinding te maken met de domeincontrollers.

In deze zelfstudie ziet u hoe u een Windows Server VM in Azure maakt en de vereiste hulpprogramma's installeert om een door Azure AD DS beheerd domein te beheren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Inzicht in de beschikbare beheertaken in een beheerd Azure AD DS-domein
> * De hulpprogramma's voor Active Directory-beheer installeren op een Windows Server-vm
> * Het Active Directory-beheercentrum gebruiken om veelvoorkomende taken uit te voeren

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Raadpleeg indien nodig de eerste zelfstudie voor [het maken en configureren van een Azure Active Directory Domain Services-exemplaar.][create-azure-ad-ds-instance]
* Een Windows Server-vm die is verbonden met het beheerde Azure AD DS-domein.
    * Raadpleeg indien nodig de vorige zelfstudie om [een Windows Server-vm te maken en deze samen te voegen bij een beheerd domein.][create-join-windows-vm]
* Een gebruikersaccount dat lid is van de Azure *AD DC-beheerdersgroep* in uw Azure AD-tenant.
* Een Azure Bastion-host die is geïmplementeerd in uw virtuele Azure AD DS-netwerk.
    * Maak indien nodig [een Azure Bastion-host][azure-bastion].

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie maakt en configureert u een beheer-VM met behulp van de Azure-portal. Meld u eerst aan bij de [Azure-portal](https://portal.azure.com)om aan de slag te gaan.

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Beschikbare beheertaken in Azure AD DS

Azure AD DS biedt een beheerd domein voor uw gebruikers, toepassingen en services die kunnen worden gebruikt. Deze aanpak wijzigt een aantal beschikbare beheertaken die u uitvoeren en welke bevoegdheden u binnen het beheerde domein hebt. Deze taken en machtigingen kunnen anders zijn dan wat u ervaart met een reguliere on-premises Active Directory Domain Services-omgeving. U ook geen verbinding maken met domeincontrollers in het door Azure AD DS beheerde domein met Extern bureaublad.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Beheertaken die u uitvoeren op een door Azure AD DS beheerd domein

Leden van de groep *AAD DC-beheerders* krijgen bevoegdheden op het beheerde Azure AD DS-domein waarmee ze taken kunnen uitvoeren, zoals:

* Configureer het ingebouwde groepsbeleidsobject (GPO) voor de containers *AADDC-computers* en *AADDC-gebruikers* in het beheerde domein.
* DNS beheren in het beheerde domein.
* Aangepaste organisatie-eenheden (OU's) maken en beheren op het beheerde domein.
* Beheerderstoegang verkrijgen tot computers die aan het beheerde domein zijn gekoppeld.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Beheerdersbevoegdheden die u niet hebt op een door Azure AD DS beheerd domein

Het beheerde Azure AD DS-domein is vergrendeld, zodat u geen bevoegdheden hebt om bepaalde beheertaken op het domein uit te voeren. Enkele van de volgende voorbeelden zijn taken die u niet uitvoeren:

* Het schema van het beheerde domein uitbreiden.
* Maak verbinding met domeincontrollers voor het beheerde domein met Extern bureaublad.
* Domeincontrollers toevoegen aan het beheerde domein.
* U hebt geen bevoegdheden *voor domeinbeheerder* of *Bedrijfsbeheerder* voor het beheerde domein.

## <a name="sign-in-to-the-windows-server-vm"></a>Aanmelden bij de WINDOWS Server-vm

In de vorige zelfstudie is een Windows Server VM gemaakt en verbonden met het beheerde Azure AD DS-domein. Laten we die VM gebruiken om de beheertools te installeren. Volg indien nodig [de stappen in de zelfstudie om een Windows Server VM te maken en lid te worden van een beheerd domein.][create-join-windows-vm]

> [!NOTE]
> In deze zelfstudie gebruikt u een Windows Server VM in Azure die is verbonden met het beheerde Azure AD DS-domein. U ook een Windows-client gebruiken, zoals Windows 10, die is verbonden met het beheerde domein.
>
> Zie [RsAT (Remote Server Administration Tools) installeren](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) voor meer informatie over het installeren van de beheerhulpprogramma's op een Windows-client.

Maak als volgt verbinding met de WINDOWS Server VM om aan de slag te gaan:

1. Selecteer **resourcegroepen** aan de linkerkant in de Azure-portal. Kies de resourcegroep waarin uw VM is gemaakt, zoals *myResourceGroup,* en selecteer vervolgens de VM, zoals *myVM*.
1. Selecteer In het deelvenster **Overzicht** voor uw vm de optie **Verbinden**en vervolgens **Bastion**.

    ![Verbinding maken met de virtuele machine van Windows met Bastion in de Azure-portal](./media/join-windows-vm/connect-to-vm.png)

1. Voer de referenties voor uw vm in en selecteer **Verbinding maken**.

   ![Verbinding maken via de Bastion-host in de Azure-portal](./media/join-windows-vm/connect-to-bastion.png)

Laat uw webbrowser indien nodig pop-ups openen zodat de Bastion-verbinding wordt weergegeven. Het duurt een paar seconden om de verbinding met uw VM te maken.

## <a name="install-active-directory-administrative-tools"></a>Active Directory-hulpprogramma's installeren

Azure AD DS-beheerde domeinen worden beheerd met dezelfde beheertools als on-premises AD DS-omgevingen, zoals het Active Directory Administrative Center (ADAC) of AD PowerShell. Deze hulpprogramma's kunnen worden geïnstalleerd als onderdeel van de RSAT-functie (Remote Server Administration Tools) op Windows Server en clientcomputers. Leden van de groep *AAD DC-beheerders* kunnen azure AD DS-beheerde domeinen vervolgens op afstand beheren met behulp van deze AD-beheertools vanaf een computer die is verbonden met het beheerde domein.

Voer de volgende stappen uit om de hulpprogramma's voor Active Directory-beheer op een vm met een domein te installeren:

1. Als **Serverbeheer** niet standaard wordt geopend wanneer u zich aanmeldt bij de vm, selecteert u het menu **Start** en kiest u **Serverbeheer**.
1. Selecteer **Rollen en onderdelen toevoegen**in het *dashboardvenster* van het venster **Serverbeheer** .
1. Selecteer **Volgende**op de pagina **Voor het begin** van de wizard Rollen en onderdelen *toevoegen*.
1. Laat voor *het installatietype*de **optie Op rollen of functie gebaseerde installatie** optie aanvinken en selecteer **Volgende**.
1. Kies op de pagina **Serverselectie** de huidige VM uit de servergroep, zoals *myvm.aaddscontoso.com*en selecteer **Volgende**.
1. Klik op de pagina **Serverrollen** op **Volgende**.
1. Vouw op de pagina **Functies** het knooppunt **Hulpmiddelen voor extern serverbeheer** uit en vouw vervolgens het knooppunt Hulpmiddelen voor **rolbeheer** uit.

    Kies de functie **AD DS- en AD LDS-hulpprogramma's** in de lijst met hulpmiddelen voor rolbeheer en selecteer **Volgende**.

    ![De 'AD DS- en AD LDS-hulpprogramma's' installeren op de pagina Functies](./media/tutorial-create-management-vm/install-features.png)

1. Selecteer **installeren** op **Install**de pagina Bevestiging . Het kan een minuut of twee duren om de administratieve hulpmiddelen te installeren.
1. Wanneer de installatie van de functie is voltooid, selecteert u **Sluiten** om de wizard **Rollen en onderdelen toevoegen** af te sluiten.

## <a name="use-active-directory-administrative-tools"></a>Active Directory-beheerhulpprogramma's gebruiken

Met de geïnstalleerde beheertools u zien hoe u deze gebruiken om het beheer van het beheervan het beheer-Azure AD DS-beheerdomein. Zorg ervoor dat u bent aangemeld bij de virtuele machine met een gebruikersaccount dat lid is van de groep *AAD DC-beheerders.*

1. Selecteer **Windows-systeembeheer in**het menu **Start** . De AD-beheergereedschappen die in de vorige stap zijn geïnstalleerd, worden weergegeven.

    ![Lijst met systeemhulpprogramma's die op de server zijn geïnstalleerd](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Selecteer **Active Directory-beheercentrum**.
1. Als u het beheerde Azure AD DS-domein wilt verkennen, kiest u de domeinnaam in het linkerdeelvenster, zoals *aaddscontoso.com*. Twee containers met de naam *AADDC Computers* en *AADDC-gebruikers* staan bovenaan de lijst.

    ![Het beschikbare containersgedeelte van het beheerde Azure AD DS-domein weergeven](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Als u de gebruikers en groepen wilt zien die behoren tot het beheerde Azure AD DS-domein, selecteert u de container **AADDC-gebruikers.** De gebruikersaccounts en -groepen van uw Azure AD-tenant worden in deze container weergegeven.

    In het volgende voorbeeld wordt in deze container een gebruikersaccount met de naam *Contoso Admin* en een groep voor *AAD DC-beheerders* weergegeven.

    ![De lijst met Azure AD DS-domeingebruikers weergeven in het Active Directory-beheercentrum](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Als u de computers wilt zien die zijn verbonden met het beheerde Azure AD DS-domein, selecteert u de container **AADDC-computers.** Een vermelding voor de huidige virtuele machine, zoals *myVM,* wordt vermeld. Computeraccounts voor alle computers die zijn verbonden met het beheerde Azure AD DS-domein, worden opgeslagen in deze container *AADDC-computers.*

Er zijn algemene acties voor Active Directory-beheercentrum beschikbaar, zoals het opnieuw instellen van een gebruikersaccountwachtwoord of het beheren van groepslidmaatschap. Deze acties werken alleen voor gebruikers en groepen die rechtstreeks zijn gemaakt in het beheerde Azure AD DS-domein. Identiteitsgegevens worden alleen gesynchroniseerd *van* Azure AD naar Azure AD DS. Er is geen terugschrijven van Azure AD DS naar Azure AD. U geen wachtwoorden of beheerd groepslidmaatschap wijzigen voor gebruikers die zijn gesynchroniseerd vanuit Azure AD en deze wijzigingen worden gesynchroniseerd.

U ook de *Active Directory-module voor Windows PowerShell*, geïnstalleerd als onderdeel van de beheerhulpprogramma's, gebruiken om veelvoorkomende acties in uw door Azure AD DS beheerde domein te beheren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Inzicht in de beschikbare beheertaken in een beheerd Azure AD DS-domein
> * De hulpprogramma's voor Active Directory-beheer installeren op een Windows Server-vm
> * Het Active Directory-beheercentrum gebruiken om veelvoorkomende taken uit te voeren

Als u veilig wilt communiceren met uw door Azure AD DS beheerde domein, schakelt u het beveiligde Lightweight Directory Access Protocol (LDAPS) in.

> [!div class="nextstepaction"]
> [Beveiligde LDAP configureren voor uw beheerde domein](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
