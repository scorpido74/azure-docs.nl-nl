---
title: Azure Automation runbooks met metrische waarschuwingen bewaken
description: Dit artikel begeleidt u bij het bewaken van Azure Automation runbooks op basis van metrische gegevens
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: f288029bb35fe4e3c71db37a1de265edbcd913b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310543"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Runbooks controleren met metrische waarschuwingen

In dit artikel leert u hoe u waarschuwingen kunt maken op basis van de voltooiings status van runbooks.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Aanmelden bij Azure op https://portal.azure.com

## <a name="create-alert"></a>Waarschuwing maken

Met waarschuwingen kunt u een voor waarde definiëren die moet worden bewaakt en een actie die moet worden uitgevoerd wanneer aan deze voor waarde wordt voldaan.

Navigeer in het Azure Portal naar uw Automation-account. Onder **bewaking**selecteert u **waarschuwingen** en klikt u op **+ nieuwe waarschuwings regel**. Het bereik voor het doel is al gedefinieerd voor uw Automation-account.

### <a name="configure-alert-criteria"></a>Waarschuwings criteria configureren

1. Klik op **+ criteria toevoegen**. Selecteer **metrische gegevens** voor het **signaal type**en kies **Totaal aantal taken** in de tabel.

2. Op de pagina **signaal logica configureren** kunt u de logica definiëren waarmee de waarschuwing wordt geactiveerd. Onder de historische grafiek ziet u twee dimensies, de naam en de **status**van het **Runbook** . Dimensies zijn verschillende eigenschappen voor een metriek die kan worden gebruikt voor het filteren van resultaten. Selecteer bij **runbooknaam**het runbook waarvoor u een melding wilt ontvangen of laat het veld leeg om op alle runbooks te waarschuwen. Selecteer voor **status**een status in de vervolg keuzelijst die u wilt controleren. De runbook-naam en status waarden die in de vervolg keuzelijst worden weer gegeven, zijn alleen voor taken die in de afgelopen week zijn uitgevoerd.

   Als u een waarschuwing wilt ontvangen over een status of runbook dat niet wordt weer gegeven in de vervolg **\+** keuzelijst, klikt u op het volgende bij de dimensie. Met deze actie opent u een dialoog venster waarin u een aangepaste waarde kunt invoeren die voor die dimensie onlangs niet is verzonden. Als u een waarde opgeeft die niet bestaat voor een eigenschap, wordt uw waarschuwing niet geactiveerd.

   > [!NOTE]
   > Als u geen naam voor de dimensie **RunbookName** toepast en er runbooks zijn die voldoen aan de status criteria, waaronder verborgen systeem runbooks, ontvangt u een waarschuwing.

3. Definieer onder **waarschuwings logica**de voor waarde en drempel waarde voor uw waarschuwing. Hieronder wordt een voor beeld van uw voor waarde gedefinieerd.

4. Selecteer onder **geëvalueerd op basis van**de tijds duur voor de query en hoe vaak de query moet worden uitgevoerd. Als u bijvoorbeeld **de laatste vijf minuten** voor de **periode** en **elke 1 minuut** voor de **frequentie**hebt gekozen, zoekt de waarschuwing naar het aantal runbooks dat in de afgelopen vijf minuten aan uw criteria is voldaan. Deze query wordt elke minuut uitgevoerd en zodra de opgegeven waarschuwings criteria niet meer in een venster van vijf minuten worden gevonden, wordt de waarschuwing automatisch opgelost. Klik op **Gereed** als u klaar bent.

   ![Een resource voor de waarschuwing selecteren](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Waarschuwingsdetails definiëren

1. Onder **2. Definieer waarschuwings Details**, geef de waarschuwing een beschrijvende naam en beschrijving. Stel de **Ernst** in die overeenkomt met uw waarschuwings voorwaarde. Er zijn vijf Ernst, variërend van 0 tot 5. De waarschuwingen worden gezien op dezelfde onafhankelijk van de ernst, u kunt de ernst overeenkomen met uw bedrijfs logica.

1. Onder aan de sectie bevindt zich een knop waarmee u de regel na voltooiing kunt inschakelen. Standaard worden regels ingeschakeld bij het maken. Als u Nee selecteert, kunt u de waarschuwing maken en wordt deze in een **Uitgeschakelde** status gemaakt. Op de pagina **regels** in azure monitor kunt u deze selecteren en op **inschakelen** klikken om de waarschuwing in te scha kelen wanneer u klaar bent.

### <a name="define-the-action-to-take"></a>Definieer de actie die moet worden uitgevoerd

1. Minder dan **3. Actie groep definiëren**, klikt u op **+ nieuwe actie groep**. Een actie groep is een groep acties die u in meer dan één waarschuwing kunt gebruiken. Deze kunnen bestaan uit, maar zijn niet beperkt tot, e-mail meldingen, runbooks, webhooks en nog veel meer. Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/platform/action-groups.md) voor meer informatie over actiegroepen

1. Voer in het vak **Naam van de actiegroep** een beschrijvende naam en een korte naam in. De korte naam wordt gebruikt in plaats van een volledige naam van de actiegroep als er meldingen via deze groep worden verzonden.

1. Selecteer in de sectie **acties** onder **actie type** **e-mail/SMS/push/Voice**.

1. Geef op de pagina **E-mail/sms/push/spraak** een naam op. Schakel het selectievakje **E-mail** in en voer een geldig e-mailadres in dat moet worden gebruikt.

   ![E-mailactiegroep configureren](./media/automation-alert-activity-log/add-action-group.png)

1. Klik op **OK** op de pagina **E-mail/sms/push/spraak** om de pagina te sluiten en klik op **OK** om de pagina **Actiegroep toevoegen** te sluiten. De naam die u op deze pagina opgeeft, wordt opgeslagen als **actie naam**.

1. Na het voltooien klikt u op **Opslaan**. Met deze actie wordt de regel gemaakt waarmee u wordt gewaarschuwd wanneer een runbook met een bepaalde status is voltooid.

> [!NOTE]
> Wanneer u een e-mail adres aan een actie groep toevoegt, wordt een e-mail melding verzonden met de mede deling dat het adres is toegevoegd aan een actie groep.

## <a name="notification"></a>Melding

Wanneer aan de waarschuwings criteria wordt voldaan, voert de actie groep de gedefinieerde actie uit. In het voor beeld van dit artikel wordt een e-mail verzonden. De volgende afbeelding is een voor beeld van een e-mail bericht dat wordt weer gegeven nadat de waarschuwing is geactiveerd:

![E-mail waarschuwing](./media/automation-alert-activity-log/alert-email.png)

Zodra de metriek niet langer buiten de opgegeven drempel waarde ligt, wordt de waarschuwing gedeactiveerd en voert de actie groep de gedefinieerde actie uit. Als er een actie type voor e-mail is geselecteerd, wordt er een oplossings-e-mail verzonden met de melding dat deze is opgelost.

## <a name="next-steps"></a>Volgende stappen

Ga door naar het volgende artikel voor meer informatie over andere manieren waarop u waarschuwingen kunt integreren in uw Automation-account.

> [!div class="nextstepaction"]
> [Een waarschuwing gebruiken om een Azure Automation runbook te activeren](automation-create-alert-triggered-runbook.md)
