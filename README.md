# Color Console

A lightweight header-only C++ library to bring colors to your Windows console. It has a very-easy-to-use API that frees you from the burden of setting and resetting with colors every time you make a call.

## Installation

Put `color.hpp` in the folder where you include headers.

## How to Use?

```c++
#include "include/color.hpp"
#include <iostream>

int main()
{
    std::cout << dye::aqua("Hello, World !") << std::endl;

    return 0;
}
```

