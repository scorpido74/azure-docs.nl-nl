---
title: Sjabloonfuncties
description: Beschrijft de functies die u moet gebruiken in een Azure Resource Manager-sjabloon om waarden op te halen, met tekenreeksen en numerieke gegevens te werken en implementatiegegevens op te halen.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: fbd82f89ed9a97a3f376a9ed6eaa8ae3760759ff
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982375"
---
# <a name="arm-template-functions"></a>ARM-sjabloonfuncties

In dit artikel worden alle functies beschreven die u gebruiken in een ARM-sjabloon (Azure Resource Manager). Zie [syntaxis van](template-expressions.md)de sjabloon voor informatie over het gebruik van functies in uw sjabloon.

Zie [Door de gebruiker gedefinieerde functies](template-syntax.md#functions)als u uw eigen functies wilt maken.

De meeste functies werken hetzelfde wanneer ze worden geïmplementeerd in een resourcegroep, abonnement, beheergroep of tenant. Een paar functies kunnen niet in alle scopes worden gebruikt. Ze staan in de onderstaande lijsten.

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>Matrix- en objectfuncties

Resource Manager biedt verschillende functies voor het werken met arrays en objecten.

* [matrix](template-functions-array.md#array)
* [samensmelten](template-functions-array.md#coalesce)
* [Concat](template-functions-array.md#concat)
* [Bevat](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [Lege](template-functions-array.md#empty)
* [Eerste](template-functions-array.md#first)
* [Snijpunt](template-functions-array.md#intersection)
* [Json](template-functions-array.md#json)
* [Laatste](template-functions-array.md#last)
* [Lengte](template-functions-array.md#length)
* [Min](template-functions-array.md#min)
* [Max](template-functions-array.md#max)
* [Bereik](template-functions-array.md#range)
* [Overslaan](template-functions-array.md#skip)
* [Nemen](template-functions-array.md#take)
* [Unie](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Vergelijkingsfuncties

Resource Manager biedt verschillende functies voor het maken van vergelijkingen in uw sjablonen.

* [equals](template-functions-comparison.md#equals)
* [less](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>Datumfuncties

Resourcemanager biedt de volgende functies voor het werken met datums.

* [dateTimeAdd dateTimeAdd dateTimeAdd dateTime](template-functions-date.md#datetimeadd)
* [utcNu](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Functies voor implementatiewaarde

Resourcemanager biedt de volgende functies voor het ophalen van waarden uit secties van de sjabloon en waarden die verband houden met de implementatie:

* [Implementatie](template-functions-deployment.md#deployment)
* [Milieu](template-functions-deployment.md#environment)
* [Parameters](template-functions-deployment.md#parameters)
* [Variabelen](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Logische functies

Resource Manager biedt de volgende functies voor het werken met logische voorwaarden:

* [En](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [Als](template-functions-logical.md#if)
* [Niet](template-functions-logical.md#not)
* [Of](template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>Numerieke functies

Resource manager biedt de volgende functies voor het werken met gehele getallen:

* [Toevoegen](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [Int](template-functions-numeric.md#int)
* [Min](template-functions-numeric.md#min)
* [Max](template-functions-numeric.md#max)
* [Mod](template-functions-numeric.md#mod)
* [Mul](template-functions-numeric.md#mul)
* [sub](template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>Resourcefuncties

Resourcemanager biedt de volgende functies voor het verkrijgen van resourcewaarden:

* [extensieResourceId](template-functions-resource.md#extensionresourceid)
* [lijstAccountSas](template-functions-resource.md#list)
* [lijstSleutels](template-functions-resource.md#listkeys)
* [lijstGeheimen](template-functions-resource.md#list)
* [lijst*](template-functions-resource.md#list)
* [Providers](template-functions-resource.md#providers)
* [Verwijzing](template-functions-resource.md#reference)
* [resourceGroep](template-functions-resource.md#resourcegroup) - kan alleen worden gebruikt in implementaties naar een resourcegroep.
* [resourceId](template-functions-resource.md#resourceid) - kan worden gebruikt op elk bereik, maar de geldige parameters veranderen afhankelijk van het bereik.
* [abonnement](template-functions-resource.md#subscription) - kan alleen worden gebruikt in implementaties voor een resourcegroep of -abonnement.
* [abonnementResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>Tekenreeksfuncties

Resourcemanager biedt de volgende functies voor het werken met tekenreeksen:

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [Concat](template-functions-string.md#concat)
* [Bevat](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [Lege](template-functions-string.md#empty)
* [endsMet](template-functions-string.md#endswith)
* [Eerste](template-functions-string.md#first)
* [Formaat](template-functions-string.md#format)
* [Guid](template-functions-string.md#guid)
* [indexVan](template-functions-string.md#indexof)
* [Laatste](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [Lengte](template-functions-string.md#length)
* [nieuwGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [Vervangen](template-functions-string.md#replace)
* [Overslaan](template-functions-string.md#skip)
* [split](template-functions-string.md#split)
* [startMet](template-functions-string.md#startswith)
* [Tekenreeks](template-functions-string.md#string)
* [Subtekenreeks](template-functions-string.md#substring)
* [Nemen](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [Toupper](template-functions-string.md#toupper)
* [Trim](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [Uri](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Volgende stappen

* Zie [Arm-sjablonen ontwerpen](template-syntax.md) voor een beschrijving van de secties in een ARM-sjabloon
* Zie [Gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md) als u meerdere sjablonen wilt samenvoegen
* Zie Meerdere exemplaren van resources maken [in Azure Resource Manager](copy-resources.md)als u een bepaald aantal keren wilt herhalen bij het maken van een type resource.
* Zie [Een toepassing implementeren met ARM-sjablonen](deploy-powershell.md) als u wilt zien hoe u de sjabloon implementeren die u hebt gemaakt,
