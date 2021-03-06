---
title: Dynamische SQL gebruiken
description: Tips voor ontwikkel oplossingen die gebruikmaken van dynamische SQL voor toegewezen SQL-groepen in azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 52bc7bdc63f754d52bf4a69097c1dd309a6dc3ec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462772"
---
# <a name="dynamic-sql-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Dynamische SQL voor toegewezen SQL-groepen in azure Synapse Analytics

In dit artikel vindt u tips voor ontwikkel oplossingen die gebruikmaken van dynamische SQL in toegewezen SQL-groepen.

## <a name="dynamic-sql-example"></a>Voor beeld van dynamische SQL

Bij het ontwikkelen van toepassings code voor toegewezen SQL-groepen, moet u mogelijk gebruikmaken van dynamische SQL om flexibele, algemene en modulaire oplossingen te leveren. Toegewezen SQL-groepen ondersteunen op dit moment geen BLOB-gegevens typen.

Het ondersteunen van BLOB-gegevens typen kan de grootte van uw teken reeksen beperken omdat de BLOB-gegevens typen zowel varchar (max) als nvarchar (max)-typen bevatten.

Als u deze typen in de toepassings code hebt gebruikt om grote teken reeksen te maken, moet u de code in segmenten afsplitsen en in plaats daarvan de instructie EXEC gebruiken.

Een eenvoudig voor beeld:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Als de teken reeks kort is, kunt u [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) als normaal gebruiken.

> [!NOTE]
> Voor de instructies die worden uitgevoerd als dynamische SQL gelden alle regels voor T-SQL-validatie.

## <a name="next-steps"></a>Volgende stappen

Zie [ontwikkelings overzicht](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.
