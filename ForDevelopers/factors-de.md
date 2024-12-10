## Faktoren, die bei der Wahl eines Modus berücksichtigt werden sollten

In diesem Abschnitt gehen wir auf die wichtigsten Faktoren ein, die Ihre Entscheidung beim Wählen zwischen SSR und interaktiven Modi leiten sollten:

1. Hauptmerkmale der Darstellung und Interaktion
2. Zustand und Navigation
3. JavaScript und externe Interoperabilität
4. Benutzererfahrungsfunktionen
5. Erweiterte Entwicklerfunktionen
6. Skalierbarkeit und Leistungsüberlegungen
7. Sicherheitsüberlegungen
8. Bereitstellung und Hosting
9. Entwicklung und Debugging
10. Übergang zwischen Modi
11. Erweiterte Entwicklerfunktionen
12. Weitere Überlegungen

### 1. Hauptmerkmale der Darstellung und Interaktion
Als Grundlage des Renderings legen diese Funktionen die technischen Fähigkeiten und Einschränkungen jedes Modus fest.

| Faktor                      | SSR (Statisches Server-Rendering) | Interaktiver Server | Interaktiver Client  |
|-----------------------------|-----------------------------------|---------------------|----------------------|
| **Initiale Ladeleistung**   | **Schnellere Initialladung**: Vorgefertigtes HTML wird sofort gesendet | **Moderat**: Die anfängliche Ladezeit umfasst einige Interaktivitätsvorbereitungen | **Langsam**: Erfordert das Laden und Initialisieren clientseitiger Ressourcen |
| **Benutzerinteraktion**     | **Eingeschränkt**: Nur statische Inhalte bis das clientseitige Rendering beginnt | **Teilweise**: Servergesteuerte Interaktivität mit potenzieller Latenz | **Vollständig**: Sofortige clientseitige Interaktivität, sobald clientseitige Ressourcen geladen sind |
| **Dynamische Inhaltsaktualisierungen** | **Nicht unterstützt**: Erfordert vollständige Seitenaktualisierung. | **Unterstützt** durch Server-Roundtrips | **Vollständig unterstützt**: Echtzeitaktualisierungen ohne Seitenaktualisierung. |
| **Ereignisbehandlung**      | Nicht unterstützt beim ersten Rendering | Wird auf dem Server behandelt, mit potenzieller Netzwerk-Latenz | Wird sofort auf dem Client behandelt |
| **OnAfterRenderAsync**      | **Nicht ausgelöst**: Nur nach interaktivem Rendering aufgerufen | **Unterstützt**:Funktion wird auf dem Server aufgerufen mit möglicher Netzwerkverzögerung für den Client |  **Voll unterstützt**: sofortige Ausführung im Browser ohne Latenz (nach clientseitigem Laden) |

---
### 2. Zustandsverwaltung und Navigation

Die Zustandsverwaltung und Navigation sind grundlegende Aspekte beim Erstellen interaktiver Webanwendungen und stehen in engem Zusammenhang mit dem Rendering.

| Faktor                  | SSR (Static Server Render) | Interaktiver Server | Interaktiver Client  |
|--------------------------|----------------------------|----------------------|-----------------------|
| **Zustandsverwaltung** | **Eingeschränkt**: Verlässt sich auf serverseitige Lösungen wie In-Memory-Speicher, verteiltes Caching, Datenbanken usw. | **Unterstützt**: Der Zustand wird auf dem Server verwaltet, oft mit SignalR für Echtzeit-Updates | **Vollständig unterstützt**: Reiche clientseitige Zustandsverwaltungsoptionen mit Frameworks wie Redux oder MobX |
| **Zustandspersistenz** | **Eingeschränkt**: Der Zustand kann mit verschiedenen Speichermechanismen auf dem Server persistent gemacht werden. Dies ermöglicht die Verwaltung des Sitzungszustands über mehrere Anfragen hinweg, auch wenn jede Anfrage zustandslos ist. | **Unterstützt**: Der Zustand bleibt während der Sitzung auf dem Server bestehen | **Vollständig unterstützt**: Clientseitige Zustandspersistenz über Interaktionen hinweg, auch offline |
| **Navigation Manager** | **Eingeschränkt**: Kann nur über serverseitige Umleitung navigieren | **Unterstützt**: Ermöglicht clientseitige Navigation mit SPA-ähnlicher Funktionalität unter Verwendung von SignalR | **Vollständig unterstützt**: Unterstützt clientseitige Navigation mit voller SPA-Funktionalität |

**Anmerkungen:**

1. **Zustandsverwaltung:**
   - SSR verlässt sich auf serverseitige Mechanismen wie In-Memory-Speicher, verteiltes Caching, Datenbanken usw., was für dynamische Anwendungen einschränkend sein kann. Komponenten werden als statisches HTML gerendert.
   - Der interaktive Server verwaltet den Zustand auf dem Server und bietet Echtzeit-Updates, erfordert jedoch eine konstante Verbindung.
   - Der interaktive Client bietet eine robuste clientseitige Zustandsverwaltung mit verschiedenen Bibliotheken und Tools.

2. **Zustandspersistenz:**
   -  Da SSR zustandslos ist, muss der Zustand bei jeder Anfrage neu geladen werden. Es ist zwar möglich, den Zustand mithilfe verschiedener Speichermechanismen auf dem Server aufrechtzuerhalten, doch müssen alle Zustandsinformationen bei jeder Anfrage neu geladen werden, was zu Ineffizienz und längeren Ladezeiten führen kann.
   - Der interaktive Server hält den Zustand während der Benutzersitzungen aufrecht, was das Benutzererlebnis verbessern kann.
   - Der interaktive Client kann den Zustand über Sitzungen hinweg persistent machen und sogar Offline-Szenarien unterstützen.

3. **Navigation Manager:**
   - SSR behandelt die Navigation in der Regel durch vollständige Seitenneuladungen. Siehe auch [.NET 9-Problem](nm9.md).
   - Der interaktive Server kann glattere Navigationserlebnisse ähnlich wie Single Page Applications (SPAs) mit Technologien wie SignalR bieten.
   - Der interaktive Client unterstützt vollständig die SPA-Navigation und bietet nahtlose Übergänge ohne vollständige Seitenneuladungen.

---

### 3. JavaScript und externe Interoperabilität
Interop-Fähigkeiten sind entscheidend für die Integration mit externen Bibliotheken und die Hinzufügung erweiterter clientseitiger Funktionalitäten.

| Faktor | SSR (Static Server Render) | Interaktiv |
|--------|----------------------------|-------------|
| **JavaScript ohne Rückgabewert** | **Teilweise unterstützt**: Enqueue-Aufrufe für spätere Ausführung auf dem Web-Client | **Vollständig unterstützt**: Sofortige Ausführung von JavaScript-Funktionen |
| **JavaScript mit Rückgabewert** | **Nicht unterstützt**: JavaScript, das Rückgabewerte benötigt, kann nicht ausgeführt werden| **Vollständig unterstützt**: Ermöglicht das Aufrufen von JavaScript-Funktionen und das Abrufen von Rückgabewerten |
| **C#-Callback von JavaScript** | **Nicht unterstützt**: In diesem Modus können keine C#-Methoden aus JavaScript aufgerufen werden | **Vollständig unterstützt**: JavaScript kann C#-Methoden direkt mit Parametern aufrufen |
| **`IJSRuntime`-Unterstützung** | **Nicht unterstützt**: JavaScript-Interop mit `IJSRuntime` wird im SSR-Modus nicht unterstützt, da es keine aktive Blazor-Laufzeit auf der serverseitig gerenderten Seite gibt. Es muss stattdessen reines JavaScript verwendet werden | **Vollständig unterstützt**: `IJSRuntime` ist verfügbar, um JavaScript-Funktionen aufzurufen und Daten zwischen Blazor und dem clientseitigen JavaScript zu übergeben  |

**Anmerkung**: Im **SSR-Modus** ist JavaScript-Interop mit **`IJSRuntime`** nicht verfügbar, da die Blazor-Laufzeit auf dem Client noch nicht initialisiert wurde. Jegliches benötigte JavaScript vor der Hydratisierung muss über standardmäßiges HTML und JavaScript ausgeführt werden. Sobald die Seite hydratisiert ist und Blazor interaktiv wird, können Sie **`IJSRuntime`** für eine dynamischere Interaktivität verwenden.

---

### 4. Benutzererfahrungsfunktionen
Diese Funktionen haben einen direkten Einfluss darauf, wie die Anwendung von den Benutzern wahrgenommen wird, und stellen eine natürliche Erweiterung der Interaktivität und Navigation dar.

| Faktor                              | SSR (Statisches Server-Rendering)    | Interaktiv    |
|-------------------------------------|--------------------------------------|---------------|
| **Formularvalidierung**             | **Nur serverseitig**: Erfordert Roundtrips für die Validierungslogik | **Vollständig unterstützt**: Echtzeit-Client-seitige Validierung wird unterstützt |
| **SEO und Metadaten-Rendering**     | **Vollständig unterstützt**: Der Inhalt wird vollständig serverseitig gerendert und ist durchsuchbar | **Teilweise unterstützt**: Erfordert zusätzlichen Aufwand, damit Crawler dynamischen Inhalt verarbeiten können |
| **Offline-Unterstützung**           | **Nicht unterstützt**: Vollständig serverabhängig | **Unterstützt**: Möglich mit Blazor WebAssembly |

---

### 5. Fortgeschrittene Entwicklerfunktionen
Sicherzustellen, dass die Anwendung für alle benutzbar ist, ist eine logische Fortsetzung nach der Abdeckung der grundlegenden Benutzererfahrungsaspekte.

| Faktor                              | SSR (Statisches Server-Rendering)    | Interaktiv    |
|-------------------------------------|--------------------------------------|---------------|
| **HttpContext-Sichtbarkeit**        | **Unterstützt**: Während der serverseitigen Ausführung verfügbar | **Nicht unterstützt**: Nach der Umstellung auf den Client nicht direkt zugänglich |
| **RenderFragment**                  |  **Unterstützt**: RenderFragment can be used but limited to static context | **Vollständig unterstützt** für die App- und Seiten-Render-Modi Deklaration<br>**Nicht unterstützt** für die Deklaration des Komponenten-Rendering-Modus |
| **Fehlerbehandlung**                | Serverseitige Fehler können während des Renderings abgefangen und behandelt werden. Fallback-Inhalte oder Fehlerseiten können bereitgestellt werden | Clientseitige Laufzeitfehler müssen in der Benutzerschnittstelle behandelt werden, typischerweise mit Fehlergrenzen oder Try-Catch-Blöcken. Server-Fehler bei API-Aufrufen müssen separat behandelt werden |

### 6. Skalierbarkeit und Leistungsüberlegungen

Leistung und Skalierbarkeit sind sowohl für die Benutzererfahrung als auch für das Systemdesign wichtig. Sie verbinden benutzerorientierte Aspekte mit technischen und betrieblichen Anliegen.

| Faktor                          | SSR (Statisches Server-Rendering) | Interaktiver Server | Interaktives WebAssembly |
|----------------------------------|----------------------------------|---------------------|-----------------|
| **Serverlast**                   | **Höher**: Jede Benutzerinteraktion erfordert eine Serveranforderung | **Moderat**: Der Server verarbeitet UI-Updates und das State-Management, aber weniger häufig als bei SSR | **Geringer**: Client-seitiges Rendering reduziert Serverlast |
| **Bandbreitennutzung**           | **Minimal**: Nur das notwendige HTML und die Assets für den ersten Ladevorgang werden gesendet | **Moderat bis hoch**: Ständige SignalR-Verbindung für Echtzeit-Updates | **Höher**: Erfordert initialen Download von .NET Runtime und Application Bundle |
| **Gleichzeitige Benutzer**       | Durch die Serverkapazität begrenzt, da jede Benutzersitzung Ressourcen benötigt | Skaliert gut mit zusätzlichen Servern, aber ist immer noch auf Serverressourcen angewiesen | Skaliert gut, da die Client-seitige Verarbeitung die Serverlast verringert |

**Anmerkung:** Während der Interaktive Server eine bessere Skalierbarkeit als SSR bietet, ist er immer noch auf Serverressourcen für UI-Updates und das State-Management angewiesen. Das Interaktive WebAssembly hingegen kann besser skalieren, indem die Verarbeitung auf die Client-Seite ausgelagert wird.

---

### 6. Skalierbarkeit und Leistungsüberlegungen

Leistung und Skalierbarkeit sind sowohl für die Benutzererfahrung als auch für das Systemdesign von Bedeutung. Sie verbinden benutzerorientierte Aspekte mit technischen und betrieblichen Anliegen.

| Faktor | SSR (Static Server Render) | Interaktiver Server | Interaktives WebAssembly |
|---|---|---|---|
| **Serverlast** | **Höher**: Jede Benutzerinteraktion erfordert eine Serveranfrage | **Moderat**: Server verwaltet UI-Updates und Zustandsverwaltung, aber weniger häufig als SSR | **Niedriger**: Clientseitiges Rendering reduziert die Serverlast |
| **Anfangsladezeit** | **Schneller**, insbesondere für statischen Inhalt | **Schneller** als WebAssembly, aber langsamer als SSR | **Langsamer** aufgrund des .NET-Laufzeitumgebung- und App-Bundle-Downloads |
| **Bandbreitennutzung** | **Minimal**: Sendet nur das notwendige HTML und die Assets für den ersten Ladevorgang | **Moderat**: Kontinuierliche SignalR-Verbindung für Echtzeit-Updates | **Höher**: Erfordert den anfänglichen Download der .NET-Laufzeitumgebung und des Anwendungsbundles |
| **Gleichzeitige Benutzer** | **Begrenzt**: Durch die Serverkapazität, da jede Benutzersitzung Ressourcen benötigt | **Skaliert** mit zusätzlichen Servern, hat jedoch Einschränkungen durch die SignalR-Infrastruktur und die Komplexität der Anwendung | **Skaliert gut**: Da die clientseitige Verarbeitung die Serverlast reduziert |
| **Auswirkungen der Netzwerklatenz** | **Weniger betroffen** von Netzwerkverzögerungen. | **Stark betroffen** von Netzwerkverzögerungen, insbesondere für Echtzeit-Updates | **Stark betroffen** von Netzwerkverzögerungen während des anfänglichen Downloads. <br> **Nicht betroffen**: Kann offline oder in unzuverlässigen Netzwerken nach dem ersten Ladevorgang funktionieren |

**Anmerkung:** Interactive Server bietet zwar eine bessere Skalierbarkeit als SSR, benötigt aber immer noch Server-Ressourcen für die Aktualisierung der Benutzeroberfläche und die Statusverwaltung. Interactive WebAssembly hingegen ist besser skalierbar, da die Verarbeitung auf die Clientseite verlagert wird.

---

### 7. Sicherheitsüberlegungen

Sicherheit ist entscheidend für den Schutz von Anwendungen, Daten und Benutzern und muss daher vor der Implementierung behandelt werden.

| Faktor                          | SSR (Statisches Server-Rendering) | Interaktiver Server | Interaktiver Client |
|----------------------------------|----------------------------------|---------------------|---------------------|
| **Umgang mit sensiblen Daten**   | **Sicherer**: Daten werden auf dem Server verarbeitet und gespeichert, wodurch die Exposition verringert wird | **Sicherer**: Sensible Daten werden hauptsächlich auf dem Server verarbeitet, wodurch die Exposition auf der Client-Seite minimiert wird | **Risikoreicher**: Mehr Daten und Logik sind auf der Client-Seite exponiert, was potenzielle Schwachstellen erhöht |
| **Authentifizierung**            | Vollständig unterstützt mit serverseitigen Sitzungen oder Cookies, um eine sichere Verwaltung zu gewährleisten | Vollständig unterstützt mit serverseitigen Sitzungen oder tokenbasierter Authentifizierung, was eine robuste Sicherheit bietet | Vollständig unterstützt, oft unter Verwendung tokenbasierter Authentifizierung, erfordert jedoch sorgfältige Handhabung, um Token-Exposition zu verhindern |
| **Cross-Site Scripting (XSS)**   | **Geringeres Risiko**: Keine clientseitige Ausführung während des ersten Ladevorgangs, was XSS-Vektoren verringert | **Geringeres Risiko**: Serverseitiges Rendering minimiert die direkte Exposition der Client-Seite gegenüber XSS| **Höheres Risiko**: Erfordert eine sorgfältige Sanitierung von Client-seitigen Eingaben und eine Ausgabe-Kodierung, um XSS-Angriffe zu verhindern |

**Anmerkungen:**
- SSR und Interaktiver Server verarbeiten Daten auf dem Server. Dies hilft, sensible Informationen sicher zu halten und zu verhindern, dass sie von Clients eingesehen werden.
- Interaktive Client-Anwendungen müssen robuste Sicherheitspraktiken implementieren, um Risiken im Zusammenhang mit der Exposition und Manipulation von Client-seitigen Daten zu mindern.
- Authentifizierungsmechanismen sind in allen Modellen im Allgemeinen robust, erfordern jedoch eine sorgfältige Implementierung, um sicherzustellen, dass Token oder Sitzungsbezeichner nicht exponiert werden.

### 8. Softwareverteilung und Hosting

Mit der Sicherheit im Hinterkopf ist die Softwareverteilung und das Hosting der nächste logische Schritt, der sich damit beschäftigt, wie die Anwendung effektiv betrieben werden kann.

| Faktor                           | SSR (Statisches Server-Rendering) | Interaktiver Server | Interaktiver Client |
|----------------------------------|----------------------------------|---------------------|---------------------|
| **Hosting-Anforderungen**        | Erfordert einen leistungsfähigen Server, um SSR-Anfragen zu verarbeiten (z. B. ASP.NET Core) | Erfordert einen Server, um Echtzeit-Interaktionen zu verwalten (z. B. SignalR) | Kann vollständig auf statischen Hosting-Plattformen laufen |
| **Edge- oder CDN-Unterstützung** | Eingeschränkt: Erfordert einen Ursprungsserver für dynamisches Rendering. Moderne CDNs können einige dynamische Inhalte zwischenspeichern, sind jedoch weiterhin auf Serververarbeitung angewiesen | Eingeschränkt: Echtzeit-Updates hängen von Serververbindungen ab; moderne CDNs können die Bereitstellung beschleunigen | Vollständig unterstützt: Statische Ressourcen können global zwischengespeichert werden, während dynamische Inhalte clientseitig verarbeitet werden |
| **[Progressive Enhancement](prog-enh.md)** | Vollständig unterstützt: Funktioniert auch in Umgebungen [ohne JavaScript](prog-eng-js) | Teilweise unterstützt: Erfordert JavaScript für Echtzeit-Interaktionen | Erfordert JavaScript für Funktionalitäten über den initialen Ladeprozess hinaus |

**Anmerkungen:**

- **Hosting-Anforderungen:** SSR und Interaktiver Server erfordern beide Serverressourcen, aber der Interaktive Client (WebAssembly) kann auf statischen Plattformen gehostet werden, was den Infrastrukturbedarf verringert.
- **Edge- oder CDN-Unterstützung:** Der Interaktive Client profitiert vom globalen Caching statischer Assets, während SSR und Interaktiver Server aufgrund ihrer Abhängigkeit von serverseitiger Verarbeitung Einschränkungen aufweisen, obwohl moderne CDNs die Bereitstellung dynamischer Inhalte optimieren können.
- **Progressive Enhancement:** SSR unterstützt Umgebungen ohne JavaScript, während sowohl der Interaktive Server als auch der Client für die vollständige Funktionalität auf JavaScript angewiesen sind.

---

### 9. Entwicklung und Debugging

Nachdem das Hosting behandelt wurde, verschiebt sich der Fokus darauf, wie Entwickler mit diesen Modi während des Entwicklungszyklus arbeiten.

| Faktor                            | SSR (Statisches Server-Rendering) | Interaktiver Server | Interaktiver Client |
|----------------------------------|----------------------------------|---------------------|---------------------|
| **Entwicklungs-Komplexität**     | **Niedriger**: Einfaches, traditionelles serverseitiges Rendering-Modell. | **Moderat**: Beinhaltet sowohl serverseitige Logik als auch Echtzeit-Browser-Interaktionen, nutzt jedoch oft bestehende serverseitige Frameworks | **Höher**: Beinhaltet sowohl clientseitige als auch serverseitige Logik, was oft komplexere Tools erfordert |
| **Debugging-Tools**              | Standard serverseitige Debugging-Tools | Kombination aus serverseitigen und browserbasierten Debugging-Tools | Primär browserbasierte Debugging-Tools |
| **Hot Reload-Unterstützung**     | **Vollständig unterstützt** in der serverseitigen Entwicklung | **Unterstützt**, aber Änderungen müssen mit dem Client synchronisiert werden | **Unterstützt**, aber Änderungen müssen mit dem Client synchronisiert werden |

**Anmerkungen:**

1. **IDE-Unterstützung in verschiedenen Modi:**
   - Alle gängigen IDEs, einschließlich **Visual Studio**, **JetBrains Rider** und **VS Code**, unterstützen vollständig das Debugging in den Modi SSR, Interactive Server und Interactive Client.
   - Funktionen wie **Hot Reload**, **Breakpoints** und **WASM-Debugging** sind weit verbreitet und einfach einzurichten.

2. **Browser-basierte Debugging-Tools:**
   - In den Modi **Interactive Client** und **WASM** sind browserbasierte Debugging-Tools (wie Chrome DevTools oder Edge Developer Tools) unerlässlich.
   - Einige Aspekte des Debuggings, wie DOM-Manipulation, JavaScript-Ausführungsfluss und clientseitige Performance-Profiling, sind **nur** innerhalb der Entwicklertools des Browsers möglich.
   - Diese Tools bieten einzigartige Funktionen wie die Echtzeit-Inspektion des DOM, Breakpoints für JavaScript, Performance-Profiling und die Möglichkeit, direkt mit der Browserumgebung zu interagieren, was nicht immer vollständig in IDEs nachgebildet werden kann.
   - Daher sollten Entwickler eine Kombination aus IDE- und Browser-Tools verwenden, um vollständige Debugging-Fähigkeiten zu erreichen, wenn sie mit clientseitigen oder interaktiven Blazor-Anwendungen arbeiten.

3. **Historischer Kontext der Komplexität:**
   - Frühere Debugging-Workflows, insbesondere für WASM und Interactive Client, erforderten zusätzliche Einrichtungen, besonders in IDEs wie VS Code. Dies schuf den Eindruck einer höheren Komplexität.
   - Im Laufe der Zeit haben verbesserte Tools in allen IDEs den Debugging-Prozess vereinfacht und viele der früheren Hürden beseitigt.
---

### 10. Übergang zwischen Modi
Nachdem erläutert wurde, wie die "Hosting"-Szenarien entwickelt und betrieben werden, wird in diesem Abschnitt beschrieben, wie die "Transition"- und "Hybrid"-Szenarien ablaufen.

| Faktor | SSR (Statisches Server-Rendering) | Interaktiver Server | Interaktiver Client | Interaktiver Auto |
|--------|-----------------------------------|---------------------|---------------------|-------------------|
| **Erhaltungszustand** | **Manuell**: Erfordert das Übertragen von Zustand zwischen Anfragen | **Automatisch**: Zustand bleibt während der Sitzung auf dem Server erhalten | **Automatisch**: Zustand bleibt im Browser des Clients erhalten. | **Adaptiv**: Zustandserhaltung hängt vom gewählten Render-Modus für jede Komponente ab |
| **Dynamisches Modus-Switching** | **Unterstützt**: Kann in jeden interaktiven Modus wechseln. Der Wechsel zum interaktiven Client erfordert normalerweise ein vollständiges Neuladen | **Unterstützt**: Kann mit minimaler Störung vom SSR-Modus wechseln, da die Verbindung persistent ist | **Unterstützt**: Kann vom SSR-Modus wechseln, erfordert jedoch in der Regel ein vollständiges Neuladen und zusätzliches Laden des WebAssembly-Laufzeitumgebung | **Dynamisch**: Wählt den Render-Modus basierend auf dem aktuellen Kontext, kann zwischen Server- und Client-Modus wechseln |
| **Ressourcennutzung** | Server-Ressourcen werden über Anfragen hinweg wiederverwendet, aber nicht mit dem Client geteilt | Server-Ressourcen werden über Anfragen hinweg und für SignalR-Verbindungen wiederverwendet | Client-Ressourcen bleiben lokal erhalten, Server-Ressourcen werden über API-Aufrufe abgerufen | Passt die Ressourcennutzung basierend auf dem gewählten Render-Modus an |

**Anmerkungen:**

1. **Erhaltungszustand**:
   - Beim **Interaktiven Server** bleibt der Zustand während der Sitzung auf dem Server erhalten.
   - Beim **Interaktiven Client** wird der Zustand im Browser gespeichert.
   - Bei **Interactive Auto** wird die Zustandserhaltung für jede Komponente entsprechend dem gewählten Rendering-Modus angepasst. Die Zustandserhaltung erfolgt nicht automatisch über die verschiedenen Rendermodi hinweg.

2. **Dynamischer Moduswechsel**
   - **Modusübergänge:** Der Wechsel zwischen Render-Modi wird unterstützt, aber jeder Übergang erfordert sorgfältige Überlegung der damit verbundenen Auswirkungen.
   - **Interaktiver Auto-Modus:** Dieser Modus bestimmt dynamisch den geeigneten Render-Modus (Server oder Client) basierend auf dem aktuellen Kontext und dem Interaktivitätsgrad der Seite. Er priorisiert die Auswahl eines Render-Modus, der mit den vorhandenen interaktiven Komponenten übereinstimmt, um die Erstellung einer neuen interaktiven Laufzeitumgebung ohne gemeinsamen Zustand zu vermeiden.
   - **Entscheidungen pro Komponente/Laden:** Der Auto-Modus bewertet und entscheidet den Render-Modus für jede Komponente oder Seitenladen unabhängig, anstatt einen festen Modus für die gesamte Sitzung anzuwenden.
   - **Warnung bei zustandsbehafteten Diensten:** Seien Sie vorsichtig bei der Verwendung zustandsbehafteter Dienste, da der gewählte Render-Modus zwischen serverbasierten und clientbasierten Diensten wechseln kann, was die Zustandsverwaltung beeinflussen könnte.
   
3. **Ressourcennutzung**:
   - In SSR und Interaktivem Server werden serverseitige Ressourcen über Anfragen hinweg wiederverwendet, aber nicht mit dem Client geteilt.
   - Beim Interaktiven Client werden Ressourcen lokal im Browser verwaltet, wobei serverseitige Ressourcen über API-Aufrufe abgerufen werden.
   - Der Interaktive Auto-Modus passt die Ressourcennutzung je nach gewähltem Render-Modus für jede Komponente an.

4. **Moduswechsel**

 Wichtige Punkte beim Wechsel zwischen den Modi:

 - **Kaskadierende Parameter**:
    - Stellen Sie sicher, dass kaskadierende Parameter während der Übergänge korrekt wiederhergestellt werden, um Null-Referenzen zu vermeiden.
    - Stellen Sie Fallback-Standards bereit, wo dies notwendig ist.

 - **Parameter-Serialisierung**:
    - Sichern Sie die Datenintegrität während der Serialisierung und Deserialisierung.
    - Überprüfen Sie die Typkompatibilität zwischen serialisierten und deserialisierten Formaten.
    - Vergessen Sie nicht, einen Standardkonstruktor einzufügen, falls dies vom Serialisierungsframework erforderlich ist.

 - **Größe der Parameter-Serialisierung**:
    - Minimieren Sie die Größe der Parameterobjekte, um die Leistung während der Übergänge zu verbessern.
    - Erwägen Sie Lazy Loading oder das Übertragen nur der wesentlichen Daten, um die Nutzlastgröße zu reduzieren.

 - **Zustandssynchronisation**:
    - Implementieren Sie die Zustandshydratation, um sicherzustellen, dass Änderungen in einem Modus beim Übergang zu einem anderen Modus korrekt widergespiegelt werden.
    - Halten Sie die Konsistenz über die Rendering-Modi hinweg aufrecht, um das Benutzererlebnis zu verbessern.
---

### 11. Erweiterte Entwicklerfunktionen
Diese Funktionen sind optional, aber wichtig für fortgeschrittene Anwendungsfälle, daher sind sie später in der Reihenfolge sinnvoll.

| Faktor | SSR (Statisches Server-Rendering) | Interaktiver Server | Interaktiver Client |
|--------|-----------------------------------|---------------------|---------------------|
| **[ARIA und Semantisches HTML](aria.md)** | **Vollständig unterstützt**: Markup ist statisch und folgt den Standards problemlos | **Vollständig unterstützt**: Der Server generiert zugängliches Markup, aber es ist wichtig, dynamische Updates sorgfältig zu behandeln | **Vollständig unterstützt**: Erfordert sorgfältige Implementierung, um sicherzustellen, dass dynamisch generierter Inhalt zugänglich bleibt |
| **Tastaturnavigation** | Unterstützt für grundlegende Interaktionen: Native Browserfunktionen wie `Tab`-Navigation und Formularübermittlung sind verfügbar. <br>**Einschränkungen:** Komplexe Interaktionen (z. B. benutzerdefinierte Tastenkombinationen oder Echtzeit-Updates) werden nicht unterstützt und erfordern den interaktiven Modus | **Unterstützt**, aber erfordert auf dem Server eine ordnungsgemäße Ereignisbehandlung für benutzerdefinierte Interaktionen | **Unterstützt**, erfordert jedoch eine ordnungsgemäße Ereignisbehandlung im clientseitigen Code für benutzerdefinierte Interaktionen |
| **Kompatibilität mit Bildschirmlesern** | **Vollständig unterstützt**: Vorab gerenderte Inhalte funktionieren nahtlos mit Bildschirmlesern | **Unterstützt**, aber Echtzeit-Updates erfordern eine sorgfältige Implementierung, um sicherzustellen, dass Bildschirmleser Änderungen ankündigen | **Unterstützt**, erfordert jedoch eine sorgfältige Implementierung, um sicherzustellen, dass dynamisch aktualisierte Inhalte ordnungsgemäß von Bildschirmlesern angekündigt werden |

**Anmerkungen:** 

1. **ARIA und Semantisches HTML**: Alle Modi unterstützen dies, aber in den interaktiven Modi muss mehr Aufmerksamkeit darauf verwendet werden, dass dynamisch aktualisierte Inhalte weiterhin zugänglich sind.

2. **Tastaturnavigation**: Während grundlegende Navigation in allen Modi unterstützt wird, erfordern benutzerdefinierte Interaktionen in den interaktiven Modi spezifische Implementierungen, um die Tastaturzugänglichkeit sicherzustellen.

3. **Kompatibilität mit Bildschirmlesern**: SSR hat einen Vorteil bei der anfänglichen Inhaltszugänglichkeit, aber beide interaktiven Modi können eine gute Kompatibilität erreichen, wenn dynamische Inhaltsaktualisierungen korrekt implementiert werden.

---

### 12. Weitere Überlegungen
Dieser Abschnitt fasst verschiedene Faktoren zusammen, die nicht in andere Kategorien passen, aber dennoch wichtig sind.

| Faktor | SSR (Statisches Server-Rendering) | Interaktiver Server | Interaktiver Client |
|--------|-----------------------------------|---------------------|---------------------|
| **Offline-Modus** | **Eingeschränkt**: Kann mit Service-Workern für grundlegende Offline-Inhalte arbeiten, aber dynamische Funktionen erfordern eine Serververbindung | **Eingeschränkt**: Erfordert eine ständige Verbindung zum Server für die vollständige Funktionalität | **Unterstützt**: Kann mit richtiger Implementierung vollständige Offline-Funktionalität ermöglichen |
| **Progressive Web App (PWA)** | **Teilweise unterstützt**: Einige PWA-Funktionen können implementiert werden, sind jedoch durch die Serverabhängigkeit begrenzt. | **Teilweise unterstützt**: Einige PWA-Funktionen können implementiert werden, sind jedoch durch die Serverabhängigkeit begrenzt. | **Vollständig unterstützt**: Funktioniert nahtlos mit PWA-Funktionen, einschließlich Offline-Funktionalität. |
| **Caching-Strategien** | Funktioniert mit traditionellen serverseitigen Caching-Mechanismen und CDNs | Kombiniert serverseitiges Caching mit SignalR-Verbindungsmanagement für optimierte Leistung | Nutzt clientseitiges Caching und Service-Worker für verbesserte Leistung und Offline-Funktionen |

**Anmerkungen:**

1. **Offline-Modus**: 
   - SSR und Interaktiver Server haben Einschränkungen aufgrund der Serverabhängigkeit.
   - Der Interaktive Client (WebAssembly) bietet die beste Unterstützung für Offline-Funktionalität.

2. **Progressive Web App (PWA)**:
   - Alle Modi können einige PWA-Funktionen implementieren, aber der Interaktive Client bietet die umfassendste Unterstützung.

3. **Caching-Strategien**:
   - Jeder Modus hat unterschiedliche Caching-Ansätze, wobei der Interaktive Client die flexibelsten clientseitigen Caching-Optionen bietet.

