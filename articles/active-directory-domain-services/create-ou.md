---
title: Een organisatie-eenheid (OU) maken in Azure AD Domain Services | Microsoft Docs'
description: Meer informatie over het maken en beheren van een aangepaste organisatie-eenheid (OU) in een beheerd Azure AD Domain Services-domein.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 7abbdf03e85f425f65a45e6640b82529c2b9c84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614072"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Een organisatie-eenheid (OU) maken in een beheerd Azure AD Domain Services-domein

Met organisatie-eenheden (OE's) in Active Directory Domain Services (AD DS) u objecten zoals gebruikersaccounts, serviceaccounts of computeraccounts logisch groeperen. U vervolgens beheerders toewijzen aan specifieke OE's en groepsbeleid toepassen om gerichte configuratie-instellingen af te dwingen.

Azure AD DS-beheerde domeinen bevatten twee ingebouwde OE's - *AADDC-computers* en *AADDC-gebruikers*. De *aaddc-computers-organisatie* bevat computerobjecten voor alle computers die zijn verbonden met het beheerde domein. De *aaddc-gebruikers-organisatie-eenheid* bevat gebruikers en groepen die zijn gesynchroniseerd vanuit de Azure AD-tenant. Terwijl u workloads maakt en uitvoert die Azure AD DS gebruiken, moet u mogelijk serviceaccounts maken voor toepassingen om zichzelf te verifiëren. Als u deze serviceaccounts wilt ordenen, maakt u vaak een aangepaste organisatie-eenheid in het beheerde Azure AD DS-domein en maakt u serviceaccounts binnen die organisatie-eenheid.

In een hybride omgeving worden de in een on-premises AD DS-omgeving gemaakte gegevens niet gesynchroniseerd met Azure AD DS. Azure AD DS-beheerde domeinen gebruiken een platte-eenheidsstructuur. Alle gebruikersaccounts en -groepen worden opgeslagen in de container *AADDC-gebruikers,* ondanks dat ze zijn gesynchroniseerd vanuit verschillende on-premises domeinen of forests, zelfs als u daar een hiërarchische organisatiestructuur hebt geconfigureerd.

In dit artikel ziet u hoe u een organisatie-eenheid maakt in uw door Azure AD DS beheerde domein.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Vul indien nodig de zelfstudie in om [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance]te maken en te configureren.
* Een Windows Server-beheer-VM die is verbonden met het beheerde Azure AD DS-domein.
    * Vul indien nodig de zelfstudie in om [een beheer-vm][tutorial-create-management-vm]te maken.
* Een gebruikersaccount dat lid is van de Azure *AD DC-beheerdersgroep* in uw Azure AD-tenant.

## <a name="custom-ou-considerations-and-limitations"></a>Aangepaste ou overwegingen en beperkingen

Wanneer u aangepaste UU's maakt in een door Azure AD DS beheerd domein, krijgt u extra beheerflexibiliteit voor gebruikersbeheer en het toepassen van groepsbeleid. In vergelijking met een on-premises AD DS-omgeving zijn er enkele beperkingen en overwegingen bij het maken en beheren van een aangepaste OU-structuur in Azure AD DS:

* Als u aangepaste U.S. wilt maken, moeten gebruikers lid zijn van de groep *AAD DC-beheerders.*
* Een gebruiker die een aangepaste organisatie-eenheid maakt, krijgt beheerdersbevoegdheden (volledige controle) over die organisatie-eenheid en is de eigenaar van de resource.
    * Standaard heeft de groep *AAD DC-beheerders* ook de volledige controle over de aangepaste organisatie-eenheid.
* Er wordt een standaard-organisatievoor *AADDC-gebruikers* gemaakt die alle gesynchroniseerde gebruikersaccounts van uw Azure AD-tenant bevat.
    * U gebruikers of groepen niet verplaatsen van de *aaddc-gebruikers-organisatie-eenheid* naar aangepaste oe's die u maakt. Alleen gebruikersaccounts of resources die zijn gemaakt in het beheerde Azure AD DS-domein kunnen worden verplaatst naar aangepaste gegevens.
* Gebruikersaccounts, groepen, serviceaccounts en computerobjecten die u maakt onder aangepaste o.a.s. zijn niet beschikbaar in uw Azure AD-tenant.
    * Deze objecten worden niet weergegeven met de Microsoft Graph API of in de Azure AD UI. ze zijn alleen beschikbaar in uw door Azure AD DS beheerde domein.

## <a name="create-a-custom-ou"></a>Een aangepaste organisatie-eenheid maken

Als u een aangepaste organisatie-eenheid wilt maken, gebruikt u de Active Directory-beheerhulpprogramma's van een vm die is verbonden met een domein. Met het Active Directory-beheercentrum u resources weergeven, bewerken en maken in een door Azure AD DS beheerd domein, inclusief Ok's.

> [!NOTE]
> Als u een aangepaste organisatie-eenheid wilt maken in een door Azure AD DS beheerd domein, moet u zijn aangemeld bij een gebruikersaccount dat lid is van de groep *AAD DC-beheerders.*

1. Meld u aan bij uw management VM. Zie Verbinding maken met een Windows Server VM voor stappen over het maken van verbinding via de [Azure-portal.][connect-windows-server-vm]
1. Selecteer In het startscherm de optie **Systeembeheer**. Er wordt een lijst met beschikbare beheerhulpprogramma's weergegeven die in de zelfstudie zijn geïnstalleerd om [een beheer-VM][tutorial-create-management-vm]te maken.
1. Als u Ok's wilt maken en beheren, selecteert u **Active Directory-beheercentrum** in de lijst met beheergereedschappen.
1. Kies in het linkerdeelvenster uw door Azure AD DS beheerde domein, zoals *aaddscontoso.com*. Een lijst met bestaande O's en resources wordt weergegeven:

    ![Selecteer uw door Azure AD DS beheerd domein in het Active Directory-beheercentrum](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

1. Het deelvenster **Taken** wordt aan de rechterkant van het Active Directory-beheercentrum weergegeven. Selecteer Onder het domein, zoals *aaddscontoso.com,* de optie **Nieuwe > Organisatie-eenheid**.

    ![Selecteer de optie om een nieuwe organisatie-eenheid te maken in het Active Directory-beheercentrum](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

1. Geef in het dialoogvenster **Organisatieeenheid maken** een **naam** op voor de nieuwe organisatie-eenheid, zoals *MyCustomOu*. Geef een korte beschrijving voor de organisatie-eenheid, zoals *aangepaste organisatievoor serviceaccounts.* Indien gewenst u ook het veld Beheerd door instellen voor de organisatie.If gewenst u ook het veld **Beheerd door** instellen voor de organisatie van de organisatie. Als u de aangepaste organisatie-eenheid wilt maken, selecteert u **OK**.

    ![Een aangepaste organisatie-eenheid maken vanuit het Active Directory-beheercentrum](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

1. Terug in het Active Directory-beheercentrum wordt de aangepaste ou nu weergegeven en is deze beschikbaar voor gebruik:

    ![Aangepaste organisatie-eenheid beschikbaar voor gebruik in het Active Directory-beheercentrum](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het gebruik van de beheertools of het maken en gebruiken van serviceaccounts:

* [Active Directory-beheercentrum: aan de slag](https://technet.microsoft.com/library/dd560651.aspx)
* [Service Accounts Step-by-Step Guide (Engelstalig)](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
