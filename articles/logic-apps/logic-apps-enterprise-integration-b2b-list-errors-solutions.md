---
title: Oplossingen voor veelvoorkomende fouten en problemen in B2B-scenario's
description: Oplossingen vinden voor veelvoorkomende fouten en problemen bij het oplossen van B2B-scenario's in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 38e281ce3d8117bff719b1bb572f09acbbb89669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666683"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>B2B-fouten en -oplossingen voor Azure Logic Apps

Met dit artikel u fouten oplossen die kunnen optreden in De B2B-scenario's van Logic Apps en worden passende acties voorgesteld om deze fouten te corrigeren.

## <a name="agreement-resolution"></a>Overeenkomstresolutie

### <a name="no-agreement-found"></a>Geen overeenkomst gevonden 

|   |   |  
|---|---|
| Foutbeschrijving | Er is geen overeenkomst gevonden met parameters voor de oplossing van de overeenkomst. | 
| Gebruikersactie | De overeenkomst moet worden toegevoegd aan het integratieaccount met overeengekomen bedrijfsidentiteiten. </br>De bedrijfsidentiteiten moeten overeenkomen met de invoerboodschap-id's. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Geen overeenkomst gevonden met identiteiten

|   |   | 
|---|---|
| Foutbeschrijving | Geen overeenkomst gevonden met identiteiten: 'AS2Identity'::'Partner1' en'AS2Identity'::'Partner3' | 
| Gebruikersactie | Ongeldig AS2-Van of AS2-To geconfigureerd voor overeenkomst. </br>Corrigeer de KOPPEN 'AS2-From' of 'AS2-To' van het AS2-bericht of de overeenkomst om de AS2-id's in de AS2-berichtheaders te matchen met overeenkomstconfiguraties. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Ontbrekende AS2-berichtkoppen  

|   |   |  
|---|---|
| Foutbeschrijving | Ongeldige AS2-headers. Een van de koppen "AS2-To" of "AS2-From" is leeg. | 
| Gebruikersactie | Er is een AS2-bericht ontvangen dat geen AS2-Van of AS2-To of beide kopteksten bevatte. </br> Controleer AS2-bericht AS2-From- en AS2-To-headers en corrigeer deze op basis van de overeenkomstconfiguratie. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Ontbrekende AS2-berichthoofden en -headers    

|   |   |  
|---|---|
| Foutbeschrijving | De inhoud van het verzoek is nietig of leeg. | 
| Gebruikersactie | Er is een AS2-bericht ontvangen dat de berichttekst niet bevatte. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>AS2-bericht decryptie mislukt

|   |   | 
|---|---|
| Foutbeschrijving |  [verwerkt/fout: decryptie mislukt] | 
| Gebruikersactie | Toevoegen @base64ToBinary aan AS2Message voordat u naar de partner verzendt. |
|||

Bijvoorbeeld:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>MDN decryptie storing

|   |   | 
|---|---|
| Foutbeschrijving |  [verwerkt/fout: decryptie mislukt] | 
| Gebruikersactie | Voeg @base64ToBinary toe aan MDN voordat u naar de partner verzendt. | 
|||

Bijvoorbeeld:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Ontbrekend ondertekeningscertificaat

|   |   |  
|---|---|
| Foutbeschrijving| Het ondertekeningscertificaat is niet geconfigureerd voor AS2-partij. </br>AS2-From: partner1 AS2-To: partner2 | 
| Gebruikersactie | As2-overeenkomstinstellingen configureren met het juiste certificaat voor ondertekening. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 en EDIFACT

### <a name="leading-or-trailing-space-found"></a>Gevonden voorloop- of naloopruimte    
    
|   |   | 
|---|---|
| Foutbeschrijving | Fout ondervonden tijdens het ontleed. De EDIFACT-transactieset met ID '123456' in uitwisseling (zonder groep) met ID '987654', met afzender-ID 'Partner1', ontvanger-ID 'Partner2' wordt opgeschort met de volgende fouten: <p>"Toonaangevende Trailing separator gevonden" |
| Gebruikersactie | De overeenkomstinstellingen die moeten worden geconfigureerd om voorloop- en naloopruimte mogelijk te maken. </br>De instellingen van de overeenkomst bewerken om voorloop- en achterstandsruimte mogelijk te maken. |
|   |   |

![ruimte toestaan](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Dubbele controle is ingeschakeld in de overeenkomst

|   |   | 
|---|---| 
| Foutbeschrijving | Controlenummer dupliceren |
| Gebruikersactie | Deze fout geeft aan dat het ontvangen bericht dubbele controlenummers bevat. </br>Corrigeer het besturingselementnummer en stuur het bericht opnieuw. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Ontbrekend schema in de overeenkomst

|   |   | 
|---|---| 
| Foutbeschrijving | Fout ondervonden tijdens het ontleed. De X12-transactieset met ID '564220001' in functionele groep met ID '56422', in uitwisseling met ID '000056422', met afzender-ID '12345678', ontvanger-ID '87654321' wordt opgeschort met de volgende fouten: <p>"Het bericht heeft een onbekend documenttype en is niet opgelost tot een van de bestaande schema's die in de overeenkomst zijn geconfigureerd" |
| Gebruikersactie | Schema configureren in de overeenkomstinstellingen.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Onjuist schema in de overeenkomst

|   |   | 
|---|---| 
| Foutbeschrijving | Het bericht heeft een onbekend documenttype en is niet opgelost in een van de bestaande schema's die in de overeenkomst zijn geconfigureerd. |
| Gebruikersactie | Configureer het juiste schema in de overeenkomstinstellingen. |
|   |   |

## <a name="flat-file"></a>Plat bestand

### <a name="input-message-with-no-body"></a>Invoerbericht zonder hoofd

|   |   | 
|---|---|
| Foutbeschrijving | OngeldigSjabloon. Niet in staat om sjabloontaaluitdrukkingen te verwerken in actie 'Flat_File_Decoding' ingangen bij regel '1' en kolom '1902': 'Vereiste eigenschap 'inhoud' verwacht een waarde, maar wordt nietig verklaard. Pad ''. |
| Gebruikersactie | Deze fout geeft aan dat het invoerbericht geen hoofdtekst bevat. |
|   |   | 

