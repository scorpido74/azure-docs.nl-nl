---
title: Een statische website integreren met Azure CDN - Azure Storage
description: Meer informatie over het cacheen van statische website-inhoud van een Azure Storage-account met behulp van Azure Content Delivery Network (CDN).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 01/22/2020
ms.openlocfilehash: 8eeff5187d27cb75b9e55eba8311dede8970bc4a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435219"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Een statische website integreren met Azure CDN

U [CDN (Azure Content Delivery Network)](../../cdn/cdn-overview.md) inschakelen om inhoud in de cache op te slaan van een [statische website](storage-blob-static-website.md) die wordt gehost in een Azure-opslagaccount. U Azure CDN gebruiken om het aangepaste domeineindpunt voor uw statische website te configureren, aangepaste TLS/SSL-certificaten in te richten en aangepaste herschrijfregels te configureren. Het configureren van Azure CDN resulteert in extra kosten, maar biedt consistente lage latenties naar uw website vanaf elke locatie ter wereld. Azure CDN biedt ook TLS-versleuteling met uw eigen certificaat. 

Zie [Azure CDN-prijzen](https://azure.microsoft.com/pricing/details/cdn/) voor meer informatie over prijzen voor Azure CDN.

## <a name="enable-azure-cdn-for-your-static-website"></a>Azure CDN inschakelen voor uw statische website

U Azure CDN rechtstreeks vanuit uw opslagaccount inschakelen voor uw statische website. Als u voor uw CDN-eindpunt geavanceerde configuratie-instellingen wilt opgeven, zoals [ optimalisatie voor het downloaden van grote bestanden](../../cdn/cdn-optimization-overview.md#large-file-download), kunt u in plaats daarvan de [Azure CDN-extensie](../../cdn/cdn-create-new-endpoint.md) gebruiken om een CDN-profiel en -eindpunt te maken.

1. Zoek uw opslagaccount in de Azure-portal en geef het accountoverzicht weer.

2. Selecteer **Azure CDN** onder het menu **Blob-Service** om Azure CDN te configureren.

    De pagina **Azure CDN** wordt weergegeven.

    ![Een CDN-eindpunt maken](../../cdn/media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)

3. Geef in de sectie **CDN-profiel** een nieuw of bestaand CDN-profiel op. 

4. Geef een prijscategorie op voor het CDN-eindpunt. Zie Prijzen van [het Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/)voor meer informatie over prijzen. Zie [Azure CDN-productfuncties vergelijken](../../cdn/cdn-features.md)voor meer informatie over de functies die beschikbaar zijn voor elke laag.

5. Geef in het veld **CDN-eindpuntnaam** een naam op voor uw CDN-eindpunt. Het CDN-eindpunt moet uniek zijn in Azure.

6. Geef op dat je het statische websiteeindpunt bent in het veld **Origin-hostname.** 

   Als u het statische eindpunt van de website wilt vinden, navigeert u naar de **statische website-instellingen** voor uw opslagaccount.  Kopieer het primaire eindpunt en plak het in de CDN-configuratie.

   > [!IMPORTANT]
   > Zorg ervoor dat u de protocol-id *(bijvoorbeeld*HTTPS) en de trailing slash in de URL verwijdert. Als het statische eindpunt van `https://mystorageaccount.z5.web.core.windows.net/`de website bijvoorbeeld `mystorageaccount.z5.web.core.windows.net` is, geeft u dit op in het veld **Oorspronkelijke hostnaam.**

   In de volgende afbeelding ziet u een voorbeeldvan eindpuntconfiguratie:

   ![Schermafbeelding van voorbeeld-CDN-eindpuntconfiguratie](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

7. Selecteer **Maken**en wacht tot deze wordt doorgegeven. Zodra het eindpunt is gemaakt, wordt dit weergegeven in de lijst met eindpunten.

8. Als u wilt controleren of het CDN-eindpunt correct is geconfigureerd, klikt u op het eindpunt om naar de instellingen te navigeren. Zoek in het CDN-overzicht voor uw opslagaccount de hostnaam van eindpunten en navigeer naar het eindpunt, zoals in de volgende afbeelding wordt weergegeven. Het formaat van uw CDN-eindpunt `https://staticwebsitesamples.azureedge.net`is vergelijkbaar met .

    ![Schermafbeelding van het overzicht van CDN-eindpunt](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

9. Zodra de doorgifte van het CDN-eindpunt is voltooid, wordt door door te navigeren naar het CDN-eindpunt de inhoud van het bestand index.html weergegeven dat u eerder naar uw statische website hebt geüpload.

10. Als u de oorsprongsinstellingen voor uw CDN-eindpunt wilt controleren, navigeert u naar **Origin** onder de sectie **Instellingen** voor uw CDN-eindpunt. U ziet dat het veld **Origin-type** is ingesteld op *Aangepaste oorsprong* en dat in het veld **Oorspronkelijke hostname** het statische eindpunt van de website wordt weergegeven.

    ![Schermafbeelding van oorsprongsinstellingen voor CDN-eindpunt](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Inhoud verwijderen uit Azure CDN

Als u niet langer een object in de cache in Azure CDN nodig hebt, kunt u een van de volgende stappen uitvoeren:

* Maak de container persoonlijk in plaats van openbaar. Zie [Anonieme leestoegang tot containers en blobs beheren](storage-manage-access-to-resources.md)voor meer informatie.
* Schakel het CDN-eindpunt uit of verwijder het met behulp van Azure Portal.
* Wijzig de gehoste service zodat deze niet meer reageert op aanvragen voor het object.

Een object dat al in de cache in Azure CDN is geplaatst, blijft in de cache totdat de Time-to-Live-periode voor het object is verstreken of totdat het eindpunt is [opgeschoond](../../cdn/cdn-purge-endpoint.md). Als de Time-to-Live-periode is verstreken, bepaalt Azure CDN of het CDN-eindpunt nog geldig is en het object nog steeds anoniem toegankelijk is. Als dat niet het geval is, wordt het object niet langer in de cache bewaard.

## <a name="next-steps"></a>Volgende stappen

(Optioneel) Voeg een aangepast domein toe aan uw Azure CDN-eindpunt. Zie [Zelfstudie: Voeg een aangepast domein toe aan uw Azure CDN-eindpunt.](../../cdn/cdn-map-content-to-custom-domain.md)
