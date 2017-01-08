# Data Storage IN Android 
     
* File-Storage 文件存储
* SharedPreference-Storage 
* Database 数据库存储     
     
## 1.File-Storage

* `openFIleOutput()`： 第一个参数为文件名，第二个是操作模式：MODE_PRIVATE(default) & MODE_APPEND    
* `openFileInput( )`: ~          
     
### 1.1 how to save data: 

```
public void save(String input) {
        FileOutputStream out  = null;
        BufferedWriter writer = null;
        try {
            out = openFileOutput("new", Context.MODE_PRIVATE);
            writer = new BufferedWriter(new OutputStreamWriter(out));
            writer.write(input);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if(writer!=null) {
                    writer.close();
                }
             } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```     


### 1.2 how to load data?

```
 public String load() {
        FileInputStream in = null;
        BufferedReader bufferedReader = null;
        StringBuilder stringBuilder = new StringBuilder();
        try {
            in = openFileInput("new");
            bufferedReader = new BufferedReader(new InputStreamReader(in));
            String hasread = "";
            while((hasread = bufferedReader.readLine())!= null) {
                stringBuilder.append(hasread);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if(bufferedReader!=null) {
                try {
                    bufferedReader.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

        }
        return stringBuilder.toString();
    }
```     
     
> Android test: 

* example source code : [FileStorageTest](https://github.com/benny201/AndroidTestProjects/tree/master/FileStorageTest "FileStorageTest")     

## 2. SharedPreferences()     

### Key 
* Storage data in `key-value` method.     
     
### 2.1 Save DATA:

#### Three Steps:      
     
#### Step 1: get a SharedPreferences object      

`three methods: `
##### 1.getSharedOreferences()     
* mehtod in Context class. Context.getSharedOreferences();     
* two parameter: getSharedOreferences(filename,mode)
* two mode : MODE_PRIVATE / MODE_MULTI_PROCESS(deprecated after Android 6.0)     
     
##### 2.getPreferences()
* just one parameter: MODE

##### 3.PreferenceManager Class     
* getDefaultSharedPreference()     
     
#### Step 2: Use edit() to get a SharedPreferences.Editor Object      
```
SharedPreferences.Editor editor = getPreferences(MODE_PRIVATE).edit();
```     

#### Step 3: Call putString()/putBoolean()/putInt().etc to add data     
     
#### Step 4: Call editor.apply() to commit data;      
        
                      
### 2.2 Load data in SharedPreference         

* **get a sharedPreference Objec** by calling **getSharedPreferences**        
* call getString / getInt / get... etc to get data        
      
### 2.3 多用于偏好设置功能,大型数据不适用     
<<<<<<< HEAD

### 2.4 记录密码实战. [sharedPreference Project](https://github.com/benny201/AndroidTestProjects/tree/master/BroadCastForOffLine "sharedPreference Project")       
* 引入了checkbox，**注意** ： 在检查完checkbox的状态后，要继续调用setChecked()将checkbox设置成原来状态       

## 3.SQLiteDatabase

* Android中集成的database
* 可以用SQL语句实现DataBase操作
* 四个关键操作：Create / Retrieve / Update / Delete -> CRUD

### 3.1 Create a Database
* Create a MyDataBaseHelper extends SQLiteOpenHelper
* create a contructor 
```Android
public MyDataBaseHelper(Context mContext, String name, SQLiteDatabase.CursorFactory cursor, int version) {
        super(mContext, name, cursor, version);
        context = mContext;
    }
```
* execute a SQL statement in OnCreate(SQLiteDatabase db) by db.execSQL(SQL statement)

```Android
//SQL statement
    private static final String Create_book = "Create table Book("
                                                + "id integer primary key autoincrement,"
                                                + "author text,"
                                                + "pages integer,"
                                                + "name text)";

@Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(Create_book);
        Toast.makeText(context,"success!",Toast.LENGTH_SHORT).show();
    }
```
* create database in mainactivity, create a database by getWritableDatabase() / getReadableDatabase()

```Android
myDataBaseHelper.getWritableDatabase();
```
       
* view database by adb shell in terminal


### 3.2 Create data
* 创建contentvalues对象进行组装数据

```Android
ContentValues contentValues = new ContentValues();
// first table
contentValues.put("author", "Benny's Success");
contentValues.put("pages", 888);
contentValues.put("name", "Benny");
```

* add data by SQLiteDatabase.insert()
```Android
SQLiteDatabase db = myDataBaseHelper.getWritableDatabase();
db.insert("Book", null, contentValues);
```
     
* clear contentValues after adding data

```Android
contentValues.clear();
```
     
### 3.3 Update data
* modify the version of database in 

```Android
myDataBaseHelper = new MyDataBaseHelper(this, "Book.db", null, 2);
```
     
* 创建contentvalues对象进行组装数据

```Android
ContentValues contentValues = new ContentValues();
contentValues.put("pages", 77);
```
     
* call SQLiteDatabase.update()

```Android
SQLiteDatabase db = myDataBaseHelper.getWritableDatabase();
db.update("Book", contentValues, "name = ?", new String[] {"HelloWord"});
```

### 3.4 Delete data
* call SQLiteDatabase.delete()

```Android
SQLiteDatabase db = myDataBaseHelper.getWritableDatabase();
db.delete("Book", "pages > ?", new String[]{"500"});
```

### 3.5 Retrieve 
* call SQLiteDatabase.query();
* 7 prarmeters in query();
* create a cursor reference to store the query() result 
```Android
SQLiteDatabase db = myDataBaseHelper.getWritableDatabase();
Cursor cursor = db.query("Book", null, null, null, null, null, null); 
```
* traversal the cursor above;
```Android
if (cursor.moveToFirst()) {
    do {
        String name = cursor.getString(cursor.getColumnIndex("name"));
        String author = cursor.getString(cursor.getColumnIndex("author"));
        int pages = cursor.getInt(cursor.getColumnIndex("pages"));

        Log.d(TAG, name + author + pages);
    } while(cursor.moveToNext());
}
```
### 实战Project     
* [DataBaseTest](https://github.com/benny201/AndroidTestProjects/tree/master/DataBaseTest "DataBaseTest")


## 4. LitePal
* [LitePal for Android](https://github.com/LitePalFramework/LitePal "LitePal for Android")
* a powerful tool for database in Android
* Using object-relational mapping (ORM) pattern : equip the SQL with object-oriented character
* easy to use
