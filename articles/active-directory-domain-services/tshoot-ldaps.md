---
title: Problemen met beveiligde LDAP in Azure AD Domain Services oplossen | Microsoft Documenten
description: Informatie over het oplossen van problemen met beveiligde LDAP (LDAPS) voor een beheerd Azure Active Directory Domain Services-domein
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132170"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Problemen met beveiligde LDAP-connectiviteit oplossen voor een beheerd Beheerd Azure Directory Domain Services-domein

Toepassingen en services die ldap (Lightweight Directory Access Protocol) gebruiken om te communiceren met Azure Active Directory Domain Services (Azure AD DS) kunnen worden [geconfigureerd om veilige LDAP te gebruiken.](tutorial-configure-ldaps.md) Een geschikt certificaat en vereiste netwerkpoorten moeten open staan om een veilige LDAP correct te laten werken.

Met dit artikel u problemen met beveiligde LDAP-toegang in Azure AD DS oplossen.

## <a name="common-connection-issues"></a>Veelvoorkomende verbindingsproblemen

Als u problemen ondervindt bij het maken van verbinding met een door Azure AD DS beheerd domein met behulp van beveiligde LDAP, controleert u de volgende stappen voor het oplossen van problemen. Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met het beheerde Azure AD DS-domein:

* De emittentketen van het beveiligde LDAP-certificaat moet op de client worden vertrouwd. U de Root-certificeringsinstantie (CA) toevoegen aan het vertrouwde basiscertificaatarchief op de client om de vertrouwensrelatie vast te stellen.
    * Zorg ervoor dat u [het certificaat exporteert en toepast op clientcomputers.][client-cert]
* Controleer of het beveiligde LDAP-certificaat voor uw beheerde domein de DNS-naam heeft in het kenmerk *Onderwerp* of het kenmerk *Alternatieve namen onderwerp.*
    * Controleer de [veilige LDAP-certificaatvereisten][certs-prereqs] en maak indien nodig een vervangend certificaat.
* Controleer of de LDAP-client, zoals *ldp.exe,* verbinding maakt met het beveiligde LDAP-eindpunt met behulp van een DNS-naam en niet met het IP-adres.
    * Het certificaat dat is toegepast op het beheerde Azure AD DS-domein bevat niet de IP-adressen van de service, alleen de DNS-namen.
* Controleer de DNS-naam waartoe de LDAP-client verbinding maakt. Het moet worden opgelost naar het openbare IP-adres voor beveiligde LDAP op het beheerde Azure AD DS-domein.
    * Als de DNS-naam wordt opgelost naar het interne IP-adres, werkt u de DNS-record bij om het externe IP-adres op te lossen.
* Voor externe connectiviteit moet de netwerkbeveiligingsgroep een regel bevatten waarmee het verkeer naar TCP-poort 636 vanaf het internet kan worden beveiligd.
    * Als u verbinding maken met het beheerde Azure AD DS-domein met behulp van beveiligde LDAP van bronnen die rechtstreeks zijn verbonden met het virtuele netwerk, maar niet met externe verbindingen, moet u [een regel voor netwerkbeveiligingmaken om veilig LDAP-verkeer toe te staan.][ldaps-nsg]

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen hebt, [opent u een Azure-ondersteuningsaanvraag][azure-support] voor extra hulp bij het oplossen van problemen.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
