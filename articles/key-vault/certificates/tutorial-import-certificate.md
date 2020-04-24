---
title: 'Zelf studie: een certificaat in Key Vault importeren met behulp van Azure Portal | Microsoft Docs'
description: Zelf studie waarin wordt getoond hoe u een certificaat importeert in Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 9496173ee006c6ca3cab557f4e63ec21647ad0fd
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82105570"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Zelf studie: een certificaat in Azure Key Vault importeren

Azure Key Vault is een cloudservice die werkt als een beveiligd archief voor geheimen. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze zelf studie maakt u een sleutel kluis en gebruikt u deze om een certificaat te importeren. Raadpleeg het [Overzicht](../general/overview.md) voor meer informatie over Key Vault.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een sleutelkluis maken.
> * Importeer een certificaat in de sleutel kluis met behulp van de portal.
> * Importeer een certificaat in de sleutel kluis met behulp van CLI.


Lees [Key Vault basis concepten](../general/basic-concepts.md)voordat u begint. 

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

![Uitvoer nadat het maken van de sleutelkluis is voltooid](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Een certificaat importeren in Key Vault

Als u een certificaat naar de kluis wilt importeren, moet u een PEM-of PFX-certificaat bestand op schijf hebben. In dit geval importeren we een certificaat met een bestands naam met de naam **ExampleCertificate**.

> [!IMPORTANT]
> In Azure Key Vault zijn de ondersteunde indelingen voor certificaten PFX en PEM. 
> - . pem-bestands indeling bevat een of meer x509-certificaat bestanden.
> - pfx-bestands indeling is een archief bestands indeling voor het opslaan van verschillende cryptografische objecten in één bestand, d.w.z. server certificaat (uitgegeven voor uw domein), een overeenkomende persoonlijke sleutel en kan eventueel een tussenliggende CA bevatten.  

1. Selecteer op de pagina eigenschappen van Key Vault de optie **certificaten**.
2. Klik op **Genereren/importeren**.
3. Kies in het scherm **een certificaat maken** de volgende waarden:
    - **Methode voor het maken van certificaten**: importeren.
    - **Certificaat naam**: ExampleCertificate.
    - **Certificaat bestand uploaden**: het certificaat bestand van de schijf selecteren
    - **Wacht woord** : als u een certificaat bestand met wachtwoord beveiliging uploadt, geeft u dit wacht woord hier op. Als dat niet het geval is, laat u dit veld leeg. Zodra het certificaat bestand is geïmporteerd, wordt dat wacht woord door sleutel kluis verwijderd.
4. Klik op **maken**.

![Certificaat eigenschappen](../media/certificates/tutorial-import-cert/cert-import.png)

Door een certificaat toe te voegen met behulp van de methode **import** , vult Azure Key kluis automatisch certificaat parameters in (dat wil zeggen de geldigheids periode, de naam van de verlener, de activerings datum, enzovoort).

Zodra u het bericht ontvangt dat het certificaat is geïmporteerd, kunt u erop klikken in de lijst om de eigenschappen ervan weer te geven. 

![Certificaat eigenschappen](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Een certificaat importeren met behulp van Azure CLI

Importeer een certificaat in een opgegeven sleutel kluis. Als u een bestaand geldig certificaat met een persoonlijke sleutel wilt importeren in Azure Key Vault, kan het bestand dat moet worden geïmporteerd, de indeling PFX of PEM hebben. Als het certificaat zich in de PEM-indeling bevindt, moet het PEM-bestand de sleutel en de x509-certificaten bevatten. Voor deze bewerking is de machtiging Certificaten/importeren vereist.

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
Meer informatie over [de para](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) meters

Nadat u het certificaat hebt geïmporteerd, kunt u het certificaat weer geven met behulp van [certificaat weer geven](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show)


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```



Nu hebt u een sleutel kluis gemaakt, een certificaat geïmporteerd en de eigenschappen van het certificaat weer gegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ in het vak **TYP DE NAAM VAN DE RESOURCEGROEP** de naam van de resourcegroep en selecteer **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Key Vault gemaakt en een certificaat in het bestand geïmporteerd. Ga verder met de volgende artikelen voor meer informatie over Key Vault en hoe u deze integreert met uw toepassingen.

- Meer informatie over het [maken van certificaten in azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- Voor beelden bekijken van het [importeren van certificaten met behulp van rest-api's](/rest/api/keyvault/importcertificate/importcertificate)
- [Azure Key Vault aanbevolen procedures](../general/best-practices.md) controleren