# PHP 語法速查表

## 在單引號前面加上斜線(\\')

我需要在所有的單引號 `'` 前加一個反斜線 `\`，使它們變成 `\'`，我如何能夠通過正則表達式來實現？

我同樣希望能夠將 `"` 轉換成 `\"`，將 `\`轉換成 `\\`。

函數 [addslashes()](http://php.net/manual/zh/function.addslashes.php)能夠實現這種操作。

```php
<?php

$str = "Is your name O'reilly?";

// 輸出： Is your name O\'reilly?
echo addslashes($str);

$str = 'You should use "PHP" to develop web';

// 輸出：You should use \"PHP\" to develop web
echo addslashes($str);
```

使用 addslashes() 的例子是當你要往數據庫中輸入數據時。例如，將名字 O'reilly 插入到資料庫中，為了避免 SQL Injection 這就需要對`'`進行轉義。

另外，防範 SQL Injection 強烈建議使用 DBMS 指定的轉義函數（比如 MySQL 是 mysqli_real_escape_string()，PostgreSQL 是 pg_escape_string()），但是如果你使用的 DBMS 沒有一個轉義函數，並且使用`\`來轉義特殊字符，你可以使用 addslashes() 函數。

請參閱函數 [mysql_escape_string()](http://php.net/manual/zh/function.mysql-escape-string.php)。

最後，還可以用函數 [stripslashes()](http://php.net/manual/zh/function.stripslashes.php)來去掉反斜線。

## Array
### array_pop

移除陣列最後一個元素，並回傳被移除的元素。

#### 語法：
```
array_pop(&目標陣列)
```
#### 範例：
```php
$segment = array (
  0 => 'admin',
  1 => 'index',
);

$b = array_pop($segment);

echo $b;

// ouput: index

print_r($segment);

// output:
/* 
Array
(
    [0] => admin
)
*/
```

### array_shift

移除陣列第一個元素，並回傳被移除的元素。

#### 語法：
```
array_shift(&目標陣列)
```
#### 範例：

```php
$segment = array (
  0 => 'admin',
  1 => 'index',
);

$b = array_shift($segment);

echo $b;

// ouput: admin

print_r($segment);

// output:
/* 
Array
(
    [0] => index
)
*/
```

### array_column

array_column — 返回陣列中指定的一欄位（縱列）

#### 語法：
```
array_column(目標陣列, 欄位名稱)
```
#### 範例：
```php
$records = array(
    array(
        'id' => 2135,
        'first_name' => 'John',
        'last_name' => 'Doe',
    ),
    array(
        'id' => 3245,
        'first_name' => 'Sally',
        'last_name' => 'Smith',
    ),
    array(
        'id' => 5342,
        'first_name' => 'Jane',
        'last_name' => 'Jones',
    )
);

$first_names = array_column($records, 'first_name');
print_r($first_names);

// output:
/* Array
(
    [0] => John
    [1] => Sally
    [2] => Jane
)
*/
 ```
 
 ### array_slice
 
 依照指定的索引與長度取得陣列內容物。
 
 #### 語法：
 ```
 array_slice(目標陣列, 起始索引, 保留長度);
 ```
 #### 範例：
 ```php
$segments = array (
    0 => '_sub',
    1 => 'admin',
    2 => 'common',
    3 => 'news',
);

$a = array_slice($segments, 3); // 取得前三個元素之後的元素
// output:
/*
array (
  0 => 'news',
)
*/

$b = array_slice($segments, 1, 2); // 取得從第一個所引算起的兩個元素
// output:
/*
array (
  0 => 'admin',
  1 => 'common',
)
*/


 ```
## JSON

### json_encode
json_encode() 非常好用，很適合拿來與 JavaScript 協同運作，
例如直接將資料庫查詢出來的陣列資料，轉換成 JSON 格式，提供給 JavaScript 來存取，
或透過 AJAX 技巧作資料交換等。

### json_encode 只能處理 UTF-8 編碼的資料，
若在 BIG5 的環境，若資料中包含中文字，在使用 json_encode() 前，
記得要先利用 iconv() 或 mb_convert_encoding() 轉碼一下，
單一變數資料要轉碼，應該沒有什麼大問題，
但若是陣列資料要轉碼，可以利用 array_walk() 作處理，
底下提供一個作法，供參考使用：
```php
$aSomeData = array_walk(function($data) {
    return mb_convert_encoding($data, "UTF-8", "BIG-5");
});
echo json_encode($aSomeData);
```

### json_decode


[json_decode](http://www.php.net/manual/en/function.json-decode.php)() 預設是將 JSON 資料，轉換成 PHP 的預設標準類別 stdClass 物件，

什麼是 stdClass 可以參考底下這篇文章：
[PHP stdClass: Storing Data in an Object Instead of an Array](http://www.webmaster-source.com/2009/08/20/php-stdclass-storing-data-object-instead-array/)

而更好用的是**第二個參數**，可以再將 stdClass 物件，轉換成關聯式陣列，好樣的 PHP。

```php
$json = '{"a":1,"b":2,"c":3,"d":4,"e":5}';

var_dump(json_decode($json));
var_dump(json_decode($json, true));
```
以上範例會輸出：
```php
object(stdClass)#1 (5) {
    ["a"] => int(1)
    ["b"] => int(2)
    ["c"] => int(3)
    ["d"] => int(4)
    ["e"] => int(5)
}

array(5) {
    ["a"] => int(1)
    ["b"] => int(2)
    ["c"] => int(3)
    ["d"] => int(4)
    ["e"] => int(5)
}
```

## 特殊
### call_user_func_array

主要用在函數名不確定，參數不確定的情況下，PHP 提供的一種通過傳遞不同參數來調用不同方法的工具。

#### 語法：
```
call_user_func_array (mixed callable , array 參數)
```
#### 範例：
1.直接調用方法
```php
function say($word){
    echo $word;
}
call_user_func_array('say', array('Hello PHP'));
```
2.調用對像中的方法
```php
class S{
    public function __construct(){
        
    }
    public static function say1($word){
        echo $word;
    }
    public function say($word){
        echo $word;
    }
}
/*使用方式一：無需實例化，調用類別的静态方法*/
call_user_func_array(array('S', 'say1'), array('Hello PHP'));

/*使用方式二：實例化後，調用類別的方法*/
$s = new S();

call_user_func_array(array($s, 'say2'), array('Hello PHP'));
call_user_func_array(array(new S(), 'say2'), array('Hello PHP'));
```


