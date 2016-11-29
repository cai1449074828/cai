
[TOC]

#framelayout或viewPager+导航栏
##framelayout+导航栏
```
    private Fragment fragment1=new ContactsFragment();
    private Fragment fragment2=new ContactsFragment();
    private Fragment fragment3=new ContactsFragment();
    private FragmentTransaction fragmentTransaction;
    private ArrayList<Fragment> list_fragment=new ArrayList<>();
    private void initFrameLayout() {
        list_fragment.add(fragment1);
        list_fragment.add(fragment2);
        list_fragment.add(fragment3);
        fragmentTransaction=getFragmentManager().beginTransaction();
        for (int i=0;i<list_fragment.size();i++){
            fragmentTransaction.add(R.id.viewPager,list_fragment.get(i));
        }
        fragmentTransaction.commit();
        showFragment(0);
    }
    private void hideFragment() {
        for (int i=0;i<list_fragment.size();i++){
            fragmentTransaction.hide(list_fragment.get(i));
        }
    }
    private void showFragment(int index){
        fragmentTransaction=getFragmentManager().beginTransaction();
        hideFragment();
        fragmentTransaction.show(list_fragment.get(index));
        fragmentTransaction.commit();
    }
    private void initButtonNavigationBar(){
        mBottomNavigationBar
                .addItem(new BottomNavigationItem(R.drawable.ic_profile_answer, "会话"))
                .addItem(new BottomNavigationItem(R.drawable.ic_profile_article, "通讯录"))
                .addItem(new BottomNavigationItem(R.drawable.ic_profile_column, "设置"))
                .setActiveColor(R.color.colorAccent)
                .setMode(BottomNavigationBar.MODE_FIXED)
                .setTabSelectedListener(new BottomNavigationBar.OnTabSelectedListener() {
                    @Override
                    public void onTabSelected(int position) {
                        showFragment(position);
                    }
                    @Override
                    public void onTabUnselected(int position) {
                    }
                    @Override
                    public void onTabReselected(int position) {
                    }
                })
                .initialise();
    }
```
##viewPager+导航栏
```
private class ViewPagerAdapter extends FragmentStatePagerAdapter {
        public ViewPagerAdapter(FragmentManager fm) {
            super(fm);
        }
        @Override
        public CharSequence getPageTitle(int position) {
            return null;
        }
        @Override
        public Fragment getItem(int position) {
            return list_fragment.get(position);
        }
        @Override
        public int getCount() {
            return list_fragment.size();
        }
    }
    private List<String> titles= Arrays.asList("1","2","3","4");
    private ArrayList<Fragment> list_fragment=new ArrayList<>();
    private Fragment fragment1=new ContactsFragment();
    private Fragment fragment2=new ContactsFragment();
    private Fragment fragment3=new ContactsFragment();
    private void initViewPager() {
        list_fragment.add(fragment1);
        list_fragment.add(fragment2);
        list_fragment.add(fragment3);
        mViewPager.setAdapter(new ViewPagerAdapter(getChildFragmentManager()));
        mViewPager.setOffscreenPageLimit(1);
mViewPager.setOnPageChangeListener(new ViewPager.OnPageChangeListener() {
            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
 
            }
 
            @Override
            public void onPageSelected(int position) {
                mBottomNavigationBar.selectTab(position);
            }
 
            @Override
            public void onPageScrollStateChanged(int state) {
 
            }
        });
//        slidingTabLayout.setViewPager(viewPager);
    }
    private void initButtonNavigationBar(){
        mBottomNavigationBar
                .addItem(new BottomNavigationItem(R.drawable.ic_profile_answer, "会话"))
                .addItem(new BottomNavigationItem(R.drawable.ic_profile_article, "通讯录"))
                .addItem(new BottomNavigationItem(R.drawable.ic_profile_column, "设置"))
                .setActiveColor(R.color.colorAccent)
                .setMode(BottomNavigationBar.MODE_FIXED)
                .setTabSelectedListener(new BottomNavigationBar.OnTabSelectedListener() {
                    @Override
                    public void onTabSelected(int position) {
                       mViewPager.setCurrentItem(position);
                    }
                    @Override
                    public void onTabUnselected(int position) {
                    }
                    @Override
                    public void onTabReselected(int position) {
                    }
                })
                .initialise();
    }
```
初始化    
```
        initFrameLayout();
        //initViewPager();
        initButtonNavigationBar();
```
并且替换 list_fragment
切换时        showFragment(position);
#RecycleView
##MyRecyclerViewAdapter
```
import android.app.Activity;
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.ViewGroup;
 
 
import com.example.administrator.ex.R;
import com.example.administrator.ex.SqlLite.Song;
 
import java.util.List;
 
/**
* Created by Administrator on 2016/8/26.
*/
public class MyRecyclerViewAdapter extends RecyclerView.Adapter<MyRecyclerViewHolder>{
    private List<Song> list;
    private Activity activity;
    public MyRecyclerViewAdapter(Activity activity, List<Song> list){
        this.activity=activity;
        this.list=list;
    }
    @Override
    public MyRecyclerViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        return new MyRecyclerViewHolder(LayoutInflater.from(activity).inflate(R.layout.item_another2,parent,false),activity);
    }
 
    @Override
    public void onBindViewHolder(MyRecyclerViewHolder holder, int position) {
        holder.textView.setText(list.get(position).getName());
        holder.textView2.setText(String.valueOf(list.get(position).getDuration()));
//            ((TextView)holder.itemView.findViewById(R.id.textView_another2)).setText(list.get(position));
    }
 
    @Override
    public int getItemCount() {
        return list.size();
    }
}
```
##MyRecyclerViewHolder
```
import android.content.Context;
import android.content.Intent;
import android.support.v7.widget.RecyclerView;
import android.view.View;
import android.widget.TextView;
 
 
import com.example.administrator.ex.R;
 
import butterknife.Bind;
import butterknife.ButterKnife;
import butterknife.OnClick;
 
/**
* Created by Administrator on 2016/8/26.
*/
public class MyRecyclerViewHolder extends RecyclerView.ViewHolder {
    @Bind(R.id.textView)
    TextView textView;
    @Bind(R.id.textView2)
    TextView textView2;
    private Context activity;
    public MyRecyclerViewHolder(View itemView, Context activity) {
        super(itemView);
        ButterKnife.bind(this,itemView);
        this.activity=activity;
    }
}
```
##activity
```
            private List<Song> allSongs=new ArrayList<>();
    private MyRecyclerViewAdapter adapter;
    private void initRecycleView() {
        adapter = new MyRecyclerViewAdapter(this, allSongs);
        mRecyclerView.setLayoutManager(new LinearLayoutManager(this));
        //自动滑动到最低,更新List时需要写一次
        mRecyclerView.setAdapter(adapter);
        //adapter.notifyDataSetChanged();
        //mRecyclerView.smoothScrollToPosition(adapter.getItemCount());
    }
```
recycleView加载时由于RecyclerView.Adapter会自动回收viewholder,所以存在BUG，导致viewholder复用,所以请每次的onBindViewHolder中viewholder要设置完整
recycleView的adapter指定的list必须是同一个list,即地址一致,否则会造成数据不刷新,如果非要不同List,请new 一个adapter;
#上下文菜单与选项菜单
```
    menu.xml:
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:title="1"/>
    <item android:title="2"/>
</menu>
```
##选项菜单:
```
Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu_main,menu);
        return super.onCreateOptionsMenu(menu);
    }
```
##上下文菜单:
```
//按下按钮后显示上下文菜单(未实现)
Override
    public void onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo) {
        getMenuInflater().inflate(R.menu.menu_main,menu);
        super.onCreateContextMenu(menu, v, menuInfo);
    }
mButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                v.showContextMenu();
                Toast.makeText(MainActivity.this, "onClick", Toast.LENGTH_SHORT).show();
            }
        });
```
#网络通信
##鸿洋大神的okhttp封装
https://github.com/hongyangAndroid/okhttputils
compile 'com.zhy:okhttputils:2.6.2'
###get
```
private void get(){
        OkHttpUtils
                .get()
                .url("http://apis.baidu.com/apistore/weatherservice/citylist")
                .addHeader("apikey","592e46b62cfe201c68bf7d9f18db11ee")
                .addParams("cityname", "朝阳")
                .build()
                .execute(new StringCallback()
                {
                    @Override
                    public void onError(Call call, Exception e, int id) {
 
                    }
 
                    @Override
                    public void onResponse(String response, int id) {
                        System.out.println("response="+response+"id="+id);
                    }
                });
    }
```
//get请求中addParams是将参数放到URL Parameter Key;post请求是放到Body Parameter Key似乎不能放到URL中
###post
```
private void post(){
        OkHttpUtils
                .post()
                .url("http://121.40.184.143:99/cailei/getPlan_zong.php")
                .addParams("account", "1")
                .build()
                .execute(new StringCallback()
                {
                    @Override
                    public void onError(Call call, Exception e, int id) {
 
                    }
                    @Override
                    public void onResponse(String response, int id) {
                        //response=[{"name":"英语四六级(推荐)","progress":"1"},{"name":"驾照","progress":"0"},{"name":"普通话","progress":"0"},{"name":"二级c语言","progress":"0"},{"name":"三级数据库","progress":"0"}]
                        List<Plan_zong> plan_zongs=new Gson().fromJson(response,new TypeToken<List<Plan_zong>>() {}.getType());
                        Log.i("Plan_zongs=","--------------------Plan_zongs--------------------");
                        for (Plan_zong plan_zong:plan_zongs){
                            Log.i("Plan_zong.name=",plan_zong.getName());
                            Log.i("Plan_zong.progress=",plan_zong.getProgress());
                        }
                        Log.i("Plan_zongs=","--------------------Plan_zongs--------------------");
//                        System.out.println("response="+response+"id="+id);
                        Log.i("Gson","="+new Gson().toJson(plan_zongs));
                    }
                });
    }
```
###post发送json
```
private void post_json(){
        Plan_zong plan_zong=new Plan_zong();
        plan_zong.setName("cai");
        plan_zong.setProgress("1");
        OkHttpUtils
                .postString()
                .url("http://121.40.184.143:99/cailei/login.php")
                .content(new Gson().toJson(plan_zong))
                .mediaType(MediaType.parse("application/json; charset=utf-8"))
                .build()
                .execute(new StringCallback() {
                    @Override
                    public void onError(Call call, Exception e, int id) {
 
                    }
 
                    @Override
                    public void onResponse(String response, int id) {
 
                    }
                });
    }
```
###post发送图片/文件+进度条
```
 private void post_file(){
        byte[] bitmap_bytes=BitmapUtil.bitmapToByte(BitmapFactory.decodeResource(getResources(),R.drawable.bt_search_branch));
        File file= new File(Environment.getExternalStorageDirectory(),"image.png");
        try {
            FileUtils.writeByteArrayToFile(file,bitmap_bytes);
            String url="http://192.168.209.2/example/setImage.php";
            OkHttpUtils
                    .postFile()
                    .url(url)
                    .file(file)
                    .build()
                    .execute(new StringCallback() {
                        @Override
                        public void inProgress(float progress, long total, int id) {
                            super.inProgress(progress, total, id);
                            Log.i("okhttp","progress="+progress);
                        }
                        @Override
                        public void onError(Call call, Exception e, int id) {

                        }

                        @Override
                        public void onResponse(String response, int id) {
                            Log.i("okhttp","response="+response);
                        }
                    });
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```
###下载文件
```
private void downloadFile(){
        OkHttpUtils//
                .get()//
                .url("http://sw.bos.baidu.com/sw-search-sp/software/e6207d37846ba/QQ_8.7.19113.0_setup.exe")//下载页
                .build()//
                .execute(new FileCallBack(Environment.getExternalStorageDirectory().getAbsolutePath(), "gson-2.2.1.jar")//下载文件路径,文件名
                {
                    @Override
                    public void inProgress(float progress, long total, int id) {
                        super.inProgress(progress, total, id);
                        Log.i("okhttp","progress="+progress);
//                        mProgressBar.setProgress((int) (100 * progress));
                    }
                    @Override
                    public void onError(Call call, Exception e, int id) {
                        Log.e("okhttp", "onError :" + e.getMessage());
                    }
                    @Override
                    public void onResponse(File response, int id) {
                        Log.e("okhttp", "onResponse :" + Environment.getExternalStorageDirectory().getAbsolutePath());

                    }
                });
    }
```
##Gson
http://www.jianshu.com/p/e740196225a4
http://blog.csdn.net/axuanqq/article/details/51441590
###Gson将json转类
```
//log={"name":"cai","progress":"1"}
Plan_zong plan_zong2=new Gson().fromJson(log, Plan_zong.class);
        Log.i("Gson","name="+plan_zong2.getName());
        Log.i("Gson","progress="+plan_zong2.getProgress());
```
###Gson将类转为Json
```
Plan_zong plan_zong=new Plan_zong();
        plan_zong.setName("cai");
        plan_zong.setProgress("1");
        String log=new Gson().toJson(plan_zong);
        Log.i("Gson",log);
```
###Gson将JsonArray转为类的List
```
//response=[{"name":"英语四六级(推荐)","progress":"1"},{"name":"驾照","progress":"0"},{"name":"普通话","progress":"0"},{"name":"二级c语言","progress":"0"},{"name":"三级数据库","progress":"0"}]
                        List<Plan_zong> plan_zongs=new Gson().fromJson(response,new TypeToken<List<Plan_zong>>() {}.getType());
                        Log.i("Plan_zongs=","--------------------Plan_zongs--------------------");
                        for (Plan_zong plan_zong:plan_zongs){
                            Log.i("Plan_zong.name=",plan_zong.getName());
                            Log.i("Plan_zong.progress=",plan_zong.getProgress());
                        }
                        Log.i("Plan_zongs=","--------------------Plan_zongs--------------------");
```
###Gson将List转JsonArray
```
new Gson().toJson(plan_zongs)
```
###Glide加载图片
```
Glide.with(activity).load(listImage.get(0)).placeholder(R.drawable.account_avatar).into(holder.imageView);
```
上面这种方式into里的imageView不能设置为固定大小，如果要用使用下面代码
```
Glide.with(activity).load(listImage.get(0)).placeholder(R.drawable.account_avatar).into(
                    new SimpleTarget<GlideDrawable>() {
                        @Override
                        public void onResourceReady(GlideDrawable resource, GlideAnimation<? super GlideDrawable> glideAnimation) {
                            holder.imageView.setImageDrawable(resource);
                        }
            });
```
#数据存储与读取
##手机内部数据库(sqlite)
```
initSqlLite();
        queryData_sqlite();
        initView();
private void initView() {
        initRecycleView();
        mButtonAdd.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                addData_sqlite();
            }
        });
        mButtonDelete.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                deleteData_sqlite();
            }
        });
        mButtonUpdata.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                upData_sqlite();
            }
        });
    }
    private List<Person> allPersons = new ArrayList<>();
    private List<Song> allSongs = new ArrayList<>();
    private MyRecyclerViewAdapter adapter;
 
    private void initRecycleView() {
        adapter = new MyRecyclerViewAdapter(this, allPersons);
        mRecyclerView.setLayoutManager(new LinearLayoutManager(this));
        //自动滑动到最低,更新List时需要写一次
        mRecyclerView.setAdapter(adapter);
        //adapter.notifyDataSetChanged();
        //mRecyclerView.smoothScrollToPosition(adapter.getItemCount());
    }
    private void initSqlLite() {
        //打开或创建test.db数据库
        SQLiteDatabase db = openOrCreateDatabase("test.db", Context.MODE_PRIVATE, null);
        db.execSQL("DROP TABLE IF EXISTS person");
        //创建person表
        db.execSQL("CREATE TABLE person (id INTEGER PRIMARY KEY AUTOINCREMENT, name VARCHAR, age VARCHAR)");
        //关闭当前数据库
        allPersons.add(new Person());
        db.close();
    }
    private void addData_sqlite() {
        SQLiteDatabase db = openOrCreateDatabase("test.db", Context.MODE_PRIVATE, null);
        Person person = new Person();
        person.setName("蔡荣程");
        person.setAge("22");
        //ContentValues以键值对的形式存放数据
        ContentValues cv = new ContentValues();
        cv.put("name", person.getName());
        cv.put("age", person.getAge());
//        //插入ContentValues中的数据
        db.insert("person", null, cv);
        allPersons.add(person);
        adapter.notifyDataSetChanged();
        Toast.makeText(this, "添加最后一项成功", Toast.LENGTH_SHORT).show();
        db.close();
    }
    private void deleteData_sqlite() {
        SQLiteDatabase db = openOrCreateDatabase("test.db", Context.MODE_PRIVATE, null);
        int id=adapter.getItemCount();
        db.delete("person", "id = ?", new String[]{String.valueOf(id)});
        allPersons.remove(id-1);
        adapter.notifyDataSetChanged();
        Toast.makeText(this, "删除最后一项成功", Toast.LENGTH_SHORT).show();
        db.close();
    }
    private void upData_sqlite() {
        SQLiteDatabase db = openOrCreateDatabase("test.db", Context.MODE_PRIVATE, null);
        ContentValues cv = new ContentValues();
        Person person = new Person();
        person.setName("蔡荣程");
        person.setAge("0");
        cv.put("name", person.getName());
        cv.put("age", person.getAge());
        db.update("person", cv, "id = ?", new String[]{String.valueOf(adapter.getItemCount())});
        allPersons.get(adapter.getItemCount()-1).setAge("0");
        adapter.notifyDataSetChanged();
        Toast.makeText(this, "修改最后一项成功", Toast.LENGTH_SHORT).show();
        db.close();
    }
    private void queryData_sqlite() {
        SQLiteDatabase db = openOrCreateDatabase("test.db", Context.MODE_PRIVATE, null);
        //        Cursor c = db.rawQuery("SELECT * FROM person WHERE age >= ?", new String[]{"33"});
        Cursor c = db.rawQuery("SELECT * FROM person",null);
        while (c.moveToNext()) {
            Person person=new Person();
            person.setName(c.getString(c.getColumnIndex("name")));
            person.setAge(c.getString(c.getColumnIndex("age")));
            allPersons.add(person);
        }
        c.close();
        db.close();
    }
```
##手机内部数据库(litepal)
compile 'org.litepal.android:core:1.4.0'
首先在application中注册
```
public class Application extends android.app.Application{
    @Override
    public void onCreate() {
        super.onCreate();
        LitePal.initialize(this);
    }
}
```
```
<application
        android:name=".Application"
```
assets内创建litepal.xml:
```
<?xml version="1.0" encoding="utf-8"?>
<litepal>
    <list>
        <mapping class="com.example.administrator.ex.SqlLite.Album"></mapping>
        //对应类Song
        <mapping class="com.example.administrator.ex.SqlLite.Song"></mapping>
    </list>
</litepal>
```
创建类Song:
```
import org.litepal.annotation.Column;
import org.litepal.crud.DataSupport;
 
/**
* Created by Administrator on 2016/11/20 0020.
*/
 
public class Song extends DataSupport {
    @Column(nullable = false)
    private String name;
 
    private int duration;
 
    @Column(ignore = true)
    private String uselessField;
 
    public void setName(String name) {
        this.name = name;
    }
 
    public void setDuration(int duration) {
        this.duration = duration;
    }
 
    public String getName() {
        return name;
    }
    public int getDuration() {
        return duration;
    }
}
```
Activity使用:
```
initData();
        queryData();
            private List<Song> allSongs=new ArrayList<>();
private void initData() {
        //根据litepal.xml初始数据库如果之前存在将优化性修改数据库
        SQLiteDatabase db = LitePal.getDatabase();
    }
    private void addData() {
        Song song1 = new Song();
        song1.setName("song1");
        song1.setDuration(320);
        song1.save();
        queryData();
    }
    private void deleteData() {
//        DataSupport.delete(Song.class, id);
//        DataSupport.deleteAll(Song.class, "duration > ?", "350");
        DataSupport.deleteAll(Song.class);
        queryData();
    }
 
    private void upData() {
        Album albumToUpdate = DataSupport.find(Album.class, 1);
        albumToUpdate.setPrice(20.99f); // raise the price
        albumToUpdate.save();
    }
    private void queryData() {
        allSongs = DataSupport.findAll(Song.class);
        List<Song> songs = DataSupport.where("name like ?", "song%").order("duration").find(Song.class);
        //不好用，请重写
        if (adapter!=null){
            adapter=null;
            initRecycleView();
        }
    }
```
##androidStudio assets文件读取
```
public String ReadTxtFile(String strFilePath)
    {
        String path = strFilePath;
        String content = ""; //文件内容字符串
        //打开文件
        File file = new File(path);
        //如果path是传递过来的参数，可以做一个非目录的判断
        if (file.isDirectory())
        {
            Log.d("TestFile", "The File doesn't not exist.");
        }
        else
        {
            try {
                InputStream instream = getResources().getAssets().open("a.json");
//                InputStream instream = new FileInputStream(file);
                if (instream != null)
                {
                    InputStreamReader inputreader = new InputStreamReader(instream);
                    BufferedReader buffreader = new BufferedReader(inputreader);
                    String line;
                    //分行读取
                    while (( line = buffreader.readLine()) != null) {
                        content += line + "\n";
                    }
                    instream.close();
                }
            }
            catch (java.io.FileNotFoundException e)
            {
                Log.d("TestFile", "The File doesn't not exist.");
            }
            catch (IOException e)
            {
                Log.d("TestFile", e.getMessage());
            }
        }
        return content;
    }
原本是从指定路径读取文本，16.17改写为从assets读取指定a.json
```
#通用组件
##DrawerLayout
###DrawerLayout全部
```
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/drawerLayout">
    <android.support.design.widget.CoordinatorLayout
        android:layout_width="match_parent"
    android:layout_height="match_parent">
    <android.support.design.widget.AppBarLayout
        android:layout_height="wrap_content"
        android:layout_width="match_parent">
        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_height="?attr/actionBarSize"
            android:layout_width="match_parent">
        </android.support.v7.widget.Toolbar>
    </android.support.design.widget.AppBarLayout>
        <FrameLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:id="@+id/frameLayout"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
        </FrameLayout>
    </android.support.design.widget.CoordinatorLayout>
    <android.support.design.widget.NavigationView
        android:id="@+id/navigationView"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"/>
</android.support.v4.widget.DrawerLayout>
```
###DrawerLayout在下面
```
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent">
    <android.support.v4.widget.DrawerLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/drawerLayout">
    <android.support.design.widget.AppBarLayout
        android:layout_height="wrap_content"
        android:layout_width="match_parent">
        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_height="?attr/actionBarSize"
            android:layout_width="match_parent">
        </android.support.v7.widget.Toolbar>
    </android.support.design.widget.AppBarLayout>
        <FrameLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:id="@+id/frameLayout">
        </FrameLayout>
        <android.support.design.widget.NavigationView
            android:id="@+id/navigationView"
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_gravity="start"/>
    </android.support.v4.widget.DrawerLayout>
</android.support.design.widget.CoordinatorLayout>
```
##CoordinatorLayout
```
<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="match_parent"
              android:layout_height="match_parent">
</android.support.design.widget.CoordinatorLayout>
```
##自定义VIEW
```
    //这个是放在布局中要加的
    public MyListView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        //默认View是只支持EXACTLY(精确大小)或者match
        setMeasuredDimension(measureWidth(widthMeasureSpec),measureWidth(heightMeasureSpec));
    }
    //可以长宽重构两次，现在只有一次
    private int measureWidth(int widthMeasureSpec){
        int measureMode=MeasureSpec.getMode(widthMeasureSpec);
        int measureSize=MeasureSpec.getSize(widthMeasureSpec);
        //精确
        if (measureMode==MeasureSpec.EXACTLY)return measureSize;
        //wrap_content 从300和容器大小选最小
        else if (measureMode==MeasureSpec.AT_MOST)return Math.min(300,measureSize);
        else return measureSize;
    }
```
#SDK/API
##百度SDK
###百度sdk初始化
androidStudio查看sha1
![](leanote://file/getImage?fileId=583d861bec73b62dd4000000)
![Alt text](https://github.com/cai1449074828/cai/blob/master/images/1480416877418.png)
其中test2的appkey为我电脑的（每台电脑不同）
下载SDK
http://wiki.lbsyun.baidu.com/cms/androidsdk/all/v4.1.1/BaiduMap_AndroidSDK_v4.1.1_All.zip
填写AppKey:
```
<application
        android:name=".Application">
         <meta-data
            android:name="com.baidu.lbsapi.API_KEY"
            android:value="申请的AppKey" />
            </application>
```
application:
```
SDKInitializer.initialize(this);
```
gradle_app
```
android {
 sourceSets {
        main {
            jniLibs.srcDir 'libs'
        }
    }
}
```
权限
```
uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="com.android.launcher.permission.READ_SETTINGS" />
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.GET_TASKS" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
```
###百度地图
layout:
```
<FrameLayout
        android:id="@+id/map"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
```
```
@SuppressWarnings("unused")
    SupportMapFragment map;
private void initMap() {
        Intent intent = getIntent();
        MapStatus.Builder builder = new MapStatus.Builder();
        if (intent.hasExtra("x") && intent.hasExtra("y")) {
            // 当用intent参数时，设置中心点为指定点
            Bundle b = intent.getExtras();
            LatLng p = new LatLng(b.getDouble("y"), b.getDouble("x"));
            builder.target(p);
        }
        builder.overlook(-20).zoom(15);
        BaiduMapOptions bo = new BaiduMapOptions().mapStatus(builder.build())
                .compassEnabled(false).zoomControlsEnabled(false);
        map = SupportMapFragment.newInstance(bo);
        FragmentManager manager = getSupportFragmentManager();
        manager.beginTransaction().add(R.id.map, map, "map_fragment").commit();
    }
```
![enter description here][1]


  [1]: ./images/1480437031301.jpg "1480437031301.jpg"