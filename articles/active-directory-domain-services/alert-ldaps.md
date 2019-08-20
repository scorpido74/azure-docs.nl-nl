---
title: 'Azure Active Directory Domain Services: Problemen met secure LDAP oplossen | Microsoft Docs'
description: Problemen met Secure LDAP voor Azure AD Domain Services oplossen
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 8f9f4a8b52548dad011f5e825fa42c50da970ea7
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613151"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services-problemen met de Secure LDAP configuratie oplossen

Dit artikel bevat oplossingen voor veelvoorkomende problemen bij het [configureren van beveiligde LDAP](tutorial-configure-ldaps.md) voor Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Configuratie van netwerk beveiligings groep Secure LDAP

**Waarschuwings bericht:**

*Secure LDAP via internet is ingeschakeld voor het beheerde domein. Toegang tot poort 636 is echter niet vergrendeld met behulp van een netwerk beveiligings groep. Hierdoor kunnen gebruikers accounts in het beheerde domein worden blootgesteld aan wacht woorden voor het afdwingen van wachtwoord aanvallen.*

### <a name="secure-ldap-port"></a>Secure LDAP poort

Wanneer secure LDAP is ingeschakeld, raden we u aan extra regels te maken om alleen binnenkomende LDAPS-toegang van bepaalde IP-adressen toe te staan. Met deze regels wordt uw domein beschermd tegen beveiligings aanvallen die een bedreiging vormen voor de beveiliging. Poort 636 biedt toegang tot uw beheerde domein. Hier vindt u informatie over het bijwerken van uw NSG om toegang toe te staan voor Secure LDAP:

1. Ga naar het [tabblad netwerk beveiligings groepen](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) in de Azure Portal
2. Kies de NSG die aan uw domein is gekoppeld in de tabel.
3. Klik op **regels voor binnenkomende beveiliging**
4. De regel voor poort 636 maken
   1. Klik op **toevoegen** in de bovenste navigatie balk.
   2. Kies **IP-adressen** voor de bron.
   3. Geef de bronbereiken van de bron poort op voor deze regel.
   4. Invoer "636" voor de doel poortbereiken.
   5. Protocol is **TCP**.
   6. Geef de regel een geschikte naam, beschrijving en prioriteit. De prioriteit van deze regel moet hoger zijn dan de prioriteit van de regel ' alles weigeren ', als u er een hebt.
   7. Klik op **OK**.
5. Controleer of de regel is gemaakt.
6. Controleer de status van uw domein in twee uur om ervoor te zorgen dat u de juiste stappen hebt voltooid.

> [!TIP]
> Poort 636 is niet de enige regel die nodig is om Azure AD Domain Services soepel uit te voeren. Ga voor meer informatie naar de [netwerk richtlijnen](network-considerations.md) of [los problemen met NSG-configuratie](alert-nsg.md) artikelen op.
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Secure LDAP certificaat verloopt

**Waarschuwings bericht:**

*Het beveiligde LDAP-certificaat voor het beheerde domein verloopt op [date]].*

**Opgelost**

Maak een nieuw beveiligd LDAP-certificaat door de stappen te volgen die worden beschreven in het artikel [Configure secure LDAP](tutorial-configure-ldaps.md) .

## <a name="contact-us"></a>Contact opnemen
Neem contact op met het product team van Azure Active Directory Domain Services om [feedback te delen of voor ondersteuning](contact-us.md).
