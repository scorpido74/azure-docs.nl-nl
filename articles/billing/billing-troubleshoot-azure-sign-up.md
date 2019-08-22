---
title: Problemen met Azure-aanmelding oplossen
description: Oplossen van een probleem bij het aanmelden voor een nieuw account in het Microsoft Azure-portal-account centrum.
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 57dc9c05778563bc0c5df1a8ac8895b51008b01f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657071"
---
# <a name="troubleshoot-azure-sign-up"></a>Problemen met Azure-aanmelding oplossen

U kunt een probleem ondervinden wanneer u zich probeert aan te melden voor een nieuw account in het Microsoft Azure-portal of het Azure-account centrum. Controleer eerst het volgende voordat u het probleem oplost:

- De gegevens die u hebt ingevoerd voor uw Azure-account profiel (inclusief het e-mail adres van de contact persoon, het adres en het telefoon nummer) zijn correct.
- De creditcard gegevens zijn juist.
- U hebt nog geen Microsoft-account met dezelfde informatie.

## <a name="resolutions"></a>Omzetting

Als u eventuele fouten wilt oplossen, selecteert u het probleem dat optreedt wanneer u zich probeert aan te melden voor Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Fout: *Kan niet door gaan met aanmelden vanwege een probleem met uw account. Neem contact op met ondersteuning voor facturering.*

Voer de volgende stappen uit om het probleem op te lossen:

1. Meld u aan bij het [Azure-account centrum](https://account.azure.com/Profile) met de referenties van de account beheerder.

2. Selecteer **Details bewerken**.

3. Zorg ervoor dat alle adres velden zijn voltooid en geldig zijn.

4. Wanneer u zich aanmeldt voor het Azure-abonnement, moet u ervoor zorgen dat het factuur adres voor de registratie van de credit card overeenkomt met uw bank records.

Als het fout bericht nog steeds wordt weer gegeven, probeert u zich aan te melden met een andere browser.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>De voortgangs balk loopt vast in de sectie *identiteits controle per kaart* .

Voor het volt ooien van de identiteits verificatie per kaart, moeten cookies van derden worden toegestaan voor uw browser.

![Identiteitverificatie via creditcard](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
Gebruik de volgende stappen om de cookie-instellingen van uw browser bij te werken.

1. Als u Chrome gebruikt, selecteert u **instellingen** >  > geavanceerde instellingen instellingen**Privacy** > -**inhoud** **weer geven**. Wis **cookies van derden en site gegevens blok keren**.

2. Als u micro soft Edge gebruikt, selecteert u **instellingen** > **Geavanceerde instellingen** > weer geven**cookies** > **blok keren cookies niet**.

3. Vernieuw de Azure-aanmeldings pagina en controleer of het probleem is opgelost.

4. Als het probleem niet is opgelost, sluit u de browser en start u deze opnieuw. Probeer het vervolgens opnieuw.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Het creditcard formulier biedt geen ondersteuning voor mijn factuur adres

Uw factuur adres moet zich in het land bevinden dat u in de sectie **over u** selecteert. Zorg ervoor dat u het juiste land selecteert.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Geen tekst berichten of aanroepen bij de verificatie van het registratie account

Hoewel het proces meestal snel is, kan het tot vier minuten duren voordat een verificatie code wordt bezorgd. Het telefoon nummer dat u voor verificatie invoert, wordt niet opgeslagen als een contact nummer voor het account.

Hier volgen enkele aanvullende tips:

- Een VoiP-telefoon nummer (Voice-over-IP) kan niet worden gebruikt voor het verificatie proces voor de telefoon.
- Dubbel Controleer het telefoon nummer dat u invoert, inclusief de land code die u in de vervolg keuzelijst selecteert.
- Als uw telefoon geen tekst berichten (SMS) ontvangt, probeert u de optie voor **bellen** .
- Zorg ervoor dat uw telefoon gesp rekken of SMS-berichten kan ontvangen van een op Verenigde Staten gebaseerd telefoon nummer.

Wanneer u het tekst bericht of telefoon gesprek ontvangt, voert u de code in die u in het tekstvak ontvangt.

### <a name="credit-card-declined-or-not-accepted"></a>De credit card is afgewezen of niet geaccepteerd

Een of meer betaalde credit cards of betaal passen worden niet geaccepteerd als betaling voor Azure-abonnementen. Zie uw betaalpas of credit card wordt [geweigerd bij Azure-aanmelding](https://support.microsoft.com/help/4042960)om te zien wat er anders kan leiden dat uw kaart wordt afgewezen.

### <a name="free-trial-is-not-available"></a>De gratis proef versie is niet beschikbaar

Hebt u in het verleden een Azure-abonnement gebruikt? De gebruiksvoorwaarden van Azure beperken de activering van de gratis proefversie alleen voor een gebruiker die nieuw is in Azure. Als u eerder een ander type Azure-abonnement hebt gehad, is het niet mogelijk om een gratis proefversie te activeren. U kunt zich aanmelden voor een [abonnement met betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Ik heb een tarief voor mijn gratis proef account gezien

U ziet mogelijk een kleine verificatie blokkering op uw creditcard account nadat u zich hebt aangemeld. Dit wordt binnen drie tot vijf dagen verwijderd. Als u zich zorgen maakt over het beheer van kosten, lees dan meer over [onverwachte kosten voor komen](billing-getting-started.md).

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Kan het Azure-vergoedings plan niet activeren zoals MSDN, BizSpark, BizSparkPlus of MPN

Zorg ervoor dat u de juiste aanmeldings referenties gebruikt. Controleer vervolgens het programma voor de vergoeding om er zeker van te zijn dat u in aanmerking komt.

- MSDN 
  - Controleer uw geschiktheids status op de pagina van uw [MSDN-account](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Als u uw status niet kunt controleren, neemt u contact op met de [klanten service centers van MSDN-abonnementen](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft voor startups
  - Meld u aan bij de [Portal van micro soft voor opstarten](https://startups.microsoft.com/#start-two) om uw geschiktheids status voor micro soft te verifiëren voor opstarten.
  - Als u uw status niet kunt verifiëren, kunt u hulp krijgen in de [forums van micro soft voor opstarten](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN 
  - Meld u aan bij de [MPN-Portal](https://mspartner.microsoft.com/Pages/Locale.aspx) om uw geschiktheids status te controleren. Als u over de juiste [bevoegdheden voor het Cloud platform](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)beschikt, komt u mogelijk in aanmerking voor extra voor delen.
  - Als u uw status niet kunt verifiëren, neemt u contact op met de [MPN-ondersteuning](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Kan geen nieuw Azure activeren in een open-abonnement

Als u een Azure in open-abonnement wilt maken, moet u een geldige OSA-sleutel (online service Activation) hebben waaraan ten minste één Azure in open token is gekoppeld. Als u geen OSA-sleutel hebt, neemt u contact op met een van de micro soft-partners die worden vermeld in [micro soft Pinpoint](http://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Fout: *U komt niet in aanmerking voor een Azure-abonnement*

Om dit probleem op te lossen, controleert u of de volgende items waar zijn:

- De gegevens die u hebt ingevoerd voor uw Azure-account profiel (inclusief het e-mail adres van de contact persoon, het adres en het telefoon nummer) zijn correct.
- De creditcard gegevens zijn juist.
- U hebt nog geen Microsoft-account die dezelfde gegevens gebruiken.

### <a name="error-your-current-account-type-is-not-supported"></a>Fout: *Uw huidige account type wordt niet ondersteund*

Dit probleem kan zich voordoen als het account is geregistreerd in een niet- [beheerde Azure AD-adres lijst](../active-directory/users-groups-roles/directory-self-service-signup.md)en niet is opgenomen in de Azure AD-Directory van uw organisatie. 

U kunt dit probleem oplossen door u aan te melden bij het Azure-account met een ander account of door de niet-beheerde AD-Directory over te nemen. Zie voor meer informatie een niet- [beheerde Directory als Administrator overnemen in azure Active Directory](../active-directory/users-groups-roles/domains-admin-takeover.md).
 
## <a name="additional-help-resources"></a>Aanvullende hulp bronnen

Andere artikelen over het oplossen van problemen met Azure-facturering en-abonnementen

- [Geweigerde kaart](billing-troubleshoot-declined-card.md)
- [Aanmeldings problemen voor abonnementen](billing-troubleshoot-sign-in-issue.md)
- [Geen abonnementen gevonden](billing-no-subscriptions-found.md)
- [Weergave voor bedrijfskosten is uitgeschakeld](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Neem contact met ons op voor hulp

Als u vragen hebt of hulp nodig hebt, [Maak een ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor Azure billing](index.md)
