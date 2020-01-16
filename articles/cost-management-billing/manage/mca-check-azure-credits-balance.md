---
title: Azure-tegoed voor een Microsoft-klantovereenkomst bijhouden
description: Lees hoe u het saldo van het Azure-tegoed voor een Microsoft-klantovereenkomst kunt bijhouden.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c8ea083e216331904c9d5741b97f69b7f5a8249a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991332"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Saldo van Azure-tegoed voor Microsoft-klantovereenkomst bijhouden

U kunt het credit saldo van Azure voor uw facturerings account controleren voor een micro soft-klant overeenkomst in de Azure Portal of via REST Api's.

In de factureringsrekening voor een Microsoft-klantovereenkomst wordt een tegoed toegewezen aan een factureringsprofiel. Elk facturerings profiel heeft zijn eigen tegoed dat automatisch wordt toegepast op de kosten voor de factuur. U moet beschikken over de rol van eigenaar van het factureringsprofiel, inzender, lezer of factuurbeheerder voor het factureringsprofiel of de rol van eigenaar, inzender of lezer voor de factureringsrekening om het Azure-tegoed voor een factureringsprofiel te kunnen bekijken. Zie [Informatie over beheerdersrollen voor Microsoft-klantovereenkomsten in Azure](understand-mca-roles.md) voor meer informatie over de rollen.

Dit artikel is van toepassing op een factureringsaccount voor een Microsoft-klantovereenkomst. [Controleer of u toegang hebt tot een Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Het saldo van uw tegoed controleren

### <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

    ![Schermopname van de zoekopdracht naar kostenbeheer en facturering in de Azure-portal](./media/mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Selecteer op de pagina betaal bereik het facturerings account waarvoor u het tegoed wilt bijhouden. Het facturerings account moet van het type **micro soft-klant overeenkomst**zijn.

    ![Schermopname van de zoekopdracht naar kostenbeheer en facturering in de Azure-portal](./media/mca-check-azure-credits-balance/list-of-scopes.png)

    > [!NOTE]
    >
    > Azure Portal het laatste factuur bereik te onthouden dat u hebt geopend en geeft het bereik weer wanneer u de volgende keer aan de pagina van Cost Management en facturering bent. U ziet de pagina betaal bereik niet als u eerder Cost Management en facturering hebt bezocht. Als dit het geval is, controleert u of u zich in het [juiste bereik](#check-access-to-a-microsoft-customer-agreement)bevindt. Als dat niet het geval is, [schakelt u het bereik](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) in om het facturerings account voor een micro soft-klant overeenkomst te selecteren.

3. Selecteer **betalings methoden** aan de linkerkant en selecteer vervolgens **Azure-tegoed**.

   ![Scherm opname van credit saldo voor een facturerings profiel](./media/mca-check-azure-credits-balance/mca-payment-methods.png)

4. De pagina Azure-tegoeden bevat de volgende secties:

   #### <a name="balance"></a>Saldo

   In het gedeelte saldo wordt de samen vatting van het tegoed van Azure weer gegeven.

   ![Scherm opname van credit saldo voor een facturerings profiel](./media/mca-check-azure-credits-balance/mca-credit-balance.png)

   | Termijn               | Definitie                           |
   |--------------------|--------------------------------------------------------|
   | Geschat saldo  | Het geschatte resterende tegoed nadat alle gefactureerde en in behandeling zijnde transacties zijn voltooid |
   | Huidig saldo    | Het tegoed sinds uw laatste factuur. Hierbij is nog geen rekening gehouden met transacties die in behandeling zijn |

   Wanneer uw geschatte saldo tot 0 daalt, worden er kosten in rekening gebracht voor al uw gebruik, met inbegrip van producten die in aanmerking komen voor betaling met een tegoed.

   #### <a name="credits-list"></a>Lijst met tegoeden

   In het gedeelte lijst met tegoeden wordt de lijst met Azure-tegoed weer gegeven.

   ![Schermopname van lijst met tegoeden voor een factureringsprofiel](./media/mca-check-azure-credits-balance/mca-credits-list.png)

   | Termijn | Definitie |
   |---|---|
   | Bron | De aanschafbron van het tegoed |
   | Begindatum | De datum waarop u het tegoed hebt gekocht |
   | Vervaldatum | De datum waarop het tegoed vervalt |
   | Huidig saldo | Het saldo na uw laatste factuur |
   | Oorspronkelijk bedrag | Het oorspronkelijke bedrag van het tegoed |
   | Status | De huidige status van het tegoed. De status kan actief, gebruikt, vervallen of bijna vervallen zijn |

   #### <a name="transactions"></a>Transacties

   In het gedeelte trans acties worden alle trans acties weer gegeven die van invloed zijn op uw tegoed.

   ![Scherm opname van krediet transacties voor een facturerings profiel](./media/mca-check-azure-credits-balance/mca-credits-transactions.png)

   | Termijn | Definitie |
   |---|---|
   | Transactie datum | De datum waarop de trans actie plaatsvond |
   | Beschrijving | Een beschrijving van de trans actie |
   | Bedrag| Het aantal transacties |
   | Saldo | Het saldo na de trans actie |

    > [!NOTE]
    >
    > Als u Azure-tegoed niet ziet op de pagina betalings wijzen, hebt u geen tegoed of hebt u het juiste bereik niet geselecteerd. Selecteer het facturerings account met tegoed of een van de facturerings profielen. Zie voor meer informatie over het wijzigen van scopes [de optie facturerings bereik in de Azure Portal](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Als u Azure-tegoeden in het bereik van de facturerings account bekijkt en het facturerings account meerdere facturerings profielen heeft, wordt op de pagina Azure-tegoed een tabel weer gegeven met een overzicht van Azure-tegoed voor elk facturerings profiel. Selecteer een facturerings profiel in de lijst, selecteer betalings wijzen en vervolgens Azure-tegoed om de details van een facturerings profiel weer te geven.

    ![Scherm opname van een krediet lijst voor een facturerings account](./media/mca-check-azure-credits-balance/mca-account-credit-list.png)

### <a name="rest-apitabrest"></a>[REST API](#tab/rest)

U kunt de [Azure Billing](https://docs.microsoft.com/rest/api/billing/)- en [Consumption](https://docs.microsoft.com/rest/api/consumption/)-API's gebruiken om het tegoedsaldo voor uw factureringsrekening programmatisch op te halen.

In de onderstaande voorbeelden worden REST API's gebruikt. PowerShell en Azure CLI worden momenteel niet ondersteund.

### <a name="find-billing-profiles-you-have-access-to"></a>Factureringsprofielen zoeken waartoe u toegang hebt

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
Via de API wordt een lijst met factureringsrekeningen en de bijbehorende factureringsprofielen geretourneerd.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Gebruik de eigenschap `displayName` van het facturerings profiel om het facturerings profiel te identificeren waarvoor u het tegoed saldo wilt controleren. Kopieer de `id` van het factureringsprofiel. Als u bijvoorbeeld het tegoedsaldo van het factureringsprofiel **Development** (Ontwikkeling) wilt controleren, kopieert u ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Plak deze waarde ergens zodat u deze kunt gebruiken tijdens de volgende stap.

### <a name="get-azure-credit-balance"></a>Saldo van Azure-tegoed ophalen

Voer de volgende aanvraag uit en vervang `<billingProfileId>` door de `id` die u in de eerste stap hebt gekopieerd (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

Via de API worden het geschatte saldo en het huidige saldo voor het factureringsprofiel geretourneerd.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Elementnaam  | Beschrijving                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | Het geschatte resterende tegoed nadat alle gefactureerde en in behandeling zijnde transacties zijn voltooid. |
| `currentBalance`   | Het tegoed sinds uw laatste factuur. Hierbij is nog geen rekening gehouden met transacties die in behandeling zijn.    |
| `pendingCreditAdjustments`      | De correcties die nog niet zijn gefactureerd, zoals terugbetalingen.  |
| `expiredCredit`      |  Het tegoed dat sinds de laatste factuur is verlopen.  |
| `pendingEligibleCharges`  | De nog niet gefactureerde kosten die in aanmerking voor betaling met het tegoed.   |

### <a name="get-list-of-credits"></a>Lijst met tegoeden ophalen

Voer de volgende aanvraag uit en vervang `<billingProfileId>` door de `id` die u in de eerste stap hebt gekopieerd (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
Via de API worden lijsten met Azure-tegoeden voor een factureringsprofiel geretourneerd.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Elementnaam  | Beschrijving                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | Het oorspronkelijke bedrag van het tegoed. |
| `closedBalance`   | Het saldo na uw laatste factuur.    |
| `source`      | De bron die aangeeft door wie en hoe het tegoed is aangeschaft. |
| `startDate`      |  De datum waarop het tegoed is geactiveerd.  |
| `expirationDate`  | De datum waarop het tegoed vervalt.   |
| `poNumber`  | Het inkoopordernummer van de factuur waarop het tegoed is gefactureerd.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Transacties die van invloed waren op het saldo van het tegoed

Voer de volgende aanvraag uit en vervang `<billingProfileId>` door de `id` die u in de eerste stap hebt gekopieerd (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). U moet een **startDate** (begindatum) en een **endDate** (einddatum) opgeven om transacties voor het gewenste tijdsbestek op te halen.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
Via de API worden alle transacties geretourneerd die van invloed waren op het tegoedsaldo voor uw factureringsprofiel.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Elementnaam  | Beschrijving                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | De datum waarop de transactie plaatsvond. |
| `description` | De beschrijving van de transactie. |
| `adjustments`   | De tegoedcorrecties voor de transactie.    |
| `creditExpired`      | De hoeveelheid tegoed die is verlopen. |
| `charges`      |  De kosten voor de transactie.  |
| `closedBalance`  | Het saldo na de transactie.   |
| `eventType`  | Het transactietype.   |
| `invoiceNumber`  | Het factuurnummer van de factuur waarop de transactie wordt gefactureerd. Dit is leeg voor een transactie in behandeling.   |

---

## <a name="how-credits-are-used"></a>Hoe tegoeden worden gebruikt

In een factureringsrekening voor een Microsoft-klantovereenkomst gebruikt u factureringsprofielen om uw facturen en betalingswijzen te beheren. Voor elk factureringsprofiel wordt maandelijks een factuur gegenereerd. U gebruikt de betalingswijzen om de factuur te betalen.

U wijst tegoed toe dat u aanschaft voor een factureringsprofiel. Als er een factuur wordt gegenereerd voor het factureringsprofiel, worden de tegoeden automatisch toegepast op de totale kosten om te berekenen welk bedrag u moet betalen. U betaalt het resterende bedrag met uw betalingswijzen, zoals een cheque, directe overboeking of creditcard.

## <a name="products-that-arent-covered-by-azure-credits"></a>Producten die niet onder Azure-tegoed vallen

 De volgende producten vallen niet onder uw Azure-tegoed. Voor het gebruik van deze producten worden altijd kosten in rekening gebracht, ongeacht het saldo van uw tegoed:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Geregistreerde gebruiker
- OpenLogic
- Geregistreerde gebruiker van Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (maandelijks)
- Visual Studio Enterprise (jaarlijks)
- Visual Studio Professional (maandelijks)
- Visual Studio Professional (jaarlijks)
- Azure Marketplace-producten
- Ondersteuningsabonnementen voor Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.

Als u hulp nodig hebt, kunt u [contact opnemen met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over factureringsrekening voor Microsoft-klantovereenkomst](../understand/mca-overview.md)
- [Toelichting van de begrippen in de factuur voor uw Microsoft-klantovereenkomst](../understand/mca-understand-your-invoice.md)
