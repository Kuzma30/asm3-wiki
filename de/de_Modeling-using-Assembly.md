Manchmal möchte man Teile modellieren, während man einen Zusammenbau erstellt. Dieses Tutorial zeigt zwei Möglichkeiten, dies zu tun.

[[images/assembly-binder.png]]

Es werden die oben gezeigten Teile zusammengestellt. Das bläuliche Teil soll eine Leiterplatte darstellen, das braune eine USB-Buchse und das grüne ist eine Blende (Schalttafel?). Zusätzlich zu der Montage dieser drei Teile, soll auch eine Öffnung in die Blende geschnitten werden, um die USB-Buchse an der Montagestelle hervorstehen zu lassen.

# Verwenden des Arbeitsbereiches `Part`

Für die erste Möglichkeit wird die Funktion `Cut` des Arbeitsbereiches `Part` verwendet. Beide Möglichkeiten verwenden den neuen `SubShapeBinder`, um die Teilform einer Gruppe mit den zugehörigen Positionsaktualisierungen zu verknüpfen. Obwohl der `SubShapeBinder` zu `PartDesign` gehört, kann er ohne einen `Body` (Körper) verwendet werden.

Zuerst erstellen wir eine Skizze für den Durchlass der Buchse. In der realen Welt verwendet man sicherlich eine externe Geometrie in der Skizze, um die Lochkontur zu zeichnen, die hier zur Vereinfachung weggelassen wird. Nach Fertigstellung wechselt man zum Arbeitsbereich `Part` und extrudiert die Skizze. Dann wird ein Zusammenbau erstellt, der den Sockel und die Lochform beinhaltet. Auswählen des Baumobjekts der Lochform im Zusammenbau, wechseln zum Arbeitsbereich `PartDesign` und klicken auf ![SubShapeBinder](../../FreeCAD/raw/LinkStage3/src/Mod/PartDesign/Gui/Resources/icons/PartDesign_SubShapeBinder.svg?sanitize=true), erstellt einen Binder zur Lochform. Dieser Binder aktualisiert automatisch die Position der Form, wenn das Loch oder der übergeordnete Zusammenbau die Position ändert.

[[images/part-binder.gif]]

Als Nächstes wird ein weiterer Zusammenbau erstellt. Hier kommt der wichtigste Schritt: Es muss die originale unbeschnittene Blende zum Zusammenbau hinzugefügt werden. Wird sie auf den Zusammenbau gezogen, erhält man wahrscheinlich eine Fehlermeldung wie "Box cannot be dragged out of Cut" bekommen; diese lässicht sich durch drücken der `STRG`-Taste umgehen. Danach kann man damit fortfahren, weitere Teile hinzuzufügen.

Nun ist es endlich so weit, die Teile zusammenzusetzen. Zuerst die Leiterplatte festsetzen (lock). Anschließend fügt man die Bedingung `PlaneAlignment-` hinzu, um die Buchse auf der Leiterplatte zu fixieren. Dann, ein weiterer wichtiger Schritt, wird die originale unbeschnittene Blende an der Platine befestigt, **anstatt** der beschnittenen. Die beschnittene Form braucht keine eigenen Randbedingungen, sie folgt der ursprünglichen Blende. Die unbeschnittene Blende kann ausgeblendet und die Blende mit Loch kann im Zusammenbau angezeigt werden.

# Verwenden des Arbeitsbereiches `PartDesign`

The second approach is to use the `PartDesign` workbench, which seems to be the natural choice when the panel itself is created using `PartDesign Body`. The first step of creating the hole shape is exactly the same as the previous approach. Because there exists a `PartDesign` body, when you create the `SubShapeBinder`, it will be added to the body automatically. Hide the hole shape, select a face of the binder shape, and create a `Pocket` to punch the hole.

Once all parts are ready, create an assembly, drop in all three parts, and assemble them as before. Notice that when the panel body changes position, the hole is not updated accordingly. You need to manually update the binder by double clicking the item in the tree view, and then recompute the document to update the body. This limitation is due to the fact that the binder needs the parent placement information to calculate the relative location of the binding shape, but FreeCAD always recomputes child before the parent.

[[images/partdesign-binder.gif]]

