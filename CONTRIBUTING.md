# Contributing to Code Potent Projects

The code standard used in Code Potent's ClassicPress plugins is near identical to the code standard used in WordPress plugins, **however,** _there are a handful of notable differences._ To ensure your pull requests aren't held up or rejected, please carefully follow the standard.

Things like **Yoda conditions?** _Not allowed._ How about **extraneous horizontal spacing?** _Nope._ What about **PHP/HTML stew?** _Absolutely not._ These are just a few of the things not allowed in Code Potent projects; see the following list for exact differences. If you are thinking about creating a substantial pull request, get in touch first to ensure it is inline with where the plugin is going _before_ doing any work on it.

___

### 1. Extraneous Spacing

Extraneous _horizontal_ spacing is not desired. For example, the following code:

```php
if ( ! $condition ) {
	some_function( $arg1, $arg2, $arg3 );
}
```
...should be rewritten as:
```php
if (!$condition) {
	some_function($arg1, $arg2, $arg3);
}
```

___

### 2. Commingling PHP & HTML

PHP files should tend to have a single, long-form opening `<?php` tag. A closing PHP tag `?>` is omitted. Jumping back and forth from PHP to HTML is not allowed; instead, HTML markup is enclosed in single-quotes and echoed. For example, the following code:

```php
<p><?php esc_html_e($some_value); ?></p>
<p><?php esc_html_e($another_value); ?></p>
```
...should be rewritten as:
```php
echo '<p>'.esc_html($some_value).'<p>';
echo '<p>'.esc_html($another_value).'<p>';
```
If you need pretty source, use `\n` in double-quotes to create breaks:
```php
echo '<p>'.esc_html($some_value).'<p>'."\n";
echo '<p>'.esc_html($another_value).'<p>'."\n";
```

___

### 3. Single Quotes

Single-quotes are preferred for most cases. HTML attributes and _escape characters_ such as `\n`, `\r`, and `\t` are an exception which should always use double quotes. For example, the following code:

```php
echo "<p class='some-class'>Some string of text.</p>";
echo "<p class='some-class'>".$this_value."</p>";
echo "<p class='some-class'>{$that_value}</p>";
```
...should be rewritten as:
```php
echo '<p class="some-class">Some string of text.</p>';
echo '<p class="some-class">'.esc_html($this_value).'</p>';
echo '<p class="some-class">'.esc_html($that_value).'</p>';
```
___

### 4. CSS Selectors

CSS selectors use hyphens `-` rather than underscores `_`. Selectors are prefixed in a consistent way to minimize the possibility of CSS collisions. For example, the following code:

```css
.some_class_selector,
#some_id_selector {
    color:#f00;
}
```
...should be rewritten as:
```css
.prefix-some-class-selector,
#prefix-some-id-selector {
    color:#f00;
}
```

___

### 5. Code Comments

If your pull request is a simple one-line bug-fix or an otherwise small change, it may not be necessary to comment the code. For larger pull requests, comment your work. Good comments not only describe what the code is doing, they also describe _why_ the code is doing it.

___

### 6. Yoda Conditions

Lazy type checking and _Yoda conditions_ are never used. Always use strict type checking to prevent false positives. For example, the following code:
```php
if (true == $the_force) {
    use_the_force();
}
```
...should be rewritten as:
```php
if ($the_force === true) {
    use_the_force();
}
```

___

### 7. Strict Type Comparisons

Always prefer `===` over `==` to ensure that the data you're testing is of the correct type. Strict checking should also be used for arrays. For example, the following code:

```php
if ($my_var == 'some string') {
    // Do something.
}

if ($my_var == true) {
    // Do something.
}

$my_array = ['some value', 'another value'];
if (in_array('some string', $my_array)) {
    // Do something.
}
```
...should be rewritten as:
```php
if ($my_var === 'some string') {
    // Do something.
}

if ($my_var === true) {
    // Do something.
}

$my_array = ['some value', 'another value'];
if (in_array('some string', $my_array), true) {
    // Do something.
}
```

___

### 8. Array Notation

Shorthand notation is used for all arrays. Instead of `array()`, use `[]`. For example, the following code:

```php
$my_array = array($val1, $val2, $val3);
```
...should be rewritten as:
```php
$my_array = [$val1, $val2, $val3];
```
...or...
```php
$my_array = [
    $val1,
    $val2,
    $val3,
    ];
```

___

### 9. Tabs not Spaces

Code is tabbed, not spaced. If values are being aligned vertically, spaces are preferred between items.

___

### 10. Initialization of Variables

All variables must be initialized in some way before use.

___

### 11. Warnings, Notices, and Errors

Warnings, notices, and errors may not be hidden in any way; this includes the use of `@` to supress notices.

___

### 12. PHP Version

The code must be compatible with PHP 7.x.x environments and it must be backward compatible with the lowest version of PHP that ClassicPress currently supports.

___

### 13. Consistent Case

**Pascal Case**

Class names and namespace declarations use _Pascal Case_ in which each word begins with a capital letter and the words are not separated. For example, the following lines depict this case.
```php
namespace MyNamespace;
class MySweetClass{}
new MySweetClass();
```

**Snake Case**

Constants, variables, functions, methods, and properties use _Snake Case_ in which each word is separated by an underscore. Constants use all uppercase letters only; all others use lowercase letters only. Here are some examples:
```php
const SIMPLE_CONSTANT = 'some value';
define('ANOTHER_CONSTANT', 'some value');
class MySweetClass {
    public $my_var = 10;
    public function __construct() {
    }
    public function get_items() {
    }
}

function just_some_random_function() {
    $my_var = true;
    // Do stuff.
}
```

___

### 14. Sanitize Early, Escape Late

All input must be **sanitized early**, meaning _as soon as it is received_. All output must be **escaped late**, meaning right where it is displayed. 

___

### 15. Internationalization

All displayable texts, with the exception of internal slugs, must be run through the appropriate translation functions. If dealing with number-related texts such as _0 items_, _1 item_, _2 items_, etc, correct pluralizaion must be accounted for.

___

### 16. Path & URL Constants

The plugins provide constants for all the common paths and URLs. Use these constants rather than executing core functions to find the same values.

___

### 17. Fail Early and Fail Often

Classes, methods, and functions should be designed to _fail early and fail often_. This keeps the code more performant and prevents deep-nesting of conditionals. Consider the following contrived function which is designed to retrieve some sort of items when the user is visiting a dashboard page:

```php
function prefix_display_items() {

    // Get items.
    $items = get_items();
    
    // if (!current_user_can('manage_options')) {
        return;
    } else {
        // Check is user is in dashboard.
        if (!is_admin()) {
            return; // Not in dashboard; bail!
        } else {
            // Do something with $items.
            echo '<ul>';
            foreach ($items as $item) {
                echo '<li>'.esc_html__($item['title']).'</li>';
            }
            echo '</ul>';
        }
    }
}
```
The above code _does work_, but, it has a couple of issues. First, if the user is not in the dashboard or if the user doesn't have suitable permission, the call to `get_items()` is needless overhead. Second, if you practice _fail early, fail often_, all those `else` clauses become needless, reducing the nesting of code. The following is a rewrite of the above function with fewer lines and more performant code:
```php
function prefix_display_items() {

    // Insufficient permission? Bail immediately.
    if (!current_user_can('manage_options')) {
        return;
    }
    
    // User not in dashboard? Bail immediately.
    if (!is_admin()) {
        return;
    }
    
    // Get and print the items.
    $items = get_items();
    echo '<ul>';
    foreach ($items as $item) {
        echo '<li>'.esc_html__($item['title']).'</li>';
    }
    echo '</ul>';
}
```

___

### 18. Scripts & Styles

Scripts and styles are only loaded for pages or views in which they are actually used. Scripts and styles are locally stored and non-minified.
