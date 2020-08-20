



# ABAP 代码备份









## 1. z_test_gsw_cc001



*&---------------------------------------------------------------------*
 *& Report Z_TEST_GSW_CC001
 *&---------------------------------------------------------------------*
 *&
 *&---------------------------------------------------------------------*
 REPORT z_test_gsw_cc001.

*******************************************************************************
*******************************************************************************
 *********************2020---4---21*********************************************
*******************************************************************************
*******************************************************************************


 *五个关键字 type,types,like ,data, tables
 \* type 指定数据类型     types 定义自定义数据类型
 \* ----eg.------ *
 TYPES :BEGIN OF mylist, "定义一个数据类型 ，名字是mylist
  name(10) TYPE c,    "定义 c 类型的字段【项】name 长度10
  number TYPE i,     "定义 i 类型的字段【项】number
  END OF mylist.     "定义自定义数据类型 mylist 的结束

 \* type 主要起到声明作用，不赋值
 \* like 和 type 的用法格式相同，eg

 DATA mycode1 LIKE sy-tcode.
 *data mycode2 TYPE mycode1.
 *指定名称为 mycode1 的变量类型与系统变量 SY-TCODE 相同。
 *不同的是 LIKE 用在已有值的数据项, 如系统变量, 而 TYPE 用在指定数据类型。
 *
 *数据项, 如系统变量, 而 TYPE 叙述则是用在指定数据类型。


 *DATA <f> [<length>] <type> [<value>] [<decimals>]
 DATA a TYPE p VALUE 10 DECIMALS 2.

 DATA:
   long TYPE c VALUE IS INITIAL,
   high TYPE p DECIMALS 2 VALUE '1.231'.


 *数据变量的定义
 DATA : BEGIN OF address,
   name(10) TYPE  c VALUE 'gsw',
   number TYPE p VALUE 666,
   END OF address.
 *使用时用字段变量加上组件名称, 如 address-name

 \* tables<dbtab>  tables: eban

 *
 *WRITE AT [/][<pos>][(<len>)] <f>.
 *斜线‘/’ 表示新的一 行。
 *<pos> 是最长为三位数字的数字或变量，表示在屏幕上的位置。
 *<len> 是最长为三位数字的数字或变量，表示输出长度。
 *如果输出内容只包含直接值（即不是变量） ，可以忽略关键字 AT。如果指定某一个位置
 *<pos>， 则无论在该位置是否有可用的空间或写有其它字段，总是在该位置输出字段。
 *如果输出长度 <len> 太短，则只显示部分字符 。数字字段从左边截断， 并用星号（ *）作前缀 。其它字段从右边截断，但是不给出该字段显示不完整的标识。
 *

 *常量的定义,使用 CONSTANTS 指令, 如定义 PI 是一个有 5 位小数的数值 3.14159 .
 CONSTANTS pi TYPE p DECIMALS 5 VALUE '3.14159'.
 *系统内置的数据
 *SPACE   空白字符串
 *SY-SUBRC    系统执行返回值，0 表示执行成功
 *SY-UNAME    登陆帐号名称
 *SY-DATUM    系统日期
 *SY-UZEIT    系统时间
 *SY-TCODE    目前的事务代码


 *STATICS 用来定义变量，使用格式与 DATA 相同。与 DATA 的区别是 STATICS 只能在子程
 *序中使用，用 STATICS 定义的变量可以在退出子程序后保留局部数据对象的值，而 DATA 不能。

 

 *---------内表---------*
 *是一种数据结构,由几个不同类型的栏位(field)組成
 *DATA: BEGIN OF <internal table> OCCURS <n> WITH HEADER LINE ,
 *<field 1> TYPE <type1>,
 *<field 2> TYPE <type 2>,
 *<field 3> TYPE <type 3>,
 *…
 *END OF <internal table>.
 *OCCURS <n>：表示预先申请的行数。
 *WITH HEADER LINE：表示是否存在表工作区即表头。存在表头表示可以直接定位到表的
 *字段上

 DATA: BEGIN OF userinfo OCCURS 20,
     id TYPE i,
     name TYPE c,
     address TYPE c,


  END OF userinfo.


 *---------定义内表的方式---------*

 *---------------------第一种 只能定义，不能用于赋值------------------------*
 TYPES: BEGIN OF ty_out,
  col1 TYPE i,
  col2(2),
  END OF ty_out.
 *与上面等价，冒号的作用*
 *TYPES BEGIN OF gs_out.
 *TYPES col1 TYPE i.
 *types col2(2).
 *types END OF gs_out.


 *-----表体gt_out ,表头 gs_out------*
 DATA: gt_out TYPE TABLE OF ty_out,
    gs_out TYPE ty_out.

 *-----表体gt_out1[] ,表头 gt_out------*
 DATA gt_out11 TYPE TABLE OF ty_out WITH HEADER LINE.

 *-----------------------第二种 -----------------------------------------------*

 *-------表体：gt_out1 表头gs_out1 ----------*
 DATA:BEGIN OF gs_out1,
   col1 TYPE i,
   col2(2),
  END OF gs_out1,
  gt_out1 LIKE TABLE OF gs_out1.

 *-------表体：gt_out2[] 表头gs_out2 ----------*
 DATA:gt_out2 LIKE TABLE OF gs_out1 WITH HEADER LINE.

 

 *-------根据表体创建表头 ----------*
 DATA: gs_out3 LIKE LINE OF gt_out2.


 *-----------------------第三种 -----------------------------------------------*
 DATA:BEGIN OF gt_out4 OCCURS 0,
  col1 TYPE i,
  col2(2),
  END OF gt_out4,
  gs_out4 LIKE LINE OF gt_out4.


 DATA : gt_out5 LIKE TABLE OF gt_out4 WITH HEADER LINE.

 

 *数据运算指令
 *1.赋值 MOVE <F1> TO <F2> ,等同于 f2 = f1
 *2.截取字符串，赋值 MOVE <F1>[+<O1>] TO <F2>[+<O2>]
 DATA:f1(10) TYPE c VALUE 'helloworld',
   f2(5) TYPE c.
 f2 = f1+5(5). "自第 3 个位置开始取出 5 个字符 ,f2 = world
 WRITE f2.

 *3.数组值的复制
 *MOVE–CORRESPONDING <Strings1> TO <Strings2>.
 DATA: BEGIN OF address1,
  firstname(10) TYPE c VALUE 'gsw',
  lastname(10) TYPE c VALUE 'jack',
  tel(12) TYPE c VALUE '132XXXXXXXX',
  END OF address1.

 DATA: BEGIN OF name,
  firstname(10) TYPE c,
  lastname(10) TYPE c,
  email(30) TYPE c,
  END OF name.

 MOVE-CORRESPONDING address1 TO name. "只有email不变

 *4.变量 CALL BY VALUE 的使用
 *WRITE (<f>) TO <g>
 DATA: name1(20) TYPE c VALUE 'source',
    source(10) TYPE c VALUE 'alibaba',
    target(10) TYPE c.
 WRITE (name1) TO target.
 WRITE / target. "alibaba


 *5.清除变量内容
 *CLEAR <f>.
 DATA n TYPE i VALUE 100.
 CLEAR n. "N 的值变为0


 *6.日期与时间
 DATA:mydate TYPE d.
 mydate = sy-datum.
 WRITE / mydate. "04212020
 mydate+6(2) = '01'.
 WRITE / mydate. "04012020
 mydate = mydate - 1.
 WRITE / mydate.  "03312020

 DATA: hours TYPE i ,
    minutes TYPE i,
    t1 TYPE t VALUE '090000',
    t2 TYPE t VALUE '120000',
    t3 TYPE t VALUE '133000',
    t4 TYPE t VALUE '173000'.
    hours = ( t4 - t1 + t3 - t2 ) / 3600. "计算工作有几小时  10
    minutes = ( t4 - t1 + t3 - t2 ) / 60. "计算工作几分钟  600


 *7.字符串数据处理

 *移位*
 *SHIFT <c> [BY <n> PLACES] [<modes>] [CIRCULAR]
 *[BY <n> PLACES]：移动的位数，默认移动一位。
 *[<modes>] : 移动的方向，没有此参数默认向左移动。
 *[CIRCULAR]: 字符串以环状方式移位

 DATA string1(13) TYPE c VALUE 'learning abap'.
 SHIFT string1. "earning abap
 SHIFT string1 BY 2 PLACES RIGHT. " earning abap
 SHIFT string1 BY 2 PLACES RIGHT CIRCULAR. "ba earning a

 *替换*
 *REPLACE <string1> WITH <string2> INTO <c>
 *将字符串 <c> 中的 <string1> 以 <string2> 来取代
 DATA: string2(13) TYPE c VALUE 'learning java',
    str1(4) TYPE c VALUE 'java',
    str2(4) TYPE c VALUE 'abap'.
 REPLACE str1 WITH str2 INTO string2. "learning abap


 *大小写转换*
 *TRANSLATE <c> TO UPPER CASE. *转成大写
 *TRANSLATE <c> TO LOWER CASE. *转成小写


 *查询子字符串*[未看]
 *SEARCH <c> FOR <str>
 *执行结果存至两个变数, SY-SUBRC 和 SY-FDPOS, 若找到则 SY-SUBRC 为 0 ，SY-FDPOS 从开始位迭代（从 0 开始计）。
 *若找不到则 SY-SUBRC 为 4, SY-FDPOS 为 0。

 DATA string3(20) TYPE c VALUE 'learining abap'.
 SEARCH string3 FOR 'abap'.
 *SY-SUBRC 值为：0
 *SY-FDPOS 值为：10


 *长度*
 *STRLEN(<c>)

 *获取子字符串*
 *<f>[+<o>][<l>]
 DATA t(20) TYPE c VALUE 'learning abap'.
 WRITE / t+9(4). "abap


 *拼接字符串*
 *CONCATENATE <STR1> <STR2> INTO <STR3>.

 DATA: str4(10) TYPE c VALUE 'i',
    str5(10) TYPE c VALUE 'am',
    str6(10) TYPE c VALUE 'learning',
    str7(10) TYPE c VALUE 'abap',
    str8(20) TYPE c.
 CONCATENATE str4 str5 str6 str7 INTO str8.  "iamlearningabap


 *去掉之间空格*
 *CONDENSE <STR0> NO-GAPS.
 *

*******************************************************************************
*******************************************************************************
 *********************2020---4---22*********************************************
*******************************************************************************
*******************************************************************************

 


 *屏幕输入指令
 *1. PARAMETER： 输入一个变量或字段内容

 *PARAMETERS <p> [DEFAULT <f>] [LOWER CASE] [OBLIGATORY] [AS CHECKBOX] [RADIOBUTTON GROUP <rad>]

 PARAMETERS: name2(8) TYPE c,
      age2 TYPE i,
      birth2 TYPE d.

 PARAMETERS: tax AS CHECKBOX DEFAULT 'x',
       ntd AS CHECKBOX.

 PARAMETERS: boy RADIOBUTTON GROUP sex DEFAULT 'X',"单选框默认 必须是大写的X
       girl RADIOBUTTON GROUP sex.

 *2. SELECTION-OPTIONS： 使用条件筛选画面来输入数据  【懵逼】
 *SELECT-OPTIONS <check-option> FOR <table-field>
 *[DEFAULT <begin> TO <end>]
 *[NO-EXTENSION] 设定不要多值输入的画面
 *[NO INTERVALS] 设定不要区间范围输入的画面
 *[LOWER CASE] 输入转换成大写
 *[OBLIGATORY] 强制要求输入

 *将条件的输入值存放入AIRLINE, 筛选对象为 SPFLI 中的 CONNID 栏位
 *TABLES SPFLI.
 *SELECT-OPTIONS AIRLINE FOR SPFLI-CONNID.

 

 DATA: NUMBER_3 TYPE I VALUE '1234567890',
    TEXT_3(10) TYPE C VALUE 'ABCDEFGHIJ'.
 \* WRITE: /(5) NUMBER_3, /(6) TEXT_3.  "*7890      ABCDEF

 

 *3 指定显示格式
 *
 *WRITE 资料项 <显示格式参数>
 *显示格式参数:
 *LEFT-JUSTIFIED   资料靠左显示
 *CENTERED      资料靠中间显示
 *RIGHT-JUSTIFIED   资料靠右显示
 *UNDER <g>      在资料项<g>的 X 轴开始坐标显示
 *NO-GAP 紧接着显示, 不留空格
 *USING EDIT MASK <m>  使用内嵌字符显示, 如 11:20:30
 *USING NO EDIT MASK   不使用内嵌字符
 *NO-ZERO       数字前面 0 的部分不显示
 *NO-SIGN      不显示正负号
 *DECIMALS <d>    显示 d 位小数字数
 *EXPONENT <e>     F(浮点数)的科学计数法表示
 *ROUND <r>     四舍五入至小数位数下 r 位
 *CURRENCY <c>    币别显示
 *DD/MM/YY     日期显示格式
 *MM/DD/YY
 *DD/MM/YYYY
 *MM/DD/YYYY
 *DDMMYY
 *MMDDYY
 *YYMMDD

 DATA: X TYPE I VALUE '104010'.

 write / x USING EDIT MASK '__:__:__'. "两个下划线  10:40:10
 write / x USING EDIT MASK '$___,___'. "三个下划线  $104.01

 *SKIP [<n>]
 INCLUDE <SYMBOL>.
 INCLUDE <ICON>.
 write: / 'Phone Sysybol:',SYM_PHONE AS SYMBOL.
 write: / 'Alarm Icon:',ICON_ALARM AS ICON.

 *显示 CHECK BOX 资料
 *WRITE <资料项> AS CHECKBOX.
 *DATA: FLAG1 VALUE ' ',
 *FLAG2 VALUE 'X'.
 *WRITE: / 'CHECK FLAG 1:' , FLAG1 AS CHECKBOX.
 *WRITE: / 'CHECK FLAG 2:' , FLAG2 AS CHECKBOX.

 

 

 

 *********************内表************************
 ****1.创建
 *1.1用自定义的表类型来定义内表*
 types: BEGIN OF line,  "*定义数组类型
    col1 TYPE i,
    col2 TYPE i,
  END OF line.
 types: itabt_1 TYPE line occurs 10.  "定义表类型
 data : myitab_1 TYPE itabt_1 WITH HEADER LINE."*定义带工作区的内表，工作区名称与内表名称相同：MYITAB_1
 *1.2 用data直接定义内表

 data: BEGIN OF myitab_2 occurs 0,  "*自带工作区的内表，工作区名称与内表名称相同：MYITAB
    col1 TYPE i,
    col2 TYPE i,
  END OF myitab_2.

 *1.3 参照数据库表结构定义内表，内表结构与所参照数据库表完全相同
 data :itab_2 like SPFLI occurs 0 WITH HEADER LINE. "ITAB 是定义的内表,SPFLI 是参照的数据库表

 *1.4 定义内表结构包含数据表的所有字段

 *data:BEGIN OF itab_3 occurs 0.
 *include STRUCTURE ZTABLE1. "ZTABLE1 是参照的数据库表,内表 ITAB 结构除包含数据库表 ZTABLE1 的所有字段外还包括字段 NAME 和字段 AGE
 *data: name(20) TYPE c,
 \*   age TYPE i,
 *END OF itab_3.

 


 ****2.增加数据


 *********************APPEND指令********************************************************
 *2.1 把内表工作区内容追加到内表中
 data:BEGIN OF myitab_4 occurs 0,
  col1 TYPE i,
  col2 TYPE i,
  END OF myitab_4.

 myitab_4-col1 = 11.
 myitab_4-col2 = 22.
 append myitab_4. "内表和工作区名称相同为：MYITAB_4

 *2.2 把相同结构数组变量内容追加到内表中(也可以把 LINE 看作 ITAB 不同名的工作区)
 *APPEND <wa> TO <itab>

 data: BEGIN OF line,
  col1 TYPE i,
  col2 TYPE i,
  END OF line.

 data itab_4 like line occurs 10.
 line-col1 = 10.
 line-col2 = 20.
 append line to itab_4.


 *2.3 将一个内表中数据追加到另一个内表中
 *APPEND LINES OF <itab1> [FROM <n1>] [TO <n2>] TO <itab2>
 *将<itab1>中自<n1>至<n2>范围的数据加入到<itab2>中。
 *APPEND LINES OF ITAB TO JTAB.


 *************************COLLECT指令*********************************************
 *使用 COLLECT 指令向内表添加数据时将有相同 standard key(非数值字段)的数据的数值字段进行汇总。
 *COLLECT [<wa> INTO] <itab>

 data: BEGIN OF itab_5 occurs 3,
    col1(10) TYPE c,
    col2 TYPE i,
  END OF itab_5.

 itab_5-col1 = 'IPhone_se2'.
 itab_5-col2 = 3299.
 collect itab_5.
 itab_5-col1 = 'IPhone_7'.
 itab_5-col2 = 1999.
 collect itab_5.
 itab_5-col1 = 'IPhone_se2'.
 itab_5-col2 = 100.
 collect itab_5. "汇总 COL2 至 COL1=iphone se2 的元素上
 loop at itab_5.
  WRITE : / itab_5-col1,itab_5-col2 .
 endloop.

 *******************************Insert指令*******************************************
 *1.insert line
 *在指定的内表位置之前插入新数据
 *INSERT [<wa> INTO] [INITIAL LINE INTO] <itab> [INDEX <idx>]
 *INITIAL LINE INTO：与前面的[<wa> INTO]参数不能同时使用，意义是插入空记录行到内表中
 *INDEX <idx>：插入在第 idx 条记录之前的位置
 data: BEGIN OF line_3,
  col1 TYPE i ,
  col2 TYPE i,
  END OF line_3.
 data itab_6 LIKE line_3 occurs 10.
 do 3 times.
   line_3-col1 = SY-INDEX * 10.
   line_3-col2 = SY-INDEX * 20.
   append line_3 to itab_6.
 enddo.
 line_3-col1 = 100.
 line_3-col2 = 200.
 insert line_3 into itab_6 index 2.
 loop at itab_6 INTO line_3.
   write:/ SY-TABIX,line_3-col1,line_3-col2.
 endloop.

 *2.插入另一 Internal Table 元素
 *INSERT LINES OF <itab1> [FROM <n1> TO <n2>] INTO <itab2> INDEX <idx>
 *将<itab1>中自<n1>至<n2>的范围的数据插入至<itab2>中, 位置在 <idx>之前。
 *INSERT LINES OF ITAB INTO JTAB INDEX 3. "将 ITAB 所有元素插入 JTAB 中, 位置在第三个元素之前。


 ****3.删除数据
 ****4.查询数据
 *4.1 ****读取内表数据
 ************************循环读取 Internal Table 元素数据****************************

 *LOOP AT <itab> [INTO <wa>] [FROM <n1> TO <n2>] [WHERE <condition>]
 *<loop expression>
 *ENDLOOP.

 

 

 ****5.更新数据

 

 


 *********条件语句 if endif***********

 data number_4 TYPE i VALUE '666'.

 if NUMBER_4 = 666.
  WRITE / NUMBER_4.
 else.
 write: /'number不等于666'.
 endif.


 *********条件语句 case endcase***********
 case number_4.
 when 666.
  write: /'number_4的值为:',NUMBER_4.
 when OTHERS.
  write: /'其它选项 '.
 endcase.

 *********条件语句 do enddo***********
 do 4 times.
  write / number_4.
 enddo.





## 2.z_test_gsw_cc002

REPORT Z_TEST_GSW_CC002 MESSAGE-ID ztest.

 TABLES:ekko,ekpo, ekbe,lfa1,makt. "采购凭证抬头，采购凭证项目，采购凭证历史，供应商主数据，物料描述

 DATA:BEGIN OF gs_out,
    ebeln   TYPE ekko-ebeln,   "采购凭证号
    ekorg   TYPE ekko-ekorg,   "采购组织
    lifnr   TYPE ekko-lifnr,   "商品供应商
    lifnr_txt TYPE lfa1-name1,   "商品供应商描述
    matnr   TYPE ekpo-matnr,   "物料编号
    maktx   TYPE makt-maktx,   "物料描述
    shkzg   TYPE ekbe-shkzg,   "借贷方标识
    ddsl   TYPE ekpo-menge,   "订单数量
    shsl   TYPE ekbe-menge,   "收货数量
    wshsl   TYPE ekbe-menge,   "未收货数量

   END OF gs_out,
   gt_out LIKE TABLE OF gs_out.

 

 *&---------------------------------------------------------------------*
 *&  选择画面定义
 *&---------------------------------------------------------------------*
 SELECTION-SCREEN:BEGIN OF BLOCK bk1 WITH FRAME TITLE text-001.
 SELECT-OPTIONS:s_ebeln FOR ekko-ebeln,         "订单号
        s_ekorg FOR ekko-ekorg,         "采购组织
        s_lifnr FOR lfa1-lifnr,         "商品供应商
        s_bedat FOR ekko-bedat,         "采购凭证日期
        s_werks FOR ekpo-werks DEFAULT '4300'.  "工厂
 SELECTION-SCREEN: END OF BLOCK bk1.

 INITIALIZATION.
  DATA:lv_date TYPE d,     "当前日期
    lv_date1 TYPE d.      "6个月前的日期
  CONCATENATE sy-datum+0(6) '01' INTO lv_date.

 ****定义一个函数得到日期**********
  CALL FUNCTION 'FIMA_DATE_CREATE'
   EXPORTING
    i_date  = lv_date
    i_months = -6 "
   IMPORTING
    e_date  = s_bedat-low.

  CALL FUNCTION 'FIMA_DATE_CREATE'
   EXPORTING
    i_date  = lv_date
    i_months = 1 "
   IMPORTING
    e_date  = lv_date1.

  lv_date1 = lv_date1 - 1.
  s_bedat-high = lv_date1.

  s_bedat-sign = 'I'.
  s_bedat-option = 'BT'.
  APPEND s_bedat.

 

 

 


 *----------------------------------------------------------------------*
 \* START-OF-SELECTION
 *----------------------------------------------------------------------*
 START-OF-SELECTION.

  PERFORM frm_get_data.
  perform frm_display.
 FORM frm_get_data.

 \* 按采供应商(EKKO-LIFNR), 采购订单号(EKKO-EBELN), 物料号(EKPO-MATNR)，
 \*  汇总：订单数量(EKPO-MENGE),
 \* 发货数量(EKBE-MENGE),
  DATA:BEGIN OF ls_sum,
     lifnr TYPE ekko-lifnr,    "商品供应商
     ebeln TYPE ekko-ebeln,    "采购凭证号
     matnr TYPE ekpo-matnr,    "物料编号
     ddsl TYPE ekpo-menge,    "订单数量
     shsl TYPE ekpo-menge,    "收货数量
    END OF ls_sum,
    lt_sum LIKE TABLE OF ls_sum.

  SELECT
   a~ebeln
   ekorg
   a~lifnr
   c~name1 AS lifnr_txt
   b~matnr
   maktx
   e~shkzg
   b~menge AS ddsl
   e~menge AS shsl
   INTO TABLE gt_out
   FROM ekko AS a
   INNER JOIN ekpo AS b ON a~ebeln = b~ebeln
   LEFT JOIN lfa1 AS c ON a~lifnr = c~lifnr
   LEFT JOIN makt AS d ON b~matnr = d~matnr AND d~spras = sy-langu
   INNER JOIN ekbe AS e ON b~ebeln = e~ebeln AND b~ebelp = e~ebelp
   WHERE a~ebeln IN s_ebeln
    AND a~ekorg IN s_ekorg
    AND a~lifnr IN s_lifnr
    AND a~bedat IN s_bedat
    AND b~werks IN s_werks.

 

  IF sy-subrc NE 0. "not equal
   MESSAGE s001 DISPLAY LIKE 'E'.
   LEAVE LIST-PROCESSING.
  ENDIF.

 


  LOOP AT gt_out ASSIGNING FIELD-SYMBOL(<lfd_out>).
   IF <lfd_out>-shkzg EQ 'H'. "如果借贷方标识是H(EKBE-SHKZG = ‘102’代表退货，则收货数量为负数。
    <lfd_out>-shsl = 0 - <lfd_out>-shsl.
   ENDIF.

   MOVE-CORRESPONDING <lfd_out> TO ls_sum.
   COLLECT ls_sum INTO lt_sum.CLEAR ls_sum.
  ENDLOOP.

  SORT lt_sum BY lifnr ebeln matnr.

  SORT gt_out BY ebeln ekorg lifnr matnr.
  DELETE ADJACENT DUPLICATES FROM gt_out COMPARING ebeln ekorg lifnr  matnr.

  LOOP AT gt_out ASSIGNING <lfd_out>.
   READ TABLE lt_sum INTO ls_sum WITH KEY lifnr = <lfd_out>-lifnr
                      ebeln = <lfd_out>-ebeln
                      matnr = <lfd_out>-matnr
                      BINARY SEARCH.
   IF sy-subrc EQ 0.
    MOVE: ls_sum-ddsl TO <lfd_out>-ddsl,
       ls_sum-shsl TO <lfd_out>-shsl.
    <lfd_out>-wshsl = ls_sum-ddsl - ls_sum-shsl.
   ENDIF.
  ENDLOOP.
 ENDFORM.

 

 ***展示数据
 FORM frm_display.
 Endform.



## 3.z_test_cc005



REPORT ztest_cc005 .


 TABLES:bseg.

 *定义内表方式


 *********1********
 TYPES:BEGIN OF ty_out,
     col1  TYPE i,
     col2(2),
    END OF ty_out.

 *TYPES BEGIN OF gs_out.
 *TYPES col1 TYPE i.
 *types col2(2).
 *types END OF gs_out.

 DATA:gt_out TYPE TABLE OF ty_out,
   gs_out TYPE ty_out.
 *表体:gt_out.
 *表头:gs_out.

 DATA gt_outt TYPE TABLE OF ty_out WITH HEADER LINE.
 *表体:gt_out[].
 *表头:gt_out.

 *********2*********
 DATA:BEGIN OF gs_out1,
    col1  TYPE i,
    col2(2),
   END OF gs_out1,
   gt_out1 LIKE TABLE OF gs_out1.

 *表体:gt_out1.
 *表头:gs_out1.

 DATA:gt_out2 LIKE TABLE OF gs_out1 WITH HEADER LINE.
 *表体:gt_out2[].
 *表头:gt_out2.

 


 *根据表体创建表头
 DATA:gs_out3 LIKE LINE OF gt_out2.


 *****3******
 DATA:BEGIN OF gt_out4 OCCURS 0,
    col1  TYPE i,
    col2(2),
   END OF gt_out4,
   gs_out4 LIKE LINE OF gt_out4.

 DATA:gt_out5 LIKE TABLE OF gt_out4 WITH HEADER LINE.









 

 

## 4.z_test_gsw_cc004

REPORT Z_TEST_GSW_CC004.
 TABLES:
  faglflext.
 DATA:BEGIN OF gs_out,
    ryear TYPE faglflext-ryear,  "会计年度
    drcrk TYPE faglflext-drcrk,   ""借方/贷方标识
    racct TYPE faglflext-racct,   ""科目
    rbukrs TYPE faglflext-rbukrs, "公司代码
    prctr TYPE faglflext-prctr,   "利润中心

    tslvt TYPE faglflext-tslvt,
    tsl01 TYPE faglflext-tsl01,
    tsl02 TYPE faglflext-tsl02,
    tsl03 TYPE faglflext-tsl03,
    tsl04 TYPE faglflext-tsl04,
    tsl05 TYPE faglflext-tsl05,
    tsl06 TYPE faglflext-tsl06,
    tsl07 TYPE faglflext-tsl07,
    tsl08 TYPE faglflext-tsl08,
    tsl09 TYPE faglflext-tsl09,
    tsl10 TYPE faglflext-tsl10,
    tsl11 TYPE faglflext-tsl11,
    tsl12 TYPE faglflext-tsl12,
    tsl13 TYPE faglflext-tsl13,
    tsl14 TYPE faglflext-tsl14,
    tsl15 TYPE faglflext-tsl15,
    tsl16 TYPE faglflext-tsl16,
   END OF gs_out,
   gt_out LIKE TABLE OF gs_out.

 DATA:BEGIN OF gs_alv,
    hxmmc1 TYPE string,
    hch1  TYPE string,
    qmye1 TYPE faglflext-tsl16,
    ncye1 TYPE faglflext-tsl16,

​    hxmmc2 TYPE string,
​    hch2  TYPE string,
​    qmye2 TYPE faglflext-tsl16,
​    ncye2 TYPE faglflext-tsl16,
   END OF gs_alv,
   gt_alv LIKE TABLE OF gs_alv.

 DATA: g_bzdw TYPE string,  "编制单位
    g_kjqj TYPE string.   "会计期间

 \* Define ALV attribute
 DATA:go_grid   TYPE REF TO cl_gui_alv_grid.
 *DATA:gs_layout  TYPE lvc_s_layo,
 \*   gt_fieldcat TYPE lvc_t_fcat,
 \*   gs_variant TYPE disvariant,
 \*   gs_fieldcat LIKE LINE OF gt_fieldcat,
 \*   gt_exclude TYPE slis_t_extab,
 \*   gv_repid  TYPE syrepid.

 *&------------------------------------------------------------------*
 *&           SELECT SCREEN
 *&------------------------------------------------------------------*

 SELECTION-SCREEN BEGIN OF BLOCK b1 WITH FRAME TITLE text-001.
 PARAMETERS:p_rbukrs TYPE faglflext-rbukrs OBLIGATORY.  "公司代码
 SELECT-OPTIONS s_prctr FOR faglflext-prctr.       "利润中心
 PARAMETERS:p_ryear TYPE faglflext-ryear OBLIGATORY,    "会计年度
      p_rpmax TYPE faglflext-rpmax OBLIGATORY.    "会计期间
 SELECTION-SCREEN END OF BLOCK b1.

 

 START-OF-SELECTION.

  PERFORM frm_get_header.
  PERFORM frm_get_data.
  PERFORM frm_display.


 FORM frm_get_header.
  DATA:l_bukrs_txt TYPE t001-butxt.  "公司代码
  DATA:l_ktext TYPE cepct-ktext.    "公司的名称
  DATA:lv_period_end TYPE sy-datum.
  DATA:lp_rpmax TYPE faglflext-rpmax.    "会计期间

  "取编制单位
  SELECT SINGLE butxt INTO ( l_bukrs_txt )
   FROM t001 WHERE bukrs EQ p_rbukrs.

  IF s_prctr IS NOT INITIAL.
   SELECT SINGLE ktext INTO ( l_ktext )
    FROM cepct
    WHERE spras EQ sy-langu
    AND prctr IN s_prctr.
    g_bzdw = l_bukrs_txt && '(' && l_ktext && ')'. "当将整型变量与字符串进行连接时，最好使用 && 操作符
  ELSE.
    g_bzdw = l_bukrs_txt.
  ENDIF.

 

  "会计期间
  IF p_rpmax < 1 .
   lp_rpmax = 1.
  ELSEIF p_rpmax > 12 .
   lp_rpmax = 12.
  ELSE.
   lp_rpmax = p_rpmax.
  ENDIF.

  CALL FUNCTION 'LAST_DAY_IN_PERIOD_GET'
   EXPORTING
    i_gjahr    = p_ryear
    i_periv    = 'K4'
    i_poper    = lp_rpmax
   IMPORTING
    e_date     = lv_period_end
   EXCEPTIONS
    input_false  = 1
    t009_notfound = 2
    t009b_notfound = 3
    OTHERS     = 4.


  CONCATENATE lv_period_end+0(4) '年' lv_period_end+4(2) '月' lv_period_end+6(2) '日' INTO g_kjqj.

 ENDFORM.

 FORM frm_get_data.
  RANGES:lr_racct FOR faglflext-racct. "有时候需要把单值的结构变成区间的结构，也就是类似SELECTION-OPTION的功能，SAP提供了RANGES来实现该功能.
  RANGES:lrr_racct FOR faglflext-racct.

  DEFINE add_racct.
   clear lr_racct.
   lr_racct+0(3) = 'ICP'.
 \*  lr_racct-sign = 'I'.
 \*  LR_RACCT-OPTION = 'CP'.
   lr_racct-low = &1.

   append lr_racct.
  END-OF-DEFINITION.

  "添加科目
   add_racct '1001*'.
   add_racct '1002*'.
   add_racct '1012*'.
   add_racct '1121*'.
   add_racct '1122*'.
   add_racct '123101*'.
   add_racct '1123*'.
   add_racct '1131*'.
   add_racct '1132*'.
   add_racct '1221*'.
   add_racct '123102*'.
   add_racct '1402*'.
   add_racct '1403*'.
   add_racct '1404*'.
   add_racct '1405*'.
   add_racct '1406*'.
   add_racct '1409*'.
   add_racct '1412*'.
   add_racct '1413*'.
   add_racct '1471*'.
   add_racct '1901*'.
   add_racct '1511*'.
   add_racct '1512*'.
   add_racct '1521*'.
   add_racct '1522*'.
   add_racct '1523*'.
   add_racct '1601*'.
   add_racct '1602*'.
   add_racct '1603*'.
   add_racct '1604*'.
   add_racct '1607*'.
   add_racct '1606*'.
   add_racct '1701*'.
   add_racct '1702*'.
   add_racct '1703*'.
   add_racct '170105*'.
   add_racct '170205*'.
   add_racct '170305*'.
   add_racct '1711*'.
   add_racct '1712*'.
   add_racct '1801*'.
   add_racct '1811*'.
   add_racct '2001*'.
   add_racct '2201*'.
   add_racct '2202*'.
   add_racct '2203*'.
   add_racct '2211*'.
   add_racct '221101*'.
   add_racct '221102*'.
   add_racct '2221*'.
   add_racct '222106*'.
   add_racct '222113*'.
   add_racct '222114*'.
   add_racct '2231*'.
   add_racct '2232*'.
   add_racct '2241*'.
   add_racct '2401*'.
   add_racct '2501*'.
   add_racct '2801*'.
   add_racct '2901*'.
   add_racct '4001*'.
   add_racct '400101*'.
   add_racct '400102*'.
   add_racct '4002*'.
   add_racct '4101*'.
   add_racct '4103*'.
   add_racct '4104*'.
   add_racct '6*'.

 


  SELECT
  ryear
  drcrk
  prctr
  racct
  rbukrs
  SUM( tslvt )
  SUM( tsl01 )
  SUM( tsl02 )
  SUM( tsl03 )
  SUM( tsl04 )
  SUM( tsl05 )
  SUM( tsl06 )
  SUM( tsl07 )
  SUM( tsl08 )
  SUM( tsl09 )
  SUM( tsl10 )
  SUM( tsl11 )
  SUM( tsl12 )
  SUM( tsl13 )
  SUM( tsl14 )
  SUM( tsl15 )
  SUM( tsl16 )
  INTO CORRESPONDING FIELDS OF TABLE gt_out
  FROM faglflext
  WHERE rbukrs EQ p_rbukrs
  AND ryear EQ p_ryear
  AND prctr IN s_prctr
  AND racct IN lr_racct
  GROUP BY ryear rbukrs drcrk prctr racct.

  DATA:l_qmye TYPE faglflext-tsl16,l_ncye TYPE faglflext-tsl16,
    l_qmye1 TYPE faglflext-tsl16,l_ncye1 TYPE faglflext-tsl16.
  "货币资金
  "1001*
  lrr_racct+0(3) = 'ICP'.
  lrr_racct-low = '1001*'.
  APPEND lrr_racct.
  lrr_racct-low = '1002*'.
  APPEND lrr_racct.
  lrr_racct-low = '1012*'.
  APPEND lrr_racct.

  LOOP AT gt_out INTO gs_out WHERE racct IN lrr_racct.
   PERFORM frm_get_qmye USING l_qmye gs_out.
   ADD gs_out-tslvt TO l_ncye.
  ENDLOOP.

  CLEAR:lrr_racct[],lrr_racct.
  lrr_racct+0(3) = 'ICP'.
  lrr_racct-low = '2001*'.
  APPEND lrr_racct.

  CLEAR:l_qmye1,l_ncye1.
  LOOP AT gt_out INTO gs_out WHERE racct IN lrr_racct.
   PERFORM frm_get_qmye USING l_qmye1 gs_out.
   ADD gs_out-tslvt TO l_ncye1.
  ENDLOOP.

  gs_alv-hxmmc1 = '货币资金'.
  gs_alv-hch1 = '2'.
  gs_alv-qmye1 = l_qmye.
  gs_alv-ncye1 = l_ncye.

  gs_alv-hxmmc2 = '短期借款'.
  gs_alv-hch2 = '48'.
  gs_alv-qmye2 = 0 - l_qmye1.
  gs_alv-ncye2 = 0 - l_ncye1.

  APPEND gs_alv TO gt_alv.

 ENDFORM.

 FORM frm_get_qmye USING p_ye TYPE faglflext-tslvt
                 p_out LIKE gs_out.
  DATA:l_times TYPE i,
    l_idx  TYPE i.
  FIELD-SYMBOLS:<lfd> TYPE faglflext-tslvt.

  CLEAR p_ye.
  IF p_rpmax > 0 AND p_rpmax < 12 .
   l_times = p_rpmax + 1.
   l_idx = 6.
   DO l_times TIMES.
    ASSIGN COMPONENT l_idx OF STRUCTURE p_out TO <lfd>.
    ADD <lfd> TO p_ye.
    ADD 1 TO l_idx.
   ENDDO.
  ELSEIF p_rpmax EQ 12.
   l_times = 17.
   l_idx = 6.
   DO l_times TIMES.
    ASSIGN COMPONENT l_idx OF STRUCTURE p_out TO <lfd>.
    ADD <lfd> TO p_ye.
    ADD 1 TO l_idx.
   ENDDO.
  ENDIF.

 ENDFORM.


 *&---------------------------------------------------------------------*
 *&   Form FRM_DISPLAY
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_display .
  DATA:ls_layo TYPE lvc_s_layo,
    lt_fcat TYPE lvc_t_fcat,
    ls_fcat TYPE lvc_s_fcat.


 *******设置报表抬头&注释
 \* PERFORM comment_build USING er_list_top_of_page[].

 \* 布局设置
  CLEAR ls_layo.
  ls_layo-zebra   = 'X'.  "斑马线
  ls_layo-no_rowmark = 'X'.  "不选择选择框
 \* ls_layo-box_fname = 'SEL'.

 \* FIELDCAT字段填充
  CLEAR lt_fcat[].
  DEFINE m_fieldcat.
   CLEAR ls_fcat.
   ls_fcat-fieldname = &1.
   ls_fcat-coltext  = &2.
   ls_fcat-outputlen = &3.
   ls_fcat-no_zero  = &4.

   APPEND ls_fcat TO lt_fcat.
  END-OF-DEFINITION.

  m_fieldcat 'hxmmc1' '资   产' '' '' .
  m_fieldcat 'hch1' '行次'  '' ''.
  m_fieldcat 'qmye1' '期末余数' '' ''.
  m_fieldcat 'ncye1' '年初余额' '' ''.
  m_fieldcat 'hxmmc2' '负债和所有者权益(或股东权益)' '' ''.
  m_fieldcat 'hch2' '行次'  '' ''.
  m_fieldcat 'qmye2' '期末余数' '' ''.
  m_fieldcat 'ncye2' '年初余额' '' ''.


  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'
   EXPORTING
    i_callback_program     = sy-repid
    is_layout_lvc        = ls_layo
    it_fieldcat_lvc       = lt_fcat[]
 \*   i_callback_pf_status_set  = 'FRM_SET_STATUS'
    i_callback_user_command   = 'FRM_USER_COMMAND'
    i_callback_html_top_of_page = 'F_TOP_OF_PAGE'
    i_save           = 'A'
   TABLES
    t_outtab          = gt_alv[]
   EXCEPTIONS
    program_error        = 1
    OTHERS           = 2.


 ENDFORM.

 FORM f_top_of_page USING p_cl_dd TYPE REF TO cl_dd_document.

  DATA:lv_str1 TYPE string,
    lv_str2 TYPE string,
    lv_str3 TYPE string,
    lv_date TYPE string.

  " 定义缓冲区变量
  DATA: m_p   TYPE i,
     m_buffer TYPE string.

  " 开始输出表头标题
  m_buffer = '<HTML><CENTER><H1>资产负债表</H1></CENTER></HTML>' .
  CALL METHOD p_cl_dd->html_insert
   EXPORTING
    contents = m_buffer
   CHANGING
    position = m_p.

  "取编制单位

  lv_date = sy-datum.
  lv_date = lv_date+0(4) && '年' && lv_date+4(2) && '月' && '26日'.

  " 输出制表人和制表日期
  CONCATENATE '<P ALIGN = CENTER >编制单位：' g_bzdw
       '&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp           &nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'
       '&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'
       '&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'
       '&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'
       '&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'
       '&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'
       '会计期间：' g_kjqj INTO m_buffer .
  CALL METHOD p_cl_dd->html_insert
   EXPORTING
    contents = m_buffer
   CHANGING
    position = m_p.

 ENDFORM.

 *&---------------------------------------------------------------------*
 *&   Form FRM_SET_STATUS
 *&---------------------------------------------------------------------*
 \*    set status
 *----------------------------------------------------------------------*
 FORM frm_set_status USING extab TYPE slis_t_extab.

  DATA:wa_excluding TYPE slis_extab.
  wa_excluding-fcode = '&INFO' .
  APPEND wa_excluding TO extab .

  SET PF-STATUS 'S1000'.

 ENDFORM. " FRM_SET_STATUS
 *&---------------------------------------------------------------------*
 *&   Form FRM_USER_COMMAND
 *&---------------------------------------------------------------------*
 *
 *----------------------------------------------------------------------*
 \*   -->I_UCOMM
 \*   -->I_SELFIELD
 *----------------------------------------------------------------------*
 FORM frm_user_command USING i_ucomm LIKE sy-ucomm
               i_selfield TYPE slis_selfield.

  DATA:l_grid  TYPE REF TO cl_gui_alv_grid,
    lv_count TYPE i.

 *- 获取alv
  CALL FUNCTION 'GET_GLOBALS_FROM_SLVC_FULLSCR'
   IMPORTING
    e_grid = l_grid.
 *- 获取alv改变值
  CALL METHOD l_grid->check_changed_data.
 *- 刷新ALV界面
  CALL METHOD l_grid->refresh_table_display.

  CASE i_ucomm.
   WHEN '&IC1'.
 \*   i_selfield-fieldname
 *i_selfield-tabindex

   WHEN OTHERS.
  ENDCASE.

  i_selfield-refresh = 'X'.

 ENDFORM. " FRM_USER_COMMAND



## 5.ztest_cc007

************************************************************************
 \* Report ZTEST_CC005
************************************************************************
 *
 \* 作者：  CC
 \* 完成日期：2020/01/13
 \* 描述：  内表及数据库指令练习
************************************************************************
 \* 版本号 日期  作者  修改描述 功能更改说明书
************************************************************************
 \* 1.0 20209/01/13 cc  程序创建
************************************************************************
 REPORT ztest_cc009 MESSAGE-ID zk.
 TABLES:lfa1,lfm1,lfbk.
 TYPE-POOLS:slis.

 DATA:BEGIN OF gs_lfa1,
    lifnr LIKE lfa1-lifnr,
    name1 LIKE lfa1-name1,
    telf1 LIKE lfa1-telf1,
    telfx LIKE lfa1-telfx,
    ktokk LIKE lfa1-ktokk,
    txt30 LIKE t077y-txt30,
    actss LIKE lfa1-actss,
    stras LIKE lfa1-stras,
    fityp LIKE lfa1-fityp,
    kraus LIKE lfa1-kraus,
   END OF gs_lfa1,
   gt_lfa1 LIKE TABLE OF gs_lfa1.

 DATA:BEGIN OF gs_lfm1,
    lifnr LIKE lfm1-lifnr,
    ekorg LIKE lfm1-ekorg,
    kalsk LIKE lfm1-kalsk,
    kalsb LIKE tmkkt-kalsb,
    minbw LIKE lfm1-minbw,
   END OF gs_lfm1,
   gt_lfm1 LIKE TABLE OF gs_lfm1.

 DATA:BEGIN OF gs_lfbk,
    lifnr LIKE lfm1-lifnr,
    banks LIKE lfbk-banks,
    bankl LIKE lfbk-bankl,
    koinh LIKE lfbk-koinh,
    banka LIKE bnka-banka,
    bankn LIKE lfbk-bankn,
    bkref LIKE lfbk-bkref,
   END OF gs_lfbk,
   gt_lfbk LIKE TABLE OF gs_lfbk.

 DATA:gv_line TYPE i.

 

 \* Define ALV attribute
 DATA:go_grid   TYPE REF TO cl_gui_alv_grid.
 DATA:gt_fieldcat TYPE lvc_t_fcat,
   gs_variant TYPE disvariant,
   gs_fieldcat LIKE LINE OF gt_fieldcat,
   gt_exclude TYPE slis_t_extab,
   gv_repid  TYPE syrepid.

 DATA: gv_layout  TYPE lvc_s_layo,       "ALV LAYOUT
    gv_layout1 TYPE lvc_s_layo,
    gv_layout2 TYPE lvc_s_layo,
    gv_layout3 TYPE lvc_s_layo,
    gv_alv_save TYPE c.       "ALV VARIANT

 DATA: wa_fieldcat     TYPE lvc_s_fcat,
    gt_fieldcat1    TYPE lvc_t_fcat,
    gt_fieldcat2    TYPE lvc_t_fcat,
    gt_fieldcat3    TYPE lvc_t_fcat,

    ref_cust_container TYPE REF TO cl_gui_custom_container,
    ref_cust_container1 TYPE REF TO cl_gui_custom_container,
    ref_cust_container2 TYPE REF TO cl_gui_custom_container,
    ref_cust_container3 TYPE REF TO cl_gui_custom_container,
     
    ref_tree      TYPE REF TO cl_gui_simple_tree,
    ref_grid1      TYPE REF TO cl_gui_alv_grid,
    ref_grid2      TYPE REF TO cl_gui_alv_grid,
    ref_grid3      TYPE REF TO cl_gui_alv_grid.

 






 CLASS lcl_event_receiver DEFINITION DEFERRED.
 DATA: event_receiver TYPE REF TO lcl_event_receiver.

 CLASS lcl_event_receiver_tree DEFINITION DEFERRED.

 CLASS lcl_event_receiver_tree DEFINITION.
  PUBLIC SECTION.
   METHODS handle_node_double_click
          FOR EVENT node_double_click OF cl_gui_simple_tree
    IMPORTING node_key.
  PRIVATE SECTION.
 ENDCLASS.

 CLASS lcl_event_receiver DEFINITION.

  PUBLIC SECTION.

   METHODS handle_hotspot_click
          FOR EVENT hotspot_click OF cl_gui_alv_grid
    IMPORTING e_row_id
          e_column_id
          es_row_no.

   METHODS handle_toolbar
          FOR EVENT toolbar OF cl_gui_alv_grid
    IMPORTING e_object e_interactive.

   METHODS handle_user_command
          FOR EVENT user_command OF cl_gui_alv_grid
    IMPORTING e_ucomm.

   METHODS handle_data_changed
          FOR EVENT data_changed OF cl_gui_alv_grid
    IMPORTING er_data_changed.


   METHODS handle_data_changed_finished
          FOR EVENT data_changed_finished OF cl_gui_alv_grid
    IMPORTING e_modified et_good_cells.

  PRIVATE SECTION.

 ENDCLASS.          "lcl_event_receiver DEFINITION

 **----------------------------------------------------------------------*
 **    CLASS lcl_event_receiver IMPLEMENTATION
 **----------------------------------------------------------------------*
 **
 **----------------------------------------------------------------------*
 CLASS lcl_event_receiver_tree IMPLEMENTATION.
  METHOD handle_node_double_click.

  ENDMETHOD.
 ENDCLASS.
 CLASS lcl_event_receiver IMPLEMENTATION.

  METHOD handle_toolbar. "设置工具栏
 \* § 2.In event handler method for event TOOLBAR: Append own functions
 \*  by using event parameter E_OBJECT.
   DATA: ls_toolbar TYPE stb_button.

   "分隔符
   CLEAR ls_toolbar.
   MOVE 3 TO ls_toolbar-butn_type.
   APPEND ls_toolbar TO e_object->mt_toolbar.

   "全选按钮
   CLEAR ls_toolbar.
   MOVE 'SELALL' TO ls_toolbar-function. "全选
   MOVE icon_select_all TO ls_toolbar-icon.
   MOVE '全选'(111) TO ls_toolbar-quickinfo.
   MOVE ''(112) TO ls_toolbar-text.
   MOVE '' TO ls_toolbar-disabled.
   APPEND ls_toolbar TO e_object->mt_toolbar.

   "取消选择
   CLEAR ls_toolbar.
   MOVE 'DESELALL' TO ls_toolbar-function. "取消选择
   MOVE icon_deselect_all TO ls_toolbar-icon.
   MOVE '取消全选'(111) TO ls_toolbar-quickinfo.
   MOVE ''(112) TO ls_toolbar-text.
   MOVE '' TO ls_toolbar-disabled.
   APPEND ls_toolbar TO e_object->mt_toolbar.

   "分隔符
   CLEAR ls_toolbar.
   MOVE 3 TO ls_toolbar-butn_type.
   APPEND ls_toolbar TO e_object->mt_toolbar.

   CLEAR ls_toolbar.
   MOVE 'ZSP' TO ls_toolbar-function. "生成付款通知单
   MOVE icon_allow TO ls_toolbar-icon. "@8X@
   MOVE '生成'(111) TO ls_toolbar-quickinfo.
   MOVE '生成结算通知单'(112) TO ls_toolbar-text.
   MOVE '' TO ls_toolbar-disabled.
   APPEND ls_toolbar TO e_object->mt_toolbar.


  ENDMETHOD.          "handle_toolbar

  METHOD handle_hotspot_click.

 \*  IF e_column_id-fieldname = 'ZJSDH'.
 \*   READ TABLE lt_data_sc INTO wa_data_sc INDEX e_row_id.
 \*   IF sy-subrc = 0.
 \*    SET PARAMETER ID 'ZJSDH' FIELD wa_data_sc-zjsdh.
 \*    PERFORM frm_display_jsdmx.
 *
 \*   ENDIF.
 \*  ENDIF.
  ENDMETHOD.

  METHOD handle_user_command. "处理用户命令
 \* § 3.In event handler method for event USER_COMMAND: Query your
 \*  function codes defined in step 2 and react accordingly.

   DATA: lt_rows TYPE lvc_t_row.
   CASE e_ucomm.
    WHEN 'SELALL'."全选
     DATA(lv_str) = '全选'.
     MESSAGE lv_str TYPE 'S' DISPLAY LIKE 'E'.
 \*    LOOP AT lt_data_sc INTO wa_data_sc WHERE checkbox = ''.
 \*     wa_data_sc-checkbox = 'X'.
 \*     MODIFY lt_data_sc FROM wa_data_sc.
 \*    ENDLOOP.

    WHEN 'DESELALL'."取消全选
 \*    LOOP AT lt_data_sc INTO wa_data_sc WHERE checkbox = 'X'.
 \*     wa_data_sc-checkbox = ''.
 \*     MODIFY lt_data_sc FROM wa_data_sc.
 \*    ENDLOOP.
     lv_str = '取消全选'.
     MESSAGE lv_str TYPE 'S' DISPLAY LIKE 'E'.
    WHEN 'ZSP'.
 \*    PERFORM frm_sc_tzd.
     lv_str = '生成'.
     MESSAGE lv_str TYPE 'S' DISPLAY LIKE 'E'.
   ENDCASE.

   CALL METHOD ref_grid1->refresh_table_display
    EXCEPTIONS
     finished = 1
     OTHERS  = 2.

  ENDMETHOD.              "handle_user_command

  METHOD handle_data_changed_finished.

   IF e_modified = ''.
    MESSAGE text-t02 TYPE 'S' DISPLAY LIKE 'E'.
    RETURN.
   ENDIF.

   MESSAGE text-t03 TYPE 'S' DISPLAY LIKE 'E'.

   "et_good_cells
   FIELD-SYMBOLS: <fs_lw_good_cells> TYPE lvc_s_modi.


   DELETE et_good_cells WHERE fieldname = 'CHECKBOX'.
   SORT et_good_cells BY row_id.
   DELETE ADJACENT DUPLICATES FROM et_good_cells COMPARING row_id.

 \*  IF NOT et_good_cells[] IS INITIAL.
 \*   LOOP AT et_good_cells ASSIGNING <fs_lw_good_cells>.
 \*    READ TABLE lt_data_sc INTO wa_data_sc
 \*                 INDEX <fs_lw_good_cells>-row_id.
 \*    IF sy-subrc = 0.
 \*     PERFORM frm_clfkje USING wa_data_sc . "处理表间平衡
 \*    ENDIF.
 \*   ENDLOOP.

 \*   PERFORM frm_refresh_table_display.

 \*  ENDIF.

  ENDMETHOD.


  METHOD handle_data_changed.

 

   MESSAGE text-t01 TYPE 'S' DISPLAY LIKE 'E'.
 \*  DATA: lt_mt_mod_cells TYPE lvc_t_modi.
 \*  FIELD-SYMBOLS: <fs_lw_mt_mod_cells> TYPE lvc_s_modi.
 \*  DATA: l_msgv1 TYPE string.
 \*  DATA: d_zyfzk LIKE ztfi083-zyfzk.
 **
 \*  lt_mt_mod_cells = er_data_changed->mt_mod_cells.
 **
 \*  DELETE lt_mt_mod_cells WHERE fieldname = 'CHECK'.
 *
 \*  DATA error_ref TYPE REF TO cx_sy_conversion_no_number.
 \*  DATA err_text TYPE string.
 *
 \*  DATA: l_value TYPE ztfi083-zyfzk.
 *
 \*  IF lt_mt_mod_cells[] IS INITIAL.
 \*   RETURN.
 \*  ENDIF.
 *
 \*  SORT lt_data BY zjsdh.
 *
 \*  LOOP AT lt_mt_mod_cells ASSIGNING <fs_lw_mt_mod_cells>.
 \*   READ TABLE lt_data_sc INTO wa_data_sc INDEX <fs_lw_mt_mod_cells>-row_id.
 \*   IF sy-subrc = 0.
 \*    CLEAR l_msgv1.
 \*    TRY .
 *
 \*      CASE <fs_lw_mt_mod_cells>-fieldname .
 *
 \*       WHEN 'zyfk'.
 *
 \*        READ TABLE lt_data INTO wa_data BINARY SEARCH WITH KEY zjsdh = wa_data_sc-zjsdh.
 \*        IF sy-subrc EQ 0.
 \*         d_zyfzk = wa_data-zyfk.
 \*        ENDIF.
 *
 \*        l_value = <fs_lw_mt_mod_cells>-value .
 \*        IF l_value < 0 OR l_value > d_zyfzk.
 \*         l_msgv1 = '预付账款须在0到预付账款之间'.
 \*        ENDIF.
 **        WHEN 'zkktz'.
 **         l_value = <fs_lw_mt_mod_cells>-value.
 **         IF l_value > 0 .
 **         l_msgv1 = '扣款调整须小于等于零'.
 **        ENDIF.
 \*      ENDCASE.
 **
 **      IF NOT l_msgv1 IS INITIAL.
 **       CALL METHOD er_data_changed->add_protocol_entry
 **        EXPORTING
 **         i_msgid   = 'Z_FI001'
 **         i_msgty   = 'E'
 **         i_msgno   = 000
 **         i_msgv1   = l_msgv1
 **         i_fieldname = <fs_lw_mt_mod_cells>-fieldname
 **         i_row_id  = <fs_lw_mt_mod_cells>-row_id
 **         i_tabix   = <fs_lw_mt_mod_cells>-tabix.
 **      ENDIF.
 *
 \*     CATCH cx_sy_conversion_no_number INTO error_ref ."convt_no_number.
 \*      err_text = error_ref->get_text( ).
 \*      l_msgv1 = err_text.
 \*      CALL METHOD er_data_changed->add_protocol_entry
 \*       EXPORTING
 \*        i_msgid   = 'Z_FI001'
 \*        i_msgty   = 'E'
 \*        i_msgno   = 000
 \*        i_msgv1   = '输入的内容不能转换为数字！请注意取消逗号等字符'
 \*        i_fieldname = <fs_lw_mt_mod_cells>-fieldname
 \*        i_row_id  = <fs_lw_mt_mod_cells>-row_id
 \*        i_tabix   = <fs_lw_mt_mod_cells>-tabix.
 \*    ENDTRY.
 \*   ENDIF.
 \*  ENDLOOP.


  ENDMETHOD.          "handle_data_changed

 ENDCLASS.          "lcl_event_receiver IMPLEMENTATION

 *&---------------------------------------------------------------------*
 *&  选择画面定义
 *&---------------------------------------------------------------------*
 SELECTION-SCREEN BEGIN OF BLOCK bk1 WITH FRAME TITLE text-001.
 SELECT-OPTIONS:s_lifnr FOR lfa1-lifnr.
 SELECTION-SCREEN END OF BLOCK bk1.
 *&---------------------------------------------------------------------*
 \*      INITIALIZATION
 *&---------------------------------------------------------------------*
 INITIALIZATION.

 *&---------------------------------------------------------------------*
 \*       AT SELECTION-SCREEN ON VALUE-REQUEST
 *&---------------------------------------------------------------------*
 *AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_file.

 *&---------------------------------------------------------------------*
 \*       AT SELECTION-SCREEN OUTPUT
 *&---------------------------------------------------------------------*
 AT SELECTION-SCREEN OUTPUT.

 *&---------------------------------------------------------------------*
 \* AT SELECTION-SCREEN
 *&---------------------------------------------------------------------*
 AT SELECTION-SCREEN.

 *&---------------------------------------------------------------------*
 \*       START-OF-SELECTION
 *&---------------------------------------------------------------------*
 START-OF-SELECTION.
  PERFORM frm_get_data.
  PERFORM frm_manipulate_data.
  CALL SCREEN 0100.
 *&------------------------------------------------------------------*
 *&       END-OF-SELECTION
 *&------------------------------------------------------------------*
 END-OF-SELECTION.
 *&---------------------------------------------------------------------*
 *&   Form FRM_GET_DATA
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_get_data .

  SELECT
    lifnr
    name1
    telf1
    telfx
    a~ktokk
    b~txt30
    actss
    stras
    fityp
    kraus
   INTO TABLE gt_lfa1
   FROM lfa1 AS a
   LEFT JOIN t077y AS b ON a~ktokk = b~ktokk AND b~spras = sy-langu
   WHERE lifnr IN s_lifnr.

  IF gt_lfa1 IS NOT INITIAL.
   "取lfm1中数据
   SELECT
   lifnr
   ekorg
   a~kalsk
   kalsb
   minbw
    INTO TABLE gt_lfm1
    FROM lfm1 AS a
    LEFT JOIN tmkkt AS b ON a~kalsk = b~kalsk AND b~spras EQ sy-langu
    FOR ALL ENTRIES IN gt_lfa1
    WHERE lifnr = gt_lfa1-lifnr.

   "取lfbk数据
   SELECT
   a~lifnr
   a~banks
   a~bankl
   koinh
   banka
   bankn
   bkref
   INTO TABLE gt_lfbk
   FROM lfbk AS a
   INNER JOIN bnka AS b ON a~banks = b~banks AND a~bankl = b~bankl
   FOR ALL ENTRIES IN gt_lfa1
   WHERE lifnr = gt_lfa1-lifnr.
  ENDIF.

 ENDFORM.
 *&---------------------------------------------------------------------*
 *&   Form FRM_MANIPULATE_DATA
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_manipulate_data .
  DATA:lt_lfm1 LIKE TABLE OF gs_lfm1,
    ls_lfm1 LIKE gs_lfm1.
  DATA:lv_tabix TYPE sy-tabix.

  lt_lfm1 = gt_lfm1.
  LOOP AT lt_lfm1 INTO ls_lfm1.
   lv_tabix = sy-tabix.
   IF ls_lfm1-ekorg = 'P001'.
    DELETE lt_lfm1 INDEX lv_tabix.
   ENDIF.
  ENDLOOP.
  SORT lt_lfm1 BY lifnr.

  SORT gt_lfm1 BY lifnr kalsk.
  DELETE ADJACENT DUPLICATES FROM gt_lfm1 COMPARING lifnr kalsk.
 *FIELD-SYMBOLs <LFS>.
  LOOP AT gt_lfm1 ASSIGNING FIELD-SYMBOL(<lfs_lfm1>).
   READ TABLE lt_lfm1 INTO ls_lfm1 WITH KEY lifnr = <lfs_lfm1>-lifnr
                   BINARY SEARCH.
   IF sy-subrc EQ 0.
    <lfs_lfm1>-minbw = ls_lfm1-minbw.
   ENDIF.
  ENDLOOP.
 ENDFORM.
 *&---------------------------------------------------------------------*
 *&   Form FRM_DISPLAY
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_display .

 ENDFORM.
 *&---------------------------------------------------------------------*
 *&   Module STATUS_0100 OUTPUT
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 MODULE status_0100 OUTPUT.
  SET PF-STATUS 'S0100'.
 \* SET TITLEBAR 'xxx'.


  IF ref_grid1 IS INITIAL.
   "创建容器对象1。
   CREATE OBJECT ref_cust_container1
    EXPORTING
     container_name = 'CTR_1'.

   CREATE OBJECT ref_grid1 EXPORTING i_parent = ref_cust_container1.

   IF event_receiver IS INITIAL.
    CREATE OBJECT event_receiver.
   ENDIF.

   SET HANDLER event_receiver->handle_hotspot_click FOR ref_grid1.
   SET HANDLER event_receiver->handle_user_command FOR ref_grid1.
   SET HANDLER event_receiver->handle_toolbar FOR ref_grid1.
   SET HANDLER event_receiver->handle_data_changed FOR ref_grid1.
   SET HANDLER event_receiver->handle_data_changed_finished FOR ref_grid1.

   "显示ALV.
   gv_layout-grid_title = 'LFA1'.
   gs_variant-report = sy-repid.
   gs_variant-handle = '0100'.
   gv_layout-stylefname = 'CELL'.

   gv_alv_save = 'A'.

   PERFORM frm_call_alvgrid TABLES gt_lfa1
                   gt_fieldcat1
                   gt_exclude
               USING ref_grid1
                  gv_layout
                  gv_alv_save
                  gs_variant
                  '1'.


   CALL METHOD cl_gui_control=>set_focus  "设置焦点在go_grid 上
    EXPORTING
     control = ref_grid1.
  ELSE.
   CALL METHOD ref_grid1->refresh_table_display
 \*  EXPORTING
 \*   is_stable   =
 \*   i_soft_refresh =
    EXCEPTIONS
     finished = 1
     OTHERS  = 2.
   IF sy-subrc <> 0.
    MESSAGE s000 WITH '刷新ALV 出现了问题！'.
   ENDIF.
  ENDIF.


  IF ref_grid2 IS INITIAL.
   "创建容器对象2。
   CREATE OBJECT ref_cust_container2
    EXPORTING
     container_name = 'CTR_2'.
   CREATE OBJECT ref_grid2 EXPORTING i_parent = ref_cust_container2.
   gv_layout-grid_title = 'LFM1'.
   PERFORM frm_call_alvgrid TABLES gt_lfm1
                   gt_fieldcat2
                   gt_exclude
               USING ref_grid2
                  gv_layout
                  gv_alv_save
                  gs_variant
                  '2'.
  ELSE.
   CALL METHOD ref_grid2->refresh_table_display
 \*  EXPORTING
 \*   is_stable   =
 \*   i_soft_refresh =
    EXCEPTIONS
     finished = 1
     OTHERS  = 2.
   IF sy-subrc <> 0.
    MESSAGE s000 WITH '刷新ALV 出现了问题！'.
   ENDIF.
  ENDIF.

  IF ref_grid3 IS INITIAL.
   "创建容器对象3。
   CREATE OBJECT ref_cust_container3
    EXPORTING
     container_name = 'CTR_3'.
   CREATE OBJECT ref_grid3 EXPORTING i_parent = ref_cust_container3.

 

   gv_layout-grid_title = 'LFBK'.
   PERFORM frm_call_alvgrid TABLES gt_lfbk
                   gt_fieldcat3
                   gt_exclude
               USING ref_grid3
                  gv_layout
                  gv_alv_save
                  gs_variant
                  '3'.
  ELSE.
   CALL METHOD ref_grid3->refresh_table_display
 \*  EXPORTING
 \*   is_stable   =
 \*   i_soft_refresh =
    EXCEPTIONS
     finished = 1
     OTHERS  = 2.
   IF sy-subrc <> 0.
    MESSAGE s000 WITH '刷新ALV 出现了问题！'.
   ENDIF.
  ENDIF.

 


 ENDMODULE.
 *&---------------------------------------------------------------------*
 *&   Module USER_COMMAND_0100 INPUT
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 MODULE user_command_0100 INPUT.
  CASE sy-ucomm.
   WHEN '&F03' OR '&F15' OR '&F12'.
    CALL METHOD ref_cust_container1->free.
    CALL METHOD ref_cust_container2->free.
    CALL METHOD ref_cust_container3->free.
    SET SCREEN 0.
   WHEN 'TEST'.
    MESSAGE text-t01 TYPE 'S' DISPLAY LIKE 'E'.
  ENDCASE.
 ENDMODULE.
 *&---------------------------------------------------------------------*
 *&   Form FRM_CALL_ALVGRID5
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_call_alvgrid TABLES ut_outtab TYPE STANDARD TABLE
                ut_fieldcat TYPE STANDARD TABLE
                ut_exclude TYPE STANDARD TABLE
             USING ref_grid TYPE REF TO cl_gui_alv_grid
                u_layout TYPE lvc_s_layo
                u_alv_save TYPE c
                u_variant TYPE disvariant
 \*               ut_exclude TYPE ui_functions
                u_flag.
  "创建显示字段
  PERFORM frm_set_fieldcat TABLES ut_fieldcat USING u_flag.


  CALL METHOD ref_grid->register_edit_event
   EXPORTING
    i_event_id = cl_gui_alv_grid=>mc_evt_modified.

  IF u_flag EQ 1.
   DATA(lv_flg) = 1.
  ELSE.
   lv_flg = 0.
  ENDIF.

  CALL METHOD ref_grid->set_ready_for_input
   EXPORTING
    i_ready_for_input = lv_flg.

  "ALV的第一次显示。
  CALL METHOD ref_grid->set_table_for_first_display
   EXPORTING
    is_layout    = u_layout
    i_save     = u_alv_save
    is_variant   = u_variant
 \*   it_toolbar_excluding = ut_exclude
   CHANGING
    it_outtab    = ut_outtab[]
    it_fieldcatalog = ut_fieldcat[].

 ENDFORM.

 *&---------------------------------------------------------------------*
 *&   Form FRM_SET_FIELDCAT5
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_set_fieldcat TABLES ut_fieldcat TYPE STANDARD TABLE USING u_flag .
  DATA: l_nn TYPE i.
  "定义宏
  DEFINE add_fieldcat.
   L_NN = L_NN + 1.
   CLEAR WA_FIELDCAT.
   WA_FIELDCAT-FIELDNAME   = &1.
   WA_FIELDCAT-COLTEXT    = &2.
   WA_FIELDCAT-COL_POS    = L_NN.
   WA_FIELDCAT-KEY      = &3.
   WA_FIELDCAT-JUST     = &4.
   WA_FIELDCAT-OUTPUTLEN   = &5.
   WA_FIELDCAT-FIX_COLUMN  = &6.
   WA_FIELDCAT-EDIT     = &7.
   WA_FIELDCAT-ICON     = ''.
   WA_FIELDCAT-HOTSPOT    = &8.
   APPEND WA_FIELDCAT TO ut_fieldcat.
  END-OF-DEFINITION.

  CLEAR: ut_fieldcat[],ut_fieldcat.
  "赋值
  CASE u_flag .
   WHEN '1'.
    add_fieldcat  'LIFNR'  '供应商编码'  ''    '' '' '' ''  'X'.
    add_fieldcat  'NAME1'  '供应商名称'  ''    '' '' '' ''  ''.
    add_fieldcat  'TELF1'  '供应商电话'  ''    '' '' '' 'X'  ''.
    add_fieldcat  'TELFX' '供应商传真'  ''    '' '' '' ''  ''.
    add_fieldcat  'KTOKK'  '供应商账户组'  ''    '' '' ''  '' ''.
    add_fieldcat  'TXT30'  '供应商账户组描述'  ''    '' ''  '' '' ''.
    add_fieldcat  'ACTSS'  '供应商状态'  ''    '' '' '' ''  ''.
    add_fieldcat  'STRAS'  '供应商地址(街道门牌号)'  ''    '' '' '' ''  ''.
    add_fieldcat  'FITYP'  '供应商税类型'  ''    '' '' '' '' '' .
    add_fieldcat  'KRAUS'  '旧供应商号'  ''    '' '' '' '' ''.
   WHEN '2'.
    add_fieldcat  'LIFNR'    '供应商编码'  ''    '' '' '' '' ''.
    add_fieldcat  'KALSK'    '方案组'  ''    '' '' '' '' ''.
    add_fieldcat  'KALSB'    '方案组描述'  ''    '' '' '' '' ''.
    add_fieldcat  'MINBW'    '最小订货金额'  ''    '' '' '' '' ''.
   WHEN '3'.

    add_fieldcat  'LIFNR'    '供应商编码'  ''    '' '' '' '' ''.
    add_fieldcat  'KOINH'    '银行户主'  ''    '' '' '' '' ''.
    add_fieldcat  'BANKA'    '开户银行'  ''    '' '' '' '' ''.
    add_fieldcat  'BANKN'    '银行帐号1'  ''    '' '' '' '' ''.
    add_fieldcat  'BKREF'    '银行帐号2'  ''    '' '' '' '' ''.

  ENDCASE.
 ENDFORM.          " FRM_SET_FIELDCAT
 *&---------------------------------------------------------------------*
 *&   Form FRM_CREATE_TREE
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_create_tree .
 *事件内表及单个事件对象.
  DATA: lt_event TYPE cntl_simple_events,
     ls_event TYPE cntl_simple_event.
 \* IF ref_tree IS INITIAL.
 \*  "创建容器对象1。
 \*  CREATE OBJECT ref_cust_container
 \*   EXPORTING
 \*    container_name = 'CTR_4'.
 *
 \*  CREATE OBJECT ref_tree
 \*   EXPORTING
 \*    parent = ref_cust_container.
 *
 \*  IF event_receiver_tree IS INITIAL.
 \*   CREATE OBJECT event_receiver_tree.
 \*  ENDIF.
 *
 **定义双击事件
 \*  ls_event-eventid = cl_gui_simple_tree=>eventid_node_double_click.
 \*  ls_event-appl_event = 'X'.
 \*  APPEND ls_event TO lt_event.
 **添加事件内表
 \*  CALL METHOD ref_tree->set_registered_gt_event
 \*   EXPORTING
 \*    gt_event = lt_event.
 *
 **注册双击事件
 \*  SET HANDLER lcl_event_receiver_tree->handle_node_double_click FOR ref_tree.
 *
 \*  CALL METHOD cl_gui_control=>set_focus  "设置焦点在go_grid 上
 \*   EXPORTING
 \*    control = ref_tree.
 \* ELSE.
 *
 \* ENDIF.
 ENDFORM.

## 6.z_test_gsw_g5

REPORT zw_test_0528.
 *&---------------------------------------------------------------------*
 *&   Module ALV_DISPLAY OUTPUT
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 *要使用到的表
 TABLES: lfa1,t077y,lfm1,tmkkt,bnka,lfbk.
 *定义变量
 DATA:container  TYPE REF TO cl_gui_custom_container, "存放ALV容器
   alv     TYPE REF TO cl_gui_alv_grid, "ALV的网格实例
   lt_fieldcat TYPE lvc_t_fcat, "存放字段目录的内表
   ls_fieldcat TYPE lvc_s_fcat,
   wa_layout  TYPE lvc_s_layo. "布局结构
 DATA:ok_code TYPE sy-ucomm."元素清单里的一致
 *定义内表结构
 DATA: BEGIN OF gs_out,
     lifnr LIKE lfa1-lifnr,
     name LIKE lfa1-name1,
     telf1 LIKE lfa1-telf1,
     telfx LIKE lfa1-telfx,
     kotkk LIKE lfa1-ktokk,
     txt30 LIKE t077y-txt30,
     kalsk LIKE lfm1-kalsk,
     kalsb LIKE tmkkt-kalsb,
     actss LIKE lfa1-actss,
     stras LIKE lfa1-stras,
     minbw LIKE lfm1-minbw,
     fityp LIKE lfa1-fityp,
     kraus LIKE lfa1-kraus,
     banka LIKE bnka-banka,
     bankn LIKE lfbk-bankn,
     bkref LIKE lfbk-bkref,
    END OF gs_out.
 DATA gt_out LIKE STANDARD TABLE OF gs_out.

 *&---------------------------------------------------------------------*
 *&  选择画面定义
 *&---------------------------------------------------------------------*
 SELECTION-SCREEN: BEGIN OF BLOCK bk1 WITH FRAME TITLE text-001.
  SELECT-OPTIONS s_lifnr FOR LFA1-LIFNR.
 SELECTION-SCREEN: END OF BLOCK bk1.

 *&---------------------------------------------------------------------*
 \*       START-OF-SELECTION
 *&---------------------------------------------------------------------*
 START-OF-SELECTION.

 


 CALL SCREEN 8002."在调用module前调用屏幕，很重要

 MODULE alv_display OUTPUT.
  PERFORM alv_display.
 ENDMODULE.


 FORM alv_display.
  IF alv IS INITIAL.
   CREATE OBJECT container  "创建容器对象
    EXPORTING
     container_name = 'CONTAINER1'.
   IF container IS NOT INITIAL.
    CREATE OBJECT alv "创建ALV的网格实例
     EXPORTING
      i_parent = container.
   ENDIF.

 


   PERFORM set_fieldcat ."封装定义显示字段

   PERFORM set_layout ."封装定义布局

   PERFORM get_data."封装取数


   CALL METHOD alv->set_table_for_first_display "调用显示ALV的方法
    EXPORTING
     i_save            = 'A'
     is_layout           = wa_layout
    CHANGING
     it_outtab           = gt_out
     it_fieldcatalog        = lt_fieldcat
    EXCEPTIONS
     invalid_parameter_combination = 1
     program_error         = 2
     too_many_lines        = 3
     OTHERS            = 4.
   IF sy-subrc <> 0.
   ENDIF.
  ELSE.
   CALL METHOD alv->refresh_table_display "如果有容器的话，调用刷新ALV的方法
    EXCEPTIONS
     finished = 1
     OTHERS  = 2.
   IF sy-subrc <> 0.
   ENDIF.
  ENDIF.
 ENDFORM.

 MODULE STATUS_8002 OUTPUT.
  SET PF-STATUS 'STANDARD'. "这里使用SE41复制一个菜单
 \* SET TITLEBAR 'xxx'.
 ENDMODULE.


 MODULE USER_COMMAND_8002 INPUT. "调用屏幕事件
  CLEAR:OK_CODE.
  CASE OK_CODE.
   WHEN '&add'.
    LEAVE SCREEN.
   WHEN OTHERS.
  ENDCASE.
 ENDMODULE.

 FORM set_fieldcat."定义显示额
  DATA:col_pos TYPE i.
 \* fieldcat
  col_pos = 0.
 *供应商编码
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'lifnr'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '供应商编码'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
 *供应商名称
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'name'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '供应商名称'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
 *供应商电话
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'telf1'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '供应商电话'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
 *供应商传真
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'telfx'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '供应商传真'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
  "供应商账户组"
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'kotkk'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '供应商账户组'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
  "供应商账户组描述"
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'txt30'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '供应商账户组描述'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
  "方案组"
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'kalsk'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '方案组'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
  "方案组描述"
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'kalsb'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '方案组描述'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
  "供应商状态"
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'actss'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '供应商状态'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
  "供应商地址(街道门牌号)"
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'stras'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '供应商地址(街道门牌号)'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
 "最小订单金额"
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'minbw'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '最小订单金额'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
 "供应商税类型"
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'fityp'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '供应商税类型'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
  "旧供应商号"
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'kraus'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '旧供应商号'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
  "开户银行"
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'banka'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '旧供应商号'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
 "银行帐号1"
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'bankn'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '银行账号1'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
 "银行账号2"
  col_pos = col_pos + 1.
  ls_fieldcat-fieldname = 'bkref'.
  ls_fieldcat-col_pos = col_pos.
  ls_fieldcat-key = 'X'.
  ls_fieldcat-scrtext_m = '银行账号2'. "这个字段和常规的不一样，常规的为SELTEXT_M
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR:ls_fieldcat.
 ENDFORM.


 FORM set_layout."定义页面布局"
  WA_LAYOUT-CWIDTH_OPT = 'X'.
  WA_LAYOUT-ZEBRA = 'X'.
  WA_LAYOUT-GRID_TITLE = '销售订单查询报表'. "小标题
  WA_LAYOUT-SMALLTITLE = 'X'.
 ENDFORM.

 FORM get_data.
   SELECT
    lfa1~lifnr ,
    lfa1~name1 as name,
     lfa1~telf1,
     lfa1~telfx,
 \*    lfa1~ktokk,
     t077y~txt30,
     lfm1~kalsk,
    tmkkt~kalsb,
     lfa1~actss,
     lfa1~stras,
    lfm1~minbw,
     lfa1~fityp,
     lfa1~kraus,
    bnka~banka,
    lfbk~bankn,
     lfbk~bkref
    INTO CORRESPONDING FIELDS OF TABLE @gt_out
    FROM
    lfa1 LEFT JOIN t077y ON lfa1~KTOKK = t077y~KTOKK
    LEFT JOIN lfm1 on lfa1~lifnr = lfm1~lifnr
    LEFT JOIN tmkkt on lfa1~spras = tmkkt~spras
    LEFT JOIN lfbk on lfa1~lifnr = lfbk~lifnr
    LEFT JOIN bnka on BNKA~BANKS = LFBK~BANKS AND BNKA~BANKL = LFBK~BANKL
    WHERE
    lfa1~lifnr in @s_lifnr AND BNKA~BANKL = LFBK~BANKL and BNKA~BANKS = LFBK~BANKS .
 \*   lfa1,t077y,lfm1,tmkkt,bnka,lfbk
    sort gt_out.
 delete adjacent duplicates from gt_out COMPARING ALL FIELDS.
 ENDFORM.

## 7.z_test_gsw_g3

REPORT Z_TEST_GSW_G3 MESSAGE-ID zk.

************************************************************************
 \* Report ZTEST_CC005
************************************************************************
 *
 \* 作者：  CC
 \* 完成日期：2020/01/13
 \* 描述：  内表及数据库指令练习
************************************************************************
 \* 版本号 日期  作者  修改描述 功能更改说明书
************************************************************************
 \* 1.0 20209/01/13 cc  程序创建
************************************************************************

 TABLES:lfa1,lfm1,lfbk.
 TYPE-POOLS:slis.


 DATA:BEGIN OF gs_lfa1,
    lifnr LIKE lfa1-lifnr,
    name1 LIKE lfa1-name1,
    telf1 LIKE lfa1-telf1,
    telfx LIKE lfa1-telfx,
    ktokk LIKE lfa1-ktokk,
    txt30 LIKE t077y-txt30,
    actss LIKE lfa1-actss,
    stras LIKE lfa1-stras,
    fityp LIKE lfa1-fityp,
    kraus LIKE lfa1-kraus,
   END OF gs_lfa1,
   gt_lfa1 LIKE TABLE OF gs_lfa1.

 DATA:BEGIN OF gs_lfm1,
    lifnr LIKE lfm1-lifnr,
    ekorg LIKE lfm1-ekorg,
    kalsk LIKE lfm1-kalsk,
    kalsb LIKE tmkkt-kalsb,
    minbw LIKE lfm1-minbw,
   END OF gs_lfm1,
   gt_lfm1 LIKE TABLE OF gs_lfm1.

 DATA:BEGIN OF gs_lfbk,
    lifnr LIKE lfm1-lifnr,
    banks LIKE lfbk-banks,
    bankl LIKE lfbk-bankl,
    koinh LIKE lfbk-koinh,
    banka LIKE bnka-banka,
    bankn LIKE lfbk-bankn,
    bkref LIKE lfbk-bkref,
   END OF gs_lfbk,
   gt_lfbk LIKE TABLE OF gs_lfbk.

 DATA:gv_line TYPE i.

 

 \* Define ALV attribute
 DATA:go_grid   TYPE REF TO cl_gui_alv_grid.
 DATA:gt_fieldcat TYPE lvc_t_fcat,
   gs_variant TYPE disvariant,
   gs_fieldcat LIKE LINE OF gt_fieldcat,
   gt_exclude TYPE slis_t_extab,
   gv_repid  TYPE syrepid.

 DATA: gv_layout  TYPE lvc_s_layo,       "ALV LAYOUT
    gv_layout1 TYPE lvc_s_layo,
    gv_layout2 TYPE lvc_s_layo,
    gv_layout3 TYPE lvc_s_layo,
    gv_alv_save TYPE c.       "ALV VARIANT

 DATA: wa_fieldcat     TYPE lvc_s_fcat,
    gt_fieldcat1    TYPE lvc_t_fcat,
    gt_fieldcat2    TYPE lvc_t_fcat,
    gt_fieldcat3    TYPE lvc_t_fcat,

    ref_cust_container TYPE REF TO cl_gui_custom_container,
    ref_cust_container1 TYPE REF TO cl_gui_custom_container,
    ref_cust_container2 TYPE REF TO cl_gui_custom_container,
    ref_cust_container3 TYPE REF TO cl_gui_custom_container,
     
    ref_tree      TYPE REF TO cl_gui_simple_tree,
    ref_grid1      TYPE REF TO cl_gui_alv_grid,
    ref_grid2      TYPE REF TO cl_gui_alv_grid,
    ref_grid3      TYPE REF TO cl_gui_alv_grid.

 




 CLASS lcl_event_receiver DEFINITION DEFERRED.
 DATA: event_receiver TYPE REF TO lcl_event_receiver.

 CLASS lcl_event_receiver_tree DEFINITION DEFERRED.

 CLASS lcl_event_receiver_tree DEFINITION.
  PUBLIC SECTION.
   METHODS handle_node_double_click
          FOR EVENT node_double_click OF cl_gui_simple_tree
    IMPORTING node_key.
  PRIVATE SECTION.
 ENDCLASS.

 CLASS lcl_event_receiver DEFINITION.

  PUBLIC SECTION.

   METHODS handle_hotspot_click
          FOR EVENT hotspot_click OF cl_gui_alv_grid
    IMPORTING e_row_id
          e_column_id
          es_row_no.

   METHODS handle_toolbar
          FOR EVENT toolbar OF cl_gui_alv_grid
    IMPORTING e_object e_interactive.

   METHODS handle_user_command
          FOR EVENT user_command OF cl_gui_alv_grid
    IMPORTING e_ucomm.

   METHODS handle_data_changed
          FOR EVENT data_changed OF cl_gui_alv_grid
    IMPORTING er_data_changed.


   METHODS handle_data_changed_finished
          FOR EVENT data_changed_finished OF cl_gui_alv_grid
    IMPORTING e_modified et_good_cells.

  PRIVATE SECTION.

 ENDCLASS.          "lcl_event_receiver DEFINITION

 **----------------------------------------------------------------------*
 **    CLASS lcl_event_receiver IMPLEMENTATION
 **----------------------------------------------------------------------*
 **
 **----------------------------------------------------------------------*
 CLASS lcl_event_receiver_tree IMPLEMENTATION.
  METHOD handle_node_double_click.

  ENDMETHOD.
 ENDCLASS.
 CLASS lcl_event_receiver IMPLEMENTATION.

  METHOD handle_toolbar. "设置工具栏
 \* § 2.In event handler method for event TOOLBAR: Append own functions
 \*  by using event parameter E_OBJECT.
   DATA: ls_toolbar TYPE stb_button.

   "分隔符
   CLEAR ls_toolbar.
   MOVE 3 TO ls_toolbar-butn_type.
   APPEND ls_toolbar TO e_object->mt_toolbar.

   "全选按钮
   CLEAR ls_toolbar.
   MOVE 'SELALL' TO ls_toolbar-function. "全选
   MOVE icon_select_all TO ls_toolbar-icon.
   MOVE '全选'(111) TO ls_toolbar-quickinfo.
   MOVE ''(112) TO ls_toolbar-text.
   MOVE '' TO ls_toolbar-disabled.
   APPEND ls_toolbar TO e_object->mt_toolbar.

   "取消选择
   CLEAR ls_toolbar.
   MOVE 'DESELALL' TO ls_toolbar-function. "取消选择
   MOVE icon_deselect_all TO ls_toolbar-icon.
   MOVE '取消全选'(111) TO ls_toolbar-quickinfo.
   MOVE ''(112) TO ls_toolbar-text.
   MOVE '' TO ls_toolbar-disabled.
   APPEND ls_toolbar TO e_object->mt_toolbar.

   "分隔符
   CLEAR ls_toolbar.
   MOVE 3 TO ls_toolbar-butn_type.
   APPEND ls_toolbar TO e_object->mt_toolbar.

   CLEAR ls_toolbar.
   MOVE 'ZSP' TO ls_toolbar-function. "生成付款通知单
   MOVE icon_allow TO ls_toolbar-icon. "@8X@
   MOVE '生成'(111) TO ls_toolbar-quickinfo.
   MOVE '生成结算通知单'(112) TO ls_toolbar-text.
   MOVE '' TO ls_toolbar-disabled.
   APPEND ls_toolbar TO e_object->mt_toolbar.


  ENDMETHOD.          "handle_toolbar

  METHOD handle_hotspot_click.

 \*  IF e_column_id-fieldname = 'ZJSDH'.
 \*   READ TABLE lt_data_sc INTO wa_data_sc INDEX e_row_id.
 \*   IF sy-subrc = 0.
 \*    SET PARAMETER ID 'ZJSDH' FIELD wa_data_sc-zjsdh.
 \*    PERFORM frm_display_jsdmx.
 *
 \*   ENDIF.
 \*  ENDIF.
  ENDMETHOD.

  METHOD handle_user_command. "处理用户命令
 \* § 3.In event handler method for event USER_COMMAND: Query your
 \*  function codes defined in step 2 and react accordingly.

   DATA: lt_rows TYPE lvc_t_row.
   CASE e_ucomm.
    WHEN 'SELALL'."全选
     DATA(lv_str) = '全选'.
     MESSAGE lv_str TYPE 'S' DISPLAY LIKE 'E'.
 \*    LOOP AT lt_data_sc INTO wa_data_sc WHERE checkbox = ''.
 \*     wa_data_sc-checkbox = 'X'.
 \*     MODIFY lt_data_sc FROM wa_data_sc.
 \*    ENDLOOP.

    WHEN 'DESELALL'."取消全选
 \*    LOOP AT lt_data_sc INTO wa_data_sc WHERE checkbox = 'X'.
 \*     wa_data_sc-checkbox = ''.
 \*     MODIFY lt_data_sc FROM wa_data_sc.
 \*    ENDLOOP.
     lv_str = '取消全选'.
     MESSAGE lv_str TYPE 'S' DISPLAY LIKE 'E'.
    WHEN 'ZSP'.
 \*    PERFORM frm_sc_tzd.
     lv_str = '生成'.
     MESSAGE lv_str TYPE 'S' DISPLAY LIKE 'E'.
   ENDCASE.

   CALL METHOD ref_grid1->refresh_table_display
    EXCEPTIONS
     finished = 1
     OTHERS  = 2.

  ENDMETHOD.              "handle_user_command

  METHOD handle_data_changed_finished.

   IF e_modified = ''.
    MESSAGE text-t02 TYPE 'S' DISPLAY LIKE 'E'.
    RETURN.
   ENDIF.

   MESSAGE text-t03 TYPE 'S' DISPLAY LIKE 'E'.

   "et_good_cells
   FIELD-SYMBOLS: <fs_lw_good_cells> TYPE lvc_s_modi.


   DELETE et_good_cells WHERE fieldname = 'CHECKBOX'.
   SORT et_good_cells BY row_id.
   DELETE ADJACENT DUPLICATES FROM et_good_cells COMPARING row_id.

 \*  IF NOT et_good_cells[] IS INITIAL.
 \*   LOOP AT et_good_cells ASSIGNING <fs_lw_good_cells>.
 \*    READ TABLE lt_data_sc INTO wa_data_sc
 \*                 INDEX <fs_lw_good_cells>-row_id.
 \*    IF sy-subrc = 0.
 \*     PERFORM frm_clfkje USING wa_data_sc . "处理表间平衡
 \*    ENDIF.
 \*   ENDLOOP.

 \*   PERFORM frm_refresh_table_display.

 \*  ENDIF.

  ENDMETHOD.


  METHOD handle_data_changed.

 

   MESSAGE text-t01 TYPE 'S' DISPLAY LIKE 'E'.
 \*  DATA: lt_mt_mod_cells TYPE lvc_t_modi.
 \*  FIELD-SYMBOLS: <fs_lw_mt_mod_cells> TYPE lvc_s_modi.
 \*  DATA: l_msgv1 TYPE string.
 \*  DATA: d_zyfzk LIKE ztfi083-zyfzk.
 **
 \*  lt_mt_mod_cells = er_data_changed->mt_mod_cells.
 **
 \*  DELETE lt_mt_mod_cells WHERE fieldname = 'CHECK'.
 *
 \*  DATA error_ref TYPE REF TO cx_sy_conversion_no_number.
 \*  DATA err_text TYPE string.
 *
 \*  DATA: l_value TYPE ztfi083-zyfzk.
 *
 \*  IF lt_mt_mod_cells[] IS INITIAL.
 \*   RETURN.
 \*  ENDIF.
 *
 \*  SORT lt_data BY zjsdh.
 *
 \*  LOOP AT lt_mt_mod_cells ASSIGNING <fs_lw_mt_mod_cells>.
 \*   READ TABLE lt_data_sc INTO wa_data_sc INDEX <fs_lw_mt_mod_cells>-row_id.
 \*   IF sy-subrc = 0.
 \*    CLEAR l_msgv1.
 \*    TRY .
 *
 \*      CASE <fs_lw_mt_mod_cells>-fieldname .
 *
 \*       WHEN 'zyfk'.
 *
 \*        READ TABLE lt_data INTO wa_data BINARY SEARCH WITH KEY zjsdh = wa_data_sc-zjsdh.
 \*        IF sy-subrc EQ 0.
 \*         d_zyfzk = wa_data-zyfk.
 \*        ENDIF.
 *
 \*        l_value = <fs_lw_mt_mod_cells>-value .
 \*        IF l_value < 0 OR l_value > d_zyfzk.
 \*         l_msgv1 = '预付账款须在0到预付账款之间'.
 \*        ENDIF.
 **        WHEN 'zkktz'.
 **         l_value = <fs_lw_mt_mod_cells>-value.
 **         IF l_value > 0 .
 **         l_msgv1 = '扣款调整须小于等于零'.
 **        ENDIF.
 \*      ENDCASE.
 **
 **      IF NOT l_msgv1 IS INITIAL.
 **       CALL METHOD er_data_changed->add_protocol_entry
 **        EXPORTING
 **         i_msgid   = 'Z_FI001'
 **         i_msgty   = 'E'
 **         i_msgno   = 000
 **         i_msgv1   = l_msgv1
 **         i_fieldname = <fs_lw_mt_mod_cells>-fieldname
 **         i_row_id  = <fs_lw_mt_mod_cells>-row_id
 **         i_tabix   = <fs_lw_mt_mod_cells>-tabix.
 **      ENDIF.
 *
 \*     CATCH cx_sy_conversion_no_number INTO error_ref ."convt_no_number.
 \*      err_text = error_ref->get_text( ).
 \*      l_msgv1 = err_text.
 \*      CALL METHOD er_data_changed->add_protocol_entry
 \*       EXPORTING
 \*        i_msgid   = 'Z_FI001'
 \*        i_msgty   = 'E'
 \*        i_msgno   = 000
 \*        i_msgv1   = '输入的内容不能转换为数字！请注意取消逗号等字符'
 \*        i_fieldname = <fs_lw_mt_mod_cells>-fieldname
 \*        i_row_id  = <fs_lw_mt_mod_cells>-row_id
 \*        i_tabix   = <fs_lw_mt_mod_cells>-tabix.
 \*    ENDTRY.
 \*   ENDIF.
 \*  ENDLOOP.


  ENDMETHOD.          "handle_data_changed

 ENDCLASS.          "lcl_event_receiver IMPLEMENTATION

 *&---------------------------------------------------------------------*
 *&  选择画面定义
 *&---------------------------------------------------------------------*
 SELECTION-SCREEN BEGIN OF BLOCK bk1 WITH FRAME TITLE text-001.
 SELECT-OPTIONS:s_lifnr FOR lfa1-lifnr.
 SELECTION-SCREEN END OF BLOCK bk1.
 *&---------------------------------------------------------------------*
 \*      INITIALIZATION
 *&---------------------------------------------------------------------*
 INITIALIZATION.

 *&---------------------------------------------------------------------*
 \*       AT SELECTION-SCREEN ON VALUE-REQUEST
 *&---------------------------------------------------------------------*
 *AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_file.

 *&---------------------------------------------------------------------*
 \*       AT SELECTION-SCREEN OUTPUT
 *&---------------------------------------------------------------------*
 AT SELECTION-SCREEN OUTPUT.

 *&---------------------------------------------------------------------*
 \* AT SELECTION-SCREEN
 *&---------------------------------------------------------------------*
 AT SELECTION-SCREEN.

 *&---------------------------------------------------------------------*
 \*       START-OF-SELECTION
 *&---------------------------------------------------------------------*
 START-OF-SELECTION.
  PERFORM frm_get_data.
  PERFORM frm_manipulate_data.
  CALL SCREEN 0100.
 *&------------------------------------------------------------------*
 *&       END-OF-SELECTION
 *&------------------------------------------------------------------*
 END-OF-SELECTION.
 *&---------------------------------------------------------------------*
 *&   Form FRM_GET_DATA
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_get_data .

  SELECT
    lifnr
    name1
    telf1
    telfx
    a~ktokk
    b~txt30
    actss
    stras
    fityp
    kraus
   INTO TABLE gt_lfa1
   FROM lfa1 AS a
   LEFT JOIN t077y AS b ON a~ktokk = b~ktokk AND b~spras = sy-langu
   WHERE lifnr IN s_lifnr.

  IF gt_lfa1 IS NOT INITIAL.
   "取lfm1中数据
   SELECT
   lifnr
   ekorg
   a~kalsk
   kalsb
   minbw
    INTO TABLE gt_lfm1
    FROM lfm1 AS a
    LEFT JOIN tmkkt AS b ON a~kalsk = b~kalsk AND b~spras EQ sy-langu
    FOR ALL ENTRIES IN gt_lfa1
    WHERE lifnr = gt_lfa1-lifnr.

   "取lfbk数据
   SELECT
   a~lifnr
   a~banks
   a~bankl
   koinh
   banka
   bankn
   bkref
   INTO TABLE gt_lfbk
   FROM lfbk AS a
   INNER JOIN bnka AS b ON a~banks = b~banks AND a~bankl = b~bankl
   FOR ALL ENTRIES IN gt_lfa1
   WHERE lifnr = gt_lfa1-lifnr.
  ENDIF.

 ENDFORM.
 *&---------------------------------------------------------------------*
 *&   Form FRM_MANIPULATE_DATA
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_manipulate_data .
  DATA:lt_lfm1 LIKE TABLE OF gs_lfm1,
    ls_lfm1 LIKE gs_lfm1.
  DATA:lv_tabix TYPE sy-tabix.

  lt_lfm1 = gt_lfm1.
  LOOP AT lt_lfm1 INTO ls_lfm1.
   lv_tabix = sy-tabix.
   IF ls_lfm1-ekorg = 'P001'.
    DELETE lt_lfm1 INDEX lv_tabix.
   ENDIF.
  ENDLOOP.
  SORT lt_lfm1 BY lifnr.

  SORT gt_lfm1 BY lifnr kalsk.
  DELETE ADJACENT DUPLICATES FROM gt_lfm1 COMPARING lifnr kalsk.
 *FIELD-SYMBOLs <LFS>.
  LOOP AT gt_lfm1 ASSIGNING FIELD-SYMBOL(<lfs_lfm1>).
   READ TABLE lt_lfm1 INTO ls_lfm1 WITH KEY lifnr = <lfs_lfm1>-lifnr
                   BINARY SEARCH.
   IF sy-subrc EQ 0.
    <lfs_lfm1>-minbw = ls_lfm1-minbw.
   ENDIF.
  ENDLOOP.
 ENDFORM.
 *&---------------------------------------------------------------------*
 *&   Form FRM_DISPLAY
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_display .

 ENDFORM.
 *&---------------------------------------------------------------------*
 *&   Module STATUS_0100 OUTPUT
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 MODULE status_0100 OUTPUT.
  SET PF-STATUS 'S0100'.
 \* SET TITLEBAR 'xxx'.


  IF ref_grid1 IS INITIAL.
   "创建容器对象1。
   CREATE OBJECT ref_cust_container1
    EXPORTING
     container_name = 'CTR_1'.

   CREATE OBJECT ref_grid1 EXPORTING i_parent = ref_cust_container1.

   IF event_receiver IS INITIAL.
    CREATE OBJECT event_receiver.
   ENDIF.

   SET HANDLER event_receiver->handle_hotspot_click FOR ref_grid1.
   SET HANDLER event_receiver->handle_user_command FOR ref_grid1.
   SET HANDLER event_receiver->handle_toolbar FOR ref_grid1.
   SET HANDLER event_receiver->handle_data_changed FOR ref_grid1.
   SET HANDLER event_receiver->handle_data_changed_finished FOR ref_grid1.

   "显示ALV.
   gv_layout-grid_title = 'LFA1'.
   gs_variant-report = sy-repid.
   gs_variant-handle = '0100'.
   gv_layout-stylefname = 'CELL'.

   gv_alv_save = 'A'.

   PERFORM frm_call_alvgrid TABLES gt_lfa1
                   gt_fieldcat1
                   gt_exclude
               USING ref_grid1
                  gv_layout
                  gv_alv_save
                  gs_variant
                  '1'.

   CALL METHOD cl_gui_control=>set_focus  "设置焦点在go_grid 上
    EXPORTING
     control = ref_grid1.
  ELSE.
   CALL METHOD ref_grid1->refresh_table_display
 \*  EXPORTING
 \*   is_stable   =
 \*   i_soft_refresh =
    EXCEPTIONS
     finished = 1
     OTHERS  = 2.
   IF sy-subrc <> 0.
    MESSAGE s000 WITH '刷新ALV 出现了问题！'.
   ENDIF.
  ENDIF.


  IF ref_grid2 IS INITIAL.
   "创建容器对象2。
   CREATE OBJECT ref_cust_container2
    EXPORTING
     container_name = 'CTR_2'.
   CREATE OBJECT ref_grid2 EXPORTING i_parent = ref_cust_container2.
   gv_layout-grid_title = 'LFM1'.
   PERFORM frm_call_alvgrid TABLES gt_lfm1
                   gt_fieldcat2
                   gt_exclude
               USING ref_grid2
                  gv_layout
                  gv_alv_save
                  gs_variant
                  '2'.
  ELSE.
   CALL METHOD ref_grid2->refresh_table_display
 \*  EXPORTING
 \*   is_stable   =
 \*   i_soft_refresh =
    EXCEPTIONS
     finished = 1
     OTHERS  = 2.
   IF sy-subrc <> 0.
    MESSAGE s000 WITH '刷新ALV 出现了问题！'.
   ENDIF.
  ENDIF.

  IF ref_grid3 IS INITIAL.
   "创建容器对象3。
   CREATE OBJECT ref_cust_container3
    EXPORTING
     container_name = 'CTR_3'.
   CREATE OBJECT ref_grid3 EXPORTING i_parent = ref_cust_container3.

 

   gv_layout-grid_title = 'LFBK'.
   PERFORM frm_call_alvgrid TABLES gt_lfbk
                   gt_fieldcat3
                   gt_exclude
               USING ref_grid3
                  gv_layout
                  gv_alv_save
                  gs_variant
                  '3'.
  ELSE.
   CALL METHOD ref_grid3->refresh_table_display
 \*  EXPORTING
 \*   is_stable   =
 \*   i_soft_refresh =
    EXCEPTIONS
     finished = 1
     OTHERS  = 2.
   IF sy-subrc <> 0.
    MESSAGE s000 WITH '刷新ALV 出现了问题！'.
   ENDIF.
  ENDIF.

 


 ENDMODULE.
 *&---------------------------------------------------------------------*
 *&   Module USER_COMMAND_0100 INPUT
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 MODULE user_command_0100 INPUT.
  CASE sy-ucomm.
   WHEN '&F03' OR '&F15' OR '&F12'.
    CALL METHOD ref_cust_container1->free.
    CALL METHOD ref_cust_container2->free.
    CALL METHOD ref_cust_container3->free.
    SET SCREEN 0.
   WHEN 'TEST'.
    MESSAGE text-t01 TYPE 'S' DISPLAY LIKE 'E'.
  ENDCASE.
 ENDMODULE.
 *&---------------------------------------------------------------------*
 *&   Form FRM_CALL_ALVGRID5
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_call_alvgrid TABLES ut_outtab TYPE STANDARD TABLE
                ut_fieldcat TYPE STANDARD TABLE
                ut_exclude TYPE STANDARD TABLE
             USING ref_grid TYPE REF TO cl_gui_alv_grid
                u_layout TYPE lvc_s_layo
                u_alv_save TYPE c
                u_variant TYPE disvariant
 \*               ut_exclude TYPE ui_functions
                u_flag.
  "创建显示字段
  PERFORM frm_set_fieldcat TABLES ut_fieldcat USING u_flag.


  CALL METHOD ref_grid->register_edit_event
   EXPORTING
    i_event_id = cl_gui_alv_grid=>mc_evt_modified.

  IF u_flag EQ 1.
   DATA(lv_flg) = 1.
  ELSE.
   lv_flg = 0.
  ENDIF.

  CALL METHOD ref_grid->set_ready_for_input
   EXPORTING
    i_ready_for_input = lv_flg.

  "ALV的第一次显示。
  CALL METHOD ref_grid->set_table_for_first_display
   EXPORTING
    is_layout    = u_layout
    i_save     = u_alv_save
    is_variant   = u_variant
 \*   it_toolbar_excluding = ut_exclude
   CHANGING
    it_outtab    = ut_outtab[]
    it_fieldcatalog = ut_fieldcat[].

 ENDFORM.

 *&---------------------------------------------------------------------*
 *&   Form FRM_SET_FIELDCAT5
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_set_fieldcat TABLES ut_fieldcat TYPE STANDARD TABLE USING u_flag .
  DATA: l_nn TYPE i.
  "定义宏
  DEFINE add_fieldcat.
   L_NN = L_NN + 1.
   CLEAR WA_FIELDCAT.
   WA_FIELDCAT-FIELDNAME   = &1.
   WA_FIELDCAT-COLTEXT    = &2.
   WA_FIELDCAT-COL_POS    = L_NN.
   WA_FIELDCAT-KEY      = &3.
   WA_FIELDCAT-JUST     = &4.
   WA_FIELDCAT-OUTPUTLEN   = &5.
   WA_FIELDCAT-FIX_COLUMN  = &6.
   WA_FIELDCAT-EDIT     = &7.
   WA_FIELDCAT-ICON     = ''.
   WA_FIELDCAT-HOTSPOT    = &8.
   APPEND WA_FIELDCAT TO ut_fieldcat.
  END-OF-DEFINITION.

  CLEAR: ut_fieldcat[],ut_fieldcat.
  "赋值
  CASE u_flag .
   WHEN '1'.
    add_fieldcat  'LIFNR'  '供应商编码'  ''    '' '' '' ''  'X'.
    add_fieldcat  'NAME1'  '供应商名称'  ''    '' '' '' ''  ''.
    add_fieldcat  'TELF1'  '供应商电话'  ''    '' '' '' 'X'  ''.
    add_fieldcat  'TELFX' '供应商传真'  ''    '' '' '' ''  ''.
    add_fieldcat  'KTOKK'  '供应商账户组'  ''    '' '' ''  '' ''.
    add_fieldcat  'TXT30'  '供应商账户组描述'  ''    '' ''  '' '' ''.
    add_fieldcat  'ACTSS'  '供应商状态'  ''    '' '' '' ''  ''.
    add_fieldcat  'STRAS'  '供应商地址(街道门牌号)'  ''    '' '' '' ''  ''.
    add_fieldcat  'FITYP'  '供应商税类型'  ''    '' '' '' '' '' .
    add_fieldcat  'KRAUS'  '旧供应商号'  ''    '' '' '' '' ''.
   WHEN '2'.
    add_fieldcat  'LIFNR'    '供应商编码'  ''    '' '' '' '' ''.
    add_fieldcat  'KALSK'    '方案组'  ''    '' '' '' '' ''.
    add_fieldcat  'KALSB'    '方案组描述'  ''    '' '' '' '' ''.
    add_fieldcat  'MINBW'    '最小订货金额'  ''    '' '' '' '' ''.
   WHEN '3'.

    add_fieldcat  'LIFNR'    '供应商编码'  ''    '' '' '' '' ''.
    add_fieldcat  'KOINH'    '银行户主'  ''    '' '' '' '' ''.
    add_fieldcat  'BANKA'    '开户银行'  ''    '' '' '' '' ''.
    add_fieldcat  'BANKN'    '银行帐号1'  ''    '' '' '' '' ''.
    add_fieldcat  'BKREF'    '银行帐号2'  ''    '' '' '' '' ''.

  ENDCASE.
 ENDFORM.          " FRM_SET_FIELDCAT
 *&---------------------------------------------------------------------*
 *&   Form FRM_CREATE_TREE
 *&---------------------------------------------------------------------*
 \*    text
 *----------------------------------------------------------------------*
 \* --> p1    text
 \* <-- p2    text
 *----------------------------------------------------------------------*
 FORM frm_create_tree .
 *事件内表及单个事件对象.
  DATA: lt_event TYPE cntl_simple_events,
     ls_event TYPE cntl_simple_event.
 \* IF ref_tree IS INITIAL.
 \*  "创建容器对象1。
 \*  CREATE OBJECT ref_cust_container
 \*   EXPORTING
 \*    container_name = 'CTR_4'.
 *
 \*  CREATE OBJECT ref_tree
 \*   EXPORTING
 \*    parent = ref_cust_container.
 *
 \*  IF event_receiver_tree IS INITIAL.
 \*   CREATE OBJECT event_receiver_tree.
 \*  ENDIF.
 *
 **定义双击事件
 \*  ls_event-eventid = cl_gui_simple_tree=>eventid_node_double_click.
 \*  ls_event-appl_event = 'X'.
 \*  APPEND ls_event TO lt_event.
 **添加事件内表
 \*  CALL METHOD ref_tree->set_registered_gt_event
 \*   EXPORTING
 \*    gt_event = lt_event.
 *
 **注册双击事件
 \*  SET HANDLER lcl_event_receiver_tree->handle_node_double_click FOR ref_tree.
 *
 \*  CALL METHOD cl_gui_control=>set_focus  "设置焦点在go_grid 上
 \*   EXPORTING
 \*    control = ref_tree.
 \* ELSE.
 *
 \* ENDIF.
 ENDFORM.

## 8.z_test_gsw_g2

REPORT Z_TEST_GSW_G2.
 ***第1步，声明 alv 相关的变量
 type-POOLs:slis.
 data: lt_fieldcat TYPE slis_t_fieldcat_alv,
    ls_fieldcat TYPE slis_fieldcat_alv,
    ls_layout TYPE slis_layout_alv,

    lt_event TYPE slis_t_event,
    ls_event TYPE slis_alv_event .

 




 DATA:   lv_colpos type int2 .

 ***第2步，定义内表
 types: BEGIN OF ty_alvshow,
  vbeln TYPE vbak-vbeln,
  erdat TYPE vbak-erdat,
  ernam TYPE vbak-ernam,
  kunnr TYPE vbak-kunnr,

  posnr TYPE vbap-posnr,
  matnr TYPE vbap-matnr,
  matkl TYPE vbap-matkl,
  zmeng TYPE vbap-zmeng,
  zieme TYPE vbap-zieme,
  werks TYPE vbap-werks,
  lgort TYPE vbap-lgort,

  END OF ty_alvshow.
 data: lt_alvshow TYPE TABLE OF ty_alvshow,
    wa_alvshow TYPE ty_alvshow.


 **读取数据

 select
  a~vbeln
  a~erdat
  a~ernam
  kunnr

  posnr
  matnr
  matkl
  zmeng
  zieme
  werks
  lgort
 FROM
  vbak as a
  INNER JOIN vbap as p
  ON a~vbeln EQ p~vbeln
  into TABLE lt_alvshow
  UP TO 100 ROWS.


 ****alv 格式控制

 **layout

 ls_layout-zebra = 'X'.
 ls_layout-detail_popup = 'X'.
 ls_layout-detail_titlebar = '详细信息'.
 ls_layout-f2code = '&ETA'.
 ls_layout-colwidth_optimize = 'X'.


 ****fieldout


 "销售凭证
 lv_colpos = 1.

 ls_fieldcat-fieldname = 'VBELN'.
 ls_fieldcat-col_pos = lv_colpos.
 ls_fieldcat-key = 'X'.
 ls_fieldcat-datatype = 'CHAR'.
 ls_fieldcat-OUTPUTLEN = '20'.
 ls_fieldcat-SELTEXT_M = '销售凭证'.
 APPEND ls_fieldcat TO lt_fieldcat.


 "销售日期
 clear ls_fieldcat.
 lv_colpos = lv_colpos + 1.
 ls_fieldcat-fieldname = 'ERDAT'.
 ls_fieldcat-col_pos = lv_colpos.
 ls_fieldcat-datatype = 'DATS'.
 ls_fieldcat-OUTPUTLEN = '8'.
 ls_fieldcat-SELTEXT_M = '销售日期'.
 APPEND ls_fieldcat TO lt_fieldcat.

 "创建人
 clear ls_fieldcat.
 lv_colpos = lv_colpos + 1.
 ls_fieldcat-fieldname = 'ERNAM'.
 ls_fieldcat-col_pos = lv_colpos.
 ls_fieldcat-datatype = 'CHAR'.
 ls_fieldcat-OUTPUTLEN = '12'.
 ls_fieldcat-SELTEXT_M = '销售人'.
 APPEND ls_fieldcat TO lt_fieldcat.


 "售达方
 clear ls_fieldcat.
 lv_colpos = lv_colpos + 1.
 ls_fieldcat-fieldname = 'KUNNR'.
 ls_fieldcat-col_pos = lv_colpos.
 ls_fieldcat-datatype = 'CHAR'.
 ls_fieldcat-OUTPUTLEN = '10'.
 ls_fieldcat-SELTEXT_M = '售达方'.
 APPEND ls_fieldcat TO lt_fieldcat.

 

 "项目编码
 clear ls_fieldcat.
 lv_colpos = lv_colpos + 1.
 ls_fieldcat-fieldname = 'POSNR'.
 ls_fieldcat-col_pos = lv_colpos.
 ls_fieldcat-datatype = 'NUMC'.
 ls_fieldcat-OUTPUTLEN = '6'.
 ls_fieldcat-SELTEXT_M = '项目编码'.
 APPEND ls_fieldcat TO lt_fieldcat.

 

 "物料
 clear ls_fieldcat.
 lv_colpos = lv_colpos + 1.
 ls_fieldcat-fieldname = 'MATNR'.
 ls_fieldcat-col_pos = lv_colpos.
 ls_fieldcat-datatype = 'CHAR'.
 ls_fieldcat-OUTPUTLEN = '9'.
 ls_fieldcat-SELTEXT_M = '物料'.
 APPEND ls_fieldcat TO lt_fieldcat.

 "物料组
 clear ls_fieldcat.
 lv_colpos = lv_colpos + 1.
 ls_fieldcat-fieldname = 'MATKL'.
 ls_fieldcat-col_pos = lv_colpos.
 ls_fieldcat-datatype = 'CHAR'.
 ls_fieldcat-OUTPUTLEN = '9'.
 ls_fieldcat-SELTEXT_M = '物料组'.
 APPEND ls_fieldcat TO lt_fieldcat.


 "数量
 clear ls_fieldcat.
 lv_colpos = lv_colpos + 1.
 ls_fieldcat-fieldname = 'ZMENG'.
 ls_fieldcat-col_pos = lv_colpos.
 ls_fieldcat-datatype = 'QUAN'.
 ls_fieldcat-OUTPUTLEN = '13'.
 ls_fieldcat-SELTEXT_M = '数量'.
 APPEND ls_fieldcat TO lt_fieldcat.


 "单位
 clear ls_fieldcat.
 lv_colpos = lv_colpos + 1.
 ls_fieldcat-fieldname = 'ZIEME'.
 ls_fieldcat-col_pos = lv_colpos.
 ls_fieldcat-datatype = 'UNIT'.
 ls_fieldcat-OUTPUTLEN = '3'.
 ls_fieldcat-SELTEXT_M = '单位'.
 APPEND ls_fieldcat TO lt_fieldcat.

 "工厂
 clear ls_fieldcat.
 lv_colpos = lv_colpos + 1.
 ls_fieldcat-fieldname = 'WERKS'.
 ls_fieldcat-col_pos = lv_colpos.
 ls_fieldcat-ref_tabname = 'VBAP'.
 APPEND ls_fieldcat TO lt_fieldcat.

 "库存
 clear ls_fieldcat.
 lv_colpos = lv_colpos + 1.
 ls_fieldcat-fieldname = 'LGORT'.
 ls_fieldcat-col_pos = lv_colpos.
 ls_fieldcat-ref_tabname = 'VBAP'.
 APPEND ls_fieldcat TO lt_fieldcat.
 clear ls_fieldcat.


 **第5步,定义事件
 ls_event-name = 'USER_COMMAND'. "用户相应事件
 ls_event-form = 'FORM_USER_COMMAND'.
 APPEND ls_event to lt_event.
 ls_event-name = 'TOP_OF_PAGE'. "显示标题
 ls_event-form = 'FORM_TOP_OF_PAGE'.
 APPEND ls_event to lt_event.

 ls_event-name = 'PF_STATUS_SET'. "设置 GUI 状态栏
 ls_event-form = 'FORM_PF_STATUS_SET'.
 APPEND ls_event to lt_event.

 


 **第6步，显示alv
 CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
 EXPORTING
 \*  I_INTERFACE_CHECK         = ' '
 \*  I_BYPASSING_BUFFER        = ' '
 \*  I_BUFFER_ACTIVE          = ' '
 \*  I_CALLBACK_PROGRAM        = ' '
 \*  I_CALLBACK_PF_STATUS_SET     = ' '
 \*  I_CALLBACK_USER_COMMAND      = ' '
 \*  I_CALLBACK_TOP_OF_PAGE      = ' '
 \*  I_CALLBACK_HTML_TOP_OF_PAGE    = ' '
 \*  I_CALLBACK_HTML_END_OF_LIST    = ' '
 \*  I_STRUCTURE_NAME         =
 \*  I_BACKGROUND_ID          = ' '
 \*  I_GRID_TITLE           =
 \*  I_GRID_SETTINGS          =
  IS_LAYOUT             = ls_layout
  IT_FIELDCAT            = lt_fieldcat
 \*  IT_EXCLUDING           =
 \*  IT_SPECIAL_GROUPS         =
 \*  IT_SORT              =
 \*  IT_FILTER             =
 \*  IS_SEL_HIDE            =
 \*  I_DEFAULT             = 'X'
 \*  I_SAVE              = ' '
 \*  IS_VARIANT            =
 \*  IT_EVENTS             =
 \*  IT_EVENT_EXIT           =
 \*  IS_PRINT             =
 \*  IS_REPREP_ID           =
 \*  I_SCREEN_START_COLUMN       = 0
 \*  I_SCREEN_START_LINE        = 0
 \*  I_SCREEN_END_COLUMN        = 0
 \*  I_SCREEN_END_LINE         = 0
 \*  I_HTML_HEIGHT_TOP         = 0
 \*  I_HTML_HEIGHT_END         = 0
 \*  IT_ALV_GRAPHICS          =
 \*  IT_HYPERLINK           =
 \*  IT_ADD_FIELDCAT          =
 \*  IT_EXCEPT_QINFO          =
 \*  IR_SALV_FULLSCREEN_ADAPTER    =
 \* IMPORTING
 \*  E_EXIT_CAUSED_BY_CALLER      =
 \*  ES_EXIT_CAUSED_BY_USER      =
  TABLES
   T_OUTTAB             = lt_alvshow
 \* EXCEPTIONS
 \*  PROGRAM_ERROR           = 1
 \*  OTHERS              = 2
      .
 IF SY-SUBRC <> 0.
 \* Implement suitable error handling here
 ENDIF.

 

**************
 form FORM_USER_COMMAND USING R_UCOMM LIKE SY-UCOMM
    RE_SELFIELD TYPE SLIS_SELFIELD.

 

 ENDFORM.

 form FORM_TOP_OF_PAGE .

 ENDFORM. .

 

 form FORM_PF_STATUS_SET .

 ENDFORM.

## 9.z_test_01.

*可执行程序
 REPORT z_test_01.

 *定义内表
 *1.自定义类型内表
 *结构类型
 TYPES: BEGIN OF ty_itab1,
     field1 TYPE char10,
     field2 TYPE int2,
    END OF ty_itab1.
 *表类型
 TYPES:t_itab1 TYPE ty_itab1 OCCURS 0.
 TYPES:t_itab2 TYPE TABLE OF ty_itab1.

 DATA:itab1 TYPE TABLE OF ty_itab1, "变量实例
   itab2 TYPE t_itab1,
   itab3 TYPE t_itab2.


 *2.直接定义
 DATA:BEGIN OF itab5 OCCURS 0,"定义了内表，同时定义了（同名工作区）结构体变量
    field1 TYPE char10,
    field2 TYPE int2,
   END OF itab5.

 *3.参考定义
 DATA itab4 LIKE itab1. "参考已经定义的内表
 DATA itab6 TYPE TABLE OF zacta. "参考数据库透明表
 DATA itab7 TYPE TABLE OF ccm_header. "参考结构

 "定义工作区"
 DATA:ls_itab1 TYPE ty_itab1.
 DATA:ls_itab2 TYPE ty_itab1.

 ls_itab1-field1 = '00001'.
 ls_itab1-field2 = 00001.
 INSERT ls_itab1 INTO itab1 INDEX 1.
 APPEND ls_itab1 TO itab1.
 COLLECT ls_itab1 INTO itab1.

 APPEND LINES OF itab1 TO itab2.

 *读取
 CLEAR ls_itab2."清空工作区
 *read table itab2 into ls_itab2 with key field1 = '00001'.
 LOOP AT itab2 INTO ls_itab2.
  WRITE: / ls_itab2-field1.
 ENDLOOP.
 BREAK-POINT.

## 10.备份 ZMDI021

```XML

*&---------------------------------------------------------------------*
*& Report  ZZTXF01
*&
*&---------------------------------------------------------------------*
*&
*&
*&---------------------------------------------------------------------*
REPORT ZMDI021.
TABLES : T001.


TYPES: BEGIN OF GTY_DATA,
  "CHECK(1) TYPE C ,         "选择框
  BUKRS(4)  TYPE C ,        "公司代码
  BUTXT(60) TYPE C ,        "公司名称

  END OF GTY_DATA.





DATA: GS_DATA TYPE GTY_DATA,
      GT_DATA TYPE TABLE OF GTY_DATA.



SELECTION-SCREEN :BEGIN OF BLOCK BK1 WITH FRAME TITLE TEXT-001.
  SELECT-OPTIONS:  P_BUKRS FOR  T001-BUKRS.
SELECTION-SCREEN END OF BLOCK BK1.

SELECTION-SCREEN BEGIN OF BLOCK BK2 WITH FRAME TITLE text-002.
PARAMETERS: p_front RADIOBUTTON GROUP rg1 DEFAULT 'X',
            p_bt RADIOBUTTON GROUP rg1.
SELECTION-SCREEN END OF BLOCK BK2.
SELECTION-SCREEN BEGIN OF BLOCK BK3 WITH FRAME TITLE text-003.


PARAMETERS:  p_size TYPE i DEFAULT 5000. "分包大小

SELECTION-SCREEN END OF BLOCK BK3.

START-OF-SELECTION.
  PERFORM GET_DATA.

*&---------------------------------------------------------------------*
*&      Form  GET_DATA
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*  -->  p1        text
*  <--  p2        text
*----------------------------------------------------------------------*
FORM GET_DATA .

  SELECT BUKRS BUTXT
  INTO CORRESPONDING FIELDS OF TABLE GT_DATA
  FROM T001
  WHERE BUKRS IN P_BUKRS.


  IF GT_DATA IS INITIAL.
    MESSAGE '没有符合条件的数据' TYPE 'S' DISPLAY LIKE 'E'.
  ELSE.
    IF p_front IS NOT INITIAL.
      PERFORM frm_display.
    ELSE.
      PERFORM frm_transport .
    ENDIF.
  ENDIF.


ENDFORM.                    " GET_DATA
*&---------------------------------------------------------------------*
*&      Form  DISPLAY_DATA
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*  -->  p1        text
*  <--  p2        text
*----------------------------------------------------------------------*
FORM frm_display .

   DATA: lt_fieldcat       TYPE lvc_t_fcat,
         ls_fieldcat       TYPE lvc_s_fcat,
         ls_layo_lvc       TYPE lvc_s_layo.


  DEFINE %%add_field.
    ls_fieldcat-fieldname = &1.
    ls_fieldcat-scrtext_m = &2.
    ls_fieldcat-edit      = &3.
    ls_fieldcat-outputlen = &4.
    ls_fieldcat-key       = &5.
    ls_fieldcat-checkbox  = &6.
    APPEND  ls_fieldcat TO  lt_fieldcat.
  END-OF-DEFINITION.

   %%add_field:
  "'CHECK'     '选择框'    'X'    '15' 'X' 'X',
  'BUKRS'     '公司代码'     ''   '15' '' '',
  'BUTXT'     '公司名称'     ''   '15' '' ''.


  ls_layo_lvc-zebra        = 'X' .
  ls_layo_lvc-cwidth_opt   = 'X' .    "列宽度自动根据内容优化
  ls_layo_lvc-detailinit   = 'X' .
  ls_layo_lvc-no_toolbar   = 'X'.

 CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'
  EXPORTING
    I_CALLBACK_PROGRAM                =  sy-repid
    I_CALLBACK_PF_STATUS_SET          = 'FRM_PF_STATUS'
    I_CALLBACK_USER_COMMAND           = 'FRM_USER_COMMAND'
    IS_LAYOUT_LVC                     = ls_layo_lvc
    IT_FIELDCAT_LVC                   = lt_fieldcat
   TABLES
     T_OUTTAB                          = gt_data
  EXCEPTIONS
    PROGRAM_ERROR                     = 1
    OTHERS                            = 2
           .
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
            WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.


ENDFORM.                    " DISPLAY_DATA

FORM frm_pf_status USING  extab TYPE slis_t_extab.
  CLEAR: extab,extab[].
  SET PF-STATUS 'S1000' EXCLUDING extab.
ENDFORM.


FORM frm_user_command USING ucomm LIKE sy-ucomm
                            selfield TYPE slis_selfield.

  IF ucomm = 'SEND'.


            PERFORM frm_transport .



  ENDIF.

  selfield-refresh = 'X'.
  selfield-col_stable = 'X'.
  selfield-row_stable = 'X'.
ENDFORM.              " FRM_DISPLAY              " FRM_DISPLAY


*&---------------------------------------------------------------------*
*&      Form  FRM_TRANSPORT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*  -->  p1        text
*  <--  p2        text
*----------------------------------------------------------------------*
FORM FRM_TRANSPORT .

  DATA: lo_proxy        TYPE REF TO zco_si_company_data_mastdata_o,
        lo_system_fault TYPE REF TO cx_ai_system_fault,
        ls_output       TYPE zmt_company_data_mastdata,
        lw_header       TYPE zdt_company_data_mastdata_head.
  DATA: lv_message TYPE string,
        l_count    TYPE i,
        l_packno   TYPE i,
        s_line     TYPE i,
        e_line     TYPE i,
        l_ddate    TYPE char14.
  DATA: lt_out     TYPE TABLE OF gty_data,
        lt_roid    TYPE lvc_t_roid,
        lw_roid    TYPE lvc_s_roid,
        lr_alv     TYPE REF TO cl_gui_alv_grid.

  CLEAR: lt_out.

  IF p_front EQ 'X'.
    "实时更新数据
    CALL FUNCTION 'GET_GLOBALS_FROM_SLVC_FULLSCR'
      IMPORTING
        e_grid = lr_alv.
    IF lr_alv IS BOUND. "判断是否建立了一个对象 等同于 IF lr_alv  IS NOT INITIAL.
      CALL METHOD lr_alv->check_changed_data.
      lr_alv->get_selected_rows( IMPORTING et_row_no = lt_roid ).
    ENDIF.
    LOOP AT lt_roid INTO lw_roid.
      READ TABLE GT_DATA INTO GS_DATA INDEX lw_roid-row_id.
      IF sy-subrc = 0.
        APPEND GS_DATA TO lt_out.
      ENDIF.
      CLEAR: lw_roid.
    ENDLOOP.
    CLEAR: lt_roid.
  ELSE.
    lt_out[] = GT_DATA[].
  ENDIF.

  CLEAR :s_line ,e_line ,l_packno.
  IF lt_out[] IS  NOT INITIAL.
    l_ddate = sy-datum && sy-uzeit.

  LOOP AT lt_out INTO DATA(lw_out).
      ADD 1 TO l_count.

      MOVE-CORRESPONDING lw_out TO lw_header.
      APPEND lw_header TO ls_output-mt_company_data_mastdata-header.

"处理分包大小
      IF l_count EQ p_size.
        ls_output-mt_company_data_mastdata-message_header-receiver = 'dmall'.
        ls_output-mt_company_data_mastdata-message_header-data_count = l_count .
        TRY.
            CREATE OBJECT lo_proxy.
            CALL METHOD lo_proxy->SI_COMPANY_DATA_MASTDATA_OUT_A
              EXPORTING
                output = ls_output.
            s_line = s_line + l_count.

            COMMIT WORK AND WAIT.
          CATCH cx_ai_system_fault INTO lo_system_fault.
            CONCATENATE lo_system_fault->errortext  '!' lo_system_fault->code INTO lv_message.
            MESSAGE s888(sabapdocu) WITH lv_message DISPLAY LIKE 'E'.
            e_line = e_line + l_count.
          CATCH cx_root.
            lv_message = '未知原因的下发失败'.
            MESSAGE s888(sabapdocu) WITH lv_message DISPLAY LIKE 'E'.
            e_line = e_line + l_count.
        ENDTRY.

        ADD 1 TO l_packno.
        l_count = 0.
        CLEAR: ls_output.
        CLEAR:lv_message.
      ENDIF.
      CLEAR: lw_header.


    ENDLOOP.

    IF l_count > 0.
        ls_output-mt_company_data_mastdata-message_header-receiver = 'dmall'.
        ls_output-mt_company_data_mastdata-message_header-data_count = l_count .
      TRY.
          CREATE OBJECT lo_proxy.
          CALL METHOD lo_proxy->SI_COMPANY_DATA_MASTDATA_OUT_A
            EXPORTING
              output = ls_output.
          s_line = s_line + l_count.

          COMMIT WORK AND WAIT.
        CATCH cx_ai_system_fault INTO lo_system_fault.

          CONCATENATE lo_system_fault->errortext  '!' lo_system_fault->code INTO lv_message.
          MESSAGE s888(sabapdocu) WITH lv_message DISPLAY LIKE 'E'.
          e_line = e_line + l_count.
        CATCH cx_root.

          lv_message = '未知原因的下发失败'.
          MESSAGE s888(sabapdocu) WITH lv_message DISPLAY LIKE 'E'.
          e_line = e_line + l_count.
      ENDTRY.

      ADD 1 TO l_packno.
      l_count = 0.
      CLEAR: ls_output.
      CLEAR:lv_message.
    ENDIF.

    MESSAGE s888(sabapdocu) WITH '发送了' && l_packno && '个数据包,' && '发送成功了' && s_line && '条，' && '失败了' && e_line && '条'.
    CLEAR: l_count,l_packno.

  ELSE.
    MESSAGE s888(sabapdocu) WITH '你还尚未选中，没有可发送数据'.
  ENDIF.



ENDFORM.                    " FRM_TRANSPORT
```

## 11.ZMDI018

```XML
*&---------------------------------------------------------------------*
*& Report  ZMDI018
*&
*&---------------------------------------------------------------------*
*
*  作者：    GSW
*  完成日期 : 2020.7.23
*  描述：供应商下发
************************************************************************
*  版本号 日期   作者   修改描述 功能更改说明书
************************************************************************
*  1.0  2020/07/23 供应商   程序创建
*&
*&---------------------------------------------------------------------*

REPORT zmdi018.

TABLES:lfa1,lfm1.

TYPES: BEGIN OF ty_out,
         lifnr  TYPE lfa1-lifnr,  "供应商
         name1  type lfa1-name1,  "名称
         zzjyfs type lfa1-zzjyfs, "经营方式
         ktokk  type lfa1-ktokk,  "供商类型
         fityp  type lfa1-fityp,  "税类型
         ekgrp  type lfm1-ekgrp,  "经营范围
         stceg  type lfa1-stceg,  "税号
         actss  type lfa1-actss,  "状态
         stras  type lfa1-stras,  "住宅号及街道
         stelf1 type lfa1-telf1,  "供应商电话
         stelf2 type lfa1-telf2,  "供应商手机
         stelfx type lfa1-telfx,  "传真号
         pname1 type knvk-name1,  "联系人
         ptelf1 type knvk-telf1,  "联系人电话
         minbw  type lfm1-minbw,  "最小订单值
         plifz  type lfm1-plifz,  "默认订货日期
         kalsk  type lfm1-kalsk,  "允许手工改价
         zzdqr(1)  type C ,"允许自动确认订单
         kraus  type lfa1-kraus,   "富基供应商号
         sortl  type lfa1-sortl,  "排序字段
         pstlz  type lfa1-pstlz,"邮政编码
         regss  type lfa1-regss,"已注册的社会保险
         lstras type lfa1-stras,"供应商地址
         telf1  type lfa1-telf1,"供应商电话
         telf2  type lfa1-telf2,"供应商手机
         telfx  type lfa1-telfx,"供应商传真
         zterm  type lfm1-zterm,"付款条件
         zzcbtz type lfa1-zzcbtz,"成本调整
         zzqylb type lfa1-zzqylb,"企业类别
         zzyyzz type lfa1-zzyyzz,"营业执照
         zzyjrq type lfa1-zzyjrq,"引进日期
         zzjswz type lfa1-zzjswz,"结算位置
         zzjypp type lfa1-zzjypp,"经营品牌
         zzjyfw type lfa1-zzjyfw,"经营范围

         noid          type char3,"序号
         issued_time   type char16,"业务系统更新时间
         creation_date type char16,""创建时间
         lfa1_key      type cdhdr-objectid,
         nochange(1)   type c,
       END OF ty_out,

       BEGIN OF ty_lfa1,
         lifnr  TYPE lfa1-lifnr,  "供应商
         name1  TYPE lfa1-name1,  "名称
         zzjyfs TYPE lfa1-zzjyfs, "经营方式
         ktokk  TYPE lfa1-ktokk,  "供商类型
         fityp  TYPE lfa1-fityp,  "税类型
         stceg  type lfa1-stceg,  "税号
         actss  type lfa1-actss,  "状态
         stras  type lfa1-stras,  "住宅号及街道
         stelf1 type lfa1-telf1,  "供应商电话
         stelf2 type lfa1-telf2,  "供应商手机
         stelfx type lfa1-telfx,  "传真号
         kraus  type lfa1-KRAUS,   "富基供应商号
         SORTL  type lfa1-SORTL,  "排序字段
         pstlz  type lfa1-pstlz,"邮政编码
         regss  type lfa1-regss,"已注册的社会保险
         lstras type lfa1-stras,"供应商地址
         telf1  type lfa1-telf1,"供应商电话
         telf2  type lfa1-telf2,"供应商手机
         telfx  type lfa1-telfx,"供应商传真
         zzcbtz type lfa1-zzcbtz,"成本调整
         zzqylb type lfa1-zzqylb,"企业类别
         zzyyzz type lfa1-zzyyzz,"营业执照
         zzyjrq type lfa1-zzyjrq,"引进日期
         zzjswz type lfa1-zzjswz,"结算位置
         zzjypp type lfa1-zzjypp,"经营品牌
         zzjyfw type lfa1-zzjyfw,"经营范围
         lfa1_key type cdhdr-objectid,
         nochange(1)   type c,
       END OF ty_lfa1,

       BEGIN OF ty_lfm1,
         ekgrp  type lfm1-ekgrp,
         minbw  type lfm1-minbw,
         plifz  type lfm1-plifz,
         kalsk  type lfm1-kalsk,
         zterm  type lfm1-zterm,
          END OF ty_lfm1,


       BEGIN OF ty_knvk,
         parnr TYPE knvk-parnr,
         name1 TYPE knvk-name1,  "联系人名称
         telf1 TYPE knvk-telf1,  "联系电话
         lifnr TYPE knvk-lifnr,
       END OF ty_knvk,

       BEGIN OF ty_cdhdr,
         objectclas TYPE cdhdr-objectclas, "对象类
         objectid   TYPE cdhdr-objectid,   "对象值
         changenr   TYPE cdhdr-changenr,   "文档更换编号
       END OF ty_cdhdr.

DATA:gt_out TYPE TABLE OF ty_out.




*----------------------------------------------------------------------*
* Selection Screen
*----------------------------------------------------------------------*
SELECTION-SCREEN BEGIN OF BLOCK b1 WITH FRAME TITLE text-b01.
PARAMETERS: p_front RADIOBUTTON GROUP rg1 DEFAULT 'X' USER-COMMAND uc01,
            p_back  RADIOBUTTON GROUP rg1.
SELECTION-SCREEN END OF BLOCK b1.

SELECTION-SCREEN BEGIN OF BLOCK b2 WITH FRAME TITLE text-b02.
PARAMETERS: p_udate TYPE cdhdr-udate.
SELECTION-SCREEN SKIP 1.
SELECT-OPTIONS : s_lifnr FOR lfa1-lifnr.
SELECT-OPTIONS : s_ktokk FOR lfa1-ktokk.
SELECTION-SCREEN END OF BLOCK b2.

SELECTION-SCREEN BEGIN OF BLOCK b3 WITH FRAME TITLE text-b03.
PARAMETERS: p_size TYPE int4 OBLIGATORY DEFAULT 10000.
SELECTION-SCREEN END OF BLOCK b3.

START-OF-SELECTION.
  PERFORM frm_main.

END-OF-SELECTION.
*&---------------------------------------------------------------------*
*&  包含                ZMDI018FRM
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*&      Form  FRM_MAIN
*&---------------------------------------------------------------------*
FORM frm_main .
  DATA: lt_lfa1  TYPE STANDARD TABLE OF ty_lfa1,
        lt_lfm1  TYPE STANDARD TABLE OF ty_lfm1,
        lt_knvk  TYPE STANDARD TABLE OF ty_knvk,
        lt_cdhdr TYPE STANDARD TABLE OF ty_cdhdr,
        lw_out   TYPE ty_out.
  DATA: l_udate TYPE sy-datum.

"取数至lt_lfa1
SELECT
         lifnr
         name1
         zzjyfs
         ktokk
         fityp
         stceg
         actss
         stras
         telf1
         telf2
         telfx
         kraus
         sortl
         pstlz
         regss
         stras
         telf1
         telf2
         telfx
         zzcbtz
         zzqylb
         zzyyzz
         zzyjrq
         zzjswz
         zzjypp
         zzjyfw
    INTO  TABLE lt_lfa1
    FROM lfa1
    WHERE lifnr IN s_lifnr
      AND ktokk IN s_ktokk.

IF sy-subrc = 0.

  SELECT  lifnr ekgrp  minbw plifz  kalsk  zterm
    INTO CORRESPONDING FIELDS OF TABLE lt_lfm1
    FROM lfm1
    FOR ALL ENTRIES IN lt_lfa1
    WHERE    lifnr = lt_lfa1-lifnr  .

   IF sy-subrc = 0.
    "取数至lt_knvk
    SELECT parnr name1 telf1 lifnr
      FROM knvk
      INTO TABLE lt_knvk
      FOR ALL ENTRIES IN lt_lfa1
      WHERE lifnr = lt_lfa1-lifnr.

    SORT lt_knvk BY lifnr parnr.
    "去重
    DELETE ADJACENT DUPLICATES FROM lt_knvk COMPARING lifnr.

    ENDIF.


    IF p_udate IS NOT INITIAL.
      l_udate = p_udate - 1.

      LOOP AT lt_lfa1 ASSIGNING FIELD-SYMBOL(<fs_lfa1>).
        <fs_lfa1>-lfa1_key  = <fs_lfa1>-lifnr.
*        CONCATENATE 'BP' <fs_lfa1>-adrnr INTO <fs_lfa1>-adrc_key RESPECTING BLANKS.
      ENDLOOP.


      IF lt_lfa1[] IS NOT INITIAL.
        SELECT objectclas objectid changenr
          INTO TABLE lt_cdhdr
          FROM cdhdr
          FOR ALL ENTRIES IN lt_lfa1
          WHERE objectid = lt_lfa1-lfa1_key
            AND objectclas = 'KRED'
            AND ( ( udate = l_udate AND utime >= '223000' ) OR ( udate = p_udate AND utime <= '223000' ) ).
      ENDIF.

      SORT: lt_cdhdr BY objectclas objectid.
      LOOP AT lt_lfa1 ASSIGNING <fs_lfa1>.
        READ TABLE lt_cdhdr TRANSPORTING NO FIELDS WITH KEY objectclas = 'KRED' objectid = <fs_lfa1>-lfa1_key BINARY SEARCH.
        IF sy-subrc <> 0.
          <fs_lfa1>-nochange = 'X'.
        ENDIF.
      ENDLOOP.


      CLEAR: lt_cdhdr.
    ENDIF.

    SORT: lt_lfa1 BY lifnr,

          lt_knvk BY lifnr parnr.

    LOOP AT lt_lfa1 INTO DATA(lw_lfa1).

      READ TABLE lt_knvk INTO DATA(lw_knvk) WITH KEY lifnr = lw_lfa1-lifnr BINARY SEARCH.

      MOVE-CORRESPONDING lw_lfa1 TO lw_out.
      lw_out-telf1 = lw_knvk-telf1.
      lw_out-name1 = lw_knvk-name1.
      " 仅当LFA1有修改时才下发
      IF  lw_lfa1-nochange IS INITIAL.
        APPEND lw_out TO gt_out.
      ENDIF.

      CLEAR: lw_lfa1,lw_knvk,lw_out.
    ENDLOOP.

    IF p_front IS NOT INITIAL.

      PERFORM frm_display.
    ELSE.
      PERFORM frm_transport .
    ENDIF.
  ELSE.
    MESSAGE '没有符合条件的数据' TYPE 'S' DISPLAY LIKE 'E'.
  ENDIF.



ENDFORM.                    " FRM_MAIN
*&---------------------------------------------------------------------*
*&      Form  FRM_DISPLAY
*&---------------------------------------------------------------------*
FORM frm_display .
  DATA: lw_fieldcat   TYPE lvc_s_fcat,
        lt_fieldcat   TYPE lvc_t_fcat,
        ls_layout_lvc TYPE lvc_s_layo.

  DEFINE add_field.
    lw_fieldcat-fieldname = &1.
    lw_fieldcat-scrtext_s = lw_fieldcat-scrtext_m = lw_fieldcat-reptext = lw_fieldcat-scrtext_l = &2.
    lw_fieldcat-edit      = &3.
    lw_fieldcat-no_zero   = &4.
    lw_fieldcat-outputlen = &5.
    IF &6 IS NOT INITIAL.
      lw_fieldcat-ref_table = &6.
      IF &7 IS INITIAL.
        lw_fieldcat-ref_field = &1.
      ELSE.
        lw_fieldcat-ref_field = &7.
      ENDIF.
    ENDIF.
    APPEND lw_fieldcat TO lt_fieldcat.
    CLEAR: lw_fieldcat.
  END-OF-DEFINITION.

  add_field: 'LIFNR'        '供商编码'           '' ''   '10' 'LFA1' '' ,
             'NAME1'        '供商名称'           '' ''   '35' 'LFA1' '' ,
             'KTOKK'        '供商类型'           '' ''   '04' 'LFA1' '' ,
             'FITYP'        '税类型'             '' ''   '02' 'LFA1' '' ,
             'STCEG'        '纳税识别号'         '' ''   '20' 'LFA1' '' ,
             'ACTSS'        '供应商状态'         '' ''   '03' 'LFA1' '' ,
             'ZZJYFS'       '经营方式'           '' ''   '01' 'LFA1' '' ,
             'STRAS'        '供应商地址'         '' ''   '35' 'LFA1' '' ,
             'STELF1'       '供应商办公电话'     '' ''   '16' 'LFA1' '' ,
             'STELF2'       '供应商手机'         '' ''   '16' 'LFA1' '' ,
             'STELFX'       '供应商传真'         '' ''   '31' 'LFA1' '' ,
             'EKGRP'        '经营范围'           '' ''   '03' 'LFM1' '' ,
             'MINBW'        '最小起订金额'       '' ''   '20' 'LFM1' '' ,
             'PLIFZ'        '默认订货日期'       '' ''   '03' 'LFM1' '' ,
             'KALSK'        '允许手工改价'       '' ''   '04' 'LFM1' '' ,
             'ZTERM'        '付款条件'           '' ''   '04' 'LFM1' '' ,
             'LNAME'        '联系人'             '' ''   '35' 'LFA1' '' ,
             'TELF1'        '联系人电话'         '' ''   '16' 'LFA1' '' .

  ls_layout_lvc-zebra        = 'X' .
  ls_layout_lvc-cwidth_opt   = 'X' .    "列宽度自动根据内容优化
  ls_layout_lvc-sel_mode     = 'D'.
  ls_layout_lvc-no_toolbar   = 'X'.

  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'
    EXPORTING
      i_callback_program       = sy-repid
      i_callback_pf_status_set = 'FRM_PF_STATUS'
      i_callback_user_command  = 'FRM_USER_COMMAND'
      is_layout_lvc            = ls_layout_lvc
      it_fieldcat_lvc          = lt_fieldcat
    TABLES
      t_outtab                 = gt_out
    EXCEPTIONS
      program_error            = 1
      OTHERS                   = 2.
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE 'S' NUMBER sy-msgno
          WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4 DISPLAY LIKE 'E'.
  ENDIF.
ENDFORM.                    " FRM_DISPLAY
*&---------------------------------------------------------------------*
*&      Form  FRM_TRANSPORT
*&---------------------------------------------------------------------*
FORM frm_pf_status USING  extab TYPE slis_t_extab.

  SET PF-STATUS 'S1000' EXCLUDING extab.
ENDFORM.
*&---------------------------------------------------------------------*
*&      Form  FRM_USER_COMMAND
*&---------------------------------------------------------------------*
FORM frm_user_command USING ucomm LIKE sy-ucomm
                            selfield TYPE slis_selfield.


  CASE ucomm.
    WHEN 'send'.
       PERFORM frm_transport .
    WHEN '&F12' OR '&F15'.
       LEAVE PROGRAM.
    WHEN '&F03'.
       LEAVE TO SCREEN 0.

  ENDCASE.



  selfield-refresh = 'X'.
  selfield-col_stable = 'X'.
  selfield-row_stable = 'X'.
ENDFORM.              " FRM_DISPLAY              " FRM_DISPLAY
*&---------------------------------------------------------------------*
*&      Form  FRM_TRANSPORT
*&---------------------------------------------------------------------*
FORM frm_transport .

  DATA:
        lo_proxy        TYPE REF TO zco_si_vender_info_zzt_out_asy,
        lo_system_fault TYPE REF TO cx_ai_system_fault,
        ls_output       TYPE zmt_vender_info_zzt ,
        lw_header       TYPE zdt_vender_info_zzt_header .


  DATA: lv_message TYPE string,
        l_count    TYPE i,
        l_packno   TYPE i,
        s_line     TYPE i,
        e_line     TYPE i,
        l_ddate    TYPE char14.
  DATA: lt_out     TYPE TABLE OF ty_out,
        lt_roid    TYPE lvc_t_roid,
        lw_roid    TYPE lvc_s_roid,
        lr_alv     TYPE REF TO cl_gui_alv_grid.

  CLEAR: lt_out.

  IF p_front EQ 'X'.
    "实时更新数据
    CALL FUNCTION 'GET_GLOBALS_FROM_SLVC_FULLSCR'
      IMPORTING
        e_grid = lr_alv.
    IF lr_alv IS BOUND. "判断是否建立了一个对象 等同于 IF lr_alv  IS NOT INITIAL.
      CALL METHOD lr_alv->check_changed_data.

      lr_alv->get_selected_rows( IMPORTING et_row_no = lt_roid ).
    ENDIF.
    LOOP AT lt_roid INTO lw_roid.
      READ TABLE gt_out INTO DATA(lw_out) INDEX lw_roid-row_id.
      IF sy-subrc = 0.
        APPEND lw_out TO lt_out.
      ENDIF.
      CLEAR: lw_roid.
    ENDLOOP.
    CLEAR: lt_roid.
  ELSE.
    lt_out[] = gt_out[].
  ENDIF.

  CLEAR :s_line ,e_line ,l_packno.
  IF lt_out[] IS  NOT INITIAL.
    l_ddate = sy-datum && sy-uzeit.

    LOOP AT lt_out INTO lw_out.
      ADD 1 TO l_count.
      CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT' "去掉前导零
        EXPORTING
          input  = lw_out-lifnr
        IMPORTING
          output = lw_out-lifnr.
      MOVE-CORRESPONDING lw_out TO lw_header.
      APPEND lw_header TO ls_output-mt_vender_info_zzt-header.


"处理分包大小
      IF l_count EQ p_size.
        ls_output-mt_vender_info_zzt-message_header-receiver = 'dmall'.
        ls_output-mt_vender_info_zzt-message_header-data_count = l_count .
        TRY.
            CREATE OBJECT lo_proxy.
            CALL METHOD lo_proxy->si_vender_info_zzt_out_asyn
              EXPORTING
                output = ls_output.
            s_line = s_line + l_count.

            COMMIT WORK AND WAIT.
          CATCH cx_ai_system_fault INTO lo_system_fault.
            CONCATENATE lo_system_fault->errortext  '!' lo_system_fault->code INTO lv_message.
            MESSAGE s888(sabapdocu) WITH lv_message DISPLAY LIKE 'E'.
            e_line = e_line + l_count.
          CATCH cx_root.
            lv_message = '未知原因的下发失败'.
            MESSAGE s888(sabapdocu) WITH lv_message DISPLAY LIKE 'E'.
            e_line = e_line + l_count.
        ENDTRY.

        ADD 1 TO l_packno.
        l_count = 0.
        CLEAR: ls_output.
        CLEAR:lv_message.
      ENDIF.
      CLEAR: lw_header.


    ENDLOOP.

    IF l_count > 0.
        ls_output-mt_vender_info_zzt-message_header-receiver = 'dmall'.
        ls_output-mt_vender_info_zzt-message_header-data_count = l_count .
      TRY.
          CREATE OBJECT lo_proxy.
          CALL METHOD lo_proxy->SI_VENDER_INFO_ZZT_OUT_ASYN
            EXPORTING
              output = ls_output.
          s_line = s_line + l_count.

          COMMIT WORK AND WAIT.
        CATCH cx_ai_system_fault INTO lo_system_fault.

          CONCATENATE lo_system_fault->errortext  '!' lo_system_fault->code INTO lv_message.
          MESSAGE s888(sabapdocu) WITH lv_message DISPLAY LIKE 'E'.
          e_line = e_line + l_count.
        CATCH cx_root.

          lv_message = '未知原因的下发失败'.
          MESSAGE s888(sabapdocu) WITH lv_message DISPLAY LIKE 'E'.
          e_line = e_line + l_count.
      ENDTRY.

      ADD 1 TO l_packno.
      l_count = 0.
      CLEAR: ls_output.
      CLEAR:lv_message.
    ENDIF.

    MESSAGE s888(sabapdocu) WITH '发送了' && l_packno && '个数据包,' && '发送成功了' && s_line && '条，' && '失败了' && e_line && '条'.
    CLEAR: l_count,l_packno.

  ELSE.
    MESSAGE s888(sabapdocu) WITH '没有可发送数据'.
  ENDIF.
ENDFORM.                    " FRM_TRANSPORT

```

