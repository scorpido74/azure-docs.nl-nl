---
title: Geavanceerde gegevens beveiliging voor IaaS in Azure Security Center | Microsoft Docs
description: " Meer informatie over het inschakelen van geavanceerde gegevens beveiliging voor IaaS in Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: 287da68617a9527bc398df577cf8d10773fa8557
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202172"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Geavanceerde gegevens beveiliging voor SQL-servers in azure Virtual Machines (open bare preview)
Geavanceerde gegevens beveiliging voor SQL-servers in azure Virtual Machines is een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Het is momenteel (in open bare preview) de functionaliteit voor halen en het beperken van mogelijke beveiligings problemen voor een Data Base en het detecteren van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw data base. 

Deze beveiligings aanbieding voor Azure Vm's SQL-servers is gebaseerd op dezelfde fundamentele technologie die wordt gebruikt in het [Azure SQL database geavanceerde gegevens beveiligings pakket](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Overzicht

Geavanceerde gegevens beveiliging biedt een aantal geavanceerde SQL-beveiligings mogelijkheden, bestaande uit evaluatie van beveiligings problemen en geavanceerde beveiliging tegen bedreigingen.

* [Evaluatie van beveiligings problemen](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) is een eenvoudig te configureren service waarmee u mogelijke beveiligings problemen met een Data Base kunt detecteren, bijhouden en helpen oplossen. Het biedt inzicht in de beveiligings status en bevat de stappen voor het oplossen van beveiligings problemen en het uitbreiden van uw data base-Fortifications.
* [Geavanceerde bedreigingen beveiliging](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot uw SQL-Server of deze misbruiken. Het controleert uw data base voortdurend op verdachte activiteiten en biedt actie gerichte beveiligings waarschuwingen voor afwijkende database toegangs patronen. Deze waarschuwingen bieden informatie over verdachte activiteiten en aanbevolen acties voor het onderzoeken en oplossen van de dreiging.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Aan de slag met geavanceerde gegevens beveiliging voor SQL op virtuele machines van Azure

Met de volgende stappen kunt u aan de slag met geavanceerde gegevens beveiliging voor SQL op de open bare preview van Azure Vm's.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Geavanceerde gegevens beveiliging instellen voor SQL op virtuele Azure-machines

**Voordat u begint**: U hebt een Log Analytics-werk ruimte nodig om de geanalyseerde beveiligings logboeken op te slaan. Als u er nog geen hebt, kunt u er een eenvoudig maken, zoals wordt uitgelegd in [een log Analytics-werk ruimte maken in de Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Verbind de virtuele machine die als host fungeert voor de SQL Server met de Log Analytics-werk ruimte. Zie [Windows-computers verbinden met Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)voor instructies.

1. Ga vanuit Azure Marketplace naar de [beveiligings oplossing voor geavanceerde SQL-gegevens](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(U kunt het vinden met behulp van de zoek optie voor Marketplace, zoals in de volgende afbeelding wordt weer geven.) De pagina **geavanceerde SQL-gegevens beveiliging** wordt geopend.

    ![Geavanceerde gegevens beveiliging voor IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Klik op **Create**. De werk plekken worden weer gegeven.

    ![Geavanceerde gegevens beveiliging maken](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Selecteer de werk ruimte die u wilt gebruiken en klik op **maken**.

   ![Werkruimte selecteren](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Start de [SQL Server van de virtuele machine](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017)opnieuw op.


## <a name="explore-and-investigate-security-alerts"></a>Beveiligings waarschuwingen verkennen en onderzoeken

U kunt uw huidige beveiligings waarschuwingen weer geven en beheren.

1. Klik op **Security Center** > **beveiligings waarschuwingen**en klik op een waarschuwing.

    ![Waarschuwing zoeken](./media/security-center-advanced-iaas-data/find-alert.png)

1. Klik in de kolom aangevallen **bron** op een resource die is aangevallen.

1. Als u waarschuwings Details en acties wilt weer geven voor het onderzoeken van de huidige dreiging en het oplossen van toekomstige dreigingen, schuift u omlaag op de pagina **algemene informatie** en klikt u in de sectie **herstel stappen** op de koppeling naar de **stappen voor onderzoek** .

    ![Herstelstappen](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Als u de logboeken wilt weer geven die zijn gekoppeld aan het activeren van de waarschuwing, gaat u naar **log Analytics-werk ruimten** en voert u de volgende stappen uit:

     > [!NOTE]
     > Als **log Analytics-werk ruimten** niet worden weer gegeven in het linkermenu, klikt u op **alle services**en zoekt u naar **log Analytics-werk ruimten**.

    1. Zorg ervoor dat de kolommen de **prijs categorie** en **WorkspaceID** kolommen weer geven. (**Log Analytics-werk ruimten** > **bewerken kolommen**, voegt **prijs categorie** toe en **WorkspaceID**.)

     ![Kolommen bewerken](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Klik op de werk ruimte met de waarschuwings Logboeken.

    1. Klik in het menu **Algemeen** op **Logboeken**

    1. Klik op het oog naast de tabel **SQLAdvancedThreatProtection** . De logboeken worden weer gegeven.

     ![Logboeken weergeven](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>E-mail meldingen voor ATP-waarschuwingen instellen 

U kunt een lijst met ontvangers instellen om een e-mail melding te ontvangen wanneer er ASC-waarschuwingen worden gegenereerd. Het e-mail bericht bevat een directe koppeling naar de waarschuwing in Azure Security Center met alle relevante informatie. 

1. Ga naar **Security Center** > **prijzen & instellingen** en klik op het betreffende abonnement

    ![Abonnements instellingen](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Klik in het menu **instellingen** op **e-mail meldingen**. 
1. Voer in het tekstvak **e-mail adres** de e-mail adressen in om de meldingen te ontvangen. U kunt meer dan één e-mail adres invoeren door de e-mail adressen te scheiden door een komma (,).  Bijvoorbeeld admin1@mycompany.com,admin2@mycompany.comadmin3@mycompany.com

      ![E-mailinstellingen](./media/security-center-advanced-iaas-data/email-settings.png)

1. Stel in de instellingen voor **e-mail meldingen** de volgende opties in:
  
    * **E-mail melding verzenden voor waarschuwingen met hoge urgentie**: In plaats van e-mail berichten verzenden voor alle waarschuwingen, verzendt u alleen voor waarschuwingen met hoge urgentie.
    * **Ook e-mail meldingen verzenden naar abonnements eigenaren**:  Verzend meldingen naar de eigen aars van de abonnementen.

1. Klik boven in het scherm met **e-mail meldingen** op **Opslaan**.

  > [!NOTE]
  > Zorg ervoor dat u op **Opslaan** klikt voordat u het venster sluit of de nieuwe instellingen voor **e-mail meldingen** worden niet opgeslagen.

## <a name="explore-vulnerability-assessment-reports"></a>Rapporten van evaluatie van beveiligings problemen verkennen

Het dash board evaluatie van beveiligings problemen biedt een overzicht van de resultaten van de evaluatie in al uw data bases. U kunt de distributie van data bases weer geven op basis van SQL Server versie, samen met een samen vatting van fouten versus het door geven van data bases en een algehele samen vatting van mislukte controles volgens risico distributie.

U kunt de resultaten van de evaluatie van beveiligings problemen en rapporten rechtstreeks van Log Analytics weer geven.

1. Navigeer naar uw Log Analytics-werk ruimte met de geavanceerde gegevens beveiligings oplossing.
1. Navigeer naar **oplossingen** en selecteer de oplossing **SQL-evaluatie van beveiligings problemen** .
1. Klik in het deel venster **samen** vatting op **samen vatting weer geven** en selecteer het **rapport evaluatie van SQL-beveiligings problemen**.

    ![SQL-evaluatie rapport](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Het rapport dashboard wordt geladen. Zorg ervoor dat het tijd venster is ingesteld op ten minste de **laatste 7 dagen** sinds scans voor de evaluatie van beveiligings problemen worden uitgevoerd op uw data bases in een vast schema van één keer per 7 dagen.

    ![De afgelopen 7 dagen instellen](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Als u meer details wilt bekijken, klikt u op een van de dashboard items. Bijvoorbeeld:

   1. Klik op een beveiligings probleem in de sectie **samen vatting van mislukte controles** om een log Analytics tabel met de resultaten voor deze controle in alle data bases weer te geven. De resultaten worden als eerste weer gegeven.

   1. Klik vervolgens op door om de Details voor elk beveiligings probleem te bekijken, met inbegrip van de beschrijving van het beveiligingslek en de impact, status, het bijbehorende risico en de werkelijke resultaten van deze data base. U kunt ook de daad werkelijke query zien die is uitgevoerd om deze controle uit te voeren en informatie over herstel voor het oplossen van dit beveiligings probleem.

    ![Werkruimte selecteren](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Werkruimte selecteren](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. U kunt alle Log Analytics query's uitvoeren op de resultaten van de evaluatie van beveiligings problemen, om de gegevens te segmenteren en te dobbelten op basis van uw behoeften.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Geavanceerde bedreigings beveiliging voor SQL-servers op Azure Vm's-waarschuwingen
Waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om SQL-servers te openen of misbruik te maken. Deze gebeurtenissen kunnen de volgende waarschuwingen activeren:

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>Waarschuwingen over afwijkende toegangs patronen (ondersteund in open bare preview)

* **Toegang vanaf ongebruikelijke locatie:** Deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor SQL Server, waarbij iemand zich vanaf een ongebruikelijke geografische locatie heeft aangemeld bij de SQL-Server. Mogelijke oorzaken:
     * Een aanvaller of voormalig kwaad aardig gebruik heeft toegang verkregen tot uw SQL Server.
     * Een rechtmatige gebruiker heeft uw SQL Server vanaf een nieuwe locatie geopend.
* **Toegang via een mogelijk schadelijke toepassing**: zijn waarschuwing wordt geactiveerd wanneer een mogelijk schadelijke toepassing wordt gebruikt voor toegang tot de data base. Mogelijke oorzaken:
     * Een aanvaller probeert uw SQL te schenden met behulp van gang bare hulpprogram ma's voor aanvallen.
     * Een rechtmatige indringings test in actie.
* **Toegang vanaf onbekende Principal**: Deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor SQL Server, waarbij iemand zich heeft aangemeld bij de SQL-Server met behulp van een ongebruikelijke principal (SQL-gebruiker). Mogelijke oorzaken:
     * Een aanvaller of voormalig kwaad aardig gebruik heeft toegang verkregen tot uw SQL Server. 
     * Een rechtmatige gebruiker heeft uw SQL Server geopend vanuit een nieuwe principal.
* **SQL-referenties voor brute force**: Deze waarschuwing wordt geactiveerd wanneer er sprake is van een abnormaal groot aantal mislukte aanmeldingen met andere referenties. Mogelijke oorzaken:
     * Een aanvaller probeert uw SQL te schenden met behulp van een brute kracht.
     * Een rechtmatige indringings test in actie.

### <a name="potential-sql-injection-attacks-coming"></a>Potentiële SQL-injectie aanvallen (beschikbaar)

* **Beveiligings probleem met SQL-injectie**: Deze waarschuwing wordt geactiveerd wanneer een toepassing een mislukte SQL-instructie in de data base genereert. Deze waarschuwing kan duiden op een mogelijk beveiligingsprobleem met SQL-injectieaanvallen. Mogelijke oorzaken:
     * Een fout in de toepassingscode die de foutieve SQL-instructie maakt
     * Toepassingscode of opgeslagen procedures schonen gebruikersinvoer niet op tijdens het construeren van de foutieve SQL-instructie, dit kan worden misbruikt voor SQL-injectie
* **Mogelijke SQL-injectie**: Deze waarschuwing wordt geactiveerd wanneer een actieve crack optreedt voor een geïdentificeerde toepassings kwets baarheid voor SQL-injectie. Dit betekent dat de aanvaller schadelijke SQL-instructies probeert te injecteren met de kwetsbare toepassingscode of opgeslagen procedures.
