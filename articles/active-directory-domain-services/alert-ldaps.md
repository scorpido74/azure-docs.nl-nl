---
title: Veilige LDAP-waarschuwingen oplossen in Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het oplossen van veelvoorkomende waarschuwingen met beveiligde LDAP voor Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: iainfou
ms.openlocfilehash: 06b0fa1979f18981ec5cf78dc9a9dbad8b196394
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258055"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Bekende problemen: Waarschuwingen Secure LDAP in Azure Active Directory Domain Services

Toepassingen en services die gebruikmaken van Lightweight Directory Access Protocol (LDAP) om te communiceren met Azure Active Directory Domain Services (Azure AD DS) kunnen worden [geconfigureerd voor het gebruik van secure LDAP](tutorial-configure-ldaps.md). Er moet een geschikt certificaat en de vereiste netwerk poorten zijn geopend voor een goede werking van de beveiliging van LDAP.

Dit artikel helpt u bij het begrijpen en oplossen van veelvoorkomende waarschuwingen met beveiligde LDAP-toegang in azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Secure LDAP netwerk configuratie

### <a name="alert-message"></a>Waarschuwings bericht

*Secure LDAP via internet is ingeschakeld voor het beheerde domein. Toegang tot poort 636 is echter niet vergrendeld met behulp van een netwerk beveiligings groep. Hierdoor kunnen gebruikers accounts in het beheerde domein worden blootgesteld aan wacht woorden voor het afdwingen van wachtwoord aanvallen.*

### <a name="resolution"></a>Oplossing

Wanneer u beveiligd LDAP inschakelt, is het raadzaam extra regels te maken voor het beperken van de toegang tot specifieke IP-adressen van de toegangs rechten voor het binnenkomende LDAPS. Met deze regels wordt het Azure AD DS beheerde domein beschermd tegen beveiligings aanvallen. Voer de volgende stappen uit om de netwerk beveiligings groep bij te werken om de TCP-poort 636 voor beveiligde LDAP te beperken:

1. In de Azure Portal zoekt en selecteert u **netwerk beveiligings groepen**.
1. Kies de netwerk beveiligings groep die is gekoppeld aan uw beheerde domein, zoals *AADDS-contoso.com-NSG*, en selecteer vervolgens **regels voor binnenkomende beveiliging**
1. **+** Een regel toevoegen voor TCP-poort 636. Selecteer, indien nodig, **Geavanceerd** in het venster om een regel te maken.
1. Kies *IP-adressen* in de vervolg keuzelijst voor de **bron**. Voer de bron-IP-adressen in die u toegang wilt verlenen voor beveiligd LDAP-verkeer.
1. Kies *een* van de **doel**waarden en voer *636* in als **doel poort bereik**.
1. Stel het **protocol** in als *TCP* en de **actie** die moet worden *toegestaan*.
1. Geef de prioriteit voor de regel op en voer een naam in, zoals *RestrictLDAPS*.
1. Wanneer u klaar bent, selecteert u **toevoegen** om de regel te maken.

De status van het beheerde domein van Azure AD DS wordt binnen twee uur automatisch bijgewerkt en de waarschuwing wordt verwijderd.

> [!TIP]
> TCP-poort 636 is niet de enige regel die nodig is om Azure AD DS soepel uit te voeren. Zie voor meer informatie de [Azure AD DS-netwerk beveiligings groepen en de vereiste poorten](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Secure LDAP certificaat verloopt

### <a name="alert-message"></a>Waarschuwings bericht

*Het beveiligde LDAP-certificaat voor het beheerde domein verloopt op [date]].*

### <a name="resolution"></a>Oplossing

Maak een vervangend beveiligd LDAP-certificaat door de stappen te volgen om [een certificaat voor beveiligde LDAP te maken](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap). Het vervangende certificaat Toep assen op Azure AD DS en het certificaat distribueren naar clients die verbinding maken met behulp van beveiligde LDAP.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen ondervindt, [opent u een ondersteunings aanvraag voor Azure][azure-support] voor aanvullende hulp bij het oplossen van problemen.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
