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
ms.openlocfilehash: d43860d71b14bdac275df51695c9206539529171
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72511463"
---
Dit artikel bevat beveiligings aanbevelingen voor Azure Virtual Machines. Volg deze aanbevelingen om te voldoen aan de beveiligings verplichtingen die in ons model voor gedeelde verantwoordelijkheid zijn beschreven. De aanbevelingen helpen u ook om de algehele beveiliging van uw web-app-oplossingen te verbeteren. Zie [gedeelde verantwoordelijkheden voor Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225237/1/Shared%20Responsibilities%20for%20Cloud%20Computing%20(2017-04-03).pdf)voor meer informatie over wat micro soft doet aan de verantwoordelijkheden van de service provider.

Sommige aanbevelingen van dit artikel kunnen automatisch worden aangepakt door Azure Security Center. Azure Security Center is de eerste verdedigings linie voor uw resources in Azure. De beveiligings status van uw Azure-resources wordt periodiek geanalyseerd om mogelijke beveiligings problemen te identificeren. Vervolgens wordt u geadviseerd om de beveiligings problemen op te lossen. Zie [beveiligings aanbevelingen in azure Security Center](../articles/security-center/security-center-recommendations.md)voor meer informatie.

Zie [Wat is Azure Security Center?](../articles/security-center/security-center-intro.md)voor algemene informatie over Azure Security Center.

## <a name="recommendations"></a>Aanbevelingen

| Category | Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|-|----|--|
| Algemeen | Wanneer u aangepaste VM-installatie kopieën bouwt, moet u de meest recente updates Toep assen. | Voordat u installatie kopieën maakt, installeert u de meest recente updates voor het besturings systeem en voor alle toepassingen die deel zullen uitmaken van uw installatie kopie.  | - |
| Algemeen | Zorg ervoor dat uw virtuele machines actueel blijven. | U kunt de [updatebeheer](../articles/automation/automation-update-management.md) -oplossing in azure Automation gebruiken om updates van het besturings systeem voor uw Windows-en Linux-computers in azure te beheren. | [Ja](../articles/security-center/security-center-apply-system-updates.md) |
| Algemeen | Maak een back-up van uw Vm's. | [Azure backup](../articles/backup/backup-overview.md) helpt bij het beveiligen van uw toepassings gegevens en minimale operationele kosten. Toepassingsfouten kunnen uw gegevens beschadigen, en menselijke fouten kunnen fouten introduceren in uw toepassingen. Azure Backup beveiligt uw virtuele machines waarop Windows en Linux worden uitgevoerd. | - |
| Algemeen | Gebruik meerdere Vm's voor grotere tolerantie en meer Beschik baarheid. | Als uw VM toepassingen uitvoert die Maxi maal beschikbaar moeten zijn, gebruikt u meerdere Vm's of [beschikbaarheids sets](../articles/virtual-machines/windows/manage-availability.md). | - |
| Algemeen | Stel een strategie voor bedrijfs continuïteit en nood herstel (BCDR) vast. | Met Azure Site Recovery kunt u kiezen uit verschillende opties die zijn ontworpen ter ondersteuning van bedrijfs continuïteit. Het ondersteunt verschillende scenario's voor replicatie en failover. Zie [over site Recovery](../articles/site-recovery/site-recovery-overview.md)voor meer informatie. | - |
| Identiteits- en toegangsbeheer | VM-verificatie centraliseren. | U kunt de verificatie van uw Windows-en Linux-Vm's centraliseren door gebruik te maken van [Azure Active Directory-verificatie](../articles/active-directory/develop/authentication-scenarios.md). | - |
| Databeveiliging | De schijven van het besturings systeem versleutelen. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) helpt u bij het versleutelen van uw Windows-en Linux IaaS-VM-schijven. Zonder de benodigde sleutels zijn de inhoud van versleutelde schijven onleesbaar. Schijf versleuteling beveiligt opgeslagen gegevens van onbevoegde toegang die anders mogelijk zouden zijn als de schijf is gekopieerd.| [Ja](../articles/security-center/security-center-apply-disk-encryption.md) |
| Databeveiliging | Gegevens schijven versleutelen. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) helpt u bij het versleutelen van uw Windows-en Linux IaaS-VM-schijven. Zonder de benodigde sleutels zijn de inhoud van versleutelde schijven onleesbaar. Schijf versleuteling beveiligt opgeslagen gegevens van onbevoegde toegang die anders mogelijk zouden zijn als de schijf is gekopieerd.| -  |
| Databeveiliging | Beperk geïnstalleerde software. | Beperk geïnstalleerde software tot wat u nodig hebt om uw oplossing toe te passen. Deze richt lijn helpt de kwets baarheid van uw oplossing te verminderen. | - |
| Databeveiliging | Gebruik anti virus of antimalware. | In azure kunt u antimalware-software gebruiken van beveiligings leveranciers zoals micro soft, Symantec, Trend Micro en Kaspersky. Deze software helpt uw Vm's te beschermen tegen schadelijke bestanden, adware en andere bedreigingen. U kunt micro soft antimalware implementeren op basis van de workloads van uw toepassing. Gebruik de standaard instelling veilig-op-basis of geavanceerde aangepaste configuratie. Zie voor meer informatie [micro soft antimalware voor Azure Cloud Services en virtual machines](../articles/security/azure-security-antimalware.md). | - |
| Databeveiliging | Bewaar sleutels en geheimen veilig. | Vereenvoudig het beheer van uw geheimen en sleutels door uw eigen aren van uw toepassingen te voorzien van een veilige, centraal beheerde optie. Dit beheer vermindert het risico van een onbedoelde inbreuk of lekkage. Azure Key Vault kunnen uw sleutels veilig opslaan in Hardware Security modules (Hsm's) die zijn gecertificeerd voor FIPS 140-2 level 2. Als u FIPs 140,2 level 3 moet gebruiken om uw sleutels en geheimen op te slaan, kunt u de [speciale HSM van Azure](../articles/dedicated-hsm/overview.md)gebruiken. | - |
| Networking | Beperk de toegang tot beheer poorten. | Aanvallers scannen de IP-adresbereiken van de open bare Cloud voor open-beheer poorten en proberen eenvoudige aanvallen zoals algemene wacht woorden en bekende problemen met niet-patches. U kunt just [-in-time-VM-toegang](../articles/security-center/security-center-just-in-time.md) gebruiken om inkomend verkeer naar uw Azure-vm's te vergren delen, waardoor de bloot stelling aan aanvallen wordt verkleind. | - |
| Networking | Netwerk toegang beperken. | Met netwerk beveiligings groepen kunt u netwerk toegang beperken en het aantal weer gegeven eind punten beheren. Zie [een netwerk beveiligings groep maken, wijzigen of verwijderen](../articles/virtual-network/manage-network-security-group.md)voor meer informatie. | - |
| Controleren | Bewaak uw Vm's. | U kunt [Azure monitor voor VM's](../articles/azure-monitor/insights/vminsights-overview.md) gebruiken om de status van uw Azure-vm's en virtuele-machine schaal sets te bewaken. Prestatie problemen met een virtuele machine kunnen leiden tot onderbrekingen in de service, waardoor het beveiligings principe van Beschik baarheid wordt geschonden. | - |

## <a name="next-steps"></a>Volgende stappen

Neem contact op met uw toepassings provider voor meer informatie over aanvullende beveiligings vereisten. Zie voor meer informatie over het ontwikkelen van beveiligde toepassingen [documentatie voor beveiligde ontwikkel aars](../articles/security/fundamentals/abstract-develop-secure-apps.md).
