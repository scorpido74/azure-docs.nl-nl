---
title: Inleiding tot Azure-wacht rijen-Azure Storage
description: Inleiding tot Azure-wacht rijen
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: cbrooks
ms.openlocfilehash: 4a2bea77578282d68d86bc1a8cea765aa2cbd555
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80060856"
---
# <a name="what-are-azure-queues"></a>Wat zijn Azure-wachtrijen?

Azure Queue Storage is een service om grote aantallen berichten op te slaan. U opent berichten van overal ter wereld via geverifieerde oproepen met HTTP of HTTPS. Een wachtrij bericht kan Maxi maal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteits limiet van een opslag account. Wacht rijen worden vaak gebruikt om een achterstand te maken voor het asynchroon verwerken van werk.

## <a name="queue-service-concepts"></a>Queue-service concepten

De Queue-service bevat de volgende onderdelen:

![Diagram van de relatie tussen een opslag account, wacht rijen en berichten](./media/storage-queues-introduction/queue1.png)

* **URL-indeling:** Wachtrijen kunnen worden opgevraagd met de volgende URL-indeling:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Met de volgende URL wordt een wachtrij in het diagram opgevraagd:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Opslag account:** Alle toegang tot Azure Storage wordt uitgevoerd via een opslag account. Zie [schaalbaarheids-en prestatie doelen voor standaard opslag accounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)voor meer informatie over de capaciteit van het opslag account.

* **Wachtrij:** Een wachtrij bevat een set berichten. De naam van de wachtrij **mag** alleen kleine letters bevatten. Zie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Wachtrijen en metagegevens een naam geven) voor informatie over de naamgeving van wachtrijen.

* **Bericht:** Een bericht in een willekeurige indeling, van maximaal 64 KB. Voor versie 2017-07-29 is de Maxi maal toegestane time-to-Live 7 dagen. Voor versie 2017-07-29 of hoger kan het maximum aantal time-to-Live elk positief getal zijn of-1 waarmee wordt aangegeven dat het bericht niet verloopt. Als deze para meter wordt wegge laten, is de standaard time-to-Live zeven dagen.

## <a name="next-steps"></a>Volgende stappen

* [Create a storage account](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Aan de slag met wacht rijen met .NET](storage-dotnet-how-to-use-queues.md)
