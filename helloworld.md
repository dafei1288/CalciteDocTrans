# 前言

说不定期更新，就不定期更新：)。

在翻译[关系代数](https://github.com/dafei1288/CalciteDocTrans/blob/master/algebra.md)这篇文档的时候，总有一种惴惴不安的感觉伴随着我，其实还是对之前[概览](https://github.com/dafei1288/CalciteDocTrans/blob/master/tutorial.md)的一知半解，而DEMO项目`Calcite-example-CSV`为了介绍特性，添加了太多代码进来，这虽然很好，因为当你执行代码的时候，就能看到所有特性，但是对于一个新手来讲却未必够友好，我也是这样的一个新手，看着文档里不知所云的概念和代码片段，经常会有挫败感。那不如我们就来实实在在的完成一个`Helloworld`来查询一个表（当然这个表示我们自己定义的格式）就这么简单。来体会一下`Calcite`的魅力吧。

这里我们的目标是：

1. 数据在一个自己可控的位置，本文写在一个Java文件的静态块里 
1. 可以执行一个简单查询

# model.json

我习惯gradle，所以起手构建一个空白gradle项目，添加依赖：
`compile group: 'org.apache.calcite', name: 'calcite-core', version: '1.17.0'`

在`resources`下构建一个`bookshop.json`：
```
{
  "version": "1.0",
  "defaultSchema": "bookshop",
  "schemas": [
    {
      "type": "custom",
      "name": "bookshop",
      "factory": "com.dafei1288.calcite.InMemorySchemaFactory",
      "operand": {
        "p1": "hello",
        "p2": "world"
      }
    }
  ]
}
```
首先给库定义一个名字：`"defaultSchema": "bookshop"`
然后描述类型`"type": "custom"`，自定义类型，其他还包括`table`，`view`等
接下来`"factory": "com.dafei1288.calcite.InMemorySchemaFactory"`相当于定义我们程序的入口，如何加载一个`schema`

在构想初期只是想实现一个简单的bookshop数据库，后面在`Storage`介绍里，也会提到，我设计了2张表，`book`和`author`。

# InMemorySchemaFactory

首先让我们来看一下代码：
```
public class InMemorySchemaFactory implements SchemaFactory {
    @Override
    public Schema create(SchemaPlus parentSchema, String name, Map<String, Object> operand) {


        System.out.println("schema name ==>  "+ name);
        System.out.println("operand ==> "+operand);

        return new InMemorySchema(name,operand);
    }
}
```
因为在`bookshop.json`里定义了属性`"factory": "com.dafei1288.calcite.InMemorySchemaFactory"`，所以`InMemorySchemaFactory`被默认加载，该类需要继承`SchemaFactory`,重写`create`方法的时候，可以根据自己需要来构建逻辑，这里我们只打印了几个参数看一眼，就略过，实例化一个`InMemorySchema`。

# InMemorySchema
我们还是先把代码贴上：
```
public class InMemorySchema extends AbstractSchema {
    private String dbName;
    private Map<String, Object> operand;

    public InMemorySchema(String name, Map<String, Object> operand) {
        this.operand = operand;
        this.dbName = dbName;
        System.out.println("");
        System.out.println("in this class ==> "+ this);

    }
    @Override
    public Map<String, Table> getTableMap() {

        Map<String, Table> tables = new HashMap<String, Table>();

        Storage.getTables().forEach(it->{
            //System.out.println("it = "+it.getName());
            tables.put(it.getName(),new InMemoryTable(it. getName(),it));
        });

        return tables;
    }
}
```
`InMemorySchema`类也是相当简单的，首先继承`AbstractSchema`,实际上需要复写的`getTableMap`就是这个方法，它的职责就是要提供一个表名和表的映射表，为了实现这个，我们需要做一些处理，当然本例里是使用了一个`Storage`类，来模拟存储表结构信息，以及数据的，这里的表结构以及其他信息都不需要外接再提供额外辅助，如果是使用其他类型的，就可能需要根据自己的实际需求，扩展`operand`属性，来携带必要参数进来了。

`Storage`直接提供了`getTables`方法，可以直接从里面获取到当前存在的表，这样直接将`Storage`内的表转化成`InMemoryTable`类就可以了。

# InMemoryTable
还是先从代码入手：
```
public class InMemoryTable extends AbstractTable implements ScannableTable {
    private String name;
    private Storage.DummyTable _table;
    private RelDataType dataType;

    InMemoryTable(String name){
        System.out.println("InMemoryTable !!!!!!    "+name );
        this.name = name;
    }

    public InMemoryTable(String name, Storage.DummyTable it) {
        this.name = name;
        this._table = it;
    }

    @Override
    public RelDataType getRowType(RelDataTypeFactory typeFactory) {
//        System.out.println("RelDataType !!!!!!");
        if(dataType == null) {
            RelDataTypeFactory.FieldInfoBuilder fieldInfo = typeFactory.builder();
            for (Storage.DummyColumn column : this._table.getColumns()) {
                RelDataType sqlType = typeFactory.createJavaType(
                        String.class);
                sqlType = SqlTypeUtil.addCharsetAndCollation(sqlType, typeFactory);
//                System.out.println(column.getName()+" / "+sqlType);
                fieldInfo.add(column.getName(), sqlType);
            }
            this.dataType = typeFactory.createStructType(fieldInfo);
        }
        return this.dataType;
    }


    @Override
    public Enumerable<Object[]> scan(DataContext root) {
        System.out.println("scan ...... ");
        return new AbstractEnumerable<Object[]>() {
            public Enumerator<Object[]> enumerator() {
                return new Enumerator<Object[]>(){
                    private int cur = 0;
                    @Override
                    public Object[] current() {
//                        System.out.println("cur = "+cur+" => ");
//                        for (int i =0;i<_table.getData(cur).length;i++){
//                            System.out.println(_table.getData(cur)[i]);
//                        }
                        return _table.getData(cur++);
                    }

                    @Override
                    public boolean moveNext() {
//                        System.out.println("++cur < _table.getRowCount() = "+(cur+1 < _table.getRowCount()));
                        return cur < _table.getRowCount() ;
                    }

                    @Override
                    public void reset() {

                    }

                    @Override
                    public void close() {

                    }
                };
            }
        };
    }
}
```
这里我保留了很多难看的`System.out`,其实也是为了展示一下我走过的弯路，在这里面，遇到奇奇怪怪的坑，由于`Calcite`的结构原因，有时出错从日志上很难发现原因，或者说很难准确断定原因，当然也许是笔者水平所限的缘故。
`InMemoryTable`需要继承`AbstractTable`实现`ScannableTable`的接口，在这里`Calcite`提供了几种`Table`接口，待日后分解。这个类里，我们主要需要处理的2个方法`public RelDataType getRowType(RelDataTypeFactory typeFactory)`和`public Enumerable<Object[]> scan(DataContext root)`.

`getRowType`用来处理列的类型的，不要被那几句代码所迷惑，为了顺利运行，并没有针对数据的类型做什么处理，而是简单粗暴了使用了String，有兴趣的话，可以根据自己的实际情况来注册，日后有机会会详细介绍这部分。
`scan`这个方法相对复杂一点，提供了全表扫面的功能，这里主要需要高速引擎，如何遍历及获取数据。其结构还是比较复杂得，为了减少本例中类的个数，避免复杂得代码结构，吓跑初学者，所以，采用了内部类嵌套的形式，含义还是比较明确的。 主要就是实现`current`和`moveNext`方法。这里还是由`Storage`提供了数据的存储功能，所以只需要遍历，获取一下数据而已，其他方法暂时不管。

写到这，其实和`Calcite`相关的代码已经完成了，整个工程的主体代码也完成了，现在只需要再介绍一下`Storage`

# Storage
```
/**
 * 用于模拟数据库结构及数据
 *
 * author : id,name,age
 * book : id,aid,name,type
 * */
public class Storage {
    public static final String SCHEMA_NAME = "bookshop";
    public static final String TABLE_AUTHOR = "AUTHOR";
    public static final String TABLE_BOOK = "BOOK";

//    public static List<DummyTable> tables = new ArrayList<>();
    public static Hashtable<String,DummyTable> _bag = new Hashtable<>();
    static{
        DummyTable author = new DummyTable(TABLE_AUTHOR);
        DummyColumn id = new DummyColumn("ID","String");
        DummyColumn name = new DummyColumn("NAME","String");
        DummyColumn age = new DummyColumn("AGE","String");
        DummyColumn aid = new DummyColumn("AID","String");
        DummyColumn type = new DummyColumn("TYPE","String");
        author.addColumn(id).addColumn(name).addColumn(age);
        author.addRow("1","jacky","33");
        author.addRow("2","wang","23");
        author.addRow("3","dd","32");
        author.addRow("4","ma","42");
//        tables.add(author);
        _bag.put(TABLE_AUTHOR,author);

        DummyTable book = new DummyTable(TABLE_BOOK);
        book.addColumn(id).addColumn(name).addColumn(aid).addColumn(type);
        book.addRow("1","1","数据山","java");
        book.addRow("2","2","大关","sql");
        book.addRow("3","1","lili","sql");
        book.addRow("4","3","ten","c#");
//        tables.add(book);
        _bag.put(TABLE_BOOK,book);
    }

    public static Collection<DummyTable> getTables(){
        return _bag.values();
    }
    public static DummyTable getTable(String tableName){return _bag.get(tableName);}

    public static class DummyTable{
      private String name;
      private List<DummyColumn> columns;
      private List<List<Object>> datas = new ArrayList<>();
      DummyTable(String name){
          this.name = name;
      }

      public String getName(){
          return this.name;
      }

        public List<DummyColumn> getColumns() {
            return columns;
        }

        public DummyTable addColumn(DummyColumn dc){
          if(this.columns == null){
              this.columns = new ArrayList<>();
          }
          this.columns.add(dc);
          return this;
        }

        public void setColumns(List<DummyColumn> columns) {
            this.columns = columns;
        }

        public Object[] getData(int index){
          return this.datas.get(index).toArray();
        }

        public int getRowCount(){
          return this.datas.size();
        }

        public void addRow(Object...objects){
          this.datas.add(Arrays.asList(objects));
        }


    }

    public static class DummyColumn{
        private String name;
        private String type;

        public DummyColumn(String name, String type) {
            this.name = name;
            this.type = type;
        }

        public String getName() {
            return name;
        }

        public String getType() {
            return type;
        }

        public void setName(String name) {
            this.name = name;
        }

        public void setType(String type) {
            this.type = type;
        }
    }

}
```
这里我们用了一个简单的结构来模拟了存储，`Storage`下面包含`DummyTable`,`DummyTable`包含`DummyColumn`,用于存放元数据信息，而数据则包含在一个`List<List<Object>>`里，各类都提供基础的`getter`和`setter`方法，数据初始化则写在静态块里。

# 测试
写个main方法测试一下：
```
public static void main(String[] args) {
        try {
            Class.forName("org.apache.calcite.jdbc.Driver");
        } catch (ClassNotFoundException e1) {
            e1.printStackTrace();
        }

        Properties info = new Properties();
        String jsonmodle = "E:\\working\\others\\写作\\calcitetutorial\\src\\main\\resources\\bookshop.json";
        try {
            Connection connection =
                    DriverManager.getConnection("jdbc:calcite:model="+jsonmodle, info);
            CalciteConnection calciteConn = connection.unwrap(CalciteConnection.class);

            ResultSet result = connection.getMetaData().getTables(null, null, null, null);
            while(result.next()) {
                System.out.println("Catalog : " + result.getString(1) + ",Database : " + result.getString(2) + ",Table : " + result.getString(3));
            }
            result.close();
            Statement st = connection.createStatement();
            result = st.executeQuery("select * from book as b");
            while(result.next()) {
                System.out.println(result.getString(1) + "\t" + result.getString(2) + "\t" + result.getString(3));
            }
            result.close();
            //connection.close();
            st = connection.createStatement();
            result = st.executeQuery("select a.name from author as a");
            while(result.next()) {
                System.out.println(result.getString(1));
            }
            result.close();
            connection.close();
        }catch(Exception e){
            e.printStackTrace();
        }
    }
```

# 后记

目前只提供了全表扫面，条件判断表连接都还不行，待日后更新。代码库传送门
:
https://github.com/dafei1288/CalciteHelloworld.git