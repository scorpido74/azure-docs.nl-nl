---
title: Sjabloonfuncties
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon om waarden op te halen, te werken met teken reeksen en cijfers en implementatie gegevens op te halen.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a15e7dfdf01a99cd23b216fafcfb44320a716d16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231285"
---
# <a name="arm-template-functions"></a>ARM-sjabloon functies

In dit artikel worden alle functies beschreven die u kunt gebruiken in een Azure Resource Manager-sjabloon (ARM). Zie de [syntaxis van sjablonen](template-expressions.md)voor meer informatie over het gebruik van functies in uw sjabloon.

Zie door de [gebruiker gedefinieerde functies](template-syntax.md#functions)om uw eigen functies te maken.

De meeste functies werken hetzelfde wanneer ze worden geïmplementeerd in een resource groep, een abonnement, een beheer groep of een Tenant. Een aantal functies kan niet worden gebruikt in alle bereiken. Deze worden vermeld in de onderstaande lijsten.

<a id="array" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-functions"></a>Matrixfuncties

Resource Manager biedt verschillende functies voor het werken met matrices.

* [matrix](template-functions-array.md#array)
* [concat](template-functions-array.md#concat)
* [daarin](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [gelaten](template-functions-array.md#empty)
* [instantie](template-functions-array.md#first)
* [Snij punt](template-functions-array.md#intersection)
* [duren](template-functions-array.md#last)
* [lange](template-functions-array.md#length)
* [Haal](template-functions-array.md#min)
* [aantal](template-functions-array.md#max)
* [bereik](template-functions-array.md#range)
* [verdergaan](template-functions-array.md#skip)
* [Houd](template-functions-array.md#take)
* [Réunion](template-functions-array.md#union)

<a id="coalesce" aria-hidden="true" />
<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Vergelijkingsfuncties

Resource Manager biedt verschillende functies voor het maken van vergelijkingen in uw sjablonen.

* [Voeg](template-functions-comparison.md#coalesce)
* [equals](template-functions-comparison.md#equals)
* [less](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>Datumfuncties

Resource Manager biedt de volgende functies voor het werken met datums.

* [dateTimeAdd](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Implementatie waarden functies

Resource Manager biedt de volgende functies voor het ophalen van waarden uit secties van de sjabloon en waarden die betrekking hebben op de implementatie:

* [inhoudsdistributiepad](template-functions-deployment.md#deployment)
* [variabelen](template-functions-deployment.md#environment)
* [instellen](template-functions-deployment.md#parameters)
* [variabelen](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Logische functies

Resource Manager biedt de volgende functies voor het werken met logische voor waarden:

* [maar](template-functions-logical.md#and)
* [booleaans](template-functions-logical.md#bool)
* [If](template-functions-logical.md#if)
* [ten](template-functions-logical.md#not)
* [of](template-functions-logical.md#or)

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

Resource Manager biedt de volgende functies voor het werken met gehele getallen:

* [toe](template-functions-numeric.md#add)
* [Functie copyindex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [Haal](template-functions-numeric.md#min)
* [aantal](template-functions-numeric.md#max)
* [mod](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [sub](template-functions-numeric.md#sub)

<a id="json" aria-hidden="true" />

## <a name="object-functions"></a>Object functies

Resource Manager biedt verschillende functies voor het werken met objecten.

* [daarin](template-functions-object.md#contains)
* [gelaten](template-functions-object.md#empty)
* [Snij punt](template-functions-object.md#intersection)
* [JSON](template-functions-object.md#json)
* [lange](template-functions-object.md#length)
* [Réunion](template-functions-object.md#union)

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

Resource Manager biedt de volgende functies voor het ophalen van resource waarden:

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [Listkeys ophalen](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [orderverzamellijst](template-functions-resource.md#list)
* [hardwareproviders](template-functions-resource.md#providers)
* [referentielaag](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) -kan alleen worden gebruikt in implementaties van een resource groep.
* [resourceId](template-functions-resource.md#resourceid) -kan in elk bereik worden gebruikt, maar de geldige para meters veranderen afhankelijk van het bereik.
* [abonnement](template-functions-resource.md#subscription) : kan alleen worden gebruikt in implementaties van een resource groep of abonnement.
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
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

Resource Manager biedt de volgende functies voor het werken met teken reeksen:

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [concat](template-functions-string.md#concat)
* [daarin](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [gelaten](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [instantie](template-functions-string.md#first)
* [Formatteer](template-functions-string.md#format)
* [guid](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [duren](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [lange](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [vervangen](template-functions-string.md#replace)
* [verdergaan](template-functions-string.md#skip)
* [delen](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [tekenreeks](template-functions-string.md#string)
* [subtekenreeks](template-functions-string.md#substring)
* [Houd](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [interne](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [URI](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Volgende stappen

* Zie [arm-sjablonen ontwerpen](template-syntax.md) voor een beschrijving van de secties in een arm-sjabloon
* Zie voor het samen voegen van meerdere sjablonen [gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md)
* Als u een bepaald aantal keer wilt herhalen bij het maken van een type resource, raadpleegt u [meerdere exemplaren van resources maken in azure Resource Manager](copy-resources.md).
* Zie [een toepassing implementeren met arm-sjablonen](deploy-powershell.md) voor meer informatie over het implementeren van de sjabloon die u hebt gemaakt.
