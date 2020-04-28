---
title: E-mail actie binnen externe controle-Azure | Microsoft Docs
description: In deze hand leiding wordt uitgelegd hoe u een e-mail actie kunt toevoegen aan een nieuwe of bestaande regel.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: c192ba73da0cfaf1832b6a1e572bd71b250a976b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74168359"
---
# <a name="add-an-email-action"></a>Een e-mail actie toevoegen

Met e-mail acties kunt u ervoor zorgen dat u nooit meldingen mist. U kunt een e-mail actie toevoegen aan een bestaande regel of wanneer u een nieuwe regel maakt.

Als u de stappen in deze hand leiding wilt uitvoeren, hebt u een geïmplementeerd exemplaar van de Accelerator voor externe controle in uw Azure-abonnement nodig.

Als u een regel wilt maken of wijzigen, moet u een [ **beheerder**zijn of de juiste machtigingen hebben](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Een bestaande rol bewerken

Volg deze stappen om een e-mail actie toe te voegen aan een bestaande regel:

1. Navigeer naar uw oplossing voor externe controle.

1. Ga in het **dash board**naar de pagina **regels** :

    ![Pagina Regels](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Klik op het selectie vakje naast de bestaande regel die u wilt wijzigen en klik vervolgens bovenaan op **bewerken** . Een Bewerk bare **regel** paneel wordt weer gegeven.

1. Schakel in de sectie **actie** de optie **e-mail** in **op aan in**.

1. De eerste keer dat u een e-mail actie in de oplossings versneller inschakelt, moet u [zich aanmelden bij Outlook](#outlook).

1. Voer een e-mail adres in het vak ontvanger in en druk op **Enter** voor elk e-mail adres dat u wilt toevoegen:

    ![Adres item](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Voer een onderwerp in voor het e-mail bericht.

1. Voer eventuele aanvullende notities voor de e-mail ontvangers in als tekst zonder opmaak. U kunt HTML-opmaak gebruiken als u [de e-mail sjabloon bewerkt](#htmledit).

1. Zorg ervoor dat de **regel status** is ingesteld op **ingeschakeld**.

1. Klik op **Toepassen**.

## <a name="create-a-new-rule"></a>Een nieuwe regel maken

Volg deze stappen om een e-mail actie toe te voegen wanneer u een nieuwe regel maakt:

1. Navigeer naar uw oplossing voor externe controle.

1. Ga in het **dash board**naar de pagina **regels** :

    ![Pagina Regels](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Volg de stappen in de [sectie een regel maken](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Volg de stappen in de sectie [een geavanceerde regel maken](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) tot het punt waar u een **Ernst niveau**hebt ingesteld. Klik nog niet op **Toep assen** .

1. Schakel in de sectie **actie** de optie **e-mail** in **op aan in**.

1. De eerste keer dat u een e-mail actie in de oplossings versneller inschakelt, moet u [zich aanmelden bij Outlook](#outlook).

1. Voer een e-mail adres in het vak ontvanger in en druk op **Enter** voor elk e-mail adres dat u wilt toevoegen:

    ![Adres item](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Voer een onderwerp in voor het e-mail bericht.

1. Voer eventuele aanvullende notities voor de e-mail ontvangers in als tekst zonder opmaak. U kunt HTML-opmaak gebruiken als u [de e-mail sjabloon bewerkt](#htmledit).

1. Zorg ervoor dat de **regel status** is ingesteld op **ingeschakeld**.

1. Klik op **Toepassen**.

Uw regel met een e-mail actie is nu ingeschakeld. Telkens wanneer de actie wordt geactiveerd, wordt er een nieuw e-mail bericht naar de ontvangers verzonden.

## <a name="sign-in-to-outlook"></a>Aanmelden bij Outlook<a name="outlook"></a>

De eerste keer dat u een e-mail actie in uw oplossings versneller inschakelt, moet u zich aanmelden bij Outlook. Met deze actie wordt het e-mail account ingesteld waarmee de e-mail meldingen worden verzonden.

> [!NOTE]
> U moet alleen een specifiek Outlook-account maken voor meldingen van de oplossings versnelling en dat account gebruiken wanneer u uw eerste e-mail actie inschakelt.

### <a name="contributor-role-outlook-setup"></a>Outlook-installatie van rol van Inzender

Als iemand in de rol **Inzender** in het abonnement de oplossings versneller heeft geïmplementeerd, is de toepassing niet gemachtigd om e-mail acties in te stellen en te controleren via de webgebruikersinterface.

Voordat u begint, moet u een Outlook-account maken dat u kunt gebruiken om e-mail meldingen van uw oplossings versneller te verzenden.

De volgende stappen laten zien hoe u de e-mail acties hand matig kunt instellen en verifiëren:

1. Navigeer naar het [Azure Portal](https://portal.azure.com).

1. Navigeer naar de resource groep voor uw oplossings versneller.

1. Klik op de **office365-connector**:

    ![API-verbinding](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Klik op de banner om het autorisatie proces te starten:

    ![autoriseren](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Klik op **autoriseren**. U wordt gevraagd om u aan te melden. Het account waarmee u zich aanmeldt, moet het e-mail adres zijn dat de toepassing gebruikt voor het verzenden van e-mail meldingen:

    ![Knop autoriseren](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Klik onderaan op **Opslaan** . Uw autorisatie slaagt als de banner is verdwenen.

1. Als u het e-mail adres wilt wijzigen van waaruit de meldingen worden verzonden, klikt u op **API-verbinding bewerken**.

    ![e-mail wijzigen](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Outlook-installatie van de rol van eigenaar

Als iemand in de rol **eigenaar** van het abonnement de oplossings versneller heeft geïmplementeerd, kan de toepassing controleren of e-mail acties correct zijn ingesteld via de webgebruikersinterface.

Voordat u begint, moet u een Outlook-account maken dat u kunt gebruiken om e-mail meldingen van uw oplossings versneller te verzenden.

Met de volgende stappen kunt u zich aanmelden en e-mail acties instellen:

1. Klik om u aan te melden bij Outlook. U gaat naar de Azure Portal:

   ![Aanmelden bij Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Klik op **autoriseren**. U wordt gevraagd om u aan te melden. Het account waarmee u zich aanmeldt, moet het e-mail adres zijn dat de toepassing gebruikt voor het verzenden van e-mail meldingen:

1. Klik op **Opslaan**. Ga terug naar de oplossings versneller en vernieuw de pagina.

1. Als u de e-mail melding hebt geconfigureerd, ziet u dit bericht:

   ![Geslaagde aanmelding bij Outlook](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## <a name="customize-the-email-html"></a>De HTML-e-mail aanpassen<a name="htmledit"></a>

Out-of-the-box biedt de oplossings versneller voor externe controle een eenvoudige HTML-sjabloon voor actie-e-mails. De e-mail sjabloon gebruikt waarden uit de instellingen voor de e-mail actie. Hier volgt een voor beeld van een e-mail bericht:

![voor beeld van e-mail](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

De volgende stappen laten zien hoe u de HTML-e-mail sjabloon kunt bewerken. U kunt bijvoorbeeld meer informatie bevatten of aangepaste installatie kopieën toevoegen:

1. Kloon de Java-of .NET-GitHub-opslag plaats voor externe bewaking:

1. Ga naar de locatie van de e-mail sjabloon:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. U kunt de para meters in deze sjabloon toevoegen of verwijderen om het bericht aan te passen. U kunt indien nodig ook aanroepen toevoegen, verwijderen of vervangen:

    Bijvoorbeeld in de .NET-code:`emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Bijvoorbeeld in de Java-code:`this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. De para meters in de sjabloon hebben `${...}`de vorm van. Als u een para meter wilt verwijderen, moet u de vereiste regel verwijderen. Als u een para meter wilt toevoegen, voegt u een regel toe met de waarde die u wilt invoegen.

1. Als u afbeeldingen of aangepaste tekst wilt toevoegen, moet u het bestand email template. HTML rechtstreeks bijwerken.

## <a name="throttling"></a>Beperking

De oplossings versneller voor externe controle gebruikt Outlook voor het verzenden van e-mail meldingen. Outlook beperkt het aantal e-mails dat wordt verzonden naar [30 e-mails per 1 minuut](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). E-mailclients die de e-mail berichten ontvangen, kunnen ook het aantal e-mails dat per minuut wordt ontvangen, beperken. Neem contact op met uw specifieke e-mailclient voor beperkingen. Wanneer u een e-mail melding voor een regel instelt, moet de regel gemiddelde waarden berekenen gedurende een periode van ten minste één minuut, en geen directe waarden gebruiken:

![Gemiddelde berekening](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding wordt uitgelegd hoe u een e-mail actie kunt toevoegen aan een nieuwe of bestaande regel in een oplossing voor externe controle. De hand leiding heeft ook geleerd hoe u de HTML-code die de bericht indeling definieert, kunt bewerken.

De voorgestelde volgende stap is informatie over [het gebruik van waarschuwingen en het oplossen van problemen met apparaten](iot-accelerators-remote-monitoring-maintain.md).
