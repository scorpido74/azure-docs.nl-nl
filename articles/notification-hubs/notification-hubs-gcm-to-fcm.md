---
title: Azure Notification Hubs en de Google Firebase Cloud Messaging (FCM) migratie
description: Beschrijft hoe Azure Notification Hubs de Google GCM-migratie naar FCM-migratie verhelpt.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127027"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Azure Notification Hubs en Google Firebase Cloud Messaging-migratie

## <a name="current-state"></a>Huidige status

Toen Google de migratie van Google Cloud Messaging (GCM) naar Firebase Cloud Messaging (FCM) aankondigde, moesten pushservices zoals de onze aanpassen hoe we meldingen naar Android-apparaten stuurden om de verandering aan te passen.

We hebben onze servicebackend bijgewerkt en vervolgens updates gepubliceerd voor onze API en SDK's als dat nodig is. Met onze implementatie hebben we de beslissing genomen om compatibiliteit te behouden met bestaande GCM-meldingsschema's om de impact van klanten te minimaliseren. Dit betekent dat we momenteel meldingen verzenden naar Android-apparaten met FCM in FCM Legacy Mode. Uiteindelijk willen we echte ondersteuning voor FCM toevoegen, inclusief de nieuwe functies en payload-indeling. Dat is een verandering op langere termijn en de huidige migratie is gericht op het behoud van compatibiliteit met bestaande toepassingen en SDK's. U de GCM- of FCM SDK's in uw app gebruiken (samen met onze SDK) en wij zorgen ervoor dat de melding correct wordt verzonden.

Sommige klanten hebben onlangs een e-mail ontvangen van Google waarin wordt gewaarschuwd voor apps die een GCM-eindpunt voor meldingen gebruiken. Dit was slechts een waarschuwing, en niets is gebroken - Uw app Android-meldingen worden nog steeds verzonden naar Google voor verwerking en Google verwerkt ze nog steeds. Sommige klanten die het GCM-eindpunt expliciet in hun serviceconfiguratie hebben opgegeven, gebruikten nog steeds het afgeschafte eindpunt. We hadden al geïdentificeerd deze kloof en waren bezig met de vaststelling van het probleem toen Google stuurde de e-mail.

We hebben dat afgeschafte eindpunt vervangen en de oplossing wordt geïmplementeerd.

## <a name="going-forward"></a>In de toekomst

Google's FCM FAQ zegt dat je niets hoeft te doen. In de [FCM FAQ](https://developers.google.com/cloud-messaging/faq), Google zei : "client SDKs en GCM tokens zal blijven werken voor onbepaalde tijd. U de nieuwste versie van Google Play Services echter niet targeten in uw Android-app, tenzij u naar FCM migreert.

Als uw app de GCM-bibliotheek gebruikt, volgt u de instructies van Google om te upgraden naar de FCM-bibliotheek in uw app. Onze SDK is compatibel met beide, dus u hoeft niets in uw app aan onze kant bij te werken (zolang u op de hoogte bent van onze SDK-versie).

## <a name="questions-and-answers"></a>Vragen en antwoorden

Hier volgen enkele antwoorden op veelgestelde vragen die we van klanten hebben gehoord:

**V:** Wat moet ik doen om compatibel te zijn tegen de cutoff datum (google's huidige cutoff datum is 29 mei en kan veranderen)?

**A:** Niets. We blijven compatibel met het bestaande GCM-meldingsschema. Uw GCM-toets blijft net zo normaal werken als alle GCM SDK's en bibliotheken die door uw toepassing worden gebruikt.

Als/wanneer u besluit om te upgraden naar de FCM SDKs en bibliotheken om te profiteren van nieuwe functies, werkt uw GCM-toets nog steeds. U desgewenst overschakelen naar het gebruik van een FCM-sleutel, maar ervoor zorgen dat u Firebase toevoegt aan uw bestaande GCM-project bij het maken van het nieuwe Firebase-project. Dit garandeert achterwaartse compatibiliteit met uw klanten die oudere versies van de app uitvoeren die nog steeds GCM SDKs en bibliotheken gebruiken.

Als u een nieuw FCM-project maakt en niet aan het bestaande GCM-project koppelt, verliest u de mogelijkheid om meldingen naar uw huidige app-installaties te pushen, omdat de nieuwe FCM-toets geen link heeft naar de oude GCM Project.

**V:** Waarom krijg ik deze e-mail over oude GCM-eindpunten die worden gebruikt? Wat moet ik doen?

**A:** Niets. We zijn gemigreerd naar de nieuwe eindpunten en zullen binnenkort klaar zijn, dus er is geen verandering nodig. Niets is gebroken, onze ene gemiste eindpunt gewoon veroorzaakt waarschuwingsberichten van Google.

**V:** Hoe kan ik overschakelen naar de nieuwe FCM SDKs en bibliotheken zonder bestaande gebruikers te breken?

A: Upgrade op elk gewenst moment. Google heeft nog geen afschaffing van bestaande GCM SDKs en bibliotheken aangekondigd. Als u ervoor wilt zorgen dat pushmeldingen niet worden doorgegeven aan uw bestaande gebruikers, moet u ervoor zorgen dat u bij het maken van het nieuwe Firebase-project dat u associeert met uw bestaande GCM-project. Dit zorgt ervoor dat nieuwe Firebase-geheimen werken voor gebruikers die de oudere versies van uw app uitvoeren met GCM SDKs en bibliotheken, evenals nieuwe gebruikers van uw app met FCM SDKs en bibliotheken.

**V:** Wanneer kan ik nieuwe FCM-functies en -schema's gebruiken voor mijn meldingen?

**A:** Zodra we een update van onze API en SDKs publiceren, blijf op de hoogte - we verwachten iets voor u te hebben in de komende maanden.
