---
title: Een Google Cloud Platform-account verbinden met Cloudyn in Azure
description: Lees hoe u een Google Cloud Platform-account kunt verbinden om kosten en gebruiksgegevens te bekijken in Cloudyn-rapporten.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 8d549a8fd7c8b03f98fe2b11c94531ed323e0e3a
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690065"
---
# <a name="connect-a-google-cloud-platform-account"></a>Een Google Cloud Platform-account verbinden

U kunt uw bestaande Google Cloud Platform-account verbinden met Cloudyn. Nadat u uw account hebt verbonden met Cloudyn, zijn kosten en gebruiksgegevens beschikbaar in Cloudyn-rapporten. Dit artikel helpt u bij het configureren en verbinden van uw Google-account met Cloudyn.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="collect-project-information"></a>Projectgegevens verzamelen

U begint met het verzamelen van gegevens over uw project.

1. Meld u aan bij de Google Cloud Platform-console op [https://console.cloud.google.com](https://console.cloud.google.com).
2. Controleer de projectgegevens die u wilt overbrengen naar Cloudyn en noteer de waarden van **Projectnaam** en **Project-ID**. Bewaar deze gegevens voor latere stappen.  
    ![Projectnaam en project-id in de Google Cloud Platform-console](./media/connect-google-account/gcp-console01.png)
3. Als facturering niet is ingeschakeld en aan uw project is gekoppeld, maakt u een factureringsaccount. Zie [Create, Modify, or Close Your Cloud Billing Account](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account) voor meer informatie.

## <a name="enable-storage-bucket-billing-export"></a>Exporteren van facturering uit opslagbucket inschakelen

Cloudyn haalt uw Google-factureringsgegevens op uit een opslagbucket. Bewaar de waarden voor **Bucketnaam** en **Rapportvoorvoegsel** voor later gebruik tijdens Cloudyn-registratie.

Als u Google Cloud Storage gebruikt voor het opslaan van gebruiksrapporten, worden er minimale kosten in rekening gebracht. Zie [Prijzen voor Cloud Storage](https://cloud.google.com/storage/pricing) voor meer informatie.

1. Als u het factuurexport naar een bestand niet hebt ingeschakeld, volgt u de instructies in [Hoe u factuurexport naar een bestand kunt inschakelen](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). U kunt de indeling JSON of CSV gebruiken voor factuurexport.
2. Ga anders in de Google Cloud Platform-console naar **Facturering** > **Facturering exporteren**. Noteer de waarden voor **Bucketnaam** en **Rapportvoorvoegsel**.  
    ![Gegevens voor factuurexport op de pagina Facturering exporteren](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>API's van Google Cloud Platform inschakelen

Cloudyn kan alleen gegevens over het gebruik en assets verzamelen als de volgende Google Cloud Platform-API's zijn ingeschakeld voor Cloudyn:

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- Google Cloud Storage
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>API's inschakelen of verifiëren

1. Selecteer in de Google Cloud Platform-console het project dat u wilt registreren bij Cloudyn.
2. Ga naar **API's en services** > **Bibliotheek**.
3. Gebruik het zoekvak om eerder vermelde API's te vinden.
4. Controleer voor elke API of **API ingeschakeld** wordt weergegeven. Klik op **INSCHAKELEN** als dat niet zo is.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Een Google Cloud-account toevoegen aan Cloudyn

1. Open de Cloudyn-portal vanuit de Azure-portal of ga naar [https://azure.cloudyn.com](https://azure.cloudyn.com/) en meld u aan.
2. Klik op **Settings** (het tandwiel) en selecteer vervolgens **Cloud Accounts**.
3. Selecteer in **Accounts Management** het tabblad **Google Accounts** en klik op **Add new +** .
4. Voer bij **Google Account Name**het e-mailadres voor het factureringsaccount in en klik vervolgens op **Next**.
5. Selecteer of typ een Google-account in het verificatievenster van Google en selecteer vervolgens **ALLOW** om cloudyn.com toegang te geven tot uw account.
6. Voeg de gevraagde projectgegevens toe die u eerder hebt genoteerd. Het gaat om **Project ID**, naam van **Project**, naam van bucket voor **billing** en rapportvoorvoegsel voor **billing file**. Klik vervolgens op **Save**.  
    ![Google-project toevoegen aan Cloudyn-account](./media/connect-google-account/add-project.png)

Uw Google-account wordt weergegeven in de lijst met accounts met de vermelding **Authenticated**. Onder het account moeten de naam en de id van het Google-project worden vermeld, gemarkeerd met een groen vinkje. De accountstatus moet **Completed** zijn.

Binnen een paar uur bevatten Cloudyn-rapporten gegevens over de kosten en het gebruik van Google.

## <a name="next-steps"></a>Volgende stappen

- Als u meer wilt weten over Cloudyn, gaat u verder met de zelfstudie [Gebruik en kosten bekijken](tutorial-review-usage.md) voor Cloudyn.
