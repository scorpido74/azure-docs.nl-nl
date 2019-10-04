---
title: 'Zelfstudie: HTTPS configureren op een aangepast Azure CDN-domein | Microsoft Docs'
description: In deze zelfstudie leert u hoe u HTTPS kunt inschakelen en uitschakelen op het Azure CDN-eindpunt van een aangepast domein.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/1/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5506e4e4bb41725fd8791d3c6e47bb0b94584923
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959439"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Zelfstudie: HTTPS op een aangepast domein van Azure CDN configureren

In deze zelfstudie ziet u hoe u het HTTPS-protocol inschakelt voor een aangepast domein dat is gekoppeld aan een Azure CDN-eindpunt. Door het HTTPS-protocol te gebruiken in uw aangepaste domein, (bijvoorbeeld https:\//www.contoso.com) zorgt u ervoor dat uw gevoelige gegevens veilig worden afgeleverd via TLS/SSL-versleuteling wanneer deze via internet worden verzonden. Wanneer uw webbrowser is verbonden met een website via HTTPS, wordt het beveiligingscertificaat van de website gevalideerd en wordt er gecontroleerd of deze is uitgegeven door een legitieme certificeringsinstantie. Via dit proces zijn uw webtoepassingen beveiligd tegen aanvallen.

Azure CDN biedt standaard ondersteuning voor HTTPS voor een hostnaam van een CDN-eindpunt. Als u een CDN-eindpunt maakt (bijvoorbeeld https:\//contoso.azureedge.net), wordt HTTPS automatisch ingeschakeld.  

Enkele belangrijke kenmerken van de aangepaste HTTPS-functie zijn:

- Geen extra kosten: er worden geen kosten in rekening gebracht voor het verwerven of vernieuwen van certificaten, of voor HTTPS-verkeer. U betaalt alleen voor de uitgaande GB van het CDN.

- Eenvoudig inschakelen: inrichten met één klik is beschikbaar in de [Microsoft Azure-portal](https://portal.azure.com). U kunt ook REST API of andere hulpprogramma’s voor ontwikkelaars gebruiken om de functie in te schakelen.

- Volledig certificaatbeheer is beschikbaar: alle aanschaf en beheer van certificaten wordt voor u afgehandeld. Certificaten worden automatisch ingericht en vernieuwd vóór de verloopdatum. Hierdoor loopt u niet het risico dat de service wordt onderbroken omdat een certificaat is verlopen.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Het HTTPS-protocol inschakelen in uw aangepast domein
> - Een CDN-beheerd certificaat gebruiken 
> - Uw eigen certificaat gebruiken
> - Het domein valideren
> - Het HTTPS-protocol uitschakelen in uw aangepast domein.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Voordat u de stappen in deze zelfstudie kunt voltooien, moet u eerst een CDN-profiel en ten minste één CDN-eindpunt maken. Zie voor meer informatie [Snelstart: Een Azure CDN-profiel en een eindpunt maken](cdn-create-new-endpoint.md).

Daarnaast moet u een aangepast Azure CDN-domein koppelen aan uw CDN-eindpunt. Zie [Zelfstudie: Een aangepast domein toevoegen aan uw Azure CDN-eindpunt](cdn-map-content-to-custom-domain.md) 

> [!IMPORTANT]
> CDN-beheerde certificaten zijn niet beschikbaar voor root-of Apex-domeinen. Als uw Azure CDN aangepast domein een root-of Apex-domein is, moet u de functie uw eigen certificaat gebruiken. 
>

---

## <a name="ssl-certificates"></a>SSL-certificaten
Als u het HTTPS-protocol wilt inschakelen voor het veilig leveren van inhoud aan een aangepast Azure CDN-domein, moet u een SSL-certificaat gebruiken. U kunt ervoor kiezen om een certificaat te gebruiken dat wordt beheerd door Azure CDN of uw eigen certificaat gebruiken.


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificatetaboption-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Optie 1 (standaard): HTTPS met een door CDN beheerd certificaat inschakelen](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Wanneer u een door CDN beheerd certificaat gebruikt, kan de HTTPS-functie met een paar muisklikken worden ingeschakeld. Azure CDN verwerkt volledige certificaatbeheertaken, zoals aanschaf en vernieuwing. Zodra u de functie hebt ingeschakeld, wordt de procedure onmiddellijk gestart. Als het aangepaste domein al is toegewezen aan het CDN-eindpunt, is geen verdere actie vereist. Azure CDN verwerkt de stappen en voltooit uw aanvraag automatisch. Als uw aangepaste domein echter elders is toegewezen, moet u e-mail gebruiken om uw domeinbezit te valideren.

Volg deze stappen om HTTPS in te schakelen in een aangepast domein:

1. Ga in de [Microsoft Azure-portal](https://portal.azure.com) naar uw profiel in **Azure CDN Standard van Microsoft**, **Azure CDN Standard van Akamai**, **Azure CDN Standard van Verizon** of **Azure CDN Premium van Verizon**.

2. Selecteer in de lijst met CDN-eindpunten het eindpunt met het aangepaste domein.

    ![Lijst met eindpunten](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    De pagina **Eindpunt** wordt weergegeven.

3. Selecteer in de lijst met aangepaste domeinen het aangepaste domein waarvoor u HTTPS wilt inschakelen.

    ![Lijst met aangepaste domeinen](./media/cdn-custom-ssl/cdn-custom-domain.png)

    De pagina **Aangepast domein** wordt weergegeven.

4. Kies onder Certificaatbeheertype **CDN-beheerd** .

5. Selecteer **Aan** om HTTPS in te schakelen.

    ![HTTP-status voor aangepast domein](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

6. Ga door naar [Domein valideren](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificatetaboption-2-enable-https-with-your-own-certificate"></a>[Optie 2: HTTPS met uw eigen certificaat inschakelen](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Deze optie is alleen beschikbaar bij **Azure CDN van micro soft** en **Azure CDN van Verizon** -profielen. 
>
 
U kunt uw eigen certificaat gebruiken voor het inschakelen van de HTTPS-functie. Dit proces verloopt via een integratie met Azure Key Vault, waarmee u uw certificaten veilig kunt opslaan. Azure CDN maakt gebruik van dit beveiligde mechanisme om uw certificaat op te vragen en er zijn maar een paar extra stappen nodig. Wanneer u uw SSL-certificaat maakt, moet u deze maken met een toegestane certificeringsinstantie (CA). Als u een niet-toegestane CA gebruikt, wordt uw aanvraag geweigerd. Zie [toegestane certificerings instanties voor het inschakelen van aangepaste HTTPS op Azure CDN](cdn-troubleshoot-allowed-ca.md)voor een lijst met toegestane ca's. Voor **Azure CDN van Verizon**wordt elke geldige certificerings instantie geaccepteerd. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Voorbereiden van uw Azure Key Vault-account en -certificaat
 
1. Azure Key Vault: u moet een Azure Key Vault-account hebben onder hetzelfde abonnement als het Azure CDN-profiel en de CDN-eindpunten die u wilt inschakelen voor aangepaste HTTPS. Maak een Azure Key Vault-account, als u dit niet hebt.
 
2. Azure Key Vault-certificaten: als u al in het bezit bent van een certificaat, kunt u dit rechtstreeks uploaden naar uw Azure Key Vault-account of u kunt via Azure Key Vault direct een certificaat maken bij een van de partnercertificeringsinstanties waarmee Azure Key Vault is geïntegreerd. 

### <a name="register-azure-cdn"></a>Azure CDN registreren

Registreer Azure CDN als een app in Azure Active Directory via PowerShell.

1. Installeer zo nodig [Azure PowerShell](/powershell/azure/install-az-ps) op uw lokale computer.

2. Voer in PowerShell de volgende opdracht uit:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![Azure CDN registreren in PowerShell](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Azure CDN toegang verlenen tot uw sleutelkluis
 
Geef Azure CDN toegang tot de certificaten (geheimen) in uw Azure Key Vault-account.

1. Selecteer in uw Key Vault-account onder instellingen **Toegangsbeleid**, selecteer daarna **Nieuwe toevoegen** om een nieuw beleid te maken.

    ![Nieuw toegangsbeleid maken](./media/cdn-custom-ssl/cdn-new-access-policy.png)

2. Zoek in **Principal selecteren** naar **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** en kies **Microsoft.Azure.Cdn**. Klik op **Selecteren**.

    ![Instellingen voor toegangsbeleid](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

3. Selecteer **certificaat machtigingen**en schakel vervolgens de selectie vakjes voor **ophalen** en **lijst** in om CDN toe te staan deze machtigingen uit te voeren om de certificaten op te halen en weer te geven.

4. Selecteer **OK**. 

    Azure CDN heeft nu toegang tot deze sleutelkluis en de certificaten (geheimen) die in deze sleutelkluis zijn opgeslagen.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Het certificaat voor implementatie door Azure CDN selecteren
 
1. Ga terug naar de Azure CDN-portal en selecteer het profiel en CDN-eindpunt voor het inschakelen van aangepaste HTTPS. 

2. Selecteer in de lijst met aangepaste domeinen het aangepaste domein waarvoor u HTTPS wilt inschakelen.

    De pagina **Aangepast domein** wordt weergegeven.

3. Kies onder Certificaatbeheertype **Mijn eigen certificaat gebruiken**. 

    ![Uw certificaat configureren](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Selecteer een sleutelkluis, certificaat (geheim) en certificaatversie.

    Azure CDN noemt de volgende informatie: 
    - De sleutelkluis-accounts voor uw abonnement-ID. 
    - De certificaten (geheimen) onder de geselecteerde sleutelkluis. 
    - De beschikbare certificaatversies. 
 
5. Selecteer **Aan** om HTTPS in te schakelen.
  
6. Wanneer u uw eigen certificaat gebruikt, is domeinvalidatie niet nodig. Ga verder met [Wachten op doorgifte](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Het domein valideren

Als u al een aangepast domein gebruikt dat is toegewezen aan uw aangepaste eindpunt met een CNAME-record of als u een eigen certificaat gebruikt, gaat u verder met  
[Er is een aangepast domein toegewezen aan uw CDN-eindpunt](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). In andere gevallen, als de CNAME-record voor uw eindpunt niet meer bestaat of als de record het subdomein cdnverify bevat, gaat u verder met [Er is geen aangepast domein toegewezen aan uw CDN-eindpunt](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Er is een aangepast domein toegewezen aan uw CDN-eindpunt met een CNAME-record

Toen u een aangepast domein toevoegde aan uw eindpunt, hebt u een CNAME-record gemaakt in de DNS-tabel van de domeinregistrar om het domein toe te wijzen aan de hostnaam van het CDN-eindpunt. Als deze CNAME-records nog steeds bestaat en niet het subdomein cdnverify bevat, wordt het met de DigiCert-CA gebruikt om het eigendom van uw aangepaste domein automatisch te valideren. 

Als u uw eigen certificaat gebruikt, is domeinvalidatie niet nodig.

Uw CNAME-record moet de volgende indeling hebben, waarbij *Naam* de naam van het aangepaste domein is, en *Waarde* de hostnaam van het CDN-eindpunt:

| Naam            | Type  | Value                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Zie [Create the CNAME DNS record](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain) (De CNAME DNS-record maken) voor meer informatie over CNAME-records.

Als de CNAME-record de juiste indeling heeft, wordt de naam van het aangepaste domein automatisch geverifieerd met DigiCert en wordt er een toegewezen certificaat voor uw domeinnaam gemaakt. U ontvangt via DigiCert geen verificatie-e-mail en u hoeft uw aanvraag niet goed te keuren. Het certificaat is één jaar geldig en wordt, vóórdat het verloopt, automatisch vernieuwd. Ga verder met [Wachten op doorgifte](#wait-for-propagation). 

Automatische validatie neemt doorgaans enkele uren in beslag. Als uw domein niet in 24 uur is gevalideerd, opent u een ondersteunings ticket.

>[!NOTE]
>Als u beschikt over een CAA-record (Certificate Authority Authorization) bij uw DNS-provider, moet deze DigiCert bevatten als een geldige CA. Met een CAA-record kunnen domeineigenaars bij hun DNS-provider opgeven welke CA’s zijn geautoriseerd om certificaten te verlenen voor hun domein. Als een CA een bestelling ontvangt voor een certificaat voor een domein met een CAA-record, en deze CA wordt niet vermeld als een geautoriseerde verlener, mag de CA het certificaat niet verlenen aan dit domein of subdomein. Zie [CAA-records beheren](https://support.dnsimple.com/articles/manage-caa-record/) voor meer informatie over het beheren van CAA-records. Zie [CAA-record Helper](https://sslmate.com/caa/) voor een hulpprogramma voor CAA-records.

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>Er is geen aangepast domein toegewezen aan uw CDN-eindpunt

>[!NOTE]
>Als u **Azure CDN van Akamai**gebruikt, moet de volgende CNAME worden ingesteld om geautomatiseerde domein validatie in te scha kelen. "_acme-Challenge. &lt;custom domein hostnaam @ no__t-1-> CNAME-> &lt;custom Domain hostname&gt;.ak-acme-challenge.azureedge.net"

Als de CNAME-record vermelding het subdomein cdnverify bevat, volgt u de overige instructies in deze stap.

DigiCert stuurt een verificatie-e-mail naar de volgende e-mail adressen. Controleer of u rechtstreeks kunt goed keuren vanuit een van de volgende adressen:

admin@&lt;uw-domeinnaam.com&gt;  
administrator@&lt;uw-domeinnaam.com&gt;  
webmaster@&lt;uw-domeinnaam.com&gt;  
hostmaster@&lt;uw-domeinnaam.com&gt;  
postmaster@&lt;uw-domeinnaam.com&gt;  

U ontvangt binnen enkele minuten een e-mailbericht, vergelijkbaar met het bericht in het volgende voorbeeld, waarin u wordt gevraagd om de aanvraag goed te keuren. Als u een spamfilter gebruikt, voegt u verification@digicert.com toe aan de lijst met toegestane e-mailadressen. Als u na 24 uur nog geen e-mailbericht hebt ontvangen, neemt u contact op met Microsoft Ondersteuning.
    
![E-mailbericht voor domeinvalidatie](./media/cdn-custom-ssl/domain-validation-email.png)

Als u op de goedkeuringskoppeling klikt, wordt u naar het volgende onlinegoedkeuringsformulier geleid: 
    
![Formulier voor domeinvalidatie](./media/cdn-custom-ssl/domain-validation-form.png)

Volg de instructies op het formulier. U hebt twee verificatieopties:

- U kunt alle toekomstige bestellingen goedkeuren die met hetzelfde account zijn geplaatst voor hetzelfde hoofddomein, bijvoorbeeld contoso.com. Deze methode wordt aangeraden als u van plan bent om extra aangepaste domeinen toe te voegen voor hetzelfde hoofddomein.

- U kunt alleen de specifieke hostnaam goedkeuren die wordt gebruikt in deze aanvraag. Voor volgende aanvragen is extra goedkeuring is vereist.

Na goedkeuring wordt het certificaat voor de naam van het aangepaste domein met DigiCert voltooid. Het certificaat is één jaar geldig en wordt, vóórdat het verloopt, automatisch vernieuwd.

## <a name="wait-for-propagation"></a>Wachten op doorgifte

Nadat de domeinnaam is gevalideerd, duurt het maximaal 6 tot 8 uur voordat de HTTPS-functie van het aangepaste domein is geactiveerd. Wanneer het proces is voltooid, is de aangepaste HTTPS-status in Azure Portal ingesteld op **Ingeschakeld** en zijn de vier bewerkingsstappen in het dialoogvenster Aangepast domein gemarkeerd als Voltooid. Het aangepaste domein is nu klaar voor gebruik van HTTPS.

![Dialoogvenster HTTPS inschakelen](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Bewerkingsvoortgang

In de volgende tabel wordt de bewerkingsvoortgang weergegeven die plaatsvindt nadat u HTTPS hebt ingeschakeld. Als u HTTPS hebt ingeschakeld, worden vier bewerkingsstappen weergegeven in het dialoogvenster Aangepast domein. Telkens als een van deze stappen actief wordt, verschijnen, tijdens de uitvoering, extra substappen onder de stap. Niet al deze substappen worden uitgevoerd. Nadat een stap is voltooid, wordt naast deze stap een groen vinkje weergegeven. 

| Bewerkingsstap | Details van bewerkingssubstap | 
| --- | --- |
| 1 Aanvraag verzenden | Aanvraag verzenden |
| | De HTTPS-aanvraag wordt verzonden. |
| | De HTTPS-aanvraag is verzonden. |
| 2 Domeinvalidatie | Het domein wordt automatisch gevalideerd als de CNAME is toegewezen aan het CDN-eindpunt. In andere gevallen wordt een verificatieaanvraag verzonden naar het e-mailadres dat wordt vermeld in de registratierecord (WHOIS-registrator) van uw domein. Verifieer het domein zo snel mogelijk. |
| | Uw domeineigendom is gevalideerd. |
| | Validatieaanvraag voor eigendom van het domein is verlopen (de klant heeft waarschijnlijk niet binnen zes dagen gereageerd). HTTPS wordt niet ingeschakeld voor uw domein. * |
| | Validatieaanvraag voor eigendom van het domein is geweigerd door de klant. HTTPS wordt niet ingeschakeld voor uw domein. * |
| 3 Certificaat inrichten | De certificeringsinstantie verleent momenteel het certificaat dat nodig is om HTTPS in te schakelen in uw domein. |
| | Het certificaat is verleend en wordt momenteel geïmplementeerd in het CDN-netwerk. Dit duurt maximaal 6 uur. |
| | Het certificaat is geïmplementeerd in het CDN-netwerk. |
| 4 Voltooien | HTTPS is ingeschakeld in uw domein. |

\* Dit bericht wordt niet weergegeven tenzij er een fout is opgetreden. 

Als er een fout optreedt voordat de aanvraag is verzonden, wordt het volgende foutbericht weergegeven:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Resources opschonen - HTTPS uitschakelen

In de voorgaande stappen hebt u het HTTPS-protocol in uw aangepaste domein ingeschakeld. Als u het aangepaste domein niet meer wilt gebruiken met HTTPS, kunt u HTTPS uitschakelen door de volgende stappen uit te voeren:

### <a name="disable-the-https-feature"></a>De HTTPS-functie uitschakelen 

1. Ga in [Azure Portal](https://portal.azure.com) naar uw profiel in **Azure CDN Standard van Microsoft**, **Azure CDN Standard van Verizon** of **Azure CDN Premium van Verizon**.

2. Klik in de lijst met eindpunten op het eindpunt met het aangepaste domein.

3. Klik op het aangepaste domein waarvoor u HTTPS wilt uitschakelen.

    ![Lijst met aangepaste domeinen](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Klik op **Uit** om HTTPS uit te schakelen. Klik vervolgens op **Toepassen**.

    ![Dialoogvenster Aangepaste HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Wachten op doorgifte

Nadat de HTTPS-functie voor aangepaste domeinen is uitgeschakeld, duurt het maximaal 6 tot 8 uur voordat dit is doorgevoerd. Wanneer het proces is voltooid, is de aangepaste HTTPS-status in Azure Portal ingesteld op **Uitgeschakeld** en zijn de drie bewerkingsstappen in het dialoogvenster Aangepast domein gemarkeerd als Voltooid. Het aangepaste domein kan niet meer gebruikmaken van HTTPS.

![Dialoogvenster HTTPS uitschakelen](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Bewerkingsvoortgang

In de volgende tabel wordt de bewerkingsvoortgang weergegeven die plaatsvindt nadat u HTTPS hebt uitgeschakeld. Als u HTTPS hebt uitgeschakeld, worden drie bewerkingsstappen weergegeven in het dialoogvenster Aangepast domein. Telkens als een van deze stappen actief wordt, verschijnen extra gegevens onder de stap. Nadat een stap is voltooid, wordt naast deze stap een groen vinkje weergegeven. 

| Bewerkingsvoortgang | Bewerkingsdetails | 
| --- | --- |
| 1 Aanvraag verzenden | De aanvraag verzenden |
| 2 Inrichting van het certificaat ongedaan maken | Certificaat verwijderen |
| 3 Voltooien | Certificaat is verwijderd |

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

1. *Wie is de certificaatprovider en welk type certificaat is gebruikt?*

    Voor zowel **Azure CDN van Verizon** als **Azure CDN van Microsoft** wordt er voor uw aangepaste domein één door Digicert geleverd toegewezen certificaat gebruikt. 

2. *Gebruikt u IP of SNI TLS/SSL?*

    Zowel **Azure CDN van Verizon** als **Azure CDN Standard van Microsoft** gebruikt SNI TLS/SSL.

3. *Wat moet ik doen als ik geen verificatie-e-mail voor het domein heb ontvangen van DigiCert?*

    Als u beschikt over een CNAME-vermelding voor uw aangepaste domein die rechtstreeks verwijst naar de hostnaam van uw eindpunt (en u niet gebruikmaakt van de subdomeinnaam cdnverify), ontvangt u een dergelijke verificatie-e-mail niet. Validatie wordt dan automatisch uitgevoerd. In andere gevallen waarbij u geen CNAME-vermelding hebt en binnen 24 uur geen e-mail hebt ontvangen, neemt u contact op met Microsoft Ondersteuning.

4. *Is het gebruik van een SAN-certificaat minder veilig dan wanneer ik een toegewezen certificaat gebruik?*
    
    Voor een SAN-certificaat gelden dezelfde standaarden voor versleuteling en beveiliging als voor een toegewezen certificaat. Alle verleende SSL-certificaten maken gebruik van SHA-256 voor verbeterde serverbeveiliging.

5. *Heb ik een CAA-record nodig bij mijn DNS-provider?*

    Nee, een CAA-record is momenteel niet vereist. Als u er echter wel een hebt, moet deze DigiCert bevatten als een geldige CA.

6. *Vanaf 20 juni 2018 gebruikt Azure CDN van Verizon standaard een toegewezen certificaat met SNI TLS/SSL. Wat gebeurt er met mijn bestaande aangepaste domeinen die gebruik maken van een SAN-certificaat (Subject Alternative Names) en TLS/SSL op basis van IP?*

    Uw bestaande domeinen worden de komende maanden geleidelijk gemigreerd naar één certificaat, als Microsoft analyseert dat alleen SNI-clientaanvragen aan uw toepassing worden gericht. Als Microsoft detecteert dat er nog niet-SNI client-aanvragen aan uw toepassing worden gericht, behouden uw domeinen het SAN-certificaat met TLS/SSL op basis van IP. In elk geval is er geen onderbreking van uw service of ondersteuning van uw clientaanvragen, ongeacht of het SNI- of niet-SNI-aanvragen zijn.

7. *Hoe werkt het vernieuwen van certificaten met uw eigen certificaat?*

    Als u er zeker van wilt zijn dat een nieuwere certificaat wordt geïmplementeerd op de PoP-infra structuur, uploadt u uw nieuwe certificaat naar Azure-sleutel kluis en kiest u vervolgens in uw SSL-instellingen op Azure CDN de nieuwste certificaat versie en klikt u op opslaan. Azure CDN wordt vervolgens uw nieuwe bijgewerkte certificaat propogate. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> - Het HTTPS-protocol inschakelen in uw aangepast domein
> - Een CDN-beheerd certificaat gebruiken 
> - Uw eigen certificaat gebruiken
> - Het domein valideren
> - Het HTTPS-protocol uitschakelen in uw aangepast domein.

Ga naar de volgende zelfstudie voor meer informatie over het configureren van caching op het CDN-eindpunt.

> [!div class="nextstepaction"]
> [Zelfstudie: Azure CDN-regels voor opslaan in cache instellen](cdn-caching-rules-tutorial.md)

