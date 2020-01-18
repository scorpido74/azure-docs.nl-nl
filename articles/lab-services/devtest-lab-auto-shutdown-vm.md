---
title: Instellingen voor automatisch afsluiten configureren voor een virtuele machine in Azure DevTest Labs
description: Meer informatie over het configureren van instellingen voor automatisch afsluiten voor een virtuele machine (VM), zodat de VM wordt afgesloten wanneer deze niet wordt gebruikt.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 4cca4fd4bc9cd880c5b7a75e54e8cfd8192bae1e
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170195"
---
# <a name="configure-autoshutdown-settings-for-a-vm-in-azure-devtest-labs"></a>Instellingen voor automatisch afsluiten configureren voor een virtuele machine in Azure DevTest Labs
Met Azure DevTest Labs kunt u kosten besparen en verspiling in uw Labs minimaliseren door beleid (instellingen) voor elk lab te beheren. In dit artikel leest u hoe u het beleid voor automatisch afsluiten configureert voor een Lab-account en instellingen voor automatisch afsluiten configureert voor een lab in het lab-account. Zie [Lab-beleid in azure DevTest Labs definiëren](devtest-lab-set-lab-policy.md)voor meer informatie over het instellen van elk lab-beleid.  

## <a name="autoshutdown-policy-for-a-lab"></a>Beleid voor automatisch afsluiten van een Lab
Als eigenaar van het lab kunt u een afsluit schema configureren voor alle virtuele machines in uw Lab. Op die manier kunt u kosten besparen van het uitvoeren van machines die niet worden gebruikt (niet-actief). U kunt een afsluit beleid afdwingen op al uw virtuele lab-Vm's, maar ook uw Lab-gebruikers de moeite hebben om een planning in te stellen voor de afzonderlijke machines. Met deze functie kunt u het beleid op uw test schema instellen, te beginnen met het beheer van volledig beheer aan uw Lab-gebruikers. Als eigenaar van het lab kunt u dit beleid configureren door de volgende stappen uit te voeren:

1. Op de start pagina van uw Lab selecteert u **configuratie en beleid**.
2. Selecteer **beleid voor automatisch afsluiten** in de sectie **schema's** van het menu links.
3. Selecteer een van de opties. In de volgende secties vindt u meer informatie over deze opties: het set-beleid is alleen van toepassing op nieuwe virtuele machines die zijn gemaakt in het lab en niet op de bestaande virtuele machines. 

    ![Opties voor automatisch afsluiten van beleid](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-autoshutdown-settings-for-a-lab"></a>Instellingen voor automatisch afsluiten configureren voor een Lab
Het beleid voor automatisch afsluiten helpt Lab-afval te minimaliseren door de tijd op te geven waarop de Vm's van deze Lab worden afgesloten.

Voer de volgende stappen uit om de beleids regels voor een Lab weer te geven (en te wijzigen):

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Selecteer **alle services**en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs het gewenste Lab.   
4. Selecteer **configuratie en beleid**.

    ![Deel venster beleids instellingen](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. Selecteer in het deel venster **configuratie en beleid** van de test **automatisch afsluiten** onder **planningen**.
   
    ![Automatisch afsluiten](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Selecteer aan om dit beleid **in te scha** kelen en **uit** te scha kelen.
7. Als u dit beleid inschakelt, geeft u de tijd (en tijd zone) op om alle virtuele machines in het huidige Lab af te sluiten.
8. Geef **Ja** of **Nee** op voor de optie voor het verzenden van een melding 15 minuten vóór de opgegeven tijd voor automatisch afsluiten. Als u **Ja**kiest, voert u het eind punt van de webhook-URL of het e-mail adres in waarmee u wilt dat de melding wordt gepost of verzonden. De gebruiker ontvangt een melding en krijgt de mogelijkheid om het afsluiten te vertragen. Zie de sectie [meldingen](#notifications) voor meer informatie. 
9. Selecteer **Opslaan**.

    Wanneer dit beleid is ingeschakeld, wordt standaard toegepast op alle virtuele machines in het huidige lab. Als u deze instelling van een specifieke virtuele machine wilt verwijderen, opent u het deel venster beheer van de virtuele machine en wijzigt u de instelling voor automatisch **Afsluiten** .

## <a name="configure-autoshutdown-settings-for-a-vm"></a>Instellingen voor automatisch afsluiten configureren voor een virtuele machine

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Gebruiker stelt een schema in en kan zich afmelden
Als deze beleids optie is ingesteld voor het lab, kunnen gebruikers het lab-schema overschrijven of afmelden. Met deze optie krijgen gebruikers van het lab volledig beheer over het automatische afsluit schema van hun Vm's. Lab-gebruikers zien geen wijziging op de pagina planning voor automatisch afsluiten van de VM.

![Optie voor automatisch afsluiten van beleid-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Gebruiker stelt een schema in en kan niet afmelden
Als deze beleids optie is ingesteld voor het lab, kunnen gebruikers het lab-schema overschrijven. Het beleid voor automatisch afsluiten kan echter niet worden afgemeld. Deze optie zorgt ervoor dat elke machine in uw Lab een schema voor automatisch afsluiten heeft. Gebruikers met een Lab kunnen het schema voor automatisch afsluiten van hun Vm's bijwerken en meldingen voor afsluiten instellen.

![Optie voor automatisch afsluiten van beleid-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>De gebruiker heeft geen controle over de planning die is ingesteld door de Lab-beheerder
Als deze beleids optie is ingesteld voor het lab, kunnen gebruikers het lab-schema niet overschrijven of afmelden. Deze optie biedt Lab-beheerder de volledige controle over de planning voor elke machine in het lab. Gebruikers met een Lab kunnen alleen meldingen voor automatisch afsluiten instellen voor hun virtuele machines.

![Optie voor automatisch afsluiten van beleid-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Meldingen
Zodra automatisch afsluiten is ingesteld door de eigenaar van het lab, worden er 15 minuten meldingen verzonden naar de Lab-gebruikers voordat de automatische afsluiting wordt geactiveerd als een van de Vm's wordt beïnvloed. Met deze optie kunnen gebruikers van het lab hun werk opslaan voordat ze worden afgesloten. De melding bevat ook koppelingen voor elke VM voor de volgende acties:

- Automatisch afsluiten voor deze tijd overs Laan
- Het automatisch afsluiten gedurende een uur of 2 uur uitstellen, zodat ze op de VM kunnen blijven werken.

Er wordt een melding verzonden via het geconfigureerde webhook-eind punt of een e-mail adres dat is opgegeven door de Lab-eigen aren in de instellingen voor automatisch afsluiten. Met webhooks kunt u integraties maken of instellen voor het abonneren op bepaalde gebeurtenissen. Wanneer een van deze gebeurtenissen wordt geactiveerd, stuurt DevTest Labs een HTTP POST-Payload naar de geconfigureerde URL van de webhook. Zie [een webhook of API Azure function maken](../azure-functions/functions-create-a-web-hook-or-api-function.md)voor meer informatie over webhooks. 

We raden u aan om webhooks te gebruiken, omdat deze uitgebreid worden ondersteund door verschillende apps (bijvoorbeeld toegestane vertraging, Azure Logic Apps enzovoort) en u uw eigen manier voor het verzenden van meldingen kunt implementeren. Zie[een logische app maken die e-mail meldingen ontvangt](devtest-lab-auto-shutdown.md#create-a-logic-app-that-receives-email-notifications)voor een voor beeld van het ontvangen van een melding over automatisch afsluiten van e-mail berichten met behulp van Azure Logic apps. 


## <a name="next-steps"></a>Volgende stappen
Zie [beleid voor automatisch afsluiten beheren voor een lab in azure DevTest Labs](devtest-lab-auto-shutdown.md)
