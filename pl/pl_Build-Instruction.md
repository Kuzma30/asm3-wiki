**UWAGA: Instrukcja kompilacji zawarta w tym artykule jest przestarzała. Środowisko Złożenie 3 teraz działa z oficjalną wersją FreeCAD 0.19 i nowszą. Ponadto możesz teraz zainstalować zaplecze solvera SolveSpave za pomocą polecenia `install py-slvs`**. Repozytorium Python Binding of Solvespace Constraint Solver można znaleźć [na GitHub](https://github.com/realthunder/slvs_py/).

~~ W chwili pisania tego tekstu środowisko Złożenie 3 działa tylko z rozwidloną [gałęzią](https://github.com/realthunder/FreeCAD/tree/LinkStage3) FreeCAD. Najpierw należy zapoznać się z tą gałęzią i [zbudować](https://github.com/realthunder/FreeCAD/tree/LinkStage3#compiling) ją samodzielnie.~~

~~ Następnie sprawdź to repozytorium bezpośrednio wewnątrz katalogu instalacji lub kompilacji FreeCAD `Ext/freecad/`. Upewnij się, że katalog został nazwany **asm3**. Środowisko pracy Złożenie 3 obsługuje wiele wiązań backends solvera. Obecnie dostępne są dwa rozwiązania, `SolveSpace` i `SymPy + SciPy`, z których obydwa mają zależności zewnętrzne. Teraz skupiamy się na tym, aby najpierw w pełni działał backend SolveSpace, a SymPy + SciPy służyły jako implementacja referencyjna dla przyszłych zastosowań. Wszystkie rozwiązania są opcjonalnie. Powinieneś zainstalować co najmniej jedno, aby móc wykonywać złożenia na podstawie wiązań, chyba że lubisz pracę samodzielną, która w rzeczywistości daje rezultat zadowalający, ponieważ Złożenie 3 zapewnia potężne wsparcie dla myszki.~~

# SolveSpace

[SolveSpace](http://solvespace.com/) jest samodzielnym oprogramowaniem CAD ze znakomitym wsparciem dla złożeń. W mojej opinii ma ono odwrotną zasadę projektowania niż FreeCAD, który jest duży, modularny i w pełni rozszerzalny. Z drugiej strony, środowisko SolveSpace, jest niewielkie i kompaktowe i robi bardzo dobrze to do czego zostało stworzone. Ale, Ty najprawdopodobniej znajdziesz coś, czego brakuje i będziesz musiał szukać innego oprogramowania do pomocy. Solwer wiązań SolveSpace jest dostępny jako mała biblioteka do integracji przez oprogramowanie firm trzecich, co daje nam możliwość wykorzystania tego, co najlepsze z obu światów.

W tej chwili nie ma oficjalnego połączenia SolveSpace z Pythonem. Poza tym wymagana jest niewielka modyfikacja, aby wprowadzić funkcjonalność złożeń SolveSpace do biblioteki solwera. Mój fork możesz znaleźć [tutaj](https://github.com/realthunder/solvespace). Sprawdź repozytorium w swoim systemie plików,

```
git clone https://github.com/realthunder/solvespace
cd solvespace
```

## Kompilacja dla Ubuntu

Aby zbudować tylko wiązanie środowiska Python

```
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_PYTHON=On ..
make _slvs
```

Jeśli masz zainstalowaną więcej niż jedną wersję środowiska Python, możesz określić żądaną wersję w następujący sposób

```
cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_PYTHON=On -DPYTHON_EXECUTABLE=/usr/bin/python3 ..
```

Po zakończeniu kompilacji należy skopiować `slvs.py` i `_slvs.so` z katalogu `build/src/swig/python/` do `asm3/py_slvs`, gdzie `asm3` jest katalogiem, w którym zainstalowano środowisko Złożenie 3. Upewnij się, że utworzyłeś pusty plik o nazwie `__init__.py` w `asm3/py_slvs`.

## Kompilacja Skrośna dla Windows

Zbudować dla 64-bitowego Windows możesz na dwa sposoby. Ta sekcja opisuje jak wykonać na Ubuntu kompilację skrośną dla Windows

```
apt-get install cmake mingw-w64
mkdir build_mingw
cd build_mingw
cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_PYTHON=On -DCMAKE_TOOLCHAIN_FILE=../cmake/Toolchain-mingw64.cmake ..
make _slvs
```
Po ukończeniu skopiuj `slvs.py` i `_slvs. yd` z `build/src/swig/python/` do `asm3/py_slvs`gdzie `asm3` jest katalogiem, w którym zainstalowałeś środowisko pracy Złożenie 3. Upewnij się, że utworzyć pusty plik o nazwie `__init__.py` na `asm3/py_slvs`.

## Kompilacja dla Windows

Aby budować w systemie Windows, należy użyć Visual Studio 2013, tego samego, z którego korzysta FreeCAD. Zainstaluj CMake i Python. Jeśli budujesz wersję 64-bitową, upewnij się, że zainstalowałeś 64-bitową wersję Pythona. Testowałem kompilację tylko z 64-bitowym Pythonem 2.7.14. Prawdopodobnie możesz użyć bibliotek Pythona zawartych w pakiecie FreeCAD libpack, dodając ścieżkę libpack do zmiennej środowiskowej `PATH`. Ale to nie działa dla mnie, z jakiegoś powodu. CMake znalazł w libpack tylko debugowalną wersję biblioteki Python.

Pobierz i rozpakuj gdzieś najnowszy [swig](http://www.swig.org/download.html), a następnie dodaj ścieżkę do zmiennej środowiskowej `PATH`. Nie testowałem budowania za pomocą starej wersji swig, która jest dołączona do FreeCAD libpack.

Przed zbudowaniem należy wyprowadzić wszystkie moduły wymagane przez slvs. Żaden z nich nie jest w rzeczywistości używany, ale nadal jest wymagany, aby spełnić wymagania sprawdzania zależności CMake

```
cd asm3/slvs
git submodule update --init --recursive
```

Uruchom CMake-gui, wybierz katalog kompilacji. Dodaj wpis typu `BOOL` o nazwie `BUILD_PYTHON`i ustaw go na wartość `true`. Następnie kliknij `configure` i wybierz Visual Studio 2013 Win64, którego używa FreeCAD. Jeśli wykonało się bez błędu, kliknij `generate`.

Na koniec otwórz plik `solvespace.sln` z katalogu kompilacji. Musisz zbudować dwa projekty, najpierw `slvs_static_excp`, a potem `_slvs`. Po zakończeniu skopiuj dane wyjściowe z miejsca kompilacji do `asm/py_slvs`

```
asm/slvs/<your_build_directory>/src/swig/python/slvs.py
asm/slvs/<your_build_directory>/src/swig/python/Release/_slvs.pyd
```

Jeśli chcesz zbudować wersję debugowalną, pobierz debugowalne biblioteki Pythona, lub umieść katalog libpack FreeCAD w zmiennej środowiskowej `PATH` przed skonfigurowaniem CMake, aby CMake mógł znaleźć debugowalną wersję biblioteki Pythona. Po zbudowaniu, przed skopiowaniem do `asm/py_slvs`, musisz zmienić nazwę `_slvs.pyd` na `_slvs_d.pyd`

## Kompilacja dla MacOS

Prekompilowana wersja binarna dla systemu MacOS znajduje się w [innym](../tree/master/py_slvs_mac) module podrzędnym, ponieważ rozszerzenie Pythona dla systemu MacOS ma taką samą nazwę jak dla Linuksa. Aby zbudować go samodzielnie do użycia w pakiecie FreeCAD App, należy najpierw skonfigurować `Homebrew` zgodnie z tym [wiki](https://www.freecadweb.org/wiki/CompileOnMac), i zbudować pakiet FreeCAD App.

Zakładając, że zainstalowałeś pakiet FreeCAD w `~/jakies/miejsce/FreeCAD.app`, a następnie sklonowałeś repozytorium Złożenie 3 w `~/jakies/miejsc/FreeCAD.app/Contents/Ext/freecad/`. I bardzo ważne, upewnij się, że nazwałeś katalog kopii __asm3__. Po tym wyprowadź moduł podrzędny `slvs` i wszystkie jego zależności.

```
cd ~/some/place/FreeCAD.app/Contents/Ext/freecad/asm3
git submodule update --init slvs
cd slvs
git submodule update --init --recursive
```

Użyj następujących poleceń, aby uruchomić konfigurację i kompilację

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
```

Po wykonaniu utwórz katalog o nazwie `py_slvs_mac` wewnątrz `asm3`, i skopiuj tam wyniki

```
cd ~/some/place/FreeCAD.app/Contents/Ext/freecad/asm3
mkdir py_slvs_mac
touch py_slvs_mac/__init__.py
cp slvs/build/src/swig/python/_slvs.so py_slvs_mac/
cp slvs/build/src/swig/python/slvs.py py_slvs_mac/
```

Na koniec musisz uczynić `_slvs.so` przenośnym, aby móc go załadować go w pakiecie FreeCAD, wykonując następujące polecenie

```
cd py_slvs_mac
install_name_tool -id "_slvs.so" _slvs.so
install_name_tool -add_rpath "@loader_path/../../../../lib/" _slvs.so
install_name_tool -change \
    "/usr/local/opt/python@2/Frameworks/Python.framework/Versions/2.7/Python" "@rpath/Python" _slvs.so
```

Ostatnie polecenie zmienia ścieżkę skonsolidowanej biblioteki tak, aby była względna w stosunku do programu ładującego biblioteki dynamiczne pakietu. Jeśli użyłeś innej konfiguracji `CMake`, możesz sprawdzić ścieżkę skonsolidowanych bibliotek, używając następującego polecenia

```
otool -L _slvs.so
```

Gotowe i możesz uruchomić FreeCAD.app i wypróbować Złożenie 3

# SymPy + SciPy

Drugi solver rozwiązywania wiązań używa [SymPy](http://www.sympy.org/) oraz [SciPy](https://www.scipy.org/). Są one w większości oparte na Pythonie, z pewną naturalną akceleracją w niektórych krytycznych miejscach. Implementacja backendu wzoruje się na SolveSpace, czyli symboliczna algebraiczna + nieliniowa metoda najmniejszych kwadratów. Można ją uznać za pythonową implementację solwera SolveSpace.

SciPy oferuje kilkanaście różnych algorytmów [minimalizacji](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.minimize.html), ale większość z nich nie może konkurować z SolveSpace pod względem wydajności. Poniższa lista przedstawia niektóre nieformalne wyniki testów z użyciem domyślnych parametrów z przykładowym [złożeniem](#create-a-super-assembly-with-external-link-array) opisanym później

| Algorytm                      | Czas wykonywania                                                    |
| ----------------------------- | ------------------------------------------------------------------- |
| SolveSpace (jako odniesienie) | 0,006s                                                              |
| Nelder-Mead                   | Nie zbiega się                                                      |
| Powell                        | 7,8s                                                                |
| CG                            | 10+37s <sup>[1](#f1)</sup>                                          |
| BFGT                          | 10+0,8 <sup>[1](#f1)</sup>                                          |
| Newtonon-CG                   | 10+61+0,5s <sup>[2](#f2),</sup><sup>[3](#f3)</sup>                  |
| L-BFGS-B                      | 10+1,5s <sup>[1](#f1),</sup><sup>[3](#f3)</sup>                     |
| TNC                           | 10+0,8s <sup>[1](#f1)</sup>                                         |
| COBYLA                        | 0,2s <sup>[3](#f3)</sup>                                            |
| SLSQP                         | 10+0,3 <sup>[1](#f1),</sup><sup>[3](#f3)</sup>                      |
| dogleg                        | 10+61+?s <sup>[2](#f2)</sup> Niepowodzenie rozwiązania, błąd linalg |
| trust-ncg                     | 10+61+1,5s <sup>[2](#f2)</sup>                                      |

<b name="f1">[1]</b> W tym obliczanie macierzy Jakobiana (10s w tym przypadku testowym), które jest realizowane przy użyciu sympy lambdify z numpy.

<b name="f2">[2]</b> W tym obliczanie macierzy Hessiana (61s w tym przypadku testowym), oprócz macierzy Jacobiana.

<b name="f3">[3]</b> Otrzymane rozwiązanie zawiera niewielkie luki dla niektórych punktów z więzami zbieżności. Niepoprawne użycie algorytmu?

Powody, dla których napisano ten backend, są następujące,

* SolveSpace posiada licencję GPL, co jest niezgodne z licencją LGPL programu FreeCAD
* Aby uzyskać lepszy wgląd w system solvera i łatwo eksperymentować z nowymi pomysłami dzięki temu, że jest oparty na języku Python,
* W przypadku przyszłego rozszerzenia, symulacja oparta na fizyce, być może?

Będziesz musiał zainstalować SymPy i SciPy dla swojej platformy.

```
install --upgrade sympy scipy
```

