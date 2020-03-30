---
title: Veilige LDAP-waarschuwingen oplossen in Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over het oplossen en oplossen van veelvoorkomende waarschuwingen met beveiligde LDAP voor Azure Active Directory Domain Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258055"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Bekende problemen: Secure LDAP-waarschuwingen in Azure Active Directory Domain Services

Toepassingen en services die ldap (Lightweight Directory Access Protocol) gebruiken om te communiceren met Azure Active Directory Domain Services (Azure AD DS) kunnen worden [geconfigureerd om veilige LDAP te gebruiken.](tutorial-configure-ldaps.md) Een geschikt certificaat en vereiste netwerkpoorten moeten open staan om een veilige LDAP correct te laten werken.

In dit artikel u veelvoorkomende waarschuwingen begrijpen en oplossen met beveiligde LDAP-toegang in Azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Beveiligde LDAP-netwerkconfiguratie

### <a name="alert-message"></a>Waarschuwingsbericht

*Beveiligde LDAP via het internet is ingeschakeld voor het beheerde domein. De toegang tot poort 636 is echter niet vergrendeld met behulp van een netwerkbeveiligingsgroep. Dit kan gebruikersaccounts op het beheerde domein blootstellen aan brute-force-aanvallen met een wachtwoord.*

### <a name="resolution"></a>Oplossing

Wanneer u beveiligde LDAP inschakelt, wordt aanbevolen om aanvullende regels te maken die de inkomende LDAPS-toegang tot specifieke IP-adressen beperken. Deze regels beschermen het door Azure AD DS beheerde domein tegen brute force-aanvallen. Voer de volgende stappen uit om de netwerkbeveiligingsgroep bij te werken om de toegang tot TCP-poort 636 voor beveiligde LDAP te beperken:

1. Zoek en selecteer **netwerkbeveiligingsgroepen**in de Azure-portal.
1. Kies de netwerkbeveiligingsgroep die is gekoppeld aan uw beheerde domein, zoals *AADDS-contoso.com-NSG*en selecteer **Vervolgens Binnenkomende beveiligingsregels**
1. **+ Voeg** een regel toe voor TCP-poort 636. Selecteer indien nodig **Geavanceerd** in het venster om een regel te maken.
1. Kies voor de **bron** *IP-adressen* in het vervolgkeuzemenu. Voer de bron-IP-adressen in die u toegang wilt verlenen voor veilig LDAP-verkeer.
1. Kies *Any* als **bestemming**en voer *vervolgens 636* in voor **bestemmingspoortbereiken.**
1. Stel het **protocol** in als *TCP* en de **actie** om dit toe te *staan*.
1. Geef de prioriteit voor de regel op en voer vervolgens een naam in zoals *RestrictLDAPS*.
1. Wanneer u klaar bent, selecteert u **Toevoegen** om de regel te maken.

De status van het Azure AD DS-domein wordt automatisch binnen twee uur bijgewerkt en verwijdert de waarschuwing.

> [!TIP]
> TCP-poort 636 is niet de enige regel die nodig is om Azure AD DS soepel te laten werken. Zie de [beveiligingsgroepen en vereiste poorten van Azure AD DS Network](network-considerations.md#network-security-groups-and-required-ports)voor meer informatie.

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Verlopen LDAP-certificaat beveiligen

### <a name="alert-message"></a>Waarschuwingsbericht

*Het beveiligde LDAP-certificaat voor het beheerde domein vervalt op [datum]].*

### <a name="resolution"></a>Oplossing

Maak een vervangend beveiligd LDAP-certificaat door de stappen te volgen om [een certificaat voor beveiligde LDAP](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)te maken. Pas het vervangingscertificaat toe op Azure AD DS en distribueer het certificaat naar alle clients die verbinding maken met beveiligde LDAP.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen hebt, [opent u een Azure-ondersteuningsaanvraag][azure-support] voor extra hulp bij het oplossen van problemen.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
