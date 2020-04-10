---
title: Overzicht van Azure Automation
description: Informatie over het gebruik van Azure Automation voor het automatiseren van de levenscyclus van infrastructuur en toepassingen.
services: automation
ms.subservice: process-automation
keywords: azure automation, DSC, powershell, state configuration, update management, change tracking, DSC, inventory, runbooks, python, graphical
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 8ee8fd4d9a81746be7b65aeb6410691a5e3aea96
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010236"
---
# <a name="an-introduction-to-azure-automation"></a>Een inleiding tot Azure Automation

In dit artikel wordt een kort overzicht gegeven van Azure Automation en worden enkele veelgestelde vragen beantwoord. Gebruik de koppelingen in dit overzicht voor meer informatie over de verschillende mogelijkheden.

## <a name="about-azure-automation"></a>Over Azure Automation

Azure Automation biedt een cloudgebaseerde automatiserings- en configuratieservice die consistent beheer ondersteunt in uw Azure- en niet-Azure-omgevingen. Het omvat procesautomatisering, configuratiebeheer, updatebeheer, gedeelde mogelijkheden en heterogene functies. Automatisering geeft u volledige controle tijdens implementatie, bewerkingen en ontmanteling van workloads en resources.

![Automatiseringsmogelijkheden](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Procesautomatisering

Met procesautomatisering in Azure Automation u frequente, tijdrovende en foutgevoelige cloudbeheertaken automatiseren. Deze service helpt u zich te concentreren op werk dat bedrijfswaarde toevoegt. Door het aantal fouten te reduceren en de efficiëntie te verbeteren, kunt u uw operationele kosten drukken. De operationele omgeving voor procesautomatisering wordt gedetailleerd beschreven in [runbook-uitvoering in Azure Automation.](automation-runbook-execution.md)

Procesautomatisering ondersteunt de integratie van Azure-services en andere openbare systemen die nodig zijn voor het implementeren, configureren en beheren van uw end-to-end processen. Met de service u [grafisch hardloopboeken](automation-runbook-types.md) maken, in PowerShell of python gebruiken. Door een [hybride runbookworker te](automation-hybrid-runbook-worker.md)gebruiken, u het beheer verenigen door on-premises omgevingen te orkestreren. [Met Webhooks](automation-webhooks.md) u voldoen aan aanvragen en zorgen voor continue levering en bewerkingen door automatisering van ITSM, DevOps en bewakingssystemen te activeren. 

## <a name="configuration-management"></a>Configuratiebeheer

Azure Automation [state configuration](automation-dsc-overview.md) is a cloud-based solution for PowerShell desired state configuration (DSC) that provides services for enterprise environments. Met deze functie u uw DSC-resources beheren in Azure Automation en configuraties toepassen op virtuele of fysieke machines vanaf een DSC pull-server in de Azure-cloud. U machineconfiguraties controleren en automatisch bijwerken op fysieke en virtuele machines, op Windows of Linux, in de cloud of on-premises. Met voorraadondersteuning u in-guest-bronnen opvragen voor inzicht in geïnstalleerde toepassingen en andere configuratie-items.
 
De configuratieservice azure automation-status biedt uitgebreide rapportage- en zoekmogelijkheden. U deze functies gebruiken om gedetailleerde informatie te vinden over wat er binnen een besturingssysteem is geconfigureerd. De service ondersteunt het bijhouden van wijzigingen tussen services, daemons, software, register en bestanden in uw omgeving om u te helpen bij het diagnosticeren van ongewenste wijzigingen en het verhogen van waarschuwingen. Een belangrijke gerelateerde functie is het melden van belangrijke gebeurtenissen, bijvoorbeeld gebeurtenissen die worden uitgegeven wanneer knooppunten afwijken van hun toegewezen configuraties. 

## <a name="update-management"></a>Updatebeheer

Azure Automation bevat de oplossing voor [updatebeheer](automation-update-management.md) voor Windows- en Linux-systemen in hybride omgevingen. Met deze oplossing krijgt u inzicht in de naleving van updates in Azure en andere clouds en on-premises. Met updatebeheer u geplande implementaties maken die de installatie van updates in een gedefinieerd onderhoudsvenster orkestreren. Als een update niet op een machine moet worden geïnstalleerd, u functies voor updatebeheer gebruiken om deze uit te sluiten van een implementatie.

## <a name="shared-capabilities"></a>Gedeelde functionaliteit

Azure Automation biedt een aantal gedeelde mogelijkheden, waaronder gedeelde resources, op rollen gebaseerd toegangscontrole, flexibele planning, integratie van bronbeheer, controle en tagging.

### <a name="shared-resources"></a><a name="shared-resources"></a>Gedeelde resources

Azure Automation bestaat uit een set gedeelde bronnen waarmee u uw omgevingen eenvoudiger op schaal kunt automatiseren en configureren.

* **[Planningen](automation-schedules.md)** - Trigger Automation-bewerkingen op vooraf gedefinieerde tijden.
* **[Modules](automation-integration-modules.md)** - Azure en andere systemen beheren. U modules importeren in het automatiseringsaccount voor Microsoft, externe, community- en op maat gemaakte cmdlets- en DSC-resources.
* **[Modulesgalerij](automation-runbook-gallery.md)** - Ondersteunt native integratie met de PowerShell-galerie om u runbooks te laten bekijken en importeren in het automatiseringsaccount. Met de galerie u snel aan de slag met het integreren en ontwerpen van uw processen vanuit PowerShell-galerie en Microsoft Script Center.
* **[Python 2-pakketten](python-packages.md)** - Ondersteuning voor Python 2-runbooks voor uw Automation-account.
* **[Referenties](automation-credentials.md)** - Sla gevoelige informatie die boeken en configuraties kunnen gebruiken veilig op tijdens uitvoering.
* **[Verbindingen](automation-connections.md)** - Sla naam-waardeparen van algemene informatie op voor verbindingen met systemen. De moduleauteur definieert verbindingen in runbooks en configuraties voor gebruik tijdens runtime.
* **[Certificaten](automation-certificates.md)** : definieer informatie die moet worden gebruikt bij verificatie en beveiliging van geïmplementeerde resources wanneer deze worden geopend door runbooks of DSC-configuraties tijdens uitvoering. 
* **[Variabelen](automation-variables.md)** - Houd inhoud vast die kan worden gebruikt in runbooks en configuraties. U variabele waarden wijzigen zonder dat u een van de runbooks of configuraties hoeft te wijzigen waarnaar wordt verwezen.

### <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Azure Automation ondersteunt rbac (role-based access control) om de toegang tot het Automatiseringsaccount en de bijbehorende resources te regelen. Zie Op rollen [gebaseerdtoegangscontrole voor Azure Automation voor](automation-role-based-access-control.md)meer informatie over het configureren van RBAC op uw Automatiseringsaccount, runbooks en taken.

### <a name="source-control-integration"></a>Integratie van bronbeheer

Azure Automation maakt [integratie van bronbeheer](source-control-integration.md)mogelijk. Deze functie bevordert de configuratie als code waarbij runbooks of configuraties kunnen worden ingecheckt in een broncontrolesysteem.

## <a name="heterogeneous-support-windows-and-linux"></a>Heterogene ondersteuning (Windows en Linux)

Automatisering is ontworpen om te werken in uw hybride cloudomgeving en ook uw Windows- en Linux-systemen. Het biedt een consistente manier om geïmplementeerde workloads en de besturingssystemen waarop ze worden uitgevoerd te automatiseren en te configureren.

## <a name="common-scenarios-for-automation"></a>Algemene scenario's voor Automation

Azure Automation ondersteunt beheer gedurende de gehele levenscyclus van uw infrastructuur en toepassingen. Algemene scenario's omvatten de volgende:

* **Runbooks schrijven** - Auteur PowerShell, PowerShell Workflow, grafische, Python 2 en DSC runbooks in algemene talen. 
* **Resources bouwen en implementeren** - Implementeer virtuele machines in een hybride omgeving met runbooks en Azure Resource Manager-sjablonen. Integreren in ontwikkeltools, zoals Jenkins en Azure DevOps.
* **VM's configureren** - Windows- en Linux-machines beoordelen en configureren met configuraties voor de infrastructuur en toepassing.
* **Kennis delen** - Breng kennis over naar het systeem over hoe uw organisatie workloads levert en onderhoudt. 
* **Voorraad ophalen** : ontvang een volledige voorraad van geïmplementeerde resources voor targeting, rapportage en naleving. 
* **Wijzigingen zoeken** : identificeer wijzigingen die een verkeerde configuratie kunnen veroorzaken en verbeter de operationele naleving.
* **Monitor** - Herstel machinewijzigingen die problemen veroorzaken en verwerk ze of escaleer ze naar beheersystemen.
* **Bescherm** - Quarantainemachines als er beveiligingswaarschuwingen worden opgehaald. Stel in-guest vereisten in.
* **Regeren** - RBAC instellen voor teams. Herstel niet-gebruikte resources.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Prijzen voor Automation

U de prijzen die zijn gekoppeld aan Azure Automation bekijken op de [prijspagina.](https://azure.microsoft.com/pricing/details/automation/)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een automatiseringsaccount maken](automation-quickstart-create-account.md)

