# ProcedureTemporaryTableTree
Mysql 存储过程，零时表技术研究


![](https://i.imgur.com/ecaWbwB.png)

Java代码调用存储过程

![](https://i.imgur.com/yRhTs4M.png)

<pre>
存储过程

          存储过程就是一组SQL语句集合，功能强大，可以实现一些比较复杂的逻辑功能，类似于Java
      语言中的语法；
 
          特性：
              1）输入输出参数，可声明变量，有if,else,case,while等控制语句，通过编写存储
                 过程，可以实现复杂的逻辑功能。
              2）函数的普遍特性：模块化，封装，代码复用。
              3）速度快：只有首次执行需要经过编译和优化步骤，后续调用直接执行。

      存储过程的优缺点：

          优点：
              1）在生产环境中，可以通过直接修改存储过程的方式修改业务逻辑，而不需要重启服务
                 器，但是如果修改的存储过程没有完整的验证过，造成的后果非常严重。
              2）执行速度快：存储过程经过编译之后会比单独一条条SQL语句执行要快。但是也没有
                 太大影响。
              3）减少网络传输：存错过程直接就存在数据库服务器上跑，所有的数据访问都在服务器
                 内部进行，不需要传输数据到其他终端。但我们的应用服务器通常与数据库服务器是
                 在同一内网，大数据的访问的瓶颈会是硬盘的速度，而不是网络。
              5）方便DBA优化。

          缺点：
              1）本质上是过程化的。
              2）不便于调试。
              3）没办法应用缓存。
              5）无法适应数据库的切割
</pre>


![](https://i.imgur.com/je1qwMZ.png)

![](https://i.imgur.com/fjJaLeG.png)

<pre>
函数
 
      存储过程与函数比较：
          1）存储过程实现的功能要复杂一些，而函数的实现的功能针对性比较强。存储过程功能强大，
             可以执行包括修改表等一些列数据库操作。
          2）存储过程一般是作为一个独立的部分来执行，而函数可以作为查询语句的一个部分来调用。     
</pre>


<pre>
零时表

         当工作在非常大的表上时，可能偶尔需要运行很多查询获得一个大量数据的小的子集，不是对
      整个表运行这些查询，而是让Mysql每次找出所需的少数记录，将记录选择到一个零时表可能更
      快些，然后在这些表运行查询。

         创建零时表语句：
               CREATE TEMPORARY TABLE tmp_table (
                      name VARCHAR(10) NOT NULL,
                      value INTEGER NOT NULL
               )

         零时表将在连接Mysql期间存在，当断开Mysql连接时，Mysql将自动删除零时表并释放所用的
      空间。

      Mysql零时表分类：
           1）外部零时表：
              通过Create temporary table创建的零时表，这种零时表称为外部零时表，这种零时
              表支队当前用户课件，当前会话结束的时候，该零时表会自动关闭，这种零时表的命名
              可以与非零时表同名（同名后非零时表将对当前用户不可见，直到同名零时表被删除）。

           2）内部零时表：

                  内部零时表是一种特殊轻量级的零时表，用来进行性能优化。这种零时表会被MySQL
              自动创建并用来存储某些操作的中间结果。这些操作可能包括在优化阶段或者执行阶段。
              这种内部表对用户来说是不可见的，但是通过explain 或者show status可以查看
              Mysql是否使用了内部零时表用来完成某个操作。内部零时表在sql语句优化的过程中
              扮演非常重要的角色，Mysql中的很多操作都要依赖内部零时表来进行优化。但是使用
              内部零时表需要创建表以及中间数据存取的代价，所以用户在写SQL语句的时候应该
              尽量的去避免使用零时表。

              内部零时表分类：
                   1）Heap零时表
                      这种零时表的所有数据都会存在内存中。
                   2）OnDisk零时表
                      这种零时表将数据全部存在磁盘上。
</pre>

<pre>
触发器：

      创建触发器来使班级表中的学生总数随着学生表中人数的增加而自动增加         

      DELIMITER $
      create trigger tri_stuInsert after insert
      on student for each row
      begin
      declare c int;
        set c = (select stuCount from class where classID=new.classID);
        update class set stuCount = c + 1 where classID = new.classID;
      end$
      DELIMITER ;

      触发器分类：
               1）after触发器
               2）before触发器
</pre>