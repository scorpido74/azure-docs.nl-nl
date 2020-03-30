---
title: XML transformeren met XSLT-kaarten
description: XSLT-kaarten toevoegen om XML te transformeren in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: e186b9713c8464f8f37e1e0bf112c4118621925c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979409"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML transformeren met kaarten in Azure Logic Apps met Enterprise Integration Pack

Als u XML-gegevens wilt overbrengen tussen indelingen voor bedrijfsintegratiescenario's in Azure Logic Apps, kan uw logische app kaarten gebruiken, of meer specifiek, XSLT-toewijzingen (Extensible Style sheet Language Transformations). Een kaart is een XML-document waarin wordt beschreven hoe gegevens uit een XML-document kunnen worden omgezet in een andere indeling. 

Stel dat u regelmatig B2B-orders of facturen ontvangt van een klant die de yyymmdd-datumnotatie gebruikt. Uw organisatie gebruikt echter de datumnotatie MMDDYYY. U een kaart definiëren en gebruiken waarmee de YYYMMDD-datumnotatie wordt omgezet in de MMDDYYY-indeling voordat u de order- of factuurgegevens opslaat in uw klantenactiviteitendatabase.

Zie [Limieten en configuratiegegevens voor Azure Logic Apps voor](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)beperkingen met betrekking tot integratieaccounts en artefacten zoals kaarten.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) waar u uw kaarten en andere artefacten opslaat voor bedrijfsintegratie en B2B-oplossingen (business-to-business).

* Als uw kaart verwijst naar een externe verzameling, moet u *zowel de assemblage als de kaart* uploaden naar uw integratieaccount. Zorg ervoor dat u [*uw vergadering eerst uploadt*](#add-assembly)en upload vervolgens de kaart die verwijst naar de assemblage.

  Als uw assemblage 2 MB of kleiner is, u uw assemblage *rechtstreeks* vanuit de Azure-portal aan uw integratieaccount toevoegen. Als uw verzameling of kaart echter groter is dan 2 MB, maar niet groter dan de [groottelimiet voor assemblages of kaarten,](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)hebt u de volgende opties:

  * Voor vergaderingen hebt u een Azure blob-container nodig waar u uw assemblage en de locatie van die container uploaden. Op die manier u die locatie later verstrekken wanneer u de assemblage toevoegt aan uw integratieaccount. 
  Voor deze taak hebt u de volgende items nodig:

    | Item | Beschrijving |
    |------|-------------|
    | [Azure-opslagaccount](../storage/common/storage-account-overview.md) | Maak in dit account een Azure blob-container voor uw verzameling. Meer informatie over [het maken van een opslagaccount](../storage/common/storage-account-create.md). |
    | Blobcontainer | In deze container u uw montage uploaden. U hebt ook de locatie van deze container nodig wanneer u de assemblage toevoegt aan uw integratieaccount. Meer informatie over het [maken van een blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Met deze tool u eenvoudiger opslagaccounts en blobcontainers beheren. Als u Storage Explorer wilt gebruiken, [downloadt en installeert u Azure Storage Explorer](https://www.storageexplorer.com/). Koppel Vervolgens Storage Explorer aan uw opslagaccount door de stappen te volgen in [Aan de slag met Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Zie [Snelstart: Een blob maken in objectopslag met Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)voor meer informatie. <p>Of zoek en selecteer uw opslagaccount in de Azure-portal. Selecteer **Storage Explorer**in het menu van uw opslagaccount . |
    |||

  * Voor kaarten u momenteel grotere kaarten toevoegen met de [Azure Logic Apps REST API - Kaarten](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

U hebt geen logische app nodig bij het maken en toevoegen van kaarten. Om een kaart te gebruiken, moet uw logische app echter worden gekoppeld aan een integratieaccount waar u die kaart opslaat. Meer informatie over [het koppelen van logische apps aan integratieaccounts](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Als u nog geen logische app hebt, leest u [hoe u logische apps maakt.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Basisverzamelingen toevoegen

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Als u uw integratieaccount wilt zoeken en openen, selecteert u in het hoofdmenu van Azure **alle services**. 
   Voer in het zoekvak 'integratieaccount' in. 
   Selecteer **Integratieaccounts**.

   ![Integratieaccount zoeken](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Selecteer het integratieaccount waar u uw verzameling wilt toevoegen, bijvoorbeeld:

   ![Integratieaccount selecteren](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Selecteer op de **pagina Overzicht van** uw integratieaccount onder **Componenten**de tegel **Verzamelingen.**

   ![Selecteer 'Vergaderingen'](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Nadat de pagina **Verzamelingen** is geopend, kiest u **Toevoegen**.

   ![Kies 'Toevoegen'](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Volg op basis van de grootte van uw assemblagebestand de stappen voor het uploaden van een verzameling die [maximaal 2 MB](#smaller-assembly) of meer dan 2 MB [bedraagt, maar slechts maximaal 8 MB.](#larger-assembly)
Zie [Limieten en configuratie voor Azure Logic Apps voor](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)beperkingen voor assemblagehoeveelheden in integratieaccounts.

> [!NOTE]
> Als u uw vergadering wijzigt, moet u uw kaart ook bijwerken of de kaart al dan niet is gewijzigd.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Samenstellingen tot 2 MB toevoegen

1. Voer **onder Assemblage toevoegen**een naam in voor uw vergadering. Klein **bestand** geselecteerd houden. Kies naast het **vak Vergadering** het mappictogram. Zoek en selecteer de verzameling die u uploadt, bijvoorbeeld:

   ![Kleinere assemblage uploaden](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   In de eigenschap **Assemblagenaam** wordt de bestandsnaam van de assemblage automatisch weergegeven nadat u de assemblage hebt geselecteerd.

1. Als je er klaar voor bent, kies **je OK.**

   Nadat het uploaden van het assemblagebestand is voltooid, wordt de verzameling weergegeven in de lijst **Meten.**

   ![Lijst met geüploade verzamelingen](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Op de **overzichtspagina van** uw integratieaccount, onder **Componenten**, toont de tegel **Verzamelingen** nu het aantal geüploade verzamelingen, bijvoorbeeld:

   ![Geüploade vergaderingen](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Samenstellingen van meer dan 2 MB toevoegen

Als u grotere samenstellingen wilt toevoegen, u uw verzameling uploaden naar een Azure blob-container in uw Azure-opslagaccount. Uw stappen voor het toevoegen van samenstellingen verschillen op basis van of uw blobcontainer openbare leestoegang heeft. Controleer dus eerst of uw blobcontainer openbare leestoegang heeft door de volgende stappen te volgen: [Openbaar toegangsniveau instellen voor blobcontainer](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Toegangsniveau voor containers controleren

1. Open Azure Storage Explorer. Vouw in het Explorer-venster uw Azure-abonnement uit als dit nog niet is uitgebreid.

1. Vouw **opslagaccounts** uit > {*uw opslagaccount*} > **Blob-containers**. Selecteer uw blobcontainer.

1. Selecteer **Public Access Level instellen**in het snelmenu van uw blobcontainer .

   * Als uw blobcontainer ten minste openbare toegang heeft, kiest u **Annuleren**en volgt u de volgende stappen later op deze pagina: [Uploaden naar containers met openbare toegang](#public-access-assemblies)

     ![Toegang tot het publiek](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Als uw blobcontainer geen openbare toegang heeft, kiest u **Annuleren**en volgt u de volgende stappen later op deze pagina: [Uploaden naar containers zonder openbare toegang](#no-public-access-assemblies)

     ![Geen toegang tot het publiek](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Uploaden naar containers met openbare toegang

1. Upload de assemblage naar uw opslagaccount. 
   Kies **Uploaden**in het rechtervenster.

1. Nadat je het uploaden hebt voltooid, selecteer je geüploade verzameling. Kies op de werkbalk **URL kopiëren** zodat u de URL van de verzameling kopieert.

1. Ga terug naar de Azure-portal waar het deelvenster **Assemblage toevoegen** is geopend. 
   Voer een naam in voor uw vergadering. 
   Kies **Groot bestand (groter dan 2 MB)**.

   Het vak **Inhoud URI** wordt nu weergegeven in plaats van het vak **Vergadering.**

1. Plak in het vak **Inhoud URI** de URL van uw vergadering. 
   Maak het toevoegen van uw montage af.

Nadat de montage is voltooid, wordt het schema weergegeven in de lijst **Verzamelingen.**
Op de **overzichtspagina van** uw integratieaccount, onder **Componenten**, toont de tegel **Verzamelingen** nu het aantal geüploade verzamelingen.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Uploaden naar containers zonder toegang tot het publiek

1. Upload de assemblage naar uw opslagaccount. 
   Kies **Uploaden**in het rechtervenster.

1. Nadat u klaar bent met het uploaden, genereert u een gedeelde toegangshandtekening (SAS) voor uw vergadering. 
   Selecteer In het snelmenu van uw vergadering de optie **Gedeelde toegangshandtekening ophalen**.

1. Selecteer in het deelvenster **Handtekening van gedeelde toegang** de optie Gedeelde toegangshandtekening URI > **Maken**op **containerniveau**genereren. 
   Nadat de SAS-URL is gegenereerd, kiest u naast het **vak URL** de optie **Kopiëren**.

1. Ga terug naar de Azure-portal waar het deelvenster **Assemblage toevoegen** is geopend. 
   Voer een naam in voor uw vergadering. 
   Kies **Groot bestand (groter dan 2 MB)**.

   Het vak **Inhoud URI** wordt nu weergegeven in plaats van het vak **Vergadering.**

1. Plak in het vak **Inhoud URI** de SAS URI die u eerder hebt gegenereerd. Maak het toevoegen van uw montage af.

Nadat de montage is voltooid, wordt de vergadering weergegeven in de lijst **Schema's.** Op de **overzichtspagina van** uw integratieaccount, onder **Componenten**, toont de tegel **Verzamelingen** nu het aantal geüploade verzamelingen.

## <a name="create-maps"></a>Kaarten maken

Als u een XSLT-document wilt maken dat u als kaart gebruiken, u Visual Studio 2015 gebruiken voor het maken van een BizTalk Integration-project met behulp van het [Enterprise Integration Pack.](logic-apps-enterprise-integration-overview.md) In dit project u een integratiekaartbestand bouwen, waarmee u items visueel toewijzen tussen twee XML-schemabestanden. Nadat u dit project hebt gebouwd, krijgt u een XSLT-document.
Zie [Limieten en configuratie voor Azure Logic Apps voor](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)beperkingen op kaarthoeveelheden in integratieaccounts. 

## <a name="add-maps"></a>Kaarten toevoegen

Nadat je alle samenstellingen hebt geüpload waarnaar je kaart verwijst, kun je nu je kaart uploaden.

1. Als u zich nog niet hebt aangemeld, meldt u zich aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-accountreferenties. 

1. Als uw integratieaccount nog niet is geopend, selecteert u in het hoofdmenu van Azure **alle services**. 
   Voer in het zoekvak 'integratieaccount' in. 
   Selecteer **Integratieaccounts**.

   ![Integratieaccount zoeken](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Selecteer het integratieaccount waar u uw kaart wilt toevoegen, bijvoorbeeld:

   ![Integratieaccount selecteren](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Selecteer op de **pagina Overzicht van** uw integratieaccount onder **Componenten**de tegel **Kaarten.**

   ![Selecteer 'Kaarten'](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Nadat de pagina **Kaarten** is geopend, kiest u **Toevoegen**.

   ![Kies 'Toevoegen'](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Kaarten tot 2 MB toevoegen

1. Voer **onder Kaart toevoegen**een naam voor uw kaart in. 

1. Selecteer **onder Maptype**het type: **Liquid**, **XSLT**, **XSLT 2.0**of **XSLT 3.0**.

1. Klein **bestand** geselecteerd houden. Kies naast het vak **Kaart** het mappictogram. Zoek en selecteer de kaart die u uploadt, bijvoorbeeld:

   ![Kaart uploaden](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Als u de eigenschap **Naam** leeg hebt gelaten, wordt de bestandsnaam van de kaart automatisch in die eigenschap weergegeven nadat u het kaartbestand hebt geselecteerd. 
   U echter wel elke unieke naam gebruiken.

1. Als je er klaar voor bent, kies **je OK.** 
   Nadat het uploaden van uw kaartbestand is voltooid, wordt de kaart weergegeven in de lijst **Kaarten.**

   ![Lijst met geüploade kaarten](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Op de **overzichtspagina van** uw integratieaccount, onder **Componenten**, toont de tegel **Kaarten** nu het aantal geüploade kaarten, bijvoorbeeld:

   ![Geüploade kaarten](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Kaarten van meer dan 2 MB toevoegen

Gebruik momenteel, om grotere kaarten toe te voegen, de [AZURE Logic Apps REST API - Kaarten](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Kaarten bewerken

Als u een bestaande kaart wilt bijwerken, moet u een nieuw kaartbestand uploaden met de gewenste wijzigingen. U echter eerst de bestaande kaart downloaden om te bewerken.

1. Zoek en open uw integratieaccount in de [Azure-portal,](https://portal.azure.com)als deze nog niet is geopend.

1. Selecteer **alle services**in het hoofdmenu van Azure . Voer in het zoekvak 'integratieaccount' in. Selecteer **Integratieaccounts**.

1. Selecteer het integratieaccount waar u uw kaart wilt bijwerken.

1. Selecteer op de **pagina Overzicht van** uw integratieaccount onder **Componenten**de tegel **Kaarten.**

1. Nadat de pagina **Kaarten** is geopend, selecteert u uw kaart. 
   Als u de kaart eerst wilt downloaden en bewerken, kiest u **Downloaden**en slaat u de kaart op.

1. Wanneer u klaar bent om de bijgewerkte kaart te uploaden, selecteert u op de pagina **Kaarten** de kaart die u wilt bijwerken en kiest **u Bijwerken**.

1. Zoek en selecteer de bijgewerkte kaart die u wilt uploaden. 
   Nadat het uploaden van uw kaartbestand is voltooid, wordt de bijgewerkte kaart weergegeven in de lijst **Kaarten.**

## <a name="delete-maps"></a>Kaarten verwijderen

1. Zoek en open uw integratieaccount in de [Azure-portal,](https://portal.azure.com)als deze nog niet is geopend.

1. Selecteer **alle services**in het hoofdmenu van Azure . 
   Voer in het zoekvak 'integratieaccount' in. 
   Selecteer **Integratieaccounts**.

1. Selecteer het integratieaccount waar u uw kaart wilt verwijderen.

1. Selecteer op de **pagina Overzicht van** uw integratieaccount onder **Componenten**de tegel **Kaarten.**

1. Nadat de pagina **Kaarten** is geopend, selecteert u uw kaart en kiest u **Verwijderen**.

1. Als u wilt bevestigen dat u de kaart wilt verwijderen, kiest u **Ja**.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Meer informatie over schema's](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Meer informatie over transformaties](../logic-apps/logic-apps-enterprise-integration-transform.md)
