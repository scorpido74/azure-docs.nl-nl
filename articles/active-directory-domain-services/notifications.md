---
title: E-mailmeldingen voor Azure AD Domain Services | Microsoft Docs'
description: Informatie over het configureren van e-mailmeldingen om u te waarschuwen voor problemen in een beheerd Azure Active Directory Domain Services-domein
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 8c6d59889e572893877f2178cade57e07aa91413
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654789"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>E-mailmeldingen configureren voor problemen in Azure Active Directory Domain Services

De status van een azure active directory domain services (Azure AD DS) beheerd domein wordt bewaakt door het Azure-platform. De statuspagina in de Azure-portal toont eventuele waarschuwingen voor het beheerde domein. Om ervoor te zorgen dat problemen tijdig worden beantwoord, kunnen e-mailmeldingen worden geconfigureerd om te rapporteren over statuswaarschuwingen zodra ze worden gedetecteerd in het beheerde Azure AD DS-domein.

In dit artikel ziet u hoe u geadresseerden voor e-mailmeldingen configureert voor een door Azure AD DS beheerd domein.

## <a name="email-notification-overview"></a>Overzicht van e-mailmeldingen

Als u wilt waarschuwen voor problemen met een door Azure AD DS beheerd domein, u e-mailmeldingen configureren. Met deze e-mailmeldingen wordt het door Azure AD DS beheerde domein opgegeven waarop de waarschuwing aanwezig is, evenals de tijd van detectie en een koppeling naar de statuspagina in de Azure-portal. U vervolgens het advies voor het oplossen van problemen volgen om de problemen op te lossen.

In het volgende voorbeeld geeft e-mailmelding aan dat er een kritieke waarschuwing of waarschuwing is gegenereerd op het beheerde Azure AD DS-domein:

![Voorbeeld e-mailmelding](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Zorg er altijd voor dat de e-mail afkomstig is van een geverifieerde Microsoft-afzender voordat u op de koppelingen in het bericht klikt. De e-mailmeldingen `azure-noreply@microsoft.com` komen altijd van het adres.

### <a name="why-would-i-receive-email-notifications"></a>Waarom zou ik e-mailmeldingen ontvangen?

Azure AD DS verzendt e-mailmeldingen voor belangrijke updates over het beheerde domein. Deze meldingen zijn alleen voor dringende problemen die van invloed zijn op de service en moeten onmiddellijk worden aangepakt. Elke e-mailmelding wordt geactiveerd door een waarschuwing op het door Azure AD DS beheerde domein. De waarschuwingen worden ook weergegeven in de Azure-portal en kunnen worden weergegeven op de [statuspagina van Azure AD DS.][check-health]

Azure AD DS verzendt geen e-mails voor advertentie-, updates- of verkoopdoeleinden.

### <a name="when-will-i-receive-email-notifications"></a>Wanneer ontvang ik e-mailmeldingen?

Er wordt onmiddellijk een melding verzonden wanneer een [nieuwe waarschuwing][troubleshoot-alerts] wordt gevonden op een door Azure AD DS beheerd domein. Als de waarschuwing niet is opgelost, worden elke vier dagen extra e-mailmeldingen verzonden als herinnering.

### <a name="who-should-receive-the-email-notifications"></a>Wie moet de e-mailmeldingen ontvangen?

De lijst met e-mailontvangers voor Azure AD DS moet bestaan uit mensen die het beheerde domein kunnen beheren en wijzigingen kunnen aanbrengen. Deze e-maillijst moet worden gezien als uw "first responders" voor eventuele waarschuwingen en problemen.

U maximaal vijf extra e-mails toevoegen voor e-mailmeldingen. Als u meer dan vijf ontvangers voor e-mailmeldingen wilt, maakt u een distributielijst en voegt u die toe aan de meldingslijst.

U er ook voor kiezen om alle *globale beheerders* van de Azure AD-map te laten ontvangen en elk lid van de *aad dc-beheerdersgroep* e-mailmeldingen te ontvangen. Azure AD DS verzendt alleen meldingen naar maximaal 100 e-mailadressen, waaronder de lijst met globale beheerders en AAD DC-beheerders.

## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

Voer de volgende stappen uit om de bestaande ontvangers van e-mailmeldingen te controleren of extra geadresseerden toe te voegen:

1. Zoek en selecteer Azure AD Domain Services in de **Azure-portal.**
1. Selecteer uw door Azure AD DS beheerde domein, zoals *aaddscontoso.com*.
1. Selecteer aan de linkerkant van het Azure AD DS-bronvenster de optie **Meldingsinstellingen**. De bestaande ontvangers voor e-mailmeldingen worden weergegeven.
1. Als u een e-mailontvanger wilt toevoegen, voert u het e-mailadres in de tabel met extra geadresseerden in.
1. Als u klaar bent, selecteert **u Opslaan** op de navigatie met de bovenste hand.

> [!WARNING]
> Wanneer u de meldingsinstellingen wijzigt, worden de meldingsinstellingen voor het gehele door Azure AD DS beheerde domein bijgewerkt, niet alleen uzelf.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Ik heb een e-mailmelding ontvangen voor een waarschuwing, maar toen ik inlogde op de Azure-portal was er geen waarschuwing. Wat is er gebeurd?

Als een waarschuwing is opgelost, wordt de waarschuwing gewist uit de Azure-portal. De meest waarschijnlijke reden is dat iemand anders die e-mailmeldingen ontvangt, de waarschuwing heeft opgelost op het door Azure AD DS beheerde domein of dat deze automatisch is opgelost door het Azure-platform.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Waarom kan ik de meldingsinstellingen niet bewerken?

Als u geen toegang hebt tot de pagina met meldingsinstellingen in de Azure-portal, hebt u niet de machtigingen om het beheerde Azure AD DS-beheerde domein te bewerken. U moet contact opnemen met een globale beheerder om machtigingen te krijgen voor het bewerken van Azure AD DS-bron of uit de lijst met geadresseerden te worden verwijderd.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Ik lijk geen e-mailmeldingen te ontvangen, ook al heb ik mijn e-mailadres opgegeven. Hoe komt dat?

Controleer uw spam of ongewenste map in uw e-mail `azure-noreply@microsoft.com`voor de melding en zorg ervoor dat de afzender van .

## <a name="next-steps"></a>Volgende stappen

Zie [Waarschuwingen oplossen op een door Azure AD DS beheerd domein voor][troubleshoot-alerts]meer informatie over het oplossen van problemen.

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
