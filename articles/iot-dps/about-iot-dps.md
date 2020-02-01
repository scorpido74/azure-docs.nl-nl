---
title: Overzicht van Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Hierin wordt het inrichten van apparaten in azure beschreven met de Device Provisioning Service (DPS) en IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: d097894c841d91d344b5958e7f5e1c10249f8b6e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904871"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Apparaten inrichten met Azure IoT Hub Device Provisioning Service
Microsoft Azure biedt een uitgebreide set geïntegreerde openbare cloudservices om tegemoet te komen aan al uw IoT-vragen. De IoT Hub Device Provisioning Service (DPS) is een Helper-service voor IoT Hub die Zero-Touch, just-in-time inrichting naar de juiste IoT-hub biedt zonder menselijke tussen komst. Met DPS kunnen miljoenen apparaten op een veilige en schaal bare manier worden ingericht.

## <a name="when-to-use-device-provisioning-service"></a>Wanneer Device Provisioning Service gebruiken
Er zijn veel inrichtings scenario's waarin DPS een uitstekende keuze is voor het verkrijgen van apparaten die zijn verbonden en geconfigureerd voor IoT Hub, zoals:

* Apparaat zonder tussenkomst van gebruiker inrichten voor een afzonderlijke IoT-oplossing zonder dat hiervoor in de fabriek (eerste installatie) hardcoding van verbindingsgegevens voor IoT-Hub nodig is
* Taakverdelings apparaten op meerdere hubs
* Apparaten verbinden met de IoT-oplossing van hun eigenaar op basis van verkooptransactiegegevens (multitenancy)
* Apparaten verbinden met een bepaalde IoT-oplossing, afhankelijk van het gebruiksscenario (isolatie van oplossing)
* Apparaat met de laagst mogelijke latentie (geo-sharding) verbinden met de IoT-hub
* Herinrichten op basis van een wijziging op het apparaat
* Aanpassen van de sleutels die door het apparaat worden gebruikt om verbinding te maken met IoT Hub (als er geen verbinding wordt gemaakt met behulp van X.509-certificaten)

## <a name="behind-the-scenes"></a>Achter de schermen
Alle scenario's die in de vorige sectie worden vermeld, kunnen worden uitgevoerd met behulp van DPS voor het inrichten op nul met dezelfde stroom. Veel van de hand matige stappen die traditioneel zijn betrokken bij het inrichten, worden geautomatiseerd met DPS om de tijd te beperken om IoT-apparaten te implementeren en het risico op hand matige fout te verlagen. In de volgende sectie wordt beschreven wat er achter de schermen gebeurt om een apparaat in te richten. De eerste stap is handmatig, alle overige gebeuren automatisch.

![Basiswerkstroom voor inrichting van apparaat](./media/about-iot-dps/dps-provisioning-flow.png)

1. De apparaatfabrikant voegt de registratiegegevens van het apparaat toe aan de lijst met registraties in Azure Portal.
2. Apparaat neemt contact op met het DPS-eind punt dat is ingesteld in de fabriek. Het apparaat geeft de identificerende informatie door aan DPS om de identiteit ervan te bewijzen.
3. DPS valideert de identiteit van het apparaat door de registratie-ID en-sleutel te valideren op basis van de inschrijvings lijst vermelding met behulp van een nonce Challenge ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) of Standard X. 509-verificatie (X. 509).
4. Met DPS wordt het apparaat geregistreerd bij een IoT-hub en wordt de [gewenste twee status](../iot-hub/iot-hub-devguide-device-twins.md)van het apparaat ingevuld.
5. De IoT-hub retourneert apparaat-ID-informatie naar DPS.
6. DPS retourneert de gegevens van de IoT hub-verbinding met het apparaat. Het apparaat kan nu rechtstreeks gegevens gaan verzenden naar de IoT-hub.
7. Het apparaat maakt verbinding met IoT Hub.
8. Het apparaat krijgt de gewenste status van de apparaatdubbel in IoT Hub.

## <a name="provisioning-process"></a>Inrichtingsproces
Er zijn twee afzonderlijke stappen in het implementatie proces van een apparaat waarbij DPS een deel afneemt dat onafhankelijk kan worden uitgevoerd:

* De **productiestap** waarin het apparaat wordt gemaakt en voorbereid in de fabriek, en
* De **configuratiestap voor de cloud** waarin Device Provisioning Service wordt geconfigureerd voor automatisch inrichten.

Beide stappen sluiten naadloos aan op bestaande productie- en implementatieprocessen. DPS vereenvoudigt ook sommige implementatie processen die hand matig moeten worden uitgevoerd om verbindings gegevens op het apparaat te verkrijgen.

### <a name="manufacturing-step"></a>Productiestap
Deze stap omvat alles wat te maken heeft met de productielijn. Voorbeelden van rollen die zijn betrokken bij deze stap zijn siliciumontwerper, siliciumfabrikant, integrator en/of de eindfabrikant van het apparaat. Deze stap gaat om het maken van de hardware zelf.

DPS introduceert geen nieuwe stap in het productie proces; in plaats daarvan wordt de bestaande stap voor het installeren van de eerste software en (in het ideale geval) de HSM op het apparaat. In plaats van een apparaat-id te maken in deze stap, wordt het apparaat geprogrammeerd met de gegevens van de inrichtingsservice, waardoor het apparaat bij inschakeling de service kan aanroepen om de verbindingsgegevens/toewijzing van de IoT-oplossing op te vragen.

In deze stap verstrekt de fabrikant ook belangrijke identificatiegegevens aan de persoon die het apparaat implementeert/gebruikt (operator). Het verstrekken van die gegevens kan zo eenvoudig zijn als het bevestigen dat alle apparaten beschikken over een X.509-certificaat dat is gegenereerd op basis van een ondertekend certificaat dat is geleverd door de persoon die het apparaat implementeert/gebruikt, of zo ingewikkeld als het extraheren van het openbare deel van een TPM-goedkeuringssleutel van elk TPM-apparaat. Deze services worden vandaag de dag aangeboden door verschillende fabrikanten van silicium.

### <a name="cloud-setup-step"></a>Configuratiestap voor de cloud
Deze stap betreft het configureren van de cloud voor een juiste automatische inrichting. In het algemeen er zijn twee typen gebruikers betrokken bij deze stap: iemand die weet hoe apparaten in eerste instantie moeten worden geconfigureerd (een operator van apparaten) en iemand anders die weet hoe apparaten moeten worden verdeeld tussen de IoT-hubs (een operator van oplossingen).

De inrichting moet in eerste instantie eenmalig worden geconfigureerd, wat meestal wordt afgehandeld door de operator van de oplossing. Zodra de inrichtingsservice is geconfigureerd, hoeft deze niet meer te worden gewijzigd, tenzij het gebruiksscenario verandert.

Nadat de service is geconfigureerd voor automatische inrichting, moet de service worden voorbereid voor de registratie van apparaten. Deze stap wordt uitgevoerd door de operator van het apparaat, die weet wat de gewenste configuratie is van het apparaat of de apparaten en die ervoor moet zorgen dat de inrichtingsservice de identiteit van het apparaat goed kan bevestigen wanneer het apparaat op zoek gaat naar de juiste IoT-hub. De operator van het apparaat neemt de belangrijkste identificatiegegevens van de fabrikant en voegt deze toe aan de lijst met registraties. De lijst met registraties kan nog worden gewijzigd als er nieuwe items worden toegevoegd of bestaande vermeldingen worden bijgewerkt met de meest recente informatie over de apparaten.

## <a name="registration-and-provisioning"></a>Registratie en inrichting
*Inrichting* kan verschillende dingen betekenen, afhankelijk van de branche waarin de term wordt gebruikt. In de context van het inrichten van IoT-apparaten bij hun cloudoplossing bestaat het proces uit twee delen:

1. Het eerste deel omvat het tot stand brengen van de eerste verbinding tussen het apparaat en de IoT-oplossing door het apparaat te registreren.
2. Het tweede gedeelte bestaat uit het toepassen van de juiste configuratie op het apparaat op basis van de specifieke vereisten van de oplossing waarbij het apparaat is geregistreerd.

Als beide stappen zijn voltooid, kunnen we zeggen dat het apparaat volledig is ingericht. Sommige cloudservices bieden alleen de eerste stap van het inrichtingsproces, het registreren van apparaten bij het eindpunt van de IoT-oplossing, maar niet de eerste configuratie. DPS automatiseert beide stappen om een naadloze inrichtings ervaring te bieden voor het apparaat.

## <a name="features-of-the-device-provisioning-service"></a>Functies van Device Provisioning Service
DPS heeft veel functies, waardoor deze ideaal is voor het inrichten van apparaten.

* Ondersteuning voor **beveiligde attestation** voor identiteiten op basis van zowel X.509 als TPM.
* **Lijst van registraties** met een volledig overzicht van de apparaten/groepen apparaten die op enige moment kunnen worden geregistreerd. De registratielijst bevat informatie over de gewenste configuratie van het apparaat als dit wordt geregistreerd en de lijst kan op elk gewenst moment worden bijgewerkt.
* **Meerdere toewijzings beleidsregels** om te bepalen hoe met DPS apparaten worden toegewezen aan IOT-hubs ter ondersteuning van uw Scenario's: laagste latentie, gelijkmatig gewogen distributie (standaard) en statische configuratie via de registratie lijst. De latentie wordt bepaald op basis van dezelfde methode als [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#performance).
* **Controle en logboekregistratie van diagnostische gegevens** om ervoor te zorgen dat alles goed werkt.
* Met **ondersteuning voor meerdere hubs** kan DPS aan de slag met het binden van apparaten aan een IOT-hub tijdens runtime. DPS kan communiceren met hubs in meerdere Azure-abonnementen.
* **Ondersteuning voor meerdere regio's** stelt DPS in staat om apparaten toe te wijzen aan IOT-hubs in andere regio's.

Zie [apparaatconcepten](concepts-device.md), [serviceconcepten](concepts-service.md) en [veiligheidsconcepten](concepts-security.md) voor meer informatie over de concepten en functies die zijn betrokken bij het inrichten van apparaten.

## <a name="cross-platform-support"></a>Ondersteuning voor meerdere platformen
Net als alle Azure IoT-Services werkt DPS op verschillende platformen met diverse besturings systemen. Azure biedt open source-SDK's in een veelheid aan [talen](https://github.com/Azure/azure-iot-sdks) ter bevordering van het verbinden van apparaten en het beheren van de service. DPS ondersteunt de volgende protocollen voor het verbinden van apparaten:

* HTTPS
* AMQP
* AMQP via WebSockets
* MQTT
* MQTT via WebSockets

DPS ondersteunt alleen HTTPS-verbindingen voor service bewerkingen.

## <a name="regions"></a>Regio's
DPS is beschikbaar in veel regio's. De bijgewerkte lijst met bestaande en nieuwe regio's voor alle services wordt aangekondigd op [Azure-regio's](https://azure.microsoft.com/regions/). U kunt de beschikbaarheid van Device Provisioning Service controleren op de pagina [Status van Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> DPS is globaal en niet gebonden aan een locatie. U moet echter een regio opgeven waarin de meta gegevens die aan uw DPS-profiel zijn gekoppeld, worden opgeslagen.

## <a name="availability"></a>Beschikbaarheid
Er is een 99,9% Service Level Agreement voor DPS, en u kunt [de sla lezen](https://azure.microsoft.com/support/legal/sla/iot-hub/). In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.

## <a name="quotas"></a>Quota
Voor elk Azure-abonnement gelden standaardquotalimieten. Deze limieten kunnen invloed hebben op het bereik van uw IoT-oplossing. De huidige limiet voor een abonnementsvariant is 10 Device Provisioning Services per abonnement.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

Meer informatie over quotalimieten vindt u hier:
* [Azure-abonnement en servicelimieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Gerelateerde Azure-onderdelen
Met DPS wordt het inrichten van apparaten geautomatiseerd met Azure IoT Hub. Lees hier meer informatie over [IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>Volgende stappen
U hebt nu een algemeen beeld van het inrichten van IoT-apparaten in Azure. De volgende stap is het uitproberen van een compleet IoT-scenario.
> [!div class="nextstepaction"]
> [IoT Hub Device Provisioning Service instellen met Azure Portal](quick-setup-auto-provision.md)
> [Een gesimuleerd apparaat maken en inrichten](quick-create-simulated-device.md)
> [Apparaat voorbereiden voor inrichten](tutorial-set-up-device.md)
