.. highlight:: cl

第二章：歡迎來到 Lisp
**************************************************

本章的目的是讓你儘快開始編程。本章結束時，你會掌握足夠多的 Common Lisp 知識來開始寫程式。

2.1 形式 (Form)
===================

人可以通過實踐來學習一件事，這對於 Lisp 來說特別有效，因爲 Lisp 是一門交互式的語言。任何 Lisp 系統都含有一個交互式的前端，叫做\ *頂層*\ (toplevel)。你在頂層輸入 Lisp 表達式，而系統會顯示它們的值。

Lisp 通常會打印一個提示符告訴你，它正在等待你的輸入。許多 Common Lisp 的實現用 ``>`` 作爲頂層提示符。本書也沿用這個符號。

一個最簡單的 Lisp 表達式是整數。如果我們在提示符後面輸入 ``1`` ，

::

  > 1
  1
  >

系統會打印出它的值，接著印出另一個提示符，告訴你它在等待更多的輸入。

在這個情況裡，打印的值與輸入的值相同。數字 ``1`` 稱之爲對自身求值。當我們輸入需要做某些計算來求值的表達式時，生活變得更加有趣了。舉例來說，如果我們想把兩個數相加，我們輸入像是：

::

  > (+ 2 3)
  5

在表達式 ``(+ 2 3)`` 裡， ``+`` 稱爲運算子，而數字 ``2`` 跟 ``3`` 稱爲實參。

在日常生活中，我們會把表達式寫作 ``2 + 3`` ，但在 Lisp 裡，我們把 ``+`` 運算子寫在前面，接著寫實參，再把整個表達式用一對括號包起來： ``(+ 2 3)`` 。這稱爲\ *前序*\ 表達式。一開始可能覺得這樣寫表達式有點怪，但事實上這種表示法是 Lisp 最美妙的東西之一。

舉例來說，我們想把三個數加起來，用日常生活的表示法，要寫兩次 ``+`` 號，

::

  2 + 3 + 4

而在 Lisp 裡，只需要增加一個實參：

::

  (+ 2 3 4)

日常生活中用 ``+`` 時，它必須有兩個實參，一個在左，一個在右。前序表示法的靈活性代表著，在 Lisp 裡， ``+`` 可以接受任意數量的實參，包含了沒有實參：

::

  > (+)
  0
  > (+ 2)
  2
  > (+ 2 3)
  5
  > (+ 2 3 4)
  9
  > (+ 2 3 4 5)
  14

由於運算子可接受不定數量的實參，我們需要用括號來標明表達式的開始與結束。

表達式可以巢狀。即表達式裡的實參，可以是另一個複雜的表達式：

::

  > (/ (- 7 1) (- 4 2))
  3

上面的表達式用中文來說是， (七減一) 除以 (四減二) 。

Lisp 表示法另一個美麗的地方是：它就是如此簡單。所有的 Lisp 表達式，要麼是 ``1`` 這樣的數原子，要麼是包在括號裡，由零個或多個表達式所構成的列表。以下是合法的 Lisp 表達式：

::

  2 (+ 2 3) (+ 2 3 4) (/ (- 7 1) (- 4 2))

稍後我們將理解到，所有的 Lisp 程式都採用這種形式。而像是 C 這種語言，有著更複雜的語法：算術表達式採用中序表示法；函數呼叫採用某種前序表示法，實參用逗號隔開；表達式用分號隔開；而一段程式用大括號隔開。

在 Lisp 裡，我們用單一的表示法，來表達所有的概念。

2.2 求值 (Evaluation)
==========================

上一小節中，我們在頂層輸入表達式，然後 Lisp 顯示它們的值。在這節裡我們深入理解一下表達式是如何被求值的。

在 Lisp 裡， ``+`` 是函數，然而如 ``(+ 2 3)`` 的表達式，是函數呼叫。

當 Lisp 對函數呼叫求值時，它做下列兩個步驟：

  1. 首先從左至右對實參求值。在這個例子當中，實參對自身求值，所以實參的值分別是 ``2`` 跟 ``3`` 。
  2. 實參的值傳入以運算子命名的函數。在這個例子當中，將 ``2`` 跟 ``3`` 傳給 ``+`` 函數，返回 ``5`` 。

如果實參本身是函數呼叫的話，上述規則同樣適用。以下是當 ``(/ (- 7 1) (- 4 2))`` 表達式被求值時的情形：

  1. Lisp 對 ``(- 7 1)`` 求值: ``7`` 求值爲 ``7`` ， ``1`` 求值爲 ``1`` ，它們被傳給函數 ``-`` ，返回 ``6`` 。
  2. Lisp 對 ``(- 4 2)`` 求值: ``4`` 求值爲 ``4`` ， ``2`` 求值爲 ``2`` ，它們被傳給函數 ``-`` ，返回 ``2`` 。
  3. 數值 ``6`` 與 ``2`` 被傳入函數 ``/`` ，返回 ``3`` 。

但不是所有的 Common Lisp 運算子都是函數，不過大部分是。函數呼叫都是這麼求值。由左至右對實參求值，將它們的數值傳入函數，來返回整個表達式的值。這稱爲 Common Lisp 的求值規則。

.. note::

  逃離麻煩

  如果你試著輸入 Lisp 不能理解的東西，它會打印一個錯誤訊息，接著帶你到一種叫做\ *中斷迴圈*\ （break loop）的頂層。
  中斷迴圈給予有經驗的程式設計師一個機會，來找出錯誤的原因，不過最初你只會想知道如何從中斷迴圈中跳出。
  如何返回頂層取決於你所使用的 Common Lisp 實現。在這個假定的實現環境中，輸入 ``:abort`` 跳出：

  .. code-block:: cl

    > (/ 1 0)
    Error: Division by zero
          Options: :abort, :backtrace
    >> :abort
    >

  附錄 A 示範了如何除錯 Lisp 程式，並給出一些常見的錯誤例子。

一個不遵守 Common Lisp 求值規則的運算子是 ``quote`` 。 ``quote`` 是一個特殊的運算子，意味著它自己有一套特別的求值規則。這個規則就是：什麼也不做。 ``quote`` 運算子接受一個實參，並完封不動地返回它。

::

   > (quote (+ 3 5))
   (+ 3 5)

爲了方便起見，Common Lisp 定義 ``'`` 作爲 ``quote`` 的縮寫。你可以在任何的表達式前，貼上一個 ``'`` ，與呼叫 ``quote`` 是同樣的效果：

::

   > '(+ 3 5)
   (+ 3 5)

使用縮寫 ``'`` 比使用整個 ``quote`` 表達式更常見。

Lisp 提供 ``quote`` 作爲一種\ *保護*\ 表達式不被求值的方式。下一節將解釋爲什麼這種保護很有用。

2.3 資料 (Data)
=====================

Lisp 提供了所有在其他語言找的到的，以及其他語言所找不到的資料型別。一個我們已經使用過的型別是\ *整數*\（integer），整數用一系列的數字來表示，比如： ``256`` 。另一個 Common Lisp 與多數語言有關，並很常見的資料型別是\ *字串*\ （string），字串用一系列被雙引號包住的字元串表示，比如： ``"ora et labora"`` [#]_ 。整數與字串一樣，都是對自身求值的。

.. [#] “ora et labora” 是拉丁文，意思是禱告與工作。

有兩個通常在別的語言所找不到的 Lisp 資料型別是\ *符號*\ （symbol）與\ *列表*\ （lists），\ *符號*\ 是英語的單詞 (words)。無論你怎麼輸入，通常會被轉換爲大寫：

::

  > 'Artichoke
  ARTICHOKE

符號（通常）不對自身求值，所以要是想引用符號，應該像上例那樣用 ``'`` 引用它。

*列表*\ 是由被括號包住的零個或多個元素來表示。元素可以是任何型別，包含列表本身。使用列表必須要引用，不然 Lisp 會以爲這是個函數呼叫：

::

  > '(my 3 "Sons")
  (MY 3 "Sons")
  > '(the list (a b c) has 3 elements)
  (THE LIST (A B C) HAS 3 ELEMENTS)

注意引號保護了整個表達式，以及裡面的表達式被求值。

你可以呼叫 ``list`` 來創建列表。由於 ``list`` 是函數，所以它的實參會被求值。這裡我們看一個在函數 ``list`` 呼叫裡面，呼叫 ``+`` 函數的例子：

::

  > (list 'my (+ 2 1) "Sons")
  (MY 3 "Sons")

我們現在來到領悟 Lisp 最卓越特性的地方之一。\ *Lisp的程式是用列表來表示的*\ 。如果實參的優雅與彈性不能說服你 Lisp 表示法是無價的工具，這裡應該能使你信服。這代表著 Lisp 程式可以寫出 Lisp 程式。 Lisp 程式設計師可以（並且經常）寫出能爲自己寫程式的程式。

不過得到第 10 章，我們才來考慮這種程式，但現在了解到列表和表達式的關係是非常重要的，而不是被它們搞混。這也就是爲什麼我們需要 ``quote`` 。如果一個列表被引用了，則求值規則對列表自身來求值；如果沒有被引用，則列表被視爲是程式碼，依求值規則對列表求值後，返回它的值。

::

  > (list '(+ 2 1) (+ 2 1))
  ((+ 2 1) 3)

這裡第一個實參被引用了，所以產生一個列表。第二個實參沒有被引用，視爲函數呼叫，經求值後得到一個數字。

在 Common Lisp 裡有兩種方法來表示空列表。你可以用一對不包括任何東西的括號來表示，或用符號 ``nil`` 來表示空表。你用哪種表示法來表示空表都沒關係，但它們都會被顯示爲 ``nil`` ：

::

  > ()
  NIL
  > nil
  NIL

你不需要引用 ``nil`` （但引用也無妨），因爲 ``nil`` 是對自身求值的。

2.4 列表操作 (List Operations)
==================================

用函數 ``cons`` 來構造列表。如果傳入的第二個實參是一個列表，則返回一個由第二個實參所構成的新列表，其中，新列表的第一個元素是先前傳入的第一個實參：

::

  > (cons 'a '(b c d))
  (A B C D)

可以通過把新元素建立在空表之上，來構造一個新列表。上一節所看到的函數 ``list`` ，不過就是一個把幾個元素加到 ``nil`` 上的快捷方式：

::

  > (cons 'a (cons 'b nil))
  (A B)
  > (list 'a 'b)
  (A B)

取出列表元素的基本函數是 ``car`` 和 ``cdr`` 。對列表取 ``car`` 返回第一個元素，而對列表取 ``cdr`` 返回第一個元素之後的所有元素：

::

  > (car '(a b c))
  A
  > (cdr '(a b c))
  (B C)

你可以把 ``car`` 與 ``cdr`` 混合使用來取得列表中的任何元素。如果我們想要取得第三個元素，我們可以：

::

  > (car (cdr (cdr '(a b c d))))
  C

不過，你可以用更簡單的 ``third`` 來做到同樣的事情：

::

  > (third '(a b c d))
  C

2.5 真與假 (Truth)
===========================

在 Common Lisp 裡，符號 ``t`` 是表示邏輯 ``真`` 的預設值。與 ``nil`` 相同， ``t``  也是對自身求值的。如果實參是一個列表，則函數 ``listp`` 返回 ``真`` ：

::

  > (listp '(a b c))
  T

函數的返回值將會被解釋成邏輯 ``真`` 或邏輯 ``假`` 時，則稱此函數爲謂詞（\ *predicate*\ ）。在 Common Lisp 裡，謂詞的名字通常以 ``p`` 結尾。

邏輯 ``假`` 在 Common Lisp 裡，用 ``nil`` ，即空表來表示。如果我們傳給 ``listp`` 的實參不是列表，則返回 ``nil`` 。

::

  > (listp 27)
  NIL

由於 ``nil`` 在 Common Lisp 裡扮演兩個角色，如果實參是一個空表，則函數 ``null`` 返回 ``真`` 。

::

  > (null nil)
  T

而如果實參是邏輯 ``假`` ，則函數 ``not`` 返回 ``真`` ：

::

  > (not nil)
  T

``null`` 與 ``not`` 做的是一樣的事情。

在 Common Lisp 裡，最簡單的條件式是 ``if`` 。通常接受三個實參：一個 *test* 表達式，一個 *then* 表達式和一個 *else* 表達式。若 ``test`` 表達式求值爲邏輯 ``真`` ，則對 ``then`` 表達式求值，並返回這個值。若 ``test`` 表達式求值爲邏輯 ``假`` ，則對 ``else`` 表達式求值，並返回這個值：

::

  > (if (listp '(a b c))
        (+ 1 2)
        (+ 5 6))
  3
  > (if (listp 27)
        (+ 1 2)
        (+ 5 6))
  11

與 ``quote`` 相同， ``if`` 是特殊的運算子。不能用函數來實現，因爲實參在函數呼叫時永遠會被求值，而 ``if`` 的特點是，只有最後兩個實參的其中一個會被求值。 ``if`` 的最後一個實參是選擇性的。如果忽略它的話，預設值是 ``nil`` ：

::

  > (if (listp 27)
       (+ 1 2))
  NIL

雖然 ``t`` 是邏輯 ``真`` 的預設表示法，任何非 ``nil`` 的東西，在邏輯的上下文裡通通被視爲 ``真`` 。

::

  > (if 27 1 2)
  1

邏輯運算子 ``and`` 和 ``or`` 與條件式類似。兩者都接受任意數量的實參，但僅對能影響返回值的幾個實參求值。如果所有的實參都爲 ``真`` （即非 ``nil`` ），那麼 ``and`` 會返回最後一個實參的值：

::

   > (and t (+ 1 2))
   3

如果其中一個實參爲 ``假`` ，那之後的所有實參都不會被求值。 ``or`` 也是如此，只要碰到一個爲 ``真`` 的實參，就停止對之後所有的實參求值。

以上這兩個運算子稱爲\ *宏*\ 。宏和特殊的運算子一樣，可以繞過一般的求值規則。第十章解釋了如何編寫你自己的宏。

2.6 函數 (Functions)
===========================

你可以用 ``defun`` 來定義新函數。通常接受三個以上的實參：一個名字，一組用列表表示的實參，以及一個或多個組成函數體的表達式。我們可能會這樣定義 ``third`` ：

::

  > (defun our-third (x)
     (car (cdr (cdr x))))
  OUR-THIRD

第一個實參說明此函數的名稱將是 ``our-third`` 。第二個實參，一個列表 ``(x)`` ，說明這個函數會接受一個形參： ``x`` 。這樣使用的佔位符符號叫做\ *變數*\ 。當變數代表了傳入函數的實參時，如這裡的 ``x`` ，又被叫做\ *形參*\ 。

定義的剩餘部分， ``(car (cdr (cdr x)))`` ，即所謂的函數主體。它告訴 Lisp 該怎麼計算此函數的返回值。所以呼叫一個 ``our-third`` 函數，對於我們作爲實參傳入的任何 ``x`` ，會返回  ``(car (cdr (cdr x)))``  ：

::

  > (our-third '(a b c d))
  C

既然我們已經討論過了變數，理解符號是什麼就更簡單了。符號是變數的名字，符號本身就是以物件的方式存在。這也是爲什麼符號，必須像列表一樣被引用。列表必須被引用，不然會被視爲程式碼。符號必須要被引用，不然會被當作變數。

你可以把函數定義想成廣義版的 Lisp 表達式。下面的表達式測試 ``1`` 和 ``4`` 的和是否大於 ``3`` ：

::

  > (> (+ 1 4) 3)
  T

通過將這些數字替換爲變數，我們可以寫個函數，測試任兩數之和是否大於第三個數：

::

  > (defun sum-greater (x y z)
     (> (+ x y) z))
  SUM-GREATER
  > (sum-greater 1 4 3)
  T

Lisp 不對程式、過程以及函數作區別。函數做了所有的事情（事實上，函數是語言的主要部分）。如果你想要把你的函數之一作爲主函數（\ *main* function），可以這麼做，但平常你就能在頂層中呼叫任何函數。這表示當你編程時，你可以把程式拆分成一小塊一小塊地來做除錯。

2.7 遞迴 (Recursion)
===========================

上一節我們所定義的函數，呼叫了別的函數來幫它們做事。比如 ``sum-greater`` 呼叫了 ``+`` 和 ``>`` 。函數可以呼叫任何函數，包括自己。自己呼叫自己的函數是\ *遞迴*\ 的。 Common Lisp 函數 ``member`` ，測試某個東西是否爲列表的成員。下面是定義成遞迴函數的簡化版：

::

  > (defun our-member (obj lst)
     (if (null lst)
         nil
     (if (eql (car lst) obj)
         lst
         (our-member obj (cdr lst)))))
  OUR-MEMBER

謂詞 ``eql`` 測試它的兩個實參是否相等；此外，這個定義的所有東西我們之前都學過了。下面是運行的情形：

::

  > (our-member 'b '(a b c))
  (B C)
  > (our-member 'z '(a b c))
  NIL

下面是 ``our-member`` 的定義對應到英語的描述。爲了知道一個物件 ``obj`` 是否爲列表 ``lst`` 的成員，我們

  1. 首先檢查 ``lst`` 列表是否爲空列表。如果是空列表，那 ``obj`` 一定不是它的成員，結束。
  2. 否則，若 ``obj`` 是列表的第一個元素時，則它是列表的成員。
  3. 不然只有當 ``obj`` 是列表其餘部分的元素時，它才是列表的成員。

當你想要了解遞迴函數是怎麼工作時，把它翻成這樣的敘述有助於你理解。

起初，許多人覺得遞迴函數很難理解。大部分的理解難處，來自於對函數使用了錯誤的比喻。人們傾向於把函數理解爲某種機器。原物料像實參一樣抵達；某些工作委派給其它函數；最後組裝起來的成品，被作爲返回值運送出去。如果我們用這種比喻來理解函數，那遞迴就自相矛盾了。機器怎可以把工作委派給自己？它已經在忙碌中了。

較好的比喻是，把函數想成一個處理的過程。在過程裡，遞迴是在自然不過的事情了。日常生活中我們經常看到遞迴的過程。舉例來說，假設一個歷史學家，對歐洲歷史上的人口變化感興趣。研究文獻的過程很可能是：

  1. 取得一個文獻的複本
  2. 尋找關於人口變化的資訊
  3. 如果這份文獻提到其它可能有用的文獻，研究它們。

過程是很容易理解的，而且它是遞迴的，因爲第三個步驟可能帶出一個或多個同樣的過程。

所以，別把 ``our-member`` 想成是一種測試某個東西是否爲列表成員的機器。而是把它想成是，決定某個東西是否爲列表成員的規則。如果我們從這個角度來考慮函數，那麼遞迴的矛盾就不復存在了。

2.8 閱讀 Lisp (Reading Lisp)
==============================

上一節我們所定義的 ``our-member`` 以五個括號結尾。更複雜的函數定義更可能以七、八個括號結尾。剛學 Lisp 的人看到這麼多括號會感到氣餒。這叫人怎麼讀這樣的程式，更不用說編了？怎麼知道哪個括號該跟哪個匹配？

答案是，你不需要這麼做。 Lisp 程式設計師用縮排來閱讀及編寫程式，而不是括號。當他們在寫程式時，他們讓文字編輯器顯示哪個括號該與哪個匹配。任何好的文字編輯器，特別是 Lisp 系統自帶的，都應該能做到括號匹配（paren-matching）。在這種編輯器中，當你輸入一個括號時，編輯器指出與其匹配的那一個。如果你的編輯器不能匹配括號，別用了，想想如何讓它做到，因爲沒有這個功能，你根本不可能編 Lisp 程式 [1]_ 。

有了好的編輯器之後，括號匹配不再會是問題。而且由於 Lisp 縮排有通用的慣例，閱讀程式也不是個問題。因爲所有人都使用一樣的習慣，你可以忽略那些括號，通過縮排來閱讀程式。

任何有經驗的 Lisp 黑客，會發現如果是這樣的 ``our-member`` 的定義很難閱讀：

::

  (defun our-member (obj lst) (if (null lst) nil (if
  (eql (car lst) obj) lst (our-member obj (cdr lst)))))

但如果程式適當地縮排時，他就沒有問題了。可以忽略大部分的括號而仍能讀懂它：

::

  defun our-member (obj lst)
   if null lst
      nil
      if eql (car lst) obj
         lst
         our-member obj (cdr lst)

事實上，這是你在紙上寫 Lisp 程式的實用方法。等輸入程式至計算機的時候，可以利用編輯器匹配括號的功能。

2.9 輸入輸出 (Input and Output)
================================

到目前爲止，我們已經利用頂層偷偷使用了 I/O 。對實際的交互程式來說，這似乎還是不太夠。在這一節，我們來看幾個輸入輸出的函數。

最普遍的 Common Lisp 輸出函數是 ``format`` 。接受兩個或兩個以上的實參，第一個實參決定輸出要打印到哪裡，第二個實參是字串模版，而剩餘的實參，通常是要插入到字串模版，用打印表示法（printed representation）所表示的物件。下面是一個典型的例子：

::

  > (format t "~A plus ~A equals ~A. ~%" 2 3 (+ 2 3))
  2 plus 3 equals 5.
  NIL

注意到有兩個東西被打印出來。第一行是 ``format`` 印出來的。第二行是呼叫 ``format`` 函數的返回值，就像平常頂層會打印出來的一樣。通常像 ``format`` 這種函數不會直接在頂層呼叫，而是在程式內部裡使用，所以返回值不會被看到。

``format`` 的第一個實參 ``t`` ，表示輸出被送到預設的地方去。通常是頂層。第二個實參是一個用作輸出模版的字串。在這字串裡，每一個 ``~A`` 表示了被填入的位置，而 ``~%`` 表示一個換行。這些被填入的位置依序由後面的實參填入。

標準的輸入函數是 ``read`` 。當沒有實參時，會讀取預設的位置，通常是頂層。下面這個函數，提示使用者輸入，並返回任何輸入的東西：

::

  (defun askem (string)
   (format t "~A" string)
   (read))

它的行爲如下：

::

  > (askem "How old are you?")
  How old are you?29

  29

記住 ``read`` 會一直永遠等在這裡，直到你輸入了某些東西，並且（通常要）按下回車。因此，不印出明確的提示資訊是很不明智的，程式會給人已經死機的印象，但其實它是在等待輸入。

第二件關於 ``read`` 所需要知道的事是，它很強大： ``read`` 是一個完整的 Lisp 解析器（parser）。不僅是可以讀入字元，然後當作字串返回它們。它解析它所讀入的東西，並返回產生出來的 Lisp 物件。在上述的例子，它返回一個數字。

``askem`` 的定義雖然很短，但體現出一些我們在之前的函數沒看過的東西。函數主體可以有不只一個表達式。函數主體可以有任意數量的表達式。當函數被呼叫時，會依序求值，函數會返回最後一個的值。

在之前的每一節中，我們堅持所謂“純粹的” Lisp ── 即沒有副作用的 Lisp 。副作用是指，表達式被求值後，對外部世界的狀態做了某些改變。當我們對一個如 ``(+ 1 2)`` 這樣純粹的 Lisp 表達式求值時，沒有產生副作用。它只返回一個值。但當我們呼叫 ``format`` 時，它不僅返回值，還印出了某些東西。這就是一種副作用。

當我們想要寫沒有副作用的程式時，則定義多個表達式的函數主體就沒有意義了。最後一個表達式的值，會被當成函數的返回值，而之前表達式的值都被捨棄了。如果這些表達式沒有副作用，你沒有任何理由告訴 Lisp ，爲什麼要去對它們求值。

2.10 變數 (Variables)
===================================

``let`` 是一個最常用的 Common Lisp 的運算子之一，它讓你引入新的區域變數（local variable）：

::

   > (let ((x 1) (y 2))
        (+ x y))
   3

一個 ``let`` 表達式有兩個部分。第一個部分是一組創建新變數的指令，指令的形式爲 *(variable expression)* 。每一個變數會被賦予相對應表達式的值。上述的例子中，我們創造了兩個變數， ``x`` 和 ``y`` ，分別被賦予初始值 ``1`` 和 ``2`` 。這些變數只在 ``let`` 的函數體內有效。

一組變數與數值之後，是一個有表達式的函數體，表達式依序被求值。但這個例子裡，只有一個表達式，呼叫 ``+`` 函數。最後一個表達式的求值結果作爲 ``let`` 的返回值。以下是一個用 ``let`` 所寫的，更有選擇性的 ``askem`` 函數：

::

  (defun ask-number ()
   (format t "Please enter a number. ")
   (let ((val (read)))
     (if (numberp val)
         val
         (ask-number))))

這個函數創建了變數 ``val`` 來儲存 ``read`` 所返回的物件。因爲它知道該如何處理這個物件，函數可以先觀察你的輸入，再決定是否返回它。你可能猜到了， ``numberp`` 是一個謂詞，測試它的實參是否爲數字。

如果使用者不是輸入一個數字， ``ask-number`` 會持續呼叫自己。最後得到一個只接受數字的函數：

::

  > (ask-number)
  Please enter a number. a
  Please enter a number. (ho hum)
  Please enter a number. 52
  52

我們已經看過的這些變數都叫做區域變數。它們只在特定的上下文裡有效。另外還有一種變數叫做全局變數（global variable），是在任何地方都是可視的。 [2]_

你可以給 ``defparameter`` 傳入符號和值，來創建一個全局變數：

::

  > (defparameter *glob* 99)
  *GLOB*

全局變數在任何地方都可以存取，除了在定義了相同名字的區域變數的表達式裡。爲了避免這種情形發生，通常我們在給全局變數命名時，以星號作開始與結束。剛才我們創造的變數可以唸作 “星-glob-星” (star-glob-star)。

你也可以用 ``defconstant`` 來定義一個全局的常數：

::

  (defconstant limit (+ *glob* 1))

我們不需要給常數一個獨一無二的名字，因爲如果有相同名字存在，就會有錯誤產生 (error)。如果你想要檢查某些符號，是否爲一個全局變數或常數，使用 ``boundp`` 函數：

::

  > (boundp '*glob*)
  T

2.11 賦值 (Assignment)
================================

在 Common Lisp 裡，最普遍的賦值運算子（assignment operator）是 ``setf`` 。可以用來給全局或區域變數賦值：

::

  > (setf *glob* 98)
  98
  > (let ((n 10))
     (setf n 2)
     n)
  2

如果 ``setf`` 的第一個實參是符號（symbol），且符號不是某個區域變數的名字，則 ``setf`` 把這個符號設爲全局變數：

::

  > (setf x (list 'a 'b 'c))
  (A B C)

也就是說，通過賦值，你可以隱式地創建全局變數。
不過，一般來說，還是使用 ``defparameter`` 明確地創建全局變數比較好。

你不僅可以給變數賦值。傳入 ``setf`` 的第一個實參，還可以是表達式或變數名。在這種情況下，第二個實參的值被插入至第一個實參所引用的位置：

::

  > (setf (car x) 'n)
  N
  > x
  (N B C)

``setf`` 的第一個實參幾乎可以是任何引用到特定位置的表達式。所有這樣的運算子在附錄 D 中被標註爲 “可設置的”（“settable”）。你可以給 ``setf`` 傳入（偶數）個實參。一個這樣的表達式

::

  (setf a 'b
        c 'd
        e 'f)

等同於依序呼叫三個單獨的 ``setf`` 函數：

::

   (setf a 'b)
   (setf c 'd)
   (setf e 'f)

2.12 函數式編程 (Functional Programming)
=============================================

函數式編程意味著撰寫利用返回值而工作的程式，而不是修改東西。它是 Lisp 的主流範式。大部分 Lisp 的內建函數被呼叫是爲了取得返回值，而不是副作用。

舉例來說，函數 ``remove`` 接受一個物件和一個列表，返回不含這個物件的新列表：

::

  > (setf lst '(c a r a t))
  (C A R A T)
  > (remove 'a lst)
  (C R T)

爲什麼不乾脆說 ``remove`` 從列表裡移除一個物件？因爲它不是這麼做的。原來的表沒有被改變：

::

  > lst
  (C A R A T)

若你真的想從列表裡移除某些東西怎麼辦？在 Lisp 通常你這麼做，把這個列表當作實參，傳入某個函數，並使用 ``setf`` 來處理返回值。要移除所有在列表 ``x`` 的 ``a`` ，我們可以說：

::

  (setf x (remove 'a x))

函數式編程本質上意味著避免使用如 ``setf`` 的函數。起初可能覺得這根本不可能，更遑論去做了。怎麼可以只憑返回值來建立程式？

完全不用到副作用是很不方便的。然而，隨著你進一步閱讀，會驚訝地發現需要用到副作用的地方很少。副作用用得越少，你就更上一層樓。

函數式編程最重要的優點之一是，它允許交互式測試（interactive testing）。在純函數式的程式裡，你可以測試每個你寫的函數。如果它返回你預期的值，你可以有信心它是對的。這額外的信心，集結起來，會產生巨大的差別。當你改動了程式裡的任何一個地方，會得到即時的改變。而這種即時的改變，使我們有一種新的編程風格。類比於電話與信件，讓我們有一種新的通訊方式。

2.13 迭代 (Iteration)
=========================

當我們想重複做一些事情時，迭代比遞迴來得更自然。典型的例子是用迭代來產生某種表格。這個函數

::

  (defun show-squares (start end)
    (do ((i start (+ i 1)))
        ((> i end) 'done)
      (format t "~A ~A~%" i (* i i))))

列印從 ``start`` 到 ``end`` 之間的整數的平方：

::

  > (show-squares 2 5)
  2 4
  3 9
  4 16
  5 25
  DONE

``do`` 宏是 Common Lisp 裡最基本的迭代運算子。和 ``let`` 類似， ``do`` 可以創建變數，而第一個實參是一組變數的規格說明列表。每個元素可以是以下的形式

::

  (variable initial update)

其中 *variable* 是一個符號， *initial* 和 *update* 是表達式。最初每個變數會被賦予 *initial* 表達式的值；每一次迭代時，會被賦予 *update* 表達式的值。在 ``show-squares`` 函數裡， ``do`` 只創建了一個變數 ``i`` 。第一次迭代時， ``i`` 被賦與 ``start`` 的值，在接下來的迭代裡， ``i`` 的值每次增加 ``1`` 。

第二個傳給 ``do`` 的實參可包含一個或多個表達式。第一個表達式用來測試迭代是否結束。在上面的例子中，測試表達式是 ``(> i end)`` 。接下來在列表中的表達式會依序被求值，直到迭代結束。而最後一個值會被當作 ``do`` 的返回值來返回。所以 ``show-squares`` 總是返回 ``done`` 。

``do`` 的剩餘參陣列成了迴圈的函數體。在每次迭代時，函數體會依序被求值。在每次迭代過程裡，變數被更新，檢查終止測試條件，接著（若測試失敗）求值函數體。

作爲對比，以下是遞迴版本的 ``show-squares`` ：

::

  (defun show-squares (i end)
     (if (> i end)
       'done
       (progn
         (format t "~A ~A~%" i (* i i))
         (show-squares (+ i 1) end))))

唯一的新東西是 ``progn`` 。 ``progn`` 接受任意數量的表達式，依序求值，並返回最後一個表達式的值。

爲了處理某些特殊情況， Common Lisp 有更簡單的迭代運算子。舉例來說，要遍歷列表的元素，你可能會使用 ``dolist`` 。以下函數返回列表的長度：

::

  (defun our-length (lst)
    (let ((len 0))
      (dolist (obj lst)
        (setf len (+ len 1)))
      len))

這裡 ``dolist`` 接受這樣形式的實參\ *(variable expression)*\ ，跟著一個具有表達式的函數主體。函數主體會被求值，而變數相繼與表達式所返回的列表元素綁定。因此上面的迴圈說，對於列表 ``lst`` 裡的每一個 ``obj`` ，遞增 ``len`` 。很顯然這個函數的遞迴版本是：

::

  (defun our-length (lst)
   (if (null lst)
       0
       (+ (our-length (cdr lst)) 1)))

也就是說，如果列表是空表，則長度爲 ``0`` ；否則長度就是對列表取 ``cdr`` 的長度加一。遞迴版本的 ``our-length`` 比較易懂，但由於它不是尾遞迴（tail-recursive）的形式 (見 13.2 節)，效率不是那麼高。

2.14 函數作爲物件 (Functions as Objects)
==========================================

函數在 Lisp 裡，和符號、字串或列表一樣，是稀鬆平常的物件。如果我們把函數的名字傳給 ``function`` ，它會返回相關聯的物件。和 ``quote`` 類似， ``function`` 是一個特殊運算子，所以我們無需引用（quote）它的實參：

::

   > (function +)
   #<Compiled-Function + 17BA4E>

這看起來很奇怪的返回值，是在典型的 Common Lisp 實現裡，函數可能的打印表示法。

到目前爲止，我們僅討論過，不管是 Lisp 打印它們，還是我們輸入它們，看起來都是一樣的物件。但這個慣例對函數不適用。一個像是 ``+`` 的內建函數 ，在內部可能是一段機器語言程式碼（machine language code）。每個 Common Lisp 實現，可以選擇任何它喜歡的外部表示法（external representation）。

如同我們可以用 ``'`` 作爲 ``quote`` 的縮寫，也可以用 ``#'`` 作爲 ``function`` 的縮寫：

::

  > #'+
  #<Compiled-Function + 17BA4E>

這個縮寫稱之爲升引號（sharp-quote）。

和別種物件類似，可以把函數當作實參傳入。有個接受函數作爲實參的函數是 ``apply`` 。``apply`` 接受一個函數和實參列表，並返回把傳入函數應用在實參列表的結果：

::

  > (apply #'+ '(1 2 3))
  6
  > (+ 1 2 3)
  6

``apply`` 可以接受任意數量的實參，只要最後一個實參是列表即可：

::

  > (apply #'+ 1 2 '(3 4 5))
  15

函數 ``funcall`` 做的是一樣的事情，但不需要把實參包裝成列表。

::

   > (funcall #'+ 1 2 3)
   6

.. note::

   什麼是 ``lambda`` ？

   ``lambda`` 表達式裡的 ``lambda`` 不是一個運算子。而只是個符號。
   在早期的 Lisp 方言裡， ``lambda`` 存在的原因是：由於函數在內部是用列表來表示，
   因此辨別列表與函數的方法，就是檢查第一個元素是否爲 ``lambda`` 。

   在 Common Lisp 裡，你可以用列表來表達函數，
   函數在內部會被表示成獨特的函數物件。因此不再需要 `lambda` 了。
   如果需要把函數記爲

    .. code-block:: cl

      ((x) (+ x 100))

   而不是

    .. code-block:: cl

      (lambda (x) (+ x 100))

   也是可以的。

   但 Lisp 程式設計師習慣用符號 ``lambda`` ，來撰寫函數，
   因此 Common Lisp 爲了傳統，而保留了 ``lambda`` 。

``defun`` 宏，創建一個函數並給函數命名。但函數不需要有名字，而且我們不需要 ``defun`` 來定義他們。和大多數的 Lisp 物件一樣，我們可以直接引用函數。

要直接引用整數，我們使用一系列的數字；要直接引用一個函數，我們使用所謂的\ *lambda 表達式*\ 。一個 ``lambda`` 表達式是一個列表，列表包含符號 ``lambda`` ，接著是形參列表，以及由零個或多個表達式所組成的函數體。

下面的 ``lambda`` 表達式，表示一個接受兩個數字並返回兩者之和的函數：

::

  (lambda (x y)
   (+ x y))

列表 ``(x y)`` 是形參列表，跟在它後面的是函數主體。

一個 ``lambda`` 表達式可以作爲函數名。和普通的函數名稱一樣， lambda 表達式也可以是函數呼叫的第一個元素，

::

  > ((lambda (x) (+ x 100)) 1)
  101

而通過在 ``lambda`` 表達式前面貼上 ``#'`` ，我們得到對應的函數，

::

  > (funcall #'(lambda (x) (+ x 100))
            1)

``lambda`` 表示法除上述用途以外，還允許我們使用匿名函數。

2.15 型別 (Types)
=========================

Lisp 處理型別的方法非常靈活。在很多語言裡，變數是有型別的，得宣告變數的型別才能使用它。在 Common Lisp 裡，數值才有型別，而變數沒有。你可以想像每個物件，都貼有一個標明其型別的標籤。這種方法叫做\ *顯式型別*\ （\ *manifest typing*\ ）。你不需要宣告變數的型別，因爲變數可以存放任何型別的物件。

雖然從來不需要宣告型別，但出於效率的考量，你可能會想要宣告變數的型別。型別宣告在第 13.3 節時討論。

Common Lisp 的內建型別，組成了一個類別的層級。物件總是不止屬於一個型別。舉例來說，數字 27 的型別，依普遍性的增加排序，依序是 ``fixnum`` 、 ``integer`` 、 ``rational`` 、 ``real`` 、 ``number`` 、 ``atom`` 和 ``t`` 型別。（數值型別將在第 9 章討論。）型別 ``t`` 是所有型別的基類（supertype）。所以每個物件都屬於 ``t`` 型別。

函數 ``typep`` 接受一個物件和一個型別，然後判定物件是否爲該型別，是的話就返回真：

::

  > (typep 27 'integer)
  T

我們會在遇到各式內建型別時來討論它們。

2.16 展望 (Looking Forward)
==================================

本章僅談到 Lisp 的表面。然而，一種非比尋常的語言形象開始出現了。首先，這個語言用單一的語法，來表達所有的程式結構。語法基於列表，列表是一種 Lisp 物件。函數本身也是 Lisp 物件，函數能用列表來表示。而 Lisp 本身就是 Lisp 程式。幾乎所有你定義的函數，與內建的 Lisp 函數沒有任何區別。

如果你對這些概念還不太了解，不用擔心。 Lisp 介紹了這麼多新穎的概念，在你能駕馭它們之前，得花時間去熟悉它們。不過至少要了解一件事：在這些概念當中，有著優雅到令人吃驚的概念。

`Richard Gabriel <http://en.wikipedia.org/wiki/Richard_P._Gabriel>`_ 曾經半開玩笑的說， C 是拿來寫 Unix 的語言。我們也可以說， Lisp 是拿來寫 Lisp 的語言。但這是兩種不同的論述。一個可以用自己編寫的語言和一種適合編寫某些特定型別應用的語言，是有著本質上的不同。這開創了新的編程方法：你不但在語言之中編程，還把語言改善成適合程式的語言。如果你想了解 Lisp 編程的本質，理解這個概念是個好的開始。

Chapter 2 總結 (Summary)
================================

1. Lisp 是一種交互式語言。如果你在頂層輸入一個表達式， Lisp 會顯示它的值。

2. Lisp 程式由表達式組成。表達式可以是原子，或一個由運算子跟著零個或多個實參的列表。前序表示法代表運算子可以有任意數量的實參。

3. Common Lisp 函數呼叫的求值規則： 依序對實參從左至右求值，接著把它們的值傳入由運算子表示的函數。 ``quote`` 運算子有自己的求值規則，它完封不動地返回實參。

4. 除了一般的資料型別， Lisp 還有符號跟列表。由於 Lisp 程式是用列表來表示的，很輕鬆就能寫出能編程的程式。

5. 三個基本的列表函數是 ``cons`` ，它創建一個列表； ``car`` ，它返回列表的第一個元素；以及 ``cdr`` ，它返回第一個元素之後的所有東西。

6. 在 Common Lisp 裡， ``t`` 表示邏輯 ``真`` ，而 ``nil`` 表示邏輯 ``假`` 。在邏輯的上下文裡，任何非 ``nil`` 的東西都視爲 ``真`` 。基本的條件式是 ``if`` 。 ``and`` 與 ``or`` 是相似的條件式。

7. Lisp 主要由函數所組成。可以用 ``defun`` 來定義新的函數。

8. 自己呼叫自己的函數是遞迴的。一個遞迴函數應該要被想成是過程，而不是機器。

9. 括號不是問題，因爲程式設計師通過縮排來閱讀與編寫 Lisp 程式。

10. 基本的 I/O 函數是 ``read`` ，它包含了一個完整的 Lisp 語法分析器，以及 ``format`` ，它通過字串模板來產生輸出。

11. 你可以用 ``let`` 來創造新的區域變數，用 ``defparameter`` 來創造全局變數。

12. 賦值運算子是 ``setf`` 。它的第一個實參可以是一個表達式。

13. 函數式編程代表避免產生副作用，也是 Lisp 的主導思維。

14. 基本的迭代運算子是 ``do`` 。

15. 函數是 Lisp 的物件。可以被當成實參傳入，並且可以用 lambda 表達式來表示。

16. 在 Lisp 裡，是數值才有型別，而不是變數。

Chapter 2 習題 (Exercises)
==================================

1. 描述下列表達式求值之後的結果：

::

  (a) (+ (- 5 1) (+ 3 7))

  (b) (list 1 (+ 2 3))

  (c) (if (listp 1) (+ 1 2) (+ 3 4))

  (d) (list (and (listp 3) t) (+ 1 2))

2. 給出 3 種不同表示 ``(a b c)`` 的 ``cons 表達式`` 。

3. 使用 ``car`` 與 ``cdr`` 來定義一個函數，返回一個列表的第四個元素。

4. 定義一個函數，接受兩個實參，返回兩者當中較大的那個。

5. 這些函數做了什麼？

::

  (a) (defun enigma (x)
        (and (not (null x))
             (or (null (car x))
                 (enigma (cdr x)))))

  (b) (defun mystery (x y)
        (if (null y)
            nil
            (if (eql (car y) x)
                0
                (let ((z (mystery x (cdr y))))
                  (and z (+ z 1))))))

6. 下列表達式， ``x`` 該是什麼，才會得到相同的結果？

::

  (a) > (car (x (cdr '(a (b c) d))))
      B
  (b) > (x 13 (/ 1 0))
      13
  (c) > (x #'list 1 nil)
      (1)


7. 只使用本章所介紹的運算子，定義一個函數，它接受一個列表作爲實參，如果有一個元素是列表時，就返回真。

8. 給出函數的迭代與遞迴版本：

(a) 接受一個正整數，並打印出數字數量的點。

(b) 接受一個列表，並返回 ``a`` 在列表裡所出現的次數。

9. 一位朋友想寫一個函數，返回列表裡所有非 ``nil`` 元素的和。他寫了此函數的兩個版本，但兩個都不能工作。請解釋每一個的錯誤在哪裡，並給出正確的版本。

::

  (a) (defun summit (lst)
        (remove nil lst)
        (apply #'+ lst))

  (b) (defun summit (lst)
        (let ((x (car lst)))
          (if (null x)
              (summit (cdr lst))
              (+ x (summit (cdr lst))))))

.. rubric:: 腳註


.. [1] 在 vi，你可以用 :set sm 來啓用括號匹配。在 Emacs，M-x lisp-mode 是一個啓用的好方法。

.. [2] 真正的區別是詞法變數（lexical）與特殊變數（special variable），但到第六章才會討論這個主題。
