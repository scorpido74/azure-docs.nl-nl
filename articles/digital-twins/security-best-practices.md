---
title: Inzicht in aanbevolen procedures voor beveiliging - Azure Digital Twins | Microsoft Documenten
description: Meer informatie over aanbevolen procedures voor beveiliging voor Azure Digital Twins en het Internet of Things.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122556"
---
# <a name="azure-digital-twins-security-best-practices"></a>Aanbevolen procedures voor azure Digital Twins-beveiliging

Azure Digital Twins-beveiliging maakt nauwkeurige toegang tot specifieke bronnen en acties in uw IoT-grafiek mogelijk. Het doet dit door middel van gedetailleerde rol en toestemmingbeheer genaamd [role-based access control](./security-role-based-access-control.md).

Azure Digital Twins maakt ook gebruik van andere beveiligingsfuncties die aanwezig zijn in Azure IoT, waaronder Azure Active Directory (Azure AD). Om die reden omvat het configureren en beveiligen van toepassingen die zijn gebouwd op Azure Digital Twins het gebruik van veel van dezelfde [Azure IoT-beveiligingspraktijken](../iot-fundamentals/iot-security-best-practices.md) die momenteel worden aanbevolen.

In dit artikel worden de belangrijkste aanbevolen procedures samengevat die u volgen.

> [!IMPORTANT]
> Als u maximale beveiliging voor uw IoT-ruimte wilt garanderen, controleert u extra beveiligingsbronnen. Zorg ervoor dat u uw apparaatleveranciers opneemt.

> [!TIP]
> Gebruik [Azure Security Center voor IoT](https://docs.microsoft.com/azure/asc-for-iot/) om IoT-beveiligingsbedreigingen en -kwetsbaarheden te detecteren.

## <a name="iot-security-best-practices"></a>Best practices voor IoT-beveiliging

Enkele belangrijke praktijken om uw IoT-apparaten veilig te beveiligen, zijn:

> [!div class="checklist"]
> * Beveilig elk apparaat dat is verbonden met uw IoT-ruimte op een tamper-proof manier.
> * Beperk de rol van elk apparaat, sensor en persoon binnen uw IoT-ruimte. Als het wordt aangetast, wordt het effect geminimaliseerd.
> * Houd rekening met het mogelijke gebruik van apparaat-IP-adresfiltering en poortbeperking.
> * Beperk de i/o- en apparaatbandbreedte om de prestaties te verbeteren. Tariefbeperking kan de beveiliging verbeteren door denial-of-service-aanvallen te voorkomen.
> * Houd de firmware, het besturingssysteem en de software up-to-date.
> * Controleer en controleer regelmatig de best practices voor apparaat-, software-, netwerk- en gatewaybeveiliging terwijl deze blijven verbeteren en evolueren.
> * Gebruik vertrouwde, gecertificeerde en compatibele beveiligingssystemen, software en apparaten. Bekijk bijvoorbeeld [de nalevingsaanbiedingen](https://azure.microsoft.com/overview/trusted-cloud/compliance/) voor Azure Cloud.

Enkele belangrijke praktijken om een IoT-ruimte veilig te beveiligen zijn:

> [!div class="checklist"]
> * Opgeslagen, opgeslagen of permanente gegevens versleutelen.
> * Vereisen dat wachtwoorden of sleutels periodiek worden gewijzigd of vernieuwd.
> * Beperk de toegang en machtigingen zorgvuldig per rol. Lees hieronder de aanbevolen procedures voor [toegangsbeheer op basis van rollen.](#role-based-access-control-best-practices)
> * Overweeg een verdeelde netwerktopologie, zodat apparaten in elk netwerk worden geïsoleerd van de andere.
> * Gebruik krachtige encryptie. Vereisen lange wachtwoorden, gebruik veilige protocollen, en [multi-factor authenticatie](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

[Monitor](./how-to-configure-monitoring.md) IoT-resources om op te letten voor uitschieters, bedreigingen of resourceparameters die buiten het bereik van de gebruikelijke bewerking vallen. Gebruik Azure Analytics voor het bewaken van beheer.

> [!IMPORTANT]
> Lees aanbevolen procedures voor Azure [IoT-beveiliging](../iot-fundamentals/iot-security-best-practices.md) om een uitgebreide IoT-beveiligingsstrategie te starten.

> [!NOTE]
> Lees [Route-gebeurtenissen en -berichten met Azure Digital Twins](./concepts-events-routing.md)voor meer informatie over het verwerken en bewaken van gebeurtenissen.

## <a name="azure-active-directory-best-practices"></a>Aanbevolen procedures voor Azure Active Directory

Azure Digital Twins gebruikt [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) om gebruikers te verifiëren en toepassingen te beschermen. Azure Active Directory ondersteunt verificatie voor verschillende moderne architecturen. Ze zijn allemaal gebaseerd op industriestandaard protocollen zoals OAuth 2.0 of OpenID Connect. Enkele belangrijke procedures om uw IoT-ruimte voor Azure Active Directory te beveiligen, zijn:

> [!div class="checklist"]
> * Sla geheimen en sleutels van Azure Active Directory-app op een veilige locatie op, zoals [Azure Key Vault.](https://azure.microsoft.com/services/key-vault/)
> * Gebruik een certificaat dat is uitgegeven door een vertrouwde [certificaatautoriteit](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) in plaats van app-geheimen om te verifiëren.
> * Beperk oAuth 2.0-toegangsbereik voor een token.
> * Controleer of de duur van een token geldig is en of een token geldig blijft.
> * Stel de juiste tijdsduur in waarvoor tokens geldig zijn. Verlopen tokens vernieuwen.
> * Verwijder ongebruikte **omleidings-URI's** en machtigingen per [best practices voor toegangsbeheer op basis van rollen](#role-based-access-control-best-practices).

## <a name="role-based-access-control-best-practices"></a>Best practices voor toegangsbeheer op basis van rollen

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Volgende stappen

* Lees aanbevolen procedures voor [IoT-beveiliging](../iot-fundamentals/iot-security-best-practices.md)voor meer informatie over de aanbevolen procedures voor Azure IoT.For learn about Azure IoT best practices, read IoT security best practices .

* Lees [Op rollen gebaseerd](./security-role-based-access-control.md)toegangscontrole voor meer informatie over toegangsbeheer op basis van rollen .

* Lees [Authenticeren met API's](./security-authenticating-apis.md)voor meer informatie over verificatie.
