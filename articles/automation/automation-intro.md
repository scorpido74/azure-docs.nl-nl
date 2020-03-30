---
title: Overzicht van Azure Automation
description: Informatie over het gebruik van Azure Automation voor het automatiseren van de levenscyclus van infrastructuur en toepassingen.
services: automation
ms.subservice: process-automation
keywords: azure automation, DSC, powershell, configuratie van gewenste status, updatebeheer, bijhouden van wijzigingen, inventaris, runbooks, python, grafisch
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 3359d99d7e20bbced8950171fa34592fd2612500
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76930400"
---
# <a name="an-introduction-to-azure-automation"></a>Een inleiding tot Azure Automation

Azure Automation biedt een cloud-gebaseerde automatiserings- en configuratieservice die voor consistent beheer in uw Azure- en niet-Azure-omgevingen zorgt. De service omvat procesautomatisering, updatebeheer en configuratie van onderdelen. Azure Automation biedt volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van werkbelastingen en bronnen.
In dit artikel wordt een kort overzicht gegeven van Azure Automation en worden enkele veelgestelde vragen beantwoord. Gebruik de koppelingen in dit overzicht voor meer informatie over de verschillende mogelijkheden.

## <a name="azure-automation-capabilities"></a>Mogelijkheden van Azure Automation

![Overzicht van mogelijkheden van Automation](media/automation-overview/automation-overview.png)

### <a name="process-automation"></a>Procesautomatisering

Azure Automation biedt u de mogelijkheid om regelmatig terugkerende, tijdrovende en foutgevoelige taken voor cloudbeheer te automatiseren. Met Azure Automation kunt u zich concentreren op werk dat bedrijfswaarde toevoegt. Door het aantal fouten te reduceren en de efficiëntie te verbeteren, kunt u uw operationele kosten drukken. U kunt Azure-services en andere openbare systemen integreren die vereist zijn voor het implementeren, configureren en beheren van uw end-to-end-processen. Met de service kunt u [runbooks in grafische vorm ontwerpen](automation-runbook-types.md) in PowerShell of Python. Met behulp van een hybride Runbook-worker kunt u het beheer combineren door on-premises omgevingen te organiseren. [Webhooks](automation-webhooks.md) bieden een manier om te voldoen aan aanvragen en zorgen voor doorlopende levering en bewerkingen door automatisering vanuit ITSM, DevOps en controlesystemen te activeren.

### <a name="configuration-management"></a>Configuratiebeheer

[Configuratie van gewenste status](automation-dsc-overview.md) van Azure Automation is een cloudoplossing voor PowerShell DSC waarmee services worden geboden die zijn vereist voor bedrijfsomgevingen. Beheer uw DSC-resources in Azure Automation en pas configuraties toe op virtuele of fysieke machines vanaf een DSC-pull-server in de Azure-cloud. Dit biedt uitgebreide rapporten waarmee u wordt geïnformeerd over belangrijke gebeurtenissen zoals wanneer knooppunten zijn afgeweken van de toegewezen configuratie. U kunt machineconfiguraties controleren en automatisch bijwerken voor fysieke en virtuele machines, Windows of Linux, in de cloud of on-premises.

U kunt de inventarisatie opvragen van in-guest resources voor zichtbaarheid van de geïnstalleerde toepassingen en andere configuratie-items. Er zijn uitgebreide rapportage- en zoekmogelijkheden beschikbaar om snel gedetailleerde informatie te vinden voor informatie over wat er binnen het besturingssysteem is geconfigureerd. U kunt wijzigingen bijhouden voor services, daemons, software, register en bestanden om snel te oorzaak van de problemen vast te stellen. Bovendien kunt u met DSC nader onderzoek doen en waarschuwingen geven wanneer er ongewenste wijzigingen in uw omgeving plaatsvinden.

### <a name="update-management"></a>Updatebeheer

Windows-en Linux bijwerken in hybride omgevingen met Azure Automation. U krijgt een beeld van de updatevereisten in Azure, on-premises en andere clouds. U kunt planningsimplementaties maken voor het organiseren van de installatie van updates binnen een gedefinieerd onderhoudsvenster. Als een update niet op een computer moet worden geïnstalleerd, kunt u die updates uitsluiten van een implementatie.

### <a name="shared-resources"></a><a name="shared-resources"></a>Gedeelde resources

Azure Automation bestaat uit een set gedeelde bronnen waarmee u uw omgevingen eenvoudiger op schaal kunt automatiseren en configureren.

* **[Schema's](automation-schedules.md)**: worden gebruikt in de service om automatisering op vooraf gedefinieerde tijden te activeren.
* **[Modules](automation-integration-modules.md)**: modules worden gebruikt voor het beheren van Azure en andere systemen. Importeer gegevens in het Automation-account voor cmdlets en DSC-resources van Microsoft, van derden of uit de community, of voor aangepaste gedefinieerde cmdlets en DSC-resources.
* **[Modulegalerie](automation-runbook-gallery.md)**: systeemeigen integratie met de PowerShell Gallery om runbooks weer te geven en deze te importeren in het Automation-account.
* **[Python 2-pakketten](python-packages.md)**: voeg Python 2-pakketten toe aan uw automation-account om deze te gebruiken in uw Python-runbooks.
* **[Referenties](automation-credentials.md)**: hiermee kunt u vertrouwelijke informatie veilig opslaan die vervolgens kan worden gebruikt door runbooks en configuraties tijdens runtime.
* **[Verbindingen](automation-connections.md)**: sla een naam of waardepaar met informatie op waarin algemene informatie is opgenomen wanneer u verbinding maakt met systemen in verbindingsresources. Verbindingen worden gedefinieerd door de module-auteur voor gebruik tijdens runtime in runbooks en configuraties.
* **[Certificaten](automation-certificates.md)**: deze kunt u opslaan en beschikbaar stellen tijdens runtime, zodat ze kunnen worden gebruikt voor verificatie en beveiliging van geïmplementeerde resources.
* **[Variabelen](automation-variables.md)**: deze bieden een manier om inhoud te gebruiken die kan worden gebruikt in runbooks en configuraties. U kunt waarden wijzigen zonder een van de runbooks en configuraties die ernaar verwijzen te moeten veranderen.

### <a name="source-control-integration"></a>Integratie van bronbeheer

Azure Automation kan worden [geïntegreerd met broncodebeheer](source-control-integration.md), zodat de configuratie als code kan worden toegepast wanneer runbooks of configuraties in een broncodebeheersysteem kunnen worden ingecheckt.

### <a name="role-based-access-control"></a>Toegangsbeheer op basis van rollen

Azure Automation biedt ondersteuning voor op rollen gebaseerd toegangsbeheer (RBAC) om de toegang tot het Automation-account en de bijbehorende resources te beheren. Voor meer informatie over de configuratie van RBAC voor uw Automation-account, runbooks en taken, raadpleegt u [Op rollen gebaseerd toegangsbeheer voor Azure Automation](automation-role-based-access-control.md).

### <a name="windows-and-linux"></a>Windows en Linux

Azure Automation is ontworpen voor gebruik in uw hybride cloudomgeving, maar ook voor Windows en Linux. Het systeem biedt een consistente manier voor het automatiseren en configureren van geïmplementeerde werkbelastingen en van het besturingssysteem waarop ze worden uitgevoerd.

### <a name="community-gallery"></a>Community-galerie

Doorzoek de [Automation-galerie](automation-runbook-gallery.md) op runbooks en modules om snel aan de slag te gaan met het integreren en ontwerpen van uw processen vanuit de PowerShell-galerie en Microsoft Script Center.

## <a name="common-scenarios-for-automation"></a>Algemene scenario's voor Automation

Azure Automation beheert de gehele levenscyclus van uw infrastructuur en toepassingen. Voorzie het systeem van informatie over de manier waarop de organisatie werkbelastingen levert en onderhoudt. Ontwerp in algemene talen zoals PowerShell, de configuratie van de gewenste status, Python en grafische runbooks. Genereer een volledige inventarisatie van geïmplementeerde resources voor doelitems, rapportage en naleving. Stel de wijzigingen vast die kunnen leiden tot een onjuiste configuratie en die kunnen zorgen voor een betere operationele naleving.

* **Resources bouwen/implementeren**: implementeer virtuele machines in een hybride omgeving met behulp van Runbooks en Azure Resource Manager-sjablonen. Integreer met ontwikkelingsprogramma's zoals Jenkins en Azure DevOps.
* **VM's configureren**: evalueer en configureer Windows- en Linux-machines met de gewenste configuratie voor infrastructuur en toepassing.
* **Controle**: stel wijzigingen op machines vast die problemen veroorzaken en die oplossingen bieden voor of escaleren met beheersystemen.
* **Beveiliging**: plaats virtuele machines in quarantaine als er een beveiligingswaarschuwing wordt gegeven. Stel in-guest vereisten in.
* **Reguleren**: stel toegangsbeheer op basis van rollen in voor klanten. Herstel niet-gebruikte resources.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Prijzen voor Automation

U kunt de prijs voor Azure Automation bekijken op de pagina [Prijzen](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een automatiseringsaccount maken](automation-quickstart-create-account.md)

