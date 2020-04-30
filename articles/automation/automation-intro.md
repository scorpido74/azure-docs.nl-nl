---
title: Overzicht van Azure Automation
description: Informatie over het gebruik van Azure Automation voor het automatiseren van de levenscyclus van infrastructuur en toepassingen.
services: automation
ms.subservice: process-automation
keywords: Azure Automation, DSC, Power shell, status configuratie, update beheer, wijzigingen bijhouden, DSC, inventaris, runbooks, Python, grafisch
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 8ee8fd4d9a81746be7b65aeb6410691a5e3aea96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81010236"
---
# <a name="an-introduction-to-azure-automation"></a>Een inleiding tot Azure Automation

In dit artikel wordt een kort overzicht gegeven van Azure Automation en worden enkele veelgestelde vragen beantwoord. Gebruik de koppelingen in dit overzicht voor meer informatie over de verschillende mogelijkheden.

## <a name="about-azure-automation"></a>Over Azure Automation

Azure Automation levert een cloud-gebaseerde Automation-en configuratie service die consistent beheer in uw Azure-en niet-Azure-omgevingen ondersteunt. Het omvat proces automatisering, configuratie beheer, update beheer, gedeelde mogelijkheden en heterogene functies. Automation geeft u volledige controle over de implementatie, bewerkingen en het buiten gebruik stellen van werk belastingen en resources.

![Automatiserings mogelijkheden](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Procesautomatisering

Proces automatisering in Azure Automation kunt u veelvuldige, tijdrovende en fout gevoelige taken voor Cloud beheer automatiseren. Met deze service kunt u zich richten op werk dat bedrijfs waarde toevoegt. Door het aantal fouten te reduceren en de efficiëntie te verbeteren, kunt u uw operationele kosten drukken. De proces automatiserings besturings omgeving wordt gedetailleerd beschreven in het uitvoeren van een [Runbook in azure Automation](automation-runbook-execution.md).

Proces automatisering ondersteunt de integratie van Azure-Services en andere open bare systemen die vereist zijn voor het implementeren, configureren en beheren van uw end-to-end-processen. Met deze service kunt u [runbooks](automation-runbook-types.md) grafisch ontwerpen, in Power shell of met python. Door een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md)te gebruiken, kunt u beheer samen voegen door te organiseren in on-premises omgevingen. [Webhooks](automation-webhooks.md) bieden u de mogelijkheid om te voldoen aan aanvragen en te zorgen voor continue levering en bewerkingen door automatisering vanuit ITSM-, DevOps-en bewakings systemen te activeren. 

## <a name="configuration-management"></a>Configuratiebeheer

Azure Automation [status configuratie](automation-dsc-overview.md) is een op de cloud gebaseerde oplossing voor Power shell desired state Configuration (DSC) die services biedt voor bedrijfs omgevingen. Met deze functie kunt u uw DSC-resources in Azure Automation beheren en configuraties Toep assen op virtuele of fysieke machines van een DSC-pull-server in de Azure-Cloud. U kunt computer configuraties bewaken en automatisch bijwerken voor fysieke en virtuele machines, Windows of Linux, in de Cloud of on-premises. Met inventaris ondersteuning kunt u in-Guest-resources zoeken naar geïnstalleerde toepassingen en andere configuratie-items.
 
De Azure Automation State Configuration-service biedt uitgebreide rapportage-en zoek mogelijkheden. U kunt deze functies gebruiken om gedetailleerde informatie te vinden over wat is geconfigureerd in een besturings systeem. De service biedt ondersteuning voor het bijhouden van wijzigingen in Services, daemons, software, REGI ster en bestanden in uw omgeving om u te helpen bij het vaststellen van ongewenste wijzigingen en het activeren van waarschuwingen. Een belang rijke gerelateerde functie is het rapporteren van belang rijke gebeurtenissen, zoals gebeurtenissen die worden uitgegeven wanneer knoop punten afwijken van de toegewezen configuraties. 

## <a name="update-management"></a>Updatebeheer

Azure Automation bevat de oplossing [Update beheer](automation-update-management.md) voor Windows-en Linux-systemen in hybride omgevingen. Met deze oplossing krijgt u inzicht in de update vereisten voor Azure en andere Clouds en on-premises. Met update beheer kunt u geplande implementaties maken die de installatie van updates binnen een gedefinieerd onderhouds venster organiseren. Als een update niet mag worden geïnstalleerd op een computer, kunt u de update beheer functies gebruiken om deze uit te sluiten van een implementatie.

## <a name="shared-capabilities"></a>Gedeelde functionaliteit

Azure Automation biedt een aantal gedeelde mogelijkheden, waaronder gedeelde resources, Toegangs beheer op basis van rollen, flexibele planning, integratie van broncode beheer, controle en tagging.

### <a name="shared-resources"></a><a name="shared-resources"></a>Gedeelde resources

Azure Automation bestaat uit een set gedeelde bronnen waarmee u uw omgevingen eenvoudiger op schaal kunt automatiseren en configureren.

* **[Schema's](automation-schedules.md)** -Automation-bewerkingen activeren op vooraf gedefinieerde tijdstippen.
* **[Modules](automation-integration-modules.md)** : beheer Azure en andere systemen. U kunt modules importeren in het Automation-account voor cmdlets van micro soft, van derden, Community en aangepaste en DSC-resources.
* **[Galerie modules](automation-runbook-gallery.md)** : ondersteunt systeem eigen integratie met de PowerShell Gallery om runbooks weer te geven en te importeren in het Automation-account. De galerie biedt u de mogelijkheid om snel aan de slag te gaan met het integreren en ontwerpen van uw processen vanuit Power shell Gallery en micro soft Script Center.
* **[Python 2-pakketten](python-packages.md)** : ondersteuning voor python 2-runbooks voor uw Automation-account.
* **[Referenties](automation-credentials.md)** : Bewaar veilig gevoelige informatie die runbooks en configuraties in runtime kunnen gebruiken.
* **[Verbindingen](automation-connections.md)** -Store name-value-paren van algemene informatie voor verbindingen met systemen. De module Auteur definieert verbindingen in runbooks en configuraties voor gebruik tijdens runtime.
* **[Certificaten](automation-certificates.md)** : Definieer informatie die moet worden gebruikt bij de verificatie en het beveiligen van geïmplementeerde bronnen wanneer deze worden geopend door RUNBOOKS of DSC-configuraties tijdens runtime. 
* **[Variabelen](automation-variables.md)** : Houd inhoud die kan worden gebruikt in runbooks en configuraties. U kunt variabelen waarden wijzigen zonder dat u de runbooks of configuraties die ernaar verwijzen, hoeft te wijzigen.

### <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Azure Automation ondersteunt op rollen gebaseerd toegangs beheer (RBAC) om toegang tot het Automation-account en de bijbehorende resources te reguleren. Zie op [rollen gebaseerd toegangs beheer voor Azure Automation voor](automation-role-based-access-control.md)meer informatie over het configureren van RBAC voor uw Automation-account, runbooks en taken.

### <a name="source-control-integration"></a>Integratie van bronbeheer

Azure Automation is [integratie van broncode beheer](source-control-integration.md)mogelijk. Deze functie bevordert de configuratie als code waar runbooks of configuraties kunnen worden ingecheckt in een broncode beheersysteem.

## <a name="heterogeneous-support-windows-and-linux"></a>Heterogene ondersteuning (Windows en Linux)

Automation is ontworpen om te werken in uw hybride cloud omgeving en op uw Windows-en Linux-systemen. Het biedt een consistente manier om geïmplementeerde workloads en de besturings systemen die ze uitvoeren te automatiseren en te configureren.

## <a name="common-scenarios-for-automation"></a>Algemene scenario's voor Automation

Azure Automation ondersteunt het beheer gedurende de levens cyclus van uw infra structuur en toepassingen. Algemene scenario's omvatten de volgende:

* **Schrijf runbooks** -Maak Power shell-, Power shell-werk stroom, grafische documenten, Python 2 en DSC-runbooks in algemene talen. 
* **Bouw en implementeer resources** -Implementeer virtuele machines in een hybride omgeving met behulp van runbooks en Azure Resource Manager sjablonen. Integreer in ontwikkel hulpprogramma's, zoals Jenkins en Azure DevOps.
* **Vm's configureren** : Evalueer en configureer Windows-en Linux-machines met configuraties voor de infra structuur en toepassing.
* **Deel kennis** : breng kennis over aan het systeem over hoe uw organisatie workloads levert en onderhoudt. 
* **Inventaris ophalen** : krijg een volledige inventarisatie van geïmplementeerde resources voor doel items, rapportage en naleving. 
* **Wijzigingen zoeken** : wijzigingen identificeren die kunnen leiden tot een onjuiste configuratie en de operationele naleving verbeteren.
* **Bewaking** : computer wijzigingen isoleren die problemen veroorzaken en deze herstellen of escaleren naar beheer systemen.
* **Beveilig** -quarantaine computers als er beveiligings waarschuwingen worden gegenereerd. Stel in-guest vereisten in.
* **Bepalen** -RBAC instellen voor teams. Herstel niet-gebruikte resources.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Prijzen voor Automation

U kunt de prijzen controleren die zijn gekoppeld aan Azure Automation op de pagina met [prijzen](https://azure.microsoft.com/pricing/details/automation/) .

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Automation-account maken](automation-quickstart-create-account.md)

