package com.ibeitia.waypoint;
/**
 * 5/9/13
 * @author Inigo Beitia
 * @author Brendan Ritter
 * @author Chloe Eghtebas
 * 
 * AndroidListViewActivity is the first screen of Waypoint and displays all available hikes.
 * It also has an button to create your own hike.
 */
import java.io.File;

import com.ibeitia.waypoint.SingleListItem;

import android.app.ActionBar;
import android.app.ListActivity;
import android.content.Intent;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuInflater;
import android.view.MenuItem;
import android.view.View;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemClickListener;
import android.widget.ArrayAdapter;
import android.widget.ListView;
import android.widget.TextView;

public class AndroidListViewActivity extends ListActivity {
	
	/**
	 * This required function is called when the menu is constructed. 
	 * It prepares menu items for display
	 * 
	 * menu-A menu object.
	 * returns-true
	 */
	@Override
    public boolean onCreateOptionsMenu(Menu menu){
    	MenuInflater inflater=getMenuInflater();
    	inflater.inflate(R.menu.menu, menu);
    	return true;
    }
	
	/**
	 * This function is called when an item in the actionBar gets selected.
	 * There is only one button in the bar, the +, which is called item1
	 * 
	 * item-A menu item
	 * returns- true if clicked
	 */
	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
	    // Handle item selection
	    switch (item.getItemId()) {
	    case R.id.item1:
			Intent intent = new Intent(this, GPSLoggerActivity.class);
			startActivity(intent);
	        return true;
	    default:
	        return super.onOptionsItemSelected(item);
	    }
	}
	/**
	 * This function is called when this activity is initialized.
	 * This function sets up all the listeners and functionality for the page
	 * 
	 * savedInstanceState- A bundle object
	 */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        //creates the action bar and sets its title
        ActionBar actionBar = getActionBar();
        actionBar.setTitle("All Hikes");
        actionBar.show();
        
        // storing string resources into Array
        File dir = new File("/sdcard/GPSLogger");
        String[] files = dir.list();
        
        // Binding Array to ListAdapter
        this.setListAdapter(new ArrayAdapter<String>(this, R.layout.list_item, R.id.label, files));
        
        ListView lv = getListView();

        // listening to single list item on click
        //if it is clicked, the activity is transitioned to detailed view of that hike
        lv.setOnItemClickListener(new OnItemClickListener() {
          public void onItemClick(AdapterView<?> parent, View view,
              int position, long id) {
        	  
        	  // selected item 
        	  String hikename = ((TextView) view).getText().toString();
        	  
        	  // Launching new Activity on selecting single List Item
        	  Intent i = new Intent(getApplicationContext(), SingleListItem.class);
        	  // sending data to new activity
        	  i.putExtra("hikename", hikename);
        	  startActivity(i);
          }
        });

    }
}