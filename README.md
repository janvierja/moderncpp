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
<img src="images\lambda-with-labels.jpg" width="60%" height="60%">
<br/>
<br/>

...whose general form is

<br/>

> **[_captures_] _front-attr_<sub>(opt)</sub> (_params﻿_) _specs_<sub>(opt)</sub> _exception_<sub>(opt)</sub> _back-attr_<sub>(opt)</sub> _trailing-type_<sub>(opt)</sub> { _body_ }**
<br/>

<img src="images\lambda-1.JPG" width="3%" height="3%">**captures**
   <br/>
   <br/>
   The captures is a comma-separated list of zero or more captures, optionally beginning with the _capture-default_.
   The capture list defines the outside variables that are accessible from within the lambda function body.
   <br/>
   <br/>
   The _capture-defaults_ are only `&` and `=`.

   |capture-default|desc|
   |---|---|
   |`&`|capture by **reference**; implicitly capture the used variables with automatic [storage duration](https://en.cppreference.com/w/cpp/language/storage_duration) by reference|
   |`=`|capture by **copy**; implicitly capture the used variables with automatic [storage duration](https://en.cppreference.com/w/cpp/language/storage_duration) by copy|
   <br/>
   
   The syntax of an individual capture in **captures** is
   <br/>

   |Syntax|Desc|Example|
   |---|---|---|
   | [ _identifier_ ] |simple capture by-copy|`[x] () {}`|
   | [ _identifier initializer_ ] <sub>since C++14</sub> |capture by-copy with an [initializer](https://en.cppreference.com/w/cpp/language/initialization)|`[x=1] () {}`|
   | [ _**&** identifier_ ] |simple capture by-reference|`[&x] () {}`|
   | [ _**&** identifier initializer_ ] <sub>since C++14</sub> |capture by-reference with an [initializer](https://en.cppreference.com/w/cpp/language/initialization)|`[&x=y] () {}`|
   | [ this ] |simple capture by-reference of the current object|`[this] () {}`|
   | [ *this ] <sub>since C++17</sub> |simple capture by-copy of the current object|`[*this] () {}`|
   <br/>

   
   <br/>

   - Simple capture by-copy

     _Syntax:_
   
     > [ _identifier_ ] ( ) { }
   
     _Example:_
     <br/>
   
     ``` c++
       int value = 1;
       auto add_to = [value] (int i) { 
	        return i + value;
       };

       printf("%d", add_to(10));

       // prints 11
     
     ```


   - Capture by-copy with an [initializer](https://en.cppreference.com/w/cpp/language/initialization)  
   
     _Syntax:_
     
     > [ _identifier initializer_ ] ( ) { }

     _Example:_
     <br/>
   
     ``` c++
      int value = 7;
      auto add_to = [x=value] (int i) { 
           return i + x;
      };

      value = 10;

      printf("%d", add_to(10));

      // prints 17
      // It's not 20 because captures are done at the point the lambda is declared, not when it's called!
     ```
     
     ``` c++
      auto add_none = [x=0] (int i) { 
         return i + x;
      };

      printf("%d", add_none(10));

      // prints 10
     ```
    

   - Simple capture by-reference  
   
     _Syntax:_
     
     > [ **&**_identifier_ ] ( ) { }

     _Example:_
     <br/>
   
     ``` c++
      int value = 7;
      auto add_to = [&value] (int i) { 
           return i + x;
      };

      printf("%d", add_to(10));

      // prints 17
     ```
     
     ``` c++
      int value = 7;
      auto add_to = [&value] (int i) { 
           return i + value;
      };

      value = 10;

      printf("%d", add_to(10));

      // prints 20
     ```
<br/>

    
> [!IMPORTANT]
> _**Captures are done at the point the lambda is declared, not when it's called!**_

<br/>
