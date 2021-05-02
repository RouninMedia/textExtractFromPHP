# textExtractFromPHP
**`textExtractFromPHP()`** extracts:

 - HTML `textNodes`
 - `title` attribute text
 - `alt` attibute text

from a PHP file.

When applied to this PHP file:

```
<?php

$Heading = txt($Page_Array[(count($Page_Array) - 1)]);

echo '
<h2 class="sb-restricted-access•safety-data-sheets»by»scotiaBeauty»»»mainHeading">Safety Data Sheets for '.$Heading.'</h2>
<p>Unfortunately, this information is not currently available online.</p>
<p>Please <a href="/contact-us/">contact Scotia Beauty</a> for this information.</p>
';

?>
```

**`textExtractFromPHP()`** will return:

```
Safety Data Sheets for Unfortunately, this information is not currently available online.
Please contact Scotia Beauty for this information.
```

____

## `textExtractFromPHP()` function

```
function textExtractFromPHP($Page_Text) {

  // PREPARE DOCUMENT
  $Page_Text = str_replace(['<?php', '?>', '\n'], '', $Page_Text);                  // REMOVE PHP FORMAT BOUNDARIES
  $Page_Text = preg_replace('/\[[^\]]+?\]/', '', $Page_Text);                       // REMOVE ALL ARRAY INDICES
  $Page_Text = preg_replace('/\$\{[^\}]+?\}/', '', $Page_Text);                     // REMOVE ALL DYNAMIC VARIABLES
  $Page_Text = str_replace('"', '\'', $Page_Text);                                  // STANDARDISE ALL QUOTES AS SINGLE QUOTES
  $Page_Text = preg_replace('/echo\s*\'/', 'echo\'', $Page_Text);                   // STANDARDISE ECHO STATEMENT START QUOTES
  $Page_Text = preg_replace('/\'\s*\;/', '\';', $Page_Text);                        // STANDARDISE ECHO STATEMENT END QUOTES
  

  // EXTRACT ECHOED MARKUP
  $Page_Text_Array = explode('echo\'', $Page_Text);
  array_shift($Page_Text_Array);
  
  for ($i = 0; $i < count($Page_Text_Array); $i++) {
  
    $Page_Text_Array[$i] = explode('\';', $Page_Text_Array[$i])[0];
  }
  
  $Page_Text = implode('', $Page_Text_Array);


  // REMOVE ALL HTML MARKUP APART FROM TEXT, TITLE TEXT AND ALT TEXT
  $Page_Text = preg_replace('/(title|alt)=\'([^\']+?)\'/', '> $2 <', $Page_Text);   // SAVE HTML title & alt ATTRIBUTES
  $Page_Text = preg_replace('/(<([^>]*?)>)/', '', $Page_Text);                      // REMOVE ALL HTML ELEMENT TAGS
  $Page_Text = preg_replace('/\'{0,2}\..*?[\'\;]{1,2}/', '', $Page_Text);           // REMOVE PHP INTERPOLATIONS
  $Page_Text = str_replace(['{', '\'', '}'], '', $Page_Text);                       // REMOVE ANY REMAINING PHP CRUFT


  // REFORMAT WHITESPACE
  $Page_Text = preg_replace('/\s+/', ' ', $Page_Text);
  $Page_Text = trim($Page_Text);

  return $Page_Text;
}

```

____

## Background Information...

### What is `ashSearch`?
`ashSearch` is the standard Search Module in `ash`.


### What is `ash`?

`ash` is a standard library of `da3sh`-based rich web components (or *modules*) which can be deployed across all `da3sh`-parsing environments (such as `ashiva`).
