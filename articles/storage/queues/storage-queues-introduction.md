---
title: Inleiding tot Azure-wachtrijen - Azure Storage
description: Inleiding tot Azure-wachtrijen
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: cbrooks
ms.openlocfilehash: 4a2bea77578282d68d86bc1a8cea765aa2cbd555
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80060856"
---
# <a name="what-are-azure-queues"></a>Wat zijn Azure-wachtrijen?

Azure Queue Storage is een service om grote aantallen berichten op te slaan. U hebt overal ter wereld toegang tot berichten via geverifieerde oproepen via HTTP of HTTPS. Een wachtrijbericht kan maximaal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Wachtrijen worden vaak gebruikt om een werkachterstand te maken om asynchroon te verwerken.

## <a name="queue-service-concepts"></a>Serviceconcepten voor wachtrijen

De Queue-service bevat de volgende onderdelen:

![Diagram met de relatie tussen een opslagaccount, wachtrijen en berichten](./media/storage-queues-introduction/queue1.png)

* **URL-indeling:** Wachtrijen kunnen worden opgevraagd met de volgende URL-indeling:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Met de volgende URL wordt een wachtrij in het diagram opgevraagd:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Opslagaccount:** Alle toegang tot Azure Storage gebeurt via een opslagaccount. Zie [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts voor](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)informatie over de capaciteit van het opslagaccount.

* **Wachtrij:** Een wachtrij bevat een set berichten. De naam van de wachtrij **moet** alle kleine letters zijn. Zie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Wachtrijen en metagegevens een naam geven) voor informatie over de naamgeving van wachtrijen.

* **Bericht:** Een bericht in een willekeurige indeling, van maximaal 64 KB. Vóór versie 2017-07-29 is de maximale toegestane tijd tot leven zeven dagen. Voor versie 2017-07-29 of hoger kan de maximale time-to-live een positief getal zijn, of -1 die aangeeft dat het bericht niet verloopt. Als deze parameter wordt weggelaten, is de standaard time-to-live zeven dagen.

## <a name="next-steps"></a>Volgende stappen

* [Een opslagaccount maken](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Aan de slag met Wachtrijen met .NET](storage-dotnet-how-to-use-queues.md)
