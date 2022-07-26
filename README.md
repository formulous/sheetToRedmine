# sheetToRedmine

  구글 스프레드 시트를 레드마인의 표 형식으로 변환해주는 프로그램이다. 구글 스프레드 시트의 확장프로그램인 Apps Script에서 사용할 수 있다.
  ```javascript
  function toRedmine4(range) {
  var _redmineContent = '';
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
  return _redmineContent;
}
```
  사용법은 구글 스프레드 시트에서 셀을 선택한 후 =toRedmine4("[변환 하고싶은 범위]")를 입력하면 된다.
