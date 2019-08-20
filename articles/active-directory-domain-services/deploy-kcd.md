---
title: 'Azure Active Directory Domain Services: Beperkte Kerberos-delegering inschakelen | Microsoft Docs'
description: Beperkte Kerberos-delegering inschakelen voor Azure Active Directory Domain Services beheerde domeinen
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: f234eaea0d4df3859ef9458ea334f1b7616add34
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612941"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Kerberos-beperkte delegering (KCD) configureren voor een beheerd domein
Veel toepassingen moeten toegang hebben tot bronnen in de context van de gebruiker. Active Directory ondersteunt een mechanisme met de naam Kerberos-delegatie, waarmee dit gebruik kan worden ingeschakeld. Verder kunt u delegering beperken zodat alleen specifieke bronnen kunnen worden geopend in de context van de gebruiker. Azure AD Domain Services beheerde domeinen verschillen van traditionele Active Directory domeinen, omdat ze veiliger zijn vergrendeld.

In dit artikel wordt beschreven hoe u beperkte Kerberos-delegering configureert in een Azure AD Domain Services beheerd domein.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Kerberos-beperkte delegering (KCD)
Met Kerberos-delegering kan een account een andere beveiligingsprincipal (zoals een gebruiker) imiteren om toegang te krijgen tot bronnen. Overweeg een webtoepassing die toegang heeft tot een back-end web-API in de context van een gebruiker. In dit voor beeld imiteert de webtoepassing (uitgevoerd in de context van een service account of een computer/machine-account) de gebruiker bij het openen van de bron (back-end web-API). Kerberos-delegering is onveilig, omdat het niet de resources beperkt die het imitatie account kan openen in de context van de gebruiker.

Kerberos-beperkte delegering (KCD) beperkt de services/resources waarvoor de opgegeven server namens een gebruiker kan optreden. Traditionele KCD vereist domein beheerders rechten om een domein account voor een service te configureren. het account wordt beperkt tot één domein.

Aan traditionele KCD zijn ook enkele problemen gekoppeld. Als de domein beheerder in eerdere besturings systemen KCD voor de service heeft geconfigureerd, had de service beheerder geen handige manier om te weten welke front-end-services zijn overgedragen aan de resource services waarvan ze eigenaar zijn. En een front-end-service die kan delegeren naar een resource service, wordt een potentieel aanvals punt weer gegeven. Als een server waarop een front-end-service wordt uitgevoerd, is aangetast en deze is geconfigureerd om te worden gedelegeerd aan resource Services, kan de resource Services ook worden aangetast.

> [!NOTE]
> U hebt geen domein beheerders rechten op een Azure AD Domain Services beheerd domein. Daarom **kunnen traditionele op account gebaseerde KCD niet worden geconfigureerd in een beheerd domein**. Gebruik op resources gebaseerde KCD zoals beschreven in dit artikel. Dit mechanisme is ook veiliger.
>
>

## <a name="resource-based-kcd"></a>KCD op basis van resources
Met Windows Server 2012 kunnen service beheerders beperkte delegering voor hun service configureren. In dit model kan de back-end-service beheerder specifieke front-end-services toestaan of weigeren met behulp van KCD. Dit model staat bekend als **op bronnen gebaseerde KCD**.

KCD op basis van resources is geconfigureerd met behulp van Power shell. U gebruikt de `Set-ADComputer` `Set-ADUser` cmdlets, afhankelijk van of het imitatie account een computer account of een gebruikers account/service account is.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Op resources gebaseerde KCD configureren voor een computer account in een beheerd domein
Stel dat er een web-app wordt uitgevoerd op de computer ' contoso-webapp.contoso.com '. De resource moet toegang hebben tot de bron (een web-API die wordt uitgevoerd op ' contoso-api.contoso.com ') in de context van domein gebruikers. U kunt als volgt een op resources gebaseerde KCD instellen voor dit scenario:

1. [Een aangepaste organisatie-eenheid maken](create-ou.md). U kunt machtigingen voor het beheren van deze aangepaste organisatie-eenheid overdragen aan gebruikers binnen het beheerde domein.
2. Word lid van beide virtuele machines (de web-app en de Web-API wordt uitgevoerd) aan het beheerde domein. Deze computer accounts maken in de aangepaste organisatie-eenheid.
3. Configureer nu KCD op basis van een bron met behulp van de volgende Power shell-opdracht:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.contoso.com
Set-ADComputer contoso-api.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> De computer accounts voor de web-app en de Web-API moeten zich in een aangepaste OE bevinden, waar u machtigingen hebt voor het configureren van op resources gebaseerde KCD. U kunt geen op resources gebaseerde KCD configureren voor een computer account in de ingebouwde container ' AAD DC-computers '.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Op resources gebaseerde KCD configureren voor een gebruikers account in een beheerd domein
Stel dat u een web-app hebt die wordt uitgevoerd als een service account ' appsvc ' en dat deze toegang moet hebben tot de resource (een web-API die wordt uitgevoerd als een service account-' backendsvc ') in de context van domein gebruikers. Zo kunt u op resources gebaseerde KCD instellen voor dit scenario.

1. [Een aangepaste organisatie-eenheid maken](create-ou.md). U kunt machtigingen voor het beheren van deze aangepaste organisatie-eenheid overdragen aan gebruikers binnen het beheerde domein.
2. Voeg de virtuele machine met de back-end-web-API/resource toe aan het beheerde domein. Maak het computer account binnen de aangepaste organisatie-eenheid.
3. Maak het service account (bijvoorbeeld ' appsvc ') dat wordt gebruikt om de web-app in de aangepaste organisatie-eenheid uit te voeren.
4. Configureer nu KCD op basis van een bron met behulp van de volgende Power shell-opdracht:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Zowel het computer account voor de back-end web-API als het service account moet zich in een aangepaste OE bevinden, waar u machtigingen hebt voor het configureren van op resources gebaseerde KCD. U kunt geen op resources gebaseerde KCD configureren voor een computer account in de ingebouwde container domein controllers DC-computers of voor gebruikers accounts in de ingebouwde ' AAD DC-gebruikers ' container. Daarom kunt u geen gebruikers accounts die zijn gesynchroniseerd vanuit Azure AD gebruiken om KCD op basis van resources in te stellen.
>

## <a name="related-content"></a>Gerelateerde inhoud
* [Azure AD Domain Services aan de slag-hand leiding](tutorial-create-instance.md)
* [Overzicht van beperkte Kerberos-overdracht](https://technet.microsoft.com/library/jj553400.aspx)
