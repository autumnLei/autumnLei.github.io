---
title: recycleview-glide-Demo
date: 2019-05-04 01:08:57
tags:
- Android
- recycleview
- glide
---
基于Android Studio 3.1.2<!-- more -->

#### app/build.gradle 文件中添加依赖  版本号跟android-support-v7包一致
```
implementation 'com.android.support:recyclerview-v7:27.1.1'
//Glide图片加载库
implementation 'com.github.bumptech.glide:glide:4.2.0'
```

#### 在activity_main.xml中添加布局
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerview"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
    
</LinearLayout>
```

#### 在layout文件下新建item_main.xml作为每个item的布局 暂时只放一个imageview
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android" android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <ImageView
        android:id="@+id/imageview"
        android:layout_width="wrap_content"
        android:layout_height="250dp" />
</LinearLayout>
```

#### 新建adapter文件夹 建立recyclerview对应adapter文件
```
public class MainRecyclerViewAdapter extends RecyclerView.Adapter<MainRecyclerViewAdapter.ViewHolder>{

    private List<String> list;
    private Context context;

    //自定义ViewHolder,包含item的所有界面元素
    public static class ViewHolder extends RecyclerView.ViewHolder {
        ImageView imageView;
        public ViewHolder(View view) {
            super(view);
            imageView = (ImageView) view.findViewById(R.id.imageview);
        }
    }

    public MainRecyclerViewAdapter(Context context, List<String> list){
        this.context = context;
        this.list = list;
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder holder, int position) {
        String url = list.get(position);
        if (url != null){
            Glide.with(context).load(url).into(holder.imageView);
            }
    }

    @Override
    public int getItemCount() {
        return list.size();
    }

    @NonNull
    @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_main, parent, false);
        ViewHolder viewHolder = new ViewHolder(view);
        return viewHolder;
    }
}
```
#### 在MainActivity中初始化recyclerview
```
public class MainActivity extends AppCompatActivity {

    private List<String> list = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initList();
        RecyclerView recyclerView = findViewById(R.id.recyclerview);
        recyclerView.setLayoutManager(new LinearLayoutManager(this));
        recyclerView.setAdapter(new MainRecyclerViewAdapter(this.getApplicationContext(), list));
        }

    private void initList(){
        for (int i = 1; i < 9; i++) {
            //天堂图片网站图片
            list.add("http://img.ivsky.com/img/bizhi/pre/201808/24/haianshang_de_dengta-00"+i+".jpg");
        }
    }
}
```

#### 申请网络权限 
```
<uses-permission android:name="android.permission.INTERNET" />
```

#### 运行效果
![](20181009213958344.png)