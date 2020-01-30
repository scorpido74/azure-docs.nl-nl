---
title: 'Zelf studie: de gebruikers interface aanpassen'
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van de gebruikers interface (UI) van uw toepassingen in Azure Active Directory B2C met behulp van de Azure Portal.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1b0ad7049375cb20d2daa0e6f920b680d03530a5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840125"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Zelf studie: de interface van gebruikers ervaringen aanpassen in Azure Active Directory B2C

U kunt [gebruikers stromen](user-flow-overview.md) gebruiken in Azure Active Directory B2C (Azure AD B2C) voor meer veelvoorkomende gebruikers ervaringen, zoals aanmelden, aanmelden en het bewerken van profielen. In de informatie in deze zelf studie leert u hoe u [de gebruikers interface (UI)](customize-ui-overview.md) van deze ervaringen kunt aanpassen met uw eigen HTML-en CSS-bestanden.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Aanpassings bestanden voor de gebruikers interface maken
> * De gebruikers stroom bijwerken voor het gebruik van de bestanden
> * De aangepaste gebruikers interface testen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

[Maak een gebruikers stroom](tutorial-create-user-flows.md) zodat gebruikers zich kunnen registreren en zich kunnen aanmelden bij uw toepassing.

## <a name="create-customization-files"></a>Aanpassings bestanden maken

U maakt een Azure Storage-account en een container en plaatst vervolgens de basis-HTML-en CSS-bestanden in de container.

### <a name="create-a-storage-account"></a>Maak een opslagaccount

Hoewel u uw bestanden op veel verschillende manieren kunt opslaan, slaat u deze op in [Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md).

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure-abonnement bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw abonnement bevat. Deze map wijkt af van de directory die uw Azure B2C-Tenant bevat.
3. Kies alle services in de linkerbovenhoek van het Azure Portal, zoek en selecteer **opslag accounts**.
4. Selecteer **Toevoegen**.
5. Selecteer onder **resource groep**de optie **nieuwe maken**, voer een naam in voor de nieuwe resource groep en klik vervolgens op **OK**.
6. Voer een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn in Azure, moet tussen de 3 en 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.
7. Selecteer de locatie van het opslag account of accepteer de standaard locatie.
8. Accepteer alle andere standaard waarden, selecteer **controleren + maken**en klik vervolgens op **maken**.
9. Nadat het opslag account is gemaakt, selecteert **u Ga naar resource**.

### <a name="create-a-container"></a>Een container maken

1. Selecteer op de pagina overzicht van het opslag account **blobs**.
2. Selecteer **container**, voer een naam in voor de container, kies **BLOB (anonieme lees toegang alleen voor blobs)** en klik vervolgens op **OK**.

### <a name="enable-cors"></a>CORS inschakelen

 Azure AD B2C-code in een browser gebruikt een moderne en standaard benadering voor het laden van aangepaste inhoud vanuit een URL die u opgeeft in een gebruikers stroom. Met CORS (cross-Origin Resource Sharing) kunnen beperkte resources op een webpagina worden aangevraagd vanuit andere domeinen.

1. Selecteer **CORS**in het menu.
2. Voer `https://your-tenant-name.b2clogin.com`in voor **toegestane oorsprongen**. Vervang `your-tenant-name` met de naam van uw Azure AD B2C-tenant. Bijvoorbeeld `https://fabrikam.b2clogin.com`. U moet alle kleine letters gebruiken bij het invoeren van de naam van uw Tenant.
3. Selecteer `GET`,`PUT`en `OPTIONS`voor **toegestane methoden**.
4. Voer een asterisk (*) in bij **toegestane headers**.
5. Voer een asterisk (*) in voor **weer gegeven headers**.
6. Voer 200 in als **maximum leeftijd**.

    ![CORS-configuratie pagina in Azure Blob-opslag in Azure Portal](./media/tutorial-customize-ui/enable-cors.png)

5. Klik op **Opslaan**.

### <a name="create-the-customization-files"></a>De aanpassings bestanden maken

Als u de gebruikers interface van de registratie-ervaring wilt aanpassen, kunt u beginnen met het maken van een eenvoudig HTML-en CSS-bestand. U kunt uw HTML op elke gewenste manier configureren, maar deze moet een **div** -element met de id `api`hebben. Bijvoorbeeld `<div id="api"></div>`. Azure AD B2C worden elementen in de `api` container geïnjecteerd wanneer de pagina wordt weer gegeven.

1. Maak in een lokale map het volgende bestand en zorg ervoor dat u `your-storage-account` wijzigt in de naam van het opslag account en `your-container` naar de naam van de container die u hebt gemaakt. Bijvoorbeeld `https://store1.blob.core.windows.net/b2c/style.css`.

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

    De pagina kan op elke gewenste manier worden ontworpen, maar het element **API** div is vereist voor een HTML-aanpassings bestand dat u maakt.

3. Sla het bestand op als *Custom-UI. html*.
4. Maak de volgende eenvoudige CSS waarmee alle elementen op de registratie-of aanmeldings pagina worden gecentreerd, met inbegrip van de elementen die Azure AD B2C injecties.

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

5. Sla het bestand op als *Style. CSS*.

### <a name="upload-the-customization-files"></a>De aanpassings bestanden uploaden

In deze zelf studie slaat u de bestanden op die u hebt gemaakt in het opslag account, zodat Azure AD B2C deze kunt openen.

1. Kies **alle services** in de linkerbovenhoek van het Azure Portal, zoek en selecteer **opslag accounts**.
2. Selecteer het opslag account dat u hebt gemaakt, selecteer **blobs**en selecteer vervolgens de container die u hebt gemaakt.
3. Selecteer **uploaden**, navigeer naar en selecteer het bestand *Custom-UI. html* en klik vervolgens op **uploaden**.

    ![De BLOB-pagina in de portal uploaden met de knop uploaden en gemarkeerde bestanden](./media/tutorial-customize-ui/upload-blob.png)

4. Kopieer de URL voor het bestand dat u hebt geüpload voor gebruik verderop in de zelf studie.
5. Herhaal stap 3 en 4 voor het bestand *Style. CSS* .

## <a name="update-the-user-flow"></a>De gebruikers stroom bijwerken

1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
2. Selecteer **gebruikers stromen (beleid)** en selecteer vervolgens de *B2C_1_signupsignin1* gebruikers stroom.
3. Selecteer **pagina-indelingen**en klik onder **Unified Sign-up of aanmeldings pagina**op **Ja** voor **aangepaste pagina-inhoud gebruiken**.
4. Voer in de URI van de **aangepaste pagina**de URI in voor het *Custom-UI. html-* bestand dat u eerder hebt vastgelegd.
5. Selecteer boven aan de pagina **Opslaan**.

## <a name="test-the-user-flow"></a>De gebruikers stroom testen

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen** en selecteer de *B2C_1_signupsignin1* gebruikers stroom.
2. Klik boven aan de pagina op **gebruikers stroom uitvoeren**.
3. Klik op de knop **gebruikers stroom uitvoeren** .

    ![De pagina gebruikers stroom uitvoeren voor de stroom registratie-of aanmeldings gebruikers](./media/tutorial-customize-ui/run-user-flow.png)

    Als het goed is, ziet u een pagina die lijkt op het volgende voor beeld met de elementen die zijn gecentreerd op basis van het CSS-bestand dat u hebt gemaakt:

    ![Webbrowser met de aangepaste UI-elementen van een registratie-of aanmeldings pagina](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Aanpassings bestanden voor de gebruikers interface maken
> * De gebruikers stroom bijwerken voor het gebruik van de bestanden
> * De aangepaste gebruikers interface testen

> [!div class="nextstepaction"]
> [Taal aanpassing in Azure Active Directory B2C](user-flow-language-customization.md)
