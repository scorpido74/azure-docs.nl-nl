---
title: Problemen met accountuitsluiting in Azure AD Domain Services oplossen | Microsoft Documenten
description: Meer informatie over het oplossen van veelvoorkomende problemen waardoor gebruikersaccounts worden vergrendeld in Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/06/2020
ms.author: iainfou
ms.openlocfilehash: 7d2e22804c06f589c7990bf8f19319b897363a93
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743446"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Problemen met accountvergrendeling oplossen met een beheerd Azure AD Domain Services-domein

Azure AD DS vergrendelt accounts na een gedefinieerde drempelwaarde om herhaalde kwaadaardige aanmeldingspogingen te voorkomen. Deze account lockout kan ook per ongeluk gebeuren zonder een aanmeldingsaanvalincident. Als een gebruiker bijvoorbeeld herhaaldelijk het verkeerde wachtwoord invoert of een service probeert een oud wachtwoord te gebruiken, wordt het account vergrendeld.

In dit artikel over het oplossen van problemen wordt beschreven waarom accountuitsluitingen plaatsvinden en hoe u het gedrag configureren en hoe u beveiligingsaudits controleren om vergrendelingsgebeurtenissen op te lossen.

## <a name="what-is-an-account-lockout"></a>Wat is een accountuitsluiting?

Een gebruikersaccount in Azure AD DS is vergrendeld wanneer is voldaan aan een gedefinieerde drempelwaarde voor mislukte aanmeldingspogingen. Dit accountlock-outgedrag is ontworpen om u te beschermen tegen herhaalde aanmeldingspogingen met brute kracht die kunnen duiden op een geautomatiseerde digitale aanval.

**Als er standaard 5 slechte wachtwoordpogingen in 2 minuten zijn, wordt het account gedurende 30 minuten vergrendeld.**

De standaarddrempelwaarden voor accountvergrendeling worden geconfigureerd met behulp van fijnmazige wachtwoordbeleid. Als u een specifieke set vereisten hebt, u deze standaarddrempelwaarden voor accountvergrendeling overschrijven. Het is echter niet aan te raden om de drempelwaarden te verhogen om te proberen de nummeraccountuitsluitingen te verminderen. Problemen met de bron van het vergrendelingsgedrag van het account eerst oplossen.

### <a name="fine-grained-password-policy"></a>Fijnkorrelig wachtwoordbeleid

Met fijnmazig wachtwoordbeleid (FGPP's) u specifieke beperkingen toepassen voor het beleid voor wachtwoord- en accountvergrendeling op verschillende gebruikers in een domein. FGPP heeft alleen gevolgen voor gebruikers binnen een door Azure AD DS beheerd domein. Cloudgebruikers en domeingebruikers die vanuit Azure AD zijn gesynchroniseerd met het beheerde Azure AD-domein, worden alleen beïnvloed door het wachtwoordbeleid binnen Azure AD DS. Hun accounts in Azure AD of een on-premises directory worden niet beïnvloed.

Beleidsregels worden gedistribueerd via groepskoppeling in het door Azure AD DS beheerde domein en alle wijzigingen die u aanbrengt, worden toegepast bij de volgende aanmelding van de gebruiker. Als u het beleid wijzigt, wordt een gebruikersaccount dat al is vergrendeld, niet ontgrendeld.

Zie [Wachtwoord- en accountuitsluitingsbeleid configureren][configure-fgpp]voor meer informatie over fijnmazige wachtwoordbeleidsregels en de verschillen tussen gebruikers die rechtstreeks zijn gemaakt in Azure AD DS versus gesynchroniseerd vanuit Azure AD.

## <a name="common-account-lockout-reasons"></a>Algemene redenen voor het vergrendelen van een account

De meest voorkomende redenen waarom een account moet worden vergrendeld, zonder kwade opzet of factoren, zijn de volgende scenario's:

* **De gebruiker sloot zichzelf buiten.**
    * Is de gebruiker na een recente wachtwoordwijziging een vorig wachtwoord blijven gebruiken? Het standaard accountuitsluitingsbeleid van 5 mislukte pogingen in 2 minuten kan worden veroorzaakt doordat de gebruiker per ongeluk een oud wachtwoord opnieuw probeert.
* **Er is een toepassing of service die een oud wachtwoord heeft.**
    * Als een account wordt gebruikt door toepassingen of services, kunnen deze bronnen herhaaldelijk proberen in te loggen met een oud wachtwoord. Dit gedrag zorgt ervoor dat het account wordt vergrendeld.
    * Probeer het accountgebruik voor meerdere verschillende toepassingen of services te minimaliseren en registreren waar referenties worden gebruikt. Als een accountwachtwoord wordt gewijzigd, werkt u de bijbehorende toepassingen of services dienovereenkomstig bij.
* **Wachtwoord is gewijzigd in een andere omgeving en het nieuwe wachtwoord is nog niet gesynchroniseerd.**
    * Als een accountwachtwoord wordt gewijzigd buiten Azure AD DS, zoals in een on-prem AD DS-omgeving, kan het enkele minuten duren voordat de wachtwoordwijziging wordt gesynchroniseerd via Azure AD en in Azure AD DS.
    * Een gebruiker die zich via Azure AD DS bij een resource probeert aan te melden voordat dat wachtwoordsynchronisatieproces is voltooid, zorgt ervoor dat zijn account wordt vergrendeld.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Problemen met accountuitsluitingen oplossen met beveiligingsaudits

Als u problemen wilt oplossen wanneer gebeurtenissen voor accountuitsluiting optreden en waar ze vandaan komen, [schakelt u beveiligingsaudits in voor Azure AD DS.][security-audit-events] Controlegebeurtenissen worden alleen vastgelegd vanaf het moment dat u de functie inschakelt. Idealiter moet u beveiligingsaudits inschakelen *voordat* er een probleem is met de vergrendeling van een account om problemen op te lossen. Als een gebruikersaccount herhaaldelijk vergrendelingsproblemen heeft, u beveiligingsaudits klaarmaken voor de volgende keer dat de situatie zich voordoet.

Nadat u beveiligingsaudits hebt ingeschakeld, u in de volgende voorbeeldquery's zien hoe u *Account Lockout-gebeurtenissen*, code *4740*, controleren.

Bekijk alle gebeurtenis op het vergrendelen van een account van de afgelopen zeven dagen:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Bekijk alle account lockout gebeurtenissen voor de afgelopen zeven dagen voor het account met de naam *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Bekijk alle account lockout-evenementen tussen 26 juni 2019 en 09:00 uur. en 1 juli 2019 middernacht, gesorteerd op basis van datum en tijd:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Volgende stappen

Zie Wachtwoord- [en accountuitsluitingsbeleid configureren][configure-fgpp]voor meer informatie over fijnmazige wachtwoordbeleid om drempelwaarden voor accountvergrendeling aan te passen.

Als u nog steeds problemen hebt met het samenvoegen van uw VM met het beheerde Azure AD DS-domein, [zoekt u hulp en opent u een ondersteuningsticket voor Azure Active Directory.][azure-ad-support]

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
