---
title: Overzicht van Azure Management-Azure governance
description: Overzicht van de gebieden van beheer voor Azure-toepassingen en bronnen met koppelingen naar inhoud op Azure-beheerhulpprogramma's.
ms.date: 12/06/2018
ms.topic: article
ms.openlocfilehash: e85d5162146fb716ed9f846dc5a828d83c669627
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013910"
---
# <a name="overview-of-management-services-in-azure"></a>Overzicht van beheer Services in azure

Governance in Azure is slechts één aspect van Azure Management. In dit artikel bevat informatie over de verschillende beheergebieden voor het implementeren en beheren van uw resources in Azure.

Beheer refereert aan de taken en processen die nodig zijn om uw zakelijke toepassingen te onderhouden, evenals de resources die deze toepassingen ondersteunen. Azure heeft veel services en hulpprogramma's die samenwerken voor volledig beheer. Deze services zijn niet alleen voor resources in Azure, maar ook in andere clouds en on-premises. Informatie over de verschillende hulpprogramma's en hoe ze samenwerken, is de eerste stap bij het ontwerpen van een volledige beheeromgeving.

In het volgende diagram ziet u de verschillende beheergebieden die nodig zijn om een toepassing of resource te onderhouden. Deze reeks gebieden kunnen worden beschouwd als een levenscyclus. Elk gebied is vereist in continu achter elkaar gedurende de levensduur van een resource. De levenscyclus van deze resource begint met de initiële implementatie via vervolgt en ten slotte bij buiten gebruik gesteld.

![Vakgebieden van beheer in azure](../monitoring/media/management-overview/management-capabilities.png)

Er is geen enkele Azure-service voldoet volledig aan de vereisten van een bepaald gebied. In plaats daarvan wordt elk gerealiseerd door verschillende services samen te werken. Sommige services, zoals Application Insights, bieden gerichte bewakingsfunctionaliteit voor webtoepassingen. Andere, zoals Azure Monitor-logboeken, Store-beheer gegevens voor andere services. Deze functie kunt u gegevens van verschillende typen die zijn verzameld door verschillende services kunt analyseren.

In de volgende gedeelten worden kort de diverse beheergebieden beschreven. Ook vindt u er koppelingen naar gedetailleerde inhoud over de belangrijkste Azure-services die voor deze gebieden bedoeld zijn.

## <a name="monitor"></a>Bewaken

Bewaken is het verzamelen en analyseren van gegevens om te controleren van de prestaties, status en beschikbaarheid van uw resources. Een strategie voor effectieve bewaking krijgt u inzicht in de bewerking van onderdelen en om te verhogen van de bedrijfstijd met meldingen. Lees een overzicht van bewaking die bevat informatie over de verschillende services gebruikt bij [bewaking van Azure-toepassingen en bronnen](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Configureren

Configureer verwijst naar de initiële implementatie en configuratie van resources en constant onderhoud.
Automatisering van deze taken kunt u redundantie, minimaliseert de tijd en moeite en de nauwkeurigheid en efficiëntie. [Azure Automation](../automation/automation-intro.md) biedt het merendeel van de services die bestemd zijn om configuratietaken te automatiseren. Terwijl runbooks procesautomatisering verwerken, helpen update- en Configuratiebeheer bij het beheren van configuratie.

## <a name="govern"></a>Governance

Governance biedt mechanismen en processen om de controle over uw toepassingen en resources in Azure te behouden. Dat omvat het plannen van initiatieven en het stellen van strategische prioriteiten.
Governance in Azure wordt hoofdzakelijk geïmplementeerd via twee services. [Azure Policy](./policy/overview.md) kunt u maken, toewijzen en beheren van beleidsdefinities om af te dwingen van regels voor uw resources. Deze functie blijft deze resources voldoen aan uw bedrijfsnormen. Met [Azure Cost Management](../cost-management/overview-cost-mgt.md) kunt u het Cloud gebruik en-uitgaven bijhouden voor uw Azure-resources en andere cloud providers.

## <a name="secure"></a>Beveiligen

De beveiliging van uw resources en -gegevens beheren. Een beveiligingsprogramma omvat het beoordelen van bedreigingen, verzamelen en analyseren van gegevens en naleving van uw toepassingen en resources. Beveiligingsanalyse beveiligingsbewaking en bedreigingsanalyse worden geleverd door [Azure Security Center](../security-center/security-center-intro.md), dat ook geïntegreerd beveiligingsbeheer en geavanceerde bedreigingsbeveiliging voor hybride cloudworkloads. Zie [Inleiding tot Azure-beveiliging](../security/fundamentals/overview.md) voor uitgebreide informatie over en richtlijnen voor het beveiligen van Azure-resources.

## <a name="protect"></a>Beschermen

Beveiliging verwijst naar het houden van uw toepassingen en gegevens beschikbaar, zelfs bij storingen die buiten het besturingselement. Bescherming in Azure wordt door twee services geboden. [Azure Backup](../backup/backup-introduction-to-azure-backup.md) biedt back-ups en herstel van uw gegevens in de cloud of on-premises. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) zorgt voor bedrijfscontinuïteit en onmiddellijk herstel tijdens een noodgeval.

## <a name="migrate"></a>Migreren

Migratie heeft betrekking op het overhevelen naar de Azure-cloud van workloads die momenteel on-premises worden uitgevoerd.
[Azure Migrate](../migrate/migrate-overview.md) is een service waarmee u de geschiktheid voor migratie van on-premises virtuele machines naar Azure beoordelen. Azure Site Recovery worden virtuele machines gemigreerd [van on-premises](../site-recovery/migrate-tutorial-on-premises-azure.md) of [vanuit Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Azure Database Migration](../dms/dms-overview.md) helpt u bij het migreren databasebronnen naar Azure-gegevensplatforms.
