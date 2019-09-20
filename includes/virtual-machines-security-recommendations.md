---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: 60d4957f65c124de683c6d156561de3b52aaec94
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71163941"
---
Dit artikel bevat beveiligings aanbevelingen voor Azure Virtual Machines. Als u deze aanbevelingen implementeert, kunt u voldoen aan uw beveiligings verplichtingen zoals beschreven in ons gedeelde verantwoordelijkheids model en wordt de algehele beveiliging van uw web-app-oplossingen verbeterd. Lees de [gedeelde verantwoordelijkheden voor Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)voor meer informatie over wat micro soft doet aan de verantwoordelijkheden van de service provider.

Enkele van de aanbevelingen in dit artikel kunnen automatisch worden bewaakt door Azure Security Center. Azure Security Center is de eerste verdedigings linie bij het beveiligen van uw resources in Azure. De beveiligings status van uw Azure-resources wordt periodiek geanalyseerd om mogelijke beveiligings problemen te identificeren. Vervolgens krijgt u aanbevelingen over hoe u deze kunt aanpakken.

- Zie [beveiligings aanbevelingen in azure Security Center](../articles/security-center/security-center-recommendations.md)voor meer informatie over Azure Security Center aanbevelingen.
- Zie [Wat is er Azure Security Center?](../articles/security-center/security-center-intro.md) voor informatie over Azure Security Center.

## <a name="recommendations"></a>Aanbevelingen

| Categorie | Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|-|----|--|
| Algemeen | Bij het bouwen van aangepaste VM-installatie kopieën de meest recente updates gebruiken | Voordat u installatie kopieën maakt, moet u ervoor zorgen dat alle meest recente updates zijn geïnstalleerd voor het besturings systeem en alle toepassingen die deel zullen uitmaken van uw installatie kopie.  | - |
| Algemeen | Uw Vm's actueel laten blijven | U kunt de [updatebeheer](../articles/automation/automation-update-management.md) -oplossing in azure Automation gebruiken om updates van het besturings systeem voor uw Windows-en Linux-computers in azure te beheren | [Ja](../articles/security-center/security-center-apply-system-updates.md) |
| Algemeen | Back-ups maken van uw virtuele machines | [Azure backup](../articles/backup/backup-overview.md) helpt bij het beveiligen van uw toepassings gegevens met minimale bedrijfs kosten. Toepassingsfouten kunnen uw gegevens beschadigen, en menselijke fouten kunnen fouten introduceren in uw toepassingen. Met Azure Backup worden uw virtuele machines met Windows en Linux beveiligd. | - |
| Algemeen | Gebruik meerdere Vm's voor grotere tolerantie en beschik baarheid | Als uw VM toepassingen uitvoert die hoge Beschik baarheid moeten hebben, moet u meerdere Vm's of [beschikbaarheids sets](../articles/virtual-machines/windows/manage-availability.md) hebben | - |
| Algemeen | Een strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) aannemen | Met Azure Site Recovery kunt u kiezen uit verschillende opties die zijn ontworpen ter ondersteuning van uw bedrijfs continuïteits behoeften. Het ondersteunt verschillende replicatie en failover scenario's. Zie [over site Recovery](../articles/site-recovery/site-recovery-overview.md) voor meer informatie. | - |
| Identiteits- en toegangsbeheer | VM-verificatie centraliseren | U kunt de verificatie van uw virtuele Windows-en Linux-machines centraliseren met [Azure AD-verificatie](../articles/active-directory/develop/authentication-scenarios.md). | - |
| Gegevensbeveiliging | Schijven van het besturings systeem versleutelen | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) helpt u bij het versleutelen van de schijven van de virtuele Windows-en Linux IaaS-machines. Door schijven te versleutelen, wordt de inhoud onleesbaar zonder de benodigde sleutels. Schijf versleuteling beveiligt opgeslagen gegevens van onbevoegde toegang die anders mogelijk zouden zijn als de schijf is gekopieerd| [Ja](../articles/security-center/security-center-apply-disk-encryption.md) |
| Gegevensbeveiliging | Gegevens schijven versleutelen | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) helpt u bij het versleutelen van de schijven van de virtuele Windows-en Linux IaaS-machines. Door schijven te versleutelen, wordt de inhoud onleesbaar zonder de benodigde sleutels. Schijf versleuteling beveiligt opgeslagen gegevens van onbevoegde toegang die anders mogelijk zouden zijn als de schijf is gekopieerd| -  |
| Gegevensbeveiliging | Geïnstalleerde software beperken | Als u de geïnstalleerde software beperkt tot wat is vereist voor het implementeren van uw oplossing, vermindert u de kwets baarheid van uw oplossing | - |
| Gegevensbeveiliging | Anti virus/antimalware gebruiken | In azure kunt u antimalware-software gebruiken van beveiligings leveranciers zoals micro soft, Symantec, Trend Micro en Kaspersky. Deze software helpt bij het beschermen van uw virtuele machines tegen schadelijke bestanden, adware en andere bedreigingen. U kunt micro soft antimalware-beveiliging implementeren op basis van de werk belastingen van uw toepassing, met een basis beveiliging, standaard of geavanceerde aangepaste configuratie. Zie voor meer informatie over micro soft antimalware voor Azure [micro soft antimalware voor azure Cloud Services en virtual machines](../articles/security/azure-security-antimalware.md) | - |
| Gegevensbeveiliging | Sleutels en geheimen veilig opslaan | Vereenvoudig het beheer van uw geheimen en sleutels door uw eigen aren van uw toepassingen te voorzien van een veilig te beheren optie, waarmee u het risico op onbedoelde inbreuken of lekkages kunt verminderen. Azure Key Vault kunnen uw sleutels veilig opslaan in Hardware Security modules (Hsm's) die zijn gecertificeerd voor FIPS 140-2 level 2. Als u uw sleutels en geheimen wilt opslaan met behulp van FIPs 140,2 level 3, kunt u de [speciale HSM van Azure](../articles/dedicated-hsm/overview.md) gebruiken | - |
| Netwerken | Toegang tot beheer poorten beperken | Aanvallers scannen de IP-adresbereiken van de open bare Cloud voor open-beheer poorten en proberen eenvoudige aanvallen zoals algemene wacht woorden en bekende problemen met niet-patches. Just [-in-time-toegang (VM) voor virtuele machines](../articles/security-center/security-center-just-in-time.md) kan worden gebruikt om inkomend verkeer naar uw Azure-vm's te vergren delen, waardoor de bloot stelling aan aanvallen wordt verkleind en zo snel mogelijk toegang tot virtuele machines kan worden gemaakt. | - |
| Netwerken | Netwerk toegang beperken | Met netwerk beveiligings groepen kunt u netwerk toegang beperken en het aantal weer gegeven eind punten beheren. Zie [een netwerk beveiligings groep maken, wijzigen of verwijderen](../articles/virtual-network/manage-network-security-group.md) voor meer informatie. | - |
| Bewaking | Uw virtuele machines bewaken | U kunt [Azure monitor voor vm's](../articles/azure-monitor/insights/vminsights-overview.md) gebruiken om de status van uw virtuele machines en virtuele-machine schaal sets van Azure te controleren. Prestatie problemen met een virtuele machine kunnen leiden tot onderbrekingen in de service, waardoor het beveiligings principe van Beschik baarheid wordt geschonden. | - |

## <a name="next-steps"></a>Volgende stappen

Neem contact op met uw toepassings provider om na te gaan of er aanvullende beveiligings vereisten gelden. Zie [documentatie voor beveiligde ontwikkel aars](../articles/security/fundamentals/abstract-develop-secure-apps.md)voor meer informatie over het ontwikkelen van beveiligde toepassingen.