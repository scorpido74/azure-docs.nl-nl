---
title: Azure-web-apps met laadbalans hosten op de zone-top
description: Een Azure DNS-aliasrecord gebruiken om geladen gebalanceerde web-apps te hosten op de zonetop
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 8ba96a028d51e6e5503bb4a8e6735b48033c9ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937370"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Azure-web-apps met laadbalans hosten op de zone-top

Het DNS-protocol voorkomt dat de toewijzing van iets anders dan een A- of AAAA-record op de top van de zone wordt uitgevoerd. Een voorbeeld zone apex is contoso.com. Deze beperking vormt een probleem voor toepassingseigenaren die load-balanced applicaties achter Traffic Manager hebben. Het is niet mogelijk om het Traffic Manager-profiel aan te wijzen vanuit de zone apex record. Als gevolg hiervan moeten eigenaren van toepassingen een tijdelijke oplossing gebruiken. Een omleiding op de toepassingslaag moet worden omgeleid van de zonetop naar een ander domein. Een voorbeeld is een omleiding\.van contoso.com naar www contoso.com. Deze regeling presenteert één storingspunt voor de omleidingsfunctie.

Met aliasrecords bestaat dit probleem niet meer. Nu kunnen toepassingseigenaren hun zone-toprecord aanwijzen op een Traffic Manager-profiel met externe eindpunten. Toepassingseigenaren kunnen hetzelfde Traffic Manager-profiel aanwijzen dat wordt gebruikt voor een ander domein binnen hun DNS-zone.

Contoso.com en www\.contoso.com kunnen bijvoorbeeld hetzelfde Traffic Manager-profiel aanwijzen. Dit is het geval zolang het profiel Traffic Manager alleen externe eindpunten heeft geconfigureerd.

In dit artikel leert u hoe u een aliasrecord maakt voor de top van uw domein en uw eindpunten voor het Traffic Manager-profiel configureert voor uw web-apps.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet een beschikbare domeinnaam hebben die u in Azure DNS kunt hosten om te testen. U moet het volledige beheer over dit domein hebben. Volledig beheer betekent ook de mogelijkheid om naamserverrecords (NS) voor het domein in te stellen.

Zie voor instructies voor het hosten van uw domein in Azure DNS [Zelfstudie: Uw domein in Azure DNS hosten](dns-delegate-domain-azure-dns.md).

Het voorbeelddomein dat wordt gebruikt voor deze zelfstudie is contoso.com, maar u moet uw eigen domeinnaam gebruiken.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep om alle resources in dit artikel te behouden.

## <a name="create-app-service-plans"></a>App-serviceplannen maken

Maak twee Web App Service-abonnementen in uw resourcegroep met de volgende tabel voor configuratiegegevens. Zie Een App Service-abonnement beheren in Azure voor meer informatie over het maken van een App [Service-abonnement.](../app-service/app-service-plan-manage.md)


|Name  |Besturingssysteem  |Locatie  |Prijscategorie  |
|---------|---------|---------|---------|
|ASP-01     |Windows|VS - oost|Dev/Test D1-Gedeeld|
|ASP-02     |Windows|VS - centraal|Dev/Test D1-Gedeeld|

## <a name="create-app-services"></a>App-services maken

Maak twee web-apps, één in elk App Service-abonnement.

1. Selecteer linksboven op de Azure-portalpagina De optie **Een bron maken**.
2. Typ **web-app** in de zoekbalk en druk op Enter.
3. Selecteer **Web-app**.
4. Selecteer **Maken**.
5. Accepteer de standaardinstellingen en gebruik de volgende tabel om de twee web-apps te configureren:

   |Name<br>(moet uniek zijn binnen .azurewebsites.net)|Resourcegroep |Runtimestack|Regio|App-serviceplan/locatie
   |---------|---------|-|-|-------|
   |App-01|Bestaande gebruiken<br>Uw resourcegroep selecteren|.NET Core 2.2|VS - oost|ASP-01(D1)|
   |App-02|Bestaande gebruiken<br>Uw resourcegroep selecteren|.NET Core 2.2|VS - centraal|ASP-02(D1)|

### <a name="gather-some-details"></a>Verzamel enkele details

Nu moet u de IP-adres en hostnaam voor de web-apps noteren.

1. Open uw brongroep en selecteer uw eerste web-app **(App-01** in dit voorbeeld).
2. Selecteer **Eigenschappen**in de linkerkolom .
3. Let op het adres onder **URL**en noteer onder **Uitgaande IP-adressen** het eerste IP-adres in de lijst. U gebruikt deze informatie later wanneer u de eindpunten van Traffic Manager configureert.
4. Herhaal dit voor **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager-profiel in uw resourcegroep. Gebruik de standaardinstellingen en typ een unieke naam in de trafficmanager.net naamruimte.

Zie [Snelstart: Een Traffic Manager-profiel maken voor een zeer beschikbare webapplicatie voor](../traffic-manager/quickstart-create-traffic-manager-profile.md)informatie over het maken van een Traffic Manager-profiel.

### <a name="create-endpoints"></a>Eindpunten maken

Nu u de eindpunten voor de twee web-apps maken.

1. Open uw resourcegroep en selecteer uw Traffic Manager-profiel.
2. Selecteer **Eindpunten in**de linkerkolom .
3. Selecteer **Toevoegen**.
4. Gebruik de volgende tabel om de eindpunten te configureren:

   |Type  |Name  |Doel  |Locatie  |Aangepaste header-instellingen|
   |---------|---------|---------|---------|---------|
   |Extern eindpunt     |Einde-01|IP-adres dat u hebt geregistreerd voor App-01|VS - oost|host:\<de URL die u hebt opgenomen voor App-01\><br>Voorbeeld: **host:app-01.azurewebsites.net**|
   |Extern eindpunt     |Einde-02|IP-adres dat u hebt geregistreerd voor App-02|VS - centraal|host:\<de URL die u hebt opgenomen voor App-02\><br>Voorbeeld: **host:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>DNS-zone maken

U een bestaande DNS-zone gebruiken om te testen, of u een nieuwe zone maken. Zie [Zelfstudie: Uw domein hosten in Azure DNS](dns-delegate-domain-azure-dns.md)als u een nieuwe DNS-zone in Azure wilt maken en delegeren.

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Een TXT-record toevoegen voor aangepaste domeinvalidatie

Wanneer u een aangepaste hostnaam toevoegt aan uw web-apps, wordt gezocht naar een specifieke TXT-record om uw domein te valideren.

1. Open uw brongroep en selecteer de DNS-zone.
2. Selecteer **Recordset**.
3. Voeg de recordset toe met de volgende tabel. Gebruik voor de waarde de werkelijke URL van de web-app die u eerder hebt opgenomen:

   |Name  |Type  |Waarde|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Een aangepast domein toevoegen

Voeg een aangepast domein toe voor beide web-apps.

1. Open uw brongroep en selecteer uw eerste web-app.
2. Selecteer **aangepaste domeinen**in de linkerkolom .
3. Selecteer **Onder Aangepaste domeinen**de optie Aangepast domein **toevoegen**.
4. Typ **onder Aangepast domein**uw aangepaste domeinnaam. Bijvoorbeeld contoso.com.
5. Selecteer **Valideren**.

   Uw domein moet validatie doorgeven en groene vinkjes weergeven naast **beschikbaarheid hostname** en **domeineigendom.**
5. Selecteer **Aangepast domein toevoegen**.
6. Als u de nieuwe hostnaam wilt weergeven onder **Hostnames die aan de site zijn toegewezen,** vernieuwt u uw browser. De vernieuwing op de pagina wordt niet altijd meteen weergegeven.
7. Herhaal deze procedure voor uw tweede web-app.

## <a name="add-the-alias-record-set"></a>De aliasrecordset toevoegen

Voeg nu een aliasrecord toe voor de zonetop.

1. Open uw brongroep en selecteer de DNS-zone.
2. Selecteer **Recordset**.
3. Voeg de recordset toe met de volgende tabel:

   |Name  |Type  |Alias record set  |Aliastype  |Azure-bron|
   |---------|---------|---------|---------|-----|
   |@     |A|Ja|Azure-bron|Traffic Manager - uw profiel|


## <a name="test-your-web-apps"></a>Uw web-apps testen

Nu u testen of u uw web-app bereiken en dat deze in balans wordt gebracht.

1. Open een webbrowser en blader naar uw domein. Bijvoorbeeld contoso.com. U ziet de standaardwebpagina.
2. Stop je eerste web-app.
3. Sluit uw webbrowser en wacht een paar minuten.
4. Start uw webbrowser en blader naar uw domein. U moet nog steeds de standaardwebpagina zien.
5. Stop uw tweede web-app.
6. Sluit uw webbrowser en wacht een paar minuten.
7. Start uw webbrowser en blader naar uw domein. U moet Fout 403 zien, die aangeeft dat de web-app is gestopt.
8. Start uw tweede web-app.
9. Sluit uw webbrowser en wacht een paar minuten.
10. Start uw webbrowser en blader naar uw domein. U moet de standaardwebpagina van de web-app opnieuw bekijken.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over aliasrecords:

- [Zelfstudie: Een aliasrecord configureren om te verwijzen naar een openbaar IP-adres van Azure](tutorial-alias-pip.md)
- [Zelfstudie: een Azure DNS-aliasrecord configureren om het gebruik van hoofddomeinnaam met Traffic Manager te ondersteunen](tutorial-alias-tm.md)
- [Veelgestelde vragen over DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Zie [Een actieve DNS-naam migreren naar Azure App Service](../app-service/manage-custom-dns-migrate-domain.md)voor meer informatie over het migreren van een actieve DNS-naam.
