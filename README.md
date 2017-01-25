# Android-Notes

> 第一行代码的笔记


## 2.0 Data Storage IN Android 
     
* File-Storage 文件存储
* SharedPreference-Storage 
* Database 数据库存储     
     
### 2.1 File-Storage

* `openFIleOutput()`： 第一个参数为文件名，第二个是操作模式：MODE_PRIVATE(default) & MODE_APPEND    
* `openFileInput( )`: ~          
     
#### 2.1.1 how to save data: 

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


#### 2.1.2 how to load data?

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

### 2.2 SharedPreferences()     

#### 2.2.1 Key 
* Storage data in `key-value` method.     
     
#### 2.2.2 Save DATA:
#### Three Steps:      
     
##### Step 1: get a SharedPreferences object      

`three methods: `
###### 1.getSharedOreferences()     
* mehtod in Context class. Context.getSharedOreferences();     
* two parameter: getSharedOreferences(filename,mode)
* two mode : MODE_PRIVATE / MODE_MULTI_PROCESS(deprecated after Android 6.0)     
     
###### 2.getPreferences()
* just one parameter: MODE

###### 3.PreferenceManager Class     
* getDefaultSharedPreference()     
     
##### Step 2: Use edit() to get a SharedPreferences.Editor Object      
```
SharedPreferences.Editor editor = getPreferences(MODE_PRIVATE).edit();
```     

##### Step 3: Call putString()/putBoolean()/putInt().etc to add data     
     
##### Step 4: Call editor.apply() to commit data;      
        
        
#### 2.2.3 Load data in SharedPreference         

* **get a sharedPreference Objec** by calling **getSharedPreferences**        
* call getString / getInt / get... etc to get data       

