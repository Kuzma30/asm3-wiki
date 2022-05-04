**HINWEIS: Die Bauanweisung in diesem Artikel ist veraltet. Assembly3 funktioniert jetzt mit der offiziellen FreeCAD-Version 0.19 und folgenden. Zusätzlich kann man jetzt das SolveSpave Solver-Backend durch `pip install py-slvs`** installieren. The Python wheel repository can be found [here](https://github.com/realthunder/slvs_py/).

~~At the moment of this writing, Assembly3 only works with a forked FreeCAD [branch](https://github.com/realthunder/FreeCAD/tree/LinkStage3). You need to first checkout this branch and [build](https://github.com/realthunder/FreeCAD/tree/LinkStage3#compiling) it yourself.~~

~~After that, checkout this repository directly inside the `Ext/freecad/` directory of your FreeCAD installation or build directory. Be sure to name the directory as **asm3**. The Assembly3 workbench supports multiple constraint solver backends. Currently, there are two backends available, `SolveSpace` and `SymPy + SciPy`, both of which have external dependency. The current focus is to get SolveSpace backend fully working first, with SymPy + SciPy serving as a reference implementation for future exploration. All backends are optional. But, you'll need at least one installed to be able to do constraint based assembling, unless you are fine with manually movement, which is actually doable because Assembly3 provides a powerful mouse dragger.~~

# SolveSpace

[SolveSpace](http://solvespace.com/) ist eine eigenständige CAD-Software mit hervorragender Baugruppenunterstützung. Ich meine es entspricht dem entgegengesetzten Designprinzip von FreeCAD, das groß, modular und vollständig erweiterbar ist. SolveSpace, auf der anderen Seite, ist schlank und kompakt und macht, was es kann, sehr gut. Aber wahrscheinlich wird man etwas finden, was man vermisst und man muss nach anderer Software zur Unterstützung suchen. Der Constraint-Solver von SolveSpace ist als eine kleine Bibliothek für die Integration durch Software von Drittanbietern verfügbar, was uns die Gelegenheit gibt, das Beste aus beiden Welten zu bringen.

Es gibt derzeit keine offizielle Python-Bindung von SolveSpace. Außerdem sind ein paar kleine Änderungen erforderlich, um die SolveSpace Baugruppen- Funktionalität in die Solver-Bibliothek zu integrieren. Meinen Zweig findet man [hier](https://github.com/realthunder/solvespace). Das Projektarchiv entpackt man im eigenen lokalen Dateisystem,

```
git clone https://github.com/realthunder/solvespace
cd solvespace
```

## Build for Ubuntu

To build the python binding only

```
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_PYTHON=On ..
make _slvs
make _slvs
```

If you have more than one version of Python installed, you may want to specify the desired version as follow

```
cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_PYTHON=On -DPYTHON_EXECUTABLE=/usr/bin/python3 ..
```

After compilation is done, copy `slvs.py` and `_slvs.so` from `build/src/swig/python/` to `asm3/py_slvs`, where `asm3` is the directory you install Assembly3 workbench. Be sure to create an empty file named `__init__.py` at `asm3/py_slvs`.

## Cross Compile for Windows

To build for Windows 64-bit, you have two options. This section shows how to cross compile for Windows on Ubuntu

```
apt-get install cmake mingw-w64
mkdir build_mingw
cd build_mingw
cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_PYTHON=On -DCMAKE_TOOLCHAIN_FILE=../cmake/Toolchain-mingw64.cmake ..
make _slvs
make _slvs
```
After finish, copy `slvs.py` and `_slvs.pyd` from `build/src/swig/python/` to `asm3/py_slvs`, where `asm3` is the directory you install Assembly3 workbench. Be sure to create an empty file named `__init__.py` at `asm3/py_slvs`.

## Build on Windows

To build on Windows, you should use Visual Studio 2013, the same one FreeCAD uses. Install CMake and Python. If you are building the 64-bit version, make sure you install the Python 64-bit version. I have only tested the build with Python 2.7.14 64-bit. You probably can use the python lib included in FreeCAD libpack by adding the libpack path to `PATH` environment variable. But it doesn't work for me somehow. CMake only found the debug version python lib in the libpack.

Download and extract the latest [swig](http://www.swig.org/download.html) to some where, and add the path to `PATH` environment variable. I haven't tested to build with the old version swig that's bundled with FreeCAD libpack.

Be sure to checkout all the submodules of slvs before building. None of them is actually used, but is still needed to satisfy CMake dependency checking,

```
cd asm3/slvs
git submodule update --init --recursive
```

Run CMake-gui, select a build directory. Add a `BOOL` type entry named `BUILD_PYTHON`, and set it to `true`. Then click `configure` and select Visual Studio 2013 Win64, which is what FreeCAD used. If done without error, click `generate`.

Finally, open the `solvespace.sln` file in the build directory. You only need to build two projects, first `slvs_static_excp`, and then `_slvs`. Once finished, copy the output at the following location to `asm/py_slvs`

```
asm/slvs/<your_build_directory>/src/swig/python/slvs.py
asm/slvs/<your_build_directory>/src/swig/python/Release/_slvs.pyd
```

If you want to build the Debug version, either download Python debug libraries, or put FreeCAD libpack directory in `PATH` environment variable before configuring CMake, so that CMake can find the debug version Python library. Once built, you must rename `_slvs.pyd` to `_slvs_d.pyd` before copying to `asm/py_slvs`

## Build for MacOS

The pre-build binary for MacOS is located at a [different](../tree/master/py_slvs_mac) sub-module, because MacOS python extension has the same name as Linux one. To build it yourself for use in FreeCAD App bundle, first you need to setup `Homebrew` according to this [wiki](https://www.freecadweb.org/wiki/CompileOnMac), and build FreeCAD App bundle.

Assuming you installed FreeCAD bundle at `~/some/place/FreeCAD.app`, then clone Assembly3 repository at `~/some/place/FreeCAD.app/Contents/Ext/freecad/`. And very importantly, make sure you name the clone directory as __asm3__. After that checkout `slvs` sub-module, and all of its own sub-modules.

```
cd ~/some/place/FreeCAD.app/Contents/Ext/freecad/asm3
git submodule update --init slvs
cd slvs
git submodule update --init --recursive
```

Use the following command to configure and build

```
mkdir build
cd build
cmake \
-DCMAKE_BUILD_TYPE=Release \
-DBUILD_PYTHON=1 \
-DPYTHON_EXECUTABLE:FILEPATH=/usr/local/opt/python@2/Frameworks/Python.framework/Versions/2.7/bin/python2.7 \
-DPYTHON_INCLUDE_DIR=/usr/local/opt/python@2/Frameworks/Python.framework/Headers/ \
-DPYTHON_LIBRARY=/usr/local/opt/python@2/Frameworks/Python.framework/Versions/2.7/lib/libpython2.7.dylib  \
-DPython_FRAMEWORKS=/usr/local/opt/python@2/Frameworks/Python.framework/ ..

make _slvs

make _slvs
```

After done, create a directory named `py_slvs_mac` under `asm3`, and copy out the results

```
cd ~/some/place/FreeCAD.app/Contents/Ext/freecad/asm3
mkdir py_slvs_mac
touch py_slvs_mac/__init__.py
cp slvs/build/src/swig/python/_slvs.so py_slvs_mac/
cp slvs/build/src/swig/python/slvs.py py_slvs_mac/
```

Finally, you must make `_slvs.so` relocatable in order to be able to load it in FreeCAD bundle, with the following command

```
cd py_slvs_mac
install_name_tool -id "_slvs.so" _slvs.so
install_name_tool -add_rpath "@loader_path/../../../../lib/" _slvs.so
install_name_tool -change \
    "/usr/local/opt/python@2/Frameworks/Python.framework/Versions/2.7/Python" "@rpath/Python" _slvs.so
```

The last command changes the linked library path to be relative to the bundle's dynamic library loader. In case you used a different `CMake` configuration, you can find out your linked library path using the following command

```
otool -L _slvs.so
```

Done, and you can fire up FreeCAD.app and try out Assembly3.

# SymPy + SciPy

The other constraint solver backend uses [SymPy](http://www.sympy.org/) and [SciPy](https://www.scipy.org/). They are mostly Python based, with some native acceleration in certain critical parts. The backend implementation models after SolveSpace's solver design, that is, symbolic algebraic + non-linear least square minimization. It can be considered as a python implementation of the SolveSpace's solver.

SciPy offers a dozen of different [minimization](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.minimize.html) algorithms, but most of which cannot compete with SolveSpace performance wise. The following list shows some non-formal testing result using default parameters with the sample [assembly](#create-a-super-assembly-with-external-link-array) described later

| Algorithmus               | Time                                                       |
| ------------------------- | ---------------------------------------------------------- |
| SolveSpace (als Referenz) | 0.006s                                                     |
| Nelder-Mead               | Not converge                                               |
| Powell                    | 7,8 s                                                      |
| CG                        | 10+37s <sup>[1](#f1)</sup>                                 |
| BFGS                      | 10+0.8 <sup>[1](#f1)</sup>                                 |
| Newton-CG                 | 10+61+0.5s <sup>[2](#f2),</sup><sup>[3](#f3)</sup>         |
| L-BFGS-B                  | 10+1.5s <sup>[1](#f1),</sup><sup>[3](#f3)</sup>            |
| TNC                       | 10+0.8s <sup>[1](#f1)</sup>                                |
| COBYLA                    | 0.2s <sup>[3](#f3)</sup>                                   |
| SLSQP                     | 10+0.3 <sup>[1](#f1),</sup><sup>[3](#f3)</sup>             |
| dogleg                    | 10+61+?s <sup>[2](#f2)</sup> Failed to solve, linalg error |
| trust-ncg                 | 10+61+1.5s <sup>[2](#f2)</sup>                             |

<b name="f1">[1]</b> Including Jacobian matrix calculation (10s in this test case), which is implemented using sympy lambdify with numpy.

<b name="f2">[2]</b> Including Hessian matrix calculation (61s in this test case), in addition to Jacobian matrix.

<b name="f3">[3]</b> The obtained solution contains small gaps in some of the coincidence constrained points. Incorrect use of the algorithm?

The reasons for writing this backend are,

* SolveSpace is under GPL, which is incompatible with FreeCAD's LGPL,
* To gain more insight of the solver system, and easy experimentation with new ideas due to its python based nature,
* For future extension, physics based simulation, maybe?

You'll need to install SymPy and SciPy for your platform.

```
pip install --upgrade sympy scipy
```

