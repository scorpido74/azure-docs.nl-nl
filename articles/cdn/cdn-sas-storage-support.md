---
title: Azure CDN gebruiken met SAS | Microsoft Documenten
description: Azure CDN ondersteunt het gebruik van Shared Access Signature (SAS) om beperkte toegang te verlenen tot privéopslagcontainers.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: c2580aa4ee22996c1bf0fe5c86064a6543450071
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260170"
---
# <a name="using-azure-cdn-with-sas"></a>Azure CDN gebruiken met SAS

Wanneer u een opslagaccount instelt voor Azure Content Delivery Network (CDN) om inhoud in de cache op te slaan, heeft iedereen die de URL's voor uw opslagcontainers kent standaard toegang tot de bestanden die u hebt geüpload. Om de bestanden in uw opslagaccount te beschermen, u de toegang van uw opslagcontainers instellen van openbaar naar privé. Als u dit doet, heeft echter niemand toegang tot uw bestanden. 

Als u beperkte toegang tot persoonlijke opslagcontainers wilt verlenen, kunt u de Shared Access Signature (SAS)-functie van uw Azure-opslagaccount gebruiken. Een SAS is een URI die beperkte toegangsrechten verleent aan uw Azure Storage-resources zonder uw accountsleutel prijs te geven. U een SAS verstrekken aan clients die u niet vertrouwt met uw opslagaccountsleutel, maar aan wie u toegang wilt delegeren tot bepaalde bronnen van opslagaccount. Door een uri voor gedeelde toegangshandtekeningen te distribueren naar deze clients, verleent u hen gedurende een bepaalde periode toegang tot een bron.
 
Met een SAS u verschillende parameters voor toegang tot een blob definiëren, zoals begin- en vervaldatums, machtigingen (lezen/schrijven) en IP-bereiken. In dit artikel wordt beschreven hoe u SAS gebruiken in combinatie met Azure CDN. Zie [SAS (Shared Access Signatures)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)gebruiken voor meer informatie over SAS, inclusief het maken ervan en de parameteropties.

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Azure CDN instellen voor gebruik met opslagSAS
De volgende drie opties worden aanbevolen voor het gebruik van SAS met Azure CDN. Alle opties gaan ervan uit dat u al een werkende SAS hebt gemaakt (zie vereisten). 
 
### <a name="prerequisites"></a>Vereisten
Als u wilt beginnen, maakt u een opslagaccount aan en genereert u vervolgens een SAS voor uw asset. U twee soorten opgeslagen toegangshandtekeningen genereren: een service SAS of een account SAS. Zie [Typen handtekeningen voor gedeelde toegang](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures)voor meer informatie .

Nadat u een SAS-token hebt gegenereerd, u `?sv=<SAS token>` toegang krijgen tot uw blob-opslagbestand door uw URL toe te voegen. Deze URL heeft de volgende indeling: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Bijvoorbeeld:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Zie [SAS-parameteroverwegingen](#sas-parameter-considerations) en parameters [voor handtekening van gedeelde toegang](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works)voor meer informatie over het instellen van parameters.

![CDN SAS-instellingen](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Optie 1: SAS gebruiken met doorvoer naar blob-opslag vanuit Azure CDN

Deze optie is de eenvoudigste en maakt gebruik van één SAS-token, dat wordt doorgegeven van Azure CDN naar de oorsprongsserver.
 
1. Selecteer een eindpunt, selecteer **Regels voor caching**en selecteer **Vervolgens Elke unieke URL in** cache in de lijst **Querytekenreeksen.**

    ![Regels voor CDN-caching](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Nadat u SAS hebt ingesteld op uw opslagaccount, moet u het SAS-token met de URL's van de CDN-eindpunt en herkomstserver gebruiken om toegang te krijgen tot het bestand. 
   
   De resulterende CDN-eindpunt-URL heeft de volgende indeling:`https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Bijvoorbeeld:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Stem de cacheduur af met cachingregels `Cache-Control` of door kopteksten toe te voegen aan de oorsprongsserver. Omdat Azure CDN het SAS-token als een eenvoudige querytekenreeks behandelt, moet u als aanbevolen procedures een cachingduur instellen die afloopt op of vóór de sas-vervaldatum. Als een bestand anders langer in de cache wordt opgeslagen dan de SAS actief is, kan het bestand toegankelijk zijn vanaf de Azure CDN-oorsprongsserver nadat de sas-vervaldatum is verstreken. Als deze situatie zich voordoet en u uw bestand in de cache ontoegankelijk wilt maken, moet u een zuiveringsbewerking in het bestand uitvoeren om het uit de cache te wissen. Zie [Azure CDN-cachinggedrag beheren met cachingregels voor](cdn-caching-rules.md)informatie over het instellen van de cacheduur op Azure CDN.

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Optie 2: Verborgen CDN SAS-token met behulp van een herschrijfregel
 
Deze optie is alleen beschikbaar voor **Azure CDN Premium van Verizon-profielen.** Met deze optie u de blob-opslag op de origin-server beveiligen. U deze optie gebruiken als u geen specifieke toegangsbeperkingen voor het bestand nodig hebt, maar wilt voorkomen dat gebruikers rechtstreeks toegang krijgen tot de opslagoorsprong om azure CDN-offload-tijden te verbeteren. Het SAS-token, dat onbekend is voor de gebruiker, is vereist voor iedereen die toegang heeft tot bestanden in de opgegeven container van de oorsprongsserver. Vanwege de URL-herschrijfregel is het SAS-token echter niet vereist op het CDN-eindpunt.
 
1. Gebruik de [regelsengine](cdn-rules-engine.md) om een URL-herschrijfregel te maken. Nieuwe regels duren maximaal 4 uur om te verspreiden.

   ![Knop CDN Beheren](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Knop CDN-regels](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   In de volgende voorbeeld-URL Herschrijven wordt een regelmatig expressiepatroon gebruikt met een vastleggende groep en een eindpunt met de naam *sasstoragedemo:*
   
   Bron:   
   `(container1\/.*)`
   
   Bestemming:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL Herschrijven](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![regel - links CDN URL Herschrijven regel - rechts](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Nadat de nieuwe regel actief is geworden, heeft iedereen toegang tot bestanden in de opgegeven container op het CDN-eindpunt, ongeacht of ze een SAS-token in de URL gebruiken. Hier is het formaat:`https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Bijvoorbeeld:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Stem de cacheduur af met cachingregels `Cache-Control` of door kopteksten toe te voegen aan de oorsprongsserver. Omdat Azure CDN het SAS-token als een eenvoudige querytekenreeks behandelt, moet u als aanbevolen procedures een cachingduur instellen die afloopt op of vóór de sas-vervaldatum. Als een bestand anders langer in de cache wordt opgeslagen dan de SAS actief is, kan het bestand toegankelijk zijn vanaf de Azure CDN-oorsprongsserver nadat de sas-vervaldatum is verstreken. Als deze situatie zich voordoet en u uw bestand in de cache ontoegankelijk wilt maken, moet u een zuiveringsbewerking in het bestand uitvoeren om het uit de cache te wissen. Zie [Azure CDN-cachinggedrag beheren met cachingregels voor](cdn-caching-rules.md)informatie over het instellen van de cacheduur op Azure CDN.

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Optie 3: CDN-beveiligingstokenverificatie gebruiken met een herschrijfregel

Als u Azure CDN-beveiligingstokenverificatie wilt gebruiken, moet u een **Azure CDN Premium hebben van het Verizon-profiel.** Deze optie is de meest veilige en aanpasbare. Clienttoegang is gebaseerd op de beveiligingsparameters die u op het beveiligingstoken instelt. Zodra u het beveiligingstoken hebt gemaakt en ingesteld, is dit vereist voor alle CDN-eindpunt-URL's. Vanwege de URL-herschrijfregel is het SAS-token echter niet vereist op het CDN-eindpunt. Als het SAS-token later ongeldig wordt, kan Azure CDN de inhoud van de oorspronkelijke server niet meer opnieuw valideren.

1. [Maak een Azure CDN-beveiligingstoken](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) en activeer het met behulp van de regelsengine voor het CDN-eindpunt en -pad waar uw gebruikers toegang hebben tot het bestand.

   Een URL voor eindpuntvan een beveiligingstoken heeft de volgende indeling:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Bijvoorbeeld:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   De parameteropties voor een verificatie van beveiligingstokens zijn anders dan de parameteropties voor een SAS-token. Als u ervoor kiest een vervaldatum te gebruiken wanneer u een beveiligingstoken maakt, moet u deze instellen op dezelfde waarde als de vervaldatum voor het SAS-token. Dit zorgt ervoor dat de verlooptijd voorspelbaar is. 
 
2. Gebruik de [regelsengine](cdn-rules-engine.md) om een URL-herschrijfregel te maken om SAS-tokentoegang tot alle blobs in de container in te schakelen. Nieuwe regels duren maximaal 4 uur om te verspreiden.

   In de volgende voorbeeld-URL Herschrijven wordt een regelmatig expressiepatroon gebruikt met een vastleggende groep en een eindpunt met de naam *sasstoragedemo:*
   
   Bron:   
   `(container1\/.*)`
   
   Bestemming:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL Herschrijven](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![regel - links CDN URL Herschrijven regel - rechts](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Als u de SAS verlengt, moet u ervoor zorgen dat u de regel Url Herschrijven bijwerkt met het nieuwe SAS-token. 

## <a name="sas-parameter-considerations"></a>Overwegingen voor SAS-parameter

Omdat SAS-parameters niet zichtbaar zijn voor Azure CDN, kan Azure CDN het leveringsgedrag niet wijzigen op basis daarvan. De gedefinieerde parameterbeperkingen zijn alleen van toepassing op aanvragen die Azure CDN aandoet op de oorsprongsserver, niet op aanvragen van de client naar Azure CDN. Dit onderscheid is belangrijk om rekening mee te houden wanneer u SAS-parameters instelt. Als deze geavanceerde mogelijkheden vereist zijn en u [optie 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)gebruikt, stelt u de juiste beperkingen in voor het Azure CDN-beveiligingstoken.

| SAS-parameternaam | Beschrijving |
| --- | --- |
| Starten | De tijd dat Azure CDN toegang krijgt tot het blobbestand. Als gevolg van klokscheefheid (wanneer een kloksignaal op verschillende tijdstippen voor verschillende componenten aankomt), kiest u een tijd 15 minuten eerder als u wilt dat het element onmiddellijk beschikbaar is. |
| Beëindigen | De tijd waarna Azure CDN geen toegang meer heeft tot het blobbestand. Eerder opgeslagen bestanden op Azure CDN zijn nog steeds toegankelijk. Als u de vervaldatum van het bestand wilt beheren, stelt u de juiste vervaldatum in op het Azure CDN-beveiligingstoken of wilt u het element verwijderen. |
| Toegestane IP-adressen | Optioneel. Als u **Azure CDN van Verizon**gebruikt, u deze parameter instellen op de bereiken die zijn gedefinieerd in [Azure CDN van verizon edge server-IP-bereiken.](/azure/cdn/cdn-pop-list-api) Als u **Azure CDN van Akamai**gebruikt, u de parameter IP-bereiken niet instellen omdat de IP-adressen niet statisch zijn.|
| Toegestane protocollen | Het protocol(s) stond toe voor een verzoek met de rekening SAS. De HTTPS-instelling wordt aanbevolen.|

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over SAS:
- [Shared Access Signatures (SAS) gebruiken](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Gedeelde toegangshandtekeningen, deel 2: Een SAS maken en gebruiken met Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Zie [Azure Content Delivery Network-assets beveiligen met tokenverificatie](https://docs.microsoft.com/azure/cdn/cdn-token-auth)voor meer informatie over het instellen van tokenverificatie.
