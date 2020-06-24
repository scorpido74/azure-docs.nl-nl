---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 818f33abfdb0655d96c0a8873a43903ee972b3bf
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82925451"
---
## <a name="register-an-azure-ad-app"></a>Een Azure AD-app registreren

Als u het aanmelden voor gebruikers van een specifieke Azure AD-organisatie wilt inschakelen, moet u een toepassing registreren in de Azure AD-Tenant van de organisatie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die de Azure AD-Tenant van uw organisatie bevat (bijvoorbeeld contoso.com). Selecteer het **filter Directory + abonnement** in het bovenste menu en kies vervolgens de map die uw Azure AD-Tenant bevat.
1. Kies **alle services** in de linkerbovenhoek van de Azure Portal en zoek en selecteer **app-registraties**.
1. Selecteer **Nieuwe registratie**.
1. Voer een **naam** in voor uw toepassing. Bijvoorbeeld `Azure AD B2C App`.
1. Accepteer de standaard selectie van **accounts in deze organisatie Directory alleen** voor deze toepassing.
1. Voor de **omleidings-URI**accepteert u de waarde van **Web**en voert u de volgende URL in in kleine letters, waar `your-B2C-tenant-name` wordt vervangen door de naam van uw Azure AD B2C-Tenant.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Bijvoorbeeld `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Selecteer **Registreren**. Noteer de **Toepassings-id (client)** voor gebruik in een latere stap.
1. Selecteer **certificaten & geheimen**en selecteer vervolgens **Nieuw client geheim**.
1. Voer een **Beschrijving** in voor het geheim, selecteer een verloop en selecteer vervolgens **toevoegen**. Noteer de **waarde** van het geheim voor gebruik in een latere stap.

### <a name="configuring-optional-claims"></a>Optionele claims configureren

Als u de `family_name` en `given_name` claims van Azure ad wilt ophalen, kunt u optionele claims voor uw toepassing configureren in de Azure Portal gebruikers interface of het toepassings manifest. Zie [optionele claims voor uw Azure AD-app bieden](/azure/active-directory/develop/active-directory-optional-claims)voor meer informatie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Zoek naar **Azure Active Directory** en selecteer deze optie.
1. Selecteer in de sectie **beheren** de optie **app-registraties**.
1. Selecteer in de lijst de toepassing waarvoor u de optionele claims wilt configureren.
1. Selecteer in de sectie **beheren** de optie **token configuratie**.
1. Selecteer **optionele claim toevoegen**.
1. Selecteer **id**voor het **token type**.
1. Selecteer de optionele claims om toe te voegen `family_name` en `given_name` .
1. Klik op **Add**.