面試 20200114
===

請將解答置於**作答**處，或置於jsbin當中。

## 求字串解

已知下面的 hash 虛擬碼函式，只包含指定的字元「abcegikmnoprstuy」。
如果使用一組長度為 7個字元 的 字串為「keepacg」時，經過 hash 函式運算後可得到「1392137277795」；
請使用任意程式語言，找出一組長度為 8 的 字串Y ，經過 hash 函式運算後可得到「56697707740052」的結果。

hash 函式的虛擬碼（pseudo-code）如下：

```clike=
Int64 hash (String s) {
    Int64 h = 7
    String letters = "abcegikmnoprstuy"
    for(Int32 i = 0; i < s.length; i++) {
        h = (h * 41 + letters.indexOf(s[i]))
    }
    return h
}

hash("keepacg") // 1392137277795
hash(Y) // 56697707740052
```

### 作答1
```javascript=
//



```

---

## 修改現行程式碼
 
修改或重構現行程式碼,
並於函數當中新增一個 type 為「爭議」的流程，
寫入的 資料表 為「table_d」，所需欄位同「解禁」

```javascript=
//Globals: MyTask,Server
//=======================================================================
// 生產出貨解禁止確認單 (CPE003).單身資料轉入DB.自動執行
//=======================================================================

var aInstance = MyTask.getArtInstance();
var fmBU = aInstance.getAppValue("BU");
var fmType = aInstance.getAppValue("type");

function quoteReplace(str) {
  return str.replaceAll('\'', '');
}

function main() {
  if (fmBU.equals("主板")) {
    var conn = Server.createSessionConnection(15);
  } else {
    var conn = Server.createSessionConnection(2);
  }
  if (conn == null) {
    Server.addErrLog("拋轉資料失敗,無法連線至資料庫!!");
  }

  var hasErr = false;
  var j = 0;

  if (fmType.equals("禁止")) {
    //表單單號
    var ansID = aInstance.getID();
    selectSQL = "select ITEM22,ITEM24,ITEM23,InsID from ART00211243315371881ITEM124 where insid = '" + ansID + "'";
    selectList = Server.SQLloadValue(selectSQL);
    for (i = 0; i < selectList.size(); i++) {
      var currtrecord = selectList.get(i);
      //筆數變數
      var Num = (i + 1);
      //產品編號 ITEM22
      var ITEM22 = quoteReplace(currtrecord.get("ITEM22"));
      //備註　ITEM24
      var ITEM24 = quoteReplace(currtrecord.get("ITEM24"));
      //排除料號 ITEM23
      var ITEM23 = quoteReplace(currtrecord.get("ITEM23"));
      //單據編號 InsID
      var InsID = currtrecord.get("InsID");
      var insertSQL = "insert into table_a_temp(table_a02, table_a03, table_a04, table_a05, table_ael01, table_ael02, table_ael03, table_ael06) values('" + Num + "', '" + ITEM22 + "', '" + ITEM24 + "', '" + ITEM23 + "', 'CPE003', '" + InsID + "', today, 'N') ";
      try {
        if (conn.updateValue(insertSQL)) {
          j++;
        } else {
          hasErr = true;
          //刪除table_a_temp
          var deleteSQL = "delete from table_a_temp where table_ael02 = '" + ansID + "'";
          conn.updateValue(deleteSQL);
          Server.addErrLog("拋轉資料到table_a_temp失敗,表單編號=" + ansID);
        }
      } catch (e) {
        hasErr = true;

        //刪除table_a_temp
        var deleteSQL = "delete from table_a_temp where table_ael02 = '" + ansID + "'";
        conn.updateValue(deleteSQL);
        Server.addErrLog("拋轉資料到table_a_temp失敗,表單編號=" + ansID + ",異常訊息" + e.message);
      }
    }
    Server.addExeLog("拋轉資料到table_a_temp成功 " + j + " 筆");
  } else if (fmType.equals("解禁")) {
    var ansID = aInstance.getID();
    selectSQL = "select ITEM22, ITEM24, ITEM23, InsID from ART00211243315371881ITEM124 where insid = '" + ansID + "'";
    selectList = Server.SQLloadValue(selectSQL);
    for (i = 0; i < selectList.size(); i++) {
      var currtrecord = selectList.get(i);
      //筆數變數
      var Num = (i + 1);
      //產品編號 ITEM22
      var ITEM22 = quoteReplace(currtrecord.get("ITEM22"));
      //備註 ITEM24
      var ITEM24 = quoteReplace(currtrecord.get("ITEM24"));
      //排除料號 ITEM23
      var ITEM23 = quoteReplace(currtrecord.get("ITEM23"));
      //單據編號 InsID
      var InsID = currtrecord.get("InsID");
      var insertSQL = "insert into table_b_temp(table_b02, table_b03, table_b04, table_bel01, table_bel02, table_bel03, table_bel06) values('" + Num + "', '" + ITEM22 + "', '" + ITEM24 + "', 'CPE003', '" + InsID + "', today, 'N') ";
      try {
        if (conn.updateValue(insertSQL)) {
          j++;
        }
        else {
          hasErr = true;
          //刪除table_b_temp
          var deleteSQL = "delete from table_b_temp where table_bel02 = '" + ansID + "'";
          conn.updateValue(deleteSQL);
          Server.addErrLog("拋轉資料到table_b_temp失敗,表單編號=" + ansID);
        }
      } catch (e) {
        hasErr = true;

        //刪除table_b_temp
        var deleteSQL = "delete from table_b_temp where table_bel02 = '" + ansID + "'";
        conn.updateValue(deleteSQL);
        Server.addErrLog("拋轉資料到table_b_temp失敗,表單編號=" + ansID + ",異常訊息" + e.message);
      }
    }
    Server.addExeLog("拋轉資料到table_b_temp成功 " + j + " 筆");
  } else if (fmType.equals("結案")) {
    var ansID = aInstance.getID();
    selectSQL = "select ITEM22, ITEM24, InsID from ART00211243315371881ITEM124 where insid = '" + ansID + "'";
    selectList = Server.SQLloadValue(selectSQL);
    for (i = 0; i < selectList.size(); i++) {
      var currtrecord = selectList.get(i);
      //筆數變數
      var Num = (i + 1);
      //產品編號 ITEM22
      var ITEM22 = quoteReplace(currtrecord.get("ITEM22"));
      //備註 ITEM24
      var ITEM24 = quoteReplace(currtrecord.get("ITEM24"));
      //單據編號 InsID
      var InsID = currtrecord.get("InsID");
      var insertSQL = "insert into table_c_temp(table_c02, table_c03, table_c04, table_cel01, table_cel02, table_cel03, table_cel06) values('" + Num + "', '" + ITEM22 + "', '" + ITEM24 + "', 'CPE003', '" + InsID + "', today, 'N') ";
      try {
        if (conn.updateValue(insertSQL)) { j++; }
        else {
          hasErr = true;
          //刪除table_b_temp
          var deleteSQL = "delete from table_c_temp where table_cel02 = '" + ansID + "'";
          conn.updateValue(deleteSQL);
          Server.addErrLog("拋轉資料到table_c_temp失敗,表單編號=" + ansID);
        }
      } catch (e) {
        hasErr = true;
        //刪除table_b_temp
        var deleteSQL = "delete from table_c_temp where table_cel02 = '" + ansID + "'";
        conn.updateValue(deleteSQL);
        Server.addErrLog("拋轉資料到table_c_temp失敗,表單編號=" + ansID + ",異常訊息" + e.message);
      }
    }
    Server.addExeLog("拋轉資料到table_c_temp成功 " + j + " 筆");
  }

  if (hasErr) {
    conn.rollback();
    Server.flowTo(MyTask, "AST01751243837794712");
  } else {
    var df = new java.text.SimpleDateFormat("yyyy/MM/dd HH:mm");
    var currentTime = df.format(java.util.Calendar.getInstance().getTime());
    var aInstance = MyTask.getArtInstance();
    var fmBU = aInstance.getAppValue("BU");
    if (!fmType.equals("結案")) {
      aInstance.setAppValue("importDate", currentTime);
    }
    conn.commit();
    Server.flowTo(MyTask, "AST01771243838136013");
  }
}

main();

```

### 作答2
```javascript=

```

###### tags: `面試`
