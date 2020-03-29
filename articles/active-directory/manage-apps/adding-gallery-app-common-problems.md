---
title: Probleem met het toevoegen van een Azure AD Gallery-toepassing | Microsoft Documenten
description: Inzicht krijgen in de veelvoorkomende problemen waarmee mensen worden geconfronteerd bij het toevoegen van Azure AD Gallery-toepassingen en wat u doen om deze op te lossen
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2b42880f99f3e87d75854166047896860f9eb14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784431"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Probleem met het toevoegen van een Azure AD Gallery-toepassing

In dit artikel u inzicht krijgen in de veelvoorkomende problemen waarmee mensen worden geconfronteerd bij het toevoegen van Azure AD Gallery-toepassingen en wat u doen om deze op te lossen.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Ik klikte op de "add" knop en mijn aanvraag duurde een lange tijd om te verschijnen

Onder bepaalde omstandigheden kan het 1-2 minuten (en soms langer) duren voordat een toepassing wordt weergegeven nadat deze aan uw directory is toegevoegd. Hoewel dit niet de normale verwachte prestaties is, u zien dat de toevoeging van de toepassing aan de gang is door te klikken op het pictogram **Meldingen** (de bel) in de rechterbovenhoek van de [Azure-portal](https://portal.azure.com/) en op zoek bent naar een **melding in uitvoering** of **voltooid** met het label Toevoegen **van toepassing.**

Als uw toepassing nooit wordt toegevoegd of als u een fout tegenkomt wanneer u op de knop **Toevoegen** klikt, ziet u een **melding** in de **status Fout.** Als u meer informatie wilt over de fout om meer te weten te komen of te delen met een ondersteuningstechnicus, u meer informatie over de fout bekijken door de stappen in de sectie [Hoe u de details van een portalmelding zien.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Ik klikte op de knop "toevoegen" en mijn toepassing is niet weergegeven

Soms, als gevolg van tijdelijke problemen, netwerkproblemen of een bug, mislukt het toevoegen van een toepassing. U zien dat dit gebeurt wanneer u op het pictogram **Meldingen** (de bel) rechtsboven in de Azure-portal klikt en u een rood (!) pictogram ziet naast de melding van de **toepassing toevoegen.** Dit geeft aan dat er een fout is opgetreden bij het maken van de toepassing.

Als u een fout tegenkomt wanneer u op de knop **Toevoegen** klikt, wordt een **melding** in een **foutstatus** weergegeven. Als u meer informatie wilt over de fout om meer te weten te komen of te delen met een ondersteuningstechnicus, u meer informatie over de fout bekijken door de stappen in de sectie [Hoe u de details van een portalmelding zien.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Ik weet niet hoe ik mijn aanvraag moet instellen zodra ik deze heb toegevoegd

Als u hulp nodig hebt bij het leren over toepassingen, is de [lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory-artikel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) een goede plek om te beginnen.

Daarnaast helpt de [Documentbibliotheek azure AD-toepassingen](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) u om meer te weten te komen over eenmalige aanmelding met Azure AD en hoe het werkt.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>De details van een portalmelding bekijken

U de details van een portalmelding bekijken door de onderstaande stappen te volgen:

1.  Het pictogram **Meldingen** (de bel) rechtsboven in de Azure-portal selecteren

2.  Selecteer een melding in een **foutstatus** (meldingen met een rode (!) ernaast).

    >[!NOTE]
    >U niet op meldingen klikken in de status **Geslaagd** of **In uitvoering.**
    >
    >

4.  Gebruik de informatie onder **Meldingsgegevens** om meer details over het probleem te begrijpen.

5.  Als u nog steeds hulp nodig hebt, u deze informatie ook delen met een ondersteuningstechnicus of de productgroep om hulp te krijgen bij uw probleem.

6.  Klik op het **kopieerpictogram** **icon** rechts van het tekstvak **Fout kopiëren** om alle meldingsgegevens te kopiëren die u wilt delen met een ondersteunings- of productgroeptechnicus.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Hulp krijgen door meldingsgegevens naar een ondersteuningstechnicus te sturen

Het is erg belangrijk dat u **alle onderstaande gegevens** deelt met een ondersteuningstechnicus als u hulp nodig hebt, zodat ze u snel kunnen helpen. U dit eenvoudig doen door **een screenshot te maken of** door te klikken op het pictogram Fout **kopiëren**, rechts van het tekstvak Met **fout kopiëren.**

## <a name="notification-details-explained"></a>Details van de melding uitgelegd

Zie de volgende beschrijvingen voor meer informatie over de meldingen.

### <a name="essential-notification-items"></a>Essentiële meldingsitems

- **Titel** – de beschrijvende titel van de kennisgeving

  * Voorbeeld : **Instellingen voor toepassingsproxy**

- **Beschrijving** – de beschrijving van wat er is gebeurd als gevolg van de operatie

  -   Voorbeeld : **interne url wordt al gebruikt door een andere toepassing**

- **Meldings-ID** – de unieke id van de melding

  -   Voorbeeld – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **Client request ID** – de specifieke aanvraag-ID van uw browser

  -   Voorbeeld – **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Time Stamp UTC** – de tijdstempel waarin de melding heeft plaatsgevonden, in UTC

  -   Voorbeeld – **2017-03-23T19:50:43.7583681Z**

- **Interne transactie-ID** – de interne ID die we kunnen gebruiken om de fout in onze systemen op te zoeken

  -   Voorbeeld – **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN** – de gebruiker die de bewerking heeft uitgevoerd

  -   Voorbeeld – **tperkins\@f128.info**

- **Tenant-ID** – de unieke id van de tenant waarvan de gebruiker die de bewerking heeft uitgevoerd, lid was van

  -   Voorbeeld – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **Gebruikersnaam :** de unieke id van de gebruiker die de bewerking heeft uitgevoerd

  -   Voorbeeld – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Gedetailleerde meldingsitems

-   **Weergavenaam** – **(kan leeg zijn)** een meer gedetailleerde weergavenaam voor de fout

    -   Voorbeeld : **Instellingen voor toepassingsproxy**

-   **Status** – de specifieke status van de kennisgeving

    -   Voorbeeld – **Mislukt**

-   **Object-ID** – **(kan leeg zijn)** de object-id waartegen de bewerking is uitgevoerd

    -   Voorbeeld – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Details** – de gedetailleerde beschrijving van wat er gebeurde als gevolg van de operatie

    -   Voorbeeld : **interne url `https://bing.com/` is ongeldig omdat deze al in gebruik is**

-   **Fout kopiëren** : klik op het **kopieerpictogram** rechts van het tekstvak **Fout kopiëren** om alle meldingsgegevens te kopiëren die u wilt delen met een ondersteuningsgroep of productgroep 
-   technicus

    -   Voorbeeld```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

