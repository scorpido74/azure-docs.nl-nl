---
title: Een gekoppelde service inrichten en beveiligen in azure Synapse Analytics
description: Meer informatie over het inrichten en beveiligen van een gekoppelde service met beheerde Vnet
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81430550"
---
# <a name="securing-a-linked-service-with-private-links"></a>Een gekoppelde service beveiligen met persoonlijke koppelingen 

In dit artikel wordt beschreven hoe u een gekoppelde service in Synapse kunt beveiligen met een persoonlijk eind punt.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* **Azure Storage account**: u Azure data Lake gen 2 gebruiken als een *brongegevens* opslag. Als u geen opslag account hebt, raadpleegt u [een Azure Storage-account maken](../../storage/blobs/data-lake-storage-quickstart-create-account.md) om er een te maken. Zorg ervoor dat het opslag account de Synapse Studio-IP-filtering heeft voor toegang tot de service en dat u alleen **geselecteerde netwerken** toegang wilt geven tot het opslag account. De instelling onder de Blade **firewalls en virtuele netwerken** moet er ongeveer uitzien als in de onderstaande afbeelding.

![Account voor beveiligde opslag](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Een gekoppelde service maken met persoonlijke koppelingen

In azure Synapse Analytics is een gekoppelde service waar u de verbindings gegevens voor andere services definieert. In deze sectie voegt u Azure Synapse Analytics en Azure Data Lake gen 2 toe als gekoppelde services.

1. Open Azure Synapse Studio en ga naar het tabblad **beheren** .
1. Onder **externe verbindingen**selecteert u **gekoppelde services**.
1. Klik op **Nieuw**om een gekoppelde service toe te voegen.
1. Selecteer de tegel Azure Data Lake Storage Gen2 in de lijst en klik op **door gaan**.
1. Zorg ervoor dat u **interactieve ontwerp**functie inschakelt. Het kan ongeveer 1 minuut duren voordat deze is ingeschakeld. 
1. Voer uw verificatie referenties in. Account sleutel, Service-Principal en beheerde identiteit worden momenteel ondersteunde verificatie typen. Klik op verbinding testen om te controleren of uw referenties correct zijn.
1. Selecteer **verbinding testen**, de fout moet mislukken omdat het opslag account geen toegang inschakelt zonder een persoonlijk eind punt te maken en goed te keuren. In het fout bericht wordt een koppeling weer gegeven om een **persoonlijk eind punt** te maken dat u kunt volgen om naar het volgende deel te gaan. Sla het volgende gedeelte over als u deze koppeling volgt.
1. Selecteer **Maken** nadat dit is voltooid.

## <a name="create-a-managed-private-endpoint"></a>Een beheerd privé-eind punt maken

In het geval kunt u niet klikken op de Hyper link bij het testen van de bovenstaande verbinding, gevolgd door het volgende pad. Nu moet u een beheerd privé-eind punt maken dat u maakt verbinding met de gekoppelde service die hierboven is gemaakt.

1. Ga naar het tabblad **beheren** .
1. Ga naar de sectie **beheerde virtuele netwerken** .
1. Selecteer **+ Nieuw** onder beheerd persoonlijk eind punt.
1. Selecteer de tegel Azure Data Lake Storage Gen2 in de lijst en selecteer **door gaan**.
1. Voer de naam in van het opslag account dat u hierboven hebt gemaakt.
1. Selecteer **Maken**
1. U moet zien nadat u enkele seconden hebt gewacht dat de persoonlijke koppeling een goed keuring vereist.

## <a name="approval-of-a-private-link"></a>Goed keuring van een persoonlijke koppeling
1. Selecteer het persoonlijke eind punt dat u hierboven hebt gemaakt. U kunt een Hyper link zien waarmee u het persoonlijke eind punt kunt goed keuren op het niveau van het opslag account. *U kunt ook rechtstreeks naar het Azure Portal Storage-account gaan en naar de Blade **persoonlijke eindpunt verbindingen** gaan.*
1. Tik het persoonlijke eind punt dat u hebt gemaakt in de studio en selecteer **goed keuren**.
1. Een beschrijving toevoegen en op **Ja** klikken
1. Ga terug naar Synapse Studio in het gedeelte **beheerde virtuele netwerken** van het tabblad **beheren**.
1. Het duurt ongeveer 1 minuut voordat de goed keuring voor uw persoonlijke eind punt wordt weer gegeven.

## <a name="check-the-connection-works"></a>Controleer of de verbinding werkt
1. Ga naar het tabblad **beheren** en selecteer de gekoppelde service die u hebt gemaakt.
1. Zorg ervoor dat **interactieve ontwerpen** actief is.
1. Selecteer **Verbinding testen**. U ziet dat de verbinding slaagt.

U hebt nu een beveiligde en particuliere verbinding tot stand gebracht tussen Synapse en uw gekoppelde service.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het beheerde persoonlijke eind punt in Synapse Analytics raadpleegt u het artikel [over het concept over Synapse Managed private endpoint](data-integration-data-lake.md) .

Zie de [gegevens opnemen in een Data Lake](data-integration-data-lake.md) artikel voor meer informatie over gegevens integratie voor Synapse Analytics.