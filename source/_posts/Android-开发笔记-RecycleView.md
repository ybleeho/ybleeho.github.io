---
title: Android 学习笔记 - RecyclerView
date: 2019-08-19 23:54:45
tags:
---
## 1. RecyclerView 概述

RecyclerView是比ListView更为高级和灵活的版本。RecyclerView由多个不同组件的共同工作来展示
信息。他们分别是Layout Manager(position),Item Animator(animate), Adapter(provide)使代码组织的更合理。


## 2. 关于Layout Manager

LinearLayoutManager: 类似ListView的风格，不同于ListView有Vertical和Horizontal方向。

GridLayoutManager: 格子布局、继承自LinearLayoutManager，实现效果类似GridView。

GridLayoutManager: 交错的格子布局，二维网格，每列微偏离前面一个，类似瀑布流的效果。

可由自己实现。

## 3. 实现步骤

1. Add Recyclerview to layout
2. Connect it to a layout manager
3. Attach an adapter for the data to be displayed
4. Add a list adapter -> create viewHolder, bind viewHolder
5. Add item animation: 默认是DefaultItemAnimator，可自行扩展。

示例代码:
```
class MyActivity : Activity() {
    private lateinit var recyclerView: RecyclerView
    private lateinit var viewAdapter: RecyclerView.Adapter<*>
    private lateinit var viewManager: RecyclerView.LayoutManager

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.my_activity)

        viewManager = LinearLayoutManager(this)
        viewAdapter = MyAdapter(myDataset)

        recyclerView = findViewById<RecyclerView>(R.id.my_recycler_view).apply {

            layoutManager = viewManager
            adapter = viewAdapter

        }
    }
    // ...
}


class MyAdapter(private val myDataset: Array<String>) :
        RecyclerView.Adapter<MyAdapter.MyViewHolder>() {

    class MyViewHolder(val textView: TextView) : RecyclerView.ViewHolder(textView)

    override fun onCreateViewHolder(parent: ViewGroup,
                                    viewType: Int): MyAdapter.MyViewHolder {
     // create a new view
    }

    // Replace the contents of a view (invoked by the layout manager)
    override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
        // - get element from your dataset at this position
        // - replace the contents of the view with that element
    }

    // Return the size of your dataset (invoked by the layout manager)
    override fun getItemCount() = myDataset.size
}


```
