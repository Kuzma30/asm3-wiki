Цей посібник є копією SolveSpace [linkage](http://solvespace.com/linkage.pl). Щоб зібрати таку систему зв'язків,

[[images/linkage-view.png]]

ми збираємося побудувати ескіз скелета, як показано нижче,

[[images/linkage-dimensioned.png]]

* Перейдіть на робоче середовище Assembly3
* Створіть порожній контейнер збірки
* Оберіть створений об'єкт збірки та натисніть ![AddWorkplane](../raw/master/freecad/asm3/Gui/Resources/icons/Assembly_Add_Workplane.svg?sanitize=true), щоб додати робочу поверхню. Зауважте, що для визначення площини ескізу можна використовувати будь-який інший об’єкт з плоским ребром або гранню. Робоча площина Assembly3 - це лише зручність.
* Це необов’язковий крок: змініть `Ширину` і `Довжину` площини на 100 мм, а потім змасштабуйте та розташуйте 3D-вид, щоб помістити всю площину в центрі. Потім сховайте площину, щоб не заважала редагуванню ескізу. Розмір площини повинен дати вам приблизне уявлення про те, якої довжини повинні бути лінії. Рекомендується намалювати початковий ескіз наближеним до кінцевого результату, щоб не заплутати вирішувач.
* Виберіть нову робочу площину та натисніть ![AddSketchPlane](../raw/master/freecad/asm3/Gui/Resources/icons/constraints/Assembly_ConstraintSketchPlane.svg?sanitize=true), щоб додати обмеження `sketchPlane`
* Тепер перейдіть на робоче середовище Draft
* Додайте кілька ліній відповідно до наведеної картинки. Ви можете додати по одному відрізку для кожної кольорової лінії, або ви можете використовувати багатосегментні лінії, наскільки це можливо. Це дозволить зменшити кількість обмежень на збіг точок, необхідних пізніше.
* Це необов'язковий крок. Для відрізка лінії з двома точками ви можете встановити властивість `Length`, щоб визначити довжину відразу. Для багатоточкових поліліній ви можете встановити довжину на наступному кроці.
* Перетягніть всі лінії в контейнер збірки.
* Для зеленої полілінії ви можете вибрати будь-яку з трьох точок і натиснути ![AddLocked](../raw/master/freecad/asm3/Gui/Resources/icons/constraints/Assembly_ConstraintLock.svg?sanitize=true), щоб створити обмеження `Заблоковано`, щоб зафіксувати цю точку.
* Виберіть вертикальний зелений відрізок лінії та натисніть ![AddLineVertical](../raw/master/freecad/asm3/Gui/Resources/icons/constraints/Assembly_ConstraintLineVertical.svg?sanitize=true), щоб створити обмеження `LineVertical`.
* Виберіть горизонтальний зелений відрізок лінії та натисніть ![AddLineHorizontal](../raw/master/freecad/asm3/Gui/Resources/icons/constraints/Assembly_ConstraintLineHorizontal.svg?sanitize=true), щоб створити обмеження `LineHorizontal`.
* Для кожного сегмента лінії виберіть і клацніть ![AddLineLength](../raw/master/freecad/asm3/Gui/Resources/icons/constraints/Assembly_ConstraintLineLength.svg?sanitize=true), щоб створити обмеження `LineLength`, щоб зафіксувати довжину. Початковий параметр довжини береться з вибраної лінії. Отже, якщо ви вже встановили властивість `Довжина` лінії в Draft, більше нічого не потрібно робити. Для інших ліній відповідно встановіть параметр обмеження `Length`.
* Виберіть пари точок відповідно до малюнка вище та натисніть ![AddPointsCoincidence](../raw/master/freecad/asm3/Gui/Resources/icons/constraints/Assembly_ConstraintPointCoincident.svg?sanitize=true), щоб створити обмеження `PointsCoincidence`.
* Щоб простежити переміщення вершини, спочатку виділіть її, а потім натисніть ![Trace](../raw/master/freecad/asm3/Gui/Resources/icons/Assembly_Trace.svg?sanitize=true). Якщо ви вмикали трасування раніше, спочатку вимкніть його, перш ніж вибрати нову вершину, а потім увімкніть знову. Якщо ви виберете ребро або грань, ви побачите рух центральної точки фігури. Якщо ви не виберете жодну фігуру, перш ніж увімкнути трасування, ви побачите рух фігуру.
* Виберіть будь-яку вершину/ребро/грань, яку потрібно перемістити, і натисніть ![Move](../raw/master/freecad/asm3/Gui/Resources/icons/Assembly_Move.svg?sanitize=true) або ![AxialMove](../raw/master/freecad/asm3/Gui/Resources/icons/Assembly_AxialMove.svg?sanitize=true), а потім потягніть стрілку для переміщення.

[[images/linkage.gif]]
