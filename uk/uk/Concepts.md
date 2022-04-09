# Система координат

Користувачам рекомендується спочатку прочитати [цей посібник](https://web.archive.org/web/20191130140733/https://www.freecadweb.org/wiki/Assembly_Basic_Tutorial), щоб отримати деяку інформацію про нову концепцію _локальної системи координат_. Цей підручник призначений для оригінального робочого середовища Assembly (незавершеного), але він також дає вичерпний огляд того, що також надає Assembly3. `Part` або контейнер `Product` зазначений в посібнику, еквівалентний контейнеру `Assembly` в Assembly3, яку, звісно, ​​можна розглядати як _частину_ і додавати до інших збірок. Є одна річ, з якою я не погоджуюсь у цьому посібнику. Концепція _глобальної системи координат_ все ще є корисною та необхідною для взаємодії з об’єктами з інших застарілих робочих середовищ. Давайте просто визначимо _глобальну систему координат_ як систему координат 3D-виду, іншими словами, місце, де ви насправді бачите об’єкт у 3D-виді, або координати, які показують в рядку стану під час наведенні курсора миші на якийсь об’єкт.

У upstream FreeCAD існує контейнер `App::Part`, який є об’єктом групового типу, який визначає локальну систему координат. Різниця в порівнянні з контейнером Assembly3 полягає в тому, що один об’єкт можна додати до одного і лише одного контейнера `App::Part`. Контейнер власника можна додати до іншого контейнера `App::Part`, але він має підкорятися правилу одного безпосереднього батьківського контейнера. Причина цього полягає в тому, що коли будь-який об’єкт додається до `App::Part`, він фізично видаляється з початкової батьківської системи координат і додається до системи координат `App::Part` власника, тому об'єкт не може з'являтися більше ніж в одній системі координат. Під _фізичним видаленням_ я маю на увазі, що дані тривимірного візуального представлення фізично переміщуються в іншу систему координат всередині графу 3D сцени (докладніше дивіться [тут](https://www.freecadweb.org/wiki/Scenegraph)).

У Assembly3 контейнері немає такого обмеження. При додаванні до контейнера Assembly3 візуальні дані об’єкта просто повторно використовуються і вставляються кілька разів у граф сцени, що означає, що об’єкт фактично існує одночасно в кількох системах координат. Це має дещо несподіваний побічний ефект. Коли об’єкт додається до збірки з певним розташуванням, об’єкт, здавалося б, стрибає на нове місце (Оновлення: з’явилася нова функція автоматичного налаштування розташування під час перекидання об’єкта в іншу систему координат. Щоб активувати його, клацніть правою кнопкою миші в будь-якому місці ієрархії документа та виберіть `Tree options -> Sync placement`). Це очікувано, оскільки об’єкт входить у нову систему координат, і, схоже, він має таку ж поведінку, як `App::Part`. Але насправді сталося те, що початковий об’єкт всередині глобальної системи координат просто робиться невидимим перед додаванням до контейнера збірки. Ви можете перевірити це, переключивши властивість `Visibility` вручну, щоб побачити об’єкт у його початковому розташуванні. Властивість кожного об’єкта `Visibility` контролює його власну видимість лише в глобальній системі координат. Кожен контейнер збірки має властивість `VisibilityList`, щоб контролювати видимість своїх дітей.

# Link (Посилання)

Ядро відгалуження FreeCAD запровадив новий тип об'єкта, що називається _Link(Посилання)_. Об’єкт типу _Link_ (не плутати з _link property_) часто не має власних даних геометрії, а замість цього має посилання на інші об’єкти (використовуючи властивість посилання) для обміну даними геометрії. Його супровідний постачальник представлення даних, `Gui::View ProviderLink`, посилається на постачальника представлення пов’язаних об’єктів для обміну візуальними даними. Це найефективніший спосіб дублювання одного і того ж об’єкта в різних місцях з додатковим перевизначенням масштабу/відзеркалювання та матеріалу. Ядро забезпечує розширення `App::LinkBase Extension` як гнучкий спосіб допомогти користувачам розширити власний об’єкт в об’єкт типу посилання. У розширенні використовується так званий _шаблон проєктування властивостей_, що означає, що саме розширення не визначає жодної властивості, але має купу попередньо визначених місць для зберігання властивостей.</0>. Розширення активує частину цих функцій в залежності від того, які властивості визначені в об’єкті. Цей шаблон проектування дозволяє об’єкту вибирати власні назви властивостей і типи.

Ядро надає два готові до використання об'єкти типу посилання, `App::Link` і `App::LinkGroup`, які відкривають різні частини `LinkBaseExtension` функціональності. `App::Link` підтримує посилання на об’єкт, у тому самому або зовнішньому документі, і має вбудовану підтримку масивів (через властивість `ElementCount`) для ефективного дублювання об'єктів. `LinkGroup` діє як група об’єктів з локальною системою координат. Він покладається на `LinkBaseExtension` і `ViewProviderLink` для надання розширених функцій, наприклад, додавання зовнішнього дочірнього об’єкта, додавання одного й того ж об’єкта кілька разів тощо. Усі контейнери Assembly3 це фактично налаштовані `LinkGroup`.

# Element (Елемент)

`Element` – це абсолютно нова концепція, представлена ​​Assembly3. It is used to minimize the dreadful consequences of geometry topological name changing, and also brings the object-oriented concept in the programming world into CAD assembling. `Element` can be considered as a declaration of connection interface of the owner assembly, so that other parent assembly can know which part of this assembly can be joined with others.

For a geometry constraint based system, each constraint defines some relationship among geometry elements of some features. Conventionally, the constraint refers to those geometry elements by their topological names, such as `Fusion001.Face1`, `Cut002.Edge2`, etc. The problem with this simple approach is that the topological name is volatile. Faces or edges may be added/removed after the geometry model is modified. More sophisticated algorithm can be applied to reduce the topological name changing, but there will never be guarantee of fixed topological names. Imagine a simple but yet extreme case where the user simply wants to replace an entire child feature, say, changing the type of some screw. The two features are totally different geometry objects with different topological naming. The user has to manually find and amend geometry element references to the original child feature in multiple constraints, which may exists in multiple assembly hierarchies, across multiple documents.

The solution, presented by Assembly3, is to use abstraction by adding multiple levels of indirections to geometry references. Each `Assembly` container has an element group that contains a list of `Elements`, which are a link type of object that links to some geometry element of some child feature of this assembly. In case the feature is also an `Assembly`, then the `Element` in upper hierarchy will instead point to the `Element` inside lower hierarchy assembly. In this way, each `Element` acts as an abstraction to which geometry element can be used by other parent assemblies. Any constraint involving some assembly will only indirectly link to the geometry element through an `Element` of some child assembly. If the geometry element's topological name changes due to whatever reason, the user only needs to change the deepest nested (i.e. nearest to the actual geometry object) `Element`'s link reference, and all upper hierarchy `Elements` and related constraints stays the same.

The `Element` is a specialized `App::Link` that links into a sub-object, using a `PropertyXLink` that accepts a `tuple(object, subname)` reference. In addition, `Element` allows to be linked by its label, instead of the immutable internal FreeCAD object name. `Element` specifically allows its label to be duplicated (but still enforces uniqueness among its siblings). This enables the user to define inter-changeable parts with the same set of elements as interface.

Let's take a look at the following assembly hierarchy for an example,

```
Assembly001
    |--Constraints001
    |       |--Constraint001
    |               |--ElementLink -> (Elements001, "$Element.")
    |               |--ElementLink001 -> (Parts001, "Assembly002.Elements002.$Element001.")
    |--Elements001
    |     |--Element -> (Parts001, "Cut.Face3")
    |--Parts001
          |--Cut
          |--Assembly002
                 |--Constraints002
                 |--Elements002
                 |      |--Element001 -> (Parts002, "Assembly003.Elements003.$Element002.")
                 |--Parts002
                       |--Assembly003
                                |--Constraints003
                                |--Elements003
                                |       |--Element002 -> (Parts003, "Fusion.Face1")
                                |--Parts003
                                       |--Fusion
```

The `Assembly001` has two child features, a `Cut` object and a child `Assembly002`, which in turn has its own child `Assembly003`. `Assembly001` contains a constraint `Constraint001` that defines the relationship of its two child features. `Constraint001` refers to two geometry element through two links, `ElementLink`, which point to a second level link, `Element`. `ElementLink001` points to `Element001`, And, because the first child feature `Cut` is not defined as an assembly, its geometry element reference is directly stored inside the parent assembly element group. `Element001`, however, links to the lower hierarchy `Element002` in its child assembly, which again links to `Element003` in its child `Assembly003`. Notice the `$` inside the subname references. It marks the followed text to be a label instead of an object name reference. If you re-label the object, all `PropertyXLink` of all opened documents containing that label reference will be automatically updated.

The grand idea is that, after the author modified an assembly, whether its a modification to the geometry model, or replacing some child feature. He needs to check all element references inside that and only that assembly, and make proper adjustment to correct any undesired changes. Other assemblies with elements or constraints referring to this assembly will stay the same (although recomputation is still required), even if those assemblies reside in different documents, or come from different authors.

Let's say, we have modified `Fusion`, and the original `Fusion.Face1` is now changed to `Face10`. All we need to do is to simply modify `Element002` inside the same owner assembly of `Fusion`. Everything else stays the same.

Again, say, we want to replace `Assembly003` with some other assembly. Now this is a bit involving, because, we added `Aseembly003` directly to `Assembly002`, instead of using a link, which can be changed dynamically. The FreeCAD core has a general command to simplify this task. Right click `Assembly003` in the tree view, and select `Link actions -> Replace with link`. `Assembly003` inside `Parts002` will now be replaced with a link that links to `Assembly003`. Every relative link that involving `Parts002.Assembly003` will be updated to `Parts002.Link_Assembly003` automatically. In our case, that will be `Element001`. You can then simply change the link to point to another assembly containing an element object with the same label `Element001` (remember element object allows duplicated labels). If you still insist on adding the new assembly directly and get rid of the link, you can use `Link actions ->
unlink`, and delete the link object afterward.

It may seem intimidating to maintain all these complex hierarchies of `Elements`, but the truth is that it is not mandatory for the user to manually create any element, at all. Simply select any two geometry elements in the 3D view, and you can create a constraint, regardless how many levels of hierarchies in-between. All intermediate `Elements` and `ElementLinks` will be created automatically. Although, for the sake of re-usability, it is best for the user as an assembly author to explicitly create `Element` as interfaces, and give them proper names for easy (re)assembling. Check out [[this|Replacing-Part]] tutorial for a demonstration of part replacement.

Last but not the least, `Element`, as well as the `ElementLink` inside a constraint, make use of a new core feature, `OnTopWhenSelected`, to forcefully show highlight of its referring geometry sub-element (Face, Edge, Vertex) when selected, regardless of any obscuring objects. The property `OnTopWhenSelected` is available to all view object, but default to `False`, while `Element` and `ElementLink` make it active by default. The on-top feature makes it even easier for the user to check any anomaly due to topological name changing.

# Selection

There are two types of selection in FreeCAD, geometry element selection by clicking in the 3D view, and whole object selection by clicking in the tree view. When using Assembly3, it is important to distinguish between these two types of selection, because there are now lots of objects with just one geometry element. While you are getting used to these, it is helpful to bring out the selection view (FreeCAD menu bar, `View -> Panels -> Selection view`). You select a geometry element by clicking any unselected element (Face, Edge or Vertex) in the 3D view. If you click an already selected element, the selection will go one hierarchy up. For example, for a `LinkGroup` shown below,

```
LinkGroup
    |--LinkGroup001
    |       |--Fusion
    |       |--Cut
    |--Cut001 
```

Suppose you have already selected `Fusion.Face1`. If you click that face again, the selection will go one hierarchy up, and select the whole `Fusion` object. If you click any where inside `Fusion` object again, the selection goes to `LinkGroup001`, and you'll see both `Fusion` and `Cut` being highlighted. If you again click anywhere inside `LinkGroup001`, `Cut001` will be highlighted, too, because the entire `LinkGroup` is selected. Click again in `LinkGroup`, the selection goes back to the geometry element you just clicked.

There is a new feature in the forked FreeCAD selection view. Check the `Enable
pick list` option in selection view. You can now pick any overlapping geometry elements that intersect with your mouse click in the selection view.

You may find it helpful to turn on tree view selection synchronization (right click in tree view, select `Sync selection`), so that the tree view will automatically scroll to the object you just selected in the 3D view. When you select an originally unselected object in the tree view, the whole object will be selected. And if you start dragging the object item in the tree view, you are dragging the whole object. If you select a geometry element in the 3D view, its owner object will also be selected in tree view. But if you then initiate dragging of that particular object item, you are in fact dragging the selected geometry element. This is an important distinction, because some containers, such as the `Constraint` object, only accept dropping of geometry element, and refuse whole object dropping.

* 2D/3D Sketch

The usage of sketch in assembly is a technique called _Skeleton Modeling_. It is a type of top-down design approach, where you draw skeleton (lines, arcs, etc) sketches to specify design criteria, and then add individual components that references those criteria. Some type of mechanical systems are naturally modeled in 2D, e.g. a pin joint that can only rotate in a plane, while others must be modeled in 3D, e.g. a ball joint.

FreeCAD already has a powerful _Sketcher_ workbench, but it is limited to creating 2D sketches. In addition, the sketcher is more geared toward geometry modeling, i.e. creating bases for extrusion, pocketing, etc. The sketch object stores all elements and constraints inside the object itself. It has its own editor and solver, which makes it probably the most complex single object in the entire FreeCAD. Instead of modifying the sketch object to suit for assembly needs, Assembly3 opt to repurpose some of the objects from the _Draft_ workbench without modification. The draft workbench, I believe, was originally used for sketching purposes before the birth of the more specialized sketcher.

At the time of this writing, Assembly3 supports using draft wire and circle/arc for sketching purpose. For normal objects added to an assembly container as a part, the placement of the object is used as constraining parameters (see [[here|Constraints-and-Solvers]] for more details). Draft wire is treated specially. Instead of constraining on the object placement, it is constrained on the coordinates of each individual points. Draft circle is still constrained on its placement like other objects, but with an additional parameter for its radius, and draft arc, two more parameters for the first and last angle that defines its two end points. Draft wires has many use cases, not all of which are acceptable by sketching constraint (e.g. `LineLength`). Only non-subdivided wires without a base or tool object attached are accepted.

The draft objects added are free to move in 3D space. To create a 2D sketch, you can add a `SketchPlane` constraint. It accepts any planar edge or face as the first element for defining the current sketching plane. Any draft object elements involved in the following constraints will be confined to this plane implicitly. You can also explicitly add elements into the `SketchPlane` constraint. To reset the current sketch plane, i.e. make the following draft elements free in 3D, add an empty `SketchPlane` constraint. You can have more than one sketch plane defined in the same assembly container.

You can checkout [[this|Create-Skeleton-Sketch]] tutorial to find out more details


