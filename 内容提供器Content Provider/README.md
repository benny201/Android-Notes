# 内容提供器(Content Provider)     

* 提供程序中的数据共享
* 保证被访数据的安全
* SharedPreferences中可以可以做到全局读写，然而并不安全, Android 6.0后已删除
* 程序共享数据的标准方式是Content Provider

## 1.两种内容提供期
* 自带的内容提供器
* 自行创建内容提供器

## 2.ContentResolver
* 提供CRUD的方法: insert(), update(), delete(), query();
* 通过 getContentResolver() 获取对象
* 与SQLiteDataBase的操作类似, 但是传入的不是表，而是Uri对象
* Uri由 authority 和 path 组成     
      
```java
Uri uri = Uri.parse("content://com.example.benny.app.provider/table1")
```
     
### 2.1 Create data
* create a ContentValue and call put() to store data
      
```java
ContentValues values = new ContentValues();
values.put("column1", "benny");
```
     
* call insert() to add data to the tables

```java
getContentResolver().insert(uri, values);
```

### 2.2 Update
* create a ContentValue and call put() as create data
* call getContentResolver().update()
     
```java
getContentResolver().update(uri, values, "column1 = ? and column2 = ?", new String[]{"text", "1"});
```

### 2.3 Retrieve
* call getContentResolver().query() to a cursor object

```java
Cursor cursor = getContentResolver().query(Uri, projection, selection, seletionArgs, sortOrder)
```
      
* traversal the cursor

```java
if (cursor != null) {
	while (cursor.moveToNext()) {
		String colum1 = cursor.getString(cursor.getColumnIndex("column1"));
		int column2 = cursor.getInt(cursor.getColumnIndex("colum2"));
	}
}
```

### 2.4 Delete
* call getContentResolver().delete();
    
```java
getContentResolver().delete(uri, values, "column1 = ? and column2 = ?", new String[]{"text", "1"});
```

### 2.5 Project实战
* 读取联系人信息
* []()

## 3. 自定义内容提供器

### 3.1 Components
* boolean onCreate()
* Cursor query()
* Uri insert()
* int update()
* int delete()
* String getType()

### 3.2 Uri
* content:// + authority + path
* *: 匹配任意长度的字符
```java
content://com.example.app.provider/* : 匹配任意表
```
* #:匹配任意长度的数字
```java
 content://com.example.app.provider/table1/* : 匹配任意一行数据
 ```
 * 用 ＊ 和 ＃ 组成两种常用的URI格式

 ### 3.3 UriMacther
* 用自定义标识符代表访问的表，如：TABLE1_DIR、TABLE2_ITEM
* call UriMacther.addURI() to add self-define indentifier
```java
uriMatcher.addURI(AUTHORITY, "book", BOOK_DIR);
```
* call UriMacther.mactch() to get the indentifier
```java
uriMatcher.match(uri)
```

### 3.3 onCreate()
* just get a databasehelper instance and return TURE
```java
 	@Override
    public boolean onCreate() {
        // TODO: Implement this to initialize your content provider on startup.
        myDataBaseHelper = new MyDataBaseHelper(getContext(), "Book.db", null, 5);
        return true;
    }
```

### 3.4 query()
* call myDataBaseHelper.getReadableDatabase() to get a SQLiteDatabase instance
* if just query one table, call 
```java
uri.getPathSegments().get(1) : get the id of item(get(0): path/get(1): id)

```
* return a cursor
```java
 		@Override
    	public Cursor query(Uri uri, String[] projection, String selection,
                        String[] selectionArgs, String sortOrder) {
        // TODO: Implement this to handle query requests from clients.
        SQLiteDatabase db = myDataBaseHelper.getReadableDatabase();
        Cursor cursor = null;
        switch (uriMatcher.match(uri)) {
            case BOOK_DIR :
                cursor = db.query("Book", projection, selection, selectionArgs, null, null, sortOrder);
                break;
            case BOOK_ITEM :
                String BOOKID = uri.getPathSegments().get(1);
                cursor = db.query("Book", projection, "id = ?", new String[]{BOOKID}, null, null, sortOrder);
                break;
            case CATEGORY_DIR :
                cursor = db.query("Category", projection, selection, selectionArgs, null, null, sortOrder);
                break;
            case CATEGORY_ITEM :
                String CATEID = uri.getPathSegments().get(1);
                cursor = db.query("Category", projection, "id = ?", new String[]{CATEID}, null, null, sortOrder);
                break;
            default:
                break;
        }
        return cursor
        }
```
      
### 3.5 insert()
* call myDataBaseHelper.getWritableDatabase() to get a SQLiteDatabase instance
* call db.insert() to insert data and get a id of the new data

```java
long newBookId = db.insert("Book", null, values);
```
     
* get a URI result for the futher delete operation

```java
	@Override
    public Uri insert(Uri uri, ContentValues values) {
        // TODO: Implement this to handle requests to insert a new row.
        SQLiteDatabase db = myDataBaseHelper.getWritableDatabase();
        Uri Uriretsult = null;
        switch (uriMatcher.match(uri)) {
            case BOOK_DIR:
            case BOOK_ITEM:
                long newBookId = db.insert("Book", null, values);
                Uriretsult = Uri.parse("content://" + AUTHORITY + "/book/" + newBookId);
                break;
            case CATEGORY_DIR:
            case CATEGORY_ITEM:
                long newCateId = db.insert("Category", null, values);
                Uriretsult = Uri.parse("content://" + AUTHORITY + "/category/" + newCateId);
                break;
            default:
                break;
        }
        return Uriretsult;

    }
```

### 3.6 update()
* just call SQLiteDatabase.update() to update

```java
	@Override
    public int update(Uri uri, ContentValues values, String selection,
                      String[] selectionArgs) {
        // TODO: Implement this to handle requests to update one or more rows.
        SQLiteDatabase db = myDataBaseHelper.getWritableDatabase();
        int updateRows = 0;
        switch (uriMatcher.match(uri)) {
            case BOOK_DIR:
                updateRows = db.update("Book", values, selection, selectionArgs);
                break;
            case BOOK_ITEM:
                String bookId = uri.getPathSegments().get(1);
                updateRows = db.update("Book", values, "id = ?", new String[]{bookId});
                break;
            case CATEGORY_DIR:
                updateRows = db.update("Category", values, selection, selectionArgs);
                break;
            case CATEGORY_ITEM:
                String cateId = uri.getPathSegments().get(1);
                updateRows = db.update("Category", values, "id = ?", new String[]{cateId});
                break;
            default:
                break;
        }
        return updateRows;
    }
```

### 3.7 delete()
* just call SQLiteDatabase.delete() to delete()
      
```java
	@Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        // Implement this to handle requests to delete one or more rows.
        SQLiteDatabase db = myDataBaseHelper.getWritableDatabase();
        int deleteRows = 0;
        switch (uriMatcher.match(uri)) {
            case BOOK_DIR:
                deleteRows = db.delete("Book", selection, selectionArgs);
                break;
            case BOOK_ITEM:
                String bookId = uri.getPathSegments().get(1);
                deleteRows = db.delete("Book", "id = ?", new String[]{bookId});
                break;
            case CATEGORY_DIR:
                deleteRows = db.delete("Category", selection, selectionArgs);
                break;
            case CATEGORY_ITEM:
                String cateId = uri.getPathSegments().get(1);
                deleteRows = db.delete("Category", "id = ?", new String[]{cateId});
                break;
            default:
                break;
        }
        return deleteRows;
    }
```

### 3.8 Content provider project
* []()

### 3.9 A example to use content provider
* []()





