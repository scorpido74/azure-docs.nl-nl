---
title: 'Azure-snelstart: Een geheim uit Key Vault instellen en ophalen met Azure Portal | Microsoft Docs'
description: Snelstart waarin wordt getoond hoe u een geheim uit Azure Key Vault instelt en ophaalt met behulp van de Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d659ca519e5bd7f26e4cca17707e0db397aae98
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424816"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Snelstart: Een geheim uit Azure Key Vault instellen en ophalen met behulp van de Azure Portal

Azure Key Vault is een cloudservice die werkt als een beveiligd archief voor geheimen. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze snelstart kunt u een sleutelkluis maken en daarin een geheim opslaan. Raadpleeg het [Overzicht](../general/overview.md) voor meer informatie over Key Vault.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vault"></a>Een kluis maken

1. Selecteer in het menu azure portal of op **de** startpagina de optie Een **bron maken**.
2. Typ **Sleutelkluis** in het zoekvak.
3. Kies **Sleutelkluis** in de lijst met resultaten.
4. Kies **Maken** in de sectie Sleutelkluis.
5. Geef in de sectie **Sleutelkluis maken** de volgende gegevens op:
    - **Naam**: geef een unieke naam op. Voor deze quickstart gebruiken we **Contoso-vault2.** 
    - **Abonnement**: kies een abonnement.
    - Kies **onder Resourcegroep**de optie **Nieuw maken** en voer een naam van de resourcegroep in.
    - Kies een locatie in de vervolgkeuzelijst **Locatie**.
    - Houd voor de overige opties de standaardwaarden aan.
6. Selecteer na het opgeven van de bovenstaande gegevens **Maken**.

Let op de onderstaande twee eigenschappen:

* **Kluisnaam**: in het voorbeeld is dat **Contoso-Vault2**. U gebruikt deze naam voor andere stappen.
* **Vault URI**: In het voorbeeld is dit https://contoso-vault2.vault.azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

![Uitvoer nadat het maken van de sleutelkluis is voltooid](../media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Als u een geheim wilt toevoegen aan de kluis, hoeft u maar een paar extra stappen uit te voeren. In dit geval voegen we een wachtwoord toe dat door een toepassing kan worden gebruikt. Het wachtwoord heeft de naam **ExamplePassword** en slaat daarin de waarde **hVFkk965BuUv** op.

1. Selecteer Geheimen op de **eigenschappenpagina's**Key Vault .
2. Klik op **Genereren/importeren**.
3. Kies in het scherm **Een geheim maken** de volgende waarden:
    - **Uploadopties**: Handmatig.
    - **Naam**: ExamplePassword.
    - **Waarde**: hVFkk965BuUv
    - Houd voor de overige waarden de standaardwaarden aan. Klik **op Maken**.

Zodra u het bericht ontvangt dat het geheim met succes is gemaakt, kunt u erop klikken in de lijst. Vervolgens ziet u enkele van de eigenschappen. Als u op de huidige versie klikt, ziet u de waarde die u hebt opgegeven in de vorige stap.

![Geheimeigenschappen](../media/quick-create-portal/current-version-hidden.png)

Door in het rechterdeelvenster op de knop Geheime waarde weergeven te klikken, u de verborgen waarde zien. 

![Geheime waarde verschenen](../media/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ in het vak **TYP DE NAAM VAN DE RESOURCEGROEP** de naam van de resourcegroep en selecteer **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je een Key Vault gemaakt en er een geheim in opgeslagen. Ga verder naar de onderstaande artikelen voor meer informatie over Key Vault en hoe u deze integreren met uw toepassingen.

- Lees een [overzicht van Azure Key Vault](../general/overview.md)
- Bekijk de handleiding van azure [Key Vault-ontwikkelaars](../general/developers-guide.md)
- Aanbevolen [procedures voor Azure Key Vault bekijken](../general/best-practices.md)