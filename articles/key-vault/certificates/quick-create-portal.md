---
title: 'Azure Quick Start: een certificaat instellen en ophalen van Key Vault met behulp van Azure Portal | Microsoft Docs'
description: Quick Start laat zien hoe u een certificaat kunt instellen en ophalen van Azure Key Vault met behulp van de Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: b0d6221aaafe3ade70bc23ce4196a7b53c9474c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424718"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-the-azure-portal"></a>Snelstartgids: een certificaat instellen en ophalen van Azure Key Vault met behulp van de Azure Portal

Azure Key Vault is een cloudservice die werkt als een beveiligd archief voor geheimen. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze Quick Start maakt u een sleutel kluis en gebruikt u deze om een certificaat op te slaan. Raadpleeg het [Overzicht](../general/overview.md) voor meer informatie over Key Vault.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vault"></a>Een kluis maken

1. Selecteer in het menu Azure Portal of op de **Start** pagina de optie **een resource maken**.
2. Typ **Sleutelkluis** in het zoekvak.
3. Kies **Sleutelkluis** in de lijst met resultaten.
4. Kies **Maken** in de sectie Sleutelkluis.
5. Geef in de sectie **Sleutelkluis maken** de volgende gegevens op:
    - **Naam**: geef een unieke naam op. Voor deze Quick Start gebruiken we voor **beeld-kluis**. 
    - **Abonnement**: kies een abonnement.
    - Kies onder **resource groep**de optie **nieuwe maken** en voer een naam voor de resource groep in.
    - Kies een locatie in de vervolgkeuzelijst **Locatie**.
    - Houd voor de overige opties de standaardwaarden aan.
6. Selecteer na het opgeven van de bovenstaande gegevens **Maken**.

Let op de onderstaande twee eigenschappen:

* **Kluis naam**: in het voor beeld is dit **voor beeld-kluis**. U gebruikt deze naam voor andere stappen.
* **Vault URI**: In het voorbeeld is dit https://example-vault.vault.azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

![Uitvoer nadat het maken van de sleutelkluis is voltooid](../media/certificates/quick-create-portal/vault-properties.png)

## <a name="add-a-certificate-to-key-vault"></a>Een certificaat toevoegen aan Key Vault

Als u een certificaat wilt toevoegen aan de kluis, hoeft u alleen maar een paar extra stappen uit te voeren. In dit geval voegen we een zelfondertekend certificaat toe dat kan worden gebruikt door een toepassing. Het certificaat heet **ExampleCertificate**.

1. Selecteer op de pagina eigenschappen van Key Vault de optie **certificaten**.
2. Klik op **Genereren/importeren**.
3. Kies in het scherm **een certificaat maken** de volgende waarden:
    - **Methode voor het maken van certificaten**: genereren.
    - **Certificaat naam**: ExampleCertificate.
    - **Onderwerp**: CN = ExampleDomain
    - Houd voor de overige waarden de standaardwaarden aan. Klik op **maken**.

Zodra u het bericht ontvangt dat het certificaat is gemaakt, kunt u erop klikken in de lijst. Vervolgens ziet u enkele van de eigenschappen. Als u op de huidige versie klikt, ziet u de waarde die u hebt opgegeven in de vorige stap.

![Certificaat eigenschappen](../media/certificates/quick-create-portal/current-version-hidden.png)

Als u op de knop ' downloaden in CER-indeling ' of ' downloaden in PFX/PEM-indeling ' klikt, kunt u het certificaat down load. 

![Certificaat downloaden](../media/certificates/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ in het vak **TYP DE NAAM VAN DE RESOURCEGROEP** de naam van de resourcegroep en selecteer **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een Key Vault gemaakt en een certificaat opgeslagen. Ga verder met de volgende artikelen voor meer informatie over Key Vault en hoe u deze integreert met uw toepassingen.

- Een [overzicht van Azure Key Vault](../general/overview.md) lezen
- Raadpleeg de [Azure Key Vault hand leiding voor ontwikkel aars](../general/developers-guide.md)
- [Azure Key Vault aanbevolen procedures](../general/best-practices.md) controleren