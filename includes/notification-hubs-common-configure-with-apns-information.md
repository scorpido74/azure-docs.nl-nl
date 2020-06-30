---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 537c86512db4706077d0089a4f71ee945623c26b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095304"
---
### <a name="configure-your-notification-hub-with-apns-information"></a>Uw Notification Hub configureren met APNS-gegevens

Selecteer **Apple** onder **Notification Services** en voer vervolgens de juiste stappen uit op basis van de aanpak die u eerder hebt gekozen in de sectie [Een certificaat maken voor Notification Hubs](#creating-a-certificate-for-notification-hubs).  

> [!NOTE]
> Gebruik **Productie** voor **Toepassingsmodus** alleen als u pushmeldingen wilt verzenden naar gebruikers die uw app in de Store hebben gekocht.

### <a name="option-1-using-a-p12-push-certificate"></a>OPTIE 1: Een .p12-pushcertificaat gebruiken

1. Selecteer **Certificaat**.

1. Selecteer het bestandspictogram.

1. Selecteer het .p12-bestand dat u eerder hebt geëxporteerd en selecteer vervolgens **Openen**.

1. Geef indien nodig het juiste wachtwoord op.

1. Selecteer de modus **Sandbox**.

1. Selecteer **Opslaan**.

### <a name="option-2-using-token-based-authentication"></a>OPTIE 2: Op tokens gebaseerde verificatie gebruiken

1. Selecteer **Token**.
1. Voer de volgende waarden in die u eerder hebt verkregen:

    - **Sleutel-id**
    - **Bundel-id**
    - **Team-id**
    - **Token**

1. Kies **Sandbox**.
1. Selecteer **Opslaan**.
