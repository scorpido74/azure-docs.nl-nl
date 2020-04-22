---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 31a6d116ae1afce9afdd3786747490980963d823
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678047"
---
## <a name="register-an-azure-ad-app"></a>Een Azure AD-app registreren

Als u aanmelding wilt inschakelen voor gebruikers van een specifieke Azure AD-organisatie, moet u een toepassing registreren binnen de azure AD-tenant van de organisatie Azure.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD-tenant voor de organisatie bevat (bijvoorbeeld contoso.com). Selecteer het **filter Directory + abonnement** in het bovenste menu en kies vervolgens de map met uw Azure AD-tenant.
1. Kies **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer **app-registraties**.
1. Selecteer **Nieuwe registratie**.
1. Voer een **naam** in voor uw toepassing. Bijvoorbeeld `Azure AD B2C App`.
1. Accepteer de standaardselectie van **accounts in deze organisatiemap alleen** voor deze toepassing.
1. Accepteer voor de **URI-omleiding**de waarde van **web**en voer de `your-B2C-tenant-name` volgende URL in alle kleine letters in, waarbij de naam van uw Azure AD B2C-tenant wordt vervangen.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Bijvoorbeeld `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Selecteer **Registreren**. Neem de **toepassings-id (client) op** voor gebruik in een latere stap.
1. Selecteer **Certificaten & geheimen**en selecteer Vervolgens Nieuw **clientgeheim**.
1. Voer een **beschrijving** voor het geheim in, selecteer een vervaldatum en selecteer **Toevoegen**. Noteer de **waarde** van het geheim voor gebruik in een latere stap.

### <a name="configuring-optional-claims"></a>Optionele claims configureren

Als u de `family_name` claims `given_name` en claims van Azure AD wilt ophalen, u optionele claims voor uw toepassing configureren in de gebruikersinterface of toepassingslijst van Azure portal. Zie [Optionele claims voor uw Azure AD-app voor](/active-directory/develop/active-directory-optional-claims.md)meer informatie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Zoek naar **Azure Active Directory** en selecteer deze optie.
1. Selecteer **app-registraties**in de sectie **Beheren** .
1. Selecteer de toepassing waarvoor u optionele claims wilt configureren in de lijst.
1. Selecteer **Tokenconfiguratie**in de sectie **Beheren** .
1. Selecteer **Optionele claim toevoegen**.
1. Selecteer **ID**voor het **type Token**.
1. Selecteer de optionele claims `family_name` `given_name`die u wilt toevoegen en .
1. Klik op **Add**.