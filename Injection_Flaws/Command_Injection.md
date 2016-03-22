Command Injection
====
使用工具
----
*    WebScarab

* * *

這是有關Injection部分的第一關，題目的要求很簡單，提供一個HTML select的form，要我們想辦法把command注入伺服器的系統。

![Command Injection-01](../_Screenshot/Injection_Flaws/Command_Ijection/Command_Ijection-01.png)

既然這樣就先看看選項裡有什麼東西吧，滿滿.help結尾的選項，其中還有一項和題目同名的選項就選他吧！（並不是非得選CommandInjection.help，其他選項也行。）

![Command Injection-02](../_Screenshot/Injection_Flaws/Command_Ijection/Command_Ijection-02.png)

輸出的結果是有關選項的說明，其中注意到紅底線的部分是呼叫.html檔案的指令，就是我們要著手的部分。

![Command Injection-03](../_Screenshot/Injection_Flaws/Command_Ijection/Command_Ijection-03.png)

接下來我使用的是WebScarab，啟動監聽以後再回到WebGoat點選一次表單讓WebScarab攔截request。

![Command Injection-04](../_Screenshot/Injection_Flaws/Command_Ijection/Command_Ijection-04.png)

若成功攔截到request會跳出一個視窗

![Command Injection-05](../_Screenshot/Injection_Flaws/Command_Ijection/Command_Ijection-05.png)

可以點選Transform來把GET轉換成POST。

![Command Injection-06](../_Screenshot/Injection_Flaws/Command_Ijection/Command_Ijection-06.png)

轉成POST以後可以很清楚地看見傳送的參數，其中HelpFile的變數存了剛剛選擇的選項。

![Command Injection-07](../_Screenshot/Injection_Flaws/Command_Ijection/Command_Ijection-07.png)

回去比對一下剛剛呼叫.html檔案的指令，當我們選擇了AccessControlMatrix.help的選項時，整串呼叫指令是用「'」單引號包著，其中檔案路徑是用「"」雙引號，那我們就可以在WebScarab中嘗試修改HelpFile的值來進行injection :`AccessControlMatrix.help" & ifconfig"`

![Command Injection-08](../_Screenshot/Injection_Flaws/Command_Ijection/Command_Ijection-08.png)

第一個「"」雙引號用來結束前面的檔案路徑，第二個「"」用來搭配系統預設結尾的「"」，變成「""」兩個雙引號內沒有包含任何東西，讓中間的「& ifconfig」成為獨立的command。

最後把修改過的request轉回GET以後在點選Accept changes送出這個request。

![Command Injection-09](../_Screenshot/Injection_Flaws/Command_Ijection/Command_Ijection-09.png)

恭喜通過了Injection Flaws的第一關！
可以看見ExecResults的部分就是command injection的重點，ifconfig在下方的確也顯示出了伺服器的資料。

![Command Injection-10](../_Screenshot/Injection_Flaws/Command_Ijection/Command_Ijection-10.png)
