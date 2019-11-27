---
title: Verbinding maken met een account van Google Cloud Platform aan Cloudyn in Azure | Microsoft Docs
description: Verbinding maken met een account van Google Cloud Platform om kosten en gebruiksgegevens in Cloudyn-rapporten weer te geven.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 937d1b6e0bc9ece0507821538fafb0f5d8c0ef99
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230139"
---
# <a name="connect-a-google-cloud-platform-account"></a>Verbinding maken met een account van Google Cloud Platform

U kunt uw bestaande Google Cloud Platform-account verbinding maken met Cloudyn. Nadat u uw account met Cloudyn verbonden, is kosten en gebruiksgegevens beschikbaar in Cloudyn-rapporten. In dit artikel helpt u bij het configureren en verbinden van uw Google-account met Cloudyn.


## <a name="collect-project-information"></a>Gegevens verzamelen

Begint u met het verzamelen van gegevens over uw project.

1. Meld u aan bij de Google Cloud Platform-console op [https://console.cloud.google.com](https://console.cloud.google.com).
2. Controleer de project gegevens die u wilt vrijgeven voor Cloudyn en noteer de **project naam** en de **project-id**. De gegevens voor de latere fasen bij de hand te houden.  
    ![project naam en project-ID weer gegeven in de Google Cloud Platform-console](./media/connect-google-account/gcp-console01.png)
3. Als u facturering niet is ingeschakeld en is gekoppeld aan uw project, maakt u een factureringsaccount. Zie [een nieuw facturerings account maken](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account)voor meer informatie.

## <a name="enable-storage-bucket-billing-export"></a>Opslag bucket facturering exporteren inschakelen

Uw Google-factureringsgegevens Cloudyn opgehaald uit een bucket van opslag. Behoud de **Bucket naam** en de informatie over het **rapport** voor het voor later gebruik tijdens de registratie van Cloudyn.

Google Cloud Storage gebruiken voor het opslaan van rapporten over gebruik leidt tot minimale kosten. Zie [prijzen voor Cloud Storage](https://cloud.google.com/storage/pricing)voor meer informatie.

1. Als u facturering exporteren niet hebt ingeschakeld, volgt u de instructies voor het [exporteren van facturering naar een bestand](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). U kunt de exportindeling van de JSON- of CSV-facturering.
2. Ga anders in de Google Cloud Platform-console naar **facturering** > het **exporteren van facturering**. Noteer de naam van de gefactureerde **Bucket** en het **rapport voorvoegsel**.  
    de export gegevens van ![facturering worden weer gegeven op de pagina facturering exporteren](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Google Cloud Platform-API's inschakelen

Voor het verzamelen van gebruiks-en asset nodig Cloudyn heeft de volgende Google Cloud Platform-API's ingeschakeld:

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- JSON-API van Google Cloud Storage
- Google Compute Engine-API

### <a name="enable-or-verify-apis"></a>In- of API's controleren

1. Selecteer het project dat u wilt registreren bij Cloudyn in de Google Cloud Platform-console.
2. Navigeer naar **api's & Services** > **bibliotheek**.
3. Zoeken gebruiken om dat elke API voor de eerder vermelde.
4. Controleer voor elke API of **API ingeschakeld** wordt weer gegeven. Anders klikt u op **inschakelen**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Een Google Cloud-account toevoegen aan Cloudyn

1. Open de Cloudyn-Portal vanuit het Azure Portal of navigeer naar [https://azure.cloudyn.com](https://azure.cloudyn.com/) en meld u aan.
2. Klik op **instellingen** (tandwiel-symbool) en selecteer vervolgens **Cloud accounts**.
3. Selecteer in **account beheer**het tabblad **Google-accounts** en klik vervolgens op **nieuwe toevoegen +** .
4. In **Google account name**voert u het e-mail adres voor het facturerings account in en klikt u op **volgende**.
5. In het dialoog venster Google-verificatie selecteert of voert u een Google-account in en **verleent** u cloudyn.com toegang tot uw account.
6. De gegevens van de aanvraag-project toevoegen dat u al vorige die u hebt genoteerd. Deze bevatten de **project-id**, de **project** naam, de naam van de **facturerings** Bucket en het voor voegsel van het **facturerings bestand** . Klik vervolgens op **Opslaan**  
    ![Google-project toevoegen aan Cloudyn-account](./media/connect-google-account/add-project.png)

Uw Google-account wordt weer gegeven in de lijst met accounts en moet worden **geauthenticeerd**. Klik hieronder moet uw Google-project de naam en ID worden weergegeven en hebben een groen vinkje symbool. De account status moet zijn **voltooid**.

Binnen een paar uur rapporten Cloudyn met gegevens over de kosten en gebruik van Google.

## <a name="next-steps"></a>Volgende stappen

- Ga verder met de zelf studie [gebruik en kosten](./tutorial-review-usage.md) voor Cloudyn voor meer informatie over Cloudyn.
