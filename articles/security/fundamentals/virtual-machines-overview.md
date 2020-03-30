---
title: Beveiligingsfuncties die worden gebruikt met Azure VM's
titleSuffix: Azure security
description: In dit artikel vindt u een overzicht van de belangrijkste Azure-beveiligingsfuncties die kunnen worden gebruikt met Azure Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: a1726e18ea8c1ba86d77d7b9ca3d50c444620361
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657160"
---
# <a name="azure-virtual-machines-security-overview"></a>Overzicht van azure Virtual Machines-beveiliging
In dit artikel vindt u een overzicht van de belangrijkste Azure-beveiligingsfuncties die kunnen worden gebruikt met virtuele machines.

U Azure Virtual Machines gebruiken om een breed scala aan computeroplossingen op een flexibele manier te implementeren. De service ondersteunt Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP en Azure BizTalk Services. U dus elke werkbelasting en elke taal op bijna elk besturingssysteem implementeren.

Een virtuele machine in Azure biedt u de flexibiliteit van virtualisatie zonder dat u de fysieke hardware hoeft te kopen en te beheren waarop de virtuele machine wordt uitgevoerd. U uw applicaties bouwen en implementeren met de zekerheid dat uw gegevens beschermd en veilig zijn in zeer beveiligde datacenters.

Met Azure u beveiligingsverbeterde, compatibele oplossingen bouwen die:

* Bescherm uw virtuele machines tegen virussen en malware.
* Versleutel uw gevoelige gegevens.
* Veilig netwerkverkeer.
* Bedreigingen identificeren en detecteren.
* Voldoe aan de nalevingsvereisten.  

## <a name="antimalware"></a>Antimalware

Met Azure u antimalwaresoftware van beveiligingsleveranciers zoals Microsoft, Symantec, Trend Micro en Kaspersky gebruiken. Deze software helpt uw virtuele machines te beschermen tegen schadelijke bestanden, adware en andere bedreigingen.

Microsoft Antimalware voor Azure Cloud Services en Virtuele Machines is een realtime beveiligingsmogelijkheid die helpt bij het identificeren en verwijderen van virussen, spyware en andere schadelijke software.  Microsoft Antimalware voor Azure biedt configureerbare waarschuwingen wanneer bekende schadelijke of ongewenste software zichzelf probeert te installeren of op uw Azure-systemen uitvoert.

Microsoft Antimalware voor Azure is een oplossing voor toepassingen en tenantomgevingen. Het is ontworpen om op de achtergrond te draaien zonder menselijke tussenkomst. U beveiliging implementeren op basis van de behoeften van uw toepassingsworkloads, met een basisveilige of geavanceerde aangepaste configuratie, inclusief antimalwarebewaking.

Meer informatie over [Microsoft Antimalware voor Azure](antimalware.md) en de belangrijkste functies die beschikbaar zijn.

Meer informatie over antimalwaresoftware om uw virtuele machines te beschermen:

* [Deploying Antimalware Solutions on Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/) (Antimalware-oplossingen implementeren op virtuele machines van Azure)
* [Trend Micro Deep Security installeren en configureren als service op een Windows-vm](/azure/virtual-machines/windows/classic/install-trend)
* [Symantec Endpoint Protection installeren en configureren op een Windows VM](/azure/virtual-machines/windows/classic/install-symantec)
* [Beveiligingsoplossingen in de Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Voor nog krachtigere bescherming u overwegen [windows Defender Advanced Threat Protection te](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)gebruiken. Met Windows Defender ATP krijgt u:

* [Vermindering van het aanvalsoppervlak](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Bescherming van de volgende generatie](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Bescherming en respons van eindpunten](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Geautomatiseerd onderzoek en sanering](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Beveiligingsscore](/windows/security/threat-protection/microsoft-defender-atp/configuration-score)
* [Geavanceerde jacht](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Beheer en API's](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft-bedreigingsbeveiliging](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Meer informatie:

* [Aan de slag met WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Overzicht van WDATP-mogelijkheden](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Hardwarebeveiligingsmodule

Het verbeteren van de beveiliging van sleutels kan de beveiliging van versleuteling en verificatie verbeteren. U het beheer en de beveiliging van uw kritieke geheimen en sleutels vereenvoudigen door ze op te slaan in Azure Key Vault.

Key Vault biedt de mogelijkheid om uw sleutels op te slaan in hardware beveiligingsmodules (HSM's) gecertificeerd volgens FIPS 140-2 Level 2-standaarden. Uw SQL Server-versleutelingssleutels voor back-up of [transparante gegevensversleuteling](https://msdn.microsoft.com/library/bb934049.aspx) kunnen allemaal worden opgeslagen in Key Vault met sleutels of geheimen van uw toepassingen. Machtigingen en toegang tot deze beveiligde items worden beheerd via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Meer informatie:

* [Wat is Azure Key Vault?](/azure/key-vault/key-vault-overview)
* [Azure Key Vault-blog](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Versleuteling van virtuele schijven

Azure Disk Encryption is een nieuwe mogelijkheid voor het versleutelen van uw Windows en Linux virtuele machine schijven. Azure Disk Encryption maakt gebruik van de industriestandaard [BitLocker-functie](https://technet.microsoft.com/library/cc732774.aspx) van Windows en de [dm-crypt-functie](https://en.wikipedia.org/wiki/Dm-crypt) van Linux om volumeversleuteling te bieden voor het besturingssysteem en de gegevensschijven.

De oplossing is geïntegreerd met Azure Key Vault om u te helpen de schijfversleutelingssleutels en -geheimen in uw key vault-abonnement te beheren en te beheren. Het zorgt ervoor dat alle gegevens in de schijven van de virtuele machine in rust worden versleuteld in Azure Storage.

Meer informatie:

* [Azure-schijfversleuteling voor IaaS-VM's](/azure/security/azure-security-disk-encryption-overview)
* [Snelstart: Een Windows Iaas-VM versleutelen met Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Back-up van virtuele machines

Azure Backup is een schaalbare oplossing die uw toepassingsgegevens beschermt met nul kapitaalinvesteringen en minimale bedrijfskosten. Toepassingsfouten kunnen uw gegevens beschadigen en menselijke fouten kunnen bugs in uw toepassingen introduceren. Met Azure Backup zijn uw virtuele machines met Windows en Linux beveiligd.

Meer informatie:

* [Wat is Azure Backup?](/azure/backup/backup-introduction-to-azure-backup)
* [Veelgestelde vragen over Azure Backup-service](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Een belangrijk onderdeel van de BCDR-strategie van uw organisatie is uitzoeken hoe bedrijfsworkloads en apps kunnen worden uitgevoerd wanneer geplande en ongeplande uitval optreedt. Azure Site Recovery helpt bij het orkestreren van replicatie, failover en herstel van workloads en apps, zodat ze beschikbaar zijn vanaf een secundaire locatie als uw primaire locatie uitvalt.

Siteherstel:

* **Vereenvoudigt uw BCDR-strategie:** Site recovery maakt het eenvoudig om replicatie, failover en herstel van meerdere zakelijke workloads en apps vanaf één locatie te verwerken. Site Recovery orkestreert replicatie en failover, maar onderschept uw toepassingsgegevens niet of heeft er geen informatie over.
* **Biedt flexibele replicatie:** door Site Recovery te gebruiken, u workloads repliceren die worden uitgevoerd op virtuele hyperv-machines, virtuele VMware-machines en fysieke Windows/Linux-servers.
* **Ondersteunt failover en herstel:** Site Recovery biedt testfailovers ter ondersteuning van noodhersteloefeningen zonder dat dit gevolgen heeft voor de productieomgevingen. Bovendien kunt u bij verwachte uitval geplande failovers uitvoeren zonder gegevensverlies, en bij onverwachte noodsituaties ongeplande failovers met minimaal gegevensverlies (afhankelijk van de replicatiefrequentie). Na failover u niet meer naar uw primaire sites. De herstelplannen van Site Recovery kunnen scripts en Azure Automation-werkmappen bevatten, zodat u failovers en het herstel van toepassingen met meerdere lagen naar behoefte kunt aanpassen.
* **Elimineert secundaire datacenters:** u repliceren naar een secundaire on-premises site of naar Azure. Het gebruik van Azure als bestemming voor noodherstel elimineert de kosten en complexiteit van het onderhoud van een secundaire site. Gerepliceerde gegevens worden opgeslagen in Azure Storage.
* **Integreert met bestaande BCDR-technologieën**: Site Recovery werkt samen met de BCDR-functies van andere toepassingen. U siteherstel bijvoorbeeld gebruiken om de SQL Server back-end van bedrijfsworkloads te beschermen. Dit omvat native ondersteuning voor SQL Server Always On om de failover van beschikbaarheidsgroepen te beheren.

Meer informatie:

* [Wat is Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [Hoe werkt Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Welke workloads worden beschermd door Azure Site Recovery?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Virtueel netwerk

Virtuele machines hebben netwerkconnectiviteit nodig. Om die vereiste te ondersteunen, vereist Azure dat virtuele machines zijn aangesloten op een virtueel Azure-netwerk.

Een Virtueel Azure-netwerk is een logische constructie die is gebouwd bovenop de fysieke Azure-netwerkstructuur. Elk logisch Virtueel Azure-netwerk is geïsoleerd van alle andere virtuele Azure-netwerken. Deze isolatie helpt ervoor te zorgen dat netwerkverkeer in uw implementaties niet toegankelijk is voor andere Microsoft Azure-klanten.

Meer informatie:

* [Overzicht van Azure-netwerkbeveiliging](network-overview.md)
* [Overzicht van virtueel netwerk](/azure/virtual-network/virtual-networks-overview)
* [Netwerkfuncties en partnerschappen voor bedrijfsscenario's](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Beheer en rapportage van beveiligingsbeleid

Azure Security Center helpt u bedreigingen te voorkomen, te detecteren en erop te reageren. Security Center geeft u meer inzicht in en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen. Het helpt bij het detecteren van bedreigingen die anders onopgemerkt zouden kunnen blijven, en werkt met een breed ecosysteem van beveiligingsoplossingen.

Security Center helpt u bij het optimaliseren en bewaken van de beveiliging van uw virtuele machines door:

* Het verstrekken van [beveiligingsaanbevelingen](/azure/security-center/security-center-recommendations) voor de virtuele machines. Voorbeelden van aanbevelingen zijn: systeemupdates toepassen, ACL-eindpunten configureren, antimalware inschakelen, netwerkbeveiligingsgroepen inschakelen en schijfversleuteling toepassen.
* De toestand van uw virtuele machines bewaken.

Meer informatie:

* [Inleiding tot Azure Security Center](/azure/security-center/security-center-intro)
* [Veelgestelde vragen van Azure Security Center](/azure/security-center/security-center-faq)
* [Planning en bewerkingen van Azure Security Center](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Naleving

Azure Virtual Machines is gecertificeerd voor FISMA, FedRAMP, HIPAA, PCI DSS Level 1 en andere belangrijke complianceprogramma's. Deze certificering maakt het voor uw eigen Azure-toepassingen gemakkelijker om aan de nalevingsvereisten te voldoen en voor uw bedrijf om te voldoen aan een breed scala aan nationale en internationale regelgevingsvereisten.

Meer informatie:

* [Microsoft Trust Center: Compliance](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Vertrouwde cloud: Microsoft Azure-beveiliging, -privacy en -naleving](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Vertrouwelijk computergebruik

Hoewel vertrouwelijk computergebruik technisch gezien geen onderdeel is van beveiliging van virtuele machines, behoort het onderwerp beveiliging van virtuele machines tot het hogere niveau van "compute"-beveiliging. Vertrouwelijk computergebruik behoort tot de categorie "compute"-beveiliging.

Vertrouwelijk computergebruik zorgt ervoor dat wanneer gegevens "in de duidelijke" zijn, wat https://en.wikipedia.org/wiki/Trusted_execution_environment nodig is voor een efficiënte verwerking, de gegevens worden beschermd binnen een Trusted Execution Environment (TEE - ook wel een enclave genoemd), waarvan een voorbeeld wordt weergegeven in de onderstaande figuur.  

TEE's zorgen ervoor dat er geen manier is om gegevens of bewerkingen van buitenaf te bekijken, zelfs niet met een debugger. Ze zorgen er zelfs voor dat alleen geautoriseerde code toegang heeft tot gegevens. Als de code wordt gewijzigd of gemanipuleerd, worden de bewerkingen geweigerd en wordt de omgeving uitgeschakeld. De TEE dwingt deze beveiligingen gedurende de uitvoering van code binnen het.

Meer informatie:

* [Introductie van Azure confidential computing](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Vertrouwelijke computergebruik in Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [best practices](iaas.md) voor beveiliging voor VM's en besturingssystemen.
