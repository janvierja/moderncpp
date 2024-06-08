# <a name="main"></a>Learning Modern C++

June 7, 2024

Author:

* [Janvier Anonical](http://www.linkedin.com/in/janvierja)

This is a living document and it that aids the author in keeping up-to-date with modern C++ (C++11 and newer).
The code in this document has been tested and compiled with GCC 13.2.0 built by the MSYS2.

Let's begin.

* [Lambda Expressions](#lambda-expressions)

<br/>
<br/>

### <a name="lambda-expressions"></a>Lambda Expressions

Full detail on lambda can be found at here: [Lambda expressions](https://en.cppreference.com/w/cpp/language/lambda) at the [C++ Reference](https://en.cppreference.com/w/) site.

The illustration below shows the parts of a lambda syntax:
<br/>
<br/>
<img src="lambda-with-labels.jpg" width="60%" height="60%">
<br/>
<br/>

..and the syntax

> **[_captures_] _front-attr_<sub>(opt)</sub> (_params﻿_) _specs_<sub>(opt)</sub> _exception_<sub>(opt)</sub> _back-attr_<sub>(opt)</sub> _trailing-type_<sub>(opt)</sub> { _body_ }**
<br/>

1. **captures**
   <br/>
   <br/>
   The captures is a comma-separated list of zero or more captures, optionally beginning with the _capture-default_.
   The capture list defines the outside variables that are accessible from within the lambda function body.
   <br/>
   <br/>
   The only _capture-defaults_ are `&` and `=`.

   |||
   |---|---|
   |`&`|implicitly capture the used variables with automatic storage duration by reference|
   |`=`|implicitly capture the used variables with automatic storage duration by copy|
   
3. *front attribute spec*
4. *parameter list*
5. *specifier*
6. *exception spec*
7. *back attribute spec*
8. *trailing return type*
9. *lambda body*
