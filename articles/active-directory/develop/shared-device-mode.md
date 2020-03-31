---
title: Modus voor gedeeld apparaat voor Android-apparaten | Azure
description: Meer informatie over de modus voor gedeeld apparaat, waarmee werknemers in de eerste lijn een Android-apparaat kunnen delen
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: marsma
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9928b64d286cc5072f28f7cc17e4af3e95662cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085612"
---
# <a name="shared-device-mode-for-android-devices"></a>Modus voor gedeeld apparaat voor Android-apparaten

> [!NOTE]
> Deze functie is beschikbaar voor openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Eerstelijnswerknemers, zoals medewerkers in de detailhandel, leden van het cockpitpersoneel en veldhulpverleners, gebruiken vaak een gedeeld mobiel apparaat om hun werk te doen. Dat wordt problematisch wanneer ze wachtwoorden of pincodes gaan delen om toegang te krijgen tot klant- en bedrijfsgegevens op het gedeelde apparaat.

Met de modus Gedeeld apparaat u een Android-apparaat configureren, zodat het eenvoudig door meerdere werknemers kan worden gedeeld. Medewerkers kunnen zich aanmelden en snel toegang krijgen tot klantgegevens. Wanneer ze klaar zijn met hun shift of taak, kunnen ze zich afmelden bij het apparaat en is het onmiddellijk klaar voor de volgende werknemer om te gebruiken.

De modus Voor gedeelde apparaten biedt ook door Microsoft door de identiteit ondersteund beheer van het apparaat.

Ontwikkelaars en cloudapparaatbeheerders werken samen om een app voor gedeelde apparaatmodus te maken:

- Ontwikkelaars schrijven een app met één account (apps met meerdere accountworden `"shared_device_mode_supported": true` niet ondersteund in de modus voor gedeeld apparaat), voegen toe aan de configuratie van de app en schrijven code om zaken als afmelding van gedeelde apparaten af te handelen.
- Apparaatbeheerders bereiden het apparaat voor om te worden gedeeld door de authenticator-app te installeren en het apparaat in te stellen op de gedeelde modus met de authenticator-app. Alleen gebruikers die zich in de rol [Cloud Device Administrator bevinden,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator) kunnen een apparaat in de gedeelde modus plaatsen met behulp van de [Authenticator-app.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) U het lidmaatschap van uw organisatierollen in de Azure-portal configureren via: **Azure Active Directory** > **Roles and Administrators** > **Cloud Device Administrator**.

 Dit artikel richt zich vooral op wat ontwikkelaars moeten denken.

## <a name="single-vs-multiple-account-applications"></a>Toepassingen met één versus meerdere account

Toepassingen die zijn geschreven met de Microsoft Authentication Library SDK (MSAL) kunnen één account of meerdere accounts beheren. Zie de [modus met één account of de modus met meerdere accounten](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account)voor meer informatie. De functies van het Microsoft-identiteitsplatform die beschikbaar zijn voor uw app, zijn afhankelijk van of de toepassing wordt uitgevoerd in de modus met één account of in de modus met meerdere accounten.

**Apps in de modus Voor gedeelde apparaten werken alleen in de modus met één account.**

> [!IMPORTANT]
> Toepassingen die alleen de modus met meerdere accountondersteunen, kunnen niet worden uitgevoerd op een gedeeld apparaat. Als een werknemer een app laadt die de modus met één account niet ondersteunt, wordt deze niet uitgevoerd op het gedeelde apparaat.
>
> Apps die zijn geschreven voordat de MSAL SDK werd uitgebracht, worden uitgevoerd in de modus met meerdere accounten en moeten worden bijgewerkt om de modus met één account te ondersteunen voordat ze kunnen worden uitgevoerd op een apparaat met een gedeelde modus.

**Ondersteuning voor zowel single-account als meerdere accounts**

Uw app kan worden gemaakt om ondersteuning te bieden voor het uitvoeren van zowel persoonlijke apparaten als gedeelde apparaten. Als uw app momenteel meerdere accounts ondersteunt en u de modus gedeeld apparaat wilt ondersteunen, voegt u ondersteuning toe voor de modus voor één account.

U wilt mogelijk ook dat uw app zijn gedrag wijzigt, afhankelijk van het type apparaat waarop deze wordt uitgevoerd. Gebruiken `ISingleAccountPublicClientApplication.isSharedDevice()` om te bepalen wanneer u moet worden uitgevoerd in de modus met één account.

Er zijn twee verschillende interfaces die het type apparaat weergeven waarop uw toepassing zich bevindt. Wanneer u een toepassingsinstantie aanvraagt bij de toepassingsfabriek van MSAL, wordt het juiste toepassingsobject automatisch geleverd.

Het volgende objectmodel illustreert het type object dat u ontvangen en wat het betekent in de context van een gedeeld apparaat:

![overervingsmodel voor openbare clienttoepassingen](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

U moet een typecontrole doen en naar de juiste `PublicClientApplication` interface werpen wanneer u uw object krijgt. De volgende code controleert op de modus meerdere account of de modus met één account en werpt het toepassingsobject op de juiste manier:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

De volgende verschillen zijn van toepassing, afhankelijk van of uw app wordt uitgevoerd op een gedeeld of persoonlijk apparaat:

|  | Apparaat in gedeelde modus  | Persoonlijk apparaat |
|---------|---------|---------|
| **Accounts**     | Eén account | Meerdere accounts |
| **Aanmelden** | Wereldwijd | Wereldwijd |
| **Afmelden** | Wereldwijd | Elke toepassing kan bepalen of de afmelding lokaal is voor de app of voor de familie van toepassingen. |
| **Ondersteunde accounttypen** | Alleen werkaccounts | Ondersteunde persoonlijke en werkaccounts  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Waarom u misschien alleen de modus met één account wilt ondersteunen

Als u een app schrijft die alleen wordt gebruikt voor eerstelijnswerknemers die een gedeeld apparaat gebruiken, raden we u aan uw toepassing te schrijven om alleen de modus met één account te ondersteunen. Dit omvat de meeste toepassingen die taakgericht zijn, zoals apps voor medische dossiers, factuur-apps en de meeste zakelijke apps. Alleen het ondersteunen van de modus met één account vereenvoudigt de ontwikkeling omdat u de extra functies die deel uitmaken van apps met meerdere accounts niet hoeft te implementeren.

## <a name="what-happens-when-the-device-mode-changes"></a>Wat gebeurt er als de apparaatmodus verandert

Als uw toepassing wordt uitgevoerd in de modus met meerdere accounts en een beheerder het apparaat in de modus gedeeld apparaat plaatst, worden alle accounts op het apparaat gewist uit de toepassing en worden de toepassingsovergangen naar de modus met één account ingeschakeld.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Afmelding van gedeelde apparaten en de algemene levenscyclus van de app

Wanneer een gebruiker zich afmeldt, moet u actie ondernemen om de privacy en gegevens van de gebruiker te beschermen. Als u bijvoorbeeld een app voor medische dossiers bouwt, wilt u ervoor zorgen dat wanneer de gebruiker zich afmeldt voor eerder weergegeven patiëntendossiers, wordt gewist. Uw aanvraag moet hiervoor worden voorbereid en elke keer dat deze op de voorgrond komt controleren.

Wanneer uw app MSAL gebruikt om de gebruiker af te melden in een app die wordt uitgevoerd op een apparaat dat in de gedeelde modus is uitgevoerd, worden het aangemelde account en de tokens in de cache verwijderd uit zowel de app als het apparaat.

In het volgende diagram ziet u de algemene levenscyclus van de app en de algemene gebeurtenissen die kunnen optreden tijdens het uitvoeren van uw app. Het diagram gaat over het moment dat een activiteit wordt gestart, het aanmelden en afmelden van een account en hoe gebeurtenissen zoals pauzeren, hervatten en stoppen van de activiteit passen.

![Levenscyclus van gedeelde apparaat-apps](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Volgende stappen

Probeer de modus Gedeeld apparaat gebruiken in de zelfstudie van [je Android-applicatie](tutorial-v2-shared-device-mode.md) die laat zien hoe je een eerstelijns werknemer-app uitvoert op een Android-apparaat in gedeelde modus.
