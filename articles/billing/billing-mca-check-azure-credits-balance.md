---
title: Azure-tegoed voor een Microsoft-klantovereenkomst bijhouden
description: Lees hoe u het saldo van het Azure-tegoed voor een Microsoft-klantovereenkomst kunt bijhouden.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: b8faf3801a2217e6ef7040a7c2b67c7e6fad8e60
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709608"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Saldo van Azure-tegoed voor Microsoft-klantovereenkomst bijhouden

U kunt het saldo van het Azure-tegoed voor een Microsoft-klantovereenkomst bijhouden in de Azure-portal. U kunt het tegoed gebruiken om te betalen voor kosten die niet onder dat tegoed vallen.

Er worden gebruikskosten in rekening gebracht wanneer u producten gebruikt die niet onder het tegoed vallen of als u het saldo van uw tegoed overschrijdt. Zie [Producten die niet onder Azure-tegoed vallen (#producten-die-niet-onder-Azure-tegoed-vallen) voor meer informatie.

Dit artikel is van toepassing op een factureringsrekening voor een Microsoft-klantovereenkomst. [Controleer of u toegang hebt tot een Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Het saldo van uw tegoed controleren

1. Meld u aan bij [Azure Portal]( https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

    ![Schermopname van de zoekopdracht naar kostenbeheer en facturering in de Azure-portal](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Selecteer **Azure-tegoed** aan de linkerkant. Afhankelijk van uw toegang moet u mogelijk eerst een factureringsrekening of factureringsprofiel selecteren en vervolgens **Azure-tegoed** selecteren.

4. Op de pagina Azure-tegoed wordt de volgende informatie weergegeven:

   ![Schermopname van het tegoedsaldo en transacties voor een factureringsprofiel](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termijn               | Definitie                           |
   |--------------------|--------------------------------------------------------|
   | Geschat saldo  | Het geschatte resterende tegoed nadat alle gefactureerde en in behandeling zijnde transacties zijn voltooid |
   | Huidig saldo    | Het tegoed sinds uw laatste factuur. Hierbij is nog geen rekening gehouden met transacties die in behandeling zijn |
   | Transacties       | Alle factureringstransacties die van invloed waren op het saldo van uw Azure-tegoed |

   Wanneer uw geschatte saldo tot 0 daalt, worden er kosten in rekening gebracht voor al uw gebruik, met inbegrip van producten die onder tegoed vallen.

6. Selecteer **Lijst met tegoeden** om een lijst met tegoeden voor het factureringsprofiel weer te geven. De lijst met tegoeden bevat de volgende informatie:

   ![Schermopname van lijst met tegoeden voor een factureringsprofiel](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termijn | Definitie |
   |---|---|
   | Geschat saldo | Het Azure-tegoed dat u nog over hebt nadat niet-gefactureerde, voor tegoed in aanmerking komende kosten van uw huidige saldo zijn afgetrokken|
   | Huidig saldo | Het Azure-tegoed dat u nog hebt voordat niet-gefactureerde, voor tegoed in aanmerking komende kosten in rekening worden gebracht. Dit wordt berekend door uw nieuwe ontvangen Azure-tegoeden op te tellen bij het saldo dat u had na uw laatste factuur|
   | Bron | De aanschafbron van het tegoed |
   | Begindatum | De datum waarop u het tegoed hebt gekocht |
   | Vervaldatum | De datum waarop het tegoed vervalt |
   | Saldo | Het saldo na uw laatste factuur |
   | Oorspronkelijk bedrag | Het oorspronkelijke bedrag van het tegoed |
   | Status | De huidige status van het tegoed. De status kan actief, gebruikt, vervallen of bijna vervallen zijn |

## <a name="how-credits-are-used"></a>Hoe tegoeden worden gebruikt

In een factureringsrekening voor een Microsoft-klantovereenkomst gebruikt u factureringsprofielen om uw facturen en betalingswijzen te beheren. Voor elk factureringsprofiel wordt maandelijks een factuur gegenereerd. U gebruikt de betalingswijzen om de factuur te betalen.

Azure-tegoed is een van die betalingswijzen. U ontvangt een tegoed van Microsoft, bijvoorbeeld vanwege een speciale aanbieding of een serviceniveau. Deze tegoeden worden toegewezen aan een factureringsprofiel. Als er een factuur wordt gegenereerd voor het factureringsprofiel, worden de tegoeden automatisch toegepast op het totale gefactureerde bedrag om te berekenen welk bedrag u moet betalen. U betaalt het resterende bedrag met een andere betalingswijze zoals een cheque of een directe overboeking.

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
- Azure-ondersteuningsplannen

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.

Als u hulp nodig hebt, neemt u [contact op met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over factureringsrekening voor Microsoft-klantovereenkomst](billing-mca-overview.md)
- [Meer informatie over de begrippen in de factuur voor uw Microsoft-klantovereenkomst](billing-mca-understand-your-invoice.md)
