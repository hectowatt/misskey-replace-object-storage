## usernameはpostgresユーザー名、dbnameはデータベース名に置き換える
user_name=$1
db_name=$2
old_domain=$3
new_domain=$4

## docker composeで接続
docker compose exec db psql -U $user_name -d $db_name

## 旧ドメインのファイル数を確認
select count(*) from drive_file where "url" like '$old_domain%';
## 旧ドメインを1件だけ確認
select * from drive_file where "url" like '%$old_domain%' LIMIT 1;
## 上記の検索結果の内容を表示
select * from drive_file where "id" = 'search_result_id';

## まずは１件だけ更新してみる
UPDATE drive_file 
SET url = REPLACE(url, '$old_domain', '$new_domain'), "thumbnailUrl" = REPLACE("thumbnailUrl", '$old_domain', '$new_domain')
WHERE "id" = 'search_result_id';


## 問題ないようであれば一括更新
UPDATE drive_file 
SET url = REPLACE(url, '$old_domain', '$new_domain'), "thumbnailUrl" = REPLACE("thumbnailUrl", '$old_domain', '$new_domain')
WHERE "url" LIKE '%$old_domain%'
and
"thumbnailUrl" LIKE '%$old_domain%';