# Bilibili—— ABAP课堂笔记

## 命名规则   

```te
MM:  【Material management】   物料

SD:  【Sales and Distribution】销售、分销

FICO:财务，成本

HCM：人力资本管理
```





```xml

c. 全局变量 GXX_XXX
                变量                        GV_
                内表（Internal Table）      GT_
                结构（Structure）           GS_
                Range                       R_
                常量                        C_
                类型（Type）                GTY_
                Parameters                  P_
                Select-options              S_
        d. 局部变量 LX_XXX
                变量                        LV_
                内表                        LT_
                结构                        LS_
        e. Function
                Exporting Parameters        E_
                Importing Parameters        I_
                Internal Table（Exporting） ET_
                Internal Table（Importing） IT_
        f. DIALOG
                Table Control               TC_
                Tabstrip Control            TS_

REPORT程序的事件
LOAD-OF-PROGRAM				程序加载
INITIALIZATION				初始化事件
AT SELECTION-SCREEN OUTPUT	 选择界面的PBO事件
AT SELECTION-SCREEN 		选择界面的PAI事件
START-OF-SELECTION	 	    取数
END-OF-SELECTION		    输出
* TOP-OF-PAGE			    表头事件(WRITE语句输出）
AT USER-COMMAND			    按钮功能
* AT LINE-SELECTION		    双击事件(WRITE语句输出)
* TOP-OF-PAGE DURING LINE-SELECTION      次级表单表头



SY-TABIX 用于在循环遍历内表时，当前记录的索引值
SY-SUBRC .系统执行某指令后,表示执行成功与否的变量,0表示成功
SY-DATUM: 当前系统日期
SY-UZEIT: 当前系统时间
SY-REPID: 当前程序名称
SY-UNAME: 当前使用者登入SAP的USERNAME
SY-TCODE: 当前执行程序的Transaction code
SY-INDEX : 当前LOOP循环过的次数
SY-SROWS: 屏幕总行数
SY-SCOLS: 屏幕总列数
SY-MANDT: 當前系統編號(CLIENT NUMBER)
SY-VLINE: 画竖线
SY-ULINE: 画横线
SY-VLINE: 垂直线
SY-LANGU: 当前登录语言

```







## 一、结构体和内表

### 1.内表定义

```xml
三种方式

1.  data  gt_out like table of <tablename>.
2.  data  gt_out type <structure>.
3.  data : begin of gt_out occurs n,
   			 <field>,
         	  <field>,
              <field>,
           end of gt_out.

```

```xml

*--------------------------------------------------------------------*
* 通过定义结构体并参考该结构定义内表
*--------------------------------------------------------------------*
TABLES: employee.                           "参考某一透明表，必须先引用定义
 
TYPES:  BEGIN OF emp,
         id          LIKE employee-id,
         name1       LIKE employee-name1,
         country     LIKE employee-country,
        END OF emp.
 
*--------------------------------------------------------------------*
* 参考EMP 结构定义一个初始化大小为10，并有HEADER LINE的内表
*--------------------------------------------------------------------*
DATA: emptab TYPE TABLE OF emp INITIAL SIZE 10 WITH HEADER LINE.
 
 
*--------------------------------------------------------------------*
* 参考EMPTAB内表，重新定义没有HEADER LINE的内表
*--------------------------------------------------------------------*
DATA: emptab_n LIKE TABLE OF emptab.
 
 
*--------------------------------------------------------------------*
* 定义一个允许重复KEY，并且没有HEADER LINE的排序表
*--------------------------------------------------------------------*
DATA: emptab_s TYPE SORT TABLE OF emp WITH NON-UNIQUE KEY id.
 
 
*--------------------------------------------------------------------*
* 通过第三种方式定义的内表，可指定具体字段，默认内表存在HEADER LINE
*--------------------------------------------------------------------*
TYPES:  BEGIN OF emp OCCURS 0,
          id         LIKE employee-id,
          name1      LIKE employee-name1,
          country    LIKE employee-country,
        END OF emp.


```



### 2.内表与工作区之间操作

```xml
append <workarea> into <itab> .
    


```

3.内表数据处理

1.遍历读取内表数据 (LOOP … ENDLOOP.)

```XML

LOOP AT emptab WHERE country BETWEEN ‘A’ AND ‘D’.
     WRITE:  / emptab-country, emptab-name1, emptab-sales.
ENDLOOP.
 
IF sy-subrc NE 0.  
     WRITE: / ‘NO ENTRIES’.  
ENDIF.
解析：

a.LOOP 语句后，允许使用WHERE语句筛选数据。

b.程序中，出现 sy-subrc 变量，这是系统全局变量，用于检查是否符合条件,

如若符合条件 sy-subrc 返回0 ，如果不符合，则返回4.
```

2.读取内表数据 (READ TABLE …)

```XML
tables: employee.

types : begin of emp,
	country like employee-countyr,
	name1 like employee-name1,
end of emp.

data: emptab type table of emp  initial size 10 with header line.
select * from employee.
	move-corresponding employee to emptab.
	append emptab.
endselect.

```



```XML

READ TABLE 选项：

1) READ TABLE <EMPTAB>.
2) READ TABLE <EMPTAB>  WITH KEY <k1> = <v1>…  <kn> = <vn>.
3) READ TABLE <EMPTAB>  WITH TABLE KEY <k1> = <v1> …  <kn> = <vn>.
4) READ TABLE <EMPTAB>  WITH KEY  = <value>.
5) READ TABLE <EMPTAB>  WITH KEY . . .  BINARY SEARCH.
6) READ TABLE <EMPTAB>  INDEX <i>.
7) READ TABLE <EMPTAB> COMPARING <f1> <f2> . . . .
8) READ TABLE <EMPTAB> COMPARING ALL FIELDS.
9) READ TABLE <EMPTAB> TRANSPORTING <f1> <f2> . . . .
10) READ TABLE <EMPTAB> TRANSPORTING NO FIELDS.
关键字说明：

[KEY|TABLE KEY]: 通过内表的主键字段查找
[BINARY SEARCH]: 二分法查找，使用该方法时，在READ TABLE之前，必须对内表排序
[INDEX]: 根据内表索引查找
[COMPARING]:只查找设置的字段
[COMPARING ALL FIELDS]:查找内表所有的字段
[TRANSPORTING]: 只输出设置的字段数据
[TRANSPORTING NO FIELDS]: 不输出任何数据
```

### 3.内表排序

对内表进行排序，指定具体排序的排序字段、排序方式（升序/降序）

默认情况下，为升序。

语法：

****SORT itab [BY  <f1> <f2>] [ASCENDING|DESCENDING]****

4.内表分类汇总（collect table）

通过COLLECT TABLE关键字，可以对内表中相同记录合并，若有数字类型(I、P、F)的字段，会将其合并汇总。

COLLECT [ INTO] itab.

```XML
TABLES: EMPLOYEE.
TYPES: BEGIN OF EMP,
		country like employee-country,
		sales   like employee-sales,
	  END OF EMP.

data  emptab    type table of emp initial size 10 with header line.
data  emptab_c  type table of emp initial size 10 with header line.
data  emptab_wa type emp.
 
* 赋值
EMPTAB-COUNTRY = 'D'.
EMPTAB-SALES         = 400.
APPEND EMPTAB.
 
EMPTAB-COUNTRY = 'USA'.
EMPTAB-SALES         = 1000.
APPEND EMPTAB.
 
EMPTAB-COUNTRY = 'GB'.
EMPTAB-SALES         = 500.
APPEND EMPTAB.
 
EMPTAB-COUNTRY = 'D'.
EMPTAB-SALES         = 7800.
APPEND EMPTAB.
 
SORT EMPTAB BY COUNTRY.
LOOP EMPTAB.  
     MOVE-CORRESPONDING EMPTAB TO EMPTAB_WA.
     COLLECT EMPTAB INTO EMPTAB_C.
ENDLOOP.
```

#### 清空内表

```XML
CLEAR   <internal table>：仅清空HEADER LINE，对内表数据存储空间不影响

REFRESH <internal table>：清空内表数据存储空间，对HEADER LINE不影响

FREE    <internal table>：清空内表数据存储空间，对HEADER LINE不影响
```



#### 获取内表信息

```xml
可以通过 DESCRIBE 关键字获取内表的相关信息。

语法：DESCRIBE TABLE <internal table> [LINES <var1>] [OCCURS <var2>].
```







## 二、模块化 – 子程序

1.创建子程序

FORM SUB.

​	   SUB CODE

ENDFORM.

2.在子程序中定义形式参数，可以通过下面几种方式：

```XML
USING
向子程序直接参数，在子程序中，传输的参数不能改变。
如若需要直接传值的，需要加上VALUE() 关键字，一般情况下，都是以引用方式传递：
PERFORM  <NAME> USING <A1> <A2> <A3>.
    
FORM <NAME> USING VALUE(<F1>)
    			 VALUE(<F2>)
    			  		<F3>. 
ENDFORM.

CHANGING 
通过子程序传输内表或参数，当程序执行成功后，执行结果会通过该表或参数返回给主程序。
PERFORM  <NAME> USING <A1> <A2> 
                CHANGING   <A3>.
    
FORM <NAME> USING VALUE(<F1>)
    			 VALUE(<F2>)
    		 CHANGING VALUE(<F3>). 
F3 = 'AAA'     .                
                            
ENDFORM.
                            
                        
TABLES
通过内表方式传输参数数据。
在定义形式参数时，在参数名后，需要声明其内表结构；
FORM SUB TABLES PT_MARA STRUCTURE MARA.
           ...
ENDFORM.
         
                            
                            
                            
*主程序
* 定义内表，带有HEADER LINE
data: begin of tab occurs 10,
      f1 like tabna-country,
      f2 like tabna-name1,
   end of tab.
data : x .
perform sub1 tables tab using x.

form sub1 tables fred structure tab using z.
  loop at fred .
       write: / fred-F1,fred-F2.
  endloop.
endform.
                            
                            
参考定义好的内表操作
* 主程序
TABLES: mara,marc.
TYPES: BEGIN OF ty_s_test,
         matnr type mara-matnr,
         werks type marc-werks,
       END OF ty_s_test.
DATA:  gt_test TYPE TABLE OF ty_s_test.
 
* 调用子程序
PERFORM sub TABLES gt_test.
 
*子程序
FORM sub TABLES pt_test LIKE gt_test.
 
  …
 
ENDFORM.
                  
                            
```





OPEN_ SQL



```XML

SELECT SINGLE 命令允许你查询一条记录 ,为了确保你查询的记录是唯一的，你必须在你的 WHERE 子句指定所有KEY值，

如若查询的记录不止一条，系统会返回代码 SY-SUBRC = 8，查询结果为空；

SELECT SINGLE <F1> <F2>  FROM <dbtab>

INTO <work area>

INTO (<f1>, <f2>, <f3> …  )

INTO CORRESPONDING FIELDS OF <work area>

WHERE  <Key1> <op>  AND <Key2> <op> …

```



FOR ALL ENTRIES IN 语句

```tex
由于内表可以临时存储多条数据，而Open SQL允许将内表数据作为查询条件，故可以通过 FOR ALL ENTRIES IN 语句参照内表作为条件查询。

相当于使用 INNER JOIN 连接两个表一样，然后在数据量庞大的时候，FOR ALL ENTRIES IN 语句会比INNER JOIN的查询快捷。

两者各有优缺点，视具体情况而定。
```



```sql
*inner join 用法*
select distinct knb1~bukrs t001~butxt
from knb1
inner join t001
on knb1~bukrs = t001~bukrs
into corresponing fields of table tb_bukrs
where kunnr in rn_kunnr.


*for all entries in 用法*
select distinct bukrs
	into corresponding fields of table gt_knb1
	from knb1.
	
	if gt_knb1[] is not initial.
	select distinct butxt from t001
		into corresponding fields of table gt_tool
		for all entries in gt_knb1
		where bukrs = gt_knb1~bukrs.
	endif
	
	
```



### ABAP 数据字典



Domain:域

![abap_09_Predefined_Types](http://www.sapjx.com/wp-content/uploads/2014/03/abap_09_Predefined_Types.png)



data element :  所有数据对象定义的基本类型，继承Domain的所有属性；它可以在Domain的基础上重新定义相关长度、格式等属性，一个Domain下可包含多个 data element   【类似于子类重写父类方法】



FIELD： 透明表字段，可以作为透明表的主/外键，继承了Data Element的所有属性。





RANGE TABLE

Range Table 常用于Open SQL语句中的条件筛选，可以优化取数效率与程序性能。

```XML
定义方式1.
 TYPE RANGE OF…
定义方式2.
 RANGES rtab FOR dobj [OCCURS N].

```



```XML





```













### 1、选择屏幕的PBO事件

–例：点选复选框，对单选按钮及描述进行成组的显示或隐藏

–注意：当对Parameters或Select-options进行隐藏时，要修改其INPUT属性，或修改active属性

```xml

PARAMETERS   P_CHECK  AS   CHECKBOX  USER-COMMAND HIDE ."生成一个复选框

Selection-screen begin of line.   "在上一行放置控件
selection-screen comment(10) text-c01 modif id sex  "文本
Parameters p_female radiobutton group  GP1 MODIF ID SEX. "单选按钮
selection-screen position 33. ”到屏幕33 的位置
selection-screen comment(10) text-c02 modif id sex. "文本
parameters p_male radiobutton group GP1 MODIF ID SEX ."单选按钮
selection-sereen end of  line.

```



```xml
AT SELECTION-SCREEN OUTPUT.
	IF P_CHECK = 'X'.
		LOOP AT SCREEN.
			IF SCREEN-GROUP1 = 'SEX'.
			SCREEN-INVESIBLE = '1'.
			SECEEN-INPUT = '0'.
			MODIFY SCREEN.
			ENDIF.
		ENDLOOP.
	ENDIF.
```



### 2、选择屏幕的PAI事件

例：当输入学生序号并点击回车后，程序根据当前输入的学生序号值进行检查，如果在数据库表中存在，在一个文本控件中返回’检查成功！’，如果不存在，返回’检查失败！’

```xml

SELECTION-SCREEN BEGIN OF BLOCK BK1 WITH FRAME TITLE TEXT-T01.  ”生成一个块
PARAMETERS: P_ZINDEX LIKE ZHQ_SCORE_01-ZINDEX OBLIGATORY. "生成一个单项选择输入框
SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN COMMENT(10) GV_CMT."用于检查序号是否存在
SELECTION-SCREEN END OF LINE.
SELCTION-OPTION: S_NAEM FOR ZHQ_SCORE_01-NAME NO-EXTENTION NO INTERVALS.  “生成一个多项选择输入框
SELECTION-SCREEN END OF BLOCK BK1.   "块结束

```



```xml
AT SELECTION-SCREEN.
	SELECT SINGLE * 
		FROM ZHQ_SCORE_01
	WHERE ZINDEX = P_ZINDEX.
I	IF SY-SUBRC = 0.
		GV_CMT = '检查成功'.
	ELSE.
		GV_CMT = '检查失败!'.
	MESSAGE E001.
	ENDIF.

```

### 3、取数   •START-OF-SELECTION

​	–这个事件是报表程序选择屏幕事件和初始化事件完成后默认进行的事件。在程序中在第一个FORM-ENDFORM之前的语句并且没有声明相关事件的语句都会默认按照顺序插到Start-of-selection事件的开始位置. 另在使用逻辑数据库时，这个事件也是逻辑数据库获取数据开始的事件。

### 4、输出   •END-OF-SELECTION

–这个事件是报表程序选择完并且处理完数据后LIST输出的事件。同时这个事件也是逻辑数据库选择数据结束的标志.

### 5、表头 •TOP-OF-PAGE

–这个事件是在报表程序里输出自定义的表头用的，在新的一页刚开始遇到write语句的时候这个事件块就会执行一次

–第一次执行：遇到程序中的第一个write语句时，跳转到TOP-OF-PAGE事件执行，执行完毕后返回执行write语句

–隐藏默认表头，在REPORT程序第一行进行设置：  REPORT program NO STANDARD PAGE HEADING .

### 6、设置按钮及标题

•SET PF-STATUS ‘0100’. 

​		–增加按钮，按钮以组的形式定义

​		–后缀EXCLUDING gt_exclude. 设置按钮的隐藏

•SET TITLEBAR ’0100’.

​		–增加标题，可以定义占位符，传入变量

​		–后缀WITH gv_title.



```xml

*隐藏按钮使用的内表
Data : begin of gt_exclude occurs 0 ,
	ucomm like sy-ucomm,
	end of gt_exclude.

gt_exclude-ucomm = 'SORTUP'.
append gt_exclude.
set titlebar '0100' with 'GT_SCORE'.
set PF-STATUS '0100' EXCLUDING GT_EXCLUDE.

END-OF-SELECTION.
	SET PF-STATUS 'STATUS' .
	SET TITLEBAR '0100' .
	IF GT_TOTAL[] IS NOT ININTAL .
		PERFORM write_data.
	ELSE.
		MESSAGE S001 DISPLAY LIKE 'E'.
	ENDIF.

```



### 7、按钮功能

•AT USER-COMMAND

–对自定义按钮增加功能，使用系统变量SY-UCOMM判断功能代码

```xml

AT USER-COMMDND.
CASE SY-COMM.
	WHEN 'BACK'.
		LEAVE TO SCREEN 0 .
	WHEN 'EXIT' OR 'CANCEL'.
		LEAVE PROGRAM.		
	WHEN 'SORTUP'.
		PERFORM SORTUP.
ENDCASE.

FORM SORTUP.
	DATA: LV_FIELDNAME(30) .
	DATA: LV_FNAME(10) .
	SY-LSIND = SY-LSIND - 1 .
	FIELD-SYMBOLS: <F_TB> TYPE ANY TABLE.
     *1.获取光标所在位置字段
       get cursor field lv_fieldname. "lv_fieldname = gt_score-zindex
       if lv_fieldname+0(8) = 'gt_score'.
        	lv_fname = lv_fieldname+9(*).
     *2.排序
        sort gt_score by (lv_fname).
     *3.重新输出
        PERFORM write_data.
		endif.
ENFFORM.




```





### 8、双击事件  •AT LINE-SELECTION

–双击表单中的某一行，可以执行跳转或再次输出表单等操作

```xml
AT LINE-SELECTION。
	PERFORM LINE_SELECTION.

FORM LINE_SELECTION.
	DATA: LV_FIELDNAME(30).
	DATA: LV_FNAME(10) .
	DATA: LV_VALUE(30) .

*1.获取光标所在位置字段
 get cursor field lv_fieldname value lv_value.  "lv_fieldname= gt_score-zindex

IF lv_fieldname = 'gt_score-zindex'.
*2.次级表单
	select single *  from ZHQ_SCORE_02
	WHERE ZINDEX = LV_VALUE.
	IF SY-SUBRC = 0.
		WRITE: / '学生编号' ,ZHQ_SCORE_02-ZINDEX,
			/ '姓名' ,ZHQ_SCORE_02-NAME,
			/ '性别' ,ZHQ_SCORE_02-SEX,
			/ '成绩' ,ZHQ_SCORE_02-SCORE,
			/ '学校代码' ,ZHQ_SCORE_02-SCHOOL.
	ELSE.
		MESSAGE E000 WITH TEXT-M01.
	ENDIF.
ELSE.
	MESSAGE E000 WITH TEXT-M02.
ENDIF.
ENDFORM.



```



PERFORM   XXX USING 【形式参数 】CHANGING 【传出】







## 三、函数

### 1.函数组

•函数模块可包含自有局部类型和数据对象定义（仅在函数模块内可见）

### 2.函数模块

–程序调用函数模块，会加载相应的整个函数组并执行函数模块。

–如果调用该组内的其他函数模块，无需重新加载便可由函数组的相同全局数据进行处理。

### 3.函数组的创建

​	–SE80，在package中右键创建函数组

​	–SE80，选择function group，直接创建函数组

​	–SE37，选择Goto，函数组，创建函数组

### 4.函数的创建

​	•分别指定传输参数

​	–传入的变量/结构：Import

​	–传出的变量/结构：Export

​	–既传入又传出的变量/结构：Changing

​	–以内表形式传输：Tables（不区分传入传出）

​	•传入参数可选择是否可选参数（不勾选Optional），传出参数都是可选参数

### 5.函数模块的调用

​	•调用语句：CALL FUNCTION ‘FUNCNAME’ （函数名称为单引号+大写）

​	•参数传输

​	–EXPORTING：程序将值传递给函数模块的导入参数

​	–IMPORTING：实际参数会分配给导出参数，可使用这些参数访问调用结果

​	–EXCEPTIONS：函数中定义的异常自动分配不同数值，当异常被提出时，SUBRC被自动赋值为相应的数字



```xml
CALL FUNCTION 'ZHQ_FUN_DIV_01'
	EXPORTING
		I_DIV1 = 
		I_DIV2 =
	IMPORTING
		E_RESULT = 
	EXCEPTIONS
		NO_ZERO = 1
		OTHERS = 2

IF SY-SYBRC <> 0.
ENDIF.

```



```XML


DATA: GV_RESULT(10) TYPE P DECIMALS 2.
PARAMETERS : P_NUM1 TYPE I OBLIGATORY,
			P_NUM2 TYPE I OBLIGATORY.

CALL FUNCTION 'ZHQ_FUN_ADD_02'
	EXPORTING
		I_DIV1 =  P_NUM1
		I_DIV2 =  P_NUM2
	IMPORTING
		E_RESULT = GV_RESULT
	EXCEPTIONS
		NO_ZERO = 1
		OTHERS = 2

CASE SY-SUBRC.
	WHEN '1' .
		WRITE:/ '被除数不能为0'.
	WHEN '0'.
		WRITE:/ P_NUM1 , '/',P_NUM2,'=',GV_RESULT.
ENDCASE.
```





### 6.常用函数表

```xml
日期时间相关函数

DAY_IN_WEEK                          根据日期返回星期几
DATE_GET_WEEK                        根据日期返回第几周
NEXT_WEEK                            根据当前周返回下周信息
WEEK_GET_FIRST_DAY                   取得一周的第一天
RP_LAST_DAY_OF_MONTHS                根据一个月的第一天获得一个月的最后一天
RP_CALC_DATE_IN_INTERVAL             年月日加减
CONVERSION_EXIT_INVDT_INPUT          转化日期格式为内部格式
CONVERSION_EXIT_INVDT_OUTPUT        转化内部日期格式为输出格式
DATE_CHECK_PLAUSIBILITY             日期有效性检查
ATE_STRING_CONVERT                  把日期字符串转化为指定的格式




数字、字符串处理相关函数

CLOI_PUT_SIGN_IN_FRONT              		  负号前置
CONVERSION_EXIT_ALPHA_INPUT      			 数字字符串补前导零
CONVERSION_EXIT_ALPHA_OUTPUT    			 数字字符串去前导零
SJIS_DBC_TO_SBC                           	  全角转化为半角
SJIS_SBC_TO_DBC                           	 半角转换为全角
STRING_REVERSE                           	  字符串反向
STRING_CENTER                            	  居中字符串
STRING_MOVE_RIGHT                     		 字符串居右
STRING_LENGTH                           	  计算字符串长度 
TEXT_SPLIT                                    字符串分割



```



## 四、ONLINE 程序概览

### 1.程序类型

–Report程序：1类型程序

•制作报表，数据列表（Data List）输出

–Online程序：M类型程序

•查询数据，录入、修改、删除等

•Module Pool 程序：以Module Pool形态进行业务流程的逻辑处理

•Online 程序，强调用Online Transaction来处理业务流程进行过程

•Screen 程序，主要使用Screen（及屏幕对象），并实现界面间Flow Logic（流逻辑）

### 2.程序对象

–界面（Screen）

–模块池（Module Pool）

•全局字段（Global Data）：声明模块池中所有模块都可使用的数据 – TOP

•PBO模块（PBO Modules）：屏幕输出前调用的模块 – O01

•PAI模块（PAI Modules）：相应用户输入而调用的模块 – I01

•子程序（Subroutines）：可以在模块中任何位置调用的子程序 – F01

–菜单（GUI Status）

–标题（GUI TITLE）

–事务代码（Transaction Code）



•Online程序不能被直接执行，必须通过事务代码，指定界面执行

### 3.界面中的事件块（Event Block）

•用户访问界面，对界面进行操作，相应的操作是通过逻辑流控制的。也就是Screen Painter中定义Flow Logic的位置

•4个事件块：

​			–PROCESS BEFORE OUTPUT.

•PBO中的处理逻辑控制界面输出前处理，如更改一些元素的值或属性

​			–PROCESS AFTER INPUT.

•PAI中的处理逻辑控制用户对界面操作后的处理，如按回车键对输入数据进行检查

​			–PROCESS ON HELP-REQUEST.

•Field Help的实现（F1帮助）

​			–PROCESS ON VALUE-REQUEST.

•输入帮助Search Help的实现（F4帮助）

•界面定义的步骤：

​			–创建界面，在Screen Attributes中定义Screen的属性

​			–在Screen Layout Designer和Element List中定义界面中的元素（位置及属性）

​			–在Screen Flow Logic中设定在Screen显示的逻辑处理和显示后对界面进行相应操作的逻辑处理

### 4.创建界面

•界面编号的选取：

​		–0000~9999

​		–其中1000和1010之间的屏幕编号为ABAP字典表的维护屏幕以及可执行程序的标准选择屏幕而预留

•属性（Attributes）

​			–设定屏幕基本属性

•元素清单（Element List）

​			–包含界面中定义的所有构成元素

​			–可编辑元素属性

​			–OK_CODE（要定义接收变量）

•流逻辑（Flow Logic）

​			–代码定义部分

•点击     ‘layout’   可以进入Screen Layout Designer

### 5.属性

•短文本

•界面类型

​				–标准界面

​				–子界面

​				–对话框

​				–选择界面

•下一屏

链接：https://pan.baidu.com/s/1M-PItyox7--c-nhS1wL2sQ 
提取码：1111









链接：https://pan.baidu.com/s/1pzqWR7-D7aPPtwozwUPgAA 
提取码：1111

​				–当前界面输出结束时出现的界面

​				–为空：程序结束

​				–程序中调用其他界面：调用优先

•行/列

​				–设置界面大小



### 6.界面中的常用关键字

| **Keyword** | **功能**                                                     |
| ----------- | ------------------------------------------------------------ |
| MODULE      | 调用Dialog Module。                                          |
| FIELD       | 指Element list中特定的Screen field。即，可以判断Screen Field的值或状态是否发生变化。在PAI中对相关Field进行控制时，一定要使用的关键字。 |
| ON          | FIELD  …… ON (Field的连接语)                                 |
| VALUE       | FIELD  …… VALUE (Field的连接语)                              |
| CHAIN       | CHAIN的开始。CHAIN可以将多个Field捆绑成一个同时进行管理。    |
| ENDCHAIN    | 结束CHAIN。                                                  |
| CALL        | 调用(CALL a Subscreen)。                                     |
| LOOP        | 开始处理Screen  Table。                                      |
| ENDLOOP     | 结束Screen  Table处理。                                      |

### 7.数据处理逻辑

![image-20200527102439605](C:\Users\ganse\AppData\Roaming\Typora\typora-user-images\image-20200527102439605.png)

### 8.简单的屏幕界面案例

```xml

PROGRAM Z_TEST_G1.

INCLUDE Z_TEST_G1TOP.
INCLUDE Z_TEST_G1O01.
INCLUDE Z_TEST_G1I01.
INCLUDE Z_TEST_G1F01.

==================FOLW LOGIC=================================
PROCESS BEFORE OUTPUT.
* MODULE STATUS_0100.
MODULE INPUT_FIELDS.

PROCESS AFTER INPUT.
* MODULE USER_COMMAND_0100.
MODULE EXIT.


==================Z_TEST_G1TOP=================================

data: ok_code like sy-ucomm.
data: gv_input1(20),
      gv_input2(20),
      gv_input3(20).

======================Z_TEST_G1PBO==============================

MODULE INPUT_FIELDS OUTPUT.
  gv_input1 = '只可输出'.
  gv_input2 = '可输入'.
  gv_input3 = '可输入'.
ENDMODULE.


======================Z_TEST_G1PAI==============================


MODULE EXIT INPUT.
  CASE SY-UCOMM.
    WHEN 'EXIT'.
       LEAVE PROGRAM.
  ENDCASE.
ENDMODULE.






```



### 9.数据检查

 针对某个字段

 	FIELD f1 MODULE m1 [ON REQUEST] .

–针对多个字段同时检查 

​	CHAIN.

​			 FIELD f1.

 			FIELD f2.

 			FIELD f3.

 MODULE m1 [ON CHAIN-REQUEST]. 

ENDCHAIN.

### 10.单选按钮

​		–没有关联功能代码，因此本身不能触发PAI事件

​		–成组设置Function Code，可以触发

​		–设置多个单选按钮，拖拽选中，右键建组

​		–默认第一个为选中

```xml


根据复选框决定是否隐藏

MODULE SET_ELEMENTS OUTPUT.

	IF GV_CHECK = 'X'  .  "显示
	

	ELSE.   "隐藏
		LOOP AT SCREEN.
			IF 
				SCREEN-GROUP3 = 'SEX'.
							“SCREEN-NAME = 'GV_RB01' .
				SCREEN-INVISIBLE = '1' .
				SCREEN-INPUT = '0' .
				MOFIFY SCREEN.
			ENDIF.
		ENDLOOP.
	ENDIF.
ENDMODULE.




```



### 11.复选框

### 12.Box控件

### 13.图标

### 14.Table Control控件

#### 1.表格控件

​		–当界面中查询多条数据时，可以使用Table Control控件来进行表单输出

​		–Table Control的行及列可以由以下元素构成：

​			•KeyWords

​			•Input/Output Fields

​			•Radio Button/Radio Button Group

​			•Checkbox

​			•Pushbutton

#### 2.使用向导创建Table Control

​	•在程序中创建Table Control使用的内表（向导会自动创建相应执行代码）

•创建Table Control

•Name of Table Control：Table Control的名称（例：TC_TAB）

•使用Internal Program Table创建，选择程序中的内表

•选择显示列

•设定属性

–Output only：只显示，不可输入

–Input Control：可输入

–With column header：带标题

–Line selection col.：行可选中

–Single：只能选中一行

–Multiple：可以选中多行

•设定按钮

–Scroll：设定滚动条

–Insert/delete line：插入/删除行按钮

–Select/deselect all：选中所有/不选中按钮

–设定选中列的字段

•设定各部分代码进入的Include程序

#### 3.Table Control列的修改

•减少

–选中input/output field，直接删除

–将PAI中的Chain部分，相应字段进行注释

•

•增加

–内表中增加字段

–使用Input/Output Field增加列

–Text控件增加描述

–在PAI的Chain部分，增加相应字段

•

•属性修改

–将Input/Output Field设成不可输入

#### 4.控件属性临时修改

•在PBO中修改控件属性

–程序每次获取控件静态属性，在PBO中对相应属性做临时修改

–以单元格修改为例

•创建变量，作为选择标志变量：GV_MODIFY

•界面中创建按钮，function code输入’CELL’

•在User-command中将GV_MODIFY值进行修改

•在PBO的循环中（因为要针对单元格判断），根据MARK字段（是否选中），修改screen表的input属性

#### 5.修改table control属性

•在PAI中修改属性（修改Table Control的静态属性）

–在PAI中修改静态属性，在PBO中输出时自动获取，按属性输出

–以列修改为例

•屏幕中创建按钮，Fct Code输入’COL’

•修改table control控件属性结构（图中为TC_TAB2）中的COLS字段（此字段为一个内表）

•创建COLS表相应的结构，通过执行其中INDEX = 5（第五列）的行，将COL中的SCREEN字段（结构）中的INPUT字段进行修改

### 15.Tabstrip控件

•Tabstrip可以实现在某个界面中，通过tab页的形式来显示多个界面

•构成：按钮及子界面区域

#### 1.使用向导创建Tabstrip Control

•创建Tabstrip Control

•Tabstrip Name：Tabstrip Control控件名称（例：TS_TAB）

•输入需要创建的各个tab页的描述

•设定各tab页的名称，及相应的子界面编号

•设定各部分代码进入的Include程序

•

•激活程序后，可以查看到程序中增加了子界面的编号，可以依次进入界面，修改界面格式

–设置方式与主界面相同，但不能设置GUI Status和GUI TITLE

#### 2.标签页的修改

•减少

–选中标签页切换位置（Pushbutton），点击删除

–在数据定义/PBO/PAI中做相应变更，也可不变更

•增加

–选择Pushbotton控件，在标签页旁边增加一个页面

•NAME: TAB_TAB4

•TEXT: TAB4

•Fct CODE: TAB_FC4

•Ref. FIELD: TAB_SCA

–增加数据定义/PBO/PAI处代码，可以直接参考复制

#### 3.GUI Status/GUI Title

•按钮

​		–在程序中定义GUI Status

​		–在PBO中创建Module，使用SET PF-STATUS语句设置按钮

​		–在PAI中根据SY-UCOMM值进行判断

​					•使用OK_CODE/OK_SAVE

 	 	… EXCLUDING …（一个字段的内表）



•标题

​			–在程序中定义GUI Title

​			–在PBO中创建Module，使用的SET TITLEBAR语句设置标题

​			–… WITH … （&1占位符）











```xml

创建数据库表
定义8个laber， 8个text，   2个radio  ，1 个checkbox



保存，先做数据检查，要求不能录取已存在的代码，将录取好 数据存入到数据库表中



判断是否超过300字？ 字节数小于900即可 
 i = 文件总长度 / 900 ，得到几份 ，进1 
byte[] arr1 = new byte[900]



影视？ 
音频长度？


图片？ 爬虫，两份txt，一份API识别，一份以符号分割还是几个字就分割？ 每句话提取


SM04
TH_POPUP

```



## 五、BDC

### 1.概览

•BDC（Batch Data Communication Program，批量数据交换程序）

–是一种通过ABAP程序将资料批量输入系统的方法

–工作原理：将用户繁荣的操作程序和步骤记录下来，然后依照流程逐步将指定的数据在指定的操作页面及栏位输入并执行

–优点：

•避免了手工进行一些重复性工作，提高效率

•输入确保数据的完整性，采用交互用户所用的同一事务代码将数据录入到SAP系统

•何时使用？

–存在大量的重复业务，就会考虑使用BDC录屏

–符合应用于采用正常交互方式输入的数据的所有检查和控制

•BDC的定义

–SAP 系统提供的BDC有两种从其它 SAP 系统和非 SAP 系统向本系统传递数据的基本方法。这两种方法统称为“批输入”或“批数据通讯”。

–两种批输入方法都通过执行常规 SAP 事务进行工作，正如用户输入一样。但是，批输入可自动执行事务，因此适用于输入以电子表格表格形式存在的大量数据。

–BDC是SAP常用的一种数据传输方法。用于一些数据量大，但是对速度又要求不高的数据传输。



### 2.BDC基本流程

•定义一个BDC程序的基本流程

​			–BDC录制，记录屏幕操作

​			–产生相关的程序及数据格式文件

​			–利用程序将相关单据信息读取到内表，并对内表的数据进行调整逻辑处理（数据检查或数据转换）

​			–调用BDC录制程序导入数据

​			–输出消息列表

•主要事务代码

​			–SHDB（录屏）

​			–SM35（查看会话）

•BDC实现的两种方式

​			–CALL TRANSACTION

​			–BATCH INPUT SESSION



BDC 的执行方式

1.前台 		A

2.后台		N

3.在处理过程中显示错误信息  E 





## 六、ALV概览

•类型池：SLIS   【type-pools】

•Fieldcat TYPE slis_t_fieldcat_alv

​			–列格式设置（表单）

​			–字段名称，列是否可修改，列宽度等

•Layout TYPE slis_layout_alv

​			–全局格式设置（结构）

​			–整表字段是否可修改，是否以斑马纹输出，是否显示选择按钮字段等

•函数：REUSE_ALV_FIELDCATALOG_MERGE

​			–根据内表结构返回FIELDCAT字段结构信息

•函数：REUSE_ALV_GRID_DISPLAY   / REUSE_ALV_LIST_DISPLAY

​			–使用GRID/LIST模式输出ALV报表

函数： REUSE_ALV_GRID_DISPLAY_LVC





### REUSE_ALV_GRID_DISPLAY_LVC函数输入参数属性的应用



```xml

CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'
* EXPORTING
*   I_INTERFACE_CHECK                 = ' '   "检查接口一致性
*   I_BYPASSING_BUFFER                =       "是否使用缓存
*   I_BUFFER_ACTIVE                   =       "是否激活缓存
*   I_CALLBACK_PROGRAM                = ' '   "调用ALV的程序名称,一般为当前程序sy-repid .
*   I_CALLBACK_PF_STATUS_SET          = ' '   "ALV工具栏
*   I_CALLBACK_USER_COMMAND           = ' '   "ALV工具栏功能实现
*   I_CALLBACK_TOP_OF_PAGE            = ' '   "ALV抬头内容信息
*   I_CALLBACK_HTML_TOP_OF_PAGE       = ' '   "ALV HTML格式抬头内容信息
*   I_CALLBACK_HTML_END_OF_LIST       = ' '   "ALV HTML格式页脚内容信息
*   I_STRUCTURE_NAME                  =       "为输出表数据结构的命名，指定了这个参数，域目录将会自动生成
*   I_BACKGROUND_ID                   = ' '   "ALV背景图片Object ID .
*   I_GRID_TITLE                      =       "ALV  标题
*   I_GRID_SETTINGS                   =       "GRID 信息
*   IS_LAYOUT_LVC                     =       "ALV输出布局样式
*   IT_FIELDCAT_LVC                   =       "设定显示的项目名称及输出设定
*   IT_EXCLUDING                      =       "隐藏设置的ALV工具栏
*   IT_SPECIAL_GROUPS_LVC             =
*   IT_SORT_LVC                       =       "ALV排序设置
*   IT_FILTER_LVC                     =       "ALV过滤设置
*   IT_HYPERLINK                      =
*   IS_SEL_HIDE                       =       "替换或修改屏幕中select-option的值
*   I_DEFAULT                         = 'X'   "用户是否可以定义默认的布局，’X'可以Space不可以
*   I_SAVE                            = ' '   "保存表格布局,’X'-只能保存全局变式；’U'-只能保存特定变式；’A'-都可以保存；Space-不能保存变式 （默认：space）
*   IS_VARIANT                        =       "表格布局变式
*   IT_EVENTS                         =       "设置事件, 类型为slis_t_event的内表（name：事件名称，form：事件的FORM）
*   IT_EVENT_EXIT                     =       "设置回调的方法的执行行为，表明用户所写的代码是在执行标准执行之前还是之后
*   IS_PRINT_LVC                      =
*   IS_REPREP_ID_LVC                  =
*   I_SCREEN_START_COLUMN             = 0     "以对话框形式显示的开始列
*   I_SCREEN_START_LINE               = 0     "以对话框形式显示的开始行
*   I_SCREEN_END_COLUMN               = 0     "以对话框形式显示的结束列
*   I_SCREEN_END_LINE                 = 0     "以对话框形式显示的结束行
*   I_HTML_HEIGHT_TOP                 =       "HTML抬头的高度
*   I_HTML_HEIGHT_END                 =       "HTML页脚的高度
*   IT_ALV_GRAPHICS                   =       "是否可以在图表中显示ALV
*   IT_EXCEPT_QINFO_LVC               =
*   IR_SALV_FULLSCREEN_ADAPTER        =
* IMPORTING
*   E_EXIT_CAUSED_BY_CALLER           =
*   ES_EXIT_CAUSED_BY_USER            =
  TABLES
    T_OUTTAB                          =
* EXCEPTIONS
*   PROGRAM_ERROR                     = 1
*   OTHERS                            = 2
          .
IF SY-SUBRC <> 0.
* Implement suitable error handling here
ENDIF.

```









## 七、常用函数合集

### 一、时间日期处理函数

#### 1.FIMA_DATE_CREATE 函数

获取输入日期前、后的年、月、日

```.
data: date type dats.
call function 'fina_data_create'
exporting
	i_date							=  '20200622'  "自定义输入日期
	i_flg_end_of_month 				 = ''
	i_year							=  2    "2年后的日期，负数表示两年前
	i_months						=  2  	"同上
	i_days							= 2	    "同上
	
	i_set_last_day_of_month			= 'X'  设置X则为自动取当月最后一天，
importing
	e_date               		   = date  .  "返回的日期为当前月份的最后一天
	
	

```



获取输入日期前、后的年、月、日

```xml

DATA calc_date TYPE p0001-begda.
 
CALL FUNCTION 'RP_CALC_DATE_IN_INTERVAL'
  EXPORTING
    date      = '20140101'    "输入日期
    days      = 10            "天数
    months    = 0             "月数
    signum    = '+'           "+号：表示 N天/月/年后的日期， -号：表示过去的日期
    years     = 0             "年数
  IMPORTING
    calc_date = calc_date.    "返回结果：10天后的日期（2014.01.11）
```



#### **2.LAST_DAY_OF_MONTHS 函数**

获取输入日期最后一天的日期

```xml

DATA date TYPE sy-datum.
 
CALL FUNCTION 'LAST_DAY_OF_MONTHS'
  EXPORTING
    day_in            = '20200622'     "输入日期
  IMPORTING
    last_day_of_month = date           "返回日期：20200631
  EXCEPTIONS
    day_in_no_date    = 1
    OTHERS            = 2.

```

#### 3.RP_LAST_DAY_OF_MONTHS 函数

获取输入日期最后一天的日期

```xml
DATA date TYPE sy-datum.
 
CALL FUNCTION 'RP_LAST_DAY_OF_MONTHS'
  EXPORTING
    day_in            = '20140101'     "输入日期
  IMPORTING
    last_day_of_month = date           "返回日期：20140131
  EXCEPTIONS
    day_in_no_date    = 1
    OTHERS            = 2.
```

#### 4.BKK_GET_MONTH_LASTDAY 函数

获取输入日期最后一天的日期

```xml
DATA date TYPE sy-datum.
 
CALL FUNCTION 'BKK_GET_MONTH_LASTDAY'
  EXPORTING
    i_date = '20140101'   "输入日期
  IMPORTING
    e_date = date.        "返回日期：20140131
 
```

#### **5.CCM_GO_BACK_MONTHS 函数**

获取输入日期N月之前的日期

```xml

DATA date TYPE sy-datum.
CALL FUNCTION 'CCM_GO_BACK_MONTHS'
  EXPORTING
    currdate   = '20140101'   "输入日期
    backmonths = 3            "过去月数
  IMPORTING
    newdate    = date.        "返回日期：20131001
```

#### **6.MONTH_PLUS_DETERMINE 函数**

获取输入日期N月之后的日期

```xml
DATA date TYPE sy-datum.
 
CALL FUNCTION 'MONTH_PLUS_DETERMINE'
  EXPORTING
    months  = 3
    olddate = '20140101'   "输入日期
  IMPORTING
    newdate = date.        "返回日期：20140401

```

#### **7.DAY_IN_WEEK 函数**

获取输入日期该天是星期几/周几

```xml
DATA wotnr TYPE p.
 
CALL FUNCTION 'DAY_IN_WEEK'
  EXPORTING
    datum = '20140101'  "输入日期
  IMPORTING
    wotnr = wotnr.      "返回：3 =》星期三/周三

```

#### **8.HR_GBSSP_GET_WEEK_DATES 函数**

获取查询日期该年的第几周和这周周一、周日日期，该天是星期几/周几（周起始日是周日）

```xml

DATA: sunday      TYPE sy-datum,
      saturday    TYPE sy-datum,
      day_in_week TYPE i,
      week_no     TYPE p08_weekno.
 
CALL FUNCTION 'HR_GBSSP_GET_WEEK_DATES'
  EXPORTING
    p_pdate       = '20140101'    "输入日期
  IMPORTING
    p_sunday      = sunday        "返回本周开始日期（周日）：2013.12.29
    p_saturday    = saturday      "返回本周结束日期（周六）：2014.01.04
    p_day_in_week = day_in_week   "返回该日星期几/周几: 4 => 星期三/周三
                                  "(这周中的第4天，由于开始日期是周日，故需要-1)
    p_week_no     = week_no.      "返回本周开始日期的周数：201352
```



#### **9.CONVERT_DATE_TO_INTERNAL 函数**

将标准日期格式转换为内部数字格式

日期的格式与用户参数有关，转化为内部数字格式时，都为：YYYYMMDD.

```xml
DATA date TYPE sy-datum.
 
CALL FUNCTION 'CONVERT_DATE_TO_INTERNAL'
  EXPORTING
    date_external            = '2014.6.1' "当前用户日期格式
    accept_initial_date      = ' '
  IMPORTING
    date_internal            = date         "输出20140101
  EXCEPTIONS
    date_external_is_invalid = 1
    OTHERS                   = 2.
 
```





#### **10.HR_99S_INTERVAL_BETWEEN_DATES 函数**

获取两个日期期间的：天数、周数、月数、年数；

包括期间月份的开始(月份第一天日期)、截止日期（月份最后一天日期）



```xml
DATA : days  TYPE i,
       weeks TYPE i,
       months TYPE i,
       years TYPE i .
CALL FUNCTION 'HR_99S_INTERVAL_BETWEEN_DATES'
  EXPORTING
    BEGDA           =   '19960222'
    ENDDA           =   '19970429'
 IMPORTING
   DAYS            =   days
   C_WEEKS         =  weeks
   C_MONTHS        =  months
   C_YEARS         =   years.
  WRITE :/ days,weeks,months,years.      

```

#### **11.HRVE_CONVERT_TIME 函数**

12小时制与24小时制的时间转换，例如：07:00:00 pm -> 19:00:00。

```xml
DATA: lv_in_time  TYPE tims,
      lv_out_time TYPE tims,
      lv_am_pm    TYPE c.
lv_in_time = '060000'.
CALL FUNCTION 'HRVE_CONVERT_TIME'
  EXPORTING
    type_time       = 'B'    " A = 24小时制 -> 12小时制  B = 12小时制 -> 24小时制
    input_time      = lv_in_time
    input_am_pm     = 'PM'
  IMPORTING
    output_time     = lv_out_time
    output_am_pm    = lv_am_pm
  EXCEPTIONS
    parameter_error = 1
    OTHERS          = 2.
 
WRITE:/ | Input Time - { lv_in_time }|.    " 输出：060000
WRITE:/ |Output Time - { lv_out_time }|.   " 输出：180000


```

#### **12.F4_DATE 函数**

为 F4 帮助显示日历，弹出日历对话框，供用户选择日期

```XML
PARAMETERS:p1(8) TYPE c.
 
AT SELECTION-SCREEN ON VALUE-REQUEST FOR p1.
  DATA:l_date TYPE sy-datum.
  CALL FUNCTION 'F4_DATE'
    EXPORTING
      date_for_first_month         = sy-datum
    IMPORTING
      select_date                  = l_date  .   "用户选择后返回的日期
   
```



#### **13.F4_CLOCK 函数**

为 F4 帮助显示时间，弹出时间对话框，供用户选择时间

```xml

PARAMETERS:p1(6) TYPE c.
 
AT SELECTION-SCREEN ON VALUE-REQUEST FOR p1.
 
  DATA: l_time TYPE sy-uzeit.
 
  CALL FUNCTION 'F4_CLOCK'
    EXPORTING
      start_time    = sy-uzeit
      display       = ' '
    IMPORTING
      selected_time = l_time.
```

二、 字符串处理函数

1.CONVERSION_EXIT_ALPHA_OUTPUT

去除前面的零

```xml
DATA  d  TYPE n .
CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
  EXPORTING
    INPUT         =  '0000002222'
 IMPORTING
   OUTPUT        = d .
          .
WRITE d .
```

2.**CONVERSION_EXIT_ALPHA_INPUT**

前面填充 零 

```xml
DATA  d  TYPE n LENGTH 10.
CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
  EXPORTING
    INPUT         = '313213'
 IMPORTING
   OUTPUT        = d.
WRITE d .
```

 **ALSM_EXCEL_TO_INTERNAL_TABLE**——读取EXCEL数据到内表

GUI_DPWNLOAD  下载文件中的数据

GUI_UPLOAD		上载数据到文件里

WS_FILENAME_GET   获取文件名

CONVERSION  负号前置

VRM_SET_VALUSE  下拉框实现函数

ALSM_EXCEL_TO_INTERNAL_TABLE     EXCEL上传函数  

### 七、程序执行顺序

#### 1.INITIALIZATION 事件

该事件在屏幕未显示之前执行，可对程序设置值及屏幕元素进行初始化设置

####  2.AT SELECTION-SCREEN OUTPUT 事件

屏幕元素声明完成后执行，并会在程序执行之前优先检查该事件下的所有代码。
通常用于对输入值校验数据，如库存是否充足，操作类型是否允许等。



#### 3.AT SELECTION-SCREEN ON … 事件

可在程序执行之前指定输入值的校验，与AT SELECTION-SCREEN OUTPUT事件类似；
只是OUTPUT事件检查屏幕输入值的校验，而ON 事件则是检查指定的输入值。
执行该事件时，其它输入域的输入状态会被锁定。

##### 3.1 AT SELECTION-SCREEN ON VALUE REQUEST FOR {para|selcrit-low|selcrit-high} 

指定输入域F4搜索帮助的事件，用于实现屏幕输入域的自定义F4搜索帮助。

##### 3.2AT SELECTION-SCREEN ON HELP REQUEST FOR 

指定输入域F1帮助的事件，用于实现屏幕输入域的自定义F1帮助。

##### 3.3 AT SELECTION-SCREEN ON {para|selcrit}

指定输入域输入值时触发，可以是[Parameters](http://www.sapjx.com/tag/parameters)对象，也可以是Select-options对象。

##### 3.4 AT SELECTION-SCREEN ON BLOCK (block)

该事件应用于设定框架的屏幕中。

##### 3.5 AT SELECTION-SCREEN ON RADIOBUTTON GROUP (radi)

该事件应用于单选框按钮组中。

##### 3.6 AT SELECTION-SCREEN ON END OF (selcrit)

该事件只应用于Select-options对象，Parameters对象不起作用。在输入域进行多行数据输入时触发。

### 4 .AT SELECTION-SCREEN 事件

与前面的 AT SELECTION-SCREEN OUTPUT 和AT SELECTION-SCREEN ON 事件类似，

区别在于执行顺序优先级低于前两者。

### 5 .START-OF-SELECTION 事件

开始执行，该事件在执行程序时触发：

![abap_03_Start_Of_Selection_Event_Demo](http://www.sapjx.com/wp-content/uploads/2014/02/abap_03_Start_Of_Selection_Event_Demo.png)

### 6.END-OF-SELECTION 事件

该事件应用于所有数据处理完成后，即在START-OF-SELECTION执行完成后，但输出屏幕还未显示前。











## FIELD SYMBOL和TYPE REF TO的用法和比较

1.定义

```XML
DATA ref TYPE REF TO data.       "不指定特定类型或者结构，可以是结构也可以是类型
DATA ref TYPE REF TO ty_ym.      "指定特定结构
DATA ref TYPE REF TO char20.     "指定特定类型
 
FIELD-SYMBOLS < FS > TYPE ANY TABLE. "任意标准表
FIELD-SYMBOLS < FS > TYPE it_ym.   "指定特定的表
FIELD-SYMBOLS < FS > TYPE any.     "不指定特定类型或者结构，可以是结构也可以是类型
FIELD-SYMBOLS < FS > TYPE ty_ym.   "指定特定结构
FIELD-SYMBOLS < FS > TYPE char20.  "指定特定类型
```

2.初始化

type ref to 有两种初始化的方法

1. create data  动态开辟内存
2. get reference of 指向已经存在的内存变量

```XML
create data ref type ty_ym		  .
create data ref like line of it_ym.
create data ref like wa_ym.
create data ref like ym.
```



```xml
assign wa_ym to <fs>. 
get reference of wa_ym into ref.
```



3.使用

```xml
指定特定类型时

ref->* = 'abc'.
<fs> = 'abc'.
```

```xml
指定特定结构时

ref->*-col1 = 'abc'.  "简写是ref->col1
<fs>-col1   = 'abc'.
```

```xml
不指定特定的类型或者结构时
FIELD-SYMBOLS < FSX > TYPE ty_ym
ASSIGN ref->* TO < FSX >.  "泛用型结构
< FSX >-col1= 'abc'.
 
FIELD-SYMBOLS < FSX > TYPE any. "
ASSIGN ref->* TO < FSX >.       "泛用型类型
< FSX > = 'abc'.
 
FIELD-SYMBOLS < FSX > TYPE any. "                 
ASSIGN COMPONENT 'col1' OF STRUCTURE < FS > INTO < FSX >. "泛用型结构
< FSX > = 'abc'.
```

```tex
注意事项：

一般来说为了使用方便，还是应该指定 Field Symbol 或者 Type Ref To 的类型或者结构，以便之后直接使用。

在使用 Type Ref To 时，如果是 TYPE REF TO DATA，那么之后想使用这个 Type Ref To 时，依然会不可避免的用到 Field Symbol;

这样代码写起来就很麻烦，还不如一开始就使用 Field Symbol 写。

但像动态内表这种事先不知道结构的场合，那么只能使用 Type Ref To，再结合 Field Symbol；

如果直接使用 Field Symbol，那么 Field Symbol 将无法参考某个已经存在的结构进行初始化。

第一步假如 TYPE REF TO DATA，后面的第二步初始化时依然要指定特定的结构，所以还不如第一步就指定结构。

建议只有在事先不知道结构时，才使用泛型定义。

所以要么定义和初始化时都指定类型或者结构，要么就是动态内表或者结构都无法在事先指定;

像第一步 TYPE REF TO DATA 第二步 CREATE DATA ref TYPE ty_ym 不是好的做法
```



4.区别

```tex
Type Ref To 和 Field Symbol 在用法上目前发现的主要区别：

1） Type Ref To 可以动态开辟内存，在动态内表时，可以等在程序运行时获得结构后再开辟内存，并且赋值给某个 Field Symbol。

而光用 Field Symbol 是做不到的，因为 Field Symbol 的初始化需要“挂”在已知结构上。

2）Type Ref To 不像 Field Symbol 那样有LOOP AT it_tab ASSIGNING < fs >的写法，ref->*不是指向内表数据，

而是类似工作区指向某块内存，所以更改数据后需要 modify 到内表，如果不需要数据了要clear。

而 Field Symbol 则不需要考虑 modify 和 clear。

3）IF < fs1 > = < fs2 > 是比较内存里的值，相对应的是IF ref1->* = ref2->*，而不能判断比较 IF ref1 = ref2 。


```

## 动态SQL

```SQL
SELECT 



```





## 八、面向对象

```XML

*  IMPORT/EXPORT 数据输入/输出接口，接口参数可以参照单个变量，结构or内表
*  CHANGE 同时作为输入输出接口，接口参数可以参照单个变量，结构or内表
*  RETURNING 返回类传递数值 该定义不能和CHANGING/EXPORTING同时使用
*  EXCEPTIONS 返回执行中所出现的错误
*
*  类函数可以拥有多个输入参数，但只能有一个输出参数
*  类的输出接口参数必须和类函数中所定义类型保持一致
*


```



```XML
CLASS MY_CLASS DEFINITION.
  PUBLIC SECTION.
  
  TYPES  GTY_CHAR20 TYPE C LENGTH 20.
  TYPES  GTY_CHAR5  TYPE C LENGTH 5.


  "实例属性
  DATA:       C_TEXT1 TYPE C LENGTH 10 VALUE 'GAN'.
  DATA:       C_TEXT2 TYPE C LENGTH 10 .
  "静态属性
  CLASS-DATA: S_TEXT TYPE C LENGTH 20 VALUE 'STATIC-CLASS'.

  "实例方法
  METHODS WRITE_CHAR.
 
  PROTECTED SECTION.

  PRIVATE SECTION.

ENDCLASS.


CLASS MY_CLASS IMPLEMENTATION.


  METHOD WRITE_CHAR.
     WRITE : /'HELLO_WORLD',C_TEXT1.
  ENDMETHOD.

  
ENDCLASS.



"对象声明
DATA MY_OBJIECT TYPE REF TO MY_CLASS.

START-OF-SELECTION.
  CREATE OBJECT MY_OBJIECT.   "实例化
  WRITE:/ '实例访问用单箭头->', MY_OBJIECT->C_TEXT1.   "实例访问用单箭头

  CALL METHOD MY_OBJIECT->WRITE_CHAR.
  

 
  "STATIC 访问
  WRITE:/ MY_CLASS=>S_TEXT. "静态属性访问用双箭头




```





```XML
计算圆的面积
CLASS MY_CLASS DEFINITION.
PUBLIC SECTION.
  TYPES GTY_P02 TYPE P LENGTH 10 DECIMALS 2.

  METHODS Get_Data1 IMPORTING VALUE(I_R)     TYPE GTY_P02
                    EXPORTING VALUE(E_ROUND) TYPE GTY_P02.



  METHODS Get_Data2 IMPORTING VALUE(I_R)     TYPE GTY_P02
                    RETURNING VALUE(E_ROUND) TYPE GTY_P02.


PROTECTED SECTION.
PRIVATE SECTION.
   CONSTANTS: C_PI TYPE GTY_P02 VALUE '3.14'.

ENDCLASS.


CLASS MY_CLASS IMPLEMENTATION.
  METHOD Get_Data1.
     E_ROUND = 2 * C_PI * I_R.
  ENDMETHOD.

  METHOD Get_Data2.
     E_ROUND = 2 * C_PI * I_R.
  ENDMETHOD.
ENDCLASS.


DATA INS_OBJECT1 TYPE REF TO MY_CLASS.

PARAMETERS P_R TYPE P LENGTH 10 DECIMALS 2 .
DATA  GV_ROUND TYPE P LENGTH 10 DECIMALS 2 .
START-OF-SELECTION.
  CREATE OBJECT INS_OBJECT1.

  CALL METHOD INS_OBJECT1->Get_Data1
      EXPORTING
        I_R      = P_R
      IMPORTING
        E_ROUND  = GV_ROUND.


  WRITE : / GV_ROUND.

*  CALL METHOD INS_OBJECT1->Get_Data2
*     EXPORTING
*        I_R      = P_R
*     RECEIVING
*        E_ROUND  = GV_ROUND.

  GV_ROUND = INS_OBJECT1->Get_Data2( P_R ).

    WRITE : / GV_ROUND.
```





```XML

事件声明   EVENTS EVT EXPORTING ...VALUE(E1 E2) TYPE TYPE
调用事件   RAISE EVENT EVT EXPORTING E1 = F1 E2 = F2..
Event Handler声明   METHODS meth for EVENT evt OF cif IMPORTING E1 E2
Event Handler注册/登陆  SET HANDLER H1 H2[FOR] ..






```

```XML
CLASS MY_CLASS DEFINITION .
PUBLIC SECTION.
METHODS CONSTRUCTOR.
METHODS: WRITE_FIRST.

ENDCLASS.

CLASS MY_CLASS IMPLEMENTATION.

  METHOD CONSTRUCTOR.

   WRITE :/ 'THIS  IS CONSTRUCTOR'.
  ENDMETHOD.

  METHOD WRITE_FIRST.
    WRITE:/ 'THIS IS MY_CLASS --->WRITE_FIRST'.
  ENDMETHOD.



ENDCLASS.



CLASS MY_SUBCLASS DEFINITION INHERITING FROM MY_CLASS.

PUBLIC SECTION.
METHODS WRITE_FIRST REDEFINITION .
METHODS WRITE_SECOND.

ENDCLASS.

CLASS MY_SUBCLASS IMPLEMENTATION.

METHOD WRITE_FIRST.
    WRITE:/ 'THIS IS MY_SUBCLASS --->WRITE_FIRST'.
  ENDMETHOD.
  METHOD WRITE_SECOND.
     WRITE:/ 'THIS IS MY_SUBCLASS --->THIS IS SECOND'.
  ENDMETHOD.
ENDCLASS.





DATA OBJ_1 TYPE REF TO MY_CLASS.
DATA OBJ TYPE REF TO MY_SUBCLASS.
START-OF-SELECTION.
  CREATE OBJECT OBJ.
  CREATE OBJECT OBJ_1.
  CALL METHOD :OBJ->WRITE_FIRST.
  CALL METHOD :OBJ->WRITE_SECOND.
```





## 九、字段符号【Field Symbol】

字段符号就是指针，允许动态访问变量，不占有自己特有的内存空间，数据名与属性到执行时刻才能确定，可以指定所有数据对象，可以明确指定数据类型，也可以不指定数据类型，若不指定，则会继承被分配的字段（对象）的数据类型。



```XML
动态分配
FIELD-SYMBOLS <FS_01> .
DATA : GV_CHAR01 TYPE C LENGTH 5 VALUE 'A',
	   GV_CHAR02 TYPE C LENGTH 5 VALUE 'B',
       GV_CHAR03 TYPE C LENGTH 5 VALUE 'C',
       GV_CHAR04 TYPE C LENGTH 5 VALUE 'D',
       GV_CHAR05 TYPE C LENGTH 5 VALUE 'E',
       GV_CHAR06 TYPE C LENGTH 5 VALUE 'F',
       GV_CHAR07 TYPE C LENGTH 5 VALUE 'G'.
    
DATA GV_FIELDNAME TYPE C LENGTH 20.
DATA GV_INDEX 	  TYPE N LENGTH 2.
GV_FIELDNAME = 'GV_CHAR' .
    
ASSIGN (GV_FIELDNAME) TO <FS_01>.
    
DO 7 TIMES.
    GV_INDEX = SY-INDEX.
    CONCATENATE 'GV_CHAR' GV_INDEX INTO GV_FIELDNAME.
    ASSIGN (GV_FIELDNAME)  TO <FS_01>.
    WRITE : / GV_INDEX,':',<FS_02>.
    
ENDDO.
    
    
    
    
    

```

## 十、内存管理

内部会话的产生

1.通过程序调用

SUBMIT XXXX



## 十一、增强

创建销售凭证 VA01

SPRO



SAP四种用户出口的类型

菜单出口

屏幕出口

功能模块出口

表/结构出口

  

应用类型

1.业务检查

2.界面增强

3.不规则业务的处理

4.搜索帮助的出口













第一代增强： 基于源代码的增删查改，找包含有USEREXIT的程序名，需要权限申请

第二代增强：

```XML
功能模块增强 
子屏幕增强
GUI STATUS功能码增强
INCLUDE STRUCTURE增强
```



SMOD :查看增强组件

CMOD:实现增强

```xml
1.cmod中创建一个project，添加所要使用的增强，激活目标Components
2.在目标function module 中编写功能代码

```



Subscreens

```xml
1.cmod中创建一个project，添加所要使用的增强，激活目标Components
2.通过SMOD 定位到目标程序，创建与其对应的屏幕号，屏幕属性为subscreen，编写功能代码

```









第三代增强：

SE18 查看BADI

SE19 为BADI接口创建一个实例，在里面实现需要增加的功能

第四代增强：

  

## 十二、SAP常用接口



WEBSERVICE

ALE/IDOC

RFC

BAPI

```tex
常用bapi 


MM 模块：
BAPI_OBJCL_CREATE 		分类视图的创建
BAPI_OBJCL_GETCLASSES    分类视图得到详细信息
BAPI_MATERIAL_SAVEREPLICA 物料视图的扩充
BAPI_GOODSMVT_CREATE		 创建物料凭证 注意表T158G可以决定goodsmvt_code
BAPI_GOODSMVT_CANCEL 冲销物料凭证
BAPI_PR_CREATE 创建PR
BAPI_PO_CREATE1 创建PO
BAPI_PO_CHANGE 修改PO和删除PO
WS_REVERSE_GOODS_ISSUE 冲销交货单的过账发货
BAPI_RESERVATION_CREATE1 创建预留
BAPI_RESERVATION_CHANGE 修改和删除预留

SD 模块
BAPI_SALESORDER_CREATEFROMDAT2 创建销售订单
SD_SALESDOCUMENT_CREATE 创建销售订单
BAPI_OUTB_DELIVERY_CREATE_SLS 根据销售订单创建交货单
BAPI_BILLINGDOC_CREATEMULTIPLE 创建发票，注意参数ref_doc_ca
BAPI_SALESORDER_CHANGE 修改或者删除销售订单
MB_CANCEL_GOODS_MOVEMENT 冲销交货单的过账发货
 BAPI_BILLINGDOC_CANCEL 发票的冲销

```









![image-20200716162604947](C:\Users\Null\AppData\Roaming\Typora\typora-user-images\image-20200716162604947.png)



MIDDLEWARE









### SAP ABAP SQL查询分析器（ABAP动态SQL执行）ZSQLEXPLORER















## 十三、功能代码



```tex

创建采购申请 【Purchase Request】 PR  ME51N
创建采购订单 【Purchase order】   PO  ME21N


```



![image-20200716165025498](C:\Users\Null\AppData\Roaming\Typora\typora-user-images\image-20200716165025498.png)



TELF1

TELF2

LSTRAS

REGSS

PSTLZ

PNAME1 

TELFX

PTELF1

ZTERM

ZZDQR

KRAUS

SORTL

ZZJYPP

ZZJYFW

ZZYYZZ

ZZQYLB

ZZCBTZ

ZZJSWZ

ZZYJRQ



```XML

*&---------------------------------------------------------------------*
*& Report  ZDMALL003
*&
*&---------------------------------------------------------------------*
*&
*&
*&---------------------------------------------------------------------*

REPORT zdmall003a.

TABLES:lfa1.

TYPES: BEGIN OF ty_out,
         lifnr  TYPE lfa1-lifnr, "供应商
         name1  TYPE lfa1-name1, "名称
         ktokk  TYPE lfa1-ktokk, "供商类型
         fityp  TYPE lfa1-fityp, "税类型
         stceg  TYPE lfa1-stceg, "税号
         actss  TYPE lfa1-actss, "状态
         zzjyfs TYPE lfa1-zzjyfs,
         stras  TYPE lfa1-stras,
         stelf1 TYPE lfa1-telf1,
         stelf2 TYPE lfa1-telf2,
         stelfx TYPE lfa1-telfx,
         ekgrp  TYPE lfm1-ekgrp,  "经营范围
         minbw  TYPE lfm1-minbw,
         plifz  TYPE lfm1-plifz,
         kalsk  TYPE lfm1-kalsk,
         zterm  TYPE lfm1-zterm,
         lname  TYPE knvk-name1, "联系人
         telf1  TYPE knvk-telf1, "联系人电话
         bankl  TYPE lfbk-bankl,
         bankn  TYPE lfbk-bankn,
         koinh  TYPE lfbk-koinh,
         banka  TYPE bnka-banka,
         brnch  TYPE char100,    " BNKA-STRAS && BNKA-BRNCH
       END OF ty_out,
       BEGIN OF ty_lfa1,
         lifnr       TYPE lfa1-lifnr, "供应商
         name1       TYPE lfa1-name1, "名称
         ktokk       TYPE lfa1-ktokk, "供商类型
         fityp       TYPE lfa1-fityp, "税类型
         ekgrp       TYPE lfm1-ekgrp,  "经营范围
         stceg       TYPE lfa1-stceg, "税号
         actss       TYPE lfa1-actss, "状态
         zzjyfs      TYPE lfa1-zzjyfs,
         minbw       TYPE lfm1-minbw,
         plifz       TYPE lfm1-plifz,
         kalsk       TYPE lfm1-kalsk,
         stras       TYPE lfa1-stras,
         stelf1      TYPE lfa1-telf1,
         stelf2      TYPE lfa1-telf2,
         stelfx      TYPE lfa1-telfx,
         zterm       TYPE lfm1-zterm,
*         adrnr    TYPE lfa1-adrnr,
         lfa1_key    TYPE cdhdr-objectid,
*         adrc_key TYPE cdhdr-objectid,
         nochange(1) TYPE c,
       END OF ty_lfa1,
       BEGIN OF ty_lfbk,
         lifnr TYPE lfbk-lifnr,
         bankl TYPE lfbk-bankl,
         bankn TYPE lfbk-bankn,
         koinh TYPE lfbk-koinh,
*         lfbk_key TYPE cdhdr-objectid,
       END OF ty_lfbk,
       BEGIN OF ty_bnka,
         banks       TYPE bnka-banks,
         bankl       TYPE bnka-bankl,
         banka       TYPE bnka-banka,
         brnch       TYPE bnka-brnch,
         stras       TYPE bnka-stras,
         bnka_key    TYPE cdhdr-objectid,
         nochange(1) TYPE c,
       END OF ty_bnka,
       BEGIN OF ty_knvk,
         parnr TYPE knvk-parnr,
         name1 TYPE knvk-name1,
         telf1 TYPE knvk-telf1,
         lifnr TYPE knvk-lifnr,
       END OF ty_knvk,
       BEGIN OF ty_cdhdr,
         objectclas TYPE cdhdr-objectclas,
         objectid   TYPE cdhdr-objectid,
         changenr   TYPE cdhdr-changenr,
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
SELECT-OPTIONS : s_ktokk FOR lfa1-ktokk DEFAULT 'Z002'.
SELECTION-SCREEN END OF BLOCK b2.

SELECTION-SCREEN BEGIN OF BLOCK b3 WITH FRAME TITLE text-b03.
PARAMETERS: p_size TYPE int4 OBLIGATORY DEFAULT 10000.
SELECTION-SCREEN END OF BLOCK b3.

START-OF-SELECTION.
  PERFORM frm_main.

END-OF-SELECTION.
*&---------------------------------------------------------------------*
*&      Form  FRM_MAIN
*&---------------------------------------------------------------------*
FORM frm_main .
  DATA: lt_lfa1  TYPE STANDARD TABLE OF ty_lfa1,
        lt_lfbk  TYPE STANDARD TABLE OF ty_lfbk,
        lt_knvk  TYPE STANDARD TABLE OF ty_knvk,
        lt_bnka  TYPE STANDARD TABLE OF ty_bnka,
        lt_cdhdr TYPE STANDARD TABLE OF ty_cdhdr,
        lw_out   TYPE ty_out.
  DATA: l_udate TYPE sy-datum.

  SELECT a~lifnr a~name1 a~ktokk a~fityp b~ekgrp a~stceg a~actss a~zzjyfs b~minbw b~plifz
         b~kalsk a~stras a~telf1 a~telf2 a~telfx b~zterm "a~adrnr
    INTO TABLE lt_lfa1
    FROM lfa1 AS a
    INNER JOIN lfm1 AS b ON a~lifnr = b~lifnr
    WHERE a~lifnr IN s_lifnr
      AND a~ktokk IN s_ktokk
      AND b~ekorg EQ 'P001'.
  IF sy-subrc = 0.
    SELECT lifnr bankl bankn koinh
      INTO TABLE lt_lfbk
      FROM lfbk
      FOR ALL ENTRIES IN lt_lfa1
      WHERE lifnr = lt_lfa1-lifnr
        AND banks = 'CN'.
    IF sy-subrc = 0.
      SELECT banks bankl banka brnch stras
        INTO TABLE lt_bnka
        FROM bnka
        FOR ALL ENTRIES IN lt_lfbk
        WHERE banks = 'CN'
          AND bankl = lt_lfbk-bankl.
    ENDIF.

    SELECT parnr name1 telf1 lifnr
      FROM knvk
      INTO TABLE lt_knvk
      FOR ALL ENTRIES IN lt_lfa1
      WHERE lifnr = lt_lfa1-lifnr.

    SORT lt_knvk BY lifnr parnr.
    DELETE ADJACENT DUPLICATES FROM lt_knvk COMPARING lifnr.

    IF p_udate IS NOT INITIAL.
      l_udate = p_udate - 1.

      LOOP AT lt_lfa1 ASSIGNING FIELD-SYMBOL(<fs_lfa1>).
        <fs_lfa1>-lfa1_key = <fs_lfa1>-lifnr.
*        CONCATENATE 'BP' <fs_lfa1>-adrnr INTO <fs_lfa1>-adrc_key RESPECTING BLANKS.
      ENDLOOP.
      LOOP AT lt_bnka ASSIGNING FIELD-SYMBOL(<fs_bnka>).
        CONCATENATE sy-mandt <fs_bnka>-banks <fs_bnka>-bankl INTO <fs_bnka>-bnka_key RESPECTING BLANKS.
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

      IF lt_bnka[] IS NOT INITIAL.
        SELECT objectclas objectid changenr
          APPENDING TABLE lt_cdhdr
          FROM cdhdr
          FOR ALL ENTRIES IN lt_bnka
          WHERE objectid = lt_bnka-bnka_key
            AND objectclas = 'BANK'
            AND ( ( udate = l_udate AND utime >= '223000' ) OR ( udate = p_udate AND utime <= '223000' ) ).
      ENDIF.

      SORT: lt_cdhdr BY objectclas objectid.
      LOOP AT lt_lfa1 ASSIGNING <fs_lfa1>.
        READ TABLE lt_cdhdr TRANSPORTING NO FIELDS WITH KEY objectclas = 'KRED' objectid = <fs_lfa1>-lfa1_key BINARY SEARCH.
        IF sy-subrc <> 0.
          <fs_lfa1>-nochange = 'X'.
        ENDIF.
      ENDLOOP.

      LOOP AT lt_bnka ASSIGNING <fs_bnka>.
        READ TABLE lt_cdhdr TRANSPORTING NO FIELDS WITH KEY objectclas = 'BANK' objectid = <fs_bnka>-bnka_key BINARY SEARCH.
        IF sy-subrc <> 0.
          <fs_bnka>-nochange = 'X'.
        ENDIF.
      ENDLOOP.

      CLEAR: lt_cdhdr.
    ENDIF.

    SORT: lt_lfa1 BY lifnr,
          lt_lfbk BY lifnr bankl,
          lt_knvk BY lifnr parnr,
          lt_bnka BY banks bankl.

    LOOP AT lt_lfa1 INTO DATA(lw_lfa1).

      READ TABLE lt_knvk INTO DATA(lw_knvk) WITH KEY lifnr = lw_lfa1-lifnr BINARY SEARCH.
      READ TABLE lt_lfbk INTO DATA(lw_lfbk) WITH KEY lifnr = lw_lfa1-lifnr BINARY SEARCH.
      READ TABLE lt_bnka INTO DATA(lw_bnka) WITH KEY banks = 'CN' bankl = lw_lfbk-bankl BINARY SEARCH.

      MOVE-CORRESPONDING lw_lfa1 TO lw_out.
      lw_out-telf1 = lw_knvk-telf1.
      lw_out-lname = lw_knvk-name1.
      lw_out-bankl = lw_lfbk-bankl.
      lw_out-bankn = lw_lfbk-bankn.
      lw_out-koinh = lw_lfbk-koinh.
      lw_out-banka = lw_bnka-banka.
      lw_out-brnch = lw_bnka-stras && lw_bnka-brnch.

      " 仅当BNKA和LFA1有修改时才下发
      IF lw_bnka-nochange IS INITIAL OR lw_lfa1-nochange IS INITIAL.
        APPEND lw_out TO gt_out.
      ENDIF.

      CLEAR: lw_lfa1,lw_knvk,lw_out,lw_lfbk,lw_bnka.
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
             'TELF1'        '联系人电话'         '' ''   '16' 'LFA1' '' ,
             'BANKL'        '银行代码'           '' ''   '15' 'LFBK' '' ,
             'BANKN'        '银行账号'           '' ''   '18' 'LFBK' '' ,
             'KOINH'        '持有者'             '' ''   '60' 'LFBK' '' ,
             'BANKA'        '银行名称'           '' ''   '60' 'BNKA' '' ,
             'BRNCH'        '分行名称'           '' ''   '100' '' '' .

  ls_layout_lvc-zebra        = 'X' .
  ls_layout_lvc-cwidth_opt   = 'X' .    "列宽度自动根据内容优化
  ls_layout_lvc-sel_mode = 'D'.
  ls_layout_lvc-no_toolbar = 'X'.

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
  clear: extab,extab[].
  SET PF-STATUS '1000' EXCLUDING extab.
ENDFORM.
*&---------------------------------------------------------------------*
*&      Form  FRM_USER_COMMAND
*&---------------------------------------------------------------------*
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
FORM frm_transport .
  DATA: lo_proxy        TYPE REF TO zco_si_supply_mastdata_out_asy,
        lo_system_fault TYPE REF TO cx_ai_system_fault,
        ls_output       TYPE zmt_supply_mastdata,
        lw_header       TYPE zdt_supply_mastdata_header.
  DATA: lv_message TYPE string,
        l_count    TYPE i,
        l_packno   TYPE i,
        s_line     TYPE i,
        e_line     TYPE i,
        l_ddate    TYPE char14.
  DATA:lt_out  TYPE TABLE OF ty_out,
       lt_roid TYPE lvc_t_roid,
       lw_roid TYPE lvc_s_roid,
       lr_alv  TYPE REF TO cl_gui_alv_grid.

  CLEAR: lt_out.

  IF p_front EQ 'X'.
    CALL FUNCTION 'GET_GLOBALS_FROM_SLVC_FULLSCR'
      IMPORTING
        e_grid = lr_alv.
    IF lr_alv IS BOUND.
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
      CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
        EXPORTING
          input  = lw_out-lifnr
        IMPORTING
          output = lw_out-lifnr.
      MOVE-CORRESPONDING lw_out TO lw_header.
      lw_header-zbhts = '3'.
      lw_header-zzdqr = ''.
      lw_header-dstus = '0'.
      lw_header-ddate = l_ddate.
      APPEND lw_header TO ls_output-mt_supply_mastdata-header.

      IF l_count EQ p_size.
        ls_output-mt_supply_mastdata-message_header-receiver = 'DMALL'.
        ls_output-mt_supply_mastdata-message_header-data_count = l_count .
        TRY.
            CREATE OBJECT lo_proxy.
            CALL METHOD lo_proxy->si_supply_mastdata_out_asyn
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
      ls_output-mt_supply_mastdata-message_header-receiver = 'DMALL'.
      ls_output-mt_supply_mastdata-message_header-data_count = l_count .
      TRY.
          CREATE OBJECT lo_proxy.
          CALL METHOD lo_proxy->si_supply_mastdata_out_asyn
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







lo_proxy	---> ZCO_SI_VENDER_INFO_ZZT_OUT_ASY,   zco_si_supply_mastdata_out_asy

ls_output ------> ZMT_VENDER_INFO_ZZT ,,,zmt_supply_mastdata

lw_header  ------------>ZDT_VENDER_INFO_ZZT_HEADER,,,zdt_supply_mastdata_header.





ZDT_MESSAGE_HEADER-receiver



```XML

ITEM  
	LIFNR   
	KOINH
	BANKA
	BANKN
	BKREF
	STRAS
	BRNCH



```



```XML
ZZTYFS             A1
EKGRP    M1 
PNAME1	A1
PTELF1	A1
KALSK	M1
PSTLZ	M1
ZTERM   M1
ZZCBTZ	A1
ZZQYLB	A1
ZZYYZZ	A1
ZZJSWZ  A1
ZZJYPP	A1
ZZJYFW	A1



NOID
ISSUED_TIME
CREATION_DATE



LT_LFM1	                                   	[7209x5(28)]Standard Table
LT_LFA1	                                   	[7641x24(716)]Standard Table
LT_LFBK	                                   	[4674x5(246)]Standard Table
LT_BNKA	                                   	[1910x6(482)]Standard Table
LT_KNVK	                                   	[657x4(142)]Standard Table


	  lw_out-Ptelf1 = lw_knvk-telf1.
      lw_out-PName1 = lw_knvk-name1.

      lw_out-bankn = lw_lfbk-bankn.
      lw_out-koinh = lw_lfbk-koinh.
  	  lw_out-BKREF = lw_lfbk-BKREF.


      lw_out-banka = lw_bnka-banka.
      lw_out-stras = lw_bnka-stras 
	  lw_out-brnch = lw_bnka-brnch.

                                   	CONTROLLER	                                   Table	                                   	Standard Table[0x2(62)]
                                   	LIFNR	                                   	0		CString{1}
                                   	KOINH	                                   	供应商2014123001	
                                   	BANKA	                                   	中国工商银行		
                                   	BANKN	                                   	5000023421123		
                                   	BKREF	                                   			CString{0}
                                   	STRAS	                                   			CString{0}
                                   	BRNCH	                                   	大坪支行		

```







