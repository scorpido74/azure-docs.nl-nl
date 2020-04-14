---
title: Zelfstudie - Een aangepast domein toevoegen aan uw Azure CDN-eindpunt | Microsoft Docs
description: In deze zelfstudie kunt u Azure CDN-eindpuntinhoud toewijzen aan een aangepast domein.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: ab4845a93771bace5bca15ea3afa7027884e36f4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260578"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Zelfstudie: Een aangepast domein toevoegen aan uw Azure CDN-eindpunt
Deze zelfstudie laat zien hoe u een aangepast domein toevoegt aan een Azure CDN-eindpunt (Content Delivery Network). Wanneer u een CDN-eindpunt gebruikt voor het leveren van inhoud, is een aangepast domein nodig als u wilt dat uw eigen domeinnaam zichtbaar is in de URL van uw CDN. Een zichtbare domeinnaam kan handig zijn voor uw klanten en nuttig zijn voor branding-doelen. 

Nadat u een CDN-eindpunt in uw profiel hebt gemaakt, is de naam van het eindpunt, namelijk een subdomein van azureedge.net, standaard opgenomen in de URL voor het leveren van CDN-inhoud (bijvoorbeeld https:\//contoso.azureedge.net/photo.png). Voor uw gemak biedt Azure CDN de mogelijkheid om een aangepast domein te koppelen met een CDN-eindpunt. Met deze optie levert u uw inhoud met een aangepast domein in uw URL in plaats van de naam van een eindpunt (bijvoorbeeld https:\//www.contoso.com/photo.png). 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een CNAME DNS-record maken.
> - Het aangepaste domein koppelen aan het CDN-eindpunt.
> - Het aangepaste domein verifiëren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u de stappen in deze zelfstudie kunt voltooien, moet u eerst een CDN-profiel en ten minste één CDN-eindpunt maken. Zie voor meer informatie [Snelstartgids: Een Azure CDN-profiel en een eindpunt maken](cdn-create-new-endpoint.md).

Als u nog geen aangepast domein hebt, moet u er eerst een aanschaffen bij een domeinprovider. Zie bijvoorbeeld [Een aangepaste domeinnaam kopen](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).

Als u van Azure gebruikmaakt voor het hosten van uw [DNS-domeinen](https://docs.microsoft.com/azure/dns/dns-overview), moet u het domeinnaam-systeem van de domeinprovider (DNS) naar een Azure DNS overdragen. Zie [Een domein delegeren aan Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)voor meer informatie. Als u van een domeinprovider gebruikmaakt voor het afhandelen van uw DNS-domein, gaat u verder met [Een DNS CNAME-record maken](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Een CNAME DNS-record maken

Voordat u een aangepast domein met een Azure CDN-eindpunt gebruiken kunt, moet u eerst een canonieke naam (CNAME)-record maken bij uw domeinprovider om te verwijzen naar uw CDN-eindpunt. Een CNAME-record is een soort DNS-record dat een brondomeinnaam toewijst aan een doeldomeinnaam. De domeinnaam van de bron is uw aangepaste domeinnaam voor Azure CDN en de domeinnaam van het doel is de hostnaam van uw CDN-eindpunt. Nadat Azure CDN de CNAME-record heeft geverifieerd die u maakt, wordt\.het verkeer dat is geadresseerd aan het aangepaste brondomein (zoals www contoso.com) doorgestuurd naar de opgegeven cdn-eindpunthostnaam (zoals contoso.azureedge.net). 

Een aangepast domein en het subdomein kunnen worden slechts aan één eindpunt tegelijk worden gekoppeld. U kunt echter verschillende subdomeinen uit het aangepaste domein gebruiken voor andere Azure-service-eindpunten met behulp van meerdere CNAME-records. U kunt ook een aangepast domein met verschillende subdomeinen toewijzen aan hetzelfde CDN-eindpunt.

> [!NOTE]
> Elk aliasrecordtype kan worden gebruikt voor aangepaste domeinen als u Azure DNS als domeinprovider gebruikt. Deze walkthrough maakt gebruik van het CNAME-recordtype. Als u A- of AAAA-recordtypen gebruikt, volgt u dezelfde stappen hieronder en vervangt u CNAME door het recordtype van uw keuze. Als u een aliasrecord gebruikt om een hoofddomein toe te voegen als een aangepast domein en u TLS wilt inschakelen, moet u handmatige validatie gebruiken zoals beschreven in [dit artikel.](https://docs.microsoft.com/azure/cdn/cdn-custom-ssl?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate#custom-domain-is-not-mapped-to-your-cdn-endpoint) Zie [Puntzone apex naar Azure CDN-eindpunten](https://docs.microsoft.com/azure/dns/dns-alias#point-zone-apex-to-azure-cdn-endpoints)voor meer informatie.

## <a name="map-the-temporary-cdnverify-subdomain"></a>Tijdelijk cdnverify subdomein toewijzen

Wanneer u een bestaand domein toewijst dat in productie is genomen, zijn er speciale overwegingen. Terwijl u uw aangepaste domein in het Azure Portal registreert, kan een korte periode van uitvaltijd voor het domein optreden. Om te voorkomen dat het webverkeer wordt onderbroken, moet u eerst uw aangepaste domein toewijzen aan de hostnaam van uw CDN-eindpunt met het subdomein Azure cdnverify, om een tijdelijke CNAME-toewijzing te maken. Met deze methode kunnen gebruikers zonder onderbreking toegang krijgen tot uw domein terwijl de DNS-toewijzing wordt uitgevoerd. 

U kunt ook uw aangepaste domein rechtstreeks aan uw CDN-eindpunt toewijzen, als u uw aangepaste domein voor het eerst gebruikt en er geen productieverkeer op wordt uitgevoerd. Ga door naar [Permanent aangepast domein toewijzen](#map-the-permanent-custom-domain).

Om een CNAME-record te maken met het subdomein cdnverify:

1. Meld u aan bij de website van de domeinprovider voor uw aangepaste domein.

2. Zoek de pagina voor het beheren van DNS-records door de documentatie van de provider te raadplegen, of te zoeken naar gebieden van de website met het label **Domeinnaam**, **DNS**, of **Serverbeheernaam**. 

3. Maak een CNAME-record vermelding voor uw aangepaste domein en vul de velden in, zoals wordt weergegeven in de volgende tabel (veldnamen kunnen verschillen):

    | Bron                    | Type  | Doel                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Bron: Voer uw aangepaste domeinnaam, inclusief het subdomein cdnverify, in de volgende indeling in: cdnverify. &lt;aangepaste domeinnaam&gt;. Bijvoorbeeld: cdnverify.www.contoso.com.

    - Type: Voer *CNAME* in.

    - Bestemming: Voer uw CDN-eindpunthostnaam, inclusief het subdomein cdnverify, in de volgende indeling in: cdnverify. _eindpuntnaam&gt; &lt;_ Bijvoorbeeld: cdnverify.contoso.azureedge.net.

4. Sla uw wijzigingen op.

Bijvoorbeeld: de procedure voor de registrar van een GoDaddy-domein is als volgt:

1. Meld u aan en selecteer het aangepaste domein dat u wilt gebruiken.

2. Selecteer in de sectie Domeinen **Alle beheren**, selecteer daarna **DNS** | **Zones beheren**.

3. Voer bij **Domeinnaam** uw aangepaste domein in en selecteer **Zoeken**.

4. Selecteer op de pagina **DNS-beheer****toevoegen**, selecteer daarna **CNAME** in de **Type**-lijst.

5. Vul de volgende velden van het CNAME-item in:

    ![CNAME-vermelding](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Type: Laat *CNAME* geselecteerd.

    - Host: Voer het subdomein in van uw aangepaste domein dat moet worden gebruikt, met inbegrip van de naam van het cdnverify-subdomein. Bijvoorbeeld: cdnverify.www.

    - Verwijst naar: Voer de hostnaam van uw CDN-eindpunt in, inclusief de naam van het subdomein cdnverify. Bijvoorbeeld: cdnverify.contoso.azureedge.net. 

    - TTL: Laat *1 uur* geselecteerd.

6. Selecteer **Opslaan**.
 
    De CNAME-vermelding wordt toegevoegd aan de tabel DNS-records.

    ![Tabel DNS-records](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Het aangepaste domein koppelen aan uw CDN-eindpunt

Nadat u uw aangepaste domein hebt geregistreerd, kunt u dit toevoegen aan uw CDN-eindpunt. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) en blader naar het CDN-profiel met het eindpunt dat u wilt toewijzen aan een aangepast domein.
    
2. Op de **CDN-profiel**pagina, selecteert u het CDN-eindpunt dat u wilt koppelen aan het aangepaste domein.

   De **Eindpunt**-pagina wordt geopend.
    
3. Selecteer **Aangepast domein**. 

   ![Knop Aangepast CDN-domein](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   De pagina **Een aangepast domein toevoegen** wordt geopend.

4. Bij **Hostnaam van het eindpunt** wordt automatisch de naam ingevuld van de host die wordt gebruikt als het doeldomein van uw CNAME-record. Deze naam wordt afgeleid van de URL van uw CDN-eindpunt: *&lt;hostnaam van eindpunt&gt;*. azureedge.net. De naam kan niet worden gewijzigd.

5. Bij **Aangepaste hostnaam** voert u de naam van uw aangepaste domein in, inclusief het subdomein, om dit te gebruiken als het brondomein van uw CNAME-record. Bijvoorbeeld www\.contoso.com of cdn.contoso.com. Gebruik niet de naam van het cdnverify-subdomein.

   ![Dialoog Aangepast CDN-domein](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. Selecteer **Toevoegen**.

   Azure controleert of het CNAME-record bestaat dat u voor de domeinnaam hebt ingevoerd. Als de CNAME juist is, wordt uw aangepaste domein gevalideerd. 

   Het kan even duren voordat de nieuwe instellingen van het aangepaste domein zijn doorgegeven aan alle CDN-edge-knooppunten: 
    - Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
    - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
    - Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden normaal gesproken binnen 10 minuten doorgegeven.   


## <a name="verify-the-custom-domain"></a>Het aangepaste domein verifiëren

Nadat u de registratie van uw aangepaste domein hebt voltooid, controleert u of het aangepaste domein verwijst naar uw CDN-eindpunt.
 
1. Zorg ervoor dat u op het eindpunt openbare inhoud hebt die in cache is opgeslagen. Bijvoorbeeld, als uw CDN-eindpunt is gekoppeld aan een opslagaccount, zal Azure CDN de inhoud in cache opslaan in een openbare container. Controleer of de container is ingesteld voor openbare toegang en ten minste één bestand bevat om het aangepaste domein te testen.

2. Ga naar het adres van het bestand met behulp van het aangepaste domein in uw browser. Als uw aangepaste domein `www.contoso.com`bijvoorbeeld is, moet de URL naar het bestand `http://www.contoso.com/my-public-container/my-file.jpg`in de cache vergelijkbaar zijn met de volgende URL: . Controleer of het resultaat hetzelfde is als wanneer u het CDN-eindpunt rechtstreeks op * &lt;endpointhostname&gt;*.azureedge.net.


## <a name="map-the-permanent-custom-domain"></a>Permanent aangepast domein toewijzen

Als u hebt gecontroleerd dat het subdomein cdnverify met succes is toegewezen aan het eindpunt (of als u een nieuwe aangepast gebruikt domein dat niet in productie is), kunt u vervolgens het aangepaste domein rechtstreeks toewijzen aan de hostnaam van uw CDN-eindpunt.

Maken van een CNAME-record voor uw aangepaste domein:

1. Meld u aan bij de website van de domeinprovider voor uw aangepaste domein.

2. Zoek de pagina voor het beheren van DNS-records door de documentatie van de provider te raadplegen of te zoeken naar gebieden met het label **Domain Name,** **DNS**of Name **Server Management**. 

3. Maak een CNAME-record vermelding voor uw aangepaste domein en vul de velden in, zoals wordt weergegeven in de volgende tabel (veldnamen kunnen verschillen):

    | Bron          | Type  | Doel           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azureedge.net |

   - Bron: Voer uw aangepaste domeinnaam in\.(bijvoorbeeld www contoso.com).

   - Type: Voer *CNAME* in.

   - Bestemming: Voer de hostnaam in van uw CDN-eindpunt. Deze moet in de volgende indeling staan:_&lt;eindpuntnaam&gt;_.azureedge.net. Bijvoorbeeld: contoso.azureedge.net.

4. Sla uw wijzigingen op.

5. Als u eerder een tijdelijk cdnverify-subdomein CNAME-record hebt gemaakt, moet u dit verwijderen. 

6. Als u dit aangepaste domein voor het eerst in de productieomgeving gebruikt, volg dan de stappen voor [Het aangepaste domein koppelen aan uw CDN-eindpunt](#associate-the-custom-domain-with-your-cdn-endpoint) en [Het aangepaste domein verifiëren](#verify-the-custom-domain).

Bijvoorbeeld: de procedure voor de registrar van een GoDaddy-domein is als volgt:

1. Meld u aan en selecteer het aangepaste domein dat u wilt gebruiken.

2. Selecteer in de sectie Domeinen **Alle beheren**, selecteer daarna **DNS** | **Zones beheren**.

3. Voer bij **Domeinnaam** uw aangepaste domein in en selecteer **Zoeken**.

4. Selecteer op de pagina **DNS-beheer****toevoegen**, selecteer daarna **CNAME** in de **Type**-lijst.

5. Vul de volgende velden van de CNAME-vermelding in:

    ![CNAME-vermelding](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Type: Laat *CNAME* geselecteerd.

    - Host: Voer het subdomein van uw aangepaste domein in om dit te gebruiken. Bijvoorbeeld, www of cdn.

    - Verwijst naar: Voer de hostnaam in van uw CDN-eindpunt. Bijvoorbeeld: contoso.azureedge.net. 

    - TTL: Laat *1 uur* geselecteerd.

6. Selecteer **Opslaan**.
 
    De CNAME-vermelding wordt toegevoegd aan de tabel DNS-records.

    ![Tabel DNS-records](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Als u een cdnverify CNAME-record hebt, selecteer dan het potloodpictogram ernaast en daarna het prullenbak-pictogram.

8. Selecteer **Verwijderen** om het CNAME-record te verwijderen.


## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u een aangepast domein toegevoegd aan een CDN-eindpunt. Als u uw eindpunt niet langer met een aangepast domein wilt koppelen, kunt u het aangepaste domein verwijderen door deze stappen uit te voeren:
 
1. Selecteer het eindpunt met het aangepaste domein dat u wilt verwijderen in uw CDN-profiel.

2. Klik vanuit de **Eindpunt**-pagina onder Aangepaste domeinen met de rechtermuisknop op het aangepaste domein dat u wilt verwijderen en selecteer vervolgens **Verwijderen** in het contextmenu.  

   Het aangepaste domein is ontkoppeld van het eindpunt.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> - Een CNAME DNS-record maken.
> - Het aangepaste domein koppelen aan het CDN-eindpunt.
> - Het aangepaste domein verifiëren.

Ga naar de volgende zelfstudie voor meer informatie over hoe u HTTPS configureert op een aangepast Azure CDN-domein.

> [!div class="nextstepaction"]
> [Zelfstudie: HTTPS configureren op een aangepast Azure CDN-domein](cdn-custom-ssl.md)


