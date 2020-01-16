---
title: Problemen met Azure-aanmelding oplossen
description: Een probleem oplossen bij aanmelding voor een nieuw account in het Microsoft Azure Portal-accountcentrum.
services: billing
author: v-miegge
manager: dcscontentpm
editor: v-jesits
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: e13a55143e554a55a2902fc2350f6fde6a8afb09
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989291"
---
# <a name="troubleshoot-azure-sign-up"></a>Problemen met Azure-aanmelding oplossen

Er kan een probleem optreden wanneer u zich in Microsoft Azure Portal of het Azure-accountcentrum probeert aan te melden voor een nieuw account. Controleer het volgende voordat u het probleem gaat oplossen:

- De gegevens die u hebt opgegeven voor uw Azure-accountprofiel (zoals het e-mailadres, adres en telefoonnummer van de contactpersoon), zijn correct.
- De creditcardgegevens zijn correct.
- U hebt geen ander Microsoft-account met dezelfde gegevens.

## <a name="resolutions"></a>Oplossingen

Selecteer het probleem dat optreedt bij aanmelding voor Azure om fouten te corrigeren.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Fout: *kan niet door gaan met aanmelden vanwege een probleem met uw account. Neem contact op met ondersteuning voor facturering.*

Volg deze stappen om het probleem op te lossen:

1. Meld u aan bij het [Azure-accountcentrum](https://account.azure.com/Profile) met de accountbeheerdersreferenties.

2. Selecteer **Details bewerken**.

3. Zorg ervoor dat alle adresvelden zijn ingevuld en geldig zijn.

4. Wanneer u zich aanmeldt voor het Azure-abonnement, moet u ervoor zorgen dat het factuuradres voor de creditcardregistratie overeenkomt met uw bankgegevens.

Als het foutbericht nog steeds wordt weer gegeven, probeert u zich aan te melden in een andere browser.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>De voortgangsbalk loopt vast in de sectie *Identiteitverificatie via creditcard*.

U moet in uw browser cookies van derden toestaan om de identiteitverificatie via creditcard te voltooien.

![Identiteitverificatie via creditcard](./media/troubleshoot-azure-sign-up/identify-verification-by-card.png)

Voer de volgende stappen uit om de cookie-instellingen van uw browser bij te werken.

1. Als u Chrome gebruikt, selecteert u **Instellingen** > **Geavanceerde instellingen weergeven** > **Privacy** > **Inhoudsinstellingen**. Wis **Cookies en sitegegevens van derden blokkeren**.

2. Als u Microsoft Edge gebruikt, selecteert u **Instellingen** > **Geavanceerde instellingen weergeven** > **Cookies** > **Geen cookies blokkeren**.

3. Vernieuw de Azure-aanmeldingspagina en controleer of het probleem is opgelost.

4. Als het probleem niet is opgelost, sluit u de browser en start u deze opnieuw op. Probeer het vervolgens opnieuw.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Het creditcardformulier biedt geen ondersteuning voor mijn factuuradres

Uw factuuradres moet zich bevinden in het land dat u selecteert in de sectie **Over u**. Zorg ervoor dat u het juiste land selecteert.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Geen tekstberichten of oproepen bij de verificatie van de accountaanmelding

Hoewel het proces meestal snel gaat, kan het tot vier minuten duren voordat er een verificatiecode wordt verstrekt. Het telefoonnummer dat u voor verificatie invoert, wordt niet opgeslagen als contactnummer voor het account.

Hier volgen enkele aanvullende tips:

- Een VoiP-telefoonnummer (Voice-over-IP) kan niet worden gebruikt voor het telefoonverificatieproces.
- Controleer het telefoonnummer dat u invoert, inclusief het landnummer dat u in de vervolgkeuzelijst selecteert.
- Als u geen tekstberichten (sms) op uw telefoon ontvangt, probeert u de optie **Mij bellen**.
- Zorg ervoor dat uw telefoon oproepen of sms-berichten kan ontvangen van een telefoonnummer uit de Verenigde Staten.

Wanneer u het sms-bericht of de oproep ontvangt, voert u in het tekstvak die code in die u hebt ontvangen.

### <a name="credit-card-declined-or-not-accepted"></a>De creditcard wordt afgewezen of niet geaccepteerd

Virtuele of prepaidcreditcards of -debitcards worden niet geaccepteerd als betaalmethode voor Azure-abonnementen. Controleer of uw debitcard of creditcard is [geweigerd bij Azure-aanmelding](https://support.microsoft.com/help/4042960) om te zien waardoor uw kaart nog meer kan zijn afgewezen.

### <a name="free-trial-is-not-available"></a>Er is geen gratis proefversie beschikbaar

Hebt u in het verleden een Azure-abonnement gebruikt? De gebruiksvoorwaarden van Azure beperken de activering van de gratis proefversie alleen voor een gebruiker die nieuw is in Azure. Als u eerder een ander type Azure-abonnement hebt gehad, is het niet mogelijk om een gratis proefversie te activeren. Overweeg u aan te melden voor een [abonnement voor Betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Ik heb kosten gezien voor mijn gratis proefaccount

U kunt na aanmelding een kleine verificatiepost voor uw creditcardaccount aantreffen. Deze wordt binnen drie tot vijf dagen verwijderd. Lees meer over [Onverwachte kosten voorkomen](getting-started.md) als u zich zorgen maakt over het kostenbeheer.

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Kan het Azure-vergoedingsplan, bijvoorbeeld MSDN, BizSpark, BizSparkPlus of MPN, niet activeren

Zorg ervoor dat u de juiste aanmeldingsreferenties gebruikt. Controleer vervolgens het voordeelprogramma om na te gaan of u wel in aanmerking komt.

- MSDN
  - Controleer uw geschiktheidsstatus op uw [MSDN-accountpagina](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Als u uw status niet kunt controleren, neemt u contact op met de [klantenservicecenters voor MSDN-abonnementen](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft voor startups
  - Meld u aan bij de [Microsoft for Startups-portal](https://startups.microsoft.com/#start-two) om uw geschiktheidsstatus voor Microsoft te verifiëren voor opstarten.
  - Als u uw status niet kunt verifiëren, kunt u ondersteuning krijgen via de [Microsoft for Startups-forums](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Meld u aan bij de [MPN-portal](https://mspartner.microsoft.com/Pages/Locale.aspx) om uw geschiktheidsstatus te controleren. Als u over de juiste [competenties voor het cloudplatform](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx) beschikt, komt u mogelijk in aanmerking voor extra voordelen.
  - Als u uw status niet kunt verifiëren, neemt u contact op met de [MPN-ondersteuning](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Kan geen nieuw Azure In Open-abonnement activeren

Als u een Azure In Open-abonnement wilt maken, moet u een geldige OSA-sleutel (Online Service Activation) hebben waaraan ten minste één Azure In Open-token is gekoppeld. Als u geen OSA-sleutel hebt, neemt u contact op met een van de Microsoft-partners die worden vermeld in [Microsoft Pinpoint](https://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Fout: *u komt niet in aanmerking voor een Azure-abonnement*

U kunt dit probleem oplossen door het volgende te controleren:

- De gegevens die u hebt opgegeven voor uw Azure-accountprofiel (zoals het e-mailadres, adres en telefoonnummer van de contactpersoon), zijn correct.
- De creditcardgegevens zijn correct.
- U hebt geen ander Microsoft-account waarvoor dezelfde gegevens worden gebruikt.

### <a name="error-your-current-account-type-is-not-supported"></a>Fout: *uw huidige account type wordt niet ondersteund*

Dit probleem kan zich voordoen als het account is geregistreerd in een [niet-beheerde Azure AD-directory](../../active-directory/users-groups-roles/directory-self-service-signup.md) en niet is opgenomen in de Azure AD-directory van uw organisatie.

U kunt dit probleem oplossen door het Azure-account aan te melden met behulp van een ander account of door de niet-beheerde AD-directory over te nemen. Zie [Een niet-beheerde directory overnemen als beheerder in Azure Active Directory](../../active-directory/users-groups-roles/domains-admin-takeover.md) voor meer informatie.

## <a name="additional-help-resources"></a>Aanvullende ondersteuningsresources

Andere artikelen met probleemoplossingen voor Azure Billing en abonnementen

- [Geweigerde kaart](troubleshoot-declined-card.md)
- [Aanmeldingsproblemen voor abonnementen](troubleshoot-sign-in-issue.md)
- [Geen abonnementen gevonden](no-subscriptions-found.md)
- [Weergave voor bedrijfskosten is uitgeschakeld](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contact met ons opnemen voor ondersteuning

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor Azure Billing](../../billing/index.md)
