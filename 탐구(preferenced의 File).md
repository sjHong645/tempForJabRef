### preference > File에 있는 기본 설정에 관한 내용


C:\Users\PC\Documents\GitHub\jabref\src\main\java\org\jabref\gui\preferences\file\FileTab.fxml 에서

``` java
<RadioButton fx:id="resolveStringsBibTex" text="%Resolve strings for standard BibTeX fields only"
                 toggleGroup="$stringsResolveToggleGroup"/>
```


둘의 차이를 그림으로 보자.

![image](https://user-images.githubusercontent.com/64796257/140474305-31193dd8-e461-46ea-886a-6858d844652e.png)

그림에서는 다음과 같은 차이가 나타나는데 

C:\Users\PC\Documents\GitHub\jabref\src\main\java\org\jabref\gui\preferences\file\FileTabViewModel.java 에서는

다음과 같은 코드 차이가 있다.

``` java
resolveStringsBibTexProperty.setValue(initialImportExportPreferences.shouldResolveStringsForStandardBibtexFields());

resolveStringsExceptProperty.setValue(initialImportExportPreferences.getNonResolvableFields());
```

다른 탭에도 비슷한 패턴이 있다. 발견한 패턴을 바탕으로 File 에 있는 내용을 좀 더 살펴보겠다.

1) BooleanPropery 선언. & 생성자는 SimpleBooleanProperty()로 생성

``` java
import org.jabref.preferences.ImportExportPreferences;

public class FileTabViewModel implements PreferenceTabViewModel {

  ...

  private final BooleanProperty resolveStringsBibTexProperty = new SimpleBooleanProperty();
  private final BooleanProperty resolveStringsAllProperty = new SimpleBooleanProperty();
  
  ...
  
  private final ImportExportPreferences initialImportExportPreferences;
  
  ...

}
```

2) setValues() 라는 메소드 내에서 다음과 같이 구현

``` java
import org.jabref.preferences.ImportExportPreferences;

public class FileTabViewModel implements PreferenceTabViewModel {
  ... 

  private final ImportExportPreferences initialImportExportPreferences;
  
  ... 
  
  public void setValues() {
    
    ...
    
    resolveStringsBibTexProperty.setValue(initialImportExportPreferences.shouldResolveStringsForStandardBibtexFields());
    resolveStringsExceptProperty.setValue(initialImportExportPreferences.getNonResolvableFields());
    
    ...
    
  }

``` 

3) storeSettings() 라는 메소드 내에서 다음과 같이 구현

``` java
import org.jabref.preferences.ImportExportPreferences;

public class FileTabViewModel implements PreferenceTabViewModel {
  ... 

  private final ImportExportPreferences initialImportExportPreferences;
  
  ... 
  
  @Override
    public void storeSettings() {
        preferences.storeOpenLastFilesOnStartup(openLastStartupProperty.getValue());

        ImportExportPreferences newImportExportPreferences = new ImportExportPreferences(
                ...
                resolveStringsBibTexProperty.getValue(),
                resolveStringsAllProperty.getValue(),
                ...
                
    }
    
``` 



