---
title: Peer ASN koppelen aan Azure-abonnement met PowerShell
titleSuffix: Azure
description: Peer ASN koppelen aan Azure-abonnement met PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75908988"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Peer ASN koppelen aan Azure-abonnement met PowerShell

Voordat u een peering-aanvraag indient, moet u uw ASN eerst koppelen aan Azure-abonnement via de onderstaande stappen.

Als u dat liever hebt, u deze handleiding voltooien via de [portal.](howto-subscription-association-portal.md)

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerASN maken om uw ASN te koppelen aan Azure-abonnement

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Registreren voor peering resource provider
Registreer u voor peering resource provider in uw abonnement met behulp van de onderstaande opdracht. Als u dit niet uitvoert, zijn Azure-resources die nodig zijn om peering in te stellen niet toegankelijk.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

U de registratiestatus controleren met de onderstaande opdrachten:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Wacht tot *RegistrationState* "Geregistreerd" wordt ingeschakeld voordat u verdergaat. Het kan 5 tot 30 minuten duren nadat u de opdracht hebt uitgevoerd.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>De peer-informatie die aan dit abonnement is gekoppeld bijwerken

Hieronder vindt u een voorbeeld om peer-informatie bij te werken.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Naam komt overeen met de naam van de resource en kan alles zijn wat je kiest. PeerName komt echter overeen met de naam van uw bedrijf en moet zo dicht mogelijk bij uw PeeringDB-profiel staan. Houd er rekening mee dat waarde voor -peerName alleen tekens a-z, A-Z en ruimte ondersteunt.

Een abonnement kan meerdere ASN's hebben. Werk de peering-informatie voor elke ASN bij. Zorg ervoor dat "naam" uniek is voor elke ASN.

Peers zullen naar verwachting een volledig en up-to-date profiel hebben op [PeeringDB](https://www.peeringdb.com). We gebruiken deze informatie tijdens de registratie om de gegevens van de peer te valideren, zoals NOC-informatie, technische contactgegevens en hun aanwezigheid in de peeringfaciliteiten, enz.

Houd er rekening mee dat in plaats van **{subscriptionId}** in de bovenstaande uitvoer de werkelijke abonnements-ID wordt weergegeven.

## <a name="view-status-of-a-peerasn"></a>Status van een PeerASN weergeven

Controleer op de status ASN-validatie met de onderstaande opdracht:

```powershell
Get-AzPeerAsn
```

Hieronder is een voorbeeld reactie:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Wacht tot de validatiestatus 'Goedgekeurd' wordt ingeschakeld voordat u een peering-aanvraag indient. Het kan tot 12 uur duren voor deze goedkeuring.

## <a name="modify-peerasn"></a>PeerAsn wijzigen
U noc-contactgegevens op elk gewenst moment wijzigen.

Hieronder is een voorbeeld:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>PeerAsn verwijderen
Het verwijderen van een PeerASN wordt momenteel niet ondersteund. Als u PeerASN wilt verwijderen, neemt u contact op met [Microsoft peering](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Volgende stappen

* [Direct-peering maken of bewerken](howto-direct-powershell.md)
* [Een verouderde Direct-peering converteren in een Azure-resource](howto-legacy-direct-powershell.md)
* [Exchange-peering maken of wijzigen](howto-exchange-powershell.md)
* [Een verouderde Exchange-peering converteren in een Azure-resource](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [veelgestelde vragen over internetpeering](faqs.md)
