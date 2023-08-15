课程链接：https://xiedaimala.com/tasks/4928e263-c37f-42a1-8494-ba01c752facf

# 开发步骤

## 创建配置文件

```bash
touch ~/.config/mangosteen/config.json
```
文件内容从 `config.json.example` 里复制过去

## 安装工具

```bash
go install -tags 'postgres' github.com/golang-migrate/migrate/v4/cmd/migrate@latest
go install github.com/mailhog/MailHog@v1.0.1
go install github.com/kyleconroy/sqlc/cmd/sqlc@latest
go install github.com/swaggo/swag/cmd/swag@latest
swag init

```

## 启动本地数据库

### psql

```bash
docker run -d --name pg-for-go-mangosteen -e POSTGRES_USER=mangosteen -e POSTGRES_PASSWORD=123456 -e POSTGRES_DB=mangosteen_dev -e PGDATA=/var/lib/postgresql/data/pgdata -v pg-go-mangosteen-data:/var/lib/postgresql/data --network=network1 postgres:14
```

### mysql

```bash
docker run -d --network=network1 --name mysql-for-go-mangosteen -e MYSQL_DATABASE=mangosteen_dev -e MYSQL_USER=mangosteen -e MYSQL_PASSWORD=123456 -e MYSQL_ROOT_PASSWORD=123456 -v mysql-go-mangosteen-data:/var/lib/mysql mysql:8 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

# 测试步骤

首先需要安装 MailHog 并运行：

```bash
go install github.com/mailhog/MailHog@v1.0.1 && MailHog
```


# 如何进行数据库迁移


## 创建迁移文件

```bash
go build . && ./mangosteen db create:migration create_users_table
# 或者
migrate create -ext sql -dir config/migrations -seq create_users_table
```
## 运行迁移文件

```bash
go build . && ./mangosteen db migrate
# 或者
migrate -database "postgres://mangosteen:123456@pg-for-go-mangosteen:5432/mangosteen_dev?sslmode=disable" -source "file://$(pwd)/config/migrations" up
```

## 回滚迁移文件(可选)

```bash
go build . && ./mangosteen db migrate:down
# 或者
migrate -database "postgres://mangosteen:123456@pg-for-go-mangosteen:5432/mangosteen_dev?sslmode=disable" -source "file://$(pwd)/config/migrations" down 1
```

