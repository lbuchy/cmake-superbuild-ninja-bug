The Make and Ninja generators behave quite differently when dealing with ExternalProjects. I wanted to document the Ninja behaviour here since I would consider it buggy/un-intuitive.

Suppose we have a 'superbuild' project that has a hierarchy like this:
```
.
├── bar
│   ├── CMakeLists.txt
│   └── main.cpp
├── CMakeLists.txt
└── foo
    ├── CMakeLists.txt
    ├── include/
    └── src/
```

The top-level cmake file sets up two external projects, foo and bar. bar depends on foo. The foo project installs libraries into a staging path and bar pulls the configuration from that path to import the foo library (and headers).

If I use the make generator and build from the top cmake file. I see that foo is built and installed, then bar is built and linked to foo. 


```
Scanning dependencies of target foo
[ 6%] Creating directories for 'foo'
[ 12%] No download step for 'foo'
[ 18%] No patch step for 'foo'
[ 25%] No update step for 'foo'
[ 31%] Performing configure step for 'foo'
-- The C compiler identification is GNU 4.8.4
-- The CXX compiler identification is GNU 4.8.4
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/foo-build
[ 37%] Performing build step for 'foo'
Scanning dependencies of target foo
[ 50%] Building CXX object CMakeFiles/foo.dir/src/foo.cpp.o
[100%] Linking CXX static library libfoo.a
[100%] Built target foo
[ 43%] Performing install step for 'foo'
[100%] Built target foo
Install the project...
-- Install configuration: ""
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/staging/lib/foo/libfoo.a
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/staging/lib/foo/foo-config.cmake
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/staging/lib/foo/foo-config-noconfig.cmake
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/staging/include/foo.h
[ 50%] Completed 'foo'
[ 50%] Built target foo
Scanning dependencies of target bar
[ 56%] Creating directories for 'bar'
[ 62%] No download step for 'bar'
[ 68%] No patch step for 'bar'
[ 75%] No update step for 'bar'
[ 81%] Performing configure step for 'bar'
-- The C compiler identification is GNU 4.8.4
-- The CXX compiler identification is GNU 4.8.4
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/bar-build
[ 87%] Performing build step for 'bar'
Scanning dependencies of target bar
[ 50%] Building CXX object CMakeFiles/bar.dir/main.cpp.o
[100%] Linking CXX executable bar
[100%] Built target bar
[ 93%] No install step for 'bar'
[100%] Completed 'bar'
[100%] Built target bar
```


If I don't change anything and run it again, then I see the foo install step run, no changes are needed and then the bar build step run (bar doesn't get relinked).


```
[ 6%] Performing build step for 'foo'
[100%] Built target foo
[ 12%] Performing install step for 'foo'
[100%] Built target foo
Install the project...
-- Install configuration: ""
-- Up-to-date: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/staging/lib/foo/libfoo.a
-- Up-to-date: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/staging/lib/foo/foo-config.cmake
-- Up-to-date: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/staging/lib/foo/foo-config-noconfig.cmake
-- Up-to-date: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/staging/include/foo.h
[ 18%] Completed 'foo'
[ 50%] Built target foo
[ 56%] Performing configure step for 'bar'
-- Configuring done
-- Generating done
-- Build files have been written to: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/bar-build
[ 62%] Performing build step for 'bar'
[100%] Built target bar
[ 68%] No install step for 'bar'
[ 75%] Completed 'bar'
[100%] Built target bar
```


Now if I touch something in foo, we can see that foo is rebuilt. The library is reinstalled and bar is relinked. This results in the correct executable.


```
[ 6%] Performing build step for 'foo'
Scanning dependencies of target foo
[ 50%] Building CXX object CMakeFiles/foo.dir/src/foo.cpp.o
[100%] Linking CXX static library libfoo.a
[100%] Built target foo
[ 12%] Performing install step for 'foo'
[100%] Built target foo
Install the project...
-- Install configuration: ""
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/staging/lib/foo/libfoo.a
-- Up-to-date: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/staging/lib/foo/foo-config.cmake
-- Up-to-date: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/staging/lib/foo/foo-config-noconfig.cmake
-- Up-to-date: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/staging/include/foo.h
[ 18%] Completed 'foo'
[ 50%] Built target foo
[ 56%] Performing configure step for 'bar'
-- Configuring done
-- Generating done
-- Build files have been written to: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/bar-build
[ 62%] Performing build step for 'bar'
[ 50%] Linking CXX executable bar
[100%] Built target bar
[ 68%] No install step for 'bar'
[ 75%] Completed 'bar'
[100%] Built target bar
```


If I use the ninja generator then the behaviour is different. On the first invocation of 'ninja, foo is built and installed, then bar is built and linked to foo. 


```
[5/16] Performing configure step for 'foo'
-- The C compiler identification is GNU 4.8.4
-- The CXX compiler identification is GNU 4.8.4
-- Check for working C compiler using: Ninja
-- Check for working C compiler using: Ninja -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler using: Ninja
-- Check for working CXX compiler using: Ninja -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/ninja/foo-build
[6/16] Performing build step for 'foo'
[1/2] Building CXX object CMakeFiles/foo.dir/src/foo.cpp.o
[2/2] Linking CXX static library libfoo.a
[7/16] Performing install step for 'foo'
[1/1] Install the project...
-- Install configuration: ""
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/ninja/staging/lib/foo/libfoo.a
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/ninja/staging/lib/foo/foo-config.cmake
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/ninja/staging/lib/foo/foo-config-noconfig.cmake
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/ninja/staging/include/foo.h
[13/16] Performing configure step for 'bar'
-- The C compiler identification is GNU 4.8.4
-- The CXX compiler identification is GNU 4.8.4
-- Check for working C compiler using: Ninja
-- Check for working C compiler using: Ninja -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler using: Ninja
-- Check for working CXX compiler using: Ninja -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/ninja/bar-build
[14/16] Performing build step for 'bar'
[1/2] Building CXX object CMakeFiles/bar.dir/main.cpp.o
[2/2] Linking CXX executable bar
[16/16] Completed 'bar'
```


On subsequent invocations (with no changes to bar), there is no work to do. Checking that the installed files are there is not done. But it sure is quick to run a no-op build!


```
[1/7] Performing build step for 'foo'
ninja: no work to do.
[2/4] Performing build step for 'bar'
ninja: no work to do.
```


But if I touch a file in foo, then foo is rebuilt but not reinstalled. bar is not relinked and thus the bar executable is stale.


```
[1/7] Performing build step for 'foo'
[1/2] Building CXX object CMakeFiles/foo.dir/src/foo.cpp.o
[2/2] Linking CXX static library libfoo.a
[2/4] Performing build step for 'bar'
ninja: no work to do.
```
