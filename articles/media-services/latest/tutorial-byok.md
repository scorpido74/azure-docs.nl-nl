---
title: Door de klant beheerde sleutels of BYOK (Bring Your Own Key) met Media Services
description: Dit is een zelfstudie voor het gebruik van door de klant beheerde sleutels met een Media Services-opslagaccount
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c26da9d888bbcdf72c052fa4bd7fcdf443a2d8f7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325844"
---
# <a name="tutorial-use-customer-managed-keys-or-bring-your-own-key-byok-with-media-services"></a>Zelfstudie: Door de klant beheerde sleutels of BYOK (Bring Your Own Key) gebruiken met Media Services

Met de 2020-05-01 API kunt u een door de klant beheerde RSA-sleutel gebruiken met een Media Services-account dat een in het systeem beheerde identiteit bevat.  Deze zelfstudie omvat een Postman-verzameling en -omgeving om REST-aanvragen te verzenden naar Azure-services.  Gebruikte services:

- Azure Active Directory-toepassingsregistratie voor Postman
- Microsoft Graph API
- Azure Storage
- Azure Key Vault
- Media Services

In deze zelfstudie leert u Postman te gebruiken voor het volgende:

> [!div class="checklist"]
> * Tokens ophalen voor gebruik met Azure-services.
> * Een resourcegroep en een opslagaccount maken.
> * Een Media Services-account maken met een in het systeem beheerde identiteit
> * Een sleutelkluis maken voor een RSA-sleutel (door de klant beheerd) voor gebruik met een opslagaccount.
> * Het Media Services-account bijwerken om de RSA-sleutel te gebruiken met het opslagaccount.
> * Variabelen gebruiken in Postman.

Als u nog geen Azure-abonnement hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- Registreer een service-principal met de juiste machtigingen.
- Installeer [Postman](https://www.postman.com).
- Download de Postman-verzameling voor deze zelfstudie op [Azure-voorbeelden: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registreer een service-principal met de benodigde machtigingen

[Een service-principal maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).  Zie [Optie twee](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) voor het ophalen van het geheim van de service-principal.  Noteer het geheim ergens. Zodra u de pagina met het geheim in de portal sluit, is deze namelijk niet meer toegankelijk.

De service-principal heeft de volgende machtigingen nodig om de taken in deze zelfstudie uit te voeren.

![de machtigingen die nodig zijn voor de service-principal](./media/tutorial-byok/service-principal-permissions-1.png)

### <a name="install-postman"></a>Postman installeren

Als u Postman nog niet hebt geïnstalleerd voor gebruik met Azure, kunt u het downloaden op [postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>De verzameling downloaden en importeren

Download de Postman-verzameling voor deze zelfstudie op [Azure-voorbeelden: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

## <a name="installation-of-collection-and-environment"></a>Installatie van verzameling en omgeving

1. Voer Postman uit.
1. Selecteer **Importeren** .
1. Selecteer **Bestanden uploaden** .
1. Ga naar de locatie waar u de bestanden met verzamelingen en omgevingen hebt opgeslagen.
1. Selecteer het bestand met de verzameling en omgeving.
1. Selecteer **Openen** .  (U ziet een waarschuwing dat de bestanden niet worden geïmporteerd als een API, maar als verzamelingen.  Dit is juist.  Dit is wat u wilt.)
1. Deze verzameling wordt nu in uw Verzamelingen weergegeven als BYOK.
1. De omgevingsvariabelen worden nu weergegeven in uw Omgevingen.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>De REST API-aanvragen in de verzameling begrijpen

De verzameling biedt de volgende REST API-aanvragen. De aanvragen moeten worden verzonden in de geboden volgorde, omdat de meeste hiervan testscripts bevatten die dynamisch globale variabelen maken voor de volgende aanvraag in de reeks. U hoeft de globale variabelen niet handmatig te maken.

In Postman worden deze variabelen weergegeven tussen `{{ }}` haken.  Bijvoorbeeld `{{bearerToken}}`.

1. AAD-token ophalen: de globale variabele *bearerToken* wordt ingesteld bij de test
2. Graph-token ophalen: de globale variabele *graphToken* wordt ingesteld bij de test
3. Details van service-principal ophalen: de globale variabele *servicePrincipalObjectId* wordt ingesteld bij de test
4. Een opslagaccount maken: de globale variabele *storageAccountId* wordt ingesteld bij de test
5. Een Media Services-account met een in het systeem beheerde identiteit maken: de globale variabele *principalId* wordt ingesteld bij de test
6. Een sleutelkluis maken, waarbij toegang wordt verleend aan de service-principal: de globale variabele *keyVaultId* wordt ingesteld bij de test
7. Een Key Vault-token ophalen: de globale variabele *keyVaultToken* wordt ingesteld bij de test
8. Een RSA-sleutel maken in de sleutelkluis: de globale variabele *keyId* wordt ingesteld bij de test
9. Het Media Services-account bijwerken om de sleutel te gebruiken met het opslagaccount: er is geen testscript voor deze aanvraag.

## <a name="define-environment-variables"></a>Omgevingsvariabelen definiëren

1. Schakel over naar de omgeving die u hebt gedownload, door de vervolgkeuzelijst met omgevingen te selecteren.
1. Stel de omgevingsvariabelen in Postman in. Ze worden ook gebruikt als variabelen tussen `{{ }}` haken.  Bijvoorbeeld `{{tenantId}}`.

    * *tenantId* = uw tenant-id
    * *servicePrincipalId* = de id van de service-principal die u hebt ingesteld met uw favoriete methode: portal, CLI, enzovoort.
    * *servicePrincipalSecret* = het geheim dat is gemaakt voor de service-principal
    * *subscription* = uw abonnements-id
    * *storageName* = de naam die u uw opslag wilt geven
    * *accountName* = de Media Service-accountnaam die u wilt gebruiken
    * *keyVaultName* = de sleutelkluisnaam die u wilt gebruiken
    * *resourceLocation* = centralus (of waar u uw resources wilt opslaan.  Deze verzameling is alleen getest met centralus.)
    * *resourceGroup* = de resourcegroepnaam

    De volgende variabelen zijn standaard voor het werken met Azure-resources. U hoeft ze dus niet te wijzigen.

    * *armResource* = `https://management.core.windows.net`
    * *graphResource* = `https://graph.windows.net/`
    * *keyVaultResource* = `https://vault.azure.net`
    * *armEndpoint* = `management.azure.com`
    * *graphEndpoint* = `graph.windows.net`
    * *aadEndpoint* = `login.microsoftonline.com`
    * *keyVaultDomainSuffix* = `vault.azure.net`

## <a name="send-the-requests"></a>De aanvragen verzenden

Zodra de omgevingsvariabelen zijn gedefinieerd, kunt u de aanvragen één voor één uitvoeren in de bovenstaande reeks, of de uitvoering van Postman gebruiken om de verzameling uit te voeren.

## <a name="change-the-key"></a>De sleutel wijzigen

In Media Services wordt automatisch gedetecteerd wanneer de sleutel is gewijzigd.  Maak nog een sleutelversie voor dezelfde sleutel om dit te testen. Deze sleutel moet binnen 15 minuten worden gedetecteerd in Media Services.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt, verder niet meer gaat gebruiken, en **u niet meer gefactureerd wilt worden voor deze resources** , verwijdert u ze.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om te lezen hoe u dit doet...
> [!div class="nextstepaction"]
> [Extern bestand coderen op basis van URL, en de video streamen met REST](stream-files-tutorial-with-rest.md)
