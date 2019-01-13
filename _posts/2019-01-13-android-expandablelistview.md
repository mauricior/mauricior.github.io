---
title: "Android: How to do an Expandable List View with groups and subgroups"
date: 2019-01-13
tags: [android, development, java]
header:
  image: "/images/nino-android/android-02.png"
excerpt: "Android, Java"
mathjax: "true"
---


Currently I’m working in a personal project using **Android**, my objective is to create an app which gathers all the information from user bank accounts or investment brokers in one place, to be easier to manage the money.

So I need to use **Expandable List View** to list each “source” of money.  The final result and the tutorial
you can see below.

**The values shown are fictitious**

**Expandable List closed**

<img src="{{ site.url }}{{ site.baseurl }}/images/nino-android/android-01.png" alt="expandable list view">

**Expandable List opened**

<img src="{{ site.url }}{{ site.baseurl }}/images/nino-android/android-02.png" alt="expandable list view">


**Let's Get Started**

After you create your project and the mainActivity, you can start by the main layout (`/res/layout/list_layout.xml`):

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ExpandableListView
        android:id="@+id/elvInvestments"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_margin="8dp" />

</LinearLayout>
```

Main item layout (group) (`/res/layout/list_group.xml`)

<img src="{{ site.url }}{{ site.baseurl }}/images/nino-android/android-list_group.png" alt="expandable list view">

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="#ffffffff"
    android:orientation="horizontal">

    <TextView
        android:id="@+id/tvGroup"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:layout_marginLeft="40dp"
        android:layout_marginRight="16dp"
        android:layout_marginTop="16dp"
        android:layout_weight="8"
        android:text="Group"
        android:textColor="@android:color/black"/>

    <TextView
        android:id="@+id/tvAmount"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_margin="18dp"
        android:layout_weight="12"
        android:gravity="right"
        android:text="Amount"
        android:textColor="@android:color/black"/>

</LinearLayout>
```
Subitem layout (group item) (`/res/layout/list_item_group.xml`):

<img src="{{ site.url }}{{ site.baseurl }}/images/nino-android/android-list_item_group.png" alt="expandable list view">

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="#ff9b999a"
    android:orientation="horizontal">

    <TextView
        android:id="@+id/tvItem"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_margin="16dp"
        android:layout_weight="7"
        android:text="Item"
        android:textColor="@android:color/white"/>

    <TextView
        android:id="@+id/tvRealAmount"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_margin="16dp"
        android:layout_weight="16"
        android:gravity="right"
        android:text="Amount"
        android:textColor="@android:color/white"/>

</LinearLayout>
```

Since we are doing a custom `ExpandableListView`, we can change colors, fonts and sizes to fit as we want.

For the information to be displayed in the `ExpandableListView`, we have to create an adapter, in this case a `BaseExpandableListAdapter` and implement the necessary methods as below:

```java
package com.distribution.fsrsolutions.nino;

import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseExpandableListAdapter;
import android.widget.TextView;


import java.util.HashMap;
import java.util.List;

public class ListViewAdapter extends BaseExpandableListAdapter {

    private List<String> lstGroups;
    private HashMap<String, List<Investment>> lstItemsGroups;
    private Context context;

    public ListViewAdapter(Context context, List<String> groups, HashMap<String, List<Investment>> itemsGroups){
        // initialize class variables
        this.context = context;
        lstGroups = groups;
        lstItemsGroups = itemsGroups;
    }

    @Override
    public int getGroupCount() {
        // returns groups count
        return lstGroups.size();
    }

    @Override
    public int getChildrenCount(int groupPosition) {
        // returns items count of a group
        return lstItemsGroups.get(getGroup(groupPosition)).size();
    }

    @Override
    public Object getGroup(int groupPosition) {
        // returns a group
        return lstGroups.get(groupPosition);
    }

    @Override
    public Object getChild(int groupPosition, int childPosition) {
        // returns a group item
        return lstItemsGroups.get(getGroup(groupPosition)).get(childPosition);
    }

    @Override
    public long getGroupId(int groupPosition) {
        // return the group id
        return groupPosition;
    }

    @Override
    public long getChildId(int groupPosition, int childPosition) {
        // returns the item id of group
        return childPosition;
    }

    @Override
    public boolean hasStableIds() {
        // returns if the ids are specific ( unique for each group or item)
        // or relatives
        return false;
    }

    @Override
    public View getGroupView(int groupPosition, boolean isExpanded, View convertView, ViewGroup parent) {
        // create main items (groups)
        if(convertView == null){
            LayoutInflater layoutInflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
            convertView = layoutInflater.inflate(R.layout.list_group, null);
        }

        TextView tvGroup = (TextView) convertView.findViewById(R.id.tvGroup);
        TextView tvAmount = (TextView) convertView.findViewById(R.id.tvAmount);

        tvGroup.setText((String) getGroup(groupPosition));
        tvAmount.setText(String.valueOf(getChildrenAmount(groupPosition)));

        return convertView;
    }

    @Override
    public View getChildView(int groupPosition, int childPosition, boolean isLastChild, View convertView, ViewGroup parent) {
        // create the subitems (items of groups)

        if(convertView == null) {
            LayoutInflater layoutInflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
            convertView = layoutInflater.inflate(R.layout.list_item_group, null);
        }

        TextView tvItem = (TextView) convertView.findViewById(R.id.tvItem);
        TextView tvRealAmount = (TextView)convertView.findViewById(R.id.tvRealAmount);

        Investment investment = (Investment) getChild(groupPosition, childPosition);
        tvItem.setText(investment.getName());
        tvRealAmount.setText(investment.getStringAmount());

        return convertView;
    }

    @Override
    public boolean isChildSelectable(int groupPosition, int childPosition) {
        // returns if the subitem (item of group) can be selected
        return true;
    }
```

As my example deals with investments, I have created the `Investment` class:

```java
package com.distribution.fsrsolutions.nino;

public class Investment {
    private String name;
    private int quantity;
    private double amount;


    public Investment(String name, int quantity, double amount){
        this.name = name;
        this.quantity = quantity;
        this.amount = amount;
    }

    public void setName(String name){
        this.name = name;
    }

    public String getName(){
        return name;
    }

    public void setQuantity(int quantity){
        this.quantity = quantity;
    }

    public int getQuantity(){
        return quantity;
    }

    public void setAmount(double amount){
        this.amount = amount;
    }

    public double getAmount(){
        return amount;
    }

    public String getStringAmount(){
        return String.format("R$ %,.2f", amount);
    }
}


```
To finalize, go to our `Activity`, we created the data and defined our adapter:

```java
package com.distribution.fsrsolutions.nino;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.ExpandableListView;
import android.widget.ImageButton;
import android.widget.TextView;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

public class MainActivity extends AppCompatActivity {


    private Integer FLAG_HIDE_UNHIDE = 0;
    private String totalAmount = "";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ExpandableListView elvInvestments = (ExpandableListView) findViewById(R.id.elvInvestments);

        // Create the groups
        final List<String> lstGroups = new ArrayList<>();
        lstGroups.add("Renda Variavél");
        lstGroups.add("Renda Fixa");
        lstGroups.add("Extrato");


        // Create items of each group
        List<Investment> lstRendaVariavel = new ArrayList<>();
        lstRendaVariavel.add(new Investment("ITSA4", 100, 1000000.00));
        lstRendaVariavel.add(new Investment("KLBN11", 500, 5673.33));
        lstRendaVariavel.add(new Investment("PETR4", 1000, 10325.26));

        List<Investment> lstRendaFixa = new ArrayList<>();
        lstRendaFixa.add(new Investment("CDB NBC", 0, 5000.00));
        lstRendaFixa.add(new Investment("CDB Modal Pré-Fixado", 0, 2500.00));
        lstRendaFixa.add(new Investment("LCA Indusval", 0, 4000.00));

        List<Investment> lstExtrato = new ArrayList<>();
        lstExtrato.add(new Investment("Transferência AG000/CC0000-0", 0, 2500.00));

        // Create the relationship of groups and your items
        final HashMap<String, List<Investment>> lstItemsGroup = new HashMap<>();
        lstItemsGroup.put(lstGroups.get(0), lstRendaVariavel);
        lstItemsGroup.put(lstGroups.get(1), lstRendaFixa);
        lstItemsGroup.put(lstGroups.get(2), lstExtrato);

        // Create an adapter (BaseExpandableListAdapter) with the data above
        final ListViewAdapter listViewAdapter = new ListViewAdapter(this, lstGroups, lstItemsGroup);
        // defines the ExpandableListView adapter
        elvInvestments.setAdapter(listViewAdapter);

    }
}

```

The final result you can see below.

<img src="{{ site.url }}{{ site.baseurl }}/images/nino-android/android-02.png" alt="expandable list view">

**References**
[ExpandableListView Tutorial](https://www.androidhive.info/2013/07/android-expandable-list-view-tutorial/)
