---
title: Peer-ASN koppelen aan een Azure-abonnement met behulp van de portal
titleSuffix: Azure
description: Peer-ASN koppelen aan een Azure-abonnement met behulp van de portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d3737be5a3186774f230aef9d932464a27a764f4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775639"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Peer-ASN koppelen aan een Azure-abonnement met behulp van de portal

Voordat u een peering-aanvraag indient, moet u eerst uw ASN met het Azure-abonnement koppelen met behulp van de onderstaande stappen.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van [Power shell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerAsn maken om uw ASN te koppelen aan een Azure-abonnement

### <a name="sign-in-to-the-portal"></a>Aanmelden bij de portal
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-peerasn"></a>PeerAsn maken
U kunt een nieuwe PeerAsn-resource maken voor het koppelen van een autonoom systeem nummer (ASN) aan een Azure-abonnement. U kunt meerdere Asn's koppelen aan een abonnement door een **PeerAsn** te maken voor elke ASN die u moet koppelen.

1. Klik op **een resource maken** > **alles weer te geven**.

    > [!div class="mx-imgBorder"]
    > ![Search PeerAsn](./media/peerasn-seeall.png)

1. Zoek naar *PeerAsn* in het zoekvak en druk op *Enter* op het toetsen bord. Klik in de resultaten op **PeerAsn** resource.

    > [!div class="mx-imgBorder"]
    > PeerAsn](./media/peerasn-launch.png) ![starten

1. Wanneer **PeerAsn** wordt gestart, klikt u op **maken**.

    > [!div class="mx-imgBorder"]
    > PeerAsn](./media/peerasn-create.png) ![maken

1. Vul op de pagina **een peer-ASN koppelen** onder het tabblad **basis beginselen** de velden in zoals hieronder wordt weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![tabblad basis PeerAsns](./media/peerasn-basics-tab.png)

    * De **naam** komt overeen met de resource naam en kan alles zijn wat u kiest.  
    * Kies het **abonnement** waaraan u het ASN wilt koppelen.
    * De naam van de **peer** komt overeen met de naam van uw bedrijf en moet zo dicht mogelijk bij uw PeeringDB-profiel staan. Houd er rekening mee dat waarde alleen de tekens a-z, A-Z en spatie ondersteunt
    * Voer uw ASN in het veld **peer ASN** in.
    * Klik op **nieuwe maken** en voer een **e-mail adres** en **telefoon nummer** in voor uw netwerk Operations Center (NOC)
1. Klik vervolgens op **controleren + maken** en houd er rekening mee dat de basis validatie van de gegevens die u hebt ingevoerd, wordt uitgevoerd. Dit wordt weer gegeven in een lint bovenaan, zoals het *uitvoeren van definitieve validatie...* .

    > [!div class="mx-imgBorder"]
    > ![tabblad controleren van PeerAsn](./media/peerasn-review-tab-validation.png)

1. Wanneer het bericht in het lint wordt *gevalideerd*, controleert u uw gegevens en verzendt u de aanvraag door op **maken**te klikken. Als de validatie niet is geslaagd, klikt u op **vorige** en herhaalt u de bovenstaande stappen om uw aanvraag aan te passen en te controleren of de ingevoerde waarden geen fouten bevatten.

    > [!div class="mx-imgBorder"]
    > ![tabblad controleren van PeerAsn](./media/peerasn-review-tab.png)

1. Nadat u de aanvraag hebt ingediend, wacht u totdat de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com). Er wordt een geslaagde implementatie weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn geslaagd](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>De status van een PeerAsn weer geven
Zodra de PeerAsn-resource is geïmplementeerd, moet u wachten tot micro soft de koppelings aanvraag heeft goedgekeurd. Het kan tot 12 uur duren voordat goed keuring is uitgevoerd. Na goed keuring ontvangt u een melding voor het e-mail adres dat is ingevoerd in de bovenstaande sectie.

> [!IMPORTANT]
> Wacht totdat de ValidationState is ingeschakeld voordat een peering-aanvraag wordt verzonden. Het kan tot 12 uur duren voordat deze goed keuring is uitgevoerd.

## <a name="modify-peerasn"></a>PeerAsn wijzigen
Het wijzigen van PeerAsn wordt momenteel niet ondersteund. Als u wijzigingen wilt aanbrengen, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>PeerAsn verwijderen
Het is momenteel niet mogelijk om een PeerAsn te verwijderen. Als u PeerAsn moet verwijderen, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Volgende stappen

* [Een directe peering maken of wijzigen met behulp van de portal](howto-direct-portal.md)
* [Een verouderde directe peering naar een Azure-resource converteren met behulp van de portal](howto-legacy-direct-portal.md)
* [Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
* [Een verouderde uitwisseling van Exchange naar Azure-resource converteren met behulp van de portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [Veelgestelde vragen over Internet peering](faqs.md)