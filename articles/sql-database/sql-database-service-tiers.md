---
title: "SQL Database のパフォーマンス: サービス レベル | Microsoft Docs"
description: "SQL Database のサービス レベルを比較します。"
keywords: "データベース オプション, データベース パフォーマンス"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 12/06/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 7bbdbe345bd468c01e2a790610bcf6c063c11f9b


---
# <a name="sql-database-service-tiers-for-single-databases-and-elastic-database-pools"></a>単一データベースとエラスティック データベース プールの SQL Database のサービス レベル
[Azure SQL Database](sql-database-technical-overview.md) は、さまざまなワークロードに対応するため、3 つのサービス レベルといくつかのパフォーマンス レベルが用意されています。 パフォーマンス レベルが高くなるほど提供されるリソースのセットが増加し、スループットが段階的に高くなるように設計されています。 サービス レベルとパフォーマンス レベルは動的に変更できます。 詳細については、「 [データベースのサービス階層とパフォーマンス レベルの変更](sql-database-scale-up.md) 」を参照してください。

データベースはそれぞれ、[サービス レベル](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)とパフォーマンス レベルを変えて管理することができます。 また、一定のリソースを共有する[エラスティック データベース プール](sql-database-service-tiers.md#elastic-database-pool-service-tiers-and-performance-in-edtus)で複数のデータベースを管理することもできます。 単一データベースで利用できるリソースは、データベース トランザクション ユニット (DTU) を単位として表現されます。これに対して、エラスティック データベース プールで利用できるリソースは、エラスティック DTU (eDTU) を単位として表現されます。 DTU と eDTU については、[DTU の概要](sql-database-what-is-a-dtu.md)に関するページを参照してください。 

どちらの場合も、サービス レベルには **Basic**、**Standard**、**Premium** があります。 

## <a name="service-tiers"></a>サービス階層
Basic、Standard、および Premium のサービス階層は、いずれも稼働時間の SLA が 99.99% で、予測可能なパフォーマンス、柔軟なビジネス継続性のオプション、セキュリティ機能、1 時間単位の課金体系などが提供されます。 次の表では、各種のアプリケーション ワークロードに最適なサービス階層の例を示します。

| サービス階層 | 対象のワークロード |
| :--- | --- |
| **Basic** |小規模なデータベースに最適です。通常は、一度に 1 つのアクティブな操作をサポートします。 たとえば、開発やテスト、使用頻度の低い小規模なアプリケーションなどに使用するデータベースがこれに該当します。 |
| **Standard** |ほとんどのクラウド アプリケーションに適しています。同時に複数のクエリをサポートしています。 たとえば、ワークグループや Web アプリケーションなどです。 |
| **Premium** |トランザクション量が多く、多数の同時ユーザーをサポートし、高度なビジネス継続性機能を必要とする場合に合わせて設計されています。 たとえば、ミッション クリティカルなアプリケーションをサポートするデータベースなどです。 |

> [!NOTE]
> Web および Business エディションは使用中止となっています。 Web Edition と Business Edition の使用を続行する場合は、 [終了に関する FAQ](https://azure.microsoft.com/pricing/details/sql-database/web-business/) をご覧ください。
> 
> 

## <a name="single-database-service-tiers-and-performance-levels"></a>Single Database サービス階層とパフォーマンス レベル
単一データベースでは、各サービス レベル内に複数のパフォーマンス レベルがあります。 ワークロードのニーズに最適なレベルを選択できる柔軟性があります。 スケールアップやスケールダウンが必要な場合は、データベースのパフォーマンス レベルをすばやく変更できます。 詳細については、「 [データベースのサービス階層とパフォーマンス レベルの変更](sql-database-scale-up.md) 」を参照してください。

ここに示されたパフォーマンス特性は、[SQL Database V12](sql-database-technical-overview.md) を使用して作成したデータベースに適用されます。 ホストされるデータベースの数にかかわらず、データベースは所定のリソースを取得します。データベースの期待されるパフォーマンス特性には影響しません。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> このサービス層テーブルのその他すべての行の詳細については、「 [サービス層の機能と制限](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)」を参照してください。
> 
> 

## <a name="elastic-database-pool-service-tiers-and-performance-in-edtus"></a>エラスティック データベース プール サービス階層と eDTU のパフォーマンス
[エラスティック データベース プール](sql-database-elastic-pool.md)内で複数のデータベースを管理することもできます。 エラスティック データベース プール内のすべてのデータベースは、共通のリソース セットを共有します。 パフォーマンス特性は、 *エラスティック データベース トランザクション ユニット* (eDTU) で測定されます。 単一データベースの場合と同様に、プールにも **Basic**、**Standard**、**Premium** という 3 つのサービス レベルがあります。 プールの場合もやはり、この 3 つのサービス階層によってパフォーマンス全体に関する制限事項といくつかの機能が定義されます。

プールでは、データベースが DTU リソースを共有して使用することができ、プール内の各データベースに特定のパフォーマンス レベルを割り当てる必要はありません。 たとえば、Standard プール内のデータベースで使用できるのは、0 eDTU から、プールの構成時に設定した最大データベース eDTU までとなります。 プールでは、多様なワークロードを持つ複数のデータベースが、プール全体で利用可能な eDTU リソースを効率的に使用できます。 詳細については、[エラスティック データベース プールの価格とパフォーマンスの考慮事項](sql-database-elastic-pool-guidance.md)に関する記事を参照してください。

次の表では、Basic、Standard、および Premium のエラスティック データベース プールの特性について説明します。

[!INCLUDE [SQL DB service tiers table for elastic database pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

プール内の各データベースは、該当するレベルにおける単一データベースの特性にも従います。 たとえば、Basic プールでは、プールあたりの最大セッション数が 4,800 ～ 28,800 の範囲に制限されます。ただし、Basic プール内の個々のデータベースでは、データベースあたりのセッション数が 300 に制限されます。

## <a name="choosing-a-service-tier"></a>サービス階層の選択
サービス レベルを決めるにはまず、データベースを単一データベースにするか、エラスティック データベース プールに追加するかを判断します。 

### <a name="choosing-a-service-tier-for-a-single-database"></a>単一データベースのサービス レベルの選択
単一データベースのサービス レベルを決めるにあたって最初に行うことは、SQL Database のエディションの選択に必要なデータベース機能を特定することです。

* データベース サイズ (パフォーマンス レベルによって異なり、Basic では最大 2 GB、Standard では最大 250 GB、Premium では最大 500 GB ～ 1 TB)
* データベース バックアップの保有期間 (Basic では 7 日間、Standard では 35 日間、Premium では 35 日間)

SQL Database のエディションを選んだら、データベースのパフォーマンス レベル (DTU 数) を決めます。 見当をつけたうえで実際の使用感に基づいて [動的にスケールアップしたりスケールダウンしたり](sql-database-scale-up.md) することができます。 [DTU Calculator](http://dtucalculator.azurewebsites.net/) を使用して、必要な DTU 数を見積もることもできます。 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>エラスティック データベース プールのサービス階層の選択
エラスティック データベース プールのサービス レベルを決めるにあたって最初に行うことは、プールのサービス レベルを選択するのに必要なデータベース機能を特定することです。

* データベース サイズ (Basic では 2 GB、Standard では 250 GB、Premium では 500 GB)
* データベース バックアップの保有期間 (Basic では 7 日間、Standard では 35 日間、Premium では 35 日間)
* プールあたりのデータベース数 (Basic では 400 個、Standard では 400 個、Premium では 50 個)
* プールあたりの最大ストレージ (Basic では 117 GB、Standard では 1,200 GB、Premium では 750 GB)

プールのサービス階層を選んだら、プールのパフォーマンス レベル (eDTU) を決めます。 見当をつけたうえで実際の使用感に基づいて [動的にスケールアップしたりスケールダウンしたり](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) することができます。 [DTU Calculator](http://dtucalculator.azurewebsites.net/) を使用してプール内の各データベースに必要な DTU 数を見積もり、それを参考にしてプールの上限を特定できます。

## <a name="next-steps"></a>次のステップ
* これらの階層の価格の詳細については、「 [SQL Database 価格](https://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。
* [エラスティック データベース プール](sql-database-elastic-pool-guidance.md)と、[エラスティック データベース プールの価格とパフォーマンスの考慮事項](sql-database-elastic-pool-guidance.md)の詳細を確認してください。
* [エラスティック データベース プールの監視、管理、サイズ変更](sql-database-elastic-pool-manage-portal.md)と[単一データベースのパフォーマンスの監視](sql-database-single-database-monitor.md)を行う方法を確認してください。
* SQL Database の階層について理解できたら、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)で実際に試してみてください。また、[初めて SQL データベースを作成するにあたっての方法](sql-database-get-started.md)を確認してください。

## <a name="additional-resources"></a>その他のリソース
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Azure SQL Database のエラスティック データベース機能に関する Microsoft Virtual Academy のビデオ コース](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)




<!--HONumber=Nov16_HO4-->


