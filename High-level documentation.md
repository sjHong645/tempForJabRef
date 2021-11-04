# High-level documentation

This page describes relevant information about the code structure of JabRef precisely and succinctly. Closer-to-code documentation is available at [Code HowTos](code-howtos.md).

**이 페이지는 JabRef의 코드 구조와 관련 있는 정보들을 정확하고 간결하게 설명할 것이다.
Code와 좀 더 관련 있는 문서는 [Code HowTos](code-howtos.md) 를 보도록 하자.**

We have been successfully transitioning from a spaghetti to a more structured architecture with the `model` in the center, and the `logic` as an intermediate layer towards the `gui` which is the outer shell. 

**우리는 성공적으로 '스파게티같은 코드'를 더욱 구조화된 아키텍처로 바꿔왔다. 
center에는 'model'이 있고 outer shell인 'gui'로 향하는 중간 층(intermediate layer)에는 'logic'이 있도록 했다.**

There are additional utility packages for `preferences` and the `cli`. 

**'preference'와 'cil'에 대한 추가적인 utility 패키지들이 있다.**

The dependencies are only directed towards the center. 

**의존성(dependencies)은 오직 'center'를 향한다.**

We have JUnit tests to detect violations of the most crucial dependencies \(between `logic`, `model`, and `gui`\), and the build will fail automatically in these cases.

**우리는 JUnit test를 가장 중요한 "'logic', 'model'과 'gui' 사이의 의존성"을 위반하는지 알아내기 위해 실행한다. 
그러면 빌드는 자동으로 실패할 것이다(??).**

The `model` represents the most important data structures \(`BibDatases`, `BibEntries`, `Events`, and related aspects\) and has only a little bit of logic attached. 

'model'은 가장 중요한 자료 구조인 'BibDatases', 'BibEntries', 'Events', 관련 자료들을 대표하고 아주 약간의 logic을 덧붙인다.
(무슨 말일지는 앞으로 더 알아가면서 파악해야겠다.)

The `logic` is responsible for reading/writing/importing/exporting and manipulating the `model`, and it is structured often as an API the `gui` can call and use. 

**'logic'은 'model'을 읽고/쓰고/import하고/export하고 조작하는 역할을 하고 'gui'가 호출하고 사용할 수 있는 API로써 구조화되어 있다.**

Only the `gui` knows the user and his preferences and can interact with him to help him solve tasks. 

**'gui'는 유저와 유저의 선호(preference)를 알고 있고 유저가 task들을 해결할 수 있도록 하기 위해서 상호작용할 수 있다.**

For each layer, we form packages according to their responsibility, i.e., vertical structuring. 

**각각의 layer에 대해서 우리는 각 층의 담당(즉, 수직적인 구조)에 따라 패키지(package)를 만들었다. (?? 명확한 의미는 모르지만 일단 더 알아보자)**

The `model` should have no dependencies to other classes of JabRef and the `logic` should only depend on `model` classes. The `cli` package bundles classes that are responsible for JabRef's command line interface. The `preferences` represents all information customizable by a user for her personal needs.

**'model'은 JabRef의 다른 클래스들과 의존성이 없어야 하고 'logic'은 오직 'model' 클래스에 대해서만 의존성을 가진다.
'cil' 패키지는 JabRef의 커맨드 라인 인터페이스(command line interface)와 관련된 클래스들만 다룬다.
'preferences'는 사용자 정의된 정보들을 표현한다.**

We use an event bus to publish events from the `model` to the other layers. This allows us to keep the architecture but still react upon changes within the core in the outer layers.

**우리는 'model'에서 다른 층으로 이벤트들을 publish 하기 위해서 이벤트 버스(event bus)를 이용한다. 이는 아키텍쳐를 보호하도록 하지만 바깥 층에 있는 core 내부의 변화에 반응하도록 한다.** 

You can view up-to-date diagrams for model, gui, and logic packages [at sourcespy](https://sourcespy.com/github/jabref/).

**model, gui, logic 패키지에 대한 최신화된 다이어그램은 [sourcespy](https://sourcespy.com/github/jabref/).에서 볼 수 있다
링크를 걸어둔 홈페이지에 들어가면 패키지들이 가지고 있는 클래스들과 그들의 관계를 도식화해서 표현했다.**

## Package Structure [![SourceSpy Dashboard](https://sourcespy.com/shield.svg)](https://sourcespy.com/github/jabref/)

Permitted dependencies in our architecture are:

```text
gui --> logic --> model
gui ------------> model
gui ------------> preferences
gui ------------> cli
gui ------------> global classes

logic ------------> model

global classes ------------> everywhere

cli ------------> model
cli ------------> logic
cli ------------> global classes
cli ------------> preferences
```

All packages and classes which are currently not part of these packages \(we are still in the process of structuring\) are considered as gui classes from a dependency stand of view.

**현재 위 그림에 없는 패키지와 클래스들은 의존성 관점에서 gui 클래스로 여기고 있다.**

## Most Important Classes and their Relation : 가장 중요한 클래스들과 그들의 관계

Both GUI and CLI are started via the `JabRefMain` which will in turn call `JabRef` which then decides whether the GUI \(`JabRefFrame`\) or the CLI \(`JabRefCLI` and a lot of code in `JabRef`\) will be started. 

**GUI와 CLI는 [JabRefMain](https://github.com/JabRef/jabref/blob/main/src/main/java/org/jabref/gui/JabRefMain.java#L33)을 통해서 시작된다. [JabRefMain](https://github.com/JabRef/jabref/blob/main/src/main/java/org/jabref/gui/JabRefMain.java#L33)은 결과적으로 JabRef라고 불릴 것이다. 
그러고 나면 GUI\('[JabRefFrame](https://github.com/JabRef/jabref/blob/main/src/main/java/org/jabref/gui/JabRefFrame.java#L151)\)' 또는 CLI\('[JabRefCLI](https://github.com/JabRef/jabref/blob/366f0cf325f977f0c5cbfa4115d5a61793f4c021/src/main/java/org/jabref/cli/JabRefCLI.java)' and a lot of code in `JabRef`\) 가 시작될 것이다.**

The `JabRefFrame` represents the Window which contains a `SidePane` on the left used for the fetchers/groups Each tab is a `BasePanel` which has a `SearchBar` at the top, a `MainTable` at the center and a `PreviewPanel` or an `EntryEditor` at the bottom. 

**'[JabRefFrame](https://github.com/JabRef/jabref/blob/main/src/main/java/org/jabref/gui/JabRefFrame.java#L151)'은 왼쪽에 있는'[SidePane](https://github.com/JabRef/jabref/blob/bb011c9313367a28990ae213b3920fe6cd10d1dc/src/main/java/org/jabref/gui/SidePane.java)'을 포함한 window를 나타낸다. 
'[SidePane](https://github.com/JabRef/jabref/blob/bb011c9313367a28990ae213b3920fe6cd10d1dc/src/main/java/org/jabref/gui/SidePane.java)'은 fetchers/groups 을 위해서 사용된다. 각각의 탭은 'BasePanel', '[MainTable](https://github.com/JabRef/jabref/blob/bee2dc8560985837bba4fb3497f1eb342a8e5c3f/src/main/java/org/jabref/gui/maintable/MainTable.java)', '[PreviewPanel](https://github.com/JabRef/jabref/blob/main/src/main/java/org/jabref/gui/preview/PreviewPanel.java#L36)'(또는 'EntryEditor') 인데
'BasePanel'은 top에 위치한 'SearchBar'에 관한 것이고 'MainTable'은 center에 위치해있으며 
'PreviewPanel'은 bottom에 위치해있다.**

=> top, bottom, center는 실행화면의 위치를 말하는 듯 하다.

Any right click on the `MainTable` is handled by the `RightClickMenu`. Each `BasePanel` holds a `BibDatabaseContext` consisting of a `BibDatabase` and the `MetaData`, which are the only relevant data of the currently shown database. 

**'MainTable'에 오른쪽클릭은 '[RightClickMenu](https://github.com/JabRef/jabref/blob/df83c9d8c709c121e69868c8149263ac2988b2ac/src/main/java/org/jabref/gui/maintable/RightClickMenu.java)'에서 관리한다. 
각각의 'BasePanel'은 현재 보여지는 데이터베이스와 유일하게 연관된 데이터인 '[BibDatabase](https://github.com/JabRef/jabref/blob/main/src/main/java/org/jabref/model/database/BibDatabase.java#L42)'와 '[MetaData](https://github.com/JabRef/jabref/blob/main/src/main/java/org/jabref/model/metadata/MetaData.java#L29)'로 구성된 '[BibDatabaseContext](https://github.com/JabRef/jabref/blob/1cdd807ae91d2b3678d3808bb7ae6064dbaf84c5/src/test/java/org/jabref/model/database/BibDatabaseContextTest.java)'를 가지고 있다.**

A `BibDatabase` has a list of `BibEntries`. Each `BibEntry` has an ID, a citation key and a key/value store for the fields with their values. 

**'[BibDatabase](https://github.com/JabRef/jabref/blob/main/src/main/java/org/jabref/model/database/BibDatabase.java#L42)'는 'BibEntries'에 관한 리스트를 가지고 있다. '[BibEntry](https://github.com/JabRef/jabref/blob/main/src/main/java/org/jabref/model/entry/BibEntry.java#L50)' 는 ID, citation key, key/value store에 관한 것이다.**


Interpreted data \(such as the type or the file field\) is stored in the `TypedBibentry` type. 
**Interpreted data는 '[TypedBibentry](https://github.com/JabRef/jabref/blob/bb011c9313367a28990ae213b3920fe6cd10d1dc/src/test/java/org/jabref/logic/TypedBibEntryTest.java)' 타입으로 저장된다.**

The user can change the `JabRefPreferences` through the `PreferencesDialog`.
**사용자는 '[JabRefPreferences](https://github.com/JabRef/jabref/blob/9c5b8ab5cf458447c57caee726705f8202aa3d88/src/main/java/org/jabref/preferences/JabRefPreferences.java)'를 `PreferencesDialog`를 통해서 변경할 수 있다**
