---
title: Uitleg over aanbevolen beveiligings procedures-Azure Digital Apparaatdubbels | Microsoft Docs
description: Meer informatie over best practices voor beveiliging voor Azure Digital Apparaatdubbels en de Internet of Things.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122556"
---
# <a name="azure-digital-twins-security-best-practices"></a>Best practices voor Azure Digital Apparaatdubbels-beveiliging

Met Azure Digital Apparaatdubbels Security kunt u nauw keurige toegang tot specifieke bronnen en acties in uw IoT-grafiek. Dit doet u door het beheer [van op rollen gebaseerd toegangs beheer op basis van](./security-role-based-access-control.md)functies en machtigingen.

Azure Digital Apparaatdubbels maakt ook gebruik van andere beveiligings functies die aanwezig zijn in azure IoT, met inbegrip van Azure Active Directory (Azure AD). Om die reden is het configureren en beveiligen van toepassingen die zijn gebouwd op Azure Digital Apparaatdubbels het gebruik van veel van dezelfde [Azure IOT-beveiligings procedures](../iot-fundamentals/iot-security-best-practices.md) die momenteel worden aanbevolen.

In dit artikel vindt u een overzicht van de aanbevolen procedures om te volgen.

> [!IMPORTANT]
> Bekijk aanvullende beveiligings bronnen om te zorgen voor een maximale beveiliging voor uw IoT-ruimte. Zorg ervoor dat u leveranciers van uw apparaten opneemt.

> [!TIP]
> Gebruik [Azure Security Center voor IOT](https://docs.microsoft.com/azure/asc-for-iot/) om IOT-beveiligings dreigingen en beveiligings problemen te detecteren.

## <a name="iot-security-best-practices"></a>Best practices voor IoT-beveiliging

Enkele belang rijke procedures voor het veilig beveiligen van uw IoT-apparaten zijn:

> [!div class="checklist"]
> * Beveilig elk apparaat dat is verbonden met uw IoT-ruimte op een andere manier voor onrecht matige wijzigingen.
> * Beperk de rol van elk apparaat, elke sensor en persoon binnen uw IoT-ruimte. Als er is geknoeid, wordt het effect geminimaliseerd.
> * Overweeg het mogelijke gebruik van IP-adres filtering en poort beperking voor apparaten.
> * Beperk I/O en bandbreedte van apparaten om de prestaties te verbeteren. De mate van beperking kan de beveiliging verbeteren door denial-of-service-aanvallen te voor komen.
> * Zorg ervoor dat de firmware, het besturings systeem en de software van het apparaat up-to-date blijven.
> * Regel matig de best practices voor apparaten, software, netwerken en gateway beveiliging controleren en controleren wanneer ze blijven verbeteren en ontwikkelen.
> * Gebruik vertrouwde, gecertificeerde en compatibele beveiligings systemen, software en apparaten. Bekijk bijvoorbeeld [de nalevings aanbiedingen](https://azure.microsoft.com/overview/trusted-cloud/compliance/) voor Azure Cloud.

Enkele belang rijke procedures voor het veilig beveiligen van een IoT-ruimte zijn onder andere:

> [!div class="checklist"]
> * Opgeslagen, opgeslagen of permanente gegevens versleutelen.
> * Vereisen dat wacht woorden of sleutels regel matig worden gewijzigd of vernieuwd.
> * Beperk de toegang en machtigingen op rol nauw keurig. Lees de sectie [Aanbevolen procedures voor toegangs beheer op basis van rollen](#role-based-access-control-best-practices) hieronder.
> * Overweeg een gesplitste netwerk topologie zodat apparaten op elk netwerk van elkaar zijn geïsoleerd.
> * Gebruik krachtige versleuteling. Lange wacht woorden vereisen, beveiligde protocollen gebruiken en [multi-factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

[Controleren](./how-to-configure-monitoring.md) IoT-bronnen om te kijken naar uitbijters, bedreigingen of resource parameters die buiten het bereik van de gebruikelijke bewerking vallen. Gebruik Azure Analytics voor bewakings beheer.

> [!IMPORTANT]
> Lees de [Aanbevolen procedures voor Azure IOT Security](../iot-fundamentals/iot-security-best-practices.md) om een uitgebreide IOT-beveiligings strategie te starten.

> [!NOTE]
> Lees [route gebeurtenissen en berichten met Azure Digital apparaatdubbels](./concepts-events-routing.md)voor meer informatie over het verwerken en bewaken van gebeurtenissen.

## <a name="azure-active-directory-best-practices"></a>Aanbevolen procedures Azure Active Directory

Azure Digital Apparaatdubbels maakt gebruik van [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) om gebruikers te verifiëren en toepassingen te beveiligen. Azure Active Directory ondersteunt verificatie voor verschillende moderne architecturen. Ze zijn allemaal gebaseerd op gestandaardiseerde protocollen, zoals OAuth 2,0 of OpenID Connect Connect. Enkele belang rijke procedures voor het beveiligen van uw IoT-ruimte voor Azure Active Directory zijn:

> [!div class="checklist"]
> * Bewaar Azure Active Directory app-geheimen en sleutels op een veilige locatie, zoals [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Gebruik een certificaat dat is uitgegeven door een vertrouwde [certificerings instantie](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) in plaats van app-geheimen om te verifiëren.
> * Beperk OAuth 2,0-toegangs bereik voor een token.
> * Controleer de tijds duur dat een token geldig is en of een token geldig blijft.
> * Stel de juiste tijds duur in waarvoor tokens geldig zijn. Vernieuw verlopen tokens.
> * Verwijder ongebruikte **omleidings-uri's** en machtigingen per [op rollen gebaseerd toegangs beheer aanbevolen procedures](#role-based-access-control-best-practices).

## <a name="role-based-access-control-best-practices"></a>Aanbevolen procedures voor toegangs beheer op basis van rollen

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Volgende stappen

* Lees voor meer informatie over best practices voor Azure IoT de [Best practices voor IOT-beveiliging](../iot-fundamentals/iot-security-best-practices.md).

* Lees op [rollen gebaseerd toegangs beheer](./security-role-based-access-control.md)voor meer informatie over op rollen gebaseerd toegangs beheer.

* Lees [verificatie met api's](./security-authenticating-apis.md)voor meer informatie over verificatie.
