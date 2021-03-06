# R-MKL
R 4.0.3 built with Intel C++ Compiler (icc) /Intel Fortran Compiler (ifort) / Math Kernel Library (MKL) for research purposes only.

# Build Status

[![Build Status](https://github.com/MitsuhaMiyamizu/R-MKL/workflows/r-mkl/badge.svg)](https://github.com/MitsuhaMiyamizu/R-MKL/actions?query=workflow%3Ar-mkl)
[![Build Status](https://github.com/MitsuhaMiyamizu/R-MKL/workflows/r-mkl-master/badge.svg)](https://github.com/MitsuhaMiyamizu/R-MKL/actions?query=workflow%3Ar-mkl-master)

# Inspired by

https://software.intel.com/content/www/us/en/develop/articles/using-intel-mkl-with-r.html

https://github.com/alexisph/high_performance_r

https://gist.github.com/cheuerde/8fb9fd0dc8c0eca17c16

https://aur.archlinux.org/cgit/aur.git/tree/PKGBUILD?h=r-mkl

https://blog.ajsuarez.com/posts/compiling-r-icc-mkl/

# Notes

According to the manual provided for developers by Intel, it's possible for you to mix openmp object files compiled with ```icc``` and ```ifort```, however, you cannot mix object files compiled by ```ifort``` and ```gfortran```, their examples are given below:

```
icc -qopenmp -c ibar.c
gcc -fopenmp -c gbar.c
ifort -qopenmp -c foo.f
ifort -qopenmp foo.o ibar.o gbar.o
```
So, we think it's possible to set LDFLAGS to ```qopenmp```, and since 19.1 supports that, we finally chose to pass this parameter to ```icc```, which may not only circumvent some issues during the compilation, but also comply with the Dev manual provided by Intel, we thought it might be more reasonable to choose ```qopenmp``` rather than ```fopenmp```, albeit according to the license of LLVM, ```libomp-dev``` is equivalent to openmp library published officially by Intel.

For more information, visit: 

[Using the openmp libraries](https://software.intel.com/content/www/us/en/develop/documentation/cpp-compiler-developer-guide-and-reference/top/optimization-and-programming-guide/openmp-support/openmp-library-support/using-the-openmp-libraries.html)

# Troubleshooting

1) https://software.intel.com/en-us/comment/1951508

2) https://community.intel.com/t5/Intel-oneAPI-Math-Kernel-Library/Build-R-with-Intel-MKL-shared-library/td-p/1034642?profile.language=en

3) https://community.intel.com/t5/Intel-C-Compiler/Error-when-compiling-R-from-source-code-ubuntu-18-04/td-p/1176401

4) https://aur.archlinux.org/packages/r-mkl/

5) https://jiangjun.netlify.app/post/compile-r-enable-mkl/

6) https://cran.r-project.org/doc/manuals/r-release/R-admin.html

7) https://community.intel.com/t5/Intel-oneAPI-Math-Kernel-Library/Build-R-with-Intel-MKL-shared-library/td-p/1034642?profile.language=en

8) https://stackoverflow.com/questions/46413691/r-make-install-of-3-4-1-on-unix-is-failing-test-reg-tests-1d/56957438

Note that the DRP's recommendation of ```date +%Z``` does not work with the 3.6 unit tests.

```
using icc to build it by uncomment the line # _CC="icc" and get:

...
icc -I../../src/extra -I/usr/include/tirpc -I. -I../../src/include -I../../src/include  -D_FORTIFY_SOURCE=2 -I../../src/nmath -DHAVE_CONFIG_H   -fopenmp -fpic  -O3 -fPIC -m64 -march=native -fp-model precise -fp-model source -I/opt/intel/mkl/include  -c arithmetic.c -o arithmetic.o
arithmetic.c(61): warning #274: declaration is not visible outside of function
  int matherr(struct exception *exc)
                     ^

arithmetic.c(63): error: pointer to incomplete class type is not allowed
      switch (exc->type) {
              ^

arithmetic.c(64): error: identifier "DOMAIN" is undefined
      case DOMAIN:
           ^

arithmetic.c(65): error: identifier "SING" is undefined
      case SING:
           ^

arithmetic.c(68): error: identifier "OVERFLOW" is undefined
      case OVERFLOW:
           ^

arithmetic.c(71): error: identifier "UNDERFLOW" is undefined
      case UNDERFLOW:
           ^

arithmetic.c(72): error: pointer to incomplete class type is not allowed
    exc->retval = 0.0;
    ^

compilation aborted for arithmetic.c (code 2)
...
```
**Delete this row**
```
sed -i '/^#undef HAVE_MATHERR/d' src/include/config.h.in
```
between configure and make will make it work.

# License

_Please read Intel Non-Commercial License for more information_

[Intel End User License Agreement for Developer](https://software.intel.com/content/dam/develop/external/us/en/documents/pdf/intel-developer-tools-eula-09-03-19.pdf)

[Intel Simplified Software License](https://software.intel.com/content/dam/develop/external/us/en/documents/pdf/intel-simplified-software-license.pdf)

***Warning***

_THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE._
