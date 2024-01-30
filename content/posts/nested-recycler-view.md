---
title: 'Nested RecyclerView Android '
date: '2024-01-17T13:06:38+08:00'
description: 'A perfect professional guide for Nested Recyclerview and its optimization techniques.'
author: 'Narayan Panthi'
cover: 'https://miro.medium.com/v2/resize:fit:1000/1*mqjP2dOzkxqs2iphhsxZ2Q.jpeg'
tags: ["Nested", "RecyclerView", "Android", "Nested RecyclerView"]
theme: 'light'
---

# Nested RecyclerView In Android



![](https://miro.medium.com/v2/resize:fit:1000/1*mqjP2dOzkxqs2iphhsxZ2Q.jpeg)

Photo by Author — Ghandruk, Nepal

💥 Hello, In this article we are going to implement a nested  **recyclerview.** We will learn how exactly nested  **recyclerview**  are made in real-time projects and learn how to optimize it properly.

We can use a  **RecyclerView**  inside another  **RecyclerView**. We refer to this as nested  **RecyclerView**. Here are some of the apps using Nested R**ecyclerView**.

![](https://miro.medium.com/v2/resize:fit:375/0*qi6v5t5sxQxzsAs_)

![](https://miro.medium.com/v2/resize:fit:1125/1*iYg81RuLA4DTKLQpk8ghSQ.png)

![](https://miro.medium.com/v2/resize:fit:1125/1*F3QJeaKvITM7wA-_p51N9w.png)

Pictures of Nested Recyclerview Samples- eLibrary, Spotify, Netflix

# Prerequisites

-   Kotlin Language
-   RecyclerView
-   Hilt Dependency Injection
-   Retrofit2
-   **API_URL** :  [https://game-of-thrones-quotes.herokuapp.com/v1/**houses**](https://game-of-thrones-quotes.herokuapp.com/v1/houses)

**Just Give me all the Code 👇**

[](https://github.com/iamnaran/template-recycler-view?source=post_page-----e5afb2b9771a--------------------------------)

## iamnaran/template-recycler-view

### A recycler view. Contribute to iamnaran/template-recycler-view development by creating an account on GitHub.

github.com

## Let's Get Started,

# Step 1 — Setting up Model & API Data

An API that has a list of objects with a nested list of other objects. Take a look at the below  **API response,** here we have the  **Game Of Thrones**  **API**  **List** which has a list of names of  **Houses** and their  **Members**  list as nested objects.

API Response

![](https://miro.medium.com/v2/resize:fit:700/1*ppSNczxcmXw1CNdUgehmHQ.png)

App Structure & UI

> Understanding the structure of API data is crucial. I’ve noticed that some developers struggle to analyze it, often making assumptions about its functionality, resulting in suboptimal development practices. It’s essential to be well-versed in the data format, recognizing the Array [] and Object {} structures, before diving into the implementation of any feature or module.

Let’s skip our  **PEP Talk** and start coding Right! 👍

![](https://miro.medium.com/v2/resize:fit:475/0*nePNyEsRPaVsxf35.gif)

To generate the  **KOTLIN**  model from  **JSON**  you can download these plugins in Android Studio.

1.  ([**JSON To Kotlin Class**](https://plugins.jetbrains.com/plugin/9960-json-to-kotlin-class-jsontokotlinclass-))
2.  ([**JSON To Java Class**](https://www.jsonschema2pojo.org/))

> **GameOfThrones.kt**  — Model

GameOfThrones Model

Now, Create an API Service to get a response from the  **API**.

> [https://game-of-thrones-quotes.herokuapp.com/v1/**houses**](https://game-of-thrones-quotes.herokuapp.com/v1/houses)

Start by breaking down our URL into  **BASE_URL**  &  **End Points.**

buildConfigField 'String', 'BASE_URL', "\"https://game-of-thrones-quotes.herokuapp.com/v1/\""

> **ApiEndPoints.kt**

object ApiEndPoints {  

   const val GAME_OF_THRONES_URL = "houses"  

}

> **ApiService.kt**

interface ApiService {  

    @GET(ApiEndPoints.GAME_OF_THRONES_URL)  
    suspend fun getGameOfThronesData():Response<List<GameOfThrones>>  
}

Now, Let’s create  **ViewModel**  to get a response from the above API service.

> **HomeViewModel.kt**

Here,  **HomeRepository** acts as a repository class to get our response &  **PreferencesHelper** acts as a helper class to store data in  **SharedPreference**. You can see the project repository to understand it more clearly.

> For simplicity, Storing in shared pref, you can save in Room Database by mapping current model into Room/Realm DB Entity & inserting it using Dao’s.

# Step 2— User Interface Setup  **(XML)**

Here, We will need three layouts,

1.  Activity UI, For Toolbar & Parent RecyclerView
2.  A Layout row Item for Parent RecyclerView — which consists of  **Title**  &  **child**  **recyclerview**.
3.  A Layout Row Item for Child RecyclerView.

![](https://miro.medium.com/v2/resize:fit:291/1*JfCQxGX0t0-ztIkWtGl0mg.png)

![](https://miro.medium.com/v2/resize:fit:280/1*PYIyIxgMVsWdRvZrKr_iBQ.png)

![](https://miro.medium.com/v2/resize:fit:281/1*Tnt4b2y7Gfek5-Ji7It_Uw.png)

Three Layouts (1–2–3): Activity, Parent Item & Child Item

> **1. activity_home.xml**
````
<?xml version="1.0" encoding="utf-8"?>  
<androidx.core.widget.NestedScrollView  
    xmlns:android="http://schemas.android.com/apk/res/android"  
    xmlns:app="http://schemas.android.com/apk/res-auto"  
    xmlns:tools="http://schemas.android.com/tools"  
    android:layout_width="match_parent"  
    android:layout_height="match_parent">  

    <androidx.constraintlayout.widget.ConstraintLayout  
        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:layout_marginStart="8dp"  
        android:layout_marginEnd="8dp"  
        android:fitsSystemWindows="true">  

        <include  
            android:id="@+id/title_layout"  
            layout="@layout/item_title_profile"  
            android:layout_width="0dp"  
            android:layout_height="wrap_content"  
            app:layout_constraintEnd_toEndOf="parent"  
            app:layout_constraintStart_toStartOf="parent"  
            app:layout_constraintTop_toTopOf="parent" />  

        <androidx.recyclerview.widget.RecyclerView  
            android:id="@+id/parent_recycler_view"  
            android:layout_width="match_parent"  
            android:layout_height="wrap_content"  
            android:clipToPadding="false"  
            android:paddingTop="10dp"  
            android:paddingBottom="60dp"  
            app:layout_constraintBottom_toBottomOf="parent"  
            app:layout_constraintTop_toBottomOf="@+id/title_layout" />  
    </androidx.constraintlayout.widget.ConstraintLayout>  
</androidx.core.widget.NestedScrollView>
````
> **2. item_row_parent.xml**
````
<?xml version="1.0" encoding="utf-8"?>  
<androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"  
    xmlns:app="http://schemas.android.com/apk/res-auto"  
    android:layout_width="match_parent"  
    android:layout_height="wrap_content"  
    android:layout_margin="15dp"  
    app:cardCornerRadius="10dp"  
    app:cardElevation="1dp">  

    <androidx.constraintlayout.widget.ConstraintLayout  
        android:layout_width="match_parent"  
        android:layout_height="wrap_content">  

        <TextView  
            android:id="@+id/content_title"  
            android:layout_width="0dp"  
            android:layout_height="wrap_content"  
            android:layout_marginTop="10dp"  
            android:layout_marginBottom="10dp"  
            android:includeFontPadding="false"  
            android:padding="10dp"  
            android:text="House of Stark"  
            android:textSize="18sp"  
            android:textStyle="bold"  
            app:layout_constraintBottom_toBottomOf="parent"  
            app:layout_constraintEnd_toEndOf="parent"  
            app:layout_constraintHorizontal_bias="1.0"  
            app:layout_constraintStart_toStartOf="parent"  
            app:layout_constraintTop_toTopOf="parent"  
            app:layout_constraintVertical_bias="0.0" />  

        <androidx.recyclerview.widget.RecyclerView  
            android:id="@+id/child_recycler_view"  
            android:layout_width="match_parent"  
            android:layout_height="wrap_content"  
            android:padding="8dp"  
            app:layout_constraintEnd_toEndOf="parent"  
            app:layout_constraintStart_toStartOf="parent"  
            app:layout_constraintTop_toBottomOf="@+id/content_title" />  

    </androidx.constraintlayout.widget.ConstraintLayout>  

</androidx.cardview.widget.CardView>
````
> **3. item_row_child.xml**
````
<?xml version="1.0" encoding="utf-8"?>  
<androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"  
    xmlns:app="http://schemas.android.com/apk/res-auto"  
    xmlns:tools="http://schemas.android.com/tools"  
    android:layout_width="wrap_content"  
    android:layout_margin="15dp"  
    app:cardCornerRadius="30dp"  
    app:cardElevation="5dp"  
    android:layout_height="wrap_content">  

    <androidx.constraintlayout.widget.ConstraintLayout  
        android:layout_width="match_parent"  
        android:layout_height="match_parent">  

        <TextView  
            android:id="@+id/name"  
            android:layout_width="match_parent"  
            android:layout_height="wrap_content"  
            android:layout_marginTop="200dp"  
            android:padding="20dp"  
            android:text="Jon Snow"  
            android:textColor="@color/colorPrimaryText"  
            app:layout_constraintBottom_toBottomOf="parent" />  

    </androidx.constraintlayout.widget.ConstraintLayout>  

</androidx.cardview.widget.CardView>
````
# Step 3— Setting up Adapter Classes

Several different classes work together to build our dynamic list. Here we have two  **recyclerviews**, which means we need two adapters. You define the adapter by extending  `[RecyclerView.Adapter](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter)`. You define the view holder by extending  `[RecyclerView.ViewHolder](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ViewHolder)`. The  _layout manager_  arranges the individual elements in your list.

Remember **the Parent-Child Concept**. Parent Adapter holds Child Adapter.

![](https://miro.medium.com/v2/resize:fit:500/0*q9ZIKVL-SrcBKFxl.gif)

A Parent-Child Illustration **👉**

Let's start by creating a child members adapter, where we will plot a list of  **members** from each house**.**

> **ChildMembersAdapter.kt**

Now, Let’s work on our  **ParentHouseAdapter**, the parent  **recyclerview**  adapter from which we will call our child adapter like below.
````
fun bind(result: GameOfThrones) {  
    itemView.content_title.text = result.name  
    val  childMembersAdapter = ChildMembersAdapter(result.members)  
    itemView.child_recycler_view.layoutManager = LinearLayoutManager(itemView.context, LinearLayoutManager.HORIZONTAL,false)  
    itemView.child_recycler_view.adapter = childMembersAdapter  

}
````

> **ParentHouseAdapter.kt**
````
package com.template.androidtemplate.ui.home.adapter

import android.view.LayoutInflater

import android.view.View

import android.view.ViewGroup

import androidx.recyclerview.widget.LinearLayoutManager

import androidx.recyclerview.widget.RecyclerView

import com.template.androidtemplate.R

import com.template.androidtemplate.data.model.GameOfThrones

import kotlinx.android.synthetic.main.item_row_parent.view.*

open class ParentHouseAdapter :

RecyclerView.Adapter<ParentHouseAdapter.DataViewHolder>() {

var gameOfThronesHouseList: List<GameOfThrones> = ArrayList()

var onItemClick: ((GameOfThrones) -> Unit)? = null

inner class DataViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {

init {

itemView.setOnClickListener {

onItemClick?.invoke(gameOfThronesHouseList[adapterPosition])

}

}

fun bind(result: GameOfThrones) {

itemView.content_title.text = result.name

val childMembersAdapter = ChildMembersAdapter(result.members)

itemView.child_recycler_view.layoutManager = LinearLayoutManager(itemView.context, LinearLayoutManager.HORIZONTAL,false)

itemView.child_recycler_view.adapter = childMembersAdapter

}

}

override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) = DataViewHolder(

LayoutInflater.from(parent.context).inflate(

R.layout.item_row_parent, parent,

false

)

)

override fun onBindViewHolder(holder: DataViewHolder, position: Int) {

holder.bind(gameOfThronesHouseList[position])

}

override fun getItemCount(): Int = gameOfThronesHouseList.size

fun addData(list: List<GameOfThrones>) {

gameOfThronesHouseList = list

notifyDataSetChanged()

}

}
````

# Final Step — Integrate Adapter In View

We have our adapter ready, In our  **Activity,** we are observing the  **API response,** we will add data to the adapter after we get a response from it.

````
package com.template.androidtemplate.ui.home.view

import android.os.Bundle

import android.util.Log

import androidx.activity.viewModels

import androidx.appcompat.app.AppCompatActivity

import androidx.lifecycle.Observer

import androidx.recyclerview.widget.LinearLayoutManager

import com.google.gson.Gson

import com.template.androidtemplate.R

import com.template.androidtemplate.data.model.GameOfThrones

import com.template.androidtemplate.ui.home.adapter.ParentHouseAdapter

import com.template.androidtemplate.ui.home.viewmodel.HomeViewModel

import com.template.androidtemplate.utils.Status

import dagger.hilt.android.AndroidEntryPoint

import kotlinx.android.synthetic.main.activity_home.*

@AndroidEntryPoint

class HomeActivity : AppCompatActivity() {

private val homeViewModel: HomeViewModel by viewModels()

private lateinit var parentHouseAdapter: ParentHouseAdapter

override fun onCreate(savedInstanceState: Bundle?) {

super.onCreate(savedInstanceState)

setContentView(R.layout.activity_home)

setUpViews()

doObserveWork()

}

private fun setUpViews() {

parent_recycler_view.layoutManager = LinearLayoutManager(this,

LinearLayoutManager.VERTICAL,false)

parentHouseAdapter = ParentHouseAdapter()

parent_recycler_view.adapter = parentHouseAdapter

}

private fun doObserveWork() {

homeViewModel.progressBarVisibility.observe(this, Observer {

})

homeViewModel.getGameOfThronesData().observe(this, Observer {

when (it.status) {

Status.SUCCESS -> {

val gson: Gson = Gson()

Log.e( "doObserveWork: ",gson.toJson(it.data) )

renderGameOfThronesList(it.data!!)

}

Status.ERROR -> {

}

Status.LOADING -> {

}

}

})

}

private fun renderGameOfThronesList(gameOfThrones: List<GameOfThrones>) {

parentHouseAdapter.addData(gameOfThrones)

parentHouseAdapter.notifyDataSetChanged()

}

}
````

Finally, We can now see all the houses with their members,

![](https://miro.medium.com/v2/resize:fit:404/1*ipmaXSsl4gDx6Pw4jJSGKg.png)

Hmm.. Output

And… Done, You have implemented a Nested RecyclerView.

> Congratulations! Now, You can make your  **Fancy UI** for child/parent recycler-view row items.

## If (isYourRecyclerViewSmooth){

## return;

## }

## continue;

# Let’s talk about Optimizations of RecyclerView

**RecyclerView**  can be very laggy when not implemented properly. It should be working at its peak performance otherwise, we can see lag/glitches while scrolling. Its performance depends on  **Design**,  **Data**,  **Calculations,**  and  **Implementation**.

Let’s find out some pro tips to remember while working with  **RecyclerView**  to maintain its performance. ⚠️

1.  **Never Ever use Recyclerview with NestedScrollView.**

As recyclerview is meant to recycle view while scrolling only the visible item gets rendered. But when used with NestedScrollView it will stop recycling views. You can use multiple view types with recyclerview or  [**ConcatAdapter**s](https://developer.android.com/reference/androidx/recyclerview/widget/ConcatAdapter)  to completely remove NestedScrollView.

**2. Avoid Heavy Calculations & Nesting views in Recyclerview Items.**

Calculating logic inside onBindViewHolder is not perfered. Do your complex calulation in model before plotting data in recyclerview. And Make your UI as simple as possible with use of minimal ViewGroups/Layouts**.** Too many nested views & calculations in recyclerview items will degrade it’s performance, as a lot of rendering will occur within visible items of recyclerview.

**3. Use ListAdapter with DiffUtil/ AsyncDiffer**

Using DiffCallbacks will completely remove notifyDatasetChanged which will avoid re-calculating &re-rendering all your views, It will only change the data that needs to be changed.

_Also, tell recyclerview your item has a unique ID. This will reduce the blinking effect on dataset notify, where it modifies only items with changes._

`parentAdapter.setHasStableIds(true)`

4. **Use RecycledViewPool**

It lets you share Views between multiple RecyclerViews. If you want to recycle views across RecyclerViews, create an instance of RecycledViewPool and use  `[setRecycledViewPool]
(https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView#setRecycledViewPool(androidx.recyclerview.widget.RecyclerView.RecycledViewPool))`.
````
`viewHolder.childRecyclerView.setRecycledViewPool(viewPool)`
````

**5. Use Item View Cache Size**

Setting the number of offscreen views to retain before adding them to the potentially shared recycled view pool.
````
`parentRecyclerView.setItemViewCacheSize(cacheSize)`
````

**6. Set Item Pre-Fetch Enable**

Sets the number of items to prefetch in collectInitialPrefetchPositions(int, LayoutPrefetchRegistry), which defines how many inner items should be prefetched when this LayoutManager’s RecyclerView is nested inside another RecyclerView.
````
`childRecyclerView.setItemPrefetchEnabled(true)`
````

**7. If the scroll is intercepting on Vertical Nested recyclerview, you can disable it with your layout manager.**
````
linearLayoutManager {  
    @Override  
    public boolean canScrollVertically() {  
        return false;  
    }  
};
````

**8. If RecyclerView is Blinking on Update**
````
binding.recyclerViewContainer.setItemAnimator(null);
````

**9. If you are observing LiveData/Flowable from the Room Database and plotting a nested recyclerview.**

Whenever you observe a list of data from room table & recyclerview gets scroll, new data is fetched continuously from room which will have to update the whole recyclerview, In such case the nested recyclerview get initialized again. This  **“continuous data reloading ”**  never stops which will slow down recyclerview performance. We could ListAdapter & improve it, but this will be heavy task to submit list on nested adapter.

> _Ok Lets understand how to solve it with an Example._

Suppose, we have  **CategoryEntity**  &  **BookEntity &** a **CategoryWithBooks**  relationship Pojo to get all categories with books observing changes of room tables.

````
@Entity(tableName = "category",  
        indices = {  
                @Index(value = "categoryId", unique = true),  
        }  
)  
class CategoryEntity{  
  private int categoryId;  
  private String categoryName;  
  private String categoryImage;  
  private Long createdAt;  
  // getter setter  
}  

@Entity(tableName = "book",  
        foreignKeys = @ForeignKey(  
                entity = CategoryEntity.class,  
                parentColumns = "categoryId",  
                childColumns = "categoryId",  
                onDelete = CASCADE,  
                onUpdate = NO_ACTION  
        ),  
        indices = {  
                @Index(value = "bookId", unique = true),  
                @Index(value = "categoryId")  
        }  
)  
class BookEntity{  
  private int bookId;  
  private int categoryId;  
  private String bookName;  
  private String bookAuthor;  
  private EntityMapType entityMapType = EntityMapType.BOOK;  
  // getter setter  
}

class CategoryWithBooks{  

  @Embedded  
  private CategoryEntity categoryEntity;  

  @Relation(parentColumn = "categoryId",  
            entityColumn = "categoryId",  
            entity = BookEntity.class  

  )  
  private List<BookEntity> bookEntities;  
  // getter setter  
}
````

To  **avoid**  creating Nested Objects we can map all the  **CategoryObject**  as a  **BookObject**  and differentiate it with a Type. Let’s call it EntityMapType.

````
enum EntityMapType { CATEGORY, BOOK }

@Transaction  
@Query("SELECT * FROM category ORDER BY createdAt DESC")  
public abstract LiveData<List<CategoryWithBooks>> getAllCategoryWithBooks();  

getViewModel().getAllCategoryWithBooks().observe(getViewLifecycleOwner(), getAllCategoryObserver);  

private final Observer<List<CategoryWithBooks>> getAllCategoryObserver = listBaseResource -> {  

        if (!listBaseResource.isEmpty()) {  
            List<Book> bookList = new ArrayList();  
            foreach(Category category: listBaseResource){  
              Book book = new Book();  
              book.setBookId(category.getCategoryId());  
              book.setEntityMapType(EntityMapType.CATEGORY);  
              ....  
              bookList.add(book);  
              bookList.addAll(category.getBookEntities())  

            }  
            // plotting recyclerview with BookList Only.  
            recyclerView.submitList(bookList);  
            // make two view type for recyclerview - TYPE_BOOK & TYPE_CATEGORY  
        } 
  ````

Hence, we can completely remove nested recyclerview which will increase performance for sure.  **One drawback is**, that the mapping calculation is being made which should not be an issue here more than submitting a list in nested recyclerview. 😄

That’s all for today, Reply with any queries & suggestions...

Thank you… Valar Dohaeris