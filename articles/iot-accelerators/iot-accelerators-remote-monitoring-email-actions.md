---
title: E-mailactie binnen bewaking op afstand - Azure | Microsoft Documenten
description: In deze handleiding ziet u hoe u een e-mailactie toevoegt aan een nieuwe of bestaande regel.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: c192ba73da0cfaf1832b6a1e572bd71b250a976b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168359"
---
# <a name="add-an-email-action"></a>Een e-mailactie toevoegen

E-mailacties zorgen ervoor dat u waarschuwingen nooit mist. U een e-mailactie toevoegen aan een bestaande regel of wanneer u een nieuwe regel maakt.

Als u de stappen in deze handleiding wilt uitvoeren, hebt u een geïmplementeerd exemplaar van de remote monitoring-oplossingsversneller in uw Azure-abonnement nodig.

Als u een regel wilt maken of wijzigen, moet u beheerder zijn [ **Administrator**of over de juiste machtigingen beschikken.](iot-accelerators-remote-monitoring-rbac.md)

## <a name="edit-an-existing-rule"></a>Een bestaande rol bewerken

Volg de volgende stappen om een e-mailactie toe te voegen aan een bestaande regel:

1. Navigeer naar uw oplossing voor externe bewaking.

1. Navigeer in het **dashboard**naar de pagina **Regels:**

    ![Pagina Regels](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Klik op het selectievakje naast de bestaande regel om te wijzigen en klik bovenaan op **Bewerken.** Er verschijnt een bewerkbaar **regelpaneel.**

1. Schakel in de sectie **Actie** **e-mail in ingeschakeld** voor **Aan**.

1. De eerste keer dat u een e-mailactie inschakelt in de oplossingsversneller, moet u [zich aanmelden bij Outlook.](#outlook)

1. Voer een e-mailadres in het vak geadresseerde in en druk op **de** enter-toets voor elk e-mailadres om toe te voegen:

    ![Adresvermelding](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Voer een onderwerp in voor de e-mail.

1. Voer eventuele aanvullende notities voor de e-mailontvangers in als platte tekst. U HTML-opmaak gebruiken als u [de e-mailsjabloon bewerkt.](#htmledit)

1. Controleer of de **regelstatus** is ingesteld op **Ingeschakeld**.

1. Klik op **Toepassen**.

## <a name="create-a-new-rule"></a>Een nieuwe regel maken

Volg deze stappen om een e-mailactie toe te voegen wanneer u een nieuwe regel maakt:

1. Navigeer naar uw oplossing voor externe bewaking.

1. Navigeer in het **dashboard**naar de pagina **Regels:**

    ![Pagina Regels](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Volg de stappen in de [sectie Een regel maken](iot-accelerators-remote-monitoring-automate.md#create-a-rule). De stappen volgen in de [sectie Een geavanceerde regel maken](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) tot het punt waarop u een **ernstniveau instelt.** Klik nog niet op **Toepassen.**

1. Schakel in de sectie **Actie** **e-mail in ingeschakeld** voor **Aan**.

1. De eerste keer dat u een e-mailactie inschakelt in de oplossingsversneller, moet u [zich aanmelden bij Outlook.](#outlook)

1. Voer een e-mailadres in het vak geadresseerde in en druk op **de** enter-toets voor elk e-mailadres om toe te voegen:

    ![Adresvermelding](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Voer een onderwerp in voor de e-mail.

1. Voer eventuele aanvullende notities voor de e-mailontvangers in als platte tekst. U HTML-opmaak gebruiken als u [de e-mailsjabloon bewerkt.](#htmledit)

1. Controleer of de **regelstatus** is ingesteld op **Ingeschakeld**.

1. Klik op **Toepassen**.

Uw regel met een e-mailactie is nu ingeschakeld. Telkens wanneer de actie wordt geactiveerd, wordt een nieuwe e-mail naar de ontvangers verzonden.

## <a name="sign-in-to-outlook"></a>Aanmelden bij Outlook<a name="outlook"></a>

De eerste keer dat u een e-mailactie inschakelt in uw oplossingsversneller, moet u zich aanmelden bij Outlook. Met deze actie wordt het e-mailaccount ingesteld dat de e-mailmeldingen verzendt.

> [!NOTE]
> U moet een specifiek Outlook-account maken alleen voor meldingen van oplossingsversnellers en dat account gebruiken wanneer u uw eerste e-mailactie inschakelt.

### <a name="contributor-role-outlook-setup"></a>Outlook-instelling voor inzenderrol

Als iemand in de **rol van bijdragers** in het abonnement de oplossingsversneller heeft geïmplementeerd, beschikt de toepassing niet over voldoende machtigingen om e-mailacties in te stellen en te verifiëren via de webgebruikersinterface.

Maak voordat u begint een Outlook-account om e-mailmeldingen te verzenden vanaf uw oplossingsversneller.

In de volgende stappen ziet u hoe u de e-mailacties handmatig instellen en verifiëren:

1. Navigeer naar de [Azure-portal](https://portal.azure.com).

1. Navigeer naar de resourcegroep voor uw oplossingsversneller.

1. Klik op de **office365-connector:**

    ![API-verbinding](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Klik op de banner om het autorisatieproces te starten:

    ![autoriseren](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Klik **op Autoriseren**. Je wordt gevraagd je aan te melden. Het account dat u gebruikt om u aan te melden, moet het e-mailadres zijn dat de toepassing gebruikt om e-mailmeldingen te verzenden:

    ![Knop Autoriseren](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Klik onderaan op **Opslaan.** Uw autorisatie zal succesvol zijn als de banner is verdwenen.

1. Als u het e-mailadres wilt wijzigen van waaruit de meldingen worden verzonden, klikt u op **API-verbinding bewerken**.

    ![e-mail wijzigen](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Outlook-instelling voor eigenaarrol

Als iemand in de **eigenaarrol** in het abonnement de oplossingsversneller heeft geïmplementeerd, kan de toepassing controleren of e-mailacties correct zijn ingesteld via de webgebruikersinterface.

Maak voordat u begint een Outlook-account om e-mailmeldingen te verzenden vanaf uw oplossingsversneller.

Met de volgende stappen u zich aanmelden en e-mailacties instellen:

1. Klik hier om u aan te melden bij Outlook. U wordt naar de Azure-portal gebracht:

   ![Aanmelden bij Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Klik **op Autoriseren**. Je wordt gevraagd je aan te melden. Het account dat u gebruikt om u aan te melden, moet het e-mailadres zijn dat de toepassing gebruikt om e-mailmeldingen te verzenden:

1. Klik op **Opslaan**. Ga terug naar uw oplossingsversneller en vernieuw de pagina.

1. Als u de e-mailmelding hebt geconfigureerd, ziet u dit bericht:

   ![Succesvol Outlook-aanmelding](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## <a name="customize-the-email-html"></a>De e-mail-HTML aanpassen<a name="htmledit"></a>

De Remote Monitoring-oplossingsversneller biedt een eenvoudige HTML-sjabloon voor actie-e-mails. De e-mailsjabloon gebruikt waarden uit de instellingen voor e-mailactie. Hier is een voorbeeld e-mail:

![voorbeeld van e-mail](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

In de volgende stappen ziet u hoe u de HTML-e-mailsjabloon bewerkt. U bijvoorbeeld meer informatie opnemen of aangepaste afbeeldingen toevoegen:

1. Kloon de Java- of .NET Remote Monitoring GitHub-repository:

1. Navigeer naar de locatie van de e-mailsjabloon:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. U parameters in deze sjabloon toevoegen of verwijderen om het bericht aan te passen. U oproepen ook toevoegen, verwijderen of vervangen als dat nodig is:

    Bijvoorbeeld in de .NET-code:`emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Bijvoorbeeld in de Java-code:`this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parameters in de sjabloon `${...}`hebben de vorm van . Als u een parameter wilt verwijderen, verwijdert u de vereiste regel. Als u een parameter wilt toevoegen, voegt u een regel toe met de waarde die u wilt invoegen.

1. Als u afbeeldingen of aangepaste tekst wilt toevoegen, werkt u het bestand EmailTemplate.HTML rechtstreeks bij.

## <a name="throttling"></a>Beperking

De oplossingsversneller voor externe bewaking gebruikt Outlook om e-mailmeldingen te verzenden. Outlook beperkt het aantal e-mails dat wordt verzonden tot [30 e-mails per 1 minuut.](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits) E-mailclients die de e-mails ontvangen, kunnen ook het aantal ontvangen e-mails per minuut beperken. Neem contact op met uw specifieke e-mailclient op beperkingen. Wanneer u e-mailmelding instelt voor een regel, moet de regel de gemiddelde waarden over een periode van ten minste één minuut berekenen en geen directe waarden gebruiken:

![Gemiddelde berekening](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding ziet u hoe u een e-mailactie toevoegt aan een nieuwe of bestaande regel binnen een oplossing voor externe bewaking. De handleiding liet u ook zien en hoe u de HTML bewerken die de berichtindeling definieert.

De voorgestelde volgende stap is om te leren [hoe u waarschuwingen gebruiken en apparaatproblemen oplossen.](iot-accelerators-remote-monitoring-maintain.md)
