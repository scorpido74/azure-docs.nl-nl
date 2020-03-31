---
title: Overzicht azure-beheer - Azure Governance
description: Overzicht van de gebieden van beheer voor Azure-toepassingen en -resources met koppelingen naar inhoud op Azure-beheertools.
ms.date: 12/06/2018
ms.topic: overview
ms.openlocfilehash: 644f4fc3a04f86426c2eb04b02c29882fa39fb88
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75980901"
---
# <a name="overview-of-management-services-in-azure"></a>Overzicht van beheerservices in Azure

Governance in Azure is een aspect van Azure Management. In dit artikel worden de verschillende beheergebieden voor het implementeren en onderhouden van uw resources in Azure beschrijft.

Beheer refereert aan de taken en processen die nodig zijn om uw zakelijke toepassingen te onderhouden, evenals de resources die deze toepassingen ondersteunen. Azure heeft veel services en hulpprogramma's die samenwerken om volledig beheer te bieden. Deze services zijn niet alleen bedoeld voor resources in Azure, maar ook voor andere clouds en on-premises. Inzicht in de verschillende tools en hoe ze samenwerken is de eerste stap in het ontwerpen van een complete managementomgeving.

In het volgende diagram ziet u de verschillende beheergebieden die nodig zijn om een toepassing of resource te onderhouden. Deze verschillende gebieden kunnen worden gezien als een levenscyclus. Elk gebied is voortdurend nodig gedurende de levensduur van een resource. Deze levenscyclus van resources begint met de eerste implementatie, door voortzetting van de bewerking en ten slotte wanneer deze wordt uitgeschakeld.

![Managementdisciplines in Azure](../monitoring/media/management-overview/management-capabilities.png)

Geen enkele Azure-service voldoet volledig aan de vereisten van een bepaald beheergebied. In plaats daarvan wordt elk gerealiseerd door verschillende diensten samen te werken. Sommige services, zoals Application Insights, bieden gerichte bewakingsfunctionaliteit voor webapplicaties. Andere, zoals Azure Monitor-logboeken, storemanagementgegevens voor andere services. Met deze functie u gegevens analyseren van verschillende typen die door verschillende services zijn verzameld.

In de volgende gedeelten worden kort de diverse beheergebieden beschreven. Ook vindt u er koppelingen naar gedetailleerde inhoud over de belangrijkste Azure-services die voor deze gebieden bedoeld zijn.

## <a name="monitor"></a>Controleren

Monitoring is de handeling van het verzamelen en analyseren van gegevens om de prestaties, status en beschikbaarheid van uw resources te controleren. Een effectieve monitoringstrategie helpt u inzicht te krijgen in de werking van componenten en uw uptime met meldingen te verhogen. Lees een overzicht van Monitoring dat betrekking heeft op de verschillende services die worden gebruikt bij [Azure-toepassingen en -resources controleren.](../monitoring/monitoring-overview.md)

## <a name="configure"></a>Configureren

Configure verwijst naar de initiële implementatie en configuratie van resources en doorlopend onderhoud.
Automatisering van deze taken stelt u in staat om redundantie te elimineren, uw tijd en moeite te minimaliseren en uw nauwkeurigheid en efficiëntie te verhogen. [Azure Automation](../automation/automation-intro.md) biedt het merendeel van de services die bestemd zijn om configuratietaken te automatiseren. Terwijl runbooks procesautomatisering verwerken, helpen configuratie- en updatebeheer bij het beheren van de configuratie.

## <a name="govern"></a>Governance

Governance biedt mechanismen en processen om de controle over uw toepassingen en resources in Azure te behouden. Dat omvat het plannen van initiatieven en het stellen van strategische prioriteiten.
Governance in Azure wordt hoofdzakelijk geïmplementeerd via twee services. [Met Azure Policy](./policy/overview.md) u beleidsdefinities maken, toewijzen en beheren om regels voor uw resources af te dwingen. Deze functie houdt deze resources in overeenstemming met uw bedrijfsnormen. [Met Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) u het cloudgebruik en de uitgaven voor uw Azure-resources en andere cloudproviders bijhouden.

## <a name="secure"></a>Beveiligen

Beheer de beveiliging van uw bronnen en gegevens. Een beveiligingsprogramma omvat het beoordelen van bedreigingen, het verzamelen en analyseren van gegevens en de naleving van uw toepassingen en resources. Beveiligingsbewaking en bedreigingsanalyse worden geleverd door [Azure Security Center,](../security-center/security-center-intro.md)dat uniform beveiligingsbeheer en geavanceerde bedreigingsbeveiliging voor hybride cloudworkloads omvat. Zie [Inleiding tot Azure Security](../security/fundamentals/overview.md) voor uitgebreide informatie en richtlijnen voor het beveiligen van Azure-bronnen.

## <a name="protect"></a>Beschermen

Bescherming verwijst naar het beschikbaar houden van uw toepassingen en gegevens, zelfs met storingen die buiten uw controle liggen. Bescherming in Azure wordt door twee services geboden. [Azure Backup](../backup/backup-introduction-to-azure-backup.md) biedt back-ups en herstel van uw gegevens in de cloud of on-premises. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) biedt bedrijfscontinuïteit en onmiddellijk herstel tijdens een ramp.

## <a name="migrate"></a>Migreren

Migratie heeft betrekking op het overhevelen naar de Azure-cloud van workloads die momenteel on-premises worden uitgevoerd.
[Azure Migrate](../migrate/migrate-overview.md) is een service waarmee u de geschiktheid van de migratie van on-premises virtuele machines naar Azure beoordelen. Azure Site Recovery migreert virtuele machines [van on-premises](../site-recovery/migrate-tutorial-on-premises-azure.md) of [van Amazon Web Services.](../site-recovery/migrate-tutorial-aws-azure.md) [Azure Database Migration](../dms/dms-overview.md) helpt u bij het migreren van databasebronnen naar Azure Data-platforms.
