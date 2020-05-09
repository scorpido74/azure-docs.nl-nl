---
title: 'Quick Start: een Azure Storage-account integreren met Azure CDN'
description: Leer hoe u Azure Content Delivery Network (CDN) kunt gebruiken en hoe u inhoud met een hoge bandbreedte via CDN kunt leveren door blobs van Azure Storage in de cache op te slaan.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 4086a8f354e5e906325d9c324410f3546a32f658
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996172"
---
# <a name="quickstart-integrate-an-azure-storage-account-with-azure-cdn"></a>Snelstartgids: een Azure Storage-account integreren met Azure CDN

In deze Snelstartgids schakelt u [Azure Content Delivery Network (CDN)](cdn-overview.md) in om inhoud van Azure Storage in de cache te plaatsen. Azure CDN biedt ontwikkelaars een mondiale oplossing voor het leveren van inhoud met hoge bandbreedte. Het kan worden gebruikt om blobs en statische inhoud van rekeninstanties in de cache op fysieke knooppunten in de Verenigde Staten, Europa, Azië, Australië en Zuid-Amerika op te slaan.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw Azure-account.

## <a name="create-a-storage-account"></a>Create a storage account

Een opslagaccount biedt toegang tot Azure Storage-services. Het opslagaccount vertegenwoordigt het hoogste niveau van de naamruimte voor het verkrijgen van toegang tot elk van de onderdelen van de Azure Storage-service: Azure Blob Storage, Queue Storage en Table Storage. Zie [Inleiding tot Microsoft Azure Storage](../storage/common/storage-introduction.md) voor meer informatie.

Als u een opslagaccount wilt maken, moet u de servicebeheerder of een co-beheerder voor het gekoppelde abonnement zijn.

1. Selecteer in de Azure Portal **een resource maken** in de linkerbovenhoek. Het deelvenster **Nieuw** verschijnt.

1. Zoek naar **opslag account** en selecteer **opslag account-blob, bestand, tabel, wachtrij** in de vervolg keuzelijst. Selecteer vervolgens **maken**:
    
    ![Opslagresource selecteren](./media/cdn-create-a-storage-account-with-cdn/cdn-select-new-storage-account.png)

1. Voer in het **deel venster opslag account maken**de volgende gegevens in:

    | Instelling | Waarde | 
    | --- | --- |
    | Project Details > resource groep | Selecteer **nieuwe maken** en gebruik de naam *CDNQuickstart-RG*. U kunt ook een bestaande resource groep gebruiken als u dat wilt. |
    | Exemplaar gegevens > naam van het opslag account | Voer een naam in voor het account met 3-24 kleine letters en cijfers. De naam moet uniek zijn binnen Azure en wordt de hostnaam in de URL die wordt gebruikt voor het adresseren van BLOB-, wachtrij-of tabel bronnen voor het abonnement. Als u een container resource in Blob Storage wilt adresseren, gebruikt u een URI in de volgende indeling: http://*&lt;storageaccountname&gt;*. blob.core.Windows.net/*&lt;container-name&gt;*.
    | Exemplaar Details > locatie | Selecteer in de vervolg keuzelijst een Azure-regio in de buurt van uw keuze. |
    
    Zorg ervoor dat alle overige details zijn ingesteld op de standaard waarden en selecteer vervolgens **controleren + maken**.

1. Het kan enkele minuten duren voordat het opslag account is gemaakt. Zodra het maken is voltooid, selecteert u **naar resource gaan** om de pagina van het opslag account te openen voor de volgende stap.

## <a name="enable-azure-cdn-for-the-storage-account"></a>Azure CDN inschakelen voor het opslagaccount

1. Selecteer **BLOB service** > **Azure CDN** in het menu links op de pagina voor uw opslag account. De pagina **Azure CDN** wordt weergegeven.

    ![Een CDN-eindpunt maken](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)
    
1. Voer in het gedeelte **Nieuw eind punt** de volgende gegevens in:

    | Instelling  | Waarde |
    | -------- | ----- |
    | **CDN-profiel** | Selecteer **nieuwe maken** en voer uw profiel naam in, bijvoorbeeld *CDN-profiel-123*. Een profiel is een verzameling eind punten. |
    | **Prijs categorie** | Selecteer een van de **standaard** opties, zoals **Standard micro soft**. |
    | **Naam van CDN-eindpunt** | Voer de hostnaam van het eind punt in, bijvoorbeeld *CDN-endpoint-123*. Deze naam moet wereld wijd uniek zijn in azure, omdat deze toegang heeft tot uw resources in de cache via het URL _ &lt;-eind punt name&gt;_. azureedge.net. |
    | **Hostnaam van oorsprong** | Een nieuw CDN-eindpunt gebruikt standaard de hostnaam van uw opslagaccount als de bronserver. |

1. Selecteer **Maken**. Zodra het eindpunt is gemaakt, wordt dit weergegeven in de lijst met eindpunten.

    ![Opslag nieuw CDN-eindpunt](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!TIP]
> Als u voor uw CDN-eindpunt geavanceerde configuratie-instellingen wilt opgeven, zoals [ optimalisatie voor het downloaden van grote bestanden](cdn-optimization-overview.md#large-file-download), kunt u in plaats daarvan de [Azure CDN-extensie](cdn-create-new-endpoint.md) gebruiken om een CDN-profiel en -eindpunt te maken.


## <a name="enable-additional-cdn-features"></a>Aanvullende CDN-functies inschakelen

Vanuit de pagina **Azure CDN** van het opslagaccount, selecteert u het CDN-eindpunt in de lijst om de configuratiepagina van het CDN-eindpunt te openen.

Op deze pagina kunt u extra CDN-functies inschakelen voor uw levering, zoals [Compressie](cdn-improve-performance.md), [Queryreeksen opslaan in cache](cdn-query-string.md) en [Geo-filteren](cdn-restrict-access-by-country.md). 
    
## <a name="enable-sas"></a>SAS inschakelen

Als u beperkte toegang wilt verlenen tot containers voor persoonlijke opslag, kunt u de functie Shared Access Signature (SAS) van uw Azure Storage account gebruiken. Een SAS is een URI die beperkte toegangsrechten verleent aan uw Azure Storage-resources zonder uw accountsleutel prijs te geven. Zie [Azure CDN gebruiken met SAS](cdn-sas-storage-support.md) voor meer informatie.

## <a name="access-cdn-content"></a>Toegang tot CDN-inhoud

Gebruik de CDN-URL op de portal om toegang te krijgen tot inhoud van de cache op het CDN. Het adres van een blob in de cache heeft de volgende indeling:

http://<*endpoint-name*\>. azureedge.net/<*myPublicContainer*\>/<*BLOB*\>

> [!NOTE]
> Nadat u ervoor hebt gezorgd dat Azure CDN toegang tot een opslagaccount heeft, komen alle openbaar beschikbare objecten in aanmerking om in de cache van CDN POP te worden geplaatst. Als u een object wijzigt dat zich momenteel in de cache op het CDN bevindt, is de nieuwe inhoud niet beschikbaar via Azure CDN totdat de inhoud van Azure CDN wordt vernieuwd na het verstrijken van de Time-to-Live-periode voor inhoud in de cache.

## <a name="remove-content-from-azure-cdn"></a>Inhoud verwijderen uit Azure CDN

Als u niet langer een object in de cache in Azure CDN nodig hebt, kunt u een van de volgende stappen uitvoeren:

- Maak de container persoonlijk in plaats van openbaar. Zie [anonieme lees toegang tot containers en blobs beheren](../storage/blobs/storage-manage-access-to-resources.md)voor meer informatie.
- Schakel het CDN-eindpunt uit of verwijder het met behulp van Azure Portal.
- Wijzig de gehoste service zodat deze niet meer reageert op aanvragen voor het object.

Een object dat al in de cache in Azure CDN is geplaatst, blijft in de cache totdat de Time-to-Live-periode voor het object is verstreken of totdat het eindpunt is [opgeschoond](cdn-purge-endpoint.md). Als de Time-to-Live-periode is verstreken, bepaalt Azure CDN of het CDN-eindpunt nog geldig is en het object nog steeds anoniem toegankelijk is. Als dat niet het geval is, wordt het object niet langer in de cache bewaard.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u een CDN-profiel en een eindpunt in een resourcegroep gemaakt. Sla deze resources op als u naar [Vervolgstappen](#next-steps) wilt gaan en informatie wilt krijgen over het toevoegen van een aangepast domein aan uw eindpunt. Als u echter denkt deze resources in de toekomst niet meer nodig te hebben, kunt u ze verwijderen door de resourcegroep te verwijderen. Op deze manier voorkomt u bijkomende kosten:

1. Selecteer **resource groepen** in het menu aan de linkerkant in het Azure Portal en selecteer vervolgens * CDNQuickstart-RG * *.

2. Selecteer op de pagina **resource groep** de optie **resource groep verwijderen**, Voer *CDNQuickstart-RG* in het tekstvak in en selecteer vervolgens **verwijderen**.

    Deze actie zal de resourcegroep, het profiel en het eindpunt dat u in deze snelstart hebt gemaakt verwijderen.

3. Als u uw opslagaccount wilt verwijderen, selecteert u dit in het dashboard en selecteert u vervolgens **Verwijderen** in het menu bovenaan.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure CDN profiel en eind punt maken](cdn-create-new-endpoint.md)

> [!div class="nextstepaction"]
> [Zelf studie: een CDN gebruiken om statische inhoud van een web-app te server](cdn-add-to-web-app.md)
