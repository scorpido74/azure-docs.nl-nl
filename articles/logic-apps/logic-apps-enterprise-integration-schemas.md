---
title: XML valideren met schema's
description: Schema's toevoegen om XML-documenten te valideren in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979382"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML valideren met schema's in Azure Logic Apps met Enterprise Integration Pack

Als u wilt controleren of documenten geldige XML gebruiken en de verwachte gegevens in de vooraf gedefinieerde indeling voor bedrijfsintegratiescenario's in Azure Logic Apps hebben, kan uw logische app schema's gebruiken. Een schema kan ook berichten valideren die logische apps uitwisselen in B2B-scenario's (business-to-business).

Zie [Limieten en configuratiegegevens voor Azure Logic Apps voor](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)beperkingen met betrekking tot integratieaccounts en artefacten zoals schema's.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, <a href="https://azure.microsoft.com/free/" target="_blank">meld u dan aan voor een gratis Azure-account</a>.

* Een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) waarin u uw schema's en andere artefacten opslaat voor bedrijfsintegratie en B2B-oplossingen (business-to-business). 

  Als uw schema [2 MB of kleiner](#smaller-schema)is, u uw schema rechtstreeks vanuit de Azure-portal aan uw integratieaccount toevoegen. Als uw schema echter groter is dan 2 MB, maar niet groter dan de [limiet voor de schemagrootte,](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)u uw schema uploaden naar een Azure-opslagaccount. 
  Als u dat schema wilt toevoegen aan uw integratieaccount, u vervolgens vanaf uw integratieaccount een koppeling maken naar uw opslagaccount. 
  Voor deze taak zijn hier de items die u nodig hebt: 

  * [Azure-opslagaccount](../storage/common/storage-account-overview.md) waar u een blobcontainer maakt voor uw schema. Meer informatie over het [maken van een opslagaccount](../storage/common/storage-account-create.md). 

  * Blob-container voor het opslaan van uw schema. Meer informatie over het [maken van een blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md). 
  U hebt de inhoud uri van uw container later nodig wanneer u het schema toevoegt aan uw integratieaccount.

  * [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md), die u gebruiken voor het beheren van opslagaccounts en blobcontainers. 
  Als u Storage Explorer wilt gebruiken, kiest u hier een van de opties:
  
    * Zoek en selecteer uw opslagaccount in de Azure-portal. 
    Selecteer **Storage Explorer**in het menu van uw opslagaccount .

    * Download [en installeer Azure Storage Explorer](https://www.storageexplorer.com/)voor de desktopversie. 
    Koppel Vervolgens Storage Explorer aan uw opslagaccount door de stappen te volgen in [Aan de slag met Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Zie [Snelstart: Een blob maken in objectopslag met Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)voor meer informatie.

U hebt geen logische app nodig bij het maken en toevoegen van schema's. Als u echter een schema wilt gebruiken, moet uw logische app worden gekoppeld aan een integratieaccount waar u dat schema opslaat. Meer informatie over [het koppelen van logische apps aan integratieaccounts](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Als u nog geen logische app hebt, leest u [hoe u logische apps maakt.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-schemas"></a>Schema's toevoegen

1. Gebruik de referenties van uw Azure-account om u aan melden bij het <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

1. Als u uw integratieaccount wilt zoeken en openen, selecteert u in het hoofdmenu van Azure **alle services**. Voer in het zoekvak 'integratieaccount' in. Selecteer **Integratieaccounts**.

   ![Integratieaccount zoeken](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Selecteer het integratieaccount waar u uw schema wilt toevoegen, bijvoorbeeld:

   ![Integratieaccount selecteren](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Selecteer op de **pagina Overzicht van** uw integratieaccount onder **Componenten**de tegel **Schema's.**

   ![Selecteer 'Schema's'](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Nadat de pagina **Schema's** is geopend, kiest u **Toevoegen**.

   ![Kies 'Toevoegen'](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Volg de stappen voor het uploaden van een schema met een inhoud [van maximaal 2 MB](#smaller-schema) of meer dan 2 [MB, maximaal 8 MB.](#larger-schema)

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Schema's tot 2 MB toevoegen

1. Voer **onder Schema toevoegen**een naam voor uw schema in. 
   Klein **bestand** geselecteerd houden. Kies naast het vak **Schema** het mappictogram. Zoek en selecteer het schema dat u uploadt, bijvoorbeeld:

   ![Kleiner schema uploaden](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Als je er klaar voor bent, kies **je OK.**

   Nadat het uploaden van uw schema is voltooid, wordt het schema weergegeven in de lijst **Schema's.**

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Schema's van meer dan 2 MB toevoegen

Als u grotere schema's wilt toevoegen, u uw schema uploaden naar een Azure blob-container in uw Azure-opslagaccount. Uw stappen voor het toevoegen van schema's verschillen op basis van of uw blobcontainer openbare leestoegang heeft. Controleer dus eerst of uw blobcontainer openbare leestoegang heeft door de volgende stappen te volgen: [Openbaar toegangsniveau instellen voor blobcontainer](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Toegangsniveau voor containers controleren

1. Open Azure Storage Explorer. Vouw in het Explorer-venster uw Azure-abonnement uit als dit nog niet is uitgebreid.

1. Vouw **opslagaccounts** uit > {*uw opslagaccount*} > **Blob-containers**. Selecteer uw blobcontainer.

1. Selecteer **Public Access Level instellen**in het snelmenu van uw blobcontainer .

   * Als uw blobcontainer ten minste openbare toegang heeft, kiest u **Annuleren**en volgt u de volgende stappen later op deze pagina: [Uploaden naar containers met openbare toegang](#public-access)

     ![Toegang tot het publiek](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Als uw blobcontainer geen openbare toegang heeft, kiest u **Annuleren**en volgt u de volgende stappen later op deze pagina: [Uploaden naar containers zonder openbare toegang](#public-access)

     ![Geen toegang tot het publiek](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Uploaden naar containers met openbare toegang

1. Upload het schema naar uw opslagaccount. 
   Kies **Uploaden**in het rechtervenster.

1. Nadat je het uploaden hebt voltooid, selecteer je geüploade schema. Kies op de werkbalk **URL kopiëren** zodat u de URL van het schema kopieert.

1. Ga terug naar de Azure-portal waar het deelvenster **Schema toevoegen** is geopend. 
   Voer een naam in voor uw vergadering. 
   Kies **Groot bestand (groter dan 2 MB)**. 

   Het vak **Inhoud URI** wordt nu weergegeven in plaats van het vak **Schema.**

1. Plak in het vak **Inhoud URI** de URL van uw schema. 
   Maak het toevoegen van uw schema af.

Nadat het uploaden van uw schema is voltooid, wordt het schema weergegeven in de lijst **Schema's.** Op de **overzichtspagina van** uw integratieaccount, onder **Componenten**, toont de tegel **Schema's** nu het aantal geüploade schema's.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Uploaden naar containers zonder toegang tot het publiek

1. Upload het schema naar uw opslagaccount. 
   Kies **Uploaden**in het rechtervenster.

1. Nadat u klaar bent met het uploaden, genereert u een gedeelde toegangshandtekening (SAS) voor uw schema. 
   Selecteer In het snelmenu van uw schema de optie **Gedeelde toegangshandtekening ophalen**.

1. Selecteer in het deelvenster **Handtekening van gedeelde toegang** de optie Gedeelde toegangshandtekening URI > **Maken**op **containerniveau**genereren. 
   Nadat de SAS-URL is gegenereerd, kiest u naast het **vak URL** de optie **Kopiëren**.

1. Ga terug naar de Azure-portal waar het deelvenster **Schema toevoegen** is geopend. Kies **Groot bestand**.

   Het vak **Inhoud URI** wordt nu weergegeven in plaats van het vak **Schema.**

1. Plak in het vak **Inhoud URI** de SAS URI die u eerder hebt gegenereerd. Maak het toevoegen van uw schema af.

Nadat het uploaden van uw schema is voltooid, wordt het schema weergegeven in de lijst **Schema's.** Op de **overzichtspagina van** uw integratieaccount, onder **Componenten**, toont de tegel **Schema's** nu het aantal geüploade schema's.

## <a name="edit-schemas"></a>Schema's bewerken

Als u een bestaand schema wilt bijwerken, moet u een nieuw schemabestand uploaden met de gewenste wijzigingen. U echter eerst het bestaande schema downloaden voor bewerking.

1. Zoek en open uw integratieaccount in de <a href="https://portal.azure.com" target="_blank">Azure-portal,</a>als deze nog niet is geopend.

1. Selecteer **alle services**in het hoofdmenu van Azure . 
   Voer in het zoekvak 'integratieaccount' in. 
   Selecteer **Integratieaccounts**.

1. Selecteer het integratieaccount waar u uw schema wilt bijwerken.

1. Selecteer op de **pagina Overzicht van** uw integratieaccount onder **Componenten**de tegel **Schema's.**

1. Nadat de pagina **Schema's** is geopend, selecteert u uw schema. 
   Als u het schema eerst wilt downloaden en bewerken, kiest u **Downloaden**en slaat u het schema op.

1. Wanneer u klaar bent om het bijgewerkte schema te **uploaden,** selecteert u op de pagina Schema's het schema dat u wilt bijwerken en kiest **u Bijwerken**.

1. Zoek en selecteer het bijgewerkte schema dat u wilt uploaden. 
   Nadat het uploaden van uw schemabestand is voltooid, wordt het bijgewerkte schema weergegeven in de lijst **Schema's.**

## <a name="delete-schemas"></a>Schema's verwijderen

1. Zoek en open uw integratieaccount in de <a href="https://portal.azure.com" target="_blank">Azure-portal,</a>als deze nog niet is geopend.

1. Selecteer **alle services**in het hoofdmenu van Azure . 
   Voer in het zoekvak 'integratieaccount' in. 
   Selecteer **Integratieaccounts**.

1. Selecteer het integratieaccount waar u uw schema wilt verwijderen.

1. Selecteer op de **pagina Overzicht van** uw integratieaccount onder **Componenten**de tegel **Schema's.**

1. Nadat de pagina **Schema's** is geopend, selecteert u uw schema en kiest u **Verwijderen**.

1. Als u wilt bevestigen dat u het schema wilt verwijderen, kiest u **Ja**.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [Meer informatie over kaarten](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Meer informatie over transformaties](../logic-apps/logic-apps-enterprise-integration-transform.md)
