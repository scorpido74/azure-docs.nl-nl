---
title: Een gekoppelde service inrichten en beveiligen in Azure Synapse Analytics
description: Meer informatie over het inrichten en beveiligen van een gekoppelde service met Managed Vnet
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430550"
---
# <a name="securing-a-linked-service-with-private-links"></a>Een gekoppelde service beveiligen met Private Links 

In dit artikel leert u hoe u een gekoppelde service in Synapse beveiligen met een privéeindpunt.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* **Azure Storage-account**: U gebruikt Azure Data Lake Gen 2 als *brongegevensarchief.* Zie [Een Azure Storage-account maken](../../storage/blobs/data-lake-storage-quickstart-create-account.md) voor stappen om er een te maken als u geen opslagaccount hebt. Zorg ervoor dat het opslagaccount de IP-filtering van Synapse Studio heeft om toegang te krijgen tot het account en dat u **geselecteerde netwerken** alleen toegang geeft tot het Opslagaccount. De instelling onder het blad **Firewalls en virtuele netwerken** moet lijken op de foto hieronder.

![Beveiligde opslagaccount](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Een gekoppelde service maken met privékoppelingen

In Azure Synapse Analytics definieert u in Azure Synapse Analytics uw verbindingsgegevens met andere services. In deze sectie voegt u Azure Synapse Analytics en Azure Data Lake Gen 2 toe als gekoppelde services.

1. Open de Azure Synapse Studio en ga naar het tabblad **Beheren.**
1. Selecteer **Linked Services**onder **Externe verbindingen**.
1. Als u een gekoppelde service wilt toevoegen, klikt u op **Nieuw**.
1. Selecteer de tegel Azure Data Lake Storage Gen2 in de lijst en klik op **Doorgaan**.
1. Zorg ervoor dat u **Interactief ontwerpen**inschakelt. Het kan ongeveer 1 minuut duren om ingeschakeld te worden. 
1. Voer uw verificatiereferenties in. Accountsleutel, serviceprincipal en beheerde identiteit worden momenteel ondersteunde verificatietypen. Klik op testverbinding om te controleren of uw referenties correct zijn.
1. Selecteer **Testverbinding**, deze moet mislukken omdat het opslagaccount geen toegang tot het account inschakelt zonder de aanmaak en goedkeuring van een privéeindpunt. In het foutbericht ziet u een koppeling om een **privéeindpunt** te maken dat u volgen om naar het volgende deel te gaan. Als u die koppeling volgt, slaat u het volgende deel over.
1. Selecteer **Maken** nadat dit is voltooid.

## <a name="create-a-managed-private-endpoint"></a>Een beheerd privéeindpunt maken

Als u niet in de hyperlink hebt geklikt bij het testen van de bovenstaande verbinding, volgt u het volgende pad. Nu moet u een beheerd privéeindpunt maken dat u verbinding maakt met de bovenstaande gekoppelde service.

1. Ga naar het tabblad **Beheren.**
1. Ga naar de sectie **Beheerde virtuele netwerken.**
1. Selecteer **+ Nieuw** onder Privéeindpunt beheer.
1. Selecteer de tegel Azure Data Lake Storage Gen2 in de lijst en selecteer **Doorgaan**.
1. Voer de naam in van het opslagaccount dat u hierboven hebt gemaakt.
1. Selecteer **Maken**
1. U moet na enkele seconden wachten zien dat de privékoppeling die is gemaakt, een goedkeuring nodig heeft.

## <a name="approval-of-a-private-link"></a>Goedkeuring van een privéverbinding
1. Selecteer het privéeindpunt dat u hierboven hebt gemaakt. U een hyperlink zien waarmee u het privéeindpunt goedkeuren op het niveau opslagaccount. *Een alternatief is om rechtstreeks naar het Azure-portal-opslagaccount te gaan en naar het **blade voor privé-eindpuntverbindingen** te gaan.*
1. Vink het privéeindpunt aan dat u in de studio hebt gemaakt en selecteer **Goedkeuren**.
1. Een beschrijving toevoegen en op **ja** klikken
1. Ga terug naar Synapse Studio in onder de sectie **Beheerde virtuele netwerken** van het tabblad **Beheren.**
1. Het duurt ongeveer 1 minuut om de goedkeuring weerspiegeld voor uw prive-eindpunt te krijgen.

## <a name="check-the-connection-works"></a>Controleer of de verbinding werkt
1. Ga naar het tabblad **Beheren** en selecteer de gekoppelde service die u hebt gemaakt.
1. Zorg ervoor dat **interactief ontwerpen** actief is.
1. Selecteer **Verbinding testen**. U moet zien dat de verbinding succesvol is.

U hebt nu een veilige en privéverbinding tot stand gebracht tussen Synapse en uw gekoppelde service!

## <a name="next-steps"></a>Volgende stappen

Zie het [concept rond Synapse Managed private endpoint-artikel](data-integration-data-lake.md) om meer inzicht te krijgen in Managed private endpoint in Synapse Analytics.

Zie de [gegevens in een Data Lake-artikel](data-integration-data-lake.md) voor meer informatie over gegevensintegratie voor Synapse Analytics.