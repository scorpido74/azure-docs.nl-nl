---
title: AMQP 1.0-aanvraag-/antwoordbewerkingen in Azure Service Bus
description: In dit artikel wordt de lijst met op AMQP-aanvragen/op antwoorden gebaseerde bewerkingen gedefinieerd in Microsoft Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: aschhab
ms.openlocfilehash: 338b411eade35fa357f64131c64b8ef2034c92b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761080"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 in Microsoft Azure Service Bus: bewerkingen op basis van aanvragen

In dit artikel wordt de lijst met bewerkingen voor microsoft Azure Service Bus-aanvragen/op basis van antwoorden gedefinieerd. Deze informatie is gebaseerd op het werkconcept VAN AMQP Management Version 1.0.  
  
Voor een gedetailleerde AMQP 1.0-protocolhandleiding op draadniveau, waarin wordt uitgelegd hoe Service Bus de technische specificatie van OASIS AMQP implementeert en voortbouwt, raadpleegt u de [AMQP 1.0 in de protocolgids][AMQP 1.0-protocolgids]voor Azure Service Bus en Event Hubs.  
  
## <a name="concepts"></a>Concepten  
  
### <a name="entity-description"></a>Beschrijving van entiteit  

Een entiteitsbeschrijving verwijst naar een [klasse ServiceBus QueueDescription,](/dotnet/api/microsoft.servicebus.messaging.queuedescription) [klasse TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)of object [van de klasse SubscriptionDescription.](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription)  
  
### <a name="brokered-message"></a>Bemiddeld bericht  

Vertegenwoordigt een bericht in Service Bus, dat is toegewezen aan een AMQP-bericht. De toewijzing wordt gedefinieerd in de [AMQP-protocolhandleiding servicebus.](service-bus-amqp-protocol-guide.md)  
  
## <a name="attach-to-entity-management-node"></a>Koppelen aan entiteitsbeheerknooppunt  

Alle bewerkingen die in dit document worden beschreven, volgen een aanvraag-/antwoordpatroon, worden toegewezen aan een entiteit en vereisen een bevestiging aan een entiteitsbeheerknooppunt.  
  
### <a name="create-link-for-sending-requests"></a>Koppeling maken voor het verzenden van aanvragen  

Hiermee maakt u een koppeling naar het beheerknooppunt voor het verzenden van aanvragen.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Koppeling maken voor het ontvangen van reacties  

Hiermee maakt u een koppeling voor het ontvangen van reacties van het beheerknooppunt.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Een verzoekbericht overbrengen  

Een verzoekbericht wordt overgezet.  
Een transactiestatus kan optioneel worden toegevoegd voor bewerkingen die transactie ondersteunen.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Een antwoordbericht ontvangen  

Ontvangt het antwoordbericht van de reactiekoppeling.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Het antwoordbericht bevindt zich in de volgende vorm:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Adres van de entiteit ServiceBus  

Service Bus-entiteiten moeten als volgt worden aangepakt:  
  
|Entiteitstype|Adres|Voorbeeld|  
|-----------------|-------------|-------------|  
|wachtrij|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|onderwerp|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|abonnement|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Berichtbewerkingen  
  
### <a name="message-renew-lock"></a>Bericht vergrendeling vernieuwen  

Hiermee wordt het vergrendelingsbericht verlengd tegen de tijd die is opgegeven in de beschrijving van de entiteit.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
 De hoofdtekst van het aanvraagbericht moet bestaan uit een amqp-waardesectie met een kaart met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|array van uuid|Ja|Tokens voor berichtvergrendeling om te vernieuwen.|  

> [!NOTE]
> Lock tokens `DeliveryTag` zijn de eigenschap op ontvangen berichten. Zie het volgende voorbeeld in de [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) die deze ophaalt. Het token kan ook in de 'DeliveryAnnotations' worden weergegeven als 'x-opt-lock-token' echter, dit is niet gegarandeerd en de `DeliveryTag` voorkeur moet de voorkeur krijgen. 
> 
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – succes, anders mislukt.|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|  
  
De hoofdtekst van het antwoordbericht moet bestaan uit een amqp-waardesectie met een kaart met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Verloopt|array van tijdstempel|Ja|Berichtvergrendelingtoken nieuwe vervaldatum die overeenkomt met de tokens voor het vergrendelen van het verzoek.|  
  
### <a name="peek-message"></a>Peek-bericht  

Peeks berichten zonder vergrendeling.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Ja|Volgnummer van waaruit u beginnen met gluren.|  
|`message-count`|int|Ja|Maximaal aantal berichten om te gluren.|  
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – heeft meer berichten<br /><br /> 204: Geen inhoud – geen berichten meer|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|  
  
De hoofdtekst van het antwoordbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|berichten|lijst met kaarten|Ja|Lijst met berichten waarin elke kaart een bericht vertegenwoordigt.|  
  
De kaart die een bericht voorstelt, moet de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|message|array van byte|Ja|AMQP 1.0 wire-gecodeerd bericht.|  
  
### <a name="schedule-message"></a>Bericht plannen  

Plant berichten. Deze bewerking ondersteunt transactie.
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|berichten|lijst met kaarten|Ja|Lijst met berichten waarin elke kaart een bericht vertegenwoordigt.|  
  
De kaart die een bericht voorstelt, moet de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bericht-id|tekenreeks|Ja|`amqpMessage.Properties.MessageId`als tekenreeks|  
|sessie-id|tekenreeks|Nee|`amqpMessage.Properties.GroupId as string`|  
|partitiesleutel|tekenreeks|Nee|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partition-key|tekenreeks|Nee|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|array van byte|Ja|AMQP 1.0 wire-gecodeerd bericht.|  
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – succes, anders mislukt.|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|  
  
De hoofdtekst van het antwoordbericht moet bestaan uit een **amqp-waardesectie** met een kaart met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|volgnummers|array van lange|Ja|Aantal geplande berichten. Het volgnummer wordt gebruikt om te annuleren.|  
  
### <a name="cancel-scheduled-message"></a>Gepland bericht annuleren  

Hiermee annuleert u geplande berichten.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|volgnummers|array van lange|Ja|Volgnummers van geplande berichten die moeten worden geannuleerd.|  
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – succes, anders mislukt.|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|   
  
## <a name="session-operations"></a>Sessiebewerkingen  
  
### <a name="session-renew-lock"></a>Vergrendeling voor sessie verlengen  

Hiermee wordt het vergrendelingsbericht verlengd tegen de tijd die is opgegeven in de beschrijving van de entiteit.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|sessie-id|tekenreeks|Ja|Sessie-ID.|  
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – heeft meer berichten<br /><br /> 204: Geen inhoud – geen berichten meer|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|  
  
De hoofdtekst van het antwoordbericht moet bestaan uit een **amqp-waardesectie** met een kaart met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Verloop|tijdstempel|Ja|Nieuwe afloop.|  
  
### <a name="peek-session-message"></a>Sessiebericht gluren  

Peeks sessie berichten zonder vergrendeling.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|van-sequentienummer|long|Ja|Volgnummer van waaruit u beginnen met gluren.|  
|aantal berichten|int|Ja|Maximaal aantal berichten om te gluren.|  
|sessie-id|tekenreeks|Ja|Sessie-ID.|  
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – heeft meer berichten<br /><br /> 204: Geen inhoud – geen berichten meer|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|  
  
De hoofdtekst van het antwoordbericht moet bestaan uit een **amqp-waardesectie** met een kaart met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|berichten|lijst met kaarten|Ja|Lijst met berichten waarin elke kaart een bericht vertegenwoordigt.|  
  
 De kaart die een bericht voorstelt, moet de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|message|array van byte|Ja|AMQP 1.0 wire-gecodeerd bericht.|  
  
### <a name="set-session-state"></a>Sessiestatus instellen  

Hiermee stelt u de status van een sessie in.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|sessie-id|tekenreeks|Ja|Sessie-ID.|  
|sessiestatus|array met bytes|Ja|Ondoorzichtige binaire gegevens.|  
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – succes, anders mislukt|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|  
  
### <a name="get-session-state"></a>Sessiestatus krijgen  

Krijgt de status van een sessie.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|sessie-id|tekenreeks|Ja|Sessie-ID.|  
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – succes, anders mislukt|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|  
  
De hoofdtekst van het antwoordbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|sessiestatus|array met bytes|Ja|Ondoorzichtige binaire gegevens.|  
  
### <a name="enumerate-sessions"></a>Sessies opsommen  

Somt sessies op een berichtenentiteit op.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|laatst bijgewerkte tijd|tijdstempel|Ja|Filter om alleen sessies op te nemen die na een bepaalde tijd zijn bijgewerkt.|  
|skip|int|Ja|Sla een aantal sessies over.|  
|top|int|Ja|Maximaal aantal sessies.|  
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – heeft meer berichten<br /><br /> 204: Geen inhoud – geen berichten meer|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|  
  
De hoofdtekst van het antwoordbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|skip|int|Ja|Aantal overgeslagen sessies als de statuscode 200 is.|  
|sessies-id's|tekenreeksmatrix|Ja|Array van sessie-id's als de statuscode 200 is.|  
  
## <a name="rule-operations"></a>Regelbewerkingen  
  
### <a name="add-rule"></a>Regel toevoegen  
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|regelnaam|tekenreeks|Ja|Regelnaam, exclusief abonnements- en onderwerpnamen.|  
|regelbeschrijving|map|Ja|Regelbeschrijving zoals opgegeven in de volgende sectie.|  
  
De **regelbeschrijvingskaart** moet de volgende vermeldingen bevatten, waarbij **sql-filter** en **correlatiefilter** elkaar uitsluiten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Ja|`sql-filter`, zoals aangegeven in de volgende sectie.|  
|correlatiefilter|map|Ja|`correlation-filter`, zoals aangegeven in de volgende sectie.|  
|sql-rule-actie|map|Ja|`sql-rule-action`, zoals aangegeven in de volgende sectie.|  
  
De sql-filter kaart moet de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|expressie|tekenreeks|Ja|Sql-filterexpressie.|  
  
De **correlatiefilterkaart** moet ten minste één van de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|correlation-id|tekenreeks|Nee||  
|bericht-id|tekenreeks|Nee||  
|tot|tekenreeks|Nee||  
|reply-to|tekenreeks|Nee||  
|label|tekenreeks|Nee||  
|sessie-id|tekenreeks|Nee||  
|reply-to-session-id|tekenreeks|Nee||  
|inhoudstype|tekenreeks|Nee||  
|properties|map|Nee|Kaarten aan Service Bus [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
De **sql-rule-action map** moet de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|expressie|tekenreeks|Ja|Sql-actie-expressie.|  
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – succes, anders mislukt|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|  
  
### <a name="remove-rule"></a>Regel verwijderen  
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|regelnaam|tekenreeks|Ja|Regelnaam, exclusief abonnements- en onderwerpnamen.|  
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – succes, anders mislukt|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|  
  
### <a name="get-rules"></a>Regels krijgen

#### <a name="request"></a>Aanvraag

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:

|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  

De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|top|int|Ja|Het aantal regels dat u op de pagina moet ophalen.|  
|skip|int|Ja|Het aantal regels dat moet worden overgeslagen. Hiermee definieert u de startindex (+1) in de lijst met regels. | 

#### <a name="response"></a>Antwoord

Het antwoordbericht bevat de volgende eigenschappen:

|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – succes, anders mislukt|  
|regels| array van de kaart|Ja|Scala aan regels. Elke regel wordt vertegenwoordigd door een kaart.|

Elke kaartvermelding in de array bevat de volgende eigenschappen:

|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|regelbeschrijving|array van beschreven objecten|Ja|`com.microsoft:rule-description:list`met AMQP beschreven code 0x00000137000000004| 

`com.microsoft.rule-description:list`is een array van beschreven objecten. De array bevat het volgende:

|Index|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
| 0 | array van beschreven objecten | Ja | `filter`zoals hieronder aangegeven. |
| 1 | array van beschreven objecten | Ja | `ruleAction`zoals hieronder aangegeven. |
| 2 | tekenreeks | Ja | naam van de regel. |

`filter`kan van een van de volgende typen zijn:

| Naam beschrijving | Descriptorcode | Waarde |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL-filter |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Correlatiefilter |
| `com.microsoft:true-filter:list` | 0x000001370000007 | True-filter dat 1=1 vertegenwoordigt |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Onwaarfilter dat 1=0 vertegenwoordigt |

`com.microsoft:sql-filter:list`is een beschreven array die omvat:

|Index|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
| 0 | tekenreeks | Ja | Sql-filter-expressie |

`com.microsoft:correlation-filter:list`is een beschreven array die omvat:

|Index (indien aanwezig)|Waardetype|Inhoud van waarde|  
|---------|----------------|--------------|
| 0 | tekenreeks | Correlatie-id |
| 1 | tekenreeks | Bericht-id |
| 2 | tekenreeks | Handeling |
| 3 | tekenreeks | Reageren op |
| 4 | tekenreeks | Label |
| 5 | tekenreeks | Sessie-id |
| 6 | tekenreeks | Sessie-id beantwoorden|
| 7 | tekenreeks | Type inhoud |
| 8 | Kaart | Toepassingskaart gedefinieerde eigenschappen |

`ruleAction`kan een van de volgende typen zijn:

| Naam beschrijving | Descriptorcode | Waarde |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x00000137000000005 | Actie voor lege regel - Geen regelactie aanwezig |
| `com.microsoft:sql-rule-action:list` | 0x00000137000000006 | SQL-regelactie |

`com.microsoft:sql-rule-action:list`is een array met beschreven objecten waarvan de eerste vermelding een tekenreeks is die de expressie van de SQL-regelactie bevat.

## <a name="deferred-message-operations"></a>Uitgestelde berichtbewerkingen  
  
### <a name="receive-by-sequence-number"></a>Ontvangen op volgnummer  

Ontvangt uitgestelde berichten per volgnummer.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|volgnummers|array van lange|Ja|Volgnummers.|  
|ontvanger-settle-modus|ubyte ubyte|Ja|**Ontvangervereffeningsmodus** zoals gespecificeerd in AMQP-kern v1.0.|  
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – succes, anders mislukt|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|  
  
De hoofdtekst van het antwoordbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|berichten|lijst met kaarten|Ja|Lijst met berichten waarbij elke kaart een bericht vertegenwoordigt.|  
  
De kaart die een bericht voorstelt, moet de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|Ja|Slottoken `receiver-settle-mode` als het 1 is.|  
|message|array van byte|Ja|AMQP 1.0 wire-gecodeerd bericht.|  
  
### <a name="update-disposition-status"></a>Status van dispositie bijwerken  

Hiermee wordt de status van uitgestelde berichten bijgewerkt. Deze bewerking ondersteunt transacties.
  
#### <a name="request"></a>Aanvraag  

Het aanvraagbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Bewerking|tekenreeks|Ja|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van de server in milliseconden.|  
  
De hoofdtekst van het aanvraagbericht moet bestaan uit een **amqp-waardesectie** met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|dispositie|tekenreeks|Ja|Voltooid<br /><br /> Verlaten<br /><br /> Geschorst|  
|lock-tokens|array van uuid|Ja|Berichtenvergrendelingstokens om de status van dispositie bij te werken.|  
|deadletter-reden|tekenreeks|Nee|Kan worden ingesteld als de status van de dispositie is **ingesteld op opgeschort**.|  
|deadletter-beschrijving|tekenreeks|Nee|Kan worden ingesteld als de status van de dispositie is **ingesteld op opgeschort**.|  
|eigenschappen-om-te-wijzigen|map|Nee|Lijst met door servicebus bemiddelde berichteigenschappen die u wilt wijzigen.|  
  
#### <a name="response"></a>Antwoord  

Het antwoordbericht moet de volgende toepassingseigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-antwoordcode [RFC2616]<br /><br /> 200: OK – succes, anders mislukt|  
|statusBeschrijving|tekenreeks|Nee|Beschrijving van de status.|

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over AMQP en Service Bus naar de volgende links:

* [Overzicht servicebus AMQP]
* [AMQP 1.0-protocolhandleiding]
* [AMQP in servicebus voor Windows Server]

[Overzicht servicebus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protocolhandleiding]: service-bus-amqp-protocol-guide.md
[AMQP in servicebus voor Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
