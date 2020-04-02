---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: df577ab2b5e9658fd55324c8fd6fd008621b4d46
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545910"
---
Dit artikel bevat beveiligingsaanbevelingen voor Virtuele Azure-machines. Volg deze aanbevelingen om te helpen voldoen aan de beveiligingsverplichtingen die in ons model voor gedeelde verantwoordelijkheid worden beschreven. De aanbevelingen helpen u ook de algehele beveiliging van uw web-app-oplossingen te verbeteren. Zie [Gedeelde verantwoordelijkheden voor cloud computing voor](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)meer informatie over wat Microsoft doet om de verantwoordelijkheden van serviceproviders te vervullen.

Sommige aanbevelingen van dit artikel kunnen automatisch worden aangepakt door Azure Security Center. Azure Security Center is de eerste verdedigingslinie voor uw resources in Azure. Het analyseert periodiek de beveiligingsstatus van uw Azure-bronnen om potentiële beveiligingsproblemen te identificeren. Het beveelt vervolgens aan hoe de kwetsbaarheden aan te pakken. Zie [Beveiligingsaanbevelingen in Azure Security Center](../articles/security-center/security-center-recommendations.md)voor meer informatie.

Zie Wat is Azure Security Center voor algemene informatie over Azure Security [Center?](../articles/security-center/security-center-intro.md)

## <a name="general"></a>Algemeen

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
| Wanneer u aangepaste VM-afbeeldingen maakt, past u de nieuwste updates toe. | Voordat u afbeeldingen maakt, installeert u de nieuwste updates voor het besturingssysteem en voor alle toepassingen die deel uitmaken van uw afbeelding.  | - |
| Houd uw VM's actueel. | U de oplossing [Voor updatebeheer](../articles/automation/automation-update-management.md) in Azure Automation gebruiken om updates van het besturingssysteem voor uw Windows- en Linux-computers in Azure te beheren. | [Ja](../articles/security-center/security-center-apply-system-updates.md) |
| Een back-up van uw VM's. | [Azure Backup](../articles/backup/backup-overview.md) helpt uw toepassingsgegevens te beschermen en heeft minimale bedrijfskosten. Toepassingsfouten kunnen uw gegevens beschadigen en menselijke fouten kunnen bugs in uw toepassingen introduceren. Azure Backup beschermt uw VM's waarop Windows en Linux worden uitgevoerd. | - |
| Gebruik meerdere VM's voor een grotere veerkracht en beschikbaarheid. | Als uw VM toepassingen uitvoert die in hoge mate beschikbaar moeten zijn, gebruikt u meerdere VM's of [beschikbaarheidssets.](../articles/virtual-machines/windows/manage-availability.md) | - |
| Neem een business continuity and disaster recovery (BCDR) strategie. | Azure Site Recovery stelt u in staat om te kiezen uit verschillende opties die zijn ontworpen om de bedrijfscontinuïteit te ondersteunen. Het ondersteunt verschillende replicatie- en failoverscenario's. Zie [Over siteherstel](../articles/site-recovery/site-recovery-overview.md)voor meer informatie. | - |

## <a name="data-security"></a>Gegevensbeveiliging

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
| Besturingssysteemschijven versleutelen. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) helpt u uw Windows- en Linux IaaS VM-schijven te versleutelen. Zonder de benodigde sleutels is de inhoud van versleutelde schijven onleesbaar. Schijfversleuteling beschermt opgeslagen gegevens tegen ongeautoriseerde toegang die anders mogelijk zou zijn als de schijf zou worden gekopieerd.| [Ja](../articles/security-center/security-center-apply-disk-encryption.md) |
| Gegevensschijven versleutelen. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) helpt u uw Windows- en Linux IaaS VM-schijven te versleutelen. Zonder de benodigde sleutels is de inhoud van versleutelde schijven onleesbaar. Schijfversleuteling beschermt opgeslagen gegevens tegen ongeautoriseerde toegang die anders mogelijk zou zijn als de schijf zou worden gekopieerd.| -  |
| Beperk geïnstalleerde software. | Beperk geïnstalleerde software tot wat nodig is om uw oplossing met succes toe te passen. Deze richtlijn helpt het aanvalsoppervlak van uw oplossing te verminderen. | - |
| Gebruik antivirus of antimalware. | In Azure u antimalwaresoftware van beveiligingsleveranciers zoals Microsoft, Symantec, Trend Micro en Kaspersky gebruiken. Deze software helpt uw VM's te beschermen tegen schadelijke bestanden, adware en andere bedreigingen. U Microsoft Antimalware implementeren op basis van uw toepassingsworkloads. Gebruik basisbeveiligde of geavanceerde aangepaste configuratie. Zie [Microsoft Antimalware voor Azure Cloud Services en Virtuele Machines voor](../articles/security/azure-security-antimalware.md)meer informatie. | - |
| Bewaar sleutels en geheimen veilig. | Vereenvoudig het beheer van uw geheimen en sleutels door eigenaren van uw toepassing een veilige, centraal beheerde optie te bieden. Dit beheer vermindert het risico op een per ongeluk compromis of lek. Azure Key Vault kan uw sleutels veilig opslaan in hardwarebeveiligingsmodules (HSM's) die zijn gecertificeerd voor FIPS 140-2 Level 2. Als u FIPs 140.2 Level 3 moet gebruiken om uw sleutels en geheimen op te slaan, u [Azure Dedicated HSM gebruiken.](../articles/dedicated-hsm/overview.md) | - |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer 

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
| VM-verificatie centraliseren. | U de verificatie van uw Windows- en Linux-VM's centraliseren met [Azure Active Directory-verificatie.](../articles/active-directory/develop/authentication-scenarios.md) | - |

## <a name="monitoring"></a>Bewaking

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
| Houd uw VM's in de gaten. | U [Azure Monitor voor VM's](../articles/azure-monitor/insights/vminsights-overview.md) gebruiken om de status van uw Azure VM's en virtuele machineschaalsets te controleren. Prestatieproblemen met een VM kunnen leiden tot serviceonderbreking, wat in strijd is met het beveiligingsprincipe van beschikbaarheid. | - |

## <a name="networking"></a>Netwerken

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
| Beperk de toegang tot beheerpoorten. | Aanvallers scannen ip-bereiken in de openbare cloud voor open beheerpoorten en proberen "eenvoudige" aanvallen zoals veelvoorkomende wachtwoorden en bekende ongepatchte kwetsbaarheden. U [just-in-time (JIT) VM-toegang](../articles/security-center/security-center-just-in-time.md) gebruiken om binnenkomend verkeer naar uw Azure VM's te vergrendelen, waardoor de blootstelling aan aanvallen wordt verminderd en u eenvoudige verbindingen met VM's biedt wanneer ze nodig zijn. | - |
| Beperk de toegang tot het netwerk. | Met netwerkbeveiligingsgroepen u de toegang tot het netwerk beperken en het aantal blootgestelde eindpunten beheren. Zie [Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen](../articles/virtual-network/manage-network-security-group.md)voor meer informatie. | - |

## <a name="next-steps"></a>Volgende stappen

Neem contact op met uw toepassingsprovider voor meer informatie over aanvullende beveiligingsvereisten. Zie [Beveiligde-ontwikkelingsdocumentatie](../articles/security/fundamentals/abstract-develop-secure-apps.md)voor meer informatie over het ontwikkelen van beveiligde toepassingen.
