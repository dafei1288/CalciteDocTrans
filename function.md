# 直播改BUG

## 修复内联查询

在上期撰写文章的时候，我还是认为这里只完成了单表查询，但是几天得研究发现，通过上次那寥寥数十行代码，已经可以完成了表连接，过滤等功能了，只是由于当时粗心写错了一些东西，造成过滤失效了，下面我来剖析一下问题。

在`Storage.java`这个文件里
```
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
    ......
}
```
只截取了部分片段， 在`book.addColumn(id).addColumn(name).addColumn(aid).addColumn(type);`这里，我把`name`列和`aid`列写颠倒了。更正过来，就可以进行正确的连接查询了。

## 过滤条件

在可以进行内联查询以后，我一直在对不能做过滤这点存在质疑，从关系代数的角度分析，应该是先做笛卡尔积，然后再过滤数据，那么就应该可以对数据进行过滤了，那么问题出在哪呢？

于是抱着试试看的心里，构建了一条查询`select * from "BOOK" as b where b.name = 数据山`，看看效果，果不其然，喜闻乐见...

```
java.sql.SQLException: Error while executing SQL "select * from "BOOK" as b where b.name = 数据山": From line 1, column 42 to line 1, column 44: Column '数据山' not found in any table
	at org.apache.calcite.avatica.Helper.createException(Helper.java:56)
	at org.apache.calcite.avatica.Helper.createException(Helper.java:41)
	at org.apache.calcite.avatica.AvaticaStatement.executeInternal(AvaticaStatement.java:163)
	at org.apache.calcite.avatica.AvaticaStatement.executeQuery(AvaticaStatement.java:227)
	at com.dafei1288.calcite.TestJDBC.main(TestJDBC.java:81)
Caused by: org.apache.calcite.runtime.CalciteContextException: From line 1, column 42 to line 1, column 44: Column '数据山' not found in any table
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:422)
	at org.apache.calcite.runtime.Resources$ExInstWithCause.ex(Resources.java:463)
	at org.apache.calcite.sql.SqlUtil.newContextException(SqlUtil.java:783)
	at org.apache.calcite.sql.SqlUtil.newContextException(SqlUtil.java:768)
	at org.apache.calcite.sql.validate.SqlValidatorImpl.newValidationError(SqlValidatorImpl.java:4759)
	at org.apache.calcite.sql.validate.DelegatingScope.fullyQualify(DelegatingScope.java:259)
	at org.apache.calcite.sql.validate.SqlValidatorImpl$Expander.visit(SqlValidatorImpl.java:5619)
	at org.apache.calcite.sql.validate.SqlValidatorImpl$Expander.visit(SqlValidatorImpl.java:5601)
	at org.apache.calcite.sql.SqlIdentifier.accept(SqlIdentifier.java:334)
	at org.apache.calcite.sql.util.SqlShuttle$CallCopyingArgHandler.visitChild(SqlShuttle.java:134)
	at org.apache.calcite.sql.util.SqlShuttle$CallCopyingArgHandler.visitChild(SqlShuttle.java:101)
	at org.apache.calcite.sql.SqlOperator.acceptCall(SqlOperator.java:859)
	at org.apache.calcite.sql.validate.SqlValidatorImpl$Expander.visitScoped(SqlValidatorImpl.java:5654)
	at org.apache.calcite.sql.validate.SqlScopedShuttle.visit(SqlScopedShuttle.java:50)
	at org.apache.calcite.sql.validate.SqlScopedShuttle.visit(SqlScopedShuttle.java:33)
	at org.apache.calcite.sql.SqlCall.accept(SqlCall.java:138)
	at org.apache.calcite.sql.validate.SqlValidatorImpl.expand(SqlValidatorImpl.java:5208)
	at org.apache.calcite.sql.validate.SqlValidatorImpl.validateWhereClause(SqlValidatorImpl.java:3948)
	at org.apache.calcite.sql.validate.SqlValidatorImpl.validateSelect(SqlValidatorImpl.java:3276)
	at org.apache.calcite.sql.validate.SelectNamespace.validateImpl(SelectNamespace.java:60)
	at org.apache.calcite.sql.validate.AbstractNamespace.validate(AbstractNamespace.java:84)
	at org.apache.calcite.sql.validate.SqlValidatorImpl.validateNamespace(SqlValidatorImpl.java:967)
	at org.apache.calcite.sql.validate.SqlValidatorImpl.validateQuery(SqlValidatorImpl.java:943)
	at org.apache.calcite.sql.SqlSelect.validate(SqlSelect.java:225)
	at org.apache.calcite.sql.validate.SqlValidatorImpl.validateScopedExpression(SqlValidatorImpl.java:918)
	at org.apache.calcite.sql.validate.SqlValidatorImpl.validate(SqlValidatorImpl.java:628)
	at org.apache.calcite.sql2rel.SqlToRelConverter.convertQuery(SqlToRelConverter.java:552)
	at org.apache.calcite.prepare.Prepare.prepareSql(Prepare.java:264)
	at org.apache.calcite.prepare.Prepare.prepareSql(Prepare.java:230)
	at org.apache.calcite.prepare.CalcitePrepareImpl.prepare2_(CalcitePrepareImpl.java:772)
	at org.apache.calcite.prepare.CalcitePrepareImpl.prepare_(CalcitePrepareImpl.java:636)
	at org.apache.calcite.prepare.CalcitePrepareImpl.prepareSql(CalcitePrepareImpl.java:606)
	at org.apache.calcite.jdbc.CalciteConnectionImpl.parseQuery(CalciteConnectionImpl.java:229)
	at org.apache.calcite.jdbc.CalciteMetaImpl.prepareAndExecute(CalciteMetaImpl.java:550)
	at org.apache.calcite.avatica.AvaticaConnection.prepareAndExecuteInternal(AvaticaConnection.java:675)
	at org.apache.calcite.avatica.AvaticaStatement.executeInternal(AvaticaStatement.java:156)
	... 2 more
Caused by: org.apache.calcite.sql.validate.SqlValidatorException: Column '数据山' not found in any table
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:422)
	at org.apache.calcite.runtime.Resources$ExInstWithCause.ex(Resources.java:463)
	at org.apache.calcite.runtime.Resources$ExInst.ex(Resources.java:572)
	... 36 more
```

看到报错一脸懵逼，`From line 1, column 42 to line 1, column 44: Column '数据山' not found in any table`这是把过滤条件当列处理了？在百思不得其解的时候，突然想起了mysql的引号问题，于是把sql修正为`select * from "BOOK" as b where b.name = '数据山'`，于是喜闻乐见变了一下

```
java.sql.SQLException: Error while executing SQL "select * from "BOOK" as b where b.name = '数据山'": while converting `B`.`NAME` = '数据山'
	at org.apache.calcite.avatica.Helper.createException(Helper.java:56)
	at org.apache.calcite.avatica.Helper.createException(Helper.java:41)
	at org.apache.calcite.avatica.AvaticaStatement.executeInternal(AvaticaStatement.java:163)
	at org.apache.calcite.avatica.AvaticaStatement.executeQuery(AvaticaStatement.java:227)
	at com.dafei1288.calcite.TestJDBC.main(TestJDBC.java:81)
Caused by: java.lang.RuntimeException: while converting `B`.`NAME` = '数据山'
	at org.apache.calcite.sql2rel.ReflectiveConvertletTable.lambda$registerNodeTypeMethod$0(ReflectiveConvertletTable.java:86)
	at org.apache.calcite.sql2rel.SqlNodeToRexConverterImpl.convertCall(SqlNodeToRexConverterImpl.java:63)
	at org.apache.calcite.sql2rel.SqlToRelConverter$Blackboard.visit(SqlToRelConverter.java:4670)
	at org.apache.calcite.sql2rel.SqlToRelConverter$Blackboard.visit(SqlToRelConverter.java:3977)
	at org.apache.calcite.sql.SqlCall.accept(SqlCall.java:138)
	at org.apache.calcite.sql2rel.SqlToRelConverter$Blackboard.convertExpression(SqlToRelConverter.java:4541)
	at org.apache.calcite.sql2rel.SqlToRelConverter.convertWhere(SqlToRelConverter.java:965)
	at org.apache.calcite.sql2rel.SqlToRelConverter.convertSelectImpl(SqlToRelConverter.java:643)
	at org.apache.calcite.sql2rel.SqlToRelConverter.convertSelect(SqlToRelConverter.java:621)
	at org.apache.calcite.sql2rel.SqlToRelConverter.convertQueryRecursive(SqlToRelConverter.java:3051)
	at org.apache.calcite.sql2rel.SqlToRelConverter.convertQuery(SqlToRelConverter.java:557)
	at org.apache.calcite.prepare.Prepare.prepareSql(Prepare.java:264)
	at org.apache.calcite.prepare.Prepare.prepareSql(Prepare.java:230)
	at org.apache.calcite.prepare.CalcitePrepareImpl.prepare2_(CalcitePrepareImpl.java:772)
	at org.apache.calcite.prepare.CalcitePrepareImpl.prepare_(CalcitePrepareImpl.java:636)
	at org.apache.calcite.prepare.CalcitePrepareImpl.prepareSql(CalcitePrepareImpl.java:606)
	at org.apache.calcite.jdbc.CalciteConnectionImpl.parseQuery(CalciteConnectionImpl.java:229)
	at org.apache.calcite.jdbc.CalciteMetaImpl.prepareAndExecute(CalciteMetaImpl.java:550)
	at org.apache.calcite.avatica.AvaticaConnection.prepareAndExecuteInternal(AvaticaConnection.java:675)
	at org.apache.calcite.avatica.AvaticaStatement.executeInternal(AvaticaStatement.java:156)
	... 2 more
Caused by: java.lang.reflect.InvocationTargetException
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.apache.calcite.sql2rel.ReflectiveConvertletTable.lambda$registerNodeTypeMethod$0(ReflectiveConvertletTable.java:83)
	... 21 more
Caused by: org.apache.calcite.runtime.CalciteException: Failed to encode '数据山' in character set 'ISO-8859-1'
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:422)
	at org.apache.calcite.runtime.Resources$ExInstWithCause.ex(Resources.java:463)
	at org.apache.calcite.runtime.Resources$ExInst.ex(Resources.java:572)
	at org.apache.calcite.util.NlsString.<init>(NlsString.java:81)
	at org.apache.calcite.rex.RexBuilder.makeLiteral(RexBuilder.java:878)
	at org.apache.calcite.rex.RexBuilder.makeCharLiteral(RexBuilder.java:1093)
	at org.apache.calcite.sql2rel.SqlNodeToRexConverterImpl.convertLiteral(SqlNodeToRexConverterImpl.java:118)
	at org.apache.calcite.sql2rel.SqlToRelConverter$Blackboard.visit(SqlToRelConverter.java:4659)
	at org.apache.calcite.sql2rel.SqlToRelConverter$Blackboard.visit(SqlToRelConverter.java:3977)
	at org.apache.calcite.sql.SqlLiteral.accept(SqlLiteral.java:532)
	at org.apache.calcite.sql2rel.SqlToRelConverter$Blackboard.convertExpression(SqlToRelConverter.java:4541)
	at org.apache.calcite.sql2rel.StandardConvertletTable.convertExpressionList(StandardConvertletTable.java:767)
	at org.apache.calcite.sql2rel.StandardConvertletTable.convertCall(StandardConvertletTable.java:743)
	at org.apache.calcite.sql2rel.StandardConvertletTable.convertCall(StandardConvertletTable.java:727)
	... 26 more
```
对于开发者来说，有变化总是好事，说明可能找到问题点了，再仔细看看日志，果然，编码问题，搞起。于是在各种检索下，得到了答案

```
System.setProperty("saffron.default.charset", ConversionUtil.NATIVE_UTF16_CHARSET_NAME);
System.setProperty("saffron.default.nationalcharset",ConversionUtil.NATIVE_UTF16_CHARSET_NAME);
System.setProperty("saffron.default.collation.name",ConversionUtil.NATIVE_UTF16_CHARSET_NAME + "$en_US");
```

在获取连接之前，将入上述环境变量，就好了,代码片段如下：

```
    public static void main(String[] args) {
        try {
            Class.forName("org.apache.calcite.jdbc.Driver");
        } catch (ClassNotFoundException e1) {
            e1.printStackTrace();
        }
        System.setProperty("saffron.default.charset", ConversionUtil.NATIVE_UTF16_CHARSET_NAME);
        System.setProperty("saffron.default.nationalcharset",ConversionUtil.NATIVE_UTF16_CHARSET_NAME);
        System.setProperty("saffron.default.collation.name",ConversionUtil.NATIVE_UTF16_CHARSET_NAME + "$en_US");

        Properties info = new Properties();
        String jsonmodle = "E:\\working\\others\\写作\\calcitetutorial\\src\\main\\resources\\bookshop.json";
        
        Connection connection =
                    DriverManager.getConnection("jdbc:calcite:model="+jsonmodle, info);
        CalciteConnection calciteConn = connection.unwrap(CalciteConnection.class);
        ......
    }
```

# 数据类型处理

在写过滤的时候，其实就发现了，没有正确的数据类型，是走不远的，所以有必要把数据类型对应好，既然是模拟数据库，那么数据基本类型，还是使用SQL习惯的类型，而这里对java还是需要有一个映射关系。

```
package com.dafei1288.calcite.storage;

import com.google.common.collect.HashBasedTable;
import com.google.common.collect.Table;
import org.apache.calcite.sql.type.SqlTypeName;

import java.math.BigDecimal;
import java.sql.Date;
import java.util.Set;

/**
 * 这里使用了GUAVA的table 作为存SQL和JAVA数据类型的数据结构
 * 这并不是一个好的设计，而是为了让大家更容易理解而做的设计
 */
public class DataTypeMapping {

    public static Table<String, SqlTypeName,Class> TYPEMAPPING= HashBasedTable.create();
    public static final String CHAR = "char";
    public static final String VARCHAR = "varchar";
    public static final String BOOLEAN = "boolean";
    public static final String DATE = "date";
    public static final String INTEGER = "integer";
    public static final String TINYINT = "tinyint";
    public static final String SMALLINT = "smallint";
    public static final String BIGINT = "bigint";
    public static final String DECIMAL = "decimal";
    public static final String NUMERIC = "numeric";
    public static final String FLOAT = "float";
    public static final String REAL = "real";
    public static final String DOUBLE = "double";
    public static final String TIME = "time";
    public static final String TIMESTAMP = "timestamp";
    public static final String ANY = "any";
    static{
        TYPEMAPPING.put(DataTypeMapping.CHAR,SqlTypeName.CHAR,Character.class);
        TYPEMAPPING.put(DataTypeMapping.VARCHAR,SqlTypeName.VARCHAR,String.class);
        TYPEMAPPING.put(DataTypeMapping.BOOLEAN,SqlTypeName.BOOLEAN,Boolean.class);
        TYPEMAPPING.put(DataTypeMapping.DATE,SqlTypeName.DATE,Date.class);
        TYPEMAPPING.put(DataTypeMapping.INTEGER,SqlTypeName.INTEGER,Integer.class);
        TYPEMAPPING.put(DataTypeMapping.TINYINT, SqlTypeName.TINYINT,Integer.class);
        TYPEMAPPING.put(DataTypeMapping.SMALLINT, SqlTypeName.SMALLINT,Integer.class);
        TYPEMAPPING.put(DataTypeMapping.BIGINT, SqlTypeName.BIGINT,Long.class);
        TYPEMAPPING.put(DataTypeMapping.DECIMAL, SqlTypeName.DECIMAL, BigDecimal.class);
        TYPEMAPPING.put(DataTypeMapping.NUMERIC, SqlTypeName.DECIMAL,Long.class);
        TYPEMAPPING.put(DataTypeMapping.FLOAT, SqlTypeName.FLOAT,Float.class);
        TYPEMAPPING.put(DataTypeMapping.REAL, SqlTypeName.REAL,Double.class);
        TYPEMAPPING.put(DataTypeMapping.DOUBLE, SqlTypeName.DOUBLE,Double.class);
        TYPEMAPPING.put(DataTypeMapping.TIME, SqlTypeName.TIME, Date.class);
        TYPEMAPPING.put(DataTypeMapping.TIMESTAMP, SqlTypeName.TIMESTAMP,Long.class);
        TYPEMAPPING.put(DataTypeMapping.ANY, SqlTypeName.ANY,String.class);
    }
    /**
     * 根据名字获取，对应的java类型
     * */
    public static Class getJavaClassByName(String name){
        Set<Table.Cell<String, SqlTypeName,Class>> table = TYPEMAPPING.cellSet();
        for(Table.Cell<String, SqlTypeName,Class> it:table){
            if(it.getRowKey().equals(name)){
                return it.getValue();
            }
        }
        return null;
    }
    public static SqlTypeName getSqlTypeByName(String name){
        for(Table.Cell<String, SqlTypeName,Class> it:TYPEMAPPING.cellSet()){
            if(it.getRowKey().equals(name)){
                return it.getColumnKey();
            }
        }
        return null;
    }
}

```

栗子中，我使用了`guava`的`table`结构，主要还是为了大家方便只管理解，`Table<String, SqlTypeName,Class>`,第一个泛型代表内部定义的数据类型字符串，第二个是SQL数据类型，第三个参数是对应的JAVA类型。由于`guava`的`table`是`xy-->z`模式，但实际上我们需要的是一个`x->yz`模式，所以在下面添加了两个辅助方法：`public static Class getJavaClassByName(String name)`通过拿到的数据类型的字符串，获得java的类型，`public static SqlTypeName getSqlTypeByName(String name)`这个方法则是拿到SQL的数据类型。

笔者一直对充血模型钟爱有加，所以在`Storage`里为`DummyColumn`添加了两个方法

```
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

        //我充血模型
        //获取JAVA类型
        public Class getJavaClass(){
            return DataTypeMapping.getJavaClassByName(this.type);
        }

        public SqlTypeName getSqlTypeName(){
            return DataTypeMapping.getSqlTypeByName(this.type);
        }
    }
```

而在数据初始化的时候，我们可以使用正确的数据类型了

```
        DummyColumn id = new DummyColumn("ID","integer");
        DummyColumn name = new DummyColumn("NAME","varchar");
        DummyColumn age = new DummyColumn("AGE","integer");
        DummyColumn aid = new DummyColumn("AID","integer");
        DummyColumn type = new DummyColumn("TYPE","varchar");
```

这样数据类型的准备工作就做好了，后面我们需要将数据类型正确的注册表里，所以在`InMemoryTable`里我们原来写死的`String.class`类型，需要根据实际数据进行设置了

```
 @Override
    public RelDataType getRowType(RelDataTypeFactory typeFactory) {
//        System.out.println("RelDataType !!!!!!");
        if(dataType == null) {
            RelDataTypeFactory.FieldInfoBuilder fieldInfo = typeFactory.builder();
            for (Storage.DummyColumn column : this._table.getColumns()) {
                RelDataType sqlType = typeFactory.createJavaType(column.getJavaClass()); //这里使用了新增的方法，原来是String.class
                sqlType = SqlTypeUtil.addCharsetAndCollation(sqlType, typeFactory);
                fieldInfo.add(column.getName(), sqlType);
            }
            this.dataType = typeFactory.createStructType(fieldInfo);
        }
        return this.dataType;
    }
```

到这里我们就搞定了数据类型。

# UDF

有了基础数据类型，对于我们后面做函数处理就方便多了，现在我们以2个简单的UDF为例，让我们继续领略`calcite`的魅力，

首先我们定义两个类，一个数学类，提供一个乘方的方法

```
package com.dafei1288.calcite.function;

public class MathFunction {
    public int square(int i){
        return i*i;
    }
}
```

另一个字符处理类，提供一个字符串连接方法，提供一个将参数转换成字符串的方法

```
package com.dafei1288.calcite.function;

public class StringFunction {
    public String concat(Object o1,Object o2){
        return "["+o1.toString()+" , "+o2.toString()+"] => "+this.toString();
    }
    public String parseString(Object o){
        return o.toString();
    }
}
```

接下来，在`InMemorySchemaFactory`里，将函数注册到数据库上，

```
package com.dafei1288.calcite;

import com.dafei1288.calcite.function.MathFunction;
import com.dafei1288.calcite.function.StringFunction;
import org.apache.calcite.schema.Schema;
import org.apache.calcite.schema.SchemaFactory;
import org.apache.calcite.schema.SchemaPlus;
import org.apache.calcite.schema.impl.ScalarFunctionImpl;

import java.util.Map;

public class InMemorySchemaFactory implements SchemaFactory {
    @Override
    public Schema create(SchemaPlus parentSchema, String name, Map<String, Object> operand) {


        System.out.println("schema name ==>  "+ name);
        System.out.println("operand ==> "+operand);

        parentSchema.add("SQUARE_FUNC",ScalarFunctionImpl.create(MathFunction.class,"square"));
        parentSchema.add("TOSTRING_FUNC",ScalarFunctionImpl.create(StringFunction.class,"parseString"));
        parentSchema.add("CONCAT_FUNC",ScalarFunctionImpl.create(StringFunction.class,"concat"));

        return new InMemorySchema(name,operand);
    }
}
```

`SchemaPlus parentSchema`提供了一个`void add(String name, Function function)`方法，`name`为函数名，这里`calcite`提供了一个工具类`ScalarFunction`,它可以通过`create`方法，可以将你写好的函数类和其对应的方法反射出来。

接下来我们做一个测试：
```
result = st.executeQuery("select SQUARE_FUNC(b.id),CONCAT_FUNC(b.id,b.name) from \"BOOK\" as b");
while(result.next()) {
    System.out.println(result.getString(1) + "\t" +result.getString(2) + "\t" );
}
```

结果

```
1	[1 , 数据山] => com.dafei1288.calcite.function.StringFunction@22bb5646	
4	[2 , 大关] => com.dafei1288.calcite.function.StringFunction@1be59f28	
9	[3 , lili] => com.dafei1288.calcite.function.StringFunction@2ce45a7b	
16	[4 , ten] => com.dafei1288.calcite.function.StringFunction@153d4abb	
```

# 技术总结

这期新内容代码有点少，但是在实验成功以后，就克制不住自己的兴奋，写了这篇文章。
后续：
1. 希望能对聚合函数做一些尝试。
2. 函数的下推，这只是一个想法，目前实现的还是相当于UDF，那么实际上数据库层应该提供了很多函数的，那么在这里，是否可以透过`calcite`将函数交给`Storage`处理...
3. streaming sql 

代码已更新：`https://github.com/dafei1288/CalciteHelloworld.git`

文档的翻译工作感觉暂时鸽了：）

