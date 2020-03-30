---
title: Problemen met aanmelding in Azure AD-domeinservices oplossen | Microsoft Documenten
description: Meer informatie over het oplossen van veelvoorkomende aanmeldingsproblemen en fouten in Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 0585ced3bc53f216ab203b4686b5800b5e14bbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612741"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Problemen met aanmeldingsproblemen met een beheerd Azure AD Domain Services-domein oplossen

De meest voorkomende redenen voor een gebruikersaccount dat zich niet kan aanmelden bij een door Azure AD DS beheerd domein, zijn de volgende scenario's:

* [Het account is nog niet gesynchroniseerd met Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS heeft niet de wachtwoordhashes om het account aan te laten melden.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [De rekening is geblokkeerd.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS kan niet synchroniseren in referenties voor accounts die buiten de Azure AD-tenant staan. Externe gebruikers kunnen zich niet aanmelden bij het beheerde Azure AD DS-domein.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Account is nog niet gesynchroniseerd met Azure AD DS

Afhankelijk van de grootte van uw map kan het even duren voordat gebruikersaccounts en referenties beschikbaar zijn in Azure AD DS. Voor grote mappen kan deze eerste eenrichtingssynchronisatie vanuit Azure AD enkele uren en maximaal een dag of twee in houden. Zorg ervoor dat u lang genoeg wacht voordat u de verificatie opnieuw probeert.

Voor hybride omgevingen waarmee Azure AD Connect wordt uitgevoerd om on-premises directorygegevens te synchroniseren met Azure AD, controleert u of u de nieuwste versie van Azure AD Connect uitvoert en Azure AD Connect hebt [geconfigureerd om een volledige synchronisatie uit te voeren nadat azure AD DS is ingeschakeld][azure-ad-connect-phs]. Als u Azure AD DS uitschakelt en vervolgens opnieuw inschakelt, moet u deze stappen opnieuw volgen.

Als u problemen blijft ondervinden met accounts die niet worden gesynchroniseerd via Azure AD Connect, start u de Azure AD Sync Service opnieuw. Open vanaf de computer met Azure AD Connect een opdrachtpromptvenster en voer de volgende opdrachten uit:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS heeft de wachtwoordhashes niet

Azure AD genereert of slaat geen wachtwoordhashes op in de indeling die vereist is voor NTLM- of Kerberos-verificatie totdat u Azure AD DS inschakelt voor uw tenant. Om veiligheidsredenen slaat Azure AD ook geen wachtwoordreferenties op in formulier clear-text. Daarom kan Azure AD deze NTLM- of Kerberos-wachtwoordhashes niet automatisch genereren op basis van de bestaande referenties van gebruikers.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Hybride omgevingen met on-premises synchronisatie

Voor hybride omgevingen die Azure AD Connect gebruiken om te synchroniseren vanuit een on-premises AD DS-omgeving, u lokaal de vereiste NTLM- of Kerberos-wachtwoordhashes genereren en synchroniseren naar Azure AD. Nadat u uw door Azure AD DS beheerde domein hebt gemaakt, [schakelt u wachtwoordhashsynchronisatie in voor Azure Active Directory Domain Services.][azure-ad-connect-phs] Zonder deze wachtwoordhashsynchronisatiestap te voltooien, u zich niet aanmelden bij een account met Azure AD DS. Als u Azure AD DS uitschakelt en vervolgens opnieuw inschakelt, moet u deze stappen opnieuw volgen.

Zie [Hoe wachtwoordhashsynchronisatie werkt voor Azure AD DS voor][phs-process]meer informatie.

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Alleen cloudomgevingen zonder on-premises synchronisatie

Azure AD DS beheerde domeinen zonder on-premises synchronisatie, alleen accounts in Azure AD, moeten ook de vereiste NTLM- of Kerberos-wachtwoordhashes genereren. Als een cloudaccount zich niet kan aanmelden, is een wachtwoordwijzigingsproces voor het account voltooid nadat het Azure AD DS is ingeschakeld?

* **Nee, het wachtwoord is niet gewijzigd.**
    * [Wijzig het wachtwoord voor het account][enable-user-accounts] om de vereiste wachtwoordhashes te genereren en wacht vervolgens 15 minuten voordat u zich opnieuw probeert aan te melden.
    * Als u Azure AD DS uitschakelt en vervolgens opnieuw inschakelt, moet elk account de stappen opnieuw volgen om hun wachtwoord te wijzigen en de vereiste wachtwoordhashes te genereren.
* **Ja, het wachtwoord is gewijzigd.**
    * Probeer je aan te melden met `driley@aaddscontoso.com`de *UPN-indeling,* zoals `AADDSCONTOSO\deeriley`, in plaats van de *SAMAccountName-indeling* zoals .
    * De *SAMAccountName* kan automatisch worden gegenereerd voor gebruikers van wie het UPN-voorvoegsel te lang is of hetzelfde is als een andere gebruiker in het beheerde domein. De *UPN-indeling* is gegarandeerd uniek binnen een Azure AD-tenant.

## <a name="the-account-is-locked-out"></a>Het account is vergrendeld

Een gebruikersaccount in Azure AD DS is vergrendeld wanneer is voldaan aan een gedefinieerde drempelwaarde voor mislukte aanmeldingspogingen. Dit accountlock-outgedrag is ontworpen om u te beschermen tegen herhaalde aanmeldingspogingen met brute kracht die kunnen duiden op een geautomatiseerde digitale aanval.

Als er standaard 5 slechte wachtwoordpogingen in 2 minuten zijn, wordt het account gedurende 30 minuten vergrendeld.

Zie Problemen met [accountuitsluiting oplossen in Azure AD DS][troubleshoot-account-lockout]voor meer informatie en het oplossen van problemen met accountvergrendeling.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen hebt met het samenvoegen van uw VM met het beheerde Azure AD DS-domein, [zoekt u hulp en opent u een ondersteuningsticket voor Azure Active Directory.][azure-ad-support]

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
