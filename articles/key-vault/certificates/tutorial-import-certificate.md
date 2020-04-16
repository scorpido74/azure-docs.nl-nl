---
title: Zelfstudie - Een certificaat importeren in Key Vault met Azure-portal | Microsoft Documenten
description: Zelfstudie met informatie over het importeren van een certificaat in Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/03/2020
ms.author: sebansal
ms.openlocfilehash: 754f30f7931f9fad6a95328cbf8ab34f70cb75a0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423108"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Zelfstudie: Een certificaat importeren in Azure Key Vault

Azure Key Vault is een cloudservice die werkt als een beveiligd archief voor geheimen. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze zelfstudie maakt u een sleutelkluis en gebruikt u deze om een certificaat te importeren. Raadpleeg het [Overzicht](../general/overview.md) voor meer informatie over Key Vault.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een sleutelkluis maken.
> * Een certificaat importeren in de kluis Sleutel via portal.
> * Een certificaat importeren in de kluis Sleutel met CLI.


Lees voordat u begint [de basisconcepten van Key Vault.](../general/basic-concepts.md) 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vault"></a>Een kluis maken

1. Selecteer in het menu azure portal of op **de** startpagina de optie Een **bron maken**.
2. Typ **Sleutelkluis** in het zoekvak.
3. Kies **Sleutelkluis** in de lijst met resultaten.
4. Kies **Maken** in de sectie Sleutelkluis.
5. Geef in de sectie **Sleutelkluis maken** de volgende gegevens op:
    - **Naam**: geef een unieke naam op. Voor deze quickstart gebruiken we **Example-Vault**. 
    - **Abonnement**: kies een abonnement.
    - Kies **onder Resourcegroep**de optie **Nieuw maken** en voer een naam van de resourcegroep in.
    - Kies een locatie in de vervolgkeuzelijst **Locatie**.
    - Houd voor de overige opties de standaardwaarden aan.
6. Selecteer na het opgeven van de bovenstaande gegevens **Maken**.

Let op de onderstaande twee eigenschappen:

* **Vault Name:** In het voorbeeld is dit **Voorbeeld-Vault**. U gebruikt deze naam voor andere stappen.
* **Vault URI**: In het voorbeeld is dit https://example-vault.vault.azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

![Uitvoer nadat het maken van de sleutelkluis is voltooid](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Een certificaat importeren in Key Vault

Als u een certificaat wilt importeren in de kluis, moet u een PEM- of PFX-certificaatbestand hebben dat op schijf staat. In dit geval importeren we een certificaat met bestandsnaam **genaamd ExampleCertificate**.

> [!IMPORTANT]
> In Azure Key Vault zijn ondersteunde certificaatindelingen PFX en PEM. 
> - .pem bestandsindeling bevat een of meer X509-certificaatbestanden.
> - .pfx-bestandsindeling is een archiefbestandsindeling voor het opslaan van meerdere cryptografische objecten in één bestand, d.w.z. servercertificaat (uitgegeven voor uw domein), een overeenkomende privésleutel en kan optioneel een tussenliggende CA bevatten.  

1. Selecteer Certificaten op de **eigenschappenpagina's**Key Vault .
2. Klik op **Genereren/importeren**.
3. Kies in het scherm **Een certificaat maken** de volgende waarden:
    - **Methode voor het maken van certificaten**: Importeren.
    - **Certificaatnaam**: Voorbeeldcertificaat.
    - **Certificaatbestand uploaden:** selecteer het certificaatbestand van de schijf
    - Houd voor de overige waarden de standaardwaarden aan. Klik **op Maken**.

![Certificaateigenschappen](../media/certificates/tutorial-import-cert/cert-import.png)

Zodra u het bericht ontvangt dat het certificaat is geïmporteerd, u erop klikken in de lijst. U dan een aantal van de eigenschappen zien. 

![Certificaateigenschappen](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Een certificaat importeren met Azure CLI

Een certificaat importeren in een opgegeven sleutelkluis. Als u een bestaand geldig certificaat met een privésleutel wilt importeren in Azure Key Vault, kan het te importeren bestand in PFX- of PEM-indeling zijn. Als het certificaat in PEM-formaat is, moet het PEM-bestand de sleutel en x509-certificaten bevatten. Voor deze bewerking zijn de certificaten/importtoestemming vereist.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```
Meer informatie over de parameters [vindt u hier](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import)

## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ in het vak **TYP DE NAAM VAN DE RESOURCEGROEP** de naam van de resourcegroep en selecteer **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Key Vault gemaakt en er een certificaat in geïmporteerd. Ga verder naar de onderstaande artikelen voor meer informatie over Key Vault en hoe u deze integreren met uw toepassingen.

- Lees meer over [Certificaten beheren in Azure Key Vault](/archive/blogs/kv/manage-certificates-via-azure-key-vault)
- Voorbeelden van [importcertificaten bekijken met REST-API's](/rest/api/keyvault/importcertificate/importcertificate)
- Aanbevolen [procedures voor Azure Key Vault bekijken](../general/best-practices.md)