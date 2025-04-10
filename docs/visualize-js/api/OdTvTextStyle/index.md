# OdTvTextStyle

## 方法

### getFontTypeface

获取当前与文本样式关联的`TrueType字体`的字体族名称。

返回一个包含字体族名称的std::wstring值。

如果当前没有TrueType字体与文本样式关联，该方法将返回一个空字符串。

### getFontBold

获取与文本样式关联的`TrueType字体`的加粗属性值。

如果字体是加粗的，则返回true；否则返回false。

如果当前文本样式未关联任何TrueType字体，该方法将返回false。

### getFontItalic

获取与文本样式关联的`TrueType字体`的斜体属性值。

如果字体是斜体，则返回true；否则返回false。

如果当前没有TrueType字体与文本样式关联，则该方法将返回false。

### getFontCharset

获取与文本样式关联的 `TrueType字体`的 Windows 字符集标识符。

一个整数值，表示字符集的标识符。例如，Unicode (UTF-8) 字符集的标识符为 65001。

如果当前文本样式未关联任何 TrueType 字体，该方法将返回 0。

### getFontPitchAndFamily

获取与当前文本样式关联的 `TrueType 字体`的 Windows 字距和字符族标识符。

一个整数值，表示字距和字符族的标识符。

如果当前文本样式未关联任何 TrueType 字体，该方法将返回 0。


### setFont

将 TrueType 字体与当前文本样式关联，并设置其通用属性。

- typeface       [输入] 字体（字体族）名称。
- bold           [输入] True - 设置为粗体。
- italic         [输入] True - 设置为斜体。
- charset        [输入] Windows 字符集标识符。
- pitchAndFamily [输入] Windows 字符间距和字体族标识符。


如果 typeface 参数为空字符串，此方法将移除之前设置的 TrueType 字体与当前文本样式的关联，并删除相关信息。

### getFileName

返回当前与文本样式关联的 SHX 字体文件名。

一个包含文件名的 std::wstring 值。

### setFileName

将指定文件中的 SHX 字体与文本样式关联。

- sUniFont [in]  SHX 格式文件的名称

