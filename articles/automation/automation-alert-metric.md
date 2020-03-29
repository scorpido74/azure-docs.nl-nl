---
title: Azure Automation-runbooks bewaken met metrische waarschuwingen
description: In dit artikel u azure automation runbooks bewaken op basis van statistieken
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: 9bd028157b33817898ef69f9e47cb8b5d9b8f381
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75367090"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Runbooks met metrische waarschuwingen bewaken

In dit artikel leert u hoe u waarschuwingen maakt op basis van de voltooiingsstatus van runbooks.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Aanmelden bij Azure op https://portal.azure.com

## <a name="create-alert"></a>Waarschuwing maken

Met waarschuwingen u een voorwaarde definiëren om te controleren op en een actie die moet worden ondernomen wanneer aan die voorwaarde is voldaan.

Navigeer in de Azure-portal naar uw automatiseringsaccount. Selecteer **onder Bewaken**de optie **Waarschuwing** en klik op + **Nieuwe waarschuwingsregel**. De ruimte voor het doel is al gedefinieerd in uw automatiseringsaccount.

### <a name="configure-alert-criteria"></a>Waarschuwingscriteria configureren

1. Klik **op + Criteria toevoegen**. Selecteer **Statistieken** voor het **signaaltype**en kies **Totaalaantal taken** in de tabel.

2. Op de pagina **Signaallogica configureren** definieert u de logica die de waarschuwing activeert. Onder de historische grafiek wordt u gepresenteerd met twee dimensies, **Runbook Name** and **Status**. Afmetingen zijn verschillende eigenschappen voor een statistiek die kan worden gebruikt om resultaten te filteren. Selecteer **bij Runbook Name**het runbook waarop u wilt waarschuwen of leeg laat om te waarschuwen voor alle runbooks. Selecteer **voor Status**een status in de vervolgkeuzelijst waarvoor u wilt controleren. De naam en statuswaarden van het runbook die in de vervolgkeuzelijst worden weergegeven, zijn alleen voor taken die in de afgelopen week zijn uitgevoerd.

   Als u wilt waarschuwen voor een status of runbook die niet **\+** wordt weergegeven in de vervolgkeuzelijst, klikt u op het volgende naast de dimensie. Met deze actie wordt een dialoogvenster geopend waarmee u een aangepaste waarde invoeren, die onlangs niet is uitgezonden voor die dimensie. Als u een waarde invoert die niet bestaat voor een eigenschap, wordt uw waarschuwing niet geactiveerd.

   > [!NOTE]
   > Als u geen naam toepast voor de dimensie **RunbookName,** ontvangt u een waarschuwing als er runbooks zijn die voldoen aan de statuscriteria, waaronder verborgen systeemrunbooks.

3. Definieer **onder Waarschuwingslogica**de voorwaarde en drempelwaarde voor uw waarschuwing. Hieronder wordt een voorbeeld van uw gedefinieerde voorwaarde weergegeven.

4. Selecteer **onder Geëvalueerd op basis**van de tijdspanne voor de query en hoe vaak u wilt dat die query wordt uitgevoerd. Als u bijvoorbeeld **Over de laatste 5 minuten** voor **Periode** en **Elke 1 minuut** voor **Frequentie**kiest, wordt in de waarschuwing gezocht naar het aantal runbooks dat in de afgelopen 5 minuten aan uw criteria voldeed. Deze query wordt elke minuut uitgevoerd en zodra de waarschuwingscriteria die u hebt gedefinieerd niet meer in een venster van 5 minuten worden gevonden, wordt de waarschuwing zelf opgelost. Klik op **Gereed** als u klaar bent.

   ![Een resource voor de waarschuwing selecteren](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Waarschuwingsdetails definiëren

1. Onder **2. Definieer waarschuwingsgegevens,** geef de waarschuwing een vriendelijke naam en beschrijving. Stel de **ernst in op** uw waarschuwingstoestand. Er zijn vijf ernst variërend van 0 tot 5. De waarschuwingen worden behandeld op dezelfde onafhankelijk van de ernst, u overeenkomen met de ernst aan uw bedrijfslogica.

1. Aan de onderkant van de sectie, is een knop waarmee u de regel in te schakelen na voltooiing. Standaard regels zijn ingeschakeld bij het maken. Als u Nee selecteert, u de waarschuwing maken en deze in een **uitgeschakelde** status maken. Op de pagina **Regels** in Azure Monitor u deze selecteren en op **Inschakelen** klikken om de waarschuwing in te schakelen wanneer u er klaar voor bent.

### <a name="define-the-action-to-take"></a>De actie definiëren die u moet uitvoeren

1. Onder **3. Definieer actiegroep**, klik **+ Nieuwe actiegroep**. Een actiegroep is een groep acties die u in meer dan één waarschuwing gebruiken. Deze kunnen omvatten, maar zijn niet beperkt tot, e-mail meldingen, runbooks, webhooks, en nog veel meer. Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/platform/action-groups.md) voor meer informatie over actiegroepen

1. Voer in het vak **Naam van de actiegroep** een beschrijvende naam en een korte naam in. De korte naam wordt gebruikt in plaats van een volledige naam van de actiegroep als er meldingen via deze groep worden verzonden.

1. Selecteer **e-mail/sms/push/spraak**in de sectie **Acties** onder **ACTIETYPE**.

1. Geef op de pagina **E-mail/sms/push/spraak** een naam op. Schakel het selectievakje **E-mail** in en voer een geldig e-mailadres in dat moet worden gebruikt.

   ![E-mailactiegroep configureren](./media/automation-alert-activity-log/add-action-group.png)

1. Klik op **OK** op de pagina **E-mail/sms/push/spraak** om de pagina te sluiten en klik op **OK** om de pagina **Actiegroep toevoegen** te sluiten. De op deze pagina opgegeven naam wordt opgeslagen als de **ACTIENAAM**.

1. Na het voltooien klikt u op **Opslaan**. Met deze actie wordt de regel gemaakt die u waarschuwt wanneer een runbook is voltooid met een bepaalde status.

> [!NOTE]
> Wanneer u een e-mailadres toevoegt aan een actiegroep, wordt een meldingsmail verzonden waarin staat dat het adres is toegevoegd aan een actiegroep.

## <a name="notification"></a>Melding

Wanneer aan de waarschuwingscriteria is voldaan, voert de actiegroep de gedefinieerde actie uit. In het voorbeeld van dit artikel wordt een e-mail verzonden. De volgende afbeelding is een voorbeeld van een e-mail die u ontvangt nadat de waarschuwing is geactiveerd:

![E-mailwaarschuwing](./media/automation-alert-activity-log/alert-email.png)

Zodra de statistiek niet meer buiten de gedefinieerde drempelwaarde valt, wordt de waarschuwing gedeactiveerd en voert de actiegroep de gedefinieerde actie uit. Als een e-mailactietype is geselecteerd, wordt een e-mail met een oplossing verzonden waarin staat dat deze is opgelost.

## <a name="next-steps"></a>Volgende stappen

Ga verder naar het volgende artikel om meer te weten te komen over andere manieren waarop u waarschuwingen integreren in uw automatiseringsaccount.

> [!div class="nextstepaction"]
> [Een waarschuwing gebruiken om een Azure Automation-runbook te activeren](automation-create-alert-triggered-runbook.md)
