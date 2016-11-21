
<h1>Spring Declarative Transaction Management</h1>
<hr>
<p>Declarative transaction management approach allows you to manage the transaction with the help of configuration instead of hard coding in your source code. This means that you can separate transaction management from the business code. You only use annotations or XML based configuration to manage the transactions. The bean configuration will specify the methods to be transactional. Here are the steps associated with declarative transaction:</p>
<ul class="list">
<li><p>We use &lt;tx:advice /&gt; tag, which creates a transaction-handling advice and same time we define a pointcut that matches all methods we wish to make transactional and reference the transactional advice.</p></li>
<li><p>If a method name has been included in the transactional configuration then created advice will begin the transaction before calling the method.</p></li>
<li><p>Target method will be executed in a <i>try / catch</i> block.</p></li>
<li><p>If the method finishes normally, the AOP advice commits the transaction successfully otherwise it performs a rollback.</p></li>
</ul>
<p>Let us see how above mentioned steps work but before we begin, it is important to have at least two database tables on which we can perform various CRUD operations with the help of transactions. Let us take <b>Student</b> table, which can be created in MySQL TEST database with the following DDL:</p>
<pre class="prettyprint notranslate prettyprinted" style=""><span class="pln">CREATE TABLE </span><span class="typ">Student</span><span class="pun">(</span><span class="pln">
   ID   INT NOT NULL AUTO_INCREMENT</span><span class="pun">,</span><span class="pln">
   NAME VARCHAR</span><span class="pun">(</span><span class="lit">20</span><span class="pun">)</span><span class="pln"> NOT NULL</span><span class="pun">,</span><span class="pln">
   AGE  INT NOT NULL</span><span class="pun">,</span><span class="pln">
   PRIMARY KEY </span><span class="pun">(</span><span class="pln">ID</span><span class="pun">)</span><span class="pln">
</span><span class="pun">);</span></pre>
<p>Second table is <b>Marks</b> in which we will maintain marks for students based on years. Here <b>SID</b> is the foreign key for Student table.</p>
<pre class="prettyprint notranslate prettyprinted" style=""><span class="pln">CREATE TABLE </span><span class="typ">Marks</span><span class="pun">(</span><span class="pln">
   SID INT NOT NULL</span><span class="pun">,</span><span class="pln">
   MARKS  INT NOT NULL</span><span class="pun">,</span><span class="pln">
   YEAR   INT NOT NULL
</span><span class="pun">);</span></pre>
<p>Now let us write our Spring JDBC application which will implement simple operations on Student and Marks tables. Let us have working Eclipse IDE in place and follow the following steps to create a Spring application:</p>
<table class="table table-bordered">
<tbody><tr><th class="fivepct">Step</th><th>Description</th></tr>
<tr><td>1</td><td>Create a project with a name <i>SpringExample</i> and create a package <i>com.tutorialspoint</i> under the <b>src</b> folder in the created project.</td></tr>
<tr><td>2</td><td>Add required Spring libraries using <i>Add External JARs</i> option as explained in the <i>Spring Hello World Example</i> chapter.</td></tr>
<tr><td>3</td><td>Add other required libraries <i>mysql-connector-java.jar</i>, <i>aopalliance-x.y.jar</i>, <i>org.springframework.jdbc.jar</i>,  and  <i>org.springframework.transaction.jar</i> in the project. You can download required libraries if you do not have them already.</td></tr>
<tr><td>4</td><td>Create DAO interface <i>StudentDAO</i> and list down all the required methods. Though it is not required and you can directly write <i>StudentJDBCTemplate</i> class, but as a good practice, let's do it.</td></tr>
<tr><td>5</td><td>Create other required Java classes <i>StudentMarks</i>, <i>StudentMarksMapper</i>, <i>StudentJDBCTemplate</i> and <i>MainApp</i> under the <i>com.tutorialspoint</i> package. You can create rest of the POJO classes if required.</td></tr>
<tr><td>6</td><td>Make sure you already created <b>Student</b> and <b>Marks</b> tables in TEST database. Also make sure your MySQL server is working fine and you have read/write access on the database using the given username and password.</td></tr>
<tr><td>7</td><td>Create Beans configuration file <i>Beans.xml</i> under the <b>src</b> folder.</td></tr>
<tr><td>8</td><td>The final step is to create the content of all the Java files and Bean Configuration file and run the application as explained below.</td></tr>
</tbody></table>
<p>Following is the content of the Data Access Object interface file <b>StudentDAO.java</b>:</p>
<pre class="prettyprint notranslate prettyprinted" style=""><span class="kwd">package</span><span class="pln"> com</span><span class="pun">.</span><span class="pln">tutorialspoint</span><span class="pun">;</span><span class="pln">

</span><span class="kwd">import</span><span class="pln"> java</span><span class="pun">.</span><span class="pln">util</span><span class="pun">.</span><span class="typ">List</span><span class="pun">;</span><span class="pln">
</span><span class="kwd">import</span><span class="pln"> javax</span><span class="pun">.</span><span class="pln">sql</span><span class="pun">.</span><span class="typ">DataSource</span><span class="pun">;</span><span class="pln">

</span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">interface</span><span class="pln"> </span><span class="typ">StudentDAO</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
   </span><span class="com">/** 
    * This is the method to be used to initialize
    * database resources ie. connection.
    */</span><span class="pln">
   </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> setDataSource</span><span class="pun">(</span><span class="typ">DataSource</span><span class="pln"> ds</span><span class="pun">);</span><span class="pln">
   </span><span class="com">/** 
    * This is the method to be used to create
    * a record in the Student and Marks tables.
    */</span><span class="pln">
   </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> create</span><span class="pun">(</span><span class="typ">String</span><span class="pln"> name</span><span class="pun">,</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> age</span><span class="pun">,</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> marks</span><span class="pun">,</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> year</span><span class="pun">);</span><span class="pln">
   </span><span class="com">/** 
    * This is the method to be used to list down
    * all the records from the Student and Marks tables.
    */</span><span class="pln">
   </span><span class="kwd">public</span><span class="pln"> </span><span class="typ">List</span><span class="pun">&lt;</span><span class="typ">StudentMarks</span><span class="pun">&gt;</span><span class="pln"> listStudents</span><span class="pun">();</span><span class="pln">
</span><span class="pun">}</span></pre>
<p>Following is the content of the <b>StudentMarks.java</b> file:</p>
<pre class="prettyprint notranslate prettyprinted" style=""><span class="kwd">package</span><span class="pln"> com</span><span class="pun">.</span><span class="pln">tutorialspoint</span><span class="pun">;</span><span class="pln">

</span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">class</span><span class="pln"> </span><span class="typ">StudentMarks</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
   </span><span class="kwd">private</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> age</span><span class="pun">;</span><span class="pln">
   </span><span class="kwd">private</span><span class="pln"> </span><span class="typ">String</span><span class="pln"> name</span><span class="pun">;</span><span class="pln">
   </span><span class="kwd">private</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> id</span><span class="pun">;</span><span class="pln">
   </span><span class="kwd">private</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> marks</span><span class="pun">;</span><span class="pln">
   </span><span class="kwd">private</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> year</span><span class="pun">;</span><span class="pln">
   </span><span class="kwd">private</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> sid</span><span class="pun">;</span><span class="pln">

   </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> setAge</span><span class="pun">(</span><span class="typ">Integer</span><span class="pln"> age</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">this</span><span class="pun">.</span><span class="pln">age </span><span class="pun">=</span><span class="pln"> age</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">
   </span><span class="kwd">public</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> getAge</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">return</span><span class="pln"> age</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">

   </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> setName</span><span class="pun">(</span><span class="typ">String</span><span class="pln"> name</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">this</span><span class="pun">.</span><span class="pln">name </span><span class="pun">=</span><span class="pln"> name</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">
   </span><span class="kwd">public</span><span class="pln"> </span><span class="typ">String</span><span class="pln"> getName</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">return</span><span class="pln"> name</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">

   </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> setId</span><span class="pun">(</span><span class="typ">Integer</span><span class="pln"> id</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">this</span><span class="pun">.</span><span class="pln">id </span><span class="pun">=</span><span class="pln"> id</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">
   </span><span class="kwd">public</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> getId</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">return</span><span class="pln"> id</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">
   </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> setMarks</span><span class="pun">(</span><span class="typ">Integer</span><span class="pln"> marks</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">this</span><span class="pun">.</span><span class="pln">marks </span><span class="pun">=</span><span class="pln"> marks</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">
   </span><span class="kwd">public</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> getMarks</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">return</span><span class="pln"> marks</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">

   </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> setYear</span><span class="pun">(</span><span class="typ">Integer</span><span class="pln"> year</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">this</span><span class="pun">.</span><span class="pln">year </span><span class="pun">=</span><span class="pln"> year</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">
   </span><span class="kwd">public</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> getYear</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">return</span><span class="pln"> year</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">

   </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> setSid</span><span class="pun">(</span><span class="typ">Integer</span><span class="pln"> sid</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">this</span><span class="pun">.</span><span class="pln">sid </span><span class="pun">=</span><span class="pln"> sid</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">
   </span><span class="kwd">public</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> getSid</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">return</span><span class="pln"> sid</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">
</span><span class="pun">}</span></pre>
<p>Following is the content of the <b>StudentMarksMapper.java</b> file:</p>
<pre class="prettyprint notranslate prettyprinted" style=""><span class="kwd">package</span><span class="pln"> com</span><span class="pun">.</span><span class="pln">tutorialspoint</span><span class="pun">;</span><span class="pln">

</span><span class="kwd">import</span><span class="pln"> java</span><span class="pun">.</span><span class="pln">sql</span><span class="pun">.</span><span class="typ">ResultSet</span><span class="pun">;</span><span class="pln">
</span><span class="kwd">import</span><span class="pln"> java</span><span class="pun">.</span><span class="pln">sql</span><span class="pun">.</span><span class="typ">SQLException</span><span class="pun">;</span><span class="pln">
</span><span class="kwd">import</span><span class="pln"> org</span><span class="pun">.</span><span class="pln">springframework</span><span class="pun">.</span><span class="pln">jdbc</span><span class="pun">.</span><span class="pln">core</span><span class="pun">.</span><span class="typ">RowMapper</span><span class="pun">;</span><span class="pln">

</span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">class</span><span class="pln"> </span><span class="typ">StudentMarksMapper</span><span class="pln"> </span><span class="kwd">implements</span><span class="pln"> </span><span class="typ">RowMapper</span><span class="pun">&lt;</span><span class="typ">StudentMarks</span><span class="pun">&gt;</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
   </span><span class="kwd">public</span><span class="pln"> </span><span class="typ">StudentMarks</span><span class="pln"> mapRow</span><span class="pun">(</span><span class="typ">ResultSet</span><span class="pln"> rs</span><span class="pun">,</span><span class="pln"> </span><span class="kwd">int</span><span class="pln"> rowNum</span><span class="pun">)</span><span class="pln"> </span><span class="kwd">throws</span><span class="pln"> </span><span class="typ">SQLException</span><span class="pln"> </span><span class="pun">{</span><span class="pln">

      </span><span class="typ">StudentMarks</span><span class="pln"> studentMarks </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">StudentMarks</span><span class="pun">();</span><span class="pln">

      studentMarks</span><span class="pun">.</span><span class="pln">setId</span><span class="pun">(</span><span class="pln">rs</span><span class="pun">.</span><span class="pln">getInt</span><span class="pun">(</span><span class="str">"id"</span><span class="pun">));</span><span class="pln">
      studentMarks</span><span class="pun">.</span><span class="pln">setName</span><span class="pun">(</span><span class="pln">rs</span><span class="pun">.</span><span class="pln">getString</span><span class="pun">(</span><span class="str">"name"</span><span class="pun">));</span><span class="pln">
      studentMarks</span><span class="pun">.</span><span class="pln">setAge</span><span class="pun">(</span><span class="pln">rs</span><span class="pun">.</span><span class="pln">getInt</span><span class="pun">(</span><span class="str">"age"</span><span class="pun">));</span><span class="pln">
      studentMarks</span><span class="pun">.</span><span class="pln">setSid</span><span class="pun">(</span><span class="pln">rs</span><span class="pun">.</span><span class="pln">getInt</span><span class="pun">(</span><span class="str">"sid"</span><span class="pun">));</span><span class="pln">
      studentMarks</span><span class="pun">.</span><span class="pln">setMarks</span><span class="pun">(</span><span class="pln">rs</span><span class="pun">.</span><span class="pln">getInt</span><span class="pun">(</span><span class="str">"marks"</span><span class="pun">));</span><span class="pln">
      studentMarks</span><span class="pun">.</span><span class="pln">setYear</span><span class="pun">(</span><span class="pln">rs</span><span class="pun">.</span><span class="pln">getInt</span><span class="pun">(</span><span class="str">"year"</span><span class="pun">));</span><span class="pln">

      </span><span class="kwd">return</span><span class="pln"> studentMarks</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">
</span><span class="pun">}</span></pre>
<p>Following is the implementation class file <b>StudentJDBCTemplate.java</b> for the defined DAO interface StudentDAO:</p>
<pre class="prettyprint notranslate prettyprinted" style=""><span class="kwd">package</span><span class="pln"> com</span><span class="pun">.</span><span class="pln">tutorialspoint</span><span class="pun">;</span><span class="pln">

</span><span class="kwd">import</span><span class="pln"> java</span><span class="pun">.</span><span class="pln">util</span><span class="pun">.</span><span class="typ">List</span><span class="pun">;</span><span class="pln">
</span><span class="kwd">import</span><span class="pln"> javax</span><span class="pun">.</span><span class="pln">sql</span><span class="pun">.</span><span class="typ">DataSource</span><span class="pun">;</span><span class="pln">
</span><span class="kwd">import</span><span class="pln"> org</span><span class="pun">.</span><span class="pln">springframework</span><span class="pun">.</span><span class="pln">dao</span><span class="pun">.</span><span class="typ">DataAccessException</span><span class="pun">;</span><span class="pln">
</span><span class="kwd">import</span><span class="pln"> org</span><span class="pun">.</span><span class="pln">springframework</span><span class="pun">.</span><span class="pln">jdbc</span><span class="pun">.</span><span class="pln">core</span><span class="pun">.</span><span class="typ">JdbcTemplate</span><span class="pun">;</span><span class="pln">

</span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">class</span><span class="pln"> </span><span class="typ">StudentJDBCTemplate</span><span class="pln"> </span><span class="kwd">implements</span><span class="pln"> </span><span class="typ">StudentDAO</span><span class="pun">{</span><span class="pln">
   </span><span class="kwd">private</span><span class="pln"> </span><span class="typ">JdbcTemplate</span><span class="pln"> jdbcTemplateObject</span><span class="pun">;</span><span class="pln">

   </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> setDataSource</span><span class="pun">(</span><span class="typ">DataSource</span><span class="pln"> dataSource</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">this</span><span class="pun">.</span><span class="pln">jdbcTemplateObject </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">JdbcTemplate</span><span class="pun">(</span><span class="pln">dataSource</span><span class="pun">);</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">

   </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> create</span><span class="pun">(</span><span class="typ">String</span><span class="pln"> name</span><span class="pun">,</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> age</span><span class="pun">,</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> marks</span><span class="pun">,</span><span class="pln"> </span><span class="typ">Integer</span><span class="pln"> year</span><span class="pun">){</span><span class="pln">

      </span><span class="kwd">try</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
         </span><span class="typ">String</span><span class="pln"> SQL1 </span><span class="pun">=</span><span class="pln"> </span><span class="str">"insert into Student (name, age) values (?, ?)"</span><span class="pun">;</span><span class="pln">
         jdbcTemplateObject</span><span class="pun">.</span><span class="pln">update</span><span class="pun">(</span><span class="pln"> SQL1</span><span class="pun">,</span><span class="pln"> name</span><span class="pun">,</span><span class="pln"> age</span><span class="pun">);</span><span class="pln">

         </span><span class="com">// Get the latest student id to be used in Marks table</span><span class="pln">
         </span><span class="typ">String</span><span class="pln"> SQL2 </span><span class="pun">=</span><span class="pln"> </span><span class="str">"select max(id) from Student"</span><span class="pun">;</span><span class="pln">
         </span><span class="kwd">int</span><span class="pln"> sid </span><span class="pun">=</span><span class="pln"> jdbcTemplateObject</span><span class="pun">.</span><span class="pln">queryForInt</span><span class="pun">(</span><span class="pln"> SQL2 </span><span class="pun">);</span><span class="pln">

         </span><span class="typ">String</span><span class="pln"> SQL3 </span><span class="pun">=</span><span class="pln"> </span><span class="str">"insert into Marks(sid, marks, year) "</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> 
                       </span><span class="str">"values (?, ?, ?)"</span><span class="pun">;</span><span class="pln">
         jdbcTemplateObject</span><span class="pun">.</span><span class="pln">update</span><span class="pun">(</span><span class="pln"> SQL3</span><span class="pun">,</span><span class="pln"> sid</span><span class="pun">,</span><span class="pln"> marks</span><span class="pun">,</span><span class="pln"> year</span><span class="pun">);</span><span class="pln">

         </span><span class="typ">System</span><span class="pun">.</span><span class="kwd">out</span><span class="pun">.</span><span class="pln">println</span><span class="pun">(</span><span class="str">"Created Name = "</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> name </span><span class="pun">+</span><span class="pln"> </span><span class="str">", Age = "</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> age</span><span class="pun">);</span><span class="pln">
         </span><span class="com">// to simulate the exception.</span><span class="pln">
         </span><span class="kwd">throw</span><span class="pln"> </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">RuntimeException</span><span class="pun">(</span><span class="str">"simulate Error condition"</span><span class="pun">)</span><span class="pln"> </span><span class="pun">;</span><span class="pln">
      </span><span class="pun">}</span><span class="pln"> </span><span class="kwd">catch</span><span class="pln"> </span><span class="pun">(</span><span class="typ">DataAccessException</span><span class="pln"> e</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
         </span><span class="typ">System</span><span class="pun">.</span><span class="kwd">out</span><span class="pun">.</span><span class="pln">println</span><span class="pun">(</span><span class="str">"Error in creating record, rolling back"</span><span class="pun">);</span><span class="pln">
         </span><span class="kwd">throw</span><span class="pln"> e</span><span class="pun">;</span><span class="pln">
      </span><span class="pun">}</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">

   </span><span class="kwd">public</span><span class="pln"> </span><span class="typ">List</span><span class="pun">&lt;</span><span class="typ">StudentMarks</span><span class="pun">&gt;</span><span class="pln"> listStudents</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="typ">String</span><span class="pln"> SQL </span><span class="pun">=</span><span class="pln"> </span><span class="str">"select * from Student, Marks where Student.id=Marks.sid"</span><span class="pun">;</span><span class="pln">

      </span><span class="typ">List</span><span class="pln"> </span><span class="pun">&lt;</span><span class="typ">StudentMarks</span><span class="pun">&gt;</span><span class="pln"> studentMarks</span><span class="pun">=</span><span class="pln">jdbcTemplateObject</span><span class="pun">.</span><span class="pln">query</span><span class="pun">(</span><span class="pln">SQL</span><span class="pun">,</span><span class="pln"> 
      </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">StudentMarksMapper</span><span class="pun">());</span><span class="pln">
      </span><span class="kwd">return</span><span class="pln"> studentMarks</span><span class="pun">;</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">
</span><span class="pun">}</span></pre>
<p>Now let us move with the main application file <b>MainApp.java</b>, which is as follows:</p>
<pre class="prettyprint notranslate prettyprinted" style=""><span class="kwd">package</span><span class="pln"> com</span><span class="pun">.</span><span class="pln">tutorialspoint</span><span class="pun">;</span><span class="pln">
</span><span class="kwd">import</span><span class="pln"> java</span><span class="pun">.</span><span class="pln">util</span><span class="pun">.</span><span class="typ">List</span><span class="pun">;</span><span class="pln">
</span><span class="kwd">import</span><span class="pln"> org</span><span class="pun">.</span><span class="pln">springframework</span><span class="pun">.</span><span class="pln">context</span><span class="pun">.</span><span class="typ">ApplicationContext</span><span class="pun">;</span><span class="pln">
</span><span class="kwd">import</span><span class="pln"> org</span><span class="pun">.</span><span class="pln">springframework</span><span class="pun">.</span><span class="pln">context</span><span class="pun">.</span><span class="pln">support</span><span class="pun">.</span><span class="typ">ClassPathXmlApplicationContext</span><span class="pun">;</span><span class="pln">

</span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">class</span><span class="pln"> </span><span class="typ">MainApp</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
   </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">static</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> main</span><span class="pun">(</span><span class="typ">String</span><span class="pun">[]</span><span class="pln"> args</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="typ">ApplicationContext</span><span class="pln"> context </span><span class="pun">=</span><span class="pln"> 
             </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">ClassPathXmlApplicationContext</span><span class="pun">(</span><span class="str">"Beans.xml"</span><span class="pun">);</span><span class="pln">

      </span><span class="typ">StudentDAO</span><span class="pln"> studentJDBCTemplate </span><span class="pun">=</span><span class="pln"> 
      </span><span class="pun">(</span><span class="typ">StudentDAO</span><span class="pun">)</span><span class="pln">context</span><span class="pun">.</span><span class="pln">getBean</span><span class="pun">(</span><span class="str">"studentJDBCTemplate"</span><span class="pun">);</span><span class="pln">
      
      </span><span class="typ">System</span><span class="pun">.</span><span class="kwd">out</span><span class="pun">.</span><span class="pln">println</span><span class="pun">(</span><span class="str">"------Records creation--------"</span><span class="pln"> </span><span class="pun">);</span><span class="pln">
      studentJDBCTemplate</span><span class="pun">.</span><span class="pln">create</span><span class="pun">(</span><span class="str">"Zara"</span><span class="pun">,</span><span class="pln"> </span><span class="lit">11</span><span class="pun">,</span><span class="pln"> </span><span class="lit">99</span><span class="pun">,</span><span class="pln"> </span><span class="lit">2010</span><span class="pun">);</span><span class="pln">
      studentJDBCTemplate</span><span class="pun">.</span><span class="pln">create</span><span class="pun">(</span><span class="str">"Nuha"</span><span class="pun">,</span><span class="pln"> </span><span class="lit">20</span><span class="pun">,</span><span class="pln"> </span><span class="lit">97</span><span class="pun">,</span><span class="pln"> </span><span class="lit">2010</span><span class="pun">);</span><span class="pln">
      studentJDBCTemplate</span><span class="pun">.</span><span class="pln">create</span><span class="pun">(</span><span class="str">"Ayan"</span><span class="pun">,</span><span class="pln"> </span><span class="lit">25</span><span class="pun">,</span><span class="pln"> </span><span class="lit">100</span><span class="pun">,</span><span class="pln"> </span><span class="lit">2011</span><span class="pun">);</span><span class="pln">

      </span><span class="typ">System</span><span class="pun">.</span><span class="kwd">out</span><span class="pun">.</span><span class="pln">println</span><span class="pun">(</span><span class="str">"------Listing all the records--------"</span><span class="pln"> </span><span class="pun">);</span><span class="pln">
      </span><span class="typ">List</span><span class="pun">&lt;</span><span class="typ">StudentMarks</span><span class="pun">&gt;</span><span class="pln"> studentMarks </span><span class="pun">=</span><span class="pln"> studentJDBCTemplate</span><span class="pun">.</span><span class="pln">listStudents</span><span class="pun">();</span><span class="pln">
      </span><span class="kwd">for</span><span class="pln"> </span><span class="pun">(</span><span class="typ">StudentMarks</span><span class="pln"> record </span><span class="pun">:</span><span class="pln"> studentMarks</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
         </span><span class="typ">System</span><span class="pun">.</span><span class="kwd">out</span><span class="pun">.</span><span class="kwd">print</span><span class="pun">(</span><span class="str">"ID : "</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> record</span><span class="pun">.</span><span class="pln">getId</span><span class="pun">()</span><span class="pln"> </span><span class="pun">);</span><span class="pln">
         </span><span class="typ">System</span><span class="pun">.</span><span class="kwd">out</span><span class="pun">.</span><span class="kwd">print</span><span class="pun">(</span><span class="str">", Name : "</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> record</span><span class="pun">.</span><span class="pln">getName</span><span class="pun">()</span><span class="pln"> </span><span class="pun">);</span><span class="pln">
         </span><span class="typ">System</span><span class="pun">.</span><span class="kwd">out</span><span class="pun">.</span><span class="kwd">print</span><span class="pun">(</span><span class="str">", Marks : "</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> record</span><span class="pun">.</span><span class="pln">getMarks</span><span class="pun">());</span><span class="pln">
         </span><span class="typ">System</span><span class="pun">.</span><span class="kwd">out</span><span class="pun">.</span><span class="kwd">print</span><span class="pun">(</span><span class="str">", Year : "</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> record</span><span class="pun">.</span><span class="pln">getYear</span><span class="pun">());</span><span class="pln">
         </span><span class="typ">System</span><span class="pun">.</span><span class="kwd">out</span><span class="pun">.</span><span class="pln">println</span><span class="pun">(</span><span class="str">", Age : "</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> record</span><span class="pun">.</span><span class="pln">getAge</span><span class="pun">());</span><span class="pln">
      </span><span class="pun">}</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">
</span><span class="pun">}</span></pre> 
<p>Following is the configuration file <b>Beans.xml</b>:</p> 
<pre class="prettyprint notranslate prettyprinted" style=""><span class="pun">&lt;?</span><span class="pln">xml version</span><span class="pun">=</span><span class="str">"1.0"</span><span class="pln"> encoding</span><span class="pun">=</span><span class="str">"UTF-8"</span><span class="pun">?&gt;</span><span class="pln">
</span><span class="tag">&lt;beans</span><span class="pln"> </span><span class="atn">xmlns</span><span class="pun">=</span><span class="atv">"http://www.springframework.org/schema/beans"</span><span class="pln">
   </span><span class="atn">xmlns:xsi</span><span class="pun">=</span><span class="atv">"http://www.w3.org/2001/XMLSchema-instance"</span><span class="pln">
   </span><span class="atn">xmlns:tx</span><span class="pun">=</span><span class="atv">"http://www.springframework.org/schema/tx"</span><span class="pln">
   </span><span class="atn">xmlns:aop</span><span class="pun">=</span><span class="atv">"http://www.springframework.org/schema/aop"</span><span class="pln">
   </span><span class="atn">xsi:schemaLocation</span><span class="pun">=</span><span class="atv">"http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
   http://www.springframework.org/schema/tx
   http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
   http://www.springframework.org/schema/aop
   http://www.springframework.org/schema/aop/spring-aop-3.0.xsd"</span><span class="tag">&gt;</span><span class="pln">

   </span><span class="com">&lt;!-- Initialization for data source --&gt;</span><span class="pln">
   </span><span class="tag">&lt;bean</span><span class="pln"> </span><span class="atn">id</span><span class="pun">=</span><span class="atv">"dataSource"</span><span class="pln"> 
      </span><span class="atn">class</span><span class="pun">=</span><span class="atv">"org.springframework.jdbc.datasource.DriverManagerDataSource"</span><span class="tag">&gt;</span><span class="pln">
      </span><span class="tag">&lt;property</span><span class="pln"> </span><span class="atn">name</span><span class="pun">=</span><span class="atv">"driverClassName"</span><span class="pln"> </span><span class="atn">value</span><span class="pun">=</span><span class="atv">"com.mysql.jdbc.Driver"</span><span class="tag">/&gt;</span><span class="pln">
      </span><span class="tag">&lt;property</span><span class="pln"> </span><span class="atn">name</span><span class="pun">=</span><span class="atv">"url"</span><span class="pln"> </span><span class="atn">value</span><span class="pun">=</span><span class="atv">"jdbc:mysql://localhost:3306/TEST"</span><span class="tag">/&gt;</span><span class="pln">
      </span><span class="tag">&lt;property</span><span class="pln"> </span><span class="atn">name</span><span class="pun">=</span><span class="atv">"username"</span><span class="pln"> </span><span class="atn">value</span><span class="pun">=</span><span class="atv">"root"</span><span class="tag">/&gt;</span><span class="pln">
      </span><span class="tag">&lt;property</span><span class="pln"> </span><span class="atn">name</span><span class="pun">=</span><span class="atv">"password"</span><span class="pln"> </span><span class="atn">value</span><span class="pun">=</span><span class="atv">"cohondob"</span><span class="tag">/&gt;</span><span class="pln">
   </span><span class="tag">&lt;/bean&gt;</span><span class="pln">
  
   </span><span class="tag">&lt;tx:advice</span><span class="pln"> </span><span class="atn">id</span><span class="pun">=</span><span class="atv">"txAdvice"</span><span class="pln">  </span><span class="atn">transaction-manager</span><span class="pun">=</span><span class="atv">"transactionManager"</span><span class="tag">&gt;</span><span class="pln">
      </span><span class="tag">&lt;tx:attributes&gt;</span><span class="pln">
      </span><span class="tag">&lt;tx:method</span><span class="pln"> </span><span class="atn">name</span><span class="pun">=</span><span class="atv">"create"</span><span class="tag">/&gt;</span><span class="pln">
      </span><span class="tag">&lt;/tx:attributes&gt;</span><span class="pln">
   </span><span class="tag">&lt;/tx:advice&gt;</span><span class="pln">
	
   </span><span class="tag">&lt;aop:config&gt;</span><span class="pln">
      </span><span class="tag">&lt;aop:pointcut</span><span class="pln"> </span><span class="atn">id</span><span class="pun">=</span><span class="atv">"createOperation"</span><span class="pln"> 
      </span><span class="atn">expression</span><span class="pun">=</span><span class="atv">"execution(* com.tutorialspoint.StudentJDBCTemplate.create(..))"</span><span class="tag">/&gt;</span><span class="pln">
      </span><span class="tag">&lt;aop:advisor</span><span class="pln"> </span><span class="atn">advice-ref</span><span class="pun">=</span><span class="atv">"txAdvice"</span><span class="pln"> </span><span class="atn">pointcut-ref</span><span class="pun">=</span><span class="atv">"createOperation"</span><span class="tag">/&gt;</span><span class="pln">
   </span><span class="tag">&lt;/aop:config&gt;</span><span class="pln">
	
   </span><span class="com">&lt;!-- Initialization for TransactionManager --&gt;</span><span class="pln">
   </span><span class="tag">&lt;bean</span><span class="pln"> </span><span class="atn">id</span><span class="pun">=</span><span class="atv">"transactionManager"</span><span class="pln">
   </span><span class="atn">class</span><span class="pun">=</span><span class="atv">"org.springframework.jdbc.datasource.DataSourceTransactionManager"</span><span class="tag">&gt;</span><span class="pln">
      </span><span class="tag">&lt;property</span><span class="pln"> </span><span class="atn">name</span><span class="pun">=</span><span class="atv">"dataSource"</span><span class="pln">  </span><span class="atn">ref</span><span class="pun">=</span><span class="atv">"dataSource"</span><span class="pln"> </span><span class="tag">/&gt;</span><span class="pln">    
   </span><span class="tag">&lt;/bean&gt;</span><span class="pln">

   </span><span class="com">&lt;!-- Definition for studentJDBCTemplate bean --&gt;</span><span class="pln">
   </span><span class="tag">&lt;bean</span><span class="pln"> </span><span class="atn">id</span><span class="pun">=</span><span class="atv">"studentJDBCTemplate"</span><span class="pln">  
   </span><span class="atn">class</span><span class="pun">=</span><span class="atv">"com.tutorialspoint.StudentJDBCTemplate"</span><span class="tag">&gt;</span><span class="pln">
      </span><span class="tag">&lt;property</span><span class="pln"> </span><span class="atn">name</span><span class="pun">=</span><span class="atv">"dataSource"</span><span class="pln">  </span><span class="atn">ref</span><span class="pun">=</span><span class="atv">"dataSource"</span><span class="pln"> </span><span class="tag">/&gt;</span><span class="pln">  
   </span><span class="tag">&lt;/bean&gt;</span><span class="pln">

</span><span class="tag">&lt;/beans&gt;</span></pre> 
<p>Once you are done with creating source and bean configuration files, let us run the application. If everything is fine with your application, this will print the following exception will be raised. In this case transaction will be rolled back and no record will be created in the database table.</p>
<pre class="result notranslate">------Records creation--------
Created Name = Zara, Age = 11
Exception in thread "main" java.lang.RuntimeException: simulate Error condition
</pre>
<p>You can try above example after removing exception, and in this case it should commit the transaction and you should see a record in the database.</p>
<hr>



