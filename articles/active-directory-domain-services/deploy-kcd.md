---
title: Beperkte delegatie met kerberos voor Azure AD-domeinservices | Microsoft Documenten
description: Meer informatie over het inschakelen van Kerberos constrained delegation (KCD) in een door Azure Active Directory Domain Services beheerd domein.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 07aa9ade25d1d986833b6da2f3907d07b752b662
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655427"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Kerberos constrained delegation (KCD) configureren in Azure Active Directory Domain Services

Wanneer u toepassingen uitvoert, kan het nodig zijn dat deze toepassingen toegang krijgen tot bronnen in de context van een andere gebruiker. Active Directory Domain Services (AD DS) ondersteunt een mechanisme genaamd *Kerberos-delegatie* waarmee deze use-case wordt ingeschakeld. Kerberos *constrained* delegation (KCD) bouwt vervolgens voort op dit mechanisme om specifieke bronnen te definiëren die toegankelijk zijn in de context van de gebruiker. Beheerde azure Active Directory Domain Services (Azure AD DS)-beheerde domeinen zijn veiliger vergrendeld dan traditionele on-premises AD DS-omgevingen, dus gebruik een veiligere kcd *op basis van resources.*

In dit artikel ziet u hoe u kerberos-beperkte delegatie configureren in een door Azure AD DS beheerd domein.

## <a name="prerequisites"></a>Vereisten

Om dit artikel te voltooien, hebt u de volgende bronnen nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Maak en configureer indien nodig [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance].
* Een Windows Server-beheer-VM die is verbonden met het beheerde Azure AD DS-domein.
    * Voer indien nodig de zelfstudie in om [een Windows Server-vm][create-join-windows-vm] te maken en deze samen te voegen aan een beheerd domein en installeer vervolgens [de AD DS-beheertools.][tutorial-create-management-vm]
* Een gebruikersaccount dat lid is van de Azure *AD DC-beheerdersgroep* in uw Azure AD-tenant.

## <a name="kerberos-constrained-delegation-overview"></a>Overzicht van de delegatie met beperkte kerberos

Met Kerberos-delegatie kan het ene account zich voordoen als een ander account om toegang te krijgen tot bronnen. Een webtoepassing die toegang heeft tot een back-endwebcomponent kan zich bijvoorbeeld voordoen als een ander gebruikersaccount wanneer deze de back-endverbinding maakt. Kerberos-delegatie is onveilig omdat het niet beperkt tot de bronnen waartoe het nabootsende account toegang heeft.

Kerberos constrained delegation (KCD) beperkt de services of resources die een bepaalde server of toepassing kan verbinden wanneer ze zich voordoen als een andere identiteit. Traditionele KCD vereist domeinbeheerdersrechten om een domeinaccount voor een service te configureren en beperkt het account om op één domein uit te voeren.

Traditionele KCD heeft ook een paar problemen. In eerdere besturingssystemen kon de servicebeheerder bijvoorbeeld niet weten welke front-endservices zijn gedelegeerd aan de resourceservices die ze bezaten. Elke front-endservice die kan worden gedelegeerd aan een resourceservice, was een mogelijk aanvalspunt. Als een server die een front-endservice heeft gehost die is geconfigureerd om te delegeren aan resourceservices, is gecompromitteerd, kunnen de resourceservices ook worden aangetast.

In een door Azure AD DS beheerd domein hebt u geen bevoegdheden voor domeinbeheerders. Als gevolg hiervan kan traditionele KCD op basis van een account niet worden geconfigureerd in een Azure AD DS een beheerd domein. Op resources gebaseerde KCD kan in plaats daarvan worden gebruikt, wat ook veiliger is.

### <a name="resource-based-kcd"></a>KCD op basis van resources

Windows Server 2012 en hoger biedt servicebeheerders de mogelijkheid om beperkte delegatie voor hun service te configureren. Dit model staat bekend als op resources gebaseerde KCD. Met deze aanpak kan de back-end servicebeheerder specifieke front-endservices toestaan of weigeren om KCD te gebruiken.

KCD op basis van resources is geconfigureerd met PowerShell. U gebruikt de cmdlets [Set-ADComputer][Set-ADComputer] of [Set-ADUser,][Set-ADUser] afhankelijk van of het nabootsende account een computeraccount of een gebruikersaccount/serviceaccount is.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>KCD op basis van resources configureren voor een computeraccount

Laten we in dit scenario aannemen dat u een web-app hebt die wordt uitgevoerd op de computer met de naam *contoso-webapp.aaddscontoso.com*. De web-app moet toegang krijgen tot een web-API die wordt uitgevoerd op de computer met de naam *contoso-api.aaddscontoso.com* in de context van domeingebruikers. Voer de volgende stappen uit om dit scenario te configureren:

1. [Een aangepaste organisatie-eenheid maken](create-ou.md). U machtigingen voor het beheren van deze aangepaste organisatie-eenheid delegeren aan gebruikers binnen het beheerde Azure AD DS-domein.
1. [Sluit zich aan bij de virtuele machines][create-join-windows-vm], zowel de software waarop de web-app wordt uitgevoerd, als degene die de web-API uitvoert, naar het beheerde Azure AD DS-domein. Maak deze computeraccounts in de aangepaste organisatie-eenheid uit de vorige stap.

    > [!NOTE]
    > De computeraccounts voor de web-app en de web-API moeten zich in een aangepaste organisatie-eenheid bevindt waar u machtigingen hebt om kcd op basis van bronnen te configureren. U kcd op basis van resources niet configureren voor een computeraccount in de ingebouwde *AAD DC-computers-container.*

1. Configureer ten slotte kcd op basis van resources met de cmdlet [Set-ADComputer][Set-ADComputer] PowerShell. Voer vanuit uw domein-samengevoegde beheer-VM de volgende cmdlets uit. *Azure AD DC administrators* Geef indien nodig uw eigen computernamen op:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>KCD op basis van bronnen configureren voor een gebruikersaccount

Laten we in dit scenario aannemen dat u een web-app hebt die wordt uitgevoerd als een serviceaccount met de naam *appsvc.* De web-app moet toegang krijgen tot een web-API die wordt uitgevoerd als een serviceaccount met de naam *backendsvc* in de context van domeingebruikers. Voer de volgende stappen uit om dit scenario te configureren:

1. [Een aangepaste organisatie-eenheid maken](create-ou.md). U machtigingen voor het beheren van deze aangepaste organisatie-eenheid delegeren aan gebruikers binnen het beheerde Azure AD DS-domein.
1. [Sluit zich aan bij de virtuele machines][create-join-windows-vm] die de backendweb-API/-bron uitvoeren naar het beheerde Azure AD DS-domein. Het computeraccount maken binnen de aangepaste organisatie-eenheid.
1. Maak het serviceaccount (bijvoorbeeld 'appsvc') dat wordt gebruikt om de web-app in de aangepaste ou uit te voeren.

    > [!NOTE]
    > Nogmaals, het computeraccount voor de web-API VM en het serviceaccount voor de web-app moeten zich in een aangepaste organisatie-eenheid bevindt waar u machtigingen hebt om op resources gebaseerde KCD te configureren. U kcd op basis van resources niet configureren voor accounts in de ingebouwde *AAD DC-computers* of *AAD DC-gebruikers.* Dit betekent ook dat u geen gebruikersaccounts gebruiken die vanuit Azure AD zijn gesynchroniseerd om KCD op basis van resources in te stellen. U moet serviceaccounts maken en gebruiken die specifiek zijn gemaakt in Azure AD DS.

1. Configureer ten slotte kcd op basis van resources met de cmdlet [Set-ADUser][Set-ADUser] PowerShell. Voer vanuit uw domein-samengevoegde beheer-VM de volgende cmdlets uit. *Azure AD DC administrators* Geef indien nodig uw eigen servicenamen op:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Volgende stappen

Zie Overzicht van beperkte [delegatie voor Kerberos][kcd-technet]voor meer informatie over hoe delegatie werkt in Active Directory Domain Services.

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
