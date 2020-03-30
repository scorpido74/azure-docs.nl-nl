---
title: 'Snelstart: gebeurtenissen verzenden met C - Azure Event Hubs'
description: 'Snelstart: in dit artikel wordt een walkthrough gemaakt voor het maken van een C-toepassing die gebeurtenissen naar Azure Event Hubs verzendt.'
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720673"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Snelstart: gebeurtenissen verzenden naar Azure-gebeurtenishubs met C

## <a name="introduction"></a>Inleiding
Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze zelfstudie wordt beschreven hoe u gebeurtenissen naar een gebeurtenishub verzendt met behulp van een consoletoepassing in C. 

## <a name="prerequisites"></a>Vereisten
Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een C ontwikkelomgeving. In deze zelfstudie wordt ervan uitgegaan dat de gcc-stack op een Azure Linux VM met Ubuntu 14.04 wordt gebruikt.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Maak een naamruimte voor gebeurtenishubs en een gebeurtenishub**. Gebruik de [Azure-portal](https://portal.azure.com) om een naamruimte van het type Gebeurtenishubs te maken en de beheerreferenties te verkrijgen die uw toepassing nodig heeft om te communiceren met de gebeurtenishub. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Download de waarde van de toegangssleutel voor de gebeurtenishub door instructies uit het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de toegangssleutel in de code die u later in deze zelfstudie schrijft. De standaardsleutelnaam is: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Code schrijven om berichten naar gebeurtenishubs te verzenden
In deze sectie ziet u hoe u een C-app schrijft om gebeurtenissen naar uw gebeurtenishub te verzenden. De code maakt gebruik van de Proton AMQP-bibliotheek van het [Apache Qpid-project.](https://qpid.apache.org/) Dit is analoog aan het gebruik van Service Bus wachtrijen en onderwerpen met AMQP van C zoals weergegeven [in dit voorbeeld](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Zie voor meer informatie de [Qpid Proton-documentatie.](https://qpid.apache.org/proton/index.html)

1. Volg vanaf de [Qpid AMQP Messenger-pagina](https://qpid.apache.org/proton/messenger.html)de instructies om Qpid Proton te installeren, afhankelijk van uw omgeving.
2. Als u de Proton-bibliotheek wilt compileren, installeert u de volgende pakketten:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Download de [Qpid Proton-bibliotheek](https://qpid.apache.org/proton/index.html)en haal deze uit, bijvoorbeeld:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Maak een build directory, compileren en installeren:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Maak in uw werkmap een nieuw bestand genaamd **sender.c** met de volgende code. Vergeet niet de waarden voor uw SAS-sleutel/-naam, naam van de gebeurtenishub en naamruimte te vervangen. U moet ook een door de URL gecodeerde versie van de sleutel vervangen door de eerder gemaakte **sendrule.** U URL-coderen [het hier](https://www.w3schools.com/tags/ref_urlencode.asp).
   
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
6. Compileer het bestand, ervan uitgaande **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Deze code maakt gebruik van een uitgaande venster van 1 om de berichten zo snel mogelijk uit te dwingen. Het wordt aanbevolen dat uw toepassing probeert berichten te batchn om de doorvoer te verhogen. Zie de [Qpid AMQP Messenger-pagina](https://qpid.apache.org/proton/messenger.html) voor informatie over het gebruik van de Qpid Proton-bibliotheek in deze en andere omgevingen en van platforms waarvoor bindingen worden verstrekt (momenteel Perl, PHP, Python en Ruby).

Voer de toepassing uit om berichten naar de gebeurtenishub te verzenden. 

Gefeliciteerd! U hebt nu berichten verzonden naar een Event Hub.

## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
