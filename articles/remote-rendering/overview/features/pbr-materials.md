---
title: PBR-materialen
description: Beschrijft het PBR-materiaaltype.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 64553506f75451c50a87932904f00a7275ea9286
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680256"
---
# <a name="pbr-materials"></a>PBR-materialen

*PBR-materialen* zijn een van de ondersteunde [materiaaltypen](../../concepts/materials.md) in Azure Remote Rendering. Ze worden gebruikt voor [mazen](../../concepts/meshes.md) die realistische verlichting moeten ontvangen.

PBR staat voor **P**hysically **B**ased **R**vertederend en betekent dat het materiaal de visuele eigenschappen van een oppervlak op een fysiek plausibele manier beschrijft, zodat realistische resultaten onder alle lichtomstandigheden mogelijk zijn. De meeste moderne game engines en content creation tools ondersteunen PBR materialen omdat ze worden beschouwd als de beste benadering van de echte wereld scenario's voor real-time rendering.

![Helm glTF monster model gemaakt door ARR](media/helmet.png)

PBR materialen zijn echter geen universele oplossing. Er zijn materialen die kleur anders reflecteren, afhankelijk van de kijkhoek. Bijvoorbeeld, sommige stoffen of auto verven. Dit soort materialen worden niet verwerkt door het standaard PBR-model en worden momenteel niet ondersteund door Azure Remote Rendering. Dit omvat PBR-extensies, zoals *Thin-Film* (meerlaagse oppervlakken) en *Clear-Coat* (voor autolakken).

## <a name="common-material-properties"></a>Algemene materiaaleigenschappen

Deze eigenschappen zijn gemeenschappelijk voor alle materialen:

* **albedoColor:** Deze kleur wordt vermenigvuldigd met andere kleuren, zoals de *albedoMap-* of *hoekpuntkleuren.* Als *transparantie* is ingeschakeld op een materiaal, wordt het alfakanaal `1` gebruikt om `0` de dekking aan te passen, met betekenis volledig ondoorzichtig en betekenis volledig transparant. Standaard is wit.

  > [!NOTE]
  > Wanneer een PBR-materiaal volledig transparant is, als een perfect schoon stuk glas, weerspiegelt het nog steeds de omgeving. Heldere vlekken zoals de zon zijn nog steeds zichtbaar in de reflectie. Dit is anders voor [kleurmaterialen.](color-materials.md)

* **albedoMap:** Een [2D-textuur](../../concepts/textures.md) voor albedowaarden per pixel.

* **alphaClipEnabled** en **alphaClipThreshold:** Als *alphaClipEnabled* waar is, worden alle pixels waarbij de alfawaarde van albedo lager is dan *alphaClipThreshold* niet getekend. Alpha clipping kan zelfs worden gebruikt zonder transparantie en is veel sneller te renderen. Alpha geknipte materialen zijn nog steeds langzamer te renderen dan volledig ondoorzichtige materialen, dat wel. Standaard is alpha clipping uitgeschakeld.

* **textureCoordinateScale** en **textureCoordinateOffset:** De schaal wordt vermenigvuldigd in de UV-structuurcoördinaten, de verschuiving wordt eraan toegevoegd. Kan worden gebruikt om de texturen uit te rekken en te verschuiven. De standaardschaal is (1, 1) en offset is (0, 0).

* **useVertexColor:** Als het net hoekpuntkleuren bevat en deze optie is ingeschakeld, worden de hoekpuntkleuren van de mazen vermenigvuldigd tot de *albedoColor* en *albedoMap.* Standaard zijn vertex-kleuren uitgeschakeld.

* **isDoubleSided:** Als dubbelzijdigheid is ingesteld op true, driehoeken met dit materiaal worden weergegeven, zelfs als de camera kijkt naar hun achterste gezichten. Voor PBR materialen verlichting is ook goed berekend voor achtervlakken. Standaard is deze optie uitgeschakeld. Zie ook [Single-sided rendering](single-sided-rendering.md).

## <a name="pbr-material-properties"></a>PBR-materiaaleigenschappen

Het kernidee van fysiek gebaseerde rendering is om *BaseColor,* *Metalness*en *Roughness* eigenschappen te gebruiken om een breed scala aan echte materialen na te bootsen. Een gedetailleerde beschrijving van PBR valt buiten het toepassingsgebied van dit artikel. Zie voor meer informatie over PBR [andere bronnen.](http://www.pbr-book.org) De volgende eigenschappen zijn specifiek voor PBR-materialen:

* **baseColor:** In PBR-materialen wordt de *albedo-kleur* aangeduid als de *basiskleur.* In Azure Remote Rendering is de eigenschap *albedo-kleur* al aanwezig via de algemene materiaaleigenschappen, dus er is geen extra eigenschap basiskleur.

* **ruwheid** en **ruwheidMap:** Ruwheid bepaalt hoe ruw of glad het oppervlak is. Ruwe oppervlakken verspreiden het licht in meer richtingen dan gladde oppervlakken, waardoor reflecties wazig in plaats van scherp. Het waardebereik `0.0` is `1.0`van tot . Wanneer `roughness` `0.0`gelijken , reflecties scherp zal zijn. Wanneer `roughness` `0.5`gelijken, reflecties zal wazig worden.

  Als zowel een ruwheidswaarde als een ruwheidskaart worden geleverd, zal de uiteindelijke waarde het product van twee zijn.

* **metaal-** en **metaalheidMap:** In de natuurkunde komt deze eigenschap overeen met de vraag of een oppervlak geleidend of d-elektrisch is. Geleidende materialen hebben verschillende reflecterende eigenschappen, en ze hebben de neiging om reflecterend te zijn zonder albedo kleur. In PBR-materialen beïnvloedt deze eigenschap hoeveel een oppervlak de omgeving weerspiegelt. Waarden variëren `0.0` `1.0`van . Wanneer metaalachtigheid is, `0.0`is de albedokleur volledig zichtbaar en ziet het materiaal eruit als plastic of keramiek. Wanneer metaalachtigheid is, `0.5`het ziet eruit als geschilderd metaal. Wanneer metaalheid `1.0`is, verliest het oppervlak bijna volledig zijn albedokleur en weerspiegelt alleen de omgeving. Bijvoorbeeld, als `metalness` `1.0` is `roughness` `0.0` en is dan een oppervlak ziet eruit als real-world spiegel.

  Als zowel een metaalheidswaarde als een metaalheidskaart worden geleverd, is de uiteindelijke waarde het product van de twee.

  ![metaalen en ruwheid](./media/metalness-roughness.png)

  Op de foto hierboven, de bol in de rechterbenedenhoek ziet eruit als een echte metalen materiaal, de linkerbenedenhoek ziet eruit als keramiek of plastic. De albedo kleur verandert ook afhankelijk van fysieke eigenschappen. Bij toenemende ruwheid verliest het materiaal reflectiescherpte.

* **normalMap:** Om fijnkorrelige details te simuleren, kan een [normale kaart](https://en.wikipedia.org/wiki/Normal_mapping) worden verstrekt.

* **occlusionMap** en **aoScale:** [Ambient occlusion](https://en.wikipedia.org/wiki/Ambient_occlusion) zorgt ervoor dat objecten met spleten er realistischer uitzien door schaduwen toe te voegen aan afgesloten gebieden. Occlusie waarde `0.0` variëren `1.0`van `0.0` tot , waar `1.0` betekent duisternis (occluded) en betekent geen occlusies. Als een 2D-structuur wordt geleverd als een occlusiekaart, wordt het effect ingeschakeld en fungeert *aoScale* als een multiplier.

  ![Occlusiekaart](./media/boom-box-ao2.gif)

* **transparant:** Voor PBR-materialen is er slechts één transparantie-instelling: het is ingeschakeld of niet. De dekking wordt gedefinieerd door het alfakanaal van de albedo-kleur. Wanneer ingeschakeld, wordt een complexere renderingpipeline aangeroepen om semi-transparante oppervlakken te tekenen. Azure Remote Rendering implementeert true [order independent transparency](https://en.wikipedia.org/wiki/Order-independent_transparency) (OIT).

  Transparante geometrie is duur om te renderen. Als u alleen gaten in een oppervlak nodig hebt, bijvoorbeeld voor de bladeren van een boom, is het beter om in plaats daarvan alfaknippen te gebruiken.

  ![Transparantie](./media/transparency.png) Let op in de afbeelding hierboven, hoe de meest rechtse bol volledig transparant is, maar de reflectie is nog steeds zichtbaar.

  > [!IMPORTANT]
  > Als materiaal tijdens runtime van ondoorzichtig naar transparant moet worden overgeschakeld, moet de renderer de *TileBasedComposition-renderingmodus* [rendering mode](../../concepts/rendering-modes.md)gebruiken. Deze beperking is niet van toepassing op materialen die om te beginnen als transparant materiaal worden omgezet.

## <a name="technical-details"></a>Technische details

Azure Remote Rendering maakt gebruik van de Cook-Torrance micro-facet BRDF met GGX NDF, Schlick Fresnel en een GGX Smith gecorreleerd zichtbaarheidterm met een Lambert diffuse term. Dit model is de de facto industrie standaard op dit moment. Voor meer diepgaande details, verwijzen naar dit artikel: [Fysiek gebaseerde Rendering - Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Een alternatief voor het *PBR-model Metalness-Roughness* dat wordt gebruikt in Azure Remote Rendering is het *PBR-model Specular-Glossiness.* Dit model kan een breder scala aan materialen vertegenwoordigen. Echter, het is duurder, en meestal niet goed werkt voor real-time gevallen.
Het is niet altijd mogelijk om van *Spiegelende glans* om te zetten in *Metalness-Roughness* omdat er *(Diffuus, Spiegelende)* waardeparen zijn die niet kunnen worden omgezet in *(BaseColor, Metalness).* De omzetting in de andere richting is eenvoudiger en nauwkeuriger, aangezien alle *paren (BaseColor, Metalness)* aan goed-bepaalde *(Diffuse, Spiegelende)* paren beantwoorden.

## <a name="next-steps"></a>Volgende stappen

* [Kleurmaterialen](color-materials.md)
* [Texturen](../../concepts/textures.md)
* [Netten](../../concepts/meshes.md)
