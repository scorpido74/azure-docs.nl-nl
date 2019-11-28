---
title: Eigendom van facturering van Azure-abonnementen krijgen
description: Meer informatie over het aanvragen van eigendom van facturering van Azure-abonnementen van andere gebruikers.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 2ac2d552aed5b11eaf5a8e4708b2cabc33ba1bb3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223812"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Eigendom van facturering van Azure-abonnementen van andere accounts krijgen

Mogelijk wilt u het eigendom van Azure-abonnementen overnemen als de bestaande factureringseigenaar uw organisatie verlaat of als u via uw factureringsaccount wilt betalen voor de abonnementen. Als u het eigendom overneemt, worden factureringsverantwoordelijkheden van abonnementen naar uw account overgedragen.

Dit artikel is van toepassing op een factureringsaccount voor een Microsoft-klantovereenkomst. [Controleer of u toegang hebt tot een Microsoft-klantovereenkomst](#check-for-access).

Als u het eigendom van facturering wilt aanvragen, moet u **eigenaar van de factuursectie** zijn of **inzender van de factuursectie**. Zie [Rollen en taken van factuursectie](billing-understand-mca-roles.md#invoice-section-roles-and-tasks) voor meer informatie.

## <a name="request-billing-ownership"></a>Eigendom van facturering aanvragen

1. Meld u aan bij [Azure Portal](https://portal.azure.com) als eigenaar of inzender van een factuursectie voor een factureringsaccount voor Microsoft-klantovereenkomst.

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van de zoekopdracht in Azure Portal naar kostenbeheer en facturering](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Selecteer **Factuursecties** aan de linkerkant. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount of factureringsprofiel selecteren. Selecteer **Factuursecties** in het factureringsaccount of -profiel.
   
   ![Schermopname van de selectie van factuursecties](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Selecteer een factuursectie in de lijst. Zodra u het eigendom van de abonnementen overneemt, worden deze in rekening gebracht bij deze factuursectie.

5. Selecteer **Overdrachtsaanvragen** aan de linkerkant en selecteer vervolgens **Toevoegen**.
 
   ![Schermopname van de selectie van overdrachtsaanvragen](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Voer het e-mailadres in van de gebruiker van wie u het eigendom van facturering wilt aanvragen. De gebruiker moet een accountbeheerder zijn op een Microsoft Online Serviceprogramma-factureringsaccount of een accounteigenaar op een Enterprise Agreement. Zie [Uw factureringsaccounts weergeven in Azure Portal](billing-view-all-accounts.md) voor meer informatie. Selecteer **Overdrachtsaanvraag verzenden**.

   ![Schermopname van de verzending van een overdrachtsaanvraag](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. De gebruiker ontvangt een e-mail met instructies voor het beoordelen van uw overdrachtsaanvraag.

   ![Schermopname van de e-mail over de beoordeling van een overdrachtsaanvraag](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. De gebruiker kan de overdrachtsaanvraag goedkeuren door de koppeling in de e-mail te selecteren en de instructies te volgen.

    ![Schermopname van de e-mail over de beoordeling van een overdrachtsaanvraag](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>De status van de overdrachtsaanvraag controleren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van de zoekopdracht in Azure Portal naar kostenbeheer en facturering](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Selecteer **Factuursecties** aan de linkerkant. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount of factureringsprofiel selecteren. Selecteer **Factuursecties** in het factureringsaccount of -profiel.
   
   ![Schermopname van de selectie van factuursecties](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Selecteer de factuursectie in de lijst waarvoor u de overdrachtsaanvraag hebt verzonden.

5. Selecteer **Overdrachtsaanvragen** aan de linkerkant. Op de pagina Overdrachtsaanvragen wordt de volgende informatie weergegeven:

    ![Schermopname van de lijst met overdrachtsaanvragen](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Kolom|Definitie|
   |---------|---------|
   |Aanvraagdatum|De datum waarop de overdrachtsaanvraag is verzonden|
   |Ontvanger|Het e-mailadres van de gebruiker aan wie u de aanvraag voor het overdragen van het eigendom van facturering hebt verzonden|
   |Vervaldatum|De datum waarop de aanvraag verloopt|
   |Status|De status van de overdrachtsaanvraag|

    De overdrachtsaanvraag kan een van de volgende statussen hebben:

   |Status|Definitie|
   |---------|---------|
   |Wordt uitgevoerd|De gebruiker heeft de overdrachtsaanvraag niet geaccepteerd|
   |Wordt verwerkt|De gebruiker heeft de overdrachtsaanvraag geaccepteerd. Facturering voor abonnementen die de gebruiker heeft geselecteerd, wordt overgebracht naar uw factuursectie|
   |Voltooid| De facturering voor abonnementen die de gebruiker heeft geselecteerd, wordt overgebracht naar uw factuursectie|
   |Voltooid met fouten|De aanvraag is voltooid, maar de facturering voor sommige abonnementen die de gebruiker heeft geselecteerd, kan niet worden overgedragen|
   |Verlopen|De gebruiker heeft de aanvraag niet op tijd geaccepteerd en de aanvraag is verlopen|
   |Geannuleerd|Iemand met toegang tot de overdrachtsaanvraag heeft de aanvraag geannuleerd|
   |Geweigerd|De gebruiker heeft de overdrachtsaanvraag geweigerd|

7. Selecteer een overdrachtsaanvraag om details weer te geven. Op de pagina met overdrachtsdetails wordt de volgende informatie weergegeven:
   
   ![Schermopname van de lijst van overgedragen abonnementen](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Kolom  |Definitie|
   |---------|---------|
   |Id van overdrachtsaanvraag|De unieke id voor uw overdrachtsaanvraag. Als u een ondersteuningsaanvraag indient, deelt u de id met de ondersteuning van Azure om uw ondersteuningsaanvraag te versnellen|
   |Overdracht aangevraagd op|De datum waarop de overdrachtsaanvraag is verzonden|
   |Overdracht aangevraagd door|Het e-mailadres van de gebruiker die de overdrachtsaanvraag heeft verzonden|
   |Overdrachtsaanvraag verloopt op| De datum waarop de overdrachtsaanvraag verloopt|
   |E-mailadres van ontvanger|Het e-mailadres van de gebruiker aan wie u de aanvraag voor het overdragen van het eigendom van facturering hebt verzonden|
   |Overdrachtskoppeling verzonden naar ontvanger|De url die naar de gebruiker is verzonden om de overdrachtsaanvraag te controleren|

## <a name="supported-subscription-types"></a>Ondersteunde abonnementstypen

U kunt het eigendom van facturering aanvragen van de hieronder vermelde typen abonnementen.

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure in Open Licensing](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass Sponsorship](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Gratis proefversie](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Aanbieding gesponsord met Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise (BizSpark)-abonnees](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise (MPN)-abonnees](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Abonnees van Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional-abonnees](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Alle tegoeden die beschikbaar zijn voor het abonnement, zijn na de overdracht niet meer beschikbaar in het nieuwe account.

\*\* Wordt alleen ondersteund voor abonnementen in accounts die worden gemaakt tijdens het aanmelden op de Azure-website.


## <a name="additional-information"></a>Aanvullende informatie

Het volgende gedeelte bevat aanvullende informatie over het overdragen van abonnementen.

### <a name="no-service-downtime"></a>Geen servicedowntime

Azure-services in het abonnement worden zonder onderbreking uitgevoerd. We maken de overgang alleen voor de factureringsrelatie voor de Azure-abonnementen die de gebruiker selecteert om over te dragen.

### <a name="disabled-subscriptions"></a>Uitgeschakelde abonnementen

Uitgeschakelde abonnementen kunnen niet worden overgedragen. Abonnementen moeten de status actief hebben om hun eigendom van facturering te kunnen overdragen.

### <a name="azure-resources-transfer"></a>Overdracht van Azure-bronnen

Alle resources van de abonnementen, zoals VM's, schijven en websites, worden overgebracht.

### <a name="azure-marketplace-products-transfer"></a>Overdracht van Azure Marketplace-producten

Azure Marketplace-producten worden overgebracht samen met hun respectieve abonnementen.

### <a name="azure-reservations-transfer"></a>Overdracht van Azure-reserveringen

Azure-reserveringen worden niet automatisch verplaatst met abonnementen. [Neem contact op met ondersteuning voor Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om reserveringen te verplaatsen.

### <a name="access-to-azure-services"></a>Toegang tot Azure-services

Toegang voor bestaande gebruikers, groepen of service-principals die is toegewezen met (Azure RBAC (op rollen gebaseerd toegangsbeheer))[../role-based-access-control/overview.md] wordt niet beïnvloed tijdens de overgang.

### <a name="azure-support-plan"></a>Azure-ondersteuningsplan

Ondersteuning voor Azure wordt niet overgedragen met de abonnementen. Als de gebruiker alle Azure-abonnementen overdraagt, vraagt u deze gebruiker om het ondersteuningsplan te annuleren.

### <a name="charges-for-transferred-subscription"></a>Kosten voor overgedragen abonnement

De oorspronkelijke factureringseigenaar van de abonnementen is verantwoordelijk voor alle kosten die zijn gerapporteerd tot het moment waarop de overdracht is voltooid. Uw factuursectie is verantwoordelijk voor de kosten die worden gerapporteerd vanaf het tijdstip van de overdracht. Er kunnen kosten zijn die plaatsvonden vóór de overdracht, maar die daarna werden gerapporteerd. Deze kosten worden weergegeven in uw factuursectie.

### <a name="cancel-a-transfer-request"></a>Een overdrachtsaanvraag annuleren

U kunt de overdrachtsaanvraag annuleren totdat de aanvraag is goedgekeurd of geweigerd. Als u de overdrachtsaanvraag wilt annuleren, gaat u naar de [pagina met overdrachtsgegevens](#check-the-transfer-request-status) en selecteert u Annuleren onderaan de pagina.

### <a name="software-as-a-service-saas-transfer"></a>Overdracht van Software als een dienst (SaaS)

SaaS-producten worden niet overgedragen met de abonnementen. Vraag de gebruiker om [Contact op te nemen met de ondersteuning voor Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om eigendom van SaaS-producten over te dragen. Samen met het eigendom van facturering kan de gebruiker ook het eigendom van resources overdragen. Met eigendom van resources kunt u beheerbewerkingen uitvoeren, zoals het verwijderen en weergeven van de details van het product. De gebruiker moet een resource-eigenaar zijn van het SaaS-product om eigendom van de resource over te dragen.

## <a name="check-for-access"></a>Controleren op toegang
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u hulp nodig hebt, kunt u [contact opnemen met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

- Het eigendom van facturering van de Azure-abonnementen wordt overgezet naar uw factuursectie. Houd de kosten voor deze abonnementen bij in [Azure Portal.](https://portal.azure.com)
- Geef anderen machtigingen om facturering voor deze abonnementen weer te geven en te beheren. Zie [Rollen en taken van een factuursectie](billing-understand-mca-roles.md#invoice-section-roles-and-tasks) voor meer informatie.
