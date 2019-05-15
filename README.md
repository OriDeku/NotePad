# NotePad
期中实验笔记本应用
## 时间戳的实现
添加一个显示时间的TextView
'<<TextView
        android:id="@+id/text1_time"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceSmall"
        android:paddingLeft="5dip"
        android:textColor="@color/colorBlack"/>>'
在projection中添加'<NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE>'
在ViewsID中补充时间部分'<String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,  NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE } ;
int[] viewIDs = { android.R.id.text1 , R.id.text1_time };>'
同时在**NotePadProvider中的insert方法和NoteEditor中的updateNote方法**中添加代码'<Long now = Long.valueOf(System.currentTimeMillis());
Date date = new Date(now);
SimpleDateFormat format = new SimpleDateFormat("yyyy.MM.dd HH:mm:ss");
String dateTime = format.format(date);>'   
***要values.put()函数中的now修改为dateTime，否则无法正确显示时间，显示将会是时间戳***
![Image text](https://github.com/OriDeku/NotePad/blob/master/view/time.jpg)

## 搜索功能的实现
添加item '<<item
    android:id="@+id/menu_search"
    android:title="@string/menu_search"
    android:icon="@android:drawable/ic_search_category_default"
    android:showAsAction="always">
</item>>'
添加case语句'< 
    case R.id.menu_search:
    Intent intent = new Intent();
    intent.setClass(NotesList.this,NoteSearch.class);
    NotesList.this.startActivity(intent);
    return true;>'
**应用searchview和listview结合实现动态搜索**'<<SearchView
        android:id="@+id/search_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:iconifiedByDefault="false"
        android:queryHint="输入搜索内容..."
        android:layout_alignParentTop="true">
    </SearchView>
    <ListView
        android:id="@android:id/list"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
    </ListView>>'
**动态搜索主要实现代码，在NoteSearch中的onQueryTextChange**'<@Override
    public boolean onQueryTextChange(String newText) {
        String selection = NotePad.Notes.COLUMN_NAME_TITLE + " Like ? ";
        SearchView searchview = (SearchView)findViewById(R.id.search_view);
        searchview.setOnQueryTextListener(NoteSearch.this);  
        String[] selectionArgs = { "%"+newText+"%" };
        Cursor cursor = managedQuery(
                getIntent().getData(),            // Use the default content URI for the provider.
                PROJECTION,                       // Return the note ID and title for each note. and modifcation date
                selection,                        // 条件左边
                selectionArgs,                    // 条件右边
                NotePad.Notes.DEFAULT_SORT_ORDER  // Use the default sort order.
        );
        String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,  NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE };
        int[] viewIDs = { android.R.id.text1 , R.id.text1_time };
        MyCursorAdapter adapter = new MyCursorAdapter(
                this,
                R.layout.noteslist_item,
                cursor,
                dataColumns,
                viewIDs
        );
        setListAdapter(adapter);
        return true;
    }>'
**onQueryTextChange方法作用是，当SearchView中文本发生变化时，执行其中代码，搜索还有一个重要的部分就是要做到模糊匹配而不是严格匹配，可以使用数据库查询语句中的LIKE和%结合来实现，newText为输入搜索的内容：**'<String[] selectionArgs = { "%"+newText+"%" };>'
![Image text](https://github.com/OriDeku/NotePad/blob/master/view/Search1.jpg)
![Image text](https://github.com/OriDeku/NotePad/blob/master/view/Search2.jpg)
![Image text](https://github.com/OriDeku/NotePad/blob/master/view/Search3.jpg)

## 扩展功能：排序
这个排序主要实现了根据创建笔记的时间和修改笔记的时间进行的排序
就是替换了原来Cursor函数中排序的位置
代码：'<case R.id.menu_sort1:
        cursor = managedQuery(
                getIntent().getData(),            
                PROJECTION,                      
                null,                          
                null,                          
                NotePad.Notes._ID 
                );
        adapter = new MyCursorAdapter(
                this,
                R.layout.noteslist_item,
                cursor,
                dataColumns,
                viewIDs
        );
        setListAdapter(adapter);
        return true;
 //修改时间排序
    case R.id.menu_sort2:
        cursor = managedQuery(
                getIntent().getData(),          
                PROJECTION,                      
                null,                            
                null,                       
                NotePad.Notes.DEFAULT_SORT_ORDER 
        );
        adapter = new MyCursorAdapter(
                this,
                R.layout.noteslist_item,
                cursor,
                dataColumns,
                viewIDs
        );
        setListAdapter(adapter);
        return true;>'
![Image text](https://github.com/OriDeku/NotePad/blob/master/view/sort.jpg)
![Image text](https://github.com/OriDeku/NotePad/blob/master/view/sort1.jpg)
![Image text](https://github.com/OriDeku/NotePad/blob/master/view/sort2.jpg)
