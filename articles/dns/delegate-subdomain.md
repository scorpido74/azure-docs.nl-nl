---
title: Een subdomein delegeren - Azure DNS
description: Ga met dit leerpad aan de slag met het delegeren van een Azure DNS-subdomein.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: cd1443a9ca8ccf7172072078734f21d789d36194
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937435"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Een Azure DNS-subdomein delegeren

U de Azure-portal gebruiken om een DNS-subdomein te delegeren. Als u bijvoorbeeld eigenaar bent van het contoso.com-domein, u een subdomein met de naam *engineering* delegeren aan een andere, afzonderlijke zone die u afzonderlijk van de contoso.com-zone beheren.

U desgevraagd een subdomein delegeren met [Azure PowerShell.](delegate-subdomain-ps.md)

## <a name="prerequisites"></a>Vereisten

Als u een Azure DNS-subdomein wilt delegeren, moet u eerst uw openbare domein delegeren aan Azure DNS. Zie [Een domein delegeren aan Azure DNS](./dns-delegate-domain-azure-dns.md) voor instructies over het configureren van uw naamservers voor overdracht. Zodra uw domein is gedelegeerd aan uw Azure DNS-zone, u uw subdomein delegeren.

> [!NOTE]
> Contoso.com wordt gebruikt als een voorbeeld in dit artikel. Vervang uw eigen domeinnaam door contoso.com.

## <a name="create-a-zone-for-your-subdomain"></a>Een zone voor uw subdomein maken

Maak eerst de zone voor het **subdomein engineering.**

1. Selecteer in de Azure-portal de optie **Een resource maken**.
2. Typ **DNS**en selecteer **DNS-zone**in het zoekvak .
3. Selecteer **Maken**.
4. Typ **engineering.contoso.com** in het deelvenster **DNS-zone maken** in het tekstvak **Naam.**
5. Selecteer de resourcegroep voor uw zone. U dezelfde resourcegroep als de bovenliggende zone gebruiken om vergelijkbare resources bij elkaar te houden.
6. Klik **op Maken**.
7. Nadat de implementatie is geslaagd, gaat u naar de nieuwe zone.

## <a name="note-the-name-servers"></a>Let op de naamservers

Noteer vervolgens de vier naamservers voor het subdomein engineering.

Noteer in het deelvenster **technische** zones de vier naamservers voor de zone. U gebruikt deze naamservers later.

## <a name="create-a-test-record"></a>Een testrecord maken

Maak een **A-record** om te gebruiken voor het testen. Maak bijvoorbeeld een **www** A-record en configureer deze met een **IP-adres van 10.10.10.10.**

## <a name="create-an-ns-record"></a>Een NS-record maken

Maak vervolgens een NS-record (name server) voor de **engineeringzone.**

1. Navigeer naar de zone voor het bovenliggende domein.
2. Selecteer **Recordset toevoegen**.
3. Typ **engineering** in het **deelvenster Record toevoegen** in het tekstvak **Naam.**
4. Selecteer **NS**voor **Type**.
5. Voer onder **Naamserver**de vier naamservers in die u eerder vanuit de **engineeringzone** hebt opgenomen.
6. Klik op **OK**.

## <a name="test-the-delegation"></a>Test de delegatie

Gebruik nslookup om de delegatie te testen.

1. Open een PowerShell-venster.
2. Typ bij opdrachtprompt`nslookup www.engineering.contoso.com.`
3. U ontvangt een niet-gezaghebbend antwoord met het adres **10.10.10.10**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren van reverse DNS voor services die worden gehost in Azure.](dns-reverse-dns-for-azure-services.md)