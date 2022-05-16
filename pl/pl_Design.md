Projekt Złożenie 3 (i fork FreeCADa) częściowo odpowiada [planowi projektu](https://www.freecadweb.org/wiki/Assembly_project) niedokończonego złożenia w programie FreeCAD, w szczególności sekcja [Infrastruktura](https://www.freecadweb.org/wiki/Assembly_project#Infrastructure) i [Model obiektów](https://www.freecadweb.org/wiki/Assembly_project#Object_model), podsumowana poniżej,

### Multi model

The forked FreeCAD core supports external object linking (with a new type of property, `PropertyXLink`, as a drop-in replacement of PropertyLink), displaying, editing, importing/exporting, and cross-document undo/redo.

### Part-tree

Assembly3 provides the `Assembly` container for holding its child features (or sub-assembly), and their constraints. It also introduce a new concept of `Elements` for declaring geometry elements used by constraints inside parent assembly. The purpose of the `Element` is to minimize the problem caused by geometry topological name changing, and make the assembly easier to maintain. See the following section for more details. A single object (e.g. Part object, sketch, or another assembly), can be added to multiple parent assemblies, either within the same or located outside of the current document. Each of its appearance inside the parent assembly has independent visibility control, but shares the same placement, meaning that if you move one instance of the object, all other instances moves relative to their parent assembly container. You can have independent placement by converting a child object into a link type object (See the following section for details). Simply right click the child object in the tree view and select `Link actions -> Replace with link`.

### Ujednolicony interfejs przeciągnij/upuść/kopiuj/wklej

API przeciągnij i upuść zostało rozszerzone, aby obiekt docelowy wiedział, gdzie upuszczony obiekt znajduje się w hierarchii obiektów, która jest w pełni wykorzystywana przez Złożenie 3. Kopiowanie i wklejanie zostało rozszerzone o obsługę zewnętrznych obiektów i pozwalała użytkownikowi zdecydować, czy chce wykonać płytkie czy głębokie kopiowanie.

### Model obiektu

Pole `SubName` w `Gui::SelectionObject` służy do trzymania wybranego elementu geometrii, takiego jak powierzchnia, krawędź lub wierzchołek. The forked FreeCAD extended usage of `SubName` to hold the path of selected object within the object hierarchy, e.g. a selection object with `Object = Assembly1` and `SubName
= Parts.Assembly2.Constraints002.Constraint.` means the user selected the `Constraint` object of `Assembly2`, which is a child feature of the part group (`Parts`) in `Assembly1`. Notice the ending `.` in `SubName`. This is for backward compatibility purpose, so that the `SubName` can still be used to refer to a geometry sub-element of some sub-object without any ambiguity. The rule is that, any sub-object references must end with a `.`, and those names without an ending `.` are sub-element references. The aforementioned `PropertyXLink` has an optional `subname` field (assign/return as a `tuple(obj,
subname)` in Python) for linking into a sub-object/element.

`Gui.Selection` został rozszerzony o kompatybilność wsteczną, aby zapewnić pełną informację o ścieżce obiektu dla każdego zaznaczenia, co umożliwia, aby ten sam obiekt był w więcej niż jednej grupie obiektów bez niejednoznaczności przy zaznaczeniu. Do rdzenia FreeCAD dodano kilka nowych API, aby dostarczyć zagnieżdżone informacje o umieszczeniu obiektu podrzędnego i geometrii. 
