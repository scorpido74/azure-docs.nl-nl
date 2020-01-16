---
title: Uw Azure-abonnement registreren bij Cloudyn | Microsoft Docs
description: In deze quickstart vindt u de details van de registratieprocedure voor het maken van een Cloudyn-proefabonnement en het aanmelden bij de Cloudyn-portal.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
manager: benshy
ms.openlocfilehash: 04ca2027ab0433c710fba40e1a980c71e1127c96
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987689"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Een individueel Azure-abonnement registreren en gegevenskosten weergeven

U gebruikt uw Azure-abonnement om u te registreren bij Cloudyn. Uw registratie biedt toegang tot de Cloudyn-portal. In deze quickstart vindt u de details van de registratieprocedure voor het maken van een Cloudyn-proefabonnement en het aanmelden bij de Cloudyn-portal. U vindt er ook informatie over het weergeven van gegevenskosten.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

- Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="register-with-cloudyn"></a>Registreren bij Cloudyn

1. Klik in Azure Portal, in de lijst met services, op **Cost Management en facturering**.
2. Klik onder **Overzicht** op **Cloudyn**  
    ![Cloudyn-pagina die wordt weergegeven in Azure Portal](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. Klik op de pagina **Cost Management** op **Go to Cloudyn** om de Cloudyn-registratiepagina in een nieuw venster te openen.
4. Typ op de pagina voor de registratie van het proefabonnement in de Cloudyn-portal de naam van uw bedrijf en selecteer **Azure Individual Subscription Owner** en klik op **Volgende**. Uw accountnaam en tenant-id worden automatisch toegevoegd aan het formulier.  
    ![Pagina voor de registratie voor een proefabonnement waar u de registratiegegevens invoert](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Selecteer uw **Aanbiedings-id - Naam** die is gekoppeld aan uw abonnement. Als u niet zeker bent van de tarief-id voor uw abonnement, kunt u uw Azure-factuur weergeven en zoeken naar **Aanbiedings-id**.
6. Accepteer de Gebruiksvoorwaarden en valideer uw gegevens. Klik vervolgens op **Volgende**.
7. Klik op de pagina **Gather additional data** op **Volgende** om Cloudyn te autoriseren Azure-brongegevens te verzamelen. De gegevens die worden verzameld zijn onder meer gegevens over gebruik, prestaties, facturering en tags van uw abonnementen.  
    ![Pagina voor het verzamelen van aanvullende gegevens waar u Cloudyn machtigt](./media/quick-register-azure-sub/gather-additional.png)
8. U gaat in de browser naar de aanmeldingspagina voor Cloudyn. Meld u aan met de referenties voor uw Azure-abonnement.
9. Klik op **Go to Cloudyn** op de Cloudyn-portal te openen. Op de pagina **Accounts Management** zou u de accountgegevens voor het Azure-abonnement moeten zien.  
    ![Pagina voor het beheren van accounts waarop Azure-abonnementsgegevens worden vermeld](./media/quick-register-azure-sub/accounts-mgt.png)

Raadpleeg [Finding your Directory GUID and Rate ID for use in Cloudyn](https://youtu.be/PaRjnyaNGMI) (Uw Directory-GUID en tarief-id zoeken voor gebruik in Cloudyn) als u een zelfstudievideo wilt bekijken over het registreren van uw Azure-abonnement.

[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de gegevens van uw Azure-abonnement gebruikt om u te registreren bij Cloudyn. U hebt zich ook aangemeld bij de Cloudyn-portal en de gegevenskosten weergegeven. Ga voor meer informatie over Cloudyn verder met de zelfstudie voor Cloudyn.

> [!div class="nextstepaction"]
> [Gebruik en kosten controleren](tutorial-review-usage.md)
