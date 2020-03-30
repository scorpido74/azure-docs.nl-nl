---
title: Azure AD-toepassingsproxy implementeren voor Azure AD-domeinservices | Microsoft Documenten
description: Meer informatie over het bieden van beveiligde toegang tot interne toepassingen voor externe werknemers door Azure Active Directory Application Proxy te implementeren en configureren in een beheerd Azure Active Directory Domain Services-domein
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/6/2019
ms.author: iainfou
ms.openlocfilehash: c6e4e6a45fbbeab64184d8ae4b0684ba055d7735
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613979"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD-toepassingsproxy implementeren voor beveiligde toegang tot interne toepassingen in een beheerd Azure AD Domain Services-domein

Met Azure AD Domain Services (Azure AD DS) u verouderde toepassingen die on-premises in Azure worden uitgevoerd, opheffen en verschuiven. Azure Active Directory (AD) Application Proxy helpt u vervolgens externe werknemers te ondersteunen door deze interne toepassingen veilig te publiceren als onderdeel van een door Azure AD DS beheerd domein, zodat ze via internet kunnen worden geopend.

Zie Hoe u veilige toegang op afstand tot interne toepassingen bieden als u nieuw bent in de Azure AD-toepassingsproxy en meer wilt weten over het [bieden van beveiligde toegang op afstand tot interne toepassingen.](../active-directory/manage-apps/application-proxy.md)

In dit artikel ziet u hoe u een Azure AD Application Proxy-connector maakt en configureert om veilige toegang te bieden tot toepassingen in een door Azure AD DS beheerd domein.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
    * Een **Azure AD Premium-licentie** is vereist om de Azure AD-toepassingsproxy te gebruiken.
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Maak en configureer indien nodig [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Een windows-vm met een domein maken

Als u verkeer wilt routeren naar toepassingen die in uw omgeving worden uitgevoerd, installeert u de component Azure AD Application Proxy-connector. Deze Azure AD Application Proxy-connector moet zijn geïnstalleerd op virtuele machines (WINDOWS Server) die zijn verbonden met het beheerde Azure AD DS-domein. Voor sommige toepassingen u meerdere servers implementeren die elk de connector hebben geïnstalleerd. Deze implementatieoptie biedt u een grotere beschikbaarheid en helpt zwaardere verificatiebelastingen te verwerken.

De VM waarop de Azure AD Application Proxy-connector wordt uitgevoerd, moet zich op hetzelfde of een virtueel netwerk van peered bevinden waarin u Azure AD DS hebt ingeschakeld. De VM's die vervolgens de toepassingen hosten die u publiceert met de toepassingsproxy, moeten ook worden geïmplementeerd op hetzelfde virtuele Azure-netwerk.

Voer de volgende stappen uit om een vm voor de Azure AD Application Proxy-connector te maken:

1. [Een aangepaste organisatie-eenheid maken](create-ou.md). U machtigingen voor het beheren van deze aangepaste organisatie-eenheid delegeren aan gebruikers binnen het beheerde Azure AD DS-domein. De VM's voor Azure AD-toepassingsproxy en die uw toepassingen uitvoeren, moeten deel uitmaken van de aangepaste organisatie-eenheid, niet van de *standaard-AAD DC-computers-eenheid.*
1. [Sluit zich aan bij de virtuele machines,][create-join-windows-vm]zowel die welke de Azure AD Application Proxy-connector uitvoeren als de apparaten waarop uw toepassingen worden uitgevoerd, naar het beheerde Azure AD DS-domein. Maak deze computeraccounts in de aangepaste organisatie-eenheid uit de vorige stap.

## <a name="download-the-azure-ad-application-proxy-connector"></a>De Azure AD Application Proxy-connector downloaden

Voer de volgende stappen uit om de Azure AD Application Proxy-connector te downloaden. Het installatiebestand dat u downloadt, wordt in de volgende sectie gekopieerd naar uw app-proxy-vm.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een gebruikersaccount met machtigingen *voor Bedrijfsbeheerders* in Azure AD.
1. Zoek naar en selecteer **Azure Active Directory** boven aan de portal en kies Vervolgens **Enterprise-toepassingen**.
1. Selecteer **Toepassingsproxy** in het menu aan de linkerkant. Als u uw eerste connector wilt maken en app-proxy wilt inschakelen, selecteert u de koppeling om een connector te **downloaden.**
1. Accepteer op de downloadpagina de licentievoorwaarden en privacyovereenkomst en selecteer **vervolgens Voorwaarden accepteren & downloaden.**

    ![De Azure AD App Proxy-connector downloaden](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>De Azure AD Application Proxy-connector installeren en registreren

Met een VM klaar om te worden gebruikt als de Azure AD Application Proxy-connector, kopieer en voer u het installatiebestand dat is gedownload van de Azure-portal uit.

1. Kopieer het installatiebestand voor azure AD-toepassingsproxy naar uw vm.
1. Voer het installatiebestand uit, zoals *AADApplicationProxyConnectorInstaller.exe*. Accepteer de licentievoorwaarden voor software.
1. Tijdens de installatie wordt u gevraagd de connector te registreren met de toepassingsproxy in uw Azure AD-map.
   * Geef de referenties op voor een globale beheerder in uw Azure AD-map. De globale ad-referenties van Azure AD kunnen afwijken van uw Azure-referenties in de portal

        > [!NOTE]
        > Het globale beheerdersaccount dat wordt gebruikt om de connector te registreren, moet tot dezelfde map behoren waar u de application proxy-service inschakelt.
        >
        > Als het Azure AD-domein bijvoorbeeld *aaddscontoso.com*is, `admin@aaddscontoso.com` moet de globale beheerder of een andere geldige alias op dat domein zijn.

   * Als de verbeterde beveiligingsconfiguratie van Internet Explorer is ingeschakeld voor de vm waar u de connector installeert, wordt het registratiescherm mogelijk geblokkeerd. Als u toegang wilt verlenen, volgt u de instructies in het foutbericht of schakelt u verbeterde beveiliging van Internet Explorer uit tijdens het installatieproces.
   * Zie [Alleengebeurtenisproxy oplossen](../active-directory/manage-apps/application-proxy-troubleshoot.md)als de connectorregistratie mislukt.
1. Aan het einde van de installatie wordt een notitie weergegeven voor omgevingen met een uitgaande proxy. Als u de Azure AD Application Proxy-connector wilt configureren om via `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`de uitgaande proxy te werken, voert u het meegeleverde script uit, zoals .
1. Op de pagina Toepassingsproxy in de Azure-portal wordt de nieuwe connector weergegeven met de status *Actief*, zoals in het volgende voorbeeld wordt weergegeven:

    ![De nieuwe Azure AD Application Proxy-connector die wordt weergegeven als actief in de Azure-portal](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Als u een hoge beschikbaarheid wilt bieden voor toepassingen die zijn geauthenticeren via de Azure AD-toepassingsproxy, u connectors op meerdere VM's installeren. Herhaal dezelfde stappen in de vorige sectie om de connector te installeren op andere servers die zijn aangesloten bij het beheerde Azure AD DS-domein.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Beperkte delegeren op basis van resources inschakelen

Als u één aanmelding voor uw toepassingen wilt gebruiken met geïntegreerde Windows-verificatie (IWA), geeft u de Azure AD Application Proxy-connectors toestemming om gebruikers na te bootsen en namens hen tokens te verzenden en te ontvangen. Als u deze machtigingen wilt verlenen, configureert u De delegatie (KCD) van Kerberos beperkt voor de connector om toegang te krijgen tot bronnen in het beheerde Azure AD DS-domein. Omdat u geen domeinbeheerdersbevoegdheden hebt in een door Azure AD DS beheerd domein, kan kcd op traditioneel accountniveau niet worden geconfigureerd op een beheerd domein. Gebruik in plaats daarvan kcd op basis van resources.

Zie [Kerberos constrained delegation (KCD) configureren in Azure Active Directory Domain Services](deploy-kcd.md)voor meer informatie.

> [!NOTE]
> U moet zijn aangemeld bij een gebruikersaccount dat lid is van de Azure *AD DC-beheerdersgroep* in uw Azure AD-tenant om de volgende PowerShell-cmdlets uit te voeren.
>
> De computeraccounts voor de VM- en toepassings-vm's van uw app-proxy-connector moeten zich in een aangepaste organisatie-eenheid bevindt, waar u machtigingen hebt om kcd op basis van bronnen te configureren. U kcd op basis van resources niet configureren voor een computeraccount in de ingebouwde *AAD DC-computers-container.*

Gebruik de [Get-ADComputer][Get-ADComputer] om de instellingen op te halen voor de computer waarop de Azure AD Application Proxy-connector is geïnstalleerd. Voer vanuit uw domein-samengevoegde beheer-VM de volgende cmdlets uit. *Azure AD DC administrators*

In het volgende voorbeeld vindt u informatie over het computeraccount met de naam *appproxy.aaddscontoso.com*. Geef uw eigen computernaam op voor de Azure AD Application Proxy VM die in de vorige stappen is geconfigureerd.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Gebruik voor elke toepassingsserver die de apps achter Azure AD Application Proxy uitvoert, de PowerDlet [Set-ADComputer][Set-ADComputer] PowerShell om kcd op basis van bronnen te configureren. In het volgende voorbeeld krijgt de Azure AD Application Proxy-connector machtigingen om de *appserver.aaddscontoso.com* computer te gebruiken:

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Als u meerdere Azure AD Application Proxy connectors implementeert, moet u voor elke connectorinstantie kcd op basis van resources configureren.

## <a name="next-steps"></a>Volgende stappen

Met de Azure AD Application Proxy geïntegreerd met Azure AD DS, publiceert u toepassingen voor gebruikers om toegang te krijgen. Zie [Toepassingen publiceren met Azure AD Application Proxy](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)voor meer informatie.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
