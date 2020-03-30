---
title: Azure DevTest Labs gebruiken voor VM- en PaaS-testomgevingen | Microsoft Documenten
description: Meer informatie over het gebruik van Azure DevTest Labs voor VM- en PaaS-testomgevingscenario's.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: d4e2c334-643a-40c9-9051-625b8f39fc86
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: c6b458091a8e5e22cca55d401e89e5e13bcf9de9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60623172"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Azure DevTest Labs gebruiken voor VM- en PaaS-testomgevingen

U kunt Azure DevTest Labs gebruiken voor het implementeren van allerlei scenario's. Eén van de belangrijkste scenario's is het inzetten van DevTest Labs voor het hosten van machines voor testers. 

In dit scenario biedt DevTest Labs deze voordelen:

- Testers kunnen de nieuwste versie van hun toepassing testen door Windows- en Linux-omgevingen snel in te richten met behulp van herbruikbare sjablonen en artefacten.
- Testers kunnen hun belastingstests omhoog schalen door meerdere test-agents in te richten.
- Beheerders kunnen de kosten beheersen door ervoor te zorgen dat:
  - Testers kunnen niet meer VM's krijgen dan ze nodig hebben.
  - VM's worden afgesloten wanneer ze niet in gebruik zijn.

![DevTest Labs gebruiken voor training](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

In dit artikel vindt u meer informatie over verschillende Azure DevTest Labs-functies die worden gebruikt om te voldoen aan de vereisten voor tester en de gedetailleerde stappen die moeten worden gevolgd om een lab in te stellen.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Testomgevingen implementeren met Azure DevTest Labs
1. **Maak het lab** 
   
    Labs zijn het startpunt in Azure DevTest Labs. Zodra u een lab hebt gemaakt, u taken uitvoeren, zoals het toevoegen van gebruikers (testers) aan het lab, het instellen van beleid om de kosten te beheersen, vm-afbeeldingen definiëren die snel kunnen maken en meer.  
   
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een lab maken in Azure DevTest Labs](devtest-lab-create-lab.md) |Meer informatie over het maken van een lab in Azure DevTest Labs in de Azure-portal. |
2. **Vm's maken in enkele minuten met behulp van kant-en-klare marketplace-afbeeldingen en aangepaste afbeeldingen** 
   
    U kant-en-klare afbeeldingen kiezen uit een breed scala aan afbeeldingen in de Azure Marketplace en deze beschikbaar maken in het lab. Als de kant-en-klare afbeeldingen niet aan uw vereisten voldoen, u een aangepaste afbeelding maken door een lab-vm te maken met behulp van een kant-en-klare afbeelding van Azure Marketplace, alle software te installeren die u nodig hebt en de VM op te slaan als een aangepaste afbeelding in het lab.

    Als u aangepaste afbeeldingen gebruikt, u overwegen een afbeeldingsfabriek te gebruiken om uw afbeeldingen te maken en te distribueren. Een afbeeldingsfabriek is een configuratie-als-code-oplossing die uw geconfigureerde afbeeldingen automatisch bouwt en distribueert. Dit bespaart de tijd die nodig is om het systeem handmatig te configureren nadat een VM is gemaakt met het basisbesturingssysteem.
  
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Azure Marketplace-installatiekopieën configureren](devtest-lab-configure-marketplace-images.md) |Lees hoe u Azure Marketplace-afbeeldingen op de witte lijst plaatsen, waardoor u alleen de afbeeldingen die u voor de testers wilt selecteren, selecteren.|
   | [Een aangepaste installatiekopie maken](devtest-lab-create-template.md) |Maak een aangepaste afbeelding door de software die u nodig hebt vooraf te installeren, zodat testers snel een VM kunnen maken met behulp van de aangepaste afbeelding.|
   | [Meer informatie over afbeeldingsfabriek](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Bekijk een video waarin wordt beschreven hoe u een afbeeldingsfabriek instelt en gebruikt.|

3. **Herbruikbare sjablonen maken voor testmachines** 
   
    Een formule in Azure DevTest Labs is een lijst met standaardeigenschapwaarden die worden gebruikt om een VM te maken. U een formule in het lab maken door een afbeelding, een VM-grootte (een combinatie van CPU en RAM) en een virtueel netwerk te kiezen. Elke tester kan de formule in het lab zien en deze gebruiken om een VM te maken. 
   
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [DevTest Labs-formules beheren om VM's te maken](devtest-lab-manage-formulas.md) |Ontdek hoe u een formule maken door een afbeelding, VM-grootte (combinatie van CPU en RAM) en een virtueel netwerk op te pikken.|

3. **MultiVM-testomgevingen maken** 
   
    U Azure Resource Manager-sjablonen gebruiken om de infrastructuur en configuratie van uw Azure-oplossing te definiëren en herhaaldelijk meerdere testVM's in een consistente status te implementeren.

    Azure PaaS-resources kunnen worden ingericht in een omgeving vanuit een Resource Manager-sjabloon en worden weergegeven in kostentracking. Het automatisch afsluiten van VM is echter niet van toepassing op PaaS-resources.

    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Multi-VM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md) |Ontdek hoe u meerdere VM's in een consistente status implementeren voor uw testomgeving.|

4. **Artefacten maken om flexibele vm-aanpassing mogelijk te maken**

   Artefacten worden gebruikt om uw toepassing te implementeren en te configureren nadat een VM is ingericht. Artefacten kunnen zijn:

   - Hulpprogramma's die u op de VM wilt installeren, zoals agents, Fiddler en Visual Studio.
   - Acties die u op de VM wilt uitvoeren, zoals het klonen van een repo.
   - Toepassingen die u wilt testen.

   Veel artefacten zijn al out-of-the-box beschikbaar. Maar als u meer aanpassing voor uw specifieke behoeften wilt, u uw eigen aangepaste artefacten maken.

   Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Aangepaste artefacten maken voor uw VM Van DevTest Labs](devtest-lab-artifact-author.md) |Maak je eigen aangepaste artefacten voor de virtuele machines in je lab.|
   | [Een Git-opslagplaats toevoegen om aangepaste artefacten en Azure Resource Manager-sjablonen op te slaan voor gebruik in Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Meer informatie over het opslaan van uw aangepaste artefacten in uw eigen privé-Git-repo.|

5. **Beheerskosten**
   
    Met Azure DevTest Labs u een beleid in het lab instellen om het maximum aantal VM's op te geven dat door een tester in het lab kan worden gemaakt. 
   
    Als uw testteam een vast werkschema heeft en u alle VM's op een bepaald tijdstip van de dag wilt stoppen en deze de volgende dag automatisch opnieuw wilt starten, u dat eenvoudig bereiken door het beleid voor automatisch afsluiten en automatisch starten in het lab in te stellen. 
   
    Ten slotte u, wanneer de ontwikkeling van de app is voltooid, alle VM's tegelijk verwijderen door één PowerShell-script uit te voeren. 
   
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Beleid voor lab maken](devtest-lab-set-lab-policy.md) |Beheer de kosten door beleid in het lab in te stellen. |
   | [Alle lab-VM's verwijderen met behulp van een PowerShell-script](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Verwijder alle labs in één bewerking wanneer het testen is voltooid.|

1. **Een virtueel netwerk toevoegen aan een lab** 
   
    DevTest Labs creëert een nieuw virtueel netwerk (VNET) wanneer een lab wordt gemaakt. Als u uw eigen VNET hebt geconfigureerd - bijvoorbeeld door ExpressRoute of site-to-site VPN te gebruiken - u deze VNET toevoegen aan de virtuele netwerkinstellingen van uw lab, zodat deze beschikbaar is bij het maken van VM's.

    Bovendien is er een Azure Active Directory-domeinjoinartefact beschikbaar dat een VM aansluit bij een domein wanneer de VM wordt gemaakt. 
   
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een virtueel netwerk configureren in Azure DevTest Labs](devtest-lab-configure-vnet.md) |Meer informatie over het configureren van een virtueel netwerk in Azure DevTest Labs met behulp van de Azure-portal.|

6. **Deel het lab met elke tester**
   
    Labs zijn rechtstreeks toegankelijk via een link die u deelt met uw testers. Ze hoeven niet eens een Azure-account te hebben, zolang ze maar een [Microsoft-account](devtest-lab-faq.md#what-is-a-microsoft-account)hebben. Testers kunnen vm's die door andere testers zijn gemaakt, niet zien.  
   
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een tester toevoegen aan een lab in Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Gebruik de Azure-portal om testers aan uw lab toe te voegen.|
   | [Testers toevoegen aan het lab met behulp van een PowerShell-script](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Gebruik PowerShell om het toevoegen van testers aan uw lab te automatiseren. |
   | [Krijg een link naar het lab](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Ontdek hoe testers rechtstreeks toegang hebben tot een lab via een hyperlink.|

7. **Automatiseer labcreatie voor meer teams** 
   
    U het maken van laboratoria automatiseren, inclusief aangepaste instellingen, door een Resource Manager-sjabloon te maken en deze steeds opnieuw te gebruiken om identieke labs te maken. 
   
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een lab maken met een resourcemanagersjabloon](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Maak labs in Azure DevTest Labs met behulp van Resource Manager-sjablonen. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

