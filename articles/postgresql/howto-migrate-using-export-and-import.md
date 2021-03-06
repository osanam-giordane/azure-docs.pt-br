---
title: Migrar um banco de dados usando Importar e Exportar no Banco de Dados do Azure para PostgreSQL
description: Descreve como extrair um banco de dados PostgreSQL para um arquivo de script e importar os dados para o banco de dados de destino desse arquivo.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 5250b10c2d9645e25c5b6f6f0f34daa50608e51b
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736636"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrar seu banco de dados PostgreSQL usando exportar e importar
Use [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) para extrair um banco de dados PostgreSQL para um arquivo de script, e [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) para importar os dados para o banco de dados de destino desse arquivo.

## <a name="prerequisites"></a>pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um [Servidor de Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall a fim de permitir o acesso e um banco de dados sob ele.
- O utilitário de linha de comando [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) instalado
- O utilitário de linha de comando [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) instalado

Execute estas etapas para exportar e importar o banco de dados PostgreSQL.

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Criar um arquivo de script usando pg_dump que contém os dados a serem carregados
Para exportar seu banco de dados PostgreSQL existente localmente ou em uma VM para um arquivo de script sql, execute o seguinte comando em seu ambiente existente:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Por exemplo, se você tiver um servidor local e um banco de dados chamado **testdb** nele:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importar os dados no Banco de Dados do Azure para PostgreSQL de destino
Você pode usar a linha de comando psql e o parâmetro --dbname (-d) para importar os dados para o servidor do Banco de Dados do Azure para PostgreSQL e carregar o arquivo sql.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Este exemplo usa um utilitário psql e um arquivo de script nomeado **testdb.sql** da etapa anterior para importar dados para o banco de dados **mypgsqldb** no servidor de destino **mydemoserver.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Próximas etapas
- Para migrar um banco de dados PostgreSQL usando despejar e restaurar, veja [Migrar seu banco de dados PostgreSQL usando despejar e restaurar](howto-migrate-using-dump-and-restore.md).
- Para obter mais informações de como migrar bancos de dados para o Banco de Dados do Azure para PostgreSQL, confira o [Guia de Migração de Banco de Dados](http://aka.ms/datamigration). 
