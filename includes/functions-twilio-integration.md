---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175836"
---
Dit voorbeeld omvat het gebruik van de [Twilio-service](https://www.twilio.com/) om sms-berichten naar een mobiele telefoon te verzenden. Azure Functions heeft al ondersteuning voor Twilio via de [Twilio-binding](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio)en het voorbeeld maakt gebruik van die functie.

Het eerste wat je nodig hebt is een Twilio account. U er https://www.twilio.com/try-twilioeen gratis maken op. Zodra je een account hebt, voeg je de volgende drie **app-instellingen** toe aan je functie-app.

| Naam van app-instelling | Waardebeschrijving |
| - | - |
| **TwilioAccountSid**  | De SID voor uw Twilio-account |
| **TwilioAuthToken**   | Het Auth-token voor uw Twilio-account |
| **TwilioPhoneNummer** | Het telefoonnummer dat is gekoppeld aan uw Twilio-account. Dit wordt gebruikt om sms-berichten te verzenden. |