---
title: SharePoint-gebruikersprofielservice inschakelen met Azure AD DS | Microsoft Documenten
description: Meer informatie over het configureren van een beheerd Azure Active Directory Domain Services-domein ter ondersteuning van profielsynchronisatie voor SharePoint Server
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: a684a669c491e35b5c6b62dd318b4fe61edeb52b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655379"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Azure Active Directory Domain Services configureren om synchronisatie van gebruikersprofielen voor SharePoint Server te ondersteunen

SharePoint Server bevat een service om gebruikersprofielen te synchroniseren. Met deze functie kunnen gebruikersprofielen op een centrale locatie worden opgeslagen en toegankelijk zijn voor meerdere SharePoint-sites en -bedrijven. Als u de gebruikersprofielservice van SharePoint Server wilt configureren, moeten de juiste machtigingen worden verleend in een azure active directory domain services (Azure AD DS) beheerd domein. Zie synchronisatie [van gebruikersprofielen in SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx)voor meer informatie.

In dit artikel ziet u hoe u Azure AD DS configureert om de sharepointserver-synchronisatieservice voor gebruikersprofielen toe te staan.

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
* Een SharePoint-serviceaccount voor de synchronisatieservice voor gebruikersprofielen.
    * Zie Indien nodig [Plannen voor beheer- en serviceaccounts in SharePoint Server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Overzicht van serviceaccounts

In een door Azure AD DS beheerd domein bestaat een beveiligingsgroep met de naam **AAD DC Service Accounts** als onderdeel van de organisatie-eenheid *Gebruikers* (OU). Leden van deze beveiligingsgroep worden de volgende bevoegdheden gedelegeerd:

- **Bevoegdheid directorywijzigingen repliceren** op de hoofddse DSE.
- **Bevoegdheid directorywijzigingen repliceren** in de`cn=configuration` context *Configuratienaamgeving* (container).

De beveiligingsgroep **AAD DC-serviceaccounts** is ook lid van de ingebouwde groep **Pre-Windows 2000 Compatible Access.**

Wanneer het serviceaccount voor de synchronisatieservice van SharePoint Server-gebruikersprofiel aan deze beveiligingsgroep wordt toegevoegd, krijgt het de vereiste bevoegdheden om correct te werken.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Ondersteuning inschakelen voor synchronisatie van SharePoint Server-gebruikersprofielen

Het serviceaccount voor SharePoint Server heeft voldoende bevoegdheden nodig om wijzigingen in de map te repliceren en sharepoint server-gebruikersprofielsynchronisatie correct te laten werken. Als u deze bevoegdheden wilt bieden, voegt u het serviceaccount dat wordt gebruikt voor synchronisatie van SharePoint-gebruikersprofielen toe aan de groep **AAD DC-serviceaccounts.**

Voer de volgende stappen uit vanaf uw Azure AD DS-beheerVM:

> [!NOTE]
> Als u groepslidmaatschap wilt bewerken in een door Azure AD DS beheerd domein, moet u zijn aangemeld bij een gebruikersaccount dat lid is van de groep *AAD DC Administrators.*

1. Selecteer In het startscherm de optie **Systeembeheer**. Er wordt een lijst met beschikbare beheerhulpprogramma's weergegeven die in de zelfstudie zijn geïnstalleerd om [een beheer-VM][tutorial-create-management-vm]te maken.
1. Als u groepslidmaatschap wilt beheren, selecteert u **Active Directory-beheercentrum** in de lijst met beheergereedschappen.
1. Kies in het linkerdeelvenster uw door Azure AD DS beheerde domein, zoals *aaddscontoso.com*. Er wordt een lijst met bestaande O's en resources weergegeven.
1. Selecteer de **organisatie-eenheid Gebruikers** en kies vervolgens de beveiligingsgroep *AAD DC-serviceaccounts.*
1. Selecteer **Leden**en kies **Toevoegen...**.
1. Voer de naam van het SharePoint-serviceaccount in en selecteer **OK**. In het volgende voorbeeld wordt het SharePoint-serviceaccount *spadmin*genoemd:

    ![Het SharePoint-serviceaccount toevoegen aan de beveiligingsgroep AAD DC Service Accounts](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Machtigingen voor Active Directory Domain Services voor profielsynchronisatie in SharePoint Server verlenen voor](https://technet.microsoft.com/library/hh296982.aspx) meer informatie

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
