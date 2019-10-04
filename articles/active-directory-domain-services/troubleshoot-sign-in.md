---
title: Problemen met aanmelden oplossen in Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het oplossen van veelvoorkomende problemen met gebruikers aanmelding en fouten in Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: aa03e388019bf696324ea7af6062ec98386df5fa
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827052"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Problemen met het aanmelden van accounts oplossen met een door Azure AD Domain Services beheerd domein

De meest voorkomende redenen voor een gebruikers account waarmee u zich niet kunt aanmelden bij een Azure AD DS beheerd domein, zijn de volgende scenario's:

* [Het account is nog niet gesynchroniseerd met Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS heeft geen wacht woord-hashes waarmee het account kan worden aangemeld.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Het account is vergrendeld.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS kan niet synchroniseren in referenties voor accounts die extern zijn voor de Azure AD-Tenant. Externe gebruikers kunnen zich niet aanmelden bij het beheerde domein van Azure AD DS.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Het account is nog niet gesynchroniseerd in azure AD DS

Afhankelijk van de grootte van uw map kan het enige tijd duren voordat gebruikers accounts en referentie-hashes beschikbaar zijn in azure AD DS. Voor grote mappen kan deze eerste eenrichtings synchronisatie vanuit Azure AD enkele uren duren en Maxi maal één dag of twee. Zorg ervoor dat u lang genoeg wacht voordat u opnieuw probeert te authenticeren.

Voor hybride omgevingen die gebruikers Azure AD Connect om on-premises Directory gegevens te synchroniseren met Azure AD, moet u ervoor zorgen dat u de meest recente versie van Azure AD Connect uitvoert en [Azure AD Connect hebt geconfigureerd om een volledige synchronisatie uit te voeren nadat u Azure hebt ingeschakeld AD DS][azure-ad-connect-phs]. Als u Azure AD DS uitschakelt en vervolgens weer inschakelt, moet u deze stappen opnieuw uitvoeren.

Als er problemen blijven optreden met accounts die niet zijn gesynchroniseerd via Azure AD Connect, start u de Azure AD Sync-service opnieuw. Op de computer waarop Azure AD Connect is geïnstalleerd, opent u een opdracht prompt venster en voert u de volgende opdrachten uit:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS heeft geen wacht woord-hashes

Azure AD genereert of slaat geen wacht woord-hashes in de vereiste indeling voor NTLM-of Kerberos-authenticatie totdat u Azure AD DS voor uw Tenant inschakelt. Uit veiligheids overwegingen slaat Azure AD ook geen wachtwoord referenties op in een normale tekst vorm. Daarom kan Azure AD deze NTLM-of Kerberos-wachtwoord hashes niet automatisch genereren op basis van de bestaande referenties van gebruikers.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Hybride omgevingen met on-premises synchronisatie

Voor hybride omgevingen met Azure AD Connect om te synchroniseren vanuit een on-premises AD DS omgeving, kunt u de vereiste NTLM-of Kerberos-wachtwoord hashes lokaal genereren en synchroniseren in azure AD. Nadat u uw door Azure AD DS beheerde domein hebt gemaakt, schakelt u de [synchronisatie van wacht woord-hashen in op Azure Active Directory Domain Services][azure-ad-connect-phs]. Als u de synchronisatie stap voor wacht woord-hash niet hebt voltooid, kunt u zich niet aanmelden bij een account met behulp van Azure AD DS. Als u Azure AD DS uitschakelt en vervolgens weer inschakelt, moet u deze stappen opnieuw volgen.

Zie [hoe wacht woord-hash synchronisatie werkt voor Azure AD DS][phs-process]voor meer informatie.

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Alleen Cloud omgevingen zonder on-premises synchronisatie

Azure AD DS beheerde domeinen zonder on-premises synchronisatie, alleen accounts in azure AD, moeten ook de vereiste NTLM-of Kerberos-wachtwoord hashes genereren. Als een Cloud account niet kan worden aangemeld, heeft een wachtwoord wijzigings proces voltooid voor het account nadat Azure AD DS is ingeschakeld?

* **Nee, het wacht woord is niet gewijzigd.**
    * [Wijzig het wacht woord voor het account][enable-user-accounts] om de vereiste wacht woord-hashes te genereren en wacht vijf tien minuten voordat u zich opnieuw probeert aan te melden.
    * Als u Azure AD DS uitschakelt en vervolgens weer inschakelt, moet elk account de stappen opnieuw uitvoeren om het wacht woord te wijzigen en de vereiste wachtwoord hashes te genereren.
* **Ja, het wacht woord is gewijzigd.**
    * Meld u aan met de *UPN* -indeling, zoals `driley@contoso.com`, in plaats van de bestands indeling *SAMAccountName* , zoals `CONTOSO\deeriley`.
    * De *SAMAccountName* kan automatisch worden gegenereerd voor gebruikers waarvan het UPN-voor voegsel langer is dan of gelijk is aan die van een andere gebruiker in het beheerde domein. De *UPN* -indeling is gegarandeerd uniek binnen een Azure AD-Tenant.

## <a name="the-account-is-locked-out"></a>Het account is vergrendeld

Een gebruikers account in azure AD DS is vergrendeld wanneer aan een gedefinieerde drempel voor mislukte aanmeldings pogingen is voldaan. Dit account vergrendelings gedrag is ontworpen om u te beschermen tegen herhaalde pogingen om te voor komen dat u zich kunt aanmelden met een geautomatiseerde digitale aanval.

Als er gedurende twee minuten vijf mislukte wachtwoord pogingen zijn, wordt het account 30 minuten vergrendeld.

Zie problemen [met account vergrendeling oplossen in Azure AD DS][troubleshoot-account-lockout]voor meer informatie en hoe u problemen met account vergrendeling kunt oplossen.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen ondervindt met het toevoegen van uw VM aan het beheerde domein van Azure AD DS, [zoekt u hulp en opent u een ondersteunings ticket voor Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
