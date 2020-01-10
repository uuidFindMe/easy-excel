# Easy Excel
> An easy way to work for excel with apache POI

## Quick Start

### 1. Add Maven repository

```xml
<dependency>
    <groupId>cn.shepherd</groupId>
    <artifactId>easy-excel</artifactId>
    <version>1.0.0</version>
</dependency>
```

### 2. Examples

Define a excel POJO.

```java

@Data
@Excel(value = "Summary", headerColor = 0x0D, herderHigh = 1024, rowHigh = 512, fontStyle = CustomFontStyle.class)
public class Model {

    @ExcelField("Name")
    private String name;

    @ExcelField(value = "Birthday", type = DATE, width = 50, headerColor = 0x0C)
    private Date birthDate;

    @ExcelField(value = "Age", type = NUMERIC)
    private Integer age;

    @ExcelField(value = "Time", type = DATE, datePattern = "h:mm:ss")
    private Date time;

    @ExcelField(value = "Excel Time", type = DATE, datePattern = "h:mm:ss")
    private double excelTime;

}
```

You can custom font style by overriding `cn.shepherd.excel.core.FontStyle`'s methods.
```java
public class CustomFontStyle implements FontStyle {

        @Override
        public boolean getItalic() {
            return true;
        }

        @Override
        public boolean getBold() {
            return true;
        }

        @Override
        public short getFontHeight() {
            return 256;
        }

        @Override
        public boolean getStrikeout() {
            return true;
        }
    }
```

Then set the `CustomFontStyle.class` to the property `fontStyle` of the Annotation `@Excel`.
```java
@Data
@Excel(value = "Summary", headerColor = 0x0D, herderHigh = 1024, rowHigh = 512, fontStyle = CustomFontStyle.class)
class Model {   
    //...
}
```

Create a test class.

```java
import static org.assertj.core.api.AssertionsForClassTypes.assertThat;

public class Tests {

    @Test
    void testExport() throws IOException {
        ExporterBase exporterBase = new DefaultExporter();
        List<Model> data = new ArrayList<>();
        Model model = new Model();
        model.setName("foo");
        model.setBirthDate(new Date());
        model.setAge(10);
        model.setTime(new Date());
        model.setExcelTime(DateUtil.convertTime("3:40:36"));
        data.add(model);
        data.add(model);
        data.add(model);
        ExcelMetadata<Model> excelMetadata = exporterBase.generateMetadata(Model.class, data);
        assertThat(excelMetadata).isNotNull();
        Workbook workbook = excelMetadata.getWorkbook();
        assertThat(workbook.getSheet("Summary")).isNotNull();
        OutputStream out = new FileOutputStream("target/foo.xls");
        workbook.write(out);
        out.close();
    }
}
```

Run the test class, and then you can find the file `foo.xls` under the target directory.

## TODO
