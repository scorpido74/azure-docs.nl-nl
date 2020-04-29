---
title: Overzicht van Azure Management-Azure governance
description: Overzicht van de beheer gebieden voor Azure-toepassingen en-resources met koppelingen naar inhoud op Azure-beheer hulpprogramma's.
ms.date: 12/06/2018
ms.topic: overview
ms.openlocfilehash: 644f4fc3a04f86426c2eb04b02c29882fa39fb88
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75980901"
---
# <a name="overview-of-management-services-in-azure"></a>Overzicht van beheer Services in azure

Governance in Azure is één aspect van Azure management. In dit artikel worden de verschillende beheer gebieden besproken voor het implementeren en onderhouden van uw resources in Azure.

Beheer refereert aan de taken en processen die nodig zijn om uw zakelijke toepassingen te onderhouden, evenals de resources die deze toepassingen ondersteunen. Azure heeft veel services en hulpprogram ma's die samen werken om volledig beheer te bieden. Deze services zijn niet alleen voor resources in azure, maar ook in andere Clouds en on-premises. Het is de eerste stap bij het ontwerpen van een volledige beheer omgeving om te zien wat de verschillende hulpprogram ma's zijn en hoe deze samen werken.

In het volgende diagram ziet u de verschillende beheergebieden die nodig zijn om een toepassing of resource te onderhouden. Deze verschillende gebieden kunnen worden beschouwd als een levens cyclus. Elk gebied is vereist, doorlopend achter de levens duur van een resource. Deze levens cyclus van resources begint met de eerste implementatie, via een voortgezette bewerking, en ten slotte bij het buiten gebruik gesteld.

![Vakgebieden van beheer in azure](../monitoring/media/management-overview/management-capabilities.png)

Geen enkele Azure-service vult de vereisten van een bepaald beheer gebied niet volledig in. In plaats daarvan wordt elk samen met verschillende services gerealiseerd. Sommige services, zoals Application Insights, bieden specifieke bewakings functionaliteit voor webtoepassingen. Andere, zoals Azure Monitor-logboeken, Store-beheer gegevens voor andere services. Met deze functie kunt u gegevens van verschillende typen die door verschillende services zijn verzameld, analyseren.

In de volgende gedeelten worden kort de diverse beheergebieden beschreven. Ook vindt u er koppelingen naar gedetailleerde inhoud over de belangrijkste Azure-services die voor deze gebieden bedoeld zijn.

## <a name="monitor"></a>Controleren

Bewaking is het verzamelen en analyseren van gegevens om de prestaties, de status en beschik baarheid van uw resources te controleren. Een efficiënte bewakings strategie helpt u bij het begrijpen van de werking van onderdelen en om uw uptime met meldingen te verg Roten. Lees een overzicht van de bewaking die betrekking heeft op de verschillende services die worden gebruikt voor het [bewaken van Azure-toepassingen en-resources](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Configureren

Configureren verwijst naar de eerste implementatie en configuratie van resources en doorlopend onderhoud.
Met automatisering van deze taken kunt u redundantie elimineren, uw tijd en moeite minimaliseren en uw nauw keurigheid en efficiëntie verhogen. [Azure Automation](../automation/automation-intro.md) biedt het merendeel van de services die bestemd zijn om configuratietaken te automatiseren. Hoewel runbooks proces automatisering, configuratie en update beheer assisteren bij het beheer van de configuratie.

## <a name="govern"></a>Governance

Governance biedt mechanismen en processen om de controle over uw toepassingen en resources in Azure te behouden. Dat omvat het plannen van initiatieven en het stellen van strategische prioriteiten.
Governance in Azure wordt hoofdzakelijk geïmplementeerd via twee services. Met [Azure Policy](./policy/overview.md) kunt u beleids definities maken, toewijzen en beheren om regels voor uw resources af te dwingen. Deze functie houdt deze resources in overeenstemming met uw bedrijfs normen. Met [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) kunt u het Cloud gebruik en-uitgaven bijhouden voor uw Azure-resources en andere cloud providers.

## <a name="secure"></a>Beveiligen

De beveiliging van uw resources en gegevens beheren. Een beveiligings programma omvat het beoordelen van bedreigingen, verzamelen en analyseren van gegevens, en naleving van uw toepassingen en bronnen. Beveiligings bewaking en bedreigings analyse worden geleverd door [Azure Security Center](../security-center/security-center-intro.md), waaronder geïntegreerd beveiligings beheer en geavanceerde bedreigings beveiliging in hybride Cloud werkbelastingen. Zie [Inleiding tot Azure-beveiliging](../security/fundamentals/overview.md) voor uitgebreide informatie en richt lijnen voor het beveiligen van Azure-resources.

## <a name="protect"></a>Beveiligen

Beveiliging is bedoeld om uw toepassingen en gegevens beschikbaar te houden, zelfs als er storingen optreden die buiten uw beheer vallen. Bescherming in Azure wordt door twee services geboden. [Azure Backup](../backup/backup-introduction-to-azure-backup.md) biedt back-ups en herstel van uw gegevens in de cloud of on-premises. [Azure site Recovery](../site-recovery/site-recovery-overview.md) biedt bedrijfs continuïteit en onmiddellijke herstel tijdens een nood geval.

## <a name="migrate"></a>Migreren

Migratie heeft betrekking op het overhevelen naar de Azure-cloud van workloads die momenteel on-premises worden uitgevoerd.
[Azure migrate](../migrate/migrate-overview.md) is een service waarmee u de migratie geschiktheid van on-premises virtuele machines naar Azure kunt beoordelen. Azure Site Recovery migreert virtuele machines [van on-premises](../site-recovery/migrate-tutorial-on-premises-azure.md) of [vanuit Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Azure data base Migration](../dms/dms-overview.md) helpt u bij het migreren van database bronnen naar Azure-gegevens platforms.
