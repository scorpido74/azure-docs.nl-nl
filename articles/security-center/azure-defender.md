---
title: Overzicht van Azure Defender en de beschik bare abonnementen
description: Meer informatie over de plannen, beveiligingen en waarschuwingen van Azure Defender. Ga vervolgens door met het inschakelen van Azure Defender voor uw abonnementen.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bb1c1e6443b5087b48aad7c3171bef557707adb1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977300"
---
# <a name="introduction-to-azure-defender"></a>Inleiding tot Azure Defender

De functies van Azure Security Center omvatten de twee grote pijlers van Cloud beveiliging:

- **Cloud Security postuur Management (CSPM)**
- **Beveiliging van Cloud werkbelasting (CWP)**

De CSPM-functies van Security Center, zoals een beveiligde Score, de detectie van onjuiste beveiligings configuraties op uw Windows-en Linux Azure-machines, zijn allemaal deel van de gratis Security Center-ervaring die beschikbaar is voor alle Azure-gebruikers. Gebruik deze CSPM-functies om uw postuur te versterken en te zorgen voor naleving van de regelgeving.

**Azure Defender** is het Cloud werkbelasting platform (CWPP) geïntegreerd in Security Center voor geavanceerde, intelligente beveiliging van uw Azure-en hybride werk belastingen.

Dit is het Azure Defender-dash board in Azure Security Center:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Een voor beeld van het dash board van Azure Defender" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Welke resource typen kan Azure Defender beveiligen?

Azure Defender biedt beveiligings waarschuwingen en geavanceerde bedreigings beveiliging voor virtuele machines, SQL-data bases, containers, webtoepassingen, uw netwerk en meer.

Wanneer u Azure Defender inschakelt vanuit het gebied met **prijzen en instellingen** van Azure Security Center, zijn de volgende Defender-abonnementen allemaal tegelijkertijd ingeschakeld en bieden zij uitgebreide beveiligingen voor de reken-, gegevens-en service lagen van uw omgeving:

- [Azure Defender voor servers](defender-for-servers-introduction.md)
- [Azure Defender voor App Service](defender-for-app-service-introduction.md)
- [Azure Defender voor Storage](defender-for-storage-introduction.md)
- [Azure Defender voor SQL](defender-for-sql-introduction.md)
- [Azure Defender voor IoT](defender-for-iot-introduction.md)
- [Azure Defender voor Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender voor containerregisters](defender-for-container-registries-introduction.md)
- [Azure Defender voor Key Vault](defender-for-key-vault-introduction.md)

Elk van deze plannen wordt afzonderlijk uitgelegd in de Security Center documentatie.


## <a name="hybrid-cloud-protection"></a>Hybride Cloud beveiliging

En uw Azure-omgeving te beschermen, kunt u Azure Defender-mogelijkheden toevoegen aan uw hybride cloud omgeving:

- Uw niet-Azure-servers beveiligen
- Beveilig uw virtuele machines in andere Clouds (zoals AWS en GCP)
- Uw IoT-apparaten beveiligen

U krijgt aangepaste bedreigings informatie en waarschuwingen met prioriteit op basis van uw specifieke omgeving, zodat u zich kunt concentreren op de belangrijkste zaken

Implementeer [Azure-Arc](https://azure.microsoft.com/services/azure-arc/) en schakel Azure Defender in om de beveiliging uit te breiden naar on-premises en virtuele machines met meerdere clouds en SQL-data bases. Azure Arc voor servers is een gratis service, maar services die worden gebruikt op servers met Arc-functionaliteit, zoals Azure Defender, worden in rekening gebracht volgens de prijzen voor die service.

Meer [informatie over Azure Arc](https://docs.microsoft.com/azure/azure-arc/overview).


## <a name="azure-defender-alerts"></a>Azure Defender-waarschuwingen 

Wanneer Azure Defender een bedreiging in een wille keurig gebied van uw omgeving detecteert, wordt er een waarschuwing gegenereerd. Deze waarschuwingen beschrijven de details van de betrokken resources, voorgestelde herstels tappen en in sommige gevallen een logische app activeren als reactie.

Of een waarschuwing wordt gegenereerd door Security Center of wordt ontvangen door Security Center van een geïntegreerd beveiligings product, kunt u het exporteren. Als u uw waarschuwingen wilt exporteren naar Azure Sentinel, eventuele SIEM van derden of een ander extern hulp programma, volgt u de instructies in [waarschuwingen exporteren naar een Siem](continuous-export.md).

> [!NOTE]
> Het kan even duren voordat waarschuwingen van verschillende bronnen worden weer gegeven. Waarschuwingen waarvoor analyse van netwerk verkeer nodig is, kunnen bijvoorbeeld langer duren dan waarschuwingen die betrekking hebben op de verdachte processen die op virtuele machines worden uitgevoerd.


## <a name="azure-defender-advanced-protection-capabilities"></a>Mogelijkheden voor geavanceerde beveiliging van Azure Defender

Azure Defender maakt gebruik van geavanceerde analyses voor aangepaste aanbevelingen met betrekking tot uw resources. 

Beveiligingen zijn onder andere het beveiligen van de beheer poorten van uw Vm's met Just-in-time-toegangs-en adaptieve toepassings besturings elementen voor het maken van lijsten met toegestane apps en die niet moeten worden uitgevoerd op uw computers. 

Gebruik de tegels voor geavanceerde beveiliging in het dash board van Azure Defender om elk van deze beveiligingen te controleren en te configureren. 

## <a name="vulnerability-assessment-and-management"></a>Beoordeling en beheer van beveiligings problemen

Azure Defender bevat beveiligings problemen met het scannen van uw virtuele machines en container registers zonder extra kosten. De scanners worden aangedreven door Qualys, maar u hebt geen Qualys-licentie nodig of zelfs een Qualys-account: alles wordt naadloos verwerkt binnen Security Center. 

Bekijk de resultaten van deze beveiligings problemen met scanners en reageer op alle in Security Center. Dit brengt Security Center dichter om het enige glas venster te zijn voor al uw Cloud beveiligings pogingen.

Meer informatie vindt u op de volgende pagina's:

- [De oplossing voor het evalueren van geïntegreerde beveiligings problemen voor Azure virtual machines Security Center](deploy-vulnerability-assessment-vm.md)
- [Beveiligings problemen in installatie kopieën in azure-container registers identificeren](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd wat de voor delen van Azure Defender zijn. 

> [!div class="nextstepaction"]
> [Azure Defender inschakelen](security-center-pricing.md)