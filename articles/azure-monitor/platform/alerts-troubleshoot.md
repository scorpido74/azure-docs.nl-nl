---
title: Problemen met Azure Monitor waarschuwingen en meldingen oplossen
description: Veelvoorkomende problemen met Azure Monitor waarschuwingen en mogelijke oplossingen.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132331"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Problemen met Azure Monitor waarschuwingen oplossen 

In dit artikel worden veelvoorkomende problemen met Azure Monitor waarschuwingen beschreven.

Azure Monitor waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen identificeren en verhelpen voordat de gebruikers van uw systeem ze merken. Zie [overzicht van waarschuwingen in Microsoft Azure](alerts-overview.md)voor meer informatie over waarschuwingen.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>De actie of melding bij mijn waarschuwing werkt niet zoals verwacht

Als u een gebrande waarschuwing in de Azure Portal kunt zien, maar een probleem met een aantal acties of meldingen hebt, raadpleegt u de volgende secties.

## <a name="did-not-receive-expected-email"></a>Er is geen verwachte e-mail ontvangen

Als er een waarschuwing wordt weer gegeven in de Azure Portal, maar u hebt de e-mail die u hebt geconfigureerd niet ontvangen, voert u de volgende stappen uit: 

1. **Is de e-mail onderdrukt door een [actie regel](alerts-action-rules.md)**? 

    Controleer door te klikken op de gebrande waarschuwing in de portal en Bekijk het tabblad geschiedenis voor onderdrukte [actie groepen](action-groups.md): 

    ![Geschiedenis van onderdrukking van waarschuwings actie regel](media/alerts-troubleshoot/history-action-rule.png)

1. **Is het type actie ' e-mail Azure Resource Manager role '?**

    Met deze actie wordt alleen gezocht naar Azure Resource Manager roltoewijzingen die zich op het abonnements bereik bevinden, en van het type *gebruiker*.  Zorg ervoor dat u de rol hebt toegewezen op het abonnements niveau en niet op het niveau van de resource of de resource groep.

1. **Accepteren uw e-mail server en Postvak externe e-mail berichten?**

    Controleer of e-mail berichten van deze drie adressen niet worden geblokkeerd:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Het is gebruikelijk dat interne mailing lijsten of distributie lijsten e-mails van externe e-mail adressen blok keren. U moet de bovenstaande e-mail adressen white list.  
    Als u wilt testen, voegt u een gewoon werk-e-mail adres (niet een adressen lijst) toe aan de actie groep en controleert u of er waarschuwingen voor deze e-mail binnenkomen. 

1. **Is het e-mail bericht verwerkt door de regels voor het postvak in of een spam filter?** 

    Controleer of er geen regels voor Postvak in zijn die de e-mail berichten verwijderen of verplaatsen naar een side-map. Regels voor Postvak in kunnen bijvoorbeeld specifieke afzenders of specifieke woorden in het onderwerp ondervangen.

    Controleer ook het volgende:
    
      - de spam instellingen van uw e-mailclient (zoals Outlook, Gmail)
      - de verzender limieten/instellingen voor ongewenste e-mail/quarantaine-instellingen van uw e-mail server (zoals Exchange, Office 365, G-suite)
      - de instellingen van uw e-mail beveiligings apparaat (zoals Barracuda, Cisco). 

1. **Hebt u per ongeluk afgemeld voor de actie groep?** 

    De e-mail waarschuwingen bieden een koppeling waarmee u zich kunt afmelden bij de actie groep. Als u wilt controleren of u per ongeluk bent afgemeld voor deze actie groep, hetzij:

    1. Open de actie groep in de portal en controleer de kolom Status:

    ![kolom Status van actie groep](media/alerts-troubleshoot/action-group-status.png)

    2. Zoek in uw e-mail bericht naar de afmeldings bevestiging:

    ![afgemeld op de waarschuwings actie groep](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Als u zich opnieuw wilt abonneren, gebruikt u de koppeling in het e-mail bericht voor het afmelden van de abonnement die u hebt ontvangen of verwijdert u het e-mail adres uit de actie groep en voegt u het vervolgens weer toe. 
 
1. **Hebt u minder geclassificeerd als gevolg van veel e-mails die naar één e-mail adres worden verzonden?** 

    E-mail is [beperkt](alerts-rate-limiting.md) tot maxi maal 100 e-mail berichten elk uur aan elk e-mail adres. Als u deze drempel waarde doorgeeft, worden extra e-mail meldingen verwijderd.  Controleer of u een bericht hebt ontvangen waarin staat dat uw e-mail adres tijdelijk beperkt is: 
 
   ![Beperkte e-mail frequentie](media/alerts-troubleshoot/email-paused.png)

   Als u een hoog volume aan meldingen wilt ontvangen zonder frequentie limiet, kunt u overwegen een andere actie te gebruiken, zoals een webhook, een logische app, Azure function of Automation-runbooks, waarvan geen rente is beperkt. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Er is geen verwachte SMS, telefoon oproep of push melding ontvangen

Als er een waarschuwing wordt weer gegeven in de portal, maar geen SMS, spraak oproep of push melding heeft ontvangen die u hebt geconfigureerd, voert u de volgende stappen uit: 

1. **Is de actie onderdrukt door een [actie regel](alerts-action-rules.md)?** 

    Controleer door te klikken op de gebrande waarschuwing in de portal en Bekijk het tabblad geschiedenis voor onderdrukte [actie groepen](action-groups.md): 

    ![Geschiedenis van onderdrukking van waarschuwings actie regel](media/alerts-troubleshoot/history-action-rule.png)

   Als dat onbedoeld is, kunt u de actie regel wijzigen, uitschakelen of verwijderen.
 
1. **SMS/Voice: is uw telefoon nummer correct?**

   Controleer de SMS-actie op typfouten in de land code of het telefoon nummer. 
 
1. **SMS/Voice: hebt u een beperkt aantal beperkingen?** 

    SMS-en spraak oproepen zijn de tarieven beperkt tot niet meer dan één melding elke vijf minuten per telefoon nummer. Als u deze drempel waarde doorgeeft, worden de meldingen verwijderd. 

      - Telefoon oproep: Controleer de oproep geschiedenis en kijk of u in de voor gaande vijf minuten een andere aanroep van Azure hebt. 
      - SMS: Controleer uw SMS-geschiedenis voor een bericht dat aangeeft dat uw telefoon nummer beperkt is. 

    Als u een hoog volume aan meldingen wilt ontvangen zonder frequentie limiet, kunt u overwegen een andere actie te gebruiken, zoals een webhook, een logische app, Azure function of Automation-runbooks, waarvan geen rente is beperkt. 
 
1. **SMS: hebt u per ongeluk afgemeld bij de actie groep?**

    Open uw SMS-geschiedenis en controleer of u geen SMS-levering van deze specifieke actie groep hebt uitgevoerd (met behulp van de action_group_short_name reply uitschakelen) of vanuit alle actie groepen (met behulp van het STOP bericht). Als u zich opnieuw wilt abonneren, verzendt u de relevante SMS-opdracht (Schakel action_group_short_name of START) of verwijdert u de SMS-actie uit de actie groep en voegt u deze vervolgens weer toe.  Zie voor meer informatie [SMS-waarschuwings gedrag in actie groepen](alerts-sms-behavior.md).

1. **Hebt u per ongeluk de meldingen op uw telefoon geblokkeerd?**

   De meeste mobiele telefoons bieden u de mogelijkheid om oproepen of SM'S te blok keren uit specifieke telefoon nummers of korte codes, of om Push meldingen van specifieke apps te blok keren (zoals de Azure mobile app). Als u wilt controleren of u per ongeluk de meldingen op uw telefoon hebt geblokkeerd, zoekt u in de documentatie die specifiek is voor het besturings systeem en het model van uw telefoon, of test u met een ander telefoon-en telefoon nummer. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Er wordt een ander type actie verwacht, maar dit is niet 

Als u een gebrande waarschuwing in de portal kunt zien, maar de geconfigureerde actie niet is geactiveerd, volgt u deze stappen: 

1. **Is de actie onderdrukt door een actie regel?** 

    Controleer door te klikken op de gebrande waarschuwing in de portal en Bekijk het tabblad geschiedenis voor onderdrukte [actie groepen](action-groups.md): 

    ![Geschiedenis van onderdrukking van waarschuwings actie regel](media/alerts-troubleshoot/history-action-rule.png)
 
    Als dat onbedoeld is, kunt u de actie regel wijzigen, uitschakelen of verwijderen. 

1. **Is er geen webhook geactiveerd?**

    1. **Zijn de bron-IP-adressen geblokkeerd?**
    
       Hiermee worden de [IP-adressen](action-groups.md#action-specific-information) white list waarvan de webhook wordt aangeroepen.

    1. **Werkt uw webhook-eind punt correct?**

       Controleer of het webhook-eind punt dat u hebt geconfigureerd juist is en of het eind punt correct werkt. Controleer de logboeken van uw webhook of instrumenteer de code ervan, zodat u deze kunt onderzoeken (bijvoorbeeld de binnenkomende Payload registreren). 

    1. **Kunt u vertraging of micro soft-teams aanroepen?**  
    Elk van deze eind punten verwacht een specifieke JSON-indeling. Volg [deze instructies](action-groups-logic-app.md) om in plaats daarvan een actie van een logische app te configureren.

    1. **Is uw webhook niet meer reageert of fouten geretourneerd?** 

        De time-outperiode voor een webhook-reactie is 10 seconden. De webhook-aanroep wordt opnieuw geprobeerd tot twee extra tijdstippen wanneer de volgende HTTP-status codes worden geretourneerd: 408, 429, 503, 504 of wanneer het HTTP-eind punt niet reageert. De eerste poging vindt plaats na 10 seconden. De tweede en laatste nieuwe poging vindt na 100 seconden plaats. Als de tweede poging mislukt, wordt het eindpunt 30 minuten lang niet meer opgeroepen voor een actiegroep.

## <a name="action-or-notification-happened-more-than-once"></a>De actie of melding is meer dan een keer opgetreden 

Als u een melding voor een waarschuwing (zoals een e-mail bericht of SMS) meer dan een keer hebt ontvangen of de actie van de waarschuwing (zoals webhook of Azure function) meerdere keren is geactiveerd, voert u de volgende stappen uit: 

1. **Is de waarschuwing echt hetzelfde?** 

    In sommige gevallen worden meerdere vergelijk bare waarschuwingen rond hetzelfde tijdstip geactiveerd. Daarom lijkt het net alsof de actie meerdere keren is geactiveerd door dezelfde waarschuwing. Zo kan een waarschuwings regel voor een activiteiten logboek zodanig worden geconfigureerd dat deze beide wordt geactiveerd wanneer een gebeurtenis is gestart, en wanneer deze is voltooid (geslaagd of mislukt) door niet te filteren op het veld gebeurtenis status. 

    Als u wilt controleren of deze acties of meldingen afkomstig zijn van verschillende waarschuwingen, bekijkt u de details van de waarschuwing, zoals de tijds tempel en de id van de waarschuwing of de correlatie-id. U kunt ook de lijst met geactiveerde waarschuwingen in de portal controleren. Als dat het geval is, moet u de logica van de waarschuwings regel aanpassen of de bron van de waarschuwing anders configureren. 

1. **Herhaalt de actie in meerdere actie groepen?** 

    Wanneer een waarschuwing wordt geactiveerd, worden alle actie groepen onafhankelijk van elkaar verwerkt. Als er dus een actie (zoals een e-mail adres) wordt weer gegeven in meerdere geactiveerde actie groepen, wordt deze eenmaal per actie groep aangeroepen. 

    Controleer het tabblad waarschuwings geschiedenis om te controleren welke actie groepen zijn geactiveerd. U ziet dat er actie groepen worden gedefinieerd in de waarschuwings regel, en actie groepen die zijn toegevoegd aan de waarschuwing op basis van actie regels: 

    ![Actie herhaald in meerdere actie groepen](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Actie of melding heeft onverwachte inhoud

Als u de waarschuwing hebt ontvangen, maar denkt dat sommige velden ontbreken of onjuist zijn, voert u de volgende stappen uit: 

1. **Hebt u de juiste indeling gekozen voor de actie?** 

    Elk actie type (e-mail adres, webhook, enzovoort) heeft twee indelingen: de standaard, verouderde indeling en de [nieuwere algemene schema-indeling](alerts-common-schema.md). Wanneer u een actie groep maakt, geeft u de indeling op die u wilt per actie: verschillende acties in de actie groepen hebben mogelijk verschillende notaties. 

    Bijvoorbeeld voor de actie webhook: 

    ![optie voor actie schema van webhook](media/alerts-troubleshoot/webhook.png)

    Controleer of de indeling die is opgegeven op actie niveau, wat u verwacht. Het is bijvoorbeeld mogelijk dat u code hebt ontwikkeld die reageert op waarschuwingen (webhook, functie, logische app, enzovoort), wat een notatie verwacht, maar later in de actie die u of iemand anders een andere indeling heeft opgegeven.  

    Controleer ook de payload-indeling (JSON) voor [waarschuwingen van het activiteiten logboek](activity-log-alerts-webhook.md), voor [waarschuwingen voor logboek registratie](alerts-log-webhook.md) (zowel Application Insights als log Analytics) voor [metrische waarschuwingen](alerts-metric-near-real-time.md#payload-schema), voor het [algemene waarschuwings schema](alerts-common-schema-definitions.md), en voor de afgeschafte [klassieke metrische waarschuwingen](alerts-webhooks.md).

 
1. **Waarschuwingen voor activiteiten logboeken: is de informatie beschikbaar in het activiteiten logboek?** 

    [Waarschuwingen voor activiteiten logboeken](activity-log-alerts.md) zijn waarschuwingen die zijn gebaseerd op gebeurtenissen die worden geschreven naar het Azure-activiteiten logboek, zoals gebeurtenissen over het maken, bijwerken of verwijderen van Azure-resources, service status-en resource status gebeurtenissen of bevindingen van Azure Advisor en Azure Policy. Als u een waarschuwing hebt ontvangen op basis van het activiteiten logboek, maar sommige velden die u nodig hebt, moet u eerst de gebeurtenissen in het activiteiten logboek zelf controleren. Als de Azure-resource de velden die u zoekt in de activiteiten logboek gebeurtenis niet heeft geschreven, worden deze velden niet opgenomen in de bijbehorende waarschuwing. 

## <a name="action-rule-is-not-working-as-expected"></a>De actie regel werkt niet zoals verwacht 

Als u een gebrande waarschuwing in de portal kunt zien, maar een gerelateerde actie regel niet werkt zoals verwacht, volgt u deze stappen: 

1. **Is de actie regel ingeschakeld?** 

    Controleer de kolom Actie regel status om te controleren of de gerelateerde actie functie is ingeschakeld. 

    ![afbeelding](media/alerts-troubleshoot/action-rule-status.png) 

    Als deze niet is ingeschakeld, kunt u de actie regel inschakelen door deze te selecteren en op inschakelen te klikken. 

1. **Is het een service status waarschuwing?** 

    Service Health Alerts (Monitor service = "Service Health") worden niet beïnvloed door actie regels. 

1. **Is de actie regel Act op uw waarschuwing?** 

    Controleer of de actie regel uw waarschuwing heeft verwerkt door te klikken op de geactiveerde waarschuwing in de portal en Bekijk het tabblad geschiedenis.

    Hier volgt een voor beeld van een actie regel die alle actie groepen onderdrukt: 
 
     ![Geschiedenis van onderdrukking van waarschuwings actie regel](media/alerts-troubleshoot/history-action-rule.png)

    Hier volgt een voor beeld van een actie regel die een andere actie groep toevoegt:

    ![Actie herhaald in meerdere actie groepen](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Komt het bereik en filter van de actie regel overeen met de geactiveerde waarschuwing?** 

    Als u denkt dat de actie regel moet worden geactiveerd, maar niet is uitgevoerd, of dat deze niet is gestart, controleert u zorgvuldig het bereik van de actie regel en filter voorwaarden, vergeleken met de eigenschappen van de geactiveerde waarschuwing. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>De waarschuwings-id van een geactiveerde waarschuwing zoeken

Wanneer u een case opent over een specifieke gebrande waarschuwing (bijvoorbeeld: als u de e-mail melding niet hebt ontvangen), moet u de waarschuwings-id opgeven. 

Ga als volgt te werk om het bestand te zoeken:

1. Navigeer in het Azure Portal naar de lijst met geactiveerde waarschuwingen en zoek deze specifieke waarschuwing. U kunt de filters gebruiken om u te helpen bij het zoeken. 

1. Klik op de waarschuwing om de details van de waarschuwing te openen. 

1. Schuif omlaag in de waarschuwings velden van het eerste tabblad (het tabblad samen vatting) totdat u deze hebt gevonden en kopieer deze. Dit veld bevat ook de Help-knop ' kopiëren naar het klem bord ' die u kunt gebruiken.  

    ![waarschuwings-id zoeken](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Probleem bij het maken, bijwerken of verwijderen van actie regels in de Azure Portal

Als er een fout is opgetreden bij het maken, bijwerken of verwijderen van een [actie regel](alerts-action-rules.md), voert u de volgende stappen uit: 

1. **Hebt u een machtigings fout ontvangen?**  

    U moet de [ingebouwde rol voor het bijhouden van Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)hebben of de specifieke machtigingen met betrekking tot actie regels en waarschuwingen.

1. **Hebt u de para meters voor de actie regel gecontroleerd?**  

    Raadpleeg de [documentatie over de actie regel](alerts-action-rules.md)of de [actie regel Power shell set-AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) opdracht. 


## <a name="next-steps"></a>Volgende stappen
- Als u een logboek waarschuwing gebruikt, raadpleegt u ook [logboek waarschuwingen voor problemen oplossen](alert-log-troubleshoot.md).
- Ga terug naar de [Azure Portal](https://portal.azure.com) om te controleren of u het probleem hebt opgelost met bovenstaande richt lijnen 
