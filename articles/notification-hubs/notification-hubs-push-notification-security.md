---
title: Beveiligingsmodel voor meldingshubs
description: Meer informatie over het beveiligingsmodel voor Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263758"
---
# <a name="notification-hubs-security"></a>Beveiliging van meldingshubs

## <a name="overview"></a>Overzicht

In dit onderwerp wordt het beveiligingsmodel van Azure Notification Hubs beschreven.

## <a name="shared-access-signature-security"></a>Beveiliging van gedeelde toegangshandtekeningen

Notification Hubs implementeert een beveiligingssysteem op entiteitsniveau, een *SAS (Shared Access Signature).* Elke regel bevat een naam, een sleutelwaarde (gedeeld geheim) en een set rechten, zoals later in [Beveiligingsclaims](#security-claims)wordt uitgelegd. 

Bij het maken van een hub worden automatisch twee regels gemaakt: een met **luisterrechten** (die de client-app gebruikt) en een met **alle** rechten (die de app backend gebruikt):

- **DefaultListenSharedAccessSignature**: verleent alleen **luistertoestemming.**
- **DefaultFullSharedAccessSignature:** geeft **machtigingen voor luisteren,** **beheren**en **verzenden toe.** Dit beleid mag alleen worden gebruikt in uw back-end van de app. Gebruik het niet in clienttoepassingen; gebruik een beleid met alleen **toegang tot luisteren.** Zie [SAS-tokens voor toegangsbeleid](#sas-tokens-for-access-policies) later in dit artikel om een nieuw aangepast toegangsbeleid te maken met een nieuw SAS-token.

Bij het uitvoeren van registratiebeheer vanuit client-apps, als de informatie die via meldingen wordt verzonden niet gevoelig is (bijvoorbeeld weerupdates), is een veelvoorkomende manier om toegang te krijgen tot een Meldingshub om de belangrijkste waarde van de regel Alleen-luisteren toegang te geven tot de client-app, en om de belangrijkste waarde van de regel volledige toegang tot de app backend te geven.

Apps mogen de sleutelwaarde niet insluiten in Windows Store-client-apps. in plaats daarvan laat de client-app deze ophalen uit de back-end van de app bij het opstarten.

Met de sleutel met **Toegang luisteren** kan een client-app zich registreren voor elke tag. Als uw app registraties moet beperken tot specifieke tags voor specifieke clients (bijvoorbeeld wanneer tags gebruikersnamen vertegenwoordigen), moet uw back-end van de app de registraties uitvoeren. Zie [Registratiebeheer](notification-hubs-push-notification-registration-management.md)voor meer informatie. Houd er rekening mee dat de client-app op deze manier geen directe toegang heeft tot Meldingenhubs.

## <a name="security-claims"></a>Beveiligingsclaims

Net als bij andere entiteiten zijn Notification Hub-bewerkingen toegestaan voor drie beveiligingsclaims: **Luisteren,** **Verzenden**en **beheren**.

| Claim   | Beschrijving                                          | Bewerkingen toegestaan |
| ------- | ---------------------------------------------------- | ------------------ |
| Luisteren  | Afzonderlijke registraties maken/bijwerken, lezen en verwijderen | Registratie maken/bijwerken<br><br>Registratie lezen<br><br>Alle registraties voor een handvat lezen<br><br>Registratie verwijderen |
| Verzenden    | Berichten verzenden naar de meldingshub                | Bericht verzenden |
| Beheren  | CRUD's op meldinghubs (inclusief het bijwerken van PNS-referenties en beveiligingssleutels) en leesregistraties op basis van tags |Hubs maken/bijwerken/lezen/verwijderen<br><br>Registraties lezen op tag |

Notification Hubs accepteert SAS-tokens die zijn gegenereerd met gedeelde sleutels die rechtstreeks op de hub zijn geconfigureerd.

Het is niet mogelijk om een melding naar meer dan één naamruimte te sturen. Naamruimten zijn logische containers voor Meldingshubs en zijn niet betrokken bij het verzenden van meldingen.

Gebruik het toegangsbeleid op naamruimteniveau (referenties) voor bewerkingen op naamruimteniveau. bijvoorbeeld: hubs aanbieden, hubs maken of verwijderen, enz. Alleen met het toegangsbeleid op hubniveau u meldingen verzenden.

### <a name="sas-tokens-for-access-policies"></a>SAS-tokens voor toegangsbeleid

Ga als volgt te werk om een nieuwe beveiligingsclaim te maken of bestaande SAS-sleutels weer te geven:

1. Meld u aan bij Azure Portal.
2. Selecteer **Alle resources**.
3. Selecteer de naam van de meldingshub waarvoor u de claim wilt maken of de SAS-sleutel wilt bekijken.
4. Selecteer **Access-beleid**in het linkermenu .
5. Selecteer **Nieuw beleid** om een nieuwe beveiligingsclaim te maken. Geef het beleid een naam en selecteer de machtigingen die u wilt verlenen. Selecteer vervolgens **OK**.
6. De volledige verbindingstekenreeks (inclusief de nieuwe SAS-toets) wordt weergegeven in het venster Toegangsbeleid. U deze tekenreeks naar het klembord kopiëren voor later gebruik.

Als u de SAS-sleutel uit een specifiek beleid wilt halen, selecteert u de knop **Kopiëren** naast het beleid met de gewenste SAS-toets. Plak deze waarde op een tijdelijke locatie en kopieer vervolgens het SAS-sleutelgedeelte van de verbindingstekenreeks. In dit voorbeeld wordt een naamruimte voor meldinghubs gebruikt, **mytestnamespace1**genaamd , en een beleid met de naam **policy2**. De SAS-sleutel is de waarde aan het einde van de tekenreeks, opgegeven door **SharedAccessKey:**

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![SAS-sleutels ophalen](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van meldingshubs](notification-hubs-push-notification-overview.md)
