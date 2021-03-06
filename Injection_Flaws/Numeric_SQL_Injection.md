Numeric SQL Injection
====
使用工具
----
*    無

* * *

繼上一題[Injection Flaws - Command Injection](Command_Injection.md)之後，可以小小的感受到Injection攻擊的威力。
這題也是HTML網頁中常見的select表單，但下方特別寫出了後端呼叫資料庫的SQL語句。

![Numeric_SQL_Injection-01](../_Screenshot/Injection_Flaws/Numeric_SQL_Injection/Numeric_SQL_Injection-01.png)

那就選幾個來看看吧！我分別選了第一個「Columbia」和第二個「Seattle」，可以發現它的值是照著選項的順序從101開始，也就是說當我們選擇了第一選項「Columbia」，送出去的參數是「101」; 選擇了第二個選項「Seattle」時，送出去的參數就是「102」。

![Numeric_SQL_Injection-02](../_Screenshot/Injection_Flaws/Numeric_SQL_Injection/Numeric_SQL_Injection-02.png)
![Numeric_SQL_Injection-03](../_Screenshot/Injection_Flaws/Numeric_SQL_Injection/Numeric_SQL_Injection-03.png)

這下有趣了，讓我們看看select form的原始碼，可以對著select元件點右鍵選擇「檢查元素」，瀏覽器會開啟開發人員工具並自動標示到select的位置。

![Numeric_SQL_Injection-04](../_Screenshot/Injection_Flaws/Numeric_SQL_Injection/Numeric_SQL_Injection-04.png)
![Numeric_SQL_Injection-05](../_Screenshot/Injection_Flaws/Numeric_SQL_Injection/Numeric_SQL_Injection-05.png)

展開select的原始碼，可以看見總共有四個選項分別是101~104。

![Numeric_SQL_Injection-06](../_Screenshot/Injection_Flaws/Numeric_SQL_Injection/Numeric_SQL_Injection-06.png)

這時候就有個問題了，「weather_data」這個資料表中真的只有這四筆資料嗎？我們可以試著修改送出去的參數來撈出所有的資料看看，對著參數點兩下可以進行修改。

![Numeric_SQL_Injection-07](../_Screenshot/Injection_Flaws/Numeric_SQL_Injection/Numeric_SQL_Injection-07.png)

「value="101"」的部分，雙引號內包著的是101的值，剛才有看到顯示的SQL語句最後會是「WHERE station = 101」，我們試著把雙引號內的參數改成這樣：`'101' OR 1=1/*`

![Numeric_SQL_Injection-08](../_Screenshot/Injection_Flaws/Numeric_SQL_Injection/Numeric_SQL_Injection-08.png)

這樣當我們選擇第一個選項「Columbia」時，送出去的參數會變成「'101' OR 1=1/*」。關於這段參數，最開始的「'101'」是一個數值，後面的「1=1」會是一個永遠為真（true）的判斷，所以不管前面的數值有沒有存在在這個資料表中，經過OR運算這段命令永遠都會是真值。最後接一個「/\*」將後方其他不相關的程式碼註解掉。
我們剛剛改的是第一個選項「Columbia」的值，所以我們在選擇一次「Columbia」並按下Go!送出經過我們修改過的參數。

![Numeric_SQL_Injection-09](../_Screenshot/Injection_Flaws/Numeric_SQL_Injection/Numeric_SQL_Injection-09.png)

Bingo！

「weather_data」這個資料表中全部的資料全部都吐出來了，可以看到除了網頁中顯示的四個選項以外，還另外隱藏了兩筆資料！
