---
title: Problemen met domein-join oplossen met Azure AD Domain Services | Microsoft Documenten
description: Lees hoe u veelvoorkomende problemen oplossen wanneer u probeert lid te worden van een vm of een toepassing verbindt met Azure Active Directory Domain Services en u geen verbinding maken of verifiëren met het beheerde domein.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: f187dba4eace61695a72e4b7b08731e65ff0d7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299105"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Problemen met het oplossen van problemen met het oplossen van domeinen met een beheerd Azure AD Domain Services-domein

Wanneer u probeert lid te worden van een virtuele machine (VM) of een toepassing verbindt met een door Azure Active Directory Domain Services (AD DS) beheerd domein, u een foutmelding krijgen dat u dit niet doen. Als u problemen met het deelnemen aan domeinen wilt oplossen, controleert u op welke van de volgende punten u een probleem hebt:

* Als u geen verificatieprompt ontvangt, kan de VM of -toepassing geen verbinding maken met het beheerde Azure AD DS-domein.
    * Begin met het oplossen [van verbindingsproblemen voor domeinverbinding.](#connectivity-issues-for-domain-join)
* Als u een fout ontvangt tijdens de verificatie, is de verbinding met het beheerde Azure AD DS-domein succesvol.
    * Begin met het oplossen van [problemen met referenties tijdens het deelnemen aan het domein](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Verbindingsproblemen voor domein-join

Als de VM het beheerde Azure AD DS-beheerde domein niet kan vinden, is er meestal een netwerkverbinding of configuratieprobleem. Bekijk de volgende stappen voor het oplossen van problemen om het probleem op te sporen en op te lossen:

1. Controleer of de VM is verbonden met hetzelfde of een virtueel netwerk met peered dat is ingeschakeld voor Azure AD DS. Als dit niet het zo is, kan de VM het domein niet vinden en verbinden om lid te worden.
    * Als de VM niet is verbonden met hetzelfde virtuele netwerk, controleert u of de virtuele netwerkpeering of VPN-verbinding *actief* of *verbonden* is om het verkeer correct te laten stromen.
1. Probeer het domein te pingen met de domeinnaam van het `ping aaddscontoso.com`door Azure AD DS beheerde domein, zoals .
    * Als het ping-antwoord mislukt, probeert u de IP-adressen te pingen voor het domein dat wordt `ping 10.0.0.4`weergegeven op de overzichtspagina in de portal voor uw door Azure AD DS beheerde domein, zoals .
    * Als u het IP-adres pingen, maar niet het domein, is DNS mogelijk onjuist geconfigureerd. Controleer of u de dns-servers van Azure AD DS-beheerde domein voor het virtuele netwerk hebt geconfigureerd.
1. Probeer de DNS-resolver-cache op de `ipconfig /flushdns`virtuele machine door te spoelen, zoals .

### <a name="network-security-group-nsg-configuration"></a>NSG-configuratie (Network Security Group)

Wanneer u een door Azure AD DS beheerd domein maakt, wordt er ook een netwerkbeveiligingsgroep gemaakt met de juiste regels voor een succesvolle domeinbewerking. Als u aanvullende regels voor netwerkbeveiligingsgroepen bewerkt of maakt, u onbedoeld poorten blokkeren die nodig zijn voor Azure AD DS om verbindings- en verificatieservices te bieden. Deze regels voor netwerkbeveiligingkunnen problemen veroorzaken, zoals wachtwoordsynchronisatie die niet wordt voltooid, dat gebruikers zich niet kunnen aanmelden of problemen met het deelnemen aan het domein.

Als u nog steeds verbindingsproblemen hebt, controleert u de volgende stappen voor het oplossen van problemen:

1. Controleer de status van uw door Azure AD DS beheerde domein in de Azure-portal. Als u een waarschuwing hebt voor *AADDS001,* blokkeert een regel voor netwerkbeveiligingsgroepen de toegang.
1. Controleer de [vereiste regels voor poorten en netwerkbeveiligingsgroepen][network-ports]. Zorg ervoor dat er geen regels voor netwerkbeveiligingsgroepen worden toegepast op de VM of het virtuele netwerk dat u verbindt vanuit het blokkeren van deze netwerkpoorten.
1. Zodra problemen met de configuratie van de netwerkbeveiligingsgroep zijn opgelost, verdwijnt de *AADDS001-waarschuwing* binnen ongeveer 2 uur van de statuspagina. Als de netwerkconnectiviteit nu beschikbaar is, probeert u opnieuw lid te worden van de virtuele machine.

## <a name="credentials-related-issues-during-domain-join"></a>Problemen met referenties tijdens domein-join

Als u een dialoogvenster krijgt waarin referenties worden gevraagd om lid te worden van het beheerde Azure AD DS-domein, kan de VM verbinding maken met het domein via het virtuele Azure-netwerk. Het domeinjoinproces mislukt bij het verifiëren van het domein of de autorisatie om het domeinjoinproces te voltooien met behulp van de referenties die worden verstrekt.

Als u problemen met betrekking tot referenties wilt oplossen, controleert u de volgende stappen voor het oplossen van problemen:

1. Probeer de UPN-indeling te gebruiken `dee@aaddscontoso.onmicrosoft.com`om referenties op te geven, zoals . Controleer of deze UPN correct is geconfigureerd in Azure AD.
    * De *SAMAccountName* voor uw account kan automatisch worden gegenereerd als er meerdere gebruikers zijn met hetzelfde UPN-voorvoegsel in uw tenant of als uw UPN-voorvoegsel te lang is. Daarom kan de *SAMAccountName-indeling* voor uw account afwijken van wat u verwacht of gebruikt in uw on-premises domein.
1. Probeer de referenties te gebruiken voor een gebruikersaccount dat deel uitmaakt van het beheerde Azure AD DS-domein om VM's toe te voegen aan het beheerde domein.
1. Zorg ervoor dat u [wachtwoordsynchronisatie][enable-password-sync] hebt ingeschakeld en lang genoeg hebt gewacht tot de oorspronkelijke wachtwoordsynchronisatie is voltooid.

## <a name="next-steps"></a>Volgende stappen

Zie [Join- en verificatieproblemen][join-authentication-issues]voor een beter begrip van de Active Directory-processen als onderdeel van de domeinjoinbewerking.

Als u nog steeds problemen hebt met het samenvoegen van uw VM met het beheerde Azure AD DS-domein, [zoekt u hulp en opent u een ondersteuningsticket voor Azure Active Directory.][azure-ad-support]

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
