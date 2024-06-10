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
<br/>

|Syntax|Meaning|
|---|---|
| [ _captures_ ] ( _params﻿_ ) _specs_<sub>(opt)</sub> _exception_<sub>(opt)</sub> _trailing-type_<sub>(opt)</sub> { _body_ } |Lambda expression with parameter list|
| [ _captures_ ]  { _body_ } |Simplest form|

<br/>

**captures**
   <br/>
   <br/>
   The captures is a comma-separated list of zero or more captures, optionally beginning with the _capture-default_.
   The capture list defines the outside variables that are accessible from within the lambda function body.
   <br/>
   
   > [!TIP]
   > _Non-local variables and variables that have static [storage duration](https://en.cppreference.com/w/cpp/language/storage_duration)_ can be used in a lambda without being captured.

   > ``` c++
   > int g = 10;
   > int main(int argc, char** argv) {
   >     static int h = 20;
   >     int i = 30;
   >
   >     [] () {
   >         printf("%d", g);    // OK: variable 'g' is global
   >         printf("%d", h);    // OK: variable 'h' is static
   >         printf("%d", i);    // error: variable 'i' is a local variable
   >     }
   > }
   > ```

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
   
     _Examples:_
     <br/>
   
     ``` c++
       int val = 1;
       auto add_to = [val] (int i) { 
	        return i + val;
       };

       auto result = add_to(10);
       assert(result == 11);    // OK: val=1 was captured by-value by the lambda expression     
     ```

     ``` c++
       int val = 1;
       auto add_to = [val] (int i) { 
	        return i + val;
       };

       val = 10;

       auto result = add_to(10);
       assert(result == 11);    // OK: val=1 was captured by-value by the lambda expression
       assert(result == 20);    // error: result != 20
                                //        val=10 is not captured by the lambda because captures are done at the point the
                                //        lambda is declared, not when it's called!
     ```


   - Capture by-copy with an [initializer](https://en.cppreference.com/w/cpp/language/initialization)  
   
     _Syntax:_
     
     > [ _identifier initializer_ ] ( ) { }

     _Examples:_
     <br/>
   
     ``` c++
      int val = 7;
      auto add_to = [x=val] (int i) { 
           return i + x;
      };

      val = 10;                // change value of val to 10

      auto result = add_to(10);

      assert(result == 17);    // OK: val=7 was captured by-value and assigned to 'x'
      assert(result == 20);    // error: result != 20
                               //        val=10 is not captured by the lambda because captures are done at the point the
                               //        lambda is declared, not when it's called!
     ```
     
     ``` c++
      auto add_none = [x=0] (int i) { 
         return i + x;
      };

      auto result = add_none(10);
      assert(result == 10);    // OK: '0' was used and assigned to 'x'
     ```
    

   - Simple capture by-reference  
   
     _Syntax:_
     
     > [ **&**_identifier_ ] ( ) { }

     _Examples:_
     <br/>
   
     ``` c++
      int val = 7;
      auto add_to = [&val] (int i) { 
           return i + val;
      };

      auto result = add_to(10);
      assert(result == 17);    // OK: val=7 was captured by-reference
     ```
     
     ``` c++
      int val = 7;
      auto add_to = [&val] (int i) { 
           return i + val;
      };

      val = 10;

      auto result = add_to(10);
      assert(result == 20);    // OK: Since the lambda captured 'val' by-reference, the change to val=10 was visible to the lambda
      assert(result == 17);    // error: val already changed to 10 and is visible to the lambda since it was a capture by-reference
     ```

   - Capture by-reference with an [initializer](https://en.cppreference.com/w/cpp/language/initialization)  
   
     _Syntax:_
     
     > [ **&**_identifier_ _initializer_ ] ( ) { }

     _Examples:_
     <br/>
   
     ``` c++
      int val = 7;
      auto add_to = [&x=val] (int i) { 
           return i + x;
      };

      val = 10;                // change value of val to 10 and consequently x also changes since it is a reference to val

      auto result = add_to(10);

      assert(result == 20);    // OK: 'x' is a reference to 'val', so whatever happens to 'val', happens to 'x'
      assert(result == 17);    // error: val already changed to 10 and is visible to the lambda expression through variable'x'
     ```
     
     ``` c++
      auto add_none = [x=0] (int i) { 
         return i + x;
      };

      auto result = add_none(10);
      assert(result == 10);    // OK: '0' was used and assigned to 'x'
     ```

<br/>

    
> [!IMPORTANT]
> _**Captures are done at the point the lambda is declared, not when it's called!**_

<br/>
