---
title: Apache Kafka ingeschakelde Event Hub-Azure Event Hubs maken | Microsoft Docs
description: Dit artikel bevat een overzicht van het maken van een Apache Kafka Azure Event Hubs-naam ruimte die is ingeschakeld met behulp van de Azure Portal.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 9ce0f74ec6d4e536bfb3fe827ae6f8ae143b640e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555813"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Apache Kafka ingeschakelde Event hubs maken

Azure Event Hubs is een Big data streaming platform as a Service (PaaS) dat miljoenen gebeurtenissen per seconde opneemt en een lage latentie en hoge door Voer voor realtime analyse en visualisatie biedt.

Azure Event Hubs biedt u een Kafka-eind punt. Met dit eind punt kan uw Event Hubs naam ruimte systeem eigen inzicht krijgen in [Apache Kafka](https://kafka.apache.org/intro) bericht protocol en api's. Met deze mogelijkheid kunt u met uw event hubs communiceren zoals u zou doen met Kafka-onderwerpen zonder uw protocol-clients te wijzigen of uw eigen clusters uit te voeren. Event Hubs ondersteunt [Apache Kafka versie 1,0](https://kafka.apache.org/10/documentation.html) en hoger.

In dit artikel wordt beschreven hoe u een Event Hubs naam ruimte maakt en hoe u de connection string nodig hebt om verbinding te maken tussen Kafka-toepassingen en Kafka-Event hubs.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken waarvoor Kafka is ingeschakeld

1. Meld u aan bij de [Microsoft Azure-portal][Azure portal] en klik op **Een resource maken** linksboven in het scherm.

2. Zoek naar Event Hubs en selecteer de hier getoonde opties:
    
    ![Zoeken naar Event Hubs in de portal](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Geef een unieke naam op en schakel Kafka in op de naamruimte. Klik op **Maken**.
    
    ![Een naamruimte maken](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Als de naamruimte is gemaakt, klikt u op het tabblad **Instellingen** op **Beleid voor gedeelde toegang** om de verbindingsreeks op te halen.

    ![Klikken op Beleid voor gedeelde toegang](./media/event-hubs-create/create-event-hub7.png)

5. U kunt de standaardwaarde **RootManageSharedAccessKey** kiezen of een nieuwe beleid toevoegen. Klik op de beleidsnaam en kopieer de verbindingsreeks. 
    
    ![Beleid selecteren](./media/event-hubs-create/create-event-hub8.png)
 
6. Voeg deze verbindingsreeks toe aan de Kafka-toepassingsconfiguratie.

U kunt nu gebeurtenissen vanaf uw toepassing, waarbij gebruikgemaakt wordt van het Kafka-protocol, naar Event Hubs streamen.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over Event Hubs naar deze koppelingen:

* [Streamen naar Event Hubs vanaf uw Kafka-toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Meer informatie over Event Hubs for Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Meer informatie over Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
