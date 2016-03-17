# ZEND_API 使用实例
[TOC]

## zend_print_zval_r

```c
ZEND_API void zend_print_zval_r(zval *expr, int indent)
```

---

**C Code :**

```c
/* {{{ proto k1_func(array $arr) : void
   Print the PHP array elements. */
PHP_FUNCTION(k1_func)
{
    zval *arr;

    ZEND_PARSE_PARAMETERS_START(1, 1)
        Z_PARAM_ARRAY(arr)
    ZEND_PARSE_PARAMETERS_END();

    zend_print_zval_r(arr, 0);
}
/* }}} */
```

**PHP Code :**

```php
<?php
$arr = [1, 2, 'name' => 'Kinner'];

k1_func($arr);
?>
```

**Output :**

```shell
Array
(
    [0] => 1
    [1] => 2
    [name] => Kinner
)
```

## zend_array_count

```c
ZEND_API uint32_t zend_array_count(HashTable *ht);
```

---

**C Code :**

```c
/* {{{ proto k1_func(array $arr) : int
   Count the number of elements in a an array. */
PHP_FUNCTION(k1_func)
{
    zval *arr;

    ZEND_PARSE_PARAMETERS_START(1, 1)
        Z_PARAM_ARRAY(arr)
    ZEND_PARSE_PARAMETERS_END();

    RETURN_LONG(zend_array_count(Z_ARRVAL_P(arr)));
}
/* }}} */
```

**PHP Code :**

```php
<?php
$arr = [1, 2, 'name' => 'Kinner'];

$count = k1_func($arr);

var_dump($count);
?>
```

**Output :**

```shell
int(3)
```

## zend_str_tolower

```c
ZEND_API void ZEND_FASTCALL zend_str_tolower(char *str, size_t length)
```

---

**C Code :**

```c
/* {{{ proto k1_func(string $str) : string
   Return the lower case string. */
PHP_FUNCTION(k1_func)
{
    zend_string *str;

    char *ret;

    ZEND_PARSE_PARAMETERS_START(1, 1)
        Z_PARAM_STR(str)
    ZEND_PARSE_PARAMETERS_END();

    ret = (char *)estrndup(ZSTR_VAL(str), ZSTR_LEN(str));

    zend_str_tolower(ret, ZSTR_LEN(str));

    RETURN_STRINGL(ret, ZSTR_LEN(str));
}
/* }}} */

以下是更高效一点的写法:

/* {{{ proto k1_func(string $str) : string
   Return the lower case string. */
PHP_FUNCTION(k1_func)
{
    zend_string *str;

    ZEND_PARSE_PARAMETERS_START(1, 1)
        Z_PARAM_STR(str)
    ZEND_PARSE_PARAMETERS_END();

    ZVAL_STRINGL(return_value, ZSTR_VAL(str), ZSTR_LEN(str));

    zend_str_tolower(Z_STRVAL_P(return_value), ZSTR_LEN(str));
}
/* }}} */
```

**PHP Code :**

```php
<?php 
$str = 'Hello Kinner!';

$str_lc = k1_func($str);

echo 'str: ';
var_dump($str);

echo 'str_lc: ';
var_dump($str_lc);
?>
```

**Output :**

```shell
str: string(13) "Hello Kinner!"
str_lc: string(13) "hello kinner!"
```

## zend_hash_find, zend_hash_str_find

```c
ZEND_API zval* ZEND_FASTCALL zend_hash_find(const HashTable *ht, zend_string *key);
ZEND_API zval* ZEND_FASTCALL zend_hash_str_find(const HashTable *ht, const char *key, size_t len);
```

---

**C Code :**

```c
/* {{{ proto k1_func($var_name) : void
   Print the PHP'nd variable value. */
PHP_FUNCTION(k1_func) {
    zend_string *var_name;
    zval *var_value;

    ZEND_PARSE_PARAMETERS_START(1, 1)
        Z_PARAM_STR(var_name)
    ZEND_PARSE_PARAMETERS_END();

 // var_value = zend_hash_str_find(&EG(symbol_table), ZSTR_VAL(var_name), ZSTR_LEN(var_name));
    var_value = zend_hash_find(&EG(symbol_table), var_name);

    if (var_value) {
        php_printf("var_name: %s, var_type: %d, var_value: %s\n", ZSTR_VAL(var_name),
                   Z_TYPE_P(var_value), Z_STRVAL_P(Z_INDIRECT_P(var_value)));
    } else {
        php_error_docref(NULL TSRMLS_CC, E_ERROR, "Cannot find variable: $%s", ZSTR_VAL(var_name));
    }
}
/* }}} */
```

**PHP Code :**

```php
<?php
$kinner_name_long_get = 'Hello Kinner Name Long Get!';

k1_func('kinner_name_long_get');
?>
```

**Output :**

```shell
var_name: kinner_name_long_get, var_type: 15, var_value: Hello Kinner Name Long Get!
```

## zend_hash_index_find

```c
ZEND_API zval* ZEND_FASTCALL zend_hash_index_find(const HashTable *ht, zend_ulong h);
```

---

**C Code :**

```c
/* {{{ proto k1_func(array $arr[, int index]) : void
   Print the PHP'nd variable value. */
PHP_FUNCTION(k1_func) {
    zend_array *arr;
    zend_long index = 0;
    zval *var_value;

    ZEND_PARSE_PARAMETERS_START(1, 2)
        Z_PARAM_ARRAY_HT(arr)
        Z_PARAM_OPTIONAL
        Z_PARAM_LONG(index)
    ZEND_PARSE_PARAMETERS_END();

    var_value = zend_hash_index_find(arr, index);

    if (var_value) {
        php_printf("$arr[%ld].type: %d, $arr[0].value: %s\n", index, Z_TYPE_P(var_value), Z_STRVAL_P(var_value));
    } else {
        php_error_docref(NULL TSRMLS_CC, E_ERROR, "Cannot find variable: $arr[%ld]", index);
    }
}
/* }}} */
```

**PHP Code :**

```php
<?php
$arr = ['one', 'two', 'three', 'four' => 'four'];

k1_func($arr, 1);
?>
```

**Output :**

```shell
$arr[1].type: 6, $arr[0].value: two
```

