---
title: Herstel na noodgevallen voor integratieaccounts
description: Uw integratieaccounts en B2B-artefacten instellen met herstel van nooddoor de regio in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 09b77862ad3379efeb8b3063a9d6c60b062ca2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905138"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Herstel van noodvoor noodgevallen in verschillende regio's instellen voor integratieaccounts in Azure Logic Apps

B2B-workloads omvatten geldtransacties zoals orders en facturen. Tijdens een rampgebeurtenis is het van cruciaal belang dat een bedrijf snel herstelt om te voldoen aan de SLA's op bedrijfsniveau die met hun partners zijn overeengekomen. In dit artikel wordt uitgelegd hoe u een bedrijfscontinuïteitsplan voor B2B-workloads opstellen. 

* Paraatheid voor noodherstel 
* Fail over naar secundaire regio tijdens een ramp 
* Terugvallen naar de primaire regio na een ramp

## <a name="disaster-recovery-readiness"></a>Paraatheid voor noodherstel  

1. Identificeer een secundaire regio en maak een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) aan in de secundaire regio.

2. Voeg partners, schema's en overeenkomsten toe voor de vereiste berichtstromen waarbij de runstatus moet worden gerepliceerd naar een secundair regio-integratieaccount.

   > [!TIP]
   > Zorg ervoor dat er consistentie is in de naamgevingsconventie van het integratieaccountartefact in verschillende regio's. 

3. Als u de runstatus uit het primaire gebied wilt halen, maakt u een logische app in het secundaire gebied. 

   Deze logische app moet een *trigger* en een *actie*hebben. 
   De trigger moet verbinding maken met het integratieaccount voor primaire regio's en de actie moet verbinding maken met het integratieaccount voor secundaire regio's. 
   Op basis van het tijdsinterval wordt in de trigger de statustabel primaire regio uitgevoerd en worden de eventuele nieuwe records opgevraagd. De actie wordt bijgewerkt naar het integratieaccount voor secundaire regio's. 
   Dit helpt om incrementele runtime status te krijgen van primaire regio naar secundaire regio.

4. Bedrijfscontinuïteit in het Logic Apps-integratieaccount is ontworpen om te ondersteunen op basis van B2B-protocollen - X12, AS2 en EDIFACT. Als u gedetailleerde stappen wilt vinden, selecteert u de desbetreffende koppelingen.

5. De aanbeveling is om ook alle primaire regiobronnen in een secundaire regio in te zetten. 

   Primaire regiobronnen omvatten Azure SQL Database of Azure Cosmos DB, Azure Service Bus en Azure Event Hubs die worden gebruikt voor messaging, Azure API Management en de Azure Logic Apps-functie in Azure App Service.   

6. Een verbinding tot stand brengen vanuit een primaire regio met een secundaire regio. Als u de runstatus uit een primaire regio wilt halen, maakt u een logische app in een secundaire regio. 

   De logische app moet een trigger en een actie hebben. 
   De trigger moet verbinding maken met een primaire regio-integratieaccount. 
   De actie moet verbinding maken met een integratieaccount in de secundaire regio. 
   Op basis van het tijdsinterval wordt in de trigger de statustabel primaire regio uitgevoerd en worden de eventuele nieuwe records opgevraagd. 
   De actie wordt bijgewerkt naar een integratieaccount in de secundaire regio. 
   Dit proces helpt om incrementele runtime-status te krijgen van de primaire regio naar het secundaire gebied.

Bedrijfscontinuïteit in een Logic Apps-integratieaccount biedt ondersteuning op basis van de B2B-protocollen X12, AS2 en EDIFACT. Zie [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) en AS2 in dit artikel voor gedetailleerde stappen over het gebruik van X12 en [AS2.](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Mislukken naar een secundaire regio tijdens een ramp

Tijdens een ramp, wanneer de primaire regio niet beschikbaar is voor bedrijfscontinuïteit, leidt u het verkeer naar de secundaire regio. Een secundaire regio helpt een bedrijf om functies snel te herstellen om te voldoen aan de RPO / RTO overeengekomen door hun partners. Het minimaliseert ook de inspanningen om van de ene regio naar de andere regio te mislukken. 

Er is een verwachte latentie tijdens het kopiëren van controlenummers van een primaire regio naar een secundaire regio. Om te voorkomen dat dubbele gegenereerde controlenummers naar partners worden verzonden tijdens een rampgebeurtenis, is de aanbeveling om de controlenummers in de secundaire regioovereenkomsten te verhogen met [PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Terugvallen naar een primaire regio na een ramp

Voer de volgende stappen uit om terug te vallen naar een primaire regio wanneer deze beschikbaar is:

1. Stop met het accepteren van berichten van partners in de secundaire regio.  

2. De gegenereerde controlenummers voor alle primaire regioovereenkomsten verhogen met [PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Direct verkeer van de secundaire regio naar de primaire regio.

4. Controleer of de logische app die in het secundaire gebied is gemaakt om de run-status uit het primaire gebied te halen, is ingeschakeld.

## <a name="x12"></a>X12 

Bedrijfscontinuïteit voor EDI X12-documenten is gebaseerd op controlenummers:

> [!TIP]
> U de [x12-sjabloon voor snel aan de start](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) ook gebruiken om logische apps te maken. Het maken van primaire en secundaire integratieaccounts zijn vereisten om de sjabloon te gebruiken. De sjabloon helpt bij het maken van twee logische apps, een voor ontvangen controlenummers en een voor gegenereerde controlenummers. De respectievelijke triggers en acties worden gemaakt in de logische apps, waarbij de trigger wordt verbonden met het primaire integratieaccount en de actie met het secundaire integratieaccount.

**Vereisten**

Als u herstel na noodgevallen wilt inschakelen voor binnenkomende berichten, selecteert u de dubbele controle-instellingen in de instellingen voor ontvangst van de X12-overeenkomst.

![Dubbele controle-instellingen selecteren](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Maak een [logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md) in een secundaire regio.    

2. Zoek op **X12**en selecteer **X12 - Wanneer een besturingselementnummer wordt gewijzigd**.   

   ![Zoeken naar X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   De trigger vraagt u om een verbinding met een integratieaccount aan te gaan. 
   De trigger moet worden gekoppeld aan een primaire regio-integratieaccount.

3. Voer een verbindingsnaam in, selecteer uw *primaire regio-integratieaccount* in de lijst en kies **Maken**.   

   ![Naam van het account primaire regio-integratie](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. De instelling **DateTime om de synchronisatie van het nummer te starten** is optioneel. De **frequentie** kan worden ingesteld op **Dag,** **Uur,** **Minuut**of **Tweede** met een interval.   

   ![Datumtijd en frequentie](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Selecteer **Nieuwe stap** > **Een actie toevoegen**.

   ![Nieuwe stap, Een actie toevoegen](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Zoek op **X12**en selecteer **X12 - Besturingselementnummers toevoegen of bijwerken**.   

   ![Besturingselementnummers toevoegen of bijwerken](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Als u een actie wilt koppelen aan een integratieaccount in een secundaire regio, selecteert u **Verbinding wijzigen** > **Nieuwe verbinding toevoegen** voor een lijst met beschikbare integratieaccounts. Voer een verbindingsnaam in, selecteer uw *secundaire regio-integratieaccount* in de lijst en kies **Maken**. 

   ![Naam van het secundaire regio-account](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Schakel over naar ruwe ingangen door rechtsboven op het pictogram te klikken.

   ![Overschakelen naar ruwe ingangen](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Selecteer Hoofdtekst in de dynamische inhoudskiezer en sla de logische app op.

   ![Dynamische inhoudsvelden](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Op basis van het tijdsinterval wordt in de trigger de tabel primaire regio ontvangen controlenummer tabel en trekt de nieuwe records. 
   Met de actie worden de records in het integratieaccount secundaire regio bijgewerkt. 
   Als er geen updates zijn, wordt de triggerstatus weergegeven als **Overgeslagen**.   

   ![Tabel met het besturingselementnummer](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Op basis van het tijdsinterval wordt de incrementele runtime-status gerepliceerd van een primaire regio naar een secundair gebied. Tijdens een ramp, wanneer de primaire regio niet beschikbaar is, leidt u het verkeer naar de secundaire regio voor bedrijfscontinuïteit. 

## <a name="edifact"></a>EDIFACT 

Bedrijfscontinuïteit voor EDI EDIFACT-documenten is gebaseerd op controlenummers.

**Vereisten**

Als u herstel na noodgevallen wilt inschakelen voor binnenkomende berichten, selecteert u de dubbele controle-instellingen in de instellingen voor ontvangst van uw EDIFACT-overeenkomst.

![Dubbele controle-instellingen selecteren](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Maak een [logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md) in een secundaire regio.    

2. Zoeken op **EDIFACT**en selecteer **EDIFACT - Wanneer een besturingselementnummer wordt gewijzigd**.

   ![Zoeken naar EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   De trigger vraagt u om een verbinding met een integratieaccount aan te gaan. 
   De trigger moet worden gekoppeld aan een primaire regio-integratieaccount. 

3. Voer een verbindingsnaam in, selecteer uw *primaire regio-integratieaccount* in de lijst en kies **Maken**.    

   ![Naam van het account primaire regio-integratie](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. De instelling **DateTime om de synchronisatie van het nummer te starten** is optioneel. De **frequentie** kan worden ingesteld op **Dag,** **Uur,** **Minuut**of **Tweede** met een interval.    

   ![Datumtijd en frequentie](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Selecteer **Nieuwe stap** > **Een actie toevoegen**.    

   ![Nieuwe stap, Een actie toevoegen](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Zoeken op **EDIFACT**en selecteer **EDIFACT - Besturingselementnummers toevoegen of bijwerken**.   

   ![Besturingselementnummers toevoegen of bijwerken](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Als u een actie wilt koppelen aan een integratieaccount in een secundaire regio, selecteert u **Verbinding wijzigen** > **Nieuwe verbinding toevoegen** voor een lijst met beschikbare integratieaccounts. Voer een verbindingsnaam in, selecteer uw *secundaire regio-integratieaccount* in de lijst en kies **Maken**.

   ![Naam van het secundaire regio-account](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Schakel over naar ruwe ingangen door rechtsboven op het pictogram te klikken.

   ![Overschakelen naar ruwe ingangen](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Selecteer Hoofdtekst in de dynamische inhoudskiezer en sla de logische app op.   

   ![Dynamische inhoudsvelden](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Op basis van het tijdsinterval wordt in de trigger de tabel primaire regio ontvangen controlenummer tabel en trekt de nieuwe records.
   Met de actie worden de records bijgewerkt naar het integratieaccount voor secundaire regio's. 
   Als er geen updates zijn, wordt de triggerstatus weergegeven als **Overgeslagen**.

   ![Tabel met het besturingselementnummer](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Op basis van het tijdsinterval wordt de incrementele runtime-status gerepliceerd van een primaire regio naar een secundair gebied. Tijdens een ramp, wanneer de primaire regio niet beschikbaar is, leidt u het verkeer naar de secundaire regio voor bedrijfscontinuïteit. 

## <a name="as2"></a>AS2 

Bedrijfscontinuïteit voor documenten die het AS2-protocol gebruiken, is gebaseerd op de bericht-id en de MIC-waarde.

> [!TIP]
> U de [AS2-sjabloon voor snel aan de start](https://github.com/Azure/azure-quickstart-templates/pull/3302) ook gebruiken om logische apps te maken. Het maken van primaire en secundaire integratieaccounts zijn vereisten om de sjabloon te gebruiken. De sjabloon helpt bij het maken van een logische app met een trigger en een actie. De logische app maakt een verbinding van een trigger naar een primaire integratieaccount en een actie naar een secundair integratieaccount.

1. Maak een [logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md) in het secundaire gebied.  

2. Zoek op **AS2**en selecteer **AS2 - Wanneer een MIC-waarde wordt gemaakt.**   

   ![Zoeken naar AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Een trigger vraagt u om een verbinding met een integratieaccount aan te gaan. 
   De trigger moet worden gekoppeld aan een primaire regio-integratieaccount. 
   
3. Voer een verbindingsnaam in, selecteer uw *primaire regio-integratieaccount* in de lijst en kies **Maken**.

   ![Naam van het account primaire regio-integratie](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. De instelling **voor het synchroniseren van de MIC-waarde** datum is optioneel. De **frequentie** kan worden ingesteld op **Dag,** **Uur,** **Minuut**of **Tweede** met een interval.   

   ![Datumtijd en frequentie](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Selecteer **Nieuwe stap** > **Een actie toevoegen**.  

   ![Nieuwe stap, Een actie toevoegen](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Zoek op **AS2**en selecteer **AS2 - MIC-inhoud toevoegen of bijwerken**.  

   ![MIC-toevoeging of -update](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Als u een actie wilt koppelen aan een secundair integratieaccount, selecteert u **Verbinding wijzigen** > **Nieuwe verbinding toevoegen** voor een lijst met beschikbare integratieaccounts. Voer een verbindingsnaam in, selecteer uw *secundaire regio-integratieaccount* in de lijst en kies **Maken**.

   ![Naam van het secundaire regio-account](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Schakel over naar ruwe ingangen door rechtsboven op het pictogram te klikken.

   ![Overschakelen naar ruwe ingangen](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Selecteer Hoofdtekst in de dynamische inhoudskiezer en sla de logische app op.   

   ![Dynamische inhoud](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Op basis van het tijdsinterval peilt de trigger de tabel primaire regio en haalt de nieuwe records. De actie wordt bijgewerkt naar het integratieaccount voor secundaire regio's. 
   Als er geen updates zijn, wordt de triggerstatus weergegeven als **Overgeslagen**.  

   ![Tabel Primaire regio](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Op basis van het tijdsinterval wordt de incrementele runtime-status gerepliceerd van het primaire gebied naar het secundaire gebied. Tijdens een ramp, wanneer de primaire regio niet beschikbaar is, leidt u het verkeer naar de secundaire regio voor bedrijfscontinuïteit. 

## <a name="next-steps"></a>Volgende stappen

[B2B-berichten bewaken met Azure Monitor-logboeken](../logic-apps/monitor-b2b-messages-log-analytics.md)

