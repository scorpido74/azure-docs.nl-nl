---
title: 'Zelfstudie: de gebruikersinterface aanpassen'
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van de gebruikersinterface (UI) van uw toepassingen in Azure Active Directory B2C met behulp van de Azure-portal.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b2b2bc8dd4e60348553228b8b418df252a8c426a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186247"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Zelfstudie: de interface van gebruikerservaringen aanpassen in Azure Active Directory B2C

Voor meer algemene gebruikerservaringen, zoals aanmelding, aanmelding en profielbewerking, u [gebruikersstromen](user-flow-overview.md) gebruiken in Azure Active Directory B2C (Azure AD B2C). De informatie in deze zelfstudie helpt u om te leren hoe [u de gebruikersinterface (UI)](customize-ui-overview.md) van deze ervaringen aanpassen met behulp van uw eigen HTML- en CSS-bestanden.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * UI-aanpassingsbestanden maken
> * De gebruikersstroom bijwerken om de bestanden te gebruiken
> * De aangepaste gebruikersinterface testen

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

[Maak een gebruikersstroom](tutorial-create-user-flows.md) zodat gebruikers zich kunnen aanmelden en zich kunnen aanmelden bij uw toepassing.

## <a name="create-customization-files"></a>Aanpassingsbestanden maken

U maakt een Azure-opslagaccount en -container en plaatst vervolgens basis-HTML- en CSS-bestanden in de container.

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Hoewel u uw bestanden op vele manieren opslaan, slaat u ze voor deze zelfstudie op in [Azure Blob-opslag.](../storage/blobs/storage-blobs-introduction.md)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure-abonnement bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw abonnement. Deze map is anders dan de map die uw Azure B2C-tenant bevat.
3. Kies Alle services in de linkerbovenhoek van de Azure-portal, zoek naar opslagaccounts en selecteer **deze.**
4. Selecteer **Toevoegen**.
5. Selecteer **onder Resourcegroep** **Nieuw maken,** voer een naam in voor de nieuwe resourcegroep en klik op **OK**.
6. Voer een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn in Azure, moet tussen de 3 en 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.
7. Selecteer de locatie van het opslagaccount of accepteer de standaardlocatie.
8. Accepteer alle andere standaardwaarden, selecteer **Controleren + maken**en klik op **Maken**.
9. Nadat het opslagaccount is gemaakt, selecteert u **Ga naar resource**.

### <a name="create-a-container"></a>Een container maken

1. Selecteer Blobs op de **overzichtspagina**van het opslagaccount .
2. Selecteer **Container,** voer een naam voor de container in, kies **Blob (alleen anonieme leestoegang voor blobs)** en klik op **OK**.

### <a name="enable-cors"></a>CORS inschakelen

 Azure AD B2C-code in een browser maakt gebruik van een moderne en standaardbenadering om aangepaste inhoud te laden vanaf een URL die u in een gebruikersstroom opgeeft. Met cross-origin resource sharing (CORS) kunnen beperkte resources op een webpagina worden aangevraagd bij andere domeinen.

1. Selecteer **CORS**in het menu.
2. Voor **toegestane oorsprong,** `https://your-tenant-name.b2clogin.com`voer . Vervang `your-tenant-name` de naam van uw Azure AD B2C-tenant. Bijvoorbeeld `https://fabrikam.b2clogin.com`. U moet alle kleine letters gebruiken bij het invoeren van uw tenantnaam.
3. Selecteer , `OPTIONS`en . **Allowed Methods** `GET``PUT`
4. Voer **voor Toegestane kopteksten**een sterretje in (*).
5. Voer **voor Exposed Headers**een sterretje (*) in.
6. Voor **Max leeftijd,** voer 200.

    ![CORS-configuratiepagina in Azure Blob-opslag in Azure-portal](./media/tutorial-customize-ui/enable-cors.png)

5. Klik op **Opslaan**.

### <a name="create-the-customization-files"></a>De aanpassingsbestanden maken

Als u de gebruikersinterface van de aanmeldingservaring wilt aanpassen, maakt u eerst een eenvoudig HTML- en CSS-bestand. U uw HTML configureren zoals u dat wilt, maar `api`het moet een **div-element** hebben met een id van. Bijvoorbeeld `<div id="api"></div>`. Azure AD B2C injecteert `api` elementen in de container wanneer de pagina wordt weergegeven.

1. Maak in een lokale map het volgende bestand `your-storage-account` en zorg ervoor dat `your-container` u de naam van het opslagaccount wijzigt en de naam van de container die u hebt gemaakt. Bijvoorbeeld `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    De pagina kan worden ontworpen op elke manier die u wilt, maar het **api** div-element is vereist voor elk HTML-aanpassingsbestand dat u maakt.

3. Sla het bestand op als *custom-ui.html*.
4. Maak de volgende eenvoudige CSS die alle elementen op de aanmeldings- of aanmeldingspagina centreert, inclusief de elementen die Azure AD B2C injecteert.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Sla het bestand op als *style.css*.

### <a name="upload-the-customization-files"></a>De aanpassingsbestanden uploaden

In deze zelfstudie slaat u de bestanden op die u in het opslagaccount hebt gemaakt, zodat Azure AD B2C er toegang toe heeft.

1. Kies **Alle services** in de linkerbovenhoek van de Azure-portal, zoek naar opslagaccounts en selecteer **deze.**
2. Selecteer het opslagaccount dat u hebt gemaakt, selecteer **Blobs**en selecteer vervolgens de container die u hebt gemaakt.
3. Selecteer **Uploaden,** navigeer naar en selecteer het bestand *custom-ui.html* en klik op **Uploaden**.

    ![Blob-pagina uploaden in portal met knop Uploaden en gemarkeerde bestanden](./media/tutorial-customize-ui/upload-blob.png)

4. Kopieer de URL voor het bestand dat u hebt geüpload om later in de zelfstudie te gebruiken.
5. Herhaal stap 3 en 4 voor het *bestand style.css.*

## <a name="update-the-user-flow"></a>De gebruikersstroom bijwerken

1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
2. Selecteer **Gebruikersstromen (beleidsregels)** en selecteer vervolgens de *B2C_1_signupsignin1* gebruikersstroom.
3. Selecteer **Pagina-indelingen**en klik onder **Aanmeldings- of aanmeldingspagina Unified**op **Aangepaste** **pagina-inhoud gebruiken**.
4. Voer in **Aangepaste pagina URI**de URI in voor het bestand *custom-ui.html* dat u eerder hebt opgenomen.
5. Selecteer Boven aan de pagina **Opslaan**.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer in uw Azure AD B2C-tenant **Gebruikersstromen** en selecteer de *B2C_1_signupsignin1* gebruikersstroom.
2. Klik boven aan de pagina op **Gebruikersstroom uitvoeren**.
3. Klik op de knop **Gebruikersstroom uitvoeren.**

    ![Gebruikersstroompagina uitvoeren voor de gebruikersstroom of aanmeldingsstroom](./media/tutorial-customize-ui/run-user-flow.png)

    U ziet een pagina die vergelijkbaar is met het volgende voorbeeld met de elementen die zijn gecentreerd op basis van het CSS-bestand dat u hebt gemaakt:

    ![Webbrowser met aanmeldings- of aanmeldingspagina met aangepaste gebruikersinterface-elementen](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * UI-aanpassingsbestanden maken
> * De gebruikersstroom bijwerken om de bestanden te gebruiken
> * De aangepaste gebruikersinterface testen

> [!div class="nextstepaction"]
> [Taalaanpassing in Azure Active Directory B2C](user-flow-language-customization.md)
