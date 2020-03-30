---
title: Waarschuwingen voor netwerkbeveiligingsgroepen oplossen in Azure AD DS | Microsoft Documenten
description: Meer informatie over het oplossen en oplossen van waarschuwingen voor netwerkbeveiligingvoor configuratie voor Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257992"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Bekende problemen: netwerkconfiguratiewaarschuwingen in Azure Active Directory Domain Services

Als u toepassingen en services correct wilt laten communiceren met Azure Active Directory Domain Services (Azure AD DS), moeten specifieke netwerkpoorten open staan om verkeer te laten stromen. In Azure beheert u de verkeersstroom met behulp van netwerkbeveiligingsgroepen. De status van een door Azure AD DS beheerd domein wordt een waarschuwing weergegeven als de vereiste regels voor netwerkbeveiligingsgroepen niet zijn ingevoerd.

In dit artikel u veelvoorkomende waarschuwingen voor problemen met de configuratie van netwerkbeveiligingsgroepen begrijpen en oplossen.

## <a name="alert-aadds104-network-error"></a>Waarschuwing AADDS104: Netwerkfout

### <a name="alert-message"></a>Waarschuwingsbericht

*Microsoft kan de domeincontrollers voor dit beheerde domein niet bereiken. Dit kan gebeuren als een NSG-netwerkbeveiligingsgroep (NSG) die is geconfigureerd op uw virtuele netwerk de toegang tot het beheerde domein blokkeert. Een andere mogelijke reden is als er een door de gebruiker gedefinieerde route is die binnenkomend verkeer van het internet blokkeert.*

Ongeldige netwerkbeveiligingsgroepregels zijn de meest voorkomende oorzaak van netwerkfouten voor Azure AD DS. De netwerkbeveiligingsgroep voor het virtuele netwerk moet toegang bieden tot specifieke poorten en protocollen. Als deze poorten zijn geblokkeerd, kan het Azure-platform het beheerde domein niet controleren of bijwerken. De synchronisatie tussen de Azure AD-map en het beheerde domein azure AD DS wordt ook beïnvloed. Zorg ervoor dat u de standaardpoorten open houdt om onderbreking van de service te voorkomen.

## <a name="default-security-rules"></a>Standaardbeveiligingsregels

De volgende standaardinkomende en uitgaande beveiligingsregels worden toegepast op de netwerkbeveiligingsgroep voor een door Azure AD DS beheerd domein. Deze regels houden Azure AD DS veilig en stellen het Azure-platform in staat om het beheerde domein te bewaken, te beheren en bij te werken. U ook een aanvullende regel hebben die binnenkomend verkeer toestaat als u [beveiligde LDAP configureert.][configure-ldaps]

### <a name="inbound-security-rules"></a>Inkomende beveiligingsregels

| Prioriteit | Name | Poort | Protocol | Bron | Doel | Actie |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Alle | Toestaan |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw (CorpNetSaw) | Alle | Toestaan |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Alle | Toestaan |
| 65000    | AllVnetInBound | Alle | Alle | VirtualNetwork | VirtualNetwork | Toestaan |
| 65001    | AllowAzureLoadBalancerInBound | Alle | Alle | AzureLoadBalancer | Alle | Toestaan |
| 65500    | DenyAllInBound | Alle | Alle | Alle | Alle | Weigeren |

### <a name="outbound-security-rules"></a>Uitgaande beveiligingsregels

| Prioriteit | Name | Poort | Protocol | Bron | Doel | Actie |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Alle | Alle | VirtualNetwork | VirtualNetwork | Toestaan |
| 65001    | AllowAzureLoadBalancerOutBound | Alle | Alle |  Alle | Internet | Toestaan |
| 65500    | DenyAllOutBound | Alle | Alle | Alle | Alle | Weigeren |

>[!NOTE]
> Azure AD DS heeft onbeperkte uitgaande toegang van het virtuele netwerk nodig. We raden u af om aanvullende regels te maken die uitgaande toegang voor het virtuele netwerk beperken.

## <a name="verify-and-edit-existing-security-rules"></a>Bestaande beveiligingsregels verifiëren en bewerken

Voer de volgende stappen uit om de bestaande beveiligingsregels te verifiëren en ervoor te zorgen dat de standaardpoorten zijn geopend:

1. Zoek en selecteer **netwerkbeveiligingsgroepen**in de Azure-portal.
1. Kies de netwerkbeveiligingsgroep die is gekoppeld aan uw beheerde domein, zoals *AADDS-contoso.com-NSG*.
1. Op de pagina **Overzicht** worden de bestaande inkomende en uitgaande beveiligingsregels weergegeven.

    Bekijk de binnenkomende en uitgaande regels en vergelijk met de lijst met vereiste regels in het vorige gedeelte. Selecteer indien nodig aangepaste regels die vereist verkeer blokkeren en verwijder deze. Als een van de vereiste regels ontbreekt, voegt u een regel toe in de volgende sectie.

    Nadat u regels hebt toegevoegd of verwijderd om het vereiste verkeer toe te staan, wordt de status van het Azure AD DS-domein binnen twee uur automatisch bijgewerkt en wordt de waarschuwing verwijderd.

### <a name="add-a-security-rule"></a>Een beveiligingsregel toevoegen

Voer de volgende stappen uit om een ontbrekende beveiligingsregel toe te voegen:

1. Zoek en selecteer **netwerkbeveiligingsgroepen**in de Azure-portal.
1. Kies de netwerkbeveiligingsgroep die is gekoppeld aan uw beheerde domein, zoals *AADDS-contoso.com-NSG*.
1. Klik **onder Instellingen** in het linkerdeelvenster op *Binnenkomende beveiligingsregels* of *uitgaande beveiligingsregels,* afhankelijk van welke regel u moet toevoegen.
1. Selecteer **Toevoegen**en maak vervolgens de vereiste regel op basis van de poort, het protocol, de richting, enz. Wanneer u klaar bent, selecteert u **OK**.

Het duurt even voordat de beveiligingsregel is toegevoegd en in de lijst wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen hebt, [opent u een Azure-ondersteuningsaanvraag][azure-support] voor extra hulp bij het oplossen van problemen.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
