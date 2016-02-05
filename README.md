# cmake-superbuild-ninja-bug

Instructions:

Setup the build, generate the files
```
mkdir build/make build/ninja

pushd build/make
cmake ../../.
popd

pushd build/ninja
cmake ../../. -GNinja
popd
```

First we will build with the make system.
```
cd build/make
make
```

This is the output
```
Scanning dependencies of target foo
[  6%] Creating directories for 'foo'
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
-- Build files have been written to: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/make/foo-build
[ 37%] Performing build step for 'foo'
Scanning dependencies of target foo
[ 50%] Building CXX object CMakeFiles/foo.dir/src/foo.cpp.o
[100%] Linking CXX static library libfoo.a
[100%] Built target foo
[ 43%] Performing install step for 'foo'
[100%] Built target foo
Install the project...
-- Install configuration: ""
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/make/staging/lib/foo/libfoo.a
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/make/staging/lib/foo/foo-config.cmake
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/make/staging/lib/foo/foo-config-noconfig.cmake
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/make/staging/include/foo.h
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
-- Build files have been written to: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/make/bar-build
[ 87%] Performing build step for 'bar'
Scanning dependencies of target bar
[ 50%] Building CXX object CMakeFiles/bar.dir/main.cpp.o
[100%] Linking CXX executable bar
[100%] Built target bar
[ 93%] No install step for 'bar'
[100%] Completed 'bar'
[100%] Built target bar
```

If we build again, this is our output.

```
[  6%] Performing build step for 'foo'
[100%] Built target foo
[ 12%] Performing install step for 'foo'
[100%] Built target foo
Install the project...
-- Install configuration: ""
-- Up-to-date: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/make/staging/lib/foo/libfoo.a
-- Up-to-date: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/make/staging/lib/foo/foo-config.cmake
-- Up-to-date: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/make/staging/lib/foo/foo-config-noconfig.cmake
-- Up-to-date: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/make/staging/include/foo.h
[ 18%] Completed 'foo'
[ 50%] Built target foo
[ 56%] Performing configure step for 'bar'
-- Configuring done
-- Generating done
-- Build files have been written to: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/make/bar-build
[ 62%] Performing build step for 'bar'
[100%] Built target bar
[ 68%] No install step for 'bar'
[ 75%] Completed 'bar'
[100%] Built target bar
```

Note that in the case of the make generator.  The dependent target is reinstalled every time, even though it hasn't changed.
For large projects with multiple dependencies, this can lead to fairly substantial times for no-op builds.

Now build with ninja
```
ninja
```

This is the output
```
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
-- Build files have been written to: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/ninja/foo-build
[6/16] Performing build step for 'foo'
[1/2] Building CXX object CMakeFiles/foo.dir/src/foo.cpp.o
[2/2] Linking CXX static library libfoo.a
[7/16] Performing install step for 'foo'
[1/1] Install the project...
-- Install configuration: ""
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/ninja/staging/lib/foo/libfoo.a
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/ninja/staging/lib/foo/foo-config.cmake
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/ninja/staging/lib/foo/foo-config-noconfig.cmake
-- Installing: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/ninja/staging/include/foo.h
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
-- Build files have been written to: /media/data/home/lbuchy/work/cmake/cmake-ninja-install-out/build/ninja/bar-build
[14/16] Performing build step for 'bar'
[1/2] Building CXX object CMakeFiles/bar.dir/main.cpp.o
[2/2] Linking CXX executable bar
[16/16] Completed 'bar'
```
Subsequent calls to ninja do this:
```
[1/7] Performing build step for 'foo'
ninja: no work to do.
[2/4] Performing build step for 'bar'
ninja: no work to do.
```

The Ninja generator does better.   It doesn't reinstall dependent projects like the make generator.

However, what happens if we change something in a dependent project?
Lets change something in foo/src/foo.cpp, perhaps make it say "BAZ"

The output of ninja is now:
```
[1/7] Performing build step for 'foo'
[1/2] Building CXX object CMakeFiles/foo.dir/src/foo.cpp.o
[2/2] Linking CXX static library libfoo.a
[2/4] Performing build step for 'bar'
ninja: no work to do.
```

Notice that it recompiled foo, but it doesn't reinstall it.  
bar is not relinked either since it's dependent library technically didnt change.  We end up with a stale executable.

