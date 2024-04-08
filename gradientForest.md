# You'll be mad when installing R package `gradientForest`

To be clear, the title doesn't mean this package is bad. It just took me a whole day to install it without any useful guidance. So I wanna talk about how to install the package.

## What You Need for Installation

I recommend to use R under Version 4.3, because I met a error report with version incompatible using R 4.3.1 .
This package needs `gfortran` as the dependency. And `gfortran` has been included in `gcc`, so you can just install the `gcc` through Homebrew. But make sure you can remember the directory of `gcc`, which will be useful later.

## Start Installation

Now you launch your Rstudio, and type `install.package('gradientForest')`, and it tells you that there's a dependency package called `extendedForest`. A `Yes` is required to proceed the installation program. Then Rstudio starts to download the source code from the Internet and automatically compiles it. But wait! There is an ERROR! It says 

>/opt/R/whatever-the-path-is/gfortran : No such file or directory

So, what happened?

Actually you may need to do some configuration about your R `makeconf` file to point the directory to your `gfortran`. You can use `Command+Shift+G` to entry the hidden directory, which usually is `/Library/Frameworks/R.framework/Versions/your version/Resources/etc`, and you will see the `makeconf` file. This file can open with notebook easily.
Find the `FC = /opt/R/arm64/bin/gfortran`, now here is the problem. You may notice that in your computer, there is no such thing. So you need to redirect the directory to your own `gcc` files. For me, the `gfortran` directory is `/opt/homebrew/Cellar/gcc/13.2.0/bin/gfortran`. Now be aware that you MUST set the directory till the very end, which is the `gfortran` shortcut or `gfortran-13` executable file. And use the command `chmod 777 gfortran-13` in terminal opened in the `bin` directory above. After this step, you can restart R and try to install the packages again.

## Functional Test

If you successfully install the packages, now I think you better do a test in case the packages can't load properly. Maybe you can load them. But for me I met some errors.

>Library not loaded : @rpath/libgfortran.5.dylib

And it told me the file `extendedForest.so` was the one made the error happen. In my Mac, the directory is `/Library/Frameworks/R.framework/Versions/4.1-arm64/Resources/library/extendedForest/libs/extendedForest.so`. To solve the problem, use command `otool -L extendedForest.so` to check the `@rpath` which will be used soon. And you will see the `libgfortran.5.dylie` is needed. But where is it?
Go back to your `gcc` directory and go to `/opt/homebrew/Cellar/gcc/13.2.0/lib/gcc/13`, the you will see the `libgfortran.5.dylib` shortcut. Copy the directory and oopen terminal in the `extendedForest.so`'s directory, then type

>install_name_tool -change @rpath/libgfortran.5.dylib /your-gcc-libgfortran.5.dylib-path extendedForest.so

Hit ENTER and restart R, and it should work. Both `extendedForest` and `gradientForest` now can be loaded properly!

Good luck analysing!
