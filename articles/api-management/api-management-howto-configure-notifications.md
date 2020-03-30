---
title: Meldingen en e-mailsjablonen configureren
titleSuffix: Azure API Management
description: Meer informatie over het configureren van meldingen en e-mailsjablonen in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 786a9e26003a7afb98307e0bd7fae94c42a2f00d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244067"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Meldingen en e-mailsjablonen configureren in Azure API Management

API-beheer biedt de mogelijkheid om meldingen voor specifieke gebeurtenissen te configureren en de e-mailsjablonen te configureren die worden gebruikt om te communiceren met de beheerders en ontwikkelaars van een API-beheerexemplaar. In dit artikel ziet u hoe u meldingen voor de beschikbare gebeurtenissen configureert en een overzicht van het configureren van de e-mailsjablonen die voor deze gebeurtenissen worden gebruikt.

## <a name="prerequisites"></a>Vereisten

Als u geen API Management-serviceinstantie hebt, voert u de volgende quickstart uit: [Een instantie voor Azure API Management maken.](get-started-create-service-instance.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>Meldingen configureren

1.  Selecteer uw **api-beheerexemplaar.**
2.  Klik **op Meldingen** om de beschikbare meldingen weer te geven.

    ![Publisher-meldingen][api-management-publisher-notifications]

    De volgende lijst met gebeurtenissen kan worden geconfigureerd voor meldingen.

    -   **Abonnementsaanvragen (waarvoor goedkeuring vereist is)** - De opgegeven e-mailontvangers en -gebruikers ontvangen e-mailmeldingen over abonnementsaanvragen voor API-producten waarvoor goedkeuring vereist is.
    -   **Nieuwe abonnementen** - De opgegeven e-mailontvangers en -gebruikers ontvangen e-mailmeldingen over nieuwe API-productabonnementen.
    -   **Aanvragen voor toepassingsgalerie** - De opgegeven e-mailontvangers en -gebruikers ontvangen e-mailmeldingen wanneer nieuwe aanvragen worden ingediend bij de toepassingsgalerie.
    -   **BCC** - De opgegeven e-mailontvangers en -gebruikers ontvangen blinde carbon kopieën van e-mail van alle e-mails die naar ontwikkelaars worden verzonden.
    -   **Nieuw probleem of opmerking** - De opgegeven e-mailontvangers en -gebruikers ontvangen e-mailmeldingen wanneer een nieuw probleem of opmerking wordt ingediend op de ontwikkelaarsportal.
    -   **Accountbericht sluiten** : de opgegeven e-mailontvangers en -gebruikers ontvangen e-mailmeldingen wanneer een account wordt gesloten.
    -   **Limiet voor abonnementsquota naderen** : de volgende e-mailontvangers en -gebruikers ontvangen e-mailmeldingen wanneer het gebruik van een abonnement in de buurt komt van het gebruiksquotum.

        > [!NOTE]
        > Meldingen worden alleen geactiveerd door het [quotum door abonnementsbeleid.](api-management-access-restriction-policies.md#SetUsageQuota) [Quota op sleutelbeleid](api-management-access-restriction-policies.md#SetUsageQuotaByKey) genereert geen meldingen.

    Voor elk evenement u e-mailontvangers opgeven via het tekstvak e-mailadres of gebruikers selecteren uit een lijst.

3.  Als u de e-mailadressen wilt opgeven die moeten worden aangemeld, voert u deze in het tekstvak e-mailadres in. Als u meerdere e-mailadressen hebt, scheidt u deze met komma's.

    ![Ontvangers van meldingen][api-management-email-addresses]

4.  Klik op **Toevoegen**.

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>Meldingssjablonen configureren

API Management biedt meldingssjablonen voor de e-mailberichten die worden verzonden tijdens het beheren en gebruiken van de service. De volgende e-mailsjablonen worden verstrekt.

-   Aanvraag galerij indiening goedgekeurd
-   De afscheidsbrief van de ontwikkelaar
-   Quotumlimiet voor ontwikkelaars nadert melding
-   Gebruiker uitnodigen
-   Nieuwe opmerking toegevoegd aan een probleem
-   Nieuw nummer ontvangen
-   Nieuw abonnement geactiveerd
-   Vernieuwde bevestiging van abonnement
-   Abonnementaanvraag wordt afgenomen
-   Inschrijvingsaanvraag ontvangen

Deze sjablonen kunnen naar wens worden gewijzigd.

Als u de e-mailsjablonen voor uw API-beheerexemplaar wilt weergeven en configureren, klikt u op **Meldingensjablonen**.

![E-mailsjablonen][api-management-email-templates]

Elke e-mailsjabloon heeft een onderwerp in platte tekst en een hoofddefinitie in HTML-indeling. Elk item kan naar wens worden aangepast.

![E-mailsjablooneditor][api-management-email-template]

De lijst **Parameters** bevat een lijst met parameters, die bij invoegen in het onderwerp of de behuizing, de aangewezen waarde wordt vervangen wanneer de e-mail wordt verzonden. Als u een parameter wilt invoegen, plaatst u de cursor op de plaats waar u de parameter wilt laten gaan en klikt u op de pijl links van de parameternaam.

> [!NOTE]
> De parameters worden niet vervangen door werkelijke waarden bij het bekijken of verzenden van een test.

Als u de wijzigingen in de e-mailsjabloon wilt opslaan, klikt u op **Opslaan**of annuleert u de wijzigingen op **Verwijderen**.

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md
