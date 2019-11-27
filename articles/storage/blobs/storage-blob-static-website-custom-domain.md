---
title: 'Zelf studie: een aangepast domein & SSL inschakelen voor een statische website in azure'
description: Leer hoe u een aangepast domein configureert voor het hosten van statische websites.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 893ac53dc9f0b6b162c5ec22e478cd15706e50fb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327499"
---
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>Zelf studie: een aangepast domein & SSL inschakelen voor een statische website in azure

Deze zelfstudie is deel twee van een serie. Hier leert u om een aangepast domein met SSL voor uw statische website in te schakelen. 

De zelfstudie laat zien hoe u met [Azure CDN](../../cdn/cdn-overview.md) het eindpunt van het aangepaste domein kunt configureren voor uw statische website. Met Azure CDN kunt u aangepaste SSL-certificaten inrichten, een aangepast domein gebruiken en aangepaste herschrijvingsregels configureren, en dat alles op hetzelfde moment. Het configureren van Azure CDN resulteert in extra kosten, maar biedt consistente lage latenties naar uw website vanaf elke locatie ter wereld. Azure CDN biedt ook SSL-versleuteling met uw eigen certificaat. Zie [Azure CDN-prijzen](https://azure.microsoft.com/pricing/details/cdn/) voor meer informatie over prijzen voor Azure CDN.

In deel twee van de serie leert u het volgende:

> [!div class="checklist"]
> * Een CDN-eindpunt op het eindpunt van een statische website maken
> * Aangepast domein en SSL inschakelen

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u deel één volt ooien, [zelf studie: host een statische website op Blob Storage](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/) om aan de slag te gaan.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Een CDN-eindpunt op het eindpunt van een statische website maken

1. Zoek uw opslag account in de Azure Portal en geef het account overzicht weer.
1. Selecteer **Azure CDN** onder het menu **Blob-Service** om Azure CDN te configureren.
1. Geef in de sectie **CDN-profiel** een nieuw of bestaand CDN-profiel op. Zie voor meer informatie [Snelstartgids: Een Azure CDN-profiel en een eindpunt maken](../../cdn/cdn-create-new-endpoint.md).
1. Geef een prijs categorie op voor het CDN-eind punt. In deze zelf studie wordt gebruikgemaakt van de standaard prijs categorie **Akamai** , omdat deze snel wordt door gegeven, meestal binnen een paar minuten. Andere prijs categorieën kunnen langer duren voordat ze worden door gegeven, maar kunnen ook andere voor delen bieden. Zie [Azure CDN product functies vergelijken](../../cdn/cdn-features.md)voor meer informatie.
1. Geef in het veld **naam van CDN-eind punt** een naam op voor uw CDN-eind punt. Het CDN-eind punt moet uniek zijn binnen Azure.
1. Geef op dat u het eind punt van een statische website bent in het veld **hostnaam van oorsprong** . Om het eind punt van een statische website te vinden, gaat u naar de **statische website** -instellingen voor uw opslag account. Kopieer het primaire eind punt en plak het in de CDN-configuratie, waarbij u de protocol-id (*bijvoorbeeld*https) verwijdert.

    In de volgende afbeelding ziet u een voor beeld van een eindpunt configuratie:

    ![Scherm opname van voor beeld van CDN-eindpunt configuratie](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Maak het CDN-eind punt en wacht totdat het is door gegeven.
1. Als u wilt controleren of het CDN-eind punt correct is geconfigureerd, klikt u op het eind punt om naar de instellingen te navigeren. Zoek in het CDN-overzicht voor uw opslag account de hostnaam van het eind punt en ga naar het eind punt, zoals wordt weer gegeven in de volgende afbeelding. De indeling van uw CDN-eind punt is vergelijkbaar met `https://staticwebsitesamples.azureedge.net`.

    ![Scherm afbeelding met overzicht van het CDN-eind punt](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Zodra de doorgifte van het CDN-eind punt is voltooid, wordt in de navigatie naar het CDN-eind punt de inhoud weer gegeven van het bestand index. html dat u eerder hebt geüpload naar uw statische website.

1. Als u de oorspronkelijke instellingen voor het CDN-eind punt wilt controleren, gaat u naar **oorsprong** in het gedeelte **instellingen** van het CDN-eind punt. U ziet dat het veld van het **type oorsprong** is ingesteld op *aangepaste oorsprong* en dat het veld **hostnaam van oorsprong** het eind punt van de statische website weergeeft.

    ![Scherm opname met de oorspronkelijke instellingen voor het CDN-eind punt](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Aangepast domein en SSL inschakelen

1. Maak een CNAME-record bij uw domeinnaamprovider als u een aangepast domein naar het CDN-eindpunt wilt omleiden. De CNAME-record voor het *www*subdomein moeten vergelijkbaar zijn met het volgende:

    ![CNAME-record voor het www-subdomein opgeven](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Geef in het Azure Portal de instellingen voor uw CDN-eind punt weer. Navigeer naar **aangepaste domeinen** onder **instellingen** om het aangepaste domein en het SSL-certificaat te configureren.
1. Selecteer **Aangepast domein toevoegen**, voer uw domeinnaam in en klik op **Toevoegen**.
1. Selecteer de nieuwe aangepaste domein toewijzing om een SSL-certificaat in te richten.
1. Stel het **aangepaste domein https** in **op**aan en klik vervolgens op **Opslaan**. Het kan enkele uren duren om uw aangepaste domein te configureren. De portal geeft de voortgang weer, zoals wordt weer gegeven in de volgende afbeelding.

    ![Scherm opname van de voortgang van de aangepaste domein configuratie](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Test de toewijzing van uw statische website aan uw aangepaste domein door de URL voor uw aangepaste domein te openen.

Zie [zelf studie: https configureren op een Azure CDN aangepast domein](../../cdn/cdn-custom-ssl.md)voor meer informatie over het inschakelen van HTTPS voor aangepaste domeinen.

## <a name="next-steps"></a>Volgende stappen

In deel twee van deze zelfstudie hebt u geleerd hoe u een aangepast domein kunt configureren met SSL in Azure CDN voor uw statische website.

Zie [Wat is Azure CDN?](../../cdn/cdn-overview.md)voor meer informatie over het configureren en gebruiken van Azure CDN.