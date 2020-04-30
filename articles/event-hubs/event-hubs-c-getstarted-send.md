---
title: 'Quick Start: gebeurtenissen verzenden met C-Azure Event Hubs'
description: 'Snelstartgids: in dit artikel vindt u een overzicht van het maken van een C-toepassing die gebeurtenissen naar Azure Event Hubs verzendt.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 5bd4bb66b7e3c3ec37724f8684105befbc9132ff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73720673"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Quick Start: gebeurtenissen verzenden naar Azure Event Hubs met C

## <a name="introduction"></a>Inleiding
Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze zelf studie wordt beschreven hoe u gebeurtenissen naar een Event Hub verzendt met behulp van een console toepassing in C. 

## <a name="prerequisites"></a>Vereisten
Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een C-ontwikkel omgeving. In deze zelf studie wordt ervan uitgegaan dat de gcc-stack op een Azure Linux-VM met Ubuntu 14,04.
* [Micro soft Visual Studio](https://www.visualstudio.com/).
* **Een event hubs naam ruimte en een event hub maken**. Gebruik de [Azure Portal](https://portal.azure.com) om een naam ruimte van het type Event hubs te maken en de beheer referenties te verkrijgen die uw toepassing nodig heeft om met de Event hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Haal de waarde van de toegangs sleutel voor de Event Hub door de instructies in het artikel: [Get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de toegangs sleutel in de code die u verderop in deze zelf studie schrijft. De naam van de standaard sleutel is: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Code schrijven om berichten te verzenden naar Event Hubs
In deze sectie wordt beschreven hoe u een C-app schrijft voor het verzenden van gebeurtenissen naar uw Event Hub. De code maakt gebruik van de Proton AMQP-bibliotheek van het [Apache Qpid-project](https://qpid.apache.org/). Dit komt overeen met het gebruik van Service Bus-wacht rijen en-onderwerpen met AMQP van C, zoals [in dit voor beeld](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)wordt weer gegeven. Zie de [QPid Proton-documentatie](https://qpid.apache.org/proton/index.html)voor meer informatie.

1. Volg de instructies voor het installeren van QPid proton op de [pagina QPID AMQP Mess enger](https://qpid.apache.org/proton/messenger.html), afhankelijk van uw omgeving.
2. Als u de Proton-bibliotheek wilt compileren, installeert u de volgende pakketten:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Down load de [QPid Proton-bibliotheek](https://qpid.apache.org/proton/index.html)en pak deze uit, bijvoorbeeld:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Een build-Directory maken, compileren en installeren:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Maak in uw werkmap een nieuw bestand met de naam **Sender. c** met de volgende code. Vergeet niet om de waarden voor uw SAS-sleutel/naam, Event Hub naam en naam ruimte te vervangen. U moet ook een URL-gecodeerde versie van de sleutel vervangen voor de **SendRule** die u eerder hebt gemaakt. U kunt de URL [hier](https://www.w3schools.com/tags/ref_urlencode.asp)coderen.
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Compileer het bestand, aangenomen **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Deze code maakt gebruik van een uitgaand venster van 1 om de berichten zo snel mogelijk af te dwingen. Het is raadzaam dat uw toepassing batch berichten probeert op te nemen om de door voer te verbeteren. Zie de [QPID AMQP Messenger-pagina](https://qpid.apache.org/proton/messenger.html) voor informatie over het gebruik van de QPid Proton-bibliotheek in deze en andere omgevingen, en van platforms waarvoor bindingen worden verstrekt (momenteel perl, PHP, python en Ruby).

Voer de toepassing uit om berichten naar de Event Hub te verzenden. 

Gefeliciteerd! U hebt nu berichten verzonden naar een Event Hub.

## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event hubs](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
