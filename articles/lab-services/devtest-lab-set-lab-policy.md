---
title: Labbeleid beheren in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het definiëren van labbeleid, zoals VM-formaten, maximale VM's per gebruiker en afsluiten van automatisering.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: aa0ffbd69e73ddbef72e0eabf79f2736079c3d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270717"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Alle beleidsregels voor een lab beheren in Azure DevTest Labs

Met Azure DevTest Labs u de kosten beheren en afval in uw laboratoria minimaliseren door beleid (instellingen) voor elk lab te beheren. In dit artikel wordt stap voor stap uitgelegd hoe u elk beleid instelt.  

## <a name="set-allowed-virtual-machine-sizes"></a>Toegestane virtuele machineformaten instellen
Het beleid voor het instellen van de toegestane VM-formaten helpt om laboratoriumverspilling te minimaliseren door u in staat te stellen op te geven welke VM-formaten in het lab zijn toegestaan. Als dit beleid is geactiveerd, kunnen alleen VM-formaten uit deze lijst worden gebruikt om VM's te maken.

1. Selecteer in de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)een lab en selecteer **Vervolgens Configuratie en beleid**.

    ![Toegang tot de configuratie en het beleid van het lab](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. Selecteer in het **deelvenster Configuratie en beleid** van het lab de optie Toegestane virtuele **machineformaten**.
   
    ![Toegestane virtuele machines maten](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Selecteer **Aan** om dit beleid in te schakelen en **Schakel het** uit.

1. Als u dit beleid inschakelt, selecteert u een of meer VM-formaten die in uw lab kunnen worden gemaakt.

1. Selecteer **Opslaan**.

## <a name="set-virtual-machines-per-user"></a>Virtuele machines per gebruiker instellen
Met het beleid voor **virtuele machines per gebruiker** u het aantal VM's opgeven dat door een afzonderlijke gebruiker kan worden gemaakt. Als een gebruiker een VM probeert te maken of te claimen wanneer de gebruikerslimiet is bereikt, geeft een foutbericht aan dat de vm niet kan worden gemaakt/geclaimd. 

1. Selecteer **virtuele machines per gebruiker**in het deelvenster Configuratie en **beleidsregels** van het lab.
   
    ![Virtuele machines per gebruiker](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selecteer **Ja** om het aantal VM's per gebruiker te beperken. Als u het aantal VM's per gebruiker niet wilt beperken, selecteert u **Nee**. Als u **Ja**selecteert, voert u een numerieke waarde in die het aantal VM's aangeeft dat door een gebruiker kan worden gemaakt of geclaimd. 

1. Selecteer **Ja** om het aantal VM's te beperken dat SSD (solid-state disk) kan gebruiken. Als u het aantal VM's dat SSD kan gebruiken niet wilt beperken, selecteert u **Nee**. Als u **Ja**selecteert, voert u een waarde in die het aantal VM's aangeeft dat met SSD kan worden gemaakt. 

1. Selecteer **Opslaan**.

## <a name="set-virtual-machines-per-lab"></a>Virtuele machines instellen per lab
Met het beleid voor **virtuele machines per lab** u het aantal VM's opgeven dat voor het huidige lab kan worden gemaakt. Als een gebruiker een VM probeert te maken wanneer de lablimiet is bereikt, geeft een foutbericht aan dat de vm niet kan worden gemaakt. 

1. Selecteer **virtuele machines per lab**in het deelvenster Configuratie en **beleid** van het lab.
   
    ![Virtuele machines per lab](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Selecteer **Ja** om het aantal VM's per lab te beperken. Als u het aantal VM's per lab niet wilt beperken, selecteert u **Nee**. Als u **Ja**selecteert, voert u een numerieke waarde in die het aantal VM's aangeeft dat door een gebruiker kan worden gemaakt of geclaimd. 

1. Selecteer **Ja** om het aantal VM's te beperken dat SSD (solid-state disk) kan gebruiken. Als u het aantal VM's dat SSD kan gebruiken niet wilt beperken, selecteert u **Nee**. Als u **Ja**selecteert, voert u een waarde in die het aantal VM's aangeeft dat met SSD kan worden gemaakt. 

1. Selecteer **Opslaan**.

## <a name="set-auto-shutdown"></a>Automatische afsluiting instellen
Het beleid voor het afsluiten van auto's helpt labverspilling te minimaliseren door u de tijd te laten opgeven waarop de VM's van dit lab worden afgesloten.

1. Selecteer Automatisch **afsluiten**in het deelvenster **Configuratie en beleid** van het lab.
   
    ![Automatisch afsluiten](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selecteer **Aan** om dit beleid in te schakelen en **Schakel het** uit.

1. Als u dit beleid inschakelt, geeft u de tijd (en tijdzone) op om alle VM's in het huidige lab af te sluiten.

1. Geef **Ja** of **Nee** op voor de optie om een melding te verzenden 15 minuten voor de opgegeven automatische afsluitingstijd. Als u **Ja**kiest, voert u een webhook URL-eindpunt of een e-mailadres in waarin wordt aangegeven waar u de melding wilt plaatsen of verzenden. De gebruiker ontvangt een melding en krijgt de mogelijkheid om de shutdown uit te stellen.

   Zie [Een webhook- of API Azure-functie maken](../azure-functions/functions-create-a-web-hook-or-api-function.md)voor meer informatie over webhooks. 

1. Selecteer **Opslaan**.

Dit beleid is standaard van toepassing op alle VM's in het huidige lab, eenmaal ingeschakeld. Als u deze instelling uit een specifieke virtuele machine wilt verwijderen, opent u het beheervenster van de virtuele machine en wijzigt u de instelling **Automatisch afsluiten.**

## <a name="set-auto-shutdown-policy"></a>Beleid voor automatisch afsluiten instellen
Als eigenaar van een lab u een afsluitschema configureren voor alle VM's in uw lab. Door dit te doen, u kosten besparen door machines die niet worden gebruikt (idle). U een afsluitbeleid voor al uw lab-VM's centraal afdwingen, maar ook uw labgebruikers de moeite besparen om een schema voor hun afzonderlijke machines in te stellen. Met deze functie u het beleid op uw labschema instellen, vanaf het bieden van geen controle tot volledige controle, voor uw labgebruikers. Als eigenaar van een lab u dit beleid configureren door de volgende stappen te nemen:

1. Selecteer op de startpagina van uw lab de optie **Configuratie en beleid**.
2. Selecteer **Beleid voor automatisch afsluiten** in het gedeelte **Schema's** van het linkermenu.
3. Selecteer een van de opties. In de volgende secties vindt u meer informatie over deze opties: Het ingestelde beleid is alleen van toepassing op nieuwe VM's die in het lab zijn gemaakt en niet op de reeds bestaande VM's. 

    ![Beleidsopties voor automatisch afsluiten](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Gebruiker stelt een planning in en kan zich afmelden
Als u uw lab op dit beleid instelt, kunnen de labgebruikers het labschema overschrijven of afmelden. Deze optie geeft labgebruikers volledige controle over het automatische afsluitschema van hun VM's. Labgebruikers zien geen wijzigingen in hun pagina met automatische afsluitingsschema's voor vm's.

![Beleidsoptie Automatisch afsluiten - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Gebruiker stelt een planning in en kan zich niet afmelden
Als u uw lab op dit beleid instelt, kunnen labgebruikers het labschema overschrijven. Ze kunnen zich echter niet afmelden voor het beleid voor automatische afsluiting. Deze optie zorgt ervoor dat elke machine in uw lab onder een automatisch afsluitschema valt. Lab-gebruikers kunnen het automatische afsluitschema van hun VM's bijwerken en meldingen voor afsluiten instellen.

![Beleidsoptie Automatisch afsluiten - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Gebruiker heeft geen controle over het schema dat is ingesteld door de labbeheerder
Als u uw lab op dit beleid instelt, kunnen labgebruikers het labschema niet overschrijven of afmelden. Deze optie biedt lab admin de volledige controle over het schema voor elke machine in het lab. Lab-gebruikers kunnen alleen automatische afsluitingsmeldingen instellen voor hun VM's.

![Beleidsoptie Automatisch afsluiten - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Automatisch starten instellen
Met het beleid voor automatisch starten u opgeven wanneer de VM's in het huidige lab moeten worden gestart.  

1. Selecteer Automatisch starten in het deelvenster **Configuratie en beleidsregels** van het lab . **Autostart**
   
    ![Automatisch starten](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Selecteer **Aan** om dit beleid in te schakelen en **Schakel het** uit.

3. Als u dit beleid inschakelt, geeft u de geplande begintijd, tijdzone en de dagen van de week waarvoor de tijd van toepassing is, op. 

4. Selecteer **Opslaan**.

Eenmaal ingeschakeld, wordt dit beleid niet automatisch toegepast op vm's in het huidige lab. Als u deze instelling wilt toepassen op een specifieke virtuele machine, opent u het beheervenster van de VM en wijzigt u de instelling **Automatisch starten.**

## <a name="set-expiration-date"></a>Vervaldatum instellen
U een vervaldatum instellen wanneer u [de VM maakt.](devtest-lab-add-vm.md) Kies **in Geavanceerde instellingen**het agendapictogram om een datum op te geven waarop de vm automatisch wordt verwijderd. Standaard verloopt de VM nooit.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
Zodra u de verschillende VM-beleidsinstellingen voor uw lab hebt gedefinieerd en toegepast, u het volgende proberen:

* [Begrijp gedeelde IP-adressen](devtest-lab-shared-ip.md) - Hiermee wordt uitgelegd hoe gedeelde IP-adressen worden gebruikt in DevTest Labs om het aantal openbare IP-adressen te minimaliseren dat nodig is om verbinding te maken met uw lab-VM's.
* [Kostenbeheer configureren](devtest-lab-configure-cost-management.md) - illustreert hoe u het grafiek **Trendvan maandelijkse geschatte kosten** gebruikt  
  om de geschatte kosten-to-date kosten van de huidige maand en de verwachte kosten aan het einde van de maand weer te geven.
* [Aangepaste afbeelding maken](devtest-lab-create-template.md) - Wanneer u een vm maakt, geeft u een basis op, die een aangepaste afbeelding of een Marketplace-afbeelding kan zijn. In dit artikel wordt uitgelegd hoe u een aangepaste afbeelding maakt op basis van een VHD-bestand.
* [Marketplace-afbeeldingen configureren](devtest-lab-configure-marketplace-images.md) - Azure DevTest Labs ondersteunt het maken van VM's op basis van Azure Marketplace-afbeeldingen. In dit artikel wordt uitgelegd hoe u opgeven welke, indien van toepassing, Azure Marketplace-afbeeldingen kunnen worden gebruikt bij het maken van VM's in een lab.
* [Maak een VM in een lab](devtest-lab-add-vm.md) - illustreert hoe u een VM maakt op basisafbeelding (aangepast of Marketplace) en hoe u werkt met artefacten in uw VM.

