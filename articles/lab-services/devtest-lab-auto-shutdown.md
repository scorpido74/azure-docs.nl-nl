---
title: Beleid voor automatisch afsluiten beheren in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het instellen van het autoafsluitbeleid voor een lab, zodat virtuele machines automatisch worden afgesloten wanneer ze niet worden gebruikt.
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
ms.date: 01/17/2020
ms.author: spelluru
ms.openlocfilehash: a2d0b9bdfba1b96ad42e45d54faf106b2361e29d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264781"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Autoshutdown configureren voor lab- en compute-virtuele machines in Azure DevTest Labs

In dit artikel wordt uitgelegd hoe u instellingen voor automatische afsluiting configureert voor laboratoriumVM's in DevTest Labs en VM's berekenen. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Automatische uitschakeling configureren voor laboratoriumVM's (DevTest Labs)
Azure DevTest Labs stelt u in staat om kosten te beheersen en afval in uw labs te minimaliseren door beleid (instellingen) voor elk lab te beheren. In dit artikel ziet u hoe u het beleid voor automatische afsluiting voor een labaccount configureert en instellingen voor automatische afsluiting configureert voor een lab in het labaccount. Zie [Labbeleid definiëren in Azure DevTest Labs](devtest-lab-set-lab-policy.md)als u wilt zien hoe u elk labbeleid instelt.  

### <a name="set-auto-shut-down-policy-for-a-lab"></a>Automatisch afsluiten-beleid instellen voor een lab
Als eigenaar van een lab u een afsluitschema configureren voor alle VM's in uw lab. Door dit te doen, u kosten besparen door machines die niet worden gebruikt (idle). U een afsluitbeleid voor al uw lab-VM's centraal afdwingen, maar ook uw labgebruikers de moeite besparen om een schema voor hun afzonderlijke machines in te stellen. Met deze functie u het beleid op uw labschema instellen, vanaf het bieden van geen controle tot volledige controle, voor uw labgebruikers. Als eigenaar van een lab u dit beleid configureren door de volgende stappen te nemen:

1. Selecteer op de startpagina van uw lab de optie **Configuratie en beleid**.
2. Selecteer **Beleid voor automatisch afsluiten** in het gedeelte **Schema's** van het linkermenu.
3. Selecteer een van de opties. In de volgende secties vindt u meer informatie over deze opties: Het ingestelde beleid is alleen van toepassing op nieuwe VM's die in het lab zijn gemaakt en niet op de reeds bestaande VM's. 

    ![Beleidsopties automatisch afsluiten](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>Instellingen voor automatisch afsluiten configureren
Het autoshutdown-beleid helpt om labverspilling te minimaliseren doordat u de tijd opgeven waarop de VM's van dit lab worden afgesloten.

Voer de volgende stappen uit om het beleid voor een lab weer te geven (en te wijzigen):

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met laboratoria het gewenste lab.   
4. Selecteer **Configuratie en beleid**.

    ![Deelvenster Beleidsinstellingen](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. Selecteer in het **deelvenster Configuratie en beleid** van het lab de optie Automatisch **afsluiten** onder **Schema's**.
   
    ![Automatisch afsluiten](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Selecteer **Aan** om dit beleid in te schakelen en **Schakel het** uit.
7. Als u dit beleid inschakelt, geeft u de tijd (en tijdzone) op om alle VM's in het huidige lab af te sluiten.
8. Geef **Ja** of **Nee** op voor de optie om een melding 30 minuten voor de opgegeven automatische afsluittijd te verzenden. Als u **Ja**kiest, voert u een webhook URL-eindpunt of e-mailadres in waarin wordt aangegeven waar u de melding wilt plaatsen of verzenden. De gebruiker ontvangt een melding en krijgt de mogelijkheid om de shutdown uit te stellen. Zie de sectie [Meldingen](#notifications) voor meer informatie. 
9. Selecteer **Opslaan**.

    Dit beleid is standaard van toepassing op alle VM's in het huidige lab, eenmaal ingeschakeld. Als u deze instelling uit een specifieke virtuele machine wilt verwijderen, opent u het beheervenster van de virtuele machine en wijzigt u de instelling **Automatisch afsluiten.**
    
> [!NOTE]
> Als u het autoshutdown-schema voor uw lab of een specifieke virtuele laboratoriummachine binnen 30 minuten na de huidige geplande tijd bijwerkt, is de bijgewerkte afsluittijd van toepassing op het schema van de volgende dag. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Gebruiker stelt een planning in en kan zich afmelden
Als u uw lab op dit beleid instelt, kunnen de labgebruikers het labschema overschrijven of afmelden. Deze optie geeft labgebruikers volledige controle over het automatische afsluitschema van hun VM's. Labgebruikers zien geen wijzigingen in hun pagina met automatische afsluitingsschema's voor vm's.

![Beleidsoptie Automatisch afsluiten - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Gebruiker stelt een planning in en kan zich niet afmelden
Als u uw lab op dit beleid instelt, kunnen labgebruikers het labschema overschrijven. Ze kunnen zich echter niet afmelden voor het beleid voor automatische afsluiting. Deze optie zorgt ervoor dat elke machine in uw lab onder een automatisch afsluitschema valt. Lab-gebruikers kunnen het automatische afsluitschema van hun VM's bijwerken en meldingen voor afsluiten instellen.

![Beleidsoptie Automatisch afsluiten - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Gebruiker heeft geen controle over het schema dat is ingesteld door de labbeheerder
Als u uw lab op dit beleid instelt, kunnen labgebruikers het labschema niet overschrijven of afmelden. Deze optie biedt lab admin de volledige controle over het schema voor elke machine in het lab. Lab-gebruikers kunnen alleen automatische afsluitingsmeldingen instellen voor hun VM's.

![Beleidsoptie Automatisch afsluiten - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>Meldingen
Zodra autoshutdown ingesteld door de eigenaar van het lab, meldingen zullen worden verzonden naar het lab gebruikers 30 minuten voordat de autoshutdown geactiveerd als een van hun VM's zal worden beïnvloed. Deze optie geeft labgebruikers de kans om hun werk op te slaan voor de shutdown. De melding bevat ook koppelingen voor elke VM voor de volgende acties:

- De automatische afsluiting voor deze tijd overslaan
- Snooze de autoshutdown voor een uur of 2 uur, zodat ze kunnen blijven werken aan de VM.

De melding wordt verzonden via het geconfigureerde eindpunt voor webhaaks of een e-mailadres dat door labeigenaren is opgegeven in de instellingen voor automatisch afsluiten. Met webhooks u integraties bouwen of instellen die zich abonneren op bepaalde gebeurtenissen. Wanneer een van deze gebeurtenissen wordt geactiveerd, stuurt DevTest Labs een HTTP POST-payload naar de geconfigureerde URL van de webhook. Zie [Een webhook- of API Azure-functie maken](../azure-functions/functions-create-a-web-hook-or-api-function.md)voor meer informatie over webhooks. 

We raden u aan webhaken te gebruiken omdat ze uitgebreid worden ondersteund door verschillende apps (bijvoorbeeld Slack, Azure Logic Apps, enzovoort.) en u in staat stelt uw eigen manier te implementeren voor het verzenden van meldingen. In dit artikel u bijvoorbeeld met dit artikel doorlopen hoe u meldingen van automatische afsluiting van e-mails ontvangen met Azure Logic Apps. Laten we eerst snel de basisstappen doorlopen om meldingen voor automatisch afsluiten in uw lab in te schakelen.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Een logische app maken die e-mailmeldingen ontvangt
[Azure Logic Apps](../logic-apps/logic-apps-overview.md) biedt veel kant-en-klare connectors waarmee u eenvoudig een service integreren met andere clients, zoals Office 365 en Twitter. Op hoog niveau kunnen de stappen voor het instellen van een Logic App voor e-mailmelding in vier fasen worden onderverdeeld: 

- Maak een logische app. 
- Configureer de ingebouwde sjabloon.
- Integreren met uw e-mailclient
- Download de URL van Webhook.

### <a name="create-a-logic-app"></a>Een logische app maken
Maak om aan de slag te gaan een logische app in uw Azure-abonnement met de volgende stappen:

1. Selecteer **+ Een resource maken** in het linkermenu, selecteer **Integratie**en selecteer Logische **App**. 

    ![Menu Nieuwe logica-app](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Voer op de pagina **Logica-app - Maken** de volgende stappen uit: 
    1. Voer een **naam** in voor de logische app.
    2. Selecteer uw **Azure-abonnement**.
    3. Maak een nieuwe **resourcegroep** of selecteer een bestaande resourcegroep. 
    4. Selecteer een **locatie** voor de logische app. 

        ![Nieuwe logische app - instellingen](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Selecteer **In de melding gaan** **naar bron** in de melding. 

    ![Ga naar resource](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Selecteer **Logic-appdesigner** onder de categorie **Implementatiehulpprogramma's.**

    ![HTTP-aanvraag/-antwoord selecteren](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Selecteer **deze sjabloon gebruiken**op de pagina HTTP **Request-Response** . 

    ![De optie Deze sjabloon gebruiken selecteren](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Kopieer de volgende JSON naar de sectie **Json-schema van aanvraagbody:** 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![Aanvraag Body JSON Schema](./media/devtest-lab-auto-shutdown/request-json.png)
7. Selecteer **+ Nieuwe stap** in de ontwerper en volg de volgende stappen:
    1. Zoeken naar **Office 365 Outlook - Stuur een e-mail**. 
    2. Selecteer **Een e-mail verzenden** vanuit **Acties**. 
    
        ![E-mail optie verzenden](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Selecteer **Aanmelden** om u aan te melden bij uw e-mailaccount. 
    4. Selecteer het veld **AAN** en kies eigenaar.
    5. Selecteer **ONDERWERP**en voer een onderwerp van de e-mailmelding in. Bijvoorbeeld: "Shutdown van machine vmName for Lab: labName."
    6. Selecteer **BODY**en definieer de inhoud van de hoofdtekst voor e-mailmelding. Bijvoorbeeld: "vmName is gepland om af te sluiten in 15 minuten. Sla deze afsluiting over door op: URL te klikken. Vertraging afsluiten voor een uur: vertragingUrl60. Shutdown van 2 uur uitgesteld: vertragingUrl120."

        ![Aanvraag Body JSON Schema](./media/devtest-lab-auto-shutdown/email-options.png)
1. Selecteer **Opslaan** op de werkbalk. Nu u de **HTTP-POST-URL**kopiëren. Selecteer de knop Kopiëren om de URL naar het klembord te kopiëren. 

    ![WebHook-URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Automatisch afsluiten configureren voor compute VM's

1. Selecteer **op** de pagina Virtuele machine de optie **Automatisch afsluiten** in het linkermenu. 
2. Selecteer op de pagina **Automatisch afsluiten** de optie **Aan** om dit beleid in te schakelen en **Schakel het** uit om het uit te schakelen.
3. Als u dit beleid inschakelt, geeft u de **tijd** (en **tijdzone)** op waarop de vm moet worden afgesloten.
4. Geef **Ja** of **Nee** op voor de optie om een melding 30 minuten voor de opgegeven automatische afsluittijd te verzenden. Als u **Ja**kiest, voert u een webhook URL-eindpunt of e-mailadres in waarin wordt aangegeven waar u de melding wilt plaatsen of verzenden. De gebruiker ontvangt een melding en krijgt de mogelijkheid om de shutdown uit te stellen. Zie de sectie [Meldingen](#notifications) voor meer informatie. 
9. Selecteer **Opslaan**.

    ![Automatische afsluiting configureren voor een compute-vm](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

## <a name="next-steps"></a>Volgende stappen
Zie [Labbeleid definiëren in Azure DevTest Labs](devtest-lab-set-lab-policy.md)voor meer informatie over het instellen van alle beleidsregels.

