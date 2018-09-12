# Color Console

A lightweight header-only C++ library to bring colors to your Windows console with a very-easy-to-use API that frees you from the burden of setting and resetting screen colors every time you make a call.

<img src="image/tech.png" width="600"/>

## Contents

- [Installation](#user-content-instllation)
- [Getting Started](#user-content-getting-started)
- [Why Use It?](#user-content-why-use-it)
- [A Real Example](#user-content-a-real-example)
- [How to Use](#user-content-how-to-use)

## Installation

Put [`color.hpp`](include/color.hpp) in the folder where you include headers.

*For Windows. C++11 support required. C++14 or above recommended.*

## Getting Started

```c++
#include "../include/color.hpp"
#include <iostream>

int main()
{
    std::cout << dye::aqua("Hello, World!") << std::endl;
    return 0;
}
```

You are seeing `Hello, World!` in aqua.

<img src="image/hello.png" width="150"/> 

*Try saying [Hello, World](examples/hello.cpp) yourself.*

## Why Use It?

<img src="image/why.png" width="450"/>

1. **No need to reset :**  most solutions on the market work like manipulators, which *constantly* require you to reset the screen color after you set it. While this traditional approach is also offered in this library in the `hue` namespace

   ```c++
   cout << "When in doubt, wear " << hue::red << "red"  << hue::reset << "." << endl;
   ```

   it can be boring to do so. Why not just `dye` the object

   ```c++
   cout << "When in doubt, wear " << dye::red("red") << "." << endl;
   ```

2. **Object-oriented :**  you may `dye` an object and save it for later (re)use

   ```c++
   auto green = dye::green("green");
   cout << "I saw "<< green << " trees, " << green << " bushes." << endl;
   ```

3. **`dye` anything :** `int` `double` `char` `std::string` ...

   ```c++
   cout << "Take the " << dye::blue("Blue") << " Line and then "
        << "catch Bus " << dye::yellow(42 + 7 % 8) << "."<< endl;
   ```

   In fact, you can `dye` any object for which `operator<<` is properly defined. Suppose we have

   ```c++
   struct DoubleVector;
   ostream & operator<<(ostream &, const DoubleVector &);
   ```

    we are free to `dye`

    ```c++
   cout << dye::purple(DoubleVector{3.14, 2.72}) << endl;
    ```


4. **`+`  dyed objects, even colors differ :**

   ```c++
   cout << dye::light_red('A') + dye::light_blue('B') + dye::light_green('C') << endl;
   ```

5. **Extra support for strings :**  be it `std::string` or C-style strings, dyed or undyed, you can mix them up without caring about their types.

   ```c++ 
   const char ca[] = "ca";
   string str = "str";
   cout << "[ " + dye::aqua(ca) + " | " + dye::aqua(str) + " ]" << endl;
   ```

6. **Convenient and extensible API :**  say `colorize` an object according to the parameter, or quickly `inverse` the color

   ```c++
   double a = 88.88;
   cout << dye::colorize(a, a >= 0 ? "red" : "green").inverse() << endl;
   ```

*Try the [above cases](examples/use.cpp) yourself.*

## A Real Example

With [Color Console](include/color.hpp), we implement an [auto marker](examples/mark.cpp) which highlights keywords given in a watch list and colorizes numbers as well. The key function is

```c++
using namespace std;

auto mark(const string & str, string color)
{
    istringstream iss(str);
    auto marked = dye::vanilla("");
    for (string line; getline(iss, line); marked += "\n") {
        istringstream lineiss(line);
        for (string text; lineiss >> text; marked += " ") {
            string pre, word, post;
            // split a text into 3 parts: word in middle, and punctuations around it
            separate(text, pre, word, post);
            marked += pre;
            if (is_keyword(word))
                marked += dye::colorize(word, color).inverse();
            else if (is_number(word))
                marked += dye::colorize(word, color);
            else
                marked += word;
            marked += post;
        }
    }
    return marked;
}
```

To mark the introductory paragraph of a tech news

```c++
cout << mark(tech_news, "light_red") << endl;
```

You will see

<img src="image/tech.png" width="600"/> 

As another example in which we mark both keywords and numbers

```c++
cout << mark(stock_news, "yellow") << endl;
```

We are having

<img src="image/stock.png" width="600"/> 

*For the details, see the [full implementation](examples/mark.cpp).*

## How to Use

- **Color Tags**

  - **Single**
    - ***Basic***    `black` `blue` `green` `aqua` `red` `purple` `yellow` `white` `grey`
    - ***Light***    `light_[basic]` *e.g.*  `light_red` (*note: no* `light_black` `light_white` `light_grey`)
    - ***Bright***    `bright_white`
  - **Background**
    - `on_[single]` *e.g.*  `on_light_aqua`
  - **Compound**
    - `[single]_on_[single]` *e.g.*  `light_red_on_bright_white`
  - **Special**
    - `vanilla` do nothing  *i.e.* in current console color

- **Namespace `dye`  :**  object-oriented solution

    <img src="image/how.png" width="450"/>

    - `dye::[color_tag](object)` generates a dyed `object` ready for colorized output
    
       ```c++
       auto a = dye::on_light_yellow(42);
       cout << a << endl;
       ```
    
    - You may use `+` or `+=` to construct a chain of dyed objects. Colors may differ, but object types must be the same.
    
        ```c++
        using vec = DoubleVector;
        
        auto b = dye::red(vec{1, 2, 3});
        b = b + dye::blue(vec{4, 5, 6});
        b += dye::green(vec{7, 8, 9});
        cout << b << endl;
        ```
    
    -  Rules for strings even more flexible. You may `+` any compatible strings, even those undyed ones.
    
        ```c++
        cout << dye::on_white(string("strings")) + " are " +
                dye::on_white("more") + string(" flexible") << endl;
        ```
    
    - `dye::colorize(object, [color_tag])` dyes the `object` with `[color_tag]` 
    
        ```c++
        cout << dye::colorize("grape", "purple") << endl;
        ```
    
    - `dye::inverse(dyed)` gets a new object in inverse color. `dyed.inverse()` does that in place.
    
        ```c++
        cout << dye::inverse(dye::red("red")) << endl;
        
        auto contrast = dye::vanilla("contrast");
        cout << contrast.inverse() << endl;
        ```

- **Namespace** `hue`

