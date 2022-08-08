# sheetToRedmine

구글 스프레드 시트를 레드마인의 표 형식으로 변환해주는 프로그램이다. 

스프레드시트를 레드마인으로 옮겨야 하는데 기존에 회사에서 사용하던 sheet2textile 확장 프로그램이 구글 보안 규정 변경으로 인해 사용할 수 없게 되었다.

업무중 사용하기 위해 기존 sheet2textile 프로그램에서 해당 기능을 추가한 확장 프로그램을 제작하게 되었다.

자동 셀 병합은 첫 번째 열에서만 한다.

- 허용되지 않은 문자에 `<pre>` 태그 추가
- collapse 기능 추가
  
```javascript
function toRedmine(range) {
  var _redmineContent = `{{collapse()
table{valign:top;font-size:small}.
|={background:#999999;color:#ffffff;}. Senario|={background:#999999;color:#ffffff;}. Test Case|={background:#999999;color:#ffffff;}. Expected|={background:#999999;color:#ffffff;}. Result|={background:#999999;color:#ffffff;}. Pass / Fail|={background:#999999;color:#ffffff;}. Comment|
`;
  var _range = SpreadsheetApp.getActiveSheet().getRange(range);
  var _values = _range.getDisplayValues();
  var _aligns = _range.getHorizontalAlignments();
  var blankNums = [];
  var temp = 1;
  var idx = 0;

  for (var i = 1; i < _values.length; i++){
    if (!_values[i][0]){
      temp += 1;
      if (i == _values.length-1)
        blankNums.push(temp);  
    }
    else if (_values[i][0]) {
      blankNums.push(temp);
      temp = 1;
      }
  }
  for (var i = 0; i < _values.length; i++) {
    var _row = _values[i];
    for (var j = 0; j < _row.length; j++) {
      var _align = _aligns[i][j];
      var _col = _row[j];
      if (_col.indexOf('|') != -1){
        _col = '<pre>' + _col + '</pre>';
      }
    if (!(j == 0 && !_col))
      _redmineContent += '|';
    if (_align.indexOf('left') > -1){
      if(j == 0 && _values[i][j]){
        if(blankNums[idx] > 1 && blankNums[idx])
           _redmineContent += `/${blankNums[idx]}<.`;
        idx += 1;
    }
    else
      _redmineContent += '<.'
  }
  if (!(j == 0 && !_col))
    _redmineContent += ' ' + _col + ' ';
  }
  _redmineContent += '|\r\n'
  }
return _redmineContent + '}}';
}
```
  - 구글 스프레드 시트에서 확장 프로그램의 Apps Script로 들어간다.
  - 해당 내용을 붙여넣은 후 저장한다.
  - 스프레드 시트 내에 셀을 선택한 후 `=toRedmine("[변환 하고싶은 범위]")`를 입력하면 된다.
