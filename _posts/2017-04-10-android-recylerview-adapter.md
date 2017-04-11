---
layout: post
title:  "Android万能RecylerView的Adapter实现（通用类实现）"
date:   2017-04-10 17:45:20
categories: Android学习笔记
tags: Android RecylerView Adapter
---

* content
{:toc}

学Android的时候，找视频在慕课网上看到了个优雅使用RecylerView实现复杂布局的视频，然后封装了一个通用的Adapter

任何RecylerView都可以用的，而且只需要写一个匿名内部类就可以实现数据渲染，还是很好用的




## 万能适配器代码

```java
public abstract class MyRecylerViewAdapter<T> extends RecyclerView.Adapter<RecyclerView.ViewHolder> {

  private Context context;
  private List<T> list;
  LayoutInflater inflater;

  MyRecylerViewAdapter(Context context, List<T> list) {
    this.context = context;
    this.list = list;
    this.inflater = LayoutInflater.from(context);
  }

  @Override
  public abstract RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType);

  @Override
  @SuppressWarnings("unchecked")
  public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
    ((TypeViewHolder) holder).bindHolder(list.get(position));
  }

  @Override
  public abstract int getItemViewType(int position);

  @Override
  public int getItemCount() {
    return list.size();
  }


  /**
   * -----------------------------------------------------------------------------------------------
   */
  abstract class TypeViewHolder extends RecyclerView.ViewHolder {

    private View convertView;
    private SparseArray<View> views = new SparseArray<>();

    TypeViewHolder(View itemView) {
      super(itemView);
      this.convertView = itemView;
    }

    @SuppressWarnings("unchecked")
    <V extends View> V getView(int viewId) {
      View view = views.get(viewId);
      if(view == null) {
        view = convertView.findViewById(viewId);
        views.put(viewId, view);
      }
      return (V) view;
    }

    TypeViewHolder setText(int viewId, String text) {
      TextView textView = getView(viewId);
      textView.setText(text);
      return this;
    }

    TypeViewHolder setBackgroundColor(int viewId, int colorId) {
      ImageView imageView = getView(viewId);
      imageView.setBackgroundResource(colorId);
      return this;
    }

    abstract void bindHolder(T model);
  }

}
```

## 使用方法

```java
recyclerView = (RecyclerView) findViewById(R.id.recyclerView);
recyclerView.setLayoutManager(new LinearLayoutManager(this));
adapter = new MyRecylerViewAdapter<DataModel>(this, list) {
  @Override
  public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
    switch (viewType) {
      case DataModel.TYPE_ONE:
        return new TypeViewHolder(inflater.inflate(R.layout.item_type_one, parent, false)) {
          @Override
          public void bindHolder(DataModel model) {
            this.setText(R.id.name, model.name);
            this.setBackgroundColor(R.id.avatar, model.avatarColor);
          }
        };
      case DataModel.TYPE_TWO:
        return new TypeViewHolder(inflater.inflate(R.layout.item_type_two, parent, false)) {
          @Override
          public void bindHolder(DataModel model) {
            this.setText(R.id.name, model.name).setText(R.id.content, model.content);
            this.setBackgroundColor(R.id.avatar, model.avatarColor);
          }
        };
      case DataModel.TYPE_THREE:
        return new TypeViewHolder(inflater.inflate(R.layout.item_type_three, parent, false)) {
          @Override
          public void bindHolder(DataModel model) {
            this.setText(R.id.name, model.name).setText(R.id.content, model.content);
            this.setBackgroundColor(R.id.avatar, model.avatarColor).setBackgroundColor(R.id.contentImage, model.contentColor);
          }
        };
    }
    return null;
  }

  @Override
  public int getItemViewType(int position) {
    return list.get(position).type;
  }
};
recyclerView.setAdapter(adapter);
```

就是这么简单，一个好的封装，可以省多少事呀！

## 参考

[http://www.imooc.com/learn/731](http://www.imooc.com/learn/731)