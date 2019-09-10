---
title: Beperkte Kerberos-overdracht voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het inschakelen van op resources gebaseerde Kerberos-beperkte delegering (KCD) in een door Azure Active Directory Domain Services beheerd domein.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: iainfou
ms.openlocfilehash: 89bc690e5a8c8d24d7732dd4e12f70a9f1f368af
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842651"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Kerberos-beperkte delegering (KCD) configureren in Azure Active Directory Domain Services

Wanneer u toepassingen uitvoert, moeten deze toepassingen mogelijk toegang hebben tot bronnen in de context van een andere gebruiker. Active Directory Domain Services (AD DS) ondersteunt een mechanisme met de naam *Kerberos delegering* , waarmee dit gebruik kan worden ingeschakeld. Kerberos- *beperkte* delegering (KCD) wordt vervolgens gebaseerd op dit mechanisme om specifieke bronnen te definiëren waartoe toegang kan worden verkregen in de context van de gebruiker. De beheerde domeinen van Azure Active Directory Domain Services (Azure AD DS) zijn veiliger vergrendeld op de traditionele on-premises AD DS omgevingen. Gebruik daarom een veiligere op *bronnen gebaseerde* KCD.

In dit artikel wordt beschreven hoe u resource-basd Kerberos-beperkte delegering configureert in een door Azure AD DS beheerd domein.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory Tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
    * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een Azure Active Directory Domain Services beheerd domein ingeschakeld en geconfigureerd in uw Azure AD-Tenant.
    * Als dat nodig is, kunt [u een Azure Active Directory Domain Services-exemplaar maken en configureren][create-azure-ad-ds-instance].
* Een Windows Server Management-VM die deel uitmaakt van het door Azure AD DS beheerde domein.
    * Als dat nodig is, voltooit u de zelf studie voor het [maken van een virtuele Windows Server-machine en voegt u deze toe aan een beheerd domein en][create-join-windows-vm] installeert u vervolgens [de AD DS-beheer hulpprogramma's][tutorial-create-management-vm].
* Een gebruikers account dat lid is van de groep *Azure AD DC-Administrators* in uw Azure AD-Tenant.

## <a name="kerberos-constrained-delegation-overview"></a>Overzicht van beperkte Kerberos-overdracht

Met Kerberos-overdracht kan een account een ander account imiteren voor toegang tot resources. Zo kan een webtoepassing die toegang heeft tot een webonderdeel van de back-end zichzelf imiteren als een ander gebruikers account wanneer dit de back-endverbinding maakt. Kerberos-delegering is onveilig omdat hiermee niet wordt beperkt tot welke bronnen het imitatie account toegang heeft.

Kerberos-beperkte delegering (KCD) beperkt de services of bronnen waarmee een opgegeven server of toepassing verbinding kan maken wanneer er een andere identiteit wordt geïmiteerd. Traditionele KCD vereist domein beheerders rechten voor het configureren van een domein account voor een service. het account wordt beperkt om te worden uitgevoerd op één domein. Traditionele KCD hebben ook een aantal problemen. In eerdere besturings systemen had de service beheerder bijvoorbeeld geen handige manier om te weten welke front-end-services zijn overgedragen aan de resource services waarvan ze eigenaar zijn. Een front-end-service die kan delegeren naar een resource service was een mogelijk aanvals punt. Als een server die een front-end-service heeft die is geconfigureerd voor delegeren aan resource Services, is aangetast, kan de resource Services ook worden aangetast.

In een door Azure AD DS beheerd domein hebt u geen domein beheerders rechten. Als gevolg hiervan kunnen traditionele op accounts gebaseerde KCD niet worden geconfigureerd in een Azure-AD DS een beheerd domein. Op resources gebaseerde KCD kunnen in plaats daarvan worden gebruikt. Dit is ook veiliger.

### <a name="resource-based-kcd"></a>KCD op basis van resources

Windows Server 2012 en hoger biedt service beheerders de mogelijkheid beperkte delegering voor hun service te configureren. Dit model staat bekend als op bronnen gebaseerde KCD. Met deze methode kan de back-end-service beheerder specifieke front-end-services toestaan of weigeren met behulp van KCD.

KCD op basis van resources is geconfigureerd met behulp van Power shell. U gebruikt de [set-ADComputer-][Set-ADComputer] of [set-ADUser-][Set-ADUser] cmdlets, afhankelijk van het feit of het imitatie account een computer account of een gebruikers account/service account is.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Op resources gebaseerde KCD configureren voor een computer account

In dit scenario wordt ervan uitgegaan dat u een web-app hebt die wordt uitgevoerd op de computer met de naam *Contoso-webapp.contoso.com*. De web-app moet toegang hebben tot een web-API die wordt uitgevoerd op de computer met de naam *Contoso-API.contoso.com* in de context van domein gebruikers. Voer de volgende stappen uit om dit scenario te configureren:

1. [Een aangepaste organisatie-eenheid maken](create-ou.md). U kunt machtigingen voor het beheren van deze aangepaste organisatie-eenheid overdragen aan gebruikers binnen het Azure AD DS beheerde domein.
1. [Domein: Voeg de virtuele machines samen][create-join-windows-vm], zowel de toepassing die de web-app uitvoert als de toepassing die de Web-API uitvoert, naar de Azure AD DS beheerde domein. Maak deze computer accounts in de aangepaste organisatie-eenheid uit de vorige stap.

    > [!NOTE]
    > De computer accounts voor de web-app en de Web-API moeten zich in een aangepaste OE bevinden, waar u machtigingen hebt voor het configureren van op resources gebaseerde KCD. U kunt geen op resources gebaseerde KCD configureren voor een computer account in de container ingebouwde *Aad DC-computers* .

1. Configureer ten slotte KCD op basis van een resource met behulp van de Power shell-cmdlet [set-ADComputer][Set-ADComputer] . Voer de volgende cmdlets uit vanaf de virtuele machine voor het beheer van het domein en u bent aangemeld als gebruikers account dat lid is van de groep *Azure AD DC-Administrators* . Geef uw eigen computer namen op als dat nodig is:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.contoso.com
    Set-ADComputer contoso-api.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Op resources gebaseerde KCD configureren voor een gebruikers account

In dit scenario wordt ervan uitgegaan dat u een web-app hebt die wordt uitgevoerd als een service account met de naam *appsvc*. De web-app moet toegang hebben tot een web-API die wordt uitgevoerd als een service account met de naam *backendsvc* in de context van domein gebruikers. Voer de volgende stappen uit om dit scenario te configureren:

1. [Een aangepaste organisatie-eenheid maken](create-ou.md). U kunt machtigingen voor het beheren van deze aangepaste organisatie-eenheid overdragen aan gebruikers binnen het Azure AD DS beheerde domein.
1. [Domein: Voeg de virtuele machines][create-join-windows-vm] die de back-end web-API/resource uitvoeren, toe aan het beheerde domein van Azure AD DS. Maak het computer account binnen de aangepaste organisatie-eenheid.
1. Maak het service account (bijvoorbeeld ' appsvc ') dat wordt gebruikt om de web-app in de aangepaste organisatie-eenheid uit te voeren.

    > [!NOTE]
    > Opnieuw, het computer account voor de Web-API-VM en het service account voor de web-app moeten zich in een aangepaste OE bevinden waar u machtigingen hebt voor het configureren van op resources gebaseerde KCD. U kunt geen op resources gebaseerde KCD configureren voor accounts in de containers ingebouwde *Aad DC-computers* of *Aad DC-gebruikers* . Dit betekent ook dat u geen gebruik kunt maken van gebruikers accounts die zijn gesynchroniseerd vanuit Azure AD om KCD op basis van resources in te stellen. U moet Service accounts maken en gebruiken die specifiek zijn gemaakt in azure AD DS.

1. Configureer ten slotte KCD op basis van een resource met behulp van de Power shell-cmdlet [set-ADUser][Set-ADUser] . Voer de volgende cmdlets uit vanaf de virtuele machine voor het beheer van het domein en u bent aangemeld als gebruikers account dat lid is van de groep *Azure AD DC-Administrators* . Geef waar nodig uw eigen service namen op:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Volgende stappen

Zie overzicht van beperkte [Kerberos-delegering][kcd-technet]voor meer informatie over hoe delegering werkt in Active Directory Domain Services.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
