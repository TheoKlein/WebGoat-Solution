Hijack a Session
====
使用工具
----
*    WebScarab
*    JHijack
  
* * *

這題題目擺明就是要我們攔截Session加以分析，若Session長度或複雜度不夠，可以藉由分析伺服器回傳的Session來找出Session的規律，進而找到伺服器發給其他人使用的Session並偽裝成其他人登入。

![Hijack a Session-01](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-01.png)

既然如此，我們就用WebScarab攔截request來看看。開啟WebScarab之後再嘗試登入一次，可以看到WebScarab中已經有資料被列出來了。

![Hijack a Session-03](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-03.png)

![Hijack a Session-02](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-02.png)

WebScarab切換到SessionID Analysis的頁面，Previous Request的欄位選擇URL:/WebGoat/attack，下方Raw的部分可以看見request詳細的內容，其中有一個特別的參數「WEAKID」，參數名字就這麼大剌剌的說這是一個“弱弱的ID”，那我們就針對這個來做分析，試著找出產生WEAKID的規律。

![Hijack a Session-04](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-04.png)

這時我們可以把Raw裡的WEAKID刪除，按下方的test嘗試送出給伺服器，發現伺服器會再產生一個新的WEAKID！

![Hijack a Session-05](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-05.png)

這時我們可以利用WebScarab送出多筆test來拿到多筆WEAKID，下方Samples輸入要測試多少資料後點選Fetch。

![Hijack a Session-06](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-06.png)

![Hijack a Session-07](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-07.png)

這時候我們就可以從一整串伺服器傳回來的WEKID中，尋找被「跳過」的WEAKID，代表這個WEAKID是存在並且屬於其他使用者的。這裡我們找到「17109-」和「17112-」，中間少了「17110-」和「17111-」，我們只要挑其中一個就好。在更仔細看，後半段只有倒數三位數字在變化。

![Hijack a Session-08](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-08.png)

這時候開啟JHijack來準備嘗試所有可能，上方的路徑參數請依照自己的環境設定，特別的是Grep的部分，我們知道WebGoat如果解題成功畫面上會有「Congratulations」的字，這個欄位就是在每次嘗試以後去尋找頁面中有無此字串。SESSION就填入攔截到的「JSESSIONID」、HijackID就是重點，這個欄位是我們想要多次嘗試不同可能的參數，此題我們要是的就是WEAKID，前面我們可以確定的部分直接填上，最後三碼不確定打上「$」，「$」的範圍就是下方的Range，剛剛有看到後三碼也是有規律的生成，所以範圍一定會介於前後兩者中間，若不確定也可以直接從「001」跑到「999」，直接把所有可能是一遍。

![Hijack a Session-09](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-09.png)

都設定好之後就可以按下Hijack，程式會把我們設定WEAKID的所有可能都送到伺服器嘗試，馬上就找到正確的WEAKID了！

![Hijack a Session-10](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-10.png)

這時我們再回到WebScarab開啟監聽攔截request並把WEAKID直接修改成我們剛剛猜到的值之後送出就可以成功以他人的WEAKID登入了！

![Hijack a Session-11](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-11.png)

![Hijack a Session-12](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-12.png)

![Hijack a Session-13](../_Screenshot/Session_Management_Flaws/Hijack_a_Session/Hijack_a_Session-13.png)