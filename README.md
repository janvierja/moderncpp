# <a name="main"></a>Learning Modern C++

June 7, 2024

Author:

* [Janvier Anonical](http://www.linkedin.com/in/janvierja)

This is a living document and it that aids the author in keeping up-to-date with modern C++ (C++11 and newer).
The code in this document has been tested and compiled with GCC 13.2.0 built by the MSYS2.

Let's begin.

* Lambda
  * [Lambda Expressions](#lambda-expressions)
  * [Generic Lambdas](#lambda-generic)
  * [Lambdas with No Capture](#lambda-no-capture)
  * [Mutable Lambda](#lambda-mutable)

<br/>
<br/>

## Lambda
<br/>

### <a name="lambda-expressions"></a>Lambda Expressions

Full detail on lambda can be found at here: [Lambda expressions](https://en.cppreference.com/w/cpp/language/lambda) at the [C++ Reference](https://en.cppreference.com/w/) site.
<br/>

|Syntax|Meaning|
|---|---|
| [ _captures_ ] ( _params﻿_ ) _specs_<sub>(opt)</sub> _exception_<sub>(opt)</sub> _trailing-type_<sub>(opt)</sub> { _body_ } |Lambda expression with parameter list|
| [ _captures_ ]  { _body_ } |Simplest form|

<br/>

<a name="lambda-captures"></a>**1. captures**
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
   | [ _identifier_ ] |simple [capture by-copy](#capture-by-copy)|`[x] () {}`|
   | [ _identifier initializer_ ] <sub>since C++14</sub> |[capture by-copy with an initializer](#capture-by-copy-with-init)|`[x=1] () {}`|
   | [ _**&** identifier_ ] |simple [capture by-reference](#capture-by-ref)|`[&x] () {}`|
   | [ _**&** identifier initializer_ ] <sub>since C++14</sub> |[capture by-reference with an initializer](#capture-by-ref-with-init)|`[&x=y] () {}`|
   | [ this ] |simple [capture by-reference of the current object](#capture-by-ref-current-obj)|`[this] () {}`|
   | [ *this ] <sub>since C++17</sub> |simple [capture by-copy of the current object](#capture-by-copy-current-obj)|`[*this] () {}`|
   <br/>

   
   <br/>

   - Simple capture by-copy<a name="capture-by-copy"></a>

     _Syntax:_

     `[ identifier ] ( ) { }`
   
     _Example 1:_
   
     ``` c++
       int val = 1;
       auto add_to = [val] (int i) { 
	        return i + val;
       };

       auto result = add_to(10);
       assert(result == 11);    // OK: val=1 was captured by-value by the lambda expression     
     ```

     _Example 2:_

     ``` c++
       int val = 1;
       auto add_to = [val] (int i) {  // Captures local copy
	        return i + val;
       };

       val = 10;                  // change value of val to 10 has no impact on `add_to`

       auto result = add_to(10);
       assert(result == 11);      // OK: val=1 was captured by-value by the lambda expression
       assert(result == 20);      // error: result != 20
                                  //        val=10 is not captured by the lambda because captures are performed when
                                  //        the lambda is created, not when it's called! So here val=1 was captured.
     ```


   - Capture by-copy with an [initializer](https://en.cppreference.com/w/cpp/language/initialization)  <a name="capture-by-copy-with-init"></a>
   
     _Syntax:_
     
     `[ identifier initializer ] ( ) { }`<sub>since C++14</sub>

     _Example 1:_
   
     ``` c++
      int val = 7;
      auto add_to = [x=val] (int i) {  // Captures local copy
           return i + x;
      };

      val = 10;                // change value of val to 10 has no impact on `add_to`

      auto result = add_to(10);

      assert(result == 17);    // OK: val=7 was captured by-value and assigned to 'x'
      assert(result == 20);    // error: result != 20
                               //        val=10 is not captured by the lambda because captures are performed
                               //        when the lambda is created, not when it's called! Here, val=7 was captured.
     ```
     _Example 2:_
     
     ``` c++
      auto add_none = [x=0] (int i) { 
         return i + x;
      };

      auto result = add_none(10);
      assert(result == 10);    // OK: '0' was used and assigned to 'x'
     ```
     
     _Example 3:_
     
     ``` c++
      int getValue() { return 77; };
     
      auto add_value = [x=getValue()] (int i) { 
         return i + x;
      };

      auto result = add_value(10);
      assert(result == 87);    // OK: getValue() returns 77 and added to 10
     ```
<br/>
    
> [!IMPORTANT]
> _**Captures are performed when the lambda is created, not when it's called!**_
<br/>

   - Simple capture by-reference  <a name="capture-by-ref"></a>
   
     _Syntax:_
     
     `[ &identifier ] ( ) { }`

     _Example 1:_
   
     ``` c++
      int val = 7;
      auto add_to = [&val] (int i) {
           return i + val;
      };

      auto result = add_to(10);
      assert(result == 17);    // OK: val=7 was captured by-reference
     ```

     _Example 2:_
     
     ``` c++
      int val = 7;
      auto add_to = [&val] (int i) {  // Captures reference to val 
           return i + val;
      };

      val = 10;                // has impact on `add_to` since reference was captured when the lambda was created

      auto result = add_to(10);
      assert(result == 20);    // OK: Since the lambda captured 'val' by-reference, the change to val=10 was visible to the lambda
      assert(result == 17);    // error: val already changed to 10 and is visible to the lambda since it was a capture by-reference
     ```
     _Example 3:_
     
     ``` c++
        int val = 7;

        auto add_to = [&val](int i) {
            auto ret = i + val;
            val = val + val;    // modify val
            return ret;
        };

        auto result = add_to(10);
     
        assert(result == 17);    // OK
        assert(val == 14);       // val was modified inside `add_to` lambda since it was captured by-reference
    ```

   - Capture by-reference with an [initializer](https://en.cppreference.com/w/cpp/language/initialization)  <a name="capture-by-ref-with-init"></a>
   
     _Syntax:_
     
     `[ &identifier initializer ] ( ) { }`<sub>since C++14</sub>

     _Example:_
   
     ``` c++
      int val = 7;
      auto add_to = [&x=val] (int i) {   // `x` is a reference to captured `val`
           return i + x;
      };

      val = 10;                // has impact on `add_to` since reference was captured when the lambda was created

      auto result = add_to(10);

      assert(result == 20);    // OK: 'x' is a reference to 'val', so whatever happens to 'val', happens to 'x'
      assert(result == 17);    // error: val already changed to 10 and is visible to the lambda expression through variable'x'
     ```
     
   - Simple capture by-reference of the current object <a name="capture-by-ref-current-obj"></a>
    <br/>
     _Syntax:_
     
     `[ this ] ( ) { }`

     _Example:_
     
``` c++
	class MyClass {
	  private:
		int _val{0};
		void mod_val(int val) { this->_val = val; }
		int get_val() const { return this->_val; }

		void modify_me()  {
			auto modify = [this] () {      // Current object captured by-reference therefore
				mod_val(100);          // modification of _val impacts `this` object
			};
		
			modify();                      // Perform the modification of _val
		}
	  public:
		int call() {

			modify_me();    // Attempt to modify _val to 100

			auto add = [this] (int i) {        // Current object captured by-reference

                                assert(this->_val == 100); // _val is 100 as set by `modify_me()`
                                assert(get_val() == 100);  // _val is 100 as set by `modify_me()`

				this->_val = i + get_val();
				return get_val();      
			};
		
			return add(30);
		}
	};
 
	int main(int argc, char** argv) {
 
		MyClass b;
	
		auto result = b.call();
		
		assert(result == 130);    // OK: result is 130 because _val modified to 100 at modify_me and 30 added to it in call()
	 
	}
``` 
   - Simple capture by-copy of the current object <a name="capture-by-copy-current-obj"></a>
    <br/>
     _Syntax:_
     
     `[ *this ] ( ) { }`<sub>since C++17</sub>

     _Example:_
     
``` c++
	class MyClass {
	  private:
		int _val{0};
		void mod_val(int val) { this->_val = val; }
		int get_val() const { return this->_val; }
	
		void modify_me()  {
			auto modify = [*this] () mutable {  // Current object captured by-copy therefore
				mod_val(100);               // modification of _val does not impact `this` object
	
				assert(get_val() == 100);   // The copied *this object is the one that gets modified
				assert(this->_val == 100);  // The original `this` object is untouched
			};
		
			modify();
		}
	
	  public:
		
		int call() {

			modify_me();    // Attempt to modify _val to 100

			auto add = [this] (int i) {           // Current object capture by-reference
	
				assert(this->get_val() == 0); // _val is still 0; modify_me did not impact _val
				assert(this->_val == 0);      // _val is still 0; modify_me did not impact _val
	
				return i + get_val();
			};
		
			return add(30);
		}
	};
	
	
	
	int main(int argc, char** argv) {
	
	    MyClass b;
	
	    auto result = b.call();
	
	    assert(result == 30);    // OK: The call to modify_me() worked on a copy of this so changes did not reflect here

	}
```   

<br/>

<a name="lambda-params"></a>**2. params**
   <br/>
   <br/>
   The [parameter list](https://en.cppreference.com/w/cpp/language/function#Parameter_list) of `operator()`
   <br/>

<a name="lambda-specs"></a>**3. specs**
   <br/>
   <br/>
   A list of the following specifiers, each specifier is allowed at most once in each sequence.
   <br/>   
   |specifier|effect|
   |---|---|
   |`mutable`|Allows [body](#lambda-body) to modify the objects captured by copy, and to call their non-const member functions.|
   |`constexpr`<sub>since C++17</sub>|Explicitly specifies that `operator()` is a [constexpr function](https://en.cppreference.com/w/cpp/language/constexpr#constexpr_function). If `operator()` satisfy all constexpr function requirements, `operator()` will be constexpr even if **constexpr** is not present.|
   <br/>
   
<a name="lambda-exception"></a>**4. exception**
   <br/>
   <br/>
   The exception specification
   <br/>
   
<a name="lambda-exception"></a>**5. traling type**
   <br/>
   <br/>
   `-> ret`, where `ret` specifies the return type.
   <br/>
   
<a name="lambda-body"></a>**5. body**
   <br/>
   <br/>
   The function body.
   <br/>   
   
### <a name="lambda-generic"></a>Generic Lambdas

This lambda is compiled for different parameter types

   ``` c++
	// define a generic lambda object
	auto twice = [](const auto x) {
		return x + x;
	};

	// lambda compiled for different parameter types
	
	auto i = twice(3);                 // i is int => 6
	auto d = twice(1.7);               // d is double => 3.4
	auto s = twice(std::string("hi")); // s is std::string => "hihi"
   ```

   <br/>   
   
### <a name="lambda-no-capture"></a>Lambdas with No Capture

Lambdas with no captures can be used as an ordinary _function pointer_.
That is,
<br/>

``` c++
    int (*fp)(int) = [](int i) { return i; };
```
<br/>

_Example:_

<br/>

``` c++
       #include <cstdlib>
    
       int atexit( void (*func)(void) );
       ...
       ...
       int main() {
           std::atexit([]() {
                              std::cout << "goodbye!" << std::endl;
                       });
       }
``` 

<br/>

Unary operator ```+``` forces the conversion of a capture-less lambda to a function pointer

<br/>

``` c++
	void foo(std::function<void()> f) { f(); }
	void foo(void (*f)()) { f(); }
	
	int main(int argc, char** argv) {
	
	    foo( []() {}  );  // error: (ambiguous) more than one instance of overloaded function "foo" matches the argument list:C/C++(308)
                              //            function "foo(std::function<void ()> f)"
                              //            function "foo(void (*f)())"
                              //            argument types are: (lambda []()->void)
	    foo( +[]() {} );  // OK: not ambiguous since forces conversion to a function pointer void (*f)(), that is, function "foo(void (*f)())"
	
	}
```
<br/>

### <a name="lambda-mutable"></a>Mutable Lambdas

<br/>

Lambdas are _stateless_ by default, that is, values captured by-value cannot be modified, however, the spec ```mutable``` makes the lambda _stateful_, that is, modification to values captured by-value are allowed.<br/>

Consider the example below.

<br/>

``` c++
#include <iostream>
#include <iterator>
#include <vector>
#include <algorithm>

// `mutable` lambda definition
auto changed = [prev = 0](auto cur) mutable {
	bool changed = cur != prev;
	prev = cur;
	return changed;
	};

// class with member function template
class MyInts {
	std::vector<int> v_;
  public:
	MyInts(std::vector<int> v) {
		this->v_ = v;
	}
	template<typename Func>
	void for_each(Func fn) {
		for(int i : v_) {
			if(fn(i)) {
				std::cout << i << " ";
			}
		}
	}
};

// non-member function template
template<typename Func>
void for_each(Func fn, std::vector<int> v) {
	for(int i : v) {
		if(fn(i)) {
			std::cout << i << " ";
		}
	}
}

int main() {

	std::vector<int> v{ 7, 42, 42, 0, 3, 3, 7 };
	
	// standard algos like `copy_if` takes callable by value meaning they
	// operate with their own copy of the lambda named `changed`
	
	std::cout << "by copy_if" << std::endl;

	// Prints 7 42 0 3 7
	std::copy_if(v.begin(), v.end(),
		std::ostream_iterator<int>{std::cout, " "},
		changed);

	std::cout << std::endl;

	// Still prints 7 42 0 3 7
	std::copy_if(v.begin(), v.end(),
		std::ostream_iterator<int>{std::cout, " "},
		changed);

	std::cout << std::endl;
	std::cout << "----" << std::endl;
	std::cout << "by member function template" << std::endl;

        // Prints 7 42 0 3 7
	MyInts my_ints(v);
	my_ints.for_each(changed);   // takes callable by value
	                             // `my_ints` has its own copy of the lambda

	std::cout << std::endl;

        // Still Prints 7 42 0 3 7
	MyInts my_ints_2(v);
	my_ints_2.for_each(changed); // takes callable by value
	                             // `my_ints_2` has its own copy of the lambda
	std::cout << std::endl;
	std::cout << "----" << std::endl;
	std::cout << "by non-member function template" << std::endl;

        // Prints 7 42 0 3 7
	for_each(changed, v);   // takes callable by value
	                        // `my_ints` has its own copy of the lambda

	std::cout << std::endl;

        // Still Prints 7 42 0 3 7
	for_each(changed, v);   // takes callable by value
	                        // `my_ints` has its own copy of the lambda

	std::cout << std::endl;
	std::cout << "----" << std::endl;
	std::cout << "by plain ol' call to the lambda `changed`" << std::endl;

	// Prints 7 42 0 3 7
	for (int i : v) {
		if (changed(i)) {
			std::cout << i << " ";
		}
	}

	std::cout << std::endl;

	// Uh-oh! Prints 42 0 3 7
	// It has detected last value "7" on previous for-each loop 
	for (int i : v) {
		if (changed(i)) {
			std::cout << i << " ";
		}
	}

	std::cout << "   // Uh-oh! It has detected last value '7' (above) on previous for-each loop" << std::endl;

	return 0;
}
```

<br/>
The output of the code block above is as follows:

```
by copy_if
7 42 0 3 7
7 42 0 3 7
----
by member function template
7 42 0 3 7
7 42 0 3 7
----
by non-member function template
7 42 0 3 7
7 42 0 3 7
----
by plain ol' call to the lambda `changed`
7 42 0 3 7
42 0 3 7    // Uh-oh! It has detected last value '7' (above) on previous for-each loop
```


