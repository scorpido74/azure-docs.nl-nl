---
title: Scenario's voor noodherstel
description: Lees wat u moet doen in het geval dat een Azure-serviceonderbreking gevolgen heeft voor virtuele Azure-machines.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 05/31/2017
ms.author: cynthn
ms.openlocfilehash: f2dc43e1f07d449bf2f8ed39ce4523c99b551dae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115623"
---
# <a name="what-if-an-azure-service-disruption-impacts-azure-vms"></a>Wat gebeurt er als een Azure-serviceonderbreking gevolgen heeft voor Azure VM's
Bij Microsoft werken we er hard aan om ervoor te zorgen dat onze services altijd voor u beschikbaar zijn wanneer u ze nodig hebt. Krachten buiten onze controle beïnvloeden ons soms op manieren die ongeplande serviceonderbrekingen veroorzaken.

Microsoft biedt een SLA (Service Level Agreement) voor zijn services als een verbintenis voor uptime en connectiviteit. De SLA voor afzonderlijke Azure-services is te vinden bij [Azure Service Level Agreements.](https://azure.microsoft.com/support/legal/sla/)

Azure heeft al veel ingebouwde platformfuncties die zeer beschikbare toepassingen ondersteunen. Lees Voor meer informatie over deze services [Disaster recovery en hoge beschikbaarheid voor Azure-toepassingen](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Dit artikel behandelt een echt scenario voor noodherstel, wanneer een hele regio een storing ondervindt als gevolg van een grote natuurramp of wijdverspreide onderbreking van de service. Dit zijn zeldzame gebeurtenissen, maar u moet zich voorbereiden op de mogelijkheid dat er een storing van een hele regio is. Als een hele regio een onderbreking van de service ondervindt, zijn de lokaal overbodige kopieën van uw gegevens tijdelijk niet beschikbaar. Als u georeplicatie hebt ingeschakeld, worden drie extra exemplaren van uw Azure Storage-blobs en -tabellen in een andere regio opgeslagen. In het geval van een volledige regionale storing of een ramp waarbij de primaire regio niet kan worden hersteld, brengt Azure alle DNS-vermeldingen opnieuw in het geo-gerepliceerde gebied.

Om u te helpen deze zeldzame gebeurtenissen te verwerken, bieden we de volgende richtlijnen voor virtuele Azure-machines in het geval van een serviceonderbreking van het hele gebied waar uw Azure-toepassing voor virtuele machines wordt geïmplementeerd.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Optie 1: Een failover starten met Azure Site Recovery
U Azure Site Recovery configureren voor uw VM's, zodat u uw toepassing binnen enkele minuten met één klik herstellen. U repliceren naar Azure-gebied van uw keuze en niet beperkt tot gekoppelde regio's. U aan de slag door [uw virtuele machines te repliceren.](https://aka.ms/a2a-getting-started) U [een herstelplan maken,](../site-recovery/site-recovery-create-recovery-plans.md) zodat u het volledige failoverproces voor uw toepassing automatiseren. U [uw failovers](../site-recovery/site-recovery-test-failover-to-azure.md) vooraf testen zonder dat dit gevolgen heeft voor de productietoepassing of de doorlopende replicatie. In het geval van een primaire regioverstoring, start u gewoon een failover en brengt u uw toepassing in doelregio.In the s of a primary region disruption, you just [initiate a failover](../site-recovery/site-recovery-failover.md) and bring your application in target region.


## <a name="option-2-wait-for-recovery"></a>Optie 2: Wachten op herstel
In dit geval is er geen actie van uw kant vereist. Weet dat we hard werken om de beschikbaarheid van de service te herstellen. U de huidige servicestatus bekijken op ons [Azure Service Health Dashboard.](https://azure.microsoft.com/status/)

Dit is de beste optie als u Azure Site Recovery, georedundante opslag voor lezen of georedundante opslag niet hebt ingesteld voordat de onderbreking wordt geopend. Als u georedundante opslag of georedundante opslag voor leestoegang hebt ingesteld voor het opslagaccount waar uw virtuele VM-harde schijven (VHD's) zijn opgeslagen, u kijken naar het herstellen van de basisafbeelding VHD en proberen er een nieuwe VM van in te richten. Dit heeft geen voorkeursoptie omdat er geen garanties zijn voor synchronisatie van gegevens. Deze optie is dan ook niet gegarandeerd om te werken.


> [!NOTE]
> Houd er rekening mee dat u geen controle hebt over dit proces en dat dit alleen geldt voor onderbrekingen in de hele regio. Hierdoor moet u ook vertrouwen op andere toepassingsspecifieke back-upstrategieën om het hoogste niveau van beschikbaarheid te bereiken. Zie voor meer informatie het gedeelte [gegevensstrategieën voor herstel na noodgevallen](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan).
>
>

## <a name="next-steps"></a>Volgende stappen

- Uw [toepassingen die op virtuele Azure-machines worden uitgevoerd, beveiligen](https://aka.ms/a2a-getting-started) met Azure Site Recovery

- Zie [Disaster recovery en hoge beschikbaarheid voor Azure-toepassingen voor](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)meer informatie over het implementeren van een strategie voor noodherstel en hoge beschikbaarheid.

- Zie [technische richtlijnen](/azure/data-lake-store/data-lake-store-disaster-recovery-guidance)voor azure tolerantie om een gedetailleerd technisch inzicht in de mogelijkheden van een cloudplatform te ontwikkelen.


- Als de instructies niet duidelijk zijn of als u wilt dat Microsoft de bewerkingen namens u uitvoert, neemt u contact op met [de klantenservice.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
