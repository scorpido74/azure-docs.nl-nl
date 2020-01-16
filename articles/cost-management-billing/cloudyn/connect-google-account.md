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
ms.openlocfilehash: 8849811560c7e3d35a40b4725dbbb63c82745123
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994569"
---
# <a name="connect-a-google-cloud-platform-account"></a>Verbinding maken met een account van Google Cloud Platform

U kunt uw bestaande Google Cloud Platform-account verbinding maken met Cloudyn. Nadat u uw account met Cloudyn verbonden, is kosten en gebruiksgegevens beschikbaar in Cloudyn-rapporten. In dit artikel helpt u bij het configureren en verbinden van uw Google-account met Cloudyn.


## <a name="collect-project-information"></a>Gegevens verzamelen

Begint u met het verzamelen van gegevens over uw project.

1. Aanmelden bij de Google Cloud Platform-console op [ https://console.cloud.google.com ](https://console.cloud.google.com).
2. Lees de projectinformatie die u voorbereiden naar Cloudyn en noteer wilt de **projectnaam** en de **Project-ID**. De gegevens voor de latere fasen bij de hand te houden.  
    ![Naam van het project en Project-ID wordt weergegeven in de Google Cloud Platform-console](./media/connect-google-account/gcp-console01.png)
3. Als u facturering niet is ingeschakeld en is gekoppeld aan uw project, maakt u een factureringsaccount. Zie voor meer informatie, [maken van een nieuwe factureringsaccount](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Opslag bucket facturering exporteren inschakelen

Uw Google-factureringsgegevens Cloudyn opgehaald uit een bucket van opslag. Houd de **Bucketnaam** en **rapport voorvoegsel** informatie handig voor later gebruik tijdens de registratie van Cloudyn.

Google Cloud Storage gebruiken voor het opslaan van rapporten over gebruik leidt tot minimale kosten. Zie voor meer informatie, [prijzen voor Cloud Storage](https://cloud.google.com/storage/pricing).

1. Als u facturering exporteren naar een bestand niet hebt ingeschakeld, volgt u de instructies op de [inschakelen facturering exporteren naar een bestand](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). U kunt de exportindeling van de JSON- of CSV-facturering.
2. Anders wordt in de Google Cloud Platform-console, Ga naar **facturering** > **facturering export**. Houd er rekening mee uw facturering **Bucketnaam** en **rapport voorvoegsel**.  
    ![Facturering export informatie die wordt weergegeven op de pagina voor het exporteren van facturering](./media/connect-google-account/billing-export.png)

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
2. Navigeer naar **API's en Services** > **bibliotheek**.
3. Zoeken gebruiken om dat elke API voor de eerder vermelde.
4. Controleer voor elke API **API ingeschakeld** wordt weergegeven. Klik anders op **inschakelen**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Een Google Cloud-account toevoegen aan Cloudyn

1. Open de Cloudyn-portal vanuit Azure portal of Ga naar [ https://azure.cloudyn.com ](https://azure.cloudyn.com/) en meld u aan.
2. Klik op **instellingen** (symbol tandwiel) en selecteer vervolgens **Cloudaccounts**.
3. In **Accounts Management**, selecteer de **Google-Accounts** tabblad en klik vervolgens op **nieuwe toevoegen +** .
4. In **Google-accountnaam**, voer het e-mailadres voor de facturering account en klik vervolgens op **volgende**.
5. In het dialoogvenster voor Google-verificatie, selecteer of typ een Google-account en vervolgens **toestaan** cloudyn.com toegang tot uw account.
6. De gegevens van de aanvraag-project toevoegen dat u al vorige die u hebt genoteerd. Dit zijn onder andere **Project-ID**, **Project** naam **facturering** Bucketnaam, en **facturering bestand** voorvoegsel rapport en klik vervolgens op  **Sla**.  
    ![Google-project toevoegen aan Cloudyn-account](./media/connect-google-account/add-project.png)

Uw Google-account wordt weergegeven in de lijst van accounts en moet er **geverifieerde**. Klik hieronder moet uw Google-project de naam en ID worden weergegeven en hebben een groen vinkje symbool. Status van de account moet zijn **voltooid**.

Binnen een paar uur rapporten Cloudyn met gegevens over de kosten en gebruik van Google.

## <a name="next-steps"></a>Volgende stappen

- Voor meer informatie over Cloudyn, blijven de [gebruik en kosten bekijken](tutorial-review-usage.md) voor Cloudyn.
