---
title: Sms-concepten in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over sms-concepten in Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943681"
---
# <a name="sms-concepts"></a>Sms-concepten

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Met Azure Communication Services kunt u sms-tekstberichten verzenden en ontvangen met behulp van de sms-clientbibliotheken van Communication Services. Deze clientbibliotheken kunnen worden gebruikt ter ondersteuning van klantenservicescenario's, afspraakherinneringen, tweeledige verificatie en andere communicatie in real time. Met sms in Communication Services kunt u op een betrouwbare manier berichten verzenden en inzicht krijgen in de bezorgings- en responspercentages voor uw campagnes.

De belangrijkste kenmerken van de SMS-clientbibliotheken van Azure Communication Services zijn:

-  **Eenvoudige** installatie om sms-mogelijkheden toe te voegen aan uw toepassingen.
- **Snelle** ondersteuning voor berichten door het gebruik van gratis nummers voor A2P (Application to Person) in de Verenigde Staten.
- **Tweerichtingsgesprekken** voor scenario's zoals klantondersteuning, waarschuwingen en afspraakherinneringen.
- **Betrouwbare levering** met realtime leveringsrapporten voor berichten die vanuit uw toepassing worden verzonden.
- **Analytische gegevens** om uw gebruikspatronen en de klantbetrokkenheid bij te houden.
- **Afmelding**ondersteuning om automatisch afmelding voor gratis nummers te detecteren en te respecteren. Communicatie Services detecteert STOP- en START-berichten en verzendt de volgende standaardantwoorden naar eindgebruikers: 
  - STOP - *'U bent afgemeld voor berichten van dit nummer. Antwoord met START om u opnieuw aan te melden.'*
  - START - *'U bent aangemeld voor berichten van dit nummer. Antwoord met STOP om u af te melden.'*
  - De STOP- en START-berichten worden naar u doorgestuurd. Azure Communication Services moedigt u aan deze afmeldingen te controleren en te implementeren om ervoor te zorgen dat er geen verdere berichten worden verzonden naar ontvangers die zich voor uw communicatie hebben afgemeld.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met het verzenden van sms-berichten](../../quickstarts/telephony-sms/send.md)

De volgende documenten zijn mogelijk interessant voor u:

- Uzelf bekend maken met de [sms-clientbibliotheek](../telephony-sms/sdk-features.md)
- Koop een [telefoonnummer](../../quickstarts/telephony-sms/get-phone-number.md) dat geschikt is voor sms
- [Plan uw sms-oplossing](../telephony-sms/plan-solution.md)
