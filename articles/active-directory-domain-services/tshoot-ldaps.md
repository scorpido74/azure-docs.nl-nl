---
title: Problemen met Secure LDAP (LDAPS) oplossen in Azure AD Domain Services | Microsoft Docs
description: Problemen met Secure LDAP (LDAPS) voor een door Azure AD Domain Services beheerd domein oplossen
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 285af0e5e5d5ab03027fc29064a5f3623ed10e2f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617050"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Problemen met Secure LDAP (LDAPS) voor een door Azure AD Domain Services beheerd domein oplossen

## <a name="connection-issues"></a>Verbindingsproblemen
Als u problemen ondervindt bij het maken van verbinding met het beheerde domein met behulp van secure LDAP:

* De uitgevers keten van het beveiligde LDAP-certificaat moet worden vertrouwd op de client. U kunt ervoor kiezen om de basis certificerings instantie toe te voegen aan het vertrouwde basis certificaat archief op de client om de vertrouwens relatie tot stand te brengen.
* Controleer of de LDAP-client (bijvoorbeeld Ldp. exe) verbinding maakt met het beveiligde LDAP-eind punt met behulp van een DNS-naam, niet op het IP-adres.
* Controleer de DNS-naam waarmee de LDAP-client verbinding maakt. Het moet worden omgezet in het open bare IP-adres voor beveiligde LDAP op het beheerde domein.
* Controleer of het beveiligde LDAP-certificaat voor uw beheerde domein de DNS-naam in het onderwerp of het kenmerk alternatieve namen voor onderwerp bevat.
* De NSG-instellingen voor het virtuele netwerk moeten het verkeer naar poort 636 van Internet toestaan. Deze stap is alleen van toepassing als u beveiligde LDAP-toegang via internet hebt ingeschakeld.


## <a name="need-help"></a>Hulp nodig?
Als u nog steeds problemen hebt met het maken van verbinding met het beheerde domein met behulp van secure LDAP, [neemt u contact op met het product team](contact-us.md) voor hulp. Neem de volgende informatie op om het probleem beter te kunnen vaststellen:
* Een scherm opname van Ldp. exe die de verbinding maakt en mislukt.
* Uw Azure AD-Tenant-ID en de DNS-domein naam van uw beheerde domein.
* De naam van de gebruiker waarmee u een binding wilt maken.


## <a name="related-content"></a>Gerelateerde inhoud
* [Azure AD Domain Services aan de slag-hand leiding](tutorial-create-instance.md)
* [Een Azure AD Domain Services domein beheren](tutorial-create-management-vm.md)
* [Basis principes LDAP-query's](https://technet.microsoft.com/library/aa996205.aspx)
* [groepsbeleid voor Azure AD Domain Services beheren](manage-group-policy.md)
* [Netwerkbeveiligingsgroepen](../virtual-network/security-overview.md)
* [Een netwerk beveiligings groep maken](../virtual-network/tutorial-filter-network-traffic.md)
