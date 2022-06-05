# Tworzenie prostego złożenia opartego na wiązaniach

* Uruchom program FreeCAD i utwórz nowy dokument
* Przełącz się na środowisko pracy `Część` i utwórz `Sześcian` i `Walec`
* Przełącz na środowisko pracy `Złożenie 3`, kliknij ![AddAssembly](../raw/master/freecad/asm3/Gui/Resources/icons/Assembly_New_Assembly.svg?sanitize=true) aby utworzyć nowe złożenie
* Wybierz `sześcian` oraz `walec` i przeciągnij je do swojego złożenia
* Wybierz dowolną płaszczyznę `walca` lub `sześcianu`i kliknij ![Move](../raw/master/freecad/asm3/Gui/Resources/icons/Assembly_Move.svg?sanitize=true) aby aktywować ręczny ruch części. Kliknij na dowolną strzałkę, by przeciągnąć `walec` na górną ścianę `sześcianu`, naciśnij klawisz `ESC` by opuścić tryb ruchu części (alternatywnie: kliknij prawym przyciskiem `Złożenie` w widoku drzewa i wybierz `Zakończ edycję`)
* Zaznacz górną ścianę `sześcianu` i (trzymając klawisz `CTRL`) zaznacz dolną ściankę lub krawędź `walca`, a następnie kliknij przycisk ![AddCoincidence](../raw/master/freecad/asm3/Gui/Resources/icons/constraints/Assembly_ConstraintCoincidence.svg?sanitize=true) aby utworzyć wiązanie współpłaszczyznowości.
* W końcu kliknij ![Solve](../raw/master/freecad/asm3/Gui/Resources/icons/AssemblyWorkbench.svg?sanitize=true) by rozwiązać układ wiązań.

[[images/simple.gif]]

Możesz kliknąć ![Auto recompute](../raw/master/freecad/asm3/Gui/Resources/icons/Assembly_AutoRecompute.svg?sanitize=true) aby włączyć automatyczne rozwiązywanie po wprowadzeniu zmian w wiązaniu.

Po utworzeniu nowego wiązania wybrane elementy zostaną podświetlone na czerwono. Kolor poszczególnych elementów ograniczających możesz łatwo zmienić na stronie właściwości obiektu widoku. Lub możesz ustawić kolor całego wiązania przez zmianę koloru samego obiektu wiązania. Upewnij się, że ustawiłeś parametr właściwości widoku `OverrideMaterial` na wartość `Prawda`.

Jeśli zauważysz, że podświetlenie elementu ograniczającego zasłania widok 3D złożenia, możesz włączyć funkcję _Automatyczna Widoczność Elementów_ klikając przycisk ![AutoElementVis](../raw/master/freecad/asm3/Gui/Resources/icons/Assembly_AutoElementVis.svg?sanitize=true). Po włączeniu tej opcji wszystkie elementy wiążące będą domyślnie ukryte. Możesz je wyświetlić, zaznaczając dowolne wiązanie lub wiązanie obiektu elementu w widoku drzewa.

Teraz zapisz ten dokument pod dowolną nazwą.

# Utwórz Super Złożenie za pomocą tablicy linków zewnętrznych

We are going to build a multi-joint _thing_ using the above assembly as the base part.

* Create a new document, and save it to whatever name you like. Yes, you need to save both the link and linked document at least once for external linking to work, because `PropertyXLink` need the file path information of both document to calculate relative path.
* Make sure the current active 3D view is the new empty document. Now, in the tree view, select the assembly we just created previously, and then hold on `CTRL` key and right click the new document item in the tree view, and select `Link actions -> Make link`. A `Link` will be created that brings the assembly into the new document. You probably need to click `Fit content` button (or press `V,F` in 3D view) to see the assembly.
* Select the link in the tree view, and change the `ElementCount` property to four. Now you have four identical assemblies.
* Create a new assembly, and then drag the link object into it.
* Select any face of any `Cube`, click ![Move](../raw/master/freecad/asm3/Gui/Resources/icons/Assembly_Move.svg?sanitize=true) and click any arrow to drag to spread out the parts. Click the edges of the movement sphere to turn the part. Press `Esc` to leave part movement.
* Select any face of the left most `Cube` in 3D view, and click ![Lock](../raw/master/freecad/asm3/Gui/Resources/icons/constraints/Assembly_ConstraintLock.svg?sanitize=true) to lock the left most sub assembly.
* Orient the parts whatever you like. Select two face from any two assembly, and create a plane coincidence constraint. If you've enabled _auto recompute_, then the two assembly will now be snapped together
* Należy postępować tak samo w przypadku pozostałych części.

[[images/super.gif]]

Now that we've made this multi-joint thingy, try to save this document, and FreeCAD will ask if you want to save the external document, too. If you answer yes, then FreeCAD will take care of ordering, and save the external document first before linking document.

Close both documents. Open the multi-joint assembly document, FreeCAD will automatically open any externally referenced documents, too. If you close the external document while leaving the linking document open, all externally linked object will vanish from 3D view. Open external document again and the objects will re-appear. This allows you to easily swap in a replacement document for whatever reason. But, it demands the replacement document having an object of the same internal name as the original linked one. You can of course, easily re-assign the link to any other object in the opened documents. Just use the property editor, click the edit button of `LinkedObject` property. In the editor window, select the desired document in the drop list, and then select the desired object. But now, you need to make sure the new linked object has the same element interface, or else the constraints will be broken.

A few more words about link array. Assembly3 normally treats any object added to its part group as a stand alone entity that can be moved as a whole. However, it has special treatment for a link array object. Each array element will be treated as separate entities, that can be constrained and moved individually. If you actually want to add the array as an integral part, simply wrap the array inside a dummy assembly without any constraint, and add that assembly instead into the parent assembly.

By the way, the `Draft` workbench now has two variation of link array, the `LinkArray` and `LinkPathArray`, which provide the same functionality as `Draft` `Array` and `PathArray`, but use link to provide duplicates. Those link arrays, by default, do not show individual element in tree view. You can still access each element through `subname` reference as usual. Having less objects can improve document saving and loading performance. It is particularly noticeable if you have large amount of array elements. You can, however, show the array element at any time by toggle property `ShowElement`. Once the elements are visible, they can be moved independently by change their placements.

# Add/Modify Element and ElementLink

It is quite easy to directly create a new constraint as shown above, with all involved `Element` and `ElementLink` being taken care of for you by Assembly3. It is also straightforward to manually add new or modify existing `Elements` and `ElementLink`. Simply select a geometry element in 3D view, and its corresponding owner object will be selected in the tree view (Remember to turn on _Sync selection_ option in tree view as mentioned before). You can then drag the selected item to the `ElementGroup` of an `Assembly` to create a new `Element`, or to a `Constraint` to add an `ElementLink`. You can modify an existing `Element` or `ElementLink` by simply dragging the item onto an existing item of `Element` or `ElementLink`. Checkout [[this|Replacing-Part]] tutorial for demonstration.

# Part Move

Assembly3 has extensive support of manual movement of nested assembly. In 3D view, select any geometry element (Face, Edge) that belongs to some assembly, and click ![Move](../raw/master/freecad/asm3/Gui/Resources/icons/Assembly_Move.svg?sanitize=true) to activate part dragging. The dragger will be centered around the selected geometry element. In case of multi-hierarchy assemblies, you will be dragging the first level sub-assembly of the top-level assembly. If you want to drag intermediate sub-assembly instead, add that assembly as the second selection (`CTRL` select) before activating part move.

If you have enabled ![AutoRecompute](../raw/master/freecad/asm3/Gui/Resources/icons/Assembly_AutoRecompute.svg?sanitize=true), any movement of the sub-assembly will cause the parent assembly to auto re-solve its constraints, as shown below. Because there are too many degree of freedom left, hence many possible solutions of the constraint system, the movement of the multi-joint assembly is jerky. Besides, the part move command is very complicated, and probably need a lot more work to make it perfect. In case when the parts are moved such that they stuck in some invalid position, as you can see from the screen cast below, simply `CTRL+Z` to undo the movement. Every time you release the mouse button, a transaction will be committed, so that you can undo/redo the previous mouse drag. You can also temporary bypass recomputation by holding `CTRL` key while dragging.


[[images/move.gif]]

# Import Złożenia zewnętrznego

W niektórych przypadkach łatwiej jest rozpowszechniać złożenie obejmujące wiele gałęzi jako pojedynczy, samodzielny dokument. Rdzeń programu FreeCAD udostępnia wygodne polecenie, które pomaga w tym, skądinąd nie tak trywialnym, zadaniu. Wystarczy kliknąć prawym przyciskiem myszki dowolną pozycję w dokumencie, który chcesz rozpowszechnić, i wybierz `Akcje łącza -> Importuj wszystkie
łącza`, i to wszystko. Kliknij w przycisk ![Solve](../raw/master/freecad/asm3/Gui/Resources/icons/AssemblyWorkbench.svg?sanitize=true) aby zobaczyć, czy wszystko jest w porządku. Oczywiście możesz wybiórczo zaimportować dowolny obiekt, który zechcesz. Po prostu kliknij prawym przyciskiem myszy na ten element i wybierz opcję `Akcje z łączami -> Importuj
link`.

