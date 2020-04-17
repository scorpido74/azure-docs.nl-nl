---
title: B2B-berichten beveiligen met certificaten
description: Certificaten toevoegen om B2B-berichten in Azure Logic Apps te beveiligen met het Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 19a1883685193e80da5f1365ec2a30db0b8754f6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450137"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>Beveiliging voor B2B-berichten verbeteren met behulp van certificaten

Wanneer u B2B-communicatie vertrouwelijk moet houden, u de beveiliging voor B2B-communicatie in uw bedrijfsintegratie-apps, met name logische apps, verhogen door certificaten toe te voegen aan uw integratieaccount. Certificaten zijn digitale documenten die de identiteitvan de deelnemers aan elektronische communicatie controleren en u helpen de communicatie op deze manieren te beveiligen:

* Berichtinhoud versleutelen.
* Berichten digitaal ondertekenen.

U deze certificaten gebruiken in uw bedrijfsintegratie-apps:

* [Openbare certificaten](https://en.wikipedia.org/wiki/Public_key_certificate), die u moet kopen bij een openbare [internetcertificaatautoriteit (CA),](https://en.wikipedia.org/wiki/Certificate_authority) maar geen sleutels nodig hebt. 

* Privécertificaten of [*zelfondertekende certificaten*](https://en.wikipedia.org/wiki/Self-signed_certificate), die u zelf maakt en uitgeeft, maar ook privésleutels nodig hebt. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Een openbaar certificaat uploaden

Als u een *openbaar certificaat* wilt gebruiken in logische apps met B2B-mogelijkheden, moet u het certificaat eerst uploaden naar uw integratieaccount. Nadat u de eigenschappen hebt gedefinieerd in de [overeenkomsten](logic-apps-enterprise-integration-agreements.md) die u maakt, is het certificaat beschikbaar om u te helpen uw B2B-berichten te beveiligen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **alle bronnen**in het hoofdmenu van Azure. Voer in het zoekvak de naam van uw integratieaccount in en selecteer vervolgens het gewenste integratieaccount.

   ![Uw integratieaccount zoeken en selecteren](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Kies **onder Componenten**de tegel **Certificaten.**

   ![Kies 'Certificaten'](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. Kies Onder **Certificaten**de optie **Toevoegen**. Geef onder **Certificaat toevoegen**deze gegevens op voor uw certificaat. Als u klaar bent, kiest u **Done**.

   | Eigenschap | Waarde | Beschrijving | 
   |----------|-------|-------------|
   | **Naam** | <*certificaatnaam*> | De naam van uw certificaat, dat in dit voorbeeld "publicCert" is | 
   | **Certificaattype** | Public | Het type van uw certificaat |
   | **Certificaat** | <*certificaat-bestandsnaam*> | Als u het certificaatbestand wilt zoeken en selecteren dat u wilt uploaden, kiest u het mappictogram naast het vak **Certificaat.** |
   ||||

   ![Kies 'Toevoegen', certificaatgegevens opgeven](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Nadat Azure uw selectie heeft gevalideerd, uploadt Azure uw certificaat.

   ![Azure toont nieuw certificaat](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Een privécertificaat uploaden

Als u een *privécertificaat* wilt gebruiken in logische apps met B2B-mogelijkheden, moet u het certificaat eerst uploaden naar uw integratieaccount. U moet ook een privésleutel hebben die u eerst toevoegt aan [Azure Key Vault.](../key-vault/key-vault-get-started.md) 

Nadat u de eigenschappen hebt gedefinieerd in de [overeenkomsten](logic-apps-enterprise-integration-agreements.md) die u maakt, is het certificaat beschikbaar om u te helpen uw B2B-berichten te beveiligen.

> [!NOTE]
> Voor privécertificaten moet u een bijbehorend openbaar certificaat toevoegen dat wordt weergegeven in de **instellingen voor verzenden en ontvangen van** de [AS2-overeenkomst](logic-apps-enterprise-integration-as2.md) voor het ondertekenen en versleutelen van berichten.

1. [Voeg uw privésleutel toe aan Azure Key Vault](../key-vault/certificates/certificate-scenarios.md#import-a-certificate) en geef een **sleutelnaam op.**
   
2. Azure Logic Apps autoriseren om bewerkingen uit te voeren op Azure Key Vault. Als u toegang wilt verlenen tot de principal van de Logic Apps-service, gebruikt u de opdracht PowerShell, [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), bijvoorbeeld:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **alle bronnen**in het hoofdmenu van Azure. Voer in het zoekvak de naam van uw integratieaccount in en selecteer vervolgens het gewenste integratieaccount.

   ![Uw integratieaccount zoeken](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Kies **onder Componenten**de tegel **Certificaten.**  

   ![De tegel Certificaten kiezen](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. Kies Onder **Certificaten**de optie **Toevoegen**. Geef onder **Certificaat toevoegen**deze gegevens op voor uw certificaat. Als u klaar bent, kiest u **Done**.

   | Eigenschap | Waarde | Beschrijving | 
   |----------|-------|-------------|
   | **Naam** | <*certificaatnaam*> | De naam van uw certificaat, dat in dit voorbeeld "privateCert" is | 
   | **Certificaattype** | Privé | Het type van uw certificaat |
   | **Certificaat** | <*certificaat-bestandsnaam*> | Als u het certificaatbestand wilt zoeken en selecteren dat u wilt uploaden, kiest u het mappictogram naast het vak **Certificaat.** Wanneer u een sleutelkluis voor de privésleutel gebruikt, is het geüploade bestand het openbare certificaat. | 
   | **Resourcegroep** | <*integratie-account-resource-groep*> | De resourcegroep van uw integratieaccount, die in dit voorbeeld 'MyResourceGroup' is, | 
   | **Key Vault** | <*sleutelkluisnaam*> | De naam van uw Azure-sleutelkluis |
   | **Sleutelnaam** | <*sleutelnaam*> | De naam van uw sleutel |
   ||||

   ![Kies 'Toevoegen', certificaatgegevens opgeven](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Nadat Azure uw selectie heeft gevalideerd, uploadt Azure uw certificaat.

   ![Azure toont nieuw certificaat](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Volgende stappen

* [Een B2B-overeenkomst maken](logic-apps-enterprise-integration-agreements.md)
