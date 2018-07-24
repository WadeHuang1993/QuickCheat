# PHP 語法速查表

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

