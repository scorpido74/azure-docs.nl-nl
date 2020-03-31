---
title: Peer-ASN koppelen aan Azure-abonnement met de portal
titleSuffix: Azure
description: Peer-ASN koppelen aan Azure-abonnement met de portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912169"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Peer-ASN koppelen aan Azure-abonnement met de portal

Voordat u een peering-aanvraag indient, moet u uw ASN eerst koppelen aan Azure-abonnement via de onderstaande stappen.

Als u dat liever hebt, u deze handleiding voltooien met behulp van de [PowerShell.](howto-subscription-association-powershell.md)

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerAsn maken om uw ASN te koppelen aan Azure-abonnement

### <a name="sign-in-to-the-portal"></a>Inloggen op de portal
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Registreren voor peering resource provider
Registreer u voor peering resource provider in uw abonnement door de onderstaande stappen te volgen. Als u dit niet uitvoert, zijn Azure-resources die nodig zijn om peering in te stellen niet toegankelijk.

1. Klik op **Abonnementen** in de linkerbovenhoek van de portal. Als u het niet ziet, klikt u op **Meer services** en zoekt u ernaar.

    > [!div class="mx-imgBorder"]
    > ![Abonnementen openen](./media/rp-subscriptions-open.png)

1. Klik op het abonnement dat u wilt gebruiken voor peering.

    > [!div class="mx-imgBorder"]
    > ![Abonnement starten](./media/rp-subscriptions-launch.png)

1. Zodra het abonnement wordt geopend, klik je aan de linkerkant op **Resourceproviders**. Zoek vervolgens in het rechterdeelvenster naar *peering* in het zoekvenster of gebruik de schuifbalk om **Microsoft.Peering** te vinden en kijk naar de **status**. Als de status is ***geregistreerd,*** slaat u de onderstaande stappen over en gaat u verder met **sectie PeerAsn maken**. Als de status ***niet geregistreerd***is, selecteert u **Microsoft.Peering** en klikt u op **Registreren**.

    > [!div class="mx-imgBorder"]
    > ![Registratie start](./media/rp-register-start.png)

1. Houd er rekening mee dat de status verandert in ***Registreren***.

    > [!div class="mx-imgBorder"]
    > ![Registratie in uitvoering](./media/rp-register-progress.png)

1. Wacht tot een min of zo voor het om registratie te voltooien. Klik vervolgens op **Vernieuwen** en controleer of de status is ***geregistreerd.***

    > [!div class="mx-imgBorder"]
    > ![Inschrijving voltooid](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>PeerAsn maken
U een nieuwe PeerAsn-bron maken voor het koppelen van een ASN (Autonomous System Number) aan Azure-abonnementen. U meerdere ASN's aan een abonnement koppelen door een **PeerAsn** te maken voor elke ASN die u moet koppelen.

1. Klik **op Een resource maken** > **Zie alles**.

    > [!div class="mx-imgBorder"]
    > ![Zoeken peerasn](./media/peerasn-seeall.png)

1. Zoek naar *PeerAsn* in het zoekvak en druk op *Enter* op het toetsenbord. Klik in de resultaten op **PeerAsn-bron.**

    > [!div class="mx-imgBorder"]
    > ![PeerAsn starten](./media/peerasn-launch.png)

1. Zodra **PeerAsn** is gestart, klik op **Maken**.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn maken](./media/peerasn-create.png)

1. Vul op de **pagina Een peer ASN** koppelen onder het tabblad **Basisbeginselen** de velden in zoals hieronder wordt weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Basisbeginselen van PeerAsn](./media/peerasn-basics-tab.png)

    * **Naam** komt overeen met resource naam en kan alles wat je kiest.  
    * Kies het **abonnement** waaraan u de ASN moet koppelen.
    * **De naam** van uw bedrijf komt overeen met de naam van uw bedrijf en moet zo dicht mogelijk bij uw PeeringDB-profiel staan. Houd er rekening mee dat waarde alleen tekens a-z, A-Z en spatie ondersteunt
    * Voer uw ASN in het veld **Peer ASN** in.
    * Klik op **Nieuw maken** en voer **e-mailadres** en **telefoonnummer** in voor uw Network Operations Center (NOC)
1. Klik vervolgens op **Controleren + maken** en observeer dat portal de basisvalidatie van de ingevoerde informatie uitvoert. Dit wordt weergegeven in een lint aan de bovenkant, als *Laatste validatie uitvoeren...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad PeerAsn-beoordeling](./media/peerasn-review-tab-validation.png)

1. Zodra het bericht op het lint wordt ingeschakeld in *Validatie geslaagd,* controleert u uw gegevens en verzendt u de aanvraag door op **Maken te**klikken. Als de validatie niet doorgaat, klikt u op **Vorige** en herhaalt u de bovenstaande stappen om uw aanvraag te wijzigen en ervoor te zorgen dat de waarden die u invoert geen fouten hebben.

    > [!div class="mx-imgBorder"]
    > ![Tabblad PeerAsn-beoordeling](./media/peerasn-review-tab.png)

1. Nadat u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [Microsoft peering](mailto:peering@microsoft.com). Een succesvolle implementatie wordt hieronder weergegeven.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn Succes](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Status van een PeerAsn weergeven
Zodra peerasn-bron is geïmplementeerd, moet u wachten tot Microsoft de koppelingsaanvraag goedkeurt. Het kan tot 12 uur duren voor goedkeuring. Na goedkeuring ontvangt u een melding naar het e-mailadres dat in de bovenstaande sectie is ingevoerd.

> [!IMPORTANT]
> Wacht tot de validatiestatus 'Goedgekeurd' wordt ingeschakeld voordat u een peering-aanvraag indient. Het kan tot 12 uur duren voor deze goedkeuring.

## <a name="modify-peerasn"></a>PeerAsn wijzigen
Het wijzigen van PeerAsn wordt momenteel niet ondersteund. Als u wilt wijzigen, neemt u contact op met [Microsoft peering](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>PeerAsn verwijderen
Het verwijderen van een PeerAsn wordt momenteel niet ondersteund. Als u PeerAsn wilt verwijderen, neemt u contact op met [Microsoft peering](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Volgende stappen

* [Direct-peering maken of bewerken met de portal](howto-direct-portal.md)
* [Een verouderde Direct-peering converteren in een Azure-resource met de portal](howto-legacy-direct-portal.md)
* [Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
* [Een verouderde Exchange-peering converteren in een Azure-resource met de portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [veelgestelde vragen over internetpeering](faqs.md)