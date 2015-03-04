---
layout: post
title: Drupal字符截取函数views_trim_text
created: 2011-11-30 01:15:22.000000000 +08:00
tags: web drupal php
---

Drupal的Views模块有一个很好用的字符截取函数views_trim_text，它的用法如下：

```php
views_trim_text($alter, $value)
```

**参数：**  
$alter

-  max_length: 字符串最大长度，超出部分将被截取
-  word_boundary: 以单词（Word）为边界来截取
-  ellipsis: 以单词（Word）为边界来截取，结尾以‘...’结束
-  html: 确保html标签的完整性

<!-- more -->

源代码：

```php
<?php
function views_trim_text($alter, $value) {
  if (drupal_strlen($value) > $alter['max_length']) {
    $value = drupal_substr($value, 0, $alter['max_length']);
    // TODO: replace this with cleanstring of ctools
    if (!empty($alter['word_boundary'])) {
      $regex = "(.*)\b.+";
      if (function_exists('mb_ereg')) {
        mb_regex_encoding('UTF-8');
        $found = mb_ereg($regex, $value, $matches);
      }
      else {
        $found = preg_match("/$regex/us", $value, $matches);
      }
      if ($found) {
        $value = $matches[1];
      }
    }
    // Remove scraps of HTML entities from the end of a strings
    $value = rtrim(preg_replace('/(?:<(?!.+>)|&(?!.+;)).*$/us', '', $value));

    if (!empty($alter['ellipsis'])) {
      $value .= '...';
    }
  }
  if (!empty($alter['html'])) {
    $value = _filter_htmlcorrector($value);
  }

  return $value;
}
?>
```

使用实例：

```php
$alter['max_length']=15;
$alter['ellipsis']=true;
$trim_title= views_trim_text($alter,$links->title); 
```
