---
title: Problemen met Azure Monitor-waarschuwingen en -meldingen oplossen
description: Veelvoorkomende problemen met Azure Monitor-waarschuwingen en mogelijke oplossingen.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132331"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Problemen oplossen in Azure Monitor-waarschuwingen 

In dit artikel worden veelvoorkomende problemen in Azure Monitor-waarschuwingen besproken.

Azure Monitor-waarschuwingen stellen u proactief op de hoogte wanneer belangrijke voorwaarden worden gevonden in uw bewakingsgegevens. Hiermee u problemen identificeren en aanpakken voordat de gebruikers van uw systeem deze opmerken. Zie Overzicht van waarschuwingen in Microsoft Azure voor meer informatie over [waarschuwingen.](alerts-overview.md)

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Actie of melding op mijn waarschuwing werkte niet zoals verwacht

Als u een geactiveerde waarschuwing zien in de Azure-portal, maar een probleem hebt met sommige acties of meldingen, raadpleegt u de volgende secties.

## <a name="did-not-receive-expected-email"></a>Geen verwachte e-mail ontvangen

Als u een geactiveerde waarschuwing zien in de Azure-portal, maar de e-mail die u erover hebt geconfigureerd, niet hebt ontvangen, voert u de volgende stappen uit: 

1. **Werd de e-mail onderdrukt door een [actieregel?](alerts-action-rules.md)** 

    Controleer door te klikken op de geactiveerde waarschuwing in de portal, en kijk op de geschiedenis tabblad voor onderdrukte [actiegroepen:](action-groups.md) 

    ![Geschiedenis van de onderdrukking van waarschuwingsregels](media/alerts-troubleshoot/history-action-rule.png)

1. **Is het type actie 'E-mail Azure Resource Manager-rol'?**

    Met deze actie wordt alleen gekeken naar Azure Resource Manager-roltoewijzingen die zich op het abonnementsbereik bevinden en naar het type *Gebruiker*.  Zorg ervoor dat u de rol op abonnementsniveau hebt toegewezen en niet op resourceniveau of resourcegroepniveau.

1. **Accepteren uw e-mailserver en -mailbox externe e-mails?**

    Controleer of e-mails van deze drie adressen niet worden geblokkeerd:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Het is gebruikelijk dat interne mailinglijsten of distributielijsten e-mails van externe e-mailadressen blokkeren. Je moet de bovenstaande e-mailadressen op de witte lijst krijgen.  
    Als u wilt testen, voegt u een regelmatig werk-e-mailadres (geen mailinglijst) toe aan de actiegroep en ziet u of er waarschuwingen binnenkomen naar die e-mail. 

1. **Is de e-mail verwerkt door inboxregels of een spamfilter?** 

    Controleer of er geen regels voor postvak IN zijn die deze e-mails verwijderen of naar een zijmap verplaatsen. Inboxregels kunnen bijvoorbeeld specifieke afzenders of specifieke woorden in het onderwerp opvangen.

    Controleer ook:
    
      - de spam-instellingen van uw e-mailclient (zoals Outlook, Gmail)
      - de afzenderlimieten / spam-instellingen / quarantaine-instellingen van uw e-mailserver (zoals Exchange, Office 365, G-suite)
      - de instellingen van uw e-mailbeveiligingstoestel, indien aanwezig (zoals Barracuda, Cisco). 

1. **Heb je je per ongeluk afgemeld bij de actiegroep?** 

    De waarschuwingse-mails bieden een link om u af te melden voor de actiegroep. Ga als volgt te werk om te controleren of u zich per ongeluk hebt afgemeld voor deze actiegroep:

    1. Open de actiegroep in de portal en controleer de kolom Status:

    ![statuskolom actiegroep](media/alerts-troubleshoot/action-group-status.png)

    2. Zoek in je e-mail naar de bevestiging voor afmelden:

    ![afgemeld voor een actiegroep voor waarschuwingen](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Als u zich opnieuw wilt abonneren, gebruikt u de koppeling in de e-mail met de bevestiging voor afmelden die u hebt ontvangen, of verwijdert u het e-mailadres uit de actiegroep en voegt u het opnieuw toe. 
 
1. **Bent u beoordeeld beperkt als gevolg van vele e-mails naar een enkel e-mailadres?** 

    E-mail is [beperkt tot](alerts-rate-limiting.md) niet meer dan 100 e-mails per uur naar elk e-mailadres. Als u deze drempel overschrijdt, worden extra e-mailmeldingen verwijderd.  Controleer of u een bericht hebt ontvangen waarin staat dat uw e-mailadres tijdelijk beperkt is: 
 
   ![E-mailtarief beperkt](media/alerts-troubleshoot/email-paused.png)

   Als u een groot aantal meldingen wilt ontvangen zonder beperking van de snelheid, u overwegen een andere actie te gebruiken, zoals webhook, logica-app, Azure-functie of automatiseringsrunbooks, waarvan geen van allen tariefbeperkt is. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Geen verwachte sms, spraakoproep of pushmelding ontvangen

Als u een geactiveerde waarschuwing in de portal zien, maar de sms,spraakoproep of pushmelding die u hierover hebt geconfigureerd, niet hebt ontvangen, voert u de volgende stappen uit: 

1. **Werd de actie onderdrukt door een [actieregel?](alerts-action-rules.md)** 

    Controleer door te klikken op de geactiveerde waarschuwing in de portal, en kijk op de geschiedenis tabblad voor onderdrukte [actiegroepen:](action-groups.md) 

    ![Geschiedenis van de onderdrukking van waarschuwingsregels](media/alerts-troubleshoot/history-action-rule.png)

   Als dat onbedoeld was, u de actieregel wijzigen, uitschakelen of verwijderen.
 
1. **SMS / stem: Klopt uw telefoonnummer?**

   Controleer de sms-actie op typefouten in de landcode of het telefoonnummer. 
 
1. **SMS / voice: bent u tarief beperkt?** 

    Sms en spraakoproepen zijn beperkt tot niet meer dan één melding per vijf minuten per telefoonnummer. Als u deze drempel overschrijdt, worden de meldingen verwijderd. 

      - Spraakoproep : controleer uw gespreksgeschiedenis en kijk of u in de voorafgaande vijf minuten een ander gesprek had dan Azure. 
      - SMS - controleer uw sms-geschiedenis op een bericht dat uw telefoonnummer beperkt is. 

    Als u een groot aantal meldingen wilt ontvangen zonder beperking van de snelheid, u overwegen een andere actie te gebruiken, zoals webhook, logica-app, Azure-functie of automatiseringsrunbooks, waarvan geen van allen tariefbeperkt is. 
 
1. **SMS: Heb je je per ongeluk afgemeld bij de actiegroep?**

    Open uw sms-geschiedenis en controleer of u zich hebt afgemeld voor de levering van sms'en van deze specifieke actiegroep (met behulp van het antwoord UITSCHAKELEN action_group_short_name) of van alle actiegroepen (met behulp van het STOP-antwoord). Als u zich opnieuw wilt abonneren, verzendt u de desbetreffende sms-opdracht (ENABLE action_group_short_name of START), of verwijdert u de sms-actie uit de actiegroep en voegt u deze opnieuw toe.  Zie [sms-waarschuwingsgedrag in actiegroepen voor](alerts-sms-behavior.md)meer informatie.

1. **Heb je per ongeluk de meldingen op je telefoon geblokkeerd?**

   Op de meeste mobiele telefoons u oproepen of sms'en van specifieke telefoonnummers of korte codes blokkeren of pushmeldingen van specifieke apps (zoals de mobiele Azure-app) blokkeren. Als u wilt controleren of u de meldingen per ongeluk op uw telefoon hebt geblokkeerd, zoekt u in de documentatie specifiek voor uw telefoonbesturingssysteem en -model of test u met een ander telefoonnummer en telefoonnummer. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Verwacht een ander type actie te activeren, maar het deed niet 

Als u een geactiveerde waarschuwing in de portal zien, maar de geconfigureerde actie niet is geactiveerd, voert u de volgende stappen uit: 

1. **Werd de actie onderdrukt door een actieregel?** 

    Controleer door te klikken op de geactiveerde waarschuwing in de portal, en kijk op de geschiedenis tabblad voor onderdrukte [actiegroepen:](action-groups.md) 

    ![Geschiedenis van de onderdrukking van waarschuwingsregels](media/alerts-troubleshoot/history-action-rule.png)
 
    Als dat onbedoeld was, u de actieregel wijzigen, uitschakelen of verwijderen. 

1. **Heeft een webhook niet trigger?**

    1. **Zijn de bron-IP-adressen geblokkeerd?**
    
       Whitelist de [IP-adressen](action-groups.md#action-specific-information) die de webhook wordt aangeroepen uit.

    1. **Werkt uw webhook eindpunt correct?**

       Controleer of het webhook-eindpunt dat u hebt geconfigureerd, correct is en of het eindpunt correct werkt. Controleer uw webhook logs of instrument de code, zodat u zou kunnen onderzoeken (bijvoorbeeld, log de inkomende payload). 

    1. **Belt u Slack of Microsoft Teams?**  
    Elk van deze eindpunten verwacht een specifieke JSON-indeling. Volg [deze instructies](action-groups-logic-app.md) om in plaats daarvan een logische app-actie te configureren.

    1. **Heeft uw webhook werd niet reageren of teruggestuurd fouten?** 

        Onze time-out periode voor een webhook reactie is 10 seconden. De webhook-oproep wordt tot twee keer opnieuw geprobeerd wanneer de volgende HTTP-statuscodes worden geretourneerd: 408, 429, 503, 504 of wanneer het HTTP-eindpunt niet reageert. De eerste poging vindt plaats na 10 seconden. De tweede en laatste poging gebeurt na 100 seconden. Als de tweede poging mislukt, wordt het eindpunt 30 minuten lang niet meer opgeroepen voor een actiegroep.

## <a name="action-or-notification-happened-more-than-once"></a>Actie of melding is meer dan eens gebeurd 

Als u meerdere keren een melding voor een waarschuwing (zoals een e-mail of sms) hebt ontvangen of de actie van de waarschuwing (zoals webhook of Azure-functie) meerdere keren is geactiveerd, voert u de volgende stappen uit: 

1. **Is het echt dezelfde waarschuwing?** 

    In sommige gevallen worden meerdere vergelijkbare waarschuwingen rond dezelfde tijd geactiveerd. Dus, het lijkt misschien alsof dezelfde waarschuwing zijn acties meerdere keren geactiveerd. Een regel voor waarschuwingvoor activiteitenlogboeken kan bijvoorbeeld worden geconfigureerd om zowel te vuren wanneer een gebeurtenis is gestart als wanneer deze is voltooid (geslaagd of mislukt), door niet te filteren op het veld gebeurtenisstatus. 

    Als u wilt controleren of deze acties of meldingen afkomstig zijn van verschillende waarschuwingen, onderzoekt u de waarschuwingsgegevens, zoals de tijdstempel en de waarschuwings-id of de correlatie-id. U ook de lijst met ontslagen waarschuwingen in het portaal controleren. Als dat het geval is, moet u de logica van de waarschuwingsregel aanpassen of de waarschuwingsbron anderszins configureren. 

1. **Wordt de actie herhaald in meerdere actiegroepen?** 

    Wanneer een waarschuwing wordt geactiveerd, wordt elk van de actiegroepen onafhankelijk verwerkt. Dus als een actie (zoals een e-mailadres) wordt weergegeven in meerdere geactiveerde actiegroepen, wordt deze één keer per actiegroep aangeroepen. 

    Als u wilt controleren welke actiegroepen zijn geactiveerd, gaat u naar het tabblad Waarschuwingsgeschiedenis. U ziet daar beide actiegroepen gedefinieerd in de waarschuwingsregel en actiegroepen die zijn toegevoegd aan de waarschuwingsregels: 

    ![Actie herhaald in meerdere actiegroepen](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Actie of melding heeft een onverwachte inhoud

Als u de waarschuwing hebt ontvangen, maar van mening bent dat sommige velden ontbreken of onjuist zijn, voert u de volgende stappen uit: 

1. **Heb je het juiste formaat voor de actie gekozen?** 

    Elk actietype (e-mail, webhook, enz.) heeft twee indelingen : de standaard- en legacy-indeling en de [nieuwere algemene schemaindeling](alerts-common-schema.md). Wanneer u een actiegroep maakt, geeft u per actie de gewenste indeling op: verschillende acties in de actiegroepen kunnen verschillende indelingen hebben. 

    Bijvoorbeeld voor webhook actie: 

    ![webhook-actieschema-optie](media/alerts-troubleshoot/webhook.png)

    Controleer of de op actieniveau opgegeven indeling is wat u verwacht. U hebt bijvoorbeeld code ontwikkeld die reageert op waarschuwingen (webhook, functie, logische app, enz.), waarbij u een indeling verwacht, maar later in de actie die u of een andere persoon een andere indeling hebt opgegeven.  

    Controleer ook de payload-indeling (JSON) op waarschuwingen voor [activiteitenlogboeken](activity-log-alerts-webhook.md), op [logboekzoekwaarschuwingen](alerts-log-webhook.md) (zowel Application Insights als logboekanalyses), voor [metrische waarschuwingen](alerts-metric-near-real-time.md#payload-schema), voor het [algemene waarschuwingsschema](alerts-common-schema-definitions.md)en voor de afgeschafte [klassieke metrische waarschuwingen](alerts-webhooks.md).

 
1. **Waarschuwingen voor activiteitenlogboeken: is de informatie beschikbaar in het activiteitenlogboek?** 

    [Waarschuwingen voor activiteitenlogboeken](activity-log-alerts.md) zijn waarschuwingen die zijn gebaseerd op gebeurtenissen die zijn geschreven naar het Azure Activity Log, zoals gebeurtenissen over het maken, bijwerken of verwijderen van Azure-resources, servicestatus en resourcestatusgebeurtenissen of bevindingen uit Azure Advisor en Azure Policy. Als u een waarschuwing hebt ontvangen op basis van het activiteitenlogboek, maar sommige velden die u nodig hebt, ontbreken of onjuist zijn, controleert u eerst de gebeurtenissen in het activiteitenlogboek zelf. Als de Azure-bron niet de velden heeft geschreven die u zoekt in de gebeurtenis activiteitslogboek, worden deze velden niet opgenomen in de bijbehorende waarschuwing. 

## <a name="action-rule-is-not-working-as-expected"></a>Actieregel werkt niet zoals verwacht 

Als u een geactiveerde waarschuwing in de portal zien, maar een gerelateerde actieregel niet werkte zoals verwacht, voert u de volgende stappen uit: 

1. **Is de actieregel ingeschakeld?** 

    Controleer de kolom van de actieregelstatus om te controleren of de gerelateerde actierol is ingeschakeld. 

    ![Grafische](media/alerts-troubleshoot/action-rule-status.png) 

    Als dit niet is ingeschakeld, u de actieregel inschakelen door deze te selecteren en op Inschakelen te klikken. 

1. **Is het een service health alert?** 

    Waarschuwingen voor servicestatus (monitorservice = "Servicestatus") worden niet beïnvloed door actieregels. 

1. **Heeft de actieregel op uw alarm gehandeld?** 

    Controleer of de actieregel uw waarschuwing heeft verwerkt door op de geactiveerde waarschuwing in de portal te klikken en kijk op het tabblad geschiedenis.

    Hier is een voorbeeld van actieregel die alle actiegroepen onderdrukt: 
 
     ![Geschiedenis van de onderdrukking van waarschuwingsregels](media/alerts-troubleshoot/history-action-rule.png)

    Hier is een voorbeeld van een actieregel die een andere actiegroep toevoegt:

    ![Actie herhaald in meerdere actiegroepen](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Komt de scope en het filter overeen met de geactiveerde waarschuwing?** 

    Als u denkt dat de actieregel had moeten worden afgevuurd, maar dat niet is gedaan, of dat deze niet had moeten worden afgevuurd, maar dat wel wel had moeten doen, moet u de werkingssfeer van de actieregel en de filtervoorwaarden ten opzichte van de eigenschappen van de geactiveerde waarschuwing zorgvuldig onderzoeken. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>De waarschuwings-id van een geactiveerd eis vinden

Wanneer u een aanvraag opent over een specifieke geactiveerde waarschuwing (zoals - als u de e-mailmelding niet hebt ontvangen), moet u de waarschuwings-id verstrekken. 

Voer de volgende stappen uit om het te vinden:

1. Navigeer in de Azure-portal naar de lijst met ontslagen waarschuwingen en zoek die specifieke waarschuwing. U de filters gebruiken om het te lokaliseren. 

1. Klik op de waarschuwing om de waarschuwingsgegevens te openen. 

1. Schuif omlaag in de waarschuwingsvelden van het eerste tabblad (het tabblad overzicht) totdat u het vindt en kopieer het. Dat veld bevat ook een helperknop 'Kopiëren naar klembord' die u gebruiken.  

    ![waarschuwings-id zoeken](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Probleem met het maken, bijwerken of verwijderen van actieregels in de Azure-portal

Als u een fout hebt ontvangen tijdens het maken, bijwerken of verwijderen van een [actieregel,](alerts-action-rules.md)voert u de volgende stappen uit: 

1. **Heeft u een toestemmingsfout ontvangen?**  

    U moet de [ingebouwde rol Monitoringcontributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)hebben of de specifieke machtigingen met betrekking tot actieregels en waarschuwingen.

1. **Heb je de parameters van de actieregel gecontroleerd?**  

    Controleer de documentatie van de [actieregel](alerts-action-rules.md)of de opdracht [PowerShell Set-AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) van de actieregel. 


## <a name="next-steps"></a>Volgende stappen
- Als u een logboekwaarschuwing gebruikt, raadpleegt u ook [Logboekwaarschuwingen oplossen](alert-log-troubleshoot.md).
- Ga terug naar de [Azure-portal](https://portal.azure.com) om te controleren of u uw probleem hebt opgelost met bovenstaande richtlijnen 
