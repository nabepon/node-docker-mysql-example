Nodeでdocker+MySQLを使うexample

## コンテナ 作成

```
docker run -v /var/lib/mysql --name sample_data busybox
docker run --volumes-from sample_data --name sampleDB -e MYSQL_ROOT_PASSWORD=pswd -d -p 3306:3306 mysql:5.6
```

※参考

- https://qiita.com/astrsk_hori/items/e3d6c237d68be1a6f548
- https://qiita.com/akinoriikeda/items/db4291dc4db3aa7c4b55

## DB 作成

```
docker exec -it sampleDB bash
mysql -u root -p
CREATE DATABASE my_db;
```

## node で DB 接続

```
yarn add mysql
```

```
var mysql = require('mysql');
var connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'pswd',
  database: 'my_db',
});

connection.connect();

connection.query('SELECT 1 + 1 AS solution', function(error, results, fields) {
  if (error) throw error;
  console.log('The solution is: ', results[0].solution);
});

connection.end();
```

## エラーが出た場合

最新の mysql のバージョンだと以下のエラーがでる

```
Error: ER_NOT_SUPPORTED_AUTH_MODE
```

その場合、mysql で以下を実行することで解消できる

https://github.com/mysqljs/mysql/issues/1507#issuecomment-385224467

```
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'pswd';
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'pswd';
```
