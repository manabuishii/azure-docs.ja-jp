---
title: "SQL Database の認証と承認 | Microsoft Docs"
description: "SQL Database のセキュリティ管理 (具体的にはサーバーレベル プリンシパル アカウントを使用してデータベースのアクセスとログインのセキュリティを管理する方法) について説明します。"
keywords: "SQL Database のセキュリティ,データベース セキュリティ管理,ログイン セキュリティ,データベース セキュリティ,データベース アクセス"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 0a65a93f-d5dc-424b-a774-7ed62d996f8c
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/14/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: a3c3aabc6b1817df3bacb98a769ff167036ef3e6
ms.openlocfilehash: d54c7c6160e3c51f34bf2c7ba2661ab1e8a51bfa


---
# <a name="controlling-and-granting-database-access"></a>データベース アクセスの制御と許可

認証されたユーザーには、さまざまなメカニズムを使用してアクセスを許可できます。 

## <a name="unrestricted-administrative-accounts"></a>制限なしの管理者アカウント
仮想 master データベースとすべてのユーザー データベースに対するアクセスに無制限のアクセス許可を持つ管理者アカウントは 2 つ考えられます。 これらのアカウントは、サーバーレベル プリンシパル アカウントと呼ばれます。

### <a name="azure-sql-database-subscriber-account"></a>Azure SQL Database サブスクライバー アカウント
SQL Database は、論理 SQL インスタンスが作成されるときに、マスター データベースに 1 つのログイン アカウントを作成します。 このアカウントは、SQL Database サブスクライバー アカウントと呼ばれることがあります。 このアカウントは、SQL Server 認証 (ユーザー名とパスワード) を使用して接続します。 このアカウントは、論理サーバー インスタンスと、そのインスタンスに接続されているすべてのユーザー データベースの管理者です。 サブスクライバー アカウントのアクセス許可を制限することはできません。 これらのアカウントのうち、存在できるのは 1 つだけです。

### <a name="azure-active-directory-administrator"></a>Azure Active Directory 管理者
Azure Active Directory の 1 つの個人またはグループ アカウントも、管理者として構成できます。 Azure AD 管理者の構成は任意ですが、SQL Database への接続に Azure AD アカウントを使用する場合は、Azure AD 管理者を構成する必要があります。 Azure Active Directory アクセスの構成の詳細については、「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](sql-database-aad-authentication.md)」と「[SSMS support for Azure AD MFA with SQL Database and SQL Data Warehouse](sql-database-ssms-mfa-authentication.md)」 (SQL Database と SQL Data Warehouse での Azure AD MFA のための SSMS のサポート) をご覧ください。

### <a name="configuring-the-firewall"></a>ファイアウォールの構成
個々の IP アドレスまたは範囲に対してサーバー レベルのファイアウォールを構成すると、Azure SQL Database サブスクライバー アカウントと Azure Active Directory アカウントは、master データベースとすべてのユーザー データベースに接続できます。 初期状態のサーバー レベルのファイアウォールは、[PowerShell](sql-database-configure-firewall-settings-powershell.md) または [REST API](sql-database-configure-firewall-settings-rest.md) を使用して、[Azure Portal](sql-database-configure-firewall-settings.md) で構成できます。 接続が確立されると、[Transact-SQL](sql-database-configure-firewall-settings-tsql.md) を使用して、サーバー レベルのファイアウォール規則を追加で構成することもできます。

### <a name="administrator-access-path"></a>Administrator access path
サーバー レベルのファイアウォールが正しく構成されている場合、SQL Database サブスクライバー アカウントと Azure Active Directory SQL Server 管理者は、SQL Server Management Studio や SQL Server Data Tools などのクライアント ツールを使用して接続できます。 すべての機能を提供しているのは、最新のツールだけです。 次の図は、2 つの管理者アカウントの標準的な構成を示しています。

![Administrator access path](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

サーバー レベルのファイアウォールで開かれているポートを使用する場合、管理者はどの SQL データベースにも接続できます。

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>SQL Server Management Studio を使用したデータベースへの接続
サーバー、データベース、サーバー レベルのファイアウォール規則の作成や、SQL Server Management Studio を使用したデータベースの照会に関するチュートリアルについては、[Azure Portal と SQL Server Management Studio を通じた Azure SQL Database のサーバー、データベース、ファイアウォール規則の使用](sql-database-get-started.md)に関するページを参照してください。

> [!IMPORTANT]
> 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

## <a name="additional-server-level-administrative-roles"></a>追加のサーバー レベルの管理者ロール
前に説明したサーバー レベルの管理者ロールのほかに、SQL Database には仮想 master データベースに 2 つの制限付き管理者ロールが用意されています。それにユーザー アカウントを追加して、データベースの作成またはログインの管理のためのアクセス許可を付与することができます。

### <a name="database-creators"></a>データベース作成者
これらの管理者ロールの 1 つは、dbmanager ロールです。 このロールのメンバーは、新しいデータベースを作成できます。 このロールを使用するには、master データベースにユーザーを作成し、そのユーザーを **dbmanager** データベース ロールに追加します。 そのユーザーは、包含データベース ユーザーにすることも、仮想 master データベースの SQL Server ログインに基づくユーザーにすることもできます。

1. 管理者アカウントを使用して、仮想 master データベースに接続します。
2. 省略可能な手順: [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) ステートメントを使用して、SQL Server 認証ログインを作成します。 サンプル ステートメントは、次のとおりです。
   
   ```
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```
   
   > [!NOTE]
   > ログイン ユーザーまたは包含データベース ユーザーを作成するときは、強力なパスワードを使用します。 詳細については、「 [強力なパスワード](https://msdn.microsoft.com/library/ms161962.aspx)」を参照してください。
   > 
   > 
   
   パフォーマンスを向上させるため、ログイン (サーバー レベルのプリンシパル) はデータベース レベルで一時的にキャッシュされます。 認証キャッシュを更新する方法については、「 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)」をご覧ください。
3. 仮想 master データベースで、 [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) ステートメントを使用してユーザーを作成します。 このユーザーは、Azure Active Directory 認証の包含データベース ユーザー (Azure AD 認証用の環境を構成した場合)、SQL Server 認証の包含データベース ユーザー、または SQL Server 認証ログインに基づく SQL Server 認証ユーザー (前の手順で作成したもの) にすることができます。サンプル ステートメントは、次のとおりです。
   
   ```
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
   CREATE USER Tran WITH PASSWORD = '<strong_password>';
   CREATE USER Mary FROM LOGIN Mary; 
   ```
4. **ALTER ROLE** ステートメントを使用して、新しいユーザーを [dbmanager](https://msdn.microsoft.com/library/ms189775.aspx) データベース ロールに追加します。 サンプル ステートメントは、次のとおりです。
   
   ```
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```
   
   > [!NOTE]
   > dbmanager は仮想 master データベースのデータベース ロールであるため、dbmanager ロールにはユーザーのみを追加できます。 データベース レベルのロールにサーバー レベルのログインを追加することはできません。
   > 
   > 
5. 必要に応じて、新しいユーザーに接続を許可するようにサーバー レベルのファイアウォールを構成します。

これで、ユーザーは仮想 master データベースに接続し、新しいデータベースを作成できるようになりました。 データベースを作成したアカウントは、そのデータベースの所有者になります。

### <a name="login-managers"></a>ログイン マネージャー
もう 1 つの管理者ロールは、ログイン マネージャー ロールです。 このロールのメンバーは、master データベースに新しいログインを作成することができます。 必要であれば、同じ手順を実行して (ログインとユーザーを作成し、ユーザーを **loginmanager** ロールに追加して)、ユーザーが仮想 master に新しいログインを作成できるようにすることができます。 通常、この作業は必要ありません。Microsoft は、ログインに基づくユーザーを使用する代わりに、データベース レベルで認証される包含データベース ユーザーを使用することを推奨しているからです。 詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。

## <a name="non-administrator-users"></a>管理者以外のユーザー
一般に、管理者以外のアカウントは、仮想 master データベースへのアクセスを必要としません。 [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx) ステートメントを使用して、データベース レベルの包含データベース ユーザーを作成してください。 このユーザーは、Azure Active Directory 認証の包含データベース ユーザー (Azure AD 認証用の環境を構成した場合)、SQL Server 認証の包含データベース ユーザー、または SQL Server 認証ログインに基づく SQL Server 認証ユーザー (前の手順で作成したもの) にすることができます。詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。 

ユーザーを作成するには、データベースに接続し、次の例のようなステートメントを実行します。

```
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

最初、ユーザーを作成できるのは、データベースの管理者の 1 人か所有者だけです。 新しいユーザーの作成を他のユーザーに許可するには、次のようなステートメントを使用して、選択したユーザーに `ALTER ANY USER` アクセス許可を付与します。

```
GRANT ALTER ANY USER TO Mary;
```

データベースのフル コントロールを他のユーザーに与えるには、`ALTER ROLE` ステートメントを使用して、そのユーザーを **db_owner** 固定データベース ロールのメンバーにします。

> [!NOTE]
> ログインに基づくデータベース ユーザーを作成するのは、主に、複数のデータベースへのアクセスを必要とする SQL Server 認証ユーザーがいる場合があるためです。 ログインに基づくユーザーは、ログインと、そのログインのために保持されている 1 つのパスワードのみに関連付けられています。 個々のデータベース内の包含データベース ユーザーは、それぞれが個別のエンティティであり、それぞれが独自のパスワードを保持します。 このため、包含データベース ユーザーは、同じパスワードを保持しない場合に混乱することがあります。
> 
> 

### <a name="configuring-the-database-level-firewall"></a>データベース レベルのファイアウォールの構成
ベスト プラクティスとして、管理者以外のユーザーは、使用するデータベースにファイアウォール経由でのみアクセスできるようにすることをお勧めします。 サーバー レベルのファイアウォール経由で IP アドレスを承認し、すべてのデータベースへのアクセスを許可するのではなく、[sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) ステートメントを使用して、データベース レベルのファイアウォールを構成してください。 データベース レベルのファイアウォールは、ポータルを使用して構成することはできません。

### <a name="non-administrator-access-path"></a>Non-administrator access path
データベース レベルのファイアウォールが正しく構成されると、データベース ユーザーは SQL Server Management Studio や SQL Server Data Tools などのクライアント ツールを使用して接続できます。 すべての機能を提供しているのは、最新のツールだけです。 次の図は、管理者以外の標準的なアクセス パスを示しています。

![Non-administrator access path](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>グループとロール
効率的なアクセス管理では、個々のユーザーではなく、グループとロールに割り当てられたアクセス許可を使用します。 

- Azure Active Directory 認証を使用する場合は、Azure Active Directory ユーザーを Azure Active Directory グループに入れます。 そのグループ用に包含データベース ユーザーを作成します。 1 人または複数のデータベース ユーザーを[データベース ロール](https://msdn.microsoft.com/library/ms189121)に追加し、データベース ロールに[アクセス許可](https://msdn.microsoft.com/library/ms191291.aspx)を割り当てます。

- SQL Server 認証を使用する場合は、データベースに包含データベース ユーザーを作成します。 1 人または複数のデータベース ユーザーを[データベース ロール](https://msdn.microsoft.com/library/ms189121)に追加し、データベース ロールに[アクセス許可](https://msdn.microsoft.com/library/ms191291.aspx)を割り当てます。

データベース ロールは、**db_owner**、**db_ddladmin**、**db_datawriter**、**db_datareader**、**db_denydatawriter**、**db_denydatareader** などの組み込みロールを指定できます。 **db_owner** は、少数のユーザーのみに完全なアクセス許可を付与する際によく使用されます。 他の固定データベース ロールは、開発段階の単純なデータベースをすばやく取得するには便利ですが、運用段階のほとんどのデータベースには推奨されません。 たとえば、**db_datareader** 固定データベース ロールは、データベース内のすべてのテーブルへの読み取りアクセスを許可しますが、これは、通常、必要以上のことです。 [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) ステートメントを使用して独自のユーザー定義データベース ロールを作成し、各ロールに対してビジネスのニーズに応じて必要な最小限のアクセス許可を慎重に付与することをお勧めします。 ユーザーが複数のロールのメンバーである場合は、それらのアクセス許可すべてが集約されます。

## <a name="permissions"></a>アクセス許可
SQL Database では、個別に許可または拒否できるアクセス許可が 100 個を超えています。 これらのアクセス許可の多くは、入れ子になっています。 たとえば、スキーマに対する `UPDATE` アクセス許可には、そのスキーマ内の各テーブルに対する `UPDATE` アクセス許可が含まれています。 ほとんどのアクセス許可システムと同様に、アクセス許可の拒否は許可より優先されます。 入れ子になっている性質と、アクセス許可の数により、データベースを正しく保護するのに適切なアクセス許可システムを設計するには、慎重な調査を行う場合があります。 まず「[権限 (データベース エンジン)](https://msdn.microsoft.com/library/ms191291.aspx)」でアクセス許可の一覧を確認してから、アクセス許可の[ポスター サイズの図](http://go.microsoft.com/fwlink/?LinkId=229142)も確認してください。


### <a name="considerations-and-restrictions"></a>考慮事項と制限
SQL Database のログインとユーザーの管理では、以下の点を考慮してください。

* ``CREATE/ALTER/DROP DATABASE`` ステートメントを実行する場合は、**master** データベースに接続する必要があります。 サーバーレベル プリンシパル ログインに対応する master データベースのデータベース ユーザーは、変更または削除できません。 
* サーバーレベル プリンシパル ログインの既定の言語は英語 (米国) です。
* 管理者 (サーバーレベル プリンシパル ログインまたは Azure AD 管理者) と、**master** データベースの **dbmanager** データベース ロールのメンバーにのみ、``CREATE DATABASE`` と ``DROP DATABASE`` ステートメントを実行する権限があります。
* ``CREATE/ALTER/DROP LOGIN`` ステートメントを実行する場合は、master データベースに接続する必要があります。 ただし、ログインの使用はお勧めできません。 代わりに、包含データベース ユーザーを使用してください。
* ユーザー データベースに接続するには、接続文字列にそのデータベースの名前を指定する必要があります。
* サーバーレベル プリンシパル ログインと、**master** データベースの **loginmanager** データベース ロールのメンバーにのみ、``CREATE LOGIN``、``ALTER LOGIN``、``DROP LOGIN`` ステートメントを実行する権限があります。
* ADO.NET アプリケーションで ``CREATE/ALTER/DROP LOGIN`` と ``CREATE/ALTER/DROP DATABASE`` ステートメントを実行する場合、パラメーター化コマンドは使用できません。 詳細については、「 [コマンドとパラメーター](https://msdn.microsoft.com/library/ms254953.aspx)」をご覧ください。
* ``CREATE/ALTER/DROP DATABASE`` と ``CREATE/ALTER/DROP LOGIN`` ステートメントを実行する場合、これらの各ステートメントは、Transact-SQL バッチ内の唯一のステートメントである必要があります。 一致しないと、エラーが発生します。 たとえば、以下の Transact-SQL は、データベースが存在するかどうかを確認します。 存在する場合は、 ``DROP DATABASE`` ステートメントが呼び出され、データベースが削除されます。 ``DROP DATABASE`` ステートメントはバッチ内の唯一のステートメントではないので、これを実行すると Transact-SQL はエラーになります。

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

* ``CREATE USER`` ステートメントを ``FOR/FROM LOGIN`` オプションと共に実行する場合、これが Transact-SQL バッチ内の唯一のステートメントである必要があります。
* ``ALTER USER`` ステートメントを ``WITH LOGIN`` オプションと共に実行する場合、これが Transact-SQL バッチ内の唯一のステートメントである必要があります。
* ユーザーに対して ``CREATE/ALTER/DROP`` を実行するには、データベースに対する ``ALTER ANY USER`` 権限が必要です。
* データベース ロールの所有者が、そのデータベース ロールに対して他のデータベース ユーザーの追加または削除を行おうとすると、「**User or role 'Name' does not exist in this database.**」というエラーが発生する場合があります。 このエラーは、所有者からはユーザーが見えないために発生します。 この問題を解決するには、そのユーザーに対する ``VIEW DEFINITION`` 権限をロールの所有者に許可します。 


## <a name="next-steps"></a>次のステップ

- ファイアウォール規則の詳細については、[Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)に関するページを参照してください。
- すべての SQL Database セキュリティ機能の概要については、[SQL セキュリティの概要](sql-database-security-overview.md)に関するページを参照してください。
- チュートリアルについては、[SQL セキュリティの使用](sql-database-get-started-security.md)に関するページを参照してください。
- ビューとストアド プロシージャについては、「[ビューとストアド プロシージャの作成](https://msdn.microsoft.com/library/ms365311.aspx)」を参照してください。
- データベース オブジェクトへのアクセス権の付与については、「[データベース オブジェクトへのアクセス権の付与](https://msdn.microsoft.com/library/ms365327.aspx)」を参照してください。




<!--HONumber=Jan17_HO1-->


